
# Aktivieren von HTTPS für eine Azure-Website

Wenn ein Benutzer Ihre Website über HTTPS besucht, wird die Kommunikation zwischen der Website und dem Browser mit der Secure Socket Layer (SSL)-Verschlüsselung geschützt. Dies ist die gängigste Methode zum Schützen von Daten, die über das Internet gesendet werden, und bietet Besuchern die Sicherheit, dass ihre Transaktionen mit Ihrer Website sicher sind. In diesem Artikel wird erläutert, wie HTTPS für eine Azure-Website aktiviert wird.

> [WACOM.NOTE] Um HTTPS für benutzerdefinierte Domänennamen zu aktivieren, müssen Sie Ihre Websites für den Standardmodus konfigurieren. Dadurch entstehen eventuell zusätzliche Kosten, wenn Sie derzeit den kostenlosen oder Freigabemodus verwenden. Weitere Informationen über Preise für den Freigabe- oder Standardmodus finden Sie unter [Preisübersicht][1].

[](bkmk_azurewebsites)<h2 data-morhtml="true">Die Domäne *.azurewebsites.net</h2>

Wenn Sie keinen benutzerdefinierten Domänennamen verwenden möchten, sondern stattdessen die Domäne *.azurewebsites.net verwenden möchten, die Ihrer Website von Azure zugewiesen wird (z. B. contoso.azurewebsites.net), wird Ihre Website bereits durch ein von Microsoft bereitgestelltes Zertifikat geschützt. Sie können **https://meinewebsite.azurewebsites.net** für den geschützten Zugriff auf Ihre Website verwenden.

In den folgenden Abschnitten dieses Dokuments wird detailliert erläutert, wie HTTPS für benutzerdefinierte Domänennamen wie **contoso.com**, **www.contoso.com** oder **\*.contoso.com** aktiviert wird.

[](bkmk_domainname)<h2 data-morhtml="true">Benutzerdefinierte Domänennamen</h2>

Wenn Sie HTTPS für einen benutzerdefinierten Domänennamen wie **contoso.com** aktivieren möchten, müssen Sie einen benutzerdefinierten Domänennamen bei einer Domänennamen-Registrierungsstelle registrieren. Weitere Informationen zum Konfigurieren des Domänennamens einer Azure-Website finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](/en-us/develop/net/common-tasks/custom-dns-web-site/). Nachdem Sie einen benutzerdefinierten Domänennamen registriert und Ihre Website zur Reaktion auf den benutzerdefinierten Namen konfiguriert haben, müssen Sie ein SSL-Zertifikat für die Domäne anfordern.

Wenn Sie einen Domänennamen registrieren, können Sie auch Unterdomänen wie **www.contoso.com** oder **mail.contoso.com** erstellen. Bevor Sie ein SSL-Zertifikat anfordern, müssen Sie zuerst festlegen, welche Domänennamen durch das Zertifikat geschützt werden. Davon hängt ab, welchen Typ von Zertifikat Sie benötigen. Wenn Sie lediglich einen einzelnen Domänennamen wie **contoso.com** oder **www.contoso.com** schützen müssen, reicht wahrscheinlich ein Basiszertifikat aus. Wenn Sie mehrere Domänennamen wie **contoso.com**, **www.contoso.com** und **mail.contoso.com** schützen müssen, wird ein Platzhalterzertifikat oder ein Zertifikat mit einem alternativen Antragstellernamen (subjectAltName, SAN) benötigt.

> [WACOM.NOTE] In den meisten Browsern wird eine Warnung angezeigt, wenn der im Zertifikat angegebene Domänenname nicht mit dem im Browser eingegebenen Domänennamen übereinstimmt. Wenn im Zertifikat z. B. nur
> www.contoso.com aufgeführt wird, der Zugriff auf die Website in
> Internet Explorer jedoch über den Domänennamen login.contoso.com
> erfolgt, wird folgende Warnung angezeigt: "Das Sicherheitszertifikat
> dieser Website wurde für die Adresse einer anderen Website
> ausgestellt."

**Basiszertifikate** sind Zertifikate, bei denen der allgemeine Name (Common Name, CN) im Zertifikat auf die spezifische Domäne oder Unterdomäne eingestellt ist, die Clients zum Besuchen der Website verwenden, zum Beispiel **www.contoso.com**. Diese Zertifikate schützen nur den vom CN angegebenen einzelnen Domänennamen.

**Platzhalterzertifikate** sind Zertifikate, bei denen der CN des Zertifikats den Platzhalter '\*' auf Unterdomänenebene enthält. So kann das Zertifikat für eine bestimmte Domäne einer einzelnen Unterdomänenebene entsprechen. Ein Platzhalterzertifikat für **\*.contoso.com** gilt beispielsweise für **www.contoso.com**, **payment.contoso.com** und **login.contoso.com**. Es gilt hingegen nicht für **test.login.contoso.com**, da hier eine zusätzliche Unterdomänenebene hinzukommt. Es gilt auch nicht für **contoso.com**, da es sich hierbei um die Stammdomänenebene und nicht um eine Unterdomäne handelt.

