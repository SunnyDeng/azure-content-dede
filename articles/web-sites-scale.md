<properties 
	pageTitle="Skalieren einer Web-App in Azure App Service" 
	description="Erfahren Sie, wie Sie Web-Apps in Azure App Service skalieren und horizontal skalieren, einschließlich automatischer Skalierung." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Skalieren einer Web-App in Azure App Service #

Zur Steigerung der Leistung und des Durchsatzes für Ihre Web-Apps unter Microsoft Azure können Sie mithilfe des [Azure-Portals](http://go.microsoft.com/fwlink/?LinkId=529715) Ihren [App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Plan vom **Kostenlos**-Modus zu einem der Modi **Freigegeben**, **Basic**, **Standard** oder **Premium** hochstufen. 

Das Skalieren von Azure-Web-Apps umfasst zwei zusammenhängende Vorgänge: Ändern des App Service-Plan-Modus auf einen höheren Servicelevel und Konfigurieren bestimmter Einstellungen nach dem Wechsel zum höheren Servicelevel. Beide Themen werden in diesem Artikel erläutert. Höhere Servicelevel wie der **Standard**- und der **Premium**-Modus bieten bessere Stabilität und Flexibilität bei der Festlegung, wie Ressourcen auf Azure verwendet werden.

Die Modusänderung und die Konfiguration erfolgt ganz einfach über die Registerkarte "Skalieren" des Verwaltungsportals. Sie können nach Bedarf hoch- oder runterskalieren. Diese Änderungen werden innerhalb von Sekunden angewendet und wirken sich auf alle Web-Apps im App Service-Plan aus. Dafür muss weder der Code geändert noch Anwendungen erneut bereitgestellt werden.

Informationen zu App Service-Plänen finden Sie unter [Was ist ein Webhostingplan?](web-sites-web-hosting-plan-overview.md) und [Azure App Service Plans In-Depth Overview (in englischer Sprache)](azure-web-sites-web-hosting-plans-in-depth-overview.md). Informationen zur Preisgestaltung und den Merkmalen der einzelnen App Service-Pläne finden Sie unter [Websites Preise](/pricing/details/web-sites/).  

> [AZURE.NOTE] Bevor Sie Web-Apps vom Modus **Kostenlos** zu einem der Modi **Basic**, **Standard** oder **Premium** ändern, müssen Sie zuerst die Kostenbeschränkung für Ihr Azure App Service-Abonnement aufheben. Informationen zum Ändern oder Anzeigen der Optionen für Ihr Microsoft Azure App Service-Abonnement finden Sie unter [Microsoft Azure-Abonnements][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Skalierung auf Modus "Freigegeben" oder "Basic"
<!-- ===================================== -->

1. Öffnen Sie in Ihrem Browser das [Azure-Portal][portal].
	
2. Klicken Sie auf dem Blatt Ihrer Web-App auf **Alle Einstellungen**, klicken Sie auf **Skalieren**, klicken Sie dann auf **Upgrade from a Free plan to add instances and get better performance**.
	
	![Auswählen eines Plans][ChooseWHP]
	
4. Wählen Sie auf dem Blatt **Choose your pricing tier** entweder **Freigegeben** oder einen **Basic**-Modus aus, und klicken Sie auf **Auswählen**.
	
	Die Registerkarte **Benachrichtigungen** blinkt mit dem grün dargestellten Wort **ERFOLG**, sobald der Vorgang abgeschlossen ist. 
	
5. Schieben Sie den Balken **Instanz** von links nach rechts, um die Anzahl der Instanzen zu erhöhen, und klicken Sie in der Befehlsleiste auf **Speichern**. Im Modus **Freigegeben** ist die Instanzgrößenoption nicht verfügbar. Weitere Informationen zu den Instanzgrößen finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Microsoft Azure][vmsizes].
	
	![Instanzgröße für Basic-Modus][ChooseBasicInstances]
	
	Die Registerkarte **Benachrichtigungen** blinkt mit dem grün dargestellten Wort **ERFOLG**, sobald der Vorgang abgeschlossen ist. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Skalieren auf Standard- oder Premium-Modus
<!-- ================================= -->

> [AZURE.NOTE] Bevor Sie einen App Service-Plan in den Modus **Standard** oder **Premium** ändern, müssen Sie das für Ihr Microsoft Azure App Service-Abonnement geltende Ausgabenlimit entfernen. Andernfalls besteht das Risiko, dass Ihre Web-App nicht mehr verfügbar ist, wenn Sie Ihr Ausgabenlimit vor dem Ende der Abrechnungsperiode erreichen. Informationen zum Ändern oder Anzeigen der Optionen für Ihr Microsoft Azure App Service-Abonnement finden Sie unter [Microsoft Azure-Abonnements][azuresubscriptions].

1. Gehen Sie für die Skalierung zum Modus **Standard** oder **Premium** zunächst vor wie bei der Skalierung auf **Freigegeben** oder **Basic**, wählen Sie dann unter **Choose your pricing tier** einen der Modi **Standard** oder **Premium**, und klicken Sie dann auf **Auswählen**. 
	
	Die Registerkarte **Benachrichtigungen** blinkt mit dem grün dargestellten Wort **ERFOLG**, sobald der Vorgang abgeschlossen ist, und der **Autoscale Mode** wird aktiviert.
	
	![Skalieren im Standard- oder Premium-Modus][ScaleStandard]
	
	Sie können weiterhin den Balken **Instanz** verschieben, um manuell für weitere Instanzen zu skalieren, genau wie weiter oben für den **Basic**-Modus beschrieben. Hier erfahren Sie jedoch, wie Sie den **Autoscale Mode** verwenden. 
	
2. Wählen Sie im **Autoscale Mode** die Option **Leistung**, um die automatische Skalierung anhand der Leistungskennzahlen durchzuführen.
	
	![Festlegen des Modus für automatische Skalierung nach Leistung][Autoscale]
	
3. Verschieben Sie unter **Instance Range** die beiden Schieberegler, um die minimale und maximale Anzahl der Instanzen für die automatische Skalierung des App Service-Plans zu definieren. Verschieben Sie für dieses Lernprogramm den Schieberegler für das Maximum auf **6** Instanzen.
	
4. Klicken Sie in der Befehlsleiste auf **Speichern**.
	
4. Klicken Sie unter **Target Metrics** auf **>**, um Regeln für die automatische Skalierung für die Standardmetrik zu konfigurieren.  
	
	![Festlegen von Zielmetriken][SetTargetMetrics]
	
	Sie können Regeln für die automatische Skalierung für verschiedene Leistungsmetriken festlegen, einschließlich CPU, Arbeitsspeicher, Datenträgerwarteschlange, HTTP-Warteschlange und Datenfluss. Hier konfigurieren Sie eine automatische Skalierung für den CPU-Prozentsatz, die Folgendes ausführt:
	
	- Skalieren um 1 Instanz nach oben, wenn die CPU in den letzten 10 Minuten über 70 % liegt
	- Skalieren um 3 Instanzen nach oben, wenn die CPU in den letzten 5 Minuten über 90 % liegt
	- Skalieren um 1 Instanz nach unten, wenn die CPU in den letzten 30 Minuten über 50 % liegt 
	
	
4. Lassen Sie die Dropdownliste **Metrik** auf **CPU-Prozentsatz** eingestellt.
	
5. Konfigurieren Sie in **Scale up rules** die erste Regel, indem Sie **Bedingung** auf **Größer**, **Schwellenwert** auf **70** (%), **Over past** auf **10** (Minuten), **Scale up by** auf **1** (Instanzen) und **Cool down** auf **10** (Minuten) festlegen. 
	
	![Festlegen der ersten Regel für automatische Skalierung][SetFirstRule]
	
	>[AZURE.NOTE] Die Einstellung **Cool down** gibt an, wie lange diese Regel nach der vorherigen Skalierungsaktion bis zur nächsten Skalierung warten soll.
	
6. Klicken Sie auf **Add Scale Up Rule**, und konfigurieren Sie die zweite Regel, indem Sie **Bedingung** auf **Größer**, **Schwellenwert** auf **90** (%), **Over past** auf **1** (Minuten), **Scale up by** auf **3** (Instanzen) und **Cool down** auf **1** (Minuten) festlegen.
	
	![Festlegen der zweiten Regel für automatische Skalierung][SetSecondRule]
	
5. Konfigurieren Sie in **Scale down rules** die dritte Regel, indem Sie **Bedingung** auf **Kleiner**, **Schwellenwert** auf **50** (%), **Over past** auf **30** (Minuten), **Scale down by** auf **1** (Instanzen) und **Cool down** auf **60** (Minuten) festlegen. 
	
	![Festlegen der dritten Regel für automatische Skalierung][SetThirdRule]
	
7. Klicken Sie in der Befehlsleiste auf **Speichern**. Die Regel für automatische Skalierung sollte jetzt auf dem Blatt **Skalierung** wiedergegeben werden. 
	
	![Ergebnisse der Regeln für automatisches Skalieren][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Skalierung einer mit der Web-App verbundenen SQL-Serverdatenbank
Wenn Sie bereits eine oder mehrere SQL Server-Datenbanken mit Ihrer Web-App verknüpft haben (unabhängig vom Modus des Anwendungsdiensts Plan), können Sie dies schnell Ihren Anforderungen entsprechend skalieren.

1. Um einer der verknüpften Datenbanken zu skalieren, öffnen Sie das Blatt Ihrer Web-App im [Azure-Portal][portal]. Klicken Sie in der reduzierbaren Dropdownliste **Essentials** auf den Link **Ressourcengruppe**. Klicken Sie dann im Bereich **Zusammenfassung** des Blatts "Ressourcengruppe" auf eine der verknüpften Datenbanken.

	![Verknüpfte Datenbanken][ResourceGroup]
	
2. Klicken Sie auf dem Blatt der verknüpften SQL-Datenbank auf den Bereich **Pricing tier**, wählen Sie eine Ebene basierend auf Ihren Leistungsanforderungen aus, und klicken Sie auf **Auswählen**. 
	
	![Skalieren Ihrer SQL-Datenbank][ScaleDatabase]
	
3. Sie können die geografische Replikation einrichten, um die die Funktionen für hohe Verfügbarkeit und Notfallwiederherstellung der SQL-Datenbank zu erhöhen. Klicken Sie hierzu auf den Bereich **Georeplikation**.
	
	![Einrichten der Georeplikation für SQL-Datenbanken][GeoReplication]

<a name="devfeatures"></a>
## Entwicklerfunktionen
Je nach Modus der Web-App stehen die folgenden entwicklungsbezogenen Funktionen zur Verfügung:

### Bitness ###

- Die Modi **Basic**, **Standard** und **Premium** unterstützen 64-Bit- und 32-Bit-Anwendungen.
- Die Planmodi **Kostenlos** und **Freigegeben** unterstützen lediglich 32-Bit-Anwendungen.

### Debugger-Support ###

- Debugger-Support ist für die Modi **Kostenlos**, **Freigegeben** und **Basic** mit 1 gleichzeitigen Verbindung pro App Service-Plan verfügbar.
- Debugger-Support ist für die Modi **Standard** und **Premium** mit 5 gleichzeitigen Verbindungen pro App Service-Plan verfügbar.

<a name="OtherFeatures"></a>
## Andere Funktionen

### Webendpunkt-Überwachung ###

- Die Webendpunkt-Überwachung ist in den Modi **Basic**, **Standard** und **Premium** verfügbar. Weitere Informationen zur Web-Endpunktüberwachung finden Sie unter [Überwachen von Websites](web-sites-monitor.md)..

- Ausführliche Informationen zu den anderen Funktionen in den App Service-Plänen, darunter Preisgestaltung und allgemein interessante Funktionen (auch für Entwickler), finden Sie unter [Websites Preise](/pricing/details/web-sites/)..

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

<a name="Next Steps"></a>	
## Nächste Schritte

- Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](/pricing/free-trial/).
- Informationen zu Preisen, Support und SLAs erhalten Sie unter den folgenden Links.
	
	[Preisdetails zur Datenübertragung](/pricing/details/data-transfers/)
	
	[Microsoft Azure-Supportpläne](/support/plans/)
	
	[Vereinbarungen zum Servicelevel](/support/legal/sla/)
	
	[Preisdetails für SQL-Datenbanken](/pricing/details/sql-database/)
	
	[Größen virtueller Computer und Cloud-Dienste für Microsoft Azure][vmsizes]
	
	[App-Service-Preisdetails](/pricing/details/web-sites/)
	
	[Anwendungsdiensts Preisdetails - SSL-Verbindungen](/pricing/details/web-sites/#ssl-connections)

- Informationen zu den Best Practices für Azure App Service, einschließlich Aufbau einer skalierbaren, robusten Architektur, finden Sie unter [Best Practices: Azure App Service Web Apps (in englischer Sprache)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Videos zum Skalieren von Web-Apps:
	
	- [Wann sollte man Azure-Websites skalieren? - Mit Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - Mit Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Wie sollte man Azure-Websites skalieren? - Mit Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[Portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!--HONumber=49-->