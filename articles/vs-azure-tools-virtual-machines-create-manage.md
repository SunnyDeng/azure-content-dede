<properties
   pageTitle="Erstellen und Verwalten virtueller Azure-Computer in Visual Studio | Microsoft Azure"
   description="Erstellen und Verwalten virtueller Azure-Computer in Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="virtual-machines"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/05/2016"
   ms.author="tarcher" />

# Erstellen und Verwalten virtueller Azure-Computer in Visual Studio

Sie können mithilfe des Server-Explorers in Visual Studio virtuelle Computer in Azure erstellen.

## Erstellen eines virtuellen Azure-Computers im Server-Explorer

Sie können einen virtuellen Computer im [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=253103) erstellen. Alternativ dazu können Sie einen virtuellen Computer in Azure auch mithilfe von Befehlen im Server-Explorer erstellen. Virtuelle Computer können z. B. verwendet werden, um ein Front-End hinter einem allgemeinen öffentlichen Endpunkt mit Lastenausgleich bereitzustellen.

### So erstellen Sie einen neuen virtuellen Computer

1. Öffnen Sie im Server-Explorer den Knoten **Azure**, und klicken Sie dann auf den Knoten **Virtuelle Computer**.

1. Klicken Sie im Kontextmenü auf **Virtuellen Computer erstellen**.

    Der Assistent zum **Erstellen eines neuen virtuellen Computers** wird angezeigt.

    ![Der Befehl "Virtuellen Computer erstellen"](./media/vs-azure-tools-virtual-machines-create-manage/IC718342.png)

1. Wählen Sie auf der Seite **Abonnement auswählen** ein Abonnement aus, das beim Erstellen des virtuellen Computers verwendet werden soll, und klicken Sie dann auf **Weiter**.

    Wenn Sie nicht bei Azure angemeldet sind, klicken Sie auf **Anmelden**, um sich anzumelden. Wählen Sie dann im Dropdownlistenfeld Ihr Azure-Abonnement aus, wenn es nicht bereits ausgewählt ist.

