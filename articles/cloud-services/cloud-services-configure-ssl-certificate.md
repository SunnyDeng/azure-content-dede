<properties 
	pageTitle="Konfigurieren von SSL für einen Cloud-Dienst – Azure" 
	description="Im Folgenden erfahren Sie, wie Sie einen HTTPS-Endpunkt für eine Webrolle angeben und ein SSL-Zertifikat zur Sicherung Ihrer Anwendung hochladen können." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/28/2015"
	ms.author="adegeo"/>




# Konfigurieren von SSL für eine Anwendung in Azure

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-configure-ssl-certificate.md)
- [Azure Preview Portal](cloud-services-configure-ssl-certificate-portal.md)

Secure Socket Layer (SSL)-Verschlüsselung ist die am häufigsten verwendete Methode zur Sicherung von Daten im Internet. Im Folgenden erfahren Sie, wie Sie einen HTTPS-Endpunkt für eine Webrolle angeben und ein SSL-Zertifikat zur Sicherung Ihrer Anwendung hochladen können.

> [AZURE.NOTE]Die Prozesse in dieser Aufgabe gelten für Azure-Cloud-Dienste. Die Vorgehensweise für Websites finden Sie unter [Configuring an SSL certificate for an Azure web site](../web-sites-configure-ssl-certificate.md) (Konfigurieren eines SSL-Zertifikats für eine Azure-Website, in englischer Sprache).

Diese Aufgabe erfordert die Verwendung einer Produktionsbereitstellung. Informationen zur Verwendung einer Stagingbereitstellung erhalten Sie am Ende dieses Themenabschnitts.

Lesen Sie [dies](cloud-services-how-to-create-deploy.md) zuerst, wenn Sie noch keinen Clouddienst erstellt haben.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]


## Schritt 1: Beziehen eines SSL-Zertifikats

Sie müssen zuerst ein SSL-Zertifikat beziehen, um SSL für eine Anwendung zu konfigurieren. Dieses muss von einer Zertifizierungsstelle, einem vertrauenswürdigen Dritten, der Zertifikate für diesen Zweck ausgibt, ausgegeben werden. Wenn Sie noch kein Zertifikat haben, müssen Sie eines von einem Unternehmen erwerben, das SSL-Zertifikate verkauft.

Das Zertifikat muss die folgenden Anforderungen für SSL-Zertifikate in Azure erfüllen:

-   Das Zertifikat muss einen privaten Schlüssel enthalten.
-   Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.
-   Der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf den Clouddienst zugegriffen wird. Für die Domäne cloudapp.net können Sie kein Zertifikat von einer Zertifizierungsstelle beziehen. Sie müssen einen benutzerdefinierten Domänennamen erwerben, den Sie für den Zugriff auf Ihren Dienst verwenden können. Wenn Sie ein Zertifikat von einer Zertifizierungsstelle anfordern, muss der Name des Antragstellers für das Zertifikat der Domäne entsprechen, über die auf Ihre Anwendung zugegriffen wird. Wenn der benutzerdefinierte Domänenname beispielsweise **contoso.com** lautet, fordern Sie von Ihrer Zertifizierungsstelle ein Zertifikat für ***.contoso.com** oder **www.contoso.com** an.
-   Das Zertifikat muss mindestens eine 2048-Bit-Verschlüsselung haben.

Zu Testzwecken können Sie ein selbstsigniertes Zertifikat erstellen und verwenden. Ein selbstsigniertes Zertifikat wird nicht über eine Zertifizierungsstelle authentifiziert. Daher kann in diesem Fall die Domäne cloudapp.net als Website-URL verwendet werden. Zum Beispiel wird in der Aufgabe unten ein selbstsigniertes Zertifikat verwendet, in dem der allgemeine Name, der im Zertifikat verwendet wird, **sslexample.cloudapp.net** lautet. Details zur Erstellung eines selbstsignierten Zertifikats mit IIS Manager finden Sie unter [Erstellen eines Dienstzertifikats für Windows Azure][].

Daraufhin müssen Sie Informationen zum Zertifikat in Ihre Definitions- und Konfigurationsdateien für den Dienst einfügen.

## Schritt 2: Ändern der Definitions- und Konfigurationsdateien für den Dienst

Ihre Anwendung muss so konfiguriert sein, dass das Zertifikat verwendet wird. Außerdem muss ein HTTPS-Endpunkt hinzugefügt werden. Daher müssen die Definitions- und Konfigurationsdateien für den Dienst aktualisiert werden.

1.  Öffnen Sie in Ihrer Entwicklungsumgebung die Dienstdefinitionsdatei (CSDEF), fügen Sie innerhalb des Bereichs **WebRole** einen Bereich **Certificates** hinzu und geben Sie die folgenden Informationen über das Zertifikat an:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    Der Bereich **Certificates** definiert den Namen des Zertifikats, dessen Speicherort sowie den Namen des Speichers. Hier wurde der Speicher der Zertifizierungsstelle als Speicherort des Zertifikats gewählt. Sie können jedoch auch andere Optionen auswählen. Weitere Informationen finden Sie unter [Verknüpfen eines Zertifikats][] mit einem Dienst.

