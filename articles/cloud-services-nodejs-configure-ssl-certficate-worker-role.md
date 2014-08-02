<properties linkid="dev-nodejs-enablesslworker" urlDisplayName="Enable SSL worker role" pageTitle="Configure SSL for a cloud service (Node.js) worker role" metaKeywords="Node.js Azure SSL, Node.js Azure, SSL worker role" description="" metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Configuring SSL for a Node.js Application in an Azure Worker Role" authors="" solutions="" manager="" editor="" />

Konfigurieren von SSL für eine Node.js-Anwendung in einer Azure-Workerrolle
===========================================================================

Secure Socket Layer (SSL)-Verschlüsselung ist die am häufigsten verwendete Methode zur Sicherung von Daten im Internet. Im Folgenden erfahren Sie, wie Sie einen HTTPS-Endpunkt für eine Node.js-Anwendung angeben, die als Azure Cloud-Dienst in einer Workerrolle gehostet ist.

**Hinweis**

Die Schritte in diesem Beitrag gelten nur für Node-Anwendungen, die als Azure-Clouddienst in einer Workerrolle gehostet werden.

Die vorliegende Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Erstellen eines Node.js-Diensts und Veröffentlichen des Diensts in der Cloud](#step1)
-   [Schritt 2: Beziehen eines SSL-Zertifikats](#step2)
-   [Schritt 3: Ändern der Anwendung, um das SSL-Zertifikat zu verwenden](#step3)
-   [Schritt 4: Ändern der Dienstdefinitionsdatei](#step4)
-   [Schritt 5: Verbinden mit der Rolleninstanz über HTTPS](#step5)

Schritt 1: Erstellen eines Node.js-Diensts und Veröffentlichen des Diensts in der Cloud
---------------------------------------------------------------------------------------

Mit den folgenden Schritten können Sie einen einfachen "Hello World"-Dienst in Node.js mit der Azure PowerShell erstellen:

1.  Suchen Sie im **Startmenü** oder auf dem **Startbildschirm** nach **Azure PowerShell**. Klicken Sie dann mit der rechten Maustaste auf **Azure PowerShell**, und wählen Sie **Als Administrator ausführen**.

    ![Azure PowerShell-Symbol](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png)

2.  Erstellen Sie einen neuen Dienst mit dem **New-AzureServiceProject**-Cmdlet.

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png)

3.  Fügen Sie mit dem **Add-AzureNodeWorkerRole**-Cmdlet eine Workerrolle zu Ihrem Dienst hinzu:

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png)

4.  Veröffentlichen Sie Ihren Dienst mit dem **Publish-AzureServiceProject**-Cmdlet in der Cloud:

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png)

    **Hinweis**

    Falls Sie noch nie Veröffentlichungseinstellungen für Ihr Azure-Abonnement importiert haben, erhalten Sie beim Veröffentlichen eine Fehlermeldung. Informationen zum Download und Import der Veröffentlichungseinstellungen für Ihr Abonnement finden Sie unter [Verwenden von Azure PowerShell für Node.js](https://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)

Der vom **Publish-AzureServiceProject**-Cmdlet zurückgegebene Wert für **Created Website URL** enthält den vollqualifizierten Domänennamen für Ihre gehostete Anwendung. Sie müssen ein SSL-Zertifikat für diesen vollqualifizierten Domänennamen beziehen und in Azure bereitstellen.

Schritt 2: Beziehen eines SSL-Zertifikats
-----------------------------------------

Sie müssen zuerst ein SSL-Zertifikat beziehen, um SSL für eine Anwendung zu konfigurieren. Dieses muss von einer Zertifizierungsstelle, einem vertrauenswürdigen Dritten, der Zertifikate für diesen Zweck ausgibt, ausgegeben werden. Wenn Sie noch kein Zertifikat haben, müssen Sie eines von einem Unternehmen erwerben, das SSL-Zertifikate verkauft.

Das Zertifikat muss die folgenden Anforderungen für SSL-Zertifikate in Azure erfüllen:

-   Das Zertifikat muss einen privaten Schlüssel enthalten.
-   Das Zertifikat muss für den Schlüsselaustausch erstellt werden (**.pfx**-Datei).
-   Der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf den Clouddienst zugegriffen wird. Sie können kein SSL-Zertifikat für die Domäne cloudapp.net beziehen, daher muss der Name des Antragstellers für das Zertifikat der Domäne entsprechen, über die auf Ihre Anwendung zugegriffen wird. Beispiel: **mysecuresite.cloudapp.net**.
-   Das Zertifikat muss mindestens eine 2048-Bit-Verschlüsselung haben.

Die **.pfx**-Datei mit dem Zertifikat wird zu Ihrem Dienstprojekt hinzugefügt und mit den folgenden Schritten in Azure bereitgestellt.

Schritt 3: Ändern der Anwendung, um das SSL-Zertifikat zu verwenden
-------------------------------------------------------------------

Wenn eine Node.js-Anwendung in einer Workerrolle bereitgestellt wird, verwaltet Node.exe das Serverzertifikat und die SSL-Verbindung. Für die Behandlung von SSL-Datenverkehr müssen Sie das 'https'-Modul anstelle von 'http' verwenden. Führen Sie die folgenden Schritte aus, um das SSL-Zertifikat zu Ihrem Projekt hinzuzufügen und ändern Sie anschließend die Anwendung, um das Zertifikat zu verwenden.

1.  Speichern Sie die **.pfx**-Datei, die Sie von Ihrer Zertifizierungsstelle (ZS) erhalten haben, im Verzeichnis, das Ihre Anwendung enthält. **c:\\node\\securesite\\workerrole1** ist z. B. das Verzeichnis, das die in diesem Artikel verwendete Anwendung enthält.

2.  Öffnen Sie die Datei **c:\\node\\securesite\\workerrole1\\server.js** mit Notepad.exe und ersetzen Sie deren Inhalt durch den folgenden Code:

    var https = require('https'); var fs = require('fs');

    var options = { pfx: fs.readFileSync('certificate.pfx'), passphrase: "password" }; var port = process.env.PORT || 8000; https.createServer(options, function (req, res) { res.writeHead(200, { 'Content-Type': 'text/plain' }); res.end('Hello World\\n'); }).listen(port);

<!-- -->

    <div class="dev-callout">
    <strong>Note</strong>
    <p>Ersetzen Sie 'certificate.pfx' durch den Namen der Zertifikatsdatei und "password" durch das Kennwort (falls vorhanden) der Zertifikatsdatei.</p>
    </div>

1.  Speichern Sie die **server.js**-Datei.

Die Änderungen an **server.js** bewirken, dass die Anwendung auf Port 443 (dem Standardport für SSL-Kommunikation) auf Kommunikation wartet, wenn diese in Azure bereitgestellt wird. Die **.pfx**-Datei wird zur Implementierung von SSL-Kommunikation über diesen Transportweg verwendet.

Schritt 4: Ändern der Dienstdefinitionsdatei
--------------------------------------------

Da Ihre Anwendung nun auf Port 443 auf Kommunikation wartet, müssen Sie ebenfalls die Dienstdefinition ändern, um Kommunikation über diesen Port zu erlauben.

1.  Öffnen Sie die Dienstdefinitionsdatei (**ServiceDefinition.csdef**) im Dienstverzeichnis, ändern Sie das http **InputEndpoint**-Element innerhalb des **Endpoints**-Bereichs, um Kommunikation über Port 443 zu erlauben:

        <WorkerRole name="WorkerRole1" vmsize="Small">
            ...
            <Endpoints>
                    <InputEndpoint name="HttpIn" protocol="tcp" 
                        port="443" />
                </Endpoints>
            ...
        </WorkerRole>

    Speichern Sie die Datei **ServiceDefinition.csdef** anschließend.

2.  Veröffentlichen Sie Ihren Dienst erneut, um die neue Konfiguration in der Cloud zu aktualisieren. Geben Sie in der Azure PowerShell-Eingabeaufforderung **Publish-AzureServiceProject** für das Dienstverzeichnis ein.

Schritt 5: Verbinden mit der Rolleninstanz über HTTPS
-----------------------------------------------------

Jetzt wird die Bereitstellung in Azure ausgeführt und Sie können eine HTTPS-Verbindung herstellen.

1.  Wählen Sie im Verwaltungsportal Ihren Cloud-Dienst aus und klicken Sie auf **Dashboard**.

2.  Blättern Sie nach unten und klicken Sie auf den Link, der als **Site URL** angezeigt wird:

    ![Website-URL](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png)

    **Hinweis**

    Falls die im Portal angezeigte URL kein HTTPS verwendet, müssen Sie die URL im Browser manuell von HTTP zu HTTPS ändern.

3.  Ein neues Browserfenster wird geöffnet und zeigt Ihre Website an.

    Ihr Browser zeigt ein Schlosssymbol an, um anzuzeigen, dass es sich um eine HTTPS-Verbindung handelt. Dies bedeutet ebenfalls, dass Ihre Anwendung korrekt für SSL konfiguriert wurde.

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png)

Zusätzliche Ressourcen
----------------------

[Verknüpfen eines Zertifikats mit einem Dienst](http://msdn.microsoft.com/en-us/library/windowsazure/gg465718.aspx)

[Konfigurieren von SSL für eine Node.js-Anwendung in einer Azure-Webrolle](/en-us/develop/nodejs/common-tasks/enable-ssl/)

[Konfigurieren eines SSL-Zertifikats für einen HTTPS-Endpunkt](http://msdn.microsoft.com/en-us/library/windowsazure/ff795779.aspx)

