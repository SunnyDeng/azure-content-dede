<properties pageTitle="What is a web hosting plan?" description="Web hosting plan overview" title="What is a web hosting plan?" authors="adamab" />

Was ist ein Webhostingplan?
===========================

Webhostingpläne stellen einen Satz an Funktionen und Kapazitäten dar, die Sie über Ihre Websites freigeben können. Webhostingpläne unterstützen ein paar Preisstufen (z. B. Kostenlos, Freigegeben, Basic und Standard), wobei jede Stufe eigene Funktionen bietet. Websites im gleichen Abonnement, in der gleichen Ressourcengruppe und am gleichen geografischen Standort können sich einen Webhostingplan teilen.

Funktionen von Webhostingplänen
-------------------------------

Jede Preisstufe (z. B. Kostenlos, Freigegeben, Basic und Standard) bietet eigene Funktionen. [Hier](http://go.microsoft.com/fwlink/?LinkID=394421) finden Sie aktuelle Informationen zu Funktionsumfang und Preisen.

Im Folgenden erhalten Sie einige nützliche Tipps im Hinblick auf Webhostingpläne und Funktionen:

-   Sie können die Preisstufe eines Webhostingplans jederzeit und ohne Ausfallzeiten ändern.
-   Websites im gleichen Abonnement, in der gleichen Ressourcengruppe und am gleichen Standort können sich einen Webhostingplan teilen.
-   Funktionen wie die automatische Skalierung treten durch Auswahl eines gezielten Webhostingplans in Kraft. Wenn Sie einzelne Websites automatisch skalieren möchten, sollten Sie dieser Website einen Webhostingplan zuweisen.

Webhostingpläne und Kapazität
-----------------------------

Webhostingpläne der Stufe "Kostenlos" und "Freigegeben" bieten Websites mit einer freigegebenen Infrastruktur, was bedeutet, dass Ihre Websites sich Ressourcen mit Websites anderer Kunden teilen.

Webhostingpläne der Stufe "Basic" und "Standard" bieten Websites mit einer dedizierten Infrastruktur, was bedeutet, dass nur die Website(s), die Sie diesem Plan zuordnen möchten, auch mit diesen Ressourcen betrieben werden. Bei dieser Stufe können Sie Ihren Webhostingplan so konfigurieren, dass eine oder mehrere VM-Instanzen verwendet werden. Es werden kleine, mittlere und größere Instanzen unterstützt. Diese virtuellen Maschinen werden in Ihrem Auftrag verwaltet, sodass Sie sich nicht mehr um Betriebssystem-Updates oder ähnliche Aufgaben kümmern müssen.

Ein Hinweis zur Stufe "Freigegeben (Vorschau)". Bei allen Stufen, mit Ausnahme von "Freigegeben", zahlen Sie einen Preis für den Webhostingplan, basierend auf der Stufe und der gewählten Kapazität. Es fallen keine zusätzlichen Gebühren für die einzelnen Websites an, die diesen Plan verwenden. Freigegebene Webhostingpläne sind anders. Aufgrund der Beschaffenheit der freigegebenen Infrastruktur fallen für jede Website im Plan einzeln Gebühren an.

### Webhostingpläne und das neue Azure-Vorschauportal

Über das neue Azure-Vorschauportal können Sie bestehende oder neue Websites mit den Webhostingplänen verbinden. Genau genommen wurden alle bestehenden Websites automatisch den Webhostingplänen zugewiesen, basierend auf dem Abonnement, dem geografischen Standort und der aktuellen Preisstufe.

Beim Erstellen einer neuen Website werden Sie im Portal gefragt, welcher Webhostingplan damit verbunden werden soll. Sie können einen neuen Webhostingplan erstellen oder einen bestehenden Plan auswählen. Wenn Sie einen bestehenden Plan verwenden möchten, muss sich die neue Website im gleichen Abonnement, am gleichen geografischen Standort und in der gleichen Ressourcengruppe wie der bestehende Plan befinden. Wenn Sie eine neue leere Website erstellen, verwendet Azure standardmäßig den zuletzt verwendeten Webhostingplan, um die bereits erstellten Pläne bestmöglich zu nutzen. Beim Erstellen einer Website mit einer Datenbank steht die Option zum Wiederverwenden eines bestehenden Plans nicht zur Verfügung.

Klicken Sie auf die Schaltfläche **Durchsuchen** in der linken Menüleiste und dann auf **Alles** in der oberen rechten Ecke des Aktivitätenfensters, das auf dem Bildschirm angezeigt wird, um alle Webhostingpläne für alle Ihre Abonnements anzuzeigen.

![](./media/web-sites-web-hosting-plan-overview/browse-everything.png)

![](./media/web-sites-web-hosting-plan-overview/browse-web-hosting-plans.png)

Sie können auch sehen, welchem Webhostingplan jede Website zugeordnet ist, indem Sie die grafische Darstellung Ihrer Ressourcengruppe aufrufen, die am oberen Rand des Website-Blades angezeigt wird.

![](./media/web-sites-web-hosting-plan-overview/web-hosting-plan-resource-map.png)

Wenn Sie auf den Plan klicken, wird ein Blade geöffnet, mit dem Sie den Webhostingplan verwalten können. [Erfahren Sie mehr über das Verwalten von Webhostingplänen](http://go.microsoft.com/fwlink/?LinkID=394411).

![](./media/web-sites-web-hosting-plan-overview/web-hosting-plan-blade.png)

