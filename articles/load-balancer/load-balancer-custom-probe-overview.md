<properties
   pageTitle="Benutzerdefinierte Load Balancer-Tests und Überwachen des Integritätsstatus | Microsoft Azure"
   description="Erfahren Sie, wie Sie mit benutzerdefinierten Tests für Azure Load Balancer Instanzen hinter einem Load Balancer überwachen."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />


# Load Balancer-Tests

Azure Load Balancer bietet die Möglichkeit, die Integrität der Serverinstanzen mithilfe von Tests zu überwachen. Wenn ein Test nicht reagiert, beendet der Load Balancer das Senden neuer Verbindungen an die fehlerhafte Instanz. Die vorhandenen Verbindungen sind nicht betroffen, und neue Verbindungen werden an fehlerfreie Instanzen gesendet.

Wenn Sie virtuelle Computer hinter einem Load Balancer verwenden, muss ein benutzerdefinierter TCP- oder HTTP-Test konfiguriert werden. Clouddienstrollen (Workerrollen und Webrollen) sind die einzigen Serverinstanzen mit überwachten Tests von Gast-Agents.

## Grundlegendes zu Anzahl und Timeout von Tests

Das Verhalten von Tests hängt von folgenden Faktoren ab:
- Anzahl der erfolgreichen Tests, bei der eine Instanz als aktiv bezeichnet werden kann.
- Anzahl der fehlerhaften Tests, bei der eine Instanz als inaktiv bezeichnet wird.

Der in SuccessFailCount festgelegte Wert für Timeout und Häufigkeit legt fest, ob eine Instanz ausgeführt oder nicht ausgeführt werden soll. Im Azure-Portal wird das Timeout auf das Doppelte des Werts für die Häufigkeit festgelegt.

Die Konfiguration von Tests muss für alle Instanzen mit Lastenausgleich für einen Endpunkt (mit anderen Worten: eine Gruppe mit Lastenausgleich) identisch sein. Das heißt, dass Sie nicht für jede Rolleninstanz oder VM im selben gehosteten Dienst für eine bestimmte Endpunktkombination eine unterschiedliche Testkonfiguration wählen können. Beispielsweise muss jede Instanz identische lokale Ports und Timeouts aufweisen.


>[AZURE.IMPORTANT] Ein Load Balancer-Test verwendet die IP-Adresse 168.63.129.16. Diese öffentliche IP-Adresse ermöglicht die Kommunikation mit internen Plattformressourcen für das Azure Virtual Network-Szenario mit eigener IP-Adresse. Die virtuelle öffentliche IP-Adresse 168.63.129.16 wird in allen Regionen verwendet und nicht geändert. Es wird empfohlen, dass Sie diese IP-Adresse in lokalen Firewallrichtlinien zulassen. Dies dürfte kein Sicherheitsrisiko darstellen, da nur die interne Azure-Plattform eine Nachricht von dieser Adresse senden kann. Andernfalls ist in einer Vielzahl von Szenarien ein unerwartetes Verhalten die Folge, wie z. B. beim Konfigurieren desselben IP-Adressbereichs von 168.63.129.16 mit duplizierten IP-Adressen.


## Weitere Informationen über die Testtypen

### Gast-Agent-Test

Dieser Test ist nur für Azure Cloud Services verfügbar. Load Balancer nutzt den Gast-Agent auf dem virtuellen Computer. Dann lauscht und antwortet er nur mit einer HTTP-OK-Antwort 200, wenn die Instanz bereit ist (also nicht gerade beschäftigt oder angehalten ist oder recycelt wird).

