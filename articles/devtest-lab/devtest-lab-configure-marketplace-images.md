<properties
	pageTitle="Konfigurieren von Azure Marketplace-Image-Einstellungen in einem DevTest Lab | Microsoft Azure"
	description="Konfigurieren, welche Azure Marketplace-Images beim Erstellen eines virtuellen Computers in einem DevTest Lab verwendet werden können"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="tarcher"/>

# Konfigurieren von Azure Marketplace-Image-Einstellungen in einem DevTest Lab

## Übersicht

DevTest Labs unterstützt die Erstellung neuer VMs auf Basis von Azure Marketplace-Images, abhängig davon, wie Sie Azure Marketplace-Images zur Verwendung in Ihrem Lab konfiguriert haben. In diesem Artikel erfahren Sie, wie Sie ggf. angeben, welche Azure Marketplace-Images zum Erstellen neuer virtueller VMs in einem DevTest Lab verwendet werden können.

## Auswählen, welche Azure Marketplace-Images beim Erstellen eines virtuellen Computers zulässig sind

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Tippen Sie auf **Durchsuchen**, und tippen Sie dann in der Liste auf **DevTest Labs**.

1. Tippen Sie in der Liste der Labs auf das gewünschte Lab.

1. Das Blatt **Einstellungen** des ausgewählten Labs wird angezeigt.

1. Tippen Sie auf dem Blatt **Einstellungen** auf **Marketplace-Images**.

	![Konfigurieren, wie Azure Marketplace-Images verwendet werden](./media/devtest-lab-configure-marketplace-images/lab-settings-marketplace-images.png)

1. Geben Sie an, ob alle qualifizierten Azure Marketplace-Images für die Verwendung als Basis einer neuen VM verfügbar sein sollen. Bei Auswahl von **Ja** sind alle Azure Marketplace-Images im Lab zulässig, die alle folgenden Kriterien erfüllen:

	- Das Image erstellt einen einzelnen virtuellen Computer, **und**
	- Das Image verwendet den Azure-Resource-Manager zur Bereitstellung von VMs, **und**
	- Das Image erfordert nicht den Erwerb eines zusätzlichen Lizenzplans.
	
	Wenn keine Images zulässig sein sollen, oder Sie angeben möchten, welche Bilder verwendet werden können, wählen Sie **Nein**.
 
	![Option, die Verwendung aller Marketplace-Images als Basis-Images für virtuelle Computer zuzulassen](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
 
1. Bei Auswahl von **Nein** im vorherigen Schritt ist das Kontrollkästchen **Zulässige Images/Alle auswählen** aktiviert. Sie können diese Option zusammen mit dem Suchfeld verwenden, um schnell alle in der Liste angezeigten Elemente auszuwählen oder ihre Auswahl aufzuheben. Sie können auch die Azure Marketplace-Images einzeln auswählen, die Sie für die Erstellung des virtuellen Computers zulassen möchten, indem Sie das Kontrollkästchen jedes entsprechenden Images aktivieren. Wählen Sie nichts aus der Liste, wenn keine Azure Marketplace-Images im Lab verwendet werden sollen.

	![Sie können angeben, welche Azure Marketplace-Images als Basis-Images für virtuelle Computer verwendet werden können.](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

## Nächste Schritte

Nachdem Sie konfiguriert haben, wie Azure Marketplace-Images beim Erstellen eines virtuellen Computers zulässig sind, ist der nächste Schritt das [Hinzufügen eines virtuellen Computers zu Ihrem DevTest Lab](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0316_2016-->