Microsoft stellt für den Domänennamen *.azurewebsites.net, der für Ihre Website automatisch erstellt wird, ein Platzhalterzertifikat bereit.

**subjectAltName** ist eine Zertifikatserweiterung, die es ermöglicht, mit einem Zertifikat verschiedene Werte oder alternative Antragstellernamen zu verknüpfen. Bei SSL-Zertifikaten können Sie zusätzliche DNS-Namen hinzufügen, für die das Zertifikat gültig ist. Ein Zertifikat, das subjectAltName verwendet, kann z. B. den CN **contoso.com** haben, aber auch alternative Namen wie **www.contoso.com**, **payment.contoso.com**, **test.login.contoso.com** und sogar **fabrikam.com**. Ein solches Zertifikat ist für alle Domänennamen gültig, die im allgemeinen Namen und in subjectAltName angegeben sind.

Ein Zertifikat kann sowohl Platzhalter als auch subjectAltName unterstützen.

[](bkmk_getcert)<h2 data-morhtml="true">Erhalten eines Zertifikats</h2>

Für Azure-Websites verwendete SSL-Zertifikate müssen von einer Zertifizierungsstelle signiert sein, einem vertrauenswürdigen Dritten, der Zertifikate für diesen Zweck ausgibt. Wenn Sie noch kein Zertifikat haben, müssen Sie eines von einem Unternehmen erwerben, das SSL-Zertifikate verkauft. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][2] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] -- in englischer Sprache) im Microsoft TechNet Wiki.

Das Zertifikat muss die folgenden Anforderungen für SSL-Zertifikate in Azure erfüllen:

* Das Zertifikat muss einen privaten Schlüssel enthalten.

* Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.

* Der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf die Website zugegriffen wird. Wenn dieses Zertifikat für mehrere Domänen gelten soll, müssen Sie, wie oben erläutert, einen Platzhalterwert verwenden oder Werte für alternative Antragstellernamen angeben.
  
  * Informationen zum Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](/en-us/develop/net/common-tasks/custom-dns-web-site/).


 
  > [WACOM.NOTE] Versuchen Sie nicht, für die Domäne azurewebsites.net
  > ein Zertifikat zu erhalten oder zu generieren.

* Das Zertifikat muss mindestens eine 2048-Bit-Verschlüsselung haben.

> [WACOM.NOTE] Von Servern privater Zertifizierungsstellen ausgegebene
> Zertifikate werden von Azure-Websites nicht unterstützt.

Um von einer Zertifizierungsstelle ein SSL-Zertifikat zu erhalten, müssen Sie eine Zertifikatsignieranforderung (Certificate Signing Request, CSR) generieren, die an die Zertifizierungsstelle gesendet wird. Die Zertifizierungsstelle sendet ein Zertifikat zurück, mit dem die CSR vervollständigt wird. Zwei gängige Möglichkeiten zum Generieren einer CSR sind die Verwendung von certmgr.exe oder von
[OpenSSL][3]-Anwendungen. Certmgr.exe steht nur unter Windows zur
Verfügung, während OpenSSL für die meisten Plattformen verfügbar ist. Eine Anleitung zu diesen beiden Dienstprogrammen finden Sie weiter unten.

Eventuell müssen Sie auch **Zwischenzertifikate** (auch Kettenzertifikate genannt) beziehen, wenn diese von Ihrer Zertifizierungsstelle verwendet werden. Zwischenzertifikate gelten als sicherer als 'nicht verkettete Zertifikate', sodass sie von Zertifizierungsstellen üblicherweise verwendet werden. Zwischenzertifikate werden auf der Website der Zertifizierungsstelle häufig als separater Download bereitgestellt. In diesem Artikel wird dargestellt, wie Sie sicherstellen können, dass Zwischenzertifikate mit dem Zertifikat zusammengeführt werden, das zu Ihrer Azure-Website hochgeladen wurde.

