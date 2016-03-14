<properties 
   pageTitle="Auflösung für virtuelle Computer und Rolleninstanzen"
   description="Szenarien für die Namensauflösung für Azure IaaS, Hybridlösungen, zwischen verschiedenen Clouddiensten, Active Directory und Verwenden von eigenen DNS-Servern"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="telmos" />

# Namensauflösung für virtuelle Computer und Rolleninstanzen

Abhängig davon, wie Sie Azure zum Hosten von IaaS, PaaS und Hybridlösungen verwenden, müssen Sie für die virtuellen Computer und Rolleninstanzen, die Sie erstellen, die Kommunikation untereinander zulassen. Obwohl diese Kommunikation über IP-Adressen erfolgen kann, ist es deutlich einfacher, Namen zu verwenden, die leicht zu merken sind und sich nicht verändern.

Wenn in Azure gehostete Rolleninstanzen und virtuelle Computer Domänennamen in interne IP-Adressen auflösen müssen, können sie eine dieser beiden Methoden verwenden:

- [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution)

- [Namensauflösung mit einem eigenen DNS-Server](#name-resolution-using-your-own-dns-server) (dieser kann Abfragen an die von Azure bereitgestellten DNS-Server weiterleiten)

Welche Art der Namensauflösung Sie verwenden, hängt davon ab, wie die virtuellen Computer und Rolleninstanzen miteinander kommunizieren müssen.

**In der folgende Tabelle werden die Szenarien und entsprechenden Lösungen für die Namensauflösung dargestellt:**

| **Szenario** | **Lösung** | **Suffix** |
|--------------|--------------|----------|
| Namensauflösung zwischen Rolleninstanzen oder virtuellen Computern im gleichen Clouddienst oder virtuellen Netzwerk | [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution)| Hostname oder FQDN |
| Namensauflösung zwischen Rolleninstanzen und virtuellen Computern in unterschiedlichen virtuellen Netzwerken | Vom Kunden verwaltete DNS-Server leiten Abfragen zwischen virtuellen Netzwerken zur Auflösung durch Azure (DNS-Proxy) weiter. Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server).| Nur FQDN |
| Auflösung lokaler Computer- und Dienstnamen von Rolleninstanzen oder virtuellen Computern in Azure | Vom Kunden verwaltete DNS-Server (z. B. lokale Domänencontroller, lokale schreibgeschützte Domänencontroller oder ein sekundärer DNS-Server, der mithilfe von Zonenübertragungen synchronisiert wird). Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server).|Nur FQDN |
| Auflösung von Azure-Hostnamen von lokalen Computern | Weiterleiten von Abfragen an einen vom Kunden verwalteten DNS-Proxyserver im zugehörigen virtuellen Netzwerk. Der Proxyserver leitet Abfragen zur Auflösung an Azure weiter. Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server).| Nur FQDN |
| Reverse-DNS für interne IPs | [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) | – |
| Namensauflösung zwischen virtuellen Computern oder Rolleninstanzen in unterschiedlichen Clouddiensten, nicht in einem virtuellen Netzwerk| Nicht zutreffend Die Konnektivität zwischen virtuellen Computern und Rolleninstanzen in verschiedenen Clouddiensten wird außerhalb eines virtuellen Netzwerks nicht unterstützt.| – |



## Von Azure bereitgestellte Namensauflösung

Zusammen mit der Auflösung des öffentlichen DNS-Namens bietet Azure die Auflösung interner Namen für virtuelle Computer und Rolleninstanzen, die sich innerhalb des gleichen virtuellen Netzwerks oder Clouddiensts befinden. Virtuelle Computer und Instanzen in einem Clouddienst verwenden dasselbe DNS-Suffix (deshalb ist der Hostname allein ausreichend). In klassischen virtuellen Netzwerken hingegen haben verschiedene Clouddienste unterschiedliche DNS-Suffixe. Deshalb wird hier der FQDN (Fully Qualified Domain Name, vollqualifizierter Domänenname) benötigt, um Namen zwischen verschiedenen Clouddiensten aufzulösen. In ARM-basierten virtuellen Netzwerken ist das DNS-Suffix im ganzen virtuellen Netzwerk gleich (FQDN nicht erforderlich), und DNS-Namen können entweder Netzwerkkarten oder virtuellen Computern zugewiesen werden. Obwohl für die von Azure bereitgestellte Namensauflösung keine Konfiguration erforderlich ist, ist sie nicht für alle Bereitstellungsszenarien die beste Lösung, wie in der Tabelle oben dargestellt.

