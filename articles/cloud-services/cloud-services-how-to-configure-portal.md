<properties 
	pageTitle="Konfigurieren eines Clouddiensts – Azure" 
	description="Hier erfahren Sie, wie Sie Clouddienste in Azure konfigurieren. Hier erfahren Sie, wie Sie die Konfiguration für Clouddienste aktualisieren und Remotezugriff auf Rolleninstanzen konfigurieren." 
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
	ms.date="06/29/2015"
	ms.author="adegeo"/>




# Konfigurieren von Clouddiensten

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-configure.md)
- [Azure Preview Portal](cloud-services-how-to-configure-portal.md)

Im Azure-Verwaltungsportal können Sie die am häufigsten für einen Clouddienst verwendeten Einstellungen konfigurieren. Wenn Sie die Konfigurationsdateien jedoch direkt aktualisieren möchten, laden Sie eine zu aktualisierende Konfigurationsdatei herunter, laden Sie anschließend die aktualisierte Datei hoch, und aktualisieren Sie den Clouddienst mit den Konfigurationsänderungen. In beiden Fällen wird die aktualisierte Konfiguration an alle Rolleninstanzen übermittelt.

Sie können auch eine Remotedesktopverbindung zu einer oder allen Rollen aktivieren, die in Ihrem Clouddienst ausgeführt werden. Mit Remotedesktop können Sie auf den Desktop Ihrer Anwendung zugreifen, während diese ausgeführt wird, und Probleme diagnostizieren und beheben. Sie können eine Remotedesktopverbindung zu Ihrer Rolle auch aktivieren, wenn Sie während der Entwicklung der Anwendung die Servicedefinitionsdatei \(.csdef\) für Remotedesktop nicht konfiguriert haben. Die Anwendung muss nicht erneut bereitgestellt werden, um eine Remotedesktopverbindung zu aktivieren.

Während der Konfigurationsupdates kann Azure nur dann eine Dienstverfügbarkeit von 99,95 Prozent sicherstellen, wenn Sie mindestens zwei Rolleninstanzen für jede Rolle haben. In diesem Fall kann ein virtueller Computer Clientanforderungen verarbeiten, während der andere aktualisiert wird. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/).

## Ändern eines Clouddiensts

1. Navigieren Sie im [Azure-Vorschauportal](http://portal.azure.com/) zu Ihrem Clouddienst.

2. Klicken Sie auf das Symbol **Einstellungen** oder den Link **Essentials/Alle Einstellungen**, um das Blatt **Einstellungen** zu öffnen.

    ![Seite „Einstellungen“](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    Hier können Sie **Eigenschaften** anzeigen, die **Konfiguration** ändern und die **Zertifikate** sowie die **Benutzer** verwalten, die auf diesen Clouddienst zugreifen können.

2. Klicken Sie unter dem Abschnitt **Überwachung** auf eine beliebige Kachel, um Warnungen zu konfigurieren.

    ![Clouddienstüberwachung](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)
    
3. Klicken Sie unter dem Abschnitt **Rollen und Instanz** auf eine beliebige Clouddienstrolle, um die Instanz zu verwalten.

    ![Clouddienstinstanz](./media/cloud-services-how-to-configure-portal/cs-instance.png)
    
    Sie können hier eine Remoteverbindung mit dem Clouddienst herstellen, den Clouddienst neu starten oder ein Reimaging durchführen.
    
    ![Schaltflächen für die Clouddienstinstanz](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

>[AZURE.NOTE]Das für den Clouddienst verwendete Betriebssystem kann nicht über das **Azure-Vorschauportal** geändert werden. Diese Einstellung können Sie nur über das andere [Portal](http://manage.windowsazure.com/) ändern. Weitere Informationen finden Sie [hier](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## Aktualisieren einer Clouddienst-Konfigurationsdatei

1. Laden Sie zunächst die vorhandene Clouddienst-Konfigurationsdatei \(.cscfg\) herunter.

    1. Navigieren Sie im [Azure-Vorschauportal](http://portal.azure.com/) zu Ihrem Clouddienst.

    2. Klicken Sie auf das Symbol **Einstellungen** oder den Link **Essentials/Alle Einstellungen**, um das Blatt **Einstellungen** zu öffnen.

        ![Seite „Einstellungen“](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    3. Klicken Sie auf das Element **Konfiguration**.

        ![Blatt „Konfiguration“](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
    
    4. Klicken Sie auf die Schaltfläche **Herunterladen**.

        ![Herunterladen](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

2. Laden Sie nach dem Update der Dienstkonfigurationsdatei die Konfigurationsupdates hoch, und wenden Sie sie an:

    1. Führen Sie die ersten drei Schritte oben aus, um das Blatt **Konfiguration** für den Clouddienst zu öffnen.
    
    2. Klicken Sie auf die Schaltfläche **Hochladen**.

        ![Hochladen](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
    3. Wählen Sie die CSCFG-Datei aus, und klicken Sie auf **OK**.

## Konfigurieren des Remotezugriffs auf Rolleninstanzen

Der Remotezugriff kann nicht über das **Azure-Vorschauportal** konfiguriert werden. Sie können Sie diese Einstellung nur über das andere [Portal](http://manage.windowsazure.com/) ändern. Weitere Informationen finden Sie [hier](cloud-services-how-to-configure.md#configure-remote-access-to-role-instances).
			
 

<!---HONumber=July15_HO5-->