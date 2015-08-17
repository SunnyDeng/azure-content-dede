<properties 
   pageTitle="Auflösung für virtuelle Computer und Rolleninstanzen"
   description="Szenarien für die Namensauflösung für Azure IaaS, Hybridlösungen, zwischen verschiedenen Clouddiensten, Active Directory und Verwenden von eigenen DNS-Servern"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="joaoma" />

# Namensauflösung für virtuelle Computer und Rolleninstanzen

Abhängig davon, wie Sie Azure zum Hosten von IaaS, PaaS und Hybridlösungen verwenden, müssen Sie für virtuelle Computer und Rolleninstanzen, die Sie erstellen, die Kommunikation mit anderen virtuellen Computern und Rolleninstanzen zulassen. Obwohl diese Kommunikation über IP-Adressen erfolgen kann, ist es deutlich einfacher, Hostnamen zu verwenden, die leicht zu merken sind. Allerdings müssen diese Hostnamen in IP-Adressen aufgelöst werden, um die Kommunikation zu ermöglichen.

Wenn in Azure gehostete Rolleninstanzen und virtuelle Computer Hostnamen und Domänennamen in interne IP-Adressen auflösen müssen, können sie eine dieser beiden Methoden verwenden:

- [Von Azure bereitgestellte Namensauflösung](azure-provided-name-resolution)

- [Namensauflösung mithilfe eines eigenen DNS-Servers](name-resolution-using-your-own-DNS-server)

Welche Art der Namensauflösung Sie verwenden, hängt davon ab, wie die virtuellen Computer und Rolleninstanzen innerhalb Ihres Clouddiensts und mit anderen Clouddiensten kommunizieren müssen.

**In der folgende Tabelle werden die Szenarien und entsprechenden Lösungen für die Namensauflösung dargestellt:**