> [AZURE.NOTE] Im Fall von Web- und Workerrollen können Sie auf die internen IP-Adressen von Rolleninstanzen auf Grundlage des Rollennamens und der Instanznummer über die REST-API für die Azure-Service-Verwaltung zugreifen. Weitere Informationen finden Sie unter [Referenz zur REST-API der Dienstverwaltung](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Funktionen und Überlegungen

**Funktionen:**

- Benutzerfreundlichkeit: Es ist keine Konfiguration erforderlich, um die von Azure bereitgestellte Namensauflösung zu verwenden.

- Der von Azure bereitgestellte Dienst zur Namensauflösung ist hoch verfügbar und erspart Ihnen das Erstellen und Verwalten von Clustern Ihrer eigenen DNS-Server.

- Kann in Verbindung mit Ihren eigenen DNS-Servern zum Auflösen von lokalen und Azure-Hostnamen verwendet werden.

- Die Namensauflösung wird zwischen Rolleninstanzen/virtuellen Computern im gleichen Clouddienst bereitgestellt, ohne dass ein FQDN erforderlich ist.

- Die Namensauflösung wird zwischen virtuellen Computern in ARM-basierten virtuellen Netzwerken ohne Notwendigkeit des FQDN bereitgestellt. In klassischen virtuellen Netzwerken ist der FQDN beim Auflösen von Namen in unterschiedlichen Clouddiensten erforderlich.

- Sie können Hostnamen verwenden, die Ihre Bereitstellungen am besten beschreiben, und müssen nicht mit automatisch generierten Namen arbeiten.

**Überlegungen:**

- Das von Azure erstellte DNS-Suffix kann nicht geändert werden.

- Sie können keine eigenen Einträge manuell registrieren.

- WINS und NetBIOS werden nicht unterstützt. (Ihre virtuellen Computer werden im Windows-Explorer nicht angezeigt.)

- Hostnamen müssen DNS-kompatibel sein. (Es dürfen nur 0-9, a-Z und "-" verwendet werden, und sie dürfen nicht mit "-" beginnen. Siehe RFC 3696 Abschnitt 2).

- Der DNS-Abfragedatenverkehr wird für den jeweiligen virtuellen Computer gedrosselt. Dies sollte auf die meisten Anwendungen keine Auswirkungen haben. Wenn eine Drosselung der Anforderungen festgestellt wird, stellen Sie sicher, dass clientseitiges Zwischenspeichern aktiviert ist. Weitere Informationen finden Sie unter [Getting the most from Azure-provided name resolution](#Getting-the-most-from-Azure-provided-name-resolution) (auf Englisch).

- Nur virtuelle Computer in den ersten 180 Clouddiensten werden für jedes klassische virtuelle Netzwerk registriert. Dies gilt nicht für ARM-basierte virtuelle Netzwerke.


### Die optimale Nutzung der von Azure bereitgestellten Namensauflösung
**Clientseitiges Caching:**

Nicht alle DNS-Abfragen müssen über das Netzwerk gesendet werden. Clientseitiges Zwischenspeichern kann die Latenz verringern und die Flexibilität bei Netzwerkproblemen verbessern, indem sich wiederholende DNS-Abfragen aus einem lokalen Cache aufgelöst werden. DNS-Einträge enthalten ein Time-To-Live (TTL), damit der Cache den Datensatz so lange wie möglich speichern kann, ohne die Aktualität der Datensätze zu beeinträchtigen, damit das clientseitige Zwischenspeichern für die meisten Situationen geeignet ist.

Der standardmäßige DNS-Client von Windows verfügt über einen integrierten DNS-Cache. Einige Linux-Distributionen bieten standardmäßig kein Zwischenspeichern. Deshalb wird empfohlen, diese Funktion zu jedem virtuellen Linux-Computer hinzuzufügen (nachdem Sie sich vergewissert haben, dass noch kein lokaler Cache vorhanden ist).

Es gibt eine Reihe verschiedener DNS-Cachingpakete, z. B. dnsmasq. Es folgen die Schritte zur Installation von dnsmasq auf den am häufigsten verwendeten Distributionen:

- **Ubuntu (verwendet resolvconf)**:
	- Installieren Sie einfach das dnsmasq-Paket ("sudo apt-get install dnsmasq").
- **SUSE (verwendet netconf)**:
	- Installieren Sie das dnsmasq-Paket ("sudo zypper install dnsmasq"). 
	- Aktivieren Sie den dnsmasq-Dienst ("systemctl enable dnsmasq.service"). 
	- Starten Sie den dnsmasq-Dienst ("systemctl start dnsmasq.service"). 
	- Bearbeiten Sie "/etc/sysconfig/network/config", und ändern Sie NETCONFIG\_DNS\_FORWARDER="" auf "dnsmasq".
	- Aktualisieren Sie resolv.conf ("netconfig update"), um den Cache als lokalen DNS-Auflöser festzulegen.
- **OpenLogic (verwendet NetworkManager)**:
	- Installieren Sie das dnsmasq-Paket ("sudo yum install dnsmasq").
	- Aktivieren Sie den dnsmasq-Dienst ("systemctl enable dnsmasq.service").
	- Starten Sie den dnsmasq-Dienst ("systemctl start dnsmasq.service").
	- Fügen Sie "prepend domain-name-servers 127.0.0.1;" zu "/etc/dhclient-eth0.conf" hinzu.
	- Starten Sie den Netzwerkdienst neu ("service network restart"), um den Cache als lokalen DNS-Auflöser festzulegen.

> [AZURE.NOTE]\: Das dnsmasq-Paket ist nur einer der vielen DNS-Caches, die für Linux verfügbar sind. Bevor Sie es nutzen, überprüfen Sie dessen Eignung für Ihre besonderen Bedürfnisse und außerdem, ob keine anderer Cache installiert ist.

**Clientseitige Wiederholungsversuche:**

DNS ist in erster Linie ein UDP-Protokoll. Da das UDP-Protokoll keine Nachrichtenübermittlung garantiert, wird die Wiederholungslogik im DNS-Protokoll selbst behandelt. Jeder DNS-Client (Betriebssystem) kann eine unterschiedliche Wiederholungslogik je nach Vorliebe des Erstellers aufweisen:

 - Windows-Betriebssysteme starten nach 1 Sekunde einen Wiederholungsversuch und dann erneut nach weiteren 2, 4 und weiteren 4 Sekunden. 
 - Das standardmäßige Linux-Setup führt nach 5 Sekunden einen Wiederholungsversuch aus. Es wird empfohlen, dies so zu ändern, dass 5 Mal im Abstand von 1 Sekunde ein Wiederholungsversuch gestartet wird.  

Geben Sie zum Überprüfen der aktuellen Einstellungen auf einem virtuellen Linux-Computer "cat /etc/resolv.conf" ein, und betrachten Sie die Zeile "Optionen", z. B.:

	options timeout:1 attempts:5

Die Datei "resolv.conf" wird normalerweise automatisch generiert und sollte nicht bearbeitet werden. Die entsprechenden Schritte zum Hinzufügen der Zeile "Optionen" variieren je nach Distribution:

- **Ubuntu** (verwendet resolvconf):
	- Fügen Sie die Optionszeile zu "/etc/resolveconf/resolv.conf.d/head" hinzu. 
	- Führen Sie "resolvconf -u" zum Aktualisieren aus.
- **SUSE** (verwendet netconf):
	- Fügen Sie "timeout:1 attempts:5' zum Parameter NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" in "/etc/sysconfig/network/config" hinzu. 
	- Führen Sie "netconfig update" zum Aktualisieren aus.
- **OpenLogic** (verwendet NetworkManager):
	- Fügen Sie 'echo "options timeout:1 attempts:5"' zu "/etc/NetworkManager/dispatcher.d/11-dhclient" hinzu. 
	- Führen Sie "service network restart" zum Aktualisieren aus.

## Namensauflösung mithilfe eines eigenen DNS-Servers
Es gibt verschiedene Situationen, in denen die Namensauflösung über die von Azure bereitgestellten Features hinausgeht. Dies kann beispielsweise bei der Verwendung von Active Directory-Domänen oder dann der Fall sein, wenn eine DNS-Auflösung zwischen virtuellen Netzwerken erforderlich ist. Um diese Szenarien abzudecken, bietet Azure Ihnen die Möglichkeit, eigene DNS-Server zu verwenden.

DNS-Server innerhalb eines virtuellen Netzwerks können DNS-Abfragen an die rekursiven Resolver von Azure weiterleiten, um Hostnamen innerhalb dieses virtuellen Netzwerks aufzulösen. Beispielsweise kann ein in Azure ausgeführter Domänencontroller (DC) auf DNS-Abfragen für die eigenen Domänen antworten und alle anderen Abfragen an Azure weiterleiten. Auf diese Weise sind sowohl Ihre lokalen Ressourcen (über den DC) als auch die von Azure bereitgestellten Hostnamen (über die Weiterleitung) für die virtuellen Computer sichtbar. Der Zugriff auf die rekursiven Resolver von Azure wird über die virtuelle IP 168.63.129.16 bereitgestellt.

Durch die DNS-Weiterleitung wird außerdem eine DNS-Auflösung zwischen virtuellen Netzwerken ermöglicht, und die lokalen Computer können von Azure bereitgestellte Hostnamen auflösen. Um einen Hostnamen eines virtuellen Computers aufzulösen, muss sich die DNS-Server-VM im selben virtuellen Netzwerk befinden und zur Weiterleitung von Abfragen für Hostnamen an Azure konfiguriert sein. Da jedes virtuelle Netzwerk ein eigenes DNS-Suffix verwendet, können Sie mithilfe von Regeln für die bedingte Weiterleitung DNS-Abfragen zur Auflösung an das richtige virtuelle Netzwerk senden. Die folgende Abbildung zeigt zwei virtuelle Netzwerke und ein lokales Netzwerk, in dem eine DNS-Auflösung zwischen virtuellen Netzwerken mithilfe dieser Methode durchgeführt wird:

![DNS-Auflösung zwischen virtuellen Netzwerken](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Bei Verwendung der von Azure bereitgestellten Namensauflösung wird jedem virtuellen Computer, der DHCP verwendet, das interne DNS-Suffix bereitgestellt. Wenn Sie eine eigene Lösung für die Namensauflösung verwenden, wird dieses Suffix nicht bereitgestellt, weil es Konflikte mit anderen DNS-Architekturen verursacht. Um per FQDN auf Computer zu verweisen oder das Suffix auf Ihren virtuellen Computern zu konfigurieren, kann das Suffix mithilfe von PowerShell oder der API ermittelt werden:

-  In einem mit ARM verwalteten virtuellen Netzwerk steht das Suffix über die [Netzwerkschnittstellenkarte](https://msdn.microsoft.com/library/azure/mt163668.aspx) oder das Cmdlet [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) zur Verfügung.    
-  Für klassische Bereitstellungen steht das Suffix über einen [Get Deployment](https://msdn.microsoft.com/library/azure/ee460804.aspx)-API-Aufruf oder das Cmdlet [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx) zur Verfügung.


Wenn eine Abfrageweiterleitung an Azure nicht Ihren Anforderungen entspricht, müssen Sie eine eigene DNS-Lösung bereitstellen. Ihre DNS-Lösung muss folgende Kriterien erfüllen:

-  Bereitstellung einer geeigneten Hostnamensauflösung, z. B. über [DDNS](virtual-networks-name-resolution-ddns.md). Beachten Sie, dass Sie bei der Verwendung von DDNS möglicherweise die DNS-Eintragsbereinigung deaktivieren müssen, da die DHCP-Leases von Azure sehr lange gültig sind und die DNS-Einträge durch eine Bereinigung möglicherweise zu früh entfernt werden. 
-  Bereitstellung einer geeigneten rekursiven Lösung, um eine Auflösung externer Domänennamen zu ermöglichen.
-  Möglichkeit zum Zugriff (TCP und UDP auf Port 53) von den Clients, die Dienste beziehen, und Internetzugriff.
-  Schutz vor einem Zugriff aus dem Internet, um mögliche Bedrohungen durch externe Agents zu minimieren.

> [AZURE.NOTE] Für eine optimale Leistung bei Verwendung von virtuellen Azure-Computern als DNS-Server sollte IPv6 deaktiviert und eine [öffentliche IP-Adresse auf Instanzebene](virtual-networks-instance-level-public-ip.mp) jedem virtuellen DNS-Servercomputer zugewiesen werden. Wenn Sie Windows Server als DNS-Server verwenden, finden Sie in [diesem Artikel](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) zusätzliche Informationen zur Leistungsanalyse und zu Optimierungsmöglichkeiten.



## Angeben von DNS-Servern

Sie können mehrere DNS-Server angeben, die von Ihren virtuellen Computern und Rolleninstanzen verwendet werden. Für jede DNS-Abfrage verwendet der Client zunächst den bevorzugten DNS-Server und verwendet die alternativen Server nur dann, wenn der bevorzugte Server nicht reagiert, d. h. wenn es keinen Lastenausgleich bei DNS-Abfragen über verschiedene DNS-Server hinweg gibt. Stellen Sie daher sicher, dass die DNS-Server in der richtigen Reihenfolge für Ihre Umgebung aufgeführt werden.

> [AZURE.NOTE] Wenn Sie die DNS-Einstellungen in einer Netzwerkkonfigurationsdatei für ein virtuelles Netzwerk ändern, das bereits bereitgestellt wurde, müssen Sie alle virtuellen Computer neu starten, damit die Änderungen wirksam werden.

### Angeben eines DNS-Servers im Verwaltungsportal

Beim Erstellen eines virtuellen Netzwerks im Verwaltungsportal können Sie die IP-Adresse und den Namen des DNS-Servers (oder der Server) angeben, die Sie verwenden möchten. Nach dem Erstellen des virtuellen Netzwerks werden die virtuellen Computer und Rolleninstanzen, die Sie im virtuellen Netzwerk bereitstellen, automatisch mit den angegebenen DNS-Einstellungen konfiguriert. DNS-Server, die für einen bestimmten Clouddienst (klassisches Azure) oder eine Netzwerkschnittstellenkarte (ARM-basierte Bereitstellungen) angegeben werden, haben Vorrang vor denen für das virtuelle Netzwerk.

### Angeben eines DNS-Servers mithilfe von Konfigurationsdateien (klassisches Azure)

Bei klassischen virtuellen Netzwerken können Sie DNS-Einstellungen mithilfe von zwei verschiedenen Konfigurationsdateien angeben: mit der *Netzwerkkonfigurationsdatei* und der *Dienstkonfigurationsdatei*.

Die Netzwerkkonfigurationsdatei beschreibt die virtuellen Netzwerke in Ihrem Abonnement. Wenn Sie einem Clouddienst in einem virtuellen Netzwerk Rolleninstanzen oder virtuelle Computer hinzufügen, werden die DNS-Einstellungen aus der Netzwerkkonfigurationsdatei auf jede Rolleninstanz oder jeden virtuellen Computer angewendet, sofern keine Clouddienst-spezifischen DNS-Server angegeben wurden.

Die Dienstkonfigurationsdatei wird für jeden Clouddienst erstellt, den Sie in Azure hinzufügen. Wenn Sie dem Clouddienst Rolleninstanzen oder virtuelle Computer hinzufügen, werden die DNS-Einstellungen aus der Dienstkonfigurationsdatei auf jede Rolleninstanz oder virtuellen Computer angewendet.

> [AZURE.NOTE] DNS-Server in der Dienstkonfigurationsdatei überschreiben die Einstellungen in der Netzwerkkonfigurationsdatei.


## Nächste Schritte

[Azure-Dienstkonfigurationsschema (.cscfg-Datei)](https://msdn.microsoft.com/library/azure/ee758710)

[Konfigurationsschema für virtuelle Netzwerke](https://msdn.microsoft.com/library/azure/jj157100)

[Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei](virtual-networks-using-network-configuration-file.md)

<!---HONumber=AcomDC_0302_2016-->