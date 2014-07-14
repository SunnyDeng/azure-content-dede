
# Azure-Netzwerke

Die einfachste Möglichkeit, eine Verbindung zu Azure-Anwendungen und-Daten herzustellen, bietet eine herkömmliche Internetverbindung. Diese einfache Lösung ist jedoch nicht immer auch die beste. Azure bietet zudem Technologien, mit denen Benutzer eine Verbindung zu Azure-Datencentern herstellen können. Diese Technologien werden im folgenden Artikel näher erläutert.

## Inhaltsverzeichnis

* [Azure Virtual Network](#Vnet)
* [Azure Traffic Manager](#TrafficMngr)

<a name="Vnet"></a> 

## Azure Virtual Network

Azure ermöglicht Ihnen, virtuelle Computer zu erstellen, die in Microsoft-Datencentern ausgeführt werden. Angenommen Ihr Unternehmen möchte diese virtuellen Computer nutzen, um Unternehmensanwendungen oder andere Software auszuführen, die von den Mitarbeitern im Unternehmen verwendet werden. Möglicherweise möchten Sie eine SharePoint-Farm in der Cloud erstellen oder eine Bestandsverwaltungsanwendung ausführen, um nur zwei Beispiele zu nennen. Um es den Benutzern möglichst leicht zu machen, soll der Zugriff auf diese Anwendungen auf die gleiche Weise erfolgen, als würden sie in Ihrem eigenen Datencenter ausgeführt.

Für diese Art von Problem gibt es eine Standardlösung: Erstellen Sie ein virtuelles privates Netzwerk (VPN). Unternehmen jeder Größe nutzen diese Möglichkeit heute, um beispielsweise Computer in den Filialen mit dem Hauptdatencenter des Unternehmens zu verbinden. Der gleiche Ansatz kann für virtuelle Azure-Computer angewendet werden, wie in Abbildung 1 zu sehen ist.

<a name="Fig1"></a>

![01_Networking](./media/azure-networking/Networking_01Networking.png)

**Abbildung 1: Azure Virtual Network ermöglicht die Erstellung eines virtuellen Netzwerks in der Cloud, das mit dem lokalen Datencenter verbunden ist.**

Wie die Abbildung zeigt, können Sie mit Azure Virtual Network eine logische Grenze um eine Gruppe von virtuellen Computern in einem Azure-Datencenter ziehen und ein sogenanntes *virtuelles Netzwerk oder kurz VNET* erstellen. Anschließend können Sie eine IPsec-Verbindung zwischen diesem VNET und dem lokalen Netzwerk einrichten. Die virtuellen Computer in einem VNET können mit virtuellen Azure-Computern und/oder Azure-Clouddiensten erstellt werden. Anders ausgedrückt, für die Erstellung kann entweder die Azure Infrastructure as a Service
(IaaS)-Technologie oder die Platform as a Service (PaaS)-Technologie
verwendet werden. Egal für welche Möglichkeit Sie sich entscheiden, zum Herstellen der IPsec-Verbindung sind ein VPN-Gatewaygerät, spezielle, mit dem lokalen Netzwerk verbundene Hardware sowie die Dienste Ihres Netzwerkadministrators notwendig. Ist die Verbindung einmal hergestellt, gleichen die im VNET ausgeführten virtuellen Azure-Computer jeder anderen Komponente Ihres Unternehmensnetzwerks.

Wie [Abbildung 1](#Fig1) nahelegt, weisen Sie den virtuellen Azure-Computern IP-Adressen aus dem gleichen IP-Adressraum zu, der auch in Ihrem eigenen Netzwerk verwendet wird. In dem hier gezeigten Szenario, in dem private IP-Adressen genutzt werden, sind die virtuellen Computer in der Cloud einfach eine IP-Teilmenge. In Ihrem lokalen Netzwerk ausgeführte Software erkennt diese virtuellen Computer als lokale Komponenten, genauso wie bei herkömmlichen VPNs. Zu beachten ist hierbei, dass die virtuellen und physischen Computer auf beiden Seiten ein beliebiges Betriebssystem ausführen können, da die Verbindung auf IP-Ebene hergestellt wird. Virtuelle Azure-Computer, auf denen Windows Server oder Linux ausgeführt wird, können mit lokalen Computern interagieren, auf denen Windows, Linux oder andere Systeme ausgeführt werden. Es ist außerdem möglich, gängige Verwaltungstools wie System Center und andere zum Verwalten der virtuellen Computer in der Cloud und der enthaltenen Anwendungen zu verwenden.

Die Nutzung von Azure Virtual Network ist in vielen Situationen sinnvoll. Wie bereits erwähnt, können Benutzer im Unternehmen so einfacher auf Cloudanwendungen zugreifen. Ein wichtiger Aspekt, der zu dieser Benutzerfreundlichkeit beträgt, ist die Fähigkeit, die virtuellen Azure-Computer in eine bestehende Active Directory-Domäne vor Ort einzubinden, um den Benutzern eine einmalige Anmeldung an den ausgeführten Anwendungen zu ermöglichen. Sie können auch eine Active Directory-Domäne in der Cloud erstellen, wenn Sie dies bevorzugen, und diese Domäne dann mit dem lokalen Netzwerk verbinden.

Die Erstellung eines VNET in einem Azure-Datencenter bietet effektiven Zugriff auf eine große Auswahl von bei Bedarf verfügbaren Ressourcen. Die können bei Bedarf virtuelle Computer erstellen, für die Sie während der Ausführung einen bestimmten Preis zahlen, und diese löschen (und nicht mehr bezahlen), sobald sie nicht mehr benötigt werden. Dies kann in Situationen sinnvoll sein, wenn ein schneller Zugriff auf einen vorkonfigurierten Computer benötigt wird, z. B. für Entwicklungsteams bei der Erstellung neuer Software. Anstatt darauf zu warten, dass ein lokaler Administrator die benötigten Ressourcen einrichtet, können sie dieser selbst in der öffentlichen Cloud erstellen.

Die Tatsache, dass virtuelle Netzwerke virtuelle Azure-Computer für die Ressourcen vor Ort wie lokale Komponenten erscheinen lässt, gilt auch umgekehrt: Software, die in Ihrem lokalen Netzwerk ausgeführt wird, wirkt auf Anwendungen, die in Ihrem Azure-VNET ausgeführt werden, wie eine lokale Komponente. Angenommen Sie möchten eine vorhandene lokale Anwendung auf Azure verlagern, beispielsweise weil der Betrieb in der Cloud kostengünstiger ist. Was aber geschieht, wenn die von der Anwendung gespeicherten Daten aufgrund gesetzlicher Auflagen lokal gespeichert werden müssen? In einer solchen Situation erkennt die Cloudanwendung durch die Nutzung des virtuellen Netzwerks ein vor Ort bereitgestelltes Datenbanksystem als lokal, was den Zugriff stark vereinfacht. Egal welches Szenario Sie wählen, das Ergebnis ist immer das gleiche: Azure wird zu einer Erweiterung Ihres eigenen Datencenters.

<a name="TrafficMngr"></a> 

## Azure Traffic Manager

Angenommen Sie haben erfolgreich eine Azure-Anwendung erstellt. Ihre Anwendung wird von vielen Menschen in vielen Ländern rund um die Welt genutzt. Dies ist ein großer Erfolg, aber wie so oft bringt dieser Erfolg neue Probleme mit sich. In diesem Beispiel wird die Anwendung wahrscheinlich in mehreren Azure-Datencentern in verschiedenen Teilen der Welt ausgeführt. Wie können Sie den Benutzeranfragen-Datenverkehr möglichst sinnvoll über diese Datencenter weiterleiten, sodass stets für eine optimale Benutzererfahrung gesorgt ist?

Azure Traffic Manager wurde entwickelt, um dieses Problem zu lösen. Abbildung 2 zeigt, wie dies geschieht.

<a name="Fig3"></a>

![03_TrafficManager](./media/azure-networking/Networking_03TrafficManager.png)

**Abbildung 2: Azure Traffic Manager leitet Anforderungen von Benutzern über verschiedene Anwendungsinstanzen in unterschiedlichen Azure-Datencentern intelligent weiter.**

In diesem Beispiel wird die Anwendung auf virtuellen Computern in vier Datencentern ausgeführt: zwei in den USA, eines in Europa und eines in Asien. Angenommen ein Benutzer in Berlin möchte auf die Anwendung zugreifen. Bei Verwendung von Traffic Manager geschieht in diesem Fall Folgendes:

Wie üblich sucht das System des Benutzers den DNS-Namen der Anwendung (Schritt 1). Die Abfrage wird an das Azure DNS-System weitergeleitet (Schritt 2), welches dann die Traffic Manager-Richtlinie für die Anwendung ermittelt. Jede Richtlinie wird vom Eigentümer der jeweiligen Azure-Anwendung erstellt, entweder über eine grafische Benutzeroberfläche oder eine REST-API. Unabhängig von der Art der Erstellung gibt diese Richtlinie ein von drei Lastenausgleichsoptionen an:

* **Leistung:** Alle Anfragen werden an das Datencenter mit der
  geringsten Latenz vor dem Benutzersystem gesendet.
* **Failover:** Alle Anforderungen werden an das vom Ersteller dieser
  Richtlinie angegebene Datencenter gesendet, es sei denn, dieses ist
  nicht verfügbar. In diesem Fall werden Anforderungen an andere
  Datencenter in der vom Ersteller der Richtlinie definierten
  Reihenfolge ihrer Priorität weitergeleitet.
* **Roundrobin:** Alle Anforderungen werden gleichmäßig auf alle
  Datencenter verteilt, in denen die Anwendung ausgeführt wird.

Nachdem die richtige Richtlinie bestimmt wurde, ermittelt Traffic Manager basierend auf der angegebenen Option (Schritt 3), an welches Datencenter diese Anforderungen gesendet werden soll. Anschließend wird der Standort des ausgewählten Datencenters an den Benutzer zurückgegeben(Schritt 4), der dann auf diese Instanz der Anwendung zugreift(Schritt 5).

Damit dies funktioniert, benötigt Traffic Manager aktuelle Informationen über die Anwendungsinstanzen, die in jedem Datencenter ausgeführt werden. Zu diesem Zweck sendet Traffic Manager in regelmäßigen Abständen ein Pingsignal über einen HTTP GET-Aufruf an jede Kopie der Anwendung und zeichnet auf, ob eine Antwort empfangen wird. Wenn eine Anwendungsinstanz nicht mehr antwortet, leitet Traffic Manager keinen Datenverkehr mehr an diese Instanz weiter, bis diese wieder auf Pings reagiert.

Nicht jede Anwendung ist so groß oder global, dass Traffic Manager von Nöten wäre. Wenn sie es jedoch ist, dann ist dieser Dienst sehr hilfreich.