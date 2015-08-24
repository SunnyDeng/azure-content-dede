Sie müssen beachten, dass es zwei Methoden zum Konfigurieren eines Verfügbarkeitsgruppenlisteners in Azure gibt. Diese Methoden unterscheiden sich durch den Typ des Azure-Lastenausgleichs, den Sie verwenden, wenn Sie den Listener erstellen. In der folgenden Tabelle sind die Unterschiede beschrieben:

| Lastenausgleichsmodul | Implementierung | Verwendung: |
| ------------- | -------------- | ----------- |
| **Extern** | Verwendet die **öffentliche virtuelle IP-Adresse** des Clouddiensts, der die virtuellen Computer hostet. | Sie müssen auf den Listener von außerhalb des virtuellen Netzwerks zugreifen, auch über das Internet. |
| **Intern** | Verwendet **internen Lastenausgleich \(ILB\)** mit einer privaten Adresse für den Listener. | Sie greifen auf den Listener nur im gleichen virtuellen Netzwerk zu. Dies schließt Site-to-SiteVPNs in Hybridszenarien ein. |

>[AZURE.IMPORTANT]Für einen Listener, der die öffentliche VIP des Clouddiensts verwendet \(externer Lastenausgleich\), gelten alle Daten, die über den Listener zurückgegeben werden, als ausgehend und werden mit normalen Datenübertragungsraten in Azure berechnet. Dies gilt auch, wenn sich der Client im selben virtuellen Netzwerk und Datencenter wie der Listener und die Datenbanken befindet. Dies ist bei einem Listener mit ILB nicht der Fall.

ILB kann nur für virtuelle Netzwerke mit regionalem Umfang konfiguriert werden. Vorhandene virtuelle Netzwerke, die für eine Affinitätsgruppe konfiguriert wurden, können kein ILB verwenden. Weitere Informationen finden Sie unter [Interner Lastenausgleich](../articles/load-balancer/load-balancer-internal-overview.md).

<!---HONumber=August15_HO7-->