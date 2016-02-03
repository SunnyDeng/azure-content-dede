<properties
   pageTitle="DMZ-Beispiel: Erstellen einer einfachen DMZ mit Netzwerksicherheitsgruppen | Microsoft Azure"
   description="Erstellen einer DMZ mit Netzwerksicherheitsgruppen (NSGs)"
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

# Beispiel 1: Erstellen einer einfachen DMZ mit Netzwerksicherheitsgruppen

[Zurück zur Seite mit bewährten Methoden zu Sicherheitsgrenzen][HOME]

Mit diesem Beispiel wird eine einfache DMZ mit vier Windows-Servern und Netzwerksicherheitsgruppen erstellt. Jeder der relevanten Befehle wird genau erläutert, um ein besseres Verständnis jedes einzelnen Schritts zu ermöglichen. Es gibt außerdem einen Abschnitt mit verschiedenen Szenarien zum Datenverkehr, in denen Schritt für Schritt erläutert wird, wie der Datenverkehr durch die verschiedenen Sicherheitsstufen in der DMZ geleitet wird. Der Referenzabschnitt schließlich enthält den vollständigen Code sowie Anweisungen zum Aufbau dieser Umgebung, damit Sie verschiedene Szenarien testen und ausprobieren können.

![Eingehende DMZ mit NSG][1]

## Beschreibung der Umgebung
Dieses Beispiel umfasst ein Abonnement, das Folgendes enthält:

- Zwei Clouddienste: "FrontEnd001" und "BackEnd001"
- Ein virtuelles Netzwerk, "CorpNetwork", mit zwei Subnetzen: "FrontEnd" und "BackEnd"
- Eine Netzwerksicherheitsgruppe, die auf beide Subnetze angewendet wird
- Eine Windows Server-Instanz, die einen Anwendungswebserver darstellt ("IIS01")
- Zwei Windows Server-Instanzen, die Back-End-Anwendungsserver darstellen ("AppVM01", "AppVM02")
- Eine Windows Server-Instanz, die einen DNS-Server darstellt ("DNS01")

Der Referenzabschnitt enthält ein PowerShell-Skript, mit dem sich der größte Teil der oben beschriebenen Umgebung erstellen lässt. Die Erstellung der virtuellen Computer und Netzwerke wird zwar durch das Beispielskript ausgeführt, aber dies wird in diesem Dokument nicht im Einzelnen beschrieben.

So erstellen Sie die Umgebung

  1.	Speichern Sie die im Referenzabschnitt enthaltene und mit dem Namen, dem Speicherort und den IP-Adressen für das jeweilige Szenario aktualisierte XML-Netzwerkkonfigurationsdatei.
  2.	Aktualisieren Sie die Benutzervariablen im Skript gemäß der Umgebung, in der das Skript ausgeführt werden soll (Abonnements, Dienstnamen usw.).
  3.	Führen Sie das Skript in PowerShell aus.

**Hinweis**: Die im PowerShell-Skript angegebene Region muss der Region in der XML-Netzwerkkonfigurationsdatei entsprechen.

Nach Ausführung des Skripts können weitere Schritte ausgeführt werden. Im Referenzabschnitt stehen zwei Skripts zum Einrichten des Webservers und des Anwendungsservers mit einer einfachen Webanwendung zur Verfügung, um Tests mit dieser DMZ-Konfiguration zu ermöglichen.

In den nächsten Abschnitten werden anhand der wichtigsten Zeilen des PowerShell-Skripts detailliert die Netzwerksicherheitsgruppen sowie deren Funktionsweise in diesem Beispiel beschrieben.

## Netzwerksicherheitsgruppen
Für dieses Beispiel wird eine Netzwerksicherheitsgruppe erstellt und dann mit sechs Regeln geladen.

>[AZURE.TIP]Im Allgemeinen sollten Sie zuerst die spezifischeren Regeln zum Zulassen von Aktionen und danach die allgemeineren Regeln zum Ablehnen von Aktionen erstellen. Die zugewiesene Priorität bestimmt, welche Regeln zuerst ausgewertet werden. Sobald eine Regel auf den Datenverkehr zutrifft, werden keine weiteren Regeln ausgewertet. NSG-Regeln können sowohl in eingehender als auch in ausgehender Richtung zutreffen (aus Perspektive des Subnetzes).

Folgende Regeln werden deklarativ für eingehenden Datenverkehr erstellt:

