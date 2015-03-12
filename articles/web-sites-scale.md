<properties 
	pageTitle="Skalieren von Websites" 
	description="erforderlich" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Skalieren von Websites

Zur Steigerung des Durchsatzes und der Leistung Ihrer Websites auf Microsoft Azure können Sie im Azure-Verwaltungsportal den Webhostingplan-Modus von "Kostenlos" auf "Freigegeben", "Basic" oder "Standard" skalieren. 

Das Skalieren von Azure-Websites umfasst zwei zusammenhängende Vorgänge: Ändern des Webhostingplan-Modus auf einen höheren Servicelevel und Konfigurieren bestimmter Einstellungen nach dem Wechsel zum höheren Servicelevel. Beide Themen werden in diesem Artikel erläutert. Höhere Servicelevel wie der Standardmodus bieten bessere Stabilität und Flexibilität bei der Festlegung, wie Ressourcen auf Azure verwendet werden.

Die Modusänderung und die Konfiguration erfolgt ganz einfach über die Registerkarte "Skalieren" des Verwaltungsportals. Sie können nach Bedarf hoch- oder runterskalieren. Diese Änderungen werden innerhalb von Sekunden angewendet und wirken sich auf alle Websites im Webhostingplan aus. Dafür muss weder der Code geändert noch Anwendungen erneut bereitgestellt werden.

