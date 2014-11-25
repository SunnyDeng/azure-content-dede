<properties title="How to create a custom template image for RemoteApp" pageTitle="How to create a custom template nimage for RemoteApp" description="Learn how to create a custom template image for RemoteApp. You can use this template with either a hybrid or cloud deployment." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="elizapo" ms.manager="kathyw" />

# Erstellen eines benutzerdefinierten Vorlagenimage für RemoteApp

Azure RemoteApp verwendet ein Windows Server 2012 R2-Vorlagenimage, um alle Programme zu hosten, die Sie an die Benutzer freigeben möchten. Um ein benutzerdefiniertes RemoteApp-Vorlagenimage zu erstellen, beginnen Sie mit einem bestehenden Abbild oder erstellen Sie ein neues. Das Abbild, das für die Verwendung mit Azure RemoteApp hochgeladen werden soll, muss folgende Anforderungen erfüllen:

-   Die Größe des Abbilds sollte ein Vielfaches der Einheit MB (1.024 KB) betragen. Wenn Sie versuchen, ein Abbild hochzuladen, das kein exaktes Vielfaches ist, schlägt der Upload fehl.
-   Das Abbild darf nicht größer als 127 GB sein.
-   Es muss sich auf einer VHD-Datei befinden (VHDX-Dateien werden derzeit nicht unterstützt).
-   Die VHD kann eine feste Größe haben oder dynamisch erweiterbar sein. Wir empfehlen eine dynamisch erweiterbare VHD, da das Hochladen dieser in Azure weniger Zeit in Anspruch nimmt.
-   Der Datenträger muss mit der Master Boot Record (MBR)-Partitionierung initialisiert werden. Die GPT-Partitionierung (GUID-Partitionstabelle) wird nicht unterstützt.
-   Die VHD muss eine einzige Installation von Windows Server 2012 R2 enthalten. Sie kann mehrere Volumes enthalten, jedoch nur eines mit einer Windows-Installation.
-   Die RDSH-Rolle (Remote Desktop Session Host) und das Desktopdarstellung-Feature müssen installiert sein.
-   Encrypting File System (EFS) muss deaktiviert sein.
-   Das Abbild muss mit SYSPREP unter Verwendung der Parameter **/oobe /generalize /shutdown** vorbereitet werden. Verwenden Sie nicht den Parameter **/mode:vm**.

**Voraussetzungen**

Bevor Sie mit der Erstellung des Dienstes beginnen, führen Sie Folgendes aus:

-   Registrieren Sie sich für die Vorschau von RemoteApp. Sie können sich unter [][]<http://azure.microsoft.com/de-de/services/remoteapp/></a> registrieren.
-   Erstellen Sie ein Benutzerkonto in Active Directory, das als Konto für den RemoteApp-Dienst dient. Beschränken Sie die Berechtigungen für dieses Konto, sodass es nur Computer in die Domäne einbinden kann.
-   Sammeln Sie folgende Informationen über das lokale Netzwerk: IP-Adresse und Informationen zum VPN-Gerät.
-   Installieren Sie das [Azure PowerShell][Azure PowerShell]-Modul.
-   Sammeln Sie Informationen über die Benutzer und Gruppen, denen Sie Zugriff gewähren möchten. Dies können Informationen zu Microsoft-Konten oder Active Directory-Organisationskonten sein.

## **Erstellen eines Vorlagenimage.**

Zum Erstellen eines neuen Vorlagenimage:

1.  Suchen Sie nach einem Windows Server 2012 R2 DVD- oder ISO-Image.
2.  Erstellen Sie eine VHD-Datei.
3.  Installieren Sie Windows Server 2012 R2
4.  Installieren Sie die RDSH-Rolle (Remote Desktop Session Host) und das Desktopdarstellung-Feature.
5.  Installieren Sie weitere, von den Anwendungen benötigte Features.
6.  Installieren und konfigurieren Sie die Anwendungen.
7.  Führen Sie weitere, von den Anwendungen benötigte Windows-Konfigurationen aus.
8.  Deaktivieren Sie Encrypting File System (EFS).
9.  Bereiten Sie das Abbild mit SYSPREP vor.

