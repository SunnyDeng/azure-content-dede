<properties
	pageTitle="Anmelden bei einem virtuellen Windows-Computer | Microsoft Azure"
	description="Verwenden Sie das Azure-Portal für die Anmeldung bei einem virtuellen Windows-Computer, der mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurde."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Anmelden bei einem virtuellen Windows-Computer über das Azure-Portal


Im Azure-Portal können Sie über die Schaltfläche **Verbinden** eine Remotedesktopsitzung starten und sich bei einem virtuellen Windows-Computer anmelden.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md).


## Herstellen einer Verbindung mit dem virtuellen Computer

1. Melden Sie sich beim Azure-Portal an.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie den virtuellen Computer aus.

3. Klicken Sie in der Befehlsleiste am unteren Rand der Seite auf **Verbinden**.

	![Anmelden beim virtuellen Computer](./media/arm_log_on_windows_vm/rm_windows_connect.png)
	

4. Nach Klicken auf **Verbinden** wird eine Remotedesktopprotokolldatei (RDP-Datei) erstellt und heruntergeladen. Klicken Sie auf **Öffnen**, damit diese Datei verwendet wird.

	![Anmelden beim virtuellen Computer](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. Klicken Sie im Fenster "Remotedesktop" auf **Verbinden**, um den Vorgang fortzusetzen.

	![Stellen Sie eine Verbindung her.](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. Geben Sie im Fenster "Windows-Sicherheit" den Benutzernamen und das Kennwort eines Kontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**.

 	In den meisten Fällen sind die Anmeldeinformationen der Benutzername und das Kennwort des lokalen Kontos, das Sie beim Erstellen des virtuellen Computers angegeben haben. Die Domäne ist der Name des virtuellen Computers im Eingabeformat *VM-Name*&#92;*Benutzername*.
	
	Wenn der virtuelle Computer zu einer Domäne in Ihrer Organisation gehört, stellen Sie sicher, dass der Benutzername den Namen der Domäne im Format *Domäne*&#92;*Benutzername* enthält. Das Konto muss zur Gruppe „Administratoren“ gehören oder über Remotezugriffsrechte für den virtuellen Computer verfügen.
	
	Wenn der virtuelle Computer ein Domänencontroller ist, geben Sie den Benutzernamen und das Kennwort eines Domänenadministratorkontos für diese Domäne an.

7.	Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen und das Anmelden zu beenden.

	![Bestätigen Sie die Identität des Computers.](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## Nächste Schritte

Wenn beim Verbindungsversuch Probleme auftreten, finden Sie unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md) Informationen.

<!---HONumber=AcomDC_0211_2016-->