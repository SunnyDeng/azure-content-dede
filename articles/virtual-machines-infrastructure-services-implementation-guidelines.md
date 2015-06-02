<properties 
	pageTitle="Implementierungsrichtlinien für Azure-Infrastrukturdienste" 
	description="Erfahren Sie mehr über die wichtigsten Entwurfs- und Implementierungsrichtlinien für die Bereitstellung einer IT-Arbeitslast in Azure-Infrastrukturdiensten." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Implementierungsrichtlinien für Azure-Infrastrukturdienste
 
Der Schwerpunkt dieser Richtlinien liegt auf den wichtigen Entwurfsentscheidungen und Aufgaben, die vielen Ressourcen zu ermitteln, die in den meisten Azure-Infrastrukturdienst-Implementierungen verwendet werden.

Azure ist eine hervorragende Plattform zur Implementierung von Konfigurationen für Machbarkeitsstudien, da nur geringe Investitionen erforderlich sind, um einen bestimmten Implementierungsansatz für Lösungen zu testen. Sie müssen jedoch zwischen den einfachen Verfahren der Machbarkeitsstudien und den schwierigeren ausführlichen Methoden für eine voll funktionsfähige, einsatzbereite Implementierung einer IT-Arbeitsauslastung unterscheiden.

In diesem Leitfaden werden viele Bereiche behandelt, bei denen Planung der Schlüssel zum Erfolg einer IT-Infrastruktur oder Azure-Arbeitslast sind. Darüber hinaus erleichtert der Leitfaden die Implementierung von Lösungen auf der Azure-Plattform durch eine Strukturierung der Erstellung der erforderlichen Ressourcen. Es gibt zwar eine gewisse Flexibilität, Microsoft empfiehlt jedoch, dass Sie diese Struktur bei der Planung und Entscheidungsfindung anwenden.

1.	Benennungskonventionen
2.	Abonnements und Konten
3.	Speicher
4.	Virtuelle Netzwerke
5.	Cloud-Dienste
6.	Verfügbarkeitsgruppen
7.	Virtual Machines

Durch das Festlegen einer guten Benennungskonvention sowie das Einhalten einer bestimmten Reihenfolge beim Erstellen von Ressourcen in Azure wird der administrative Aufwand stark reduziert und die Erfolgswahrscheinlichkeit für alle Implementierungsprojekte erhöht.

