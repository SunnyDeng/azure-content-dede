#Aktivieren von HTTPS für eine Azure-Website

> [WACOM.NOTE]
> Schneller ans Ziel kommen - mit der NEUEN [Komplettanleitung für Azure](http://support.microsoft.com/kb/2990804).  Mit dieser Anleitung wird das Zuordnen eines benutzerdefinierten Domänennamens zu Azure-Clouddiensten und Azure-Websites sowie das Absichern der Kommunikation mittels SSL zum Kinderspiel.

Sie können die Kommunikation zwischen Website und Browser mit HTTPS sichern, wobei Secure Socket Layer (SSL)-Verschlüsselung verwendet wird. Dies ist die gängigste Methode zum Schützen von Daten, die über das Internet gesendet werden, und bietet Besuchern die Sicherheit, dass ihre Transaktionen mit Ihrer Website sicher sind. In diesem Artikel wird erläutert, wie Sie HTTPS für eine Azure-Website konfigurieren. 

<a href="bkmk_azurewebsites"></a><h2>HTTPS für die Domäne \*.azurewebsites.net</h2>

Wenn Sie keinen benutzerdefinierten Domänennamen verwenden möchten, sondern stattdessen die Domäne *.azurewebsites.net verwenden möchten, die Ihrer Website von Azure zugewiesen wird (z. B. contoso.azurewebsites.net), wird Ihre Website bereits durch ein von Microsoft bereitgestelltes Zertifikat geschützt, das HTTPS aktiviert. Sie können **https://mywebsite.azurewebsites.net** für den geschützten Zugriff auf Ihre Website verwenden. \*.azurewebsites.net ist jedoch eine Domäne mit Platzhalter. Wie [alle Platzhalterdomänen](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/) ist es nicht so sicher wie die Verwendung einer benutzerdefinierten Domäne durch Ihr eigenes Zertifikat. 

In den folgenden Abschnitten dieses Dokuments wird detailliert erläutert, wie HTTPS für benutzerdefinierte Domänen wie contoso.com**contoso.com**, **www.contoso.com** oder **\*.contoso.com aktiviert wird.**

<a href="bkmk_domainname"></a><h2>Aktivieren von SSL für Ihre benutzerdefinierte Domäne</h2>

Wenn Sie HTTPS für einen benutzerdefinierten Domänennamen **contoso.com** aktivieren möchten, müssen Sie zuerst einen benutzerdefinierten Domänennamen bei einer Domänennamen-Registrierungsstelle registrieren. Weitere Informationen zum Konfigurieren des Domänennamens einer Azure-Website finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](/de-de/develop/net/common-tasks/custom-dns-web-site/). Nachdem Sie einen benutzerdefinierten Domänennamen registriert und Ihre Website zur Reaktion auf den benutzerdefinierten Namen konfiguriert haben, müssen Sie ein SSL-Zertifikat für die Domäne anfordern. 

> [WACOM.NOTE] Um HTTPS für benutzerdefinierte Domänennamen zu aktivieren, müssen Sie Ihre Website für den **Standard**-Webhostingplanmodus konfigurieren. Dadurch entstehen eventuell zusätzliche Kosten, wenn Sie derzeit den kostenlosen oder Freigabemodus verwenden. Weitere Informationen zu den Preisen für Freigabe- und **Standard**modus finden Sie in der [Preisübersicht][Preisgestaltung]. 

Verfügen Sie über eine gültige benutzerdefinierte Domäne, umfasst die Aktivierung von HTTPS für Ihre Website die folgenden Schritte:

