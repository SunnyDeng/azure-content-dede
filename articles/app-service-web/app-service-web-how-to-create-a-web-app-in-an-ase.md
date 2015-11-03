<properties
	pageTitle="Erstellen einer Web-App in einer App Service-Umgebung"
	description="Erfahren Sie, wie Web-Apps und App Service-Plänen in einer App Service-Umgebung (App Service Environment, ASE) erstellt werden."
	services="app-service"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="10/26/2015"
	ms.author="ccompy"/>

# Erstellen einer Web-App in einer App Service-Umgebung

## Übersicht

Dieses Tutorial zeigt die Erstellung von Web-Apps und App Service-Plänen in einer [App Service-Umgebung](app-service-app-service-environment-intro.md) (App Service Environment, ASE):

> [AZURE.NOTE]Wenn Sie wissen möchten, wie eine Web-App erstellt wird, dies jedoch nicht in einer App Service-Umgebung durchführen möchten, finden Sie weitere Informationen unter [Erstellen einer .NET-Web-App](web-sites-dotnet-get-started.md) oder in einem der zugehörigen Tutorials für andere Sprachen und Frameworks.

## Voraussetzungen

Bei diesem Tutorial wird vorausgesetzt, dass Sie eine App Service-Umgebung erstellt haben. Wenn das nicht der Fall ist, finden Sie weitere Informationen unter [Erstellen einer App Service-Umgebung](app-service-web-how-to-create-an-app-service-environment.md).

## Erstellen einer Web-App

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Neu > Web und mobil > Web-App**. 

	![][1]

2. Wählen Sie Ihr Abonnement aus.

	Achten Sie bei mehreren Abonnements darauf, dass Sie zum Erstellen einer App in Ihrer App Service-Umgebung dasselbe Abonnement verwenden müssen, das Sie beim Erstellen der App Service-Umgebung verwendet haben.

3. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie.

	*Ressourcengruppen* ermöglichen es Ihnen, verwandte Azure-Ressourcen als eine Einheit zu verwalten, und sind hilfreich beim Einrichten von *RBAC-Regeln* (Role-Based Access Control, rollenbasierte Zugriffssteuerung) für Ihre Apps. Weitere Informationen finden Sie unter [Verwalten Ihrer Azure-Ressourcen][ResourceGroups].

4. Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen.

	Bei *App Service-Plänen* handelt es sich um einen verwalteten Satz von Web-Apps. Bei Auswahl der Preise gelten diese nicht für die einzelnen Apps, sondern für den App Service-Plan. Um die Anzahl von Instanzen einer Web-App zentral hochzuskalieren, skalieren Sie die Anzahl von Instanzen Ihres App Service-Plans. Dies wirkt sich auf sämtliche Web-Apps innerhalb des betroffenen Plans aus. Für einige Funktionen wie Websiteslots oder die VNET-Integration gelten innerhalb des Plans ebenfalls Mengenbeschränkungen. Weitere Informationen hierzu finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

	Sie können die App Service-Pläne in Ihrer ASE anhand des Standorts identifizieren, der unter dem Plannamen angegeben ist.

	![][5]

	Wenn Sie einen App Service-Plan verwenden möchten, der bereits in Ihrer App Service-Umgebung vorhanden ist, wählen Sie diesen aus. Wenn Sie einen neuen App Service-Plan erstellen möchten, finden Sie weitere Informationen im folgenden Abschnitt dieses Tutorials, [Erstellen eines App Service-Plans in einer App Service-Umgebung](#createplan).

5. Geben Sie den Namen für Ihre Web-App ein, und klicken Sie dann auf **Erstellen**.

	Der Name Ihrer Web-App muss innerhalb von Azure App Service eindeutig sein. Wenn Sie daher eine Web-App mit dem Namen „diesistmeinewebapp“ erstellen möchten, darf gegenwärtig keine andere Web-App mit dem Namen „diesistmeinewebapp“ in Azure App Service vorhanden sein.

	Die URL einer Web-App in einer ASE lautet:

	[*Sitename*].[*Name Ihrer App Service-Umgebung*].p.azurewebsites.net

	anstelle von

	[*Sitename*].azurewebsites.net

## <a name="createplan"></a> Erstellen eines App Service-Plans

Wenn Sie einen App Service-Plan in einer App Service-Umgebung erstellen, stehen andere Worker zur Auswahl, da es in einer ASE keine gemeinsam verwendeten Worker gibt. Es können ausschließlich die Worker verwendet werden, die der App Service-Umgebung vom Administrator zugewiesen wurden. Um einen neuen Plan erstellen zu können, müssen dem ASE-Workerpool mehr Worker zugewiesen sein als die Gesamtzahl der Instanzen all Ihrer Pläne innerhalb dieses Workerpools. Wenn im Workerpool Ihrer App Service-Umgebung nicht genügend Worker vorhanden sind, um Ihren Plan zu erstellen, müssen Sie zusammen mit dem ASE-Administrator weitere Worker hinzufügen.

Ein weiterer Unterschied bei App Service-Plänen, die in einer App Service-Umgebung gehostet werden, besteht darin, dass keine Preise ausgewählt werden können. Mit einer App Service-Umgebung zahlen Sie für die vom System verwendeten Computeressourcen, und es fallen keine zusätzlichen Kosten für die Pläne innerhalb der Umgebung an. Beim Erstellen eines App Service-Plans wählen Sie üblicherweise einen Tarif, auf dessen Basis die Abrechnung erfolgt. Bei einer App Service-Umgebung handelt es sich im Wesentlichen um einen privaten Speicherort, an dem Sie Inhalte erstellen können. Sie zahlen nicht für das Hosten Ihrer Inhalte, sondern für die Umgebung.

Die folgenden Schritte zeigen, wie Sie einen App Service-Plan während der Erstellung einer Web-App erstellen, wie im vorherigen Abschnitt des Tutorials erläutert.

1. Klicken Sie in der Oberfläche zur Planauswahl auf **Neu erstellen**, und geben Sie einen Namen für den Plan ein, wie Sie dies normalerweise auch außerhalb einer ASE durchführen.

2. Wählen Sie aus der Standortauswahl die ASE aus, die Sie verwenden möchten.

	Da eine App Service-Umgebung im Wesentlichen ein privater Bereitstellungsort ist, wird sie unter "Standort" angezeigt.

	![][2]

	Nach der Auswahl einer ASE in der Standortauswahl wird die Oberfläche zur Erstellung von App Service-Plänen aktualisiert. Der Standort zeigt nun den Namen des ASE-Systems und die Region an, in der es sich befindet. Die Tarifplanauswahl wird durch eine Workerpoolauswahl ersetzt.

	![][3]

### Auswählen eines Workerpools

In Azure App Service und außerhalb einer App Service-Umgebung sind bei der Auswahl eines dedizierten Tarifplans üblicherweise drei Computegrößen verfügbar. Auf ähnliche Weise können Sie für eine ASE bis zu drei Workerpools und die Computegröße angeben, die für den jeweiligen Workerpool verwendet wird. Für Mandanten der ASE bedeutet dies, dass anstelle der Auswahl eines Tarifplans mit Computegröße für Ihren App Service-Plan ein sogenannter *Workerpool* ausgewählt wird.

Auf der Benutzeroberfläche zur Auswahl des Workerpools wird unterhalb des Namens die Servergröße angezeigt, die für diesen Workerpool verwendet wird. Die verfügbare Größe entspricht der Anzahl von Serverinstanzen, die zur Verwendung in diesem Pools verfügbar sind. Möglicherweise verfügt der Pool insgesamt über mehr Instanzen als diese Anzahl, der hier angezeigte Wert bezieht sich jedoch lediglich auf die Anzahl von Instanzen, die gegenwärtig nicht verwendet werden. Informationen zum Hinzufügen von Computeressourcen zu Ihrer App Service-Umgebung finden Sie unter [Konfigurieren der App Service-Umgebung](app-service-web-configure-an-app-service-environment.md).

![][4]

In diesem Beispiel werden lediglich zwei verfügbare Workerpools angezeigt. Der Grund dafür ist, dass der Administrator der App Service-Umgebung nur diesen beiden Workerpools Hosts zugewiesen hat. Der dritte Pool würde angezeigt, wenn ihm virtuelle Computer zugewiesen würden.

## Nach der Erstellung der Web-App

Im Zusammenhang mit dem Ausführen von Web-Apps und dem Verwalten von App Service-Plänen in einer ASE müssen einige Aspekte berücksichtigt werden.

Wie bereits erwähnt, ist der Besitzer der ASE für die Größe des Systems und damit ebenso dafür verantwortlich, dass eine ausreichend große Kapazität zum Hosten der gewünschten App Service-Pläne verfügbar ist. Wenn keine Worker verfügbar sind, kann der App Service-Plan nicht erstellt werden. Dies gilt ebenfalls für das Skalieren Ihrer Web-App. Wenn weitere Instanzen erforderlich sind, müssen Sie den Administrator der App Service-Umgebung bitten, weitere Worker hinzufügen.

Nach dem Erstellen der Web-App und des App Service-Plans sollten Sie eine zentrale Hochskalierung durchführen. Um Fehlertoleranz für Ihre Apps zu gewährleisten, werden in einer ASE immer mindestens zwei Instanzen Ihres App Service-Plans benötigt. Das Skalieren eines App Service-Plans in einer ASE entspricht dem normalen Skalieren über die Oberfläche des App Service-Plans. Weitere Informationen zum Skalieren finden Sie unter [Skalieren einer Web-App in einer App Service-Umgebung](app-service-web-scale-a-web-app-in-an-app-service-environment.md).

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

<!---HONumber=Nov15_HO1-->