> [AZURE.NOTE]Affinitätsgruppen werden nicht beschrieben, da deren Verwendung überholt ist. Weitere Informationen finden Sie unter [Informationen zu regionalen VNETs und Affinitätsgruppen](https://msdn.microsoft.com/library/azure/jj156085.aspx).

## 1. Benennungskonventionen

Es sollte eine geeignete Benennungskonvention vorhanden sein, bevor Sie mit der Entwicklung in Azure beginnen. Durch eine Benennungskonvention wird sichergestellt, dass alle Ressourcen vorhersagbare Namen haben, die den Verwaltungsaufwand dieser Ressourcen senken.

Sie können auch einen bestimmten Satz von Benennungskonventionen für Ihre gesamte Organisation oder für ein bestimmtes Azure-Abonnement oder -Konto anwenden. Es ist zwar einfach, für Einzelpersonen innerhalb von Organisationen implizite Regeln für die Arbeit mit Azure-Ressourcen einzurichten, bei der Arbeit eines Teams an einem Projekt in Azure ist dieses Modell jedoch nicht gut geeignet.

Sie sollten den Satz von Benennungskonventionen im Voraus vereinbaren. Es gibt einige Überlegungen hinsichtlich Benennungskonventionen, die für die gesamten Regelsätze gelten, die diese Konventionen bilden.

### Affixe

Wenn Sie bestimmte Ressourcen erstellen, verwendet Azure einige Standards zur Vereinfachung der Verwaltung der Ressourcen, die diesen Ressourcen zugeordnet sind. Wenn Sie beispielsweise den ersten virtuellen Computer für einen neuen Clouddienst erstellen, versucht das Azure-Verwaltungsportal, den Namen des virtuellen Computers als Namen für einen neuen Clouddienst für den virtuellen Computer zu verwenden.

Daher ist es vorteilhaft, die Ressourcentypen zu ermitteln, die ein Affix zum Erkennen des Typs benötigen. Darüber hinaus sollten Sie eindeutig festlegen, an welcher Stelle sich das Affix befindet:

- Am Anfang des Namens (Präfix)
- Am Ende des Namens (Suffix)

Hier sind z. B. zwei mögliche Namen für einen Clouddienst, der ein Berechnungsmodul hostet:

- Svc-CalculationEngine (Präfix)
- CalculationEngine-Svc (Suffix)

Affixe können auf verschiedene Aspekte verweisen, die die entsprechenden Ressourcen beschreiben. Die folgende Tabelle zeigt einige üblicherweise verwendete Beispiele.

Aspekt | Beispiele | Hinweise
--- | --- | ---
Environment | dev, stg, prod | Abhängig von Zweck und Name der jeweiligen Umgebung.
Standort | usw (USA Westen), use (USA Osten 2) | Abhängig von der Region des Datencenters oder der Region der Organisation.
Azure-Komponente, -Dienst oder -Produkt | Svc für Clouddienst, VNET für virtuelles Netzwerk | Je nach Produkt, das die Ressource unterstützt.
Rolle | sql, ora, sp, iis | Abhängig von der Rolle des virtuellen Computers.
Instanz | 01, 02, 03 usw. | Für Ressourcen, die mehr als eine Instanz haben können. Beispielsweise Webserver mit Lastenausgleich in einem Clouddienst.
		
Beim Einrichten der Benennungskonventionen sollten Sie sicherstellen, dass diese eindeutig angeben, welche Affixe für die einzelnen Ressourcentypen verwendet werden sollen und an welcher Position (Präfix und Suffix).

### Datumsangaben

In vielen Fällen ist es wichtig, das Datum der Erstellung anhand des Namens einer Ressource bestimmen zu können. Microsoft empfiehlt das Datumsformat JJJJMMTT. Durch dieses Format wird sichergestellt, dass nicht nur das vollständige Datum aufgezeichnet wird, sondern auch zwei Ressourcen gleichzeitig alphabetisch und chronologisch sortiert werden, deren Namen sich nur durch das Datum unterscheiden.

### Benennen von Ressourcen
Sie sollten die einzelnen Ressourcentypen in der Benennungskonvention definieren, die Regeln für die Zuweisung von Namen für die erstellten Ressourcen enthalten sollte. Diese Regeln sollten auf alle Ressourcentypen angewendet werden, z. B.:

- Abonnements
- Konten
- Speicherkonten
- Virtuelle Netzwerke
- Subnetze
- Verfügbarkeitsgruppen
- Cloud Services
- Virtual Machines
- Endpunkte
- Netzwerksicherheitsgruppen
- Rollen

Namen sollten so aussagekräftig wie möglich sein, sodass der Name genügend Informationen bietet, um festzustellen, auf welche Ressource er verweist.

### Computernamen

Wenn Administratoren einen virtuellen Computer erstellen, erfordert Microsoft Azure, dass ein Name für den virtuellen Computer angegeben wird. Microsoft Azure verwendet den Namen des virtuellen Computers als den Ressourcennamen des virtuellen Azure-Computers. Azure verwendet den gleichen Namen wie den Computernamen im Betriebssystem auf des virtuellen Computers. Allerdings können diese Namen nicht immer identisch sein.

In Fällen, in denen ein virtueller Computer aus einer VHD-Datei erstellt wird, die bereits ein Betriebssystem enthält, kann sich der Name des virtuellen Computers in Microsoft Azure vom Betriebssystem-Computernamen des virtuellen Computers unterscheiden. Dies kann zusätzliche Probleme bei der Verwaltung virtueller Computer verursachen, deshalb wird davon abgeraten. Stellen Sie immer sicher, dass der Ressourcenname des virtuellen Azure-Computers mit dem Computernamen im Betriebssystem des virtuellen Computers übereinstimmt.

Es wird empfohlen, den gleichen Namen für den virtuellen Azure-Computer und den Computer mit dem zugrunde liegenden Betriebssystem zu verwenden. Befolgen Sie daher die NetBIOS-Benennungsregeln, wie in [Namenskonventionen für Microsoft NetBIOS-Computer](https://support.microsoft.com/kb/188997/) beschrieben.

### Speicherkontonamen

Für die Benennung von Speicherkonten gelten spezielle Regeln. Sie können nur Kleinbuchstaben und Zahlen und den zugewiesenen Namen verwenden. Verkettet mit dem Dienst (Blob, Tabelle oder Warteschlange) und der Standarddomäne ("core.windows.net") sollte dies für einen global gültigen, eindeutigen DNS-Namen sorgen. Wenn beispielsweise das Speicherkonto den Namen "meinspeicherkonto" hat, ergeben die folgenden URLs gültige und eindeutige DNS-Namen:

- meinspeicherkonto.blob.core.windows.net
- meinspeicherkonto.table.core.windows.net
- meinspeicherkonto.queue.core.windows.net

Darüber hinaus können Speicherkonten Container nutzen. Diese müssen den Benennungskonventionen folgen, wie unter [Benennen von Containern, BLOBs und Metadaten und Verweisen auf diese](https://msdn.microsoft.com/library/azure/dd135715.aspx) beschrieben.

### Azure Building Block-Namen

Azure Building Blocks sind Dienste auf Anwendungsebene, die von Azure i. d. R. Anwendungen angeboten werden, die PaaS-Features nutzen, obwohl auch IaaS-Ressourcen einige davon nutzen, wie Azure SQL, Traffic Manager und andere.

Diese Dienste beruhen auf einem Array von Artefakten, die in Azure erstellt und registriert werden. Sie müssen auch in den Benennungskonventionen berücksichtigt werden.

### Wiederholung der Implementierungsrichtlinien für Benennungskonventionen

Entscheidung:

- Wie lauten Ihre Benennungskonventionen für Azure-Ressourcen? 

Aufgabe:

- Definieren Sie die Benennungskonventionen in Bezug auf Affixe, Hierarchie, Zeichenfolgenwerte und andere Richtlinien für Azure-Ressourcen.

## 2. Abonnements und Konten

Für die Arbeit mit Azure benötigen Sie mindestens ein Azure-Abonnement. Ressourcen, wie z. B. Clouddienste oder virtuelle Computer, sind im Kontext dieser Abonnements vorhanden.

- Unternehmenskunden verwenden i. d. R. eine Unternehmensanmeldung, die die oberste Ressource in der Hierarchie darstellt und einem oder mehreren Konten zugeordnet ist. 
- Für Benutzer und Kunden ohne Unternehmensanmeldung ist die oberste Ressource das Konto.
- Abonnements sind Konten zugeordnet, und jedes Konto kann über mehrere Abonnements verfügen. Azure verzeichnet die Abrechnungsinformationen auf Abonnementebene.

Aufgrund der maximal zwei Hierarchieebenen für die Konto-/Abonnement-Beziehung ist es wichtig, die Benennungskonvention für Konten und Abonnements an die Abrechnungsanforderungen anzupassen. Wenn z. B. ein globales Unternehmen Azure verwendet, nutzt dieses pro Region möglicherweise ein Konto, und die Abonnements werden auf Regionsebene verwaltet.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub01.png)
  
Sie können beispielsweise diese Struktur verwenden.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub02.png)
  
Wenn sich diesem Beispiel folgend eine Region dazu entscheidet, mehr als ein Abonnement einer bestimmten Gruppe zuzuordnen, sollte die Benennungskonvention eine Methode enthalten, mit der das zusätzliche Abonnement entweder im Kontonamen oder im Abonnementnamen codiert wird. Diese Organisation ermöglicht Abrechnungsdaten, mit denen die neuen Hierarchieebenen bei den Abrechnungsberichten generiert werden.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub03.png)

