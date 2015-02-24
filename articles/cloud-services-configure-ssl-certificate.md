<properties urlDisplayName="Enable SSL" pageTitle="Konfigurieren von SSL für einen Cloud-Dienst - Azure" metaKeywords="Azure SSL, Azure HTTPS, Azure SSL, Azure HTTPS, .NET Azure SSL, .NET Azure HTTPS, C# Azure SSL, C# Azure HTTPS, VB Azure SSL, VB Azure HTTPS" description="Im Folgenden erfahren Sie, wie Sie einen HTTPS-Endpunkt für eine Webrolle angeben und ein SSL-Zertifikat zur Sicherung Ihrer Anwendung hochladen können." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Configuring SSL for an application in Azure" authors="adegeo" solutions="" manager="timlt" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="adegeo" />




# Configuring SSL for an application in Azure

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Secure Socket Layer (SSL)-Verschlüsselung ist die am häufigsten verwendete Methode zur Sicherung von Daten im Internet. Im Folgenden erfahren Sie, wie Sie einen HTTPS-Endpunkt für eine Webrolle angeben und ein SSL-Zertifikat zur Sicherung Ihrer Anwendung hochladen können.

<div class="dev-callout">Hinweis
<p>Die Vorgehensweisen in dieser Aufgabe gelten für Azure-Clouddienste. Für Websites siehe <a href="../web-sites-configure-ssl-certificate/">Konfigurieren eines SSL-Zertifikats für eine Azure-Website</a>.</p>
</div>

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Beziehen eines SSL-Zertifikats][]
-   [Schritt 2: Ändern der Definitions- und Konfigurationsdateien für den Dienst][]
-   [Schritt 3: Hochladen des Bereitstellungspakets und des Zertifikats][]
-   [Schritt 4: Verbinden der Rolleninstanzen über HTTPS][]

Diese Aufgabe erfordert die Verwendung einer Produktionsbereitstellung. Informationen zur Verwendung einer Stagingbereitstellung erhalten Sie am Ende dieses Themenabschnitts.

<h2><a name="step1"> </a>Schritt 1: Beziehen eines SSL-Zertifikats</h2>

Sie müssen zuerst ein SSL-Zertifikat beziehen, um SSL für eine Anwendung zu konfigurieren. Dieses muss von einer Zertifizierungsstelle, einem vertrauenswürdigen Dritten, der Zertifikate für diesen Zweck ausgibt, ausgegeben werden. Wenn Sie noch kein Zertifikat haben, müssen Sie eines von einem Unternehmen erwerben, das SSL-Zertifikate verkauft.

Das Zertifikat muss die folgenden Anforderungen für SSL-Zertifikate in Azure erfüllen:

-   Das Zertifikat muss einen privaten Schlüssel enthalten.
-   Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.
-   Der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf den Clouddienst zugegriffen wird. Für die Domäne cloudapp.net können Sie kein Zertifikat von einer Zertifizierungsstelle beziehen. Sie müssen einen benutzerdefinierten Domänennamen erwerben, den Sie für den Zugriff auf Ihren Dienst verwenden können. Wenn Sie ein Zertifikat von einer Zertifizierungsstelle anfordern, muss der Name des Antragstellers für das Zertifikat der Domäne entsprechen, über die auf Ihre Anwendung zugegriffen wird. Wenn beispielsweise der benutzerdefinierte Domänenname **contoso.com** lautet, fordern Sie von Ihrer Zertifizierungsstelle ein Zertifikat für ***.contoso.com** oder **www.contoso.com** an.
-   Das Zertifikat muss mindestens eine 2048-Bit-Verschlüsselung haben.

Zu Testzwecken können Sie ein selbstsigniertes Zertifikat erstellen und verwenden. Ein selbstsigniertes Zertifikat wird nicht über eine Zertifizierungsstelle authentifiziert. Daher kann in diesem Fall die Domäne cloudapp.net als Website-URL verwendet werden. Zum Beispiel wird in der Aufgabe unten ein selbstsigniertes Zertifikat verwendet, in dem der allgemeine Name, der im Zertifikat verwendet wird, **sslexample.cloudapp.net** lautet. Details zur Erstellung eines selbstsignierten Zertifikats mit IIS Manager finden Sie unter [Erstellen eines Zertifikats für eine Rolle][].