> [WACOM.NOTE] Bei jeder dieser Vorgehensweisen werden Sie aufgefordert,
> einen **allgemeinen Namen** einzugeben. Wenn Sie ein
> Platzhalterzertifikat für mehrere Domänen (www.contoso.com,
> sales.contoso.com) erhalten, muss dieser Wert *.domänenname (z. B.
> *.contoso.com) lauten. Wenn Sie ein Zertifikat für einen einzelnen
> Domänenname erhalten, muss dieser Wert genau mit dem Wert
> übereinstimmen, den die Benutzer im Browser eingeben, um Ihre Website
> zu besuchen, zum Beispiel www.contoso.com.
> 
> Wenn Sie einen Platzhalternamen wie *.contoso.com und einen
> Stammdomänennamen wie contoso.com unterstützen müssen, können Sie ein
> Platzhalterzertifikat für einen alternativen Antragstellernamen
> verwenden. Ein Beispiel zum Erstellen einer Zertifikatanforderung,
> welche die SubjectAltName-Erweiterungen verwendet, finden Sie unter
> [SubjectAltName-Zertifikat](#bkmk_subjectaltname).
> 
> Weitere Informationen zum Konfigurieren des Domänennamens einer
> Azure-Website finden Sie unter [Konfigurieren eines
> benutzerdefinierten Domänennamens für eine
> Azure-Website](/en-us/develop/net/common-tasks/custom-dns-web-site/).

### Beziehen eines Zertifikats mit Certreq.exe (nur Windows)

Certreq.exe ist ein Windows-Dienstprogramm zum Erstellen von Zertifikatanforderungen. Es gehört seit Windows XP/Windows Server 2000 zur Windows-Basisinstallation und dürfte daher auf Windows-Systemen jüngeren Datums verfügbar sein. Gehen Sie folgendermaßen vor, um mit certreq.exe ein SSL-Zertifikat zu erhalten.

Informationen zum Erstellen eines selbstsignierten Zertifikats für Testzwecke finden Sie im Abschnitt [Selbstsignierte Zertifikate](#bkmk_selfsigned) dieses Dokuments.

Informationen zum Erstellen einer Zertifikatanforderung mit dem IIS-Manager finden Sie unter [Erhalten eines Zertifikats mit dem IIS-Manager](#bkmk_iismgr).

1.  Öffnen Sie den **Editor**, und erstellen Sie ein neues Dokument mit folgendem Inhalt. Ersetzen Sie **mysite.com** in der Zeile "Subject" durch den benutzerdefinierten Domänennamen Ihrer Website. Zum Beispiel: Subject = "CN=www.contoso.com".
    
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
    
    Weitere Informationen über die oben angegebenen Optionen sowie über
    weitere verfügbare Optionen finden Sie in der
    [Certreq-Referenzdokumentation][4] (in englischer Sprache).

2.  Speichern Sie die Textdatei unter **myrequest.txt**.

3.  Führen Sie auf der **Startseite** oder im **Startmenü** **cmd.exe** aus.

4.  Geben Sie an der Eingabeaufforderung den folgenden Befehl ein, um die Zertifikatanforderungsdatei zu erstellen:
    
         certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr
    
    Geben Sie den Pfad zu der in Schritt 1 erstellten Datei **myrequest.txt** an sowie den Pfad, der beim Erstellen der Datei **myrequest.csr** verwendet werden soll.

5.  Senden Sie die Datei **myrequest.csr** an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Dazu müssen Sie die Datei eventuell hochladen oder im Editor öffnen und den Inhalt direkt in ein Webformular eingeben.
    
    Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][2] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm[Mitgliedszertifizierungsstellen] -- in englischer Sprache) im Microsoft TechNet Wiki.

6.  Wenn die Zertifizierungsstelle Ihnen eine Zertifikatdatei (.CER) zur Verfügung gestellt hat, speichern Sie diese Datei auf dem Computer, mit dem die Anforderung generiert wurde. Verwenden Sie anschließend den folgenden Befehl, um die Anforderung zu akzeptieren und den Prozess der Zertifikatgenerierung abzuschließen.
    
         certreq -accept -user mycert.cer
    
    In diesem Fall dient das Zertifikat **mycert.cer**, das Sie von der Zertifizierungsstelle erhalten haben, dazu, die Signatur des Zertifikats zu vervollständigen. Es wird keine Datei erstellt, sondern das Zertifikat wird im Windows-Zertifikatspeicher abgelegt.

7.  Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat in den nächsten Schritten exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die für Microsoft IIS bereitgestellte Version.
    
    Klicken Sie nach dem Herunterladen des Zertifikats im Explorer mit der rechten Maustaste darauf, und wählen Sie **Zertifikat installieren**. Verwenden Sie die Standardwerte im **Zertifikat-Importassistenten**, und wählen Sie **Weiter** aus, bis der Import abgeschlossen ist.

8.  Um das Zertifikat aus dem Zertifikatspeicher zu exportieren, führen Sie **certmgr.msc** auf der **Startseite** oder im **Startmenü** aus. Wenn **Zertifikat-Manager** angezeigt wird, erweitern Sie den Ordner **Persönlich**, und wählen Sie dann **Zertifikate** aus. Suchen Sie im Feld **Ausgestellt für** nach einem Eintrag mit dem benutzerdefinierten Domänennamen, für den Sie ein Zertifikat angefordert haben. Im Feld **Ausgestellt von** muss die Zertifizierungsstelle aufgeführt werden, die Sie für dieses Zertifikat verwendet haben.
    
    ![hier Bild von Zertifikat-Manager
    einfügen](./media/configure-ssl-web-site/waws-certmgr.png)

9.  Klicken Sie mit der rechten Maustaste auf das Zertifikat, und wählen Sie **Alle Aufgaben** und anschließend **Exportieren**. Klicken Sie im **Zertifikat-Exportassistenten** auf **Weiter**, und wählen Sie dann **Ja, privaten Schlüssel exportieren**. Klicken Sie auf **Weiter**.
    
    ![Privaten Schlüssel
    exportieren](./media/configure-ssl-web-site/waws-certwiz1.png)

10. Wählen Sie **Persönlicher Informationsaustausch - PKCS #12**, **Alle Zertifikate in der Zertifikatkette berücksichtigen** und **Alle erweiterten Eigenschaften exportieren**. Klicken Sie auf **Weiter**.
    
    ![Alle Zertifikate und erweiterten Eigenschaften
    berücksichtigen](./media/configure-ssl-web-site/waws-certwiz2.png)

11. Wählen Sie **Kennwort** aus. Geben Sie das Kennwort ein, und bestätigen Sie es. Klicken Sie auf **Weiter**.
    
    ![Kennwort
    angeben](./media/configure-ssl-web-site/waws-certwiz3.png)

12. Geben Sie einen Pfad und Dateinamen an, in dem das exportierte Zertifikat enthalten ist. Der Dateiname muss die Erweiterung **.pfx** haben. Klicken Sie auf **Weiter**, um den Prozess abzuschließen.
    
    ![Dateipfad
    angeben](./media/configure-ssl-web-site/waws-certwiz4.png)

Jetzt können Sie die exportierte PFX-Datei zu Ihrer Azure-Website hochladen.

### Erhalten eines Zertifikats mit OpenSSL

1.  Generieren Sie einen privaten Schlüssel und eine Zertifikatsignieranforderung, indem Sie Folgendes in einer Befehlszeile, einer Bash- oder Terminalsitzung eingeben:
    
         openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2.  Geben Sie bei Aufforderung die entsprechenden Informationen ein. Zum Beispiel:

		Ländername (Code aus zwei Buchstaben)
        Name des Bundeslands/der Provinz (vollständiger Name) []: Washington
        Ortsname (z. B. Stadt) []: Redmond
        Organisationsname (z. B. Firma) []: Microsoft
        Name der Organisationseinheit (z. B. Abteilung) []: Azure
        Allgemeiner Name (z. B. Ihr Name) []: www.microsoft.com
        E-Mail-Adresse []:
    
    	Geben Sie die folgenden 'zusätzlichen' Attribute ei, die mit der Zertifikatanforderung gesendet werden sollen
    
       	ein abzurufendes Kennwort []: 
    
    Nach Abschluss dieses Prozesses haben Sie zwei Dateien: **myserver.key** und **server.csr**. Die Datei **server.csr** enthält die Zertifikatsignieranforderung.

1.  Senden Sie die Zertifikatsignieranforderung an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][2] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] -- in englischer Sprache) im Microsoft TechNet Wiki.

