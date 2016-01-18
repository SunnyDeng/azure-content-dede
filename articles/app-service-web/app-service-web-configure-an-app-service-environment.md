<properties 
	pageTitle="Konfigurieren einer App Service-Umgebung" 
	description="Konfiguration, Verwaltung und Überwachung von App Service-Umgebungen" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="ccompy"/>


# Konfigurieren einer App Service-Umgebung #

## Übersicht ##

App Service-Umgebungen sind ein Feature des Premium-Tarifs im Azure App Service, das neue Funktionen für die Skalierung und den Netzwerkzugriff bietet. Diese neue Skalierungsfunktion erlaubt das Platzieren einer Instanz des Azure App Service in Ihrem VNET. Mit der Funktion können Sie Web-Apps, Mobile Apps und API-Apps hosten. Logik-Apps können noch nicht in einer App Service-Umgebung ausgeführt werden.

Wenn Sie mit der Funktion der App Service-Umgebung nicht vertraut sind, lesen Sie das Dokument [Was ist eine App Service-Umgebung](app-service-app-service-environment-intro.md). Informationen zum Erstellen einer App Service-Umgebung finden Sie im Dokument [Erstellen einer App Service-Umgebung](app-service-web-how-to-create-an-app-service-environment.md).

Allgemein besteht eine App Service-Umgebung aus mehreren Hauptkomponenten:

- Compute-Ressourcen, die im gehosteten Dienst der Azure App-Umgebung ausgeführt werden
- Speicher
- Datenbank
- Ein klassisches „v1“-Virtual Network mit mindestens einem Subnetz
- Subnetz mit darin ausgeführtem gehosteten Dienst der Azure App-Umgebung

Die Computeressourcen werden für Ihre vier Ressourcenpools verwendet. Jede App Service-Umgebung verfügt über eine Gruppe von Front-Ends und drei Workerpools. Sie müssen nicht alle drei Workerpools verwenden und können auch nur einen einzigen nutzen. Die Front-Ends sind die HTTP-Endpunkte für die in der App Service-Umgebung gehosteten Apps. In den Workern werden die Apps tatsächlich ausgeführt. Die Frage, wann Sie mehr Front-Ends oder Worker hinzufügen müssen, ist mit der Leistung der in der App Service-Umgebung platzierten Apps verbunden. Nehmen Sie beispielsweise an, in Ihrer App Service-Umgebung befindet sich nur eine einzige App. Dies ist eine Hello-World-App, bei der eine sehr hohe Anzahl von Anforderungen eingeht. In diesem Fall müssten Sie die Anzahl der Front-Ends erhöhen, um die HTTP-Last zu bewältigen. Die Anzahl der Worker muss dagegen nicht erhöht werden. Dies alles von Hand zu steuern wäre eine sehr mühselige Aufgabe, insbesondere, wenn Sie berücksichtigen, dass in jeder App Service-Umgebung verschiedene Apps mit sehr unterschiedlichen Leistungskriterien ausgeführt werden. Deshalb wurde den App Service-Umgebungen eine Funktion zum automatischen Skalieren hinzugefügt, die hier für Erleichterungen sorgt. Einzelheiten zum Skalieren und automatischen Skalieren von App Service-Umgebungen erfahren Sie unter [Konfigurieren der automatischen Skalierung in einer App Service-Umgebung][ASEAutoscale].

Jede App Service-Umgebung ist mit 500 GB Speicherplatz konfiguriert. Dieser Speicherplatz wird von allen Apps in der App Service-Umgebung verwendet. Er ist Teil der App Service-Umgebung und kann derzeit nicht anders genutzt werden (z. B. als Speicherplatz für den Kunden).

Die Datenbank enthält Informationen, die die Umgebung definieren, und auch Details zu den darin ausgeführten Apps. Dies ist ebenfalls Teil des gebuchten Azure-Abonnements. Die Kunden haben hier keine direkten Möglichkeiten zu Änderungen.

