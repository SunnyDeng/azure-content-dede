<properties
   pageTitle="Computing-, Netzwerk- und Speicheranbieter | Microsoft Azure"
   description="Konzeptuelle Übersicht über die Computing-, Netzwerk- und Speicherressourcenanbieter (CRP, NRP und SRP) im Azure-Ressourcen-Manager"
   services="virtual-machines"
   documentationCenter=""
   authors="mahthi"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="mahthi"/>

# Azure Computing-, Netzwerk- und Speicheranbieter unter dem Azure-Ressourcen-Manager

Wenn der Azure-Ressourcen-Manager um Rechen-, Netzwerk- und Speicherkapazitäten erweitert wird, stellt dies eine erhebliche Vereinfachung der Bereitstellung und Verwaltung komplexer Anwendungen dar, die in IaaS ausgeführt werden. Für viele Anwendungen ist eine Kombination von Ressourcen wie virtuellen Netzwerken, Speicherkonten, virtuellen Computern und Netzwerkschnittstellen erforderlich. Mit dem Azure-Ressourcen-Manager können Sie eine JSON-Vorlage erstellen, um alle Ressourcen gemeinsam als eine einzige Anwendung bereitzustellen und zu verwalten.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Vorteile der Integration von Computing-, Netzwerk- und Speicherkapazitäten unter dem Azure-Ressourcen-Manager

Mit dem Azure-Ressourcen-Manager können Sie vordefinierte Anwendungsvorlagen unkompliziert nutzen oder selbst Anwendungsvorlagen erstellen, um Rechen-, Netzwerk- und Speicherressourcen in Azure bereitzustellen und zu verwalten. In diesem Abschnitt werden die Vorteile einer Bereitstellung von Ressourcen mithilfe der Azure-Ressourcen-Manager erläutert.

-	Komplexität ganz einfach – Entwicklung, Integration und Zusammenarbeit rund um komplexe Anwendungen mit der gesamten Skala von Azure-Ressourcen (z. B. Websites, SQL-Datenbanken, virtuelle Computer, virtuelle Netzwerke) aus einer Vorlagendatei, die freigegeben werden kann
-	Flexibilität durch reproduzierbare Bereitstellungen für Entwicklung, DevOps und Systemadministratoren durch Verwendung der gleichen Vorlagendatei
-	Tiefe Integration von VM-Erweiterungen (benutzerdefinierte Skripts, DSC, Chef, Puppet usw.) mit dem Azure-Ressourcen-Manager in einer Vorlagendatei ermöglicht eine unkomplizierte Orchestrierung der VM-Setupkonfiguration
-	Definieren von Tags und Weitergabe dieser Tags zur Abrechnung bei Server-, Netzwerk- und Speicher-Ressourcen
-	Einfache, doch genaue organisatorische Ressourcenzugriffsverwaltung mit Azure Role-Based Access Control (RBAC, rollenbasierte Zugriffskontrolle)
-	Vereinfachter Upgrade-/Updateverlauf durch Ändern und erneutes Bereitstellen der Originalvorlage


## Verbesserungen der Computing-, Netzwerk- und Speicher-APIs unter dem Azure-Ressourcen-Manager

Zusätzlich zu den oben genannten Vorteilen sind einige bedeutende Leistungsverbesserungen in den veröffentlichten APIs verfügbar.

