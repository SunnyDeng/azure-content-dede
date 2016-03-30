<properties
	pageTitle="Verschiedene Möglichkeiten zum Erstellen eines virtuellen Linux-Computers | Microsoft Azure"
	description="Listet die verschiedenen Möglichkeiten zum Erstellen eines virtuellen Linux-Computers in Azure auf und enthält Links zu weiteren Anweisungen."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/07/2016"
	ms.author="dkshir"/>

# Verschiedene Möglichkeiten zum Erstellen eines virtuellen Linux-Computers mit Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]klassisches Bereitstellungsmodell.

Um bei verschiedenen Benutzern und für verschiedene Zwecke möglichst gut zu passen, verfügt Azure über verschiedene Möglichkeiten, einen virtuellen Computer zu erstellen. Dieser Artikel fasst diese Unterschiede sowie die Entscheidungen, die Sie beim Erstellen Ihres virtuellen Linux-Computers treffen können, zusammen.

Azure-Ressourcen-Manager-Vorlagen wurden vor Kurzem als eine Möglichkeit eingeführt, um einen virtuellen Computer und dessen unterschiedlichen Ressourcen als eine logische Bereitstellungseinheit zu erstellen und zu verwalten. Sie finden nachfolgend Anweisungen für diesen Ansatz, sofern verfügbar. Weitere Informationen zum Azure-Ressourcen-Manager und zum Verwalten von Ressourcen als eine Einheit finden Sie in der [Übersicht](../resource-group-overview.md).

## Tool-Optionen

### GUI: Azure-Portal

Die grafische Benutzeroberfläche des [Azure-Portals](https://portal.azure.com) bietet eine einfache Möglichkeit zum Testen eines virtuellen Computers. Dies gilt insbesondere dann, wenn Sie noch nicht viel Erfahrung mit Azure haben. Verwenden Sie das Azure-Portal, um den virtuellen Computer zu erstellen:

* [Erstellen eines virtuellen Linux-Computers im Azure-Portal](virtual-machines-linux-portal-create.md) 

### Befehlsshell: Azure-Befehlszeilenschnittstelle 

Falls Sie die Arbeit in einer Befehlsshell bevorzugen, können Sie die Azure-Befehlszeilenschnittstelle für Mac-, Linux- und Windows-Benutzer verwenden.

Informationen zur Azure-Befehlszeilenschnittstelle finden Sie in diesen Tutorials:

* [Erstellen eines virtuellen Linux-Computers über die Befehlszeilenschnittstelle für Entwicklung und Tests](virtual-machines-linux-quick-create-cli.md) 

* [Erstellen einer geschützten Linux-VM mit einer Azure-Vorlage](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

## Betriebssystem und Image-Optionen

Wählen Sie ein Image basierend auf dem Betriebssystem, das Sie ausführen möchten. Azure und seine Partner bieten viele Images, von denen einige Anwendungen und Tools enthalten. Sie können auch ein eigenes Images verwenden.

### Azure-Images

In allen obigen Artikeln können Sie einfach ein vorhandenes Azure-Image verwenden, um einen virtuellen Computer zu erstellen und ihn für die Vernetzung, den Lastenausgleich oder andere Anforderungen anzupassen. Über das Portal ist der Azure Marketplace für unter Azure bereitgestellte Images verfügbar. Eine ähnliche Liste erhalten Sie unter Verwendung der Befehlszeile. Führen Sie beispielsweise `azure vm image list` in der Azure-Befehlszeilenschnittstelle aus, um eine Liste aller verfügbaren Images, geordnet nach Ort und Herausgeber, zu erhalten. Weitere Informationen finden Sie unter [Navigieren durch und Aussuchen von Images virtueller Azure-Computer mit der Azure-Befehlszeilenschnittstelle](virtual-machines-linux-cli-ps-findimage.md).

### Verwenden eines eigenen Image

Verwenden Sie ein Image, das auf einem vorhandenen virtuellen Azure-Computer basiert, indem Sie diesen virtuellen Computer *erfassen* oder ein eigenes, auf einer virtuellen Festplatte (Virtual Hard Disk, VHD) gespeichertes Image hochladen. Weitere Informationen finden Sie unter:

* [Von Azure unterstützte Distributionen](virtual-machines-linux-endorsed-distros.md)

* [Informationen zu nicht unterstützten Distributionen](virtual-machines-linux-create-upload-generic.md)

* [Erfassen eines virtuellen Linux-Computers, um ihn als Resource Manager-Vorlage zu verwenden](virtual-machines-linux-capture-image.md)

## Nächste Schritte

* Probieren Sie eines der Tutorials aus, um eine Linux-VM über das [Portal](virtual-machines-linux-portal-create.md), mit der [Befehlszeilenschnittstelle](virtual-machines-linux-quick-create-cli.md) oder mit einer Azure Resource Manager-[Vorlage](virtual-machines-linux-cli-deploy-templates.md) zu erstellen.

* Nach dem Erstellen einer Linux-VM können Sie leicht [einen Datenträger hinzufügen](virtual-machines-linux-add-disk.md).

<!---HONumber=AcomDC_0323_2016-->