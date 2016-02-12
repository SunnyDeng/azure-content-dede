<properties
	pageTitle="Definieren von Labrichtlinien | Microsoft Azure"
	description="Erfahren Sie, wie Sie Labrichtlinien wie VM-Größen, die maximale Anzahl von VMs pro Benutzer und das automatisierte Herunterfahren definieren."
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
	ms.date="02/03/2016"
	ms.author="tarcher"/>

# Definieren von Labrichtlinien.

## Übersicht

Mit DevTest Lab können Sie Richtlinien für Schlüssel angeben, die die Verwendung Ihres Labs und seiner virtuellen Computer steuern. Diese Richtlinien schließen Kostenschwellenwerte, zulässige VM-Größen, maximale Anzahl von VMs pro Benutzer und Auto-Shutdown für die VMs im Lab ein.

## Zugreifen auf die Richtlinien eines Labs

Um die Richtlinien für ein Lab anzeigen und ändern zu können, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

1. Tippen Sie auf **Durchsuchen**, und tippen Sie dann in der Liste auf **DevTest Labs**.

1. Tippen Sie in der Liste der Labs auf das gewünschte Lab.

1. Tippen Sie auf **Einstellungen**.

	![Einstellungen](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. Auf dem Blatt **Einstellungen** wird eine Gruppierung von Einstellungen mit dem Namen **Richtlinien** angezeigt.

	![Einstellungen](./media/devtest-lab-set-lab-policy/policies.png)

	Tippen Sie in der Liste unten auf die gewünschte Richtlinie, um weitere Informationen zum Festlegen zu erhalten:

	- Kostenschwellenwerte: Diese Richtlinie wird derzeit nicht unterstützt.

	- [Allowed VM Sizes](#set-allowed-vm-sizes): Wählen Sie die Liste der im Lab zulässigen VM-Größen aus. Ein Benutzer kann nur aus dieser Liste virtuelle Computer erstellen.

	- [Maximum VMs](#set-maximum-vms): Geben Sie die maximale Anzahl von virtuellen Computern an, die für ein Lab erstellt werden können, sowie die maximale Anzahl von virtuellen Computern, die von einem Benutzer erstellt werden können.

	- [Auto shutdown](#set-auto-shutdown): Geben Sie den Zeitpunkt an, zu dem die virtuellen Computer des aktuellen Labs heruntergefahren werden müssen.

## Festlegen von zulässigen VM-Größen

Die Richtlinie für die zulässigen VM-Größen hilft dabei, unnötigen Aufwand im Lab zu minimieren, indem sie Ihnen ermöglicht, die im Lab zulässigen VM-Größen anzugeben. Wenn diese Richtlinie aktiviert ist, können nur VM-Größen aus dieser Liste zum Erstellen von virtuellen Computern verwendet werden.

1. Tippen Sie auf dem Blatt **Einstellungen** für das Lab unter **Richtlinien** auf **Allowed VM Sizes**.

	![Einstellungen](./media/devtest-lab-set-lab-policy/allowed-vm-sizes-policy.png)
 
1. Tippen Sie auf **Ein**, um diese Richtlinie zu aktivieren, und auf **Aus**, um sie zu deaktivieren.

1. Wenn Sie diese Richtlinie aktiviert haben, tippen Sie auf eine oder mehrere VM-Größen, die im Testlabor erstellt werden können.

1. Tippen Sie auf **Speichern**.

## Festlegen der maximalen Anzahl von VMs

Mithilfe der Richtlinie für die maximale Anzahl von VMs können Sie die maximale Anzahl von virtuellen Computern angeben, die für das derzeitige Lab erstellt werden können, sowie die maximale Anzahl von virtuellen Computern, die von einem Benutzer erstellt werden können. Wenn ein Benutzer versucht, einen neuen virtuellen Computer zu erstellen, und die Benutzergrenze oder die Labgrenze erreicht wurde, wird in einer Fehlermeldung darauf hingewiesen, dass der virtuelle Computer nicht erstellt werden kann.

1. Tippen Sie auf dem Blatt **Einstellungen** für das Lab unter **Richtlinien** auf **Maximum VMs**.

	![Einstellungen](./media/devtest-lab-set-lab-policy/max-vms-policies.png)

1. Gehen Sie im Abschnitt **Policy per User** folgendermaßen vor:
 
	1. Tippen Sie auf **Ein**, um diese Richtlinie zu aktivieren, und auf **Aus**, um sie zu deaktivieren.
	
	1. Wenn Sie diese Richtlinie aktiviert haben, geben Sie im Textfeld **Maximum VMs allowed per User** einen numerischen Wert für die maximale Anzahl von virtuellen Computern ein, die von einem Benutzer erstellt werden können. Wenn Sie eine ungültige Zahl eingeben, zeigt die Benutzeroberfläche die maximal zulässige Zahl für dieses Feld an.

1. Gehen Sie im Abschnitt **Policy per Lab** folgendermaßen vor:
 
	1. Tippen Sie auf **Ein**, um diese Richtlinie zu aktivieren, und auf **Aus**, um sie zu deaktivieren.
	
	1. Wenn Sie diese Richtlinie aktiviert haben, geben Sie im Textfeld **Maximum VMs allowed in this lab** einen numerischen Wert für die maximale Anzahl von virtuellen Computern ein, die für das derzeitige Lab erstellt werden können. Wenn Sie eine ungültige Zahl eingeben, zeigt die Benutzeroberfläche die maximal zulässige Zahl für dieses Feld an.

1. Tippen Sie auf **Speichern**.

## Festlegen des Auto-Shutdowns

Die Auto-Shutdown-Richtlinie hilft dabei, unnötigen Aufwand im Lab zu minimieren, indem sie Ihnen ermöglicht, die Uhrzeit anzugeben, zu der die virtuellen Computer für dieses Lab heruntergefahren werden.

1. Tippen Sie auf dem Blatt **Einstellungen** für das Lab unter **Richtlinien** auf **Auto Shutdown**.

	![Einstellungen](./media/devtest-lab-set-lab-policy/auto-shutdown-policy.png)

1. Tippen Sie auf **Ein**, um diese Richtlinie zu aktivieren, und auf **Aus**, um sie zu deaktivieren.

1. Wenn Sie diese Richtlinie aktiviert haben, geben Sie eine Uhrzeit an, zu der alle virtuellen Computer im derzeitigen Lab heruntergefahren werden sollen.

1. Tippen Sie auf **Speichern**.

<!---HONumber=AcomDC_0204_2016-->