1. Wählen Sie auf der Seite **Image des virtuellen Computers auswählen** aus dem Dropdownlistenfeld **Imagetyp** einen Imagetyp aus, und wählen Sie dann im Listenfeld **Imagename** ein Image des virtuellen Computers aus. Wenn Sie fertig sind, klicken Sie auf **Weiter**.

    ![Seite zum Auswählen eines Image für einen virtuellen Computer](./media/vs-azure-tools-virtual-machines-create-manage/IC744137.png)

    Sie können die folgenden Imagetypen auswählen.

    - **Öffentliche Images** führt die Images virtueller Computer für Betriebssysteme und Serversoftware wie etwa Windows Server und SQL Server auf.

    - **MSDN-Images** führt die Images virtueller Computer für Software auf, die für MSDN-Abonnenten verfügbar ist, z. B. Visual Studio und Microsoft Dynamics.

    - **Private Images** führt spezialisierte und generalisierte Images virtueller Computer auf, die Sie erstellt haben.

    Weitere Informationen zu spezialisierten und generalisierten virtuellen Computern finden Sie unter [VM-Image](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/). Weitere Informationen zum Umwandeln eines virtuellen Computers in eine Vorlage, mit der schnell neue, vorkonfigurierte virtuelle Computer erstellt werden können, finden Sie unter [Erfassen eines virtuellen Windows-Computers, um ihn als Vorlage zu verwenden](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/).

    Sie können auf den Namen des Images eines virtuellen Computers klicken, um rechts auf der Seite Informationen zum Image anzuzeigen.

    >[AZURE.NOTE] Sie können den Listen **Öffentliche Images** oder **MSDN-Images** keine Images virtueller Computer hinzufügen, da diese Listen schreibgeschützt sind. Alle virtuellen Computer, die Sie erstellen, werden der Liste **Private Images** hinzugefügt.

    >Wenn Sie über ein MSDN-Abonnement auf Visual Studio-Ebene verfügen, können Sie einen vordefinierten virtuellen Azure-Computer erstellen, der Visual Studio enthält, sowie verschiedene weitere Images. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers in Visual Studio mithilfe von Images – Visual Studio 2013-Katalogimage für MSDN-Abonnenten](http://visualstudio2013msdngalleryimage.azurewebsites.net) und [MSDN-Abonnements](https://www.visualstudio.com/products/msdn-subscriptions-vs).

1. Geben Sie auf der Seite **Grundeinstellungen des virtuellen Computers** einen Computernamen ein, und fügen Sie dann die Spezifikationen für den virtuellen Computer (z. B. die Größe) sowie einen Benutzernamen und ein Kennwort hinzu. Wenn Sie fertig sind, klicken Sie auf **Weiter**.

    Sie verwenden den neuen Namen und das Kennwort, um sich mithilfe von Remotedesktop am Computer anzumelden. Daher ist es sinnvoll, sich diese Angaben zu notieren. Nachdem Sie einen virtuellen Azure-Computer in Visual Studio erstellt haben, können Sie seine Größe und andere Einstellungen im [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=253103) ändern.

        >[AZURE.NOTE] If you choose larger sizes for the virtual machine, extra charges may apply. See [Virtual Machines Pricing Details](https://azure.microsoft.com/pricing/details/virtual-machines/) for more information.

1. Für virtuelle Computer, die in Visual Studio erstellt werden, ist ein Clouddienst erforderlich. Wählen Sie auf der Seite **Einstellungen des Clouddiensts** einen Clouddienst für den virtuellen Computer aus, oder klicken Sie in der Dropdownliste auf **Neu erstellen…**, wenn Sie noch nicht über einen Clouddienst verfügen oder einen neuen Clouddienst verwenden möchten. Ein Speicherkonto ist ebenfalls erforderlich. Wählen Sie daher aus dem Dropdownlistenfeld **Speicherkonto** ein Speicherkonto aus (oder erstellen Sie ein neues Speicherkonto). Weitere Informationen finden Sie unter [Einführung in Microsoft Azure Storage](./storage/storage-introduction/).

1. Wenn Sie ein virtuelles Netzwerk angeben möchten (dies ist optional), wählen Sie dieses in den Dropdownlistenfeldern "Virtuelles Netzwerk" und "Subnetz" aus.

    Virtuelle Computer, die Mitglieder einer Verfügbarkeitsgruppe sind, werden in verschiedenen Fehlerdomänen bereitgestellt. Weitere Informationen finden Sie unter [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/).

1. Wenn Ihr virtueller Computer zu einer Verfügbarkeitsgruppe gehören soll (ebenfalls optional), aktivieren Sie das Kontrollkästchen **Verfügbarkeitsgruppe angeben**, und wählen Sie dann im Dropdownlistenfeld eine Verfügbarkeitsgruppe aus. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **Weiter**.

    Indem Sie Ihren virtuellen Computer einer Verfügbarkeitsgruppe hinzufügen, können Sie sicherstellen, dass die Anwendung bei Netzwerkfehlern, Hardwarefehlern des lokalen Datenträgers und während geplanter Ausfallzeiten verfügbar bleibt. Zum Erstellen von virtuellen Netzwerken, Subnetzen und Verfügbarkeitsgruppen müssen Sie das [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=253103) verwenden. Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/).

1. Geben Sie auf der Seite **Endpunkte** die öffentlichen Endpunkte an, die für Benutzer Ihres virtuellen Computers verfügbar sein sollen. Sie können z. B. zusätzlich zu den Remotedesktop- und PowerShell-Endpunkten, die standardmäßig aktiviert sind, HTTP (Port 80) aktivieren. Um einen Endpunkt hinzuzufügen, wählen Sie im Dropdownlistenfeld **Portname** einen Endpunkt aus, und klicken Sie auf die Schaltfläche **Hinzufügen**. Wenn Sie einen Endpunkt entfernen möchten, wählen Sie in der Liste der Endpunkte das rote **X** neben dem Namen aus.

    ![Die Seite "Endpunkte" im Assistenten für virtuelle Computer](./media/vs-azure-tools-virtual-machines-create-manage/IC718351.png)

    Welche Endpunkte verfügbar sind, richtet sich nach dem Clouddienst, den Sie für Ihren virtuellen Computer ausgewählt haben. Weitere Informationen finden Sie unter [Azure-Dienstendpunkte](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).

    >[AZURE.NOTE] Durch Aktivieren öffentlicher Endpunkte werden Dienste auf Ihrem virtuellen Computer im Internet verfügbar. Stellen Sie sicher, dass die Endpunkte und Dienste auf Ihrem virtuellen Computer installiert und ordnungsgemäß konfiguriert werden, z. B. durch Festlegen von Zugriffssteuerungslisten (Access Control Lists, ACLs) für die Endpunkte. Weitere Informationen finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).

