properties linkid="manage-scenarios-how-to-scale-websites" urlDisplayName="How to scale" pageTitle="How to scale web sites - Microsoft Azure service management" metaKeywords="Azure scaling web sites" description="Learn how to scale web sites in Azure to use Free, Shared, Basic and Standard web hosting plans." metaCanonical="" services="web-sites" documentationCenter="" title="How to Scale Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

Skalieren von Websites
======================

Für höheren Durchsatz und bessere Leistung Ihrer Websites auf Microsoft Azure können Sie das Azure-Verwaltungsportal verwenden, um den Modus Ihres Webhosting-Plans von Free in Shared, Basic oder Standard zu skalieren.

Das Skalieren von Azure-Websites umfasst zwei zusammenhängende Vorgänge: Erstens wird der Modus Ihres Webhosting-Plans auf einen höheren Servicelevel geändert, und zweitens werden bestimmte Einstellungen konfiguriert, nachdem Sie den höheren Servicelevel haben. Beide Themen werden in diesem Artikel erläutert. Höhere Servicelevel wie der Standardmodus bieten bessere Stabilität und Flexibilität bei der Festlegung, wie Ressourcen auf Azure verwendet werden.

Die Modusänderung und die Konfiguration erfolgt ganz einfach über die Registerkarte "Scale" des Verwaltungsportals. Sie können nach Bedarf hoch- oder runterskalieren. Diese Änderungen werden innerhalb von Sekunden angewendet und wirken sich auf alle Websites im Webhosting-Plan aus. Dafür muss weder der Code geändert noch Anwendungen erneut bereitgestellt werden.

