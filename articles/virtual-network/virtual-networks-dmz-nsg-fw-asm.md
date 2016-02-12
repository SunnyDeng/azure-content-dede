<properties
   pageTitle="DMZ-Beispiel – Erstellen einer DMZ zum Schützen von Anwendungen mit einer Firewall und NSGs | Microsoft Azure"
   description="Erstellen einer DMZ mit einer Firewall und Netzwerksicherheitsgruppen (NSGs)"
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
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# Beispiel 2 – Erstellen einer DMZ zum Schützen von Anwendungen mit einer Firewall und NSGs

[Zurück zur Seite mit bewährten Methoden zur Sicherheitsbegrenzung][HOME]

In diesem Beispiel wird eine DMZ mit einer Firewall, vier Windows-Servern und Netzwerksicherheitsgruppen erstellt. Jeder der relevanten Befehle wird genau erläutert, um ein besseres Verständnis jedes einzelnen Schritts zu ermöglichen. Es gibt außerdem einen Abschnitt mit verschiedenen Szenarien zum Datenverkehr, in denen Schritt für Schritt erläutert wird, wie der Datenverkehr durch die verschiedenen Sicherheitsstufen in der DMZ geleitet wird. Der Referenzabschnitt schließlich enthält den vollständigen Code sowie Anweisungen zum Aufbau dieser Umgebung, damit Sie verschiedene Szenarien testen und ausprobieren können.

![Eingehende DMZ mit NVA und NSG][1]

## Beschreibung der Umgebung
Dieses Beispiel umfasst ein Abonnement, das Folgendes enthält:

- Zwei Clouddienste: "FrontEnd001" und "BackEnd001"
- Ein virtuelles Netzwerk, "CorpNetwork", mit zwei Subnetzen: "FrontEnd" und "BackEnd"
- Eine einzelne Netzwerksicherheitsgruppe, die auf beide Subnetze angewendet wird
- Ein virtuelles Netzwerkgerät, in diesem Fall eine Barracuda NextGen-Firewall, das mit dem Front-End-Subnetz verbunden ist
- Eine Windows Server-Instanz, die einen Anwendungswebserver darstellt ("IIS01")
- Zwei Windows Server-Instanzen, die Back-End-Anwendungsserver darstellen ("AppVM01", "AppVM02")
- Eine Windows Server-Instanz, die einen DNS-Server darstellt ("DNS01")

>[AZURE.NOTE] In diesem Beispiel wird zwar eine Barracuda NextGen-Firewall verwendet, aber es könnten auch andere virtuelle Netzwerkgeräte eingesetzt werden.

Der Referenzabschnitt enthält ein PowerShell-Skript, mit dem sich der größte Teil der oben beschriebenen Umgebung erstellen lässt. Die Erstellung der virtuellen Computer und Netzwerke wird zwar durch das Beispielskript ausgeführt, aber dies wird in diesem Dokument nicht im Einzelnen beschrieben.
 
So erstellen Sie die Umgebung

  1.	Speichern Sie die im Referenzabschnitt enthaltene und mit dem Namen, dem Speicherort und den IP-Adressen für das jeweilige Szenario aktualisierte XML-Netzwerkkonfigurationsdatei.
  2.	Aktualisieren Sie die Benutzervariablen im Skript gemäß der Umgebung, in der das Skript ausgeführt werden soll (Abonnements, Dienstnamen usw.).
  3.	Führen Sie das Skript in PowerShell aus.

**Hinweis**: Die im PowerShell-Skript angegebene Region muss der Region in der XML-Netzwerkkonfigurationsdatei entsprechen.

Nachdem das Skript erfolgreich ausgeführt wurde, können im Anschluss folgende Schritte ausgeführt werden:

1.	Richten Sie die Firewallregeln ein. Dieses Thema wird unten im Abschnitt „Firewallregeln“ beschrieben.
2.	Optional stehen im Referenzabschnitt zwei Skripts zum Einrichten des Webservers und des Anwendungsservers mit einer einfachen Webanwendung zur Verfügung, um Tests mit dieser DMZ-Konfiguration zu ermöglichen.

