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
   ms.date="09/02/2015"
   ms.author="joaoma" />

# Namensauflösung für virtuelle Computer und Rolleninstanzen

Abhängig davon, wie Sie Azure zum Hosten von IaaS, PaaS und Hybridlösungen verwenden, müssen Sie für die virtuellen Computer und Rolleninstanzen, die Sie erstellen, die Kommunikation untereinander zulassen. Obwohl diese Kommunikation über IP-Adressen erfolgen kann, ist es deutlich einfacher, Namen zu verwenden, die leicht zu merken sind und sich nicht verändern.

Wenn in Azure gehostete Rolleninstanzen und virtuelle Computer Domänennamen in interne IP-Adressen auflösen müssen, können sie eine dieser beiden Methoden verwenden:

- [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution)

- [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server)

Welche Art der Namensauflösung Sie verwenden, hängt davon ab, wie die virtuellen Computer und Rolleninstanzen miteinander kommunizieren müssen.

**In der folgende Tabelle werden die Szenarien und entsprechenden Lösungen für die Namensauflösung dargestellt:**

| **Szenario** | **Namensauflösung bereitgestellt von:** | **Weitere Informationen finden Sie unter:** |
|--------------|----------------------------------|-------------------------------|
| Namensauflösung zwischen Rolleninstanzen oder virtuellen Computern im gleichen Clouddienst | Von Azure bereitgestellte Namensauflösung | [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution)|
| Namensauflösung zwischen virtuellen Computern und Rolleninstanzen im gleichen virtuellen Netzwerk | Von Azure bereitgestellte Namensauflösung (nur für ARM-basierte Bereitstellungen) oder Namensauflösung mithilfe eines eigenen DNS-Servers | [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution), [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements) |
| Namensauflösung zwischen virtuellen Computern und Rolleninstanzen in unterschiedlichen virtuellen Netzwerken | Namensauflösung mithilfe eines eigenen DNS-Servers| [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements)|
| Standortübergreifend: Namensauflösung zwischen Rolleninstanzen oder virtuellen Computern in Azure und lokalen Computern| Namensauflösung mithilfe eines eigenen DNS-Servers| [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements)|
| Reverse-Lookup von internen IP-Adressen| Namensauflösung mithilfe eines eigenen DNS-Servers| [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements)|
| Namensauflösung für nicht-öffentliche Domänen (z. B. Active Directory-Domänen)| Namensauflösung mithilfe eines eigenen DNS-Servers| [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements)|
| Namensauflösung zwischen Rolleninstanzen in unterschiedlichen Clouddiensten, nicht in einem virtuellen Netzwerk| Nicht zutreffend Die Konnektivität zwischen virtuellen Computern und Rolleninstanzen in verschiedenen Clouddiensten wird außerhalb eines virtuellen Netzwerks nicht unterstützt.| Nicht zutreffend|


## Von Azure bereitgestellte Namensauflösung

Zusammen mit der Auflösung des öffentlichen DNS-Namens bietet Azure die Auflösung interner Namen für virtuelle Computer und Rolleninstanzen, die sich innerhalb des gleichen virtuellen Netzwerks oder Clouddienstes befinden. Virtuelle Computer und Instanzen in einem Clouddienst verwenden das gleiche DNS-Suffix. Daher ist innerhalb eines Clouddiensts der Hostname allein ausreichend. In klassischen virtuellen Netzwerken hingegen haben verschiedene Clouddienste unterschiedliche DNS-Suffixe. Deshalb wird hier der FQDN (Fully Qualified Domain Name, vollqualifizierter Domänenname) benötigt, um Namen zwischen verschiedenen Clouddiensten aufzulösen. In auf Azure-Ressourcen-Manager basierenden virtuellen Netzwerken ist das DNS-Suffix im ganzen virtuellen Netzwerk gleich, sodass der FQDN nicht erforderlich ist, und der DNS-Name kann entweder der Netzwerkkarte oder dem virtuellen Computer zugewiesen werden. Obwohl für die von Azure bereitgestellte Namensauflösung keine Konfiguration erforderlich ist, ist sie nicht für alle Bereitstellungsszenarien die beste Lösung, wie in der Tabelle oben dargestellt.