Informationen zum Webhosting-Plan finden Sie unter [Webhosting-Pläne für Azure-Websites](http://go.microsoft.com/fwlink/?LinkId=9845584). Informationen zur Preisgestaltung und zu den Funktionen der einzelnen Webhosting-Pläne finden Sie unter den [Preisdetails für Websites](http://www.windowsazure.com/de-de/pricing/details/web-sites/).

> [WACOM.NOTE] Bevor Sie den Webhosting-Planmodus einer Website von **Free** in **Basic** oder **Standard** ändern, müssen Sie zunächst die für Ihr Website-Abonnement geltenden Kostenbeschränkungen aufheben. Informationen zum Ändern oder Anzeigen der Optionen für Ihr Microsoft Azure-Website-Abonnement finden Sie unter [Microsoft Azure-Abonnements](http://go.microsoft.com/fwlink/?LinkID=235288).

Themen in diesem Artikel:

-   [Skalierung auf Shared- oder Basic-Planmodus](#scalingsharedorbasic)
-   [Skalierung auf Standard-Planmodus](#scalingstandard)
-   [Skalierung einer mit der Website verbundenen SQL-Serverdatenbank](#ScalingSQLServer)
-   [Entwicklerfunktionen](#devfeatures)
-   [Andere Funktionen](#OtherFeatures)

## <a name="scalingsharedorbasic"></a>Skalierung auf Shared- oder Basic-Planmodus

1.  Öffnen Sie das [Verwaltungsportal](https://manage.windowsazure.com/) in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Web Sites** auf Ihre Website.

    ![Website auswählen](./media/web-sites-scale/01SelectWebSite.png)

3.  Klicken Sie auf die Registerkarte **Scale**.

    ![Die Registerkarte "Scale"](./media/web-sites-scale/02SelectScaleTab.png)

4.  Wählen Sie im Abschnitt **Web Hosting Plan Mode** entweder **SHARED** oder **BASIC** aus. In dieser Beispielabbildung wurde "Basic" verwendet.

    ![Webhosting-Plan auswählen](./media/web-sites-scale/03aChooseWHP.png)

    Im Abschnitt **Web Hosting Plan Sites** wird eine kurze Liste der Websites im aktuellen Plan angezeigt. Alle Websites im aktuellen Plan werden in den von Ihnen ausgewählten Webhosting-Planmodus übernommen.

5.  Klicken Sie im Abschnitt **Capacity** auf **Instance Size**. Die verfügbaren Optionen sind **Small**, **Medium** und **Large**. Im Shared-Modus ist die Instanzgrößenoption nicht verfügbar. Weitere Informationen zu den Instanzgrößen finden Sie unter [Größen virtueller Computer und Clouddienste für Windows Azure](http://go.microsoft.com/fwlink/?LinkId=309169).

    ![Instanzgröße für Basic-Modus](./media/web-sites-scale/03bChooseBasicInstanceSize.png)

6.  Verwenden Sie den Schieberegler, um die gewünschte Anzahl für **Instance Count** festzulegen.

    ![Anzahl der Instanzen für Basic-Modus](./media/web-sites-scale/04ChooseBasicInstanceCount.png)

7.  Klicken Sie in der Befehlsleiste auf **Save**.

    ![Schaltfläche "Save"](./media/web-sites-scale/05SaveButton.png)

    > [WACOM.NOTE] Sie können die Einstellungen für **Web Hosting Plan**, **Instance Size** und **Instance Count** auch separat speichern.

8.  Sie werden in einer Bestätigungsmeldung darauf hingewiesen, dass Websites im selben Webhosting-Plan wie die aktuelle Website ebenfalls in den neuen Modus übernommen werden. Klicken Sie auf **Yes**, um die Änderung abzuschließen.

    In diesem Beispiel wurde der Planmodus in **Basic** geändert:

    ![Planänderung abgeschlossen](./media/web-sites-scale/06BasicComplete.png)

## Skalierung auf Standard-Planmodus

> [WACOM.NOTE] Bevor Sie einen Webhosting-Plan in den Standardmodus übernehmen, müssen Sie die für Ihr Microsoft Azure-Website-Abonnement geltende Ausgabekapazität entfernen. Andernfalls besteht das Risiko, dass Ihre Website nicht mehr verfügbar ist, wenn Sie Ihre Ausgabekapazität vor dem Ende der Abrechnungsperiode erreichen. Informationen zum Ändern oder Anzeigen der Optionen für Ihr Microsoft Azure-Website-Abonnement finden Sie unter [Microsoft Azure-Abonnements](http://go.microsoft.com/fwlink/?LinkID=235288).

1.  Befolgen Sie für die Skalierung auf Standard dieselben anfänglichen Schritte wie bei der Skalierung auf Shared oder Basic, und wählen Sie dann **Standard** als **Web Hosting Plan Mode** aus.

    ![Standardplan auswählen](./media/web-sites-scale/07ChooseStandard.png)

    Wie zuvor wird im Abschnitt **Web Hosting Plan Sites** eine kurze Liste der Websites im aktuellen Plan angezeigt. In diesem Fall werden alle Websites im aktuellen Plan in den Standardmodus übernommen.

2.  Nach der Auswahl von **Standard** wird der Abschnitt **Capacity** erweitert, um die Optionen für **Instance Size** und **Instance Count** anzuzeigen, die auch im Basic-Modus verfügbar sind. Die Optionen **Edit Scale Settings for Schedule** und **Scale by Metric** stehen nur im Standardmodus zur Verfügung.

    ![Kapazitätsabschnitt in Standard](./media/web-sites-scale/08CapacitySectionStandard.png)

3.  Konfigurieren Sie die **Instance Size**. Die verfügbaren Optionen sind **Small**, **Medium** und **Large**.

    ![Instanzgröße auswählen](./media/web-sites-scale/09ChooseInstanceSize.png)

    Weitere Informationen zu den Instanzgrößen finden Sie unter [Größen virtueller Computer und Clouddienste für Windows Azure](http://go.microsoft.com/fwlink/?LinkId=309169).

4.  Wenn Sie Ressourcen basierend auf Tages- oder Nachtzeiten, Wochentagen oder Wochenende und/oder bestimmten Daten und Zeiten automatisch skalieren möchten, wählen Sie **Set up schedule times** unter der Option **Edit Scale Settings for Schedule** aus.

    ![Zeitpläne einrichten](./media/web-sites-scale/10SetUpScheduleTimesButton.png)

5.  Das Dialogfeld **Set up schedule times** enthält eine Reihe von nützlichen Konfigurationsoptionen.

    ![Dialogfeld für Zeitpläne einrichten](./media/web-sites-scale/11SetUpScheduleTimesDialog.png)

6.  Wählen Sie unter **Recurring Schedules** die Option **Differing scale between Day and Night** und/oder **Differing Scale between Weekday and Weekend** aus, um Ressourcen basierend auf unterschiedlichen Zeitplänen für Tages- oder Nachtzeiten und/oder Wochentagen bzw. Wochenenden zu skalieren.

    > [WACOM.NOTE] Für diese Funktion beginnt das Wochenende am Freitagabend (standardmäßig um 20 Uhr) und endet am Montagmorgen (standardmäßig um 8 Uhr). Das Wochenendprofil verwendet denselben Start- und Endtag, den Sie in der Einstellung **Time** angeben.

7.  Wählen Sie unter **Time** eine Start- und Endzeit für den Tag in halbstündigen Schritten sowie eine Zeitzone aus. Standardmäßig beginnt der Tag um 8 Uhr und endet um 20 Uhr. Die Sommerzeit wird für die ausgewählte Zeitzone berücksichtigt.

8.  Unter **Specific Dates** können Sie benannte Zeitrahmen erstellen, in denen Sie die Ressourcen skalieren möchten. Beispielsweise können Sie zusätzliche Ressourcen für einen Schlussverkauf oder für ein Ereignis bereitstellen, für das Sie stark erhöhten Webdatenverkehr erwarten.

9.  Nachdem Sie Ihre Auswahl getroffen haben, klicken Sie auf **OK**, um das Dialogfeld **Schedule Times** zu schließen.

10. Im Dialogfeld **Edit Scale Settings for Schedule** werden jetzt konfigurierbare Zeitpläne oder Ereignisse basierend auf den von Ihnen vorgenommenen Änderungen angezeigt. Wählen Sie einen der wiederholten Zeitpläne oder bestimmte Daten zur Konfiguration aus.

    ![Edit scale settings for schedule](./media/web-sites-scale/EditScaleSettingsForSchedule)

    Sie können jetzt die Funktionen **Scale by Metric** und **Instance Count** verwenden, um die Skalierung der Ressourcen für jeden ausgewählten Zeitplan anzupassen. 

1.  Um die Anzahl der Instanzen, die Ihre Website bei Laständerungen verwendet, dynamisch anzupassen, aktivieren Sie die Option **Scale by Metric** durch Auswahl von **CPU**.

    ![Scale By Metric](./media/web-sites-scale/13ScaleByMetric.png)

    Das Diagramm zeigt die Anzahl der Instanzen an, die in der letzten Woche verwendet wurden. Sie können das Diagramm zur Überwachung der Skalierungsaktivität verwenden.

1.  **Scale by Metric** ändert die Funktion **Instance Count**, sodass Sie die Mindest- und Höchstanzahl virtueller Computer festlegen können, die für die automatische Skalierung verwendet werden soll. Azure wird das festgelegte Limit nie über- bzw. unterschreiten.

    ![Anzahl der Instanzen](./media/web-sites-scale/14InstanceCount.png)

2.  **Scale by Metric** aktiviert außerdem die Option **Target CPU**, mit der Sie einen Zielbereich für die CPU-Auslastung angeben können. Dieser Bereich stellt die durchschnittliche CPU-Auslastung der Website dar. Microsoft Azure fügt Standardinstanzen hinzu bzw. entfernt sie, um die Website in diesem Bereich zu halten.

    ![Ziel-CPU](./media/web-sites-scale/15TargetCPU.png)

    **Hinweis**: Wenn **Scale by Metric** aktiviert ist, prüft Microsoft Azure die CPU der Website alle fünf Minuten und fügt bei Bedarf zu diesem Zeitpunkt Instanzen hinzu. Ist die CPU-Auslastung niedrig, entfernt Microsoft Azure alle zwei Stunden Instanzen, um sicherzustellen, dass die Website leistungsfähig bleibt. Im Allgemeinen ist eine Mindest-Instanzanzahl von 1 ausreichend. Wenn Sie jedoch plötzliche Spitzenlasten auf der Website erwarten, legen Sie die Mindestanzahl der Instanzen so fest, dass sie die Last bewältigen können. Tritt beispielsweise eine unerwartete Spitzenlast während des 5-Minuten-Intervalls auf, bevor Microsoft Azure die CPU-Auslastung erneut prüft, kann es vorkommen, dass die Website während dieser Zeit nicht reagiert. Wenn Sie also plötzliche große Datenverkehrsmengen erwarten, stellen Sie die Mindest-Instanzanzahl höher ein, um diese Spitzenlasten abzufangen.

3.  Nachdem Sie die Änderungen an den Objekten in der Liste **Edit Scale Settings for Schedule** vorgenommen haben, klicken Sie in der Befehlsleiste unten auf der Seite auf **Save**, um alle Planungseinstellungen gleichzeitig zu speichern (so müssen Sie nicht jeden Zeitplan einzeln speichern).

## Skalierung einer mit der Website verbundenen SQL-Serverdatenbank
Wenn mit Ihrer Website ein oder mehrere SQL-Serverdatenbanken verknüpft sind (unabhängig vom Webhosting-Planmodus), werden diese unter dem Abschnitt **Linked Resources** unten auf der Seite "Scaling" aufgelistet.

1.  Um eine dieser Datenbanken zu skalieren, klicken Sie im Abschnitt **Linked Resources** auf den Link **Manage scale for this database** neben dem Namen der Datenbank.

    ![Verknüpfte Datenbank](./media/web-sites-scale/16LinkedResources.png)

2.  Über den Link gelangen Sie zur Registerkarte "SQL Server" im Azure-Verwaltungsportal, wo Sie **Edition** und **Maximum Size** für die Datenbank konfigurieren können:

    ![Skalieren der SQL-Serverdatenbank](./media/web-sites-scale/17ScaleDatabase.png)

    Wählen Sie für **Edition** den Wert **Web** oder **Business** aus, je nach erforderlicher Speicherkapazität. Die Edition **Web** bietet mehrere kleinere Kapazitäten, während **Business** eine Reihe größerer Kapazitäten zur Verfügung stellt.

    Der ausgewählte Wert für **Max Size** gibt die Obergrenze für die Datenbank an. Die Datenbankgebühren basieren auf der tatsächlich gespeicherten Datenmenge, daher wirkt sich eine Änderung der Eigenschaft **Max Size** nicht unmittelbar auf die Datenbankgebühren aus. Weitere Informationen finden Sie unter [Konten und Abrechnung in Microsoft Azure SQL-Datenbanken](http://go.microsoft.com/fwlink/?LinkId=234930).

## Entwicklerfunktionen
hängen vom Webhosting-Planmodus ab; die folgenden entwicklungsbezogenen Funktionen sind verfügbar:

**Bitness**

-   Der Basic- und Standard-Planmodus unterstützt 64-Bit- und 32-Bit-Anwendungen.
-   Der Free- und Shared-Planmodus unterstützt nur 32-Bit-Anwendungen.

**Debugger-Support**

-   Debugger-Support ist für die Webhosting-Planmodi Free, Shared und Basic mit 1 gleichzeitigen Verbindung pro Anwendung erhältlich.
-   Debugger-Support ist für den Webhosting-Planmodus Standard mit 5 gleichzeitigen Verbindungen pro Anwendung erhältlich.

## Andere Funktionen

**Web Endpoint Monitoring**

-   Die Web-Endpunktüberwachung ist in den Webhosting-Planmodi Basic und Standard verfügbar. Weitere Informationen zur Web-Endpunktüberwachung finden Sie unter [Überwachen von Websites](http://www.windowsazure.com/de-de/documentation/articles/web-sites-monitor/).

-   Ausführliche Informationen zu den anderen Funktionen in den Webhosting-Plänen, einschließlich Preisgestaltung und allgemein interessanten Funktionen (auch für Entwickler) finden Sie unter [Preisdetails für Websites](http://www.windowsazure.com/de-de/pricing/details/web-sites/).

## Nächste Schritte
 - Informationen zu den Best Practices für Azure-Websites, einschließlich Aufbau einer skalierbaren, robusten Architektur, finden Sie unter [Best Practices: Windows Azure-Websites (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

-   Informationen zu Preisen, Support und SLAs erhalten Sie unter den folgenden Links.

    [Preisdetails zur Datenübertragung](http://www.windowsazure.com/de-de/pricing/details/data-transfers/)

    [Supportpläne für Microsoft Azure](http://www.windowsazure.com/de-de/support/plans/)

    [Vereinbarungen zum Servicelevel (SLAs)](http://www.windowsazure.com/de-de/support/legal/sla/)

    [Preisdetails für SQL-Datenbanken](http://www.windowsazure.com/de-de/pricing/details/sql-database/)

    [Größen virtueller Computer und Clouddienste für Windows Azure](http://go.microsoft.com/fwlink/?LinkId=309169)

    [Preisdetails für Websites](http://www.windowsazure.com/de-de/pricing/details/web-sites/)

    [Preisdetails für Websites - SSL-Verbindungen](http://www.windowsazure.com/de-de/pricing/details/web-sites/#ssl-connections)

-   Die folgenden Links führen zu Videos über die Skalierung von Azure-Websites:

    [Wann sollte man Azure-Websites skalieren - mit Stefan Schackow](http://www.windowsazure.com/de-de/documentation/videos/azure-web-sites-free-vs-standard-scaling/)

    [Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - mit Stefan Schackow](http://www.windowsazure.com/de-de/documentation/videos/auto-scaling-azure-web-sites/)

    [Wie sollte man Azure-Websites skalieren - mit Stefan Schackow](http://www.windowsazure.com/de-de/documentation/videos/how-azure-web-sites-scale/)

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

