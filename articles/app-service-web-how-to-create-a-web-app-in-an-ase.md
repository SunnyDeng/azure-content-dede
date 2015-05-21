<properties 
	pageTitle="Erstellen einer Web-App in einer App Service-Umgebung" 
	description="Vorgehensweise zum Erstellen von Web-Apps und App Service-Plänen in einer App Service-Umgebung" 
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

# Erstellen einer Web-App in einer App Service-Umgebung #

Das Erstellen von Web-Apps ist in einer App Service-Umgebung nahezu identisch mit den Schritten in einer regulären Umgebung. Wenn Sie mit der Funktion der App Service-Umgebung nicht vertraut sind, lesen Sie das Dokument [Was ist eine App Service-Umgebung][WhatisASE].

Um eine Web-App in einer App Service-Umgebung erstellen zu können, müssen Sie zunächst über eine App Service-Umgebung verfügen. Einzelheiten zum Erstellen einer App Service-Umgebung finden Sie im Dokument [Erstellen einer App Service-Umgebung][HowtoCreateASE].

Der erste Schritt beim Erstellen einer Web-App ist das Erstellen oder Auswählen eines App Service-Plans \(ASP\). Um einen ASP in einer App Service-Umgebung zu erstellen, führen Sie zunächst die üblichen Schritte zum Erstellen einer Web-App durch. Wählen Sie dazu "Neu" -\> "Web + mobil" -\> "Web-App".

![][1]


Bei Verwendung eines App Service-Plans, der in Ihrer App Service-Umgebung bereits erstellt wurde, wählen Sie den gewünschten Plan aus, geben den Namen für Ihre Web-App ein und wählen "Erstellen". Diese Vorgehensweise entspricht den üblichen Schritten zum Erstellen einer Web-App. Der Hauptunterschied liegt darin, dass der Zugriff auf Ihre Web-App über die folgende Adresse erfolgt: 

[*Sitename*].[*Name Ihrer App Service-Umgebung*].p.azurewebsites.net
 
anstelle von

[*Sitename*].azurewebsites.net

Vorerst muss der Name Ihrer Web-App innerhalb des gesamten Azure App Service eindeutig sein. Wenn Sie eine Web-App mit dem Namen "diesistmeinewebapp" erstellen möchten, darf gegenwärtig also keine andere Web-App mit dem Namen "diesistmeinewebapp" in Azure App Service vorhanden sein.

### App Service-Pläne ###

Bei App-Service-Plänen handelt es sich um einen verwalteten Satz an Web-Apps. Bei Auswahl der Preise gelten diese nicht für die einzelnen Apps, sondern für den App Service-Plan. Um die Anzahl von Instanzen einer Web-App zu erhöhen, erhöhen Sie die Anzahl von Instanzen Ihres App Service-Plans. Dies wirkt sich auf sämtliche Web-Apps innerhalb des betroffenen Plans aus. Für einige Funktionen wie Websiteslots oder die VNET-Integration gelten innerhalb des Plans ebenfalls Mengenbeschränkungen. Weitere Informationen zu App Service-Plänen finden Sie im Dokument [Azure App Service-Pläne - Detaillierte Übersicht][Appserviceplans].

Der Vorgang zum Erstellen eines neuen App Service-Plans unterscheidet sich in einigen Punkten von den Schritten zum Erstellen eines App Service-Plans in einer App Service-Umgebung. Beispielsweise stehen andere Worker zur Auswahl, da es in einer App Service-Umgebung keine gemeinsamen Worker gibt. Es können ausschließlich die Worker verwendet werden, die der App Service-Umgebung vom Administrator zugewiesen wurden. Um einen neuen App Service-Plan erstellen zu können, müssen Ihrer App Service-Umgebung also mehr Worker zugewiesen sein als die Gesamtzahl der Instanzen all Ihrer App Service-Pläne innerhalb Ihrer App Service-Umgebung. Wenn Ihre App Service-Umgebung nicht genügend Worker vorhanden sind, um einen App Service-Plan zu erstellen, müssen Sie den Administrator Ihrer App Service-Umgebung bitten, weitere Worker hinzuzufügen.

