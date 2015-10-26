<properties 
	pageTitle="Ändern der Preisstufe in Azure App Service" 
	description="Erfahren Sie, wie Sie Web-Apps, mobile Apps, API-Apps und Logik-Apps in Azure App Service skalieren sowie eine automatische Skalierung durchführen." 
	services="app-service" 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="stepsic"/>

# Ändern der Preisstufe in Azure App Service

Zur Steigerung von Leistung und Durchsatz für Ihre Web-Apps in Microsoft Azure können Sie Ihren App Service-Plan im [Azure-Portal](https://portal.azure.com/) vom Tarif **Free** auf die Tarife **Shared**, **Basic**, **Standard** oder **Premium** hochstufen.

Der verfügbare Dienstumfang für Ihren App Service-Plan richtet sich nach der [*Preisstufe* für den Plan](/pricing/details/app-service/). Höhere Tarife wie **Standard** und **Premium** bieten eine höhere Stabilität und Flexibilität bei der Festlegung, wie Ressourcen in Azure verwendet werden. Die Änderung der Preisstufe wirkt sich auf die Anzahl von Kernen und die Menge des Arbeitsspeichers aus, die für Ihren Dienst zur Verfügung steht. Dieser Vorgang wird als *vertikale Skalierung* (nach oben oder unten) bezeichnet.

Zusätzlich zum Ändern der Preisstufe können Sie die Anzahl von Instanzen für Ihren Dienst ändern. Dieser Vorgang wird als *horizontale Skalierung* bezeichnet. Weitere Informationen zur *horizontalen Skalierung* finden Sie unter [Manuelles oder automatisches Skalieren der Instanzenanzahl](../insights-how-to-scale.md).

Weitere Informationen zu App Service-Plänen finden Sie unter [Was ist ein App Service-Plan?](../web-sites-web-hosting-plan-overview.md) und [Azure App Service-Pläne – Detaillierte Übersicht](azure-web-sites-web-hosting-plans-in-depth-overview.md). Informationen zu Preisen und Features der einzelnen App Service-Pläne finden Sie unter [App Service – Preisdetails](/pricing/details/app-service/).

Die Skalierung funktioniert anders, wenn Sie eine dedizierte [App Service-Umgebung](app-service-app-service-environment-intro.md) verwenden möchten. Weitere Details finden Sie unter [Skalieren von Web-Apps in einer App Service-Umgebung](app-service-web-scale-a-web-app-in-an-app-service-environment.md).

> [AZURE.NOTE]Bevor Sie Web-Apps vom Modus **Free** zum Modus **Basic**, **Standard **oder **Premium** wechseln, müssen Sie zunächst das Ausgabenlimit für Ihr Azure App Service-Abonnement aufheben. Informationen zum Anzeigen oder Ändern von Optionen für Ihr Microsoft Azure App Service-Abonnement finden Sie unter [Microsoft Azure-Abonnement][azuresubscriptions].

<a name="scalingsharedorbasic"></a> <a name="scalingstandard"></a>

## Auswählen der Preisstufe

1. Öffnen Sie in Ihrem Browser das [Azure-Portal][portal], und navigieren Sie zur der App, die Sie skalieren möchten.
	
2. Klicken Sie im Bereich **Grundlegende Informationen** für Ihre App auf den Link **App Service-Plan/Preisstufe**.

3. Klicken Sie auf **Preisstufe**, um die Liste der möglichen Servicelevel für Ihren Plan anzuzeigen. Für jede Preisstufe ist ein geschätzter Preis angegeben, um Ihnen einen Überblick über die durchschnittlichen Kosten für diese Preisstufe zu geben.
	
	![Auswählen eines Plans](./media/app-service-scale/ChoosePricingTier.png)
	
4. Klicken Sie nach Auswahl der gewünschten Preisstufe auf **Auswählen**.
	
	Auf der Registerkarte **Benachrichtigungen** wird in grüner Schrift der Text **ERFOLGREICH** angezeigt, sobald der Vorgang abgeschlossen wurde.
 
Außerdem erhalten Sie [hier](http://go.microsoft.com/fwlink/?LinkId=309169) Informationen zu den verschiedenen Compute-Ebenen in Azure.
	
<a name="ScalingSQLServer"></a>
##Skalieren der zugehörige Ressourcen
Wenn Ihre App von anderen Diensten abhängt, beispielsweise von SQL oder Storage, können Sie diese Dienste basierend auf Ihren Anforderungen ebenfalls skalieren.

1. Klicken Sie in **Grundlegende Informationen** auf den Link **Ressourcengruppe**.

2. Klicken Sie dann im Bereich **Zusammenfassung** des Blatts für die Ressourcengruppen auf eine der Datenbanken (oder eine andere Ressource, die Sie skalieren möchten).

	![Verknüpfte Datenbank](./media/app-service-scale/ResourceGroup.png)
	
3. Klicken Sie im Blatt für Ihre verknüpfte SQL-Datenbank auf den Bereich **Preisstufe**, wählen Sie basierend auf Ihren Leistungsanforderungen eine Preisstufe, und klicken Sie auf **Auswählen**.
	
	![Skalieren Ihrer SQL-Datenbank](./media/app-service-scale/ScaleDatabase.png)
	
4. Wenn Ihre App Storage verwendet, wird automatisch die Georeplikation eingerichtet, wenn Sie eine Preisstufe auswählen, die die Georeplikation unterstützt. Für SLQ hingegen müssen Sie die Georeplikation manuell konfigurieren, um eine hohe Verfügbarkeit und eine Notfallwiederherstellung für Ihre SQL-Datenbank zu gewährleisten. Klicken Sie hierzu auf den Bereich **Georeplikation**.
	
	![Einrichten der geografischen Replikation für SQL-Datenbanken](./media/app-service-scale/GeoReplication.png)
	
<a name="devfeatures"></a>
## Entwicklerfunktionen
Je nach Preisstufe stehen die folgenden entwicklungsbezogenen Funktionen zur Verfügung:

### Bitness ###

- Die Preisstufen **Basic**, **Standard** und **Premium** unterstützen 64-Bit- und 32-Bit-Anwendungen.
- Die Preisstufen **Free** und **Shared** unterstützen nur 32-Bit-Anwendungen.

### Debugger-Support ###

- Debugger-Support ist für die Modi **Kostenlos**, **Freigegeben** und**Basic** bei 1 gleichzeitigen Verbindung pro App Service-Plan verfügbar.
- Debugger-Support ist für die Modi **Standard** und **Premium** bei 5 gleichzeitigen Verbindungen pro App Service-Plan verfügbar.

<a name="OtherFeatures"></a>
## Andere Funktionen

- Ausführliche Informationen zu allen weiteren Funktionen in den App Service-Plänen, einschließlich Preisgestaltung und allgemein interessanten Funktionen (auch für Entwickler) finden Sie in den [App Service – Preisdetails](/pricing/details/web-sites/).

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

<a name="Next Steps"></a>
## Nächste Schritte

- Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](/pricing/free-trial/).
- Informationen zu Preisen, Support und SLAs erhalten Sie unter den folgenden Links.
	
	[Preisdetails zur Datenübertragung](/pricing/details/data-transfers/)
	
	[Supportpläne für Microsoft Azure](/support/plans/)
	
	[Vereinbarungen zum Servicelevel (SLAs)](/support/legal/sla/)
	
	[Preisdetails für SQL-Datenbanken](/pricing/details/sql-database/)
	
	[Größen virtueller Computer und Clouddienste für Windows Azure][vmsizes]
	
	[App-Service-Preisdetails](/pricing/details/app-service/)
	
	[App-Service-Preisdetails - SSL-Verbindungen](/pricing/details/web-sites/#ssl-connections)

- Informationen zu den Best Practices für Azure App Service, einschließlich dem Aufbau einer skalierbaren, robusten Architektur, finden Sie unter [Best Practices: Azure App Service-Web-Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Videos zum Skalieren von Web-Apps:
	
	- [Wann sollte man Azure-Websites skalieren? - Mit Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - Mit Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Wie sollte man Azure-Websites skalieren? - Mit Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- LINKS -->
[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
 

<!---HONumber=Oct15_HO3-->