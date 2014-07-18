<properties linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Windows Server VHD to Azure" metaKeywords="Azure VHD, uploading VHD" description="Learn how to create and upload a virtual hard disk (VHD) in Azure that has the Windows Server operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Windows Server Operating System" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Erstellen und Hochladen einer virtuellen Festplatte, die das Windows Server-Betriebssystem enthält
==================================================================================================

In Azure läuft ein virtueller Computer mit dem Betriebssystem, das bei dessen Erstellung gewählt wurde. Azure speichert das Betriebssystem eines virtuellen Computers auf einer virtuellen Festplatte im VHD-Format (als .vhd-Datei). Eine VHD eines Betriebssystems, die zur Duplizierung angelegt wurde, wird Image genannt. In diesem Artikel wird beschrieben, wie Sie Ihr eigenes Image durch Hochladen einer .vhd-Datei mit einem von Ihnen installierten und generalisierten Betriebssystem erstellen. Weitere Informationen zu Datenträgern und Images in Azure finden Sie unter [Datenträger und Images verwalten](http://msdn.microsoft.com/en-us/library/windowsazure/jj672979.aspx).

**Hinweis**: Beim Erstellen eines virtuellen Computers haben Sie die Möglichkeit, Einstellungen des Betriebssystems Ihrer Anwendung anzupassen. Die von Ihnen gewählte Konfiguration wird für den entsprechenden virtuellen Computer auf der Festplatte gespeichert. Anweisungen dazu finden Sie unter [How to Create a Custom Virtual Machine](/en-us/manage/windows/how-to-guides/custom-create-a-vm/) (in englischer Sprache).

Voraussetzungen
---------------
In diesem Artikel wird vorausgesetzt, dass Sie über Folgendes verfügen:

**Ein Verwaltungszertifikat** - Sie haben ein Verwaltungszertifikat für das Abonnement erstellt, für das Sie eine VHD hochladen möchten, und Sie haben das Zertifikat in eine .cer-Datei exportiert. Weitere Informationen zum Erstellen von Zertifikaten finden Sie unter [Erstellen eines Verwaltungszertifikats für Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx).

**Ein in einer .vhd-Datei gespeichertes unterstütztes Windows-Betriebssystem** - Sie haben ein unterstütztes Windows Server-Betriebssystem auf einer virtuellen Festplatte installiert. Zum Erstellen von .vhd-Dateien gibt es verschiedene Tools. Sie können eine Virtualisierungslösung wie Hyper-V verwenden, um die .vhd-Datei zu erstellen und das Betriebssystem zu installieren. Anweisungen dazu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/en-us/library/hh846766.aspx).

**Wichtig**: Das neuere VHDX-Format wird in Azure nicht unterstützt. Mit Hyper-V Manager oder convert-vhd cmdlet können Sie die Festplatte in das VHD-Format konvertieren.

- **Windows Server-Betriebssystemmedien.** Diese Aufgabe erfordert eine .iso-Datei mit dem Windows Server-Betriebssystem. Die folgenden Windows Server-Versionen werden unterstützt:

    <TABLE BORDER="1" WIDTH="600">
	  	<TR BGCOLOR="#E9E7E7">
	    	<th>Betriebssystem</th>
	    	<th>SKU</th>
	    	<th>Service Pack</th>
	    	<th>Architektur</th>
	    </TR>
	    <tr>
	    	<td>Windows Server 2012</td>
	    	<td>Alle Editionen</td>
	    	<td>n. z.</td>
	    	<td>x64</td>
		</tr>
		<tr>
	    	<td>Windows Server 2008 R2</td>
	    	<td>Alle Editionen</td>
	    	<td>SP1</td>
	    	<td>x64</td>
	    </tr>
    </table>

