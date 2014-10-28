<properties linkid="dev-node-remotedesktop" urlDisplayName="Enable Remote Desktop" pageTitle="Enable remote desktop for cloud services (Node.js)" metaKeywords="Azure Node.js remote access, Azure Node.js remote connection, Azure Node.js VM access, Azure Node.js virtual machine access" description="Learn how to enable remote-desktop access for the virtual machines hosting your Azure Node.js application. " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Enabling Remote Desktop in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Aktivieren von Remotedesktop in Azure

Mit Remotedesktop können Sie auf den Desktop einer Rolleninstanz
zugreifen, die in Azure ausgeführt wird. Über eine Remotedesktopverbindung
lassen sich virtuelle Computer konfigurieren oder
Probleme mit der Anwendung lösen.

<div class="dev-callout"><br></br>
    <b>Hinweis</b>
    <p>Die Schritte in diesem Beitrag gelten nur f&uuml;r Node-Anwendungen, die als Azure-Clouddienst gehostet werden.</p>
</div>

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Konfigurieren des Diensts für Remotedesktopzugriff mit Azure PowerShell][Schritt 1: Konfigurieren des Diensts für Remotedesktopzugriff mit Azure PowerShell]
-   [Schritt 2: Verbinden mit der Rolleninstanz][Schritt 2: Verbinden mit der Rolleninstanz]
-   [Schritt 3: Konfigurieren des Diensts zur Deaktivierung des Remotedesktopzugriffs mit Azure PowerShell][Schritt 3: Konfigurieren des Diensts zur Deaktivierung des Remotedesktopzugriffs mit Azure PowerShell]

## <a name="step1"> </a>Schritt 1: Konfigurieren des Diensts für Remotedesktopzugriff mit Azure PowerShell

Um Remotedesktop verwenden zu können, müssen Sie Ihre Dienstdefinition
und -konfiguration mit einem Benutzernamen, einem Kennwort
und einem Zertifikat für die Authentifizierung von Rolleninstanzen in der Cloud konfigurieren. [Azure PowerShell][Azure PowerShell] enthält das cmdlet **Enable-AzureServiceProjectRemoteDesktop**, das diese
Konfiguration für Sie durchführt.

Führen Sie am Computer, an dem die Dienstdefinition erstellt wurde,
die folgenden Schritte aus.

1.  Klicken Sie im Menü **Start** auf **Azure PowerShell**.

    ![Azure PowerShell-Startmenüeintrag][Azure PowerShell-Startmenüeintrag]

2.  Wechseln Sie zum Dienstverzeichnis, und geben Sie
    **Enable-AzureServiceProjectRemoteDesktop** und dann einen
    Benutzernamen sowie ein Kennwort zur Authentifizierung von Rolleninstanzen
    in der Cloud ein.

    ![enable-azureserviceprojectremotedesktop][enable-azureserviceprojectremotedesktop]

3.  Veröffentlichen Sie die Änderungen an der Dienstkonfiguration in der Cloud. Geben Sie an der Eingabeaufforderung für
    **Azure PowerShell** den Text
    **Publish-AzureServiceProject** ein.

    ![publish-azureserviceproject][publish-azureserviceproject]

Wenn diese Schritte ausgeführt sind, werden die Rolleninstanzen des Diensts
in der Cloud für Remotedesktopzugriff konfiguriert.

## <a name="step2"> </a>Schritt 2: Verbinden mit der Rolleninstanz

Wenn Ihre Bereitstellung in Azure ausgeführt wird, können Sie die
Verbindung mit der Rolleninstanz herstellen.

1.  Wählen Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] die Option **Clouddienste** und den oben in Schritt 1 bereitgestellten Dienst aus

    ![Azure-Verwaltungsportal][1]

