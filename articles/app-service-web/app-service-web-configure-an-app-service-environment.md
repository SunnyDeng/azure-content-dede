<properties 
	pageTitle="Konfigurieren einer App Service-Umgebung" 
	description="Konfiguration, Verwaltung und Überwachung von App Service-Umgebungen" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Konfigurieren einer App Service-Umgebung #

## Übersicht ##

App Service-Umgebungen sind eine neue Funktion des Premium-Tarifs, die in der Vorschau angeboten wird. Sie bietet neue Funktionen für die Skalierung und den Netzwerkzugriff. Diese neue Skalierungsfunktion erlaubt das Platzieren einer Instanz des Azure App Service in Ihrem VNET. Wenn Sie mit der Funktion der App Service-Umgebung nicht vertraut sind, lesen Sie das Dokument [Was ist eine App Service-Umgebung]/app-service-app-service-environment-intro.md). Informationen zum Erstellen einer App Service-Umgebung finden Sie im Dokument [Erstellen einer App Service-Umgebung](app-service-web-how-to-create-an-app-service-environment.md).

Im Allgemeinen besteht eine App Service-Umgebung aus mehreren Hauptkomponenten:

- Compute-Ressourcen, die im gehosteten Dienst der Azure App-Umgebung ausgeführt werden
- Speicher
- Datenbank
- Virtual Network mit mindestens einem Subnetz
- Subnetz mit darin ausgeführtem gehosteten Dienst der Azure App-Umgebung

Zum Verwalten und Überwachen Ihrer App Service-Umgebungen können Sie im Azure-Vorschauportal über "Durchsuchen" -> "App Service-Umgebungen" auf eine Benutzeroberfläche zugreifen. Die erste Version verfügt über die nötigen Funktionen zum Verwalten des Systems. Sie wird in den kommenden Wochen weiterhin durch zusätzliche Funktionen ergänzt.

![][1]

## Überwachung ##

In der ersten Vorschauversion sind nicht viele Metrikfunktionen verfügbar, sie werden jedoch in Kürze eingeführt. Anhand dieser Metrikfunktionen können Systemadministratoren leichter Entscheidungen im Hinblick auf die Skalierung und den Betrieb des Systems treffen.

Im Portal können Sie jetzt auch alle App Service-Pläne in der App Service-Umgebung sowie alle Web-Apps in der App Service-Umgebung auflisten. Zum Anzeigen der Listen rufen Sie "Einstellungen" auf, und wählen Sie das Element, an dem Sie interessiert sind.

![][3]

In beiden Listen sehen Sie die Workerpoolzuweisung mit der Anzahl der verwendeten Instanzen und der Größe der Compute-Ressource. Details zur Leistung innerhalb eines einzelnen App Service-Plans stehen wie gehabt zur Verfügung, also durch Öffnen der Benutzeroberfläche des App Service-Plans.

![][4]

## Compute-Ressourcen ##

Die Compute-Ressourcen, Storage und Datenbank werden alle durch den Azure App Service betrieben. Die Menge und die Größen der Compute-Ressourcen können jedoch vom Benutzer festgelegt werden.

Unabhängig von der Größe der Compute-Ressourcen sind mindestens zwei Front-End-Server und zwei Worker erforderlich. Eine App Service-Umgebung kann für die Verwendung von bis zu 55 Compute-Ressourcen insgesamt konfiguriert werden. Von diesen 55 Compute-Ressourcen können nur 50 zum Hosten von Workloads eingesetzt werden. Dafür gibt es zwei Gründe. Es sind mindestens zwei Front-End-Compute-Ressourcen erforderlich. So bleiben bis zu 53 zur Unterstützung der Workerpoolzuweisung übrig. Um jedoch Fehlertoleranz bereitzustellen, muss nach den folgenden Regeln eine zusätzliche Compute-Ressource zugewiesen werden:

- Jeder Workerpool benötigt mindestens eine weitere Compute-Ressource, der keine Workload zugewiesen werden kann
- Wenn die Menge von Compute-Ressourcen in einem Pool einen bestimmten Wert überschreitet, ist eine weitere Compute-Ressource erforderlich

Im Rahmen der Fehlertoleranzanforderungen gilt innerhalb eines einzelnen Workerpools Folgendes für einen bestimmten Wert von X Ressourcen, die einem Workerpool zugewiesen sind:

- Wenn X zwischen 2 und 20 liegt, beträgt die Anzahl der Compute-Ressourcen, die für Workloads verwendet werden können, X-1
- Wenn X zwischen 21 und 40 liegt, beträgt die Anzahl der Compute-Ressourcen, die für Workloads verwendet werden können, X-2
- Wenn X zwischen 41 und 53 liegt, beträgt die Anzahl der Compute-Ressourcen, die für Workloads verwendet werden können, X-3

Sie können nicht nur die Menge der Compute-Ressourcen verwalten, die einem bestimmten Pool zugewiesen werden können, Sie besitzen außerdem auch die Kontrolle über die Größe. In App Service-Umgebungen können Sie aus vier unterschiedlichen Größen mit den Namen P1 bis P4 auswählen. Einzelheiten zu diesen Größen und ihren Preisen finden Sie unter [App Service-Preise](../app-service/app-service-value-prop-what-is.md). Die Größen P1 bis P3 der Compute-Ressourcen entsprechen denjenigen, die üblicherweise zur Verfügung stehen. Die P4-Compute-Ressource bietet 8 Kerne mit 14 GB Arbeitsspeicher und steht nur in einer App Service-Umgebung zur Verfügung.