Im nächsten Abschnitt werden die meisten Skriptanweisungen für Netzwerksicherheitsgruppen beschrieben.

## Netzwerksicherheitsgruppen
Für dieses Beispiel wird eine Netzwerksicherheitsgruppe erstellt und dann mit sechs Regeln geladen.

>[AZURE.TIP] Im Allgemeinen sollten Sie zuerst die spezifischeren Regeln zum Zulassen von Aktionen und danach die allgemeineren Regeln zum Ablehnen von Aktionen erstellen. Die zugewiesene Priorität bestimmt, welche Regeln zuerst ausgewertet werden. Sobald eine Regel auf den Datenverkehr zutrifft, werden keine weiteren Regeln ausgewertet. NSG-Regeln können sowohl in eingehender als auch in ausgehender Richtung zutreffen (aus Perspektive des Subnetzes).

Folgende Regeln werden deklarativ für eingehenden Datenverkehr erstellt:

1.	Interner DNS-Datenverkehr (Port 53) wird zugelassen.
2.	RDP-Datenverkehr (Port 3389) aus dem Internet zu jedem virtuellen Computer wird zugelassen.
3.	HTTP-Datenverkehr (Port 80) über das Internet an den NVA (Firewall) wird zugelassen.
4.	Jeglicher Datenverkehr (alle Ports) von IIS01 zu AppVM1 wird zugelassen.
5.	Jeglicher Datenverkehr (alle Ports) aus dem Internet in das gesamte VNet (beide Subnetze) wird abgelehnt.
6.	Jeglicher Datenverkehr (alle Ports) vom Front-End-Subnetz zum Back-End-Subnetz wird abgelehnt.

Wenn diese Regeln an jedes Subnetz gebunden sind und eine HTTP-Anforderung aus dem Internet an den Webserver eingeht, gilt sowohl Regel 3 (Zulassen) als auch Regel 5 (Verweigern). Da Regel 3 aber eine höhere Priorität hat, wird nur diese Regel angewendet, und Regel 5 wird nicht berücksichtigt. Aus diesem Grund würde die HTTP-Anforderung für die Firewall als zulässig eingestuft werden. Falls derselbe Datenverkehr versuchen würde, den DNS01-Server zu erreichen, würde Regel 5 (Verweigern) zuerst gelten. Für den Datenverkehr wird die Übergabe an den Server also nicht zugelassen. Mit Regel 6 (Verweigern) wird die Kommunikation des Front-End-Subnetzes mit dem Back-End-Subnetz blockiert (mit Ausnahme von zulässigem Datenverkehr in den Regeln 1 und 4). Dies dient dem Schutz des Back-End-Netzwerks, falls ein Angreifer die Webanwendung am Front-End attackiert. Der Angreifer hat dann nur beschränkten Zugriff auf das „geschützte“ Back-End-Netzwerk (nur auf Ressourcen, die auf dem Server AppVM01 verfügbar gemacht werden).

Es gibt eine Standardregel für ausgehenden Datenverkehr, die das Senden von Datenverkehr an das Internet zulässt. In diesem Beispiel wird ausgehender Datenverkehr zugelassen, und es werden keine Regeln für die ausgehende Richtung geändert. Zum Sperren von Datenverkehr in beiden Richtungen ist das benutzerdefinierte Routing erforderlich. Dies wird in einem anderen Beispiel behandelt, das im [Hauptdokument zu Sicherheitsgrenzen][HOME] enthalten ist.

Die oben beschriebenen NSG-Regeln weisen eine starke Ähnlichkeit mit den NSG-Regeln unter [Beispiel 1 – Erstellen einer einfachen DMZ mit NSGs][Example1] auf. Lesen Sie sich die NSG-Beschreibung in diesem Dokument durch, um ausführliche Informationen zu den einzelnen NSG-Regeln und ihren Attributen zu erhalten.

