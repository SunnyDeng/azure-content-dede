<properties 
	pageTitle="Erstellen und Bereitstellen eines Cloud-Diensts - Azure" 
	description="Hier erfahren Sie, wie Sie einen Cloud-Dienst mithilfe der Schnellerfassung in Azure erstellen und bereitstellen." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="adegeo"/>





# Erstellen und Bereitstellen eines Cloud-Diensts

Das Azure-Verwaltungsportal stellt zwei Methoden zum Erstellen und Bereitstellen eines Cloud-Diensts bereit: **Schnellerstellung** und **Benutzerdefinierte Erstellung**. 

In diesem Thema wird erläutert, wie Sie die Schnellerstellungsmethode zum Erstellen eines neuen Cloud-Diensts und dann **Hochladen** verwenden, um ein Cloud-Dienstpaket in Azure hochzuladen und bereitzustellen. Wenn Sie diese Methode verwenden, stellt das Azure-Verwaltungsportal praktische Links zum Erfüllen aller Anforderungen zur Verfügung. Wenn Sie Ihren Cloud-Dienst bei der Erstellung auch bereitstellen möchten, können Sie beides mithilfe von **Benutzerdefinierte Erstellung** durchführen. 

**Hinweis**   Wenn Sie Ihren Cloud-Dienst von Windows Team Foundation Services (TFS) aus veröffentlichen möchten, verwenden Sie Schnellerstellung. Richten Sie die TFS-Veröffentlichung dann über **Schnellstart** oder das Dashboard ein. Weitere Informationen finden Sie unter [Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online][TFSTutorialForCloudService] oder in der Hilfe zur **Schnellstart**-Seite.

## Inhaltsverzeichnis##