Informationen zu Webhostingplänen finden Sie unter [Was ist ein Webhostingplan?](http://azure.microsoft.com/documentation/articles/web-sites-web-hosting-plan-overview/) und [Webhostingpläne für Azure-Websites: ausführliche Übersicht](http://www.azure.microsoft.com/de-de/Documentation/Articles/azure-web-sites-web-hosting-plans-in-depth-overview/). Informationen zur Preisgestaltung und zu den Funktionen der einzelnen Webhostingpläne finden Sie unter [Preisdetails zu Websites](http://azure.microsoft.com/pricing/details/web-sites/).  

> [AZURE.NOTE] Bevor Sie den Webhostingplan-Modus einer Website von **Kostenlos** in **Basic** oder **Standard** ändern, müssen Sie zunächst das für Ihr Azure-Websites-Abonnement geltende Ausgabenlimit aufheben. Informationen zum Ändern oder Anzeigen der Optionen für Ihr Microsoft Azure-Websites-Abonnement finden Sie unter [Microsoft Azure-Abonnements][azuresubscriptions].

Themen in diesem Artikel:

- [Skalierung auf Shared- oder Basic-Planmodus](#scalingsharedorbasic)
- [Skalierung auf Standard-Planmodus](#scalingstandard)
- [Skalierung einer mit der Website verbundenen SQL-Serverdatenbank](#ScalingSQLServer)
- [Entwicklerfunktionen](#devfeatures)
- [Andere Funktionen](#OtherFeatures)

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Skalierung auf Shared- oder Basic-Planmodus
<!-- ===================================== -->

1. Öffnen Sie in Ihrem Browser das [Verwaltungsportal][portal].
	
2. Wählen Sie auf der Registerkarte **Websites** Ihre Website aus.
	
	![Selecting a website][SelectWebsite]
	
3. Klicken Sie auf die Registerkarte **Skalieren**.
	
	![The scale tab][SelectScaleTab]
	
4. Wählen Sie im Abschnitt **Webhostingplan-Modus** entweder **FREIGEGEBEN** oder **BASIC** aus. In dieser Beispielabbildung wird "Basic" verwendet.
	
	![Choose Web Hosting Plan][ChooseWHP]
	
	Im Abschnitt **Webhostingplan-Sites** wird eine kurze Liste der Websites im aktuellen Plan angezeigt. Alle Websites im aktuellen Plan werden in den von Ihnen ausgewählten Webhostingplan-Modus übernommen.
	
5. Klicken Sie im Abschnitt **Kapazität** auf **Instanzgröße**. Die verfügbaren Optionen sind **Klein**, **Mittel** und **Groß**. Im Modus "Freigegeben" ist die Instanzgrößenoption nicht verfügbar. Weitere Informationen zu den Instanzgrößen finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Microsoft Azure][vmsizes].
	
	![Instance size for Basic mode][ChooseBasicInstanceSize]
	
6. Wählen mithilfe des Schiebereglers die gewünschte **Instanzanzahl**.
	
	![Instance count for Basic mode][ChooseBasicInstanceCount]
	
7. Klicken Sie auf der Befehlsleiste auf **Speichern**. 
	
	![Save button][SaveButton]
 	
	> [AZURE.NOTE] Sie können die Einstellungen für **Webhostingplan**, **Instanzgröße** und **Instanzanzahl** auch separat speichern.
	
8. Sie werden in einer Bestätigungsmeldung darauf hingewiesen, dass Websites im selben Webhostingplan wie die aktuelle Website ebenfalls in den neuen Modus übernommen werden. Klicken Sie auf **Ja**, um die Änderung abzuschließen. 
	
	In diesem Beispiel wurde der Planmodus in **Basic** geändert:
	
	![Plan change complete][BasicComplete]
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Skalierung auf Standard-Planmodus
<!-- ================================= -->

> [AZURE.NOTE] Bevor Sie einen Webhostingplan in den Modus "Standard" ändern, müssen Sie das für Ihr Microsoft Azure-Websites-Abonnement geltende Ausgabenlimit entfernen. Andernfalls besteht das Risiko, dass Ihre Website nicht mehr verfügbar ist, wenn Sie Ihr Ausgabenlimit vor dem Ende der Abrechnungsperiode erreichen. Informationen zum Ändern oder Anzeigen der Optionen für Ihr Microsoft Azure-Websites-Abonnement finden Sie unter [Microsoft Azure-Abonnements][azuresubscriptions].

1. Befolgen Sie für die Skalierung auf "Standard" dieselben anfänglichen Schritte wie bei der Skalierung auf "Freigegeben" oder "Basic", und wählen Sie dann **Standard** als **Webhostingplan-Modus** aus. 
	
	![Choose Standard Plan][ChooseStandard]
	
	Wie zuvor wird im Abschnitt **Webhostingplan-Sites** eine kurze Liste der Websites im aktuellen Plan angezeigt. In diesem Fall werden alle Websites im aktuellen Plan in den Standardmodus übernommen.
	
2. Nach der Auswahl von **Standard** wird der Abschnitt **Kapazität** erweitert, um die Optionen für **Instanzgröße** und **Instanzanzahl** anzuzeigen, die auch im Basic-Modus verfügbar sind. Die Optionen **Skalierungseinstellungen für Zeitplan bearbeiten** und **Nach Metrik skalieren** stehen nur im Standardmodus zur Verfügung.
	
	![Capacity section in Standard][CapacitySectionStandard]
	
3. Konfigurieren Sie die **Instanzgröße**. Die verfügbaren Optionen sind **Klein**, **Mittel** und **Groß**.
	
	![Choose instance size][ChooseInstanceSize]
	
	Weitere Informationen zu den Instanzgrößen finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Microsoft Azure][vmsizes].
	
4. Wenn Sie Ressourcen basierend auf Tages- oder Nachtzeiten, Wochentagen oder Wochenende und/oder bestimmten Daten und Zeiten automatisch skalieren möchten, wählen Sie **Zeiten für Zeitplan einrichten** in der Option **Skalierungseinstellungen für Zeitplan bearbeiten** aus.
	
	![Set up schedule times][SetUpScheduleTimesButton]
	
5. Das Dialogfeld **Zeiten für Zeitplan einrichten** enthält eine Reihe von nützlichen Konfigurationsoptionen.
	
	![SetUpScheduleTimesDialog][SetUpScheduleTimesDialog]
	
6. Wählen Sie unter **Zeitplanserie** die Option **Verschiedene Skalierungseinstellungen für Tag und Nacht** und/oder **Wochentag und Wochenende** aus, um Ressourcen basierend auf unterschiedlichen Zeitplänen für Tages- oder Nachtzeiten und/oder Wochentagen bzw. Wochenenden zu skalieren.
	
	> [AZURE.NOTE] Für diese Funktion beginnt das Wochenende am Ende des Freitags (standardmäßig um 20:00 Uhr) und endet mit Beginn des Montags (standardmäßig um 8:00 Uhr). Das Wochenendprofil verwendet denselben Start- und Endtag, den Sie in der Einstellung **Zeit** angeben.
	
7. Wählen Sie unter **Zeit** eine Start- und Endzeit für den Tag in halbstündigen Schritten sowie eine Zeitzone aus. Standardmäßig beginnt der Tag um 8 Uhr und endet um 20 Uhr. Die Sommerzeit wird für die ausgewählte Zeitzone berücksichtigt. 
	
8. Unter **Spezifische Datumsangaben** können Sie benannte Zeitrahmen erstellen, in denen Sie die Ressourcen skalieren möchten. Beispielsweise können Sie zusätzliche Ressourcen für einen Schlussverkauf oder für ein Ereignis bereitstellen, für das Sie stark erhöhten Webdatenverkehr erwarten.
	
9. Nachdem Sie Ihre Auswahl getroffen haben, klicken Sie auf **OK**, um das Dialogfeld **Zeiten für Zeitplan einrichten** zu schließen.
	
10.   Im Dialogfeld **Skalierungseinstellungen für Zeitplan bearbeiten** werden jetzt konfigurierbare Zeitpläne oder Ereignisse basierend auf den von Ihnen vorgenommenen Änderungen angezeigt. Wählen Sie einen der wiederholten Zeitpläne oder bestimmte Daten zur Konfiguration aus. 
	
	![Edit scale settings for schedule][EditScaleSettingsForSchedule]
	
	Sie können jetzt die Optionen **Nach Metrik skalieren** und **Instanzgröße** verwenden, um die Skalierung der Ressourcen für jeden ausgewählten Zeitplan anzupassen. 
	
11.  Um die Anzahl der Instanzen, die Ihre Website bei Laständerungen verwendet, dynamisch anzupassen, aktivieren Sie die Option **Nach Metrik skalieren** durch Auswahl von **CPU**.
	
	![Scale By Metric][ScaleByMetric]
	
	Das Diagramm zeigt die Anzahl der Instanzen an, die in der letzten Woche verwendet wurden. Sie können das Diagramm zur Überwachung der Skalierungsaktivität verwenden.
	
12. **Nach Metrik skalieren** ändert die Funktion **Instanzanzahl**, sodass Sie die Mindest- und Höchstanzahl virtueller Computer festlegen können, die für die automatische Skalierung verwendet werden soll. Azure wird das festgelegte Limit nie über- bzw. unterschreiten.
	
	![Instance count][InstanceCount]
	
13. **Nach Metrik skalieren** aktiviert außerdem die Option **Ziel CPU**, mit der Sie einen Zielbereich für die CPU-Auslastung angeben können. Dieser Bereich stellt die durchschnittliche CPU-Auslastung der Website dar. Microsoft Azure fügt Standardinstanzen hinzu bzw. entfernt sie, um die Website in diesem Bereich zu halten.
	

	
	**Hinweis**: Wenn **Nach Metrik skalieren** aktiviert ist, prüft Microsoft Azure die CPU der Website alle fünf Minuten und fügt bei Bedarf zu diesem Zeitpunkt Instanzen hinzu. Ist die CPU-Auslastung niedrig, entfernt Microsoft Azure alle zwei Stunden Instanzen, um sicherzustellen, dass die Website leistungsfähig bleibt. Im Allgemeinen ist eine Mindest-Instanzanzahl von 1 ausreichend. Wenn Sie jedoch plötzliche Spitzenlasten auf der Website erwarten, legen Sie die Mindestanzahl der Instanzen so fest, dass sie die Last bewältigen können. Tritt beispielsweise eine unerwartete Spitzenlast während des 5-Minuten-Intervalls auf, bevor Microsoft Azure die CPU-Auslastung erneut prüft, kann es vorkommen, dass die Website während dieser Zeit nicht reagiert. Wenn Sie also plötzliche große Datenverkehrsmengen erwarten, stellen Sie die Mindest-Instanzanzahl höher ein, um diese Spitzenlasten abzufangen. 
	
14. Nachdem Sie die Änderungen an den Objekten in der Liste **Skalierungseinstellungen für Zeitplan bearbeiten** vorgenommen haben, klicken Sie in der Befehlsleiste am unteren Rand der Seite auf **Speichern**, um alle Zeitplaneinstellungen gleichzeitig zu speichern (so müssen Sie nicht jeden Zeitplan einzeln speichern).

> [AZURE.NOTE] [Im Azure-Vorschauportal](https://portal.azure.com/) können Sie nicht nur nach CPU-Prozentsatz skalieren, sondern auch nach den anderen Metriken für die Arbeitsspeicherauslastung, Warteschlangenlänge des Datenträgers, HTTP-Warteschlangenlänge, eingehende Daten und ausgehende Daten. Außerdem besteht die Möglichkeit, eine oder mehrere Regeln für das Hoch- und Herunterskalieren zu erstellen, die Ihnen noch mehr individuelle Kontrolle über die Skalierung bieten. Weitere Informationen finden Sie unter [Skalieren von Websites](http://azure.microsoft.com/documentation/articles/insights-how-to-scale/) in der Dokumentation zum Azure-Vorschauportal.

<a name="ScalingSQLServer"></a>
##Skalierung einer mit der Website verbundenen SQL-Serverdatenbank	
Wenn mit Ihrer Website ein oder mehrere SQL Server-Datenbanken verknüpft sind (unabhängig vom Webhostingplan-Modus), werden diese im Abschnitt **Verknüpfte Ressourcen** unten auf der Seite "Skalieren" aufgeführt.

1. Um eine dieser Datenbanken zu skalieren, klicken Sie im Abschnitt **Verknüpfte Ressourcen** auf den Link **Skalierung für diese Datenbank verwalten** neben dem Namen der Datenbank.
	
	![Linked database][LinkedResources]
	
2. Über den Link gelangen Sie zur SQL Server-Registerkarte im Azure-Verwaltungsportal, wo Sie **Edition** und **Maximale Größe** für die Datenbank konfigurieren können:
	
	![Scale your SQL Server database][ScaleDatabase]
	
	Für **Edition** wählen Sie **BASIC**, **STANDARD** oder **PREMIUM** abhängig von der Speicherkapazität, die Sie benötigen. Zur weiteren Entwicklung der Editionen **Web** und **BUSINESS** lesen Sie die [häufig gestellte Fragen zu Web und Business Edition Sunset](http://msdn.microsoft.com/library/azure/dn741330.aspx).
	
	Der ausgewählte Wert für **Maximale Größe** gibt die Obergrenze für die Datenbank an. Die Datenbankgebühren basieren auf der tatsächlich gespeicherten Datenmenge, daher wirkt sich eine Änderung der Eigenschaft **Maximale Größe** nicht unmittelbar auf die Datenbankgebühren aus. Weitere Informationen finden Sie unter [Konten und Abrechnung in Microsoft Azure SQL-Datenbanken][SQLaccountsbilling].

<a name="devfeatures"></a>
## Entwicklerfunktionen
Je nach Webhostingplan-Modus stehen die folgenden entwicklungsbezogenen Funktionen zur Verfügung:

**Bitness**

- Der Basic- und Standard-Planmodus unterstützt 64-Bit- und 32-Bit-Anwendungen.
- Der Free- und Shared-Planmodus unterstützt nur 32-Bit-Anwendungen.

**Debugger-Support**

- Debugger-Support ist für die Webhostingplan-Modi "Kostenlos", "Freigegeben" und "Basic" mit einer gleichzeitigen Verbindung pro Anwendung erhältlich.
- Debugger-Support ist für den Webhostingplan-Modus "Standard" mit fünf gleichzeitigen Verbindungen pro Anwendung erhältlich.

<a name="OtherFeatures"></a>
## Andere Funktionen

**Webendpunkt-Überwachung**

- Die Webendpunkt-Überwachung ist in den Webhostingplan-Modi "Basic" und "Standard" verfügbar. Weitere Informationen zur Webendpunkt-Überwachung finden Sie unter [Überwachen von Websites](http://azure.microsoft.com/documentation/articles/web-sites-monitor/).

- Ausführliche Informationen zu den anderen Funktionen in den Webhostingplänen, darunter Preisgestaltung und allgemein interessante Funktionen (auch für Entwickler), finden Sie unter [Preisdetails zu Websites](http://azure.microsoft.com/pricing/details/web-sites/).

<a name="Next Steps"></a>	
## Nächste Schritte

- Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

- Informationen zu Preisen, Support und SLAs erhalten Sie unter den folgenden Links.
	
	[Preisdetails zur Datenübertragung](http://azure.microsoft.com/pricing/details/data-transfers/)
	
	[Microsoft Azure-Supportpläne](http://azure.microsoft.com/support/plans/)
	
	[Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/)
	
	[Preisdetails für SQL-Datenbanken](http://azure.microsoft.com/pricing/details/sql-database/)
	
	[Größen virtueller Computer und Cloud-Dienste für Microsoft Azure][vmsizes]
	
	[Preisdetails zu Websites](http://azure.microsoft.com/pricing/details/web-sites/)
	
	[Preisdetails zu Websites - SSL-Verbindungen](http://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)

- Informationen zu den Best Practices für Azure-Websites, einschließlich Aufbau einer skalierbaren, robusten Architektur, finden Sie unter [Best Practices: Microsoft Azure-Websites (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Videos zur Skalierung von Azure-Websites:
	
	[Wann sollte man Azure-Websites skalieren? - Mit Stefan Schackow](http://azure.microsoft.com/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - Mit Stefan Schackow](http://azure.microsoft.com/documentation/videos/auto-scaling-azure-web-sites/)

	[Wie sollte man Azure-Websites skalieren? - Mit Stefan Schackow](http://azure.microsoft.com/documentation/videos/how-azure-web-sites-scale/)



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://manage.windowsazure.com/

<!-- IMAGES -->
[SelectWebsite]: ./media/web-sites-scale/01SelectWebSite.png
[SelectScaleTab]: ./media/web-sites-scale/02SelectScaleTab.png

[ChooseWHP]: ./media/web-sites-scale/03aChooseWHP.png
[ChooseBasicInstanceSize]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
[ChooseBasicInstanceCount]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ChooseStandard]: ./media/web-sites-scale/07ChooseStandard.png
[CapacitySectionStandard]: ./media/web-sites-scale/08CapacitySectionStandard.png
[ChooseInstanceSize]: ./media/web-sites-scale/09ChooseInstanceSize.png
[SetUpScheduleTimesButton]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
[SetUpScheduleTimesDialog]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
[EditScaleSettingsForSchedule]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
[ScaleByMetric]: ./media/web-sites-scale/13ScaleByMetric.png
[InstanceCount]: ./media/web-sites-scale/14InstanceCount.png
[TargetCPU]: ./media/web-sites-scale/15TargetCPU.png
[LinkedResources]: ./media/web-sites-scale/16LinkedResources.png
[ScaleDatabase]: ./media/web-sites-scale/17ScaleDatabase.png




<!--HONumber=42-->