## Firewallregeln
Zur Verwaltung der Firewall und Erstellung der erforderlichen Konfigurationen muss ein Verwaltungsclient auf einem PC installiert werden. Informationen zur Verwaltung des Geräts finden Sie in der Dokumentation zu Ihrer Firewall (bzw. einem anderen virtuellen Netzwerkgerät). Im Rest dieses Abschnitts wird die Konfiguration der Firewall über den Verwaltungsclient des Anbieters beschrieben (d. h. nicht über das Azure-Portal oder PowerShell).

Anweisungen zum Herunterladen des Clients und Herstellen einer Verbindung zur in diesem Beispiel verwendeten Barracuda-Firewall finden Sie unter [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin).

In der Firewall müssen Weiterleitungsregeln erstellt werden. Da in diesem Beispiel nur eingehender Internetdatenverkehr an die Firewall und dann an den Webserver geleitet wird, ist nur eine NAT-Weiterleitungsregel erforderlich. Für die Barracuda NextGen-Firewall in diesem Beispiel würde es sich bei der Regel um eine Destination NAT-Regel („Dst NAT“) zum Übergeben des Datenverkehrs handeln.

Um die folgende Regel zu erstellen (oder vorhandene Standardregeln zu überprüfen), öffnen Sie das Dashboard des Barracuda NG Admin-Clients, wechseln zur Registerkarte „Configuration“ und klicken im Abschnitt „Operational Configuration“ auf „Ruleset“. In einem Raster namens „Main Rules“ werden die vorhandenen aktivierten und deaktivierten Regeln der Firewall angezeigt. In der oberen rechten Ecke dieses Rasters befindet sich ein kleines grünes Pluszeichen. Klicken Sie auf dieses Zeichen, um eine neue Regel zu erstellen. (Hinweis: Möglicherweise ist die Firewall „gesperrt“, sodass keine Änderungen vorgenommen werden können. Wenn eine Schaltfläche mit der Bezeichnung „Lock“ angezeigt wird und Sie keine Regeln erstellen oder bearbeiten können, klicken Sie auf diese Schaltfläche, um den Regelsatz zu entsperren und die Bearbeitung zu ermöglichen.) Wenn Sie eine vorhandene Regel bearbeiten möchten, wählen Sie diese Regel aus, klicken Sie mit der rechten Maustaste, und wählen Sie „Edit Rule“.

Erstellen Sie eine neue Regel, und geben Sie einen Namen ein, z. B. „WebTraffic“.

Das Symbol für die Destination NAT-Regel sieht wie folgt aus: ![Destination NAT-Symbol][2]

Die Regel selbst würde wie folgt aussehen:

![Firewallregel][3]

Hierbei werden alle eingehenden Adressen, die auf die Firewall treffen und für HTTP (Port 80 oder 443 für HTTPS) bestimmt sind, an die Schnittstelle „DHCP1 Local IP“ der Firewall gesendet und an den Webserver mit der IP-Adresse 10.0.1.5 umgeleitet. Da der Datenverkehr über Port 80 eingeht und über Port 80 an den Webserver geleitet wird, war keine Portänderung erforderlich. Die Zielliste hätte aber 10.0.1.5:8080 lauten können, wenn unser Webserver an Port 8080 lauschen würde, sodass der eingehende Port 80 der Firewall in den eingehenden Port 8080 auf dem Webserver übersetzt werden würde.

Eine Verbindungsmethode sollte auch angegeben werden. Für die Zielregel aus dem Internet ist „Dynamic SNAT" am besten geeignet.

Obwohl nur eine Regel erstellt wurde, ist es wichtig, dass die Priorität dafür richtig festgelegt wird. Wenn sich diese neue Regel im Raster aller Regeln der Firewall an unterster Stelle befindet (unterhalb der Regel „BLOCKALL“), wird sie niemals berücksichtigt. Achten Sie darauf, dass die neu erstellte Regel für den Webdatenverkehr oberhalb der Regel BLOCKALL angeordnet ist.

Nach der Erstellung der Regel muss sie per Pushvorgang an die Firewall übertragen und dann aktiviert werden. Wenn dies nicht erfolgt, wird die Regeländerung nicht wirksam. Der Push- und Aktivierungsprozess wird im nächsten Abschnitt beschrieben.

