<properties
   pageTitle="Erstellen von Vorlagen mit Linux-VM-Erweiterungen | Microsoft Azure"
   description="Hier finden Sie Informationen zum Erstellen von Azure Resource Manager-Vorlagen mit Erweiterungen für virtuelle Linux-Computer."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Erstellen von Azure Resource Manager-Vorlagen mit Linux-VM-Erweiterungen

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Führen Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl aus:

      Azure VM extension list

Dieser Befehl gibt den Herausgebernamen, den Erweiterungsnamen und die Version zurück:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Diese drei Eigenschaften sind im oben dargestellten Vorlagenausschnitt „publisher“, „type“ und „typeHandlerVersion“ zugeordnet.

>[AZURE.NOTE]Es empfiehlt sich, die neueste Erweiterungsversion zu verwenden, um die neuesten Funktionen zu erhalten.

## Identifizieren des Schemas für die Erweiterungskonfigurationsparameter

Im nächsten Schritt zur Erstellung einer Erweiterungsvorlage wird das Format für die Bereitstellung von Konfigurationsparametern festgelegt. Jede Erweiterung unterstützt einen eigenen Satz von Parametern.

Eine Beispielkonfiguration für Linux-Erweiterungen finden Sie unter [Beispiele für Linux-Erweiterungen](virtual-machines-linux-extensions-configuration-samples.md).

Eine vollständige Vorlage mit VM-Erweiterungen finden Sie hier:

[Benutzerdefinierte Skripterweiterung auf einem virtuellen Linux-Computer](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Die erstellte Vorlage können Sie anschließend über die Azure-Befehlszeilenschnittstelle bereitstellen.

<!---HONumber=AcomDC_0323_2016-->