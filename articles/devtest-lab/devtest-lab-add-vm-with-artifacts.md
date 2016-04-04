<properties
	pageTitle="Hinzufügen einer VM mit Artefakten zu einem DevTest Lab | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine VM mit Artefakten einem DevTest Lab hinzufügen."
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
	ms.date="03/21/2016"
	ms.author="tarcher"/>

# Hinzufügen einer VM mit Artefakten zu einem DevTest Lab

> [AZURE.NOTE] Klicken Sie auf den folgenden Link, um das Video zu diesem Artikel anzuzeigen: [Gewusst wie: Erstellen von VMs mit Artefakten in einem DevTest Lab](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab).

## Übersicht

Sie erstellen eine VM in einem DevTest Lab aus einem Basisimage, das entweder ein [benutzerdefiniertes Image](./devtest-lab-create-template.md) oder ein Marketplace-Image ist.

Mit DevTest Lab-*Artefakten* können Sie *Aktionen* angeben, die ausgeführt werden, wenn die VM erstellt wird.

Artefaktaktionen können Prozeduren ausführen, z.B. die Ausführung von Windows PowerShell-Skripts und Bash-Befehlen und eine Softwareinstallation.

Mit *Parametern* für Artefakte können Sie das Artefakt für ein bestimmtes Szenario anpassen.

In diesem Artikel wird die Erstellung eines virtuellen Computers in Ihrem Lab mithilfe von Artefakten veranschaulicht.

## Hinzufügen einer VM mit Artefakten

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Tippen Sie auf **Durchsuchen**, und tippen Sie dann in der Liste auf **DevTest Labs**.

1. Tippen Sie in der Liste der Labs auf das Lab, in dem Sie den neuen virtuellen Computer erstellen möchten.