1. Nachdem Sie die Einstellungen des virtuellen Computers konfiguriert haben, klicken Sie auf die Schaltfläche **Erstellen**, um den virtuellen Computer zu erstellen.

    Während Azure den virtuellen Computer erstellt, zeigt das **Azure-Aktivitätsprotokoll** den Status des Erstellungsvorgangs des virtuellen Computers an.

    ![Aktivitätsprotokoll für virtuelle Computer – wird ausgeführt](./media/vs-azure-tools-virtual-machines-create-manage/IC744138.png)

    Wenn Sie nur Informationen zum virtuellen Computer anzeigen möchten, klicken Sie im **Azure-Aktivitätsprotokoll** auf die Registerkarte **Virtuelle Computer**.

    ![Aktivitätsprotokoll für virtuelle Computer – abgeschlossen](./media/vs-azure-tools-virtual-machines-create-manage/IC744139.png)

    Wenn der Vorgang erfolgreich abgeschlossen wurde, wird der neue virtuelle Computer unter dem Knoten **Virtuelle Computer** im Server-Explorer angezeigt. Sie können sich an diesem Computer anmelden, indem Sie auf die Verknüpfung **Mithilfe von Remotedesktop verbinden** klicken.

    ![Virtueller Computer im Server-Explorer](./media/vs-azure-tools-virtual-machines-create-manage/IC744140.png)

## Verwalten Ihrer virtuellen Computer

Auf der Konfigurationsseite des virtuellen Computers können Sie den ausgewählten virtuellen Computer herunterfahren, Verbindungen herstellen, den Computer aktualisieren und dem Computer Prüfpunkte hinzufügen. Außerdem können Sie Einstellungen für den virtuellen Computer anzeigen oder ändern. Sie können Folgendes ausführen:

- Ändern der Größe des virtuellen Computers

- Auswählen der Verfügbarkeitsgruppe, die mit dem virtuellen Computer verwendet werden soll

- Hinzufügen, Entfernen oder Ändern von Einstellungen für öffentliche Endpunkte

- Hinzufügen, Entfernen oder Konfigurieren von Erweiterungen für virtuelle Computer

- Anzeigen von Informationen zu den Datenträgern, die dem virtuellen Computer zugeordnet sind

### Anzeigen oder Ändern der Einstellungen virtueller Computer

1. Wählen Sie im Server-Explorer im Knoten **Virtuelle Azure-Computer** Ihren virtuellen Computer aus.

1. Wählen Sie im Kontextmenü die Option **Konfigurieren** aus, um die Konfigurationsseite des virtuellen Computers anzuzeigen.

    ![Die Konfigurationsseite für virtuelle Azure-Computer](./media/vs-azure-tools-virtual-machines-create-manage/IC744141.png)

1. Zeigen Sie die Informationen des virtuellen Computers, oder ändern Sie diese.

### Speichern oder Wiederherstellen des Status Ihres virtuellen Computers

Wenn Sie Ihren virtuellen Computer konfigurieren und Software darauf installieren, ist es sinnvoll, den Fortschritt regelmäßig zu speichern, indem Sie Prüfpunkte für den virtuellen Computer erstellen. Ein Prüfpunkt ist eine Momentaufnahme bzw. ein Image des aktuellen Status Ihres virtuellen Computers. Wenn ein Problem mit dem virtuellen Computer auftritt oder Sie den virtuellen Computer neu konfigurieren möchten, können Sie Zeit sparen, indem Sie einen früheren Prüfpunktstatus des virtuellen Computers wiederherstellen, anstatt neu zu beginnen.

