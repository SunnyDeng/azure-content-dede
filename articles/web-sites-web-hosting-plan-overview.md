<properties pageTitle="Was ist ein Webhostingplan?" description="Übersicht über den Webhostingplan" title="What is a web hosting plan?" services="web-sites" authors="adamab" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="adamab" />

# Was ist ein Webhostingplan? #

Webhostingpläne stellen einen Satz an Funktionen und Kapazitäten dar, die Sie über Ihre Websites freigeben können.  Webhostingpläne unterstützen ein paar Preisstufen (z. B. Kostenlos, Freigegeben, Basic und Standard), wobei jede Stufe eigene Funktionen bietet.  Websites im gleichen Abonnement, in der gleichen Ressourcengruppe und am gleichen geografischen Standort können sich einen Webhostingplan teilen.

## Funktionen von Webhostingplänen

Jede Preisstufe (z. B. Kostenlos, Freigegeben, Basic und Standard) bietet eigene Funktionen.  [Hier](http://go.microsoft.com/fwlink/?LinkID=394421) finden Sie aktuelle Informationen zu Funktionsumfang und Preisen.

Im Folgenden erhalten Sie einige nützliche Tipps im Hinblick auf Webhostingpläne und Funktionen:

- Sie können die Preisstufe eines Webhostingplans jederzeit und ohne Ausfallzeiten ändern.
- Websites im gleichen Abonnement, in der gleichen Ressourcengruppe und am gleichen Standort können sich einen Webhostingplan teilen. 
- Funktionen wie die automatische Skalierung treten durch Auswahl eines gezielten Webhostingplans in Kraft.  Wenn Sie einzelne Websites automatisch skalieren möchten, sollten Sie dieser Website einen Webhostingplan zuweisen.

[Erfahren Sie mehr über das Verwalten von Webhostingplänen](http://go.microsoft.com/fwlink/?LinkID=394411).

## Webhostingpläne und Kapazität

Webhostingpläne der Stufe "Kostenlos" und "Freigegeben" bieten Websites mit einer freigegebenen Infrastruktur, was bedeutet, dass Ihre Websites sich Ressourcen mit Websites anderer Kunden teilen.  

Webhostingpläne der Stufe "Basic" und "Standard" bieten Websites mit einer dedizierten Infrastruktur, was bedeutet, dass nur die Website(s), die Sie diesem Plan zuordnen möchten, auch mit diesen Ressourcen betrieben werden.  Bei dieser Stufe können Sie Ihren Webhostingplan so konfigurieren, dass eine oder mehrere VM-Instanzen verwendet werden.  Es werden kleine, mittlere und größere Instanzen unterstützt.  Diese virtuellen Maschinen werden in Ihrem Auftrag verwaltet, sodass Sie sich nicht mehr um Betriebssystem-Updates oder ähnliche Aufgaben kümmern müssen.  

Ein Hinweis zur Stufe "Freigegeben (Vorschau)".  Bei allen Stufen, mit Ausnahme von "Freigegeben", zahlen Sie einen Preis für den Webhostingplan, basierend auf der Stufe und der gewählten Kapazität. Es fallen keine zusätzlichen Gebühren für die einzelnen Websites an, die diesen Plan verwenden. Freigegebene Webhostingpläne sind anders.  Aufgrund der Beschaffenheit der freigegebenen Infrastruktur fallen für jede Website im Plan einzeln Gebühren an.  

### Webhostingpläne und das neue Azure-Vorschauportal

Über das neue Azure-Vorschauportal können Sie bestehende oder neue Websites mit Webhostingplänen verbinden.  Genau genommen wurden alle bestehenden Websites automatisch Webhostingplänen zugewiesen, basierend auf dem Abonnement, dem geografischen Standort und der aktuellen Preisstufe.  

Beim Erstellen einer neuen Website werden Sie im Portal gefragt, welcher Webhostingplan damit verbunden werden soll.  Sie können einen neuen Webhostingplan erstellen oder einen bestehenden Plan auswählen.  Wenn Sie einen bestehenden Plan verwenden möchten, muss sich die neue Website im gleichen Abonnement, am gleichen geografischen Standort und in der gleichen Ressourcengruppe wie der bestehende Plan befinden.  Wenn Sie eine neue leere Website erstellen, verwendet Azure standardmäßig den zuletzt verwendeten Webhostingplan, um die bereits erstellten Pläne bestmöglich zu nutzen.  Beim Erstellen einer Website mit einer Datenbank steht die Option zum Wiederverwenden eines bestehenden Plans nicht zur Verfügung.

Klicken Sie auf die Schaltfläche **Durchsuchen** in der linken Menüleiste und dann auf **Alles** in der oberen rechten Ecke des Aktivitätenfensters, das auf dem Bildschirm angezeigt wird, um alle Webhostingpläne für alle Ihre Abonnements anzuzeigen.

![][BrowseEverythingScreenshot]
![][BrowseWebHostingPlansScreenshot]

Sie können auch sehen, welchem Webhostingplan jede Website zugeordnet ist, indem Sie die grafische Darstellung Ihrer Ressourcengruppe aufrufen, die am oberen Rand des Websitefensters angezeigt wird.

![][WebHostingPlanResourceMapScreenshot]

Wenn Sie auf den Plan klicken, wird ein Blade geöffnet, mit dem Sie den Webhostingplan verwalten können.  [Erfahren Sie mehr über das Verwalten von Webhostingplänen](http://go.microsoft.com/fwlink/?LinkID=394411).

![][WebHostingPlanBladeScreenshot]
	

### Nächste Schritte

Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](http://azure.microsoft.com/en-us/pricing/free-trial/).

<!-- Images. -->
[BrowseEverythingScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-everything.png
[WebHostingPlanResourceMapScreenshot]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-resource-map.png
[WebHostingPlanBladeScreenshot]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-blade.png
[BrowseWebHostingPlansScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-web-hosting-plans.png
