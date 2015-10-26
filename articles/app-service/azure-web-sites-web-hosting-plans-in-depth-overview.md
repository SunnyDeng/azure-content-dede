<properties 
	pageTitle="Azure App Service-Pläne – Detaillierte Übersicht" 
	description="Erfahren Sie, wie App Service-Pläne für Azure App Service funktionieren, und wie Ihre Verwaltungsumgebung davon profitiert." 
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="byvinyal"/>

#Azure App Service-Pläne – Detaillierte Übersicht#

Ein **App Service-Plan** stellt einen Satz von Funktionen und Kapazitäten dar, die Sie in mehreren Apps in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) gemeinsam nutzen können, einschließlich Web-Apps, mobilen Apps, Logik-Apps oder API-Apps. Diese Pläne unterstützen 5 Preisstufen (**Free**, **Shared**, **Basic**, **Standard** und **Premium**), wobei jede Preisstufe über eigene Funktionen und Kapazitäten verfügt. Apps im selben Abonnement und am gleichen geografischen Standort können den gleichen Plan verwenden. Alle Apps mit gemeinsamem Plan können alle Funktionen und Features nutzen, die in der Preisstufe des Plans vorgesehen sind. Alle einem bestimmten Plan zugeordneten Apps werden auf den Ressourcen ausgeführt, die vom Plan vorgegeben sind. Wenn Ihr Plan beispielsweise so konfiguriert ist, dass zwei "kleine" Instanzen der Preisstufe "Standard" verwendet werden, werden alle Apps, die diesem Plan zugeordnet sind, auf beiden Instanzen ausgeführt und haben Zugriff auf die Funktionalität der Preisstufe "Standard". Planinstanzen, auf denen Apps ausgeführt werden, auf, sind vollständig verwaltbar und bieten hohe Verfügbarkeit.

In diesem Artikel lernen Sie die Hauptmerkmale kennen, z. B. Preisstufe und Umfang eines App Service-Plans und ihre Rolle beim Verwalten Ihrer Apps.

##Apps und App Service-Pläne

Jede App in App Service kann nur einem App Service-Plan gleichzeitig zugeordnet sein.

Apps und Pläne sind in einer Ressourcengruppe enthalten. Ressourcengruppen fungieren als Lebenszyklusgrenze für alle darin enthaltenen Ressourcen. Mit Ressourcengruppen können Sie alle Ihre Ressourcen in einer Anwendung gemeinsam verwalten.

Die Möglichkeit mehrerer App Service-Pläne in einer einzelnen Ressourcengruppe lässt das Zuordnen verschiedener Apps zu verschiedenen physischen Ressourcen zu. Dies ermöglicht beispielsweise die Trennung von Ressourcen zwischen Entwicklungs-, Test-und Produktionsumgebungen. Ein Szenario hierfür ist, wenn Sie einen Plan mit eigenen dedizierten Ressourcen Ihren Produktions-Apps und einen zweiten Plan Ihren Entwicklungs- und Testumgebungen zuordnen möchten. Auf diese Weise konkurrieren Auslastungstests für eine neue Version Ihrer Apps nicht um dieselben Ressourcen wie Ihre Produktions-Apps, die für reale Kunden bereitstehen.

Mehrere Pläne in einer einzelnen Ressourcengruppe ermöglichen Ihnen die Definition einer Anwendung, die sich über mehrere geografische Regionen erstreckt. Eine hochverfügbare Anwendung in zwei Regionen umfasst z. B. zwei Pläne, je einen pro Region, und eine App pro Plan. In diesem Fall sind alle Kopien der Anwendung einer einzelnen Ressourcengruppen zugeordnet. Dank einer zentralen Ansicht einer Ressourcengruppe mit mehreren Plänen und mehreren Apps können Sie die Integrität der Anwendung auf einfache Weise verwalten, steuern und anzeigen.

## Erstellen eines neuen App Service-Plans im Vergleich zum Verwenden eines vorhandenen Plans

Bei der Erstellung einer neuen App sollten Sie die Erstellung einer neuen Ressourcengruppe erwägen, wenn die zu erstellende App eine ganz neues Projekt darstellt. In diesem Fall sollten Sie eine neue Ressourcengruppe, einen neuen Plan und eine App erstellen.

Wenn die App, die Sie erstellen möchten, eine Komponente einer größeren Anwendung ist, muss diese Webanwendung in der Ressourcengruppe erstellt werden, die dieser größeren Anwendung zugeordnet ist.

Unabhängig davon, ob die neue App eine völlig neue oder Teil einer größeren Anwendung ist, können Sie einen vorhandenen App Service-Plan nutzen, um sie zu hosten, oder einen neuen erstellen. Dies ist eher eine Frage der Kapazität und erwarteten Auslastung. Wenn diese neue App ressourcenintensiv sein und andere Skalierungsfaktoren als die anderen Apps haben wird, die in einem vorhandenen Plan gehostet werden, wird empfohlen, sie in einem eigenen Plan zu isolieren.

