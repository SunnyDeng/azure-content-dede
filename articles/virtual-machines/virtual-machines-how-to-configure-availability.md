<properties
	pageTitle="Konfigurieren einer Verfügbarkeitsgruppe für virtuelle Computer | Microsoft Azure"
	description="Konfigurieren Sie eine Verfügbarkeitsgruppe für einen neuen oder vorhandenen virtuellen Computer im klassischen Bereitstellungsmodell mithilfe des klassischen Azure-Portals und mit Azure PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="cynthn"/>

# Konfigurieren einer Verfügbarkeitsgruppe für virtuelle Computer im klassischen Bereitstellungsmodell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Eine Verfügbarkeitsgruppe hilft, dass Ihre virtuellen Computer während eines Ausfalls wie während einer Wartung verfügbar bleiben. Das Platzieren von mindestens zwei ähnlich konfigurierten virtuellen Computern in einer Verfügbarkeitsgruppe erstellt die Redundanz, die zum Wahren der Verfügbarkeit der Anwendungen oder Dienste erforderlich ist, die auf Ihrem virtuellen Computer ausgeführt werden. Ausführliche Informationen hierzu finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer][].

Sie sollten eine Kombination aus Verfügbarkeitssätzen und Endpunkten mit Lastenausgleich verwenden, um sicherzustellen, dass Ihre Anwendung stets verfügbar ist und effizient ausgeführt wird. Details über Endpunkte mit Lastenausgleich finden Sie unter [Lastenausgleich für Azure-Infrastrukturdienste][].

Mithilfe einer der beiden folgenden Optionen können Sie einen virtuellen Computer in eine Verfügbarkeitsgruppe versetzen:

- [Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe][]. Fügen Sie der Gruppe anschließend die neuen virtuellen Computer hinzu, wenn Sie diese virtuellen Computer erstellen.
- [Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu][].

>[AZURE.NOTE]Virtuelle Computer, die Sie in dieselbe Verfügbarkeitsgruppe versetzen möchten, müssen zum selben Clouddienst gehören.

## <a id="createset"> </a>Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe##

Dafür können Sie entweder das klassische Azure-Portal oder die Azure PowerShell-Befehle verwenden.

Verwenden des klassischen Azure-Portals:

1. Melden Sie sich beim klassischen Azure-Portal an, falls noch nicht geschehen.

2. Klicken Sie in der Befehlsleiste auf **Neu**.

3. Klicken Sie auf **Virtueller Computer** und dann auf **Aus Katalog**.

4. Verwenden Sie die ersten beiden Bildschirme zum Auswählen eines Images, eines Benutzernamens, eines Kennworts usw. Weitere Details finden Sie unter [Erstellen eines virtuellen Windows-Computers][].

5. Im dritten Bildschirm können Sie die Ressourcen für Netzwerk, Speicher und Verfügbarkeit konfigurieren. Gehen Sie wie folgt vor:

	1. Wählen Sie den entsprechenden Clouddienst. Lassen Sie die Option **Einen neuen Clouddienst erstellen** ausgewählt (es sei denn, Sie fügen diesen neuen virtuellen Computer einem vorhandenen VM-Clouddienst hinzu). Geben Sie dann unter **DNS-Name des Clouddiensts** einen Namen ein. Der DNS-Name wird ein Teil des URI, der zum Kontaktieren des virtuellen Computers verwendet wird. Der Clouddienst fungiert als eine Kommunikations- und Isolationsgruppe. Alle virtuellen Computer im selben Clouddienst können miteinander kommunizieren, für den Lastenausgleich eingerichtet werden und in derselben Verfügbarkeitsgruppe platziert werden.

	2. Geben Sie unter **Region/Affinitätsgruppe/Virtuelles Netzwerk** ein virtuelles Netzwerk an, wenn Sie eins einsetzen möchten. **Wichtig**: Wenn der virtuelle Computer ein virtuelles Netzwerk verwenden soll, müssen Sie ihn bei seiner Erstellung in das virtuelle Netzwerk einbinden. Sie können den virtuellen Computer nicht in ein virtuelles Netzwerk einbinden, nachdem Sie den virtuellen Computer erstellt haben. Weitere Informationen finden Sie unter [Virtuelle Netzwerke im Überblick][].

	3. Erstellen Sie die Verfügbarkeitsgruppe. Belassen Sie die Auswahl unter **Verfügbarkeitsgruppe** bei **Verfügbarkeitsgruppe erstellen**. Geben Sie dann einen Namen für die Gruppe ein.

	4. Erstellen Sie die Standardendpunkte, und fügen Sie bei Bedarf weitere hinzu. Sie können Endpunkt auch später hinzufügen.

	![Erstellen einer Verfügbarkeitsgruppe für einen neuen virtuellen Computer](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png)