Wie bereits erwähnt, befindet sich die App Service-Umgebung derzeit in der Vorschau und kann daher noch ausgebaut werden. Zusätzlich zu weiteren Überwachungsfunktionen werden weitere Verwaltungsfunktionen eingeführt, sobald die App Service-Umgebung allgemein verfügbar wird. Derzeit können nur wenige Elemente über diese Schnittstelle verwaltet werden:

- Anzahl der Compute-Ressourcen in den einzelnen Pools
- Größe der Compute-Ressourcen in den einzelnen Pools
- Anzahl der verfügbaren IP-Adressen

Um diese Elemente zu steuern, wählen Sie oben im Bildschirm das Konfigurationselement "Skalieren".

![][2]

Hier können die Menge der Compute-Ressourcen in den einzelnen Pools und deren Größe angepasst werden. Bevor Sie Änderungen vornehmen, sollten Sie jedoch unbedingt einige Dinge beachten:

- Änderungen können je nach Umfang der angeforderten Änderung Stunden dauern.
- Wenn bereits eine Konfigurationsänderung in der App Service-Umgebung verarbeitet wird, kann keine weitere Änderung gestartet werden.
- Wenn Sie die Größe der Compute-Ressourcen in einem Workerpool ändern, verursacht dies möglicherweise Ausfälle für die Web-Apps, die in diesem Workerpool ausgeführt werden.

Das Hinzufügen weiterer Instanzen zu einem Workerpool ist unkritisch und führt nicht zur Beeinträchtigung des Systems. Das Ändern der Größe der Compute-Ressource in einem Workerpool besitzt hingegen ganz andere Auswirkungen. Um Ausfallzeiten für Apps während einer Größenänderung an einem Workerpool zu vermeiden, empfiehlt sich Folgendes:

- Verwenden Sie einen ungenutzten Workerpool, um die erforderlichen Instanzen in der gewünschten Größe festzulegen.
- Skalieren Sie die App Service-Pläne für den neuen Workerpool.  
 
Dies führt bei ausgeführten Apps zu sehr viel weniger Unterbrechungen als das Ändern der Größe von Compute-Ressourcen während der Ausführung von Workloads. Details zum Skalieren von Web-Apps in einer App Service-Umgebung finden Sie unter [Skalieren von Web-Apps in einer App Service-Umgebung](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

## Virtuelles Netzwerk ##

[Virtual Network][virtualnetwork] und Subnetz werden vollständig vom Benutzer gesteuert. App Service-Umgebungen besitzen einige Netzwerkanforderungen, aber der Rest unterliegt der Steuerung durch den Benutzer. Die App Service-Umgebung stellt folgende Anforderungen:

- Ein VNET mit mindestens 512 Adressen
- Ein Subnetz mit mindestens 256 Adressen 
- Das VNET muss ein regionales VNET sein  
 
Die Verwaltung Ihres VNET erfolgt über die normale Virtual Network-Benutzeroberfläche.

Da diese Funktion den Azure App Service in Ihr VNET versetzt, können die in Ihrer App Service-Umgebung gehosteten Apps jetzt auf Ressourcen zugreifen, die über ExpressRoute oder Site-to-Site-VPNs zur Verfügung gestellt werden. Die Apps in Ihren App Service-Umgebungen benötigen keine zusätzlichen Netzwerkfunktionen für den Zugriff auf Ressourcen, die für das VNET verfügbar sind, das Ihre App Service-Umgebung hostet.

Falls gewünscht, können Sie den Zugriff jetzt auch mithilfe von Netzwerksicherheitsgruppen steuern. Diese Funktion ermöglicht Ihnen das Sperren Ihrer App Service-Umgebung, sodass nur die von Ihnen angegebenen IP-Adressen darauf zugreifen können. Weitere Informationen hierzu finden Sie unter [Steuern des eingehenden Datenverkehrs in einer App Service-Umgebung](app-service-app-service-environment-control-inbound-traffic.md).

## Löschen einer App Service-Umgebung ##

Wenn Sie eine App Service-Umgebung löschen möchten, verwenden Sie einfach die Aktion "Löschen" im oberen Bereich des Blatts "App Service-Umgebung". Eine App Service-Umgebung kann jedoch nur gelöscht werden, wenn sie keine Inhalte enthält. Achten Sie darauf, dass Sie alle Web-Apps und App Service-Pläne entfernen, bevor Sie die App Service-Umgebung löschen.

## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Erstellen einer App Service-Umgebung](app-service-web-how-to-create-an-app-service-environment.md)

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/configureaseblade.png
[2]: ./media/app-service-web-configure-an-app-service-environment/configurescale.png
[3]: ./media/app-service-web-configure-an-app-service-environment/configureasplist.png
[4]: ./media/app-service-web-configure-an-app-service-environment/configurewebapplist.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=62-->