<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Erstellen einer Hybridbereitstellung von RemoteApp

Es existieren zwei Bereitstellungsarten für RemoteApps:

-   Cloud: Die Bereitstellung erfolgt vollständig in der Cloud mithilfe der Option **Schnellerfassung** im Azure-Verwaltungsportal.
-   Hybrid: Verwendet ein virtuelles Netzwerk für lokalen Zugriff und wird mithilfe der Optionen **Mit VPN erstellen** im Verwaltungsportal erstellt.

Dieses Lernprogramm führt Sie durch den Prozess der Erstellung einer Hybridbereitstellung. Es besteht aus sieben Schritten:

1.  Erstellen eines RemoteApp-Vorlagenimage.
2.  Erstellen eines RemoteApp-Dienstes.
3.  Verbinden mit einem virtuellen Netzwerk.
4.  Verbinden mit einem Vorlagenimage.
5.  Konfigurieren der Verzeichnissynchronisierung Dies ist für die Synchronisierung von Benutzern, Gruppen, Kontakten und Kennwörtern vom lokalen Active Directory zum Azure Active Directory-Mandanten erforderlich.
6.  Veröffentlichen von RemoteApp-Programmen.
7.  Konfigurieren des Benutzerzugriffs.

**Voraussetzungen**

Bevor Sie mit der Erstellung des Dienstes beginnen, führen Sie Folgendes aus:

-   Installieren Sie [erforderliche Updates][], um die Leistung der Azure RemoteApp zu verbessern.
-   Registrieren Sie sich für die [Vorschau der RemoteApp][].
-   Erstellen Sie ein Benutzerkonto in Active Directory, das als Konto für den RemoteApp-Dienst dient. Beschränken Sie die Berechtigungen für dieses Konto, sodass es nur Computer in die Domäne einbinden kann.
-   Sammeln Sie folgende Informationen über das lokale Netzwerk: IP-Adresse und Informationen zum VPN-Gerät.
-   Installieren Sie das [Azure PowerShell][]-Modul.
-   Sammeln Sie Informationen über die Benutzer und Gruppen, denen Sie Zugriff gewähren möchten. Dies können Informationen zu Microsoft-Konten oder Active Directory-Organisationskonten sein.

## **Schritt 1: Erstellen eines Vorlagenimage**

Azure RemoteApp verwendet ein Windows Server 2012 R2-Vorlagenimage, um alle Programme zu hosten, die Sie an die Benutzer freigeben möchten. Um ein benutzerdefiniertes RemoteApp-Vorlagenimage zu erstellen, beginnen Sie mit einem bestehenden Abbild oder erstellen Sie ein neues. Das Abbild, das für die Verwendung mit Azure RemoteApp hochgeladen werden soll, muss folgende Anforderungen erfüllen:

-   Es muss sich auf einer VHD-Datei befinden (VHDX-Dateien werden derzeit nicht unterstützt).
-   Die VHD kann eine feste Größe haben oder dynamisch erweiterbar sein. Wir empfehlen eine dynamisch erweiterbare VHD, da das Hochladen dieser in Azure weniger Zeit in Anspruch nimmt.
-   Der Datenträger muss mit der Master Boot Record (MBR)-Partitionierung initialisiert werden. Die GPT-Partitionierung (GUID-Partitionstabelle) wird nicht unterstützt.
-   Die VHD muss eine einzige Installation von Windows Server 2012 R2 enthalten. Sie kann mehrere Volumes enthalten, jedoch nur eines mit einer Windows-Installation.
-   Die RDSH-Rolle (Remote Desktop Session Host) und das Desktopdarstellung-Feature müssen installiert sein.
-   Encrypting File System (EFS) muss deaktiviert sein.
-   Das Abbild muss mit SYSPREP unter Verwendung der Parameter **/oobe /generalize /shutdown** vorbereitet werden. Verwenden Sie nicht den Parameter **/mode:vm**.

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
        2.  Geben Sie Speicherort, Größe und VHD-Format an. Wählen Sie **Dynamisch expandierend** und klicken Sie auf **OK**.

            Die Erstellung benötigt einige Sekunden. Wenn die Erstellung abgeschlossen ist, sollte in der Datenträgerverwaltungskonsole ein neuer Datenträger ohne Laufwerksbuchstaben und mit dem Status "Nicht initialisiert" angezeigt werden.

        -   Klicken Sie mit der rechten Maustaste auf den Datenträger (nicht auf den nicht zugewiesenen Speicherplatz) und klicken Sie dann auf **Datenträger initialisieren**. Wählen Sie als Partitionsstil **MBR** (Master Boot Record) und klicken Sie auf **OK**.
        -   Erstellen eines neuen Volume: Klicken Sie mit der rechten Maustaste auf den auf den nicht zugewiesenen Speicherplatz und dann auf **Neues einfaches Volume**. Sie können die Standardeinstellungen im Assistenten verwenden; weisen Sie jedoch einen Laufwerksbuchstaben zu, um Probleme beim Hochladen des Vorlagenimage zu vermeiden.
        -   Klicken Sie mit der rechten Maustaste auf den Datenträger und dann auf **VHD trennen**.