| **Szenario** | **Namensauflösung bereitgestellt von:** | **Weitere Informationen finden Sie unter:** |
|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Namensauflösung zwischen Rolleninstanzen oder virtuellen Computern im gleichen Clouddienst | Von Azure bereitgestellte Namensauflösung | [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution) |
| Namensauflösung zwischen virtuellen Computern und Rolleninstanzen im gleichen virtuellen Netzwerk | Von Azure bereitgestellte Namensauflösung – mit FQDNorName-Auflösung über den eigenen DNS-Server | – [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution) – [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) – [Anforderungen für DNS-Server](#dns-server-requirements) |
| Namensauflösung zwischen virtuellen Computern und Rolleninstanzen in unterschiedlichen virtuellen Netzwerken | Namensauflösung mithilfe eines eigenen DNS-Servers | – [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) – [Anforderungen für DNS-Server](#dns-server-requirements) |
| Standortübergreifend: Namensauflösung zwischen Rolleninstanzen oder virtuellen Computern in Azure und lokalen Computern | Namensauflösung mithilfe eines eigenen DNS-Servers | – [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) – [Anforderungen für DNS-Server](#dns-server-requirements) |
| Reverse-Lookup von internen IP-Adressen | Namensauflösung mithilfe eines eigenen DNS-Servers | – [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) – [Anforderungen für DNS-Server](#dns-server-requirements) |
| Namensauflösung für benutzerdefinierte Domänen (z. B. Active Directory-Domänen, von Ihnen registrierte Domänen) | Namensauflösung mithilfe eines eigenen DNS-Servers | – [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) – [Anforderungen für DNS-Server](#dns-server-requirements) |
| Namensauflösung zwischen Rolleninstanzen in unterschiedlichen Clouddiensten, nicht in einem virtuellen Netzwerk | Nicht zutreffend Die Konnektivität zwischen virtuellen Computern und Rolleninstanzen in verschiedenen Clouddiensten wird außerhalb eines virtuellen Netzwerks nicht unterstützt. | Nicht zutreffend |

> [AZURE.NOTE]Die Namensauflösung zwischen Computern im Internet und Ihren öffentlichen Endpunkten wird automatisch von Azure bereitgestellt und erfordert keine Konfiguration.

## Von Azure bereitgestellte Namensauflösung

Zusammen mit der Auflösung für öffentliche Internetdomänen bietet Azure die Auflösung von Hostnamen für virtuelle Computer und Rolleninstanzen, die sich im gleichen Clouddienst befinden, mithilfe von Hostnamen. Zwischen virtuellen Computern und Rolleninstanzen in verschiedenen Clouddiensten, die sich im gleichen virtuellen Netzwerk befinden, werden FQDNs verwendet. Obwohl für die von Azure bereitgestellte Namensauflösung keine Konfiguration erforderlich ist, ist sie nicht für alle Bereitstellungsszenarien die beste Lösung, wie in der Tabelle oben dargestellt.

> [AZURE.NOTE]Im Fall von Web- und Workerrollen können Sie auf die internen IP-Adressen von Rolleninstanzen auf Grundlage des Rollennamens und der Instanznummer über die REST-API für die Azure-Service-Verwaltung zugreifen. Weitere Informationen finden Sie unter [Referenz zur REST-API der Dienstverwaltung](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Funktionen und Überlegungen

**Funktionen:**

- Benutzerfreundlichkeit: Es ist keine Konfiguration erforderlich, um den von Azure bereitgestellten DNS-Dienst zu verwenden.

- Die Namensauflösung wird zwischen Rolleninstanzen oder virtuellen Computern im gleichen Clouddienst bereitgestellt.

- Die Namensauflösung wird zwischen Rolleninstanzen und virtuellen Computern, die sich im gleichen virtuellen Netzwerk, aber in verschiedenen Clouddiensten befinden, mithilfe des FQDN der Zielrolleninstanz oder des virtuellen Zielcomputers bereitgestellt.

- Sie können die Hostnamen erstellen, die Ihre Bereitstellungen am besten beschreiben, und müssen nicht mit automatisch generierten Namen arbeiten.

- Standard-DNS-Lookups zum Auflösen der Namen von öffentlichen Domänen werden unterstützt.

**Überlegungen:**

- Die Namensauflösung zwischen virtuellen Netzwerken ist nicht verfügbar.

- Sie können nur Hostnamen und FQDNs für virtuelle Computer und Rolleninstanzen registrieren, die sich unter den ersten 180 Clouddiensten befinden, die einem virtuellen Azure-Netzwerk hinzugefügt wurden. Wenn Sie über mehr als 180 Clouddienste verfügen, müssen Sie unabhängig von der Anzahl von virtuellen Computern und Rolleninstanzen in jedem Dienst eigene DNS-Server für die Namensauflösung bereitstellen.

- Die Verwendung von mehreren Hostnamen für den gleichen virtuellen Computer oder die gleiche Rolleninstanz wird nicht unterstützt.

- Die standortübergreifende Namensauflösung ist nicht verfügbar.

- Das von Azure erstellte DNS-Suffix kann nicht geändert werden.

- Sie können keine eigenen Einträge im von Azure bereitgestellten DNS manuell registrieren.

- WINS und NetBIOS werden nicht unterstützt. (Sie können Ihre virtuellen Computer nicht im Netzwerkbrowser in Windows Explorer auflisten.)

- Hostnamen müssen DNS-kompatibel sein. (Es dürfen nur 0-9, a-Z und "-" verwendet werden, und sie dürfen nicht mit "-" beginnen. Siehe RFC 3696 Abschnitt 2).

- Der DNS-Anfragedatenverkehr wird pro virtuellem Computer gedrosselt. Wenn Ihre Anwendung häufige DNS-Abfragen für mehrere Zielnamen ausführt, kann es bei einigen Abfragen zu Zeitüberschreitungen kommen. Um dies zu vermeiden, wird die Verwendung der clientseitigen Zwischenspeicherung empfohlen.

## Namensauflösung mithilfe eines eigenen DNS-Servers

Wenn Ihre Anforderungen an die Namensauflösung über die von Azure bereitgestellten Funktionen hinausgehen, können Sie einen eigenen DNS-Server verwenden. Wenn Sie einen eigenen DNS-Server verwenden, müssen Sie die für Ihre Clouddienste erforderlichen Datensätze selbst verwalten.

> [AZURE.NOTE]Es wird empfohlen, die Verwendung eines externen DNS-Servers zu vermeiden, wenn dies nicht aufgrund des Bereitstellungsszenarios erforderlich ist.

## Anforderungen für DNS-Server

Wenn Sie planen, eine nicht von Azure bereitgestellte Namensauflösung zu verwenden, muss der von Ihnen angegebene DNS-Server Folgendes unterstützen: Der DNS-Server muss die dynamische DNS-Registrierung über dynamisches DNS (DDNS) akzeptieren.

- Beim DNS-Server muss der Aufräumvorgang für Datensätze deaktiviert sein. Für Azure-IP-Adressen gilt eine lange Leasedauer, was dazu führen kann, dass Datensätze auf dem DNS-Server während des Aufräumvorgangs entfernt werden.

- Bei dem DNS-Server muss die Rekursion aktiviert werden, um die Auflösung von externen Domänennamen zu ermöglichen.

- Auf den DNS-Server muss von den Clients, die eine Namensauflösung anfordern, und den Diensten und virtuellen Computern, die ihre Namen registrieren, zugegriffen werden können (über TCP/UDP-Port 53).

- Es wird außerdem empfohlen, den DNS-Server gegenüber einem Zugriff aus dem Internet zu sichern, da viele Bots nach offenen rekursiven DNS-Resolvern suchen.


## Angeben von DNS-Servern

Sie können mehrere DNS-Server angeben, die von Ihren virtuellen Computern und Rolleninstanzen verwendet werden. DNS-Server werden dabei jedoch in der Reihenfolge verwendet, in der sie zum Failover angegeben sind (im Gegensatz zu Roundrobin). Für jede DNS-Abfrage verwendet der Client zunächst den bevorzugten DNS-Server und verwendet die alternativen Server nur dann, wenn der bevorzugte Server nicht reagiert. Stellen Sie daher sicher, dass die DNS-Server in der richtigen Reihenfolge für Ihre Umgebung aufgeführt werden.

> [AZURE.NOTE]Wenn Sie die DNS-Einstellungen in einer Netzwerkkonfigurationsdatei für ein virtuelles Netzwerk ändern, das bereits bereitgestellt wurde, müssen Sie alle virtuellen Computer neu starten, damit die Änderungen wirksam werden.

### Angeben eines DNS-Servers über das Verwaltungsportal

Beim Erstellen des virtuellen Netzwerks über das Verwaltungsportal können Sie die IP-Adresse und den Namen des DNS-Servers (oder der Server) angeben, die Sie verwenden möchten. Nach dem Erstellen des virtuellen Netzwerks werden die virtuellen Computer und Rolleninstanzen, die Sie im virtuellen Netzwerk bereitstellen, automatisch mit den angegebenen DNS-Einstellungen konfiguriert, es sei denn, Sie geben an, welche DNS-Server für die Bereitstellung verwendet werden sollen. Weitere Informationen zum Konfigurieren von Einstellungen für Azure Virtual Network finden Sie unter [Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](https://msdn.microsoft.com/library/azure/jj156074.aspx).

> [AZURE.NOTE]Sie können maximal 9 DNS-Server verwenden.

### Angeben eines DNS-Servers mithilfe von Konfigurationsdateien

Sie können DNS-Einstellungen mithilfe von zwei verschiedenen Konfigurationsdateien angeben: mit der *Netzwerkkonfigurationsdatei* und der *Dienstkonfigurationsdatei*.

Die Netzwerkkonfigurationsdatei wird für jedes virtuelle Netzwerk erstellt, das Sie in Azure hinzufügen. Wenn Sie einem Clouddienst in einem virtuellen Netzwerk Rolleninstanzen oder virtuelle Computer hinzufügen, werden die DNS-Einstellungen aus der Netzwerkkonfigurationsdatei auf die Rolleninstanz oder den virtuellen Computer angewendet, sofern die Dienstkonfigurationsdatei über eigene DNS-Einstellungen verfügt.

Die Dienstkonfigurationsdatei wird für jeden Clouddienst erstellt, den Sie in Azure hinzufügen. Wenn Sie dem Clouddienst Rolleninstanzen oder virtuelle Computer hinzufügen, werden die DNS-Einstellungen aus der Dienstkonfigurationsdatei auf die Rolleninstanz oder den virtuellen Computer angewendet.

> [AZURE.NOTE]Einstellungen in der Dienstkonfigurationsdatei überschreiben die Einstellungen in der Netzwerkkonfigurationsdatei. Wenn z. B. ein virtueller Computer zu einem Clouddienst hinzugefügt wird, der Teil eines virtuellen Netzwerks ist, und sowohl die Netzwerkkonfigurationsdatei als auch die Dienstkonfigurationsdatei DNS-Einstellungen enthält, werden die DNS-Einstellungen in der Dienstkonfigurationsdatei auf den virtuellen Computer angewendet.


## Siehe auch

[Azure-Dienstkonfigurationsschema](https://msdn.microsoft.com/library/azure/ee758710)[Konfigurationsschema für virtuelle Azure-Netzwerke](https://msdn.microsoft.com/library/azure/jj157100)[Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](https://msdn.microsoft.com/library/azure/jj156074.aspx)[Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei](https://msdn.microsoft.com/library/azure/jj156097.aspx)[Aufgaben für die Azure Virtual Network-Konfiguration](https://msdn.microsoft.com/library/azure/jj156206.aspx)

<!---HONumber=August15_HO6-->