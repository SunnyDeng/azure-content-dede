<properties title="How to Scale Websites" pageTitle="How to Scale Websites" description="required" metaKeywords="scaling Azure websites" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Skalieren von Websites

Zur Steigerung des Durchsatzes und der Leistung Ihrer Websites auf Microsoft Azure können Sie im Azure-Verwaltungsportal den Webhostingplan-Modus von "Kostenlos" auf "Freigegeben", "Basic" oder "Standard" skalieren.

Das Skalieren von Azure-Websites umfasst zwei zusammenhängende Vorgänge: Ändern des Webhostingplan-Modus auf einen höheren Servicelevel und Konfigurieren bestimmter Einstellungen nach dem Wechsel zum höheren Servicelevel. Beide Themen werden in diesem Artikel erläutert. Höhere Servicelevel wie der Standardmodus bieten bessere Stabilität und Flexibilität bei der Festlegung, wie Ressourcen auf Azure verwendet werden.

Die Modusänderung und die Konfiguration erfolgt ganz einfach über die Registerkarte "Skalieren" des Verwaltungsportals. Sie können nach Bedarf hoch- oder runterskalieren. Diese Änderungen werden innerhalb von Sekunden angewendet und wirken sich auf alle Websites im Webhostingplan aus. Dafür muss weder der Code geändert noch Anwendungen erneut bereitgestellt werden.

Informationen zu Webhostingplänen finden Sie unter [Was ist ein Webhostingplan?][Was ist ein Webhostingplan?] und [Webhostingpläne für Azure-Websites: ausführliche Übersicht][Webhostingpläne für Azure-Websites: ausführliche Übersicht]. Informationen zur Preisgestaltung und zu den Funktionen der einzelnen Webhostingpläne finden Sie unter [Preisdetails zu Websites][Preisdetails zu Websites].

> [WACOM.NOTE] Bevor Sie den Webhostingplan-Modus einer Website von **Kostenlos** in **Basic** oder **Standard** ändern, müssen Sie zunächst das für Ihr Azure-Websites-Abonnement geltende Ausgabenlimit aufheben. Informationen zum Ändern oder Anzeigen der Optionen für Ihr Microsoft Azure-Websites-Abonnement finden Sie unter [Microsoft Azure-Abonnements][Microsoft Azure-Abonnements].

Themen in diesem Artikel:

-   [Skalierung auf Shared- oder Basic-Planmodus][Skalierung auf Shared- oder Basic-Planmodus]
-   [Skalierung auf Standard-Planmodus][Skalierung auf Standard-Planmodus]
-   [Skalierung einer mit der Website verbundenen SQL-Serverdatenbank][Skalierung einer mit der Website verbundenen SQL-Serverdatenbank]
-   [Entwicklerfunktionen][Entwicklerfunktionen]
-   [Andere Funktionen][Andere Funktionen]

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->

## Skalierung auf Shared- oder Basic-Planmodus

<!-- ===================================== -->

1.  Öffnen Sie das [Verwaltungsportal][Verwaltungsportal] in Ihrem Browser.

2.  Wählen Sie auf der Registerkarte **Websites** Ihre Website aus.

    ![Website auswählen][Website auswählen]

3.  Klicken Sie auf die Registerkarte **Scale**.

    ![Die Registerkarte "Skalierung"][Die Registerkarte "Skalierung"]

4.  Wählen Sie im Abschnitt **Web Hosting Plan Mode** entweder **SHARED** oder **BASIC** aus. In dieser Beispielabbildung wurde "Basic" verwendet.

    ![Webhostingplan auswählen][Webhostingplan auswählen]

    Im Abschnitt **Web Hosting Plan Sites** wird eine kurze Liste der Websites im aktuellen Plan angezeigt. Alle Websites im aktuellen Plan werden in den von Ihnen ausgewählten Webhostingplan-Modus übernommen.

5.  Klicken Sie im Abschnitt **Capacity** auf **Instance Size**. Die verfügbaren Optionen sind **Small**, **Medium** und **Large**. Im Shared-Modus ist die Instanzgrößenoption nicht verfügbar. Weitere Informationen zu den Instanzgrößen finden Sie unter [Größen virtueller Computer und Clouddienste für Windows Azure][Größen virtueller Computer und Clouddienste für Windows Azure].

    ![Instanzgröße für Basic-Modus][Instanzgröße für Basic-Modus]

6.  Verwenden Sie den Schieberegler, um die gewünschte Anzahl für **Instance Count** festzulegen.

    ![Anzahl der Instanzen für Basic-Modus][Anzahl der Instanzen für Basic-Modus]

