<properties 
	pageTitle="Überwachen der Verfügbarkeit und Reaktionszeit von Websites" 
	description="Richten Sie Webtests in Application Insights ein. Erhalten Sie Benachrichtigungen, wenn eine Website nicht mehr zur Verfügung steht oder langsam reagiert."
	services="application-insights" 
documentationCenter=""
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/27/2015" 
	ms.author="awills"/>
 
# Überwachen der Verfügbarkeit und Reaktionszeit von Websites

Nachdem Sie die Webanwendung bereitgestellt haben, können Sie Webtests einrichten, um die Verfügbarkeit und Reaktionszeit zu überwachen. Application Insights sendet regelmäßig Webanforderungen von verschiedenen Punkten rund um die Welt und benachrichtigt Sie, wenn Ihre Anwendung langsam oder gar nicht reagiert.

![Web test example](./media/appinsights/appinsights-10webtestresult.png)

Sie können für jeden HTTP-Endpunkt, der über das öffentliche Internet zugänglich ist, Webtests einrichten.

*Handelt es sich um eine Azure-Website? Erstellen Sie [einfach den Webtest im Website-Blade ][azurewebtest].*


1. [Erstellen Sie eine neue Ressource?](#create)
1. [Einrichten eines Webtests](#setup)
1. [Ergebnisse anzeigen](#monitor)
2. [Wenn Sie Fehler finden ...](#failures)
2. [Webtests mit mehreren Schritten](#multistep)
1. [Bearbeiten oder Deaktivieren von Tests](#edit)


 [Video](#video)
 [Nächste Schritte](#next)

## Einrichten eines Webtests

### <a name="create"></a>1. Erstellen Sie eine neue Ressource?

Überspringen Sie diesen Schritt, wenn Sie bereit [eine Application Insights-Startressource][für] diese Anwendung eingerichtet haben und die Verfügbarkeitsdaten am gleichen Ort angezeigt werden sollen.

Melden Sie sich bei [Microsoft Azure](http://azure.com) an, wechseln Sie zum [Vorschauportal](https://portal.azure.com) und erstellen Sie eine neue Application Insights-Ressource. 

![New > Application Insights](./media/appinsights/appinsights-11newApp.png)

### <a name="setup"></a>2. Erstellen eines Webtests

Klicken Sie im Übersichtsblade Ihrer Anwendung durch die Webtestskachel. 

![Click the empty availability test](./media/appinsights/appinsights-12avail.png)

*Sie haben bereits einige Webtests? Klicken Sie durch die Webtestkacheln und wählen Sie "Add Webtest" aus.*

Richten Sie die Testdetails ein.

![Fill at least the URL of your website](./media/appinsights/appinsights-13availChoices.png)

- **Die URL** muss aus dem öffentlichen Internet heraus sichtbar sein. Sie kann auch eine Abfragezeichenfolge enthalten - Sie können beispielsweise Ihre Datenbank ein wenig abfragen. Wenn die URL in eine Umleitung aufgelöst wird, folgen wir ihr bis zu 10 Umleitungen.

- **Teststandorte** sind die Orte, von denen unsere Server Webanforderungen an Ihre URL senden. Wählen Sie zwei oder drei aus, sodass Sie Probleme mit der Website von Netzwerkproblemen unterscheiden können. Sie können maximal drei auswählen.

- **Erfolgskriterien**:
    **HTTP-Rückgabecodes**: 200 sind üblich. 

    **Inhaltsübereinstimmungs**zeichenfolge, zum Beispiel "Willkommen!" Wir testen, dass sie in jeder Antwort auftritt. Dies muss eine Zeichenfolge in Klartext, ohne Platzhalter sein. Vergessen Sie nicht, diese zu aktualisieren, wenn sich der Seiteninhalt ändert.

- **Warnungen**: Standardmäßig werden Warnungen an Sie gesendet, wenn über 15 Minuten mehrere Fehlmeldungen auftreten. Sie können dies auf engere Überwachung einstellen oder die E-Mail-Adresse für die Benachrichtigung ändern.

#### Testen weiterer URLs

Sie können weitere Tests für beliebig viele URLs hinzufügen. Neben dem Testen der Startseite können Sie sich auch vergewissern, dass die Datenbank läuft, indem Sie eine Such-URL testen.

![On the web tests blade, choose Add](./media/appinsights/appinsights-16anotherWebtest.png)


### <a name="monitor"></a>3. Anzeigen von Verfügbarkeitsberichten

Klicken Sie nach 1 bis 2 Minuten auf "Aktualisieren" im Übersichtsblade. (In dieser Version erfolgt die Aktualisierung nicht automatisch.)

![Summary results on the home blade](./media/appinsights/appinsights-14availSummary.png)

Das Diagramm im Übersichtsblade kombiniert die Ergebnisse für alle Webtests dieser Anwendung.

#### Seitenkomponenten

Bilder, Stylesheets und Skripts sowie andere statische Komponenten werden als Teil des Tests angefordert.  

Die aufgezeichnete Reaktionszeit ist der Zeitaufwand für das vollständige Laden aller Komponenten.

Wenn eine Komponente nicht geladen werden kann, wird der Test als fehlgeschlagenen markiert.

## <a name="failures"></a>Wenn Sie Fehler finden ...

Klicken Sie durch das Blade mit den Webtests, um separate Ergebnisse für jeden Test anzuzeigen.

Öffnen Sie einen bestimmten Webtest.

![Click a specific webtest](./media/appinsights/appinsights-15webTestList.png)

Führen Sie einen Bildlauf bis zu **Tests mit Fehlern** aus. Wählen Sie dann ein Ergebnis aus.

![Click a specific webtest](./media/appinsights/appinsights-17-availViewDetails.png)

Im Ergebnis wird die Fehlerursache angezeigt.

![Webtest run result](./media/appinsights/appinsights-18-availDetails.png)

Wenn Sie weitere Details wünschen, können Sie die Ergebnisdatei herunterladen und in Visual Studio überprüfen.



##<a name="multistep"></a>Webtests mit mehreren Schritten 

Sie können ein Szenario überwachen, das eine Sequenz mit mehreren URLs umfasst. Wenn Sie zum Beispiel eine Verkaufswebsite überwachen, können Sie testen, ob das Hinzufügen von Artikeln zum Einkaufswagen ordnungsgemäß funktioniert. 

Um einen mehrstufigen Ttest zu erstellen, zeichnen das Szenario mit Visual Studio auf. Laden Sie dann die Aufzeichnung in Application Insights hoch. Application Insights wiederholt das Szenario in bestimmten Abständen und überprüft die Antworten.

#### 1. Aufzeichnen eines Szenarios

Use Visual Studio Ultimate to record a web session.

1. Create a web performance test project.
    ![In Visual Studio, create a new project from the Web Performance and Load Test template.](./media/appinsights/appinsights-71webtest-multi-vs-create.png)
2. Öffnen Sie die .webtest-Datei und starten Sie die Aufzeichnung.
    ![Open the .webtest file and click Record.](./media/appinsights/appinsights-71webtest-multi-vs-start.png)
3. Führen Sie die Benutzeraktionen durch, die Sie im Test simulieren möchten: Öffnen Sie die Website, fügen Sie dem Warenkorb ein Produkt hinzu usw. Beenden Sie dann den Test. 
    ![The web test recorder runs in Internet Explorer.](./media/appinsights/appinsights-71webtest-multi-vs-record.png)
    Erstellen Sie kein langes Szenario. Es gibt eine Beschränkung von 100 Schritten und 2 Minuten.
4. Führen Sie den Test in Visual Studio aus, um sicherzustellen, dass er funktioniert.
    Das Webtest-Ausführungsprogramm öffnet einen Webbrowser und wiederholt die aufgezeichneten Aktionen. Stellen Sie sicher, dass der Test wie erwartet funktioniert. 
    ![In Visual Studio, open the .webtest file and click Run.](./media/appinsights/appinsights-71webtest-multi-vs-run.png)
 

(Fügen Sie keinen Schleifen in den Webtestcode ein.)

#### 2. Hochladen des Webtests in Application Insights

Erstellen Sie im Application Insights-Portal einen neuen Webtest.

![On the web tests blade, choose Add.](./media/appinsights/appinsights-16anotherWebtest.png)

Wählen Sie einen mehrstufigen Test aus und laden Sie die .webtest-Datei hoch.

![Select multi-step webtest.](./media/appinsights/appinsights-71webtestUpload.png)

Zeigen Sie die Testergebnisse und alle Fehler auf die gleiche Weise wie für die Tests mit einer einzelnen Url an. 

Eine häufige Ursache für Fehler ist, dass der Test zu lange ausgeführt wird. Er darf nicht länger als zwei Minuten ausgeführt werden.


### Einfügen von Zeiten und Zufallszahlen in einen mehrstufigen Test

Angenommen, Sie testen ein Tool, das zeitabhängige Daten, wie z. B. Bestände, aus einem externen Feed abruft. Beim Aufzeichnen eines Webtests müssen Sie bestimmte Zeiten verwenden, aber als Parameter des Tests - StartTime und EndTime - festlegen.

![A web test with parameters.](./media/appinsights/appinsights-72webtest-parameters.png)

Wenn Sie den Test ausführen, sollte EndTime immer die aktuelle Uhrzeit widerspiegeln, und StartTime sollte der Uhrzeit vor 15 Minuten entsprechen.

Webtest-Plug-Ins bieten dazu die entsprechende Möglichkeit.

1. Fügen Sie ein Webtest-Plug-In für jeden gewünschten Variablenparameterwert hinzu. Wählen Sie in der Webtest-Symbolleiste **Add Web Test Plugin** aus.

    ![Choose Add Web Test Plugin and select a type.](./media/appinsights/appinsights-72webtest-plugins.png)

    In diesem Beispiel verwenden wir zwei Instanzen des Datum-Uhrzeit-Plug-Ins. Eine Instanz wird für "vor 15 Minuten vor" und eine andere für "jetzt" verwendet. 

2. Öffnen Sie die Eigenschaften der einzelnen Plug-Ins. Geben sie dem jeweiligen Plug-In einen Namen und legen Sie die Verwendung der aktuellen Uhrzeit fest. Legen Sie für eines der Plug-Ins "Add Minutes = -15" fest.

    ![Set name, Use Current Time, and Add Minutes.](./media/appinsights/appinsights-72webtest-plugin-parameters.png)

3. Verwenden Sie im Webtestparameter {{Plug-In-Name}} zum Verweisen auf einen Plug-In-Namen.

    ![In the test parameter, use {{plug-in name}}.](./media/appinsights/appinsights-72webtest-plugin-name.png)

Laden Sie jetzt den Tests in das Portal hoch. Bei jeder Ausführung des Tests werden die dynamischen Werte verwendet.

## <a name="edit"></a> Bearbeiten oder Deaktivieren von Tests

Öffnen Sie einen Test, um ihn zu bearbeiten oder zu deaktivieren.

![Edit or disable a web test](./media/appinsights/appinsights-19-availEdit.png)

Sie können Webtests beispielsweise deaktivieren, während Sie Wartungsarbeiten für den Dienst durchführen.

## <a name="video"></a>Video

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Nächste Schritte

[Diagnoseprotokolle durchsuchen][Diagnose]

[Problembehandlung][Fragen und Antworten]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




[azurewebtest]: ../insights-create-web-tests/

<!--HONumber=46--> 