6. Klicken Sie im vierten Bildschirm auf die zu installierenden Erweiterungen. Erweiterungen bieten Features, die die Verwaltung des virtuellen Computers vereinfachen, beispielsweise das Ausführen von Antischadsoftware oder das Zurücksetzen von Kennwörtern. Details finden Sie im Thema zum [Azure VM-Agent und VM-Erweiterungen](virtual-machines-extensions-agent-about.md).

7.	Klicken Sie auf den Pfeil, um den virtuellen Computer und den Verfügbarkeitssatz zu erstellen.

	Wenn Sie im Dashboard des neuen virtuellen Computers auf **Konfigurieren** klicken, sehen Sie, dass der virtuelle Computer zur neuen Verfügbarkeitsgruppe gehört.

Beachten Sie Folgendes, wenn Sie einen virtuellen Azure-Computer mithilfe von Azure PowerShell-Befehlen erstellen und einer neuen oder vorhandenen Verfügbarkeitsgruppe hinzufügen:

- [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren von Linux-basierten virtuellen Computern](virtual-machines-ps-create-preconfigure-linux-vms.md)

## <a id="addmachine"> </a>Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu##

Im klassischen Azur-Portal können Sie einer vorhandenen Verfügbarkeitsgruppe vorhandene virtuelle Computer hinzufügen oder einen neuen virtuellen Computer für sie erstellen. (Beachten Sie, dass die virtuellen Computer in derselben Verfügbarkeitsgruppe zum selben Clouddienst gehören müssen.) Die Schritte sind beinah identisch. Mit Azure PowerShell können Sie einer vorhandenen Verfügbarkeitsgruppe den virtuellen Computer hinzufügen.

1. Melden Sie sich beim klassischen Azure-Portal an, falls noch nicht geschehen.

2. Klicken Sie in der Befehlsleiste auf **Virtuelle Computer**.

3. Klicken Sie in der Liste der virtuellen Computer auf die Namen der virtuellen Computer, die Sie der Gruppe hinzufügen möchten.

4. Klicken Sie auf den Registerkarten unter dem Namen des virtuellen Computers auf **Konfigurieren**.

5. Suchen Sie im Abschnitt „Einstellungen“ nach **Verfügbarkeitsgruppe**. Führen Sie einen der folgenden Schritte aus:

	A: Wählen Sie **Verfügbarkeitsgruppe erstellen**, und geben Sie einen Namen für die Gruppe ein.

	B. Wählen Sie **Verfügbarkeitsgruppe auswählen**, und wählen Sie eine Gruppe aus der Liste aus.

	![Erstellen einer Verfügbarkeitsgruppe für einen vorhandenen virtuellen Computer](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png)

6. Klicken Sie auf **Speichern**.

Um Azure PowerShell-Befehle zu verwenden, öffnen Sie eine Azure PowerShell-Sitzung mit Administratorrechten, und führen Sie den folgenden Befehl aus. Für die Platzhalter (z. B. &lt;VmCloudServiceName&gt;) ersetzen Sie alles innerhalb der Anführungszeichen einschließlich der Zeichen < and > durch die richtigen Namen.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE]Der virtuelle Computer muss möglicherweise neu gestartet werden, um das Hinzufügen zur Verfügbarkeitsgruppe abzuschließen.

## Zusätzliche Ressourcen

[Artikel zu virtuellen Computern in der Dienstverwaltung]

<!-- LINKS -->
[Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe]: #createset
[Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu]: #addmachine

[Lastenausgleich für Azure-Infrastrukturdienste]: virtual-machines-load-balance.md
[Verwalten der Verfügbarkeit virtueller Computer]: virtual-machines-manage-availability.md
[Erstellen eines virtuellen Windows-Computers]: virtual-machines-windows-tutorial.md
[Virtuelle Netzwerke im Überblick]: virtual-networks-overview.md
[Artikel zu virtuellen Computern in der Dienstverwaltung]: virtual-machines-service-management-articles.md

<!---HONumber=AcomDC_1203_2015-->