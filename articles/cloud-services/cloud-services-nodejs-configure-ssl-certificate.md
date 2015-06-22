<properties 
	pageTitle="Konfigurieren von SSL für einen Cloud-Dienst (Node.js) – Azure" 
	description="Geben Sie einen HTTPS-Endpunkt für eine Node.js-Webrolle an, und laden Sie ein SSL-Zertifikat zur Sicherung Ihrer Anwendung hoch." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="mwasson"/>




# Konfigurieren von SSL für eine Node.js-Anwendung in einer Azure-Webrolle

Secure Socket Layer (SSL)-Verschlüsselung ist die am häufigsten verwendete Methode zur Sicherung von Daten im Internet. Im Folgenden erfahren Sie, wie Sie einen HTTPS-Endpunkt für eine Node.js-Anwendung angeben können, die als Azure Cloud-Dienst in einer Webrolle gehostet ist, und wie Sie ein SSL-Zertifikat zur Sicherung Ihrer Anwendung hochladen können.

> [AZURE.NOTE]Die Schritte in diesem Beitrag gelten nur für Node-Anwendungen, die als Azure-Cloud-Dienst in einer Webrolle gehostet werden. Weitere Informationen zu Websites finden Sie unter [Configuring an SSL certificate for an Azure website](../web-sites-configure-ssl-certificate.md) (Konfigurieren eines SSL-Zertifikats für eine Azure-Website, in englischer Sprache).

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Erstellen eines Node.js-Diensts und Veröffentlichen des Diensts in der Cloud]
-   [Schritt 2: Beziehen eines SSL-Zertifikats]
-   [Schritt 3: Importieren des SSL-Zertifikats]
-   [Schritt 4: Ändern der Definitions- und Konfigurationsdateien für den Dienst]
-   [Schritt 5: Herstellen einer Verbindung mit der Rolleninstanz über HTTPS]

## <a name="step1"> </a>Schritt 1: Erstellen eines Node.js-Diensts und Veröffentlichen des Diensts in der Cloud

Wenn eine Node.js-Anwendung in einer Azure-Webrolle bereitgestellt wird, werden das Serverzertifikat und die SSL-Verbindung von Internet Information Services (IIS) verwaltet, sodass der Node.js-Dienst geschrieben werden kann, als wäre er ein HTTP-Dienst. Mit den folgenden Schritten können Sie einen einfachen "Hello World"-Dienst in Node.js mit der Azure PowerShell erstellen:

1. Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**. Klicken Sie dann mit der rechten Maustaste auf **Azure PowerShell**, und wählen Sie **Als Administrator ausführen**.

	![Azure PowerShell-Symbol][powershell-menu]

[AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]


2.  Erstellen Sie ein neues Dienstprojekt mit dem **New-AzureServiceProject**-Cmdlet. 

	![][1]

3.  Fügen Sie mit dem **Add-AzureNodeWebRole**-Cmdlet eine Webrolle zu Ihrem Dienst hinzu:

    ![][2]

