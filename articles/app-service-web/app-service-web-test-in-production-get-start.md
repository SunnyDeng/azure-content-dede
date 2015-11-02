<properties
	pageTitle="Erste Schritte beim Testen in der Produktionsumgebung für Web-Apps"
	description="Informationen zum TiP-Feature (Test in Production, Testen in der Produktionsumgebung) in Azure App Service-Web-Apps."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="cephalin"/>

# Erste Schritte beim Testen in der Produktionsumgebung für Web-Apps

Das Testen von Web-Apps in der Produktionsumgebung unter Verwendung von Livedatenverkehr der Kunden – auch Livetesting genannt – ist eine Teststrategie, die App-Entwickler immer häufiger in ihre Methodik der [agilen Entwicklung](https://en.wikipedia.org/wiki/Agile_software_development) integrieren. Mit dieser Strategie können Sie die Qualität Ihrer Apps in Ihrer Produktionsumgebung mit Livedatenverkehr von Benutzern testen und müssen nicht mit künstlich erzeugten Daten und simuliertem Datenverkehr in einer Testumgebung arbeiten. Indem Sie Ihre neue App für echte Benutzer verfügbar machen, erhalten Sie wichtige Informationen zu den tatsächlichen Problemen, die bei Ihrer App nach der Bereitstellung auftreten können. Sie können die Funktionsfähigkeit, die Leistung und den Nutzen Ihrer App-Updates anhand des Umfangs, der Geschwindigkeit und der Variabilität echten Benutzerdatenverkehrs überprüfen – diese Aspekte lassen sich in einer Textumgebung nicht genau genug annähern.

## Datenverkehrsrouting in App Service-Web-Apps

Mit dem Feature für Datenverkehrsrouting in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) können Sie einen Teil des Livedatenverkehrs von Benutzern an einen oder mehrere [Bereitstellungsslots](web-sites-staged-publishing.md) weiterleiten und Ihre App mit [Azure Application Insights](/services/application-insights/), [Azure HDInsight](/services/hdinsight/) oder einem Drittanbietertool wie [New Relic](/marketplace/partners/newrelic/newrelic/) analysieren, um die Änderungen zu überprüfen. Sie können z. B. folgende Szenarien mit App Service implementieren:

- Ermitteln von Bugs in Funktionen oder Leistungsengpässen in Ihren Updates vor der Bereitstellung in der gesamten Website
- Durchführen von „kontrollierten Test-Flights“ Ihrer Änderungen durch Messen von Nutzungsmetriken in der Beta-App
- Sukzessives Einführen eines Updates und problemloses Zurücksetzen auf die aktuelle Version, falls ein Fehler auftritt 
- Optimieren der Geschäftsergebnisse Ihrer App durch Ausführen von [A/B-Tests](https://en.wikipedia.org/wiki/A/B_testing) oder [multivariaten Tests](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing) in mehreren Bereitstellungsslots

### Anforderungen für die Verwendung des Datenverkehrsroutings in Web-Apps

- Ihre Web-App muss im Tarif **Standard** oder **Premium** ausgeführt werden, da dies für mehrere Bereitstellungsslots erforderlich ist.
- Damit das Datenverkehrsrouting ordnungsgemäß ausgeführt werden kann, müssen in den Browsern der Benutzer Cookies aktiviert sein. Das Datenverkehrsrouting verwendet Cookies, um einen Clientbrowser während der Dauer einer Clientsitzung auf einen Bereitstellungsslot festzulegen.
- Die Funktion unterstützt erweiterte TiP-Szenarien durch Azure PowerShell-Cmdlets.

## Weiterleiten eines Datenverkehrssegments an einen Bereitstellungsslot

Auf der Basisebene in jedem TiP-Szenario leiten Sie einen vorab definierten Prozentsatz des Livedatenverkehrs an einen nicht produktiven Bereitstellungsslot weiter. Führen Sie dazu die folgenden Schritte aus:

>[AZURE.NOTE]Bei den folgenden Schritten wird davon ausgegangen, dass Sie bereits über einen [nicht produktiven Bereitstellungsslot](web-sites-staged-publishing.md) verfügen und die gewünschten Web-App-Inhalte in diesem Slot [bereitgestellt](web-sites-publish-source-control.md) wurden.

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.
2. Klicken Sie auf dem Blatt der Web-App auf **Einstellungen** > **Datenverkehrsrouting**. ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. Wählen Sie den Slot aus, an den Sie den Datenverkehr weiterleiten möchten, sowie den gewünschten Prozentsatz des Gesamtdatenverkehrs, und klicken Sie auf **Speichern**.

	![](./media/app-service-web-test-in-production/02-select-slot.png)

4. Wechseln Sie zum Blatt des Bereitstellungsslots. Hier sollte der Livedatenverkehr angezeigt werden, der an diesen Slot weitergeleitet wird.

	![](./media/app-service-web-test-in-production/03-traffic-routed.png)

Nachdem das Datenverkehrsrouting konfiguriert ist, wird der angegebene Prozentsatz an Clients nach dem Zufallsprinzip an den nicht produktiven Slot weitergeleitet. Beachten Sie jedoch: Sobald ein Client automatisch an einen bestimmten Slot weitergeleitet wird, ist er für die gesamte Dauer der Clientsitzung auf diesen Slot festgelegt. Diese Festlegung erfolgt über ein Cookie. Wenn Sie sich die HTTP-Anforderungen anschauen, finden Sie ein `TipMix`-Cookie in jeder nachfolgenden Anforderung.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## Weiterleiten von Clientanforderungen an einen bestimmten Slot

Neben dem automatischen Datenverkehrsrouting kann App Service Anforderungen auch an einen bestimmten Slot weiterleiten. Dies ist hilfreich, wenn Sie Ihren Benutzern ermöglichen möchten, die Beta-App zu nutzen oder die Nutzung zu beenden. Zu diesem Zweck verwenden Sie den `x-ms-routing-name`-Abfrageparameter.

Um Benutzer mithilfe von `x-ms-routing-name` an einen bestimmten Slot weiterzuleiten, müssen Sie sicherstellen, dass der Slot der Datenverkehrsroutingliste bereits hinzugefügt wurde. Da Sie explizit an einen Slot weiterleiten möchten, spielt der tatsächliche Routingprozentsatz keine Rolle. Sie können bei Bedarf einen „Betalink“ erstellen, auf den Benutzer klicken können, um auf die Beta-App zuzugreifen.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### Beenden der Nutzung der Beta-App für Benutzer

Damit Benutzer die Nutzung Ihrer Beta-App beenden können, können Sie z. B. folgenden Link auf Ihre Webseite bereitstellen:

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

Die Zeichenfolge `x-ms-routing-name=self` gibt den Produktionsslot an. Wenn der Clientbrowser auf den Link zugreift, wird er nicht nur an den Produktionsslot weitergeleitet, sondern jede nachfolgende Anforderung enthält das `x-ms-routing-name=self`-Cookie, das die Sitzung auf den Produktionsslot festlegt.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### Nutzen der Beta-App durch Benutzer

Damit Benutzer Ihre Beta-App nutzen können, legen Sie den gleichen Abfrageparameter auf den Namen des nicht produktiven Slots fest, z. B.:

		<webappname>.azurewebsites.net/?x-ms-routing-name=staging

## Weitere Ressourcen ##

-   [Einrichten von Stagingumgebungen für Web-Apps in Azure App Service](web-sites-staged-publishing.md)
-	[Vorhersagbares Bereitstellen einer komplexen Anwendung in Azure](app-service-deploy-complex-application-predictably.md)
-   [Agile Softwareentwicklung mit Azure App Service](app-service-agile-software-development.md)
-	[Effektive Verwendung der DevOps-Umgebungen für Ihre Web-Apps](app-service-web-staged-publishing-realworld-scenarios.md)

<!---HONumber=Oct15_HO4-->