3.  Installieren von Windows Server 2012 R2:

    1.  Erstellen Sie einen neuen virtuellen Computer. Verwenden Sie den Assistenten für neue virtuelle Computer im Hyper-V Manager oder Hyper-V für Clients.

        1.  Wählen Sie auf der Seite "Virtuelle Festplatte anschließen" **Vorhandene virtuelle Festplatte verwenden** und gehen Sie zu der im vorhergehenden Schritt erstellten VHD.
        2.  Wählen Sie auf der Seite "Installationsoptionen" **Betriebssystem von Start-CD/DVD\_ROM installieren** und wählen Sie dann den Ort der Windows Server 2012 R2-Installationsdatenträger.
        3.  Wählen Sie im Assistenten die weiteren Optionen, die für die Installation von Windows und der Anwendungen erforderlich sind. Beenden Sie den Assistenten.

    2.  Bearbeiten Sie nach Abschluss des Assistenten die Einstellungen des virtuellen Computers und nehmen Sie alle für die Installation von Windows und Ihrer Programme erforderlichen Änderungen, wie z. B. bezüglich der Zahl der virtuellen Prozessoren, vor. Klicken Sie dann auf **OK**.
    3.  Stellen Sie eine Verbindung mit dem virtuellen Computer her und installieren Sie Windows Server 2012 R2.

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
    12. Wählen Sie auf der Seite "Installationsauswahl bestätigen" falls erforderlich **Zielserver automatisch neu starten** und klicken Sie bei Erscheinen der Neustartwarnung auf **Ja**.
    13. Klicken Sie auf **Installieren**. Der Computer wird neu gestartet.

5.  Installieren Sie die für Ihre Anwendungen benötigten zusätzlichen Features, wie z. B. .NET Framework 3.5. Führen Sie dazu den Assistenten zum Hinzufügen von Rollen und Features aus.
6.  Installieren und konfigurieren Sie die Programme und Anwendungen, die Sie über RemoteApp veröffentlichen möchten.

    **Wichtig:** Microsoft empfiehlt, die RDSH-Rolle vor der Installation der Anwendungen zu installieren, um sicherzugehen, dass Probleme mit der Anwendungskompatibilität vor dem Hochladen des Abbilds in RemoteApp entdeckt werden.

7.  Führen Sie weitere, von den Anwendungen benötigte Windows-Konfigurationen aus.
8.  Deaktivieren Sie Encrypting File System (EFS). Führen Sie in einem Befehlsfenster mit erhöhten Rechten den folgenden Befehl aus:

        Fsutil behavior set disableencryption 1

    Alternativ können Sie in der Registrierung den folgenden DWORD-Wert festlegen oder hinzufügen:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1