1. [Beziehen eines SSL-Zertifikats](#bkmk_getcert)
1. [Konfigurieren des Standardmodus](#bkmk_standardmode)
1. [Konfigurieren von SSL](#bkmk_configuressl)
1. [Erzwingen von HTTPS auf Ihrer Azure-Website](#bkmk_enforce)

<a href="bkmk_getcert"></a><h2>Beziehen eines SSL-Zertifikats</h2>

Bevor Sie ein SSL-Zertifikat anfordern, müssen Sie zuerst festlegen, welche Domänennamen durch das Zertifikat geschützt werden. Davon hängt ab, welchen Typ von Zertifikat Sie benötigen. Wenn Sie lediglich einen einzelnen Domänennamen wie **contoso.com** oder **www.contoso.com** schützen müssen, reicht ein Basiszertifikat aus. Wenn Sie mehrere Domänennamen wie **contoso.com**, **www.contoso.com** und **mail.contoso.com** schützen müssen, können Sie ein [Platzhalterzertifikat](http://en.wikipedia.org/wiki/Wildcard_certificate) oder ein Zertifikat mit einem [alternativen Antragstellernamen](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName) abrufen.

Mit Azure-Websites verwendete SSL-Zertifikate müssen von einer [Zertifizierungsstelle (Certificate Authority, CA)](http://en.wikipedia.org/wiki/Certificate_authority) signiert werden. Wenn Sie noch kein Zertifikat haben, müssen Sie eines von einem Unternehmen erwerben, das SSL-Zertifikate ausstellt. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] - in englischer Sprache) im Microsoft TechNet Wiki.

Das Zertifikat muss die folgenden Anforderungen für SSL-Zertifikate in Azure erfüllen:

* Das Zertifikat muss einen privaten Schlüssel enthalten.
* Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.
* Der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf die Website zugegriffen wird. Wenn dieses Zertifikat für mehrere Domänen gelten soll, müssen Sie, wie oben erläutert, einen Platzhalterwert verwenden oder Werte für alternative Antragstellernamen angeben.
* Das Zertifikat muss mindestens eine 2048-Bit-Verschlüsselung haben.
* Von Servern privater Zertifizierungsstellen ausgegebene Zertifikate werden von Azure-Websites nicht unterstützt.

Um ein SSL-Zertifikat für die Verwendung mit Azure-Websites zu erhalten, müssen Sie eine Zertifikatsignieranforderungsdatei (CSR) an eine Zertifizierungsstelle senden und anschließend eine PFX-Datei aus dem Zertifikat generieren, das Sie zurückerhalten haben. Sie können dazu das Tool Ihrer Wahl verwenden. Im Folgenden finden Sie einige der häufigsten Möglichkeiten, ein Zertifikat abzurufen:

- [Erhalten eines Zertifikats mit Certreq.exe](#bkmk_certreq)
- [Erhalten eines Zertifikats mit IIS-Manager](#bkmk_iismgr)
- [Erhalten eines Zertifikats mit OpenSSL](#bkmk_openssl)
- [Erhalten eines SubjectAltName-Zertifikats mit OpenSSL](#bkmk_subjectaltname)
- [Generieren selbstsignierter Zertifikate (nur für Testzwecke)](#bkmk_selfsigned) 

> [WACOM.NOTE] Wenn Sie die Schritte befolgen, werden Sie aufgefordert, einen **Allgemeinen Namen**, z. B. "www.contoso.com" einzugeben. Für Platzhalterzertifikate sollte dieser Wert \*.domainname (z. B. \*.contoso.com) sein. Wenn Sie einen Platzhalternamen wie *.contoso.com und einen Stammdomänennamen wie contoso.com unterstützen müssen, können Sie ein Platzhalterzertifikat für einen alternativen Antragstellernamen (subjectAltName) verwenden.

> [WACOM.NOTE] Zertifikate für die Kryptografie für elliptische Kurven (ECC) werden für Azure-Websites unterstützt; sie sind jedoch noch relativ neu, daher sollten Sie bei Ihrer Zertifizierungsstelle erfragen, wie genau Sie die CSR-Datei erstellen.

Eventuell müssen Sie auch **[Zwischenzertifikate](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (auch Kettenzertifikate genannt) beziehen, wenn diese von Ihrer Zertifizierungsstelle verwendet werden. Zwischenzertifikate gelten als sicherer als 'nicht verkettete Zertifikate', sodass sie von Zertifizierungsstellen üblicherweise verwendet werden. Zwischenzertifikate werden auf der Website der Zertifizierungsstelle häufig als separater Download bereitgestellt. In diesem Artikel wird dargestellt, wie Sie sicherstellen können, dass Zwischenzertifikate mit dem Zertifikat zusammengeführt werden, das zu Ihrer Azure-Website hochgeladen wurde. 

<a href="bkmk_certreq"></a>
###Beziehen eines Zertifikats mit Certreq.exe (nur Windows)

Certreq.exe ist ein Windows-Dienstprogramm zum Erstellen von Zertifikatanforderungen. Es gehört seit Windows XP/Windows Server 2000 zur Windows-Basisinstallation und dürfte daher auf Windows-Systemen jüngeren Datums verfügbar sein. Gehen Sie folgendermaßen vor, um mit certreq.exe ein SSL-Zertifikat zu erhalten.

1. Öffnen Sie den **Editor**, und erstellen Sie ein neues Dokument mit folgendem Inhalt. Ersetzen Sie **mysite.com** in der Zeile "Subject" durch den benutzerdefinierten Domänennamen Ihrer Website. Zum Beispiel: Subject = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		RequestType = CMC

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Weitere Informationen über die oben angegebenen Optionen sowie über weitere verfügbare Optionen finden Sie in der [Certreq-Referenzdokumentation](http://technet.microsoft.com/library/cc725793.aspx) (in englischer Sprache).

2. Speichern Sie die Textdatei unter **myrequest.txt**.

3. Führen Sie auf der **Startseite** oder im **Startmenü** **cmd.exe** aus.

4. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein, um die Zertifikatanforderungsdatei zu erstellen:

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Geben Sie den Pfad zu der in Schritt 1 erstellten Datei **myrequest.txt** an sowie den Pfad, der beim Erstellen der Datei **myrequest.csr** verwendet werden soll.

5. Senden Sie die Datei **myrequest.csr** an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Dazu müssen Sie die Datei eventuell hochladen oder im Editor öffnen und den Inhalt direkt in ein Webformular eingeben.

	Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] - in englischer Sprache) im Microsoft TechNet Wiki.

6. Wenn die Zertifizierungsstelle Ihnen eine Zertifikatdatei (.CER) zur Verfügung gestellt hat, speichern Sie diese Datei auf dem Computer, mit dem die Anforderung generiert wurde. Verwenden Sie anschließend den folgenden Befehl, um die Anforderung zu akzeptieren und den Prozess der Zertifikatgenerierung abzuschließen.

		certreq -accept -user mycert.cer

	In diesem Fall dient das Zertifikat **mycert.cer**, das Sie von der Zertifizierungsstelle erhalten haben, dazu, die Signatur des Zertifikats zu vervollständigen. Es wird keine Datei erstellt, sondern das Zertifikat wird im Windows-Zertifikatspeicher abgelegt.

6. Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat in den nächsten Schritten exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die für Microsoft IIS bereitgestellte Version.

	Klicken Sie nach dem Herunterladen des Zertifikats im Explorer mit der rechten Maustaste darauf, und wählen Sie **Zertifikat installieren**. Verwenden Sie die Standardwerte im **Zertifikat-Importassistenten**, und wählen Sie **Weiter** aus, bis der Import abgeschlossen ist.

7. Um das Zertifikat aus dem Zertifikatspeicher zu exportieren, führen Sie **certmgr.msc** auf der **Startseite** oder im **Startmenü** aus. Wenn **Zertifikat-Manager** angezeigt wird, erweitern Sie den Ordner **Persönlich**, und wählen Sie dann **Zertifikate** aus. Suchen Sie im Feld **Ausgestellt für** nach einem Eintrag mit dem benutzerdefinierten Domänennamen, für den Sie ein Zertifikat angefordert haben. Im Feld **Ausgestellt von** muss die Zertifizierungsstelle aufgeführt werden, die Sie für dieses Zertifikat verwendet haben.

	![insert image of cert manager here][certmgr]

9. Klicken Sie mit der rechten Maustaste auf das Zertifikat, und wählen Sie **Alle Aufgaben** und anschließend **Exportieren**. Klicken Sie im **Zertifikat-Exportassistenten** auf **Weiter**, und wählen Sie dann **Ja, privaten Schlüssel exportieren**. Klicken Sie auf **Weiter**.

	![Export the private key][certwiz1]

10. Wählen Sie **Persönlicher Informationsaustausch - PKCS #12**, **Alle Zertifikate in der Zertifikatkette berücksichtigen** und **Alle erweiterten Eigenschaften exportieren**. Klicken Sie auf **Weiter**.

	![include all certs and extended properties][certwiz2]

11. Wählen Sie **Kennwort** aus. Geben Sie das Kennwort ein, und bestätigen Sie es. Klicken Sie auf **Weiter**.

	![specify a password][certwiz3]

12. Geben Sie einen Pfad und Dateinamen an, in dem das exportierte Zertifikat enthalten ist. Der Dateiname muss die Erweiterung **.pfx** haben. Klicken Sie auf **Weiter**, um den Prozess abzuschließen.

	![provide a file path][certwiz4]

Jetzt können Sie die exportierte PFX-Datei zu Ihrer Azure-Website hochladen.

<a href="bkmk_openssl"></a>
###Erhalten eines Zertifikats mit OpenSSL

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

		Geben Sie die folgenden 'zusätzlichen' Attribute ei, die mit der Zertifikatanforderung gesendet werden sollen

       	Ein abzurufendes Kennwort []: 

	Nach Abschluss dieses Prozesses haben Sie zwei Dateien: **myserver.key** und **server.csr**. Die Datei **server.csr** enthält die Zertifikatsignieranforderung.

3. Senden Sie die Zertifikatsignieranforderung an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] - in englischer Sprache) im Microsoft TechNet Wiki.

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

	Speichern Sie die Datei.

5. Verwenden Sie in der Befehlszeile, der Bash- oder Terminalsitzung den folgenden Befehl, um **myserver.key** und **myserver.crt** in **myserver.pfx** zu konvertieren, das Format, das von Azure-Websites benötigt wird:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Geben Sie bei der entsprechenden Aufforderung ein Kennwort ein, um die PFX-Datei zu schützen.

	<div class="dev-callout"> 
	<b>Hinweis</b>
	<p>Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat im nächsten Schritt exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die Version, die als PEM-Datei (Dateierweiterung .pem) bereitgestellt wird.</p>
	<p>Der folgende Befehl zeigt, wie eine PFX-Datei erstellt wird, die Zwischenzertifikate enthält, die in der Datei <b>intermediate-cets.pem</b> enthalten sind:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Nach der Ausführung dieses Befehls verfügen Sie über die Datei **myserver.pfx**, die für Azure-Websites geeignet ist.

###<a name="bkmk_iismgr"></a>Erhalten eines Zertifikats mit dem IIS-Manager

Wenn Sie mit IIS-Manager vertraut sind, können Sie damit ein Zertifikat generieren, das für Azure-Websites verwendet werden kann.

1. Generieren einer CSR-Datei mit IIS-Manager für die Zertifizierungsstelle. Weitere Informationen zum Generieren einer CSR finden Sie unter [Anfordern eines Internetserverzertifikats (IIS 7)][iiscsr].

2. Senden Sie die Zertifikatsignieranforderung an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] - in englischer Sprache) im Microsoft TechNet Wiki.

3. Tragen Sie in die CSR das von der Zertifizierungsstelle bereitgestellte Zertifikat ein. Weitere Informationen zum Ausfüllen der CSR finden Sie unter [Installieren eines Internetserverzertifikats (IIS 7)][installcertiis].

4. Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat im nächsten Schritt exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die für Microsoft IIS bereitgestellte Version.

	Klicken Sie nach dem Herunterladen des Zertifikats im Explorer mit der rechten Maustaste darauf, und wählen Sie **Zertifikat installieren**. Verwenden Sie die Standardwerte im **Zertifikat-Importassistenten**, und wählen Sie **Weiter** aus, bis der Import abgeschlossen ist.

4. Exportieren Sie das Zertifikat aus IIS Manager. Weitere Informationen zum [Exportieren des Zertifikats finden Sie unter Exportieren eines Serverzertifikats (IIS 7)][exportcertiis]. Die exportierte Datei wird später nach Azure hochgeladen, um für Ihre Azure-Website verwendet zu werden.

	<div class="dev-callout"> 
	<b>Hinweis</b>
	<p>Aktivieren Sie während des Exportprozesses die Option <strong>Ja, privaten Schlüssel exportieren</strong>. Damit wird der private Schlüssel in das exportierte Zertifikat einbezogen.</p>
	</div>

	<div class="dev-callout"> 
	<b>Hinweis</b>
	<p>Aktivieren Sie während des Exportprozesses die Optionen <strong>Alle Zertifikate im Zertifizierungspfad einbeziehen</strong> und <strong>Alle erweiterten Eigenschaften exportieren</strong>. Damit werden alle Zwischenzertifikate in das exportierte Zertifikat einbezogen.</p>
	</div>


###<a href="bkmk_subjectaltname"></a>Erhalten eines SubjectAltName-Zertifikats mit OpenSSL

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

	Das Feld "commonName_default" müssen Sie nicht ändern, da Sie in einem der folgenden Schritte aufgefordert werden, Ihren allgemeinen Namen einzugeben.

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

3. Senden Sie die Zertifikatsignieranforderung an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] - in englischer Sprache) im Microsoft TechNet Wiki.

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

	Speichern Sie die Datei.

5. Verwenden Sie in der Befehlszeile, der Bash- oder Terminalsitzung den folgenden Befehl, um **myserver.key** und **myserver.crt** in **myserver.pfx** zu konvertieren, das Format, das von Azure-Websites benötigt wird:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Geben Sie bei der entsprechenden Aufforderung ein Kennwort ein, um die PFX-Datei zu schützen.

	<div class="dev-callout"> 
	<b>Hinweis</b>
	<p>Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat im nächsten Schritt exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die Version, die als PEM-Datei (Dateierweiterung .pem) bereitgestellt wird.</p>
	<p>Der folgende Befehl zeigt, wie eine PFX-Datei erstellt wird, die Zwischenzertifikate enthält, die in der Datei <b>intermediate-cets.pem</b> enthalten sind:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Nach der Ausführung dieses Befehls verfügen Sie über die Datei **myserver.pfx**, die für Azure-Websites geeignet ist.


###<a href="bkmk_selfsigned"></a>Generieren eines selbstsignierten Zertifikats (nur für Testzwecke)

In bestimmten Fällen möchten Sie eventuell ein Zertifikat für Testzwecke erhalten und den Kauf eines Zertifikats von einer vertrauenswürdigen Zertifizierungsstelle verzögern, bis Sie die Produktion aufnehmen. Diese Lücke kann durch selbstsignierte Zertifikate gefüllt werden. Ein selbstsigniertes Zertifikat ist ein Zertifikat, das Sie erstellen und signieren, als wären Sie eine Zertifizierungsstelle. Zwar kann mit diesem Zertifikat eine Website geschützt werden, die meisten Browser geben jedoch Fehler zurück, wenn die Website besucht wird, da das Zertifikat nicht von einer vertrauenswürdigen Zertifizierungsstelle signiert wurde. Einige Browser verweigern möglicherweise sogar den Aufruf der Website.

- [Generieren eines selbstsignierten Zertifikats mit makecert](#bkmk_ssmakecert)
- [Generieren eines selbstsignierten Zertifikats mit OpenSSL](#bkmk_ssopenssl)

<a href="bkmk_ssmakecert"></a>
#### Generieren eines selbstsignierten Zertifikats mit makecert

Mit den folgenden Schritten können Sie ein Testzertifikat mit einem Windows-System erstellen, in dem Visual Studio installiert ist:

1. Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Developer-Eingabeaufforderung**. Klicken Sie dann mit der rechten Maustaste auf **Developer-Eingabeaufforderung**, und wählen Sie **Als Administrator ausführen**.

	Wenn das Dialogfeld "Benutzerkontensteuerung" angezeigt wird, wählen Sie **Ja**, um fortzufahren.

2. Geben Sie an der Developer-Eingabeaufforderung den folgenden Befehl ein, um ein neues selbstsigniertes Zertifikat zu erstellen. **serverdnsname** muss durch den DNS-Namen Ihrer Website ersetzt werden.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Mit diesem Befehl wird ein Zertifikat erstellt, das zwischen dem 01.01.2013 und dem 01.01.2014 gültig ist und den Speicherort im Zertifikatspeicher "CurrentUser" ablegt.

3. Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Windows PowerShell**, und starten Sie diese Anwendung.

4. Geben Sie an der Eingabeaufforderung von Windows PowerShell die folgenden Befehle ein, um das zuvor erstellte Zertifikat zu exportieren:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	Damit wird das angegebene Kennwort in $mypwd als geschützte Zeichenfolge gespeichert. Anschließend wird das Zertifikat mithilfe des DNS-Namens gesucht, der mit dem Parameter **dnsname** angegeben wird, und in die mit dem Parameter **filepath** angegebene Datei exportiert. Die exportierte Datei wird mit der geschützten Zeichenfolge geschützt, die das Kennwort enthält.

<a href="bkmk_ssopenssl"></a>
####Erstellen eines selbstsignierten Zertifikats mit OpenSSL

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
        commonName			= Common Name (eg, your website's domain name)
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

	Damit werden die Konfigurationseinstellungen festgelegt, die zum Erzeugen eines SSL-Zertifikats erforderlich sind, das von Azure-Websites verwendet werden kann.

2. Generieren Sie ein neues selbstsigniertes Zertifikat, indem Sie Folgendes in einer Befehlszeile, einer Bash- oder Terminalsitzung eingeben:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Damit wird unter Verwendung der in der Datei **serverauth.cnf** festgelegten Konfigurationseinstellungen ein neues Zertifikat erstellt.

3. Verwenden Sie den folgenden Befehl, um das Zertifikat in eine PFX-Datei zu exportieren, die zu einer Azure-Website hochgeladen werden kann:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Geben Sie bei der entsprechenden Aufforderung ein Kennwort ein, um die PFX-Datei zu schützen.

	Die mit diesem Befehl erzeugte Datei **myserver.pfx** kann für Testzwecke zum Schützen der Azure-Website verwendet werden.

<a href="bkmk_standardmode"></a><h2> Konfigurieren des Standardmodus</h2>

Die Aktivierung von HTTPS für eine benutzerdefinierte Domäne steht nur für den **Standard**-Webhostingplanmodus von Azure-Websites zur Verfügung. Gehen Sie folgendermaßen vor, um zum **Standard**modus zu wechseln.

> [WACOM.NOTE] Bevor Sie eine Website vom kostenlosen Websitemodus zum **Standard**modus umschalten, müssen Sie die für Ihr Website-Abonnement geltende Ausgabekapazität entfernen. Andernfalls besteht das Risiko, dass Ihre Website nicht mehr verfügbar ist, wenn Sie Ihre Ausgabekapazität vor dem Ende der Abrechnungsperiode erreichen. Weitere Informationen zu den Preisen für Freigabe- und **Standard**modus finden Sie in der [Preisübersicht][Preisgestaltung].

1. Öffnen Sie in Ihrem Browser das [Verwaltungsportal][portal].

2. Klicken Sie auf der Registerkarte **Web Sites** auf den Namen Ihrer Website.

	![selecting a web site][Website]

3. Klicken Sie auf die Registerkarte **SKALIEREN**.

	![The scale tab][Skalieren]

4. Legen Sie im Abschnitt **Allgemein** den Webhostingplanmodus durch Klicken auf **STANDARD** fest.

	![standard mode selected][Standard]

5. Klicken Sie auf **Speichern**. Klicken Sie bei der entsprechenden Aufforderung auf **Ja**.

	> [WACOM.NOTE] Wenn die Fehlermeldung "Configuring scale for website '&lt;site name&gt;' failed" angezeigt wird, können Sie mit der Schaltfläche "Details" weitere Informationen abrufen. Eventuell wird die Fehlermeldung "Not enough available standard instance servers to satisfy this request." angezeigt. Wenn diese Fehlermeldung angezeigt wird, rufen Sie [Supportoptionen für Azure](http://www.windowsazure.com/de-de/support/options/) auf.


##<a href="bkmk_configuressl"></a>Konfigurieren von SSL

Bevor Sie die Schritte in diesem Abschnitt ausführen, müssen Sie mit Ihrer Azure-Website einen benutzerdefinierten Domänennamen verknüpft haben. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website][customdomain].

1. Öffnen Sie in Ihrem Browser das [Azure Verwaltungsportal][portal].

2. Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Website, und wählen Sie die Registerkarte **KONFIGURIEREN** aus.

	![the configure tab][configure]

3. Klicken Sie im Abschnitt **Zertifikate** auf **Upload a Certificate.**

	![upload a certificate][uploadcert]

4. Wählen Sie mit dem Dialogfeld **Upload a Certificate** die zuvor mit dem IIS-Manager oder mit OpenSSL erstellte PFX-Zertifikatdatei aus. Geben Sie ggf. das Kennwort ein, mit dem die PFX-Datei geschützt wurde. Klicken Sie zum Schluss auf das **Häkchen**, um das Zertifikat hochzuladen.

	![upload certificate dialog][uploadcertdlg]

5. Wählen Sie auf der Registerkarte **Konfigurieren** im Abschnitt **SSL-Bindungen** mit den Dropdownlisten den Domänennamen, der mit SSL geschützt werden soll, sowie das zu verwendende Zertifikat aus. Sie können auch auswählen, ob die SSL auf Basis der [Servernamensanzeige (Server Name Indication, SNI)][sni] oder eine IP-basierte SSL verwendet werden soll.

	![ssl bindings][sslbindings]
	
	* Bei IP-basiertem SSL wird ein Zertifikat mit einem Domänennamen verknüpft, indem die dedizierte öffentliche IP-Adresse des Servers dem Domänennamen zugeordnet wird. Voraussetzung dafür ist, dass jeder mit Ihrem Dienst verknüpfte Domänenname (contoso.com, fabricam.com usw.) eine dedizierte IP-Adresse hat. Dies ist die herkömmliche Methode der Verknüpfung von SSL-Zertifikaten mit einem Webserver.

	* SNI-basiertes SSL ist eine Erweiterung für SSL und [Transport Layer Security][tls] (TLS). Dabei können mehrere Domänen die gleiche IP-Adresse gemeinsam nutzen, während jede Domäne über eigene Sicherheitszertifikate verfügt. Die meisten modernen Browser (einschließlich Internet Explorer, Chrome, Firefox und Opera) unterstützen SNI, ältere Browser hingegen möglicherweise nicht. Weitere Informationen über SNI finden Sie im Wikipedia-Artikel [Server Name Indication][sni] (in englischer Sprache).

6. Klicken Sie auf **Speichern**, um die Änderungen zu speichern und SSL zu aktivieren.

> [WACOM.NOTE] Wenn Sie **IP based SSL** ausgewählt haben und Ihre benutzerdefinierte Domäne einen A-Datensatz verwendet, müssen Sie die folgenden zusätzlichen Schritte ausführen:
>
> 1. Nach der Konfiguration einer IP-basierten SSL-Bindung wird Ihrer Website eine dedizierte IP-Adresse zugewiesen. Sie finden diese IP-Adresse auf der Seite **Dashboard** Ihrer Website im Abschnitt **quick glance**. Sie wird als **Virtuelle IP-Adresse** aufgeführt:
>    
>     ![Virtual IP address](./media/configure-ssl-web-site/staticip.png)
>    
>     Diese IP-Adresse unterscheidet sich von der virtuellen IP-Adresse, die zuvor zum Konfigurieren des A-Datensatzes für Ihre Domäne verwendet wurde. Wenn SNI-basiertes SSL konfiguriert bzw. wenn die Verwendung von SSL nicht konfiguriert ist, wird für diesen Eintrag keine Adresse aufgeführt.
>
> 2. Ändern Sie mit den von der Domänennamen-Registrierungsstelle bereitgestellten Tools den A-Datensatz für den benutzerdefinierten Domänennamen, sodass dieser auf die im vorherigen Schritt genannte IP-Adresse verweist.


Jetzt sollten Sie in der Lage sein, Ihre Website über 'HTTPS://' statt 'HTTP://' aufzurufen, um zu überprüfen, ob das Zertifikat einwandfrei konfiguriert wurde.

##<a href="bkmk_enforce"></a>Erzwingen von HTTPS auf Ihrer Azure-Website

Azure-Websites erzwingen *nicht* HTTPS. Besucher können weiterhin auf Ihre Website mit HTTP zugreifen, was die Sicherheit der Website beeinträchtigen kann. Wenn Sie HTTPS für Ihre Website erzwingen möchten, können Sie das Modul **URL-Rewrite** verwenden. Das Modul URL Rewrite ist bei Azure-Websites enthalten. Damit können Sie Regeln definieren, die auf eingehende Anfragen angewendet werden, bevor diese Anfragen an Ihre Anwendung weitergegeben werden. **Es kann für Anwendungen verwendet werden, die in einer Programmiersprache geschrieben sind, die von Azure-Websites unterstützt wird.** 

> [WACOM.NOTE] .NET MVC-Anwendungen sollten den [RequireHttps](http://msdn.microsoft.com/de-de/library/system.web.mvc.requirehttpsattribute.aspx)-Filter anstelle von URL Rewrite verwenden. Weitere Informationen zur Verwendung von RequireHttps finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung für eine Azure-Website](/de-de/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).
> 
> Informationen zur programmatischen Weiterleitung von Anfragen, die andere Programmiersprachen und Frameworks verwenden, finden Sie in der Dokumentation für diese Technologien.

Die Regeln zu URL Rewrite sind in einer **web.config**-Datei im Stammverzeichnis Ihrer Anwendung gespeichert. Das folgende Beispiel enthält eine grundlegende URL-Umschreibungsregel, die den eingehenden Datenverkehr zur Verwendung von HTTPS zwingt.

<a name="example"></a>**Beispiel URL-Umschreibung Web.Config**

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

Diese Regel funktioniert durch die Rückgabe eines HTTP-Statuscode von 301 (Permanent Redirect), wenn der Benutzer eine Seite mit HTTP anfragt. Der Code 301 leitet die Anfrage an die gleiche URL weiter, die der Besucher angefragt hat, aber ersetzt den HTTP-Teil der Anfrage mit HTTPS. HTTP://contoso.com würde beispielsweise zu HTTPS://contoso.com weitergeleitet werden.

> [WACOM.NOTE] Wenn Ihre Anwendung in **Node.js**, **PHP**, **Python Django** oder **Java** geschrieben ist, enthält sie wahrscheinliche keine web.config-Datei. Dennoch verwenden **Node.js**, **Python Django** und **Java** eine web.config, wenn sie auf Azure-Websites gehostet werden. Azure erstellt die Datei während der Bereitstellung automatisch. Deshalb sehen Sie sie nicht. Wenn Sie eine solche Datei als Teil Ihrer Anwendung einfügen, wird die von Azure automatisch generierte Datei überschrieben.

###.NET

Ändern Sie die web.config-Datei bei .NET-Anwendungen für Ihre Anwendung und fügen Sie den Abschnitt **&lt;rewrite>** aus dem [Beispiele](#example) zum Abschnitt **&lt;system.WebServer>** hinzu.

Wenn Ihre web.config-Datei bereits einen Anschnitt **&lt;rewrite>** enthält, fügen Sie die **&lt;rule>** aus dem [Beispiel](#example) als ersten Eintrag im Abschnitt **&lt;rules>** hinzu.

###PHP

Speichern Sie bei PHP-Anwendungen einfach das [Beispiel](#example) als web.config-Datei im Stammverzeichnis Ihrer Anwendung und stellen Sie die Anwendung auf Ihrer Azure-Website erneut bereit.

###Node.js, Python Django und Java

Für Node.js-, Python Django- und Java-Anwendungen wird automatisch eine web.config-Datei erstellt, wenn sie keine haben. Sie existiert aber nur auf dem Server, da sie während der Bereitstellung erstellt wird. Die automatisch generierte Datei enthält Einstellungen, die Azure befehlen, wie Ihre Anwendung gehostet werden soll.

Um die automatisch generierte Datei von der Website abzurufen und zu ändern, verwenden Sie die folgenden Schritte.

1. Laden Sie die Datei über FTP herunter (siehe [Hochladen/herunterladen von Dateien über FTP und Sammeln von Diagnoseprotokollen](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Fügen Sie die Datei dem Stammverzeichnis Ihrer Anwendung hinzu.

3. Fügen Sie die Umschreibungsregeln mithilfe der folgenden Informationen hinzu.

	* **Node.js und Python Django**

		Die web.config-Datei, die für Node.js- und Python Django-Anwendungen erstellt wurde, hat bereits einen Abschnitt **&lt;rewrite>**, der **&lt;rule>**-Einträge enthält, die für die ordnungsgemäße Funktion der Seite benötigt werden. Um die Seite zur Verwendung von HTTPS zu zwingen, fügen Sie die **&lt;rule>** aus dem Beispiel als ersten Eintrag im Abschnitt **&lt;rules>** hinzu. Damit wird HTTPS erzwungen, während die übrigen Regeln bestehen bleiben.

	* **Java**
	
		Die web.config-Datei für Java-Anwendungen mit Apache Tomcat enthält keinen Abschnitt **&lt;rewrite>**. Deshalb müssen Sie den Abschnitt **&lt;rewrite>** aus dem Beispiel zum Abschnitt **&lt;system.webServer>** hinzufügen.

4. Stellen Sie das Projekt (einschließlich der aktualisierten web.config-Datei) erneut für Azure bereit

Sobald Sie eine web.config-Datei mit einer Umschreibungsregeln zur Erzwingung von HTTPS bereitstellen, sollte sie sofort in Kraft treten und alle Anfragen zu HTTPS umleiten.

Weitere Informationen zum IIS-URL-Rewrite-Modul finden Sie in der Dokumentation zu [URL Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite). 

## Weitere Ressourcen
- [Microsoft Azure Trust Center](/de-de/support/trust-center/security/)
- [Configuration options unlocked in Azure Web Sites (Konfigurationsoptionen in Azure-Websites, in englischer Sprache)](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Aktivieren der Diagnoseprotokollierung](/de-de/documentation/articles/web-sites-enable-diagnostic-log/)
- [Konfigurieren von Websites](/de-de/documentation/articles/web-sites-configure/)
- [Azure-Verwaltungsportal](https://manage.windowsazure.com)

[customdomain]: /de-de/develop/net/common-tasks/custom-dns-web-site/
[iiscsr]: http://technet.microsoft.com/de-de/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/de-de/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/de-de/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: https://www.windowsazure.com/de-de/pricing/details/
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
