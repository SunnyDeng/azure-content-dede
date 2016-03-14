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

Azure Load Balancer bietet die Möglichkeit, die Integrität der Serverinstanzen mithilfe von Tests zu überwachen. Wenn ein Test nicht reagiert, beendet Azure Load Balancer das Senden neuer Verbindungen an die fehlerhaften Instanzen. Die vorhandenen Verbindungen sind nicht betroffen, und neue Verbindungsanforderungen werden an fehlerfreie Instanzen gesendet.

Wenn Sie virtuelle Computer hinter einem Load Balancer verwenden, muss ein benutzerdefinierter TCP- oder HTTP-Test konfiguriert werden. Clouddienstrollen (Workerrollen und Webrollen) sind die einzigen Serverinstanzen mit überwachten Tests von Gast-Agents.
 
## Grundlegendes zu Anzahl und Timeout von Tests

Das Testverhalten hängt vom Verhältnis erfolgreicher und nicht erfolgreicher Tests ab, das erforderlich ist, um eine Instanz als in/außer Betrieb zu markieren. Die Formel für die Berechnung lautet SuccessFailCount = Timeout/Häufigkeit. Für das Portal wird das Timeout auf das Doppelte des Werts der Häufigkeit festgelegt (Timeout = Häufigkeit * 2).

Die Konfiguration von Tests aller Instanzen mit Lastenausgleich für einen Endpunkt (Satz mit Lastenausgleich) muss identisch sein. Das heißt, dass Sie nicht für jede Rolleninstanz oder VM im selben gehosteten Dienst für eine bestimmte Endpunktkombination eine unterschiedliche Testkonfiguration (z. B. lokaler Port, Timeout usw.) wählen können.


>[AZURE.IMPORTANT] Ein Load Balancer-Test verwendet die IP-Adresse 168.63.129.16. Diese öffentliche IP-Adresse dient zum Ermöglichen eines Kommunikationskanals zu internen Plattformressourcen für das Szenario mit einem virtuellen Netzwerk mit Ihrer eigenen IP-Adresse. Die virtuelle öffentliche IP-Adresse 168.63.129.16 wird in allen Regionen verwendet und nicht geändert. Die IP-Adresse sollte in allen lokalen Firewallrichtlinien zulässig sein. Dies dürfte kein Sicherheitsrisiko darstellen, da nur die interne Azure-Plattform eine Nachricht von dieser Adresse senden kann. Andernfalls ist in einer Vielzahl von Szenarien ein unerwartetes Verhalten die Folge, wie z. B. beim Konfigurieren desselben IP-Adressbereichs von 168.63.129.16 mit duplizierter IP-Adresse.


## Arten von Tests

### Gast-Agent-Test

Nur für Clouddienste. Azure Load Balancer nutzt den Gast-Agent auf dem virtuellen Computer und überwacht und antwortet nur mit einer HTTP-OK-Antwort 200, wenn die Instanz bereit ist (d. h. die Instanz nicht ausgelastet ist bzw. recycelt oder angehalten wird usw.).