9.  Wenn Sie das Abbild in einem **Virtuellen Azure-Computer** erstellen, müssen Sie die Datei **\\Windows\\Panther\\Unattend.xml** löschen oder umbenennen, da sie anderenfalls das Uploadskript blockiert, das in einem der folgenden Schritte verwendet wird.
10. Bereiten Sie das Abbild mit SYSPREP vor. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten den folgenden Befehl aus:

    **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

    **Hinweis:** Verwenden Sie nicht den Switch **/mode:vm** des SYSPREP-Befehls, auch wenn es sich um einen virtuellen Computer handelt.

## **Schritt 2: Erstellen eines RemoteApp-Diensts**

1.  Gehen Sie im [Azure-Verwaltungsportal][] zur Seite "RemoteApp".
2.  Klicken Sie auf **Neu \> Mit VPN erstellen**.
3.  Geben Sie einen Namen für den Dienst ein und klicken Sie auf **RemoteApp-Dienst erstellen**.

Gehen Sie nach dem Erstellen des RemoteApp-Dienstes zur RemoteApp-Seite **Schnellstart**, um mit der Einrichtung fortzufahren.

## **Schritt 3: Verbinden mit einem virtuellen Netzwerk**

Ein virtuelles Netzwerk ermöglicht es den Benutzern, auf Daten auf Ihrem lokalen Netzwerk über RemoteApp-Remoteressourcen zuzugreifen. Die Konfiguration der Verbindung mit dem virtuellen Netzwerk besteht aus vier Schritten:

1.  Klicken Sie auf der Seite "Schnellstart" auf **Virtuelles RemoteApp-Netzwerk verbinden**.
2.  Bestimmen Sie, ob ein neues virtuelles Netzwerk erstellt oder ein vorhandenes verwendet werden soll. In diesem Lernprogramm erstellen wir ein neues Netzwerk.
3.  Geben Sie die folgenden Informationen an:

    -   Name
    -   Adressraum des virtuellen Netzwerks
    -   lokaler Adressraum
    -   IP-Adresse des DNS-Servers
    -   IP-Adresse des VPN

    Weitere Informationen erhalten Sie unter [Konfigurieren eines standortübergreifenden VPN im Verwaltungsportal][].

4.  Klicken Sie auf der Seite "Schnellstart" auf **Skript herunterladen**, um ein Skript zur Konfiguration des VPN-Geräts herunterzuladen, damit dieses eine Verbindung mit dem gerade erstellten virtuellen Netzwerk herstellt. Sie benötigen folgende Informationen über das VPN-Gerät:

    -   Hersteller
    -   Plattform
    -   Betriebssystem

    Speichern Sie das Skript und führen Sie es auf dem VPN-Gerät aus.

    **Hinweis:** Der Status des virtuellen Netzwerks ändert sich nach dem Ausführen dieses Skripts in "Bereit". Dies kann 5 bis 7 Minuten dauern. Sie können das Netzwerk erst verwenden, wenn es fertiggestellt ist.

5.  Klicken Sie abschließend auf der Seite "Schnellstart" auf **Lokaler Domäne beitreten**. Fügen Sie das Konto des RemoteApp-Dienstes zur lokalen Active Directory-Domäne hinzu. Dazu benötigen Sie den Domänennamen, die Organisationseinheit, den Benutzernamen des Kontos und das Kennwort.

## **Schritt 4: Verbinden mit einem RemoteApp-Vorlagenimage**

Ein RemoteApp-Vorlagenimage enthält die Programme, die Sie an die Benutzer freigeben möchten. Sie können das in Schritt 1 erstellte Vorlagenimage hochladen oder eine Verbindung zu einem vorhandenen Abbild (das bereits in Azure hochgeladen wurde) herstellen.

Wenn Sie ein neues Abbild hochladen, müssen Sie den Namen des Abbilds eingeben und den Speicherort wählen. Auf der nächsten Seite des Assistenten wird Ihnen eine Gruppe von PowerShell-Cmdlets angezeigt. Kopieren Sie diese Cmdlets und führen Sie sie von einer Azure PowerShell-Eingabeaufforderung mit erhöhten Rechten aus, um das Abbild hochzuladen.