Dies sind die einzelnen Schritte zum Erstellen eines neuen Abbilds:

1.  Suchen Sie nach einem Windows Server 2012 R2 DVD- oder ISO-Image.
2.  Erstellen Sie mithilfe der Datenträgerverwaltung eine VHD-Datei.

    1.  Starten Sie die Datenträgerverwaltung (diskmgmt.msc).
    2.  Erstellen Sie eine dynamisch expandierende VHD mit 40 GB oder mehr. (Schätzen Sie den Speicherplatz, den Sie für Windows sowie für Ihre Anwendungen und Anpassungen benötigen. Windows Server mit der RDSH-Rolle und dem Desktopdarstellung-Feature benötigen etwa 10 GB.)

        1.  Klicken Sie auf **Aktion \> VHD erstellen**.
        2.  Geben Sie Speicherort, Größe und VHD-Format an. Wählen Sie **Dynamisch expandierend**, und klicken Sie anschließend auf **OK**.

            Die Erstellung benötigt einige Sekunden. Wenn die Erstellung abgeschlossen ist, sollte in der Datenträgerverwaltungskonsole ein neuer Datenträger ohne Laufwerksbuchstaben und mit dem Status "Nicht initialisiert" angezeigt werden.

        -   Klicken Sie mit der rechten Maustaste auf den Datenträger (nicht auf den nicht zugewiesenen Speicherplatz) und klicken Sie dann auf **Datenträger initialisieren**. Wählen Sie als Partitionsstil **MBR** (Master Boot Record) und klicken Sie auf **OK**.
        -   Erstellen eines neuen Volume: Klicken Sie mit der rechten Maustaste auf den auf den nicht zugewiesenen Speicherplatz und dann auf **Neues einfaches Volume**. Sie können die Standardeinstellungen im Assistenten verwenden; weisen Sie jedoch einen Laufwerksbuchstaben zu, um Probleme beim Hochladen des Vorlagenimage zu vermeiden.
        -   Klicken Sie mit der rechten Maustaste auf den Datenträger und dann auf **VHD trennen**.

3.  Installieren von Windows Server 2012 R2:

    1.  Erstellen Sie einen neuen virtuellen Computer. Verwenden Sie den Assistenten für neue virtuelle Computer im Hyper-V Manager oder Hyper-V für Clients.

        1.  Wählen Sie auf der Seite "Generation angeben" die Option **Generation 1** aus.
        2.  Wählen Sie auf der Seite "Virtuelle Festplatte anschließen" **Vorhandene virtuelle Festplatte verwenden** und gehen Sie zu der im vorhergehenden Schritt erstellten VHD.
        3.  Wählen Sie auf der Seite "Installationsoptionen" **Betriebssystem von Start-CD/DVD\_ROM installieren** und wählen Sie dann den Ort der Windows Server 2012 R2-Installationsdatenträger.
        4.  Wählen Sie im Assistenten die weiteren Optionen, die für die Installation von Windows und der Anwendungen erforderlich sind. Beenden Sie den Assistenten.

    2.  Bearbeiten Sie nach Abschluss des Assistenten die Einstellungen des virtuellen Computers, und nehmen Sie alle für die Installation von Windows und Ihrer Programme erforderlichen Änderungen vor, z. B. bezüglich der Zahl der virtuellen Prozessoren. Klicken Sie dann auf **OK**.
    3.  Stellen Sie eine Verbindung mit dem virtuellen Computer her, und installieren Sie Windows Server 2012 R2.

