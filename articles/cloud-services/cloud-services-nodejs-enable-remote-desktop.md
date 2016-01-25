<properties 
	pageTitle="Aktivieren von Remotedesktop für Clouddienste (Node.js)" 
	description="Hier erfahren Sie, wie Sie Remotedesktopzugriff für virtuelle Computer aktivieren, die auf Ihrer Azure-Node.js-Anwendung gehostet werden." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>


# Aktivieren von Remotedesktop in Azure

Mit Remotedesktop können Sie auf den Desktop einer Rolleninstanz zugreifen, die in Azure ausgeführt wird. Sie können eine Remotedesktop-Verbindung zur Konfiguration eines virtuellen Computers oder zur Lösung von Problemen mit Ihrer Anwendung verwenden.

> [AZURE.NOTE]Dieser Beitrag gilt nur für Node.js-Anwendungen, die als Azure-Clouddienst gehostet werden.


## Voraussetzungen

- Installieren und konfigurieren Sie [Azure PowerShell](../install-configure-powershell.md).
- Stellen Sie eine Node.js-App in einem Azure-Clouddienst bereit. Weitere Informationen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Clouddienst](cloud-services-nodejs-develop-deploy-app.md).


## Schritt 1: Verwenden von Azure PowerShell zum Konfigurieren des Diensts für Remotedesktopzugriff

Um Remotedesktop verwenden zu können, müssen Sie die Azure-Dienstdefinition und Konfiguration mit einem Benutzernamen, Kennwort und Zertifikat aktualisieren.

Führen Sie die folgenden Schritte auf einem Computer aus, der die Quelldateien für die App enthält.

1. Führen Sie **Azure PowerShell** als Administrator aus. (Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**.)

2.  Navigieren Sie zu dem Verzeichnis, das die Dienstdefinitionsdateien (.csdef) und die Dienstkonfigurationsdateien (.cscfg) enthält.

3. Führen Sie das folgende PowerShell-Cmdlet aus:

		Enable-AzureServiceProjectRemoteDesktop

4. Geben Sie an der Eingabeaufforderung einen Benutzernamen und ein Kennwort ein.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Geben Sie das folgende PowerShell-Cmdlet ein, um die Änderungen zu veröffentlichen:

    	Publish-AzureServiceProject

	![publish-azureserviceproject][publish-project]

## Schritt 2: Verbinden mit der Rolleninstanz

Nachdem Sie die aktualisierte Dienstdefinition veröffentlicht haben, können Sie eine Verbindung mit der Rolleninstanz herstellen.

1.  Wählen Sie im [klassischen Azure-Portal] die Option **Cloud Services** und anschließend den Dienst aus.

	![Klassisches Azure-Portal][cloud-services]

2.  Klicken Sie auf **Instanzen** und anschließend auf **Produktion** oder **Staging**, um die Instanzen für Ihren Dienst anzuzeigen. Wählen Sie eine Instanz aus, und klicken Sie unten auf der Seite auf **Verbinden**.

    ![Die Seite "Instanzen"][3]

2.  Wenn Sie auf **Verbinden** klicken, werden Sie vom Webbrowser aufgefordert, eine .rdp-Datei zu speichern. Öffnen Sie diese Datei. (Wenn Sie beispielsweise Internet Explorer verwenden, klicken Sie auf **Öffnen**.)

    ![Aufforderung, die .rdp-Datei zu öffnen oder zu speichern][4]

3.  Wenn die Datei geöffnet wird, erscheint der folgende Sicherheitshinweis:

    ![Windows-Sicherheitshinweis][5]

4.  Klicken Sie auf **Verbinden**. Ein Sicherheitshinweis wird angezeigt, mit dem gemeldet wird, dass Sie Ihre Anmeldedaten eingeben müssen, um auf die Instanz zuzugreifen. Geben Sie das Kennwort ein, das Sie in [Schritt 1]\[Schritt 1: Konfigurieren des Diensts für Remotedesktopzugriff mit Azure PowerShell] erstellt haben, und klicken Sie dann auf **OK.**

    ![Hinweis auf Benutzernamen/Kennwort][6]

Wenn die Verbindung hergestellt ist, zeigt die Remotedesktopverbindung den Desktop der Instanz in Azure an.

![Remotedesktopsitzung][7]

## Schritt 3: Konfigurieren des Diensts zur Deaktivierung des Remotedesktopzugriffs 

Wenn Sie die Remotedesktopverbindungen zu den Rolleninstanzen in der Cloud nicht mehr benötigen, deaktivieren Sie den Remotedesktopzugriff über [Azure PowerShell].

1.  Führen Sie das folgende PowerShell-Cmdlet aus:

    	Disable-AzureServiceProjectRemoteDesktop

2.  Geben Sie das folgende PowerShell-Cmdlet ein, um die Änderungen zu veröffentlichen:

    	Publish-AzureServiceProject

## Zusätzliche Ressourcen

- [Fernzugriff auf Rolleninstanzen in Azure] 
- [Verwenden von Remotedesktop mit Azure-Rollen]
- [Node.js Developer Center](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[klassischen Azure-Portal]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Fernzugriff auf Rolleninstanzen in Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Verwenden von Remotedesktop mit Azure-Rollen]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 

<!---HONumber=AcomDC_0114_2016-->