1.	Interner DNS-Datenverkehr (Port 53) wird zugelassen.
2.	RDP-Datenverkehr (Port 3389) aus dem Internet zu jedem virtuellen Computer wird zugelassen.
3.	HTTP-Datenverkehr (Port 80) aus dem Internet zum Webserver (IIS01) wird zugelassen.
4.	Jeglicher Datenverkehr (alle Ports) von IIS01 zu AppVM1 wird zugelassen.
5.	Jeglicher Datenverkehr (alle Ports) aus dem Internet in das gesamte VNet (beide Subnetze) wird abgelehnt.
6.	Jeglicher Datenverkehr (alle Ports) vom Front-End-Subnetz zum Back-End-Subnetz wird abgelehnt.

Diese Regeln sind an jedes Subnetz gebunden. Wenn also eine HTTP-Anforderung aus dem Internet an den Webserver gesendet wird, gilt sowohl Regel 3 (Zulassen) als auch Regel 5 (Ablehnen). Da jedoch Regel 3 eine höhere Priorität besitzt, wird nur diese angewendet, und Regel 5 wird nicht berücksichtigt. Aus diesem Grund wird die HTTP-Anforderung für den Webserver als zulässig eingestuft. Falls versucht würde, denselben Datenverkehr an den DNS01-Server zu senden, würde Regel 5 (Ablehnen) zuerst angewendet, der Datenverkehr also nicht an den Server übergeben. Mit Regel 6 (Verweigern) wird die Kommunikation des Front-End-Subnetzes mit dem Back-End-Subnetz blockiert (mit Ausnahme von zulässigem Datenverkehr in den Regeln 1 und 4). Dies dient dem Schutz des Back-End-Netzwerks, falls ein Angreifer die Webanwendung am Front-End attackiert. Der Angreifer hat dann nur beschränkten Zugriff auf das „geschützte“ Back-End-Netzwerk (nur auf Ressourcen, die auf dem Server AppVM01 verfügbar gemacht werden).

Es gibt eine Standardregel für ausgehenden Datenverkehr, die das Senden von Datenverkehr an das Internet zulässt. In diesem Beispiel wird ausgehender Datenverkehr zugelassen, und es werden keine ausgehenden Regeln geändert. Um den Datenverkehr in beide Richtungen zu sperren, ist benutzerdefiniertes Routing (User Defined Routing, UDR) erforderlich. Dies wird in Beispiel 3 erläutert.

Jede Regel wird im Folgenden detailliert beschrieben (Hinweis: Jedes Element in der unten gezeigten Liste, das mit einem Dollarzeichen beginnt, wie etwa $NSGName, ist eine benutzerdefinierte Variable aus dem Skript im Referenzabschnitt dieses Dokuments):

1. Zuerst muss eine Netzwerksicherheitsgruppe zum Speichern der Regeln erstellt werden:

	    New-AzureNetworkSecurityGroup -Name $NSGName `
            -Location $DeploymentLocation `
            -Label "Security group for $VNetName subnets in $DeploymentLocation"
 
2.	Mit der ersten Regel in diesem Beispiel wird DNS-Datenverkehr zwischen allen internen Netzwerken und dem DNS-Server im Back-End-Subnetz zugelassen. Die Regel weist einige wichtige Parameter auf:
  - Der Parameter "Type" gibt an, für welche Datenverkehrsrichtung diese Regel gilt – aus der Perspektive des Subnetzes oder des virtuellen Computers (je nachdem, wo diese Netzwerksicherheitsgruppe gebunden wurde). Wenn der Typ "eingehend" lautet und Datenverkehr an das Subnetz gesendet wird, wird die Regel angewendet. Auf Datenverkehr, der das Subnetz verlässt, wirkt sich die Regel dagegen nicht aus.
  - Der Parameter "Priority" legt die Reihenfolge fest, in der ein Datenverkehrsfluss ausgewertet wird. Je niedriger die Nummer ist, desto höher ist die Priorität. Sobald eine Regel auf einen bestimmten Datenverkehrsfluss angewendet wird, werden keine weiteren Regeln verarbeitet. Wenn daher eine Regel mit Priorität 1 Datenverkehr zulässt und eine Regel mit Priorität 2 Datenverkehr ablehnt und beide Regeln auf einen Datenverkehrsfluss zutreffen, wird dieser Datenverkehr zugelassen, da Regel 1 mit der höheren Priorität greift und keine weiteren Regeln angewendet werden.
  - Der Parameter "Action" gibt an, ob Datenverkehr, auf den diese Regel zutrifft, blockiert oder zugelassen wird.

			Get-AzureNetworkSecurityGroup -Name $NSGName | `
                Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
                -Type Inbound -Priority 100 -Action Allow `
                -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
                -DestinationAddressPrefix $VMIP[4] `
                -DestinationPortRange '53' `
                -Protocol *