7.  Klicken Sie in der Befehlsleiste auf **Save**.

    ![Schaltfläche "Save"][Schaltfläche "Save"]

    > [WACOM.NOTE] Sie können die Einstellungen für **Web Hosting Plan**, **Instance Size** und **Instance Count** auch separat speichern.

8.  Sie werden in einer Bestätigungsmeldung darauf hingewiesen, dass Websites im selben Webhostingplan wie die aktuelle Website ebenfalls in den neuen Modus übernommen werden. Klicken Sie auf **Yes**, um die Änderung abzuschließen.

    In diesem Beispiel wurde der Planmodus in **Basic** geändert:

    ![Planänderung abgeschlossen][Planänderung abgeschlossen]

<a name="scalingstandard"></a>
<!-- ================================= -->

## Skalierung auf Standard-Planmodus

<!-- ================================= -->

> [WACOM.NOTE] Bevor Sie einen Webhostingplan in den Modus "Standard" übernehmen, müssen Sie das für Ihr Microsoft Azure-Websites-Abonnement geltende Ausgabenlimit entfernen. Andernfalls besteht das Risiko, dass Ihre Website nicht mehr verfügbar ist, wenn Sie Ihr Ausgabenlimit vor dem Ende der Abrechnungsperiode erreichen. Informationen zum Ändern oder Anzeigen der Optionen für Ihr Microsoft Azure-Websites-Abonnement finden Sie unter [Microsoft Azure-Abonnements][Microsoft Azure-Abonnements].

1.  Befolgen Sie für die Skalierung auf Standard dieselben anfänglichen Schritte wie bei der Skalierung auf Shared oder Basic, und wählen Sie dann **Standard** als **Web Hosting Plan Mode** aus.

    ![Standardplan auswählen][Standardplan auswählen]

    Wie zuvor wird im Abschnitt **Web Hosting Plan Sites** eine kurze Liste der Websites im aktuellen Plan angezeigt. In diesem Fall werden alle Websites im aktuellen Plan in den Standardmodus übernommen.

2.  Nach der Auswahl von **Standard** wird der Abschnitt **Capacity** erweitert, um die Optionen für **Instance Size** und **Instance Count** anzuzeigen, die auch im Basic-Modus verfügbar sind. Die Optionen **Edit Scale Settings for Schedule** und **Scale by Metric** stehen nur im Standardmodus zur Verfügung.

    ![Kapazitätsabschnitt in Standard][Kapazitätsabschnitt in Standard]

3.  Konfigurieren Sie die **Instance Size**. Die verfügbaren Optionen sind **Small**, **Medium** und **Large**.

    ![Instanzgröße auswählen][Instanzgröße auswählen]

    Weitere Informationen zu den Instanzgrößen finden Sie unter [Größen virtueller Computer und Clouddienste für Windows Azure][Größen virtueller Computer und Clouddienste für Windows Azure].

4.  Wenn Sie Ressourcen basierend auf Tages- oder Nachtzeiten, Wochentagen oder Wochenende und/oder bestimmten Daten und Zeiten automatisch skalieren möchten, wählen Sie **Set up schedule times** unter der Option **Edit Scale Settings for Schedule** aus.

    ![Zeitpläne einrichten][Zeitpläne einrichten]

5.  Das Dialogfeld **Set up schedule times** enthält eine Reihe von nützlichen Konfigurationsoptionen.

    ![Dialogfeld für Zeitpläne einrichten][Dialogfeld für Zeitpläne einrichten]

6.  Wählen Sie unter **Recurring Schedules** die Option **Differing scale between Day and Night** und/oder **Differing Scale between Weekday and Weekend** aus, um Ressourcen basierend auf unterschiedlichen Zeitplänen für Tages- oder Nachtzeiten und/oder Wochentagen bzw. Wochenenden zu skalieren.

    > [WACOM.NOTE] Für diese Funktion beginnt das Wochenende am Freitagabend (standardmäßig um 20 Uhr) und endet am Montagmorgen (standardmäßig um 8 Uhr). Das Wochenendprofil verwendet denselben Start- und Endtag, den Sie in der Einstellung **Time** angeben.

7.  Wählen Sie unter **Time** eine Start- und Endzeit für den Tag in halbstündigen Schritten sowie eine Zeitzone aus. Standardmäßig beginnt der Tag um 8 Uhr und endet um 20 Uhr. Die Sommerzeit wird für die ausgewählte Zeitzone berücksichtigt.