4.  Veröffentlichen Sie Ihren Dienst mit dem **Publish-AzureServiceProject**-Cmdlet in der Cloud:

    ![][3]

	> [AZURE.NOTE]Falls Sie noch nie Veröffentlichungseinstellungen für Ihr Azure-Abonnement importiert haben, erhalten Sie beim Veröffentlichen eine Fehlermeldung. Informationen zum Download und Import der Veröffentlichungseinstellungen für Ihr Abonnement finden Sie unter [Verwenden von Azure PowerShell für Node.js](https://www.windowsazure.com/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)

Der vom **Publish-AzureServiceProject**-Cmdlet zurückgegebene Wert für **Created Website URL** enthält den vollqualifizierten Domänennamen für Ihre gehostete Anwendung. Sie müssen ein SSL-Zertifikat für diesen vollqualifizierten Domänennamen beziehen und in Azure bereitstellen.

## <a name="step2"> </a>Schritt 2: Beziehen eines SSL-Zertifikats

Sie müssen zuerst ein SSL-Zertifikat beziehen, um SSL für eine Anwendung zu konfigurieren. Dieses muss von einer Zertifizierungsstelle, einem vertrauenswürdigen Dritten, der Zertifikate für diesen Zweck ausgibt, ausgegeben werden. Wenn Sie noch kein Zertifikat haben, müssen Sie eines von einem Unternehmen erwerben, das SSL-Zertifikate verkauft.

Das Zertifikat muss die folgenden Anforderungen für SSL-Zertifikate in Azure erfüllen:

-   Das Zertifikat muss einen privaten Schlüssel enthalten.
-   Das Zertifikat muss für den Schlüsselaustausch erstellt werden (.pfx-Datei).
-   Der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf den Clouddienst zugegriffen wird. Sie können kein SSL-Zertifikat für die Domäne cloudapp.net beziehen, daher muss der Name des Antragstellers für das Zertifikat der Domäne entsprechen, über die auf Ihre Anwendung zugegriffen wird. Beispiel: __mysecuresite.cloudapp.net__.
-   Das Zertifikat muss mindestens eine 2048-Bit-Verschlüsselung haben.

## <a name="step3"> </a>Schritt 3: Importieren des SSL-Zertifikats

Sobald Sie über ein Zertifikat verfügen, installieren Sie es in den Zertifikatspeicher auf Ihrem Entwicklungscomputer. Dieses Zertifikat wird abgerufen und im Rahmen Ihres Anwendungsbereitstellungspakets nach Azure hochgeladen. Das Paket basiert auf den von Ihnen in einem nachfolgenden Schritt vorgenommenen Konfigurationsänderungen.

> [AZURE.NOTE]Die in diesem Abschnitt angewendeten Schritte basieren auf der Windows 8-Version des Zertifikatimport-Assistenten. Wenn Sie eine frühere Version von Windows verwenden, stimmen die hier aufgelisteten Schritte unter Umständen nicht mit der im Assistenten angezeigten Reihenfolge überein. Sollte dies der Fall sein, lesen Sie diesen Abschnitt vor dem Verwenden des Zertifikatimport-Assistenten vollständig durch, sodass Sie sich darüber im Klaren sind, welche globalen Aktionen durchgeführt werden müssen.

Zum Importieren des SSL-Zertifikats führen Sie die folgenden Schritte durch:

1.   Navigieren Sie mithilfe des Windows Explorers zu dem Verzeichnis, in dem sich die **.pfx**-Datei befindet, welche das Zertifikat enthält. Doppelklicken Sie anschließend auf das Zertifikat. Dadurch wird der Zertifikatimport-Assistent angezeigt.
	
	![Zertifikat-Assistent][cert-wizard]

2.   Wählen Sie im Bereich **Store Location** die Option **Current User**, und klicken Sie dann auf **Weiter**. Das Zertifikat wird nun für Ihr Benutzerkonto in den Zertifikatspeicher installiert.

3.   Fahren Sie im Assistenten fort, und akzeptieren Sie dabei sämtliche Voreinstellungen, bis Sie zum Bildschirm **Private key protection** gelangen. Hier müssen Sie das Kennwort (sofern vorhanden) für das Zertifikat eingeben. Außerdem müssen Sie **Mark this key as exportable** auswählen. Klicken Sie anschließend auf **Next**.

	![Schutz durch privaten Schlüssel][key-protection]

4. Fahren Sie im Assistenten weiter fort. Akzeptieren Sie die Voreinstellungen, bis das Zertifikat erfolgreich installiert ist.

Sie müssen nun Ihre Dienstdefinition modifizieren, um einen Bezug zu dem von Ihnen installierten Zertifikat herzustellen.

## <a name="step4"> </a>Schritt 4: Ändern der Definitions- und Konfigurationsdateien für den Dienst

Ihre Anwendung muss so konfiguriert sein, dass auf das Zertifikat verwiesen wird. Außerdem muss ein HTTPS-Endpunkt hinzugefügt werden. Daher müssen die Definitions- und Konfigurationsdateien für den Dienst aktualisiert werden.

1.  Öffnen Sie in Ihrem Dienstverzeichnis die Dienstdefinitionsdatei (ServiceDefinition.csdef). Fügen Sie innerhalb des Bereichs **WebRole** einen Bereich **Certificates** hinzu, und geben Sie die folgenden Informationen über das Zertifikat an:

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    Der Bereich **Certificates** definiert den Namen des Zertifikats, dessen Speicherort sowie den Namen des Speichers. Da das Zertifikat im Benutzerzertifikatspeicher installiert ist, wird der Wert "My" verwendet. Andere Zertifikatspeicherorte können ebenfalls verwendet werden. Weitere Informationen finden Sie unter [Verknüpfen eines Zertifikats (mit einem Dienst)](http://msdn.microsoft.com/library/windowsazure/gg465718.aspx).

2.  Aktualisieren Sie in der Dienstdefinitionsdatei im Abschnitt **Endpoints** das **InputEndpoint**-HTTP-Element, um HTTPS zu aktivieren:

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    Alle erforderlichen Änderungen an der Dienstdefinitionsdatei sind jetzt abgeschlossen. Sie müssen jedoch noch die Zertifikatinformationen zur Dienstkonfigurationsdatei hinzufügen.

3.  Fügen Sie in Ihren Dienstkonfigurationsdateien (**ServiceConfiguration.Cloud.cscfg** und **ServiceConfiguration.Local.cscfg**) das Zertifikat in den leeren Abschnitt **
4.  ** innerhalb des Abschnitts **Role** ein. Damit ersetzen Sie den unten angegebenen Beispielfingerabdruckwert durch den Wert Ihres Zertifikats:

        <Role name="WebRole1">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

4.  Veröffentlichen Sie den Dienst erneut, um die Dienstkonfiguration in der Cloud zu aktualisieren. Geben Sie in der Azure PowerShell-Eingabeaufforderung **Publish-AzureServiceProject** für das Dienstverzeichnis ein.

	Im Rahmen des Veröffentlichungsprozesses wird das referenzierte Zertifikat aus dem lokalen Zertifikatspeicher kopiert und in das Bereitstellungspaket eingebunden.

## <a name="step5"> </a>Schritt 5: Herstellen einer Verbindung mit der Rolleninstanz über HTTPS

Jetzt wird die Bereitstellung in Azure ausgeführt und Sie können eine HTTPS-Verbindung herstellen.

1.  Wählen Sie im Verwaltungsportal Ihren Cloud-Dienst aus und klicken Sie auf **Dashboard**.

2. Blättern Sie nach unten und klicken Sie auf den Link, der als **Site URL** angezeigt wird:

    ![Website-URL][site-url]

	> [AZURE.IMPORTANT]Falls die im Portal angezeigte URL kein HTTPS verwendet, müssen Sie die URL im Browser manuell von HTTP zu HTTPS ändern.

3.  Ein neues Browserfenster wird geöffnet und zeigt Ihre Website an.

    Ihr Browser zeigt ein Schlosssymbol an, um anzuzeigen, dass es sich um eine HTTPS-Verbindung handelt. Dies bedeutet ebenfalls, dass Ihre Anwendung korrekt für SSL konfiguriert wurde.

    ![][8]

## Zusätzliche Ressourcen

[Verknüpfen eines Zertifikats mit einem Dienst]

[Konfigurieren von SSL für eine Node.js-Anwendung in einer Azure-Workerrolle]

[Konfigurieren eines SSL-Zertifikats für einen HTTPS-Endpunkt]

[Schritt 1: Erstellen eines Node.js-Diensts und Veröffentlichen des Diensts in der Cloud]: #step1
[Schritt 2: Beziehen eines SSL-Zertifikats]: #step2
[Schritt 3: Importieren des SSL-Zertifikats]: #step3
[Schritt 4: Ändern der Definitions- und Konfigurationsdateien für den Dienst]: #step4
[Schritt 5: Herstellen einer Verbindung mit der Rolleninstanz über HTTPS]: #step5
[**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
[1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
[2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
[3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
[Azure Management Portal]: http://manage.windowsazure.com
[Verknüpfen eines Zertifikats mit einem Dienst]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
[site-url]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
[8]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
[Konfigurieren eines SSL-Zertifikats für einen HTTPS-Endpunkt]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
[powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
[cert-wizard]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
[key-protection]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
[Konfigurieren von SSL für eine Node.js-Anwendung in einer Azure-Workerrolle]: /develop/nodejs/common-tasks/enable-ssl-worker-role/

<!--HONumber=54--> 