-	Möglichkeit zu umfassender paralleler Bereitstellung von virtuellen Computern
-	Unterstützung für 3 Fehlerdomänen in Verfügbarkeitsgruppen
-	Verbesserte Erweiterung benutzerdefinierter Skripts: Alle Skripts aus jeder öffentlich zugänglichen benutzerdefinierten URL können angegeben werden
- Integration von virtuellen Computern mit dem Azure-Schlüsseltresor ermöglicht hoch sicheres Speichern und privates Bereitstellen geheimer Informationen von [FIPS-überprüften](http://wikipedia.org/wiki/FIPS_140-2) [Hardwaresicherheitsmodulen](http://wikipedia.org/wiki/Hardware_security_module)
-	Bietet die Grundbausteine für Netzwerke über APIs, sodass Kunden komplexe Anwendungen mit Netzwerkschnittstellen, Lastenausgleichsmodulen und virtuellen Netzwerken erstellen können.
-	Netzwerkschnittstellen als neue Objekte ermöglichen das Aufrechterhalten und Weiterverwenden komplexer Netzwerkkonfigurationen für virtuelle Computer
-	Lastenausgleichsmodule als erstklassige Ressourcen ermöglichen IP-Adresszuweisungen
-	Präzise Virtual Network-APIs ermöglichen die eine einfache Verwaltung einzelner virtueller Netzwerke

## Konzeptuelle Unterschiede durch die Einführung neuer APIs

In diesem Abschnitt werden einige der wichtigsten konzeptuellen Unterschiede zwischen den heute verfügbaren XML-basierten APIs und den im Azure-Ressourcen-Manager verfügbaren JSON-basierten APIs für Rechen-, Netzwerk- und Speicherkapazitäten erläutert.

 Element | Azure-Dienstverwaltung (XML-basiert) | Rechen-, Netzwerk- und Speicheranbieter (JSON-basiert)
 ---|---|---
| Clouddienst für virtuelle Computer |	Beim Clouddienst handelte es sich um einen Container für die virtuellen Computer, für den Plattformverfügbarkeit sowie Lastenausgleich erforderlich waren. | Der Clouddienst ist kein erforderliches Objekt zum Erstellen eines virtuellen Computers mithilfe des neuen Modells mehr. |
| Verfügbarkeitsgruppen | Die Plattformverfügbarkeit wurde durch das Konfigurieren des gleichen „AvailabilitySetName“ auf den virtuellen Computern angezeigt. Die maximale Anzahl von Fehlerdomänen betrug 2. | Verfügbarkeitsgruppen sind Ressourcen, die vom Microsoft.Compute-Anbieter bereitgestellt werden. Virtuelle Computer, für die eine hohe Verfügbarkeit erforderlich ist, müssen in der Verfügbarkeitsgruppe enthalten sein. Die maximale Anzahl von Fehlerdomänen beträgt nun 3. |
| Affinitätsgruppen |	Zum Erstellen von virtuellen Netzwerken waren Affinitätsgruppen erforderlich. Dies ist jedoch seit der Einführung regionaler virtueller Netzwerke nicht mehr erforderlich. |Zur Vereinfachung ist das Affinitätsgruppenkonzept in über den Azure-Ressourcen-Manager verfügbaren APIs nicht vorhanden. |
| Lastenausgleich | Durch das Erstellen von Clouddiensten ist ein impliziter Lastenausgleich für die bereitgestellten virtuellen Computer verfügbar. | Der Lastenausgleich ist eine Ressource, die vom Microsoft.Network-Anbieter bereitgestellt wird. Die primäre Netzwerkschnittstelle der virtuellen Computer, die mit einem Lastenausgleich versehen werden soll, muss auf das Lastenausgleichsmodul verweisen. Lastenausgleichsmodule können intern oder extern sein. [Weitere Informationen](resource-groups-networking.md) |
|Virtuelle IP-Adresse | Clouddienste erhalten eine Standard-VIP (virtuelle IP-Adresse), wenn ihnen ein virtueller Computer hinzugefügt wird. Die virtuelle IP-Adresse ist dem impliziten Lastenausgleich zugeordnet. | Die öffentliche IP-Adresse ist eine Ressource, die vom Microsoft.Network-Anbieter bereitgestellt wird. Die öffentliche IP-Adresse kann statisch (reserviert) oder dynamisch sein. Dynamische öffentliche IP-Adressen können einem Lastenausgleich zugewiesen werden. Öffentliche IP-Adressen können mithilfe von Sicherheitsgruppen gesichert werden. |
|Reservierte IP-Adresse|	Sie können eine IP-Adresse in Azure reservieren und einem Clouddienst zuordnen, um die Persistenz der IP-Adresse sicherzustellen. | Die Öffentliche IP-Adresse kann im Modus „Static“ erstellt werden. Sie bietet die gleiche Funktionalität wie eine reservierte IP-Adresse („Reserved IP Address“). Statische öffentliche IP-Adressen können nur direkt einem Lastenausgleich zugewiesen werden. |
|Öffentliche IP-Adresse (PIP) pro virtuellem Computer | Öffentliche IP-Adressen können virtuellen Computern auch direkt zugeordnet werden. | Die öffentliche IP-Adresse ist eine Ressource, die vom Microsoft.Network-Anbieter bereitgestellt wird. Die öffentliche IP-Adresse kann statisch (reserviert) oder dynamisch sein. Netzwerkschnittstellen können jedoch nur dynamische öffentliche IP-Adressen zugewiesen werden, um direkt öffentliche IPs pro virtuellem Computer abzurufen. |
|Endpunkte| Eingabeendpunkte mussten auf virtuellen Computern konfiguriert werden, um Konnektivität für bestimmte Ports zu ermöglichen. Zu den häufigen Verbindungsmodi mit virtuellen Computern gehört das Einrichten von Eingabeendpunkten. | Es können auch eingehende NAT-Regeln auf Lastenausgleichsmodulen konfiguriert werden, um Endpunkte auf bestimmten Ports zum Herstellen einer Verbindung mit den virtuellen Computern zu aktivieren. |
|DNS-Name| Clouddienste erhielten implizite global eindeutige DNS-Namen. Beispiel: `mycoffeeshop.cloudapp.net`. | DNS-Namen sind optionale Parameter, die auf öffentlichen IP-Adressressourcen angegeben werden können. Der FQDN weist das folgende Format auf: `<domainlabel>.<region>.cloudapp.azure.com`. |
|Netzwerkschnittstellen | Primäre und sekundäre Netzwerkschnittstellen und deren Eigenschaften wurden als Netzwerkkonfiguration eines virtuellen Computers definiert. | Die Netzwerkschnittstelle ist eine Ressource, die vom Microsoft.Network-Anbieter bereitgestellt wird. Der Lebenszyklus von Netzwerkschnittstellen ist nicht an virtuelle Computer gebunden. |

## Erste Schritte mit Azure-Vorlagen für virtuelle Computer

Sie können mit den Azure-Vorlagen beginnen, indem Sie die verschiedenen Tools zum Entwickeln und Bereitstellen der Plattform nutzen.

### Azure-Vorschauportal

Das Azure-Vorschauportal bietet weiterhin die Möglichkeit, virtuelle Computer gleichzeitig mit dem klassischen Bereitstellungsmodell und mit dem Ressourcen-Manager-Bereitstellungsmodell bereitzustellen. Im Azure-Vorschauportal sind auch Bereitstellungen mit benutzerdefinierten Vorlagen möglich.

### Azure PowerShell

Azure PowerShell wird zwei Bereitstellungsmodi bieten: den **AzureServiceManagement**-Modus und den **AzureResourceManager**-Modus. Der AzureResourceManager-Modus enthält jetzt auch die Cmdlets zum Verwalten von virtuellen Computern, virtuellen Netzwerken und Speicherkonten. Weitere Informationen dazu finden Sie [hier](../powershell-azure-resource-manager.md).

### Azure-Befehlszeilenschnittstelle

Der Azure-Befehlszeilenschnittstelle (Azure-CLI) wird zwei Bereitstellungsmodi bieten: den **AzureServiceManagement**-Modus und den **AzureResourceManager**-Modus. Der AzureResourceManager-Modus enthält jetzt auch Befehle zum Verwalten von virtuellen Computern, virtuellen Netzwerken und Speicherkonten. Weitere Informationen dazu finden Sie [hier](xplat-cli-azure-resource-manager.md).

### Visual Studio

Mit der aktuellen Version von Azure SDK für Visual Studio können Sie virtuelle Computer sowie komplexe Anwendungen direkt in Visual Studio erstellen und bereitstellen. Visual Studio bietet die Möglichkeit zum Bereitstellen aus einer Liste vordefinierter Vorlagen oder aus einer leeren Vorlage.

### REST-APIs

Die ausführliche REST-API-Dokumentation zu den Rechen-, Netzwerk- und Speicherressourcenanbietern finden Sie [hier](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Häufig gestellte Fragen

**Kann ich mit dem neuen Azure-Ressourcen-Manager einen virtuellen Computer erstellen, der in einem mithilfe der Azure-Dienstverwaltungs-APIs erstellten virtuellen Netzwerk oder Speicherkonto bereitgestellt wird?**

Dies wird zurzeit nicht unterstützt. Sie können die neuen Azure-Ressourcen-Manager-APIs nicht zum Bereitstellen eines virtuellen Computers in einem mithilfe der Azure-Dienstverwaltungs-APIs erstellten virtuellen Netzwerk oder Speicherkonto verwenden.

**Kann ich mit den neuen Azure-Ressourcen-Manager-APIs einen virtuellen Computer aus einem Benutzerabbild erstellen, das mithilfe der Azure-Ressourcen-Manager-APIs erstellt wurde?**

Dies wird zurzeit nicht unterstützt. Sie können jedoch die VHD-Dateien aus einem Speicherkonto kopieren, das mithilfe der Dienstverwaltungs-APIs erstellt wurde, und sie in ein neues Konto einfügen, das mithilfe der Azure-Ressourcen-Manager-APIs erstellt wurde.

**Welche Auswirkungen gibt es auf das Kontingent meines Abonnements?**

Die Kontingente für virtuelle Computer, virtuelle Netzwerke und Speicherkonten, die über die neuen Azure-Ressourcen-Manager-APIs erstellt wurden, und Ihre aktuell vorhandenen Kontingente sind separat. Jedes Abonnement erhält neue Kontingente, um die Ressourcen mit den neuen APIs zu erstellen. Weitere Informationen zu den zusätzlichen Kontingenten finden Sie [hier](../azure-subscription-service-limits.md).

**Kann ich meine automatisierten Skripts zur Bereitstellung virtueller Computer, virtueller Netzwerke, Speicherkonten usw. durch die neuen Azure-Ressourcen-Manager-APIs weiterhin verwenden?**

Die Automatisierungen und Skripts, die Sie erstellt haben, bleiben für die vorhandenen virtuellen Computer und virtuellen Netzwerke funktionsfähig, die im Azure-Dienstverwaltungsmodus erstellt wurden. Die Skripts müssen jedoch aktualisiert werden, damit das neue Schema zum Erstellen der gleichen Ressourcen über den neuen Azure-Ressourcen-Manager-Modus verwendet werden kann. Informieren Sie sich näher über das Ändern Ihrer [Azure-CLI-Skripts](xplat-cli-azure-manage-vm-asm-arm.md).

**Können die mit den neuen Azure-Ressourcen-Manager-APIs erstellten virtuellen Netzwerke mit meinem Express Route-Schaltkreis verbunden werden?**

Dies wird zurzeit nicht unterstützt. Die mit den neuen Azure-Ressourcen-Manager-APIs erstellten virtuellen Netzwerke können nicht mit Express Route-Schaltkreisen verbunden werden Dies wird zukünftig unterstützt.

**Wo finde ich Beispiele für Vorlagen für den Azure-Ressourcen-Manager?**

Einen umfassenden Satz von Startervorlagen finden Sie unter [Schnellstartvorlagen für den Azure-Ressourcen-Manager](http://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_1125_2015-->