2.  Sobald Sie ein Zertifikat von einer Zertifizierungsstelle erhalten haben, speichern Sie es in einer Datei mit dem Namen **myserver.crt**. Wenn die Zertifizierungsstelle das Zertifikat im Textformat bereitgestellt hat, fügen Sie den Zertifikattext einfach in die Datei **myserver.crt** ein. In einem Texteditor sieht der Dateiinhalt etwa folgendermaßen aus:
    
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

3.  Verwenden Sie in der Befehlszeile, der Bash- oder Terminalsitzung den folgenden Befehl, um **myserver.key** und **myserver.crt** in **myserver.pfx** zu konvertieren, das Format, das von Azure-Websites benötigt wird:
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    Geben Sie bei der entsprechenden Aufforderung ein Kennwort ein, um die PFX-Datei zu schützen.
    
    <div  class="dev-callout" markdown="1">

**Hinweis**
Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat im nächsten Schritt exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die Version, die als PEM-Datei(Dateierweiterung .pem) bereitgestellt wird.


Der folgende Befehl zeigt, wie eine PFX-Datei erstellt wird, die Zwischenzertifikate enthält, die in der Datei
**intermediate-cets.pem** enthalten sind:



		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem



</div>

    
Nach der Ausführung dieses Befehls verfügen Sie über die Datei **myserver.pfx**, die für Azure-Websites geeignet ist.

[](bkmk_standardmode)<h2 data-morhtml="true">Konfigurieren des Standardmodus</h2>

Die Aktivierung von HTTPS für eine benutzerdefinierte Domäne steht nur für den Standardmodus von Azure-Websites zur Verfügung. Gehen Sie folgendermaßen vor, um zum Standardmodus zu wechseln.

> [WACOM.NOTE] Bevor Sie eine Website vom kostenlosen Websitemodus zum
> Standard-Websitemodus umschalten, müssen Sie die für Ihr
> Website-Abonnement geltende Ausgabekapazität entfernen. Andernfalls
> besteht das Risiko, dass Ihre Website nicht mehr verfügbar ist, wenn
> Sie Ihre Ausgabekapazität vor dem Ende der Abrechnungsperiode
> erreichen. Weitere Informationen über Preise für den Freigabe- oder
> Standardmodus finden Sie unter [Preisübersicht][1].