Wenn Sie eine Verbindung zu einem vorhandenen Abbild herstellen, geben Sie einfach den Namen und den Speicherort des Abbilds sowie das dazugehörige Azure-Abonnement an.

## **Schritt 5: Konfigurieren der Active Directory-Verzeichnissynchronisierung**

RemoteApp erfordert die Verzeichnissynchronisierung zwischen Azure Active Directory und dem lokalen Active Directory, um Benutzer, Gruppen, Kontakte und Kennwörter zum Azure Active Directory-Mandanten zu synchronisieren. Informationen zur Planung und eine detaillierte Anleitung finden Sie unter [Fahrplan zur Verzeichnissynchronisierung][].

## **Schritt 6: Veröffentlichen von RemoteApp-Programmen**

Ein RemoteApp-Programm ist die App oder das Programm, das Sie den Benutzern zur Verfügung stellen. Es befindet sich im Vorlagenimage, das Sie für den Dienst hochgeladen haben. Wenn ein Benutzer auf das RemoteApp-Programm zugreift, scheint dieses Programm in der lokalen Umgebung des Benutzers zu laufen, tatsächlich wird es aber in Azure ausgeführt.

Bevor ein Benutzer aber auf das RemoteApp-Programm zugreifen kann, muss es im Endbenutzer-Feed – einer Liste verfügbarer Programme, auf die der Benutzer über das Azure-Portal zugreift – veröffentlicht werden.

Sie können in Ihrem RemoteApp-Dienst mehrere Programme veröffentlichen. Um ein Programm hinzuzufügen, klicken Sie auf der Seite mit den RemoteApp-Programmen auf **Veröffentlichen**. Sie können das Programm vom Startmenü des Vorlagenimage aus veröffentlichen oder den Pfad zum Programm im Vorlagenimage angeben. Wenn Sie das Programm vom Startmenü aus veröffentlichen möchten, wählen Sie das zu veröffentlichende Programm aus. Wenn Sie den Pfad angeben möchten, geben Sie den Namen des Programms sowie den Pfad an, unter dem das Programm im Vorlagenimage installiert ist.

## **Schritt 7: Konfigurieren des Benutzerzugriffs**

Nach dem Erstellen des RemoteApp-Dienstes müssen Sie nun die Benutzer und Gruppen hinzufügen, die Zugriff auf die Remoteressourcen haben sollen. Die Benutzer und Gruppen müssen in dem Active Directory-Mandanten vorhanden sein, der zu dem Abonnement gehört, das Sie für die Erstellung dieses RemoteApp-Dienstes verwendet haben.

1.  Klicken Sie auf der Seite "Schnellstart" auf **Benutzerzugriff konfigurieren**.
2.  Geben Sie das Organisationskonto, den Gruppennamen (aus Active Directory) oder das Microsoft-Konto ein, dem Sie den Zugriff erteilen möchten.

    Verwenden Sie für Benutzer das Format “<user@domain.com>”. Für Gruppen geben Sie den Gruppennamen ein.

3.  Sobald die Benutzer oder Gruppen überprüft wurden, klicken Sie auf **Speichern**.

## Nächste Schritte

Sie haben die RemoteApp-Hybridbereitstellung nun erfolgreich erstellt und bereitgestellt. Als Nächstes müssen die Benutzer den Remotedesktopclient herunterladen und installieren. Die URL für den Client finden Sie auf der RemoteApp-Schnellstartseite. Die Benutzer müssen sich nun bei Azure anmelden und auf die veröffentlichten RemoteApp-Programme zugreifen.

  [erforderliche Updates]: http://support.microsoft.com/kb/2977219
  [Vorschau der RemoteApp]: http://azure.microsoft.com/en-us/services/remoteapp/
  [Azure PowerShell]: http://azure.microsoft.com/de-de/documentation/articles/install-configure-powershell/
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Konfigurieren eines standortübergreifenden VPN im Verwaltungsportal]: http://msdn.microsoft.com/library/azure/dn133795.aspx
  [Fahrplan zur Verzeichnissynchronisierung]: http://msdn.microsoft.com//library/azure/hh967642.aspx
