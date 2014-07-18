<properties linkid="dev-net-commons-tasks-enable-ssl" urlDisplayName="Enable SSL" pageTitle="Configure SSL for a cloud service - Azure" metaKeywords="Azure SSL, Azure HTTPS, Azure SSL, Azure HTTPS, .NET Azure SSL, .NET Azure HTTPS, C# Azure SSL, C# Azure HTTPS, VB Azure SSL, VB Azure HTTPS" description="Learn how to specify an HTTPS endpoint for a web role and how to upload an SSL certificate to secure your application." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Configuring SSL for an application in Azure" authors="" solutions="" manager="jeffreyg" editor="mollybos" />



Konfigurieren von SSL für eine Anwendung in Azure
=================================================

Secure Socket Layer (SSL)-Verschlüsselung ist die am häufigsten verwendete Methode zur Sicherung von Daten im Internet. Im Folgenden erfahren Sie, wie Sie einen HTTPS-Endpunkt für eine Webrolle angeben und ein SSL-Zertifikat zur Sicherung Ihrer Anwendung hochladen können.

Hinweis

Die Prozesse in dieser Aufgabe gelten für Azure-Clouddienste. Die Vorgehensweise für Websites finden Sie unter [Configuring an SSL certificate for an Azure web site](../web-sites-configure-ssl-certificate/) (Konfigurieren eines SSL-Zertifikats für eine Azure-Website, in englischer Sprache).

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Beziehen eines SSL-Zertifikats](#step1)
-   [Schritt 2: Ändern der Definitions- und Konfigurationsdateien für den Dienst](#step2)
-   [Schritt 3: Hochladen des Bereitstellungspakets und des Zertifikats](#step3)
-   [Schritt 4: Verbinden der Rolleninstanzen über HTTPS](#step4)

Diese Aufgabe erfordert die Verwendung einer Produktionsbereitstellung. Informationen zur Verwendung einer Stagingbereitstellung erhalten Sie am Ende dieses Themenabschnitts.

Beziehen eines SSL-ZertifikatsSchritt 1: Beziehen eines SSL-Zertifikats
-----------------------------------------------------------------------

Sie müssen zuerst ein SSL-Zertifikat beziehen, um SSL für eine Anwendung zu konfigurieren. Dieses muss von einer Zertifizierungsstelle, einem vertrauenswürdigen Dritten, der Zertifikate für diesen Zweck ausgibt, ausgegeben werden. Wenn Sie noch kein Zertifikat haben, müssen Sie eines von einem Unternehmen erwerben, das SSL-Zertifikate verkauft.

Das Zertifikat muss die folgenden Anforderungen für SSL-Zertifikate in Azure erfüllen:

-   Das Zertifikat muss einen privaten Schlüssel enthalten.
-   Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.
-   Der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf den Clouddienst zugegriffen wird. Für die Domäne cloudapp.net können Sie kein Zertifikat von einer Zertifizierungsstelle beziehen. Sie müssen einen benutzerdefinierten Domänennamen erwerben, den Sie für den Zugriff auf Ihren Dienst verwenden können. Wenn Sie ein Zertifikat von einer Zertifizierungsstelle anfordern, muss der Name des Antragstellers für das Zertifikat der Domäne entsprechen, über die auf Ihre Anwendung zugegriffen wird. Wenn beispielsweise der benutzerdefinierte Domänenname **contoso.com** lautet, fordern Sie von Ihrer Zertifizierungsstelle ein Zertifikat für **\*.contoso.com** oder **www.contoso.com** an.
-   Das Zertifikat muss mindestens eine 2048-Bit-Verschlüsselung haben.

Zu Testzwecken können Sie ein selbstsigniertes Zertifikat erstellen und verwenden. Ein selbstsigniertes Zertifikat wird nicht über eine Zertifizierungsstelle authentifiziert. Daher kann in diesem Fall die Domäne cloudapp.net als Website-URL verwendet werden. Zum Beispiel wird in der Aufgabe unten ein selbstsigniertes Zertifikat verwendet, in dem der allgemeine Name, der im Zertifikat verwendet wird, **sslexample.cloudapp.net** lautet. Details zur Erstellung eines selbstsignierten Zertifikats mit IIS Manager finden Sie unter [Erstellen eines Dienstzertifikats für Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg432987.aspx).

Daraufhin müssen Sie Informationen zum Zertifikat in Ihre Definitions- und Konfigurationsdateien für den Dienst einfügen.

Ändern von Dienstdefinitions-/KonfigurationsdateienSchritt 2: Ändern der Definitions- und Konfigurationsdateien für den Dienst
------------------------------------------------------------------------------------------------------------------------------

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

    Der Bereich **Certificates** definiert den Namen des Zertifikats, dessen Speicherort sowie den Namen des Speichers. Hier wurde der Speicher der Zertifizierungsstelle als Speicherort des Zertifikats gewählt. Sie können jedoch auch andere Optionen auswählen. Weitere Informationen finden Sie unter [Verknüpfen eines Zertifikats](http://msdn.microsoft.com/en-us/library/windowsazure/gg465718.aspx) mit einem Dienst.

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

Hochladen in AzureSchritt 3: Hochladen des Bereitstellungspakets und des Zertifikats
------------------------------------------------------------------------------------

Ihr Bereitstellungspaket wurde so aktualisiert, dass das Zertifikat verwendet wird. Außerdem wurde ein HTTPS-Endpunkt hinzugefügt. Jetzt können Sie das Paket und das Zertifikat in Azure über das Verwaltungsportal hochladen

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.
2.  Klicken Sie auf **Neu**, dann auf **Cloud Service** und schließlich auf **Custom Create**.
3.  Geben Sie im Dialog **Create a cloud service** Werte für URL, Region/Affinitätsgruppe und Abonnement ein. Stellen Sie sicher, dass die Option **Deploy a cloud service package now** aktiviert ist, und klicken Sie dann auf die Schaltfläche **Weiter**.
4.  Geben Sie im Dialog **Publish your cloud service** die erforderlichen Informationen für Ihren Clouddienst ein, wählen Sie für die Umgebung **Production** aus, und stellen Sie sicher, dass **Add certificates now** aktiviert ist. (Wenn eine der Rollen eine einzelne Instanz enthält, stellen Sie sicher, dass **Deploy even if one or more roles contain a single instance** aktiviert ist.)

    ![Clouddienst veröffentlichen](./media/cloud-services-dotnet-configure-ssl-certificate/CreateCloudService.png)

5.  Klicken Sie auf die Schaltfläche **Weiter**.
6.  Geben Sie im Dialog **Add Certificate** den Speicherort für das SSL-Zertifikat (Datei im PFX-Format) und das Kennwort für das Zertifikat ein, und klicken Sie auf **attach certificate**.

    ![Zertifikat hinzufügen](./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificate.png)

7.  Stellen Sie sicher, dass Ihr Zertifikat im Bereich **Attached Certificates** aufgelistet ist.

    ![Angehängte Zertifikate](./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificateComplete.png)

8.  Klicken Sie auf die Schaltfläche **Complete**, um den Clouddienst zu erstellen. Wenn sich die Bereitstellung im Status **Ready** befindet, können Sie mit den nächsten Schritten fortfahren.

Verbindung über HTTPSSchritt 4: Verbinden der Rolleninstanzen über HTTPS
------------------------------------------------------------------------

Jetzt wird die Bereitstellung in Azure ausgeführt und Sie können eine HTTPS-Verbindung herstellen.

1.  Wählen Sie im Verwaltungsportal die Bereitstellung aus, und klicken Sie dann unterhalb der **Website-URL** auf den Link.

    ![Website-URL festlegen](./media/cloud-services-dotnet-configure-ssl-certificate/CopyURL.png)

2.  Passen Sie in Ihrem Webbrowser den Link so an, dass **https** statt **http** verwendet wird, und rufen Sie dann die Seite auf.

    **Hinweis:** Wenn Sie ein selbstsigniertes Zertifikat verwenden und zu einem HTTPS-Endpunkt wechseln, der mit dem selbstsignierten Zertifikat verknüpft ist, wird im Browser ein Zertifikatfehler angezeigt. Verwenden Sie zur Lösung dieses Problems ein Zertifikat, das von einer vertrauenswürdigen Zertifizierungsstelle signiert wurde. Bis zu diesem Zeitpunkt können Sie den Fehler ignorieren. (Sie können auch das selbstsignierte Zertifikat zum Zertifikatspeicher der vertrauenswürdigen Zertifizierungsstelle des Benutzers hinzufügen.)

    ![SSL-Beispielwebsite](./media/cloud-services-dotnet-configure-ssl-certificate/SSLCloudService.png)

Wenn Sie SSL für eine Staging- statt für eine Produktionsbereitstellung verwenden möchten, müssen Sie zuerst die URL festlegen, die für die Stagingbereitstellung verwendet werden soll. Stellen Sie Ihren Clouddienst in der Stagingumgebung bereit, ohne ein Zertifikat oder Zertifikatinformationen hinzuzufügen. Nach der Bereitstellung können Sie die GUID-basierte URL festlegen, die im Verwaltungsportal im Feld **Website-URL** aufgelistet ist. Erstellen Sie ein Zertifikat mit einem allgemeinen Namen, welcher der GUID-basierten URL entspricht (beispielsweise **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). Verwenden Sie das Verwaltungsportal, um das Zertifikat zu Ihrem bereitgestellten Clouddienst hinzuzufügen. Fügen Sie dann die Zertifikatinformationen zu den CSDEF- und CSCFG-Dateien hinzu, erstellen Sie erneut ein Anwendungspaket, und aktualisieren Sie schließlich Ihre Stagingbereitstellung, sodass das neue Paket und die neue CSCFG-Datei verwendet werden.

Zusätzliche RessourcenZusätzliche Ressourcen
--------------------------------------------

-   [Verknüpfen eines Zertifikats mit einem Dienst](http://msdn.microsoft.com/en-us/library/windowsazure/gg465718.aspx)

-   [Konfigurieren eines SSL-Zertifikats für einen HTTPS-Endpunkt](http://msdn.microsoft.com/en-us/library/windowsazure/ff795779.aspx)


