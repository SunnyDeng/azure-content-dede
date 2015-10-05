<properties
	pageTitle="Erstellen und Bereitstellen eines Clouddiensts| Microsoft Azure"
	description="Hier erfahren Sie, wie Sie einen Clouddienst mithilfe der Schnellerfassung in Azure erstellen und bereitstellen."
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
	ms.date="09/22/2015"
	ms.author="adegeo"/>




# Erstellen und Bereitstellen eines Clouddiensts

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-create-deploy.md)
- [Azure Preview Portal](cloud-services-how-to-create-deploy-portal.md)

Das Azure-Portal bietet zwei Methoden zum Erstellen und Bereitstellen eines Clouddiensts: **Schnellerfassung** und **Benutzerdefiniert erstellen**.

In diesem Thema wird erläutert, wie Sie die Schnellerfassungsmethode zum Erstellen eines neuen Clouddiensts und dann **Hochladen** verwenden, um ein Clouddienstpaket in Azure hochzuladen und bereitzustellen. Wenn Sie diese Methode verwenden, werden im Azure-Portal praktische Links zum Erfüllen aller Anforderungen zur Verfügung gestellt. Wenn Sie Ihren Clouddienst bei der Erstellung auch bereitstellen möchten, können Sie beides mithilfe von **Benutzerdefinierte Erstellung** durchführen.

> [AZURE.NOTE]Wenn Sie Ihren Clouddienst aus Visual Studio Online (VSO) veröffentlichen möchten, verwenden Sie Schnellerstellung. Richten Sie die VSO-Veröffentlichung dann über **Schnellstart** oder das Dashboard ein. Weitere Informationen finden Sie unter [Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online][TFSTutorialForCloudService] oder in der Hilfe zur **Schnellstart**-Seite.

## Konzepte
Für die Bereitstellung einer Anwendung als Clouddienst in Azure sind drei Komponenten erforderlich:

- **Dienstdefinition:** Die Clouddienst-Definitionsdatei (.csdef) definiert das Dienstmodell einschließlich der Rollenanzahl.

- **Dienstkonfiguration:** Die Clouddienst-Konfigurationsdatei (.cscfg) enthält Konfigurationseinstellungen für den Clouddienst sowie einzelne Rollen, darunter die Anzahl der Rolleninstanzen.

- **Dienstpaket:** Das Dienstpaket (.cspkg) enthält den Anwendungscode, die Konfigurationen und die Dienstdefinitionsdatei.
  
Weitere Informationen zu diesen Komponenten sowie zum Erstellen eines Pakets finden Sie [hier](cloud-services-model-and-package.md).