## Regelaktivierung
Nachdem der Regelsatz aufgrund der Hinzufügung dieser Regel geändert wurde, muss er in die Firewall hochgeladen und aktiviert werden.

![Aktivierung der Firewallregeln][4]

In der oberen rechten Ecke des Verwaltungsclients befinden sich verschiedene Schaltflächen. Klicken Sie auf die Schaltfläche „Send Changes“, um die geänderten Regeln an die Firewall zu senden, und klicken Sie dann auf die Schaltfläche „Activate“.

Mit der Aktivierung des Firewallregelsatzes ist die Erstellung dieser Beispielumgebung abgeschlossen. Optional können die Skripts für die Vorgänge nach der Erstellung im Abschnitt „References“ ausgeführt werden, um dieser Umgebung eine Anwendung hinzuzufügen, damit die unten angegebenen Datenverkehrsszenarien getestet werden können.

>[AZURE.IMPORTANT] Es ist wichtig, sich zu vergegenwärtigen, dass der Zugang zum Webserver nicht direkt erfolgt. Wenn ein Browser eine HTTP-Seite von FrontEnd001.CloudApp.Net anfordert, übergibt der HTTP-Endpunkt (Port 80) diesen Datenverkehr an die Firewall, nicht an den Webserver. Anschließend leitet die Firewall die Anforderung gemäß der oben erstellten Regel per NAT an den Webserver weiter.

## Datenverkehrsszenarien

#### (Zugelassen) Web an Webserver über die Firewall
1.	Internetbenutzer fordert HTTP-Seite von FrontEnd001.CloudApp.Net (Internet-Clouddienst) an.
2.	Clouddienst übergibt Datenverkehr über offenen Endpunkt an Port 80 an lokale Firewallschnittstelle unter 10.0.1.4:80.
3.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft nicht zu, weiter zur nächsten Regel.
  3.	NSG-Regel 3 (Internet an Firewall) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
4.	Datenverkehr trifft an interner IP-Adresse der Firewall ein (10.0.1.4).
5.	Firewall-Weiterleitungsregel erkennt, dass es sich um Datenverkehr für Port 80 handelt und leitet diesen an den Webserver IIS01 um.
6.	IIS01 lauscht auf Webdatenverkehr, empfängt diese Anforderung und beginnt mit der Verarbeitung der Anforderung.
7.	IIS01 fragt Informationen von SQL Server unter AppVM01 ab.
8.	Keine ausgehenden Regeln im Front-End-Subnetz, Datenverkehr wird zugelassen.
9.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft nicht zu, weiter zur nächsten Regel.
  3.	NSG-Regel 3 (Internet an Firewall) trifft nicht zu, weiter zur nächsten Regel.
  4.	NSG-Regel 4 (IIS01 an AppVM01) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
10.	AppVM01 empfängt die SQL-Abfrage und antwortet.
11.	Da es keine ausgehenden Regeln im Back-End-Subnetz gibt, wird die Antwort zugelassen.
12.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	Es gibt keine NSG-Regel für eingehenden Datenverkehr vom Back-End- zum Front-End-Subnetz, daher trifft keine der NSG-Regeln zu.
  2.	Da die standardmäßige Systemregel Datenverkehr zwischen Subnetzen zulässt, wird der Datenverkehr zugelassen.
13.	Der IIS-Server empfängt die SQL-Antwort, erstellt die HTTP-Antwort und sendet sie an den Anforderer.
14.	Da es sich um eine NAT-Sitzung von der Firewall handelt, wird als Antwortziel (zuerst) die Firewall verwendet.
15.	Die Firewall empfängt die Antwort vom Webserver und leitet sie zurück an den Internetbenutzer.
16.	Da im Front-End-Subnetz keine ausgehenden Regeln vorhanden sind, wird die Antwort zugelassen, und der Internetbenutzer empfängt die angeforderte Webseite.

