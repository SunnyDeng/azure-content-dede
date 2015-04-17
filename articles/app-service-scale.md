<properties 
	pageTitle="Skalieren einer Web-App in Azure App Service" 
	description="Erfahren Sie, wie Sie eine Web-App in Azure App Service vertikal und horizontal skalieren sowie eine automatische Skalierung durchführen." 
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
	ms.date="03/23/2015" 
	ms.author="cephalin"/>

# Skalieren einer Web-App in Azure App Service #

Zur Verbesserung von Leistung und Durchsatz Ihrer Web-Apps in Microsoft Azure und zur Aktivierung von mehr Funktionen zur Erfüllung Ihrer Geschäftsanforderungen können Sie mit dem Azure-Verwaltungsportal Ihren App Service-Plan vom Modus "Kostenlos" in den Modus "Shared", "Basic", "Standard" oder "Premium" (Vorschau) ändern. 

Das Skalieren von Azure-Web-Apps umfasst zwei zusammenhängende Vorgänge: Ändern des Modus für den App Service-Plan auf einen höheren Servicelevel und Konfigurieren bestimmter Einstellungen nach dem Wechsel zum höheren Servicelevel. Beide Themen werden in diesem Artikel erläutert. Höhere Servicelevel wie der Standardmodus bieten bessere Stabilität und Flexibilität bei der Festlegung, wie Ressourcen auf Azure verwendet werden.

Die Modusänderung und die Konfiguration erfolgt ganz einfach über die Registerkarte "Skalieren" des Verwaltungsportals. Sie können nach Bedarf hoch- oder runterskalieren. Diese Änderungen werden innerhalb von Sekunden wirksam und auf alle Web-Apps in Ihrem Serviceplan angewendet. Dafür muss weder der Code geändert noch Anwendungen erneut bereitgestellt werden.

