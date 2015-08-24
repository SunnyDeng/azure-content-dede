Wenn auf allen Servern im Cluster Windows Server 2008 R2 oder Windows Server 2012 ausgeführt wird, müssen Sie sicherstellen, dass das Hotfix [KB2854082](http://support.microsoft.com/kb/2854082) auf allen lokalen Servern oder Azure-VMs, die Teil des Clusters sind, installiert ist. Auf allen Servern oder virtuellen Computern, die sich im Cluster befinden, aber nicht in der Verfügbarkeitsgruppe, sollte dieser Hotfix ebenfalls installiert sein.

Laden Sie in der Remotedesktopsitzung für jeden Clusterknoten [KB2854082](http://support.microsoft.com/kb/2854082) in ein lokales Verzeichnis herunter. Installieren Sie dann den Hotfix nacheinander auf allen Clusterknoten. Wenn der Clusterdienst derzeit auf dem Clusterknoten ausgeführt wird, wird der Server am Ende der Installation des Hotfixes neu gestartet.

>[AZURE.WARNING]Das Beenden des Clusterdiensts oder das Neustarten des Servers wirkt sich auf den Quorumzustand des Clusters und der Verfügbarkeitsgruppe aus und kann dazu führen, dass der Cluster offline geschaltet wird. Um die hohe Verfügbarkeit des Clusters während der Installation zu gewährleisten, überprüfen Sie Folgendes:
>
> - Der Cluster weist einen optimalen Quorumzustand auf. 
> - Vor der Installation des Hotfixes auf einem Knoten sind alle Clusterknoten online.
> - Die Installation des Hotfixes auf einem Knoten wird vollständig abgeschlossen, einschließlich des Neustarts des Servers, bevor die Installation des Hotfixes auf einem anderen Knoten im Cluster ausgeführt wird.

<!---HONumber=August15_HO7-->