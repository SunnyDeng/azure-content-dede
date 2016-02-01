<properties
	pageTitle="Erfassen von virtuellen Linux-Computern für die Verwendung als Vorlage | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit dem Azure-Ressourcen-Manager-Bereitstellungsmodell ein Image eines Linux-basierten virtuellen Azure-Computers erfassen können."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/05/2015"
	ms.author="danlep"/>


# Erfassen eines virtuellen Linux-Computers zur Verwendung als Ressourcen-Manager-Vorlage

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-linux-capture-image.md).


Dieser Artikel erläutert, wie Sie mit Azure Command-Line Interface (CLI) einen virtuellen Azure-Computer erfassen, auf dem Linux ausgeführt wird, um ihn als Azure-Ressourcen-Manager-Vorlage zum Erstellen anderer virtueller Computer zu verwenden. Diese Vorlage umfasst den Betriebssystemdatenträger und die an den virtuellen Computer angefügten Datenträger. Nicht enthalten sind die Ressourcen des virtuellen Netzwerks, die Sie zum Erstellen eines virtuellen Azure-Ressourcen-Manager-Computers benötigen, In den meisten Fällen ist eine separate Einrichtung erforderlich, bevor Sie einen weiteren virtuellen Computer erstellen, der die Vorlage verwendet.

## Voraussetzungen

Diese Schritte setzen voraus, dass Sie bereits mithilfe des Azure-Ressourcen-Manager-Bereitstellungsmodells einen virtuellen Azure-Computer erstellt, das Betriebssystem konfiguriert, beliebige Datenträger angefügt und weitere Anpassungen wie die Installation von Anwendungen vorgenommen haben. Ist das noch nicht erfolgt, finden Sie hier Anweisungen zum Verwenden der Azure-CLI im Azure-Ressourcen-Manager-Modus:

