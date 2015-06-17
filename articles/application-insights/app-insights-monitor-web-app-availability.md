<properties 
	pageTitle="Überwachen der Verfügbarkeit und Reaktionsfähigkeit von Websites" 
	description="Richten Sie Webtests in Application Insights ein. Erhalten Sie Benachrichtigungen, wenn eine Website nicht mehr zur Verfügung steht oder langsam reagiert." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="awills"/>
 
# Überwachen der Verfügbarkeit und Reaktionsfähigkeit von Websites

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Nachdem Sie die Webanwendung bereitgestellt haben, können Sie Webtests einrichten, um die Verfügbarkeit und Reaktionszeit zu überwachen. Application Insights sendet regelmäßig Webanforderungen von verschiedenen Punkten rund um die Welt und benachrichtigt Sie, wenn Ihre Anwendung langsam oder gar nicht reagiert.

![Beispiel für Webtest](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Sie können für jeden HTTP-Endpunkt, der über das öffentliche Internet zugänglich ist, Webtests einrichten.

Es gibt zwei Arten von Webtests:

* [URL-Ping-Test](#set-up-a-url-ping-test): einen einfachen Test, den Sie im Azure-Portal erstellen können.
* [Mit mehreren Schritten Webtest](#multi-step-web-tests): die in Visual Studio Ultimate oder Visual Studio Enterprise erstellen und auf das Portal hochladen.

*Handelt es sich um eine Azure Web app? Nur [Webtest erstellen, in der Web-app-Blade][azure-availability].*



## Richten Sie ein URL-Ping-test

### <a name="create"></a>1. Erstellen Sie eine neue Ressource?

Überspringen Sie diesen Schritt, wenn Sie haben bereits [Richten Sie eine Ressource Application Insights][start] für diese Anwendung, und Sie die Verfügbarkeitsverlaufsdaten am selben Ort angezeigt möchten.

Melden Sie sich bis zu [Microsoft Azure](http://azure.com), fahren Sie mit der [Azure-Portal](https://portal.azure.com), und erstellen Sie eine neue Application Insights-Ressource.

![Neu > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Das Fenster "Übersicht" für die neue Ressource wird geöffnet. Um dies zu einem beliebigen Zeitpunkt finden die [Azure-Portal](https://portal.azure.com), klicken Sie auf Durchsuchen.

### <a name="setup"></a>2. Erstellen eines Webtests

Suchen Sie in der Application Insights-Ressource für die Verfügbarkeit Kachel. Klicken Sie auf, um das Fenster des Web-Tests für Ihre Anwendung zu öffnen, und fügen Sie einen Webtest.

![Mindestens die URL der Website eintragen](./media/app-insights-monitor-web-app-availability/13-availability.png)

- **Der URL** muss aus dem öffentlichen Internet sichtbar sein. Sie kann auch eine Abfragezeichenfolge enthalten - Sie können beispielsweise Ihre Datenbank ein wenig abfragen. Wenn die URL in eine Umleitung aufgelöst wird, folgen wir ihr bis zu 10 Umleitungen.

- Wenn **Wiederholungen aktivieren** ausgewählt ist, wird der Test fehlschlägt, nach kurzer Zeit wiederholt wird. Nur wenn drei aufeinander folgende Versuche fehl, wird ein Fehler gemeldet. Nachfolgende Tests werden dann normalen Intervall ausgeführt. Wiederholen ist bis zum nächsten Erfolg vorübergehend unterbrochen werden. Mit dieser Regel wird an jedem Standort Test unabhängig voneinander angewendet.

- **Speicherorte testen** werden an folgenden Stellen aus, wenn unsere Server Webanforderungen an Ihre URL senden. Wählen Sie mehrere aus, damit Sie Probleme in Ihrer Website von Netzwerkproblemen unterschieden werden können. Sie können bis zu 16 Speicherorte auswählen.

- **Erfolgskriterien**:

    **HTTP-Statuscode**: 200 üblich ist.

    **Inhalts**: eine Zeichenfolge wie "Willkommen!" Wir testen, dass sie in jeder Antwort auftritt. Dies muss eine Zeichenfolge in Klartext, ohne Platzhalter sein. Vergessen Sie nicht, diese zu aktualisieren, wenn sich der Seiteninhalt ändert.


- **Warnungen** werden standardmäßig an Sie gesendet werden, wenn mehr als 15 Minuten Wiederholter Fehler vorliegen. Sie können dies auf engere Überwachung einstellen oder die E-Mail-Adresse für die Benachrichtigung ändern.

#### Testen weiterer URLs

Sie können Tests hinzufügen, so viele URLs wie gewünscht. Neben dem Testen der Startseite können Sie sich auch vergewissern, dass die Datenbank läuft, indem Sie eine Such-URL testen.


### <a name="monitor"></a>3. Die Verfügbarkeitsberichte anzeigen

Klicken Sie nach 1 bis 2 Minuten auf das Fenster "Verfügbarkeit". (Es nicht automatisch aktualisiert.)

![Ergebnisübersicht im Startfenster](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Das Diagramm auf das Fenster "Verfügbarkeit" werden die Ergebnisse für alle Webtests dieser Anwendung kombiniert.

#### Seitenkomponenten

Bilder, Stylesheets und Skripts sowie andere statische Komponenten werden als Teil des Tests angefordert.

Die aufgezeichnete Reaktionszeit ist der Zeitaufwand für das vollständige Laden aller Komponenten.

Wenn eine Komponente nicht geladen werden kann, wird der Test als fehlgeschlagenen markiert.

## <a name="failures"></a>Wenn Sie Fehler finden Sie unter...

Im Web Tests Blade einen Bildlauf nach unten aus, und klicken Sie auf einen Test, in denen Sie Fehler entdecken.

![Auf bestimmten Webtest klicken](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

Dies zeigt die Ergebnisse für diesen Test.

![Auf bestimmten Webtest klicken](./media/app-insights-monitor-web-app-availability/16-1test.png)

Der Test ausgeführt wird, über verschiedene Positionen – auswählen, werden die Ergebnisse weniger als 100 %.

![Auf bestimmten Webtest klicken](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)


Führen Sie einen Bildlauf nach unten zum **fehlgeschlagene Tests** und wählen Sie ein Ergebnis.

Klicken Sie auf das Ergebnis, um eine Auswertung in das Portal und sehen, warum es fehlgeschlagen ist.

![Ausführungsergebnis des Webtests](./media/app-insights-monitor-web-app-availability/18-availDetails.png)


Alternativ können Sie die Ergebnisdatei herunterladen und in Visual Studio zu überprüfen.


*Sieht gut aus, aber als fehlerhaft gemeldet?* Überprüfen Sie alle Abbilder, Skripts, Stylesheets und andere Dateien, die von der Seite geladen. Wenn diese fehlschlägt, wird der Test gemeldet werden fehlgeschlagen ist, selbst wenn die Haupt-html-Seite OK lädt.



## Webtests mit mehreren Schritten

Sie können ein Szenario überwachen, das eine Sequenz mit mehreren URLs umfasst. Wenn Sie zum Beispiel eine Verkaufswebsite überwachen, können Sie testen, ob das Hinzufügen von Artikeln zum Einkaufswagen ordnungsgemäß funktioniert.

Um einen mehrstufigen Ttest zu erstellen, zeichnen das Szenario mit Visual Studio auf. Laden Sie dann die Aufzeichnung in Application Insights hoch. Application Insights wiederholt das Szenario in bestimmten Abständen und überprüft die Antworten.

#### 1\. Zeichnen Sie ein Szenario

Verwenden Sie Visual Studio Ultimate, um eine Web-Sitzung aufzuzeichnen.

1. Erstellen Sie ein Projekt.

    ![Erstellen Sie in Visual Studio ein neues Projekt aus der Vorlage für Webleistungstests und Auslastungstests.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

2. Öffnen Sie die .webtest-Datei und starten Sie die Aufzeichnung.

    ![Öffnen Sie die Webtest-Datei, und klicken Sie auf Datensatz.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)

3. Führen Sie die Benutzeraktionen, die Sie im Test simulieren möchten: Öffnen Sie Ihre Website und Hinzufügen eines Produkts zum Warenkorb. Beenden Sie dann den Test.

    ![Im Web test Recorder ausgeführt wird in Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Erstellen Sie kein langes Szenario. Es gibt eine Beschränkung von 100 Schritten und 2 Minuten.

4. Führen Sie den Test in Visual Studio aus, um sicherzustellen, dass er funktioniert.

    Das Webtest-Ausführungsprogramm öffnet einen Webbrowser und wiederholt die aufgezeichneten Aktionen. Stellen Sie sicher, dass der Test wie erwartet funktioniert.

    ![Klicken Sie in Visual Studio öffnen Sie die Webtest-Datei zu, und klicken Sie auf ausführen.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)
 

(Fügen Sie keinen Schleifen in den Webtestcode ein.)

#### 2\. Hochladen von Webtests in Application Insights

1. Erstellen Sie im Application Insights-Portal einen neuen Webtest.

    ![Wählen Sie auf der Web Tests Blade hinzufügen aus.](./media/app-insights-monitor-web-app-availability/16-another-test.png)

2. Wählen Sie einen mehrstufigen Test aus und laden Sie die .webtest-Datei hoch.

    ![Wählen Sie aus mehreren Schritten bestehenden Webtest.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

Zeigen Sie die Testergebnisse und alle Fehler auf die gleiche Weise wie für die Tests mit einer einzelnen Url an.

Eine häufige Ursache für Fehler ist, dass der Test zu lange ausgeführt wird. Er darf nicht länger als zwei Minuten ausgeführt werden.

Vergessen Sie nicht, dass alle Ressourcen einer Seite für den Test erfolgreich ausgeführt werden, ordnungsgemäß laden müssen einschließlich Skripts, Stylesheets, Bilder und So weiter.


### Einfügen von Zeiten und Zufallszahlen in einen mehrstufigen Test

Angenommen Sie, Sie testen ein Tool, die zeitabhängige Daten wie z. B. Bestände aus einem externen Feed abruft. Beim Aufzeichnen eines Webtests müssen Sie bestimmte Zeiten verwenden, aber als Parameter des Tests – StartTime und EndTime – festlegen.

![Ein Webtest mit Parametern.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Wenn Sie den Test ausführen, EndTime immer zum aktuellen Zeitpunkt werden sollen, und StartTime beträgt 15 Minuten vor.

Webtest-Plug-Ins bieten dazu die entsprechende Möglichkeit.

1. Fügen Sie ein Webtest-Plug-In für jeden gewünschten Variablenparameterwert hinzu. Wählen Sie in der Web-Symbolleiste **Test-Plug-In hinzufügen**.

    ![Wählen Sie Test-Plug-In hinzufügen, und wählen Sie einen Typ aus.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    In diesem Beispiel verwenden wir das Datum-Zeit-Plug-in zwei Instanzen. Eine Instanz ist für "15 Minuten vor" und "vorläufig".

2. Öffnen Sie die Eigenschaften der einzelnen Plug-Ins. Geben sie einen Namen ein, und legen sie die aktuelle Uhrzeit verwendet. Legen Sie für eines der Plug-Ins "Add Minutes = -15" fest.

    ![Legen Sie Name, verwenden aktuelle Uhrzeit und Protokoll hinzufügen.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. Verwenden Sie im Webtestparameter {{Plug-In-Name}} zum Verweisen auf einen Plug-In-Namen.

    ![Verwenden Sie im Testparameter {{Plug-in-Name}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Laden Sie jetzt den Tests in das Portal hoch. Bei jeder Ausführung des Tests werden die dynamischen Werte verwendet.

## <a name="edit"></a> Bearbeiten oder Deaktivieren eines Tests

Öffnen Sie einen Test, um ihn zu bearbeiten oder zu deaktivieren.

![Webtest bearbeiten oder deaktivieren](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Sie können Webtests beispielsweise deaktivieren, während Sie Wartungsarbeiten für den Dienst durchführen.

## <a name="video"></a>Video

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Nächste Schritte

[Diagnoseprotokolle durchsuchen][diagnostic]

[Problembehandlung][qna]




<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->