1.  Öffnen Sie das [Verwaltungsportal][5] in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Website.
    
    ![Website
    auswählen](./media/configure-ssl-web-site/sslwebsite.png)

3.  Klicken Sie auf die Registerkarte **SCALE**.
    
    ![Die Registerkarte
    "Skalierung"](./media/configure-ssl-web-site/sslscale.png)

4.  Legen Sie im Abschnitt **Allgemein** den Websitemodus durch Klicken auf **Standard** fest.
    
    ![Standardmodus
    ausgewählt](./media/configure-ssl-web-site/sslreserved.png)

5.  Klicken Sie auf **Speichern**. Klicken Sie bei der entsprechenden Aufforderung auf **Ja**.


   
    > [WACOM.NOTE] Wenn die Fehlermeldung "Configuring scale for web
    > site '<site name>' failed" angezeigt wird, können Sie mit
    > der Schaltfläche "Details" weitere Informationen abrufen.
    > Eventuell wird die Fehlermeldung "Not enough available standard
    > instance servers to satisfy this request." angezeigt. Wenn diese
    > Fehlermeldung angezeigt wird, rufen Sie [Supportoptionen für
    > Azure][6] auf.

[](bkmk_configuressl)<h2 data-morhtml="true">Konfigurieren von SSL</h2>

Bevor Sie die Schritte in diesem Abschnitt ausführen, müssen Sie mit Ihrer Azure-Website einen benutzerdefinierten Domänennamen verknüpft haben. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](/en-us/develop/net/common-tasks/custom-dns-web-site/).

1.  Öffnen Sie das [Azure Verwaltungsportal][5] in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Website, und wählen Sie die Registerkarte **Konfigurieren** aus.
    
    ![Die Registerkarte
    "Konfigurieren"](./media/configure-ssl-web-site/sslconfig.png)

3.  Klicken Sie im Abschnitt **Zertifikate** auf **Upload a Certificate**.
    
    ![Zertifikat
    hochladen](./media/configure-ssl-web-site/ssluploadcert.png)

4.  Wählen Sie mit dem Dialogfeld **Upload a Certificate** die zuvor mit dem IIS-Manager oder mit OpenSSL erstellte PFX-Zertifikatdatei aus. Geben Sie ggf. das Kennwort ein, mit dem die PFX-Datei geschützt wurde. Klicken Sie zum Schluss auf das Häkchen****, um das Zertifikat hochzuladen.
    
    ![Dialogfeld zum Hochladen des
    Zertifikats](./media/configure-ssl-web-site/ssluploaddlg.png)

5.  Wählen Sie auf der Registerkarte **Konfigurieren** im Abschnitt **SSL-Bindungen** mit den Dropdownlisten den Domänennamen, der mit SSL geschützt werden soll, sowie das zu verwendende Zertifikat aus. Sie können auch auswählen, ob die SSL auf Basis der [Servernamensanzeige][7] (Server Name Indication, SNI) oder eine IP-basierte SSL verwendet werden soll.
    
    ![SSL-Bindungen](./media/configure-ssl-web-site/sslbindings.png)
    
    * Bei IP-basiertem SSL wird ein Zertifikat mit einem Domänennamen verknüpft, indem die dedizierte öffentliche IP-Adresse des Servers dem Domänennamen zugeordnet wird. Voraussetzung dafür ist, dass jeder mit Ihrem Dienst verknüpfte Domänenname (contoso.com, fabricam.com usw.) eine dedizierte IP-Adresse hat. Dies ist die herkömmliche Methode der Verknüpfung von SSL-Zertifikaten mit einem Webserver.
    
    * SNI-basiertes SSL ist eine Erweiterung für SSL und [Transport Layer Security][8] (TLS). Dabei können mehrere Domänen die gleiche IP-Adresse gemeinsam nutzen, während jede Domäne über eigene Sicherheitszertifikate verfügt. Die meisten modernen Browser (einschließlich Internet Explorer, Chrome, Firefox und Opera) unterstützen SNI, ältere Browser hingegen möglicherweise nicht. Weitere Informationen über SNI finden Sie im Wikipedia-Artikel [Server Name Indication][7] (in englischer Sprache).

6.  Klicken Sie auf **Speichern**, um die Änderungen zu speichern und SSL zu aktivieren.

