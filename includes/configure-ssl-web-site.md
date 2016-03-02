
In diesem Artikel wird die Vorgehensweise beim Konfigurieren von HTTPS für eine Web-App in Azure App Service beschrieben. Dabei wird die Clientzertifikatauthentifizierung nicht behandelt. Informationen hierzu finden Sie unter [Konfigurieren von gegenseitiger TLS-Authentifizierung für Web-Apps](../articles/app-service-web/app-service-web-configure-tls-mutual-auth.md).

Standardmäßig ermöglicht Azure über ein Platzhalterzertifikat für die Domäne „*.azurewebsites.net“ bereits HTTPS für Ihre App. Wenn Sie keine benutzerdefinierte Domäne konfigurieren möchten, können Sie das Standard-HTTPS-Zertifikat nutzen. [Platzhalterdomänen](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/) sind jedoch generell nicht so sicher wie die Verwendung einer benutzerdefinierten Domäne mit Ihrem eigenen Zertifikat.

In den folgenden Abschnitten dieses Dokuments wird detailliert erläutert, wie HTTPS für benutzerdefinierte Domänen wie **contoso.com**, **www.contoso.com** oder ***.contoso.com** aktiviert wird.

<a name="bkmk_domainname"></a>
## Aktivieren von SSL für Ihre benutzerdefinierte Domäne

Wenn Sie HTTPS für eine benutzerdefinierte Domäne wie **contoso.com** aktivieren möchten, müssen Sie zunächst einen [benutzerdefinierten Domänennamen in Azure App Service konfigurieren](../articles/app-service-web/web-sites-custom-domain-name.md). Gehen Sie anschließend wie folgt vor:

1. [Beziehen eines SSL-Zertifikats](#bkmk_getcert)
2. [Konfigurieren des Standard- oder Premium-Tarifs](#bkmk_standardmode)
2. [Konfigurieren von SSL in Ihrer App](#bkmk_configuressl)
3. [Erzwingen von SSL für Ihre App](#bkmk_enforce) (optional)

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**.

<a name="bkmk_getcert"></a>
## 1\. Beziehen eines SSL-Zertifikats

Bevor Sie ein SSL-Zertifikat anfordern, müssen Sie zuerst festlegen, welche Domänennamen durch das Zertifikat geschützt werden. Davon hängt ab, welchen Typ von Zertifikat Sie benötigen. Wenn Sie lediglich einen einzelnen Domänennamen wie **contoso.com** oder **www.contoso.com** schützen müssen, reicht ein Basiszertifikat aus. Wenn Sie mehrere Domänennamen, wie z. B. **contoso.com**, **www.contoso.com** und **mail.contoso.com**, sichern müssen, erhalten Sie ein [Platzhalterzertifikat](http://en.wikipedia.org/wiki/Wildcard_certificate) oder ein [Zertifikat mit einem alternativen Antragstellernamen](http://en.wikipedia.org/wiki/SubjectAltName) (SubjectAltName).

Mit App Service verwendete SSL-Zertifikate müssen von einer [Zertifizierungsstelle](http://en.wikipedia.org/wiki/Certificate_authority) (CA) signiert werden. Wenn Sie noch kein Zertifikat haben, müssen Sie eines von einem Unternehmen erwerben, das SSL-Zertifikate ausstellt. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] – in englischer Sprache) im Microsoft TechNet Wiki.

Das Zertifikat muss die folgenden Anforderungen für SSL-Zertifikate in Azure erfüllen:

* Das Zertifikat muss einen privaten Schlüssel enthalten.
* Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.
* Der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf die App zugegriffen wird. Wenn dieses Zertifikat für mehrere Domänen gelten soll, müssen Sie, wie oben erläutert, einen Platzhalterwert verwenden oder Werte für alternative Antragstellernamen angeben.
* Das Zertifikat muss mindestens eine 2048-Bit-Verschlüsselung haben.
* Von Servern privater Zertifizierungsstellen ausgegebene Zertifikate werden von Azure App Service nicht unterstützt.

Um ein SSL-Zertifikat für die Verwendung mit Azure App Service zu erhalten, müssen Sie eine Zertifikatsignieranforderungsdatei (CSR) an eine Zertifizierungsstelle senden und anschließend eine PFX-Datei aus dem Zertifikat generieren, das Sie zurückerhalten haben. Sie können dazu das Tool Ihrer Wahl verwenden. Im Folgenden finden Sie einige der häufigsten Möglichkeiten, ein Zertifikat abzurufen:

- [Abrufen eines Zertifikats mit "Certreq.exe"](#bkmk_certreq)
- [Abrufen eines Zertifikats mit IIS-Manager](#bkmk_iismgr)
- [Erhalten eines Zertifikats mit OpenSSL](#bkmk_openssl)
- [Abrufen eines SubjectAltName-Zertifikats mit OpenSSL](#bkmk_subjectaltname)
- [Generieren selbstsignierter Zertifikate (nur für Testzwecke)](#bkmk_selfsigned)

> [AZURE.NOTE] Wenn Sie die Schritte befolgen, werden Sie aufgefordert, einen **Allgemeinen Namen**, z. B. `www.contoso.com` einzugeben. Für Platzhalterzertifikate sollte dieser Wert \*.domainname (z. B. \*.contoso.com) lauten. Wenn Sie einen Platzhalternamen wie \*.contoso.com und einen Stammdomänennamen wie contoso.com unterstützen müssen, können Sie ein Platzhalterzertifikat für einen alternativen Antragstellernamen (subjectAltName) verwenden.
>
> Zertifikate für die Kryptografie für elliptische Kurven (ECC) werden für Azure App Service zwar unterstützt, sie sind jedoch noch relativ neu, daher sollten Sie bei Ihrer Zertifizierungsstelle erfragen, wie genau Sie die CSR-Datei erstellen.

Eventuell müssen Sie auch **[Zwischenzertifikate](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (auch Kettenzertifikate genannt) anfordern, wenn diese von Ihrer Zertifizierungsstelle verwendet werden. Zwischenzertifikate gelten als sicherer als 'nicht verkettete Zertifikate', sodass sie von Zertifizierungsstellen üblicherweise verwendet werden. Zwischenzertifikate werden auf der Website der Zertifizierungsstelle häufig als separater Download bereitgestellt. In diesem Artikel wird dargestellt, wie Sie sicherstellen können, dass Zwischenzertifikate mit dem Zertifikat zusammengeführt werden, das zu Ihren Apps hochgeladen wurde.

<a name="bkmk_certreq"></a>
### Beziehen eines Zertifikats mit Certreq.exe (nur Windows)

Certreq.exe ist ein Windows-Dienstprogramm zum Erstellen von Zertifikatanforderungen. Es gehört seit Windows XP/Windows Server 2000 zur Windows-Basisinstallation und dürfte daher auf Windows-Systemen jüngeren Datums verfügbar sein. Gehen Sie folgendermaßen vor, um mit certreq.exe ein SSL-Zertifikat zu erhalten.

1. Öffnen Sie den **Editor**, und erstellen Sie ein neues Dokument mit folgendem Inhalt. Ersetzen Sie **mysite.com** in der Zeile „Subject“ durch den benutzerdefinierten Domänennamen Ihrer App. Zum Beispiel: Subject = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Weitere Informationen zu den oben angegebenen Optionen sowie über weitere verfügbare Optionen finden Sie in der [Certreq-Referenz](http://technet.microsoft.com/library/cc725793.aspx) (in englischer Sprache).

2. Speichern Sie die Textdatei unter **myrequest.txt**.

3. Führen Sie auf der **Startseite** oder im **Startmenü** **cmd.exe** aus.

4. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein, um die Zertifikatanforderungsdatei zu erstellen:

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Geben Sie den Pfad zu der in Schritt 1 erstellten Datei **myrequest.txt** an sowie den Pfad, der beim Erstellen der Datei **myrequest.csr** verwendet werden soll.

5. Senden Sie die Datei **myrequest.csr** an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Dazu müssen Sie die Datei eventuell hochladen oder im Editor öffnen und den Inhalt direkt in ein Webformular eingeben.

	Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] – in englischer Sprache) im Microsoft TechNet Wiki.

6. Wenn die Zertifizierungsstelle Ihnen eine Zertifikatdatei (.CER) zur Verfügung gestellt hat, speichern Sie diese Datei auf dem Computer, mit dem die Anforderung generiert wurde. Verwenden Sie anschließend den folgenden Befehl, um die Anforderung zu akzeptieren und den Prozess der Zertifikatgenerierung abzuschließen.

		certreq -accept -user mycert.cer

	In diesem Fall dient das Zertifikat **mycert.cer**, das Sie von der Zertifizierungsstelle erhalten haben, dazu, die Signatur des Zertifikats zu vervollständigen. Es wird keine Datei erstellt, sondern das Zertifikat wird im Windows-Zertifikatspeicher abgelegt.

6. Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat in den nächsten Schritten exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die für Microsoft IIS bereitgestellte Version.

	Klicken Sie nach dem Herunterladen des Zertifikats im Explorer mit der rechten Maustaste darauf, und wählen Sie **Zertifikat installieren**. Verwenden Sie die Standardwerte im **Zertifikat-Importassistenten**, und wählen Sie **Weiter** aus, bis der Import abgeschlossen ist.

7. Um das Zertifikat aus dem Zertifikatspeicher zu exportieren, führen Sie **certmgr.msc** auf der **Startseite** oder im **Startmenü** aus. Wenn **Zertifikat-Manager** angezeigt wird, erweitern Sie den Ordner **Persönlich**, und wählen Sie dann **Zertifikate** aus. Suchen Sie im Feld **Ausgestellt für** nach einem Eintrag mit dem benutzerdefinierten Domänennamen, für den Sie ein Zertifikat angefordert haben. Im Feld **Ausgestellt von** muss die Zertifizierungsstelle aufgeführt werden, die Sie für dieses Zertifikat verwendet haben.

	![hier Bild von Zertifikat-Manager einfügen][certmgr]

9. Klicken Sie mit der rechten Maustaste auf das Zertifikat, und wählen Sie **Alle Aufgaben** und anschließend **Exportieren**. Klicken Sie im **Zertifikat-Exportassistenten** auf **Weiter**, und wählen Sie dann **Ja, privaten Schlüssel exportieren**. Klicken Sie auf **Weiter**.

	![Privaten Schlüssel exportieren][certwiz1]

10. Wählen Sie **Persönlicher Informationsaustausch - PKCS #12**, **Alle Zertifikate in der Zertifikatkette berücksichtigen** und **Alle erweiterten Eigenschaften exportieren**. Klicken Sie auf **Weiter**.

	![Alle Zertifikate und erweiterten Eigenschaften berücksichtigen][certwiz2]

11. Wählen Sie **Kennwort** aus. Geben Sie das Kennwort ein, und bestätigen Sie es. Klicken Sie auf **Weiter**.

	![Kennwort angeben][certwiz3]

12. Geben Sie einen Pfad und Dateinamen an, in dem das exportierte Zertifikat enthalten ist. Der Dateiname muss die Erweiterung **.pfx** haben. Klicken Sie auf **Weiter**, um den Prozess abzuschließen.

	![Dateipfad angeben][certwiz4]

Jetzt können Sie die exportierte PFX-Datei zu Ihrer App in Azure App Service hochladen.

<a name="bkmk_openssl"></a>
### Erhalten eines Zertifikats mit OpenSSL

1. Generieren Sie einen privaten Schlüssel und eine Zertifikatsignieranforderung, indem Sie Folgendes in einer Befehlszeile, einer Bash- oder Terminalsitzung eingeben:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Geben Sie bei Aufforderung die entsprechenden Informationen ein. Beispiel:

 		Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []:

	Nach Abschluss dieses Prozesses haben Sie zwei Dateien: **myserver.key** und **server.csr**. Die Datei **server.csr** enthält die Zertifikatsignieranforderung.

3. Senden Sie die Zertifikatsignieranforderung an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] – in englischer Sprache) im Microsoft TechNet Wiki.

4. Sobald Sie ein Zertifikat von einer Zertifizierungsstelle erhalten haben, speichern Sie es in einer Datei mit dem Namen **myserver.crt**. Wenn die Zertifizierungsstelle das Zertifikat im Textformat bereitgestellt hat, fügen Sie den Zertifikattext einfach in die Datei **myserver.crt** ein. In einem Texteditor sieht der Dateiinhalt etwa folgendermaßen aus:

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	Speichern Sie die Datei .

5. Verwenden Sie in der Eingabeaufforderung, der Bash- oder Terminalsitzung den folgenden Befehl, um **myserver.key** und **myserver.crt** in **myserver.pfx** zu konvertieren, das Format, das von Azure App Service benötigt wird:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Geben Sie bei der entsprechenden Aufforderung ein Kennwort ein, um die PFX-Datei zu schützen.

	> [AZURE.NOTE] Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat im nächsten Schritt exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die Version, die als PEM-Datei (Dateierweiterung .pem) bereitgestellt wird.
	>
	> Der folgende Befehl zeigt, wie eine PFX-Datei erstellt wird, die Zwischenzertifikate enthält, die in der Datei **intermediate-cets.pem** enthalten sind:
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Nach der Ausführung dieses Befehls verfügen Sie über die Datei **myserver.pfx**, die für Azure App Service geeignet ist.

<a name="bkmk_iismgr"></a>
### Erhalten eines Zertifikats mit dem IIS-Manager

Wenn Sie mit IIS-Manager vertraut sind, können Sie damit ein Zertifikat generieren, das für Azure App Service verwendet werden kann.

1. Generieren einer CSR-Datei mit IIS-Manager für die Zertifizierungsstelle. Weitere Informationen zum Generieren einer CSR finden Sie unter [Anfordern eines Internetserverzertifikats (IIS 7)][iiscsr].

2. Senden Sie die Zertifikatsignieranforderung an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] – in englischer Sprache) im Microsoft TechNet Wiki.

3. Tragen Sie in die CSR das von der Zertifizierungsstelle bereitgestellte Zertifikat ein. Weitere Informationen zum Ausfüllen der CSR finden Sie unter [Installieren eines Internetserverzertifikats (IIS 7)][installcertiis].

4. Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat im nächsten Schritt exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die für Microsoft IIS bereitgestellte Version.

	Klicken Sie nach dem Herunterladen des Zertifikats im Explorer mit der rechten Maustaste darauf, und wählen Sie **Zertifikat installieren**. Verwenden Sie die Standardwerte im **Zertifikat-Importassistenten**, und wählen Sie **Weiter** aus, bis der Import abgeschlossen ist.

4. Exportieren Sie das Zertifikat aus IIS Manager. Weitere Informationen zum Exportieren des Zertifikats finden Sie unter [Exportieren eines Serverzertifikats (IIS 7)][exportcertiis]. Die exportierte Datei wird später nach Azure hochgeladen, um für Ihre App verwendet zu werden.

	> [AZURE.NOTE] Aktivieren Sie während des Exportprozesses die Option <strong>Ja, privaten Schlüssel exportieren</strong>. Damit wird der private Schlüssel in das exportierte Zertifikat einbezogen.

	> [AZURE.NOTE] Aktivieren Sie während des Exportprozesses die Optionen **Alle Zertifikate im Zertifizierungspfad einbeziehen** und **Alle erweiterten Eigenschaften exportieren**. Damit werden alle Zwischenzertifikate in das exportierte Zertifikat einbezogen.

<a name="bkmk_subjectaltname"></a>
### Erhalten eines SubjectAltName-Zertifikats mit OpenSSL

Mit OpenSSL kann eine Zertifikatanforderung erstellt werden, welche die SubjectAltName-Erweiterung verwendet, sodass mehrere Domänennamen mit einem Zertifikat unterstützt werden können. Dazu wird jedoch eine Konfigurationsdatei benötigt. Im Folgenden wird erläutert, wie eine Konfigurationsdatei erstellt wird und wie damit ein Zertifikat angefordert wird.

1. Erstellen Sie eine neue Datei mit dem Namen __sancert.cnf__, und verwenden Sie für die Datei den folgenden Inhalt:

		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default =
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default =
		localityName = Locality Name (eg, city)
		localityName_default =
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  =
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	Beachten Sie die Zeile, die mit 'subjectAltName' beginnt. Ersetzen Sie die derzeit aufgelisteten Domänennamen durch die Domänennamen, die zusätzlich zum allgemeinen Namen unterstützt werden sollen. Beispiel:

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	Das Feld "commonName\_default" müssen Sie nicht ändern, da Sie in einem der folgenden Schritte aufgefordert werden, Ihren allgemeinen Namen einzugeben.

2. Speichern Sie die Datei __sancert.cnf__.

1. Generieren Sie einen privaten Schlüssel und die Zertifikatsignieranforderung mithilfe der Konfigurationsdatei sancert.cnf. Verwenden Sie in einer Bash- oder Terminalsitzung den folgenden Befehl:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Geben Sie bei Aufforderung die entsprechenden Informationen ein. Beispiel:

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com


	Nach Abschluss dieses Prozesses haben Sie zwei Dateien: **myserver.key** und **server.csr**. Die Datei **server.csr** enthält die Zertifikatsignieranforderung.

3. Senden Sie die Zertifikatsignieranforderung an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] – in englischer Sprache) im Microsoft TechNet Wiki.

4. Sobald Sie ein Zertifikat von einer Zertifizierungsstelle erhalten haben, speichern Sie es in einer Datei mit dem Namen **myserver.crt**. Wenn die Zertifizierungsstelle das Zertifikat im Textformat bereitgestellt hat, fügen Sie den Zertifikattext einfach in die Datei **myserver.crt** ein. In einem Texteditor sieht der Dateiinhalt etwa folgendermaßen aus:

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	Speichern Sie die Datei .

5. Verwenden Sie in der Eingabeaufforderung, der Bash- oder Terminalsitzung den folgenden Befehl, um **myserver.key** und **myserver.crt** in **myserver.pfx** zu konvertieren, das Format, das von Azure App Service benötigt wird:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Geben Sie bei der entsprechenden Aufforderung ein Kennwort ein, um die PFX-Datei zu schützen.

	> [AZURE.NOTE] Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat im nächsten Schritt exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die Version, die als PEM-Datei (Dateierweiterung .pem) bereitgestellt wird.
	>
	> Der folgende Befehl zeigt, wie eine PFX-Datei erstellt wird, die Zwischenzertifikate enthält, die in der Datei **intermediate-cets.pem** enthalten sind:
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Nach der Ausführung dieses Befehls verfügen Sie über die Datei **myserver.pfx**, die für Azure App Service geeignet ist.

<a name="bkmk_selfsigned"></a>
### Generieren eines selbstsignierten Zertifikats (nur für Testzwecke)

In bestimmten Fällen möchten Sie eventuell ein Zertifikat für Testzwecke erhalten und den Kauf eines Zertifikats von einer vertrauenswürdigen Zertifizierungsstelle verzögern, bis Sie die Produktion aufnehmen. Diese Lücke kann durch selbstsignierte Zertifikate gefüllt werden. Ein selbstsigniertes Zertifikat ist ein Zertifikat, das Sie erstellen und signieren, als wären Sie eine Zertifizierungsstelle. Zwar kann mit diesem Zertifikat eine App geschützt werden, die meisten Browser geben jedoch Fehler zurück, wenn die App besucht wird, da das Zertifikat nicht von einer vertrauenswürdigen Zertifizierungsstelle signiert wurde. Einige Browser verweigern möglicherweise sogar das Aufrufen der App.

- [Generieren eines selbstsignierten Zertifikats mit makecert](#bkmk_ssmakecert)
- [Generieren eines selbstsignierten Zertifikats mit OpenSSL](#bkmk_ssopenssl)

<a name="bkmk_ssmakecert"></a>
#### Generieren eines selbstsignierten Zertifikats mit makecert ####

Mit den folgenden Schritten können Sie ein Testzertifikat mit einem Windows-System erstellen, in dem Visual Studio installiert ist:

1. Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Developer-Eingabeaufforderung**. Klicken Sie dann mit der rechten Maustaste auf **Developer-Eingabeaufforderung**, und wählen Sie **Als Administrator ausführen**.

	Wenn das Dialogfeld "Benutzerkontensteuerung" angezeigt wird, wählen Sie **Ja**, um fortzufahren.

2. Geben Sie an der Developer-Eingabeaufforderung den folgenden Befehl ein, um ein neues selbstsigniertes Zertifikat zu erstellen. **serverdnsname** muss durch den DNS-Namen Ihrer App ersetzt werden.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Mit diesem Befehl wird ein Zertifikat erstellt, das zwischen dem 01.01.2013 und dem 01.01.2014 gültig ist und den Speicherort im Zertifikatspeicher "CurrentUser" ablegt.

3. Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Windows PowerShell**, und starten Sie diese Anwendung.

4. Geben Sie an der Eingabeaufforderung von Windows PowerShell die folgenden Befehle ein, um das zuvor erstellte Zertifikat zu exportieren:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	Damit wird das angegebene Kennwort in $mypwd als geschützte Zeichenfolge gespeichert. Anschließend wird das Zertifikat mithilfe des DNS-Namens gesucht, der mit dem Parameter **dnsname** angegeben wird, und in die mit dem Parameter **filepath** angegebene Datei exportiert. Die exportierte Datei wird mit der geschützten Zeichenfolge geschützt, die das Kennwort enthält.

<a name="bkmk_ssopenssl"></a>
####Generieren eines selbstsignierten Zertifikats mit OpenSSL ####

1. Erstellen Sie ein neues Dokument mit dem Namen **serverauth.cnf**, und verwenden Sie für die Datei den folgenden Inhalt:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your app's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	Damit werden die Konfigurationseinstellungen festgelegt, die zum Erzeugen eines SSL-Zertifikats erforderlich sind, das von Azure App Service verwendet werden kann.

2. Generieren Sie ein neues selbstsigniertes Zertifikat, indem Sie Folgendes in einer Befehlszeile, einer Bash- oder Terminalsitzung eingeben:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Damit wird unter Verwendung der in der Datei **serverauth.cnf** festgelegten Konfigurationseinstellungen ein neues Zertifikat erstellt.

3. Verwenden Sie den folgenden Befehl, um das Zertifikat in eine PFX-Datei zu exportieren, die zu einer App in Azure App Service hochgeladen werden kann:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Geben Sie bei der entsprechenden Aufforderung ein Kennwort ein, um die PFX-Datei zu schützen.

	Die mit diesem Befehl erzeugte Datei **myserver.pfx** kann für Testzwecke zum Schützen der App verwendet werden.

<a name="bkmk_standardmode"></a>
## 2\. Konfigurieren des Standard- oder Premium-Tarifs

Die Aktivierung von HTTPS für eine benutzerdefinierte Domäne steht nur für die Tarife **Standard** und **Premium** in Azure App Service zur Verfügung. Gehen Sie folgendermaßen vor, um Ihren App Service-Plan in den Tarif **Standard** zu ändern.

> [AZURE.NOTE] Bevor Sie eine App vom Tarif **Free** auf den Tarif **Standard** umstellen, müssen Sie das für Ihr Abonnement geltende Ausgabenlimit aufheben. Andernfalls besteht das Risiko, dass Ihre App nicht mehr verfügbar ist, wenn Sie Ihr Ausgabenlimit vor Ende des Abrechnungszeitraums erreichen. Weitere Informationen zu den Preisen für die Tarife „Shared“ und **Standard** finden Sie in der [Preisübersicht][pricing].

1.	Öffnen Sie in Ihrem Browser das [Azure-Portal](https://portal.azure.com).
	
2.	Klicken Sie auf der Seite links auf die Option **App Service**.

4.	Klicken Sie auf den Namen der App.

5.	Klicken Sie auf der Seite **Essentials** auf **Einstellungen**.

6.	Klicken Sie auf **Zentral hochskalieren**.
	
	![Die Registerkarte "Skalierung"][scale]

7.	Legen Sie im Abschnitt **Zentral hochskalieren** den Modus für den App Service-Plan fest, indem Sie auf **Auswählen** klicken.

	> [AZURE.NOTE] Wenn die Fehlermeldung "Fehler beim Konfigurieren der Skalierung für Web-App '&lt;App-Name&gt;'" angezeigt wird, können Sie mit der Schaltfläche "Details" weitere Informationen abrufen. Eventuell wird die Fehlermeldung "Not enough available standard instance servers to satisfy this request." angezeigt. Wenn diese Fehlermeldung angezeigt wird, rufen Sie [Supportoptionen für Azure](/support/options/) auf.

<a name="bkmk_configuressl"></a>
## 3\. Konfigurieren von SSL in Ihrer App

Bevor Sie die Schritte in diesem Abschnitt ausführen, müssen Sie einen benutzerdefinierten Domänennamen mit Ihrer App verknüpft haben. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Web-App][customdomain].

1.	Öffnen Sie in Ihrem Browser das [Azure-Portal](https://portal.azure.com).

2.	Klicken Sie auf der Seite links auf die Option **App Service**.

4.	Klicken Sie auf den Namen der App.

5.	Klicken Sie auf der Seite **Essentials** auf **Einstellungen**.

6.	Klicken Sie auf **Benutzerdefinierte Domänen und SSL**.

	![Die Registerkarte "Konfiguration"][configure]

7.	Klicken Sie im Bereich **Zertifikate** auf **Hochladen**.

8.	Wählen Sie mit dem Dialogfeld **Zertifikat hochladen** die zuvor mit dem IIS-Manager oder mit OpenSSL erstellte PFX-Zertifikatdatei aus. Geben Sie ggf. das Kennwort ein, mit dem die PFX-Datei geschützt wurde. Klicken Sie abschließend auf **Speichern**, um das Zertifikat hochzuladen.

	![Hochladen des SSL-Zertifikats][uploadcert]

9. Wählen Sie auf der Registerkarte **SSL-Einstellungen** im Abschnitt **SSL-Bindungen** mithilfe der Dropdownlisten den Domänennamen, der mit SSL geschützt werden soll, sowie das zu verwendende Zertifikat aus. Sie können auch auswählen, ob die SSL auf Basis der [Servernamensanzeige][sni] (Server Name Indication, SNI) oder eine IP-basierte SSL verwendet werden soll.

	![SSL-Bindungen][sslbindings]

	* Bei IP-basiertem SSL wird ein Zertifikat mit einem Domänennamen verknüpft, indem die dedizierte öffentliche IP-Adresse des Servers dem Domänennamen zugeordnet wird. Voraussetzung dafür ist, dass jeder mit Ihrem Dienst verknüpfte Domänenname (contoso.com, fabricam.com usw.) eine dedizierte IP-Adresse hat. Dies ist die herkömmliche Methode der Verknüpfung von SSL-Zertifikaten mit einem Webserver.

	* SNI-basiertes SSL ist eine Erweiterung für SSL und [Transport Layer Security][tls] (TLS). Dabei können mehrere Domänen die gleiche IP-Adresse gemeinsam nutzen, während jede Domäne über eigene Sicherheitszertifikate verfügt. Die meisten modernen Browser (einschließlich Internet Explorer, Chrome, Firefox und Opera) unterstützen SNI, ältere Browser hingegen möglicherweise nicht. Weitere Informationen über SNI finden Sie im Wikipedia-Artikel [Server Name Indication][sni] (in englischer Sprache).

10. Klicken Sie auf **Speichern**, um die Änderungen zu speichern und SSL zu aktivieren.

> [AZURE.NOTE] Wenn Sie **IP-basiertes SSL** ausgewählt haben und Ihre benutzerdefinierte Domäne einen A-Datensatz verwendet, müssen Sie die folgenden zusätzlichen Schritte ausführen:
>
> 1. Nach der Konfiguration einer IP-basierten SSL-Bindung wird Ihrer App eine dedizierte IP-Adresse zugewiesen. Sie finden diese IP-Adresse auf der Seite **Dashboard** Ihrer App im Abschnitt **Auf einen Blick**. Sie wird als **Virtuelle IP-Adresse** aufgeführt:
>    
>     ![Virtuelle IP-Adresse](./media/configure-ssl-web-site/staticip.png)
>    
>     Diese IP-Adresse unterscheidet sich von der virtuellen IP-Adresse, die zuvor zum Konfigurieren des A-Datensatzes für Ihre Domäne verwendet wurde. Wenn SNI-basiertes SSL konfiguriert bzw. wenn die Verwendung von SSL nicht konfiguriert ist, wird für diesen Eintrag keine Adresse aufgeführt.
>
> 2. Ändern Sie mit den von der Domänennamen-Registrierungsstelle bereitgestellten Tools den A-Datensatz für den benutzerdefinierten Domänennamen, sodass dieser auf die im vorherigen Schritt genannte IP-Adresse verweist.


Sie sollten jetzt in der Lage sein, Ihre App über `HTTPS://` anstelle von `HTTP://` zu erreichen. Sie können auf diese Weise überprüfen, ob das Zertifikat richtig konfiguriert wurde.

<a name="bkmk_enforce"></a>
## 4\. Erzwingen von HTTPS in Ihrer App

Mit Azure App Service wird die Verwendung von HTTPS *nicht* erzwungen. Besucher können Ihre App weiterhin mithilfe von HTTP aufrufen, wodurch die Sicherheit Ihrer App aber möglicherweise beeinträchtigt wird. Wenn Sie HTTPS für Ihre App erzwingen möchten, können Sie das **URL Rewrite**-Modul verwenden. Das Modul URL Rewrite ist in Azure App Service enthalten. Damit können Sie Regeln definieren, die auf eingehende Anfragen angewendet werden, bevor diese Anfragen an Ihre Anwendung weitergegeben werden. **Es kann für Anwendungen genutzt werden, die in einer von Azure unterstützten Programmiersprache geschrieben sind.**

> [AZURE.NOTE] .NET MVC-Anwendungen sollten den [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx)-Filter anstelle von "URL Rewrite" verwenden. Weitere Informationen zur Verwendung von "RequireHttps" finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung für eine Web-App](../articles/app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
>
> Informationen zur programmatischen Weiterleitung von Anfragen, die andere Programmiersprachen und Frameworks verwenden, finden Sie in der Dokumentation für diese Technologien.

Die Regeln zu URL Rewrite sind in einer **web.config**-Datei im Stammverzeichnis Ihrer Anwendung gespeichert. Das folgende Beispiel enthält eine grundlegende URL-Umschreibungsregel, die den eingehenden Datenverkehr zur Verwendung von HTTPS zwingt.

<a name="example"></a>URL Rewrite-Beispiel "Web.Config"

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <rewrite>
	      <rules>
	        <rule name="Force HTTPS" enabled="true">
	          <match url="(.*)" ignoreCase="false" />
	          <conditions>
	            <add input="{HTTPS}" pattern="off" />
	          </conditions>
	          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
	        </rule>
	      </rules>
	    </rewrite>
	  </system.webServer>
	</configuration>

Diese Regel funktioniert durch die Rückgabe eines HTTP-Statuscode von 301 (Permanent Redirect), wenn der Benutzer eine Seite mit HTTP anfragt. Der Code 301 leitet die Anfrage an die gleiche URL weiter, die der Besucher angefragt hat, aber ersetzt den HTTP-Teil der Anfrage mit HTTPS. Beispielsweise würde HTTP://contoso.com an HTTPS://contoso.com umgeleitet werden.

> [AZURE.NOTE] Wenn Ihre Anwendung in **Node.js**, **PHP**, **Python Django** oder **Java** geschrieben ist, enthält sie wahrscheinliche keine Datei "web.config". Dennoch verwenden **Node.js**, **Python Django** und **Java** eine Datei "web.config", wenn sie in Azure App Service gehostet werden. Azure erstellt die Datei während der Bereitstellung automatisch, deshalb sehen Sie sie nicht. Wenn Sie eine solche Datei als Teil Ihrer Anwendung einfügen, wird die von Azure automatisch generierte Datei überschrieben.

###.NET

Ändern Sie die web.config-Datei bei .NET-Anwendungen für Ihre Anwendung und fügen Sie den Abschnitt **&lt;rewrite>** aus dem [Beispiel](#example) zum Abschnitt **&lt;system.WebServer>** hinzu.

Wenn Ihre web.config-Datei bereits einen Anschnitt **&lt;rewrite>** enthält, fügen Sie die **&lt;rule>** aus dem [Beispiel](#example) als ersten Eintrag im Abschnitt **&lt;rules>** hinzu.

###PHP

Speichern Sie bei PHP-Anwendungen einfach das [Beispiel](#example) als web.config-Datei im Stammverzeichnis Ihrer Anwendung, und stellen Sie die Anwendung für Ihre App erneut bereit.

###Node.js, Python Django und Java

Für Node.js-, Python Django- und Java-Apps wird automatisch eine web.config-Datei erstellt, wenn sie keine haben. Sie existiert aber nur auf dem Server, da sie während der Bereitstellung erstellt wird. Die automatisch generierte Datei enthält Einstellungen, die Azure befehlen, wie Ihre Anwendung gehostet werden soll.

Um die automatisch generierte Datei von der App abzurufen und zu ändern, führen Sie die folgenden Schritte aus.

1. Laden Sie die Datei über FTP herunter (siehe [Hochladen/herunterladen von Dateien über FTP und Sammeln von Diagnoseprotokollen](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Fügen Sie die Datei dem Stammverzeichnis Ihrer Anwendung hinzu.

3. Fügen Sie die Umschreibungsregeln mithilfe der folgenden Informationen hinzu.

	* **Node.js und Python Django**

		Die Datei „web.config“, die für Node.js- und Python Django-Anwendungen erstellt wurde, enthält bereits einen **&lt;rewrite>**-Abschnitt mit **&lt;rule>**-Einträgen, die für die ordnungsgemäße Funktion der Website benötigt werden. Um für die App die Verwendung von HTTPS zu erzwingen, fügen Sie die **&lt;rule>** aus dem Beispiel als ersten Eintrag im **&lt;rules>**-Abschnitt hinzu. Damit wird HTTPS erzwungen, während die übrigen Regeln bestehen bleiben.

	* **Java**

		Die Datei "web.config" für Java-Anwendungen mit Apache Tomcat enthält keinen Abschnitt **&lt;rewrite>**. Deshalb müssen Sie den Abschnitt **&lt;rewrite>** aus dem Beispiel zum Abschnitt **&lt;system.webServer>** hinzufügen.

4. Stellen Sie das Projekt (einschließlich der aktualisierten web.config-Datei) erneut für Azure bereit

Sobald Sie eine web.config-Datei mit einer Umschreibungsregeln zur Erzwingung von HTTPS bereitstellen, sollte sie sofort in Kraft treten und alle Anfragen zu HTTPS umleiten.

Weitere Informationen zum IIS-URL-Rewrite-Modul finden Sie unter der Dokumentation [URL-Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite).

## Weitere Ressourcen ##
- [Microsoft Azure Trust Center](/support/trust-center/security/)
- [Konfigurationsoptionen in Azure-Websites](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Aktivieren der Diagnoseprotokollierung](../articles/app-service-web/web-sites-enable-diagnostic-log.md)
- [Konfigurieren von Web-Apps in Azure App Service](../articles/app-service-web/web-sites-configure.md)
- [Azure-Verwaltungsportal](https://manage.windowsazure.com)

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, bevor Sie sich für ein Azure-Konto registrieren, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine temporäre Starter-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

[customdomain]: ../articles/app-service-web/web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png

<!----HONumber=AcomDC_0211_2016-->