Das virtuelle Netzwerk, das mit der App Service-Umgebung verwendet wird, kann das beim Erstellen der App Service-Umgebung eingerichtete sein oder eines, das bereits vorher vorhanden war. Wenn sich Ihre App Service-Umgebung in einer Ressourcengruppe befinden soll, die von der für Ihre App Service-Umgebung verwendeten getrennt ist, dann müssen Sie Ihr VNET unabhängig vom Arbeitsablauf bei der App Service-Erstellung erstellen. Es ist eine gute Idee, das zu verwendende Subnetz gleichzeitig mit der App Service-Umgebung zu erstellen. Wenn Sie das Subnetz bei der Erstellung der App Service-Umgebung erstellen, befindet sich die App Service-Umgebung zwingend in derselben Ressourcengruppe wie das VNET. Zurzeit werden nur klassische V1-VNETs unterstützt.

Die Benutzeroberfläche zum Verwalten und Überwachen der App Service-Umgebung finden Sie im Azure-Portal. Wenn für Sie eine App Service-Umgebung vorhanden ist, sehen Sie normalerweise in der Randleiste das App Service-Symbol. Im Azure-Portal werden App Service-Umgebungen mit folgendem Symbol dargestellt:

![][1]

Sie können das Symbol verwenden oder unten in der Randleiste auf das Chevron (Größer-als-Zeichen) klicken und „App Service-Umgebungen“ wählen. Mit beiden Vorgehensweisen erreichen Sie dasselbe: Sie öffnen eine Benutzeroberfläche, die all Ihre App Service-Umgebungen auflistet. Wenn Sie auf eine der aufgelisteten App Service-Umgebungen klicken, wird eine Benutzeroberfläche zum Überwachen und Verwalten der jeweiligen Umgebung angezeigt.

![][2]

Das erste Blatt zeigt einige Eigenschaften der App Service-Umgebung zusammen mit einem Metrikdiagramm pro Ressourcenpool. Einige Eigenschaften im Block „Zusammenfassung“ sind auch Links, mit denen Sie das entsprechende Blatt öffnen können. Sie können z. B. auf den VNET-Namen klicken und dadurch die Benutzeroberfläche für das VNET öffnen, in dem Ihre App Service-Umgebung ausgeführt wird. Mit „App Service-Pläne“ und „Apps“ öffnen Sie jeweils Blätter mit Auflistungen dieser in Ihrer App Service-Umgebung enthaltenen Elemente.

## Überwachung ##

Mit den Diagrammen können Sie eine Reihe von Leistungsmetriken im jeweiligen Ressourcenpool verfolgen. Für den Front-End-Pool ist es sehr sinnvoll, die durchschnittliche CPU- und Speicherauslastung zu überwachen. Bei den Front-Ends wird die Last verteilt. Wenn Sie hier den Durchschnitt betrachten, erhalten Sie einen guten Überblick über die allgemeine Leistung. Bei den Workerpools ist das jedoch anders. Die Worker in einem Workerpool können von mehreren App Service-Plänen genutzt werden. In diesem Fall sind CPU- und Speicherauslastung keine besonders aussagekräftigen Informationen. Es ist wichtiger zu verfolgen, wie viele Worker verwendet wurden und wie viele verfügbar sind. Dies gilt besonders, wenn Sie das System für die Nutzung durch andere verwalten.

Alle Metriken, die sich in den Diagrammen verfolgen lassen, können auch zur Einrichtung von Warnungen verwendet werden. Beim Einrichten von Warnungen gehen Sie genauso vor wie sonst auch im App Service. Sie können eine Warnung einrichten, indem Sie den für Warnungen vorgesehenen Teil der Benutzeroberfläche verwenden oder indem Sie auf einer beliebigen Benutzeroberfläche für Metriken die Details öffnen und auf „Warnung hinzufügen“ klicken.
 
![][3]

Bei den gerade diskutierten Metriken handelt es sich um Metriken für App Service-Umgebungen. Darüber hinaus stehen auch auf der Ebene der App Service-Pläne Metriken zur Verfügung. Dort ist das Überwachen von CPU- und Speicherauslastung besonders sinnvoll. In einer App Service-Umgebung sind alle App Service-Pläne dedizierte App Service-Pläne. Das heißt: Nur die Apps, die auf den zugeordneten Hosts für den jeweiligen App Service-Plan ausgeführt werden, sind Apps in diesem App Service-Plan. Um die Details zu Ihrem App Service-Plan anzuzeigen, rufen Sie den App Service-Plan aus einer beliebigen Liste in der Benutzeroberfläche der App Service-Umgebung heraus auf, oder Sie nutzen die Funktion zum Durchsuchen von App Service-Plänen, die alle Pläne auflistet.