> [WACOM.NOTE] Wenn Sie **IP based SSL** ausgewählt haben und Ihre
> benutzerdefinierte Domäne einen A-Datensatz verwendet, müssen Sie die
> folgenden zusätzlichen Schritte ausführen:
> 
> 1.  Nach der Konfiguration einer IP-basierten SSL-Bindung wird Ihrer
>     Website eine dedizierte IP-Adresse zugewiesen. Sie finden diese
>     IP-Adresse auf der Seite **Dashboard** Ihrer Website im Abschnitt
>     **quick glance**. Sie wird als **Virtuelle IP-Adresse**
>     aufgeführt:
>     
>     ![Virtuelle
>     IP-Adresse](./media/configure-ssl-web-site/staticip.png)
>     
>     Diese IP-Adresse unterscheidet sich von der virtuellen IP-Adresse,
>     die zuvor zum Konfigurieren des A-Datensatzes für Ihre Domäne
>     verwendet wurde. Wenn SNI-basiertes SSL konfiguriert bzw. wenn die
>     Verwendung von SSL nicht konfiguriert ist, wird für diesen Eintrag
>     keine Adresse aufgeführt.
> 
> 2.  Ändern Sie mit den von der Domänennamen-Registrierungsstelle
>     bereitgestellten Tools den A-Datensatz für den benutzerdefinierten
>     Domänennamen, sodass dieser auf die im vorherigen Schritt genannte
>     IP-Adresse verweist.

Jetzt sollten Sie in der Lage sein, Ihre Website über HTTPS aufzurufen, um zu überprüfen, ob das Zertifikat einwandfrei konfiguriert wurde.

[](bkmk_subjectaltname)<h2 data-morhtml="true">SubjectAltName-Zertifikate (optional)</h2>

Mit OpenSSL kann eine Zertifikatanforderung erstellt werden, welche die SubjectAltName-Erweiterung verwendet, sodass mehrere Domänennamen mit einem Zertifikat unterstützt werden können. Dazu wird jedoch eine Konfigurationsdatei benötigt. Im Folgenden wird erläutert, wie eine Konfigurationsdatei erstellt wird und wie damit ein Zertifikat angefordert wird.

1.  Erstellen Sie eine neue Datei mit dem Namen **sancert.cnf**, und verwenden Sie für die Datei den folgenden Inhalt:

		 # -------------- BEGIN custom sancert.cnf -----
         HOME = .
         oid_section = new_oids
         [ new_oids ]
         [ req ]
         default_days = 730
         distinguished_name     = req_distinguished_name
         encrypt_key = no
         string_mask = nombstr
         req_extensions = v3_req # Erweiterungen zum Hinzufügen einer Zertifikatanforderung
         [ req_distinguished_name ]
         countryName = Ländername (Code aus 2 Buchstaben)
         countryName_default = 
         stateOrProvinceName = Name des Bundeslands/der Provinz (vollständiger Name)
         stateOrProvinceName_default = 
         localityName = Ortsname (z.B. Stadt)
         localityName_default = 
         organizationalUnitName  = Name der Organisationseinheit (z. B. Abteilung)
         organizationalUnitName_default  = 
         commonName              = Ihr allgemeiner Namen (z. B. Domänenname)
         commonName_default      = www.mydomain.com
         commonName_max = 64
         [ v3_req ]
         subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com

		 # -------------- END custom sancert.cnf -----
    
    Beachten Sie die Zeile, die mit 'subjectAltName' beginnt. Ersetzen Sie die derzeit aufgelisteten Domänennamen durch die Domänennamen, die zusätzlich zum allgemeinen Namen unterstützt werden sollen. Zum Beispiel:
    
         subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com
    
    Das Feld "commonName_default" müssen Sie nicht ändern, da Sie in einem der folgenden Schritte aufgefordert werden, Ihren allgemeinen Namen einzugeben.

2.  Speichern Sie die Datei **sancert.cnf**.

3.  Generieren Sie einen privaten Schlüssel und die Zertifikatsignieranforderung mithilfe der Konfigurationsdatei sancert.cnf. Verwenden Sie in einer Bash- oder Terminalsitzung den folgenden Befehl:
    
         openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

4.  Geben Sie bei Aufforderung die entsprechenden Informationen ein. Zum Beispiel:

		Ländername (Code aus zwei Buchstaben) []: US
        Name des Bundeslands/der Provinz (vollständiger Name) []: Washington
        Ortsname (z. B. Stadt) []: Redmond
        Name der Organisationseinheit (z. B. Abteilung) []: Azure
        Ihr allgemeiner Name (z. B. Domänenname) []: www.microsoft.com
     
    
    Nach Abschluss dieses Prozesses haben Sie zwei Dateien: **myserver.key** und **server.csr**. Die Datei **server.csr** enthält die Zertifikatsignieranforderung.

1.  Senden Sie die Zertifikatsignieranforderung an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][2] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] -- in englischer Sprache) im Microsoft TechNet Wiki.

2.  Sobald Sie ein Zertifikat von einer Zertifizierungsstelle erhalten haben, speichern Sie es in einer Datei mit dem Namen **myserver.crt**. Wenn die Zertifizierungsstelle das Zertifikat im Textformat bereitgestellt hat, fügen Sie den Zertifikattext einfach in die Datei **myserver.crt** ein. In einem Texteditor sieht der Dateiinhalt etwa folgendermaßen aus:
    
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

