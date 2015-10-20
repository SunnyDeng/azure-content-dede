<properties
	pageTitle="Erstellen einer Web-App in einer App Service-Umgebung"
	description="Vorgehensweise zum Erstellen von Web-Apps und App Service-Plänen in einer App Service-Umgebung"
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
	ms.topic="get-started-article" 
	ms.date="09/15/2015"
	ms.author="ccompy"/>

# Erstellen einer Web-App in einer App Service-Umgebung #

Das Erstellen von Web-Apps ist in einer App Service-Umgebung nahezu identisch mit den Schritten in einer regulären Umgebung. Wenn Sie mit der Funktion der App Service-Umgebung nicht vertraut sind, lesen Sie das Dokument [Was ist eine App Service-Umgebung](app-service-app-service-environment-intro.md).

Um eine Web-App in einer App Service-Umgebung erstellen zu können, müssen Sie zunächst über eine App Service-Umgebung verfügen. Einzelheiten zum Erstellen einer App Service-Umgebung finden Sie im Dokument [Erstellen einer App Service-Umgebung](app-service-web-how-to-create-an-app-service-environment.md).

Der erste Schritt beim Erstellen einer Web-App ist die Auswahl Ihres Abonnements. Achten Sie bei mehreren Abonnements darauf, dass Sie zum Erstellen einer App in Ihrer App Service-Umgebung dasselbe Abonnement verwenden müssen, das Sie beim Erstellen der App Service-Umgebung verwendet haben. Im nächsten Schritt wird eine Ressourcengruppe ausgewählt oder erstellt. Wenn Sie mit Ressourcengruppen nicht vertraut sind, finden Sie hier weitere Informationen: [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen][ResourceGroups]. Ressourcengruppen helfen nicht nur bei der Verwaltung Ihrer Ressourcen, sie sind auch für das Einrichten von RBAC-Regeln für Ihre Apps wichtig.

Nachdem Sie Ihr Abonnement und die Ressourcengruppe ausgewählt haben, müssen Sie einen App Service-Plan (ASP) erstellen oder auswählen. Wenn Sie einen neuen ASP in Ihrer App Service-Umgebung erstellen müssen, müssen Sie einen Namen für den ASP angeben, die gewünschte App Service-Umgebung unter Speicherorte auswählen und den Workerpool auswählen, in dem sich der ASP befinden soll. Dies wird weiter unten ausführlicher beschrieben. Wenn Sie einen ASP in einer App Service-Umgebung auswählen, entspricht der App-Erstellungsfluss der herkömmlichen Erstellung einer App. Führen Sie dazu den Web-App-Erstellungsfluss aus, indem Sie zunächst "Neu" > "Web + Mobile" > "Web-App" auswählen.

![][1]


Bei Verwendung eines App Service-Plans, den Sie bereits in Ihrer App Service-Umgebung erstellt haben, wählen Sie diesen Plan aus, geben Sie den Namen für Ihre Web-App ein und wählen "Erstellen". Diese Vorgehensweise entspricht den üblichen Schritten zum Erstellen einer Web-App. Sie können die ASPs in Ihre App Service-Umgebung anhand des Standorts identifizieren, der unter dem ASP-Namen angegeben ist.

![][5]

Eine neue App ist hier erreichbar:

[*Sitename*].[*Name Ihrer App Service-Umgebung*].p.azurewebsites.net

anstelle von

[*Sitename*].azurewebsites.net

Vorerst muss der Name Ihrer Web-App innerhalb des gesamten Azure App Service eindeutig sein. Wenn Sie eine Web-App mit dem Namen "diesistmeinewebapp" erstellen möchten, darf gegenwärtig also keine andere Web-App mit dem Namen "diesistmeinewebapp" in Azure App Service vorhanden sein.

### App Service-Pläne ###