- [Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und der Azure-CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

Beispielsweise können Sie eine Ressourcengruppe mit dem Namen *MyResourceGroup* in der Region USA Mitte erstellen. Verwenden Sie dann einen **azure vm quick-create**-Befehl ähnlich dem Folgenden zur Bereitstellung eines virtuellen Computers mit Ubuntu 14.04 LTS in der Ressourcengruppe.

 	azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "centralus" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:14.04.201507060 -u <your-user-name> -p <your-password>

Nachdem der virtuelle Computer bereitgestellt und ausgeführt wird, sollten Sie einen Datenträger anbringen. Wie das geht, erfahren Sie [hier](virtual-machines-linux-tutorial.md#attach-and-mount-a-disk).

Um weitere Anpassungen vorzunehmen, müssen Sie den virtuellen Computer mit einem SSH-Client Ihrer Wahl verbinden. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit dem virtuellen Azure-Linux-Computer über SSH](virtual-machines-linux-tutorial-portal-rm.md#connect-to-your-azure-linux-vm-using-strongsshstrong)


## Erfassen des virtuellen Computers

1. Wenn Sie den virtuellen Computer erfassen möchten, stellen Sie mit Ihrem SSH-Client eine Verbindung dazu her.

2. Geben Sie im SSH-Fenster den folgenden Befehl ein. Beachten Sie, dass das Ergebnis aus **waagent** je nach Version dieses Dienstprogramms geringfügig abweichen kann:

	`sudo waagent -deprovision+user`

	Dieser Befehl versucht, das System zu bereinigen und für eine erneute Bereitstellung vorzubereiten. Bei diesem Vorgang werden die folgenden Aufgaben ausgeführt:

	- Entfernen von SSH-Hostschlüsseln (sofern "Provisioning.RegenerateSshHostKeyPair" in der Konfigurationsdatei auf "y" festgelegt ist)
	- Löschen der Namenserverkonfiguration in "/etc/resolv.conf"
	- Entfernen des Kennworts des `root`-Benutzers aus "/etc/shadow" (sofern "Provisioning.DeleteRootPassword" in der Konfigurationsdatei auf "y" festgelegt ist)
	- Entfernen von zwischengespeicherten DHCP-Clientleases
	- Setzt den Hostnamen auf "localhost.localdomain" zurück
	- Löscht das zuletzt bereitgestellte Benutzerkonto (aus /var/lib/waagent abgerufen) und die zugehörigen Daten.

	>[AZURE.NOTE]Beim Aufheben der Bereitstellung werden Dateien und Daten gelöscht, um das Image zu "verallgemeinern". Führen Sie diesen Befehl nur auf einem virtuellen Computer aus, den Sie als Image erfassen möchten. Dies garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden oder dass es für eine erneute Verteilung an Dritte genutzt werden kann.

3. Geben Sie **y** ein, um fortzufahren. Sie können den **-force**-Parameter hinzufügen, um diesen Bestätigungsschritt zu vermeiden.

4. Geben Sie **exit** ein, um den SSH-Client zu schließen.

	>[AZURE.NOTE]Bei den nächsten Schritten wird davon ausgegangen, dass Sie [die Azure-Befehlszeilenschnittstelle auf dem Clientcomputer installiert haben](../xplat-cli-install.md).

5. Öffnen Sie auf dem Clientcomputer die Azure-CLI, und melden Sie sich bei Ihrem Azure-Abonnement an. Weitere Informationen hierzu finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure-CLI](../xplat-cli-connect.md).

6. Stellen Sie sicher, dass Sie sich im Ressourcen-Manager-Modus befinden:

	`azure config mode arm`

7. Beenden Sie den bereits aufgehobenen virtuellen Computer mithilfe des folgenden Befehls:

	`azure vm stop –g <your-resource-group-name> -n <your-virtual-machine-name>`

8. Generalisieren Sie den virtuellen Computer mit dem folgenden Befehl:

	`azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>`

9. Erfassen Sie jetzt das Image und eine lokale Dateivorlage mit dem folgenden Befehl:

	`azure vm capture –g <your-resource-group-name> -n <your-virtual-machine-name> -p <your-vhd-name-prefix> -t <your-template-file-name.json>`

	Dieser Befehl erstellt ein generalisiertes Betriebssystem-Image mit dem VHD-Namenspräfix, das Sie für den VM-Datenträger angeben. Die Image-VHD-Dateien werden standardmäßig im selben Speicherkonto erstellt wie der ursprünglich verwendete virtuellen Computer. Mit der **-t** Option erstellen Sie eine lokale JSON-Dateivorlage, die Sie zum Erstellen eines neuen virtuellen Computers aus dem Image verwenden können.

>[AZURE.TIP]Um den Speicherort eines Images zu suchen, öffnen Sie die JSON-Vorlagendatei. Suchen Sie im **storageProfile** die **uri** des **images** im **system**-Container. Der Uri des Betriebssystemdatenträger-Images lautet ähnlich wie `https://clixxxxxxxxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/your-prefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.

## Bereitstellen eines neuen virtuellen Computers über das erfasste Image
Jetzt können Sie das Image mit einer Vorlage zum Erstellen eines neuen virtuellen Linux-Computers verwenden. Diese Schritte zeigen, wie Sie mit der Azure-CLI und der JSON-Dateivorlage, die Sie mit dem `azure vm capture`-Befehl erstellt haben, den virtuellen Computers in einem neuen virtuellen Netzwerk erstellen.

### Erstellen von Netzwerkressourcen

Um die Vorlage zu verwenden, müssen Sie zunächst ein virtuelles Netzwerk und eine NIC für Ihren neuen virtuellen Computer einrichten. Am besten erstellen Sie eine neue Ressourcengruppe für diese Ressourcen. Führen Sie Befehle ähnlich dem folgenden aus, indem Sie die Namen durch Ihre Ressourcen und einen entsprechenden Azure-Speicherort (in diesen Befehlen „centralus“) ersetzen:

	azure group create <your-new-resource-group-name> -l "centralus"

	azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"

	azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>

	azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"

	azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

Zum Bereitstellen eines virtuellen Computers aus dem Image mithilfe der während der Erfassung gespeicherten JSON-Datei benötigen Sie die ID der NIC. Diese erfahren Sie, indem Sie den folgenden Befehl ausführen.

	azure network nic show <your-new-resource-group-name> <your-nic-name>

Die **Id** in der Ausgabe ist eine ähnliche Zeichenfolge.

	/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>



### Erstellen einer neuen Bereitstellung
Führen Sie nun den folgenden Befehl aus, um Ihren virtuellen Computer aus dem erfassten VM-Image und der gespeicherten JSON-Vorlagendatei zu erstellen.

	azure group deployment create –g <your-new-resource-group-name> -n <your-new-deployment-name> -f <your-template-file-name.json>

Geben Sie bei der entsprechenden Aufforderung einen neuen Namen für den virtuellen Computer, den Admin-Benutzernamen und das Kennwort sowie die ID der Netzwerkkarte ein, die Sie zuvor erstellt haben.

	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	vmName: mynewvm
	adminUserName: myadminuser
	adminPassword: ********
	networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

Ist die Bereitstellung erfolgreich, sieht die Ausgabe etwa wie folgt aus.

	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "dlnewdeploy"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mynewdeploy
	data:    ResourceGroupName  : mynewrg
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
	data:    Mode               : Incremental
	data:    Name                Type          Value


	data:    ------------------  ------------  -------------------------------------

	data:    vmName              String        mynewvm


	data:    vmSize              String        Standard_D1


	data:    adminUserName       String        myadminuser


	data:    adminPassword       SecureString  undefined


	data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
	info:    group deployment create command OK

### Überprüfen der Bereitstellung

Stellen Sie über SSH eine Verbindung zum erstellen virtuellen Computer her, um die Bereitstellung zu prüfen und den neuen virtuellen Computer zu verwenden. Um eine Verbindung über SSH herzustellen, suchen Sie die IP-Adresse des erstellten virtuellen Computers, indem Sie folgenden Befehl ausführen:

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>

Die öffentliche IP-Adresse wird in der Befehlsausgabe aufgeführt. Die Verbindung zum virtuellen Linux-Computer erfolgt standardmäßig über SSH auf Port 22.

## Erstellen weiterer virtueller Computer mit der Vorlage

Verwenden Sie das erfasste Image und die Vorlage zum Bereitstellen zusätzlicher virtueller Computer mithilfe der im vorherigen Abschnitt beschriebenen Schritte.

* Stellen Sie sicher, dass sich das Image des virtuellen Computers im gleichen Speicherkonto wie die VHD Ihres virtuellen Computers befindet.
* Kopieren Sie die JSON-Dateivorlage, und geben Sie einen eindeutigen Wert für die **uri** jeder VHD Ihres virtuellen Computers ein.
* Erstellen einer neuen NIC im selben oder in einem anderen virtuellen Netzwerk
* Erstellen Sie eine Bereitstellung in der Ressourcengruppe, in der Sie das virtuelle Netzwerk mithilfe der JSON-Dateivorlage einrichten.

Wenn Sie möchten, dass das Netzwerk beim Erstellen eines virtuellen Computers automatisch eingerichtet wird, verwenden Sie die [101-vm-from-user-image-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) von GitHub. Diese Vorlage erstellt einen virtuellen Computer aus Ihrem benutzerdefinierten Image sowie das erforderliche virtuelle Netzwerk, die öffentliche IP-Adresse und die NIC-Ressourcen. Eine exemplarische Vorgehensweise zur Verwendung der Vorlage im Azure-Portal finden Sie unter [Gewusst wie: Erstellen eines virtuellen Computers von einem benutzerdefinierten Abbild mithilfe einer ARM-Vorlage](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

## Verwenden des „azure vm create“-Befehls

Sie sollten stets eine Ressourcen-Manager-Vorlage verwenden, um einen virtuellen Computer aus dem Image zu erstellen. Allerdings können Sie den virtuellen Computer _imperativ_ erstellen, indem Sie den **azure vm creat**-Befehl mit dem **--os-disk-vhd** (**-d**)-Parameter verwenden.

Führen Sie vor dem Ausführen von **azure vm create** mit dem Image folgende Schritte aus:

1.	Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe für die Bereitstellung aus.

2.	Erstellen Sie eine öffentliche IP-Adressressource und eine NIC-Ressource für den neuen virtuellen Computer. Informationen zum Erstellen eines virtuellen Netzwerks, einer öffentlichen IP-Adresse und NIC mithilfe der CLI finden Sie weiter oben in diesem Artikel. (**azure vm create** kann auch eine neue NIC erstellen, jedoch müssen Sie zusätzliche Parameter für ein virtuelles Netzwerk und Subnetz eingeben.)

3.	Stellen Sie sicher, dass Sie das VHD-Image in einen Blob-Container-Speicherort kopieren, das keine Ordner enthält (virtuelle Verzeichnisse). Standardmäßig befindet sich das erfasste Image in geschachtelten Ordnern in einem Speicher-Blob-Container (URI ähnlich wie `https://clixxxxxxxxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/your-prefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`. Mit dem **azure vm create**-Befehl können Sie einen virtuellen Computer nur aus einer VHD eines Betriebssystemdatenträgers auf der oberen Etage eines Blob-Containers erstellen. Beispielsweise könnten Sie die Image-VHD in `https://yourstorage.blob.core.windows.net/vhds/your-prefix-OsDisk.vhd` kopieren.

Führen Sie einen Befehl aus, der dem folgenden ähnelt:

	azure vm create -g <your-resource-group-name> -n <your-new-vm-name> -l eastus -y Linux -o <your-storage-account-name> -d "https://yourstorage.blob.core.windows.net/vhds/your-prefix-OsDisk.vhd" -z Standard_A1 -u <your-admin-name> -p <your-admin-password> -f <your-nic-name>

Führen Sie für zusätzliche Befehlsoptionen `azure help vm create` aus.

## Nächste Schritte

Informationen zur Verwaltung Ihrer virtuellen Computer mit der CLI finden Sie unter [Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und der Azure-CLI](virtual-machines-deploy-rmtemplates-azure-cli.md).

<!---HONumber=AcomDC_0121_2016-->