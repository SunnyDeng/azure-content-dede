<properties linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="How to create and deploy" pageTitle="How to create and deploy a cloud service - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="" editor="" />

Erstellen und Bereitstellen eines Clouddiensts
==============================================

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Das Azure-Verwaltungsportal stellt zwei Methoden zum Erstellen und Bereitstellen eines Clouddiensts bereit: **Schnellerstellung** und **Benutzerdefinierte Erstellung**.

In diesem Thema wird erläutert, wie Sie die Schnellerstellungsmethode zum Erstellen eines neuen Clouddiensts und dann **Hochladen** verwenden, um ein Clouddienstpaket in Azure hochzuladen und bereitzustellen. Wenn Sie diese Methode verwenden, stellt das Azure-Verwaltungsportal praktische Links zum Erfüllen aller Anforderungen zur Verfügung. Wenn Sie Ihren Clouddienst bei der Erstellung auch bereitstellen möchten, können Sie beides mithilfe von **Benutzerdefinierte Erstellung** durchführen.

**Hinweis** Wenn Sie Ihren Clouddienst von Windows Team Foundation Services (TFS) aus veröffentlichen möchten, verwenden Sie Schnellerstellung. Richten Sie die TFS-Veröffentlichung dann über **Schnellstart** oder das Dashboard ein. Weitere Informationen finden Sie im Thema zur [Fortlaufenden Bereitstellung für Windows Azure mithilfe von Team Foundation Service](http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409) oder in der Hilfe zur **Schnellstart**-Seite.

Inhaltsverzeichnis
------------------