3.  Verwenden Sie in der Befehlszeile, der Bash- oder Terminalsitzung den folgenden Befehl, um **myserver.key** und **myserver.crt** in **myserver.pfx** zu konvertieren, das Format, das von Azure-Websites benötigt wird:
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    Geben Sie bei der entsprechenden Aufforderung ein Kennwort ein, um die PFX-Datei zu schützen.
    
    <div  class="dev-callout" markdown="1">

**Hinweis**
Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat im nächsten Schritt exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur Verfügung gestellt. Wählen Sie die Version, die als PEM-Datei
(Dateierweiterung .pem) bereitgestellt wird.


Der folgende Befehl zeigt, wie eine PFX-Datei erstellt wird, die Zwischenzertifikate enthält, die in der Datei
**intermediate-cets.pem** enthalten sind:



		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem



</div>

    
Nach der Ausführung dieses Befehls verfügen Sie über die Datei **myserver.pfx**, die für Azure-Websites geeignet ist.

## <a name="bkmk_iismgr"></a>Erhalten eines Zertifikats mit dem IIS-Manager (optional)

Wenn Sie mit IIS-Manager vertraut sind, können Sie damit ein Zertifikat generieren, das für Azure-Websites verwendet werden kann.

1.  Generieren Sie mit IIS-Manager eine Zertifikatsignieranforderung (CSR), die an die Zertifizierungsstelle gesendet wird. Weitere Informationen zum Generieren einer CSR finden Sie unter [Anfordern eines Internetserverzertifikats (IIS 7)][9].

2.  Senden Sie die Zertifikatsignieranforderung an eine Zertifizierungsstelle, um ein SSL-Zertifikat zu erhalten. Eine Liste von Zertifizierungsstellen finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][2] (Windows- und Windows Phone 8-SSL-Stammzertifikatsprogramm [Mitgliedszertifizierungsstellen] -- in englischer Sprache) im Microsoft TechNet Wiki.

3.  Tragen Sie in die CSR das von der Zertifizierungsstelle bereitgestellte Zertifikat ein. Weitere Informationen zum Ausfüllen der CSR finden Sie unter [Installieren eines Internetserverzertifikats (IIS 7)][10].

4.  Wenn die Zertifizierungsstelle Zwischenzertifikate verwendet, müssen Sie diese Zertifikate installieren, bevor Sie das Zertifikat im nächsten Schritt exportieren. In der Regel werden diese Zertifikate als separater Download von der Zertifizierungsstelle und in verschiedenen Formaten für unterschiedliche Webservertypen zur
    Verfügung gestellt. Wählen Sie die für Microsoft IIS bereitgestellte Version.
    
    Klicken Sie nach dem Herunterladen des Zertifikats im Explorer mit der rechten Maustaste darauf, und wählen Sie **Zertifikat installieren**. Verwenden Sie die Standardwerte im **Zertifikat-Importassistenten**, und wählen Sie **Weiter** aus, bis der Import abgeschlossen ist.

5.  Exportieren Sie das Zertifikat aus IIS Manager. Weitere Informationen zum Exportieren des Zertifikats finden Sie unter [Exportieren eines Serverzertifikats (IIS 7)][11]. Die exportierte Datei wird später nach Azure hochgeladen, um für Ihre Azure-Website verwendet zu werden.
    
    <div  class="dev-callout" markdown="1">

	<b>Hinweis</b>
	<p>Aktivieren Sie während des Exportprozesses die Option <b>Ja, privaten Schlüssel exportieren</b>. Damit wird der private Schlüssel in das exportierte Zertifikat einbezogen.</p>


    </div>

    
    <div  class="dev-callout" markdown="1">

	<b>Hinweis</b>
	Aktivieren Sie während des Exportprozesses die Optionen <b>Alle Zertifikate im Zertifizierungspfad einbeziehen</b> und <b>Alle erweiterten Eigenschaften exportieren</b>. Damit werden alle Zwischenzertifikate in das exportierte Zertifikat einbezogen.


    </div>


[](bkmk_selfsigned)<h2 data-morhtml="true">Selbstsignierte Zertifikate (optional)</h2>

In bestimmten Fällen möchten Sie eventuell ein Zertifikat für Testzwecke erhalten und den Kauf eines Zertifikats von einer vertrauenswürdigen Zertifizierungsstelle verzögern, bis Sie die Produktion aufnehmen. Diese Lücke kann durch selbstsignierte Zertifikate gefüllt werden. Ein selbstsigniertes Zertifikat ist ein Zertifikat, das Sie erstellen und signieren, als wären Sie eine Zertifizierungsstelle. Zwar kann mit diesem Zertifikat eine Website geschützt werden, die meisten Browser geben jedoch Fehler zurück, wenn die Website besucht wird, da das Zertifikat nicht von einer vertrauenswürdigen Zertifizierungsstelle signiert wurde. Einige Browser verweigern möglicherweise sogar den Aufruf der Website.

Es gibt zwar viele Möglichkeiten, ein selbstsigniertes Zertifikat zu erstellen, dieser Artikel enthält jedoch nur Erläuterungen zur Verwendung von **makecert** und **OpenSSL**.

