<properties 
   pageTitle="Migrieren von Affinitätsgruppen zu einem regionalen virtuellen Netzwerk (VNet)"
   description="Erfahren Sie, wie Sie von Affinitätsgruppen zu regionalen VNets migrieren."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2015"
   ms.author="telmos" />

# Migrieren von Affinitätsgruppen zu einem regionalen virtuellen Netzwerk (VNet)

Mit einer Affinitätsgruppe können Sie sicherstellen, dass Ressourcen, die innerhalb der gleichen Affinitätsgruppe erstellt werden, physisch von Servern gehostet werden, die sich in geringem Abstand zueinander befinden. Auf diese Weise können die Ressourcen schneller miteinander kommunizieren. In der Vergangenheit waren Affinitätsgruppen eine Voraussetzung für die Erstellung virtueller Netzwerke (VNets). Damals konnte der Netzwerk-Manager-Dienst, der die VNets verwaltete, nur innerhalb einer Gruppe physischer Server oder innerhalb einer Skalierungseinheit verwendet werden. Aufgrund von Verbesserungen bei der Architektur konnte die Netzwerkverwaltung inzwischen jedoch auf eine Region ausgeweitet werden.

Deshalb ist die Verwendung von Affinitätsgruppen nicht mehr empfehlenswert und für virtuelle Netzwerke auch nicht mehr erforderlich. Die Verwendung von Affinitätsgruppen für VNets wird durch Regionen ersetzt. VNets mit regionaler Zuordnung werden als regionale VNets bezeichnet.

Darüber hinaus raten wir grundsätzlich von der Verwendung von Affinitätsgruppen ab. Abgesehen von der VNet-Anforderung wurde mit Affinitätsgruppen auch sichergestellt, dass etwa Compute- und Speicherressourcen nah beieinander platziert wurden. Dank der aktuellen Netzwerkarchitektur von Azure ist dies jedoch nicht mehr erforderlich. Informationen zu den wenigen Sonderfällen, in denen sich unter Umständen noch die Verwendung einer Affinitätsgruppe empfiehlt, finden Sie unter [Affinitätsgruppen und virtuelle Computer](#Affinity-groups-and-VMs).

## Erstellen regionaler VNets und Migrieren zu regionalen VNets

Verwenden Sie bei der Erstellung neuer VNets ab sofort die Option *Region*. Diese Option steht im Verwaltungsportal zur Verfügung. In der Netzwerkkonfigurationsdatei wird die Option dagegen als *Location* angezeigt.

>[AZURE.IMPORTANT]Die Erstellung eines virtuellen Netzwerks mit Zuordnung zu einer Affinitätsgruppe ist zwar weiterhin technisch möglich, aber nicht mehr notwendig. Viele neue Features wie etwa Netzwerksicherheitsgruppen stehen nur bei Verwendung eines regionalen VNets und nicht bei Verwendung virtueller Netzwerke mit Affinitätsgruppenzuordnung zur Verfügung.

### Informationen zu VNets mit bestehender Affinitätsgruppenzuordnung

Für VNets mit bestehender Affinitätsgruppenzuordnung ist die Migration zu regionalen VNets möglich. Gehen Sie folgendermaßen vor, um zu einem regionalen VNet zu migrieren:

1. Exportieren Sie die Netzwerkkonfigurationsdatei. Dafür können Sie entweder PowerShell oder das Verwaltungsportal verwenden. Informationen zur Verwendung des Verwaltungsportals finden Sie unter [Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei](../virtual-networks-using-network-configuration-file/).

1. Bearbeiten Sie die Netzwerkkonfigurationsdatei, und ersetzen Sie dabei die alten Werte durch die neuen Werte.

	> [AZURE.NOTE]Geben Sie für **Location** die Region an, die Sie auch für die Affinitätsgruppe angegeben haben, die Ihrem VNet zugeordnet ist. Ist Ihr VNet also beispielsweise einer Affinitätsgruppe im Westen der USA zugeordnet, müssen Sie bei der Migration als Ort ebenfalls den Westen der USA angeben.
	
	Bearbeiten Sie die folgenden Zeilen Ihrer Netzwerkkonfigurationsdatei, und ersetzen Sie dabei die Werte durch Ihre eigenen Werte:

	**Alter Wert:** <VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG">

	**Neuer Wert:** <VirtualNetworkSitename="VNetUSWest" Location="West US">

1. Speichern Sie die Änderungen, und importieren[](../virtual-networks-using-network-configuration-file/) Sie die Netzwerkkonfiguration in Azure.

>[AZURE.INFO]Bei dieser Migration kommt es zu keinerlei Ausfallzeiten für Ihre Dienste.

## Affinitätsgruppen und virtuelle Computer

Wie bereits erwähnt, wird grundsätzlich von der Verwendung von Affinitätsgruppen für virtuelle Computer abgeraten. Affinitätsgruppen sollten nur dann noch verwendet werden, wenn bei einer Gruppe virtueller Computer unbedingt eine geringstmögliche Netzwerklatenz zwischen den virtuellen Computern erreicht werden muss. Durch die Platzierung virtueller Computer in einer Affinitätsgruppe werden alle virtuellen Computer im gleichen Servercluster oder in der gleichen Skalierungseinheit platziert.

Beachten Sie, dass die Verwendung einer Affinitätsgruppe zwei (möglicherweise negative) Auswirkungen hat:

- Der Satz der VM-Größen ist auf den Satz der VM-Größen beschränkt, der von der Compute-Skalierungseinheit bereitgestellt wird.

- Die Wahrscheinlichkeit, dass sich kein neuer virtueller Computer zuweisen lässt, nimmt zu. Dieser Fall tritt ein, wenn die Kapazität der spezifischen Skalierungseinheit für die Affinitätsgruppe erschöpft ist.

### Vorgehensweise bei Verwendung eines virtuellen Computers in einer Affinitätsgruppe

Virtuelle Computer, die sich derzeit in einer Affinitätsgruppe befinden, müssen nicht aus der Gruppe entfernt werden.

Die Bereitstellung eines virtuellen Computers erfolgt in einer einzelnen Skalierungseinheit. Affinitätsgruppen können zwar den Satz verfügbarer VM-Größen für eine neue VM-Bereitstellung einschränken, alle vorhandenen und bereits bereitgestellten virtuellen Computer sind jedoch bereits auf den Satz von VM-Größen beschränkt, der in der entsprechenden Skalierungseinheit verfügbar ist. Daher hat das Entfernen eines virtuellen Computers aus der Affinitätsgruppe keinerlei Auswirkungen.
 

<!---HONumber=Oct15_HO3-->