Weitere Informationen finden Sie unter [Konfigurieren der Dienstdefinitionsdatei (.csdef) für Integritätstests](https://msdn.microsoft.com/library/azure/jj151530.asp) und [Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff für Clouddienste](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).
 
### Was kann einen Gast-Agent-Test veranlassen, eine Instanz als fehlerhaft zu markieren?

Wenn der Gast-Agent nicht mit dem HTTP-OK-Code 200 antwortet, kennzeichnet der Azure-Lastenausgleich die Instanz als nicht reagierend und sendet keinen Datenverkehr mehr an diese Instanz. Der Azure-Lastenausgleich sendet weiterhin ein Ping an die Instanz, und falls der Gast-Agent mit HTTP-Code 200 antwortet, sendet der Azure-Lastenausgleich wieder Datenverkehr an diese Instanz.

Bei Verwendung einer Webrolle wird der Websitecode in der Regel in der ausführbaren Datei „w3wp.exe“ ausgeführt, die von der Azure-Struktur oder dem Gast-Agent nicht überwacht wird. Fehler in „w3wp.exe“ (z. B. HTTP 500-Antworten) werden dem Gast-Agent nicht gemeldet, und der Load Balancer weiß nicht, dass er diese Instanz aus der Rotation entfernen sollte.


### Benutzerdefinierter HTTP-Test

Der benutzerdefinierte HTTP-Load Balancer-Test überschreibt den Standard-Gast-Agent-Test und ermöglicht Ihnen, Ihre eigene Logik zum Bestimmen der Integrität der Rolleninstanz zu erstellen. Der Load Balancer testet den Endpunkt (standardmäßig alle 15 Sekunden), und die Instanz wird in der Load Balancer-Rotation berücksichtigt, wenn sie innerhalb des Zeitlimits (standardmäßig 31 Sekunden) mit einem HTTP 200-Status reagiert. Dies kann hilfreich sein, um eigene Logik zum Entfernen von Instanzen aus der Load Balancer-Rotation zu implementieren, wie z. B. durch Rückgabe eines Nicht-200-Status, wenn die Instanz über 90 % CPU-Auslastung aufweist. Für Webrollen mit „w3wp.exe“ bedeutet dies auch eine automatische Überwachung der Website, da Fehler im Websitecode einen Nicht-200-Status an den Load Balancer-Test zurückgeben.

>[AZURE.NOTE] Der benutzerdefinierter HTTP-Test unterstützt nur relative Pfade und das HTTP-Protokoll. HTTPS wird nicht unterstützt.


### Was kann einen benutzerdefinierten HTTP-Test veranlassen, eine Instanz als fehlerhaft zu markieren? 

- Die HTTP-Anwendung gibt einen anderen HTTP-Antwortcode als 200 zurück (d. h. 403, 404, 500 usw.). Hierbei handelt es sich um eine positive Bestätigung, dass die Anwendungsinstanz sofort außer Betrieb genommen werden sollte.

-  Wenn der HTTP-Server nach dem Timeoutzeitraum nicht mehr reagiert. Beachten Sie, dass dies je nach festgelegtem Timeoutwert bedeuten kann, dass mehrere Testanforderungen unbeantwortet bleiben, bevor der Test mit „Ausgefallen“ gekennzeichnet wird (d. h. „SuccessFailCount“-Tests werden gesendet).
- 	Wenn der Server die Verbindung über TCP Reset schließt.

### Benutzerdefinierter TCP-Test

TCP-Tests leiten eine Verbindung über einen Drei-Wege-Handshake mit dem definierten Port ein.

### Was kann einen benutzerdefinierten TCP-Test veranlassen, eine Instanz als fehlerhaft zu markieren?

- Wenn der TCP-Server nach dem Timeoutzeitraum nicht mehr reagiert. Das hängt von der Anzahl misslungener Testanforderungen ab, die als nicht beantwortet konfiguriert wurden, ehe der Test als „Ausgefallen“ markiert wird.
- 	Eine TCP Reset wird von der Rolleninstanz empfangen.

Unter [Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff im Ressourcen-Manager](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer) erfahren Sie, wie ein HTTP-Integritätstest oder TCP-Test konfiguriert wird.

## Erneutes Hinzufügen fehlerfreier Instanzen zum Load Balancer

TCP- und HTTP-Tests werden als fehlerfrei eingestuft und markieren die Rolleninstanz als fehlerfrei, wenn:

- Beim ersten Start der VM der Load Balancer einen positiven Test aufweist.
- Der Wert von „SuccessFailCount“ bestimmt die Anzahl erfolgreicher Tests, die erforderlich sind, um die Rolleninstanz als fehlerfrei zu markieren. Wenn eine Rolleninstanz entfernt wurde, sind eine „SuccessFailCount“ entsprechende Anzahl erfolgreicher Tests erforderlich, um die Rolleninstanz als „In Betrieb“ zu kennzeichnen.

>[AZURE.NOTE] Wenn die Integrität einer Rolleninstanz schwankt, wartet der Azure Load Balancer länger, ehe die Rolleninstanz wieder in den fehlerfreien Zustand versetzt wird. Dies erfolgt zum Schutz der Benutzer und Infrastruktur über die Richtlinie.

## Protokollanalyse für den Load Balancer

Mit der [Protokollanalyse für den Load Balancer](load-balancer-monitor-log.md) können Sie den Testintegritätsstatus und die Testanzahl überprüfen. Die Protokollierung kann mit Power BI oder Operation Insights verwendet werden, um Statistiken zum Integritätsstatus des Load Balancers bereitzustellen.
 

<!---HONumber=AcomDC_0302_2016-->