#### (Zugelassen) RDP an Back-End
1.	Server Admin im Internet fordert RDP-Sitzung für AppVM01 an BackEnd001.CloudApp.Net:xxxxx an, wobei „xxxxx“ für die zufällig zugewiesene Portnummer für RDP zu AppVM01 steht (den zugewiesenen Port finden Sie im Azure-Portal oder per PowerShell).
2.	Da die Firewall nur unter der Adresse FrontEnd001.CloudApp.Net lauscht, ist sie an diesem Datenverkehrsfluss nicht beteiligt.
3.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
4.	Da keine ausgehenden Regeln vorhanden sind, werden Standardregeln angewendet, und der zurückkommende Datenverkehr wird zugelassen.
5.	Die RDP-Sitzung wird aktiviert.
6.	AppVM01 fordert zur Eingabe von Benutzername und Kennwort auf.

#### (Zugelassen) Webserver-DNS-Lookup auf DNS-Server
1.	Der Webserver IIS01 benötigt einen Datenfeed von „www.data.gov“, muss jedoch die Adresse auflösen.
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

#### (Zugelassen) Webserver-Zugriffsdatei unter AppVM01
1.	IIS01 fragt nach einer Datei unter AppVM01.
2.	Keine ausgehenden Regeln im Front-End-Subnetz, Datenverkehr wird zugelassen.
3.	Das Back-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft nicht zu, weiter zur nächsten Regel.
  3.	NSG-Regel 3 (Internet an Firewall) trifft nicht zu, weiter zur nächsten Regel.
  4.	NSG-Regel 4 (IIS01 an AppVM01) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
4.	AppVM01 empfängt die Anforderung und antwortet mit der Datei (autorisierter Zugriff vorausgesetzt).
5.	Da es keine ausgehenden Regeln im Back-End-Subnetz gibt, wird die Antwort zugelassen.
6.	Das Front-End-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
  1.	Es gibt keine NSG-Regel für eingehenden Datenverkehr vom Back-End- zum Front-End-Subnetz, daher trifft keine der NSG-Regeln zu.
  2.	Da die standardmäßige Systemregel Datenverkehr zwischen Subnetzen zulässt, wird der Datenverkehr zugelassen.
7.	Der IIS-Server empfängt die Datei.

#### (Verweigert) Web direkt an Webserver
Da sich der Webserver IIS01 und die Firewall in demselben Clouddienst befinden, haben beide die gleiche öffentliche IP-Adresse. Der gesamte HTTP-Datenverkehr würde also an die Firewall geleitet werden. Die Anforderung würde zwar erfolgreich bereitgestellt werden, aber sie kann nicht direkt an den Webserver gehen, sondern wird wie vorgesehen erst durch die Firewall geleitet. Informationen zum Datenverkehrsfluss finden Sie im ersten Szenario dieses Abschnitts.

#### (Verweigert) Web an Back-End-Server
1.	Internetbenutzer versucht, über den BackEnd001.CloudApp.Net-Dienst auf eine Datei auf AppVM01 zuzugreifen.
2.	Da keine Endpunkte für Dateifreigaben geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher den Server nicht erreichen.
3.	Wären aus irgendeinem Grund Endpunkte offen, würde die NSG-Regel 5 (Internet an VNet) diesen Datenverkehr blockieren.

#### (Verweigert) Web-DNS-Lookup auf DNS-Server
1.	Internetbenutzer versucht, über den BackEnd001.CloudApp.Net-Dienst auf DNS01 einen internen DNS-Eintrag nachzuschlagen.
2.	Da keine Endpunkte für DNS geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher den Server nicht erreichen.
3.	Wenn die Endpunkte aus irgendeinem Grund geöffnet wären, würde NSG-Regel 5 (Internet an VNet) diesen Datenverkehr blockieren. (Hinweis: Regel 1 (DNS) würde aus zwei Gründen nicht gelten: Erstens befindet sich die Quelladresse im Internet, und diese Regel gilt nur für das lokale VNet als Quelle, und zweitens handelt es sich um eine Zulassungsregel, sodass damit niemals Datenverkehr verweigert wird.)

