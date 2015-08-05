<properties
	pageTitle="Erstellen Sie einen virtuellen Computer unter Windows im Azure-Vorschauportal | Microsoft Azure"
	description="Erfahren Sie, wie Sie mithilfe des Azure Marketplace im Azure-Vorschauportal eine Azure-VM-Ressource unter Windows erstellen."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="kathydav"/>

# Erstellen eines virtuellen Computers unter Windows im Azure-Vorschauportal#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Manager](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

In diesem Lernprogramm erfahren Sie, wie einfach es ist, in wenigen Minuten einen virtuellen Azure-Computer (VM) im Azure-Vorschauportal zu erstellen. Wir verwenden ein Windows Server 2012 R2 Datacenter-Image als Beispiel für die Erstellung des virtuellen Computers im Azure-Ressourcen-Manager, aber dies ist nur eines von vielen Images, die Azure bereitstellt. Beachten Sie, dass Ihre Imageauswahl von Ihrem Abonnement abhängt. Beispielsweise können Desktop-Images für MSDN-Abonnenten verfügbar sein.

Sie können virtuelle Computer auch unter Verwendung eigener Images, mithilfe von Ressourcen-Manager-Vorlagen oder mit Automatisierungstools erstellen. Informationen zu den verschiedenen Methoden finden Sie unter [Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Auswählen des Images

Wechseln Sie im Vorschauportal zum Azure Marketplace, um das gewünschte Windows-Server-Image zu finden.

1. Melden Sie sich beim [Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie im Menü "Hub" auf **Neu** > **Compute** > **Windows Server 2012 R2 Datacenter**.

	![Marketplace](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP]Um weitere Images zu suchen, klicken Sie auf **Marketplace**, und suchen oder filtern Sie dann die verfügbaren Elemente.

3. Wählen Sie auf der Seite **Windows Server 2012 R2 Datacenter** die Option **Ressourcen-Manager-Stapel verwenden**, um den virtuellen Computer im Azure-Ressourcen-Manager zu erstellen. (Für die meisten neuen Workloads empfiehlt sich der Ressourcen-Manager-Stapel. Überlegungen hierzu finden Sie unter [Azure-Compute-, -Netzwerk- und -Speicheranbieter im Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md). Klicken Sie dann auf **Erstellen**.

	![Suchen auf dem Marketplace](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## Erstellen des virtuellen Computers

Nachdem Sie das Image ausgewählt haben, können Sie für den Großteil der Konfiguration die Azure-Standardeinstellungen verwenden und den virtuellen Computer rasch erstellen.

1. Klicken Sie auf dem Blatt **Virtuellen Computer erstellen** auf **Grundlagen**. Geben Sie einen **Namen** für den virtuellen Computer, sowie den **Benutzernamen** des Administrators und ein starkes **Kennwort** ein. Wenn Sie über mehr als ein Abonnement verfügen, geben Sie das für den neuen virtuellen Computer geltende Abonnement an sowie eine neue oder vorhandene **Ressourcengruppe** und den **Standort** eines Azure-Rechenzentrums.

	![Konfigurieren von VM-Grundlagen](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	>[AZURE.NOTE]**Benutzername** bezieht sich auf das Administratorkonto, das Sie zum Verwalten des Servers verwenden. Erstellen Sie ein Kennwort, das schwer zu erraten ist, dass Sie sich aber gut merken können. **Sie brauchen den Benutzernamen und das Passwort, um sich am virtuellen Computer anzumelden**.

2. Klicken Sie auf **Größe**, und wählen Sie eine für Ihre Anforderungen geeignete Größe für den virtuellen Computer aus. Für jede Größe ist die Anzahl von Prozessorkernen, den Arbeitsspeicher und andere Features angegeben, wie z. B. Unterstützung für Storage Premium, die sich auf den Preis auswirken. Azure empfiehlt anhand des gewählten Images automatisch bestimmte Größen.

	![Konfigurieren der VM-Größe](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE]Storage Premium steht für virtuelle Computer der DS-Reihe in bestimmten Regionen zur Verfügung. Storage Premium die beste Speicheroption für datenintensive Workloads wie Datenbanken. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](storage-premium-storage-preview-portal.md).

3. Klicken Sie auf **Einstellungen**, um die Einstellungen für Hauptspeicher und Netzwerkfähigkeit des neuen virtuellen Computers anzuzeigen. Für einen ersten virtuellen Computer können Sie im Allgemeinen die Standardeinstellungen akzeptieren. Wenn Sie eine geeignete Größe für den virtuellen Computer ausgewählt haben, können Sie durch Auswahl von **Premium (SSD)** unter **Datenträgertyp** Storage Premium testen.

	![Konfigurieren von VM-Einstellungen](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. Klicken Sie auf **Zusammenfassung**, um Ihre Konfigurationsoptionen zu überprüfen. Wenn Sie die Einstellungen überprüft oder aktualisiert haben, klicken Sie auf **Erstellen**.

	![Konfigurieren von VM-Einstellungen](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Während Azure den virtuellen Computer erstellt, können Sie den Status unter **Benachrichtigungen** im Hub-Menü nachverfolgen. Nachdem Azure den virtuellen Computer erstellt hat, wird dieser in Ihrem Startmenü angezeigt, sofern Sie die Option **An Startmenü anheften** auf dem Blatt **Virtuellen Computer erstellen** nicht deaktiviert haben.

## Melden Sie sich beim virtuellen Computer an.

Nachdem Sie den virtuellen Computer erstellt haben, sollten Sie sich bei diesem anmelden, um die Einstellungen und Anwendungen zu verwalten, die darauf ausgeführt werden.

>[AZURE.NOTE]Tipps zu den Voraussetzungen und zur Problembehandlung finden Sie unter [Verbinden mit einem virtuellen Azure-Computer mit RDP oder SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Melden Sie sich beim [Vorschauportal](https://portal.azure.com) an, falls noch nicht geschehen.

2. Klicken Sie auf Ihren VM im Startboard Wenn Sie den virtuellen Computer suchen müssen, klicken Sie auf **Alle durchsuchen** > **Aktuell** oder **Alle durchsuchen** > **Virtuelle Computer**. Wählen Sie dann Ihren virtuellen Computer aus der Liste aus.

3. Klicken Sie im VM-Blatt auf **Verbinden**.

	![Melden Sie sich beim virtuellen Computer an.](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Klicken Sie auf **Öffnen**, um die Remotedesktop-Protokolldatei zu verwenden, die automatisch für den virtuellen Windows Server-Computer erstellt wurde.

5. Klicken Sie auf **Verbinden**.

6. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben, und klicken Sie dann auf **OK**.

7. Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

	Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

## Nächste Schritte

* Verwenden Sie Azure PowerShell und die Azure-Befehlszeilenschnittstelle zum [Suchen und Auswählen von VM-Images](resource-groups-vm-searching.md).
* Automatisieren Sie die VM- und Workloadbereitstellung und die Verwaltung unter Verwendung des [Azure-Ressourcen-Managers](virtual-machines-how-to-automate-azure-resource-manager.md) und der [Azure-Ressourcen-Manager-Vorlagen](http://azure.microsoft.com/documentation/templates/).

<!---HONumber=July15_HO4-->