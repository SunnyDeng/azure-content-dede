<properties 
	pageTitle="Skalieren eine Web-App in einer App Service-Umgebung" 
	description="Skalierung einer Web-App in einer App Service-Umgebung" 
	services="app-services\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Skalieren von Web-Apps in einer App Service-Umgebung #

Allgemein betrachtet sind App Service-Umgebungen im Wesentlichen persönliche Bereitstellungen von Azure App Service im VNET und nur über Ihr Abonnement verwaltbar. Sie bieten neue Netzwerkfunktionen, da sie in das VNET integriert sind und in höherem Maß skalierbar sind, als dies normalerweise in Azure App Service-Umgebungen möglich ist. Weitere Informationen rund um das Thema App Service-Umgebung finden Sie unter [Was ist eine App Service-Umgebung?][WhatisASE]. Weitere Informationen zum Erstellen einer App Service-Umgebung oder zum Erstellen einer Web-App in einer App Service-Umgebung finden Sie unter [Erstellen einer App Service-Umgebung][HowtoCreateASE] und [Erstellen einer Web-App in einer App Service-Umgebung][CreateWebappinASE].

Denken Sie daran: Wenn Sie normalerweise ein Skalierungsattribut für eine Web-App ändern, wird diese Änderung auf Ebene des App Service-Plans durchgeführt. Nähere Informationen zum Skalieren von App Service-Plänen oder zu App Service-Plänen außerhalb von App Service-Umgebungen finden Sie unter [Skalieren eine Web-App in Azure App Service][ScaleWebapp] und [App Service-Pläne: ausführliche Übersicht][Appserviceplans].

Die Skalierung einer Web-App in einer App Service-Umgebung ähnelt sehr stark dem normalen Skalieren von Web-Apps. In Azure App Service können üblicherweise drei Faktoren skaliert werden:

- Tarif
- Workergröße (für dedizierte Instanzen)
- Anzahl von Instanzen

In einer App Service-Umgebung ist es nicht erforderlich, den Tarif auszuwählen oder zu ändern. Diese wird im Hinblick auf die Funktionalität bereits im Premium-Plan ausgeführt. In einer App Service-Umgebung gibt es keine freigegebenen Worker. Alle Worker sind dedizierte Worker. Anstelle von festen Größen kann der Administrator der App Service-Umgebung die Größe der Compute-Ressource zuweisen, die für jeden Workerpool verwendet wird. Dies bedeutet, dass Sie P4-Compute-Ressourcen für Workerpool 1 und P1-Compute-Ressourcen für Workerpool 2 zuweisen können, wenn dies gewünscht ist. Es ist keine Reihenfolge nach Größe erforderlich. Ausführliche Informationen zu Größen und Preisen finden Sie im Dokument [Azure App Service-Preise][AppServicePricing]. Damit stehen für die Skalierung von Web-Apps und App Service-Plänen in einer App Service-Umgebung die folgenden Optionen zur Verfügung:

- Auswahl der Workerpools
- Anzahl von Instanzen

Ändern Sie diese Elemente über die geeignete Benutzeroberfläche in Ihrem App Service-Plan.

![][1]

### Skalieren der Anzahl von Instanzen ###

Wenn Sie Ihre Web-App in einer App Service-Umgebung erstellen, sollten Sie sie auf mindestens 2 Instanzen skalieren, um Fehlertoleranz bereitzustellen.

Wenn Ihre App Service-Umgebung über ausreichend Kapazität verfügt, ist dies ganz einfach. Wechseln Sie zum App Service-Plan, der die zu skalierenden Websites umfasst, und wählen Sie die Option zum Skalieren. Daraufhin wird die Benutzeroberfläche geöffnet. Legen Sie ganz einfach mithilfe des Schiebereglers die gewünschte Anzahl von Instanzen fest, und speichern Sie Ihre Einstellungen.

![][2]

Beachten Sie, dass der App Service-Plan nicht über die Anzahl der verfügbaren Compute-Ressourcen hinaus skaliert werden kann, die im Workerpool für Ihren App Service-Plan enthalten sind. Wenn Sie mehr Kapazität benötigen, müssen Sie den Administrator Ihrer App Service-Umgebung bitten, weitere Compute-Ressourcen zum betreffenden Workerpool hinzuzufügen. Weitere Informationen rund um die Neukonfiguration der App Service-Umgebung finden Sie unter [Konfigurieren einer App Service-Umgebung][HowtoConfigureASE].
 

### Auswahl des Workerpools ###

Auf die Auswahl des Workerpools greifen Sie über die Benutzeroberfläche des App Service-Plans zu. Öffnen Sie den App Service-Plan, den Sie skalieren und für den Sie einen Workerpool auswählen möchten. Es werden alle Workerpools angezeigt, die Sie in der App Service-Umgebung konfiguriert haben. Wenn Sie nur über einen Workerpool verfügen, wird nur dieser Pool aufgeführt. Um den Workerpool für Ihren App Service-Plan zu ändern, wählen Sie einfach den Workerpool aus, in den Sie Ihren App Service-Plan verschieben möchten.

![][3]

Vor der Ausführung dieses Schritts müssen Sie sicherstellen, dass eine angemessene Kapazität für Ihren App Service-Plan verfügbar ist. In der Liste der Workerpools wird nicht nur der Name des Workerpools aufgeführt, sondern Sie können auch sehen, wie viele Worker im Workerpool zur Verfügung stehen. Stellen Sie sicher, dass eine ausreichende Anzahl von Instanzen für Ihren App Service-Plan vorhanden ist. Wenn Sie mehr Compute-Ressourcen im Workerpool benötigen, in den Sie Ihren App Service-Plan verschieben möchten, bitten Sie den Administrator Ihrer App Service-Umgebung, weitere Ressourcen hinzuzufügen.

Das Verschieben einer Web-App aus einem Workerpool verursacht einen Neustart Ihrer Web-Apps. Dies kann eine gewisse Ausfallzeit für Ihre App bedeuten, je nach benötigter Zeit für den Neustart.

## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Erstellen einer App Service-Umgebung][HowtoCreateASE]

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleasp.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleinstances.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scalepool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-app-service-environment/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
<!--HONumber=52-->