Weitere Informationen finden Sie unter [Konfigurieren der Dienstdefinitionsdatei (CSDEF) für Integritätstests](https://msdn.microsoft.com/library/azure/jj151530.asp) und [Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff für Clouddienste](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### Was kann einen Gast-Agent-Test veranlassen, eine Instanz als fehlerhaft zu markieren?

Wenn der Gast-Agent nicht mit dem HTTP-OK-Code 200 antwortet, kennzeichnet der Load Balancer die Instanz als nicht reagierend und sendet keinen Datenverkehr mehr an diese Instanz. Der Load Balancer pingt die Instanz weiterhin. Wenn der Gast-Agent mit dem HTTP-Code 200 antwortet, sendet der Load Balancer wieder Datenverkehr an diese Instanz.

Wenn Sie eine Webrolle verwenden, wird der Websitecode in der Regel in „w3wp.exe“ ausgeführt. Dieses Programm wird nicht von der Azure-Fabric oder vom Gast-Agent überwacht. Das bedeutet, dass Fehler in „w3wp.exe“ (z. B. HTTP 500-Antworten) nicht an den Gast-Agent gemeldet werden und dass der Load Balancer diese Instanz nicht aus der Rotation entfernt.


### Benutzerdefinierter HTTP-Test

Der benutzerdefinierte HTTP-Load Balancer-Test erhält Vorrang vor dem Standard-Gast-Agent-Test, sodass Sie Ihre eigene Logik zum Bestimmen der Integrität der Rolleninstanz erstellen können. Der Load Balancer testet Ihren Endpunkt standardmäßig alle 15 Sekunden. Die Instanz wird in die Load Balancer-Rotation einbezogen, wenn sie innerhalb des Zeitlimits (standardmäßig 31 Sekunden) mit HTTP 200 reagiert.

Dies kann für die Implementierung Ihrer eigenen Logik zum Entfernen von Instanzen aus der Load Balancer-Rotation nützlich sein. Sie könnten z. B. eine Instanz entfernen, wenn sie über 90 % CPU beansprucht und einen anderen Status als 200 zurückgibt. Wenn Ihre Webrollen „w3wp.exe“ verwenden, bedeutet dies auch eine automatische Überwachung der Website, da Fehler im Websitecode einen Nicht-200-Status an den Load Balancer-Test zurückgeben.

>[AZURE.NOTE] Der benutzerdefinierte HTTP-Test unterstützt nur relative Pfade und das HTTP-Protokoll. HTTPS wird nicht unterstützt.


### Was kann einen benutzerdefinierten HTTP-Test veranlassen, eine Instanz als fehlerhaft zu markieren?

- Die HTTP-Anwendung gibt einen anderen HTTP-Antwortcode als 200 zurück (z. B. 403, 404 oder 500). Hierbei handelt es sich um eine positive Bestätigung, dass die Anwendungsinstanz sofort außer Betrieb genommen werden sollte.

-  Der HTTP-Server reagiert nach dem Timeoutzeitraum nicht mehr. Je nach dem festgelegten Timeoutwert kann dies bedeuten, dass mehrere Testanforderungen unbeantwortet bleiben, bevor der Test als nicht ausgeführt markiert wird (d. h. bevor SuccessFailCount-Tests gesendet werden).
- 	Der Server schließt die Verbindung durch „TCP Reset“.

### Benutzerdefinierter TCP-Test

TCP-Tests leiten eine Verbindung über einen Drei-Wege-Handshake mit dem definierten Port ein.

### Was kann einen benutzerdefinierten TCP-Test veranlassen, eine Instanz als fehlerhaft zu markieren?

- Der TCP-Server reagiert nach dem Timeoutzeitraum nicht mehr. Wann der Test als nicht ausgeführt markiert wird, hängt von der Anzahl fehlerhafter Testanforderungen ab, die unbeantwortet bleiben können, bevor der Test als nicht ausgeführt gilt.
- 	Der Test empfängt ein TCP Reset von der Rolleninstanz.

Weitere Informationen zum Konfigurieren eines HTTP-Integritätstests oder eines TCP-Tests finden Sie unter [Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff in Resource Manager unter Verwendung von PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## Erneutes Hinzufügen fehlerfreier Instanzen zum Load Balancer

TCP- und HTTP-Tests werden als fehlerfrei eingestuft und markieren die Rolleninstanz als fehlerfrei, wenn:

-  Beim ersten Start der VM erhält der Load Balancer einen positiven Test.
- Der Wert von „SuccessFailCount“ (oben beschrieben) definiert die Anzahl erfolgreicher Tests, die erforderlich sind, um die Rolleninstanz als fehlerfrei zu markieren. Wenn eine Rolleninstanz entfernt wurde, muss die Anzahl der erfolgreichen, aufeinanderfolgenden Tests gleich oder größer sein als der Wert von SuccessFailCount, damit die Rolleninstanz als aktiv markiert wird.

>[AZURE.NOTE] Wenn die Integrität einer Rolleninstanz schwankt, wartet der Load Balancer länger, ehe die Rolleninstanz wieder in den fehlerfreien Zustand versetzt wird. Dies erfolgt zum Schutz der Benutzer und Infrastruktur über die Richtlinie.

## Verwenden der Protokollanalyse für den Load Balancer

Mit der [Protokollanalyse für den Load Balancer](load-balancer-monitor-log.md) können Sie den Testintegritätsstatus und die Testanzahl überprüfen. Die Protokollierung kann mit Power BI oder Azure Operational Insights verwendet werden, um Statistiken zum Integritätsstatus des Load Balancers bereitzustellen.

<!---HONumber=AcomDC_0323_2016-->