2.  Fügen Sie in der Dienstdefinitionsdatei im Bereich **Endpoints** ein **InputEndpoint**-Element hinzu, um HTTPS zu aktivieren:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  Fügen Sie in der Dienstdefinitionsdatei im Bereich **Sites** ein **Binding**-Element hinzu. Dadurch wird eine HTTPS-Bindung hinzugefügt, die den Endpunkt Ihrer Website zuordnet.

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Alle erforderlichen Änderungen an der Dienstdefinitionsdatei sind jetzt abgeschlossen. Sie müssen jedoch noch die Zertifikatinformationen zur Dienstkonfigurationsdatei hinzufügen.

4.  Fügen Sie in der Dienstkonfigurationsdatei (CSCFG) mit dem Namen ServiceConfiguration.Cloud.cscfg im Bereich **Role** einen Bereich **Certificates** hinzu, und ersetzen Sie damit den Fingerabdruckwert aus dem folgenden Beispiel:

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(Im Beispiel oben wird **sha1** für den Fingerabdruckalgorithmus verwendet. Geben Sie den entsprechenden Wert für den Fingerabdruckalgorithmus Ihres Zertifikats an.)

Die Definitions- und Konfigurationsdateien für den Dienst wurden aktualisiert. Erstellen Sie jetzt Ihr Bereitstellungspaket und laden Sie es in Azure hoch. Wenn Sie **cspack** verwenden, stellen Sie sicher, dass Sie nicht die Kennzeichnung **/generateConfigurationFile** verwenden, da dies die Zertifikatinformationen überschreibt, die Sie zuvor eingefügt haben.

## Schritt 3: Hochladen eines Zertifikats

Ihr Bereitstellungspaket wurde so aktualisiert, dass das Zertifikat verwendet wird. Außerdem wurde ein HTTPS-Endpunkt hinzugefügt. Jetzt können Sie das Paket und das Zertifikat in Azure über das Verwaltungsportal hochladen

1. Melden Sie sich beim [Azure-Verwaltungsportal][] an. 
2. Klicken Sie im linken Navigationsbereich auf **Cloud Services**.
3. Klicken Sie auf den gewünschten Clouddienst.
4. Klicken Sie auf die Registerkarte **Zertifikate**.

    ![Klicken Sie auf die Registerkarte „Zertifikate“.](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. Klicken Sie auf die Schaltfläche **Upload**.

    ![Hochladen](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. Geben Sie **Datei** und **Kennwort** an, und klicken Sie dann auf **Fertig stellen** (das Häkchen).

## Schritt 4: Herstellen einer Verbindung mit der Rolleninstanz über HTTPS

Jetzt wird die Bereitstellung in Azure ausgeführt und Sie können eine HTTPS-Verbindung herstellen.

1.  Wählen Sie im Verwaltungsportal die Bereitstellung aus, und klicken Sie dann unterhalb der **Website-URL** auf den Link.

    ![Website-URL festlegen][2]

2.  Passen Sie in Ihrem Webbrowser den Link so an, dass **https** statt **http** verwendet wird, und rufen Sie dann die Seite auf.

    **Hinweis:** Wenn Sie ein selbstsigniertes Zertifikat verwenden und zu einem HTTPS-Endpunkt wechseln, der mit dem selbstsignierten Zertifikat verknüpft ist, wird im Browser ein Zertifikatfehler angezeigt. Verwenden Sie zur Lösung dieses Problems ein Zertifikat, das von einer vertrauenswürdigen Zertifizierungsstelle signiert wurde. Bis zu diesem Zeitpunkt können Sie den Fehler ignorieren. (Sie können auch das selbstsignierte Zertifikat zum Zertifikatspeicher der vertrauenswürdigen Zertifizierungsstelle des Benutzers hinzufügen.)

    ![SSL-Beispielwebsite][3]

Wenn Sie SSL für eine Staging- statt für eine Produktionsbereitstellung verwenden möchten, müssen Sie zuerst die URL festlegen, die für die Stagingbereitstellung verwendet werden soll. Stellen Sie Ihren Clouddienst in der Stagingumgebung bereit, ohne ein Zertifikat oder Zertifikatinformationen hinzuzufügen. Nach der Bereitstellung können Sie die GUID-basierte URL festlegen, die im Verwaltungsportal im Feld **Website-URL** aufgelistet ist. Erstellen Sie ein Zertifikat mit einem allgemeinen Namen, welcher der GUID-basierten URL entspricht (beispielsweise **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). Verwenden Sie das Verwaltungsportal, um das Zertifikat zu Ihrem bereitgestellten Clouddienst hinzuzufügen. Fügen Sie dann die Zertifikatinformationen zu den CSDEF- und CSCFG-Dateien hinzu, erstellen Sie erneut ein Anwendungspaket, und aktualisieren Sie schließlich Ihre Stagingbereitstellung, sodass das neue Paket und die neue CSCFG-Datei verwendet werden.

## Zusätzliche Ressourcen

* [Verknüpfen eines Zertifikats mit einem Dienst][]

* [Konfigurieren eines SSL-Zertifikats für einen HTTPS-Endpunkt][]

  [Erstellen eines Dienstzertifikats für Windows Azure]: http://msdn.microsoft.com/library/azure/gg432987.aspx
  [Verknüpfen eines Zertifikats]: http://msdn.microsoft.com/library/azure/gg465718.aspx
  [Verknüpfen eines Zertifikats mit einem Dienst]: http://msdn.microsoft.com/library/azure/gg465718.aspx
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png
  [Konfigurieren eines SSL-Zertifikats für einen HTTPS-Endpunkt]: http://msdn.microsoft.com/library/azure/ff795779.aspx
 

<!---HONumber=July15_HO3-->