<properties 
	pageTitle="Gewusst wie: Konfigurieren einer Verfügbarkeitsgruppe für virtuelle Computer" 
	description="Enthält die Schritte zum Konfigurieren einer Verfügbarkeitsgruppe für einen neuen oder vorhandenen virtuellen Computer in Azure mithilfe des Azure-Verwaltungsportals und von Azure PowerShell-Befehlen" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="kathydav"/>

#Gewusst wie: Konfigurieren einer Verfügbarkeitsgruppe für virtuelle Computer#


Eine Verfügbarkeitsgruppe hilft, dass Ihre virtuellen Computer während eines Ausfalls wie während einer Wartung verfügbar bleiben. Das Platzieren von mindestens zwei ähnlich konfigurierten virtuellen Computern in einer Verfügbarkeitsgruppe erstellt die Redundanz, die zum Wahren der Verfügbarkeit der Anwendungen oder Dienste erforderlich ist, die auf Ihrem virtuellen Computer ausgeführt werden. Ausführliche Informationen hierzu finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer][].

Sie sollten eine Kombination aus Verfügbarkeitssätzen und Endpunkten mit Lastenausgleich verwenden, um sicherzustellen, dass Ihre Anwendung stets verfügbar ist und effizient ausgeführt wird. Details über Endpunkte mit Lastenausgleich finden Sie unter [Lastenausgleich für Azure-Infrastrukturdienste][].

Mithilfe einer der beiden folgenden Optionen können Sie einen virtuellen Computer in eine Verfügbarkeitsgruppe versetzen:

- [Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe][]. Fügen Sie der Gruppe anschließend die neuen virtuellen Computer hinzu, wenn Sie diese virtuellen Computer erstellen.
- [Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu][].


>[AZURE.NOTE]Virtuelle Computer, die Sie in dieselbe Verfügbarkeitsgruppe versetzen möchten, müssen zum selben Cloud-Dienst gehören.

## <a id="createset"> </a>Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe##

Dafür können Sie entweder das Verwaltungsportal oder die Azure PowerShell-Befehle verwenden.

So verwenden Sie das Verwaltungsportal

1. Melden Sie sich auf dem [Azure-Verwaltungsportal](http://manage.windowsazure.com) an, falls Sie dies noch nicht getan haben.

2. Klicken Sie in der Befehlsleiste auf **Neu**.

3. Klicken Sie auf **Virtueller Computer** und dann auf **Aus Katalog**.

4. Verwenden Sie die ersten beiden Bildschirme zum Auswählen eines Images, eines Benutzernamens, Kennworts usw. Weitere Details finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][].
 
5. Auf dem dritten Bildschirm können Sie die Ressourcen für Netzwerk, Speicher und Verfügbarkeit konfigurieren. Gehen Sie wie folgt vor:
	 
	1. Treffen Sie die entsprechende Auswahl für den Cloud-Dienst. Lassen Sie die Option **Einen neuen Cloud-Dienst erstellen** ausgewählt (es sei denn, Sie fügen einem vorhandenen VM-Cloud-Dienst diesen neuen virtuellen Computer hinzu). Geben Sie dann unter **DNS-Name des Cloud-Diensts** einen Namen ein. Der DNS-Name wird ein Teil des URI, der zum Kontaktieren des virtuellen Computers verwendet wird. Der Cloud-Dienst fungiert als eine Kommunikations- und Isolationsgruppe. Alle virtuellen Computer im selben Cloud-Dienst können miteinander kommunizieren, für den Lastenausgleich eingerichtet werden und in derselben Verfügbarkeitsgruppe platziert werden. 

	2. Geben Sie unter **Region/Affinitätsgruppe/Virtuelles Netzwerk** ein virtuelles Netzwerk an, wenn Sie eins einsetzen möchten. **Wichtig**: Wenn der virtuelle Computer ein virtuelles Netzwerk verwenden soll, müssen Sie ihn bei der Erstellung mit dem virtuellen Netzwerk verknüpfen. Sie können den virtuellen Computer nicht mit einem virtuellen Netzwerk verknüpfen, nachdem Sie den virtuellen Computer erstellt haben. Weitere Informationen finden Sie unter [Virtuelle Netzwerke im Überblick][].
	
	3. Erstellen Sie die Verfügbarkeitsgruppe. Belassen Sie die Auswahl unter **Verfügbarkeitsgruppe** bei **Verfügbarkeitsgruppe erstellen**. Geben Sie dann einen Namen für die Gruppe ein.

	4. Erstellen Sie die Standardendpunkte, und fügen Sie bei Bedarf weitere hinzu. Sie können Endpunkt auch später hinzufügen.

	![Erstellen einer Verfügbarkeitsgruppe für einen neuen virtuellen Computer](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png)

