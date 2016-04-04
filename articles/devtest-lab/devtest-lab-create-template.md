    <properties
	pageTitle="Create a DevTest Lab custom image from a VHD file | Microsoft Azure"
	description="Learn how to create a custom image from a VHD file, which can then be used to create VMs in a DevTest Lab"
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
	ms.date="03/13/2016"
	ms.author="tarcher"/>

# Erstellen eines benutzerdefinierten DevTest Lab-Images von einer VHD-Datei

## Übersicht

Nachdem Sie [ein DevTest Lab erstellt](devtest-lab-create-lab.md) haben, können Sie [diesem Lab virtuelle Computer (VMs) hinzufügen](devtest-lab-add-vm-with-artifacts.md). Wenn Sie einen virtuellen Computer erstellen, geben Sie eine *Basis* an, was entweder ein *benutzerdefiniertes Image* oder *Marketplace-Image* sein kann. In diesem Artikel erfahren Sie, wie Sie ein benutzerdefiniertes Image aus einer VHD-Datei erstellen. Beachten Sie, dass Sie Zugriff auf eine gültige VHD-Datei benötigen, um alle Schritte in diesem Artikel auszuführen.

## Erstellen eines benutzerdefinierten Images

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Tippen Sie auf **Durchsuchen**, und tippen Sie dann in der Liste auf **DevTest Labs**.

1. Tippen Sie in der Liste der Labs auf das gewünschte Lab.

1. Das Blatt **Einstellungen** des ausgewählten Labs wird angezeigt.

1. Tippen Sie auf dem Blatt **Einstellungen** für das Lab auf **Benutzerdefinierte Images**.

    ![Option „Benutzerdefinierte Images“](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. Tippen Sie auf dem Blatt **Benutzerdefinierte Images** auf **+ benutzerdefiniertes Image**.

    ![Hinzufügen des benutzerdefinierten Images](./media/devtest-lab-create-template/add-custom-image.png)

1. Geben Sie den Namen des benutzerdefinierten Images ein. Dieser Name wird in der Liste der Basis-Images angezeigt, wenn Sie einen neuen virtuellen Computer erstellen.

1. Geben Sie die Beschreibung des benutzerdefinierten Images ein. Diese Beschreibung wird in der Liste der Basis-Images angezeigt, wenn Sie einen neuen virtuellen Computer erstellen.

1. Tippen Sie auf **VHD-Datei**.

1. Wenn Sie Zugriff auf eine VHD-Datei haben, die nicht aufgelistet ist, fügen Sie sie gemäß der Anweisungen im Abschnitt [VHD-Datei hochladen](#upload-a-vhd-file) hinzu, und kehren Sie nach Abschluss zu diesem Punkt zurück.

1. Wählen Sie die gewünschte VHD-Datei aus.

1. Tippen Sie auf **OK**, um das Blatt **VHD-Datei** zu schließen.

1. Tippen Sie auf **Betriebssystemkonfiguration**.

1. Wählen Sie auf der Registerkarte **Betriebssystemkonfiguration** eine der Optionen **Windows** oder **Linux** aus.

1. Wenn Sie **Windows** ausgewählt haben, geben Sie über das Kontrollkästchen an, ob *Sysprep* auf dem Computer ausgeführt wurde.

1. Tippen Sie auf **OK**, um das Blatt **Betriebssystemkonfiguration** zu schließen.

1. Tippen Sie auf **OK**, um das benutzerdefinierte Image zu erstellen.

1. Gehen Sie zum Abschnitt [Nächste Schritte](#next-steps).

##Hochladen einer VHD-Datei

Um ein neues benutzerdefiniertes Image hinzuzufügen, benötigen Sie Zugriff auf eine VHD-Datei.

1. Tippen Sie auf dem Blatt **VHD-Datei** auf **Hochladen einer VHD-Datei mit PowerShell**.

    ![Image hochladen](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. Auf dem nächsten Blatt wird eine Anleitung zum Ändern und Ausführen eines PowerShell-Skripts angezeigt, das eine VHD-Datei in Ihr Azure-Abonnement hochlädt. **Hinweis**: Dieser Vorgang kann abhängig von der Größe der VHD-Datei und der Geschwindigkeit Ihrer Verbindung lange dauern.

##Nächste Schritte

Nachdem Sie ein benutzerdefiniertes Image zum Erstellen eines virtuellen Computers hinzugefügt haben, besteht der nächste Schritt darin, [Ihrem DevTest Lab einen virtuellen Computer hinzuzufügen](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0323_2016-->