Das Erstellen eines neuen Plans ermöglicht Ihnen das Zuordnen eines neuen Satzes von Ressourcen für Ihre Web-App und ermöglicht eine bessere Kontrolle der Ressourcenzuordnung, da jeder Plan einen eigenen Satz von Instanzen erhält.
 
Die Fähigkeit zum Verschieben von Apps zwischen Plänen ermöglicht Ihnen auch, die Art und Weise zu ändern, in der Ressourcen innerhalb der größeren Anwendung zugeordnet werden.
 
Wenn Sie schließlich eine neue App in einer anderen Region erstellen möchten und diese Region über keinen vorhandenen Plan verfügt, müssen Sie einen neuen Plan in dieser Region erstellen, damit Sie Ihre App in dieser hosten können.

## Wie erstelle ich einen Plan?

Sie können keinen leeren App Service-Plan erstellen. Sie können jedoch bei der App-Erstellung einen neuen Plan erstellen.

Klicken Sie hierzu im [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) auf **NEU**, anschließend auf **Web + mobil** und dann auf **Web-Apps**, **Mobile Apps**, **Logik-Apps** oder **API-Apps**. ![][createWebApp]

Sie können dann den App Service-Plan für die neue Anwendung auswählen oder erstellen.
  
 ![][createASP]
 
## Wie kann ich eine App in einen anderen App Service-Plan verschieben?

Sie können eine App im [Azure-Portal](http://portal.azure.com) in einen anderen App Service-Plan verschieben. Apps können zwischen Plänen in derselben geografischen Region verschoben werden.

Um eine Anwendung in einen anderen Plan zu verschieben, navigieren Sie zu der App, die Sie verschieben möchten, und klicken dann auf **App Service-Plan ändern**.
 
Dadurch wird das Blatt "App Service-Plan" geöffnet. Jetzt können Sie entweder einen vorhandenen Plan auswählen oder einen neuen erstellen. Pläne in einer anderen geografischen Region werden abgeblendet dargestellt und können nicht ausgewählt werden.

![][change]

Beachten Sie, dass jeder Plan eine eigene Preisstufe aufweist. Wenn Sie eine Website von der Preisstufe **Free** in die Preisstufe **Standard** verschieben, kann Ihre App alle Features und Ressourcen der Preisstufe **Standard** nutzen.

## Skalieren eines App Service-Plans

Es gibt zwei Möglichkeiten, einen Plan zu skalieren:

- Ändern Sie die **Preisstufe** des Plans. Ein Plan der Preisstufe **Basic** kann beispielsweise in einen Plan der Preisstufe **Standard** oder **Premium** geändert werden. Anschließend können alle Apps, die diesem Plan zugeordnet sind, die von der neuen Preisstufe bereitgestellten Funktionen nutzen.
- Ändern Sie die **Instanzgröße** eines Plan. Ein Plan der Preisstufe **Basic** mit **kleinen** Instanzen kann beispielsweise zur Verwendung **großer** Instanzen geändert werden. Alle Apps, die diesem Plan zugeordnet sind, können die zusätzlichen Arbeitsspeicher- und CPU-Ressourcen nutzen, die aufgrund der größeren Instanzen verfügbar sind.
- Ändern Sie die **Instanzanzahl** des Plans. Ein **Standard**-Plan mit 3 Instanzen kann z. B. auf 10 Instanzen skaliert werden. Ein **Premium**-Plan (Vorschau) kann (mit einigen Einschränkungen) auf 20 Instanzen skaliert werden. Alle Apps, die diesem Plan zugeordnet sind, können die zusätzlichen Arbeitsspeicher- und CPU-Ressourcen nutzen, die aufgrund der größeren Instanzanzahl verfügbar sind.

In der folgenden Abbildung sehen Sie das Blatt **App Service-Plan** und das Blatt **Preisstufe**. Durch Klicken auf den Bereich **Preisstufe** im Blatt **App Service-Plan** wird das Blatt **Preisstufe** erweitert, in dem Sie die Preisstufe und Instanzgröße des Plans ändern können.
 
 ![][pricingtier]

##Zusammenfassung

App Service-Pläne stellen einen Satz an Funktionen und Kapazitäten dar, die Sie App-übergreifend gemeinsam nutzen können. App Service-Pläne bieten Ihnen die Flexibilität, bestimmte Apps einer bestimmten Gruppe von Ressourcen zuzuordnen und die Auslastung Ihrer Azure-Ressourcen weiter zu optimieren. Wenn Sie in Ihrer Testumgebung Geld sparen möchten, können Sie einen Plan für mehrere Apps freigeben. Sie können außerdem den Durchsatz für Ihre Produktionsumgebung maximieren, indem Sie sie über mehrere Regionen und Pläne skalieren.

## Änderungen

* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 
 
 
 <!-- Images. -->
[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png

<!---HONumber=Oct15_HO3-->