6. Wählen Sie auf dem vierten Bildschirm die zu installierenden Erweiterungen aus. Erweiterungen bieten Features, die die Verwaltung des virtuellen Computers vereinfachen, beispielsweise das Ausführen von Antischadsoftware oder das Zurücksetzen von Kennwörtern. Details finden Sie im Thema zum [Azure VM-Agent und VM-Erweiterungen](http://go.microsoft.com/fwlink/p/?LinkId=XXX).

7.	Klicken Sie auf den Pfeil, um den virtuellen Computer und den Verfügbarkeitssatz zu erstellen.

	Wenn Sie im Dashboard des neuen virtuellen Computers auf **Konfigurieren** klicken, sehen Sie, dass der virtuelle Computer zur neuen Verfügbarkeitsgruppe gehört.

Beachten Sie Folgendes, um Azure PowerShell-Befehle zum Erstellen eines virtuellen Azure-Computers zu verwenden und ihn zu einer neuen oder vorhandenen Verfügbarkeitsgruppe hinzuzufügen:

- [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren von Linux-basierten virtuellen Computern](virtual-machines-ps-create-preconfigure-linux-vms.md)


## <a id="addmachine"> </a>Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu##

Im Verwaltungsportal können Sie einer vorhandenen Verfügbarkeitsgruppe vorhandene virtuelle Computer hinzufügen oder einen neuen für sie erstellen. (Beachten Sie, dass die virtuellen Computer in derselben Verfügbarkeitsgruppe zum selben Cloud-Dienst gehören müssen.) Die Schritte sind beinah identisch. Mit Azure PowerShell können Sie einer vorhandenen Verfügbarkeitsgruppe den virtuellen Computer hinzufügen.

1. Melden Sie sich am [Azure-Verwaltungsportal](http://manage.windowsazure.com) an, falls noch nicht geschehen.

2. Klicken Sie auf der Navigationsleiste auf **Virtuelle Computer**.

3. Klicken Sie in der Liste der virtuellen Computer auf den virtuellen Computer, den Sie zur Gruppe hinzufügen möchten.

4. Klicken Sie auf den Registerkarten unter dem Namen des virtuellen Computers auf **Konfigurieren**.

5. Suchen Sie im Abschnitt „Einstellungen“ nach **Verfügbarkeitsgruppe**. Führen Sie einen der folgenden Schritte aus:

	A: Wählen Sie **Verfügbarkeitsgruppe erstellen** aus, und geben Sie einen Namen für die Gruppe ein.

	B. Wählen Sie **Verfügbarkeitsgruppe auswählen** aus, und wählen Sie einen Namen aus der Liste aus.

	![Erstellen einer Verfügbarkeitsgruppe für einen vorhandenen virtuellen Computer](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png)

6. Klicken Sie auf **Speichern**.

Um Azure PowerShell-Befehle zu verwenden, öffnen Sie eine Azure PowerShell-Sitzung mit Administratorrechten, und führen Sie den folgenden Befehl aus. Für die Platzhalter (z. B. &lt;VmCloudServiceName&gt;) ersetzen Sie alles innerhalb der Anführungszeichen einschließlich der Zeichen < and > durch die richtigen Namen.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE]Der virtuelle Computer muss möglicherweise neu gestartet werden, um das Hinzufügen zur Verfügbarkeitsgruppe abzuschließen.


##Zusätzliche Ressourcen
[Informationen zu Azure-VM-Konfigurationseinstellungen]

<!-- LINKS -->
[Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe]: #createset
[Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu]: #addmachine
[Lastenausgleich für Azure-Infrastrukturdienste]: virtual-machines-load-balance.md
[Verwalten der Verfügbarkeit virtueller Computer]: virtual-machines-manage-availability.md
[Erstellen eines virtuellen Windows Server-Computers]: virtual-machines-windows-tutorial.md
[Virtuelle Netzwerke im Überblick]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[Informationen zu Azure-VM-Konfigurationseinstellungen]: http://msdn.microsoft.com/library/azure/dn763935.aspx


 

<!---HONumber=July15_HO4-->