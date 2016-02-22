<properties pageTitle="Ausführen von Cassandra mit Linux in Azure | Microsoft Azure" description="Ausführen eines Cassandra-Clusters unter Linux auf virtuellen Azure-Computern aus einer Node.js-Anwendung." services="virtual-machines" documentationCenter="nodejs" authors="rmcmurray" manager="wpickett" editor="" azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="robmcm"/>

# Ausführen von Cassandra mit Linux auf Azure und Zugreifen aus Node.js 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/).

## Übersicht
Microsoft Azure ist eine offene Cloudplattform, die sowohl Microsoft- als auch Nicht-Microsoft-Software ausführt. Dies schließt Betriebssysteme, Anwendungsserver, Messaging-Middleware sowie SQL- und NoSQL-Datenbanken aus kommerziellen und Open-Source-Modellen ein. Das Erstellen von robusten Diensten für öffentliche Clouds einschließlich Azure erfordert eine sorgfältige Planung und eine wohl überlegte Architektur für Anwendungsserver und Speicherebenen. Die verteilte Speicherarchitektur von Cassandra unterstützt auf natürliche Weise das Erstellen von Systemen mit hoher Verfügbarkeit, die bei Clusterfehlern fehlertolerant sind. Cassandra ist eine NoSQL-Datenbank der Cloud-Größenordnung, die von der Apache Software Foundation unter "cassandra.apache.org" verwaltet wird. Cassandra ist in Java geschrieben und kann daher auf Windows- und auf Linux-Plattformen ausgeführt werden.

Dieser Artikel soll schwerpunktmäßig die Cassandra-Bereitstellung unter Ubuntu als Cluster in einem oder in mehreren Rechenzentren unter Nutzung von Microsoft Azure Virtual Machines und Virtual Networks zeigen. Die Clusterbereitstellung für produktionsoptimierte Workloads ist nicht Gegenstand dieses Artikels, weil Knotenkonfigurationen mit mehreren Datenträgern, ein geeigneter Ringtopologie-Entwurf und Datenmodellierung zur Unterstützung der Anforderungen an die Replikation, die Datenkonsistenz, den Durchsatz und die hohe Verfügbarkeit erforderlich sind.

Dieser Artikel zeigt grundlegende Vorgehensweisen, um zu zeigen, welche Schritte zum Erstellen des Cassandra-Clusters im Vergleich zu Docker, Chef oder Puppet erforderlich sind. Dies kann die Bereitstellung der Infrastruktur entscheidend vereinfachen.

## Bereitstellungsmodelle 
Microsoft Azure-Netzwerke ermöglichen die Bereitstellung von isolierten privaten Clustern. Der Zugriff auf diese kann eingeschränkt werden, um differenzierte Netzwerksicherheit zu erzielen. Da in diesem Artikel die Cassandra-Bereitstellung auf einer grundlegenden Ebene gezeigt wird, werden die Konsistenzebene und der optimale Speicherentwurf für den Durchsatz nicht behandelt. Die folgende Liste nennt die Netzwerkanforderungen für unseren hypothetischen Cluster:

- Externe Systeme können nicht von innerhalb oder außerhalb von Azure auf die Cassandra-Datenbank zugreifen.
- Der Cassandra-Cluster muss sich hinter einem Lastenausgleichsmodul für Thrift-Datenverkehr befinden.
- Bereitstellen von Cassandra-Knoten in zwei Gruppen in jedem Rechenzentrum für eine optimierte Clusterverfügbarkeit 
- Sperren des Clusters, damit nur die Anwendungsserverfarm direkten Zugriff auf die Datenbank besitzt
- Keine anderen öffentlichen Netzwerkendpunkte als SSH-Endpunkte
- Jeder Cassandra-Knoten benötigt eine feste interne IP-Adresse.

Cassandra kann in einer einzelnen Azure-Region oder in mehreren Regionen basierend auf der dezentralen Natur des Workloads bereitgestellt werden. Das Bereitstellungsmodell für mehrere Regionen kann genutzt werden, um Endbenutzer in geringerer Entfernung zu einem bestimmten geografischen Ort über die gleiche Cassandra-Infrastruktur zu bedienen. Die in Cassandra integrierte Knotenreplikation übernimmt die Synchronisierung von mehreren Masterschreibvorgängen, die aus mehreren Rechenzentren stammen, und stellt Anwendungen eine konsistente Sicht der Daten zur Verfügung. Die Bereitstellung für mehrere Regionen kann auch die Risikominimierung von weit reichenden Azure-Dienstausfällen unterstützen. Die optimierbare Konsistenz- und Replikationstopologie von Cassandra kann verschiedene RPO-Anforderungen von Anwendungen erfüllen.

### Bereitstellung in einer Region
Wir beginnen mit der Bereitstellung in einer Region und nutzen dann die gewonnenen Erfahrungen beim Erstellen eines Modells für mehrere Regionen. Virtuelle Azure-Netzwerke werden zum Erstellen isolierter Subnetze verwendet, damit die oben genannten Anforderungen an die Netzwerksicherheit erfüllt werden können. Der Vorgang, der zum Erstellen der Bereitstellung in einer Region beschrieben wird, verwendet Ubuntu 14.04 LTS und Cassandra 2.08. Der Vorgang kann jedoch problemlos für andere Linux-Varianten übernommen werden. Die folgenden systemischen Merkmale gelten für eine Bereitstellung in einer Region.