-   [Konzepte](#concepts)
-   [Vorbereiten Ihrer App](#prepare)
-   [Bevor Sie beginnen](#begin)
-   [Vorgehensweise: Erstellen eines Clouddiensts mithilfe der Schnellerstellung](#quick)
-   [Vorgehensweise: Hochladen eines Zertifikats für einen Clouddienst](#uploadcertificate)
-   [Vorgehensweise: Bereitstellen eines Clouddiensts](#deploy)

Konzepte
--------

Für die Bereitstellung einer Anwendung als Clouddienst in Azure sind drei Komponenten erforderlich:

> -   **Dienstdefinitionsdatei** Die Clouddienst-Definitionsdatei (CSDEF) definiert das Dienstmodell einschließlich der Rollenanzahl.

> -   **Dienstkonfigurationsdatei** Die Clouddienst-Konfigurationsdatei (CSCFG) stellt Konfigurationseinstellungen für den Clouddienst und individuelle Rollen bereit, einschließlich der Anzahl der Rolleninstanzen.

> -   **Dienstpaket** Das Dienstpaket (CSPKG) enthält den Anwendungscode und die Dienstdefinitionsdatei.

Vorbereiten Ihrer App
---------------------

Bevor Sie einen Clouddienst bereitstellen können, müssen Sie das Clouddienstpaket (CSPKG) aus dem Anwendungscode und eine Clouddienst-Konfigurationsdatei (CSCFG) erstellen. Jedes Clouddienstpaket enthält Anwendungsdateien und -konfigurationen. Die Dienstkonfigurationsdatei stellt die Konfigurationseinstellungen bereit.

Das Azure-SDK stellt Tools zum Vorbereiten dieser erforderlichen Bereitstellungsdateien bereit. Sie können das SDK auf der Seite [Azure-Downloads](http://www.windowsazure.com/de-de/develop/downloads/) in der Sprache herunterladen, in der Sie den Anwendungscode entwickeln möchten.

Wenn Sie Clouddienste zum ersten Mal verwenden, können Sie ein beispielhaftes Clouddienstpaket (CSPKG) und eine Clouddienst-Konfigurationsdatei (CSCFG) aus den [Azure-Codebeispielen](http://code.msdn.microsoft.com/windowsazure/) herunterladen.

Drei Clouddienstfunktionen benötigen vor dem Export eines Dienstpakets spezielle Konfigurationen:

-   Wenn Sie einen Clouddienst bereitstellen möchten, der Secure Sockets Layer (SSL) für die Datenverschlüsselung verwendet, konfigurieren Sie die Anwendung für SSL. Weitere Informationen finden Sie unter [Konfigurieren eines SSL-Zertifikats auf einem HTTPS-Endpunkt](http://msdn.microsoft.com/de-de/library/windowsazure/ff795779.aspx).

-   Wenn Sie Remotedesktopverbindungen zu Rolleninstanzen konfigurieren möchten, konfigurieren Sie die Rollen für Remotedesktop. Weitere Informationen zum Vorbereiten der Dienstdefinitionsdateien für den Remotezugriff finden Sie im [Überblick über das Einrichten einer Remotedesktopverbindung für eine Rolle](http://msdn.microsoft.com/de-de/library/windowsazure/gg433010.aspx).

-   Wenn Sie die ausführliche Überwachung für den Clouddienst konfigurieren möchten, aktivieren Sie für den Clouddienst die Azure-Diagnose. *Minimale Überwachung* (die Standardüberwachungsstufe) verwendet Leistungsindikatoren, die aus den Hostbetriebssystemen für Rolleninstanzen (virtuelle Computer) erfasst wurden. Bei der "ausführlichen Überwachung" werden zusätzliche Kennzahlen basierend auf Leistungsdaten innerhalb der Rolleninstanzen erfasst, um eine genauere Analyse von Problemen zu ermöglichen, die während der Anwendungsverarbeitung auftreten. Informationen zum Aktivieren der Azure-Diagnose finden Sie unter [Aktivieren der Diagnose in Azure](http://www.windowsazure.com/de-de/develop/net/common-tasks/diagnostics/).

Bevor Sie beginnen
------------------

-   Falls Sie das Azure-SDK noch nicht installiert haben, klicken Sie auf **Azure-SDK installieren**, um die [Azure-Downloadseite](http://www.windowsazure.com/de-de/develop/downloads/) zu öffnen. Laden Sie dann das SDK für die Sprache herunter, in der Sie den Code entwickeln möchten. (Dazu haben Sie auch später noch die Möglichkeit.)

-   Falls Rolleninstanzen ein Zertifikat erfordern, erstellen Sie die Zertifikate. Clouddienste erfordern eine PFX-Datei mit einem privaten Schlüssel. Sie können die Zertifikate zu Azure hochladen, wenn Sie den Clouddienst erstellen und bereitstellen. Informationen zum Erstellen von Zertifikaten finden Sie unter [Konfigurieren eines SSL-Zertifikats auf einem HTTPS-Endpunkt](http://msdn.microsoft.com/de-de/library/windowsazure/ff795779.aspx).

-   Wenn Sie den Clouddienst für eine Affinitätsgruppe bereitstellen möchten, erstellen Sie die Affinitätsgruppe. Sie können eine Affinitätsgruppe verwenden, um den Clouddienst und andere Azure-Dienste für den gleichen Standort in einer Region bereitzustellen. Sie können die Affinitätsgruppe im Bereich **Netzwerke** des Verwaltungsportals auf der Seite **Affinitätsgruppen** erstellen. Weitere Informationen finden Sie in der Hilfe zur Seite **Affinitätsgruppen**.

Vorgehensweise: Erstellen eines Clouddiensts mithilfe der Schnellerstellung
---------------------------------------------------------------------------

1.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Neu**, klicken Sie auf **Clouddienst** und dann auf **Schnellerstellung**.

    ![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2.  Geben Sie unter **URL** einen Unterdomänennamen ein, der in der öffentlichen URL für den Zugriff auf Ihren Clouddienst in Produktionsbereitstellungen verwendet werden soll. Das URL-Format für Produktionsbereitstellungen lautet: http://*meineURL*.cloudapp.net.

3.  Wählen Sie unter **Region/Affinitätsgruppe** die geografische Region oder die Affinitätsgruppe aus, für die der Clouddienst bereitgestellt werden soll. Wählen Sie eine Affinitätsgruppe aus, wenn Sie den Clouddienst für den gleichen Standort wie andere Azure-Dienste innerhalb einer Region bereitstellen möchten.

    > [WACOM.NOTE] Um eine Affinitätsgruppe zu erstellen, öffnen Sie den Bereich **Netzwerke** des Verwaltungsportals, klicken Sie auf **Affinitätsgruppen** und klicken Sie dann auf **Neue Affinitätsgruppe erstellen** oder **Erstellen**. Sie können Affinitätsgruppen verwenden, die Sie im früheren Azure-Verwaltungsportal erstellt haben. Sie können Affinitätsgruppen außerdem mithilfe der Azure-Dienstverwaltungs-API erstellen und verwalten. Weitere Informationen finden Sie unter [Vorgänge für Affinitätsgruppen](http://msdn.microsoft.com/de-de/library/windowsazure/ee460798.aspx).

4.  Klicken Sie auf **Clouddienst erstellen**.

    Sie können den Status des Prozesses im Nachrichtenbereich unten im Fenster überwachen.

    Der Bereich **Clouddienste** wird geöffnet und der neue Clouddienst wird angezeigt. Wenn der Status auf "Erstellt" geändert wird, wurde die Erstellung des Clouddiensts erfolgreich abgeschlossen.

    ![CloudServices\_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

    Falls Rollen im Clouddienst ein Zertifikat für die Datenverschlüsselung mit Secure Sockets Layer (SSL) erfordern und das Zertifikat nicht zu Azure hochgeladen wurde, müssen Sie das Zertifikat hochladen, bevor Sie den Clouddienst bereitstellen. Nachdem Sie ein Zertifikat hochgeladen haben, können alle Windows-Anwendungen, die in den Rolleninstanzen ausgeführt werden, auf das Zertifikat zugreifen.

Vorgehensweise: Hochladen eines Zertifikats für einen Clouddienst
-----------------------------------------------------------------

1.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Clouddienste**. Klicken Sie dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.

    ![CloudServices\_EmptyDashboard](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)

2.  Klicken Sie auf **Zertifikate**, um die unten angezeigte Seite **Zertifikate** zu öffnen.

    ![CloudServices\_CertificatesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png)

3.  Klicken Sie auf **Neues Zertifikat hinzufügen** oder auf **Hochladen**. **Zertifikat hinzufügen** wird geöffnet.

    ![CloudServices\_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

4.  Verwenden Sie in **Zertifikatsdatei** die Option **Durchsuchen**, um das zu verwendende Zertifikat (PFX-Datei) auszuwählen.

5.  Geben Sie in **Kennwort** den privaten Schlüssel für das Zertifikat ein.

6.  Klicken Sie auf "OK" (Häkchen).

    Sie können des Fortschritt des Hochladens im unten angezeigten Nachrichtenbereich überwachen. Wenn das Hochladen abgeschlossen ist, wird das Zertifikat zur Tabelle hinzugefügt. Klicken Sie im Nachrichtenbereich auf den Pfeil nach unten, um die Nachricht zu schließen, oder auf das X, um die Nachricht zu entfernen.

    ![CloudServices\_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

    Sie können den Clouddienst aus dem Dashboard oder aus dem **Schnellstart** bereitstellen.

Vorgehensweise: Bereitstellen eines Clouddiensts
------------------------------------------------

1.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Clouddienste**. Klicken Sie dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.

2.  Klicken Sie auf **Schnellstart** (das Symbol links neben **Dashboard**), um die unten angezeigte Seite **Schnellstart** zu öffnen. (Sie können Ihren Clouddienst auch mithilfe von **Hochladen** auf dem Dashboard bereitstellen.)

    ![CloudServices\_QuickStartPage](./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png)

3.  Falls Sie das Azure-SDK noch nicht installiert haben, klicken Sie auf **Azure-SDK installieren**, um die [Azure-Downloadseite](http://www.windowsazure.com/de-de/develop/downloads/) zu öffnen. Laden Sie dann das SDK für die Sprache herunter, in der Sie den Code entwickeln möchten.

    Auf der Downloadseite können Sie außerdem Clientbibliotheken und Quellcode für die Entwicklung von Web-Apps in Node.js, Java, PHP und anderen Sprachen installieren, die Sie als skalierbare Azure-Clouddienste bereitstellen können.

    > [WACOM.NOTE] Für zuvor erstellte Clouddienste (die zuvor als *gehostete Dienste* bezeichnet wurden) müssen Sie sicherstellen, dass die Gastbetriebssysteme auf den virtuellen Computern (Rolleninstanzen) mit der von Ihnen installierten Azure-SDK-Version kompatibel sind. Weitere Informationen finden Sie in den [Versionshinweisen zum Azure-SDK](http://msdn.microsoft.com/de-de/library/windowsazure/hh552718.aspx).

4.  Klicken Sie auf **Neue Produktionsbereitstellung** oder **Neue Stagingbereitstellung**.

    Wenn Sie den Clouddienst in Azure vor der Bereitstellung für die Produktion testen möchten, können Sie eine Stagingbereitstellung vornehmen. In der Stagingumgebung identifiziert die GUID (Globally Unique Identifier) des Clouddiensts den Clouddienst in URLs (*GUID*.cloudapp.net). In der Produktionsumgebung wird das von Ihnen zugewiesene benutzerfreundlichere DNS-Präfix verwendet (zum Beispiel *meinDienst*.cloudapp.net). Wenn Sie den Clouddienst in der Stagingumgebung für die Produktion heraufstufen möchten, verwenden Sie **Tauschen**, um Clientanforderungen an diese Bereitstellung umzuleiten.

    Wenn Sie eine Bereitstellungsumgebung auswählen, wird **Paket hochladen** geöffnet.

    ![CloudServices\_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

5.  Geben Sie unter **Bereitstellungsname** einen Namen für die neue Bereitstellung ein, zum Beispiel "MeinClouddienstv1".

6.  Verwenden Sie unter **Paket** die Optionen **Durchsuchen**, um die zu verwendende Dienstpaketdatei (CSPKG) auszuwählen.

7.  Verwenden Sie unter **Konfiguration** die Option **Durchsuchen**, um die zu verwendende Dienstkonfigurationsdatei (CSCFG) auszuwählen.

8.  Falls der Clouddienst Rollen mit nur einer Instanz umfasst, aktivieren Sie das Kontrollkästchen mit der Bezeichnung wie **Auch bereitstellen, wenn eine oder mehrere Rollen eine einzelne Instanz enthalten**, um das Fortsetzen der Bereitstellung zu ermöglichen.

Azure kann nur dann 99,95 % Zugriff auf den Clouddienst während Wartungen und Dienstaktualisierungen garantieren, wenn jede Rolle über mindestens zwei Instanzen verfügt. Bei Bedarf können Sie zusätzliche Rolleninstanzen auf der Seite **Skalieren** nach der Bereitstellung des Clouddiensts hinzufügen. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel (SLAs)](http://www.windowsazure.com/de-de/support/legal/sla/).

1.  Klicken Sie auf "OK" (Häkchen), um die Clouddienstbereitstellung zu starten.

    Sie können den Status der Bereitstellung im Nachrichtenbereich überwachen. Klicken Sie auf den Pfeil nach unten, um die Nachricht auszublenden.

    ![CloudServices\_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

### So überprüfen Sie, ob Ihre Bereitstellung erfolgreich abgeschlossen wurde

1.  Klicken Sie auf **Dashboard**.

2.  Klicken Sie unter **Schnellansicht** auf die Website-URL, um den Clouddienst in einem Webbrowser zu öffnen.

<!-- -->

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)