## Vorbereiten Ihrer App
Bevor Sie einen Clouddienst bereitstellen können, müssen Sie das Clouddienstpaket (CSPKG) aus dem Anwendungscode und eine Clouddienstkonfigurationsdatei (CSCFG) erstellen. Das Azure-SDK stellt Tools zum Vorbereiten dieser erforderlichen Bereitstellungsdateien bereit. Sie können das SDK auf der Seite [Azure-Downloads](http://azure.microsoft.com/downloads/) in der Sprache herunterladen, in der Sie den Anwendungscode entwickeln möchten.

Drei Clouddienstfunktionen benötigen vor dem Export eines Dienstpakets spezielle Konfigurationen:

- Wenn Sie einen Clouddienst bereitstellen möchten, der Secure Sockets Layer (SSL) für die Datenverschlüsselung verwendet, konfigurieren Sie die Anwendung für SSL. Weitere Informationen finden Sie unter [Konfigurieren eines SSL-Zertifikats auf einem HTTPS-Endpunkt](http://msdn.microsoft.com/library/azure/ff795779.aspx).

- Wenn Sie Remotedesktopverbindungen zu Rolleninstanzen konfigurieren möchten, konfigurieren Sie die Rollen für Remotedesktop. Weitere Informationen zum Vorbereiten der Dienstdefinitionsdateien für den Remotezugriff finden Sie im [Einrichten einer Remotedesktopverbindung für eine Rolle in Azure](http://msdn.microsoft.com/library/hh124107.aspx).

- Wenn Sie die ausführliche Überwachung für den Clouddienst konfigurieren möchten, aktivieren Sie für den Clouddienst die Azure-Diagnose. *Minimale Überwachung* (die Standardüberwachungsstufe) verwendet Leistungsindikatoren, die aus den Hostbetriebssystemen für Rolleninstanzen (virtuelle Computer) erfasst wurden. Bei der "ausführlichen Überwachung" werden zusätzliche Kennzahlen basierend auf Leistungsdaten innerhalb der Rolleninstanzen erfasst, um eine genauere Analyse von Problemen zu ermöglichen, die während der Anwendungsverarbeitung auftreten. Informationen zum Aktivieren der Azure-Diagnose finden Sie unter [Aktivieren der Diagnose in Azure](cloud-services-dotnet-diagnostics.md).

- Sie müssen das Dienstpaket erstellen, um einen Clouddienst mit Bereitstellungen von Webrollen oder Workerrollen zu erstellen. Informationen zu den Dateien dieses Pakets erhalten Sie unter [Einrichten eines Clouddiensts für Azure](http://msdn.microsoft.com/library/hh124108.aspx). Informationen zum Erstellen des Pakets finden Sie unter [Packen einer Azure-Anwendung](http://msdn.microsoft.com/library/hh403979.aspx). Wenn Sie Visual Studio zur Entwicklung Ihrer Anwendung verwenden, finden Sie weitere Informationen unter [Veröffentlichen eines Clouddiensts mit Azure Tools](http://msdn.microsoft.com/library/ff683672.aspx).

## Voraussetzungen

- Falls Sie das Azure-SDK noch nicht installiert haben, klicken Sie auf **Azure-SDK installieren**, um die [Azure-Downloadseite](http://azure.microsoft.com/downloads/) zu öffnen. Laden Sie dann das SDK für die Sprache herunter, in der Sie den Code entwickeln möchten. (Dazu haben Sie auch später noch die Möglichkeit.)

- Falls Rolleninstanzen ein Zertifikat erfordern, erstellen Sie die Zertifikate. Clouddienste erfordern eine PFX-Datei mit einem privaten Schlüssel. Sie können die Zertifikate zu Azure hochladen, wenn Sie den Clouddienst erstellen und bereitstellen. Weitere Informationen zu Zertifikaten finden Sie unter [Verwalten von Zertifikaten](http://msdn.microsoft.com/library/gg981929.aspx).

- Wenn Sie den Clouddienst für eine Affinitätsgruppe bereitstellen möchten, erstellen Sie die Affinitätsgruppe. Sie können eine Affinitätsgruppe verwenden, um den Clouddienst und andere Azure-Dienste für den gleichen Standort in einer Region bereitzustellen. Sie können die Affinitätsgruppe im Bereich **Netzwerke** des Verwaltungsportals auf der Seite **Affinitätsgruppen** erstellen. Weitere Informationen finden Sie unter [Erstellen einer Affinitätsgruppe im Verwaltungsportal](http://msdn.microsoft.com/library/jj156209.aspx).


## Erstellen eines Clouddiensts mithilfe der Schnellerfassung

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Neu**>**Server**>**Cloud-Dienst**>**Schnellerstellung**.

	![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. Geben Sie unter **URL** einen Unterdomänennamen ein, der in der öffentlichen URL für den Zugriff auf Ihren Clouddienst in Produktionsbereitstellungen verwendet werden soll. Das URL-Format für Produktionsbereitstellungen lautet: http://*myURL*.cloudapp.net.

3. Wählen Sie unter **Region oder Affinitätsgruppe** die geografische Region oder die Affinitätsgruppe aus, für die der Clouddienst bereitgestellt werden soll. Wählen Sie eine Affinitätsgruppe aus, wenn Sie den Clouddienst für den gleichen Standort wie andere Azure-Dienste innerhalb einer Region bereitstellen möchten.

4. Klicken Sie auf **Clouddienst erstellen**.

	![CloudServices\_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

	Sie können den Status des Prozesses im Meldungsbereich unten im Fenster überwachen.

	Der Bereich **Clouddienste** wird geöffnet und der neue Clouddienst wird angezeigt. Wenn der Status auf "Erstellt" geändert wird, wurde die Erstellung des Clouddiensts erfolgreich abgeschlossen.

	![CloudServices\_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


## Hochladen eines Zertifikats für einen Clouddienst

1. Klicken Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com/) auf **Clouddienste** und dann auf den Namen des Clouddiensts, und klicken Sie dann auf **Zertifikate**.

	![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Klicken Sie auf **Zertifikat hochladen** oder auf **Hochladen**.

3. Verwenden Sie in **Datei** die Option **Durchsuchen**, um das zu verwendende Zertifikat (PFX-Datei) auszuwählen.

4. Geben Sie in **Kennwort** den privaten Schlüssel für das Zertifikat ein.

5. Klicken Sie auf **OK** (Häkchen).

	![CloudServices\_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

	Sie können des Fortschritt des Hochladens im unten angezeigten Nachrichtenbereich überwachen. Wenn das Hochladen abgeschlossen ist, wird das Zertifikat zur Tabelle hinzugefügt. Klicken Sie im Nachrichtenbereich auf "OK", um die Nachricht zu schließen.

	![CloudServices\_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## Bereitstellen eines Clouddiensts

1. Klicken Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com/) auf **Clouddienste** und dann auf den Namen des Clouddiensts, und klicken Sie dann auf **Dashboard**.

	Das Dashboard wird in der Produktionsumgebung geöffnet. Sie könnten an dieser Stelle "Staging" auswählen, um die Anwendung in der Stagingumgebung bereitzustellen. Weitere Informationen finden Sie unter [Verwalten von Bereitstellungen in Azure](http://msdn.microsoft.com/library/gg433027.aspx).


2. Klicken Sie auf **Laden Sie eine neue Produktionsbereitstellung hoch** oder auf **Hochladen**.

3. Geben Sie unter **Bereitstellungsbezeichnung** einen Namen für die neue Bereitstellung ein, zum Beispiel "MeinClouddienstv4".

3. Verwenden Sie unter **Paket** die Optionen **Durchsuchen**, um die zu verwendende Dienstpaketdatei (CSPKG) auszuwählen.

4. Verwenden Sie unter **Konfiguration** die Option **Durchsuchen**, um die zu verwendende Dienstkonfigurationsdatei (CSCFG) auszuwählen.

5. Falls der Clouddienst Rollen mit nur einer Instanz umfasst, aktivieren Sie das Kontrollkästchen mit der Bezeichnung wie **Auch bereitstellen, wenn eine oder mehrere Rollen eine einzelne Instanz enthalten**, um das Fortsetzen der Bereitstellung zu ermöglichen.

    Azure kann nur dann 99,95 % Zugriff auf den Clouddienst während Wartungen und Dienstaktualisierungen garantieren, wenn jede Rolle über mindestens zwei Instanzen verfügt. Bei Bedarf können Sie zusätzliche Rolleninstanzen auf der Seite **Skalieren** nach der Bereitstellung des Clouddiensts hinzufügen. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/).

6. Klicken Sie auf **OK** (Häkchen), um die Clouddienstbereitstellung zu starten.

	![CloudServices\_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

	Sie können den Status der Bereitstellung im Nachrichtenbereich überwachen. Klicken Sie auf "OK", um die Nachricht zu schließen.

	![CloudServices\_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## Überprüfen, ob Ihre Bereitstellung erfolgreich abgeschlossen wurde

1. Klicken Sie auf **Dashboard**.

	Der Status sollte anzeigen, dass der Dienst **Ausgeführt** wird.

2. Klicken Sie unter **Schnellansicht** auf die Website-URL, um den Clouddienst in einem Webbrowser zu öffnen.

    ![CloudServices\_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
 
## Nächste Schritte

* [Allgemeine Konfiguration Ihres Clouddiensts](cloud-services-how-to-configure.md)
* [Konfigurieren eines benutzerdefinierten Domänennamens](cloud-services-custom-domain-name.md)
* [Verwalten Ihres Clouddiensts](cloud-services-how-to-manage.md)
* Konfigurieren von [SSL-Zertifikaten](cloud-services-configure-ssl-certificate.md)

<!---HONumber=Sept15_HO4-->