**Hohe Verfügbarkeit:** Die in Abbildung 1 gezeigten Cassandra-Knoten werden für zwei Verfügbarkeitsgruppen bereitgestellt, sodass die Knoten auf mehrere Fehlerdomänen verteilt sind und eine hohe Verfügbarkeit gewährleisten. Virtuelle Computer, die mit Anmerkungen zu jeder Verfügbarkeitsgruppe versehen sind, werden zwei Fehlerdomänen zugeordnet. Microsoft Azure verwendet das Konzept der Fehlerdomäne zum Verwalten ungeplanter Ausfallzeiten (z. B. Hardware- oder Softwarefehler) und das Konzept der Upgradedomäne (z. B. Host- oder Gastbetriebssystem-Patches und -Upgrades bzw. Anwendungsupgrades) zum Verwalten geplanter Ausfallzeiten. Welche Rolle Fehler- und Upgradedomänen beim Erreichen einer hohen Verfügbarkeit spielen, lesen Sie unter [Notfallwiederherstellung und hohe Verfügbarkeit für Azure-Anwendungen](http://msdn.microsoft.com/library/dn251004.aspx).

![Bereitstellung in einer Region](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

Abbildung 1: Bereitstellung in einer Region

Beachten Sie, dass Azure zum Zeitpunkt der Erstellung dieses Dokuments die explizite Zuordnung einer Gruppe von virtuellen Computern zu einer bestimmten Fehlerdomäne nicht zulässt. Daher ist es selbst mit dem Bereitstellungsmodell, das in Abbildung 1 dargestellt wird, statistisch möglich, dass alle virtuellen Computer ggf. zwei Fehlerdomänen anstelle von vier Fehlerdomänen zugeordnet werden.

**Lastenausgleich von Thrift-Datenverkehr:** Thrift-Clientbibliotheken innerhalb des Webservers stellen eine Verbindung mit dem Cluster über ein internes Lastenausgleichsmodul her. Dies erfordert, dass dem Subnetz "data" ein internes Lastenausgleichsmodul (siehe Abbildung 1) in Verbindung mit dem Clouddienst hinzugefügt wird, der den Cassandra-Cluster hostet. Nachdem das interne Lastenausgleichsmodul definiert wurde, muss jedem Knoten der Endpunkt mit Lastenausgleich mit den Anmerkungen einer Gruppe mit Lastenausgleich mit dem zuvor definierten Namen des Lastenausgleichsmoduls hinzugefügt werden. Weitere Details finden Sie unter [Interner Azure-Lastenausgleich](../load-balancer/load-balancer-internal-overview.md).

**Cluster-Seeds:** Es ist wichtig, dass die Knoten mit der höchsten Verfügbarkeit für Seeds ausgewählt werden, weil die neuen Knoten mit Seed-Knoten zum Ermitteln der Topologie des Clusters kommunizieren. Ein Knoten aus jeder Verfügbarkeitsgruppe wird als Seedknoten definiert, um eine einzelne Fehlerquelle zu vermeiden

**Replikationsfaktor und Konsistenzebene:** Die integrierte hohe Verfügbarkeit und Datendauerhaftigkeit von Cassandra ist durch den Replikationsfaktor (RF – Anzahl der Kopien jeder Zeile, die auf dem Cluster gespeichert sind) und die Konsistenzebene (Anzahl der Replikate, die gelesen/geschrieben werden sollen, bevor das Ergebnis an den Aufrufer zurückgegeben wird) charakterisiert. Der Replikationsfaktor wird während der KEYSPACE-Erstellung (ähnlich wie bei einer relationalen Datenbank) angegeben, während die Konsistenzebene beim Ausgeben der CRUD-Abfrage angegeben wird. Weitere Informationen zu den Konsistenzdetails sowie die Formel zum Berechnen des Quorums finden Sie in der Cassandra-Dokumentation unter [Konfiguration für Konsistenz](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html).

Cassandra unterstützt zwei Arten von Datenintegritätsmodellen – Konsistenz und Eventual Consistency. Sobald ein Schreibvorgang abgeschlossen wurde, bestimmen der Replikationsfaktor und die Konsistenzebene zusammen, ob die Daten konsistent sind oder ob Eventual Consistency vorliegt. Wenn z. B. QUORUM als Konsistenzebene angegeben wird, ist die Datenkonsistenz immer sichergestellt, während alle Konsistenzebenen, in denen weniger Replikate geschrieben werden, als für das Erreichen von QUORUM (z. B. ONE) erforderlich sind, zu Daten führen, die Eventual Consistency aufweisen.

Der oben dargestellte Cluster mit 8 Knoten, einem Replikationsfaktor von 3 und QUORUM (2 Knoten werden aus Konsistenzgründen gelesen bzw. geschrieben) können den theoretischen Verlust von höchstens einem Knoten pro Replikationsgruppe hinnehmen, bevor die Anwendung den Fehler bemerkt. Dabei wird davon ausgegangen, dass alle Keyspaces über gut ausgewogene Lese-/Schreibanforderungen verfügen. Die folgenden Parameter werden für den bereitgestellten Cluster verwendet:

Cassandra-Clusterkonfiguration mit einer Region:

| Clusterparameter | Wert | Anmerkungen |
| ----------------- | ----- | ------- |
| Anzahl der Knoten (N) | 8 | Gesamtzahl der Knoten im Cluster |
| Replikationsfaktor (RF) | 3 |	Die Anzahl der Replikate einer angegebenen Zeile |
| Konsistenzebene (Schreiben) | QUORUM [(RF/2) +1= 2] Das Ergebnis der Formel wird abgerundet | Schreibt höchstens zwei Replikate, bevor die Antwort an den Aufrufer gesendet wird. Das dritte Replikat wird als mit Eventual Consistency geschrieben. |
| Konsistenzebene (Lesen) | QUORUM [(RF/2) +1= 2] Das Ergebnis der Formel wird abgerundet] | Liest 2 Replikate vor dem Senden der Antwort an den Aufrufer. |
| Replikationsstrategie | NetworkTopologyStrategy Weitere Informationen finden Sie unter [Datenreplikation](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) in der Cassandra-Dokumentation. | Verstehen der Bereitstellungstopologie und Platzieren von Replikaten auf Knoten, damit sich letztlich nicht alle Replikate im gleichen Rack befinden. |
| Snitch | GossipingPropertyFileSnitch Weitere Informationen finden Sie unter [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) in der Cassandra-Dokumentation. | NetworkTopologyStrategy verwendet ein Snitch-Konzept, um die Topologie zu verstehen. GossipingPropertyFileSnitch bietet eine bessere Steuerung bei der Zuordnung der einzelnen Knoten zum Rechenzentrum und Rack. Der Cluster verwendet dann gossip, um diese Informationen zu verteilen. Dies ist viel einfacher in einer dynamischen IP-Einstellung im Vergleich zu PropertyFileSnitch. |


**Azure-Überlegungen zum Cassandra-Cluster:** Microsoft Azure Virtual Machines verwendet Azure-Blob-Speicher für die Datenträgerdauerhaftigkeit. Azure Storage speichert drei Replikate der Datenträger für hohe Dauerhaftigkeit. Dies bedeutet, dass jede Zeile mit Daten, die in eine Cassandra-Tabelle eingefügt wird, bereits in drei Replikaten gespeichert ist und somit die Datenkonsistenz bereits sichergestellt wurde, selbst wenn der Replikationsfaktor (RF) 1 ist. Das Hauptproblem beim Replikationsfaktor 1 besteht darin, dass bei der Anwendung bereits Ausfallzeiten auftreten, wenn nur ein Cassandra-Knoten ausfällt. Wenn ein Knoten jedoch aufgrund von durch Azure Fabric Controller erkannten Problemen (z. B. Hardware- oder Systemsoftwarefehlern) ausfällt, wird ein neuer Knoten an seiner Stelle mithilfe der gleichen Speicherlaufwerke bereitgestellt. Die Bereitstellung eines neuen Knotens zum Ersetzen des alten Knotens kann einige Minuten in Anspruch nehmen. Für geplante Wartungsaktivitäten (z. B. Änderungen am Gastbetriebssystem, Cassandra-Upgrades und Anwendungsänderungen) führt Azure Fabric Controller parallele Upgrades der Knoten im Cluster aus. Durch parallele Upgrades können ebenfalls mehrere Knoten gleichzeitig ausfallen. Der Cluster kann daher kurzzeitig für mehrere Partitionen ausfallen. Die Daten gehen jedoch durch die integrierte Redundanz von Azure Storage nicht verloren.

Für Systeme, die in Azure bereitgestellt werden und keine hohe Verfügbarkeit benötigen (z. B. ungefähr 99,9 % – entspricht 8,76 Stunden/Jahr, Details finden Sie unter [Hohe Verfügbarkeit](http://en.wikipedia.org/wiki/High_availability)), können Sie für die Ausführung ggf. RF=1 und Konsistenzebene=ONE verwenden. Für Anwendungen mit Anforderungen für hohe Verfügbarkeit können RF=3 und Konsistenzebene=QUORUM den Ausfall eines der Knoten für eines der Replikate tolerieren. RF=1 in herkömmlichen Bereitstellungen (z. B. lokal) kann aufgrund möglicher Datenverluste nicht verwendet werden, die durch Probleme wie etwa Datenträgerfehler verursacht werden.

## Bereitstellung in mehreren Regionen
Das oben beschriebene auf das Rechenzentrum ausgelegte Replikations- und Konsistenzmodell von Cassandra unterstützt die integrierte Bereitstellung in mehreren Regionen ohne externe Tools. Dies unterscheidet sich wesentlich von den herkömmlichen relationalen Datenbanken, in denen die Einrichtung der Datenbankspiegelung für Multimasterschreibvorgänge sehr komplex sein kann. Die Einrichtung von Cassandra in mehreren Regionen kann z. B. die folgenden Nutzungsszenarien unterstützen:

**Ortsnahe Bereitstellung:** Mehrinstanzenfähige Anwendungen mit einer klaren Zuordnung von Mandantenbenutzern zu einer Region können von den geringen Latenzzeiten des Clusters in mehreren Regionen profitieren. Ein Ausbildungsverwaltungssystem für Bildungseinrichtungen kann beispielsweise einen verteilten Cluster in den Regionen USA (Ost) und USA (West) bereitstellen, um für die entsprechenden Universitäten Transaktionen und Analysen zur Verfügung zu stellen. Die Daten können für Lese- und Schreibvorgänge lokal konsistent sein und "Eventual Consistency" für beide Regionen aufweisen. Es gibt auch andere Beispiele, wie Medienverteilung, E-Commerce und alle anderen Szenarien, die sich auf eine geokonzentrierte Benutzerbasis beziehen. Dies sind gute Anwendungsbeispiele für dieses Bereitstellungsmodell.

**Hohe Verfügbarkeit:** Redundanz ist ein Schlüsselfaktor beim Erzielen hoher Verfügbarkeit für Software und Hardware. Einzelheiten finden Sie unter "Erstellen zuverlässiger Cloudsysteme unter Microsoft Azure". Unter Microsoft Azure besteht das einzig zuverlässige Verfahren zum Erreichen echter Redundanz in der Bereitstellung eines Clusters in mehreren Regionen. Anwendungen können in einem Aktiv/Aktiv- oder Aktiv/Passiv-Modus bereitgestellt werden. Wenn eine der Regionen ausfällt, kann Azure Traffic Manager Datenverkehr in die aktive Region umleiten. Bei einer Bereitstellung in einer Region mit einer Verfügbarkeit von 99,9 % kann eine Bereitstellung in zwei Regionen eine Verfügbarkeit von 99,9999 % erzielen. Dieser Wert wird mithilfe der folgenden Formel berechnet: (1-(1-0,999) * (1-0,999))*100). Weitere Informationen finden Sie im vorangegangenen Artikel.

**Notfallwiederherstellung:** Ein Cassandra-Cluster in mehreren Regionen kann bei einem ordnungsgemäßen Entwurf schwerwiegenden Rechenzentrumsausfällen standhalten. Wenn eine Region ausfällt, kann die in anderen Regionen bereitgestellte Anwendung Endbenutzer bedienen. Wie alle anderen Implementierungen für Geschäftskontinuität muss die Anwendung tolerieren, dass einige Daten aufgrund der Daten in der asynchronen Pipeline verloren gehen. Durch Cassandra erfolgt die Wiederherstellung jedoch wesentlich schneller als bei den Wiederherstellungsvorgängen herkömmlicher Datenbanken. Abbildung 2 zeigt ein typisches Bereitstellungsmodell in mehreren Regionen mit acht Knoten in jeder Region. Beide Regionen sind Spiegelbilder voneinander für die gleiche Symmetrie. Echte Entwürfe hängen von den Anforderungen im Hinblick auf die Art des Workloads (z. B. Transaktions- oder Analysedaten), RPO, RTO, Datenkonsistenz und Verfügbarkeit ab.

![Bereitstellung in mehreren Regionen](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

Abbildung 2: Cassandra-Bereitstellung in mehreren Regionen

### Netzwerkintegration
Gruppen von virtuellen Computern, die in privaten Netzwerken in zwei Regionen bereitgestellt werden, kommunizieren miteinander über einen VPN-Tunnel. Der VPN-Tunnel verbindet zwei Softwaregateways, die während des Netzwerkbereitstellungsvorgangs bereitgestellt werden. Beide Regionen weisen eine ähnliche Netzwerkarchitektur in Bezug auf die Subnetze für "Web" und "Daten" auf. Azure-Netzwerke ermöglichen das Erstellen einer beliebigen Anzahl von Subnetzen nach Bedarf sowie das Anwenden von ACLs gemäß der Netzwerksicherheit. Beim Entwerfen der Clustertopologie müssen die Kommunikationslatenz zwischen Rechenzentren und die wirtschaftlichen Auswirkungen des Netzwerkdatenverkehrs berücksichtigt werden.

### Datenkonsistenz für eine Bereitstellung in mehreren Rechenzentren
Verteilte Bereitstellungen müssen die Auswirkungen der Clustertopologie auf den Durchsatz und die hohe Verfügbarkeit berücksichtigen. Der RF und die Konsistenzebene müssen so gewählt werden, dass die Quorumressource nicht von der Verfügbarkeit aller Rechenzentren abhängig ist. Bei einem System, das hohe Konsistenz benötigt, stellt ein LOCAL\_QUORUM als Konsistenzebene (für Lese- und Schreibvorgänge) sicher, dass die lokalen Lese- und Schreibvorgänge von den lokalen Knoten ausgeführt werden, während Daten asynchron in Remoterechenzentren repliziert werden. In Tabelle 2 werden die Konfigurationsdetails für den Cluster in mehreren Regionen zusammengefasst, die weiter unten in diesem Dokument beschrieben werden.

**Cassandra-Clusterkonfiguration in zwei Regionen**


| Clusterparameter | Wert | Anmerkungen |
| ----------------- | ----- | ------- |
| Anzahl der Knoten (N) | 8 + 8 | Gesamtzahl der Knoten im Cluster |
| Replikationsfaktor (RF) | 3 | Die Anzahl der Replikate einer angegebenen Zeile |
| Konsistenzebene (Schreiben) | LOCAL\_QUORUM [(Summe(RF)/2) +1) = 4] Das Ergebnis der Formel wird abgerundet | Zwei Knoten werden synchron in das erste Rechenzentrum geschrieben. Die weiteren zwei Knoten, die für das Quorum benötigt werden, werden asynchron in das zweite Rechenzentrum geschrieben. |
| Konsistenzebene (Lesen) | LOCAL\_QUORUM ((RF/2) + 1) = 2 Das Ergebnis der Formel wird abgerundet. | Leseanforderungen werden nur von einer Region erfüllt. Zwei Knoten werden gelesen, bevor die Antwort zurück an den Client gesendet wird. |
| Replikationsstrategie | NetworkTopologyStrategy Weitere Informationen finden Sie unter [Datenreplikation](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) in der Cassandra-Dokumentation. | Verstehen der Bereitstellungstopologie und Platzieren von Replikaten auf Knoten, damit sich letztlich nicht alle Replikate im gleichen Rack befinden. |
| Snitch | GossipingPropertyFileSnitch Weitere Informationen finden Sie unter [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) in der Cassandra-Dokumentation. | NetworkTopologyStrategy verwendet ein Snitch-Konzept, um die Topologie zu verstehen. GossipingPropertyFileSnitch bietet eine bessere Steuerung bei der Zuordnung der einzelnen Knoten zum Rechenzentrum und Rack. Der Cluster verwendet dann gossip, um diese Informationen zu verteilen. Dies ist viel einfacher in einer dynamischen IP-Einstellung im Vergleich zu PropertyFileSnitch. | 
 

##SOFTWAREKONFIGURATION
Die folgenden Softwareversionen werden während der Bereitstellung verwendet:

<table>
<tr><th>Software</th><th>Quelle</th><th>Version</th></tr>
<tr><td>JRE	</td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA	</td><td>[JNA] (https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu	</td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Da für das Herunterladen von JRE eine manuelle Zustimmung zur Oracle-Lizenz erforderlich ist, sollten Sie zur Vereinfachung der Bereitstellung die gesamte erforderliche Software auf den Desktop herunterladen, um sie später in das Ubuntu-Vorlagenimage hochzuladen, das als Vorstufe der Clusterbereitstellung erstellt wird.

Laden Sie die oben aufgeführte Software in ein bekanntes Downloadverzeichnis (z. B. "%TEMP%/downloads" unter Windows oder "~/downloads" unter den meisten Linux-Distributionen oder Mac) auf den lokalen Computer herunter.

### ERSTELLEN DES VIRTUELLEN UBUNTU-COMPUTERS
In diesem Schritt des Vorgangs wird das Ubuntu-Image mit der erforderlichen Software erstellt, damit das Image für die Bereitstellung von mehreren Cassandra-Knoten wiederverwendet werden kann.
####Schritt 1: Generieren eines SSH-Schlüsselpaars
Azure benötigt einen öffentlichen X509-Schlüssel, der bei seiner Bereitstellung PEM- oder DER-codiert ist. Erstellen Sie ein öffentlich/privates Schlüsselpaar, indem Sie die Anleitungen unter "Verwenden von SSH mit Linux in Azure" befolgen. Wenn Sie "putty.exe" als SSH-Client unter Windows oder Linux verwenden möchten, müssen Sie den PEM-codierten privaten RSA-Schlüssel mithilfe von "puttygen.exe" in das PPK-Format konvertieren. Anleitungen dazu finden Sie auf der oben genannten Webseite.

####Schritt 2: Erstellen des virtuellen Ubuntu-Vorlagencomputers
Melden Sie sich zum Erstellen des virtuellen Vorlagencomputers beim klassischen Azure-Portal an, und führen Sie dann die folgenden Schritte aus: Klicken Sie auf NEU, COMPUTE, VIRTUELLER COMPUTER, AUS KATALOG, UBUNTU, Ubuntu Server 14.04 LTS und dann auf den Pfeil nach rechts. Ein Lernprogramm, das die Erstellung eines virtuellen Linux-Computers beschreibt, finden Sie unter "Erstellen eines virtuellen Linux-Computers".

Geben Sie die folgenden Informationen auf dem Bildschirm Nr. 1 "Konfiguration des virtuellen Computers" ein:

<table>
<tr><th>FELDNAME              </td><td>       FELDWERT               </td><td>         ANMERKUNGEN                </td><tr>
<tr><td>VERÖFFENTLICHUNGSDATUM DER VERSION    </td><td> Wählen Sie ein Datum aus der Dropdown-Liste aus</td><td></td><tr>
<tr><td>NAME DES VIRTUELLEN COMPUTERS    </td><td> cass-template	               </td><td> Dies ist der Hostname des virtuellen Computers. </td><tr>
<tr><td>EBENE	                 </td><td> STANDARD	                       </td><td> Behalten Sie die Standardwerte bei.              </td><tr>
<tr><td>GRÖSSE	                 </td><td> A1                              </td><td>Wählen Sie den virtuellen Computer anhand der E/A-Anforderungen aus. Behalten Sie zu diesem Zweck die Standardwerte bei. </td><tr>
<tr><td> NEUER BENUTZERNAME	         </td><td> localadmin	                   </td><td> "admin" ist ein reservierter Benutzername in Ubuntu 12.xx oder höher.</td><tr>
<tr><td> AUTHENTIFIZIERUNG	     </td><td> Aktivieren Sie das Kontrollkästchen.                 </td><td>Aktivieren Sie es, wenn Sie die Authentifizierung mit einem SSH-Schlüssel sichern möchten. </td><tr>
<tr><td> ZERTIFIKAT	         </td><td> Der Dateiname des Zertifikats für den öffentlichen Schlüssel. </td><td> Verwenden Sie den zuvor erstellten öffentlichen Schlüssel.</td><tr>
<tr><td> Neues Kennwort	</td><td> sicheres Kennwort </td><td> </td><tr>
<tr><td> Kennwort bestätigen	</td><td> Sicheres Kennwort </td><td></td><tr>
</table>

Geben Sie die folgenden Informationen im Bildschirm Nr. 2 "Konfiguration des virtuellen Computers" ein:

<table>
<tr><th>FELDNAME             </th><th> FELDWERT	                   </th><th> ANMERKUNGEN                                 </th></tr>
<tr><td> CLOUDDIENST	</td><td> Einen neuen Clouddienst erstellen	</td><td>Der Clouddienst ist eine Containerserverressource wie virtuelle Computer.</td></tr>
<tr><td> DNS-NAME DES CLOUDDIENSTS	</td><td>ubuntu-template.cloudapp.net	</td><td>Geben Sie dem Lastenausgleichsmodul einen dem Computer nicht bekannten Namen.</td></tr>
<tr><td> REGION/AFFINITY GROUP/VIRTUAL NETWORK </td><td>	USA (West)	</td><td> Wählen Sie eine Region aus, in der Ihre Webanwendungen auf den Cassandra-Cluster zugreifen.</td></tr>
<tr><td>STORAGE ACCOUNT </td><td>	Verwenden Sie den Standardwert.	</td><td>Verwenden Sie das Standardspeicherkonto oder ein zuvor erstelltes Speicherkonto in einer bestimmten Region.</td></tr>
<tr><td>VERFÜGBARKEITSGRUPPE </td><td>	Keine </td><td>	Lassen Sie diese Angabe leer.</td></tr>
<tr><td>ENDPUNKTE	</td><td>Verwenden Sie den Standardwert. </td><td>	Verwenden Sie die SSH-Standardkonfiguration. </td></tr>
</table>

Klicken Sie auf den Pfeil nach rechts, behalten Sie die Standardwerte auf dem Bildschirm Nr. 3 bei, und klicken Sie dann auf die Schaltfläche mit dem Häkchen, um den Bereitstellungsvorgang für den virtuellen Computer abzuschließen. Nach einigen Minuten sollte der virtuelle Computer mit dem Namen "ubuntu-template" den Status "Wird ausgeführt" aufweisen.

###INSTALLIEREN DER ERFORDERLICHEN SOFTWARE
####Schritt 1: Hochladen von Tarballs 
Kopieren Sie die zuvor heruntergeladene Software mit scp oder pscp mithilfe des folgenden Befehlsformats in das Verzeichnis "~/downloads":

#####pscp server-jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

Wiederholen Sie den oben angegebenen Befehl für JRE sowie für die Cassandra-Komponenten.

####Schritt 2: Vorbereiten der Verzeichnisstruktur und Extrahieren der Archive
Melden Sie sich am virtuellen Computer an, und erstellen Sie die Verzeichnisstruktur. Extrahieren Sie die Software als Superuser mit dem unten gezeigten Bash-Skript:

	#!/bin/bash
	CASS_INSTALL_DIR="/opt/cassandra"
	JRE_INSTALL_DIR="/opt/java"
	CASS_DATA_DIR="/var/lib/cassandra"
	CASS_LOG_DIR="/var/log/cassandra"
	DOWNLOADS_DIR="~/downloads"
	JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
	CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
	SVC_USER="localadmin"
	
	RESET_ERROR=1
	MKDIR_ERROR=2
	
	reset_installation ()
	{
	   rm -rf $CASS_INSTALL_DIR 2> /dev/null
	   rm -rf $JRE_INSTALL_DIR 2> /dev/null
	   rm -rf $CASS_DATA_DIR 2> /dev/null
	   rm -rf $CASS_LOG_DIR 2> /dev/null
	}
	make_dir ()
	{
	   if [ -z "$1" ]
	   then
	      echo "make_dir: invalid directory name"
	      exit $MKDIR_ERROR
	   fi
	   
	   if [ -d "$1" ]
	   then
	      echo "make_dir: directory already exists"
	      exit $MKDIR_ERROR
	   fi
	
	   mkdir $1 2>/dev/null
	   if [ $? != 0 ]
	   then
	      echo "directory creation failed"
	      exit $MKDIR_ERROR
	   fi
	}
	
	unzip()
	{
	   if [ $# == 2 ]
	   then
	      tar xzf $1 -C $2
	   else
	      echo "archive error"
	   fi
	   
	}
	
	if [ -n "$1" ]
	then
	   SVC_USER=$1
	fi
	
	reset_installation 
	make_dir $CASS_INSTALL_DIR
	make_dir $JRE_INSTALL_DIR
	make_dir $CASS_DATA_DIR
	make_dir $CASS_LOG_DIR
	
	#unzip JRE and Cassandra 
	unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
	unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR
	
	#Change the ownership to the service credentials
	
	chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
	chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
	echo "edit /etc/profile to add JRE to the PATH"
	echo "installation is complete"


Wenn Sie dieses Skript in das vim-Fenster einfügen, stellen Sie sicher, dass Sie den Wagenrücklauf ("\\r") mithilfe des folgenden Befehls entfernen:

	tr -d '\r' <infile.sh >outfile.sh

####Schritt 3: Bearbeiten von "etc/profile"
Fügen Sie Folgendes am Ende an:

	JAVA_HOME=/opt/java/jdk1.8.0_05 
	CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
	PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
	export JAVA_HOME
	export CASS_HOME
	export PATH

####Schritt 4: Installieren von JNA für die Produktionssysteme
Verwenden Sie die folgende Befehlssequenz: Der Befehl installiert jna-3.2.7.jar und jna-platform-3.2.7.jar im Verzeichnis /usr/share.java directory sudo apt-get install libjna-java

Erstellen Sie symbolische Links im Verzeichnis "$CASS\_HOME/lib", damit das Cassandra-Startskript diese JAR-Dateien finden kann:

	ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

	ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####Schritt 5: Konfigurieren von "cassandra.yaml"
Bearbeiten Sie "cassandra.yaml" für jeden virtuellen Computer entsprechend der Konfiguration, die von allen virtuellen Computern benötigt wird. [Wir werden dies während der eigentlichen Bereitstellung optimieren]:

<table>
<tr><th>Feldname   </th><th> Wert  </th><th>	Anmerkungen </th></tr>
<tr><td>cluster_name </td><td>	"CustomerService"	</td><td> Verwenden Sie den Namen, der Ihre Bereitstellung angibt.</td></tr> 
<tr><td>listen_address	</td><td>[Leer lassen]	</td><td> Löschen Sie "localhost". </td></tr>
<tr><td>rpc_address   </td><td>[Leer lassen]	</td><td> Löschen Sie "localhost". </td></tr>
<tr><td>seeds	</td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"	</td><td>Eine Liste aller IP-Adressen, die als Ausgangswerte gekennzeichnet sind.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Wird von NetworkTopologyStrategy zum Ableiten des Rechenzentrums und Racks des virtuellen Computers verwendet.</td></tr>
</table>

####Schritt 6: Erfassen des Images des virtuellen Computers
Melden Sie sich am virtuellen Computer mit dem Hostnamen ("hk-cas-template.cloudapp.net") und dem zuvor erstellten privaten SSH-Schlüssel an. Ausführliche Anleitungen zum Anmelden mit dem Befehl "ssh" oder mit "putty.exe" finden Sie unter "Verwenden von SSH mit Linux in Azure".

Führen Sie die folgende Aktionssequenz aus, um das Image zu erfassen:
#####1\. Aufheben der Bereitstellung
Verwenden Sie den Befehl "sudo waagent –deprovision+user", um Informationen zu entfernen, die für die Instanz des virtuellen Computers spezifisch sind. Weitere Details zur Image-Erfassung finden Sie unter [Gewusst wie: Erfassen eines virtuellen Linux-Computers, um ihn als Vorlage zu verwenden](virtual-machines-linux-capture-image.md).

#####2: Herunterfahren des virtuellen Computers
Stellen Sie sicher, dass der virtuelle Computer markiert ist, und klicken Sie dann unten auf der Befehlsleiste auf den Link "HERUNTERFAHREN".

#####3: Erfassen des Image
Stellen Sie sicher, dass der virtuelle Computer markiert ist, und klicken Sie dann unten auf der Befehlsleiste auf den Link "AUFNEHMEN". Geben Sie im nächsten Bildschirm einen IMAGENAMEN (z. B. "hk-cas-2-08-ub-14-04-2014071") und eine geeignete IMAGEBESCHREIBUNG an, und klicken Sie dann auf das Häkchen, um den ERFASSUNGSVORGANG zu beenden.

Nach einigen Sekunden sollte das Image im Abschnitt "EIGENE IMAGES" des Image-Katalogs verfügbar sein. Der virtuelle Quellcomputer wird automatisch gelöscht, nachdem das Image erfolgreich erfasst wurde.

##Bereitstellungsvorgang in einer Region
**Schritt 1: Erstellen des virtuellen Netzwerks** Melden Sie sich beim klassischen Azure-Portal an, und erstellen Sie ein virtuelles Netzwerk mit den in der Tabelle angegebenen Attributen. Eine ausführliche Beschreibung der erforderlichen Schritte finden Sie unter [Konfigurieren eines virtuellen Netzwerks nur für die Cloud im klassischen Azure-Portal](../virtual-network/virtual-networks-create-vnet-classic-portal.md).

<table>
<tr><th>Attributname des virtuellen Computers</th><th>Wert</th><th>Anmerkungen</th></tr>
<tr><td>Name</td><td>vnet-cass-west-us</td><td></td></tr>	
<tr><td>Region</td><td>USA (West)</td><td></td></tr>	
<tr><td>DNS-Server	</td><td>Keine</td><td>Ignorieren Sie diese Angabe, da wir keinen DNS-Server verwenden.</td></tr>
<tr><td>Punkt-zu-Site-VPN konfigurieren</td><td>Keine</td><td> Ignorieren Sie diese Angabe.</td></tr>
<tr><td>Site-to-Site-VPN konfigurieren</td><td>Keiner</td><td> Ignorieren Sie diese Angabe.</td></tr>
<tr><td>Adressraum</td><td>10.1.0.0/16</td><td></td></tr>	
<tr><td>Start-IP</td><td>10.1.0.0</td><td></td></tr>	
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Fügen Sie die folgenden Subnetze hinzu:

<table>
<tr><th>Name</th><th>Start-IP</th><th>CIDR</th><th>Anmerkungen</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Das Subnetz für die Webfarm.</td></tr>
<tr><td>data</td><td>10.1.2.0</td><td>/24 (251)</td><td>Das Subnetz für die Datenbankknoten.</td></tr>
</table>

Die Daten- und Websubnetze können mithilfe von Netzwerksicherheitsgruppen geschützt werden. Dies wird in diesem Artikel jedoch nicht behandelt.

**Schritt 2: Bereitstellen virtueller Computer** Mithilfe des zuvor erstellten Image erstellen Sie die folgenden virtuellen Computer auf dem Cloudserver "hk-c-svc-west" und binden diese wie unten dargestellt an die jeweiligen Subnetze:

<table>
<tr><th>Computername    </th><th>Subnetz	</th><th>IP-Adresse	</th><th>Verfügbarkeitsgruppe</th><th>DC/Rack</th><th>Ausgangswert?</th></tr>
<tr><td>hk-c1-west-us	</td><td>data	</td><td>10.1.2.4	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1 </td><td>Ja</td></tr>
<tr><td>hk-c2-west-us	</td><td>data	</td><td>10.1.2.5	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1	</td><td>Nein </td></tr>
<tr><td>hk-c3-west-us	</td><td>data	</td><td>10.1.2.6	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>Ja</td></tr>
<tr><td>hk-c4-west-us	</td><td>data	</td><td>10.1.2.7	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>Nein </td></tr>
<tr><td>hk-c5-west-us	</td><td>data	</td><td>10.1.2.8	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>Ja</td></tr>
<tr><td>hk-c6-west-us	</td><td>data	</td><td>10.1.2.9	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>Nein </td></tr>
<tr><td>hk-c7-west-us	</td><td>data	</td><td>10.1.2.10	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>Ja</td></tr>
<tr><td>hk-c8-west-us	</td><td>data	</td><td>10.1.2.11	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>Nein </td></tr>
<tr><td>hk-w1-west-us	</td><td>web	</td><td>10.1.1.4	</td><td>hk-w-aset-1	</td><td>                       </td><td>–</td></tr>
<tr><td>hk-w2-west-us	</td><td>web	</td><td>10.1.1.5	</td><td>hk-w-aset-1	</td><td>                       </td><td>–</td></tr>
</table>

Für das Erstellen der oben aufgeführten Liste der virtuellen Computer ist der folgende Vorgang erforderlich:

1.  Erstellen eines leeren Clouddiensts in einer bestimmten Region
2.	Erstellen eines virtuellen Computers aus dem zuvor erfassten Image und Anfügen des Images an das zuvor erstellte virtuelle Netzwerk. Wiederholen Sie diese Schritte für alle virtuellen Computer.
3.	Hinzufügen eines internen Lastenausgleichsmoduls zum Clouddienst und Anfügen des Moduls an das Subnetz "data"
4.	Hinzufügen eines Endpunkts mit Lastenausgleich für Thrift-Datenverkehr über eine Gruppe mit Lastenausgleich, die mit dem zuvor erstellten internen Lastenausgleichsmodul verbunden ist, für jeden virtuellen Computer, der zuvor erstellt wurde.

Das oben beschriebene Verfahren kann über das klassische Azure-Portal ausgeführt werden. Verwenden Sie einen Windows-Computer (wenn Sie keinen Zugriff auf einen Windows-Computer besitzen, verwenden Sie einen virtuellen Computer in Azure). Stellen Sie alle acht virtuellen Computer automatisch mithilfe des folgenden PowerShell-Skripts bereit.

**Liste 1: PowerShell-Skript für die Bereitstellung virtueller Maschinen**
		
		#Tested with Azure Powershell - November 2014	
		#This powershell script deployes a number of VMs from an existing image inside an Azure region
		#Import your Azure subscription into the current Powershell session before proceeding
		#The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template
		
		#fundamental variables - change these to reflect your subscription
		$country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
		$numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
		$subscriptionName = "Azure_subscription_name"; 
		$vmSize="ExtraSmall"; $imageName="your_linux_image_name"
		$ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"
		
		#generated variables
		$serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"
		
		$vmNames = @()
		for ($i=0; $i -lt $numVMs; $i++)
		{
		   $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
		}
		
		#select an Azure subscription already imported into Powershell session
		Select-AzureSubscription -SubscriptionName $subscriptionName -Current
		Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount
		
		#create an empty cloud service
		New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
		Write-Host "Created $serviceName"
		
		$VMList= @()   # stores the list of azure vm configuration objects
		#create the list of VMs
		foreach($vmName in $vmNames)
		{
		   $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
		   Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
		   Set-AzureSubnet "data"
		}
		
		New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList
		
		#Create internal load balancer
		Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
		Write-Host "Created $ilbName"
		#Add add the thrift endpoint to the internal load balancer for all the VMs
		foreach($vmName in $vmNames)
		{
		    Get-AzureVM -ServiceName $serviceName -Name $vmName |
		        Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName | 
		        Update-AzureVM 
		
		    Write-Host "created $vmName"     
		}

**Schritt 3: Konfigurieren von Cassandra auf jedem virtuellen Computer**

Melden Sie sich am virtuellen Computer an, und gehen Sie dann wie folgt vor:

* Bearbeiten Sie "$CASS\_HOME/conf/cassandra-rackdc.properties", um die Rechenzentrums- und Rackeigenschaften anzugeben:
      
       dc =EASTUS, rack =rack1

* Bearbeiten Sie "cassandra.yaml", um die Seedknoten wie unten gezeigt zu konfigurieren:
     
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Schritt 4: Starten der virtuellen Computer und Testen des Clusters**

Melden Sie sich an einem der Knoten (z. B. "hk-c1-west-us") an, und führen Sie dann den folgenden Befehl aus, um den Status des Clusters anzuzeigen:
       
       nodetool –h 10.1.2.4 –p 7199 status 

Die Anzeige sollte für einen Cluster mit acht Knoten ähnlich der Abbildung unten sein:

<table>
<tr><th>Status</th></th>Adresse	</th><th>Last	</th><th>Token	</th><th>Besitzer </th><th>Host-ID	</th><th>Rack</th></tr>
<tr><th>UN	</td><td>10.1.2.4 	</td><td>87.81 KB	</td><td>256	</td><td>38,0&#160;%	</td><td>GUID (entfernt)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.5 	</td><td>41.08 KB	</td><td>256	</td><td>68,9&#160;%	</td><td>GUID (entfernt)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.6 	</td><td>55.29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (entfernt)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.7 	</td><td>55.29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (entfernt)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.8 	</td><td>55.29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (entfernt)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.9 	</td><td>55.29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (entfernt)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.10 	</td><td>55.29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (entfernt)</td><td>rack4</td></tr>
<tr><th>UN	</td><td>10.1.2.11 	</td><td>55.29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (entfernt)</td><td>rack4</td></tr>
</table>

## Testen des Clusters in einer Region
Verwenden Sie die folgenden Schritte zum Testen des Clusters:

1.    Rufen Sie die IP-Adresse des internen Lastenausgleichsmoduls mithilfe des PowerShell-Cmdlets "Get-AzureInternalLoadbalancer" ab (z. B. "10.1.2.101"). Die Syntax des Befehls wird unten gezeigt: Get-AzureLoadbalancer -ServiceName "hk-c-svc-west-us" [zeigt die Details des internen Lastenausgleichsmoduls zusammen mit seiner IP-Adresse an].
2.	Melden Sie sich am virtuellen Webfarmcomputer (z. B. "hk-w1-west-us") mithilfe von Putty oder ssh an.
3.	Führen Sie "$CASS\_HOME/bin/cqlsh 10.1.2.101 9160" aus. 
4.	Verwenden Sie die folgenden CQL-Befehle, um zu überprüfen, ob der Cluster funktionsfähig ist:

		CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };	
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		
		SELECT * FROM Customers;

Die Anzeige sollte der Abbildung unten ähneln:

<table>
  <tr><th> customer_id </th><th> firstname </th><th> lastname </th></tr>
  <tr><td> 1 </td><td> John </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Doe </td></tr>
</table>

Bitte beachten Sie, dass der in Schritt 4 erstellte Keyspace SimpleStrategy mit einem Replikationsfaktor von 3 verwendet. SimpleStrategy wird für eine einzelne Rechenzentrumsbereitstellungen empfohlen, während NetworkTopologyStrategy für Bereitstellungen in mehreren Rechenzentren empfohlen wird. Ein Replikationsfaktor von 3 toleriert Knotenausfälle.

##<a id="tworegion"> </a>Bereitstellung für mehrere Regionen
Bei diesem Verfahren wird die abgeschlossene Bereitstellung in einer Region genutzt und der gleiche Vorgang für die Installation in der zweiten Region wiederholt. Der Hauptunterschied zwischen einer Bereitstellung in einer Region und in mehreren Regionen besteht in der Einrichtung des VPN-Tunnels für die Kommunikation zwischen den Regionen. Wir beginnen mit der Netzwerkinstallation, stellen die virtuellen Computer bereit und konfigurieren dann Cassandra.

###Schritt 1: Erstellen des virtuellen Netzwerks in der zweiten Region
Melden Sie sich beim klassischen Azure-Portal an, und erstellen Sie ein virtuelles Netzwerk mit den in der Tabelle angegebenen Attributen. Eine ausführliche Beschreibung der erforderlichen Schritte finden Sie unter [Konfigurieren eines virtuellen Netzwerks nur für die Cloud im klassischen Azure-Portal](../virtual-network/virtual-networks-create-vnet.md).

<table>
<tr><th>Attributname    </th><th>Wert	</th><th>Anmerkungen</th></tr>
<tr><td>Name	</td><td>vnet-cass-east-us</td><td></td></tr>	
<tr><td>Region	</td><td>USA (Ost)</td><td></td></tr>	
<tr><td>DNS-Server		</td><td></td><td>Ignorieren Sie diese Angabe, da wir keinen DNS-Server verwenden.</td></tr>
<tr><td>Punkt-zu-Site-VPN konfigurieren</td><td></td><td>		Ignorieren Sie diese Angabe.</td></tr>
<tr><td>Site-to-Site-VPN konfigurieren</td><td></td><td>		Ignorieren Sie diese Angabe.</td></tr>
<tr><td>Adressraum	</td><td>10.2.0.0/16</td><td></td></tr>	
<tr><td>Start-IP	</td><td>10.2.0.0	</td><td></td></tr>
<tr><td>CIDR	</td><td>/16 (65531)</td><td></td></tr>
</table>

Fügen Sie die folgenden Subnetze hinzu: <table> <tr><th>Name </th><th>Start-IP </th><th>CIDR </th><th>Anmerkungen</th></tr> <tr><td>web </td><td>10.2.1.0 </td><td>/24 (251) </td><td>Subnetz für die Webfarm</td></tr> <tr><td>data </td><td>10.2.2.0 </td><td>/24 (251) </td><td>Subnetz für die Datenbankknoten</td></tr> </table>


###Schritt 2: Erstellen lokaler Netzwerke
Ein lokales Netzwerk in virtuellen Azure-Netzwerken ist ein Proxyadressraum, der einem Remotestandort (einschließlich einer privaten Cloud) oder einer anderen Azure-Region zugeordnet ist. Dieser Proxyadressraum ist an ein Remotegateway für das Routing des Netzwerks an die richtigen Netzwerkziele gebunden. Anleitungen zum Einrichten einer VNET-zu-VNET-Verbindung finden Sie unter [Konfigurieren einer VNet-zu-VNet-Verbindung](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

Erstellen Sie zwei lokale Netzwerke mit den folgenden Details:

| Netzwerkname | Adresse des VPN-Gateways | Adressraum | Anmerkungen |
| ------------ | ------------------- | ------------- | ------- |
| hk-lnet-map-to-east-us | 23\.1.1.1 | 10\.2.0.0/16 | Geben Sie beim Erstellen des lokalen Netzwerks eine Platzhalter-Gatewayadresse an. Die tatsächliche Gatewayadresse wird angegeben, nachdem das Gateway erstellt wurde. Stellen Sie sicher, dass der Adressraum mit dem entsprechenden Remote-VNET genau übereinstimmt. In diesem Fall wird das VNET in der Region "USA (Ost)" erstellt. |
| hk-lnet-map-to-west-us | 23\.2.2.2 | 10\.1.0.0/16 | Geben Sie beim Erstellen des lokalen Netzwerks eine Platzhalter-Gatewayadresse an. Die tatsächliche Gatewayadresse wird angegeben, nachdem das Gateway erstellt wurde. Stellen Sie sicher, dass der Adressraum mit dem entsprechenden Remote-VNET genau übereinstimmt. In diesem Fall wird das VNET in der Region "USA West" erstellt. |


###Schritt 3: Zuordnen des "lokalen" Netzwerks zu den entsprechenden VNETs
Wählen Sie im klassischen Azure-Portal jedes VNet aus, klicken Sie auf „Konfigurieren“, aktivieren Sie „Eine Verbindung mit dem lokalen Netzwerk herstellen“, und wählen Sie dann die lokalen Netzwerke mit den folgenden Details aus:


| Virtual Network | Lokales Netzwerk |
| --------------- | ------------- |
| hk-vnet-west-us | hk-lnet-map-to-east-us |
| hk-vnet-east-us | hk-lnet-map-to-west-us |


###Schritt 4: Erstellen von Gateways für VNET1 und VNET2
Klicken Sie im Dashboard beider virtueller Netzwerke auf "GATEWAY ERSTELLEN". Auf diese Weise wird der VPN-Gatewaybereitstellungsvorgang ausgelöst. Nach einigen Minuten sollte das Dashboard jedes virtuellen Netzwerks die tatsächliche Gatewayadresse anzeigen.

###Schritt 5: Aktualisieren der "lokalen" Netzwerke mit den entsprechenden "Gatewayadressen"###
Bearbeiten Sie die lokalen Netzwerke, um die IP-Platzhalteradressen der Gateways durch die tatsächlichen IP-Adressen der soeben bereitgestellten Gateways zu ersetzen. Verwenden Sie die folgende Zuordnung:

<table>
<tr><th>Lokales Netzwerk    </th><th>Gateway des virtuellen Netzwerks</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Gateway für "hk-vnet-west-us".</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Gateway für "hk-vnet-east-us".</td></tr>
</table>

###Schritt 6: Aktualisieren des gemeinsam verwendeten Schlüssels
Verwenden Sie das folgende PowerShell-Skript zum Aktualisieren des IPSec-Schlüssels jedes VPN-Gateways. [Verwenden Sie den gleichen Schlüssel für beide Gateways]: Set-AzureVNetGatewayKey -VNetName hk-vnet-east-us -LocalNetworkSiteName hk-lnet-map-to-west-us -SharedKey D9E76BKK Set-AzureVNetGatewayKey -VNetName hk-vnet-west-us -LocalNetworkSiteName hk-lnet-map-to-east-us -SharedKey D9E76BKK

###Schritt 7: Einrichten der VNET-zu-VNET-Verbindung
Verwenden Sie im klassischen Azure-Portal das Menü „DASHBOARD“ der beiden virtuellen Netzwerke, um die Gateway-zu-Gateway-Verbindung herzustellen. Verwenden Sie die Menüelemente "VERBINDEN" in der unteren Symbolleiste. Nach einigen Minuten sollte das Dashboard die Verbindungsdetails in grafischer Form anzeigen.

###Schritt 8: Erstellen der virtuellen Maschinen in der Region Nr. 2 
Erstellen Sie das Ubuntu-Image wie für die Bereitstellung in der Region Nr. 1 beschrieben, indem Sie die gleichen Schritte ausführen, oder kopieren Sie die VHD-Imagedatei in das Azure-Speicherkonto in der Region Nr. 2, und erstellen Sie dann das Image. Verwenden Sie dieses Image, und erstellen Sie die folgende Liste virtueller Computer in einem neuen Clouddienst "hk-c-svc-east-us":


| Computername | Subnetz | IP-Adresse | Verfügbarkeitsgruppe | DC/Rack | Ausgangswert? |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| hk-c1-east-us | data | 10\.2.2.4 | hk-c-aset-1 | dc =EASTUS rack =rack1 | Ja |
| hk-c2-east-us | data | 10\.2.2.5 | hk-c-aset-1 | dc =EASTUS rack =rack1 | Nein |
| hk-c3-east-us | data | 10\.2.2.6 | hk-c-aset-1 | dc =EASTUS rack =rack2 | Ja |
| hk-c5-east-us | data | 10\.2.2.8 | hk-c-aset-2 | dc =EASTUS rack =rack3 | Ja |
| hk-c6-east-us | data | 10\.2.2.9 | hk-c-aset-2 | dc =EASTUS rack =rack3 | Nein |
| hk-c7-east-us | data | 10\.2.2.10 | hk-c-aset-2 | dc =EASTUS rack =rack4 | Ja |
| hk-c8-east-us | data | 10\.2.2.11 | hk-c-aset-2 | dc =EASTUS rack =rack4 | Nein |
| hk-w1-east-us | web | 10\.2.1.4 | hk-w-aset-1 | – | – |
| hk-w2-east-us | web | 10\.2.1.5 | hk-w-aset-1 | – | – |


Führen Sie die gleichen Schritte wie für die Region Nr. 1 aus, verwenden Sie jedoch den Adressraum "10.2.xxx.xxx".

###Schritt 9: Konfigurieren von Cassandra auf jeder virtuellen Maschine
Melden Sie sich am virtuellen Computer an, und gehen Sie dann wie folgt vor:

1. Bearbeiten Sie "$CASS\_HOME/conf/cassandra-rackdc.properties", um die Rechenzentrums- und Rackeigenschaften im folgenden Format anzugeben: dc =EASTUS rack =rack1
2. Bearbeiten Sie "cassandra.yaml", um die Seed-Knoten zu konfigurieren: Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

###Schritt 10: Starten von Cassandra
Melden Sie sich an jedem virtuellen Computer an, und starten Sie Cassandra im Hintergrund, indem Sie den folgenden Befehl ausführen: $CASS\_HOME/bin/cassandra

## Testen des Clusters in mehreren Regionen
Inzwischen wurde Cassandra auf 16 Knoten mit acht Knoten in jeder Azure-Region bereitgestellt. Diese Knoten befinden sich aufgrund des gemeinsamen Clusternamens und der Seedknotenkonfiguration im gleichen Cluster. Verwenden Sie die folgenden Schritte zum Testen des Clusters:

###Schritt 1: Abrufen der IP-Adresse des internen Lastenausgleichsmoduls für beide Regionen mithilfe von PowerShell
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  

    Beachten Sie die IP-Adressen, die angezeigt werden (z. B. "west - 10.1.2.101", "east - 10.2.2.101").

###Schritt 2: Ausführen der folgenden Befehle in der Region "West" nach der Anmeldung bei "hk-w1-west-us"
1.    Führen Sie "$CASS\_HOME/bin/cqlsh 10.1.2.101 9160" aus. 
2.	Führen Sie die folgenden CQL-Befehle aus:

		CREATE KEYSPACE customers_ks
		WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

Die Anzeige sollte der Abbildung unten ähneln:

| customer\_id | firstname | Lastname |
| ----------- | --------- | -------- |
| 1 | John | Doe |
| 2 | Jane | Doe |


###Schritt 3: Ausführen der folgenden Befehle in der Region "Ost" nach der Anmeldung bei "hk-w1-east-us"
1.    Führen Sie "$CASS\_HOME/bin/cqlsh 10.2.2.101 9160" aus. 
2.	Führen Sie die folgenden CQL-Befehle aus:

		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

Sie sollten die gleiche Anzeige wie für die Region "West" erhalten:


| customer\_id | firstname | Lastname |
|------------ | --------- | ---------- |
| 1 | John | Doe |
| 2 | Jane | Doe |


Führen Sie weitere Einfügevorgänge aus, und beobachten Sie, dass diese in die Region "USA (West)" des Clusters repliziert werden.

## Testen des Cassandra-Clusters aus Node.js
Mithilfe eines der zuvor in der Ebene "web" erstellten virtuellen Linux-Computers führen wir nun ein einfaches Node.js-Skript zum Lesen der zuvor eingefügten Daten aus.

**Schritt 1: Installieren von Node.js und des Cassandra-Clients**

1. Installieren Sie Node.js und npm.
2. Installieren Sie das Node-Paket "cassandra-client" mithilfe von npm.
3. Führen Sie das folgende Skript an der Eingabeaufforderung der Shell aus. Es zeigt die JSON-Zeichenfolge der abgerufenen Daten an: 

		var pooledCon = require('cassandra-client').PooledConnection;
		var ksName = "custsupport_ks";
		var cfName = "customers_cf";
		var hostList = ['internal_loadbalancer_ip:9160'];
		var ksConOptions = { hosts: hostList,
		                     keyspace: ksName, use_bigints: false };
		
		function createKeyspace(callback){
		   var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
		   var sysConOptions = { hosts: hostList,  
		                         keyspace: 'system', use_bigints: false };
		   var con = new pooledCon(sysConOptions);
		   con.execute(cql,[],function(err) {
		   if (err) {
		     console.log("Failed to create Keyspace: " + ksName);
		     console.log(err);
		   }
		   else {
		     console.log("Created Keyspace: " + ksName);
		     callback(ksConOptions, populateCustomerData);
		   }
		   });
		   con.shutdown();
		} 
		
		function createColumnFamily(ksConOptions, callback){
		  var params = ['customers_cf','custid','varint','custname',
		                'text','custaddress','text'];
		  var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
		var con =  new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) {
		         console.log("Failed to create column family: " + params[0]);
		         console.log(err);
		      }
		      else {
		         console.log("Created column family: " + params[0]);
		         callback();
		      }
		  });
		  con.shutdown();
		} 
		
		//populate Data
		function populateCustomerData() {
		   var params = ['John','Infinity Dr, TX', 1];
		   updateCustomer(ksConOptions,params);
		
		   params = ['Tom','Fermat Ln, WA', 2];
		   updateCustomer(ksConOptions,params);
		}
		
		//update will also insert the record if none exists
		function updateCustomer(ksConOptions,params)
		{
		  var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) console.log(err);
		      else console.log("Inserted customer : " + params[0]);
		  });
		  con.shutdown();
		}
		
		//read the two rows inserted above
		function readCustomer(ksConOptions)
		{
		  var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,[],function(err,rows) {
		      if (err) 
		         console.log(err);
		      else 
		         for (var i=0; i<rows.length; i++)
		            console.log(JSON.stringify(rows[i]));
		    });
		   con.shutdown();
		}
		
		//exectue the code
		createKeyspace(createColumnFamily);
		readCustomer(ksConOptions)


## Zusammenfassung 
Microsoft Azure ist eine flexible Plattform, die das Ausführen von Microsoft- sowie von Open Source-Software ermöglicht, wie in dieser Übung gezeigt wurde. Cassandra-Cluster mit hoher Verfügbarkeit können in einem Rechenzentrum über die Verteilung der Clusterknoten auf mehrere Fehlerdomänen bereitgestellt werden. Cassandra-Cluster können auch in mehreren geografisch entfernten Azure-Regionen bereitgestellt werden, damit die Systeme vor Katastrophen geschützt sind. Azure und Cassandra in Kombination ermöglichen das Erstellen hochgradig skalierbarer Clouddienste mit hoher Verfügbarkeit, die nach einem Notfall wiederhergestellt werden können. Heutige Dienste mit Internetskalierung benötigen solche Systeme.

##Referenzen##
- [http://cassandra.apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com) 
- [http://www.nodejs.org](http://www.nodejs.org) 

<!---HONumber=AcomDC_0211_2016-->