> [AZURE.NOTE] Im Fall von Web- und Workerrollen können Sie auf die internen IP-Adressen von Rolleninstanzen auf Grundlage des Rollennamens und der Instanznummer über die REST-API für die Azure-Service-Verwaltung zugreifen. Weitere Informationen finden Sie unter [Referenz zur REST-API der Dienstverwaltung](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Funktionen und Überlegungen

**Funktionen:**

- Benutzerfreundlichkeit: Es ist keine Konfiguration erforderlich, um die von Azure bereitgestellte Namensauflösung zu verwenden.

- Der von Azure bereitgestellte Dienst zur Namensauflösung ist hoch verfügbar und erspart Ihnen das Erstellen und Verwalten von Clustern Ihrer eigenen DNS-Server.

- Die Namensauflösung wird zwischen Rolleninstanzen/virtuellen Computern im gleichen Clouddienst bereitgestellt, ohne dass ein FQDN erforderlich ist.

- Die Namensauflösung wird zwischen virtuellen Computern in ARM-basierten virtuellen Netzwerken ohne Notwendigkeit des FQDN bereitgestellt. In klassischen virtuellen Netzwerken ist der FQDN beim Auflösen von Namen in unterschiedlichen Clouddiensten erforderlich.

- Sie können die Hostnamen erstellen, die Ihre Bereitstellungen am besten beschreiben, und müssen nicht mit automatisch generierten Namen arbeiten.

**Überlegungen:**

- Die Namensauflösung zwischen virtuellen Netzwerken sowie zwischen Azure und lokalen Computern ist nicht verfügbar.

- Das von Azure erstellte DNS-Suffix kann nicht geändert werden.

- Sie können keine eigenen Einträge manuell registrieren.

- WINS und NetBIOS werden nicht unterstützt. (Sie können Ihre virtuellen Computer nicht im Netzwerkbrowser in Windows Explorer auflisten.)

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

Wenn Ihre Anforderungen an die Namensauflösung über die von Azure bereitgestellten Funktionen hinausgehen, können Sie Ihre eigenen DNS-Server verwenden. Beim Verwenden Ihrer eigenen DNS-Server sind Sie für die Verwaltung der DNS-Einträge selbst verantwortlich.

> [AZURE.NOTE] Es wird empfohlen, die Verwendung eines externen DNS-Servers zu vermeiden, wenn dies nicht aufgrund des Bereitstellungsszenarios erforderlich ist.

## Anforderungen für DNS-Server

Wenn Sie planen, eine nicht von Azure bereitgestellte Namensauflösung zu verwenden, muss der von Ihnen angegebene DNS-Server Folgendes unterstützen:

- Da Ihre DNS-Server die von Azure bereitgestellten DNS-Server ersetzen, muss Ihre DNS-Lösung den Anforderungen für die Kommunikation zwischen den virtuellen Computern gerecht werden. Wenn bei der Kommunikation zwischen den virtuellen Computern auf Hostnamen zurückgegriffen wird, müssen diese Hostnamen in Ihren DNS-Servern registriert werden. Dabei sind etliche Faktoren und Abhängigkeiten zu berücksichtigen. Einige nützliche Anhaltspunkte finden Sie in [diesem Dokument](virtual-networks-name-resolution-ddns.md).

- Falls auf dynamisches DNS zurückgegriffen wird, sollte beim DNS-Server der Aufräumvorgang für Datensätze deaktiviert sein. In Azure haben IP-Adressen einen langen DHCP-Lease, was dazu führen kann, dass Datensätze während des Aufräumvorgangs vom DNS-Server entfernt werden.

- Bei dem DNS-Server muss die Rekursion aktiviert werden, um die Auflösung von externen Domänennamen zu ermöglichen.

- Auf den DNS-Server muss von den Clients, die eine Namensauflösung anfordern, und den Diensten und virtuellen Computern, die ihre Namen registrieren, zugegriffen werden können (über TCP/UDP-Port 53).

- Es wird außerdem empfohlen, den DNS-Server gegenüber einem Zugriff aus dem Internet zu sichern, da viele Bots nach offenen rekursiven DNS-Resolvern suchen.

- Für eine optimale Leistung bei Verwendung von virtuellen Azure-Computern als DNS-Server sollte IPv6 deaktiviert und eine [öffentliche IP-Adresse auf Instanzebene](virtual-networks-instance-level-public-ip.mp) jedem virtuellen DNS-Servercomputer zugewiesen werden.
	- Wenn Sie Windows Server als DNS-Server verwenden, finden Sie in [diesem Artikel](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) Informationen zur Leistungsanalyse und zu Optimierungsmöglichkeiten.


## Angeben von DNS-Servern

Sie können mehrere DNS-Server angeben, die von Ihren virtuellen Computern und Rolleninstanzen verwendet werden. Für jede DNS-Abfrage verwendet der Client zunächst den bevorzugten DNS-Server und verwendet die alternativen Server nur dann, wenn der bevorzugte Server nicht reagiert, d. h. wenn es keinen Lastenausgleich bei DNS-Abfragen über verschiedene DNS-Server hinweg gibt. Stellen Sie daher sicher, dass die DNS-Server in der richtigen Reihenfolge für Ihre Umgebung aufgeführt werden.

> [AZURE.NOTE] Wenn Sie die DNS-Einstellungen in einer Netzwerkkonfigurationsdatei für ein virtuelles Netzwerk ändern, das bereits bereitgestellt wurde, müssen Sie alle virtuellen Computer neu starten, damit die Änderungen wirksam werden.

### Angeben eines DNS-Servers im Verwaltungsportal

Beim Erstellen eines virtuellen Netzwerks im Verwaltungsportal können Sie die IP-Adresse und den Namen des DNS-Servers (oder der Server) angeben, die Sie verwenden möchten. Nach dem Erstellen des virtuellen Netzwerks werden die virtuellen Computer und Rolleninstanzen, die Sie im virtuellen Netzwerk bereitstellen, automatisch mit den angegebenen DNS-Einstellungen konfiguriert. DNS-Server, die für einen bestimmten Clouddienst (klassisches Azure) oder eine Netzwerkschnittstellenkarte (ARM-basierte Bereitstellungen) angegeben werden, haben Vorrang vor denen für das virtuelle Netzwerk. Siehe [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](virtual-networks-settings.md).

### Angeben eines DNS-Servers mithilfe von Konfigurationsdateien (klassisches Azure)

Bei klassischen virtuellen Netzwerken können Sie DNS-Einstellungen mithilfe von zwei verschiedenen Konfigurationsdateien angeben: mit der *Netzwerkkonfigurationsdatei* und der *Dienstkonfigurationsdatei*.

Die Netzwerk-Konfigurationsdatei beschreibt die virtuellen Netzwerke in Ihrem Abonnement. Wenn Sie einem Clouddienst in einem virtuellen Netzwerk Rolleninstanzen oder virtuelle Computer hinzufügen, werden die DNS-Einstellungen aus der Netzwerkkonfigurationsdatei auf jede Rolleninstanz oder jeden virtuellen Computer angewendet, sofern keine Clouddienst-spezifischen DNS-Server angegeben wurden.

Die Dienstkonfigurationsdatei wird für jeden Clouddienst erstellt, den Sie in Azure hinzufügen. Wenn Sie dem Clouddienst Rolleninstanzen oder virtuelle Computer hinzufügen, werden die DNS-Einstellungen aus der Dienstkonfigurationsdatei auf jede Rolleninstanz oder virtuellen Computer angewendet.

> [AZURE.NOTE] DNS-Server in der Dienstkonfigurationsdatei überschreiben die Einstellungen in der Netzwerkkonfigurationsdatei.


## Nächste Schritte

[Azure-Dienstkonfigurationsschema (.cscfg-Datei)](https://msdn.microsoft.com/library/azure/ee758710)

[Konfigurationsschema für virtuelle Netzwerke](https://msdn.microsoft.com/library/azure/jj157100)

[Informationen zum Konfigurieren von Einstellungen für virtuelle Netzwerke im Verwaltungsportal](virtual-networks-settings.md)

[Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei](virtual-networks-using-network-configuration-file.md)

<!---HONumber=AcomDC_0204_2016-->