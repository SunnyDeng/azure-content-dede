<properties
	pageTitle="Synchronisieren von Inhalt aus einem Cloudordner in Azure App Service"
	description="Erfahren Sie, wie Sie Ihre App in Azure App Service durch Inhaltssynchronisierung aus einem Cloudordner bereitstellen."
	services="app-service"
	documentationCenter=""
	authors="dariac"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="dariac"/>
    
# Synchronisieren von Inhalt aus einem Cloudordner in Azure App Service

Eine der Möglichkeiten für die Bereitstellung auf[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), ist die Synchronisierung Ihrer Inhalte aus beliebten Cloudspeicherdiensten wie Dropbox und OneDrive.

## <a name="overview"></a>Übersicht über die Bereitstellung von Inhaltssynchronisierung

Die On-Demand Bereitstellung von Inhaltssynchronisierung wird vom [Kudu-Bereitstellungsmodul](https://github.com/projectkudu/kudu/wiki) unterstützt, das in App Service integriert ist. Im [Azure-Portal](https://portal.azure.com) können Sie in Ihrem Cloudspeicher einen Ordner bestimmen, mit Ihrem App-Code und den App-Inhalten in diesem Ordner arbeiten und per Klick auf eine Schaltfläche die Synchronisierung mit App Service durchführen. Inhaltssynchronisierung nutzt den Kudu-Prozess für „Build & Deployment“.
    
## <a name="contentsync"></a>Aktivieren der Bereitstellung von Inhaltssynchronisierung
Um die Inhaltssynchronisierung aus dem [Azure-Portal](https://portal.azure.com) zu aktivieren, gehen Sie folgendermaßen vor:

1. Klicken Sie im Azure-Portal auf dem Blatt Ihrer App auf **Einstellungen** > **Bereitstellungsquelle**. Klicken Sie auf **Quelle auswählen**, und wählen Sie dann **OneDrive** oder **Dropbox** als Quelle für die Bereitstellung. 

    ![Inhaltssynchronisierung](./media/app-service-deploy-content-sync/deployment_source.png)

2. Schließen Sie den Authorisierungsworkflow zum Aktivieren von App Service ab, um auf einem bestimmten vordefinierten, gekennzeichneten Pfad für OneDrive oder Dropbox zuzugreifen, in dem alle Ihre App Service-Inhalte gespeichert werden. Nach der Autorisierung gibt Ihnen die App Service-Plattform die Möglichkeit, einen Inhaltsordner unterhalb des festgelegten Inhaltspfads zu erstellen oder einen vorhandenen Inhaltsordner unter diesem festgelegten Inhaltspfad auszuwählen. Die festgelegten Inhaltspfade unter Ihren Cloudspeicherkonten, die für die Synchronisierung von App Service verwendet werden, sind die Folgenden:
    * **OneDrive**: `Apps\Azure Web Apps` 
    * **Dropbox**: `Dropbox\Apps\Azure`

3. Nach der Erstsynchronisierung des Inhalts kann die Inhaltssynchronisierung vom Azure-Portal bei Bedarf initiiert werden. Der Bereitstellungsverlauf ist auf dem Blatt **Bereitstellungen** verfügbar.

    ![Bereitstellungsverlauf](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Weitere Informationen zur Dropbox-Bereitstellung finden Sie unter [Deploy from Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx) (Bereitstellen von Dropbox).

<!---HONumber=AcomDC_0323_2016-->