Die Organisation könnte folgendermaßen aussehen.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub04.png)
 
Microsoft stellt ausführliche Abrechnungen in einer herunterladbaren Datei für einzelne Konten oder für alle Konten in einem Enterprise Agreement zur Verfügung. Sie können diese Datei z. B. mit Excel verarbeiten. Dieser Prozess würde Daten erfassen, die Ressourcen, die mehr als eine Ebene der Hierarchie codieren, in separate Spalten partitionieren und mithilfe einer PivotTable oder PowerPivot dynamische Berichtsfunktionen bereitstellen.

### Wiederholung der Richtlinienimplementierung für Konten und Abonnements

Entscheidung:

- Welche Abonnementgruppen und Konten benötigen Sie, um Ihre IT-Arbeitslast oder -Infrastruktur zu hosten?

Aufgabe:

- Erstellen Sie die Gruppe von Abonnements und Konten mit Ihrer Benennungskonvention.

## 3. Speicher

Speicher ist ein integraler Bestandteil jeder Azure-Lösung, da er nicht nur Dienste auf Anwendungsebene bereitstellt, sondern auch einen Teil der Infrastruktur zur Unterstützung von virtuellen Computer ausmacht.
 
Es sind zwei Arten von Speicher in Azure verfügbar. Mit Standardspeicher erhalten Sie Zugriff auf Blob-Speicher, Tabellenspeicher, Warteschlangenspeicher und Dateispeicher. Premium-Speicher wurde für Anwendungen mit hoher Leistung, wie z. B. SQL Server in einem AlwaysOn-Cluster, entworfen und unterstützt derzeit nur virtuelle Azure-Datenträger.