1. Tippen Sie auf dem Blatt des Labs auf **+ Lab-VM**, wie in der folgenden Abbildung dargestellt. ![DevTest Lab-Startblatt](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Geben Sie auf dem Blatt **Lab-VM** einen Namen für den neuen virtuellen Computer in das Textfeld **Name der Lab-VM** ein.

1. Tippen Sie auf **Basis/Erforderliche Einstellungen konfigurieren**, und wählen Sie ein Basisimage für die VM aus.

    ![Lab-VM-Einstellungen](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Nach dem Auswählen eines Basisimages und Tippen auf **OK** wird das Blatt **Lab-VM** erweitert, um Benutzeroberflächen-Elemente für die Angabe von Informationen zu Benutzerkonten einschließlich **Benutzername**, **Authentifizierungstyp** (wenn Linux das Betriebssystem für die ausgewählte Basis ist) und **Kennwort** (unter Voraussetzung des Authentifizierungstyps *Kennwort*) einzubeziehen.

    ![Erweitertes Lab-VM-Blatt](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Geben Sie einen **Benutzernamen** ein, dem Administratorrechte auf dem virtuellen Computer erteilt werden.

1. Wenn das Betriebssystem für die ausgewählte Basis Linux ist, geben Sie entweder *Kennwort* oder *Öffentlicher SSH-Schlüssel* als Authentifizierungstyp ein.

1. Geben Sie je nach angegebenem Authentifizierungstyp ein Kennwort oder einen öffentlichen SSH-Schlüssel ein.

1. Tippen Sie auf **VM-Größe**, und wählen Sie eines der vordefinierten Elemente aus, mit denen die Prozessorkerne, die RAM-Größe und die Größe der Festplatte der zu erstellenden VM angegeben werden.

1. Tippen Sie auf **Virtuelles Netzwerk**, und wählen Sie das gewünschte virtuelle Netzwerk aus.

1. Tippen Sie auf **Subnetz**, und wählen Sie das Subnetz.

1. Wenn die Lab-Richtlinie öffentliche IP-Adressen für das ausgewählte Subnetz zulässt, geben Sie durch Auswahl von **Ja** oder **Nein** an, ob die IP-Adresse öffentlich sein soll. Andernfalls ist diese Option deaktiviert und als **Nein** ausgewählt.

1. Tippen Sie auf **Artefakte**, und wählen Sie aus der Liste der Artefakte die Artefakte aus, die Sie dem Basisimage hinzufügen möchten, und konfigurieren Sie sie. **Hinweis:** Wenn Sie noch nicht mit DevTest Labs oder dem Konfigurieren von Artefakten vertraut sind, fahren Sie mit dem Abschnitt [Hinzufügen eines vorhandenen Artefakts zu einer VM](#add-an-existing-artifact-to-a-vm) fort, und kehren Sie dann hierher zurück.

1. Tippen Sie auf **Erstellen**, um die angegebene VM dem Lab hinzuzufügen.

1. Auf dem Blatt für das Lab wird der Status der VM-Erstellung angezeigt: erst als **Erstellung**, dann als **Wird ausgeführt**, nachdem die VM gestartet wurde.

1. Gehen Sie zum Abschnitt [Nächste Schritte](#next-steps).

## Hinzufügen eines vorhandenen Artefakts zu einer VM

Beim Erstellen eines virtuellen Computers können Sie vorhandene Artefakte hinzufügen. Jedes Lab enthält Artefakte aus dem öffentlichen DevTest Lab-Artefaktrepository, sowie Artefakte, die Sie erstellt und Ihrem eigenen Artefaktrepository hinzugefügt haben. Informationen zum Erstellen von Artefakten finden Sie im Artikel [Erstellen von benutzerdefinierten Artefakten für Ihre DevTest Lab-VM](devtest-lab-artifact-author.md).

1. Tippen Sie auf dem Blatt **Lab-VM** auf **Artefakte**. 

1. Tippen Sie auf dem Blatt **Artefakte hinzufügen** auf das gewünschte Artefakt.

![Blatt „Artefakte hinzufügen“](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Geben Sie die erforderlichen Parameterwerte und alle optionalen Parameter ein, die Sie benötigen.  

1. Tippen Sie auf **Hinzufügen**, um das Artefakt hinzuzufügen, und kehren Sie zum Blatt **Artefakte hinzufügen** zurück.

1. Fügen Sie weiterhin nach Bedarf für Ihren virtuellen Computer Artefakte hinzu.

1. Sobald Sie Ihre Artefakte hinzugefügt haben, können Sie [die Reihenfolge ändern, in der die Artefakte ausgeführt werden](#change-the-order-in-which-artifacts-are-run). Sie können auch zum [Anzeigen oder Ändern eines Artefakts](#view-or-modify-an-artifact) zurückgehen.

## Ändern der Reihenfolge, in der Artefakte ausgeführt werden

Standardmäßig werden die Aktionen der Artefakte in der Reihenfolge ausgeführt, in der sie der VM hinzugefügt wurden. Die folgenden Schritte veranschaulichen, wie Sie die Reihenfolge ändern, in der die Artefakte ausgeführt werden.

1. Tippen Sie oben auf dem Blatt **Artefakte hinzufügen** auf den Link, der die Zahl der Artefakte angibt, die der VM hinzugefügt wurden.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Positionieren Sie die Artefakte in der Liste mit Drag & Drop so, dass sie die gewünschte Reihenfolge widerspiegeln. **Hinweis:** Wenn beim Ziehen eines Artefakts Probleme auftreten, stellen Sie sicher, dass Sie von der linken Seite des Artefakts aus ziehen.

1. Tippen Sie nach Abschluss auf **OK**.

## Anzeigen oder Ändern eines Artefakts

Die folgenden Schritte veranschaulichen das Anzeigen oder Ändern der Parameter eines Artefakts:

1. Tippen Sie oben auf dem Blatt **Artefakte hinzufügen** auf den Link, der die Zahl der Artefakte angibt, die der VM hinzugefügt wurden.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Tippen Sie oben auf dem Blatt **Ausgewählte Artefakte** auf das Artefakt, das Sie anzeigen oder bearbeiten möchten.

1. Nehmen Sie auf dem Blatt **Artefakt hinzufügen** die erforderlichen Änderungen vor, und tippen Sie auf **OK**, um das Blatt **Artefakt hinzufügen** zu schließen.

1. Tippen Sie auf **OK**, um das Blatt **Ausgewählte Artefakte** zu schließen.

## Nächste Schritte

- Sobald die VM erstellt ist, können Sie sich durch Tippen auf **Verbinden** auf dem Blatt der VM mit der VM verbinden.
- Informationen zum Erstellen von Artefakten finden Sie im Artikel [Erstellen von benutzerdefinierten Artefakten für Ihre DevTest Lab-VM](devtest-lab-artifact-author.md).

<!---HONumber=AcomDC_0323_2016-->