3.	Diese Regel lässt RDP-Datenverkehr aus dem Internet an den RDP-Port auf jedem Server in beiden Subnetzen im VNet zu. Diese Regel verwendet zwei bestimmte Arten von Adresspräfixen: "VIRTUAL\_NETWORK" und "INTERNET". Dies ist eine einfache Möglichkeit, eine größere Bandbreite an Adresspräfixen zu verarbeiten.

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
	    	Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
	    	-Type Inbound -Priority 110 -Action Allow `
	    	-SourceAddressPrefix INTERNET -SourcePortRange '*' `
	    	-DestinationAddressPrefix VIRTUAL_NETWORK `
	    	-DestinationPortRange '3389' `
	    	-Protocol *

4.	Diese Regel lässt zu, dass eingehender Internetdatenverkehr den Webserver erreicht. Sie ändert nicht das Routingverhalten, sondern ermöglicht nur die Weiterleitung von Datenverkehr mit dem Ziel IIS01. Wenn also eingehender Datenverkehr aus dem Internet den Webserver als Ziel hat, lässt diese Regel den Datenverkehr zu und verhindert die Auswertung weiterer Regeln. (In der Regel mit Priorität 140 wird sämtlicher eingehender Datenverkehr aus dem Internet blockiert.) Wenn Sie nur HTTP-Datenverkehr verarbeiten, kann diese Regel weiter eingeschränkt werden und nur Zielport 80 zulassen.

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
		    Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
    		-Type Inbound -Priority 120 -Action Allow `
    		-SourceAddressPrefix Internet -SourcePortRange '*' `
    		-DestinationAddressPrefix $VMIP[0] `
    		-DestinationPortRange '*' `
    		-Protocol *

5.	Diese Regel lässt Datenverkehr vom IIS01-Server an den AppVM01-Server zu. Eine spätere Regel blockiert sämtlichen Datenverkehr vom Front-End zum Back-End. Wenn der Port bekannt ist, sollte dieser der Regel hinzugefügt werden, um deren Funktionalität zu verbessern. Wenn der IIS-Server z. B. nur Daten an SQL Server auf AppVM01 sendet, sollte der Zielportbereich von "*" (beliebig) in 1433 (dem SQL-Port) geändert werden. So bietet AppVM01 eine geringere Angriffsfläche bei Angriffen auf die Webanwendung.

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
		    Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
		    -Type Inbound -Priority 130 -Action Allow `
	    -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
	    -DestinationAddressPrefix $VMIP[2] `
	    -DestinationPortRange '*' `
	    -Protocol *
 
6.	Dies Regel lehnt Datenverkehr aus dem Internet an alle Server im Netzwerk ab. In Kombination mit den Regeln mit Priorität 110 und 120 wird nur eingehender Datenverkehr aus dem Internet an die Firewall- und RDP-Ports anderer Server zugelassen, jeglicher anderer Datenverkehr wird blockiert.

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
		    Set-AzureNetworkSecurityRule `
		    -Name "Isolate the $VNetName VNet from the Internet" `
		    -Type Inbound -Priority 140 -Action Deny `
		    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
		    -DestinationAddressPrefix VIRTUAL_NETWORK `
		    -DestinationPortRange '*' `
		    -Protocol *
 
7.	Die letzte Regel lehnt Datenverkehr vom Front-End-Subnetz zum Back-End-Subnetz ab. Da es sich um eine eingehende Regel handelt, wird Datenverkehr in umgekehrter Richtung (vom Back-End zum Front-End) zugelassen.

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
    		Set-AzureNetworkSecurityRule `
    		-Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
    		-Type Inbound -Priority 150 -Action Deny `
    		-SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
    		-DestinationAddressPrefix $BEPrefix `
    		-DestinationPortRange '*' `
    		-Protocol * 