Unter Umständen kennen Sie bereits die Funktionen zur automatischen Skalierung, die für App Service-Pläne außerhalb einer App Service-Umgebung bereitstehen, und wissen, wie diese die für eine Ressource verfügbaren Metriken nutzen. Dieselben Prinzipien gelten auch für die automatische Skalierung einer App Service-Umgebung. Sie können nicht nur weiterhin die App Service-Pläne anhand von Metriken in einer App Service-Umgebung automatisch skalieren, sondern Sie können außerdem auch Regeln zur automatischen Skalierung für die App Service-Umgebung selbst festlegen. Einzelheiten zur Einrichtung der automatischen Skalierung finden Sie in einer ausführlichen Anleitung unter [Automatische Skalierung in App Service-Umgebungen][ASEAutoscale].


## Eigenschaften ##

Das Blatt „Einstellungen“ wird automatisch angezeigt, wenn Sie das Blatt „App Service-Umgebung“ öffnen. Im oberen Bereich finden Sie „Eigenschaften“. Einige Elemente sind identisch mit dem, was Sie unter „Zusammenfassung“ sehen. Sehr nützlich sind jedoch die VIP-Adresse und die ausgehende IP-Adresse. Zurzeit stimmen die beiden überein, aber in Zukunft ist es wahrscheinlich möglich, dass sich die ausgehende IP-Adresse von der VIP-Adresse unterscheidet. Deshalb werden beide hier aufgeführt. Wenn Sie also nicht berücksichtigen, dass Sie ggf. SSL einrichten und eine IP-Adresse nur für eine einzige App in der App Service-Umgebung hinzufügen, ist die IP-Adresse, die im DNS für die in der App Service-Umgebung erstellten Apps festgelegt wird, die VIP-Adresse, die Sie hier unter „Eigenschaften“ sehen.

![][4]

## IP-Adressen ##

Hier können Sie der App Service-Umgebung weitere IP-Adressen für Ihre Apps hinzufügen. Wenn Sie in der App Service-Umgebung eine App erstellen und dafür IP-SSL einrichten möchten, benötigen Sie eine nur für diese App reservierte IP-Adresse. Damit das möglich wird, muss die App Service-Umgebung über eigene IP-Adressen verfügen, die zugewiesen werden können. Beim Erstellen einer App Service-Umgebung wird eine einzige Adresse für diesen Zweck vorgesehen. Wenn Sie weitere Adressen benötigen, fügen Sie an dieser Stelle weitere hinzu. Bevor Sie nun aber vorsorglich die maximal mögliche Anzahl festlegen (denn Sie könnten ja irgendwann einmal mehr benötigen), müssen Sie wissen, dass für zusätzliche IP-Adressen Kosten anfallen. Details zu den zusätzlichen Kosten finden Sie auf unserer Seite zur Preisgestaltung unter [App Service-Preise][AppServicePricing]. Scrollen Sie dort nach unten bis zum Abschnitt zu SSL-Verbindungen. Der IP-SSL-Preis ist der zusätzliche Preis.

**HINWEIS:** Das Hinzufügen weiterer IP-Adressen ist ein Skalierungsvorgang. Zu einem gegebenen Zeitpunkt kann immer nur ein Skalierungsvorgang ausgeführt werden. Es gibt drei Skalierungsvorgänge:

- Ändern der Anzahl von IP-Adressen in der App Service-Umgebung, die für die IP-SSL-Nutzung zur Verfügung stehen
- Ändern der Größe der Computeressource, die in einem Ressourcenpool verwendet wird
- Ändern der Anzahl von Computeressourcen in einem Ressourcenpool, entweder manuell oder durch automatische Skalierung

## Ressourcenpools ##

Aus „Einstellungen“ können Sie zur Benutzeroberfläche für den Front-End-Pool oder für den Workerpool wechseln. Auf jedem der Ressourcenpoolblätter werden nur die Informationen für den jeweiligen Ressourcenpool angezeigt. Außerdem enthalten die Blätter Steuerelemente, mit denen dieser Ressourcenpool skaliert werden kann.