Ein weiterer Unterschied bei App Service-Plänen, die in einer App Service-Umgebung gehostet werden, besteht darin, dass keine Preise ausgewählt werden können. Mit einer App Service-Umgebung zahlen Sie für die vom System verwendeten Compute-Ressourcen, und es fallen keine zusätzlichen Kosten für die App Service-Pläne innerhalb der Umgebung an. Beim Erstellen eines App Service-Plans wählen Sie üblicherweise einen Tarif, auf dessen Basis die Abrechnung erfolgt. Bei einer App Service-Umgebung handelt es sich im Wesentlichen um einen privaten Speicherort, an dem Sie Inhalte erstellen können. Sie zahlen nicht für das Hosten Ihrer Inhalte, sondern für die Umgebung.

### Auswählen Ihrer App Service-Umgebung ###

Da es sich bei einer App Service-Umgebung im Wesentlichen um einen privaten Bereitstellungsort handelt, wählen Sie zunächst in der Speicherortauswahl die App Service-Umgebung aus.

![][2]

Nach der Auswahl wird die Benutzeroberfläche aktualisiert, wobei die Tarifauswahl durch eine Liste zur Auswahl des Workerpools ersetzt wird. Als Speicherort wird der Systemname der App Service-Umgebung und die Region angezeigt, in der sich dieses befindet. Unterhalb der URL ersetzt der Domänenname für die App Service-Umgebung den üblicherweise angezeigten Eintrag ".azurewebsites.net" durch den Namen der App Service-Umgebung.

![][3]

### Auswahl des Workerpools ###

In Azure App Service und außerhalb einer App Service-Umgebung sind bei der Auswahl eines dedizierten Tarifs üblicherweise 3 Größen verfügbar. Auf ähnliche Weise können Kunden mit einer App Service-Umgebung bis zu 3 Workerpools und die Größe des virtuellen Computers angeben, der für den jeweiligen Workerpool verwendet wird. Anstelle eines Tarifs für Ihren App Service-Plan wählen Sie einen sogenannten Workerpool.

Auf der Benutzeroberfläche zur Auswahl des Workerpools wird unterhalb des Namens die Größe der virtuellen Computer angezeigt, die für diesen Workerpool verwendet werden. Die verfügbare Größe entspricht der Anzahl von virtuellen Computern, die zur Verwendung in diesem Pools verfügbar sind. Möglicherweise verfügt der Pool insgesamt über mehr virtuelle Computer als diese Anzahl, der hier angezeigte Wert bezieht sich jedoch lediglich auf die Anzahl von virtuellen Computern, die gegenwärtig nicht verwendet werden. Informationen zum Hinzufügen von Compute-Ressourcen zu Ihrer App Service-Umgebung finden Sie im Dokument [Konfigurieren der App Service-Umgebung][HowtoConfigureASE].

![][4]

In diesem Beispiel werden lediglich zwei verfügbare Workerpools angezeigt. Der Grund dafür ist, dass der Administrator der App Service-Umgebung nur diesen beiden Workerpools virtuelle Computer zugewiesen hat. Der dritte Pool würde angezeigt, wenn ihm virtuelle Computer zugewiesen würden.

### Nach der Erstellung der Web-App ###

Im Zusammenhang mit dem Ausführen von Web-Apps und Verwalten von App Service-Plänen in einer App Service-Umgebung müssen einige Überlegungen berücksichtigt werden.

Wie bereits erwähnt, ist der Besitzer der App Service-Umgebung für die Größe des Systems und damit ebenso dafür verantwortlich, dass eine ausreichend große Kapazität zum Hosten der gewünschten App Service-Pläne verfügbar ist. Wenn keine Worker verfügbar sind, kann der App Service-Plan nicht erstellt werden. Dies gilt ebenfalls für das Skalieren Ihrer Web-App. Wenn weitere Instanzen erforderlich sind, müssen Sie den Administrator der App Service-Umgebung bitten, weitere Worker hinzufügen.

Nach dem Erstellen der Web-App und des App Service-Plans, sollten Sie eine Skalierung durchführen. Um Fehlertoleranz für Ihre Apps zu gewährleisten, werden in einer App Service-Umgebung immer mindestens 2 Instanzen Ihres App Service-Plans benötigt. Die Vorgehensweise zum Skalieren eines App Service-Plans in einer App Service-Umgebung ist mit den üblichen Schritten über die Benutzeroberfläche des App Service-Plans identisch. Weitere Einzelheiten zum Skalieren finden Sie im Dokument [Skalieren einer Web-App in einer App Service-Umgebung][HowtoScale]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment-in-an-ase/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
<!--HONumber=52-->