8.  Unter **Specific Dates** können Sie benannte Zeitrahmen erstellen, in denen Sie die Ressourcen skalieren möchten. Beispielsweise können Sie zusätzliche Ressourcen für einen Schlussverkauf oder für ein Ereignis bereitstellen, für das Sie stark erhöhten Webdatenverkehr erwarten.

9.  Nachdem Sie Ihre Auswahl getroffen haben, klicken Sie auf **OK**, um das Dialogfeld **Schedule Times** zu schließen.

10. Im Dialogfeld **Edit Scale Settings for Schedule** werden jetzt konfigurierbare Zeitpläne oder Ereignisse basierend auf den von Ihnen vorgenommenen Änderungen angezeigt. Wählen Sie einen der wiederholten Zeitpläne oder bestimmte Daten zur Konfiguration aus.

    ![Skalierungseinstellungen für Zeitplan bearbeiten][Skalierungseinstellungen für Zeitplan bearbeiten]

    Sie können jetzt die Funktionen **Nach Metrik skalieren** und **Instanzanzahl** verwenden, um die Skalierung der Ressourcen für jeden ausgewählten Zeitplan anzupassen.

11. Um die Anzahl der Instanzen, die Ihre Website bei Laständerungen verwendet, dynamisch anzupassen, aktivieren Sie die Option **Nach Metrik skalieren** durch Auswahl von **CPU**.

    ![Nach Metrik skalieren][Nach Metrik skalieren]

    Das Diagramm zeigt die Anzahl der Instanzen an, die in der letzten Woche verwendet wurden. Sie können das Diagramm zur Überwachung der Skalierungsaktivität verwenden.

12. **Scale by Metric** ändert die Funktion **Instance Count**, sodass Sie die Mindest- und Höchstanzahl virtueller Computer festlegen können, die für die automatische Skalierung verwendet werden soll. Azure wird das festgelegte Limit nie über- bzw. unterschreiten.

    ![Anzahl der Instanzen][Anzahl der Instanzen]

13. **Scale by Metric** aktiviert außerdem die Option **Target CPU**, mit der Sie einen Zielbereich für die CPU-Auslastung angeben können. Dieser Bereich stellt die durchschnittliche CPU-Auslastung der Website dar. Microsoft Azure fügt Standardinstanzen hinzu bzw. entfernt sie, um die Website in diesem Bereich zu halten.

    ![Ziel-CPU][Ziel-CPU]

    **Hinweis**: Wenn **Nach Metrik skalieren** aktiviert ist, prüft Microsoft Azure die CPU der Website alle fünf Minuten und fügt bei Bedarf zu diesem Zeitpunkt Instanzen hinzu. Ist die CPU-Auslastung niedrig, entfernt Microsoft Azure alle zwei Stunden Instanzen, um sicherzustellen, dass die Website leistungsfähig bleibt. Im Allgemeinen ist eine Mindest-Instanzanzahl von 1 ausreichend. Wenn Sie jedoch plötzliche Spitzenlasten auf der Website erwarten, legen Sie die Mindestanzahl der Instanzen so fest, dass sie die Last bewältigen können. Tritt beispielsweise eine unerwartete Spitzenlast während des 5-Minuten-Intervalls auf, bevor Microsoft Azure die CPU-Auslastung erneut prüft, kann es vorkommen, dass die Website während dieser Zeit nicht reagiert. Wenn Sie also plötzliche große Datenverkehrsmengen erwarten, stellen Sie die Mindest-Instanzanzahl höher ein, um diese Spitzenlasten abzufangen.

14. Nachdem Sie die Änderungen an den Objekten in der Liste **Skalierungseinstellungen für Zeitplan bearbeiten** vorgenommen haben, klicken Sie in der Befehlsleiste am unteren Rand der Seite auf **Speichern**, um alle Zeitplaneinstellungen gleichzeitig zu speichern (so müssen Sie nicht jeden Zeitplan einzeln speichern).

> [WACOM.NOTE] Im [Azure-Vorschauportal][Azure-Vorschauportal] können Sie nicht nur nach CPU-Prozentsatz skalieren, sondern auch nach den anderen Metriken für die Arbeitsspeicherauslastung, Warteschlangenlänge des Datenträgers, HTTP-Warteschlangenlänge, eingehende Daten und ausgehende Daten. Außerdem besteht die Möglichkeit, eine oder mehrere Regeln für das Hoch- und Herunterskalieren zu erstellen, die Ihnen noch mehr individuelle Kontrolle über die Skalierung bieten. Weitere Informationen finden Sie unter [Skalieren von Websites][Skalieren von Websites] in der Dokumentation zum Azure-Vorschauportal.

