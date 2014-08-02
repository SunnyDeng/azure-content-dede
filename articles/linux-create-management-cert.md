<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Manage certificates" pageTitle="Manage certificates for Linux virtual machines in Azure" metaKeywords="Azure management certs, uploading management certs, Azure Service Management API" description="Learn how to create and upload management certificates for Linux in Azure. The certificate is required if you use the Service Management API." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create management certificates for Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Verwaltungszertifikate für Linux in Azure erstellen
===================================================

Sie benötigen ein Verwaltungszertifikat, wenn Sie die Dienstverwaltungs-API zur Interaktion mit der Azure-Imageplattform verwenden möchten.

Für die Erstellung und Verwaltung dieser Zertifikate existiert bereits eine Dokumentation unter <http://msdn.microsoft.com/en-us/library/windowsazure/gg551721.aspx>. Sie können Ihr Verwaltungszertifikat auch mit OpenSSL erstellen. Weitere Informationen finden Sie unter [OpenSSL](http://openssl.org/). Diese Dokumentation konzentriert sich jedoch hauptsächlich auf das Silverlight-Portal, das möglicherweise nicht allen Linux-Benutzern zur Verfügung steht. Dort wird beschrieben, wie Sie an diese Zertifikate gelangen, diese mit unseren verschiedenen Tools und Partnern integrieren und für sich selbst verwenden können, bis diese Funktionalität im Azure-Verwaltungsportal zur Verfügung steht.

Inhaltsverzeichnis
------------------