### Erstellen eines selbstsignierten Zertifikats mit makecert

Mit den folgenden Schritten können Sie ein Testzertifikat mit einem Windows-System erstellen, in dem Visual Studio installiert ist:

1.  Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Developer-Eingabeaufforderung**. Klicken Sie dann mit der rechten Maustaste auf **Developer-Eingabeaufforderung**, und wählen Sie **Als Administrator ausführen**.
    
    Wenn das Dialogfeld "Benutzerkontensteuerung" angezeigt wird, wählen Sie **Ja**, um fortzufahren.

2.  Geben Sie an der Developer-Eingabeaufforderung den folgenden Befehl ein, um ein neues selbstsigniertes Zertifikat zu erstellen. **serverdnsname** muss durch den DNS-Namen Ihrer Website ersetzt werden.
    
         makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048
    
    Mit diesem Befehl wird ein Zertifikat erstellt, das zwischen dem 01.01.2013 und dem 01.01.2014 gültig ist und den Speicherort im Zertifikatspeicher "CurrentUser" ablegt.

3.  Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Windows PowerShell**, und starten Sie diese Anwendung.

4.  Geben Sie an der Eingabeaufforderung von Windows PowerShell die folgenden Befehle ein, um das zuvor erstellte Zertifikat zu exportieren:
    
         $mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
         get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd
    
    Damit wird das angegebene Kennwort in $mypwd als geschützte Zeichenfolge gespeichert. Anschließend wird das Zertifikat mithilfe des DNS-Namens gesucht, der mit dem Parameter **dnsname** angegeben wird, und in die mit dem Parameter **filepath** angegebene Datei exportiert. Die exportierte Datei wird mit der geschützten Zeichenfolge geschützt, die das Kennwort enthält.

### Erstellen eines selbstsignierten Zertifikats mit OpenSSL

1.  Erstellen Sie ein neues Dokument mit dem Namen **serverauth.cnf**, und verwenden Sie für die Datei den folgenden Inhalt:
    
         [ req ]
         default_bits           = 2048
         default_keyfile        = privkey.pem
         distinguished_name     = req_distinguished_name
         attributes             = req_attributes
         x509_extensions        = v3_ca
        
         [ req_distinguished_name ]
         countryName			= Ländername (Code aus zwei Buchstaben)
         countryName_min			= 2
         countryName_max			= 2
         stateOrProvinceName		= Name des Bundeslands/der Provinz (vollständiger Name)
         localityName			= Ortsname (z. B. Stadt)
         0.organizationName		= Organisationsname (z. B. Firma)
         organizationalUnitName		= Name der Organisationseinheit (z. B. Abteilung)
         commonName			= allgemeiner Name (z. B. der Domänennamen Ihrer Website)
         commonName_max			= 64
         emailAddress			= E-Mail-Adresse
         emailAddress_max		= 40
        
         [ req_attributes ]
         challengePassword		= abzufragendes Kennwort
         challengePassword_min		= 4
         challengePassword_max		= 20
        
         [ v3_ca ]
          subjectKeyIdentifier=hash
          authorityKeyIdentifier=keyid:always,issuer:always
          basicConstraints = CA:false
          keyUsage=nonRepudiation, digitalSignature, keyEncipherment
          extendedKeyUsage = serverAuth
    
    Damit werden die Konfigurationseinstellungen festgelegt, die zum Erzeugen eines SSL-Zertifikats erforderlich sind, das von Azure-Websites verwendet werden kann.

2.  Generieren Sie ein neues selbstsigniertes Zertifikat, indem Sie Folgendes in einer Befehlszeile, einer Bash- oder Terminalsitzung eingeben:
    
         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf
    
    Damit wird unter Verwendung der in der Datei **serverauth.cnf** festgelegten Konfigurationseinstellungen ein neues Zertifikat erstellt.

3.  Verwenden Sie den folgenden Befehl, um das Zertifikat in eine PFX-Datei zu exportieren, die zu einer Azure-Website hochgeladen werden kann:
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    Geben Sie bei der entsprechenden Aufforderung ein Kennwort ein, um die PFX-Datei zu schützen.
    
    Die mit diesem Befehl erzeugte Datei **myserver.pfx** kann für Testzwecke zum Schützen der Azure-Website verwendet werden.



[1]: https://www.windowsazure.com/en-us/pricing/details/
[2]: http://go.microsoft.com/fwlink/?LinkID=269988
[3]: http://www.openssl.org/
[4]: http://technet.microsoft.com/library/cc725793.aspx
[5]: https://manage.windowsazure.com/
[6]: http://www.windowsazure.com/en-us/support/options/
[7]: http://en.wikipedia.org/wiki/Server_Name_Indication
[8]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[9]: http://technet.microsoft.com/en-us/library/cc732906(WS.10).aspx
[10]: http://technet.microsoft.com/en-us/library/cc771816(WS.10).aspx
[11]: http://technet.microsoft.com/en-us/library/cc731386(WS.10).aspx