<a name="ScalingSQLServer"></a>

## Skalierung einer mit der Website verbundenen SQL-Serverdatenbank

Wenn mit Ihrer Website ein oder mehrere SQL Server-Datenbanken verknüpft sind (unabhängig vom Webhostingplan-Modus), werden diese im Abschnitt **Verknüpfte Ressourcen** unten auf der Seite "Skalieren" aufgeführt.

1.  Um eine dieser Datenbanken zu skalieren, klicken Sie im Abschnitt **Verknüpfte Ressourcen** auf den Link **Skalierung für diese Datenbank verwalten** neben dem Namen der Datenbank.

    ![Verknüpfte Datenbank][Verknüpfte Datenbank]

2.  Über den Link gelangen Sie zur SQL Server-Registerkarte im Azure-Verwaltungsportal, wo Sie **Edition** und **Maximale Größe** für die Datenbank konfigurieren können:

    ![Skalieren der SQL-Serverdatenbank][Skalieren der SQL-Serverdatenbank]

    Wählen Sie für **Edition** den Wert **Web** oder **Business** aus, je nach erforderlicher Speicherkapazität. Die Edition **Web** bietet mehrere kleinere Kapazitäten, während **Business** eine Reihe größerer Kapazitäten zur Verfügung stellt.

    Der ausgewählte Wert für **Maximale Größe** gibt die Obergrenze für die Datenbank an. Die Datenbankgebühren basieren auf der tatsächlich gespeicherten Datenmenge, daher wirkt sich eine Änderung der Eigenschaft **Maximale Größe** nicht unmittelbar auf die Datenbankgebühren aus. Weitere Informationen finden Sie unter [Konten und Abrechnung in Microsoft Azure SQL-Datenbanken][Konten und Abrechnung in Microsoft Azure SQL-Datenbanken].

<a name="devfeatures"></a>

## Entwicklerfunktionen

Je nach Webhostingplan-Modus stehen die folgenden entwicklungsbezogenen Funktionen zur Verfügung:

**Bitness**

-   Der Basic- und Standard-Planmodus unterstützt 64-Bit- und 32-Bit-Anwendungen.
-   Der Free- und Shared-Planmodus unterstützt nur 32-Bit-Anwendungen.

**Debugger-Support**

-   Debugger-Support ist für die Webhostingplan-Modi "Kostenlos", "Freigegeben" und "Basic" mit einer gleichzeitigen Verbindung pro Anwendung erhältlich.
-   Debugger-Support ist für den Webhostingplan-Modus "Standard" mit fünf gleichzeitigen Verbindungen pro Anwendung erhältlich.

<a name="OtherFeatures"></a>

## Andere Funktionen

**Webendpunkt-Überwachung**

-   Die Webendpunkt-Überwachung ist in den Webhostingplan-Modi "Basic" und "Standard" verfügbar. Weitere Informationen zur Webendpunkt-Überwachung finden Sie unter [Überwachen von Websites][Überwachen von Websites].

-   Ausführliche Informationen zu den anderen Funktionen in den Webhostingplänen, darunter Preisgestaltung und allgemein interessante Funktionen (auch für Entwickler), finden Sie unter [Preisdetails zu Websites][Preisdetails zu Websites].

<a name="Next Steps"></a>

## Nächste Schritte

-   Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion][Kostenlose Microsoft Azure-Testversion].

-   Informationen zu Preisen, Support und SLAs erhalten Sie unter den folgenden Links.

    [Preisdetails zur Datenübertragung][Preisdetails zur Datenübertragung]

    [Supportpläne für Microsoft Azure][Supportpläne für Microsoft Azure]

    [Vereinbarungen zum Servicelevel (SLAs)][Vereinbarungen zum Servicelevel (SLAs)]

    [Preisdetails für SQL-Datenbanken][Preisdetails für SQL-Datenbanken]

    [Größen virtueller Computer und Clouddienste für Windows Azure][Größen virtueller Computer und Clouddienste für Windows Azure]

    [Preisdetails zu Websites][Preisdetails zu Websites]

    [Preisdetails zu Websites - SSL-Verbindungen][Preisdetails zu Websites - SSL-Verbindungen]

-   Informationen zu den Best Practices für Azure-Websites, einschließlich Aufbau einer skalierbaren, robusten Architektur, finden Sie unter [Best Practices: Windows Azure-Websites (WAWS)][Best Practices: Windows Azure-Websites (WAWS)].

