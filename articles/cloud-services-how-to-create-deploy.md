<properties linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="How to create and deploy" pageTitle="How to create and deploy a cloud service - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Erstellen und Bereitstellen eines Cloud-Diensts

[WACOM.INCLUDE [Haftungsausschluss](../includes/disclaimer.md)]

Das Azure-Verwaltungsportal stellt zwei Methoden zum Erstellen und Bereitstellen eines Cloud-Diensts bereit: **Schnellerstellung** und **Benutzerdefinierte Erstellung**.

In diesem Thema wird erläutert, wie Sie die Schnellerstellungsmethode zum Erstellen eines neuen Cloud-Diensts und dann **Hochladen** verwenden, um ein Cloud-Dienstpaket in Azure hochzuladen und bereitzustellen. Wenn Sie diese Methode verwenden, stellt das Azure-Verwaltungsportal praktische Links zum Erfüllen aller Anforderungen zur Verfügung. Wenn Sie Ihren Cloud-Dienst bei der Erstellung auch bereitstellen möchten, können Sie beides mithilfe von **Benutzerdefinierte Erstellung** durchführen.

**Hinweis** Wenn Sie Ihren Cloud-Dienst von Windows Team Foundation Services (TFS) aus veröffentlichen möchten, verwenden Sie Schnellerstellung. Richten Sie die TFS-Veröffentlichung dann über **Schnellstart** oder das Dashboard ein. Weitere Informationen finden Sie unter [Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online][TFSTutorialForCloudService] oder in der Hilfe zur **Schnellstart**-Seite.

## Inhaltsverzeichnis