### So erstellen Sie einen Prüfpunkt für einen virtuellen Computer

1. Wählen Sie im Server-Explorer im Knoten **Virtuelle Azure-Computer** Ihren virtuellen Computer aus.

1. Wählen Sie im Kontextmenü die Option **Konfigurieren** aus, um die Konfigurationsseite des virtuellen Computers anzuzeigen.

1. Wählen Sie auf der Konfigurationsseite die Schaltfläche **Image aufzeichnen** aus.

    ![Aufzeichnungsschaltfläche auf der Konfigurationsseite in Azure](./media/vs-azure-tools-virtual-machines-create-manage/IC744142.png)

    Das Dialogfeld **Virtuellen Computer erfassen** wird angezeigt.

    ![Das Azure-Dialogfeld zum Aufzeichnen des virtuellen Computers](./media/vs-azure-tools-virtual-machines-create-manage/IC744143.png)

1. Geben Sie eine Bezeichnung und eine Beschreibung für das Image ein. Eine Standardbezeichnung und -beschreibung werden bereitgestellt, Sie können diese jedoch auf Wunsch mit Ihren eigenen Angaben überschreiben.

1. Wenn Sie Sysprep bereits auf diesem virtuellen Computer ausgeführt haben, aktivieren Sie das Kontrollkästchen **Ich habe Sysprep auf dem virtuellen Computer ausgeführt**.

    Sysprep ist ein Tool, das u. a. systemspezifische Daten aus der Windows-Version des virtuellen Computers entfernt und auf diese Weise eine Vorlage erstellt, die andere Computer verwenden können. Weitere Informationen hierzu finden Sie unter [Erfassen eines virtuellen Windows-Computers, um ihn als Vorlage zu verwenden](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/).

1. Nachdem Sie die Aufzeichnungseinstellungen konfiguriert haben, klicken Sie auf die Schaltfläche **Aufzeichnen**, um den Prüfpunkt zu erstellen.

    Während Azure den Prüfpunkt erstellt, zeigt das **Azure-Aktivitätsprotokoll** den Status des Vorgangs an.

    ![Aufzeichnen eines Prüfpunkts für einen virtuellen Computer](./media/vs-azure-tools-virtual-machines-create-manage/IC744144.png)

    Nachdem die Erstellung des Prüfpunkts abgeschlossen wurde, wird der Vorgang im **Azure-Aktivitätsprotokoll** angezeigt.

    ![Prüfpunktvorgang abgeschlossen](./media/vs-azure-tools-virtual-machines-create-manage/IC744145.png)

## So verwalten Sie Prüfpunkte für virtuelle Computer

### So stellen Sie einen zuvor gespeicherten Zustand eines virtuellen Computers wieder her

- Befolgen Sie die Schritte unter [Schritt für Schritt: Ausführen von Cloudwiederherstellungen virtueller Microsoft Azure-Computer mithilfe von PowerShell – Teil 2](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx).

### So löschen Sie einen Prüfpunkt

1. Wechseln Sie zum [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=253103).

1. Wählen Sie auf der Konfigurationsseite des virtuellen Computers oben auf der Seite die Registerkarte **Images** aus.

1. Wählen Sie den Prüfpunkt aus, den Sie löschen möchten, und klicken Sie dann unten auf der Seite auf die Schaltfläche **Löschen**.

## Herunterfahren des virtuellen Computers

1. Wählen Sie im Server-Explorer im Knoten **Virtuelle Azure-Computer** den virtuellen Computer aus, den Sie herunterfahren möchten.

1. Wählen Sie im Kontextmenü den Befehl **Herunterfahren** aus, oder wählen Sie **Konfigurieren** aus, um die Konfigurationsseite des virtuellen Computers anzuzeigen, und klicken Sie dann auf die Schaltfläche **Herunterfahren**.

## Nächste Schritte

Weitere Informationen zum Erstellen von virtuellen Computern finden Sie unter [Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-tutorial.md) und [Erstellen ein virtuellen Windows-Computers im Azure-Vorschauportal](virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_0128_2016-->