Das Grundlagenblatt für jeden Ressourcenpool enthält ein Diagramm mit den Metriken zu diesem Ressourcenpool. Ähnlich wie bei den Diagrammen im App Service-Umgebungsblatt können Sie auch hier das Diagramm öffnen und wie gewünscht Warnungen einrichten. Ob Sie eine Warnung im App Service-Umgebungsblatt für einen bestimmten Ressourcenpool einrichten oder dafür das Blatt für den Ressourcenpool verwenden, spielt keine Rolle – das Ergebnis ist dasselbe. Im Blatt „Einstellungen“ für den Workerpool können Sie alle Apps oder App Service-Pläne auflisten, die in diesem Workerpool ausgeführt werden.

![][5]

### Skalieren der Menge von Computeressourcen ###

Einen guten Überblick über das Skalieren von Apps in einer App Service-Umgebung erhalten Sie in dieser Anleitung: [Skalieren von Apps in einer App Service-Umgebung](app-service-web-scale-a-web-app-in-an-app-service-environment.md). Wenn Sie mehr über das Konfigurieren der automatischen Skalierung für die Ressourcenpools in App Service-Umgebungen erfahren möchten, lesen Sie diesen Artikel: [Automatische Skalierung in App Service-Umgebungen][ASEAutoscale]. Diese Beschreibung befasst sich mit manuellen Skalierungsvorgängen für die Ressourcenpools.

Die Ressourcenpools, Front-Ends und Worker sind für die Mandanten nicht direkt zugänglich. Das heißt, Sie können nicht per RDP darauf zugreifen, ihre Bereitstellung ändern oder als Administrator für sie tätig werden. Sie werden von Azure betrieben und verwaltet. Allerdings können die Menge und die Größe der Computeressourcen vom Benutzer festgelegt werden.

Es gibt drei Möglichkeiten, mit denen Sie beeinflussen können, wie viele Server in den Ressourcenpools verwendet werden: mit dem Skalierungsvorgang oben auf dem Hauptblatt für die App Service-Umgebung, mit manuellen Skalierungsvorgängen auf dem Blatt „Skalieren“ für den jeweiligen Ressourcenpool (zu finden unter „Einstellungen“) sowie mit der automatischen Skalierung, die Sie auf dem Blatt „Skalieren“ für den jeweiligen Ressourcenpool einrichten.

Um den Skalierungsvorgang auf dem Blatt „App Service-Umgebung“ zu verwenden, klicken Sie auf „Skalieren“, ziehen den betreffenden Schieberegler auf die gewünschte Menge und speichern die neue Einstellung. Mit dieser Benutzeroberfläche können Sie auch die Größe ändern.

![][6]

Um die manuellen oder automatischen Skalierungsfunktionen in einem spezifischen Ressourcenpool zu verwenden, wechseln Sie zunächst im Blatt „App Service-Umgebung“ zu „Einstellungen“. In „Einstellungen“ öffnen Sie den Front-End-Pool oder Workerpools wie gewünscht und öffnen dann den zu ändernden Pool. Unter „Einstellungen“ befindet sich ein Eintrag „Skalieren“ mit Chevron. Auf dem dadurch geöffneten Blatt können Sie manuelle oder automatische Skalierungen vornehmen.

![][7]

Eine App Service-Umgebung kann für die Verwendung von bis zu 55 Compute-Ressourcen insgesamt konfiguriert werden. Von diesen 55 Compute-Ressourcen können nur 50 zum Hosten von Workloads eingesetzt werden. Dafür gibt es zwei Gründe. Es sind mindestens zwei Front-End-Compute-Ressourcen erforderlich. So bleiben bis zu 53 zur Unterstützung der Workerpoolzuweisung übrig. Um Fehlertoleranz bereitzustellen, muss nach den folgenden Regeln eine zusätzliche Computeressource zugewiesen werden:

- Jeder Workerpool benötigt mindestens eine weitere Computeressource, der keine Workload zugewiesen werden kann.
- Wenn die Menge von Computeressourcen in einem Workerpool einen bestimmten Wert überschreitet, ist aus Gründen der Fehlertoleranz eine weitere Computeressource erforderlich. Im Front-End-Pool ist dies nicht der Fall.