2.  Klicken Sie auf **Instanzen** und anschließend auf **Produktion** oder **Staging**, um die Instanzen für Ihren Dienst anzuzeigen. Wählen Sie eine Instanz aus, und klicken Sie unten auf der Seite auf **Verbinden**.

    ![Die Seite "Instanzen"][Die Seite "Instanzen"]

3.  Wenn Sie auf **Verbinden** klicken, werden Sie vom Webbrowser
    aufgefordert, eine RDP-Datei zu speichern. Wenn Sie Internet Explorer verwenden, klicken Sie auf **Öffnen**.

    ![Aufforderung, die .rdp-Datei zu öffnen oder zu speichern][Aufforderung, die .rdp-Datei zu öffnen oder zu speichern]

4.  Wenn die Datei geöffnet wird, erscheint der folgende Sicherheitshinweis:

    ![Windows-Sicherheitshinweis][Windows-Sicherheitshinweis]

5.  Klicken Sie auf **Verbinden**. Sie werden in einem Sicherheitshinweis
    dazu aufgefordert, Ihre Anmeldeinformationen für den Zugriff auf die Instanz einzugeben. Geben Sie das Kennwort ein, das
    Sie in [Schritt 1][Schritt 1: Konfigurieren des Diensts für Remotedesktopzugriff mit Azure PowerShell] erstellt haben, und klicken Sie auf **OK**.

    ![Hinweis auf Benutzernamen/Kennwort][Hinweis auf Benutzernamen/Kennwort]

Wenn die Verbindung hergestellt ist, zeigt die Remotedesktopverbindung
den Desktop der Instanz in Azure an. Sie haben nun erfolgreich
Fernzugriff auf Ihre Instanz erlangt und können alle zur Verwaltung Ihrer
Anwendung nötigen Aufgaben ausführen.

![Remotedesktopsitzung][Remotedesktopsitzung]

## <a name="step3"> </a>Schritt 3: Konfigurieren des Diensts zur Deaktivierung des Remotedesktopzugriffs mit Azure PowerShell

Wenn Sie die Remotedesktopverbindung zu den Rolleninstanzen in
der Cloud nicht mehr benötigen, deaktivieren Sie den Remotedesktopzugriff über die [Azure PowerShell][Azure PowerShell].

1.  Klicken Sie im Menü **Start** auf **Azure PowerShell**.

2.  Wechseln Sie ins Dienstverzeichnis, und geben Sie
    **Disable-AzureServiceProjectRemoteDesktop** ein:

3.  Veröffentlichen Sie die Änderungen an der Dienstkonfiguration in der Cloud. Geben Sie an der Eingabeaufforderung für
    **Azure PowerShell** den Text
    **Publish-AzureServiceProject** ein:

## Zusätzliche Ressourcen

-   [Fernzugriff auf Rolleninstanzen in Azure][Fernzugriff auf Rolleninstanzen in Azure]
-   [Verwendung von Remotedesktop mit Azure-Rollen][Verwendung von Remotedesktop mit Azure-Rollen]

  [Schritt 1: Konfigurieren des Diensts für Remotedesktopzugriff mit Azure PowerShell]: #step1
  [Schritt 2: Verbinden mit der Rolleninstanz]: #step2
  [Schritt 3: Konfigurieren des Diensts zur Deaktivierung des Remotedesktopzugriffs mit Azure PowerShell]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  [Azure PowerShell-Startmenüeintrag]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
  [enable-azureserviceprojectremotedesktop]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
  [publish-azureserviceproject]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [1]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [Die Seite "Instanzen"]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [Aufforderung, die .rdp-Datei zu öffnen oder zu speichern]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [Windows-Sicherheitshinweis]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [Hinweis auf Benutzernamen/Kennwort]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [Remotedesktopsitzung]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  [Fernzugriff auf Rolleninstanzen in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/hh124107.aspx
  [Verwendung von Remotedesktop mit Azure-Rollen]: http://msdn.microsoft.com/de-de/library/windowsazure/gg443832.aspx