Speicherkonten sind an Skalierbarkeitsziele gebunden. Machen Sie sich unter [Microsoft Azure-Abonnement- und -Dienstgrenzen, -Kontingente und -Einschränkungen](azure-subscription-service-limits.md#storage-limits) mit den aktuellen Azure-Speichergrenzwerten vertraut. Siehe auch [Skalierbarkeits- und Leistungsziele für Azure-Speicher](storage-scalability-targets.md).

Azure erstellt virtuelle Computer mit Betriebssystem-Datenträger, einem temporären Datenträger und null oder mehreren optionalen Datenträgern. Der Betriebssystem-Datenträger und Datenträger mit Daten sind Azure-Blobs, während der temporäre Datenträger sich im lokalen Speicher des Knotens befindet, in dem auch der Computer gespeichert ist. Dadurch eignet sich der temporäre Datenträger nicht für Daten, die während eines Systemneustarts beibehalten werden müssen, da der Computer im Hintergrund von einem Knoten zu einem anderen migriert werden kann, wodurch Daten auf diesem Datenträger verloren gehen. Speichern Sie keine Daten im temporären Laufwerk.

Betriebssystem-Datenträger und Datenträger mit Daten verfügen über eine maximale Größe von 1.023 GB, da die maximale Größe eines Blobs 1.024 GB beträgt, und darin müssen die Metadaten (Fußzeile) der VHD-Datei (ein GB hat 10.243 Bytes) enthalten sein. Sie können unter Windows Datenträgerstriping implementieren, um diese Beschränkung zu übergehen.

### Stripesetdatenträger
Neben der Möglichkeit, Datenträger mit mehr als 1.023 GB zu erstellen, wird durch Datenträgerstriping in vielen Fällen die Leistung verbessert, indem mehrere Blobs als Speicher für ein einzelnes Volume dienen können. Dadurch wird die zum Schreiben und Lesen von Daten von einem einzelnen Datenträger erforderliche E/A parallelisiert.

Azure erzwingt Grenzwerte für die Anzahl von Datenträgern und die verfügbare Bandbreite, die sich nach der Größe des virtuellen Computers richten. Ausführliche Informationen finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Wenn Sie Datenträgerstriping für Azure-Datenträger verwenden, beachten Sie die folgenden Richtlinien:

- Datenträger sollten immer die maximale Größe (1.023 GB) haben.
- Fügen Sie die maximal zulässige Anzahl von Datenträgern für den virtuellen Computer an.
- Verwenden Sie Speicherplatzkonfigurationen.
- Verwenden Sie Speicherstripingkonfigurationen.
- Vermeiden Sie die Verwendung von Azure-Datenträger-Cachingoptionen (Cachingrichtlinie = Keine)

Weitere Informationen finden Sie unter [Storage Spaces – Designing for Performance](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx) (Speicherplätze – Entwerfen für optimale Leistung, in englischer Sprache).

### Mehrere Speicherkonten

Mithilfe mehrerer Speicherkonten zur Unterstützung von Datenträgern, die vielen virtuellen Computern zugeordnet sind, stellen Sie sicher, dass die aggregierte E/A dieser Datenträger deutlich unter den Skalierbarkeitszielen für diese Speicherkonten liegt.

Microsoft empfiehlt, dass Sie mit der Bereitstellung eines virtuellen Computers pro Speicherkonto beginnen.

### Entwurf des Speicherlayouts

Um diese Strategien zum Implementieren des Datenträgersubsystems der virtuellen Computer mit guter Leistung zu implementieren, nutzt eine IT-Arbeitslast oder -Infrastruktur i. d. R. viele Speicherkonten. Dadurch können viele VHD-Blobs gehostet werden. In einigen Fällen ist mehr als ein Blob einem einzelnen Volume auf einem virtuellen Computer zugeordnet.

Dies kann die Verwaltungsaufgaben komplexer gestalten. Es ist wichtig, eine solide Strategie für Speicher einschließlich der entsprechenden Namen für die zugrunde liegenden Datenträger und die zugeordneten VHD-Blobs zu entwerfen.

### Wiederholung der Implementierungsrichtlinien für Speicher

Entscheidungen:

- Benötigen Sie Datenträgerstriping, um Datenträger mit mehr als 500 TB zu erstellen?
- Benötigen Sie Datenträgerstriping, um optimale Leistung für Ihre Arbeitslast zu erreichen?
- Welche Speicherkonten benötigen Sie, um Ihre IT-Arbeitslast oder -Infrastruktur zu hosten?

Aufgabe:

- Erstellen Sie die Gruppe von Speicherkonten mit Ihrer Benennungskonvention. Sie können das Azure-Vorschauportal, das Azure-Verwaltungsportal oder das PowerShell-Cmdlet **New-AzureStorageAccount** verwenden.

## 4. Cloud Services

Clouddienste sind ein wesentlicher Baustein von Azure, sowohl für PaaS- als auch IaaS-Dienste. Bei PaaS stellen Clouddienste eine Zuordnung von Rollen dar, deren Instanzen untereinander kommunizieren können. Clouddienste werden mit einer öffentlichen virtuellen IP-Adresse (VIP) und einem Lastenausgleich verknüpft, der eingehenden Datenverkehr aus dem Internet akzeptiert und diesen für die Rollen ausgleicht, die den Datenverkehr empfangen.

Clouddienste bieten bei IaaS ähnliche Funktionalität, obwohl in den meisten Fällen die Lastenausgleichsfunktionen zum Weiterleiten von Datenverkehr an bestimmte TCP- oder UDP-Ports aus dem Internet an die vielen virtuellen Computer innerhalb des Clouddiensts verwendet wird.

Clouddienstnamen sind in IaaS besonders wichtig, da Azure sie als Teil der Standard-Benennungskonvention für Datenträger verwendet. Der Name des Clouddiensts darf nur Buchstaben, Zahlen und Bindestriche enthalten. Das erste und das letzte Zeichen im Feld müssen Buchstaben oder Zahlen sein.

Microsoft Azure macht die Clouddienstnamen verfügbar, da sie der VIP-Adresse in der Domäne "cloudapp.NET" zugeordnet sind. Für eine bessere Benutzererfahrung in der Anwendung kann ggf. ein kürzerer Vanity-Name konfiguriert werden, der den vollqualifizierten Clouddienstnamen ersetzt. Dies erfolgt normalerweise über einen CNAME-Eintrag in Ihrem öffentlichen DNS, der den öffentlichen DNS-Namen der Ressource (z. B. "www.contoso.com") dem DNS-Namen des Clouddiensts zuordnet, der die Ressource (z. B. den Clouddienst mit dem Webserver für "www.contoso.com") hostet.

Darüber hinaus müssen Benennungskonvention für Clouddienste möglicherweise Ausnahmen tolerieren, da die Namen der Clouddienste für alle anderen Microsoft Azure-Clouddienste unabhängig vom Microsoft Azure-Mandanten eindeutig sein müssen.

Azure-Abonnements können maximal 200 Clouddienste unterstützen.

### Wiederholung der Implementierungsrichtlinien für Clouddienste

Entscheidung:

- Welche Clouddienste benötigen Sie, um Ihre IT-Arbeitslast oder -Infrastruktur zu hosten? 

Aufgabe:

- Erstellen Sie die Gruppe von Clouddiensten mit Ihrer Benennungskonvention. Sie können das Azure-Verwaltungsportal oder das PowerShell-Cmdlet **New-AzureService** verwenden.

## 5. Virtuelle Netzwerke

Der nächste logische Schritt ist die Erstellung der virtuellen Netzwerke, die für die Kommunikation zwischen den virtuellen Computern in der Lösung erforderlich sind. Obwohl es möglich ist, mehrere virtuelle Computer einer IT-Arbeitslast in einem Clouddienst zu hosten, werden virtuelle Netzwerke empfohlen.

Virtuelle Netzwerke sind Container für virtuelle Computer, für die auch Subnetze, benutzerdefinierte Adressierung und DNS-Konfigurationsoptionen festgelegt werden können. Virtuelle Computer können unabhängig davon, welchem Clouddienst sie angehören, direkt mit anderen Computern im gleichen virtuellen Netzwerk kommunizieren. Innerhalb des virtuellen Netzwerks bleibt diese Kommunikation privat, ohne dass sie über die öffentlichen Endpunkte übermittelt werden muss. Diese Kommunikation kann mithilfe der IP-Adresse oder des Namens über einen DNS-Server im virtuellen Netzwerk oder lokal erfolgen, wenn der virtuelle Computer mit dem Unternehmensnetzwerk verbunden ist.

### Standortkonnektivität:
Wenn lokale Benutzer und Computer nicht kontinuierlich mit virtuellen Computern im virtuellen Azure-Netzwerk verbunden sein müssen, erstellen Sie ein virtuelles Netzwerk auf ausschließlicher Cloudbasis.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet01.png)
 
