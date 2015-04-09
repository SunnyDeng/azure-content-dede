<properties 
	pageTitle="Azure App Service-Pläne - Detaillierte Übersicht" 
	description="Erfahren Sie, wie App Service-Pläne für Azure App Service funktionieren und wie Ihre Verwaltungsumgebung davon profitiert." 
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2014" 
	ms.author="byvinyal"/>

# Azure App Service-Pläne - Detaillierte Übersicht

Ein **App Service-Plan** stellt einen Satz von Funktionen und Kapazitäten dar, die Sie in mehreren Apps in [Azure App Services](http://go.microsoft.com/fwlink/?LinkId=529714) gemeinsam nutzen können, einschließlich Web-Apps, mobilen Apps, Logik-Apps oder API-Apps. Diese Pläne unterstützt 5 Preisstufen (**Kostenlos**, **Freigegeben**, **Basic**, **Standard** und **Premium**), wobei jede Stufe über eigene Funktionen und Kapazitäten verfügt. Apps im gleichen Abonnement und am gleichen geografischen Standort können sich einen Plan teilen. Alle Apps mit gemeinsamem Plan können alle Funktionen und Features nutzen, die von der Stufe des Plans vorgesehen sind. Alle einem bestimmten Plan zugeordneten Anwendungen werden auf den Ressourcen ausgeführt, die vom Plan vorgegeben sind. Wenn Ihr Plan beispielsweise so konfiguriert ist, dass zwei "kleine" Instanzen auf der Dienststufe "Standard" verwendet werden, werden alle Anwendungen, die diesem Plan zugeordnet sind, auf beiden Instanzen ausgeführt und haben Zugriff auf die Funktionalität der Dienststufe "Standard". Planinstanzen, auf denen Anwendungen ausgeführt werden, auf, sind vollständig verwaltbar und hochverfügbar.

In diesem Artikel lernen Sie die Hauptmerkmale kennen, z. B. Stufe und Umfang eines App Service-Plans und ihre Rolle beim Verwalten Ihrer Apps.

## Apps und App Service-Plan

Jede App in App Service kann nur einem App Service-Plan gleichzeitig zugeordnet sein. 

Apps und Pläne sind in einer Ressourcengruppe enthalten. Ressourcengruppen fungieren als Lebenszyklusgrenze für alle darin enthaltenen Ressourcen. Mit Ressourcengruppen können Sie alle Ihre Ressourcen in einer Anwendung gemeinsam verwalten.

Die Möglichkeit mehrerer App Service-Pläne in einer einzelnen Ressourcengruppen lässt das Zuordnen verschiedener Apps zu verschiedenen physischen Ressourcen zu. Dies ermöglicht beispielsweise die Trennung von Ressourcen zwischen Entwicklungs-, Test-und Produktionsumgebungen. Ein Szenario hierfür ist, wenn Sie einen Plan mit eigenen dedizierten Ressourcen Ihren Produktionsanwendungen und einen zweiten Plan Ihren Entwicklungs- und Testumgebungen zuordnen möchten. Auf diese Weise konkurrieren Auslastungstests für eine neue Version Ihrer Anwendungen nicht um dieselben Ressourcen wie Ihre Produktionsanwendungen, die für reale Kunden bereitstehen.

Mehrere Pläne in einer einzelnen Ressourcengruppe ermöglichen Ihnen die Definition einer Anwendung, die sich über mehrere geografische Regionen erstreckt. Eine hochverfügbare Anwendung in zwei Regionen umfasst z. B. zwei Pläne, je einen pro Region, und eine App pro Plan. In diesem Fall sind alle Kopien der Anwendung einer einzelnen Ressourcengruppen zugeordnet. Dank einer zentralen Ansicht einer Ressourcengruppe mit mehreren Plänen und mehreren Anwendungen können Sie diese Anwendungen schnell und einfach verwalten, steuern und überwachen.

## Erstellen eines neuen App Service-Plans im Vergleich zum Verwenden eines vorhandenen Plans

Bei der Erstellung einer neuen App sollten Sie die Erstellung einer neuen Ressourcengruppe erwägen, wenn die zu erstellende App eine ganz neues Projekt darstellt. In diesem Fall sollten Sie eine neue Ressourcengruppe, einen neuen Plan und eine App erstellen.

Wenn die App, die Sie erstellen möchten, eine Komponente einer größeren Anwendung ist, muss diese Webanwendung in der Ressourcengruppe erstellt werden, die dieser größeren Anwendung zugeordnet ist.

Unabhängig davon, ob die neue App eine völlig neue oder Teil einer größeren Anwendung ist, können Sie einen vorhandenen App Service-Plan nutzen, um sie zu hosten, oder einen neuen erstellen. Dies ist eher eine Frage der Kapazität und erwarteten Auslastung. Wenn diese neue App ressourcenintensiv sein und andere Skalierungsfaktoren als die anderen Apps haben wird, die in einem vorhandenen Plan gehostet werden, wird empfohlen, sie in einem eigenen Plan zu isolieren.

Das Erstellen eines neuen Plans ermöglicht Ihnen das Zuordnen eines neuen Satzes von Ressourcen für Ihre Web-App und bietet Ihnen größere Kontrolle über die Ressourcenzuordnung, da jeder Plan einen eigenen Satz von Instanzen erhält.
 
Die Fähigkeit zum Verschieben von Apps zwischen Plänen ermöglicht Ihnen auch, die Art und Weise zu ändern, in der Ressourcen innerhalb der größeren Anwendung zugeordnet werden.
 
Wenn Sie schließlich eine neue App in einer anderen Region erstellen möchten und diese Region über keinen vorhandenen Plan verfügt, müssen Sie einen neuen Plan in dieser Region erstellen, damit Sie Ihre App in dieser hosten können.

## App Service-Pläne - Häufig gestellte Fragen

**Frage**: Wie erstelle ich einen Plan?

**Antwort**: Sie können keinen leeren App Service-Plan erstellen, jedoch kann ein neuer Plan während der Erstellung der App explizit erstellt werden.

Klicken Sie hierfür im [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) auf **NEU**, dann auf **Web + mobil** und danach auf **Web-Apps**, **Mobile Apps**, **Logik-Apps** oder **API-Apps**. Sie können dann den App Service-Plan für die neue Anwendung auswählen oder erstellen.
 
![App Service-Pläne - Häufig gestellte Fragen](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png)

**Frage**: Wie weise ich eine App einem **App Service-Plan** zu?

**Antwort**: Anwendungen können während des Erstellungsprozesses einem vorhandenen Plan zugewiesen werden.

Klicken Sie hierfür im [Azure-Portal](http://portal.azure.com) auf **NEU**, dann auf **Web + mobil** und danach auf **Web-Apps**, **Mobile Apps**, **Logik-Apps** oder **API-Apps**. Sie können dann den App Service-Plan für die neue Anwendung auswählen oder erstellen. Wenn Sie auf **oder vorhandenen auswählen** erhalten Sie die Liste der vorhandenen Pläne, aus denen Sie wählen können.

![App Service-Pläne - Häufig gestellte Fragen](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png)
 
**Frage**: Wie kann ich eine App in einen anderen App Service-Plan verschieben?

**Antwort**: Sie können eine App im [Azure-Portal](http://portal.azure.com) in einen anderen App Service-Plan verschieben. Apps können zwischen Plänen in derselben geografischen Region verschoben werden.

Um eine Anwendung in einen anderen Plan zu verschieben, navigieren Sie zu der Anwendung, die Sie verschieben möchten, und klicken dann auf **App Service-Plan ändern**.
 
Dadurch wird das Blatt "App Service-Plan" geöffnet. An dieser Stelle können Sie entweder einen vorhandenen Plan auswählen oder einen neuen erstellen. Pläne in einer anderen geografischen Region sind abgeblendet und können nicht ausgewählt werden.

![App Service-Pläne - Häufig gestellte Fragen](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png)

Beachten Sie, dass jeder Plan eine eigene Preisstufe hat. Wenn Sie eine Website von der Stufe **Kostenlos** auf die Stufe **Standard** verschieben, kann Ihre App alle Features und Ressourcen der Stufe **Standard** nutzen.

**Frage**: Wie kann ich einen App Service-Plan skalieren?

**Antwort**: Es gibt zwei Möglichkeiten, einen Plan zu skalieren:

- Ändern Sie die **Preisstufe** des Plans. Ein Plan auf der Stufe **Basic** kann beispielsweise in einen Plan auf der Stufe **Standard** oder **Premium** geändert werden. Anschließend können alle Anwendungen, die diesem Plan zugeordnet sind, die von der neuen Dienststufe gebotenen Funktionen nutzen.
- Ändern Sie die **Instanzgröße** eines Plan. Ein Plan auf der Stufe **Basic** mit **kleinen** Instanzen kann beispielsweise für das Verwenden **großer** Instanzen geändert werden. Alle Anwendungen, die diesem Plan zugeordnet sind, können die zusätzlichen Arbeitsspeicher- und CPU-Ressourcen nutzen, die vom größeren Instanztyp geboten werden.
- Ändern Sie die **Instanzanzahl** des Plans. Ein **Standard**-Plan mit einer Skalierung um 3 Instanzen kann z. B. auf 10 Instanzen skaliert werden. Ein **Premium**-Plan (Vorschau) kann (mit einigen Einschränkungen) auf 20 Instanzen skaliert werden. Alle Anwendungen, die diesem Plan zugeordnet sind, können die zusätzlichen Arbeitsspeicher- und CPU-Ressourcen nutzen, die vom größeren Instanzanzahl geboten werden.

In der folgenden Abbildung sehen Sie das Blatt **App Service-Plan** und das Blatt **Preisstufe**. Durch Klicken auf den Bereich **Preisstufe** im Blatt **App Service-Plan** wird das Blatt **Preisstufe** erweitert, in dem Sie die Preisstufe und Instanzgröße des Plans ändern können.
 
![App Service-Pläne - Häufig gestellte Fragen](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png)

## Zusammenfassung

App Service-Pläne stellen einen Satz an Funktionen und Kapazitäten dar, die Sie anwendungsübergreifend gemeinsam nutzen können. App Service-Pläne bieten Ihnen die Flexibilität, bestimmte Anwendungen einer bestimmten Gruppe von Ressourcen zuzuordnen und die Auslastung Ihrer Azure-Ressourcen weiter zu optimieren. Wenn Sie in Ihrer Testumgebung Geld sparen möchten, können Sie einen Plan für mehrere Apps freigeben. Sie können auch den Durchsatz für Ihre Produktionsumgebung maximieren, indem Sie sie über mehrere Regionen und Pläne skalieren.

## Was sich geändert hat

* Informationen über die Änderung von Websites zu App Service finden Sie unter: [Azure App Service und seine Auswirkungen auf vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714)
* Informationen über die Änderung vom alten Portal zum neuen Portal finden Sie unter: [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->