-   Das Cmdlet [Add-AzureVHD](http://msdn.microsoft.com/en-us/library/windowsazure/dn205185.aspx), das Bestandteil des Azure PowerShell-Moduls ist. Informationen zum Herunterladen dieses Moduls finden Sie unter [Azure Downloads](/en-us/develop/downloads/).

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Vorbereiten des hochzuladenden Images](#prepimage)
-   [Schritt 2: Erstellen eines Speicherkontos in Azure](#createstorage)
-   [Schritt 3: Vorbereiten der Verbindung mit Azure](#prepAzure)
-   [Schritt 4: Hochladen der .vhd-Datei](#upload)

Schritt 1: Vorbereiten des hochzuladenden Images
------------------------------------------------

Bevor das Bild zu Azure hochgeladen werden kann, muss es mit dem Tool Sysprep generalisiert werden. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [How to Use Sysprep: An Introduction](http://technet.microsoft.com/en-us/library/bb457073.aspx) (in englischer Sprache).

Auf dem soeben erstellten virtuellen Computer führen Sie das folgende Verfahren durch:

1.  Melden Sie sich beim Betriebssystem an.

2.  Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator. Wechseln Sie das Verzeichnis zu **%windir%\\system32\\sysprep**, und führen Sie dann `sysprep.exe` aus.

    ![Öffnen eines Eingabeaufforderungsfensters](./media/virtual-machines-create-upload-vhd-windows-server/sysprepcommand.png)

3.  Das Dialogfeld **Systemvorbereitungstool** wird angezeigt.

    ![Starten von Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  Wählen Sie unter **Systembereinigungsaktion** die Option **Out-of-Box-Experience (OOBE) für System aktivieren** und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist.

5.  Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

6.  Klicken Sie auf **OK**.

Schritt 2: Erstellen eines Speicherkontos in Azure
--------------------------------------------------

Ein Speicherkonto stellt die höchste Ebene des Namespace für den Zugriff auf Speicherdienste dar und ist Ihrem Azure-Abonnement zugeordnet. Sie benötigen ein Speicherkonto in Azure, um eine .vhd-Datei auf Azure hochladen zu können, die zum Erstellen eines virtuellen Computers verwendet werden kann. Über das Azure-Verwaltungsportal können Sie ein Speicherkonto erstellen.

1.  Melden Sie sich beim Azure-Verwaltungsportal an.

2.  Klicken Sie in der Befehlsleiste auf **Neu**.

3.  Klicken Sie auf **Speicherkonto** und dann auf **Schnellerfassung**.

    ![Schnelles Erstellen eines Speicherkontos](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4.  Füllen Sie die Felder wie folgt aus:

    ![Eingeben von Details zum Speicherkonto](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

-   Geben Sie unter **URL** einen Unterdomänennamen ein, der im URL für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Name wird der Hostname im URL, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

-   Wählen Sie den Standort oder die Affinitätsgruppe für das Speicherkonto. Durch Angabe einer Affinitätsgruppe können Sie Ihre Clouddienste im gleichen Datencenter mit Ihrem Speicher zusammenstellen.

-   Entscheiden Sie, ob Sie Georeplikation für das Speicherkonto verwenden möchten. Georeplikation ist standardmäßig aktiviert. Mit dieser Option werden Ihre Daten kostenlos zu einem sekundärem Standort gebracht, damit Ihr Speicher auf diesen ausweichen kann, falls ein größerer Ausfall auftritt, der am primären Standort nicht gehandhabt werden kann. Der sekundäre Standort wird automatisch zugewiesen und kann nicht geändert werden. Falls gesetzliche Bestimmungen oder Unternehmensrichtlinien eine strengere Kontrolle über den Standort des cloudbasierten Speichers verlangen, können Sie die Georeplikation deaktivieren. Es ist jedoch zu beachten, dass bei späterer Aktivierung von Georeplikation eine einmalige Datenübertragungsgebühr für das Replizieren der bestehenden Daten am sekundären Standort fällig wird. Speicherdienste ohne Georeplikation werden mit einer Ermäßigung angeboten.

5.  Klicken Sie auf **Speicherkonto erstellen**.

    Das Konto wird nun unter **Speicherkonten** angezeigt.

    ![Speicherkonto erfolgreich erstellt](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

Schritt 3: Vorbereiten der Verbindung mit Azure
-----------------------------------------------

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen Ihrem Computer und Ihrem Abonnement in Azure herstellen.

1.  Öffnen Sie ein Azure PowerShell-Fenster.

2.  Geben Sie Folgendes ein:

    `Get-AzurePublishSettingsFile`

    Mit diesem Befehl wird ein Browserfenster geöffnet und automatisch eine .publishsettings-Datei heruntergeladen, die Informationen und ein Zertifikat für Ihr Azure-Abonnement enthält.

3.  Speichern Sie die .publishsettings-Datei.

4.  Geben Sie Folgendes ein:

    `Import-AzurePublishSettingsFile <PathToFile>`

    Wobei `<PathToFile>` der vollständige Pfad zur .publishsettings-Datei ist.

    Weitere Informationen finden Sie unter [Erste Schritte mit Azure-Cmdlets](http://msdn.microsoft.com/en-us/library/windowsazure/jj554332.aspx)


Schritt 4: Hochladen der .vhd-Datei
-----------------------------------

Beim Hochladen der .vhd-Datei können Sie diese an beliebiger Stelle im Blob-Speicher ablegen. Bei den folgenden Befehlsbeispielen ist **BlobStorageURL** der URL für das in Schritt 2 erstellte Speicherkonto, und **YourImagesFolder** ist der Container im Blob-Speicher, wo Sie Ihre Images ablegen möchten. **VHDName** ist die Bezeichnung, die im Verwaltungsportal zur Kennzeichnung der virtuellen Festplatte angezeigt wird. **PathToVHDFile** ist der vollständige Pfad und Name der .vhd-Datei.

1.  Im Azure PowerShell-Fenster des vorigen Schritts geben Sie Folgendes ein:

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    Weitere Informationen finden Sie unter [Add-AzureVhd](http://msdn.microsoft.com/en-us/library/windowsazure/dn205185.aspx) (in englischer Sprache).

Hinzufügen des Images zur Liste benutzerdefinierter Images
----------------------------------------------------------
Nach dem Hochladen fügen Sie die .vhd-Datei als Image zu der Ihrem Abonnement zugeordneten Liste benutzerdefinierter Images hinzu.

1.  Im Verwaltungsportal klicken Sie unter **Alle Elemente** auf **Virtuelle Computer**.

2.  Unter Virtuelle Computer klicken Sie auf **Images** und dann auf **Erstellen**.

3.  In **Ein Image aus einer VHD erstellen** geben Sie einen Namen und den URL zur hochgeladenen .vhd-Datei an.

4.  Markieren Sie **Ich habe Sysprep auf dem virtuellen Computer ausgeführt, der dieser VHD zugeordnet ist.**, um zu bestätigen, dass Sie das Betriebssystem in Schritt 2 generalisiert haben, und klicken Sie dann auf **OK**.


Nächste Schritte
----------------
Nachdem das Image in der Liste verfügbar ist, kann es zum Erstellen von virtuellen Computern verwendet werden. Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers](../virtual-machines-windows-tutorial/).


[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload