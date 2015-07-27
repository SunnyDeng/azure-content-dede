<properties
	pageTitle="Erstellen Sie einen virtuellen Computer unter Windows im Azure-Vorschauportal"
	description="Erfahren Sie, wie Sie mithilfe der VM-Galerie im Azure-Vorschauportal einen virtuellen Azure-Computer unter Windows erstellen."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/03/2015"
	ms.author="kathydav"/>

# Erstellen Sie einen virtuellen Computer unter Windows im Azure-Vorschauportal#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

In diesem Lernprogramm erfahren Sie, wie einfach es ist, einen virtuellen Azure-Computer (VM) im Azure-Vorschauportal zu erstellen. Als Beispiel wird ein Windows Server-Image verwendet. Dies ist jedoch nur eines von vielen Images, die Azure bietet. Beachten Sie, dass Ihre Imageauswahl von Ihrem Abonnement abhängt. Beispielsweise können Desktop-Images für MSDN-Abonnenten verfügbar sein.

Sie können auch mithilfe [eigener Images](virtual-machines-create-upload-vhd-windows-server.md) virtuelle Computer erstellen. Informationen zu dieser und zu anderen Methoden finden Sie unter [Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Erstellen des virtuellen Computers

Unter Verwendung von Windows Server 2012 R2 Datacenter als Beispiel können Sie in wenigen Minuten das Erstellen eines virtuellen Computers ausprobieren. Sie können die Azure-Standardeinstellungen für den Großteil der Konfiguration verwenden.

1. Melden Sie sich beim [Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie im Hub-Menü auf **Neu**.

3. Klicken Sie im Blatt **Neu** auf **Compute** **>** **Windows Server 2012 R2 Datacenter**.

	![Wählen Sie ein VM-Image aus dem Marketplace aus.](./media/virtual-machines-windows-tutorial/marketplace_portal.png)

4. Geben Sie im Fenster **VM Erstellen** den **Hostnamen** ein, den Sie für den VM verwenden möchten, sowie den **Benutzernamen** des Administrators und ein starkes **Passwort**. **Benutzername** bezieht sich auf das Administratorkonto, das Sie zum Verwalten des Servers verwenden. Erstellen Sie ein Kennwort, das schwer zu erraten ist, dass Sie sich aber gut merken können. **Sie brauchen den Benutzernamen und das Passwort, um sich am virtuellen Computer anzumelden**. Wenn Sie das Kennwort vergessen haben, können Sie es mithilfe [dieser Anweisungen](virtual-machines-windows-reset-password.md) zurücksetzen.

	![Konfigurieren Sie den Hostnamen und die Anmeldeinformationen.](./media/virtual-machines-windows-tutorial/create_vm_name_pwd_portal.png)

5. Überprüfen Sie die Standardeinstellungen, z. B. **Preisstufe** und **Optionale Konfiguration**. Diese Optionen beeinflussen die Größe des virtuellen Computers und auch Netzwerkoptionen, z. B. die Domänenmitgliedschaft. Um beispielsweise Premium-Speicher auf einem virtuellen Computer zu testen, müssen Sie eine unterstützte Region und Größe auswählen. Für den ersten virtuellen Computer sind die Standardeinstellungen in der Regel ausreichend.

	>[AZURE.NOTE]Premium-Speicher ist für virtuelle Computer der DS-Reihe in bestimmten Regionen verfügbar. Premium-Speicher ist die beste Speicheroption für datenintensive Arbeitslasten wie beispielsweise Datenbanken. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](storage-premium-storage-preview-portal.md).

6. Wenn Sie die Einstellungen überprüft oder aktualisiert haben, klicken Sie auf **Erstellen**.

7. Während Azure den VM erstellt, können Sie den Fortschritt in **Benachrichtigungen** im Hub-Menü verfolgen. Nachdem Azure den virtuellen Computer erstellt hat, wird dieser in Ihrem Startmenü angezeigt, sofern Sie die Option **An Startmenü anheften** auf dem Blatt **Virtuellen Computer erstellen** nicht deaktiviert haben.

## Melden Sie sich beim virtuellen Computer an.

Nachdem Sie den virtuellen Computer erstellt haben, sollten Sie sich bei diesem anmelden, um die Einstellungen und Anwendungen zu verwalten, die darauf ausgeführt werden.

>[AZURE.NOTE]Tipps zu den Voraussetzungen und zur Problembehandlung finden Sie unter [Verbinden mit einem virtuellen Azure-Computer mit RDP oder SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Melden Sie sich beim [Vorschauportal](https://portal.azure.com) an, falls noch nicht geschehen.

2. Klicken Sie im Startmenü auf Ihren virtuellen Computer. Wenn Sie den virtuellen Computer suchen müssen, klicken Sie auf **Durchsuchen** > **Virtuelle Computer**. Wählen Sie dann Ihren virtuellen Computer aus der Liste aus.

3. Klicken Sie im VM-Blatt auf **Verbinden**.

	![Melden Sie sich beim virtuellen Computer an.](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Klicken Sie auf **Öffnen**, um die Remotedesktop-Protokolldatei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.

5. Klicken Sie auf **Verbinden**.

6. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers erstellt haben, und klicken Sie dann auf **OK**.

7. Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

	Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

## Nächste Schritte

Weitere Informationen zur Konfiguration von virtuellen Windows-Computern auf Azure finden Sie in den folgenden Artikeln:

[Verbinden virtueller Computer mit einem virtuellen Netzwerk oder einem Clouddienst](cloud-services-connect-virtual-machine.md)

[Anfügen eines Datenträgers an einen virtuellen Computer](storage-windows-attach-disk.md)

[Verwalten der Verfügbarkeit virtueller Computer](../manage-availability-virtual-machines.md)

[Zu den Azure-VM-Konfigurationseinstellungen](http://msdn.microsoft.com/library/azure/dn763935.aspx)
 

<!---HONumber=July15_HO2-->