Bei App-Service-Plänen handelt es sich um einen verwalteten Satz an Web-Apps. Bei Auswahl der Preise gelten diese nicht für die einzelnen Apps, sondern für den App Service-Plan. Um die Anzahl von Instanzen einer Web-App zu erhöhen, erhöhen Sie die Anzahl von Instanzen Ihres App Service-Plans. Dies wirkt sich auf sämtliche Web-Apps innerhalb des betroffenen Plans aus. Für einige Funktionen wie Websiteslots oder die VNET-Integration gelten innerhalb des Plans ebenfalls Mengenbeschränkungen. Weitere Informationen zu App Service-Plänen finden Sie im Dokument [Azure App Service-Pläne - Detaillierte Übersicht](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

Der Vorgang zum Erstellen eines neuen App Service-Plans unterscheidet sich in einigen Punkten von den Schritten zum Erstellen eines App Service-Plans in einer App Service-Umgebung. Beispielsweise stehen andere Worker zur Auswahl, da es in einer App Service-Umgebung keine gemeinsamen Worker gibt. Es können ausschließlich die Worker verwendet werden, die der App Service-Umgebung vom Administrator zugewiesen wurden. Um einen neuen App Service-Plan (ASP) erstellen zu können, müssen dem Workerpool Ihrer App Service-Umgebung mehr Worker zugewiesen sein als die Gesamtzahl der Instanzen all Ihrer App Service-Pläne innerhalb dieses Workerpools. Wenn im Workerpool Ihrer App Service-Umgebung nicht genügend Worker vorhanden sind, um einen App Service-Plan zu erstellen, müssen Sie zusammen mit dem Administrator Ihrer App Service-Umgebung weitere Worker hinzufügen.

Ein weiterer Unterschied bei App Service-Plänen, die in einer App Service-Umgebung gehostet werden, besteht darin, dass keine Preise ausgewählt werden können. Mit einer App Service-Umgebung zahlen Sie für die vom System verwendeten Compute-Ressourcen, und es fallen keine zusätzlichen Kosten für die App Service-Pläne innerhalb der Umgebung an. Beim Erstellen eines App Service-Plans wählen Sie üblicherweise einen Tarif, auf dessen Basis die Abrechnung erfolgt. Bei einer App Service-Umgebung handelt es sich im Wesentlichen um einen privaten Speicherort, an dem Sie Inhalte erstellen können. Sie zahlen nicht für das Hosten Ihrer Inhalte, sondern für die Umgebung.

### Erstellen eines ASP für Ihre App Service-Umgebung ###

Zum Erstellen eines ASP in einer App Service-Umgebung wählen Sie zunächst "Neu erstellen" in der ASP-Auswahl-Benutzeroberfläche aus und geben Sie einen Namen für Ihren ASP an, so wie Sie es von außerhalb einer App Service-Umgebung gewohnt sind. Im nächsten Schritt wählen Sie die gewünschte App Service-Umgebung aus der Standortauswahl aus. Da eine App Service-Umgebung im Wesentlichen ein privater Bereitstellungsort ist, wird sie unter "Standort" angezeigt.

![][2]

Nach der Auswahl einer App Service-Umgebung in der Standortauswahl, wird die ASP-Erstellungs-Benutzeroberfläche aktualisiert. Der Standort zeigt nun den Namen des App Service-Umgebungssystems und die Region an, in der es sich befindet. Die Tarifplanauswahl wird durch eine Workerpoolauswahl ersetzt.

![][3]

### Auswahl des Workerpools ###

In Azure App Service und außerhalb einer App Service-Umgebung sind bei der Auswahl eines dedizierten Tarifplans üblicherweise 3 Servergrößen verfügbar. Auf ähnliche Weise können Kunden mit einer App Service-Umgebung bis zu 3 Workerpools und die Servergröße angeben, die für den jeweiligen Workerpool verwendet wird. Für Mandanten bedeutet dies, dass anstelle der Auswahl eines Tarifplans mit Servergröße für Ihren ASP, ein sogenannter Workerpool ausgewählt wird.

Auf der Benutzeroberfläche zur Auswahl des Workerpools wird unterhalb des Namens die Servergröße angezeigt, die für diesen Workerpool verwendet wird. Die verfügbare Größe entspricht der Anzahl von Serverinstanzen, die zur Verwendung in diesem Pools verfügbar sind. Möglicherweise verfügt der Pool insgesamt über mehr Instanzen als diese Anzahl, der hier angezeigte Wert bezieht sich jedoch lediglich auf die Anzahl von Instanzen, die gegenwärtig nicht verwendet werden. Informationen zum Hinzufügen von Compute-Ressourcen zu Ihrer App Service-Umgebung finden Sie im Dokument [Konfigurieren der App Service-Umgebung](app-service-web-configure-an-app-service-environment.md).

![][4]

In diesem Beispiel werden lediglich zwei verfügbare Workerpools angezeigt. Der Grund dafür ist, dass der Administrator der App Service-Umgebung nur diesen beiden Workerpools Hosts zugewiesen hat. Der dritte Pool würde angezeigt, wenn ihm virtuelle Computer zugewiesen würden.

### Nach der Erstellung der Web-App ###

Im Zusammenhang mit dem Ausführen von Web-Apps und Verwalten von App Service-Plänen in einer App Service-Umgebung müssen einige Überlegungen berücksichtigt werden.

Wie bereits erwähnt, ist der Besitzer der App Service-Umgebung für die Größe des Systems und damit ebenso dafür verantwortlich, dass eine ausreichend große Kapazität zum Hosten der gewünschten App Service-Pläne verfügbar ist. Wenn keine Worker verfügbar sind, kann der App Service-Plan nicht erstellt werden. Dies gilt ebenfalls für das Skalieren Ihrer Web-App. Wenn weitere Instanzen erforderlich sind, müssen Sie den Administrator der App Service-Umgebung bitten, weitere Worker hinzufügen.

Nach dem Erstellen der Web-App und des App Service-Plans, sollten Sie eine Skalierung durchführen. Um Fehlertoleranz für Ihre Apps zu gewährleisten, werden in einer App Service-Umgebung immer mindestens 2 Instanzen Ihres App Service-Plans benötigt. Die Vorgehensweise zum Skalieren eines App Service-Plans in einer App Service-Umgebung ist mit den üblichen Schritten über die Benutzeroberfläche des App Service-Plans identisch. Weitere Einzelheiten zum Skalieren finden Sie im Dokument [Skalieren einer Web-App in einer App Service-Umgebung](app-service-web-scale-a-web-app-in-an-app-service-environment.md).


[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/

<!---HONumber=Oct15_HO3-->