Daraufhin müssen Sie Informationen zum Zertifikat in Ihre Definitions- und Konfigurationsdateien für den Dienst einfügen.

<h2><a name="step2"> </a>Schritt 2: Ändern der Definitions- und Konfigurationsdateien für den Dienst</h2>

Ihre Anwendung muss so konfiguriert sein, dass das Zertifikat verwendet wird. Außerdem muss ein HTTPS-Endpunkt hinzugefügt werden. Daher müssen die Definitions- und Konfigurationsdateien für den Dienst aktualisiert werden.

1.  Öffnen Sie in Ihrer Entwicklungsumgebung die Dienstdefinitionsdatei     (CSDEF). Fügen Sie innerhalb des Abschnitts **WebRole** den Abschnitt **Certificates**     hinzu, und geben Sie die folgenden Informationen über das      Zertifikat an:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    Der Bereich **Certificates** definiert den Namen des Zertifikats, dessen Speicherort sowie den Namen des Speichers. Hier wurde der Speicher der Zertifizierungsstelle als Speicherort des Zertifikats gewählt. Sie können jedoch auch andere Optionen auswählen. Weitere Informationen finden Sie unter [Verknüpfen eines Zertifikats mit einem Dienst][].

2.  Fügen Sie in der Dienstdefinitionsdatei im Bereich **Endpoints** ein **InputEndpoint**-Element hinzu, um HTTPS zu aktivieren: 
        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  Fügen Sie in der Dienstdefinitionsdatei ein **Binding**-Element im
    Abschnitt **Sites** hinzu. Dadurch wird eine HTTPS-Bindung hinzugefügt, die den
    Endpunkt Ihrer Website zuordnet:

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

4.  Fügen Sie in der Dienstkonfigurationsdatei (CSCFG) mit dem Namen ServiceConfiguration.Cloud.cscfg im Bereich **Role**     einen Bereich **Certificates** hinzu, und ersetzen Sie damit den Fingerabdruckwert aus dem folgenden Beispiel mit dem aus Ihrem Zertifikat:

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

<h2><a name="step3"> </a>Schritt 3: Hochladen des Bereitstellungspakets und des Zertifikats</h2>

Ihr Bereitstellungspaket wurde so aktualisiert, dass das Zertifikat verwendet wird. Außerdem wurde ein HTTPS-Endpunkt hinzugefügt. Jetzt können Sie das Paket und das Zertifikat in Azure über das Verwaltungsportal hochladen

1. Melden Sie sich beim [Azure-Verwaltungsportal][] an. 
2. Klicken Sie auf **Neu**, dann auf **Cloud-Dienst** und schließlich auf **Benutzerdefiniert erstellen**.
3. Geben Sie im Dialogfeld **Cloud-Dienst erstellen** Werte für URL, Region/Affinitätsgruppe und Abonnement ein. Stellen Sie sicher, dass die Option **Cloud-Dienstpaket jetzt bereitstellen** aktiviert ist, und klicken Sie dann auf die Schaltfläche **Weiter**.
3. Geben Sie im Dialogfeld **Cloud-Dienst veröffentlichen** die erforderlichen Informationen für Ihren Cloud-Dienst ein, wählen Sie für die Umgebung **Produktion** aus, und stellen Sie sicher, dass **Zertifikate jetzt hinzufügen** aktiviert ist. (Wenn eine der Rollen eine einzelne Instanz enthält, stellen Sie sicher, dass **Auch dann bereitstellen, wenn für eine oder mehrere Rollen nur eine Instanz vorhanden ist** aktiviert ist.) 

    ![Publish your cloud service][0]

4.  Klicken Sie auf die Schaltfläche **Weiter**.
5.  Geben Sie im Dialogfeld **Zertifikat hinzufügen** den Speicherort für das SSL-Zertifikat (Datei im PFX-Format) und das Kennwort für das Zertifikat ein, und klicken Sie auf **Zertifikat anfügen**.  

    ![Add certificate][1]