-   [Konzepte](#concepts)
-   [Vorbereiten Ihrer App](#prepare)
-   [Vorbemerkungen][Vorbemerkungen]
-   [Gewusst wie: Erstellen eines Cloud-Diensts mithilfe der Schnellerstellung](#quick)
-   [Gewusst wie: Hochladen eines Zertifikats für einen Cloud-Dienst](#uploadcertificate)
-   [Gewusst wie: Bereitstellen eines Cloud-Diensts](#deploy)

## <span id="concepts"></span></a>Konzepte

Für die Bereitstellung einer Anwendung als Cloud-Dienst in Azure sind drei Komponenten erforderlich:

> -   **Dienstdefinitionsdatei** Die Cloud-Dienstdefinitionsdatei (CSDEF) definiert das Dienstmodell einschließlich der Rollenanzahl.

> -   **Dienstkonfigurationsdatei** Die Cloud-Dienstkonfigurationsdatei (CSCFG) stellt Konfigurationseinstellungen für den Cloud-Dienst und individuelle Rollen bereit, einschließlich der Anzahl der Rolleninstanzen.

> -   **Dienstpaket** Das Dienstpaket (CSPKG) enthält den Anwendungscode und die Dienstdefinitionsdatei.

## <span id="prepare"></span></a>Vorbereiten Ihrer App

Bevor Sie einen Cloud-Dienst bereitstellen können, müssen Sie das Cloud-Dienstpaket (CSPKG) aus dem Anwendungscode und eine Cloud-Dienstkonfigurationsdatei (CSCFG) erstellen. Jedes Cloud-Dienstpaket enthält Anwendungsdateien und -konfigurationen. Die Dienstkonfigurationsdatei stellt die Konfigurationseinstellungen bereit.

Das Azure-SDK stellt Tools zum Vorbereiten dieser erforderlichen Bereitstellungsdateien bereit. Sie können das SDK auf der Seite [Azure-Downloads](http://www.windowsazure.com/de-de/develop/downloads/) in der Sprache herunterladen, in der Sie den Anwendungscode entwickeln möchten.

Wenn Sie Cloud-Dienste zum ersten Mal verwenden, können Sie ein beispielhaftes Cloud-Dienstpaket (CSPKG) und eine Cloud-Dienstkonfigurationsdatei (CSCFG) aus den [Azure-Codebeispielen](http://code.msdn.microsoft.com/windowsazure/).  herunterladen.

Drei Cloud-Dienstfunktionen benötigen vor dem Export eines Dienstpakets spezielle Konfigurationen:

-   Wenn Sie einen Cloud-Dienst bereitstellen möchten, der Secure Sockets Layer (SSL) für die Datenverschlüsselung verwendet, konfigurieren Sie die Anwendung für SSL. Weitere Informationen finden Sie unter [Konfigurieren eines SSL-Zertifikats auf einem HTTPS-Endpunkt](http://msdn.microsoft.com/de-de/library/windowsazure/ff795779.aspx).

-   Wenn Sie Remotedesktopverbindungen zu Rolleninstanzen konfigurieren möchten, konfigurieren Sie die Rollen für Remotedesktop. Weitere Informationen zum Vorbereiten der Dienstdefinitionsdateien für den Remotezugriff finden Sie im [Überblick über das Einrichten einer Remotedesktopverbindung für eine Rolle](http://msdn.microsoft.com/de-de/library/windowsazure/gg433010.aspx).

-   Wenn Sie die ausführliche Überwachung für den Cloud-Dienst konfigurieren möchten, aktivieren Sie für den Cloud-Dienst die Azure-Diagnose. *Minimale Überwachung* (die Standardüberwachungsstufe) verwendet Leistungsindikatoren, die aus den Hostbetriebssystemen für Rolleninstanzen (virtuelle Computer) erfasst wurden. Bei der "ausführlichen Überwachung" werden zusätzliche Kennzahlen basierend auf Leistungsdaten innerhalb der Rolleninstanzen erfasst, um eine genauere Analyse von Problemen zu ermöglichen, die während der Anwendungsverarbeitung auftreten. Informationen zum Aktivieren der Azure-Diagnose finden Sie unter [Aktivieren der Diagnose in Azure](http://www.windowsazure.com/de-de/develop/net/common-tasks/diagnostics/).

## <span id="begin"></span></a>Vorbemerkungen

-   Falls Sie das Azure-SDK noch nicht installiert haben, klicken Sie auf **Azure-SDK installieren**, um die [Azure-Downloadseite](http://www.windowsazure.com/de-de/develop/downloads/) zu öffnen. Laden Sie dann das SDK für die Sprache herunter, in der Sie den Code entwickeln möchten. (Dazu haben Sie auch später noch die Möglichkeit.)

-   Falls Rolleninstanzen ein Zertifikat erfordern, erstellen Sie die Zertifikate. Cloud-Dienste erfordern eine PFX-Datei mit einem privaten Schlüssel. Sie können die Zertifikate zu Azure hochladen, wenn Sie den Cloud-Dienst erstellen und bereitstellen. Informationen zum Erstellen von Zertifikaten finden Sie unter [Konfigurieren eines SSL-Zertifikats auf einem HTTPS-Endpunkt](http://msdn.microsoft.com/de-de/library/windowsazure/ff795779.aspx).

-   Wenn Sie den Cloud-Dienst für eine Affinitätsgruppe bereitstellen möchten, erstellen Sie die Affinitätsgruppe. Sie können eine Affinitätsgruppe verwenden, um den Cloud-Dienst und andere Azure-Dienste für den gleichen Standort in einer Region bereitzustellen. Sie können die Affinitätsgruppe im Bereich **Netzwerke** des Verwaltungsportals auf der Seite **Affinitätsgruppen** erstellen. Weitere Informationen finden Sie in der Hilfe zur Seite **Affinitätsgruppen**.

## <span id="quick"></span></a>Gewusst wie: Erstellen eines Cloud-Diensts mithilfe der Schnellerstellung

1.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Neu**, **Server** und **Cloud-Dienst** und dann auf **Schnellerstellung**.

    ![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2.  Geben Sie unter **URL** einen Unterdomänennamen ein, der in der öffentlichen URL für den Zugriff auf Ihren Cloud-Dienst in Produktionsbereitstellungen verwendet werden soll. Das URL-Format für Produktionsbereitstellungen lautet: http://*myURL*.cloudapp.net.

3.  Wählen Sie unter **Region oder Affinitätsgruppe** die geografische Region oder die Affinitätsgruppe aus, für die der Cloud-Dienst bereitgestellt werden soll. Wählen Sie eine Affinitätsgruppe aus, wenn Sie den Cloud-Dienst für den gleichen Standort wie andere Azure-Dienste innerhalb einer Region bereitstellen möchten.

    > [WACOM.NOTE] 
    > Öffnen Sie den Bereich **Netzwerke** des Verwaltungsportals, klicken Sie auf **Affinitätsgruppen** und dann entweder auf **Neue Affinitätsgruppe erstellen** oder auf **Erstellen**, um eine Affinitätsgruppe zu erstellen. Sie können Affinitätsgruppen verwenden, die Sie im früheren Azure-Verwaltungsportal erstellt haben. Sie können Affinitätsgruppen außerdem mithilfe der Azure-Dienstverwaltungs-API erstellen und verwalten. Weitere Informationen finden Sie unter [Vorgänge für Affinitätsgruppen](http://msdn.microsoft.com/de-de/library/windowsazure/ee460798.aspx).

4.  Klicken Sie auf **Cloud-Dienst erstellen**.

    Sie können den Status des Prozesses im Meldungsbereich unten im Fenster überwachen.

    Der Bereich **Cloud-Dienste** wird geöffnet und der neue Cloud-Dienst wird angezeigt. Wenn der Status auf "Erstellt" geändert wird, wurde die Erstellung des Cloud-Diensts erfolgreich abgeschlossen.

    ![CloudServices\_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

    Falls Rollen im Cloud-Dienst ein Zertifikat für die Datenverschlüsselung mit Secure Sockets Layer (SSL) erfordern und das Zertifikat nicht zu Azure hochgeladen wurde, müssen Sie das Zertifikat hochladen, bevor Sie den Cloud-Dienst bereitstellen. Nachdem Sie ein Zertifikat hochgeladen haben, können alle Windows-Anwendungen, die in den Rolleninstanzen ausgeführt werden, auf das Zertifikat zugreifen.

## <span id="uploadcertificate"></span></a>Gewusst wie: Hochladen eines Zertifikats für einen Cloud-Dienst

1.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Cloud-Dienste**. Klicken Sie anschließend auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen.

    ![CloudServices\_EmptyDashboard](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2.  Klicken Sie auf **Zertifikate**, um die unten angezeigte Seite **Zertifikate** zu öffnen.

    ![CloudServices\_CertificatesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png)

3.  Klicken Sie auf **Zertifikat hochladen**.
     **Zertifikat hochladen** wird geöffnet.

    ![CloudServices\_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

4.  Verwenden Sie in **Zertifikatsdatei** die Option **Durchsuchen**, um das zu verwendende Zertifikat (PFX- oder CER-Datei) auszuwählen.

5.  Geben Sie in **Kennwort** den privaten Schlüssel für das Zertifikat ein.

6.  Klicken Sie auf "OK" (Häkchen).

    Sie können des Fortschritt des Hochladens im unten angezeigten Nachrichtenbereich überwachen. Wenn das Hochladen abgeschlossen ist, wird das Zertifikat zur Tabelle hinzugefügt. Klicken Sie im Nachrichtenbereich auf den Pfeil nach unten, um die Nachricht zu schließen, oder auf das X, um die Nachricht zu entfernen.

    ![CloudServices\_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

    Sie können den Cloud-Dienst aus dem Dashboard oder aus dem **Schnellstart** bereitstellen.

## <span id="deploy"></span></a>Gewusst wie: Bereitstellen eines Cloud-Diensts

1.  Falls Sie das Azure-SDK noch nicht installiert haben, klicken Sie auf **Azure-SDK installieren**, um die [Azure-Downloadseite](http://www.windowsazure.com/de-de/develop/downloads/) zu öffnen. Laden Sie dann das SDK für die Sprache herunter, in der Sie den Code entwickeln möchten.

    Auf der Downloadseite können Sie außerdem Clientbibliotheken und Quellcode für die Entwicklung von Web-Apps in Node.js, Java, PHP und anderen Sprachen installieren, die Sie als skalierbare Azure-Cloud-Dienste bereitstellen können.

    > [WACOM.NOTE]
    > Für zuvor erstellte Cloud-Dienste (die zuvor als *gehostete Dienste* bezeichnet wurden) müssen Sie sicherstellen, dass die Gastbetriebssysteme auf den virtuellen Computern (Rolleninstanzen) mit der von Ihnen installierten Azure-SDK-Version kompatibel sind. Weitere Informationen finden Sie in den [Versionshinweisen zum Azure-SDK](http://msdn.microsoft.com/de-de/library/windowsazure/hh552718.aspx).

2.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Cloud-Dienste**. Klicken Sie anschließend auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen, und klicken Sie auf **Dashboard**.

3.  Klicken Sie auf **Produktion** oder **Staging** aus.

    Wenn Sie den Cloud-Dienst in Azure vor der Bereitstellung für die Produktion testen möchten, können Sie eine Stagingbereitstellung vornehmen. In der Stagingumgebung identifiziert die GUID (Globally Unique Identifier) des Cloud-Diensts den Cloud-Dienst in URLs (*GUID*.cloudapp.net). In der Produktionsumgebung wird das von Ihnen zugewiesene benutzerfreundlichere DNS-Präfix verwendet (zum Beispiel *meinDienst*.cloudapp.net). Wenn Sie den Cloud-Dienst in der Stagingumgebung für die Produktion heraufstufen möchten, verwenden Sie **Tauschen**, um Clientanforderungen an diese Bereitstellung umzuleiten.

    ![CloudServices\_QuickStartPage](./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png)

4.  Klicken Sie im Dashboard auf **Hochladen**, um Ihren Cloud-Dienst bereitzustellen.

    ![CloudServices\_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

5.  Geben Sie unter **Bereitstellungsbezeichnung** einen Namen für die neue Bereitstellung ein, zum Beispiel "MeinClouddienstv1".

6.  Verwenden Sie unter **Paket** die Optionen **Durchsuchen**, um die zu verwendende Dienstpaketdatei (CSPKG) auszuwählen.

7.  Verwenden Sie unter **Konfiguration** die Option **Durchsuchen**, um die zu verwendende Dienstkonfigurationsdatei (CSCFG) auszuwählen.

8.  Falls der Cloud-Dienst Rollen mit nur einer Instanz umfasst, aktivieren Sie das Kontrollkästchen mit der Bezeichnung wie **Auch bereitstellen, wenn eine oder mehrere Rollen eine einzelne Instanz enthalten**, um das Fortsetzen der Bereitstellung zu ermöglichen.

Azure kann nur dann 99,95 % Zugriff auf den Cloud-Dienst während Wartungen und Dienstaktualisierungen garantieren, wenn jede Rolle über mindestens zwei Instanzen verfügt. Bei Bedarf können Sie zusätzliche Rolleninstanzen auf der Seite **Skalieren** nach der Bereitstellung des Cloud-Diensts hinzufügen. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](http://www.windowsazure.com/de-de/support/legal/sla/).

1.  Klicken Sie auf "OK" (Häkchen), um die Cloud-Dienstbereitstellung zu starten.

    Sie können den Status der Bereitstellung im Nachrichtenbereich überwachen. Klicken Sie auf den Pfeil nach unten, um die Nachricht auszublenden.

    ![CloudServices\_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

### So überprüfen Sie, ob Ihre Bereitstellung erfolgreich abgeschlossen wurde

1.  Klicken Sie auf **Dashboard**.

2.  Klicken Sie unter **Schnellansicht** auf die Website-URL, um den Cloud-Dienst in einem Webbrowser zu öffnen.

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

  [Haftungsausschluss]: ../includes/disclaimer.md
  [Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
  [Konzepte]: #concepts
  [Vorbereiten Ihrer App]: #prepare
  [Vorbemerkungen]: #begin
  [Gewusst wie: Erstellen eines Cloud-Diensts mithilfe der Schnellerstellung]: #quick
  [Gewusst wie: Hochladen eines Zertifikats für einen Cloud-Dienst]: #uploadcertificate
  [Gewusst wie: Bereitstellen eines Cloud-Diensts]: #deploy
  [Azure-Downloads]: http://www.windowsazure.com/de-de/develop/downloads/
  [Azure-Codebeispielen]: http://code.msdn.microsoft.com/windowsazure/
  [Konfigurieren eines SSL-Zertifikats auf einem HTTPS-Endpunkt]: http://msdn.microsoft.com/de-de/library/windowsazure/ff795779.aspx
  [Überblick über das Einrichten einer Remotedesktopverbindung für eine Rolle]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433010.aspx
  [Aktivieren der Diagnose in Azure]: http://www.windowsazure.com/de-de/develop/net/common-tasks/diagnostics/
  [Verwaltungsportal]: http://manage.windowsazure.com/
  [CloudServices\_QuickCreate]: ./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png
  [http://\*meineURL]: http://*myURL
  [Vorgänge für Affinitätsgruppen]: http://msdn.microsoft.com/de-de/library/windowsazure/ee460798.aspx
  [CloudServices\_CloudServicesPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png
  [CloudServices\_EmptyDashboard]: ./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png
  [CloudServices\_CertificatesPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png
  [CloudServices\_AddaCertificate]: ./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png
  [CloudServices\_CertificateProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png
  [Versionshinweisen zum Azure-SDK]: http://msdn.microsoft.com/de-de/library/windowsazure/hh552718.aspx
  [CloudServices\_QuickStartPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png
  [CloudServices\_UploadaPackage]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png
  [Vereinbarungen zum Servicelevel]: http://www.windowsazure.com/de-de/support/legal/sla/
  [CloudServices\_UploadProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png
