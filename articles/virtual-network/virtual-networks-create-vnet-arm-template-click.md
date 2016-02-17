<properties
   pageTitle="Erstellen eines virtuellen Netzwerks mit einer ARM-Vorlage | Microsoft Azure"
   description="Erfahren Sie, wie Sie ein virtuelles Netzwerk mit einer ARM-Vorlage erstellen | Ressourcen-Manager."
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos"/>

# Erstellen eines virtuellen Netzwerks mit einer ARM-Vorlage

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]In diesem Dokument wird beschrieben, wie ein virtuelles Netzwerk (VNet) mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wird. Sie haben auch die Möglichkeit, [ein virtuelles Netzwerks im klassischen Bereitstellungsmodell zu erstellen](virtual-networks-create-vnet-classic-pportal.md).

Sie erfahren, wie Sie eine vorhandene ARM-Vorlage von GitHub herunterladen und ändern sowie die Vorlage aus GitHub, PowerShell und der Azure-Befehlszeilenschnittstelle bereitstellen.

Wenn Sie die ARM-Vorlage, ohne sie zu ändern, einfach direkt aus GitHub bereitstellen, springen Sie zu [Bereitstellen einer Vorlage aus Github](#deploy-the-arm-template-by-using-click-to-deploy).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]

<!---HONumber=AcomDC_0211_2016-->