## Datenverkehrsszenarien
#### (*Zulässig*) Web an Webserver
1.	Internetbenutzer fordert HTTP-Seite von FrontEnd001.CloudApp.Net (Clouddienst mit Schnittstelle zum Internet) an.
2.	Clouddienst übergibt Datenverkehr über offenen Endpunkt an Port 80 an IIS01 (den Webserver).
3.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft nicht zu, weiter zur nächsten Regel.
  3.	NSG-Regel 3 (Internet an IIS01) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
4.	Datenverkehr trifft an interner IP-Adresse des Webservers IIS01 ein (10.0.1.5).
5.	IIS01 lauscht auf Webdatenverkehr, empfängt diese Anforderung und beginnt mit der Verarbeitung der Anforderung.
6.	IIS01 fragt Informationen von SQL Server unter AppVM01 ab.
7.	Keine ausgehenden Regeln im Front-End-Subnetz, Datenverkehr wird zugelassen.
8.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft nicht zu, weiter zur nächsten Regel.
  3.	NSG-Regel 3 (Internet an Firewall) trifft nicht zu, weiter zur nächsten Regel.
  4.	NSG-Regel 4 (IIS01 an AppVM01) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
9.	AppVM01 empfängt die SQL-Abfrage und antwortet.
10.	Da es keine ausgehenden Regeln im Back-End-Subnetz gibt, wird die Antwort zugelassen.
11.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	Es gibt keine NSG-Regel für eingehenden Datenverkehr vom Back-End- zum Front-End-Subnetz, daher trifft keine der NSG-Regeln zu.
  2.	Da die standardmäßige Systemregel Datenverkehr zwischen Subnetzen zulässt, wird der Datenverkehr zugelassen.
12.	Der IIS-Server empfängt die SQL-Antwort, erstellt die HTTP-Antwort und sendet sie an den Anforderer.
13.	Da im Front-End-Subnetz keine ausgehenden Regeln vorhanden sind, wird die Antwort zugelassen, und der Internetbenutzer empfängt die angeforderte Webseite.

#### *(Zulässig)* RDP an Back-End
1.	Serveradministrator im Internet fordert RDP-Sitzung für AppVM01 an BackEnd001.CloudApp.Net:xxxxx an, wobei "xxxxx" für die zufällig zugewiesene Portnummer für RDP auf AppVM01 steht (den zugewiesenen Port finden Sie im Azure-Portal oder über PowerShell).
2.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
3.	Da keine ausgehenden Regeln vorhanden sind, werden Standardregeln angewendet, und der zurückkommende Datenverkehr wird zugelassen.
4.	Die RDP-Sitzung wird aktiviert.
5.	AppVM01 fordert zur Eingabe von Benutzername und Kennwort auf.

#### (*Zulässig*) Webserver-DNS-Lookup auf DNS-Server
1.	Der Webserver IIS01 benötigt einen Datenfeed von "www.data.gov", muss jedoch die Adresse auflösen.
2.	Die Netzwerkkonfiguration für das VNet listet DNS01 (10.0.2.4 im Back-End-Subnetz) als primären DNS-Server auf, IIS01 sendet die DNS-Anforderung an DNS01.
3.	Keine ausgehenden Regeln im Front-End-Subnetz, Datenverkehr wird zugelassen.
4.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	 NSG-Regel 1 (DNS) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
5.	DNS-Server empfängt Anforderung.
6.	Die Adresse ist nicht im DNS-Server zwischengespeichert, daher fragt der DNS-Server die Adresse bei einem DNS-Stammserver im Internet ab.
7.	Keine ausgehenden Regeln im Back-End-Subnetz, Datenverkehr wird zugelassen.
8.	Der DNS-Server im Internet antwortet. Da diese Sitzung intern initiiert wurde, wird die Antwort zugelassen.
9.	Der DNS-Server speichert die Antwort zwischen und gibt die Antwort auf die ursprüngliche Anforderung zurück an IIS01.
10.	Keine ausgehenden Regeln im Back-End-Subnetz, Datenverkehr wird zugelassen.
11.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	Es gibt keine NSG-Regel für eingehenden Datenverkehr vom Back-End- zum Front-End-Subnetz, daher trifft keine der NSG-Regeln zu.
  2.	Da die standardmäßige Systemregel Datenverkehr zwischen Subnetzen zulässt, wird der Datenverkehr zugelassen.
12.	IIS01 empfängt die Antwort von DNS01.

