    <properties
	pageTitle="Create VM templates | Microsoft Azure"
	description="Learn how to create VM templates from VHD images"
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
	ms.date="01/30/2016"
	ms.author="tarcher"/>

# Erstellen von VM-Vorlagen

## Übersicht

Sobald Sie [ein Lab erstellt](devtest-lab-create-lab.md) haben, können Sie [diesem aus einer Liste von VM-Vorlagen virtuelle Computer hinzufügen](devtest-lab-add-vm-with-artifacts.md). In diesem Artikel erfahren Sie, wie Sie eine Imagedatei für eine virtuelle Festplatte (VHD) als Vorlage für das Erstellen virtueller Computer hochladen und konfigurieren. Wenn Sie mit VHD-Images nicht vertraut sind, erfahren Sie im Artikel [Erstellen und Hochladen einer Windows Server-VHD nach Azure](/virtual-machines/virtual-machines-create-upload-vhd-windows-server.md), wie VHD-Images erstellt werden. Nachdem Sie ein VHD-Image erstellt oder Zugriff auf dieses erhalten haben, führt dieser Artikel Sie durch das Hochladen des Images und das Erstellen einer Vorlage aus diesem.

## Erstellen einer VM-Vorlage

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

1. Tippen Sie auf **Durchsuchen**, und tippen Sie dann in der Liste auf **DevTest Labs**.

1. Tippen Sie in der Liste der Labs auf das gewünschte Lab.

1. Tippen Sie auf dem Blatt des Labs auf **Einstellungen**.

    ![Labeinstellungen](./media/devtest-lab-create-template/lab-blade-settings.png)

1. Tippen Sie auf dem Blatt **Einstellungen** für das Lab auf **Vorlagen**.

    ![Option „Vorlagen“](./media/devtest-lab-create-template/lab-blade-settings-templates.png)

1. Tippen Sie auf dem Blatt **Vorlagen** auf **+ Vorlage**.

    ![Vorlage hinzufügen](./media/devtest-lab-create-template/add-template.png)

1. Gehen Sie auf dem Blatt **Vorlage hinzufügen** folgendermaßen vor:

	1. Geben Sie den Namen der Vorlage ein. Dieser Name wird in der Liste der Vorlagen angezeigt, wenn Sie einen neuen virtuellen Computer erstellen.

	1. Geben Sie die Beschreibung der Vorlage ein. Diese Beschreibung wird in der Liste der Vorlagen angezeigt, wenn Sie einen neuen virtuellen Computer erstellen.

	1. Tippen Sie auf **Image**.

	1. Wenn das gewünschte Image nicht aufgeführt ist und Sie es hinzufügen möchten, fahren Sie mit dem Abschnitt [Hinzufügen eines neuen Vorlagenimages](#add-a-new-template-image) fort, und kehren Sie anschließend hierher zurück.

	1. Wählen Sie das gewünschte Image aus.

	1. Tippen Sie auf **OK**, um das Blatt **Vorlage hinzufügen** zu schließen.

1. Tippen Sie auf **Betriebssystemkonfiguration**.

1. Wählen Sie auf der Registerkarte **Betriebssystemkonfiguration** eine der Optionen **Windows** oder **Linux** aus.

1. Wenn Sie **Windows** ausgewählt haben, geben Sie über das Kontrollkästchen an, ob *Sysprep* auf dem Computer ausgeführt wurde.

1. Geben Sie einen **Benutzernamen** für den Computer ein.

1. Geben Sie ein **Kennwort** für den Computer ein. **Hinweis**: Das Kennwort wird als Klartext angezeigt.

1. Tippen Sie auf **OK**, um das Blatt **Betriebssystemkonfiguration** zu schließen.

1. Geben Sie den **Speicherort** an.

1. Tippen Sie auf **OK**, um die Vorlage zu erstellen.

##Hinzufügen eines neuen Vorlagenimages

Wenn Sie ein neues Vorlagenimage hinzufügen möchten, benötigen Sie Zugriff auf eine VHD-Imagedatei.

1. Tippen Sie auf dem Blatt **Add Template Image** auf **Upload an image using PowerShell**.

    ![Image hochladen](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. Auf dem nächsten Blatt wird eine Anleitung zum Ändern und Ausführen eines PowerShell-Skripts angezeigt, das eine VHD-Imagedatei in Ihr Azure-Abonnement hochlädt. **Hinweis**: Dieser Vorgang kann abhängig von der Größe der Imagedatei und der Geschwindigkeit Ihrer Verbindung lange dauern.

##Nächste Schritte

Nachdem Sie eine VM-Vorlage zum Erstellen eines virtuellen Computers hinzugefügt haben, besteht der nächste Schritt darin, [Ihrem DevTest Lab einen virtuellen Computer hinzuzufügen](devtest-lab-add-vm-with-artifacts).

<!---HONumber=AcomDC_0204_2016-->