<properties
	pageTitle="Erstellen eines virtuellen Computers unter Windows im Azure-Portal | Microsoft Azure"
	description="Erfahren Sie, wie Sie mithilfe von Azure Marketplace im Azure-Portal eine Azure-VM oder einen virtuellen Computer, auf dem Windows ausgeführt wird, erstellen können."
	keywords="Azure-VM, Erstellen eines virtuellen Computers, virtueller Computer, Einrichten eines virtuellen Computers"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/14/2015"
	ms.author="cynthn"/>

# Erstellen Sie einen virtuellen Windows-Computer im Azure-Portal#

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [Azure classic portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)

<br>

In diesem Tutorial erfahren Sie, wie einfach es ist, in wenigen Minuten einen virtuellen Azure-Computer im Azure-Portal zu erstellen. Wir verwenden ein Windows Server 2012 R2 Datacenter-Image als Beispiel für das Erstellen des virtuellen Computers, aber dies ist nur eines von vielen in Azure bereitgestellten Images. Ihre Imageauswahl hängt von Ihrem Abonnement ab. Beispielsweise können Desktop-Images für MSDN-Abonnenten verfügbar sein.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell. Ausführliche Informationen zum Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).

Sie können virtuelle Computer auch unter Verwendung eigener Images, mithilfe von Ressourcen-Manager-Vorlagen oder mit Automatisierungstools erstellen. Informationen zu den verschiedenen Methoden finden Sie unter [Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Exemplarische Vorgehensweise per Video

Hier finden Sie eine exemplarische Vorgehensweise für dieses Lernprogramm.

[AZURE.VIDEO create-a-virtual-machine-running-windows-in-the-azure-preview-portal]

## Wählen Sie das Image des virtuellen Computers aus.

1. Melden Sie sich beim Azure-Portal an.

2. Klicken Sie im Menü „Hub“ auf **Neu** > **Compute** > **Windows Server 2012 R2 Datacenter**.

	![Screenshot, der die Azure-VM-Images zeigt, die im Vorschauportal verfügbar sind](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP]Um weitere Images zu suchen, klicken Sie auf **Marketplace**, und durchsuchen oder filtern Sie dann die verfügbaren Elemente.

3. Wählen Sie auf der Seite **Windows Server 2012 R2 Datacenter** unter **Bereitstellungsmodell auswählen** die Option **Ressourcen-Manager** aus. Klicken Sie auf **Erstellen**.

	![Screenshot, der das Bereitstellungsmodell zeigt, das für eine Azure-VM ausgewählt werden kann](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## Erstellen eines virtuellen Computers

Nachdem Sie das Image ausgewählt haben, können Sie für den Großteil der Konfiguration die Azure-Standardeinstellungen verwenden und den virtuellen Computer rasch erstellen.

1. Klicken Sie auf dem Blatt **Virtuellen Computer erstellen** auf **Grundlagen**. Geben Sie einen **Namen** für den virtuellen Computer, sowie den **Benutzernamen** des Administrators und ein starkes **Kennwort** ein. Wenn Sie über mehr als ein Abonnement verfügen, geben Sie das für den neuen virtuellen Computer geltende Abonnement sowie eine neue oder vorhandene **Ressourcengruppe** und den **Standort** eines Azure-Datencenters an.

	![Screenshot, der die grundlegenden Einstellungen zeigt, die für eine Azure-VM konfiguriert werden können](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	>[AZURE.NOTE]**Benutzername** bezieht sich auf das Administratorkonto, das Sie zum Verwalten des Servers verwenden. Erstellen Sie ein Kennwort, das schwer zu erraten ist, das Sie sich aber gut merken können. **Sie brauchen den Benutzernamen und das Passwort, um sich am virtuellen Computer anzumelden**.

2. Klicken Sie auf **Größe**, und wählen Sie eine für Ihre Anforderungen geeignete Größe für den virtuellen Computer aus. Für jede Größe ist die Anzahl von Prozessorkernen, den Arbeitsspeicher und andere Features angegeben, wie z. B. Unterstützung für Storage Premium, die sich auf den Preis auswirken. Azure empfiehlt anhand des gewählten Images automatisch bestimmte Größen.

	![Screenshot, der die Azure-VM-Größen zeigt, die Sie auswählen können](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE]Storage Premium steht für virtuelle Computer der DS-Reihe in bestimmten Regionen zur Verfügung. Storage Premium ist die beste Speicheroption für datenintensive Workloads wie Datenbanken. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](storage-premium-storage-preview-portal.md).

3. Klicken Sie auf **Einstellungen**, um die Speicher- und Netzwerkeinstellungen des neuen virtuellen Computers anzuzeigen. Für einen ersten virtuellen Computer können Sie i. Allg. die Standardeinstellungen übernehmen. Wenn Sie eine geeignete Größe für den virtuellen Computer ausgewählt haben, können Sie durch Auswahl von **Premium (SSD)** unter **Datenträgertyp** Storage Premium testen.

	![Screenshot, der die optionalen Features zeigt, die Sie für eine Azure-VM konfigurieren können](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. Klicken Sie auf **Zusammenfassung**, um Ihre Konfigurationsoptionen zu überprüfen. Wenn Sie die Einstellungen überprüft oder aktualisiert haben, klicken Sie auf **Erstellen**.

	![Screenshot, der die Zusammenfassung der getroffenen Konfigurationsauswahl für die Azure-VM zeigt](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Während Azure den virtuellen Computer erstellt, können Sie den Status unter **Benachrichtigungen** im Menü „Hub“ nachverfolgen. Nachdem Azure den virtuellen Computer erstellt hat, wird dieser in Ihrem Startmenü angezeigt, sofern Sie die Option **An Startmenü anheften** auf dem Blatt **Virtuellen Computer erstellen** nicht deaktiviert haben.

## Anmelden beim virtuellen Computer

Nachdem Sie den virtuellen Computer erstellt haben, sollten Sie sich bei diesem anmelden, um die Einstellungen und Anwendungen zu verwalten, die darauf ausgeführt werden.

>[AZURE.NOTE]Tipps zu den Voraussetzungen und zur Problembehandlung finden Sie unter [Verbinden mit einem virtuellen Azure-Computer mit RDP oder SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Melden Sie sich beim Azure-Portal an, falls Sie dies noch nicht getan haben.

2. Klicken Sie auf dem virtuellen Computer auf das Startmenü. Wenn Sie den virtuellen Computer suchen müssen, klicken Sie auf **Alle durchsuchen** > **Aktuell** oder **Alle durchsuchen** > **Virtuelle Computer**. Wählen Sie dann den virtuellen Computer aus der Liste aus.

3. Klicken Sie auf dem Blatt des virtuellen Computers auf **Verbinden**.

	![Screenshot, der anzeigt, wo Sie die Schaltfläche „Verbinden“ auf dem Azure-VM-Blatt finden](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Klicken Sie auf **Öffnen**, um die Remotedesktop-Protokolldatei zu verwenden, die automatisch für den virtuellen Windows Server-Computer erstellt wurde.

5. Klicken Sie auf **Verbinden**.

6. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben, und klicken Sie dann auf **OK**.

7. Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

	Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

## Nächste Schritte

* Verwenden Sie Azure PowerShell und die Azure-Befehlszeilenschnittstelle zum [Suchen und Auswählen von Images für virtuelle Computer](resource-groups-vm-searching.md).
* Automatisieren Sie die Bereitstellung und Verwaltung von virtuellen Computern und Workloads mithilfe von [Azure-Ressourcen-Manager](virtual-machines-how-to-automate-azure-resource-manager.md) und [Azure-Ressourcen-Manager-Vorlagen](http://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_1203_2015-->