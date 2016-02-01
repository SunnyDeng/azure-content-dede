<properties
   pageTitle="DMZ-Beispiel: Erstellen einer DMZ zum Schutz von Netzwerken mit Firewall, UDR und NSG | Microsoft Azure"
   description="Erstellen einer DMZ mit Firewall, benutzerdefiniertem Routing (User Defined Routing, UDR) und Netzwerksicherheitsgruppen (Network Security Groups, NSGs)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/20/2016"
   ms.author="jonor;sivae"/>

# Beispiel 3: Erstellen einer DMZ zum Schutz von Netzwerken mit Firewall, UDR und NSG

[Zurück zur Seite mit bewährten Methoden zu Sicherheitsgrenzen][HOME]

In diesem Beispiel wird eine DMZ mit einer Firewall, vier Windows-Servern, benutzerdefiniertem Routing, IP-Weiterleitung und Netzwerksicherheitsgruppen erstellt. Jeder der relevanten Befehle wird genau erläutert, um ein besseres Verständnis jedes einzelnen Schritts zu ermöglichen. Es gibt außerdem einen Abschnitt mit verschiedenen Szenarien zum Datenverkehr, in denen Schritt für Schritt erläutert wird, wie der Datenverkehr durch die verschiedenen Sicherheitsstufen in der DMZ geleitet wird. Der Referenzabschnitt schließlich enthält den vollständigen Code sowie Anweisungen zum Aufbau dieser Umgebung, damit Sie verschiedene Szenarien testen und ausprobieren können.

![Bidirektionale DMZ mit virtuellem Netzwerkgerät, Netzwerksicherheitsgruppe und benutzerdefiniertem Routing][1]

## Einrichten der Umgebung
Dieses Beispiel umfasst ein Abonnement, das Folgendes enthält:

- Drei Clouddienste: "SecSvc001", "FrontEnd001" und "BackEnd001"
- Ein virtuelles Netzwerk, "CorpNetwork", mit drei Subnetzen: "SecNet", "FrontEnd" und "BackEnd"
- Ein virtuelles Netzwerkgerät, in diesem Beispiel eine Firewall, das mit dem Subnetz "SecNet" verbunden ist
- Eine Windows Server-Instanz, die einen Anwendungswebserver darstellt ("IIS01")
- Zwei Windows Server-Instanzen, die Back-End-Anwendungsserver darstellen ("AppVM01", "AppVM02")
- Eine Windows Server-Instanz, die einen DNS-Server darstellt ("DNS01")

Der Referenzabschnitt enthält ein PowerShell-Skript, mit dem sich der größte Teil der oben beschriebenen Umgebung erstellen lässt. Die Erstellung der virtuellen Computer und Netzwerke wird zwar durch das Beispielskript ausgeführt, aber dies wird in diesem Dokument nicht im Einzelnen beschrieben.

So erstellen Sie die Umgebung

  1.	Speichern Sie die im Referenzabschnitt enthaltene und mit dem Namen, dem Speicherort und den IP-Adressen für das jeweilige Szenario aktualisierte XML-Netzwerkkonfigurationsdatei.
  2.	Aktualisieren Sie die Benutzervariablen im Skript gemäß der Umgebung, in der das Skript ausgeführt werden soll (Abonnements, Dienstnamen usw.).
  3.	Führen Sie das Skript in PowerShell aus.

**Hinweis**: Die im PowerShell-Skript angegebene Region muss der Region in der XML-Netzwerkkonfigurationsdatei entsprechen.

Nachdem das Skript erfolgreich ausgeführt wurde, können im Anschluss folgende Schritte ausgeführt werden:

1.	Richten Sie die Firewallregeln ein. Dieses Thema wird im unten stehenden Abschnitt "Firewallregeln" beschrieben.
2.	Optional stehen im Referenzabschnitt zwei Skripts zum Einrichten des Webservers und des Anwendungsservers mit einer einfachen Webanwendung zur Verfügung, um Tests mit dieser DMZ-Konfiguration zu ermöglichen.

Nachdem das Skript erfolgreich ausgeführt wurde, müssen die Firewallregeln eingerichtet werden. Dieses Thema wird im Abschnitt "Firewallregeln" beschrieben.

## Benutzerdefiniertes Routing
Standardmäßig sind folgende Systemrouten definiert:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

"VNETLocal" ist immer das definierte Adresspräfix (bzw. die definierten Adresspräfixe) des VNets für dieses spezifische Netzwerk (d. h. diese Angabe ändert sich je nach VNet und je nachdem, wie jedes VNet definiert ist). Die anderen Systemrouten sind statisch und standardmäßig wie oben angegeben.

Zur Priorität: Routen werden anhand der LPM-Methode (Longest Prefix Match, Übereinstimmung mit dem längsten Präfix) verarbeitet. Es wird also die spezifischste Route in der Tabelle für eine bestimmte Zieladresse verwendet.

Daher würde Datenverkehr (zum Beispiel zum Server DNS01 mit der Adresse 10.0.2.4), der in das lokale Netzwerk (10.0.0.0/16) geleitet werden soll, aufgrund der Route 10.0.0.0/16 über das VNet an sein Ziel weitergeleitet. Anders gesagt: Für 10.0.2.4 ist 10.0.0.0/16 die am genauesten spezifizierte Route. Zwar könnten auch die Routen 10.0.0.0/8 und 0.0.0.0/0 gelten, da sie aber weniger spezifisch sind, wirken sie sich auf diesen Datenverkehr nicht aus. Daher wäre der nächste Hop für 10.0.2.4 das lokale VNet, und der Datenverkehr würde einfach an das Ziel weitergeleitet.

Wenn der Datenverkehr zum Beispiel an 10.1.1.1 geleitet werden soll, würde die Route 10.0.0.0/16 nicht gelten. 10.0.0.0/8 wäre die spezifischste Adresse, und da der nächste Hop "Null" lautet, würde der Datenverkehr verworfen ("black hole").

Wenn das Ziel für keins der Null- oder der VNETLocal-Präfixe gilt, würde es der am wenigsten spezifischen Route zu 0.0.0.0/0 folgen. Damit wäre das Internet der nächste Hop, und der Datenverkehr würde über die Azure-Internetgrenze hinaus geleitet.

Wenn die Routingtabelle zwei identische Präfixe aufweist, gilt folgende Präferenzreihenfolge, basierend auf dem Routenattribut "source":

1.	<blank> = Eine benutzerdefinierte Route, die der Tabelle manuell hinzugefügt wurde.
2.	"VPNGateway" = Eine dynamische Route (BGP bei Verwendung in Hybridnetzwerken), die durch ein dynamisches Netzwerkprotokoll hinzugefügt wurde. Diese Routen können sich im Lauf der Zeit ändern, da das dynamische Protokoll Änderungen im Peernetzwerk automatisch widerspiegelt.
3.	"Default" = Die Systemrouten, das lokale VNet und die statischen Einträge, wie in der Routingtabelle oben gezeigt.

>[AZURE.NOTE]Aufgrund der Komplexität des dynamischen Routings im Azure-Gateway gelten für das benutzerdefinierte Routing (User Defined Routing, UDR) und ExpressRoute Einschränkungen. Auf Subnetze, die mit dem Azure-Gateway kommunizieren, das die ExpressRoute-Verbindung bereitstellt, sollte kein UDR angewendet werden. Darüber hinaus kann das Azure-Gateway nicht als NextHop-Gerät für andere UDR-gebundene Subnetze fungieren. Die Möglichkeit zur vollständigen Integration von UDR und ExpressRoute ist für eine spätere Azure-Version geplant.

#### Erstellen der lokalen Routen

In diesem Beispiel werden zwei Routingtabellen benötigt, jeweils eine für das Front-End- und das Back-End-Subnetz. Jede Tabelle enthält statische Routen, die sich für das jeweilige Subnetz eignen. In diesem Beispiel weist jede Tabelle drei Routen auf:

1. Datenverkehr im lokalen Subnetz ohne definierten nächsten Hop, sodass der Datenverkehr im lokalen Subnetz die Firewall umgehen kann.
2. Virtueller Netzwerkdatenverkehr mit einem als Firewall definierten nächsten Hop. Dies setzt die Standardregel außer Kraft, die ein direktes Routing des lokalen VNet-Datenverkehrs ermöglicht.
3. Der gesamte verbleibende Datenverkehr (0/0) mit einem als Firewall definierten nächsten Hop.

Sobald die Routingtabellen erstellt wurden, sind sie an ihre Subnetze gebunden. Die Routingtabelle für das Front-End-Subnetz sollte nach Erstellung und Bindung an das Subnetz wie folgt aussehen:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
		 {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


In diesem Beispiel werden die folgenden Befehle verwendet, um die Routingtabelle zu erstellen, eine benutzerdefinierte Route hinzuzufügen und die Routingtabelle anschließend an ein Subnetz zu binden (Hinweis: Alle unten gezeigten Elemente, die mit einem Dollarzeichen beginnen, wie etwa $BESubnet, sind benutzerdefinierte Variablen aus dem Skript im Referenzabschnitt dieses Dokuments):

1.	Zuerst muss die Basisroutingtabelle erstellt werden. Dieser Codeausschnitt zeigt die Erstellung der Tabelle für das Back-End-Subnetz. Im Skript wird auch eine entsprechende Tabelle für das Front-End-Subnetz erstellt.

		New-AzureRouteTable -Name $BERouteTableName `
		    -Location $DeploymentLocation `
		    -Label "Route table for $BESubnet subnet"

2.	Nachdem die Routingtabelle erstellt wurde, können bestimmte benutzerdefinierte Routen hinzugefügt werden. In diesem Codeausschnitt wird der gesamte Datenverkehr (0.0.0.0/0) über das virtuelle Gerät weitergeleitet (mithilfe einer Variablen, $VMIP[0], wird die IP-Adresse übergeben, die weiter oben im Skript bei der Erstellung des virtuellen Geräts zugewiesen wurde). Im Skript wird auch eine entsprechende Regel in der Front-End-Tabelle erstellt.

		Get-AzureRouteTable $BERouteTableName |`
		    Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
		    -NextHopType VirtualAppliance `
		    -NextHopIpAddress $VMIP[0]

3. Der oben stehende Routeneintrag überschreibt die Standardroute "0.0.0.0/0". Da aber die Standardroute "10.0.0.0/16" noch vorhanden ist, kann der Datenverkehr im VNet ohne Umweg über das virtuelle Netzwerkgerät direkt an das Ziel weitergeleitet werden. Um dieses Verhalten zu korrigieren, muss folgende Regel hinzugefügt werden.

	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]