Im Rahmen der Fehlertoleranzanforderungen gilt innerhalb eines einzelnen Workerpools Folgendes für einen bestimmten Wert von X Ressourcen, die einem Workerpool zugewiesen sind:

- Wenn X zwischen 2 und 20 liegt, beträgt die Anzahl der Compute-Ressourcen, die für Workloads verwendet werden können, X-1
- Wenn X zwischen 21 und 40 liegt, beträgt die Anzahl der Compute-Ressourcen, die für Workloads verwendet werden können, X-2
- Wenn X zwischen 41 und 53 liegt, beträgt die Anzahl der Compute-Ressourcen, die für Workloads verwendet werden können, X-3

Bedenken Sie, dass mindestens zwei Front-End-Server und zwei Worker erforderlich sind. Die vorherigen Angaben lassen sich durch ein paar Beispiele gut veranschaulichen.

- Wenn sich 30 Worker in einem einzigen Pool befinden, können 28 für Workloads verwendet werden. 
- Wenn sich zwei Worker in einem einzigen Pool befinden, kann einer für Workloads verwendet werden.
- Wenn sich 20 Worker in einem einzigen Pool befinden, können 19 für Workloads verwendet werden.  
- Wenn sich 21 Worker in einem einzigen Pool befinden, können immer noch nur 19 für Workloads verwendet werden.  

Der Aspekt der Fehlertoleranz ist wichtig, und Sie müssen ihn im Auge behalten, wenn Sie bei der Skalierung bestimmte Schwellenwerte überschreiten. Wenn Sie ausgehend von 20 Instanzen mehr Kapazität hinzufügen möchten, sollten Sie 22 oder mehr Instanzen wählen, weil bei 21 die Kapazität nicht steigt. Dasselbe trifft zu, wenn Sie 40 Instanzen überschreiten. Die nächste Anzahl, bei der die Kapazität steigt, ist 42.

### Skalieren der Größe von Computeressourcen ###

Sie können nicht nur die Menge der Compute-Ressourcen verwalten, die einem bestimmten Pool zugewiesen werden können, Sie besitzen außerdem auch die Kontrolle über die Größe. In App Service-Umgebungen können Sie aus vier unterschiedlichen Größen mit den Namen P1 bis P4 auswählen. Einzelheiten zu diesen Größen und ihren Preisen finden Sie unter [App Service-Preise](../app-service/app-service-value-prop-what-is.md). Die Größen P1 bis P3 der Compute-Ressourcen entsprechen denjenigen, die üblicherweise zur Verfügung stehen. Die P4-Compute-Ressource bietet 8 Kerne mit 14 GB Arbeitsspeicher und steht nur in einer App Service-Umgebung zur Verfügung.

Wenn Sie die Größe der in Ihren Pools verwendeten Computeressourcen ändern möchten, gibt es dafür zwei Wege. Auf dem Blatt „App Service-Umgebung“ sowie auf dem Blatt „Tarife“, das Sie über „Einstellungen“ im jeweiligen Ressourcenpool aufrufen können, gibt es einen Befehl zum Skalieren.

![][8]

Bevor Sie Änderungen vornehmen, sollten Sie jedoch unbedingt einige Dinge beachten:

- Änderungen können je nach Umfang unter Umständen mehrere Stunden dauern.
- Wenn bereits eine Konfigurationsänderung in der App Service-Umgebung verarbeitet wird, kann keine weitere Änderung gestartet werden.
- Wenn Sie die Größe der Computeressourcen in einem Workerpool ändern, verursacht dies möglicherweise Ausfälle für die Apps, die in diesem Workerpool ausgeführt werden.

Das Hinzufügen weiterer Instanzen zu einem Workerpool ist unkritisch und führt für die Ressourcen in diesem Pool nicht zu App-Ausfällen. Das Ändern der Größe der Compute-Ressource in einem Workerpool besitzt hingegen ganz andere Auswirkungen. Um Ausfallzeiten für Apps während einer Größenänderung an einem Workerpool zu vermeiden, empfiehlt sich Folgendes:

- Verwenden Sie einen ungenutzten Workerpool, um die erforderlichen Instanzen in der gewünschten Größe festzulegen.
- Skalieren Sie die App Service-Pläne für den neuen Workerpool.  
 
Dies führt bei ausgeführten Apps zu sehr viel weniger Unterbrechungen als das Ändern der Größe von Compute-Ressourcen während der Ausführung von Workloads. Details zum Skalieren von Apps in einer App Service-Umgebung finden Sie unter [Skalieren von Apps in einer App Service-Umgebung](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

## Virtual Network ##

Anders als beim gehosteten Dienst, der die App Service-Umgebung umfasst, werden das [virtuelle Netzwerk][virtualnetwork] und das Subnetz vom Benutzer gesteuert. App Service-Umgebungen weisen einige Netzwerkanforderungen auf, aber der Rest unterliegt der Steuerung durch den Benutzer. Die App Service-Umgebung stellt folgende Anforderungen:

- Ein klassisches „v1“-VNET muss vorhanden sein. 
- ein Subnetz mit mindestens 8 Adressen 
- Das VNET muss ein regionales VNET sein  
 
Die Verwaltung Ihres VNET erfolgt über die Virtual Network-Benutzeroberfläche oder über PowerShell.

Da diese Funktion den Azure App Service in Ihr VNET versetzt, können die in Ihrer App Service-Umgebung gehosteten Apps jetzt auf Ressourcen zugreifen, die über ExpressRoute oder Site-to-Site-VPNs zur Verfügung gestellt werden. Die Apps in Ihren App Service-Umgebungen benötigen keine zusätzlichen Netzwerkfunktionen für den Zugriff auf Ressourcen, die für das VNET verfügbar sind, das Ihre App Service-Umgebung hostet. Das bedeutet, dass Sie keine VNET-Integration oder Hybridverbindungen verwenden müssen, um Ressourcen in das VNET zu bringen oder mit dem VNET zu verbinden. Sie können jedoch nach wie vor beide Funktionen nutzen, um auf Ressourcen in Netzwerken zuzugreifen, die nicht mit dem VNET verbunden sind. Zum Beispiel können Sie mithilfe der VNET-Integration ein VNET integrieren, das Teil Ihres Abonnements, aber nicht mit dem VNET verbunden ist, in dem sich Ihre App Service-Umgebung befindet. Sie können auch weiterhin Hybridverbindungen verwenden, um ganz normal auf Ressourcen in anderen Netzwerken zuzugreifen.

Falls Ihr VNET mit einem ExpressRoute-VPN konfiguriert ist, müssen Sie einige der Routinganforderungen einer App Service-Umgebung kennen. Es gibt einige Konfigurationen für benutzerdefiniertes Routing (UDR, User Defined Routing), die nicht mit einer App Service-Umgebung kompatibel sind. Weitere Details zum Ausführen einer App Service-Umgebung in einem VNET mit ExpressRoute finden Sie im Dokument [Ausführen einer App Service-Umgebung in einem VNET mit ExpressRoute][ExpressRoute].

Außerdem können Sie den Zugriff auf Ihre Apps jetzt auch mithilfe von Netzwerksicherheitsgruppen steuern. Diese Funktion ermöglicht Ihnen das Sperren Ihrer App Service-Umgebung, sodass nur die von Ihnen angegebenen IP-Adressen darauf zugreifen können. Weitere Informationen hierzu finden Sie unter [Steuern des eingehenden Datenverkehrs in einer App Service-Umgebung](app-service-app-service-environment-control-inbound-traffic.md).

## Löschen einer App Service-Umgebung ##

Wenn Sie eine App Service-Umgebung löschen möchten, verwenden Sie einfach die Aktion "Löschen" im oberen Bereich des Blatts "App Service-Umgebung". Wenn Sie dies tun, werden Sie aufgefordert, den Namen Ihrer App Service-Umgebung einzugeben. Auf diese Weise bestätigen Sie, dass Sie diesen Schritt wirklich ausführen möchten. HINWEIS: Beim Löschen einer App Service-Umgebung wird auch deren gesamter Inhalt gelöscht.

![][9]

## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Erstellen einer App Service-Umgebung](app-service-web-how-to-create-an-app-service-environment.md)

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0107_2016-->