Weitere Informationen zu App Service-Plänen (vormals "Webhostingpläne") finden Sie unter [Was ist ein App Service-Hostingplan?](web-sites-web-hosting-plan-overview.md) und [Azure App Service-Hostingpläne - Detaillierte Übersicht](azure-web-sites-web-hosting-plans-in-depth-overview.md). Informationen zur Preisgestaltung und zu den Funktionen der einzelnen App Service-Pläne finden Sie unter den [App Service-Preisdetails](http://http://azure.microsoft.com/pricing/details/web-sites/).  

> [AZURE.NOTE] Vor dem Wechsel einer Web-App vom Modus **Kostenlos** in den Modus **Basic** oder **Standard** sollten Sie zunächst das für Ihr Azure App Service-Abonnement geltende Ausgabenlimit aufheben. Informationen zum Anzeigen oder Ändern von Optionen für Ihr Microsoft Azure App Service-Abonnement finden Sie unter [Microsoft Azure-Abonnement][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Skalieren auf den App Service-Planmodus "Shared" oder "Basic"
<!-- ===================================== -->

1. Öffnen Sie in Ihrem Browser das [Azure-Portal][portal].
	
2. Klicken Sie im Blatt Ihrer Web-App auf **Alle Einstellungen**, klicken Sie dann auf **Skalieren** und anschließend auf **Upgrade von einem kostenlosen Plan, um Instanzen hinzuzufügen und die Leistung zu steigern**.
	
	![Choose Hosting Plan][ChooseWHP]
	
4. Wählen Sie im Blatt **Wählen Sie Ihre Preisstufe** entweder **Shared** oder den Modus **Basic**, und klicken Sie dann auf **Auswählen**.

	Auf der Registerkarte **Benachrichtigungen** wird in Grün der Hinweis **ERFOLGREICH** angezeigt, sobald der Vorgang abgeschlossen wurde. 
	
5. Bewegen Sie den Schieberegler **Instanz** von links nach rechts, um die Anzahl von Instanzen zu erhöhen, und klicken Sie in der Befehlsleiste auf **Speichern**. Die Option zur Festlegung der Instanzengröße steht im Modus **Shared** nicht zur Verfügung. Weitere Informationen zu den Instanzengrößen finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Azure][vmsizes].
	
	![Instance size for Basic mode][ChooseBasicInstances]
	
	Auf der Registerkarte **Benachrichtigungen** wird in Grün der Hinweis **ERFOLGREICH** angezeigt, sobald der Vorgang abgeschlossen wurde. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Skalieren auf den App Service-Planmodus "Standard"
<!-- ================================= -->

> [AZURE.NOTE] Bevor Sie einen App Service-Plan vom Modus **Kostenlos** in den Modus **Standard** ändern, sollten Sie das für Ihr Microsoft Azure App Service-Abonnement geltende Ausgabenlimit entfernen. Andernfalls besteht das Risiko, dass Ihre Website nicht mehr verfügbar ist, wenn Sie Ihr Ausgabenlimit vor dem Ende der Abrechnungsperiode erreichen. Informationen zum Anzeigen oder Ändern von Optionen für Ihr Microsoft Azure App Service-Abonnement finden Sie unter [Microsoft Azure-Abonnement][azuresubscriptions].

1. Führen Sie zum Skalieren auf den Modus **Standard** die gleichen ersten Schritte wie beim Skalieren auf **Shared** oder **Basic** aus. Wählen Sie dann unter **Wählen Sie Ihre Preisstufe** die Option **Standard**, und klicken Sie auf **Auswählen**. 
	
	Auf der Registerkarte **Benachrichtigungen** wird in Grün der Hinweis **ERFOLGREICH** angezeigt, sobald der Vorgang abgeschlossen wurde, und der **Autoskalierungsmodus** wird aktiviert.

	![Scale in Standard Mode][ScaleStandard]

	Sie können wie im Modus **Basic** (siehe oben) weiterhin den Schieberegler **Instanz** bewegen, um manuell die Anzahl von Instanzen zu erhöhen. In diesem Abschnitt wird jedoch die Verwendung des **Autoskalierungsmodus** erläutert. 

2. Wählen Sie im **Autoskalierungsmodus** die Einstellung **Leistung**, um eine automatische Skalierung basierend auf Leistungsmetriken durchzuführen.
	
	![Autoscale Mode set to Performance][Autoscale]
	
3. Bewegen Sie unter **Instanzenbereich** die zwei Schieberegler, um die minimale und maximale Anzahl von Instanzen bei der automatischen Skalierung für den App Service-Plan zu definieren. In diesem Lernprogramm wird der Schieberegler für die maximale Instanzenanzahl auf **6** festgelegt.

4. Klicken Sie in der Befehlsleiste auf **Speichern**.

4. Klicken Sie unterhalb von **Zielmetriken** auf **>**, um Regeln für die automatische Skalierung für die Standardmetrik zu konfigurieren.  
	
	![Set Target Metrics][SetTargetMetrics]
	
	Sie können Regeln für die automatische Skalierung konfigurieren, die für verschiedene Leistungsmetriken gelten, darunter z. B. CPU, Arbeitsspeicher, Datenträgerwarteschlange, HTTP-Warteschlange und Datenfluss. In diesem Beispiel konfigurieren Sie die automatische Skalierung für den CPU-Prozentsatz in folgender Weise:

	- Skalierung nach oben um 1 Instanz, wenn der CPU-Prozentsatz in den letzten 10 Minuten über 70 % liegt
	- Skalierung nach oben um 3 Instanzen, wenn der CPU-Prozentsatz in den letzten 5 Minuten über 90 % liegt
	- Skalierung nach unten um 1 Instanz, wenn der CPU-Prozentsatz in den letzten 30 Minuten unter 50 % liegt 


4. Behalten Sie im Dropdownfeld **Metrik** die Einstellung **CPU-Prozentsatz** bei.
	
5. Konfigurieren Sie in **Regeln für Skalierung nach oben** die erste Regel durch Festlegen der Einstellung **Bedingung** auf **Größer als**, **Schwellenwert** auf **70** (%), **In den letzten** auf **10** (Minuten), **Skalierung nach oben um** auf **1** (Instanz) und **Cooldown** auf **10** (Minuten). 
	
	![Set First Autoscale Rule][SetFirstRule]

	>[AZURE.NOTE] Die Einstellung **Cooldown** gibt an, wie lange diese Regel nach einer Skalierungsaktion beibehalten werden soll, bis eine erneute Skalierung durchgeführt wird.
	
6. Konfigurieren Sie in **Regel für Skalierung nach oben hinzufügen**, und konfigurieren Sie die zweite Regel durch Festlegen der Einstellung **Bedingung** auf **Größer als**, **Schwellenwert** auf **90** (%), **In den letzten** auf **1** (Minuten), **Skalierung nach oben um** auf **3** (Instanz) und **Cooldown** auf **1** (Minuten).
	
	![Set Second Autoscale Rule][SetSecondRule]
	
5. Konfigurieren Sie in **Regeln für Skalierung nach unten** die erste Regel durch Festlegen der Einstellung **Bedingung** auf **Weniger als**, **Schwellenwert** auf **50** (%), **In den letzten** auf **30** (Minuten), **Skalierung nach oben um** auf **1** (Instanz) und **Cooldown** auf **60** (Minuten). 
	
	![Set Third Autoscale Rule][SetThirdRule]
	
7. Klicken Sie in der Befehlsleiste auf **Speichern**. Ihre Regel für die automatische Skalierung sollte jetzt im Blatt **Skalieren** angezeigt werden. 
	
	![Set Autoscale Rule Result][SetRulesFinal]
	
<a name="ScalingSQLServer"></a>
##Skalieren einer SQL Server-Datenbank mit Verbindung zu Ihrer Web-App
Wenn Sie eine oder mehrere SQL Server-Datenbanken mit Ihrer Web-App verknüpft haben (unabhängig vom App Service-Planmodus), können Sie diese basierend auf Ihren Anforderungen jederzeit schnell skalieren.

1. Öffnen Sie das Blatt für Ihre Web-App im[Azure-Portal][portal], um eine der verknüpften Datenbanken zu skalieren. Klicken Sie im reduzierbaren Dropdownfeld **Grundlagen** auf den Link **Ressourcengruppe**. Klicken Sie anschließend im Bereich **Zusammenfassung** des Blatts für die Ressourcengruppe auf eine der verknüpften Datenbanken.
	
	![Linked database][ResourceGroup]
	
2. Klicken Sie im Blatt für Ihre verknüpfte SQL-Datenbank auf den Bereich **Preisstufe**, wählen Sie basierend auf Ihren Leistungsanforderungen eine Preisstufe, und klicken Sie auf **Auswählen**. 
	
	![Scale your SQL Database][ScaleDatabase]
	
3. Sie können außerdem die geografische Replikation einrichten, um eine hohe Verfügbarkeit und eine Notfallwiederherstellung für Ihre SQL-Datenbank zu gewährleisten. Klicken Sie hierzu auf den Bereich **Georeplikation**.

	![Set up geo-replication for SQL Database][GeoReplication]

<a name="devfeatures"></a>
## Entwicklerfunktionen
Abhängig vom Serviceplanmodus der Web-App stehen die folgenden entwicklungsbezogenen Funktionen zur Verfügung:

### Bitness ###

- Der Basic- und Standard-Planmodus unterstützt 64-Bit- und 32-Bit-Anwendungen.
- Der Free- und Shared-Planmodus unterstützt nur 32-Bit-Anwendungen.

### Debugger-Support ###

- Debugger-Support ist für die App Service-Modi "Kostenlos", "Shared" und "Basic" mit 1 gleichzeitigen Verbindung pro Anwendung erhältlich.
- Debugger-Support ist für den App Service-Modus "Standard" mit 5 gleichzeitigen Verbindungen pro Anwendung erhältlich.

<a name="OtherFeatures"></a>
## Andere Funktionen

### Webendpunkt-Überwachung ###

- Die Webendpunkt-Überwachung ist in den App Service-Modi "Basic" und "Standard" verfügbar. Informationen zur Web-Endpunktüberwachung finden Sie unter [Überwachen von Web-Apps](web-sites-monitor.md).

- Ausführliche Informationen zu allen weiteren Funktionen in den App Service-Plänen, einschließlich Preisgestaltung und allgemein interessanten Funktionen (auch für Entwickler) finden Sie unter [App Service-Preisdetails](http://azure.microsoft.com/pricing/details/web-sites/).

<a name="Next Steps"></a>	
## Nächste Schritte

- Informationen zum Einstieg in Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

- Informationen zu Preisen, Support und SLAs erhalten Sie unter den folgenden Links.
	
	[Preisdetails zur Datenübertragung](http://azure.microsoft.com/pricing/details/data-transfers/)
	
	[Microsoft Azure-Supportpläne](http://azure.microsoft.com/support/plans/)
	
	[Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/)
	
	[Preisdetails für SQL-Datenbanken](http://azure.microsoft.com/pricing/details/sql-database/)
	
	[Größen virtueller Computer und Cloud-Dienste für Microsoft Azure][vmsizes]
	
	[App-Service-Preisdetails](http://azure.microsoft.com/pricing/details/web-sites/)
	
	[App-Service-Preisdetails - SSL-Verbindungen](http://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)

- Informationen zu den Best Practices für Azure App Service, einschließlich Aufbau einer skalierbaren, robusten Architektur, finden Sie unter [Best Practices: Microsoft Azure-Websites (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Videos zur Skalierung von Azure-Websites:
	
	[Wann sollte man Azure-Websites skalieren? - Mit Stefan Schackow](http://azure.microsoft.com/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - Mit Stefan Schackow](http://azure.microsoft.com/documentation/videos/auto-scaling-azure-web-sites/)

	[Wie sollte man Azure-Websites skalieren? - Mit Stefan Schackow](http://azure.microsoft.com/documentation/videos/how-azure-web-sites-scale/)



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

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