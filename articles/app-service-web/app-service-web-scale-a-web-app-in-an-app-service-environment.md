<properties 
	pageTitle="Gewusst wie: Skalieren von Apps in einer App Service-Umgebung" 
	description="Skalierung einer Web-App in einer App Service-Umgebung" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/14/2016" 
	ms.author="ccompy"/>

# Skalieren von Apps in einer App Service-Umgebung #

Allgemein betrachtet sind App Service-Umgebungen im Wesentlichen persönliche Bereitstellungen von Azure App Service im VNET und nur über Ihr Abonnement verwaltbar. Sie bieten neue Netzwerkfunktionen, da sie in das VNET integriert sind und in höherem Maß skalierbar sind, als dies normalerweise in Azure App Service-Umgebungen möglich ist. Weitere Informationen rund um das Thema App Service-Umgebung finden Sie unter [Was ist eine App Service-Umgebung?][WhatisASE]. Weitere Informationen zum Erstellen einer App Service-Umgebung oder zum Erstellen einer Web-App in einer App Service-Umgebung finden Sie unter [Erstellen einer App Service-Umgebung][HowtoCreateASE] und [Erstellen einer Web-App in einer App Service-Umgebung][CreateWebappinASE].

Denken Sie daran: Wenn Sie normalerweise ein Skalierungsattribut für eine Web-App, eine mobile App oder eine API-App in Azure App Service ändern, wird diese Änderung auf Ebene des App Service-Plans (ASP) durchgeführt. Nähere Informationen zum Skalieren von App Service-Plänen oder zu App Service-Plänen außerhalb von App Service-Umgebungen finden Sie unter [Skalieren eine Web-App in Azure App Service][ScaleWebapp] und [App Service-Pläne: ausführliche Übersicht][Appserviceplans].

Die Skalierung einer App in einer App Service-Umgebung ähnelt sehr stark dem normalen Skalieren von Apps. In Azure App Service können üblicherweise drei Faktoren skaliert werden:

- Tarif
- Workergröße (für dedizierte Instanzen)
- Anzahl von Instanzen

In einer App Service-Umgebung ist es nicht erforderlich, den Tarif auszuwählen oder zu ändern. Diese wird im Hinblick auf die Funktionalität bereits im Premium-Plan ausgeführt. In einer App Service-Umgebung gibt es keine freigegebenen Worker. Alle Worker sind dedizierte Worker.

Im Hinblick auf Workergrößen kann der Administrator der App Service-Umgebung die Größe der Computeressource zuweisen, die für jeden Workerpool verwendet wird. Dies bedeutet, dass Sie P4-Compute-Ressourcen für Workerpool 1 und P1-Compute-Ressourcen für Workerpool 2 zuweisen können, wenn dies gewünscht ist. Es ist keine Reihenfolge nach Größe erforderlich. Ausführliche Informationen zu Größen und Preisen finden Sie im Dokument [Azure App Service-Preise][AppServicePricing]. Damit stehen für die Skalierung von Web-Apps und App Service-Plänen in einer App Service-Umgebung die folgenden Optionen zur Verfügung:

- Auswahl der Workerpools
- Anzahl von Instanzen

Das Ändern dieser Elemente erfolgt über die entsprechende Benutzeroberfläche in Ihrem in der App Service-Umgebung gehosteten App Service-Plan. Beachten Sie, dass der ASP nicht über die Anzahl der verfügbaren Computeressourcen hinaus zentral hochskaliert werden kann, die im Workerpool für Ihren ASP enthalten sind. Wenn Sie mehr Kapazität benötigen, müssen Sie den Administrator Ihrer App Service-Umgebung bitten, weitere Compute-Ressourcen zum betreffenden Workerpool hinzuzufügen. Weitere Informationen rund um die Neukonfiguration der App Service-Umgebung finden Sie unter [Konfigurieren einer App Service-Umgebung][HowtoConfigureASE]. Sie sollten auch die Features für das automatische Skalieren der App Service-Umgebung nutzen, um nach Zeitplan oder Metriken Kapazitäten hinzuzufügen. Weitere Informationen zum Konfigurieren der automatischen Skalierung für die App Service-Umgebung selbst finden Sie unter [Automatische Skalierung und App Service-Umgebungen][ASEAutoscale].