-   Videos zur Skalierung von Azure-Websites:

    [Wann sollte man Azure-Websites skalieren? - Mit Stefan Schackow][Wann sollte man Azure-Websites skalieren? - Mit Stefan Schackow]

    [Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - Mit Stefan Schackow][Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - Mit Stefan Schackow]

    [Wie sollte man Azure-Websites skalieren? - Mit Stefan Schackow][Wie sollte man Azure-Websites skalieren? - Mit Stefan Schackow]


  [Was ist ein Webhostingplan?]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-web-hosting-plan-overview/
  [Webhostingpläne für Azure-Websites: ausführliche Übersicht]: http://www.azure.microsoft.com/de-de/Documentation/Articles/azure-web-sites-web-hosting-plans-in-depth-overview/
  [Preisdetails zu Websites]: http://www.windowsazure.com/de-de/pricing/details/web-sites/
  [Microsoft Azure-Abonnements]: http://go.microsoft.com/fwlink/?LinkID=235288
  [Skalierung auf Shared- oder Basic-Planmodus]: #scalingsharedorbasic
  [Skalierung auf Standard-Planmodus]: #scalingstandard
  [Skalierung einer mit der Website verbundenen SQL-Serverdatenbank]: #ScalingSQLServer
  [Entwicklerfunktionen]: #devfeatures
  [Andere Funktionen]: #OtherFeatures
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [Website auswählen]: ./media/web-sites-scale/01SelectWebSite.png
  [Die Registerkarte "Skalierung"]: ./media/web-sites-scale/02SelectScaleTab.png
  [Webhostingplan auswählen]: ./media/web-sites-scale/03aChooseWHP.png
  [Größen virtueller Computer und Clouddienste für Windows Azure]: http://go.microsoft.com/fwlink/?LinkId=309169
  [Instanzgröße für Basic-Modus]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
  [Anzahl der Instanzen für Basic-Modus]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
  [Schaltfläche "Save"]: ./media/web-sites-scale/05SaveButton.png
  [Planänderung abgeschlossen]: ./media/web-sites-scale/06BasicComplete.png
  [Standardplan auswählen]: ./media/web-sites-scale/07ChooseStandard.png
  [Kapazitätsabschnitt in Standard]: ./media/web-sites-scale/08CapacitySectionStandard.png
  [Instanzgröße auswählen]: ./media/web-sites-scale/09ChooseInstanceSize.png
  [Zeitpläne einrichten]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
  [Dialogfeld für Zeitpläne einrichten]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
  [Skalierungseinstellungen für Zeitplan bearbeiten]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
  [Nach Metrik skalieren]: ./media/web-sites-scale/13ScaleByMetric.png
  [Anzahl der Instanzen]: ./media/web-sites-scale/14InstanceCount.png
  [Ziel-CPU]: ./media/web-sites-scale/15TargetCPU.png
  [Azure-Vorschauportal]: https://portal.azure.com/
  [Skalieren von Websites]: http://azure.microsoft.com/de-de/documentation/articles/insights-how-to-scale/
  [Verknüpfte Datenbank]: ./media/web-sites-scale/16LinkedResources.png
  [Skalieren der SQL-Serverdatenbank]: ./media/web-sites-scale/17ScaleDatabase.png
  [Konten und Abrechnung in Microsoft Azure SQL-Datenbanken]: http://go.microsoft.com/fwlink/?LinkId=234930
  [Überwachen von Websites]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-monitor/
  [Kostenlose Microsoft Azure-Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Preisdetails zur Datenübertragung]: http://www.windowsazure.com/de-de/pricing/details/data-transfers/
  [Supportpläne für Microsoft Azure]: http://www.windowsazure.com/de-de/support/plans/
  [Vereinbarungen zum Servicelevel (SLAs)]: http://www.windowsazure.com/de-de/support/legal/sla/
  [Preisdetails für SQL-Datenbanken]: http://www.windowsazure.com/de-de/pricing/details/sql-database/
  [Preisdetails zu Websites - SSL-Verbindungen]: http://www.windowsazure.com/de-de/pricing/details/web-sites/#ssl-connections
  [Best Practices: Windows Azure-Websites (WAWS)]: http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx
  [Wann sollte man Azure-Websites skalieren? - Mit Stefan Schackow]: http://www.windowsazure.com/de-de/documentation/videos/azure-web-sites-free-vs-standard-scaling/
  [Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - Mit Stefan Schackow]: http://www.windowsazure.com/de-de/documentation/videos/auto-scaling-azure-web-sites/
  [Wie sollte man Azure-Websites skalieren? - Mit Stefan Schackow]: http://www.windowsazure.com/de-de/documentation/videos/how-azure-web-sites-scale/