6.  Stellen Sie sicher, dass Ihr Zertifikat im Bereich **Angefügte Zertifikate** aufgelistet ist.

    ![Attached certificates][4]

7.  Klicken Sie auf die Schaltfläche **Abschließen**, um den Cloud-Dienst zu erstellen. Wenn sich die Bereitstellung im Status **Bereit** befindet, können Sie mit den nächsten Schritten fortfahren.

<h2><a name="step4"> </a>Schritt 4: Verbinden der Rolleninstanzen über HTTPS</h2>

Jetzt wird die Bereitstellung in Azure ausgeführt und Sie können
eine HTTPS-Verbindung herstellen.

1.  Wählen Sie im Verwaltungsportal die Bereitstellung aus, und klicken Sie dann unter **Website-URL** auf den Link.

    ![Determine site URL][2]

2.  Passen Sie in Ihrem Webbrowser den Link so an, dass **https** statt **http** verwendet wird, und rufen Sie dann die Seite auf.

    **Hinweis:** Wenn Sie ein selbstsigniertes Zertifikat verwenden und zu einem HTTPS-Endpunkt wechseln, der mit dem selbstsignierten Zertifikat verknüpft ist, wird im Browser ein Zertifikatfehler angezeigt. Verwenden Sie zur Lösung dieses Problems ein Zertifikat, das von einer vertrauenswürdigen Zertifizierungsstelle signiert wurde. Bis zu diesem Zeitpunkt können Sie den Fehler ignorieren. (Sie können auch das selbstsignierte Zertifikat zum Zertifikatspeicher der vertrauenswürdigen Zertifizierungsstelle des Benutzers hinzufügen.)

    ![SSL example web site][3]

Wenn Sie SSL für eine Staging- statt für eine Produktionsbereitstellung verwenden möchten, müssen Sie zuerst die URL festlegen, die für die Stagingbereitstellung verwendet werden soll. Stellen Sie Ihren Clouddienst in der Stagingumgebung bereit, ohne ein Zertifikat oder Zertifikatinformationen hinzuzufügen. Nach der Bereitstellung können Sie die GUID-basierte URL festlegen, die im Verwaltungsportal im Feld **Website-URL** aufgelistet ist. Erstellen Sie ein Zertifikat mit einem allgemeinen Namen, welcher der GUID-basierten URL entspricht (beispielsweise **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). Verwenden Sie das Verwaltungsportal, um das Zertifikat zu Ihrem bereitgestellten Cloud-Dienst hinzuzufügen. Fügen Sie dann die Zertifikatinformationen zu den CSDEF- und CSCFG-Dateien hinzu, erstellen Sie erneut ein Anwendungspaket, und aktualisieren Sie schließlich Ihre Stagingbereitstellung, sodass das neue Paket und die neue CSCFG-Datei verwendet werden.

<h2><a name="additional_resources"> </a>Zusätzliche Ressourcen</h2>

* [Verknüpfen eines Zertifikats mit einem Dienst][]

* [Konfigurieren eines SSL-Zertifikats für einen HTTPS-Endpunkt][]

  [Schritt 1: Beziehen eines SSL-Zertifikats]: #step1
  [Schritt 2: Ändern der Definitions- und Konfigurationsdateien für den Dienst]: #step2
  [Schritt 3: Hochladen des Bereitstellungspakets und des Zertifikats]: #step3
  [Schritt 4: Verbinden der Rolleninstanzen über HTTPS]: #step4
  [Erstellen eines Zertifikats für eine Rolle]: http://msdn.microsoft.com/de-de/library/windowsazure/gg432987.aspx
  [Verknüpfen eines Zertifikats mit einem Dienst]: http://msdn.microsoft.com/de-de/library/windowsazure/gg465718.aspx
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-dotnet-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-dotnet-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-dotnet-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificateComplete.png  
  [Konfigurieren eines SSL-Zertifikats für einen HTTPS-Endpunkt]: http://msdn.microsoft.com/de-de/library/windowsazure/ff795779.aspx

<!--HONumber=35.1-->
