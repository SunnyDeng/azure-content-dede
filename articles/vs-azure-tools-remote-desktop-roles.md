<properties 
   pageTitle="Verwendung von Remotedesktop mit Azure-Rollen"
   description="Verwendung von Remotedesktop mit Azure-Rollen"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="kempb" />

# Verwendung von Remotedesktop mit Azure-Rollen

Mit dem Azure SDK und Remotedesktopdiensten greifen Sie auf Azure-Rollen und virtuelle Computer zu, die von Azure gehostet werden. In Visual Studio können Sie Remotedesktopdienste in einem Azure-Projekt konfigurieren. Zum Aktivieren von Remotedesktopdiensten müssen Sie ein Arbeitsprojekt mit einer Rolle oder mehreren Rollen erstellen und es anschließend in Azure veröffentlichen.

>[AZURE.IMPORTANT]Greifen Sie auf Azure-Rollen nur zum Zweck der Problembehandlung oder Entwicklung zu. Der Zweck aller virtuellen Computer besteht in der Ausführung einer bestimmten Rolle in der Azure-Anwendung und nicht in der Ausführung anderer Clientanwendungen. Wenn Sie Azure zum Hosten eines virtuellen Computers für beliebige Zwecke verwenden möchten, finden Sie weitere Informationen unter "Zugreifen auf virtuelle Computer in Azure über den Server-Explorer".

## So aktivieren und verwenden Sie Remotedesktop für eine Azure-Rolle

1. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für das Projekt, und wählen Sie dann **Veröffentlichen** aus.

    Der **Assistent zum Veröffentlichen einer Azure-Anwendung** wird angezeigt.

    ![Befehl "Veröffentlichen" für ein Clouddienstprojekt](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. Aktivieren Sie am unteren Rand der Seite **Microsoft Azure-Veröffentlichungseinstellungen** des Assistenten das Kontrollkästchen **Remotedesktop für alle Rollen aktivieren**.

    Das Dialogfeld **Remotedesktopkonfiguration** wird angezeigt.

1. Klicken Sie am unteren Rand des Dialogfelds **Remotedesktopkonfiguration** auf die Schaltfläche **Weitere Optionen**.
 
    Es wird ein Dropdownlistenfeld angezeigt, über das Sie ein Zertifikat erstellen oder auswählen können, damit Sie die Anmeldeinformationen beim Herstellen einer Verbindung über Remotedesktop verschlüsseln können.

1. Wählen Sie in der Dropdownliste die Option **<Create>** oder ein vorhandenes Zertifikat in der Liste aus.

    Wenn Sie ein vorhandenes Zertifikat wählen, überspringen Sie die folgenden Schritte.

    >[AZURE.NOTE]Die für eine Remotedesktopverbindung erforderlichen Zertifikate unterscheiden sich von den für andere Azure-Vorgänge verwendeten Zertifikaten. Das Remotezugriffszertifikat muss über einen privaten Schlüssel verfügen.

    Das Dialogfeld **Zertifikat erstellen** wird angezeigt.

    1. Geben Sie einen Anzeigenamen für das neue Zertifikat ein, und klicken Sie dann auf **OK**. Das neue Zertifikat wird im Dropdownlistenfeld angezeigt.

    1. Geben Sie im Dialogfeld **Remotedesktopkonfiguration** einen Benutzernamen und ein Kennwort ein.
    
        Sie können kein vorhandenes Konto verwenden. Geben Sie nicht "Administrator" als Benutzernamen für das neue Konto an.

        >[AZURE.NOTE]Wenn das Kennwort nicht den Komplexitätsanforderungen entspricht, wird ein rotes Symbol neben dem Textfeld des Kennworts angezeigt. Das Kennwort muss Großbuchstaben, Kleinbuchstaben und Zahlen oder Symbole enthalten.

    1. Wählen Sie ein Ablaufdatum für das Konto aus. Nach diesem Datum werden Remotedesktopverbindungen blockiert.

    1. Nachdem Sie alle erforderlichen Informationen angegeben haben, klicken Sie auf **OK**.
    
        Den CSCFG- und CSDEF-Dateien werden mehrere Einstellungen zur Aktivierung von Remotezugriffsdiensten hinzugefügt.

1. Klicken Sie im **Assistenten für die Microsoft Azure-Veröffentlichungseinstellungen** auf **OK**, wenn Sie den Clouddienst veröffentlichen möchten.

    Wenn Sie den Dienst noch nicht veröffentlichen möchten, klicken Sie auf **Abbrechen**. Die Konfigurationseinstellungen werden gespeichert, und Sie können den Clouddienst später veröffentlichen.

## Herstellen einer Verbindung mit einer Azure-Rolle mithilfe von Remotedesktop

Nach der Veröffentlichung des Clouddiensts in Azure können Sie sich mit dem Server-Explorer bei den virtuellen Computern anmelden, die von Azure gehostet werden.

1. Erweitern Sie in Server-Explorer den Knoten **Azure** und dann den Knoten für einen Clouddienst und eine seiner Rollen, um eine Liste der Instanzen anzuzeigen.

1. Öffnen Sie das Kontextmenü für einen Knoten für die Instanz, und wählen Sie **Mithilfe von Remotedesktop verbinden** aus.

    ![Herstellen einer Verbindung über Remotedesktop](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. Geben Sie den Benutzernamen und das zuvor erstellte Kennwort ein. Sie sind nun bei Ihrer Remotesitzung angemeldet.

<!---HONumber=Oct15_HO3-->