<properties 
	pageTitle="Aktivieren von Remotedesktop für Cloud-Dienste (Node.js)" 
	description="Hier erfahren Sie, wie Sie Remotedesktopzugriff für virtuelle Computer aktivieren, die auf Ihrer Azure-Node.js-Anwendung gehostet werden." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Aktivieren von Remotedesktop in Azure

Mit Remotedesktop können Sie auf den Desktop einer Rolleninstanz zugreifen, die in Azure ausgeführt wird. Sie können eine Remotedesktop-Verbindung zur Konfiguration eines virtuellen Computers oder zur Lösung von Problemen mit Ihrer Anwendung verwenden.

> [AZURE.NOTE] Die Schritte in diesem Beitrag gelten nur für Node-Anwendungen, die als Azure-Cloud-Dienst gehostet werden.

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Konfigurieren des Diensts für Remotedesktopzugriff mit Azure PowerShell]
-   [Schritt 2: Verbinden mit der Rolleninstanz]
-   [Schritt 3: Konfigurieren des Dienstes zur Deaktivierung des Remotedesktop-Zugriffs mit Azure PowerShell]

## <a name="step1"> </a>Schritt 1: Konfigurieren des Diensts für Remotedesktopzugriff mit Azure PowerShell

Um Remotedesktop verwenden zu können, müssen Sie Ihre Dienstdefinition und -konfiguration mit einem Benutzernamen, einem Kennwort und einem Zertifikat für die Authentifizierung von Rolleninstanzen in der Cloud konfigurieren. [Azure PowerShell] enthält das Cmdlet **Enable-AzureServiceProjectRemoteDesktop**, das diese Konfiguration für Sie durchführt.

Führen Sie am Computer, an dem die Dienstdefinition erstellt wurde, die folgenden Schritte aus.

1.  Klicken Sie im Menü **Start** auf **Azure PowerShell**.

	![Azure PowerShell start menu entry][powershell-menu]

2.  Wechseln Sie zum Dienstverzeichnis, und geben Sie **Enable-AzureServiceProjectRemoteDesktop** und dann einen Benutzernamen sowie ein Kennwort zur Authentifizierung von Rolleninstanzen in der Cloud ein.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Veröffentlichen Sie die Änderungen an der Dienstkonfiguration in der Cloud. Geben Sie an der Eingabeaufforderung für **Azure PowerShell** den Text **Publish-AzureServiceProject** ein.

	![publish-azureserviceproject][publish-project]

Wenn diese Schritte ausgeführt sind, werden die Rolleninstanzen des Dienstes in der Cloud für Remotedesktop-Zugriff konfiguriert.

## <a name="step2"> </a>Schritt 2: Verbinden mit der Rolleninstanz

Wenn Ihre Bereitstellung in Azure ausgeführt wird, können Sie die Verbindung zur Rolleninstanz herstellen.

1.  Wählen Sie im [Azure-Verwaltungsportal] die Option **Cloud-Dienste** und den oben in Schritt 1 bereitgestellten Dienst aus.

	![azure management portal][cloud-services]

2.  Klicken Sie auf **Instanzen** und anschließend auf **Produktion** oder **Staging**, um die Instanzen für Ihren Dienst anzuzeigen. Wählen Sie eine Instanz aus, und klicken Sie unten auf der Seite auf **Verbinden**.

    ![The instances page][3]

2.  Wenn Sie auf **Verbinden** klicken, werden Sie vom Webbrowser aufgefordert, eine RDP-Datei zu speichern. Wenn Sie Internet Explorer verwenden, klicken Sie auf **Öffnen**.

    ![prompt to open or save the .rdp file][4]

3.  Wenn die Datei geöffnet wird, erscheint der folgende Sicherheitshinweis:

    ![Windows security prompt][5]

4.  Klicken Sie auf **Verbinden**. Sie werden in einem Sicherheitshinweis dazu aufgefordert, Ihre Anmeldeinformationen für den Zugriff auf die Instanz einzugeben. Geben Sie das Kennwort ein, das Sie in [Schritt 1][Step 1: erstellt haben Konfigurieren Sie den Dienst für Remotedesktopzugriff mit Azure PowerShell], und klicken Sie dann auf **OK**.

    ![username/password prompt][6]

Wenn die Verbindung hergestellt ist, zeigt die Remotedesktopverbindung den Desktop der Instanz in Azure an. Sie haben nun erfolgreich Fernzugriff auf Ihre Instanz erlangt und können alle zur Verwaltung Ihrer Anwendung nötigen Aufgaben ausführen.

![Remote desktop session][7]

## <a name="step3"> </a>Schritt 3: Konfigurieren des Diensts zur Deaktivierung des Remotedesktopzugriffs mit Azure PowerShell

Wenn Sie die Remotedesktopverbindung zu den Rolleninstanzen in der Cloud nicht mehr benötigen, deaktivieren Sie den Remotedesktopzugriff über die [Azure PowerShell].

1.  Klicken Sie im Menü **Start** auf **Azure PowerShell**.

2.  Wechseln Sie ins Dienstverzeichnis, und geben Sie **Disable-AzureServiceProjectRemoteDesktop** ein:

3.  Veröffentlichen Sie die Änderungen an der Dienstkonfiguration in der Cloud. Geben Sie an der Eingabeaufforderung für **Azure PowerShell** den Text **Publish-AzureServiceProject** ein.

## Zusätzliche Ressourcen

- [Fernzugriff auf Rolleninstanzen in Azure] 
- [Verwendung von Remotedesktop mit Azure-Rollen]

  [Schritt 1: Konfigurieren des Diensts für Remotedesktopzugriff mit Azure PowerShell]: #step1
  [Step 1: erstellt haben Konfigurieren Sie den Dienst für Remotedesktopzugriff mit Azure PowerShell]: #step1
  [Schritt 2: Verbinden mit der Rolleninstanz]: #step2
  [Schritt 3: Konfigurieren des Dienstes zur Deaktivierung des Remotedesktop-Zugriffs mit Azure PowerShell]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure-Verwaltungsportal]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Fernzugriff auf Rolleninstanzen in Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Verwendung von Remotedesktop mit Azure-Rollen]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx

<!--HONumber=45--> 