-   [Abrufen eines Verwaltungszertifikat aus der publishsettings-Datei](#createcert)
-   [Erstellen eines Verwaltungszertifikats im Azure-Verwaltungsportal](#management)

Gewusst wie: Erstellen und Hochladen eines Verwaltungszertifikats
-----------------------------------------------------------------

Auf der folgenden Seite können Sie ein Verwaltungszertifikat für Azure erstellen: <https://windows.azure.com/download/publishprofile.aspx>

Die Seite fordert Sie auf, sich mit Ihren Portal-Anmeldeinformationen anzumelden und erstellt anschließend ein Verwaltungszertifikat für Sie, das zusammen mit einer Ihrer Abonnement-ID in einer publishsettings-Datei verpackt ist und das Sie herunterladen können.

![linuxcredentials](./media/linux-create-management-cert/linuxcredentials.png)

Speichern Sie diese Datei unbedingt an einem sicheren Ort, da diese nicht wiederhergestellt werden kann und Sie ansonsten ein neues Verwaltungszertifikat erstellen müssen. (Die maximale Anzahl der im System verwendbaren Zertifikate ist begrenzt. Genauere Informationen finden Sie im entsprechenden Abschnitt auf dieser Website.) Anschließend können Sie das Zertifikat auf verschiedene Arten verwenden:

### In Visual Studio

![VSpublish](./media/linux-create-management-cert/VSpublish.png)

### In der Linux Azure-Befehlszeile

Führen Sie den folgenden Azure-Importbefehl aus, um das Zertifikat zu importieren und es später verwenden zu können:

![cmdlinepublish](./media/linux-create-management-cert/cmdlinepublish.png)

Falls Sie das Tool für andere Partner oder Software benötigen, müssen Sie das Verwaltungszertifikat aus der Datei extrahieren und Base 64-decodieren. Manche Partner wie z. B. ScaleXtreme und SUSE Studio können die Datei direkt in ihrer aktuellen Form verwenden.

Führen Sie diese Prozedur aus, um das Verwaltungszertifikat zu extrahieren.

Sie müssen den Base64-codierten Inhalt zwischen den Anführungszeichen nach "ManagementCertificate" aus der Datei extrahieren.

    xml version="1.0" encoding="utf-8"
    >
    <PublishData>
      <PublishProfile PublishMethod="AzureServiceManagementAPI"
        Url="https://management.core.windows.net/"
        ManagementCertificate="xxxxx">
        <Subscription Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
          Name="hjereztest" />
      </PublishProfile>
    </PublishData>

Kopieren Sie diesen Wert in eine Datei und decodieren Sie seinen Inhalt mit einem Base 64-Decoder. In Linux können Sie den folgenden Befehl verwenden:

    Base64 -d [encodedfile] > [decodedfile].pfx

Als Ergebnis erhalten Sie eine pfx-Datei, die Sie mit openssl in andere Formate konvertieren können, um bei Bedarf den privaten Schlüssel zu extrahieren:

openssl.exe pkcs12 -in publicAndprivate.pfx -nocerts -out privateKey.pem

In Windows können Sie die pfx-Datei mit PowerShell oder einem kostenlosen Base 64-Decoder für Windows decodieren und extrahieren, z. B. [http://www.fourmilab.ch/webtools/base64/base64.zip](). Führen Sie dazu den folgenden Befehl aus:

    base64 -d key.txt ->key.pfx

#### Limit für die Zertifikatsgenerierung

Sie können mit diesem Tool maximal 10 Zertifikate in der Plattform erstellen. Leider können die Schlüssel nach deren Erstellung nicht wiederhergestellt werden, da wir diese privaten Schlüssel an keiner anderen Stelle im System speichern. Falls Sie das Zertifikatslimit im System erreicht haben, müssen Sie den Support in den Azure-Foren kontaktieren, um Ihre Zertifikate löschen zu lassen. Alternativ können Sie einen Browser verwenden, der das alte Silverlight-Portal darstellen kann, und diese Aufgaben dort ausführen.

#### Falls Ihr privater Schlüssel nicht mehr sicher ist

Falls Ihr privater Schlüssel zu irgendeinem Zeitpunkt nicht mehr sicher ist, müssen Sie mit einem Browser, der Silverlight unterstützt, das alte Portal öffnen und die entsprechenden Verwaltungszertifikate löschen oder den Support über die Foren kontaktieren.
 Es reicht nicht aus, neue Zertifikate zu generieren, da alle 10 Zertifikate gültig sind und Ihr alter unsicherer Schlüssel immer für den Zugriff auf die Webseite gültig ist.

Erstellen eines Verwaltungszertifikats im Azure-Verwaltungsportal
-----------------------------------------------------------------

Sie können Verwaltungszertifikate auf verschiedene Arten erstellen. Weitere Informationen zum Erstellen von Zertifikaten finden Sie unter [Erstellen eines Verwaltungszertifikats für Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx). Nachdem Sie das Zertifikat erstellt haben, fügen Sie es in Azure dem Abonnement hinzu.

1.  Melden Sie sich auf dem Azure-Verwaltungsportal an.

2.  Klicken Sie im Navigationsbereich auf **Settings**.

3.  Klicken Sie unter **Management Certificates** auf **Upload a management certificate**.

4.  Navigieren Sie in **Upload a management certificate** zu Ihrer Zertifikatsdatei und klicken Sie auf **OK**.

### Abrufen des Fingerabdrucks des Zertifikats und der Abonnement-ID

Sie benötigen den Fingerabdruck des Verwaltungszertifikats, das Sie hinzugefügt haben, und Sie benötigen die Abonnement-ID, um die .vhd-Datei in Azure hochladen zu können.

1.  Klicken Sie im Azure-Verwaltungsportal auf **Settings**.

2.  Klicken Sie unter **Management Certificates** auf Ihr Zertifikat und notieren Sie den Fingerabdruck im Bereich **Properties**, indem Sie den Wert kopieren und an einem später verfügbaren Ort einfügen.

Sie benötigen außerdem die ID Ihres Abonnements, um die .vhd-Datei hochzuladen.

1.  Klicken Sie im Azure-Verwaltungsportal auf **All Items**.

2.  Kopieren Sie im mittleren Bereich unter **Subscription** die ID Ihres Abonnements und fügen Sie diese an einem später verfügbaren Ort ein.

### Angabe dieser Daten in Tools, falls Sie Ihren eigenen Schlüssel generiert haben

#### Für CSUPLOAD

1.  Öffnen Sie ein Azure SDK-Eingabeaufforderungsfenster als Administrator.
2.  Legen Sie die Verbindungszeichenfolge fest, indem Sie die **Subscriptionid** und **CertThumbprint** durch die Werte ersetzen, die Sie zuvor abgerufen haben:

        csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

#### Für Linux Azure-Befehlszeilentools

Sie müssen die von Ihnen erstellte PFX-Datei mit Base 64 codieren, indem Sie in openssl den folgenden Befehl ausführen:

       Base64 [file] \> [econded file]

Anschließend müssen Sie Ihre Abonnement-ID und die Base64-codierte PFX-Datei in einer einzigen Datei mit der folgenden Struktur zusammenführen:

     
        xml version="1.0" encoding="utf-8"
        >
            <PublishData>
              <PublishProfile PublishMethod="AzureServiceManagementAPI"
                Url="https://management.core.windows.net/"
                ManagementCertificate="xxxxx">
                <Subscription Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
                  Name="hjereztest" />
              </PublishProfile>
            </PublishData>

Wobei xxxxx der Inhalt der [codierten Datei] ist. Geben Sie die Details für die Linux Azure-Befehlszeilentools mit den folgenden Befehlen an: Azure account import (File)

