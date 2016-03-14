<properties
	pageTitle="Erstellen eines virtuellen Azure-Computers mit Linux im klassischen Azure-Portal | Microsoft Azure"
	description="Verwenden Sie das klassische Azure-Portal, um mithilfe der Azure-Ressourcengruppen einen virtuellen Azure-Computer (VM) zu erstellen, auf dem Linux ausgeführt wird."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Erstellen eines virtuellen Linux-Computers im Azure-Portal

> [AZURE.SELECTOR]
- [Portal – Windows](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [PowerShell – Vorlage](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Portal – Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [BEFEHLSZEILENSCHNITTSTELLE (CLI)](virtual-machines-linux-tutorial.md)

<br>


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

Das Erstellen eines virtuellen Azure-Computers (VM), auf dem Linux ausdeführt wird, ist ganz einfach. In diesem Tutorial erfahren Sie, wie Sie mit dem Azure-Portal einen solchen virtuellen Computer schnell erstellen können, und es wird anhand der Datei `~/.ssh/id_rsa.pub`, die den öffentlichen Schlüssel enthält, die Konfiguration einer **SSH**-Verbindung zum Schutz Ihres virtuellen Computers gezeigt. Sie können auch mithilfe [eigener Images als Vorlagen](virtual-machines-linux-create-upload-vhd.md) entsprechende virtuelle Linux-Computer erstellen.

> [AZURE.NOTE] In diesem Lernprogramm wird ein virtueller Azure-Computer erstellt, der über die Azure-Ressourcengruppen-API verwaltet wird. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md).


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Auswählen des Images

Wechseln Sie im Vorschauportal zum Azure Marketplace, um das gewünschte Windows Server-VM-Image zu finden.

1. Melden Sie sich beim [Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie im Hub-Menü auf **Neu** > **Compute** > **Ubuntu Server 14.04 LTS**.

	![Auswählen eines VM-Abbilds](media/virtual-machines-linux-tutorial-portal-rm/chooseubuntuvm.png)

	> [AZURE.TIP] Um weitere Images zu suchen, klicken Sie auf **Marketplace**, und durchsuchen oder filtern Sie dann die verfügbaren Elemente.

3. Wählen Sie unten auf der Seite **Ubuntu Server 14.04 LTS** die Option **Use the Resource Manager stack** aus, um den virtuellen Computer im Azure-Ressourcen-Manager zu erstellen. Beachten Sie, dass für die meisten neuen Workloads der Ressourcen-Manager-Stapel empfehlenswert ist. Überlegungen hierzu finden Sie unter [Azure Computing-, Netzwerk- und Speicheranbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md).

4. Klicken Sie anschließend auf ![Schaltfläche erstellen](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png).

	![Wechseln zum Ressourcen-Manager-Compute-Stapel](media/virtual-machines-linux-tutorial-portal-rm/changetoresourcestack.png)

## Erstellen des virtuellen Computers

Nachdem Sie das Image ausgewählt haben, können Sie für den Großteil der Konfiguration die Azure-Standardeinstellungen verwenden und den virtuellen Computer rasch erstellen.

1. Klicken Sie auf dem Blatt **Virtuellen Computer erstellen** auf **Grundlagen**. Geben Sie den gewünschten **Namen** für den virtuellen Computer und eine Datei für den öffentlichen Schlüssel ein (im **SSH-RSA**-Format, in diesem Fall aus der Datei `~/.ssh/id_rsa.pub`). Wenn Sie über mehrere Abonnements verfügen, geben Sie das für den neuen virtuellen Computer geltende Abonnement an sowie eine neue oder vorhandene **Ressourcengruppe** und den **Speicherort** eines Azure-Rechenzentrums.

	![](media/virtual-machines-linux-tutorial-portal-rm/step-1-thebasics.png)

	> [AZURE.NOTE] Außerdem können Sie hier die Authentifizierung mit Benutzername und Kennwort auswählen und die betreffenden Daten eingeben, falls Sie Ihre **SSH**-Sitzung nicht mit dem Austausch eines öffentlichen und privaten Schlüssels schützen möchten.

2. Klicken Sie auf **Größe**, und wählen Sie eine für Ihre Anforderungen geeignete Größe für den virtuellen Computer aus. Für jede Größe sind die Anzahl von Prozessorkernen, der Arbeitsspeicher und andere Features angegeben, wie z. B. Unterstützung für Storage Premium, die sich auf den Preis auswirken. Azure empfiehlt anhand des gewählten Abbilds automatisch bestimmte Größen. Klicken Sie anschließend auf ![Schaltfläche auswählen](media/virtual-machines-linux-tutorial-portal-rm/selectbutton-size.png).

	>[AZURE.NOTE] Storage Premium steht für virtuelle Computer der DS-Reihe in bestimmten Regionen zur Verfügung. Storage Premium ist die beste Speicheroption für datenintensive Workloads wie Datenbanken. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](../storage/storage-premium-storage.md).

3. Klicken Sie auf **Einstellungen**, um die Einstellungen für Hauptspeicher und Netzwerkfähigkeit des neuen virtuellen Computers anzuzeigen. Für einen ersten virtuellen Computer können Sie im Allgemeinen die Standardeinstellungen akzeptieren. Wenn Sie eine geeignete Größe für den virtuellen Computer ausgewählt haben, können Sie durch Auswahl von **Premium (SSD)** unter **Datenträgertyp** Storage Premium testen. Klicken Sie anschließend auf ![Schaltfläche "OK"](media/virtual-machines-linux-tutorial-portal-rm/okbutton.png).

	![](media/virtual-machines-linux-tutorial-portal-rm/step-3-settings.png)

6. Klicken Sie auf **Zusammenfassung**, um Ihre Konfigurationsoptionen zu überprüfen. Wenn Sie die Einstellungen überprüft oder aktualisiert haben, klicken Sie auf ![Schaltfläche "OK"](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png).

	![Zusammenfassung der Erstellung](media/virtual-machines-linux-tutorial-portal-rm/summarybeforecreation.png)

8. Während Azure den virtuellen Computer erstellt, können Sie den Status unter **Benachrichtigungen** im Hub-Menü nachverfolgen. Nachdem Azure den virtuellen Computer erstellt hat, wird dieser in Ihrem Startmenü angezeigt, sofern Sie die Option **An Startmenü anheften** auf dem Blatt **Virtuellen Computer erstellen** nicht deaktiviert haben.

	> [AZURE.NOTE] Beachten Sie, dass die Zusammenfassung keinen öffentlichen DNS-Namen enthält, wie das beispielsweise der Fall ist, wenn ein virtueller Computer innerhalb eines Clouddiensts mithilfe des Service-Management-Compute-Stapels erstellt wird.

## Herstellen einer Verbindung mit dem virtuellen Azure-Linux-Computer über **SSH**

Sie können sich jetzt wie gewohnt über **SSH** mit dem virtuellen Ubuntu-Computer verbinden. Allerdings müssen Sie die IP-Adresse ermitteln, die dem virtuellen Azure-Computer zugeteilt wurde, indem Sie die Kachel des virtuellen Computers mit dessen Ressourcen öffnen. Klicken Sie hierzu auf **Durchsuchen**, und wählen Sie **Zuletzt verwendet** aus, um nach dem erstellten virtuellen Computer zu suchen, oder klicken Sie auf die für Sie erstellte Kachel im Startmenü. Suchen und kopieren Sie in beiden Fällen die in der folgenden Grafik gezeigte **öffentliche IP-Adresse**.

![Zusammenfassung der erfolgreichen Erstellung](media/virtual-machines-linux-tutorial-portal-rm/successresultwithip.png)

Nun können Sie per **SSH** auf Ihren virtuellen Azure-Computer zugreifen und mit der Arbeit beginnen.

	ssh ops@23.96.106.1 -p 22
	The authenticity of host '23.96.106.1 (23.96.106.1)' can't be established.
	ECDSA key fingerprint is bc:ee:50:2b:ca:67:b0:1a:24:2f:8a:cb:42:00:42:55.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '23.96.106.1' (ECDSA) to the list of known hosts.
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-43-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Mon Jul 13 21:36:28 UTC 2015

	  System load: 0.31              Memory usage: 5%   Processes:       208
	  Usage of /:  42.1% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@ubuntuvm:~$


> [AZURE.NOTE] Sie können auch einen vollqualifizierten Domänennamen (FQDN) für den virtuellen Computer im Portal konfigurieren. Weitere Informationen über das [Erstellen von FQDNs im Portal](virtual-machines-create-fqdn-on-portal.md).

## Nächste Schritte

Weitere Informationen zu Linux auf Azure erhalten Sie unter:

- [Linux und Open-Source-Computing auf Azure](virtual-machines-linux-opensource.md)

- [Verwenden des Azure-Befehlszeilentools für Mac und Linux](virtual-machines-command-line-tools.md)

- [Bereitstellen einer LAMP-App mithilfe der Azure-CustomScript-Erweiterung für Linux](virtual-machines-linux-script-lamp.md)

- [Die Docker-Erweiterung für virtuelle Linux-Computer auf Azure](virtual-machines-docker-vm-extension.md)

<!---HONumber=AcomDC_0302_2016-->