#### (*Zulässig*) Webserver greift auf Datei auf AppVM01 zu
1.	IIS01 fordert eine Datei auf AppVM01 an.
2.	Keine ausgehenden Regeln im Front-End-Subnetz, Datenverkehr wird zugelassen.
3.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft nicht zu, weiter zur nächsten Regel.
  3.	NSG-Regel 3 (Internet an IIS01) trifft nicht zu, weiter zur nächsten Regel.
  4.	NSG-Regel 4 (IIS01 an AppVM01) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
4.	AppVM01 empfängt die Anforderung und antwortet mit der Datei (autorisierter Zugriff vorausgesetzt).
5.	Da es keine ausgehenden Regeln im Back-End-Subnetz gibt, wird die Antwort zugelassen.
6.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	Es gibt keine NSG-Regel für eingehenden Datenverkehr vom Back-End- zum Front-End-Subnetz, daher trifft keine der NSG-Regeln zu.
  2.	Da die standardmäßige Systemregel Datenverkehr zwischen Subnetzen zulässt, wird der Datenverkehr zugelassen.
7.	Der IIS-Server empfängt die Datei.

#### (*Abgelehnt*) Web an Back-End-Server
1.	Internetbenutzer versucht, über den BackEnd001.CloudApp.Net-Dienst auf eine Datei auf AppVM01 zuzugreifen.
2.	Da keine Endpunkte für Dateifreigaben geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher den Server nicht erreichen.
3.	Wären aus irgendeinem Grund Endpunkte offen, würde die NSG-Regel 5 (Internet an VNet) diesen Datenverkehr blockieren.

#### (*Abgelehnt*) Web-DNS-Lookup auf DNS-Server
1.	Internetbenutzer versucht, über den BackEnd001.CloudApp.Net-Dienst auf DNS01 einen internen DNS-Eintrag nachzuschlagen.
2.	Da keine Endpunkte für DNS geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher den Server nicht erreichen.
3.	Wären aus irgendeinem Grund Endpunkte offen, würde NSG-Regel 5 (Internet an VNet) diesen Datenverkehr blockieren. (Hinweis: Regel 1 (DNS) würde aus zwei Gründen nicht gelten: Erstens befindet sich die Quelladresse im Internet, und diese Regel gilt nur für das lokale VNet als Quelle, und zweitens handelt es sich um eine Zulassungsregel, mit der niemals Datenverkehr abgelehnt wird.)

#### (*Abgelehnt*) Zugriff auf SQL aus dem Web über Firewall
1.	Internetbenutzer fordert SQL-Daten von FrontEnd001.CloudApp.Net (Clouddienst mit Schnittstelle zum Internet) an.
2.	Da keine Endpunkte für SQL geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher die Firewall nicht erreichen.
3.	Wenn Endpunkte aus irgendeinem Grund geöffnet wären, würde das Front-End-Subnetz mit der Verarbeitung der eingehenden Regel beginnen:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft nicht zu, weiter zur nächsten Regel.
  3.	NSG-Regel 3 (Internet an IIS01) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
4.	Datenverkehr trifft an interner IP-Adresse von IIS01 ein (10.0.1.5).
5.	IIS01 lauscht nicht auf Port 1433, daher wird diese Anforderung nicht beantwortet.

## Zusammenfassung
Dies ist eine relativ einfache und direkte Möglichkeit, das Back-End-Subnetz von eingehendem Datenverkehr zu isolieren.

Weitere Beispiele und einen Überblick über die Sicherheitgrenzen des Netzwerks finden Sie [hier][HOME].

## Referenzen
### Hauptskript und Netzwerkkonfiguration
Speichern Sie das vollständige Skript in einer PowerShell-Skriptdatei. Speichern Sie die Netzwerkkonfiguration in einer Datei namens "NetworkConf1.xml". Bearbeiten Sie die benutzerdefinierten Variablen nach Bedarf. Führen Sie das Skript aus, und befolgen Sie dann oben stehende Anweisungen zur Einrichtung der Firewallregel.

#### Vollständiges Skript
Dieses Skript führt basierend auf den benutzerdefinierten Variablen Folgendes aus:

1.	Herstellen einer Verbindung mit einem Azure-Abonnement
2.	Erstellen eines neuen Speicherkontos
3.	Erstellen eines neuen VNets und zweier Subnetze, wie in der Netzwerkkonfigurationsdatei definiert
4.	Erstellen von Windows Server-VMs (Build 4)
5.	Konfigurieren von Netzwerksicherheitsgruppen einschließlich:
  -	Erstellen einer Netzwerksicherheitsgruppe
  -	Auffüllen der Gruppe mit Regeln
  -	Binden der Netzwerksicherheitsgruppe an die geeigneten Subnetze

Dieses PowerShell-Skript sollte lokal auf einem mit dem Internet verbundenen PC oder Server ausgeführt werden.

>[AZURE.IMPORTANT]Während der Ausführung des Skripts werden in PowerShell möglicherweise Warnungen oder Informationsmeldungen angezeigt. Nur in Rot angezeigte Fehlermeldungen müssen genauer beachtet und ggf. gelöst werden.


	<# 
	 .SYNOPSIS
	  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)
	
	 .DESCRIPTION
	  This script will build out a sample DMZ setup containing:
	   - A default storage account for VM disks
	   - Two new cloud services
	   - Two Subnets (FrontEnd and BackEnd subnets)
	   - One server on the FrontEnd Subnet
	   - Three Servers on the BackEnd Subnet
	   - Network Security Groups to allow/deny traffic patterns as declared
	  
	  Before running script, ensure the network configuration file is created in
	  the directory referenced by $NetworkConfigFile variable (or update the
	  variable to reflect the path and file name of the config file being used).
	
	 .Notes
	  Security requirements are different for each use case and can be addressed in a
	  myriad of ways. Please be sure that any sensitive data or applications are behind
	  the appropriate layer(s) of protection. This script serves as an example of some
	  of the techniques that can be used, but should not be used for all scenarios. You
	  are responsible to assess your security needs and the appropriate protections
	  needed, and then effectively implement those protections.
	
	  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
	   IIS01      - 10.0.1.5
	 
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
	    $FrontEndService = "FrontEnd001"
	    $BackEndService = "BackEnd001"
	
	  # Network Details
	    $VNetName = "CorpNetwork"
	    $FESubnet = "FrontEnd"
	    $FEPrefix = "10.0.1.0/24"
	    $BESubnet = "BackEnd"
	    $BEPrefix = "10.0.2.0/24"
	    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"
	
	  # VM Base Disk Image Details
	    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	  
	  # NSG Details
	    $NSGName = "MyVNetSG"
	
	# User Defined VM Specific Config
	    # Note: To ensure proper NSG Rule creation later in this script:
	    #       - The Web Server must be VM 0
	    #       - The AppVM1 Server must be VM 1
	    #       - The DNS server must be VM 3
	    #
	    #       Otherwise the NSG rules in the last section of this
	    #       script will need to be changed to match the modified
	    #       VM array numbers ($i) so the NSG Rule IP addresses
	    #       are aligned to the associated VM IP addresses.
	
	    # VM 0 - The Web Server
	      $VMName += "IIS01"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.5"
	
	    # VM 1 - The First Application Server
	      $VMName += "AppVM01"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.5"
	
	    # VM 2 - The Second Application Server
	      $VMName += "AppVM02"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.6"
	
	    # VM 3 - The DNS Server
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
	
	If (Test-AzureName -Service -Name $FrontEndService) { 
	    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
	
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
	    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
	    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
	
	# Build VMs
	    $i=0
	    $VMName | Foreach {
	        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
	        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
	            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
	            Remove-AzureEndpoint -Name "PowerShell" | `
	            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
	        $i++
	    }
	    # Add HTTP Endpoint for IIS01
	    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM
	
	# Configure NSG
	    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
	    
	  # Build the NSG
	    Write-Host "Building the NSG" -ForegroundColor Cyan
	    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
	
	  # Add NSG Rules
	    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
	        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
	        -SourceAddressPrefix Internet -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
	        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
	        -Protocol *
	    
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
	        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
	        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
	        -Protocol *
	
	    # Assign the NSG to the Subnets
	        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
	        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
	        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
	
	# Optional Post-script Manual Configuration
	  # Install Test Web App (Run Post-Build Script on the IIS Server)
	  # Install Backend resource (Run Post-Build Script on the AppVM01)
	  Write-Host
	  Write-Host "Build Complete!" -ForegroundColor Green
	  Write-Host
	  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
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
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Eingehende DMZ mit NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

<!---HONumber=AcomDC_0121_2016-->