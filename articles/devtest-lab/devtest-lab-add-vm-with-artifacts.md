    <properties
	pageTitle="Add a VM with artifacts to a DevTest Lab | Microsoft Azure"
	description="Create a new virtual machine with Artifacts in DevTest Lab."
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
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Hinzufügen einer VM mit Artefakten zu einem Azure DevTest Lab

## Übersicht

Sie erstellen einen virtuellen Computer in einem DevTest Lab aus einem Azure-Basisimage oder aus einem Image, das Sie in Ihr Lab hochgeladen haben.

Mit DevTest Lab-*Artefakten* können Sie Aktionen angeben, die ausgeführt werden, wenn die VM erstellt wird. Artefaktaktionen können Prozeduren durchführen, z. B. die Ausführung von PowerShell- und Bash-Befehlen, und Software installieren. Mit *Parametern* für Artefakte können Sie das Artefakt für ein bestimmtes Szenario anpassen.

Ihr Lab enthält Artefakte aus dem öffentlichen DevTest Lab-Artefaktrepository sowie Artefakte, die erstellt und Ihrem eigenen Artefaktrepository hinzugefügt wurden.

In diesem Artikel wird die Erstellung einer VM in Ihrem Lab mithilfe von Artefakten veranschaulicht.

## Hinzufügen einer VM mit Artefakten

1. Melden Sie sich beim [Azure-Vorschauportal](http://portal.azure.com) an.

1. Tippen Sie auf **Durchsuchen**, und tippen Sie dann in der Liste auf **DevTest Labs**.

1. Tippen Sie in der Liste der Labs auf das Lab, in dem Sie die neue VM erstellen möchten.

1. Tippen Sie auf dem Blatt des Labs auf **+ Lab-VM**, wie in der folgenden Abbildung dargestellt. ![DevTest Lab-Startblatt](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Geben Sie auf dem Blatt **Lab-VM** einen Namen für den neuen virtuellen Computer in das Textfeld **Name der Lab-VM** ein.

1. Tippen Sie auf **Basis/Erforderliche Einstellungen konfigurieren**, und wählen Sie ein Basisimage für die VM aus.

    ![Lab-VM-Einstellungen](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Nach der Auswahl eines Basisimages werden auf dem Blatt **Lab-VM** auch die Elemente **Benutzername** und **Kennwort** angezeigt. Geben Sie einen **Benutzernamen** ein, dem Administratorrechte auf dem virtuellen Computer erteilt werden.

    ![Erweitertes Lab-VM-Blatt](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Geben Sie ein **Kennwort** ein.

1. Tippen Sie auf **VM-Größe**, und wählen Sie eines der vordefinierten Elemente aus, mit denen die Prozessorkerne, die RAM-Größe und die Größe der Festplatte der zu erstellenden VM angegeben werden.

1. Tippen Sie auf **Artefakte**, und wählen Sie aus der Liste der Artefakte die Artefakte aus, die Sie dem Basisimage hinzufügen möchten, und konfigurieren Sie sie. **Hinweis:** Wenn Sie noch nicht mit DevTest Labs oder dem Konfigurieren von Artefakten vertraut sind, fahren Sie mit dem Abschnitt [Auswählen und Konfigurieren eines Artefakts](#configuring-an-artifact) fort, und kehren Sie dann hierher zurück.

1. Tippen Sie auf **Erstellen**, um die angegebene VM dem Lab hinzuzufügen.

1. Auf dem Blatt für das Lab wird der Status der VM-Erstellung angezeigt: erst als **Erstellung**, dann als **Wird ausgeführt**, nachdem die VM gestartet wurde. Tippen Sie zum Herstellen einer Verbindung mit der VM auf die VM und dann auf dem VM-Blatt auf **Verbinden**.

## Auswählen und Konfigurieren eines Artefakts

Beim Erstellen einer VM können Sie Artefakte hinzufügen, indem Sie auf dem Blatt **Lab-VM** auf **Artefakte** tippen. Dadurch wird das Blatt **Artefakte hinzufügen** angezeigt, auf dem Sie die Artefakte der VM aus dem offiziellen DevTest Lab-Repository (**offizielles Repository**) und Artefakte aus dem Teamrepository hinzufügen und konfigurieren können.

![Blatt „Artefakte hinzufügen“](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

**Hinzufügen eines Artefakts zu einer VM**

Gehen Sie für jedes Artefakt, das Sie zur VM hinzufügen möchten, wie folgt vor:

1. Tippen Sie auf dem Blatt **Artefakte hinzufügen** auf das gewünschte Artefakt, um ein Blatt anzuzeigen, auf dem Sie Artefaktparameter angeben können.  

2. Geben Sie die erforderlichen Parameterwerte und alle optionalen Parameter ein, die Sie benötigen.

3. Tippen Sie auf **Hinzufügen**, um das Artefakt hinzuzufügen, und kehren Sie zum Blatt **Artefakte hinzufügen** zurück.

**Ändern der Reihenfolge, in der Artefakte ausgeführt werden**

Wenn Sie Artefakte zur VM hinzufügen und konfigurieren, erscheint oben im Blatt **Artefakte hinzufügen** ein Link mit der aktuellen Anzahl der Artefakte. Standardmäßig werden die Aktionen der Artefakte in der Reihenfolge ausgeführt, in der sie der VM hinzugefügt wurden. Um die Reihenfolge, in der die Artefakte ausgeführt werden, zu ändern, ziehen Sie die Artefakte einfach in der Liste an die gewünschte Stelle, und tippen Sie abschließend auf **OK**.

**Anzeigen/Ändern ausgewählter Artefakte**

Gehen Sie zum Anzeigen oder Ändern der Parameter ausgewählter Artefakte wie folgt vor:

1. Tippen Sie oben auf dem Blatt **Artefakte hinzufügen** auf der Link, der angibt, wie viele Artefakte der VM hinzugefügt wurden.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Tippen Sie zum Anzeigen oder Bearbeiten der Parameter für ein bestimmtes Artefakt auf dem Blatt **Ausgewählte Artefakte** auf dieses Artefakt.

1. Nehmen Sie die erforderlichen Änderungen vor, und tippen Sie auf **OK**, um das Blatt **Artefakt hinzufügen** zu schließen.

1. Tippen Sie auf **OK**, um das Blatt **Ausgewählte Artefakte** zu schließen.

1. Tippen Sie auf **OK**, um das Blatt **Artefakte hinzufügen** zu schließen.

## Nächste Schritte

Erfahren Sie, wie Sie [benutzerdefinierte Artefakte für Ihre VM erstellen](devtest-lab-artifact-author.md).

<!---HONumber=Nov15_HO4-->