#### (Verweigert) Webzugriff auf SQL über die Firewall
1.	Internetbenutzer fordert SQL-Daten von FrontEnd001.CloudApp.Net (Internet-Clouddienst) an.
2.	Da keine Endpunkte für SQL geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher die Firewall nicht erreichen.
3.	Wenn Endpunkte aus irgendeinem Grund geöffnet wären, würde das Front-End-Subnetz mit der Verarbeitung der eingehenden Regel beginnen:
  1.	NSG-Regel 1 (DNS) trifft nicht zu, weiter zur nächsten Regel.
  2.	NSG-Regel 2 (RDP) trifft nicht zu, weiter zur nächsten Regel.
  3.	NSG-Regel 3 (Internet an Firewall) trifft zu, Datenverkehr wird zugelassen, Regelverarbeitung wird beendet.
4.	Datenverkehr trifft an interner IP-Adresse der Firewall ein (10.0.1.4).
5.	Firewall verfügt nicht über Weiterleitungsregeln für SQL und verwirft den Datenverkehr.

## Zusammenfassung
Dies ist eine relativ einfache Möglichkeit zum Schützen Ihrer Anwendung mit einer Firewall und zum Isolieren des Back-End-Subnetzes vor eingehendem Datenverkehr.

Weitere Beispiele und eine Übersicht über die Sicherheitsgrenzen des Netzwerks finden Sie [hier][HOME].

## Referenzen
### Hauptskript und Netzwerkkonfiguration
Speichern Sie das vollständige Skript in einer PowerShell-Skriptdatei. Speichern Sie die Netzwerkkonfiguration in einer Datei namens „NetworkConf2.xml“. Bearbeiten Sie die benutzerdefinierten Variablen nach Bedarf. Führen Sie das Skript aus, und befolgen Sie dann oben stehende Anweisungen zur Einrichtung der Firewallregel.

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

>[AZURE.IMPORTANT] Während der Ausführung des Skripts werden in PowerShell möglicherweise Warnungen oder Informationsmeldungen angezeigt. Nur in Rot angezeigte Fehlermeldungen müssen genauer beachtet und ggf. gelöst werden.


	<# 
	 .SYNOPSIS
	  Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
	
	 .DESCRIPTION
	  This script will build out a sample DMZ setup containing:
	   - A default storage account for VM disks
	   - Two new cloud services
	   - Two Subnets (FrontEnd and BackEnd subnets)
	   - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
	   - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
	   myFirewall - 10.0.1.4
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
	    $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
	
	  # VM Base Disk Image Details
	    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	    $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	
	  # NSG Details
	    $NSGName = "MyVNetSG"
	
	# User Defined VM Specific Config
	    # Note: To ensure proper NSG Rule creation later in this script:
	    #       - The Web Server must be VM 1
	    #       - The AppVM1 Server must be VM 2
	    #       - The DNS server must be VM 4
	    #
	    #       Otherwise the NSG rules in the last section of this
	    #       script will need to be changed to match the modified
	    #       VM array numbers ($i) so the NSG Rule IP addresses
	    #       are aligned to the associated VM IP addresses.
	
	    # VM 0 - The Network Virtual Appliance (NVA)
	      $VMName += "myFirewall"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Firewall"
	      $img += $FWImg
	      $size += "Small"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.4"
	
	    # VM 1 - The Web Server
	      $VMName += "IIS01"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.5"
	
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
	        If ($VMFamily[$i] -eq "Firewall") 
	            { 
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	            # Set up all the EndPoints we'll need once we're up and running
	            # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
	            #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
	            #       forwarding rule, so the HTTP endpoint here will have the same public and local
	            #       port and the firewall will do the NATing and redirection as declared in the
	            #       firewall rule.
	            Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
	            }
	        Else
	            {
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
	                Remove-AzureEndpoint -Name "PowerShell" | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	                # Note: A Remote Desktop endpoint is automatically created when each VM is created.
	            }
	        $i++
	    }
	
	# Configure NSG
	    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
	    
	  # Build the NSG
	    Write-Host "Building the NSG" -ForegroundColor Cyan
	    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
	
	  # Add NSG Rules
	    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
	        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
	        -SourceAddressPrefix Internet -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
	        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Eingehende DMZ mit NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Destination NAT-Symbol"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Firewallregel"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Aktivierung der Firewallregeln"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md

<!---HONumber=AcomDC_0204_2016-->