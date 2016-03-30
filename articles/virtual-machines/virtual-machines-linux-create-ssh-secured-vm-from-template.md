<properties
	pageTitle="Erstellen einer geschützten Linux-VM mit einer Azure-Vorlage | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine geschützte Linux-VM unter Azure mit einer Azure Resource Manager-Vorlage erstellen."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/08/2016"
	ms.author="vlivech"/>

# Erstellen einer geschützten Linux-VM mit einer Azure-Vorlage

Mit Vorlagen können Sie VMs unter Azure mit Einstellungen erstellen, die Sie während des Startprozesses anpassen möchten, z.B. Benutzer- und Hostnamen. In diesem Artikel geht es um das Starten einer Ubuntu-VM mit einer Azure-Vorlage, bei der eine Netzwerksicherheitsgruppe (NSG) mit nur einem geöffneten Port (22 für SSH) erstellt wird und für die zur Anmeldung SSH-Schlüssel benötigt werden.

Bei Azure Resource Manager-Vorlagen handelt es sich um JSON-Dateien, die für einfache einmalige Aufgaben verwendet werden können, z.B. das Starten einer Ubuntu-VM wie in diesem Artikel. Sie können auch zum Erstellen komplexer Azure-Konfigurationen für ganze Umgebungen verwendet werden, z.B. eine Test-, Entwicklungs- oder Produktionsbereitstellung vom Netzwerk über das Betriebssystem bis zur Bereitstellung des Anwendungsstapels.

## Zielsetzung

- Erstellen einer per SSH geschützten Linux-VM in Azure mit einer Azure-Vorlage

## Voraussetzungen

- Sie benötigen ein Azure-Konto ([kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/)), eine [Datei mit einem öffentlichen SSH-Schlüssel](virtual-machines-linux-mac-create-ssh-keys.md) und die [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md). Versetzen Sie die Befehlszeilenschnittstelle dann in den Ressourcenmodus, indem Sie `azure config mode arm` eingeben. Melden Sie sich anschließend mit der Befehlszeilenschnittstelle an Azure an, indem Sie `azure login` eingeben.

## Kurze Zusammenfassung der Befehle

Für diese Bereitstellung wird nur ein Befehl benötigt. Außerdem müssen Sie eine Option auswählen, um anzugeben, wo sich die Azure Resource Manager-Vorlage befindet. In diesem Thema wird eine Vorlage direkt aus der Azure-Schnellstartvorlage aus dem GitHub-Repository als Beispiel verwendet. Mit den verschiedenen Optionen unten wird lediglich angegeben, wo sich die Vorlage und die Parameterdateien befinden.

1. `azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]`

## Erstellen der Linux-VM

Im folgenden Codebeispiel wird veranschaulicht, wie Sie `azure group create` aufrufen, um eine Ressourcengruppe zu erstellen, und gleichzeitig eine per SSH geschützte Linux-VM bereitstellen, indem Sie [diese Azure Resource Manager-Vorlage](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) verwenden. Beachten Sie, dass Sie in Ihrem Beispiel Namen verwenden müssen, die für Ihre Umgebung eindeutig sind. In diesem Beispiel werden `quicksecuretemplate` als Ressourcengruppenname, `securelinux` als VM-Name und `quicksecurelinux` als Name einer Unterdomäne verwendet.

```bash
azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## Ausführliche exemplarische Vorgehensweise

Sie können eine neue Ressourcengruppe erstellen und eine VM bereitstellen, indem Sie den Parameter `--template-uri` verwenden. Außerdem können Sie eine Vorlage herunterladen oder lokal erstellen und die Vorlage mit dem Parameter `--template-file` und einem Pfad zur Vorlagendatei als Argument übergeben. Sie werden von der Azure-Befehlszeilenschnittstelle aufgefordert, die für die Vorlage erforderlichen Parameter anzugeben.

## Nächste Schritte

Nachdem Sie Linux-VMs mit Vorlagen erstellt haben, können Sie sich darüber informieren, welche anderen App-Frameworks für die Verwendung mit Vorlagen verfügbar sind. Im [Katalog mit den Vorlagen](https://azure.microsoft.com/documentation/templates/) können Sie weitere App-Frameworks für die Bereitstellung auswählen.

<!---HONumber=AcomDC_0323_2016-->