* [Konzepte](#concepts)
* [Vorbereiten Ihrer App](#prepare)
* [Vorbemerkungen](#begin)
* [Vorgehensweise: Erstellen eines Cloud-Diensts mithilfe der Schnellerstellung](#quick)
* [Vorgehensweise: Hochladen eines Zertifikats für einen Cloud-Dienst](#uploadcertificate)
* [Vorgehensweise: Bereitstellen eines Cloud-Diensts](#deploy)


<h2><a id="concepts"></a>Konzepte</h2>
Für die Bereitstellung einer Anwendung als Cloud-Dienst in Azure sind drei Komponenten erforderlich:

>- **Dienstdefinitionsdatei**   Die Dienstdefinitionsdatei (CSDEF) definiert das Dienstmodell einschließlich der Rollenanzahl.

>- **Dienstkonfigurationsdatei**   Die Cloud-Dienstkonfigurationsdatei (CSCFG) stellt Konfigurationseinstellungen für den Cloud-Dienst und individuelle Rollen bereit, einschließlich der Anzahl der Rolleninstanzen.

>- **Dienstpaket**   Das Dienstpaket (CSPKG) enthält den Anwendungscode und die Dienstdefinitionsdatei.

<h2><a id="prepare"></a>Vorbereiten Ihrer App</h2>
Bevor Sie einen Cloud-Dienst bereitstellen können, müssen Sie das Cloud-Dienstpaket (CSPKG) aus dem Anwendungscode und eine Cloud-Dienstkonfigurationsdatei (CSCFG) erstellen. Jedes Cloud-Dienstpaket enthält Anwendungsdateien und -konfigurationen. Die Dienstkonfigurationsdatei stellt die Konfigurationseinstellungen bereit.

Das Azure-SDK stellt Tools zum Vorbereiten dieser erforderlichen Bereitstellungsdateien bereit. Sie können das SDK auf der Seite [Azure-Downloads](http://azure.microsoft.com/develop/downloads/) in der Sprache herunterladen, in der Sie den Anwendungscode entwickeln möchten.

Drei Cloud-Dienstfunktionen benötigen vor dem Export eines Dienstpakets spezielle Konfigurationen:

- Wenn Sie einen Cloud-Dienst bereitstellen möchten, der Secure Sockets Layer (SSL) für die Datenverschlüsselung verwendet, konfigurieren Sie die Anwendung für SSL. Weitere Informationen finden Sie unter [Konfigurieren eines SSL-Zertifikats auf einem HTTPS-Endpunkt](http://msdn.microsoft.com/library/windowsazure/ff795779.aspx).

- Wenn Sie Remotedesktopverbindungen zu Rolleninstanzen konfigurieren möchten, konfigurieren Sie die Rollen für Remotedesktop. Weitere Informationen zum Vorbereiten der Dienstdefinitionsdateien für den Remotezugriff finden Sie unter [Einrichten einer Remotedesktopverbindung für eine Rolle in Azure](http://msdn.microsoft.com/library/hh124107.aspx).

- Wenn Sie die ausführliche Überwachung für den Cloud-Dienst konfigurieren möchten, aktivieren Sie für den Cloud-Dienst die Azure-Diagnose. *Minimal monitoring* (die Standardüberwachungsstufe) verwendet Leistungsindikatoren, die aus den Hostbetriebssystemen für Rolleninstanzen (virtuelle Computer) erfasst wurden. Bei der "ausführlichen Überwachung" werden zusätzliche Kennzahlen basierend auf Leistungsdaten innerhalb der Rolleninstanzen erfasst, um eine genauere Analyse von Problemen zu ermöglichen, die während der Anwendungsverarbeitung auftreten. Informationen zum Aktivieren der Azure-Diagnose finden Sie unter [Aktivieren der Diagnose in Azure](http://azure.microsoft.com/develop/net/common-tasks/diagnostics/).

- Sie müssen das Dienstpaket erstellen, um einen Cloud-Dienst mit Bereitstellungen von Webrollen oder Workerrollen zu erstellen. Informationen zu den Dateien dieses Pakets erhalten Sie unter [Einrichten eines Cloud-Diensts für Azure](http://msdn.microsoft.com/library/hh124108.aspx). Informationen zur Erstellung des Pakets finden Sie unter [Packen einer Microsoft Azure-Anwendung](http://msdn.microsoft.com/library/hh403979.aspx). Wenn Sie Visual Studio zur Entwicklung Ihrer Anwendung verwenden, finden Sie weitere Informationen unter [Veröffentlichen eines Cloud-Diensts mit Azure Tools](http://msdn.microsoft.com/library/ff683672.aspx).

<h2><a id="begin"></a>Voraussetzungen</h2>

- Falls Sie das Azure-SDK noch nicht installiert haben, klicken Sie auf **Azure-SDK installieren**, um die [Azure-Downloadseite](http://azure.microsoft.com/develop/downloads/) zu öffnen. Laden Sie dann das SDK für die Sprache herunter, in der Sie den Code entwickeln möchten. (Dazu haben Sie auch später noch die Möglichkeit.)

- Falls Rolleninstanzen ein Zertifikat erfordern, erstellen Sie die Zertifikate. Cloud-Dienste erfordern eine PFX-Datei mit einem privaten Schlüssel. Sie können die Zertifikate zu Azure hochladen, wenn Sie den Cloud-Dienst erstellen und bereitstellen. Weitere Informationen zu Zertifikaten finden Sie unter [Verwalten von Zertifikaten](http://msdn.microsoft.com/library/gg981929.aspx).

- Wenn Sie den Cloud-Dienst für eine Affinitätsgruppe bereitstellen möchten, erstellen Sie die Affinitätsgruppe. Sie können eine Affinitätsgruppe verwenden, um den Cloud-Dienst und andere Azure-Dienste für den gleichen Standort in einer Region bereitzustellen. Sie können die Affinitätsgruppe im Bereich **Netzwerke** des Verwaltungsportals auf der Seite **Affinitätsgruppen** erstellen. Weitere Informationen finden Sie unter [Erstellen einer Affinitätsgruppe im Verwaltungsportal](http://msdn.microsoft.com/library/jj156209.aspx).


<h2><a id="quick"></a>Vorgehensweise: Erstellen eines Cloud-Diensts mithilfe der Schnellerstellung</h2>

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Neu**>**Server**>**Cloud-Dienst**>**Schnellerstellung**.

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. Geben Sie unter **URL** einen Unterdomänennamen ein, der in der öffentlichen URL für den Zugriff auf Ihren Cloud-Dienst in Produktionsbereitstellungen verwendet werden soll. Das URL-Format für Produktionsbereitstellungen lautet: http://*myURL*.cloudapp.net.

3. Wählen Sie unter **Region oder Affinitätsgruppe** die geografische Region oder die Affinitätsgruppe aus, für die der Cloud-Dienst bereitgestellt werden soll. Wählen Sie eine Affinitätsgruppe aus, wenn Sie den Cloud-Dienst für den gleichen Standort wie andere Azure-Dienste innerhalb einer Region bereitstellen möchten.

4. Klicken Sie auf **Cloud-Dienst erstellen**.

	![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

	Sie können den Status des Prozesses im Meldungsbereich unten im Fenster überwachen.

	Der Bereich **Cloud-Dienste** wird geöffnet und der neue Cloud-Dienst wird angezeigt. Wenn der Status auf "Erstellt" geändert wird, wurde die Erstellung des Cloud-Diensts erfolgreich abgeschlossen.

	![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


<h2><a id="uploadcertificate"></a>Vorgehensweise: Hochladen eines Zertifikats für einen Cloud-Dienst</h2>

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, und klicken Sie dann auf **Zertifikate**.

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Klicken Sie auf **Zertifikat hochladen** oder auf **Hochladen**.

3. Verwenden Sie in **Datei** die Option **Durchsuchen**, um das zu verwendende Zertifikat (PFX-Datei) auszuwählen.

4. Geben Sie in **Kennwort** den privaten Schlüssel für das Zertifikat ein.

5. Klicken Sie auf **OK** (Häkchen).

	![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

	Sie können des Fortschritt des Hochladens im unten angezeigten Nachrichtenbereich überwachen. Wenn das Hochladen abgeschlossen ist, wird das Zertifikat zur Tabelle hinzugefügt. Klicken Sie im Nachrichtenbereich auf "OK", um die Nachricht zu schließen.

	![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

<h2><a id="deploy"></a>Vorgehensweise: Bereitstellen eines Cloud-Diensts</h2>

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, und klicken Sie dann auf **Dashboard**.

	Das Dashboard wird in der Produktionsumgebung geöffnet. Sie könnten an dieser Stelle "Staging" auswählen, um die Anwendung in der Stagingumgebung bereitzustellen. Weitere Informationen finden Sie unter [Verwalten von Bereitstellungen in Azure](http://msdn.microsoft.com/library/gg433027.aspx).

	 
2. Klicken Sie auf **Laden Sie eine neue Produktionsbereitstellung hoch** oder auf **Hochladen**.

3. Geben Sie unter **Bereitstellungsbezeichnung** einen Namen für die neue Bereitstellung ein, zum Beispiel "MeinClouddienstv4".

3. Verwenden Sie unter **Paket** die Option **Durchsuchen**, um die zu verwendende Dienstpaketdatei (CSPKG) auszuwählen.

4. Verwenden Sie unter **Konfiguration** die Option **Durchsuchen**, um die zu verwendende Dienstkonfigurationsdatei (CSCFG) auszuwählen.

5. Falls der Cloud-Dienst Rollen mit nur einer Instanz umfasst, aktivieren Sie das Kontrollkästchen mit der Bezeichnung wie **Auch bereitstellen, wenn eine oder mehrere Rollen eine einzelne Instanz enthalten**, um das Fortsetzen der Bereitstellung zu ermöglichen.

 Azure kann nur dann 99,95 % Zugriff auf den Cloud-Dienst während Wartungen und Dienstaktualisierungen garantieren, wenn jede Rolle über mindestens zwei Instanzen verfügt. Bei Bedarf können Sie zusätzliche Rolleninstanzen auf der Seite **Skalieren** nach der Bereitstellung des Cloud-Diensts hinzufügen. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/).

6. Klicken Sie auf **OK** (Häkchen), um die Cloud-Dienstbereitstellung zu starten.

	![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
 

	Sie können den Status der Bereitstellung im Nachrichtenbereich überwachen. Klicken Sie auf "OK", um die Nachricht zu schließen.

	![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

### So überprüfen Sie, ob Ihre Bereitstellung erfolgreich abgeschlossen wurde###

1. Klicken Sie auf **Dashboard**.

	Der Status sollte anzeigen, dass der Dienst **Ausgeführt** wird.

2. Klicken Sie unter **Schnellansicht** auf die Website-URL, um den Cloud-Dienst in einem Webbrowser zu öffnen.

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409

	![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


<!--HONumber=45--> 