Dies eignet sich normalerweise für Internetarbeitslasten, wie z. B. Internetwebserver. Sie können diese virtuellen Computer mithilfe von Remotedesktopverbindungen, PowerShell-Remotesitzungen, Secure Shell (SSH)-Verbindungen und Punkt-zu-Standort-VPN-Verbindungen verwalten.

Da sie nicht mit Ihrem lokalen Netzwerk verbunden sind, können ausschließliche Cloudnetzwerke einen beliebigen Teil des privaten IP-Adressbereichs verwenden.

Wenn lokale Benutzer und Computer ständige Verbindungen mit virtuellen Computern in einem virtuellen Azure-Netzwerk benötigen, erstellen Sie ein standortübergreifendes virtuelles Netzwerk und verbinden es über eine ExpressRoute- oder Standort-zu-Standort-VPN-Verbindung mit Ihrem lokalen Netzwerk.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet02.png)
 
In dieser Konfiguration stellt das virtuelle Azure-Netzwerk im Grunde eine cloudbasierte Erweiterung des lokalen Netzwerks dar.

Da sie eine Verbindung mit Ihrem lokalen Netzwerk herstellen, müssen standortübergreifende virtuelle Netzwerke einen Teil des Adressraums Ihrer Organisation verwenden, der eindeutig ist, und die Routinginfrastruktur muss ein Routing des Datenverkehrs an diesen Bereich unterstützen, indem die Daten an das lokale VPN-Gerät geleitet werden.

Um Pakete aus Ihrem standortübergreifenden virtuellen Netzwerk in Ihr lokales Netzwerk zu übertragen, müssen Sie den Satz von entsprechenden lokalen Adresspräfixen als Teil der Definition des lokalen Netzwerks für das virtuelle Netzwerk konfigurieren. Je nach dem Adressraum des virtuellen Netzwerks sowie den entsprechenden lokalen Standorten können viele Adresspräfixe im lokalen Netzwerk vorhanden sein.

Sie können ein virtuelles Netzwerk auf ausschließlicher Cloudbasis in ein standortübergreifendes virtuelles Netzwerk konvertieren. Dabei ist es jedoch höchstwahrscheinlich erforderlich, dass Sie den virtuellen Netzwerkadressbereich, die Subnetze und Ihre virtuellen Computer, die statische, Azure zugewiesene IP-Adressen (dynamische IP-Adressen, DIPs) verwenden, neu nummerieren. Sie sollten daher sorgfältig den virtuellen Netzwerktyp planen, den Sie benötigen (cloudbasiert oder standortübergreifend), bevor Sie das Netzwerk erstellen.

### Subnetze
Subnetze ermöglichen es Ihnen, Ressourcen zu organisieren, die entweder logisch (z. B. in einem Subnetz für virtuelle Computer, die der gleichen Anwendung zugeordnet sind) oder physisch (z. B. in einem Subnetz pro Clouddienst) verknüpft sind. Sie können mit Subnetzen auch Isolationstechniken zur Erhöhung der Sicherheit implementieren.

Für standortübergreifende virtuelle Netzwerke sollten Sie Subnetze mit den gleichen Konventionen entwerfen, die Sie für lokale Ressourcen verwenden. Bedenken Sie dabei jedoch, dass **Azure immer die ersten drei IP-Adressen des Adressbereichs für jedes Subnetz verwendet**. Um die erforderliche Anzahl der Adressen für das Subnetz zu ermitteln, zählen Sie die virtuellen Computer, die Sie jetzt benötigen, und schätzen Sie, wie viele für zukünftiges Wachstum hinzukommen können. Verwenden Sie dann die folgende Tabelle zum Bestimmen der Größe des Subnetzes.
 
Anzahl benötigter virtueller Computer | Anzahl der erforderlichen Hostbits | Subnetzgröße 
--- | --- | --- 
1–3 | 3 | /29
4–11 | 4 | /28
12–27 | 5 | /27
28–59 | 6 | /26
60–123 | 7 | /25

> [AZURE.NOTE]Für normale lokale Subnetze ist die maximale Anzahl von Adressen für ein Subnetz mit n Hostbits 2<sup>n</sup>-2. Für ein Azure-Subnetz ist die maximale Anzahl von Adressen für ein Subnetz mit n Hostbits 2<sup>n</sup>-5 (2+3 für die Adressen, die Azure in jedem Subnetz verwendet).

Wenn Sie eine zu geringe Subnetzgröße wählen, müssen Sie die virtuellen Computer im Subnetz neu nummerieren und neu bereitstellen.

### Wiederholung der Implementierungsrichtlinien für virtuelle Netzwerke

Entscheidungen:

- Welchen virtuellen Netzwerktyp benötigen Sie, um Ihre IT-Arbeitslast oder -Infrastruktur zu hosten (cloudbasiert oder standortübergreifend)?
- Wie viel Adressraum benötigen Sie bei standortübergreifenden virtuellen Netzwerken, um die Subnetze und virtuellen Computer jetzt und zukünftig bei einer wahrscheinlichen Erweiterung zu hosten?

Aufgaben:

- Definieren Sie den Adressraum für das virtuelle Netzwerk.
- Definieren Sie den Satz von Subnetzen und den Adressraum für die Netzwerke.
- Definieren Sie für standortübergreifende virtuelle Netzwerke den Satz von LAN-Adressbereichen für lokale Speicherorte, die die virtuellen Computer im virtuellen Netzwerk erreichen müssen.
- Erstellen Sie das virtuelle Netzwerk mit der Benennungskonvention. Sie können das Azure-Vorschauportal oder das Azure-Verwaltungsportal verwenden.

## 6. Verfügbarkeitsgruppen

In Azure-PaaS enthalten Clouddienste eine oder mehrere Rollen, die Anwendungscode ausführen. Rollen können eine oder mehrere virtuelle Computerinstanzen aufweisen, die die Struktur automatisch bereitstellt. Azure kann die Instanzen in diesen Rollen jederzeit aktualisieren. Da sie jedoch Teil der gleichen Rolle sind, aktualisiert Azure nicht alle gleichzeitig, um eine Dienstunterbrechung für die Rolle zu verhindern.

In Azure-IaaS wird das Konzept der Rolle nicht berücksichtigt, da jeder virtuelle IaaS-Computer eine Rolle mit einer einzelnen Instanz darstellt. Um Azure darauf hinzuweisen, nicht gleichzeitig zwei oder mehr zugeordnete Computer auszuschalten (z. B. für OS-Updates des Knotens, in dem sie sich befinden), wurde das Konzept der Verfügbarkeit eingeführt. Eine Verfügbarkeitsgruppe teilt Azure mit, nicht alle Computer in der gleichen Verfügbarkeitsgruppe auszuschalten, um zu verhindern, dass eine Dienstunterbrechung entsteht. Die virtuellen Computer, die Mitglieder einer Verfügbarkeitsgruppe sind, bieten eine per Servicelevelvereinbarung garantierte Verfügbarkeit von 99,95 %.

Verfügbarkeitsgruppen müssen Teil der Planung für hohe Verfügbarkeit der Lösung sein. Eine Verfügbarkeitsgruppe wird als Satz von virtuellen Computern innerhalb eines einzelnen Clouddiensts definiert, die den gleichen Verfügbarkeitsgruppennamen aufweisen. Sie können Verfügbarkeitsgruppen erstellen, nachdem Sie Clouddienste erstellt haben.

### Wiederholung der Implementierungsrichtlinien für Verfügbarkeitsgruppen

Entscheidung:

- Wie viele Verfügbarkeitsgruppen benötigen Sie für die verschiedenen Rollen und Ebenen in Ihrer IT-Arbeitslast oder -Infrastruktur?

Aufgabe:

- Definieren Sie die Verfügbarkeitsgruppen mit den Benennungskonvention. Sie können einen virtuellen Computer mit einer Verfügbarkeitsgruppe verknüpfen, wenn Sie die virtuellen Computer erstellen, oder Sie verknüpfen einen virtuellen Computer mit einer Verfügbarkeitsgruppe, nachdem der virtuelle Computer erstellt wurde.

## 7. Virtual Machines

In Azure-PaaS verwaltet Azure virtuelle Computer und deren zugehörige Datenträger. Sie müssen Clouddienste und Rollen erstellen und benennen. Azure erstellt anschließend Instanzen, die diesen Rollen zugeordnet sind. Im Fall von Azure-IaaS können Sie selbst entscheiden, ob Sie Namen für Clouddienste, virtuelle Computer und zugehörige Datenträger angeben.

Um den Verwaltungsaufwand zu reduzieren, verwendet das Azure-Verwaltungsportal den Computernamen als Standardnamen für den zugeordneten Clouddienst (wenn der Kunde einen neuen Clouddienst im Assistenten zur Erstellung des virtuellen Computers erstellt).

Darüber hinaus benennt Azure Datenträger und unterstützende VHD-Blobs mit einer Kombination aus Clouddienstname, Computername und Erstellungsdatum.

Im Allgemeinen ist die Anzahl der Datenträger wesentlich größer als die Anzahl der virtuellen Computer. Sie sollten vorsichtig vorgehen, wenn Sie virtuelle Computer bearbeiten, um zu verhindern, dass Datenträger verwaisen. Außerdem können Datenträger gelöscht werden, ohne dass das unterstützende Blob gelöscht wird. Wenn dies der Fall ist, bleibt das Blob im Speicherkonto, bis es manuell gelöscht wird.

### Wiederholung der Implementierungsrichtlinien für virtuelle Computer

Entscheidung:

- Wie viele virtuelle Computer müssen Sie für die IT-Arbeitslast oder -Infrastruktur bereitstellen?

Aufgaben:

- Definieren Sie die Namen der einzelnen virtuellen Computer mit Ihren Benennungskonventionen.
- Sie können virtuelle Computer über das Azure-Vorschauportal, das Azure-Verwaltungsportal oder das PowerShell-Cmdlet **New-AzureVM** erstellen.

## Beispiel einer IT-Arbeitslast: Contoso-Finanzanalysemodul

Die Contoso Corporation hat ein Finanzanalysemodul der nächsten Generation mit hochmodernen proprietären Algorithmen zum Handel mit Futures entwickelt. Das Unternehmen möchte seinen Kunden dieses Modul als Gruppe von Servern in Azure in folgender Form zur Verfügung stellen:

- Zwei (und zukünftig weitere) IIS-basierte Webserver, auf denen benutzerdefinierte Webdienste in einer Webebene ausgeführt werden
- Zwei (und zukünftig weitere) IIS-basierte Anwendungsserver, die die Berechnungen in einer Anwendungsebene ausführen
- Ein SQL Server 2014-Cluster mit AlwaysOn-Verfügbarkeitsgruppen (zwei SQL Server und ein Mehrheitsknotenzeuge), die historische und laufende Berechnungsdaten in einer Datenbankebene speichern
- Zwei Active Directory-Domänencontroller für eine eigenständige Gesamtstruktur und Domäne in der Authentifizierungsebene, die vom SQL Server-Cluster benötigt wird
- Alle Server befinden sich in zwei Subnetzen – ein Front-End-Subnetz für die Webserver und ein Back-End-Subnetz für die Anwendungsserver, ein SQL Server 2014-Cluster und Domänencontroller

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-tiers.png)
 
Eingehender sicherer Webdatenverkehr der Contoso-Kunden aus dem Internet erfordert Lastenausgleich zwischen den Webservern. Von den Berechnungen angeforderter Datenverkehr in Form von HTTP-Anforderungen der Webserver muss zwischen den Anwendungsservern ausgeglichen werden. Darüber hinaus muss das Modul für hohe Verfügbarkeit entworfen werden.

Der Entwurf muss Folgendes umfassen:

- Ein Contoso-Azure-Abonnement und -Konto
- Speicherkonten
- Ein virtuelles Netzwerk mit zwei Subnetzen
- Einen Satz von Clouddiensten
- Verfügbarkeitsgruppen für die Sätze von Servern mit gleichen Rollen
- Virtual Machines

Alle oben aufgeführten Elemente werden anhand der Contoso-Benennungskonventionen benannt:

- Contoso verwendet [IT-Arbeitslast]-[Standort]-[Azure-Ressource] als Präfix. In diesem Beispiel ist "azfae" (Azure-Finanzanalysemodul) der Name der IT-Arbeitslast und "use" (USA Osten 2) der Standort, da sich die meisten Contoso-Kunden an der Ostküste der USA befinden.
- Speicherkonten verwenden "contosoazfaeusesa[Beschreibung]". Beachten Sie, dass zur Eindeutigkeit Contoso zum Präfix hinzugefügt wurde, und Speicherkontennamen unterstützen keine Bindestriche.
- Clouddienste verwenden "contoso-azfae-use-cs-[Beschreibung]". Beachten Sie, dass zur Eindeutigkeit Contoso zum Präfix hinzugefügt wurde.
- Virtuelle Netzwerke verwenden "AZFAE-USE-VN[Anzahl]".
- Verfügbarkeitsgruppen verwenden "azfae-use-as-[Rolle]".
- Die Namen der virtuellen Computer folgen der Konvention verwenden "use azfae-use-vm-[VM-Name]".

### Azure-Abonnements und -Konten

Contoso verwendet das Enterprise-Abonnement mit dem Namen Contoso-Enterprise-Abonnement zur Abrechnung dieser IT-Arbeitslast.

### Speicherkonten

Contoso hat festgestellt, dass zwei Speicherkonten erforderlich sind:

- **contosoazfaeusesawebapp** für den Standardspeicher der Webserver, Anwendungsserver und Domänencontroller und die zusätzlichen Datenträger
- **contosoazfaeusesasqlclust** für den Premium-Speicher der SQL Server-Clusterserver und die zusätzlichen Datenträger

Contoso hat zwei Speicherkonten mit den folgenden PowerShell-Befehlen erstellt:

	New-AzureStorageAccount -StorageAccountName "contosoazfaeusesawebapp" -Location "East US 2"
	New-AzureStorageAccount -StorageAccountName "contosoazfaeusesasqlclust" -Location "East US 2" -Type Premium_LRS

### Ein virtuelles Netzwerk mit Subnetzen

Da das virtuelle Netzwerk keine fortlaufende Verbindung mit dem lokalen Netzwerk von Contoso benötigt, entschied sich Contoso für ein virtuelles Netzwerk auf ausschließlicher Cloudbasis.

Sie haben ein virtuelles Netzwerk auf ausschließlicher Cloudbasis mit den folgenden Einstellungen über das Azure-Vorschauportal erstellt:

- Name: AZFAE-USE-VN01
- Standort: East US 2
- Adressraum des virtuellen Netzwerks: 10.0.0.0/8
- Erstes Subnetz:
	- Name: FrontEnd
	- Adressraum: 10.0.1.0/24
- Zweites Subnetz:
	- Name: BackEnd
	- Adressraum: 10.0.2.0/24

### Cloud-Dienste

Contoso hat sich für zwei Clouddienste entschieden:

- **contoso-azfae-use-cs-frontend** für die Front-End-Webserver
- **contoso-azfae-use-cs-backend** für Back-End-Anwendungsserver, SQL Server-Clusterserver und Domänencontroller

Contoso hat zwei Clouddienste mit den folgenden PowerShell-Befehlen erstellt:

	New-AzureService -Service "contoso-azfae-use-cs-frontend" -Location "East US 2"
	New-AzureService -Service "contoso-azfae-use-cs-backend" -Location "East US 2"

### Verfügbarkeitsgruppen

Um hohe Verfügbarkeit für alle vier Ebenen des Finanzanalysemoduls zu gewährleisten, entschied sich Contoso für vier Verfügbarkeitsgruppen:

- **azfae-use-as-dc** für Domänencontroller
- **azfae-use-as-web** für die Webserver
- **azfae-use-as-app** für die Anwendungsserver
- **azfae-use-as-sql** für die Server im SQL Server-Cluster