![][1]

### Skalieren der Anzahl von Instanzen ###

Wenn Sie Ihre Web-App in einer App Service-Umgebung erstellen, sollten Sie sie auf mindestens 2 Instanzen skalieren, um Fehlertoleranz bereitzustellen.

Wenn Ihre App Service-Umgebung über ausreichend Kapazität verfügt, ist dies ganz einfach. Wechseln Sie zum App Service-Plan, der die zu skalierenden Websites umfasst, und wählen Sie die Option zum Skalieren. Daraufhin wird die Benutzeroberfläche geöffnet, auf der Sie manuell die Skalierung für Ihren ASP festlegen oder Regeln für das automatische Skalieren Ihres ASP konfigurieren können. Zum manuellen Skalieren der App legen Sie einfach ***Skalieren nach*** auf ***Eine Anzahl von Instanzen, die ich manuell festlege*** fest. Von hier aus können Sie entweder den Schieberegler auf die gewünschte Menge einstellen oder diese in das Feld neben dem Schieberegler eingeben.

![][2]

Die Regeln für das automatische Skalieren eines ASPs in einer App Service-Umgebung funktionieren wie gewohnt. Sie können ***CPU-Prozentsatz*** unter ***Skalieren nach*** auswählen und Regeln für das automatische Skalieren des ASPs basierend auf einem CPU-Prozentsatz erstellen, oder Sie können komplexere Regeln erstellen, die ***Zeitpläne und Leistungsregeln*** verwenden. Ausführlichere Informationen zur Konfiguration der automatischen Skalierung finden Sie in der Anleitung unter [Skalieren einer Web-App in Azure App Service][AppScale].


### Auswahl des Workerpools ###

Wie bereits erwähnt erfolgt die Auswahl des Workerpools über die Benutzeroberfläche des App Service-Plans. Öffnen Sie das Blatt für den App Service-Plan, den Sie skalieren und für den Sie einen Workerpool auswählen möchten. Es werden alle Workerpools angezeigt, die Sie in der App Service-Umgebung konfiguriert haben. Wenn Sie nur über einen Workerpool verfügen, wird nur dieser Pool aufgeführt. Um den Workerpool für Ihren ASP zu ändern, wählen Sie einfach den Workerpool aus, in den Sie Ihren App Service-Plan verschieben möchten.

![][3]

Vor dem Verschieben des ASPs aus einem Workerpool in einen anderen sollten Sie sicherstellen, dass Sie über eine ausreichende Kapazität für den ASP verfügen. In der Liste der Workerpools wird nicht nur der Name des Workerpools aufgeführt, sondern Sie können auch sehen, wie viele Worker im Workerpool zur Verfügung stehen. Stellen Sie sicher, dass eine ausreichende Anzahl von Instanzen für Ihren App Service-Plan vorhanden ist. Wenn Sie mehr Compute-Ressourcen im Workerpool benötigen, in den Sie Ihren App Service-Plan verschieben möchten, bitten Sie den Administrator Ihrer App Service-Umgebung, weitere Ressourcen hinzuzufügen.

> [AZURE.NOTE] Das Verschieben eines ASPs aus einem Workerpool verursacht einen Neustart der Apps in diesem ASP. Dies kann eine gewisse Ausfallzeit für Ihre App bedeuten, je nach benötigter Zeit für den Neustart der Apps.

## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Erstellen einer App Service-Umgebung][HowtoCreateASE]

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/

<!---HONumber=AcomDC_0128_2016-->