4.  Installieren Sie die RDSH-Rolle (Remote Desktop Session Host) und das Desktopdarstellung-Feature:

    1.  Starten Sie den Server-Manager.
    2.  Klicken Sie auf dem Willkommensbildschirm oder im Menü **Verwalten** auf **Rollen und Features hinzufügen**.
    3.  Klicken Sie auf der Seite "Vorbemerkungen" auf **Weiter**.
    4.  Wählen Sie **Rollenbasierte oder featurebasierte Installation** und klicken Sie auf **Weiter**.
    5.  Wählen Sie den lokalen Computer aus der Liste und klicken Sie auf **Weiter**.
    6.  Wählen Sie **Remotedesktopdienste** und klicken Sie dann auf **Weiter**.
    7.  Erweitern Sie **Benutzeroberflächen und Infrastruktur** und wählen Sie **Desktopdarstellung**.
    8.  Klicken Sie auf **Features hinzufügen** und anschließend auf **Weiter**.
    9.  Klicken Sie auf der Seite "Remotedesktopdienste" auf **Weiter**.
    10. Klicken Sie auf **Remotedesktop-Sitzungshost**.
    11. Klicken Sie auf **Features hinzufügen** und anschließend auf **Weiter**.
    12. Wählen Sie auf der Seite "Installationsauswahl bestätigen" falls erforderlich **Zielserver automatisch neu starten**, und klicken Sie bei Anzeige der Neustartwarnung auf **Ja**.
    13. Klicken Sie auf **Installieren**. Der Computer wird neu gestartet.

5.  Installieren Sie die für Ihre Anwendungen benötigten zusätzlichen Features, wie z. B. .NET Framework 3.5. Führen Sie dazu den Assistenten zum Hinzufügen von Rollen und Features aus.
6.  Installieren und konfigurieren Sie die Programme und Anwendungen, die Sie über RemoteApp veröffentlichen möchten.

    **Wichtig:** Microsoft empfiehlt, die RDSH-Rolle vor der Installation der Anwendungen zu installieren, um sicherzugehen, dass Probleme mit der Anwendungskompatibilität vor dem Hochladen des Abbilds in RemoteApp entdeckt werden.

7.  Führen Sie weitere, von den Anwendungen benötigte Windows-Konfigurationen aus.
8.  Deaktivieren Sie Encrypting File System (EFS). Führen Sie in einem Befehlsfenster mit erhöhten Rechten den folgenden Befehl aus:

        Fsutil behavior set disableencryption 1

    Alternativ können Sie in der Registrierung den folgenden DWORD-Wert festlegen oder hinzufügen:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1

9.  Wenn Sie das Abbild in einem virtuellen Azure-Computer erstellen, müssen Sie die Datei **\\%windir%\\Panther\\Unattend.xml** umbenennen, da sie anderenfalls das Uploadskript blockiert, das in einem der folgenden Schritte verwendet wird. Benennen Sie diese Datei in "Unattend.old" um, damit sie ggf. darauf zurückgreifen können, falls Sie die Bereitstellung rückgängig machen müssen.
10. Bereiten Sie das Abbild mit SYSPREP vor. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten den folgenden Befehl aus:

    **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

    **Hinweis:** Verwenden Sie nicht den Switch **/mode:vm** des SYSPREP-Befehls, auch wenn es sich um einen virtuellen Computer handelt.

## Nächste Schritte

Nach dem Erstellen des benutzerdefinierten Vorlagenimage müssen Sie dieses Abbild in die RemoteApp-Bereitstellung hochladen. Informationen zum Erstellen einer Bereitstellung finden Sie in den folgenden Artikeln:

-   [Erstellen einer Hybridbereitstellung von RemoteApp][Erstellen einer Hybridbereitstellung von RemoteApp]
-   [Erstellen einer Cloud-Bereitstellung einer RemoteApp][Erstellen einer Cloud-Bereitstellung einer RemoteApp]

  []: http://azure.microsoft.com/de-de/services/remoteapp/
  [Azure PowerShell]: http://azure.microsoft.com/de-de/documentation/articles/install-configure-powershell/
  [Erstellen einer Hybridbereitstellung von RemoteApp]: http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-hybrid-deployment/
  [Erstellen einer Cloud-Bereitstellung einer RemoteApp]: http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-cloud-deployment/