Diese Verfügbarkeitsgruppen werden zusammen mit den virtuellen Computern erstellt.

### Virtual Machines

Contoso hat sich für die folgenden Namen für die virtuellen Azure-Computer entschieden:

- **azfae-use-vm-dc01** für den ersten Domänencontroller
- **azfae-use-vm-dc02** für den zweiten Domänencontroller
- **azfae-use-vm-web01** für den ersten Webserver
- **azfae-use-vm-web02** für den zweiten Webserver
- **azfae-use-vm-app01** für den ersten Anwendungsserver
- **azfae-use-vm-app02** für den zweiten Anwendungsserver
- **azfae-use-vm-sql01** für den ersten SQL Server im SQL Server-Cluster
- **azfae-use-vm-sql02** für den zweiten SQL Server im SQL Server-Cluster
- **azfae-use-vm-sqlmn01** für den Mehrheitsknotenzeugen im SQL Server-Cluster

Hier sehen Sie die daraus resultierende Konfiguration.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-config.png)
 
Diese Konfiguration umfasst:

- Ein virtuelles Netzwerk auf ausschließlicher Cloudbasis mit zwei Subnetzen (Front-End- und Back-End)
- Zwei Clouddienste
- Zwei Speicherkonten
- Vier Verfügbarkeitsgruppen, eine für jede Ebene des Finanzanalysemoduls
- Die virtuellen Computer für die vier Ebenen
- Eine externe Lastenausgleichsgruppe für HTTPS-basierten Webdatenverkehr aus dem Internet an die Webserver
- Eine interne Lastenausgleichsgruppe für unverschlüsselten Webdatenverkehr von den Webservern an die Anwendungsserver

Diese Azure-PowerShell-Befehle erstellen die virtuellen Computer in dieser Konfiguration für die zuvor erstellten Speicherkonten, Clouddienste und das virtuelle Netzwerk.

	#Specify the storage account for the web and application servers
	Set-AzureSubscription –SubscriptionName "Contoso Enterprise Subscription" -CurrentStorageAccountName "contosoazfaeusesawebapp"
	
	#Specify the cloud service name for the web servers
	$ServiceName="contoso-azfae-use-cs-frontend"
	
	#Get the image string for the latest version of the Windows Server 2012 R2 Datacenter image in the gallery
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	#Create the first web server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first web server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-web01 -InstanceSize large -ImageName $image -AvailabilitySetName azfae-use-as-web
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames FrontEnd
	$vm1 | Add-AzureEndpoint -Name Web1 -Protocol tcp -LocalPort 443 -PublicPort 443 -LBSetName "WebSet" -DefaultProbe
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second web server 
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second web server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-web02 -InstanceSize Large -ImageName $image -AvailabilitySetName azfae-use-as-web
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames FrontEnd
	$vm1 | Add-AzureEndpoint -Name Web2 -Protocol tcp -LocalPort 443 -PublicPort 443 -LBSetName "WebSet" -DefaultProbe
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Specify the cloud service name for the application, SQL server, and authentication tiers
	$ServiceName="contoso-azfae-use-cs-backend"
	
	#Create the first domain controller server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-dc01 -InstanceSize Small -ImageName $image -AvailabilitySetName azfae-use-as-dc
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second domain controller server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-dc02 -InstanceSize Small -ImageName $image -AvailabilitySetName azfae-use-as-dc
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-	AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create an internal load balancer instance for the application server tier 
	Add-AzureInternalLoadBalancer -ServiceName $ServiceName -InternalLoadBalancerName "AppTierILB" –SubnetName BackEnd –StaticVNetIPAddress 10.0.2.100
	
	#Create the first application server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first application server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-app01 -InstanceSize Large -ImageName $image -AvailabilitySetName azfae-use-as-app
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureEndpoint -Name App1 -Protocol tcp -LocalPort 80 -PublicPort 80 -LBSetName "AppSet" -InternalLoadBalancerName "AppTierILB" -DefaultProbe
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 500 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-	AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second application server 
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second application server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-app02 -InstanceSize Large -ImageName $image -AvailabilitySetName azfae-use-as-app
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Add-AzureEndpoint -Name App2 -Protocol tcp -LocalPort 80 -PublicPort 80 -LBSetName "AppSet" -InternalLoadBalancerName "AppTierILB" -DefaultProbe
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 500 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Specify the premium storage account for the SQL Server cluster
	Set-AzureSubscription –SubscriptionName "Contoso Enterprise Subscription" -CurrentStorageAccountName "contosoazfaeusesasqlclust"
	
	#Create the majority node witness server for the SQL Server cluster
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the majority node witness server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-sqlmn01 -InstanceSize Medium -ImageName $image -AvailabilitySetName azfae-use-as-sql
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Change the image string for the latest version of the SQL Server 2014 image in the gallery
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	#Create the first SQL Server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SQL Server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-sql01 -InstanceSize A5 -ImageName $image  -AvailabilitySetName azfae-use-as-sql
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1000 -DiskLabel SQLFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second SQL Server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SQL Server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-sql02 -InstanceSize A5 -ImageName $image  -AvailabilitySetName azfae-use-as-sql
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1000 -DiskLabel SQLFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01

## Zusätzliche Ressourcen

[Microsoft Azure-Abonnements und Diensteinschränkungen, Kontingente und Einschränkungen](azure-subscription-service-limits.md#storage-limits)

[Größen virtueller Computer und Clouddienste für Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx)

[Skalierbarkeits- und Leistungsziele für Azure-Speicher](storage-scalability-targets.md)

<!--HONumber=54-->