4. An diesem Punkt muss eine Auswahl getroffen werden. Mit den beiden oben angegebenen Routen wird der gesamte Datenverkehr zur Bewertung an die Firewall geleitet, sogar Datenverkehr innerhalb eines einzelnen Subnetzes. Dies ist möglicherweise erwünscht. Um jedoch eine lokale Weiterleitung des Datenverkehrs innerhalb eines Subnetzes ohne Beteiligung einer Firewall zu ermöglichen, kann eine dritte, sehr spezifische Regel hinzugefügt werden. Diese Route gibt an, dass Datenverkehr an jede Adresse, die für das lokalen Netzwerk bestimmt ist, direkt dorthin geleitet werden kann (NextHopType = VNETLocal).

	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
	        -NextHopType VNETLocal

5.	Wenn die Routingtabelle erstellt und mit benutzerdefinierten Routen aufgefüllt wurde, muss sie an ein Subnetz gebunden werden. Im Skript wird auch die Front-End-Routingtabelle an das Front-End-Subnetz gebunden. Hier sehen Sie das Bindungsskript für das Back-End-Subnetz.

		Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
		   -SubnetName $BESubnet `
		   -RouteTableName $BERouteTableName

## IP-Weiterleitung
Die IP-Weiterleitung ist eine begleitende Funktion für das benutzerdefinierte Routing (User Defined Routing, UDR). Es handelt sich um eine Einstellung in einem virtuellen Gerät, das diesem ermöglicht, nicht speziell an dieses Gerät adressierten Datenverkehr zu empfangen und diesen an das endgültige Ziel weiterzuleiten.

Ein Beispiel: Wenn Datenverkehr aus AppVM01 eine Anforderung an den Server DNS01 sendet, leitet UDR diese Anforderung an die Firewall weiter. Wenn die IP-Weiterleitung aktiviert ist, wird Datenverkehr für das Ziel DNS01 (10.0.2.4) vom Gerät (10.0.0.4) akzeptiert und dann an das endgültige Ziel (10.0.2.4) weitergeleitet. Ist die IP-Weiterleitung in der Firewall nicht aktiviert, wird der Datenverkehr vom Gerät nicht akzeptiert, selbst wenn in der Routingtabelle die Firewall als nächster Hop angegeben ist.

>[AZURE.IMPORTANT]Es wichtig, die IP-Weiterleitung zusammen mit dem benutzerdefinierten Routing zu aktivieren.

Die Einrichtung der IP-Weiterleitung erfolgt über einen einzigen Befehl und kann während der Erstellung des virtuellen Computers ausgeführt werden. In diesem Beispiel befindet sich der Codeausschnitt am Ende des Skripts und ist mit den UDR-Befehlen gruppiert:

1.	Rufen Sie die VM-Instanz auf, die Ihr virtuelles Gerät darstellt – in diesem Fall die Firewall –, und aktivieren Sie die IP-Weiterleitung (Hinweis: Jedes rote Element, das mit einem Dollarzeichen beginnt, wie etwa $VMName[0], ist eine benutzerdefinierte Variable aus dem Skript im Referenzabschnitt dieses Dokuments. Die in eckigen Klammern eingeschlossene Null, [0], repräsentiert den ersten virtuellen Computer im VM-Array. Damit das Beispielskript ohne Änderung funktioniert, muss es sich beim ersten virtuellen Computer, VM 0, um die Firewall handeln):

		Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
		   |Set-AzureIPForwarding -Enable

## Netzwerksicherheitsgruppen
In diesem Beispiel wird eine Netzwerksicherheitsgruppe erstellt und dann mit einer einzigen Regel geladen. Diese Gruppe wird dann nur an die Front-End- und Back-End-Subnetze (nicht an SecNet) gebunden. Die folgende Regel wird deklarativ erstellt:

1.	Jeglicher Datenverkehr (alle Ports) aus dem Internet an das gesamte VNet (alle Subnetze) wird abgelehnt.

Obwohl Netzwerksicherheitsgruppen in diesem Beispiel verwendet werden, dienen sie hauptsächlich als zweite Stufe zum Schutz gegen manuelle Fehlkonfigurationen. Es soll der gesamte eingehende Datenverkehr aus dem Internet an das Front-End- oder das Back-End-Subnetz blockiert werden. Der Datenverkehr soll nur durch das SecNet-Subnetz an die Firewall weitergeleitet werden (und danach ggf. an die Front-End- und Back-End-Subnetze). Darüber hinaus soll – wenn die UDR-Regeln eingerichtet sind – jeglicher Datenverkehr, der an die Front-End- oder Back-End-Subnetze weitergeleitet wurde, nach außen an die Firewall geleitet werden (dank UDR). Die Firewall erkennt diesen Datenverkehr als asymmetrisch und verwirft den ausgehenden Datenverkehr. Es gibt daher drei Sicherheitsebenen, die das Front-End- und das Back-End-Subnetz schützen: 1. keine offenen Endpunkte in den Clouddiensten FrontEnd001 und BackEnd001. 2. Netzwerksicherheitsgruppen lehnen Datenverkehr aus dem Internet ab. 3. Die Firewall verwirft asymmetrischen Datenverkehr.

Ein interessanter Aspekt in Bezug auf die Netzwerksicherheitsgruppe in diesem Beispiel ist, dass sie nur eine Regel enthält (siehe unten), mit der Internetdatenverkehr im gesamten virtuellen Netzwerk abgelehnt wird, einschließlich des Sicherheitssubnetzes.

	Get-AzureNetworkSecurityGroup -Name $NSGName | `
	    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
	    from the Internet" `
	    -Type Inbound -Priority 100 -Action Deny `
	    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	    -DestinationAddressPrefix VIRTUAL_NETWORK `
	    -DestinationPortRange '*' `
	    -Protocol *

Da jedoch die Netzwerksicherheitsgruppe nur an die Front-End- und Back-End-Subnetze gebunden ist, wird die Regel bei eingehendem Datenverkehr an das Sicherheitssubnetz nicht angewendet. Obwohl also die Netzwerksicherheitsgruppe festlegt, dass kein Internetdatenverkehr an eine beliebige Adresse im VNet übertragen werden darf, wird der Datenverkehr dennoch an das Sicherheitssubnetz weitergeleitet, da die Netzwerksicherheitsgruppe nicht an das Sicherheitssubnetz gebunden wurde.

	Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
	    -SubnetName $FESubnet -VirtualNetworkName $VNetName
	
	Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
	    -SubnetName $BESubnet -VirtualNetworkName $VNetName

## Firewallregeln
In der Firewall müssen Weiterleitungsregeln erstellt werden. Da die Firewall sämtlichen eingehenden und ausgehenden Datenverkehr sowie den Datenverkehr innerhalb des VNets blockiert oder weiterleitet, müssen zahlreiche Firewallregeln festgelegt werden. Darüber hinaus wird jeglicher eingehender Datenverkehr über die öffentliche IP-Adresse des Sicherheitsdiensts geleitet (über unterschiedliche Ports) und muss von der Firewall verarbeitet werden. Eine bewährte Methode besteht darin, die logischen Datenflüsse in Diagrammen abzubilden, bevor Subnetze und Firewallregeln eingerichtet werden, um spätere Überarbeitungen zu vermeiden. Die folgende Abbildung zeigt eine logische Ansicht der Firewallregeln für dieses Beispiel:
 
![Logische Ansicht der Firewallregeln][2]

>[AZURE.NOTE]Je nach verwendetem virtuellem Netzwerkgerät variieren die Verwaltungsports. In diesem Beispiel wird auf eine Barracuda NG Firewall verwiesen, die die Ports 22, 801 und 807 verwendet. Informationen dazu, über welche Ports das verwendete Gerät genau verwaltet wird, finden Sie in der Dokumentation des Geräteanbieters.

### Beschreibung der logischen Regel
Im oben gezeigten logischen Diagramm wird das Sicherheitssubnetz nicht angezeigt, da die Firewall die einzige Ressource in diesem Subnetz ist. Dieses Diagramm zeigt die Firewallregeln und die Art und Weise, wie diese Datenverkehrsflüsse logisch zulassen oder ablehnen. Das Diagramm zeigt nicht den tatsächlichen Weiterleitungspfad. Die für den RDP-Datenverkehr ausgewählten externen Ports liegen in einem höheren Bereich (8014-8026) und wurden zur Anpassung an die letzten beiden Oktette der lokalen IP-Adresse ausgewählt, um die Lesbarkeit zu vereinfachen (die lokale Serveradresse 10.0.1.4 ist beispielsweise Port 8014 zugeordnet). Es können jedoch auch Ports mit einer höheren Nummer ausgewählt werden, wenn sie keine Konflikte verursachen.

In diesem Beispiel werden 7 Arten von Regeln benötigt, die wie folgt beschrieben werden:

- Externe Regeln (für eingehenden Datenverkehr):
  1.	Firewallverwaltungsregel: Diese Regel zur Anwendungsumleitung ermöglicht die Weiterleitung von Datenverkehr an die Verwaltungsports des virtuellen Netzwerkgeräts.
  2.	RDP-Regeln (für jeden Windows-Server): Diese vier Regeln (eine für jeden Server) ermöglichen die Verwaltung der einzelnen Server über RDP. Diese können je nach Funktionsumfang des verwendeten virtuellen Netzwerkgeräts in eine Regel gebündelt werden.
  3.	Regeln für den Anwendungsdatenverkehr: Es gibt zwei Regeln für den Anwendungsdatenverkehr, eine für den Datenverkehr im Front-End, eine für den Datenverkehr im Back-End (z. B. vom Webserver zur Datenebene). Die Konfiguration dieser Regeln richtet sich nach der Netzwerkarchitektur (der Platzierung Ihrer Server) und dem Datenverkehrsfluss (Richtung des Datenverkehrs und verwendete Ports).
      - Mit der ersten Regel gelangt der tatsächliche Anwendungsdatenverkehr zum Anwendungsserver. Während die anderen Regeln für Sicherheit, Verwaltung usw. sorgen, ermöglichen Anwendungsregeln es Benutzern oder Diensten, auf die Anwendung(en) zuzugreifen. In diesem Beispiel befindet sich ein einziger Webserver an Port 80. Daher leitet eine einzige Firewallanwendungsregel den eingehenden Datenverkehr an die externe IP-Adresse, also die interne IP-Adresse der Webserver um. Die umgeleitete Datenverkehrssitzung wird per Netzwerkadressübersetzung (Network Address Translation, NAT) an den internen Server weitergeleitet.
      - Bei der zweiten Regel für Anwendungsdatenverkehr handelt es sich um die Back-End-Regel, dank derer der Webserver über jeden Port mit dem Server AppVM01 (nicht jedoch mit dem Server AppVM02) kommunizieren kann.
- Interne Regeln (für VNet-internen Datenverkehr)
  4.	Regel für ausgehenden Datenverkehr zum Internet: Mit dieser Regel kann Datenverkehr aus allen Netzwerken in die ausgewählten Netzwerke übergeben werden. Diese Regel ist üblicherweise standardmäßig in der Firewall vorhanden, jedoch deaktiviert. Diese Regel sollte für dieses Beispiel aktiviert werden.
  5.	DNS-Regel: Mit dieser Regel kann nur DNS-Datenverkehr (Port 53) an den DNS-Server übergeben werden. Für diese Umgebung wird der größte Teil des Datenverkehrs aus dem Front-End an das Back-End blockiert. Diese Regel lässt DNS-Datenverkehr aus allen lokalen Subnetzen explizit zu.
  6.	Subnetz-zu-Subnetz-Regel: Mit dieser Regel kann jeder Server im Back-End-Subnetz eine Verbindung mit jedem Server im Front-End-Subnetz herstellen (nicht jedoch umgekehrt).
- Ausfallsicherheitsregel (für Datenverkehr, für den keine der obigen Regeln gilt):
  7.	Regel zum Ablehnen jeglichen Datenverkehrs: Dies sollte (hinsichtlich der Priorität) die letzte Regel sein. Sollte ein Datenverkehrsfluss keiner der vorherigen Regeln entsprechen, wird er durch diese Regel verworfen. Dies ist eine Standardregel, die üblicherweise aktiviert ist. Im Allgemeinen sind keine Änderungen erforderlich.

>[AZURE.TIP]In Bezug auf die zweite Anwendungsdatenverkehrsregel ist in diesem Beispiel einfachheitshalber jeder Port zulässig. In einem realen Szenario sollten die spezifischsten Port- und Adressbereiche verwendet werden, um die Angriffsfläche dieser Regel zu reduzieren.

<br />

>[AZURE.IMPORTANT]Nachdem alle oben genannten Regeln erstellt wurden, ist es wichtig, die Priorität jeder einzelnen Regel zu prüfen, um sicherzustellen, dass der Datenverkehr wie gewünscht zugelassen oder abgelehnt wird. In diesem Beispiel wurden die Regeln nach Priorität geordnet. Es kann leicht passieren, aufgrund einer falschen Reihenfolge der Regeln von der Firewall ausgesperrt zu werden. Stellen Sie auf jeden Fall sicher, dass die Verwaltung der Firewall selbst immer die Regel mit der höchsten Priorität ist.

### Voraussetzungen für Regeln
Eine Voraussetzung für den virtuellen Computer, auf dem die Firewall ausgeführt wird, sind öffentliche Endpunkte. Damit die Firewall Datenverkehr verarbeiten kann, müssen die entsprechenden Endpunkte offen sein. Es gibt drei Arten von Datenverkehr in diesem Beispiel: 1. Verwaltungsdatenverkehr zur Steuerung der Firewall und Firewallregeln. 2. RDP-Datenverkehr zur Steuerung der Windows-Server. 3. Anwendungsdatenverkehr. Diese werden in den drei Kategorien für den Datenverkehr in der oberen Hälfte der logischen Ansicht für die oben erläuterten Firewallregeln angezeigt.

>[AZURE.IMPORTANT]An dieser Stelle muss daran erinnert werden, dass **sämtlicher** Datenverkehr durch die Firewall geleitet wird. Um also eine Remotedesktopverbindung mit dem IIS01-Server herzustellen – selbst wenn dieser sich im Front-End-Clouddienst und im Front-End-Subnetz befindet –, ist für den Zugriff auf diesen Server eine RDP-Verbindung mit der Firewall über Port 8014 erforderlich. Anschließend muss der Firewall ermöglicht werden, die RDP-Anforderung intern an den RDP-Port auf IIS01 weiterzuleiten. Die Schaltfläche "Verbinden" im Azure-Portal funktioniert hier nicht, da es keinen direkten RDP-Pfad zu IIS01 gibt (zumindest nicht für das Portal). Dies bedeutet, dass alle Verbindungen aus dem Internet an den Sicherheitsdienst und einen Port, z. B. secscv001.cloudapp.net:xxxx, weitergeleitet werden (Informationen zur Zuordnung zwischen externem Port und interner IP-Adresse und internem Port finden Sie in obigem Diagramm).

Ein Endpunkt kann entweder zum Zeitpunkt der VM-Erstellung oder danach geöffnet werden, wie im Beispielskript erfolgt und im Codeausschnitt unten gezeigt (Hinweis: Jedes Element, das mit einem Dollarzeichen beginnt, wie etwa $VMName[$i], ist eine benutzerdefinierte Variable aus dem Skript im Referenzabschnitt dieses Dokuments. Die in Klammern stehenden Zeichen [$i] stellen die Arraynummer eines bestimmten virtuellen Computers in einem VM-Array dar):

	Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
	    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
	    Update-AzureVM

Aufgrund der Verwendung von Variablen ist es hier nicht klar ersichtlich, aber Endpunkte werden **nur** im Sicherheitsclouddienst geöffnet. Dadurch soll sichergestellt werden, dass sämtlicher eingehender Datenverkehr über die Firewall verarbeitet (geroutet, per Netzwerkadressübersetzung weitergeleitet oder verworfen) wird.

Zur Verwaltung der Firewall und Erstellung der erforderlichen Konfigurationen muss ein Verwaltungsclient auf einem PC installiert werden. Informationen zur Verwaltung des Geräts finden Sie in der Dokumentation zu Ihrer Firewall (bzw. einem anderen virtuellen Netzwerkgerät). Im Rest dieses Abschnitts und im nächsten Abschnitt, "Erstellen von Firewallregeln", wird die Konfiguration der Firewall über den Verwaltungsclient des Anbieters beschrieben (d. h. nicht über das Azure-Portal oder PowerShell).

Anweisungen zum Herunterladen des Clients und Herstellen einer Verbindung mit der in diesem Beispiel verwendeten Barracuda-Firewall finden Sie unter [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin).

Nach der Anmeldung bei der Firewall, jedoch noch vor dem Erstellen von Firewallregeln sollten Sie zwei erforderliche Objektklassen einrichten, die die Erstellung der Regeln vereinfachen können: "Network" und "Service".

In diesem Beispiel sollen drei benannte Netzwerkobjekte definiert werden: je eines für das Front-End-Subnetz und das Back-End-Subnetz sowie ein Netzwerkobjekt für die IP-Adresse des DNS-Servers. So erstellen Sie ein benanntes Netzwerk: Navigieren Sie auf dem Dashboard des Barracuda NG Admin-Clients zur Registerkarte "Configuration", und klicken Sie im Abschnitt "Operational Configuration" auf "Ruleset". Klicken Sie im Menü "Firewall Objects" auf "Networks", und klicken Sie im Menü "Edit Networks" auf "New". Jetzt kann durch Hinzufügen des Namens und des Präfixes das Netzwerkobjekt erstellt werden:

![Erstellen eines Front-End-Netzwerkobjekts][3]
 
Damit wird ein benanntes Netzwerk für das Front-End-Subnetz erstellt. Ein ähnliches Objekt muss auch für das Back-End-Subnetz erstellt werden. Jetzt können die Subnetze in den Firewallregeln leichter anhand ihrer Namen referenziert werden.

Für das Objekt für den DNS-Server:

![Erstellen eines DNS-Serverobjekts][4]

Dieser Verweis auf eine einzelne IP-Adresse wird in einer DNS-Regel weiter unten in diesem Dokument verwendet.

Die zweite Art erforderlicher Objekte sind Service-Objekte. Diese repräsentieren die RDP-Verbindungsports für jeden Server. Da das vorhandene RDP-Dienstobjekt an den RDP-Standardport 3389 gebunden ist, können neue Dienstobjekte erstellt werden, um Datenverkehr aus den externen Ports (8014-8026) zuzulassen. Die neuen Ports können auch zum vorhandenen RDP-Dienst hinzugefügt werden, zu Demonstrationszwecken wird jedoch eine eigene Regel für jeden Server erstellt. So erstellen Sie eine neue RDP-Regel für einen Server: Navigieren Sie auf dem Dashboard des Barracuda NG Admin-Clients zur Registerkarte "Configuration", und klicken Sie im Abschnitt "Operational Configuration" auf "Ruleset". Klicken Sie im Menü "Firewall Objects" auf "Services", führen Sie einen Bildlauf durch die Liste der Dienste durch, und wählen Sie den Dienst "RDP" aus. Klicken Sie mit der rechten Maustaste, und wählen Sie "Copy" aus. Klicken Sie erneut mit der rechten Maustaste, und wählen Sie "Paste" aus. Nun verfügen Sie über ein Dienstobjekt namens "RDP-Copy1", das bearbeitet werden kann. Klicken Sie mit der rechten Maustaste auf "RDP-Copy1", und wählen Sie "Edit". Das Fenster "Edit Services Object" wird geöffnet, wie hier gezeigt:

![Kopie der RDP-Standardregel][5]

Die Werte können bearbeitet werden, um den RDP-Dienst für einen bestimmten Server zu repräsentieren. Für AppVM01 muss die obige RDP-Standardregel bearbeitet werden. Geben Sie einen neuen Dienstnamen, eine Beschreibung und einen externen RDP-Port ein, wie in Abbildung 8 dargestellt (Hinweis: die Ports wurden vom RDP-Standardport 3389 in den externen Port geändert, der für diesen spezifischen Server verwendet wird. Für AppVM01 lautet der externe Port 8025). Hier sehen Sie den geänderten Dienst:

![AppVM01-Regel][6]
 
Wiederholen Sie den Prozess, um die RDP-Dienste für die anderen Server zu erstellen: AppVM02, DNS01 und IIS01. Durch diese Dienste wird die Erstellung von Regeln im nächsten Abschnitt einfacher und klarer.

>[AZURE.NOTE]Aus zwei Gründen wird kein RDP-Dienst für die Firewall benötigt: 1. die Firewall-VM ist ein Linux-basiertes Image, zur VM-Verwaltung würde also SSH anstatt RDP auf Port 22 verwendet. 2. Port 22 sowie zwei weitere Verwaltungsports sind in der ersten oben beschriebenen Verwaltungsregel zulässig, um Verbindungen zum Zweck der Verwaltung zu ermöglichen.

### Erstellen von Firewallregeln
In diesem Beispiel werden drei Arten von Firewallregeln verwendet, die jeweils unterschiedliche Symbole aufweisen:

Die Regel zur Anwendungsumleitung: ![Symbol der Regel zur Anwendungsumleitung][7]

Die Ziel-NAT-Regel: ![Symbol der Ziel-NAT-Regel][8]

Die Übergaberegel: ![Symbol der Übergaberegel][9]

Weitere Informationen zu diesen Regeln finden Sie auf der Barracuda-Website.

Um die folgenden Regeln zu erstellen (oder vorhandene Standardregeln zu überprüfen), öffnen Sie das Dashboard des Barracuda NG Admin-Clients, wechseln Sie zur Registerkarte "Configuration", und klicken Sie im Abschnitt "Operational Configuration" auf "Ruleset". In einem Raster namens "Main Rules" werden die vorhandenen aktivierten und deaktivierten Regeln dieser Firewall angezeigt. In der oberen rechten Ecke dieses Rasters befindet sich ein kleines grünes Pluszeichen. Klicken Sie auf dieses Zeichen, um eine neue Regel zu erstellen (Hinweis: Möglicherweise ist die Firewall "gesperrt", sodass keine Änderungen vorgenommen werden können. Wenn eine Schaltfläche mit der Bezeichnung "Lock" angezeigt wird und Sie keine Regeln erstellen oder bearbeiten können, klicken Sie auf diese Schaltfläche, um den Regelsatz zu entsperren und die Bearbeitung zu ermöglichen). Wenn Sie eine vorhandene Regel bearbeiten möchten, wählen Sie diese Regel aus, klicken mit der rechten Maustaste und wählen "Edit Rule".

Nachdem Regeln erstellt und/oder bearbeitet wurden, müssen sie an die Firewall übertragen und anschließend aktiviert werden. Der Übertragungs- und Aktivierungsprozess wird unter den detaillierten Regelbeschreibungen erläutert.

Die Einzelheiten zu allen Regeln, die für dieses Beispiel erforderlich sind, werden im Folgenden beschrieben:

- **Firewallverwaltungsregel**: Diese Regel zur Anwendungsumleitung ermöglicht die Weiterleitung von Datenverkehr an die Verwaltungsports des virtuellen Netzwerkgeräts, bei dem es sich in diesem Fall um eine Barracuda NG-Firewall handelt. Die Verwaltungsports lauten 801, 807 und optional 22. Die externen und internen Ports sind die gleichen (d. h. es findet keine Portübersetzung statt). Bei dieser Regel, SETUP-MGMT-ACCESS, handelt es sich um eine Standardregel, die standardmäßig aktiviert ist (in Barracuda NG Firewall, Version 6.1).

	![Firewallverwaltungsregel][10]

>[AZURE.TIP]Der Quelladressraum in dieser Regel lautet "Any", wenn die IP-Verwaltungsadressbereiche bekannt sind. Eine Verkleinerung dieses Bereichs würde auch die Angriffsfläche der Verwaltungsports verringern.

- **RDP-Regeln**: Diese Ziel-NAT-Regeln ermöglichen die Verwaltung der einzelnen Server über RDP. Zum Erstellen dieser Regel sind vier wichtige Felder erforderlich:
  1.	Source: Um eine Verwaltung per RDP von einem beliebigen Standort aus zu ermöglichen, wird im Feld "Source" der Verweis "Any" verwendet.
  2.	Service: Verwenden Sie das geeignete, zuvor erstellte Dienstobjekt, in diesem Fall "AppVM01 RDP", die externen Ports leiten an die lokale IP-Adresse des Servers sowie an Port 3386 (den RDP-Standardport) weiter. Diese spezifische Regel dient zum Zugriff auf AppVM01 über RDP.
  3.	Destination: Dies sollte der *lokale Port der Firewall* sein, "DCHP 1 Local IP" oder "eth0" bei Verwendung von statischen IP-Adressen. Die Ordnungszahl (eth0, eth1 usw.) kann abweichen, wenn Ihr Netzwerkgerät über mehrere lokale Schnittstellen verfügt. Hierbei handelt es sich um den Port, über den die Firewall Datenverkehr sendet (kann der gleiche sein wie der empfangende Port). Das eigentliche Weiterleitungsziel befindet sich im Feld "Target List".
  4.	Redirection: Dieser Abschnitt informiert das virtuelle Gerät über das endgültige Ziel des umgeleiteten Datenverkehrs. Die einfachste Umleitung besteht darin, IP-Adresse und Port (optional) in das Feld "Target List" einzutragen. Wenn kein Port angegeben ist, wird der Zielport der eingehenden Anforderung verwendet (d. h. es findet keine Übersetzung statt). Ist ein Port angegeben, wird dieser zusammen mit der IP-Adresse per Netzwerkadressübersetzung weitergeleitet.

	![RDP-Firewallregel][11]

    Es müssen insgesamt vier RDP-Regeln erstellt werden:

    | Regelname | Server | Dienst | Zielliste |
    |----------------|---------|-------------|---------------|
    | RDP-to-IIS01 | IIS01 | IIS01 RDP | 10\.0.1.4:3389 |
    | RDP-to-DNS01 | DNS01 | DNS01 RDP | 10\.0.2.4:3389 |
    | RDP-to-AppVM01 | AppVM01 | AppVM01 RDP | 10\.0.2.5:3389 |
    | RDP-to-AppVM02 | AppVM02 | AppVm02 RDP | 10\.0.2.6:3389 |
  
>[AZURE.TIP]Indem Sie den Bereich in den Feldern "Source" und "Service" verkleinern, verringern Sie die Angriffsfläche. Es sollte der kleinste Bereich verwendet werden, der eine ordnungsgemäße Funktionsweise sicherstellt.

- **Regeln für den Anwendungsdatenverkehr**: Es gibt zwei Regeln für den Anwendungsdatenverkehr, eine für den Datenverkehr im Front-End, eine für den Datenverkehr im Back-End (z. B. vom Webserver zur Datenebene). Diese Regeln richten sich nach der Netzwerkarchitektur (der Platzierung Ihrer Server) und dem Datenverkehrsfluss (Richtung des Datenverkehrs und verwendete Ports).

	Zunächst wird die Front-End-Regel für Webdatenverkehr beschrieben:

	![Firewallwebregel][12]
 
	Mit dieser Ziel-NAT-Regel gelangt der tatsächliche Anwendungsdatenverkehr zum Anwendungsserver. Während die anderen Regeln für Sicherheit, Verwaltung usw. sorgen, ermöglichen Anwendungsregeln es Benutzern oder Diensten, auf die Anwendung(en) zuzugreifen. In diesem Beispiel befindet sich ein einziger Webserver an Port 80. Daher leitet die einzige Firewallanwendungsregel den eingehenden Datenverkehr an die externe IP-Adresse, also die interne IP-Adresse der Webserver um.

	**Hinweis**: Im Feld "Target List" wurde kein Port zugewiesen, daher wird der eingehende Port 80 (oder 443 für den ausgewählten Dienst) für die Umleitung des Webservers verwendet. Wenn der Webserver an einem anderen Port lauscht, z. B. an Port 8080, kann das Feld "Target List" auf den Wert 10.0.1.4:8080 aktualisiert werden, um eine Umleitung auch an diesem Port zu ermöglichen.

	Bei der nächsten Regel für Anwendungsdatenverkehr handelt es sich um die Back-End-Regel, dank derer der Webserver über jeden Dienst mit dem Server AppVM01 (nicht jedoch mit dem Server AppVM02) kommunizieren kann:

	![AppVM01-Firewallregel][13]

	Mit dieser Übergaberegel kann jeder IIS-Server im Front-End-Subnetz den Server AppVM01 (IP-Adresse 10.0.2.5) an jedem Port erreichen und dabei jedes Protokoll verwenden, um auf Daten zuzugreifen, die von der Webanwendung benötigt werden.

	In diesem Screenshot wird "<explicit-dest>" im Feld "Destination" verwendet, um 10.0.2.5 als Ziel anzugeben. Die Festlegung kann, wie hier gezeigt, explizit oder über ein benanntes Netzwerkobjekt erfolgen (wie in den erforderlichen Objekten für den DNS-Server konfiguriert). Der Administrator der Firewall entscheidet darüber, welche Methode verwendet wird. Um 10.0.2.5 als explizites Ziel hinzuzufügen, doppelklicken Sie auf die erste leere Zeile unter <explicit-dest>, und geben Sie im angezeigten Fenster die Adresse ein.

    Mit dieser Übergaberegel wird keine Netzwerkadressübersetzung benötigt, da es sich hier um internen Datenverkehr handelt. Die Verbindungsmethode kann also auf "No SNAT" festgelegt werden.

	**Hinweis**: Das Quellnetzwerk in dieser Regel kann jede Ressource im Front-End-Subnetz sein. Wenn es nur einen oder eine bekannte Anzahl von Webservern gibt, kann eine Netzwerkobjektressource zum Angeben der genauen IP-Adressen dieser Server anstelle des gesamten Front-End-Subnetzes verwendet werden.

>[AZURE.TIP]Diese Regel verwendet den Dienst "Any", um die Einrichtung und Verwendung der Beispielanwendung zu vereinfachen. Dies ermöglicht auch ICMPv4 (Ping) in einer einzigen Regel. Dies ist jedoch kein empfohlenes Verfahren. Die Ports und Protokolle ("Services") sollten auf das Mindestmaß reduziert werden, mit dem ein Anwendungsbetrieb möglich ist, um die Angriffsfläche an dieser Grenze zu verringern.

<br />

>[AZURE.TIP]Obwohl in dieser Regel ein Verweis auf ein explizites Ziel verwendet wird, sollte für die gesamte Firewallkonfiguration ein konsistenter Ansatz zur Anwendung kommen. Es empfiehlt sich, in der gesamten Konfiguration ein benanntes Netzwerkobjekt zu verwenden, um die Lesbarkeit und Unterstützung zu vereinfachen. Das explizite Ziel wird hier nur zur Demonstration einer alternativen Verweismethode verwendet und wird (insbesondere in komplexen Konfigurationen) nicht allgemein empfohlen.

- **Regel für ausgehenden Datenverkehr zum Internet**: Mit dieser Übergaberegel kann Datenverkehr aus jedem Quellnetzwerk an die ausgewählten Zielnetzwerke übergeben werden. Diese Regel ist üblicherweise standardmäßig in der Barracuda NG Firewall vorhanden, jedoch deaktiviert. Klicken Sie mit der rechten Maustaste auf diese Regel, um den Befehl zum Aktivieren der Regel aufzurufen. Die hier gezeigte Regel wurde bearbeitet, um die beiden lokalen Subnetze hinzuzufügen, die in diesem Dokument im Abschnitt zu Voraussetzungen als Verweise auf das Quellattribut dieser Regel erstellt wurden.

	![Ausgehende Firewallregel][14]

- **DNS-Regel**: Mit dieser Übergaberegel kann nur DNS-Datenverkehr (Port 53) an den DNS-Server übergeben werden. Für diese Umgebung wird der größte Teil des Datenverkehrs aus dem Front-End an das Back-End blockiert. Diese Regel lässt DNS-Datenverkehr explizit zu.

	![DNS-Firewallregel][15]

	**Hinweis**: In diesem Screenshot ist auch die Verbindungsmethode enthalten. Da diese Regel für Datenverkehr zwischen internen IP-Adressen gedacht ist, ist keine Netzwerkadressübersetzung erforderlich. Daher ist die Verbindungsmethode für diese Übergaberegel auf "No SNAT" festgelegt.

- **Subnetz-zu-Subnetz-Regel**: Hierbei handelt es sich um eine Standardübergaberegel, die aktiviert und bearbeitet wurde, um jedem Server im Back-End-Subnetz die Herstellung einer Verbindung mit jedem Server im Front-End-Subnetz zu ermöglichen. Diese Regel wirkt sich nur auf internen Datenverkehr aus, daher kann die Verbindungsmethode auf "No SNAT" festgelegt werden.

	![VNet-interne Firewallregel][16]

	**Hinweis**: Das Kontrollkästchen für bidirektionalen Datenverkehr ist deaktiviert (dies gilt für die meisten Regeln). Dies ist für diese Regel von großer Bedeutung, da die Regel damit unidirektional ist, eine Verbindung also nur vom Back-End-Subnetz zum Front-End-Netzwerk initiiert werden kann, nicht jedoch umgekehrt. Wäre dieses Kontrollkästchen aktiviert, würde die Regel bidirektionalen Datenverkehr zulassen, was in unserem logischen Diagramm nicht vorgesehen ist.

- **Regel zum Ablehnen jeglichen Datenverkehrs**: Dies sollte (hinsichtlich der Priorität) die letzte Regel sein. Sollte ein Datenverkehrsfluss keiner der vorherigen Regeln entsprechen, wird er durch diese Regel verworfen. Dies ist eine Standardregel, die üblicherweise aktiviert ist. Im Allgemeinen sind keine Änderungen erforderlich.

	![Firewallregel "Alle ablehnen"][17]

>[AZURE.IMPORTANT]Nachdem alle oben genannten Regeln erstellt wurden, ist es wichtig, die Priorität jeder einzelnen Regel zu prüfen, um sicherzustellen, dass der Datenverkehr wie gewünscht zugelassen oder abgelehnt wird. In diesem Beispiel befinden sich die Regeln im Barracuda-Verwaltungsclient in der Hauptanzeige der Weiterleitungsregeln in der gewünschten Reihenfolge.

## Regelaktivierung
Nachdem der Regelsatz gemäß den Angaben des logischen Diagramms geändert wurde, muss er auf die Firewall hochgeladen und dann aktiviert werden.

![Aktivierung der Firewallregeln][18]
 
In der oberen rechten Ecke des Verwaltungsclients befinden sich verschiedene Schaltflächen. Klicken Sie auf die Schaltfläche „Send Changes“, um die geänderten Regeln an die Firewall zu senden, und klicken Sie dann auf die Schaltfläche „Activate“.
 
Mit der Aktivierung des Firewallregelsatzes ist die Erstellung dieser Beispielumgebung abgeschlossen.

## Datenverkehrsszenarien
>[AZURE.IMPORTANT]An dieser Stelle muss daran erinnert werden, dass **sämtlicher** Datenverkehr durch die Firewall geleitet wird. Um also eine Remotedesktopverbindung mit dem IIS01-Server herzustellen – selbst wenn dieser sich im Front-End-Clouddienst und im Front-End-Subnetz befindet –, ist für den Zugriff auf diesen Server eine RDP-Verbindung mit der Firewall über Port 8014 erforderlich. Anschließend muss der Firewall ermöglicht werden, die RDP-Anforderung intern an den RDP-Port auf IIS01 weiterzuleiten. Die Schaltfläche "Verbinden" im Azure-Portal funktioniert hier nicht, da es keinen direkten RDP-Pfad zu IIS01 gibt (zumindest nicht für das Portal). Dies bedeutet, dass alle Verbindungen aus dem Internet an den Sicherheitsdienst und einen Port, z. B. secscv001.cloudapp.net:xxxx, weitergeleitet werden.

Für diese Szenarien sollten folgende Firewallregeln eingerichtet sein:

1.	Firewallverwaltung
2.	RDP an IIS01
3.	RDP an DNS01
4.	RDP an AppVM01
5.	RDP an AppVM02
6.	Datenverkehr aus Anwendung zum Web
7.	Datenverkehr aus Anwendung an AppVM01
8.	Ausgehend zum Internet
9.	Front-End an DNS01
10.	Subnetzinterner Datenverkehr (nur vom Back-End zum Front-End)
11.	Alle ablehnen

Der tatsächliche Regelsatz einer Firewall umfasst höchstwahrscheinlich noch viele weitere Regeln, die auch andere Prioritäten aufweisen als die hier aufgeführten. Diese Liste und die Nummerierung sollen nur die Relevanz zwischen diesen elf Regeln und die relative Priorität untereinander verdeutlichen. Anders gesagt: in der tatsächlichen Firewall kann es sich bei "RDP an IIS01" um Regel Nr. 5 handeln, solange diese Regel sich jedoch unterhalb der Regel "Firewallverwaltung" und oberhalb von "RDP an DNS01" befindet, entspricht die Konfiguration der Intention dieser Liste. Diese Liste trägt in den unten stehenden Szenarien auch zur Kürze bei, z. B. "WL-Regel 9 (DNS)". (WL steht für Weiterleitung.) Ebenfalls aus Gründen der Kürze werden die vier RDP-Regeln kollektiv als "die RDP-Regeln" bezeichnet, wenn es in einem Datenverkehrsszenario nicht um RDP geht.

Denken Sie auch daran, dass Netzwerksicherheitsgruppen für den eingehenden Internetdatenverkehr in den Front-End- und Back-End-Subnetzen eingerichtet wurden.

#### (Zulässig) Internet an Webserver
1.	Internetbenutzer fordert HTTP-Seite von SecSvc001.CloudApp.Net (Clouddienst mit Schnittstelle zum Internet) an.
2.	Clouddienst übergibt Datenverkehr über offenen Endpunkt an Port 80 an Firewallschnittstelle an 10.0.0.4:80.
3.	Dem Sicherheitssubnetz ist keine NSG zugewiesen, daher lassen die systemeigenen NSG-Regeln den Datenverkehr an die Firewall zu.
4.	Datenverkehr trifft an interner IP-Adresse der Firewall ein (10.0.1.4).
5.	Firewall beginnt mit Regelverarbeitung:
  1.	WL-Regel 1 (WL-Verwaltung) trifft nicht zu, weiter zur nächsten Regel.
  2.	WL-Regeln 2-5 (RDP-Regeln) treffen nicht zu, weiter zur nächsten Regel.
  3.	WL-Regel 6 (Anwendung-Web) trifft zu, Datenverkehr wird zugelassen, Firewall leitet per NAT an 10.0.1.4 (IIS01) weiter.
6.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (Internet blockieren) trifft nicht zu (der Datenverkehr wurde von der Firewall per NAT weitergeleitet, daher gilt als Quelladresse jetzt die Firewall, die sich im Sicherheitssubnetz befindet und von der NSG des Front-End-Subnetzes als "lokaler" Datenverkehrs angesehen und daher zugelassen wird), weiter zur nächsten Regel.
  2.	NSG-Standardregeln lassen Datenverkehr zwischen Subnetzen zu, Datenverkehr wird zugelassen, NSG-Regelverarbeitung wird beendet.
7.	IIS01 lauscht auf Webdatenverkehr, empfängt diese Anforderung und beginnt mit der Verarbeitung der Anforderung.
8.	IIS01 versucht, eine FTP-Sitzung mit AppVM01 im Back-End-Subnetz zu initiieren.
9.	Aufgrund der UDR-Route im Front-End-Subnetz ist die Firewall der nächste Hop.
10.	Keine ausgehenden Regeln im Front-End-Subnetz, Datenverkehr wird zugelassen.
11.	Firewall beginnt mit Regelverarbeitung:
  1.	WL-Regel 1 (WL-Verwaltung) trifft nicht zu, weiter zur nächsten Regel.
  2.	WL-Regeln 2-5 (RDP-Regeln) treffen nicht zu, weiter zur nächsten Regel.
  3.	WL-Regel 6 (Anwendung-Web) trifft nicht zu, weiter zur nächsten Regel.
  4.	WL-Regel 7 (Anwendung-Back-End) trifft zu, Datenverkehr wird zugelassen, Firewall leitet Datenverkehr an 10.0.2.5 (AppVM01).
12.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (Internet blockieren) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Standardregeln lassen Datenverkehr zwischen Subnetzen zu, Datenverkehr wird zugelassen, NSG-Regelverarbeitung wird beendet.
13.	 AppVM01 empfängt die Anforderung, initiiert die Sitzung und antwortet.
14.	Aufgrund der UDR-Route im Back-End-Subnetz ist die Firewall der nächste Hop.
15.	Da es keine ausgehenden NSG-Regeln im Back-End-Subnetz gibt, wird die Antwort zugelassen.
16.	Da es sich hier um zurückkommenden Datenverkehr in einer vorhandenen Sitzung handelt, übergibt die Firewall die Antwort an den Webserver (IIS01).
17.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (Internet blockieren) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Standardregeln lassen Datenverkehr zwischen Subnetzen zu, Datenverkehr wird zugelassen, NSG-Regelverarbeitung wird beendet.
18.	Der IIS-Server empfängt die Antwort, schließt die Transaktion mit AppVM01 ab, und schließt dann die Erstellung der HTTP-Antwort ab. Diese HTTP-Antwort wird an den Anforderer gesendet.
19.	Da es keine ausgehenden NSG-Regeln im Front-End-Subnetz gibt, wird die Antwort zugelassen.
20.	Die HTTP-Antwort trifft an der Firewall ein, und da es sich um die Antwort auf eine vorhandene NAT-Sitzung handelt, wird sie von der Firewall akzeptiert.
21.	Die Firewall leitet die Antwort anschließend an den Internetbenutzer weiter.
22.	Da im Front-End-Subnetz keine ausgehenden NSG-Regeln oder UDR-Hops vorhanden sind, wird die Antwort zugelassen, und der Internetbenutzer empfängt die angeforderte Webseite.

#### (Zulässig) Internet-RDP an Back-End
1.	Serveradministrator im Internet fordert RDP-Sitzung mit AppVM01 über SecSvc001.CloudApp.Net:8025 an. 8025 ist die vom Benutzer zugewiesene Portnummer für die Firewallregel "RDP an AppVM01".
2.	Der Clouddienst übergibt den Datenverkehr über den offenen Endpunkt an Port 8025 an die Firewallschnittstelle an 10.0.0.4:8025.
3.	Dem Sicherheitssubnetz ist keine NSG zugewiesen, daher lassen die systemeigenen NSG-Regeln den Datenverkehr an die Firewall zu.
4.	Firewall beginnt mit Regelverarbeitung:
  1.	WL-Regel 1 (WL-Verwaltung) trifft nicht zu, weiter zur nächsten Regel.
  2.	WL-Regel 2 (RDP-IIS) trifft nicht zu, weiter zur nächsten Regel.
  3.	WL-Regel 3 (RDP-DNS01) trifft nicht zu, weiter zur nächsten Regel.
  4.	WL-Regel 4 (RDP-AppVM01) trifft zu, Datenverkehr wird zugelassen, Firewall leitet per NAT an 10.0.2.5:3386 (RDP-Port auf AppVM01) weiter.
5.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (Internet blockieren) trifft nicht zu (der Datenverkehr wurde von der Firewall per NAT weitergeleitet, daher gilt als Quelladresse jetzt die Firewall, die sich im Sicherheitssubnetz befindet und von der NSG des Back-End-Subnetzes als "lokaler" Datenverkehrs angesehen und daher zugelassen wird), weiter zur nächsten Regel.
  2.	NSG-Standardregeln lassen Datenverkehr zwischen Subnetzen zu, Datenverkehr wird zugelassen, NSG-Regelverarbeitung wird beendet.
6.	AppVM01 lauscht auf RDP-Datenverkehr und antwortet.
7.	Da keine ausgehenden NSG-Regeln vorhanden sind, werden Standardregeln angewendet, und der zurückkommende Datenverkehr wird zugelassen.
8.	UDR routet ausgehenden Datenverkehr an die Firewall, die den nächsten Hop darstellt.
9.	Da es sich hier um zurückkommenden Datenverkehr in einer vorhandenen Sitzung handelt, übergibt die Firewall die Antwort an den Internetbenutzer.
10.	Die RDP-Sitzung wird aktiviert.
11.	AppVM01 fordert zur Eingabe von Benutzername und Kennwort auf.

#### (Zugelassen) Webserver-DNS-Lookup auf DNS-Server
1.	Der Webserver IIS01 benötigt einen Datenfeed von „www.data.gov“, muss jedoch die Adresse auflösen.
2.	Die Netzwerkkonfiguration für das VNet listet DNS01 (10.0.2.4 im Back-End-Subnetz) als primären DNS-Server, IIS01 sendet die DNS-Anforderung an DNS01.
3.	UDR routet ausgehenden Datenverkehr an die Firewall, die den nächsten Hop darstellt.
4.	An das Front-End-Subnetz sind keine ausgehenden NSG-Regeln gebunden, Datenverkehr wird zugelassen.
5.	Firewall beginnt mit Regelverarbeitung:
  1.	WL-Regel 1 (WL-Verwaltung) trifft nicht zu, weiter zur nächsten Regel.
  2.	WL-Regeln 2-5 (RDP-Regeln) treffen nicht zu, weiter zur nächsten Regel.
  3.	WL-Regeln 6 und 7 (Anwendungsregeln) treffen nicht zu, weiter zur nächsten Regel.
  4.	WL-Regel 8 (ins Internet) trifft nicht zu, weiter zur nächsten Regel.
  5.	WL-Regel 9 (DNS) trifft zu, Datenverkehr wird zugelassen, Firewall leitet Datenverkehr an 10.0.2.4 (DNS01).
6.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (Internet blockieren) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Standardregeln lassen Datenverkehr zwischen Subnetzen zu, Datenverkehr wird zugelassen, NSG-Regelverarbeitung wird beendet.
7.	DNS-Server empfängt Anforderung.
8.	Die Adresse ist nicht im DNS-Server zwischengespeichert, daher fragt der DNS-Server die Adresse bei einem DNS-Stammserver im Internet ab.
9.	UDR routet ausgehenden Datenverkehr an die Firewall, die den nächsten Hop darstellt.
10.	Keine ausgehenden NSG-Regeln im Back-End-Subnetz, Datenverkehr wird zugelassen.
11.	Firewall beginnt mit Regelverarbeitung:
  1.	WL-Regel 1 (WL-Verwaltung) trifft nicht zu, weiter zur nächsten Regel.
  2.	WL-Regeln 2-5 (RDP-Regeln) treffen nicht zu, weiter zur nächsten Regel.
  3.	WL-Regeln 6 und 7 (Anwendungsregeln) treffen nicht zu, weiter zur nächsten Regel.
  4.	 WL-Regel 8 (ins Internet) trifft zu, Datenverkehr wird zugelassen, Sitzung wird per SNAT an den DNS-Stammserver im Internet weitergeleitet.
12.	Der DNS-Server im Internet antwortet. Da diese Sitzung von der Firewall aus initiiert wurde, wird die Antwort von der Firewall akzeptiert.
13.	Da es sich hier um eine vorhandene Sitzung handelt, leitet die Firewall die Antwort an den Ausgangsserver DNS01 weiter.
14.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (Internet blockieren) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Standardregeln lassen Datenverkehr zwischen Subnetzen zu, Datenverkehr wird zugelassen, NSG-Regelverarbeitung wird beendet.
15.	Der DNS-Server empfängt die Antwort, speichert sie zwischen und gibt dann die Antwort auf die ursprüngliche Anforderung zurück an IIS01.
16.	Aufgrund der UDR-Route im Back-End-Subnetz ist die Firewall der nächste Hop.
17.	Im Back-End-Subnetz sind keine ausgehenden NSG-Regeln vorhanden, Datenverkehr wird zugelassen.
18.	Dies ist eine vorhandene Sitzung in der Firewall, die Antwort wird von der Firewall an den IIS-Server weitergeleitet.
19.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	Es gibt keine NSG-Regel für eingehenden Datenverkehr vom Back-End- zum Front-End-Subnetz, daher trifft keine der NSG-Regeln zu.
  2.	Da die standardmäßige Systemregel Datenverkehr zwischen Subnetzen zulässt, wird der Datenverkehr zugelassen.
20.	IIS01 empfängt die Antwort von DNS01.

#### (Zulässig) Back-End-Server an Front-End-Server
1.	Ein über RDP bei AppVM02 angemeldeter Administrator fordert eine Datei über den Windows-Datei-Explorer direkt vom IIS01-Server an.
2.	Aufgrund der UDR-Route im Back-End-Subnetz ist die Firewall der nächste Hop.
3.	Da es keine ausgehenden NSG-Regeln im Back-End-Subnetz gibt, wird die Antwort zugelassen.
4.	Firewall beginnt mit Regelverarbeitung:
  1.	WL-Regel 1 (WL-Verwaltung) trifft nicht zu, weiter zur nächsten Regel.
  2.	WL-Regeln 2-5 (RDP-Regeln) treffen nicht zu, weiter zur nächsten Regel.
  3.	WL-Regeln 6 und 7 (Anwendungsregeln) treffen nicht zu, weiter zur nächsten Regel.
  4.	WL-Regel 8 (ins Internet) trifft nicht zu, weiter zur nächsten Regel.
  5.	WL-Regel 9 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  6.	WL-Regel 10 (subnetzintern) trifft zu, Datenverkehr wird zugelassen, Firewall übergibt Datenverkehr an 10.0.1.4 (IIS01).
5.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (Internet blockieren) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Standardregeln lassen Datenverkehr zwischen Subnetzen zu, Datenverkehr wird zugelassen, NSG-Regelverarbeitung wird beendet.
6.	Vorausgesetzt, Authentifizierung und Autorisierung sind ordnungsgemäß erfolgt, akzeptiert IIS01 die Anforderung und antwortet.
7.	Aufgrund der UDR-Route im Front-End-Subnetz ist die Firewall der nächste Hop.
8.	Da es keine ausgehenden NSG-Regeln im Front-End-Subnetz gibt, wird die Antwort zugelassen.
9.	Da es sich hier um eine vorhandene Sitzung in der Firewall handelt, wird die Antwort zugelassen, und die Firewall gibt die Antwort an AppVM02 zurück.
10.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (Internet blockieren) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Standardregeln lassen Datenverkehr zwischen Subnetzen zu, Datenverkehr wird zugelassen, NSG-Regelverarbeitung wird beendet.
11.	AppVM02 empfängt die Antwort.

#### (Abgelehnt) Internet direkt an Webserver
1.	Internetbenutzer versucht, über den FrontEnd001.CloudApp.Net-Dienst auf Webserver IIS01 zuzugreifen.
2.	Da keine Endpunkte für HTTP-Datenverkehr geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher den Server nicht erreichen.
3.	Wären aus irgendeinem Grund Endpunkte offen, würde die NSG-Regel (Internet blockieren) im Front-End-Subnetz diesen Datenverkehr blockieren.
4.	Und schließlich würde die UDR-Route im Front-End-Subnetz jeglichen ausgehenden Datenverkehr von IIS01 an die Firewall (als nächsten Hop) senden. Die Firewall würde dies als asymmetrischen Datenverkehr betrachten und die ausgehende Antwort verwerfen. Daher existieren mindestens drei verschiedene Sicherheitsstufen zwischen dem Internet und IIS01 über den zugehörigen Clouddienst, um einen nicht autorisierten bzw. unangemessenen Zugriff zu verhindern.

#### (Abgelehnt) Internet an Back-End-Server
1.	Internetbenutzer versucht, über den BackEnd001.CloudApp.Net-Dienst auf eine Datei auf AppVM01 zuzugreifen.
2.	Da keine Endpunkte für Dateifreigaben geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher den Server nicht erreichen.
3.	Wären aus irgendeinem Grund Endpunkte offen, würde die NSG-Regel (Internet blockieren) diesen Datenverkehr blockieren.
4.	Und schließlich würde die UDR-Route jeglichen ausgehenden Datenverkehr von AppVM01 an die Firewall (als nächsten Hop) senden. Die Firewall würde dies als asymmetrischen Datenverkehr betrachten und die ausgehende Antwort verwerfen. Daher existieren mindestens drei verschiedene Sicherheitsstufen zwischen Internet und AppVM01 über den zugehörigen Clouddienst, um einen nicht autorisierten bzw. unangemessenen Zugriff zu verhindern.

#### (Abgelehnt) Front-End-Server an Back-End-Server
1.	Angenommen, die Sicherheit von IIS01 wäre beeinträchtigt und auf dem Server würde Schadsoftware ausgeführt, die versucht, die Back-End-Subnetzserver zu scannen.
2.	Die UDR-Route im Front-End-Subnetz würde jeglichen ausgehenden Datenverkehr von IIS01 an die Firewall als nächsten Hop senden. Dies kann durch den virtuellen Computer, dessen Sicherheit beeinträchtigt ist, nicht geändert werden.
3.	Die Firewall würde den Datenverkehr verarbeiten. Wenn die Anforderung an AppVM01 oder an den DNS-Server für ein DNS-Lookup gesendet würde, könnte dieser Datenverkehr (aufgrund der WL-Regeln 7 und 9) möglicherweise von der Firewall zugelassen werden. Jeglicher weiterer Datenverkehr würde durch WL-Regel 11 (alle ablehnen) blockiert.
4.	Wenn in der Firewall eine erweiterte Bedrohungserkennung aktiviert wäre (dieses Thema wird im vorliegenden Dokument nicht behandelt, Informationen zu den erweiterten Funktionen zur Abwehr von Bedrohungen erhalten Sie in der Anbieterdokumentation für Ihr Netzwerkgerät), würde selbst Datenverkehr abgelehnt, der von den in diesem Thema beschriebenen grundlegenden Weiterleitungsregeln weitergeleitet würde. Voraussetzung hierfür wäre, dass der Datenverkehr bekannte Signaturen oder Muster enthält, die eine erweiterte Bedrohungsregel auslösen.

#### (Abgelehnt) Internet-DNS-Lookup im DNS-Server
1.	Internetbenutzer versucht, über den BackEnd001.CloudApp.Net-Dienst auf DNS01 einen internen DNS-Eintrag nachzuschlagen. 
2.	Da keine Endpunkte für DNS-Datenverkehr geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher den Server nicht erreichen.
3.	Wären aus irgendeinem Grund Endpunkte offen, würde die NSG-Regel (Internet blockieren) im Front-End-Subnetz diesen Datenverkehr blockieren.
4.	Und schließlich würde die UDR-Route im Back-End-Subnetz jeglichen ausgehenden Datenverkehr von DNS01 an die Firewall (als nächsten Hop) senden. Die Firewall würde dies als asymmetrischen Datenverkehr betrachten und die ausgehende Antwort verwerfen. Daher existieren mindestens drei verschiedene Sicherheitsstufen zwischen Internet und DNS01 über den zugehörigen Clouddienst, um einen nicht autorisierten bzw. unangemessenen Zugriff zu verhindern.

#### (Abgelehnt) Zugriff auf SQL aus dem Internet über Firewall
1.	Internetbenutzer fordert SQL-Daten von SecSvc001.CloudApp.Net (Clouddienst mit Schnittstelle zum Internet) an.
2.	Da keine Endpunkte für SQL geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher die Firewall nicht erreichen.
3.	Wären aus irgendeinem Grund SQL-Endpunkte offen, würde die Firewall mit der Regelverarbeitung beginnen:
  1.	WL-Regel 1 (WL-Verwaltung) trifft nicht zu, weiter zur nächsten Regel.
  2.	WL-Regeln 2-5 (RDP-Regeln) treffen nicht zu, weiter zur nächsten Regel.
  3.	WL-Regeln 6 & 7 (Anwendungsregeln) treffen nicht zu, weiter zur nächsten Regel.
  4.	WL-Regel 8 (ins Internet) trifft nicht zu, weiter zur nächsten Regel.
  5.	WL-Regel 9 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  6.	WL-Regel 10 (subnetzintern) trifft nicht zu, weiter zur nächsten Regel.
  7.	WL-Regel 11 (alle ablehnen) trifft zu, Datenverkehr wird blockiert, Regelverarbeitung wird beendet.


## Referenzen
### Hauptskript und Netzwerkkonfiguration
Speichern Sie das vollständige Skript in einer PowerShell-Skriptdatei. Speichern Sie die Netzwerkkonfiguration in einer Datei namens „NetworkConf2.xml“. Bearbeiten Sie die benutzerdefinierten Variablen nach Bedarf. Führen Sie das Skript aus, und befolgen Sie dann oben stehende Anweisungen zur Einrichtung der Firewallregel.

#### Vollständiges Skript
Dieses Skript führt basierend auf den benutzerdefinierten Variablen Folgendes aus:

1.	Herstellen einer Verbindung mit einem Azure-Abonnement
2.	Erstellen eines neuen Speicherkontos
3.	Erstellen eines neuen VNets und dreier Subnetze, wie in der Netzwerkkonfigurationsdatei definiert
4.	Erstellen von fünf virtuellen Computern: einen als Firewall und vier als Windows-Server
5.	Konfigurieren von UDR einschließlich:
  1.	Erstellen von zwei neuen Routingtabellen
  2.	Hinzufügen von Routen zu den Tabellen
  3.	Binden der Tabellen an geeignete Subnetze
6.	Aktivieren der IP-Weiterleitung auf dem virtuellen Netzwerkgerät
7.	Konfigurieren von Netzwerksicherheitsgruppen einschließlich:
  1.	Erstellen einer Netzwerksicherheitsgruppe
  2.	Hinzufügen einer Regel
  3.	Binden der Netzwerksicherheitsgruppe an die geeigneten Subnetze

Dieses PowerShell-Skript sollte lokal auf einem mit dem Internet verbundenen PC oder Server ausgeführt werden.

>[AZURE.IMPORTANT]Während der Ausführung des Skripts werden in PowerShell möglicherweise Warnungen oder Informationsmeldungen angezeigt. Nur in Rot angezeigte Fehlermeldungen müssen genauer beachtet und ggf. gelöst werden.

	<# 
	 .SYNOPSIS
	  Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
	
	 .DESCRIPTION
	  This script will build out a sample DMZ setup containing:
	   - A default storage account for VM disks
	   - Three new cloud services
	   - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
	   - A Network Virtual Appliance (NVA), in this case a Barracuda NG Firewall
	   - One server on the FrontEnd Subnet
	   - Three Servers on the BackEnd Subnet
	   - IP Forwading from the FireWall out to the internet
	   - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
	
	  Before running script, ensure the network configuration file is created in
	  the directory referenced by $NetworkConfigFile variable (or update the
	  variable to reflect the path and file name of the config file being used).
	
	 .Notes
	  Everyone's security requirements are different and can be addressed in a myriad of ways.
	  Please be sure that any sensitive data or applications are behind the appropriate
	  layer(s) of protection. This script serves as an example of some of the techniques
	  that can be used, but should not be used for all scenarios. You are responsible to
	  assess your security needs and the appropriate protections needed, and then effectively
	  implement those protections.
	
	  Security Service (SecNet subnet 10.0.0.0/24)
	   myFirewall - 10.0.0.4
	 
	  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
	   IIS01      - 10.0.1.4
	 
	  BackEnd Service (BackEnd subnet 10.0.2.0/24)
	   DNS01      - 10.0.2.4
	   AppVM01    - 10.0.2.5
	   AppVM02    - 10.0.2.6
	
	#>
	
	# Fixed Variables
	    $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
	    $VMName = @()
	    $ServiceName = @()
	    $VMFamily = @()
	    $img = @()
	    $size = @()
	    $SubnetName = @()
	    $VMIP = @()
	
	# User Defined Global Variables
	  # These should be changes to reflect your subscription and services
	  # Invalid options will fail in the validation section
	
	  # Subscription Access Details
	    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	
	  # VM Account, Location, and Storage Details
	    $LocalAdmin = "theAdmin"
	    $DeploymentLocation = "Central US"
	    $StorageAccountName = "vmstore02"
	
	  # Service Details
	    $SecureService = "SecSvc001"
	    $FrontEndService = "FrontEnd001"
	    $BackEndService = "BackEnd001"
	
	  # Network Details
	    $VNetName = "CorpNetwork"
	    $VNetPrefix = "10.0.0.0/16"
	    $SecNet = "SecNet"
	    $FESubnet = "FrontEnd"
	    $FEPrefix = "10.0.1.0/24"
	    $BESubnet = "BackEnd"
	    $BEPrefix = "10.0.2.0/24"
	    $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
	
	  # VM Base Disk Image Details
	    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	    $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NG Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	
	  # UDR Details
	    $FERouteTableName = "FrontEndSubnetRouteTable"
	    $BERouteTableName = "BackEndSubnetRouteTable"
	
	  # NSG Details
	    $NSGName = "MyVNetSG"
	
	# User Defined VM Specific Config
	    # Note: To ensure UDR and IP forwarding is setup
	    # properly this script requires VM 0 be the NVA.
	
	    # VM 0 - The Network Virtual Appliance (NVA)
	      $VMName += "myFirewall"
	      $ServiceName += $SecureService
	      $VMFamily += "Firewall"
	      $img += $FWImg
	      $size += "Small"
	      $SubnetName += $SecNet
	      $VMIP += "10.0.0.4"
	
	    # VM 1 - The Web Server
	      $VMName += "IIS01"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.4"
	
	    # VM 2 - The First Appliaction Server
	      $VMName += "AppVM01"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.5"
	
	    # VM 3 - The Second Appliaction Server
	      $VMName += "AppVM02"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.6"
	
	    # VM 4 - The DNS Server
	      $VMName += "DNS01"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.4"
	
	# ----------------------------- #
	# No User Defined Varibles or   #
	# Configuration past this point #
	# ----------------------------- #
	
	  # Get your Azure accounts
	    Add-AzureAccount
	    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
	    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
	
	  # Create Storage Account
	    If (Test-AzureName -Storage -Name $StorageAccountName) { 
	        Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
	        Return}
	    Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
	          New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
	
	  # Update Subscription Pointer to New Storage Account
	    Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
	    Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
	
	# Validation
	$FatalError = $false
	
	If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
	     Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
	     $FatalError = $true}
	
	If (Test-AzureName -Service -Name $SecureService) { 
	    Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
	
	If (Test-AzureName -Service -Name $FrontEndService) { 
	    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
	
	If (Test-AzureName -Service -Name $BackEndService) { 
	    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
	
	If (-Not (Test-Path $NetworkConfigFile)) { 
	    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The network config file was found" -ForegroundColor Green
	        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
	            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
	            $FatalError = $true}
	        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
	
	If ($FatalError) {
	    Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
	    Return}
	Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
	
	# Create VNET
	    Write-Host "Creating VNET" -ForegroundColor Cyan 
	    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
	
	# Create Services
	    Write-Host "Creating Services" -ForegroundColor Cyan
	    New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
	    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
	    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
	
	# Build VMs
	    $i=0
	    $VMName | Foreach {
	        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
	        If ($VMFamily[$i] -eq "Firewall") 
	            { 
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	            # Set up all the EndPoints we'll need once we're up and running
	            # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
	            #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
	            #       rule, so all of these endpoint have the same public and local port and the firewall
	            #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
	            Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
	            }
	        Else
	            {
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
	                Remove-AzureEndpoint -Name "RemoteDesktop" | `
	                Remove-AzureEndpoint -Name "PowerShell" | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	            }
	        $i++
	    }
	
	# Configure UDR and IP Forwarding
	    Write-Host "Configuring UDR" -ForegroundColor Cyan
	
	  # Create the Route Tables
	    Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
	    New-AzureRouteTable -Name $BERouteTableName `
	        -Location $DeploymentLocation `
	        -Label "Route table for $BESubnet subnet"
	    New-AzureRouteTable -Name $FERouteTableName `
	        -Location $DeploymentLocation `
	        -Label "Route table for $FESubnet subnet"
	
	  # Add Routes to Route Tables
	    Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
	        -NextHopType VNETLocal
	    Get-AzureRouteTable $FERouteTableName `
	        |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $FERouteTableName `
	        |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $FERouteTableName `
	        |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
	        -NextHopType VNETLocal
	
	  # Assoicate the Route Tables with the Subnets
	    Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
	    Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
	        -SubnetName $BESubnet `
	        -RouteTableName $BERouteTableName
	    Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
	        -SubnetName $FESubnet `
	        -RouteTableName $FERouteTableName
	
	 # Enable IP Forwarding on the Virtual Appliance
	    Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
	        |Set-AzureIPForwarding -Enable
	
	# Configure NSG
	    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
	
	  # Build the NSG
	    Write-Host "Building the NSG" -ForegroundColor Cyan
	    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
	
	  # Add NSG Rule
	    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
	        -Protocol *
	
	  # Assign the NSG to two Subnets
	    # The NSG is *not* bound to the Security Subnet. The result
	    # is that internet traffic flows only to the Security subnet
	    # since the NSG bound to the Frontend and Backback subnets
	    # will Deny internet traffic to those subnets.
	    Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
	    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
	    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
	
	# Optional Post-script Manual Configuration
	  # Configure Firewall
	  # Install Test Web App (Run Post-Build Script on the IIS Server)
	  # Install Backend resource (Run Post-Build Script on the AppVM01)
	  Write-Host
	  Write-Host "Build Complete!" -ForegroundColor Green
	  Write-Host
	  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
	  Write-Host " - Configure Firewall" -ForegroundColor Gray
	  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
	  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
	  Write-Host
	

#### Netzwerkkonfigurationsdatei
Speichern Sie diese XML-Datei mit dem aktualisierten Speicherort, und fügen Sie den Link zu dieser Datei in die $NetworkConfigFile-Variable im obigen Skript ein.

	<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
	  <VirtualNetworkConfiguration>
	    <Dns>
	      <DnsServers>
	        <DnsServer name="DNS01" IPAddress="10.0.2.4" />
	        <DnsServer name="Level3" IPAddress="209.244.0.3" />
	      </DnsServers>
	    </Dns>
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="CorpNetwork" Location="Central US">
	        <AddressSpace>
	          <AddressPrefix>10.0.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="SecNet">
	            <AddressPrefix>10.0.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="FrontEnd">
	            <AddressPrefix>10.0.1.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="BackEnd">
	            <AddressPrefix>10.0.2.0/24</AddressPrefix>
	          </Subnet>
	        </Subnets>
	        <DnsServersRef>
	          <DnsServerRef name="DNS01" />
	          <DnsServerRef name="Level3" />
	        </DnsServersRef>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>
	  </VirtualNetworkConfiguration>
	</NetworkConfiguration>

#### Beispielanwendungsskripts
Wenn Sie eine Beispielanwendung für dieses und weitere DMZ-Beispiele installieren möchten, finden Sie eine Anwendung dieser Art unter folgendem Link: [Beispielanwendungsskript][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Bidirektionale DMZ mit virtuellem Netzwerkgerät, Netzwerksicherheitsgruppe und benutzerdefiniertem Routing"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Logische Ansicht der Firewallregeln"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Erstellen eines Front-End-Netzwerkobjekts"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Erstellen eines DNS-Serverobjekts"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopie der RDP-Standardregel"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01-Regel"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Symbol der Regel zur Anwendungsumleitung"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Symbol der Ziel-NAT-Regel"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Symbol der Übergaberegel"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Firewallverwaltungsregel"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "RDP-Firewallregel"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Firewallwebregel"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "AppVM01-Firewallregel"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Ausgehende Firewallregel"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "DNS-Firewallregel"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "VNet-interne Firewallregel"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Firewallregel "Alle ablehnen""
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Aktivierung der Firewallregeln"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

<!---HONumber=AcomDC_0121_2016-->