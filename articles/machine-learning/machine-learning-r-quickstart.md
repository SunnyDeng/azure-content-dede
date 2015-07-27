<properties
	pageTitle="Schnellstart-Lernprogramm für die Sprache R fürMachine Learning | Microsoft Azure"
	description="Verwenden Sie dieses Lernprogramm für die Programmierung in R, um einen schnellen Einstieg in die Verwendung der Sprache R mit Azure Machine Learning Studio zum Erstellen von Vorhersagelösungen zu erhalten."
	keywords="quickstart,r language,r programming language,r programming tutorial"
	services="machine-learning"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/26/2015"
	ms.author="larryfr"/>

# Schnellstart-Lernprogramm für die Programmiersprache R für Azure Machine Learning

Stephen F. Elston, PhD.

##  Einführung

Dieses Schnellstart-Lernprogramm hilft Ihnen bei der schnellen Erweiterung von Azure Machine Learning mithilfe der Programmiersprache R. Führen Sie dieses Lernprogramm für die Programmierung mit R aus, um R-Code innerhalb von Azure Machine Learning zu erstellen, zu testen und auszuführen. Beim Durcharbeiten dieses Lernprogramms erstellen Sie unter Verwendung der Sprache R in Azure Machine Learning eine vollständige Vorhersagelösung.

Microsoft Azure Machine Learning enthält viele leistungsfähige Module für Machine Learning und Datenbearbeitung. Die leistungsfähige Sprache R wurde als die Lingua franca der Analytik beschrieben. Glücklicherweise lassen sich Analyse und Datenbearbeitung in Azure Machine Learning mithilfe von R erweitern. Diese Kombination bietet die einfache Skalierbarkeit von Bereitstellungen aus Azure Machine Learning zusammen mit der Flexibilität und den tief greifenden Analysefunktionen von R.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


###Vorhersagen und das DataSet

Vorhersagen stellen eine verbreitet angewendete und durchaus nützliche Analysemethode dar. Allgemeine Anwendungen reichen von der Vorhersage der Umsätze für Saisonartikel, über die Bestimmung der optimalen Lagerbestände bis hin zur Vorhersage makroökonomischer Variablen. Vorhersagen werden normalerweise mit Zeitreihenmodellen erstellt.

Zeitreihendaten sind Daten, deren Werte einen Zeitindex besitzen. Der Zeitindex kann regelmäßig sein, z. B. monatlich oder jede Minute, oder auch unregelmäßig. Ein Zeitreihenmodell basiert auf Zeitreihendaten. Die Programmiersprache R enthält ein flexibles Framework sowie umfassende Analysefunktionen für Zeitreihendaten.

In diesem Schnellstarthandbuch arbeiten wir mit kalifornischen Milchproduktions- und -preisdaten. Diese Daten umfassen monatliche Informationen zur Produktion mehrerer Milcherzeugnisse sowie zum Preis von Milchfett, einem Vergleichsrohstoff.

Die in diesem Artikel verwendeten Daten können zusammen mit R-Skripts [hier][download] heruntergeladen werden. Diese Daten wurden ursprünglich aus Informationen zusammengesetzt, die von der University of Wisconsin unter http://future.aae.wisc.edu/tab/production.html zur Verfügung gestellt werden.

###	Organisation

Wir werden mehrere Schritte durchlaufen, in denen Sie erfahren, wie Sie in der Azure Machine Learning-Umgebung R-Code zur Analyse und Datenbearbeitung erstellen, testen und ausführen.

* Zunächst werden die Grundlagen der Verwendung der Sprache R in der Azure Machine Learning Studio-Umgebung behandelt.

* Wir fahren dann mit der Erläuterung verschiedener E/A-Aspekte von Daten, R-Code und Grafiken in der Azure Machine Learning-Umgebung fort.

* Danach erstellen wir den ersten Teil der Vorhersagelösung, indem wir Code für die Datenbereinigung und -transformation erstellen.

* Mit den vorbereiteten Daten führen wir eine Analyse der Korrelationen zwischen mehreren der Variablen in unserem DataSet durch.

* Schließlich erstellen wir ein saisonales Zeitreihen-Vorhersagemodell für die Produktion von Milcherzeugnissen.

##<a id="mlstudio"></a>Interagieren mit der Sprache R in Machine Learning Studio

Dieser Abschnitt führt Sie durch einige Grundlagen der Interaktion mit der Programmiersprache R in der Machine Learning Studio-Umgebung. Die Sprache R stellt ein leistungsfähiges Tool zum Erstellen benutzerdefinierter Analyse- und Datenbearbeitungsmodule innerhalb der Azure Machine Learning-Umgebung bereit.

Wir verwenden RStudio, um R-Code in geringem Umfang zu entwickeln, zu testen und zu debuggen. Dieser Code wird dann ausgeschnitten und in ein ausführbereites [Execute R Script][execute-r-script]-Modul in Machine Learning Studio eingefügt.

###Das "Execute R Script"-Modul

In Machine Learning Studio werden R-Skripts innerhalb des [Execute R Script][execute-r-script]-Moduls ausgeführt. Ein Beispiel für das [Execute R Script][execute-r-script]-Modul in Machine Learning Studio sehen Sie in Abbildung 1.

 ![Programmiersprache R: Die Machine Learning Studio-Umgebung mit dem ausgewählten "Execute R Script"-Modul][1]

*Abbildung 1: Die Machine Learning Studio-Umgebung, in der das ausgewählte "Execute R Script"-Modul angezeigt wird.*

Sehen wir uns unter Bezugnahme auf Abbildung 1 einige der Hauptteile der Machine Learning Studio-Umgebung für die Arbeit mit dem [Execute R Script][execute-r-script]-Modul an.

- Die Module in dem Experiment werden im mittleren Bereich angezeigt.

- Der obere Teil des rechten Bereichs enthält ein Fenster zum Anzeigen und Bearbeiten Ihrer R-Skripts.

- Im unteren Teil des rechten Bereichs werden einige Eigenschaften des [Execute R Script][execute-r-script]-Moduls angezeigt. Sie können die Fehler- und Ausgabeprotokolle anzeigen, indem Sie auf die entsprechenden Stellen dieses Bereichs klicken.

Natürlich wird das [Execute R Script][execute-r-script]-Modul ausführlicher im Rest dieses Dokuments behandelt.

Bei der Arbeit mit komplexen R-Funktionen empfiehlt es sich, dass Sie das Bearbeiten, Testen und Debuggen in RStudio durchführen. Wie bei jeder Softwareentwicklung erweitern Sie Ihren Code schrittweise und testen ihn mit kleinen, einfachen Testfällen. Danach schneiden Sie Ihre Funktionen aus und fügen sie in das Fenster des [Execute R Script][execute-r-script]-Moduls ein. Dieser Ansatz erlaubt es Ihnen, sowohl die RStudio-IDE (Integrated Development Environment) als auch die Leistungsfähigkeit von Azure Machine Learning zu nutzen.

####Ausführen von R-Code

Jeder R-Code im [Execute R Script][execute-r-script]-Modul wird ausgeführt, wenn Sie das Experiment ausführen, indem Sie auf die Schaltfläche **Ausführen** klicken Nach Abschluss der Ausführung wird ein Häkchen auf dem Symbol [Execute R Script][execute-r-script] angezeigt.

####Defensive R-Codierung für Azure Machine Learning

Wenn Sie R-Code für beispielsweise einen Webdienst mithilfe von Azure Machine Learning entwickeln, sollten Sie auf jeden Fall planen, wie Ihr Code eine unerwartete Dateneingabe und Ausnahmen behandeln soll. Um Übersichtlichkeit zu gewährleisten, wurden in die meisten der dargestellten Codebeispiele kaum Testverfahren oder Verfahren zur Behandlung von Ausnahmen aufgenommen. Im weiteren Verlauf erhalten Sie jedoch einige Beispiele für Funktionen, die die Ausnahmebehandlungsfunktionen von R verwenden.

Wenn Sie eine umfassendere Erläuterung der Ausnahmebehandlung in R benötigen, lesen Sie die entsprechenden Abschnitte des Buchs von Wickham, das in [Anhang B – Weitere Informationen](#appendixb) aufgeführt ist


####Debuggen und Testen von R in Machine Learning Studio

Zu Wiederholungszwecken empfehlen wir Ihnen, Ihren R-Code in geringem Umfang in RStudio zu testen und zu debuggen. Es gibt jedoch Fälle, in denen Sie Probleme im R-Code im [Execute R Script][execute-r-script]-Modul selbst ermitteln müssen. Darüber hinaus ist es eine bewährte Praxis, die eigenen Ergebnisse in Machine Learning Studio zu überprüfen.

Ausgaben der Ausführung Ihres R-Codes und von der Azure Machine Learning-Plattform finden sich hauptsächlich in der Datei "output.log". Einige zusätzliche Informationen befinden sich auch in der Datei "error.log".

Tritt in Machine Learning Studio ein Fehler während der Ausführung Ihres R-Codes auf, sollten Sie als Erstes in der Datei "error.log" nachsehen. Die Datei "error.log" kann nützliche Fehlermeldungen enthalten, die Ihnen dabei helfen, den Fehler zu verstehen und zu korrigieren. Um die Datei "error.log" anzuzeigen, klicken Sie im Bereich **Eigenschaften** für das [Execute R Script][execute-r-script]-Modul mit dem Fehler auf **Fehlerprotokoll anzeigen**.

Ich führe beispielsweise den folgenden R-Code mit einer nicht definierten Variable "y" in einem [Execute R Script][execute-r-script]-Modul aus:

	x <- 1.0
	z <- x + y

Die Ausführung dieses Codes schlägt fehl, was zu einem Fehlerzustand führt. Wenn Sie im Bereich **Eigenschaften** auf **Fehlerprotokoll anzeigen** klicken, wird das in Abbildung 2 dargestellte Dialogfeld angezeigt:

  ![Popupfenster mit Fehlermeldung][2]

*Abbildung 2: Popupfenster mit Fehlermeldung*

Sie müssen sich anscheinend in der Datei "output.log" die R-Fehlermeldung ansehen. Klicken Sie auf das [Execute R Script][execute-r-script]-Modul und dann rechts im Bereich **Eigenschaften** auf **View output log**. Ein neues Browserfenster wird geöffnet, in dem Folgendes angezeigt wird:


	[ModuleOutput] [1] 14000
	[ModuleOutput]
	[ModuleOutput] Loading objects:
	[ModuleOutput]
	[ModuleOutput]   port1
	[ModuleOutput]
	[ModuleOutput] [1] "Loading variable port1..."
	[ModuleOutput]
	[ModuleOutput] Error in eval(expr, envir, enclos) : object 'y' not found

Diese Fehlermeldung enthält keine Überraschungen, und das Problem wird eindeutig identifiziert.

Um den Wert eines beliebigen Objekts in R zu überprüfen, können Sie diese Werte in die Datei "output.log" ausgeben. Die Regeln zum Untersuchen von Objektwerten sind im Wesentlichen mit denen in einer interaktiven R-Sitzung identisch. Wenn Sie beispielsweise einen Variablennamen in eine Zeile eingeben, wird der Wert des Objekts in die Datei "output.log" ausgegeben.

####Pakete in Machine Learning Studio

Azure Machine Learning verfügt über mehr als 350 vorinstallierte R-Sprachpakete. Sie können den folgenden Code im [Execute R Script][execute-r-script]-Modul verwenden, um eine Liste der vorinstallierten Pakete abzurufen.

	data.set <- data.frame(installed.packages())
	maml.mapOutputPort("data.set")

Wenn Sie die letzte Zeile des Codes im Moment noch nicht verstehen, lesen Sie trotzdem weiter. Die Verwendung von R in der Azure Machine Learning-Umgebung wird im restlichen Teil dieses Dokuments noch ausführlich erörtert.

###	Einführung in RStudio

RStudio ist eine weit verbreitete IDE für R. Wir verwenden RStudio zum Bearbeiten, Testen und Debuggen einiger R-Codeabschnitte, die in diesem Schnellstarthandbuch verwendet werden. Nachdem R-Code getestet wurde und bereitet ist, schneiden Sie ihn einfach im RStudio-Editor aus und fügen ihn in ein [Execute R Script][execute-r-script]-Modul in Machine Learning Studio ein.

Wenn die Programmiersprache R nicht auf Ihrem Desktopcomputer installiert ist, sollten Sie die Installation jetzt durchführen. Kostenlose Downloads der Open Source-Sprache R sind im CRAN (Comprehensive R Archive Network) unter [http://www.r-project.org/](http://www.r-project.org/) verfügbar. Es stehen dort Downloads für Windows, Mac OS und Linux/UNIX zur Verfügung. Wählen Sie einen Spiegelserver in Ihrer Nähe aus, und befolgen Sie die Downloadanweisungen. Zusätzlich finden Sie im CRAN eine Fülle von nützlichen Analyse- und Datenbearbeitungspaketen.

Wenn Sie noch keine Erfahrungen mit RStudio haben, sollten Sie die Desktopversion herunterladen und installieren. Sie finden die RStudio-Downloads für Windows, Mac OS und Linux/UNIX unter http://www.rstudio.com/products/RStudio/. Befolgen Sie die bereitgestellten Anweisungen zum Installieren von RStudio auf Ihrem Desktopcomputer.

Ein Einführungslernprogramm zu RStudio finden Sie unter https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

Einige zusätzliche Informationen zur Verwendung von RStudio finden Sie in [Anhang A][appendixa].

##<a id="scriptmodule"></a>Durchführen von Dateneingaben und -ausgaben im "Execute R Script"-Modul

In diesem Abschnitt wird erörtert, wie Sie Daten in das [Execute R Script][execute-r-script]-Modul ein- und daraus ausgeben können. Sie erfahren, wie verschiedene Datentypen, die vom [Execute R Script][execute-r-script]-Modul ein- und ausgelesen werden, zu handhaben sind.

Der vollständige Code für diesen Abschnitt befindet sich in der ZIP-Datei, die Sie zuvor heruntergeladen haben.

###Laden und Überprüfen von Daten in Machine Learning Studio

####<a id="loading"></a>Laden des DataSets

Wir beginnen damit, dass wir die Datei **csdairydata.csv** in Azure Machine Learning Studio laden.

- Starten der Azure Machine Learning Studio-Umgebung

- Klicken Sie auf das "+" unten links im Bildschirm, und wählen Sie **DataSet** aus.

- Klicken Sie auf **Aus lokaler Datei** und anschließend auf **Durchsuchen**, um die Daten auszuwählen.

- Stellen Sie sicher, dass Sie als DataSet-Typ **Generische CSV-Datei mit Header (.csv)** ausgewählt haben.

- Klicken Sie auf das Häkchen.

- Nachdem das DataSet hochgeladen wurde, können Sie es anzeigen, indem Sie auf die Registerkarte **DataSets** klicken.

####Erstellen eines Experiments

Nachdem wir nun einige Daten in ML Studio geladen haben, erstellen wir ein Experiment, um die Analyse durchführen zu können.

- Klicken Sie unten links auf das "+", und wählen Sie **Experiment** und anschließend **Leeres Experiment** aus.

- Sie können das Experiment benennen, indem Sie oben auf der Seite den Titel **Experiment erstellt am...** auswählen und bearbeiten. Ändern Sie den Titel beispielsweise zu **CA Dairy Analysis**.

- Erweitern Sie links auf der Experimentseite die Einträge **Gespeicherte DataSets** und **Meine DataSets**. Hier wird die Datei **cadairydata.csv** angezeigt, die Sie zuvor hochgeladen haben.

- Ziehen Sie das DataSet **csdairydata.csv** per Drag & Drop in das Experiment.

- Geben Sie im linken Bereich oben in das Feld **Search experiment items** als Suchbegriff [Execute R Script][execute-r-script] ein. Daraufhin wird das Modul in der Suchliste angezeigt.

- Ziehen Sie das [Execute R Script][execute-r-script]-Modul per Drag & Drop auf Ihre Palette.

- Verbinden Sie die Ausgabe des DataSets **csdairydata.csv** mit der ganz linken Eingabe (**Dataset1**) von [Execute R Script][execute-r-script].

- **Vergessen Sie nicht, auf "Speichern" zu klicken.**

An diesem Punkt sollte das Experiment ungefähr wie in Abbildung 3 aussehen.

![Das "CA Dairy Analysis"-Experiment mit DataSet und "Execute R Script"-Modul][3]

*Abbildung 3: Das "CA Dairy Analysis"-Experiment mit DataSet und "Execute R Script"-Modul.*

####Überprüfen der Daten

Lassen Sie uns einen Blick auf die Daten werfen, die wir in unser Experiment geladen haben. Klicken Sie im Experiment auf die Ausgabe des DataSets **cadairydata.csv**, und wählen Sie **Visualisieren** aus. Ihre Anzeige sollte etwa wie in Abbildung 4 aussehen.

![Zusammenfassung des DataSets "cadairydata.csv".][4]

*Abbildung 4: Zusammenfassung des DataSets "cadairydata.csv".*

In dieser Ansicht sehen wir viele nützliche Informationen. Die Zeile **Feature Type** zeigt an, welche Datentypen Azure Machine Learning Studio den Spalten in unserem DataSet zuweist. Wir sehen auch die ersten Zeilen dieses DataSets. Ein solch kurzer Blick ist eine gute Funktionsüberprüfung, bevor wir mit der wirklichen Arbeit beginnen.

###	Erstes R-Skript

Lassen Sie uns zuerst ein einfaches erstes R-Skript erstellen, um damit in Azure Machine Learning Studio zu experimentieren. Wir haben folgendes Skript in RStudio erstellt und getestet:

	## Only one of the following two lines should be used
	## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
	## If in RStudio, use the second line with read.csv()
	cadairydata <- maml.mapInputPort(1)
	# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
	str(cadairydata)
	pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
	## The following line should be executed only when running in
	## Azure Machine Learning Studio
	maml.mapOutputPort('cadairydata')

Jetzt müssen wir dieses Skript in Azure Machine Learning Studio übertragen. Wir könnten es einfach ausschneiden und einfügen. In diesem Fall jedoch übertragen wird unser R-Skript mithilfe einer ZIP-Datei.

###	Dateneingabe in das "Execute R Script"-Modul

Sehen Sie sich die Eingaben für das [Execute R Script][execute-r-script]-Modul an. In diesem Beispiel lesen wir die kalifornischen Milchproduktdaten in das [Execute R Script][execute-r-script]-Modul ein.

Es gibt drei mögliche Eingaben für das [Execute R Script][execute-r-script]-Modul. Sie können eine davon oder alle diese Eingaben verwenden, abhängig von Ihrer Anwendung. Es ist auch absolut sinnvoll, ein R-Skript zu verwenden, das gar keine Eingaben akzeptiert.

Betrachten wir nun jede dieser Eingaben von links nach rechts. Sie können den Namen jeder einzelnen Eingabe anzeigen, indem Sie mit dem Mauszeiger auf die Eingabe zeigen und die QuickInfo lesen.

#### Script Bundle

Die Eingabe "Script Bundle" erlaubt es Ihnen, den Inhalt einer ZIP-Datei an das [Execute R Script][execute-r-script]-Modul zu übergeben. Sie können einen der folgenden Befehle verwenden, um den Inhalt der ZIP-Datei in Ihren R-Code einzulesen:

	source("src/yourfile.R") # Reads a zipped R script
	load("src/yourData.rdata") # Reads a zipped R data file

> [AZURE.NOTE]Azure Machine Learning behandelt Dateien in dem ZIP, als wären sie im Verzeichnis "src/", weshalb Sie Ihren Dateinamen diesen Ordnernamen als Präfix voranstellen müssen.

Das Laden von DataSets haben wir bereits unter [Laden des DataSets](#loading) erläutert Nachdem Sie das im vorherigen Abschnitt gezeigte R-Skript erstellt und getestet haben, führen Sie folgende Schritte aus:

1. Speichern Sie das R-Skript in einer ".R"-Datei. Wir geben unserer Skriptdatei den Namen "simpleplot.R".  

2.  Erstellen Sie eine ZIP-Datei, und kopieren Sie Ihr Skript in diese ZIP-Datei.

3.	Fügen Sie Ihre Datei den **DataSets** in Machine Learning Studio hinzu, und geben Sie dabei den Typ **zip** an. Die ZIP-Datei sollte jetzt in Ihren DataSets angezeigt werden.

4.	Ziehen Sie die ZIP-Datei aus **DataSets** auf den **ML Studio-Bereich**, und legen Sie sie dort ab.

5.	Verbinden Sie die Ausgabe des **ZIP-Daten**-Symbols mit der Eingabe **Script Bundle** des[Execute R Script][execute-r-script]-Moduls.

6.	Geben Sie die `source()`-Funktion mit dem Namen Ihrer ZIP-Datei in das Codefenster des [Execute R Script][execute-r-script]-Moduls ein. In diesem Fall geben Sie `source("src/SimplePlot.R")` ein.

7.	Denken Sie daran, auf **Speichern** zu klicken.

Nachdem diese Schritte abgeschlossen sind, führt das [Execute R Script][execute-r-script]-Modul das R-Skript aus der ZIP-Datei aus, wenn das Experiment ausgeführt wird. An diesem Punkt sollte das Experiment ungefähr wie in Abbildung 5 aussehen.

![Experiment unter Verwendung des ZIP-komprimierten R-Skripts][6]

*Abbildung 5: Experiment unter Verwendung des ZIP-komprimierten R-Skripts.*

#### Dataset1

Sie können mithilfe der Eingabe "Dataset1" eine rechteckige Datentabelle an Ihren R-Code übergeben. In unserem einfachen Skript liest die `maml.mapInputPort(1)`-Funktion die Daten von Port 1. Diese Daten werden dann einem Dataframe-Variablennamen in Ihrem Code zugewiesen. In unserem einfachen Skript führt die erste Zeile des Codes die Zuweisung durch.

	cadairydata <- maml.mapInputPort(1)

Führen Sie das Experiment aus, indem Sie auf die Schaltfläche **Ausführen** klicken. Wenn die Ausführung abgeschlossen ist, klicken Sie auf das [Execute R Script][execute-r-script]-Modul und dann im Bereich "Properties" auf **View output log**. In Ihrem Browser sollte eine neue Seite mit dem Inhalt der Datei "Output.log" angezeigt werden. Wenn Sie nach unten scrollen, sollte etwa Folgendes angezeigt werden:

	[ModuleOutput] [1] "Loading variable port1..."
	[ModuleOutput]
	[ModuleOutput] 'data.frame':	228 obs. of  9 variables:
	[ModuleOutput]
	[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
	[ModuleOutput]
	[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
	[ModuleOutput]
	[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
	[ModuleOutput]
	[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
	[ModuleOutput]
	[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
	[ModuleOutput]
	[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
	[ModuleOutput]
	[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
	[ModuleOutput]
	[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
	[ModuleOutput]
	[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Diese Ergebnisse sind größtenteils wie erwartet, mit 228 Beobachtungen und 9 Spalten im Dataframe. Wir sehen die Spaltennamen, den R-Datentyp und ein Beispiel für jede Spalte.

> [AZURE.NOTE]Dieselbe Druckausgabe steht bequem über die Ausgabe "R Device" des [Execute R Script][execute-r-script]-Moduls zur Verfügung. Die Ausgaben des [Execute R Script][execute-r-script]-Moduls werden im nächsten Abschnitt erklärt.

####Dataset2

Das Verhalten der Eingabe "Dataset2" ist mit der von Dataset1 identisch. Mit dieser Eingabe können Sie eine zweite rechteckige Datentabelle an Ihren R-Code übergeben. Die `maml.mapInputPort(2)`-Funktion wird mit dem Argument 2 verwendet, um diese Daten zu übergeben.

###"Execute R Script"-Ausgaben

####Ausgeben eines Dataframes

Sie können den Inhalt eines R-Dataframes mithilfe der `maml.mapOutputPort()`-Funktion als rechteckige Tabelle über den Ergebnisport "Dataset1" ausgeben. In unserem einfachen R-Skript erfolgt dies durch die folgende Zeile.

	maml.mapOutputPort('cadairydata')

Klicken Sie nach dem Ausführen des Experiments auf den Ergebnisausgabeport "Dataset1" und dann auf **Visualisieren**. Ihre Anzeige sollte der in Abbildung 6 ähneln.

![Visualisierung der Ausgabe der kalifornischen Milchproduktdaten][7]

*Abbildung 6: Visualisierung der Ausgabe der kalifornischen Milchproduktdaten.*

Diese Ausgabe ist identisch mit der Eingabe, genau wie erwartet.

###	"R Device"-Ausgabe

Die "R Device"-Ausgabe des [Execute R Script][execute-r-script]-Moduls enthält Meldungen und Grafikausgaben. Sowohl die Standardausgabe als auch die Standardfehlermeldungen von R werden an den "R Device"-Ausgabeport gesendet.

Um die "R Device"-Ausgabe anzuzeigen, klicken Sie auf den Port und dann auf **Visualisieren**. Die Standardausgabe und Standardfehlermeldung des R-Skripts werden wie in Abbildung 7 angezeigt.

![Standardausgabe und Standardfehlermeldung des "R Device"-Ports][8]

*Abbildung 7: Standardausgabe und Standardfehlermeldung des "R Device"-Ports.*

Wenn wir nach unten scrollen, sehen wir die Grafikausgabe unseres R-Skripts wie in Abbildung 8.

![Grafikausgabe des "R Device"-Ports][9]

*Abbildung 8: Grafikausgabe des "R Device"-Ports.*

##<a id="filtering"></a>Filtern und Transformieren von Daten

In diesem Abschnitt werden wir einige grundlegende Vorgänge zum Filtern und Transformieren von Daten mit den kalifornischen Milchproduktdaten ausführen. Am Ende dieses Abschnitts erhalten wir dann Daten in einem Format, das für die Erstellung eines Analysemodells geeignet ist.

Wir werden in diesem Abschnitt insbesondere mehrere allgemeine Aufgaben zur Datenbereinigung und -transformation durchführen: Typtransformationen, Filtern von Dataframes, Hinzufügen neuer, berechneter Spalten und Werttransformationen. Dieser Hintergrund sollte Ihnen beim Umgang mit den vielen Variationen, mit denen Sie in Szenarios der Realität konfrontiert werden, helfen.

Der vollständige R-Code für diesen Abschnitt befindet sich in der ZIP-Datei, die Sie zuvor heruntergeladen haben.

###	Typtransformationen

Nachdem wir nun die kalifornischen Milchproduktdaten in den R-Code im [Execute R Script][execute-r-script]-Modul einlesen können, müssen wir sicherstellen, dass die Daten in den Spalten den gewünschten Typ und das gewünschte Format aufweisen.

R ist eine dynamisch typisierte Sprache, d. h., dass die Datentypen ganz nach Bedarf vom einen in den anderen umgewandelt werden. Die atomaren Datentypen in R umfassen die Typen numerisch, logisch und Zeichen. Der Faktortyp wird verwendet, um Kategoriedaten kompakt zu speichern. Weitere Informationen zu Datentypen finden Sie in den Verweisen im [Anhang B – Weitere Informationen](#appendixb).

Wenn die Tabellendaten aus einer externen Quelle in R eingelesen werden, ist es immer eine gute Idee, die resultierenden Typen in den Spalten zu überprüfen. Möglicherweise benötigen Sie eine Spalte vom Typ "Zeichen", aber in vielen Fällen wird diese als "Faktor" oder umgekehrt angezeigt. In anderen Fällen werden die Daten in einer Spalte, die eigentlich numerisch sein sollte, als Zeichendaten dargestellt, z. B. "1,23" anstelle der Gleitkommazahl 1,23.

Glücklicherweise lassen sich die Typen einfach untereinander, solange eine Zuordnung möglich ist. Zum Beispiel können Sie "Nevada" nicht in einen numerischen Wert konvertieren, aber Sie können es in einen Faktor (Kategorievariable) konvertieren. Als weiteres Beispiel können Sie eine numerische 1 in ein Zeichen "1" oder einen Faktor konvertieren.

Die Syntax für jede dieser Konvertierungen ist einfach: `as.datatype()`. Diese Typkonvertierungsfunktionen umfassen Folgendes:

* `as.numeric()`

* `as.character()`

* `as.logical()`

* `as.factor()`

Untersuchen der Datentypen der Spalten, die wir im vorherigen Abschnitt eingegeben haben: Alle Spalten sind vom Typ "numerisch", mit Ausnahme der Spalte mit der Bezeichnung "Month", die vom Typ "Zeichen" ist. Lassen Sie uns nun diese Spalte in den Typ "Faktor" konvertieren und die Ergebnisse testen.

Ich haben die Zeile gelöscht, die die Punktdiagrammmatrix erstellt, und eine Zeile hinzugefügt, die die Spalte "Month" in den Typ "Faktor" konvertiert. In diesem Experiment schneiden wir den R-Code einfach aus und fügen ihn in das Codefenster des [Execute R Script][execute-r-script]-Moduls ein. Sie könnten auch die ZIP-Datei aktualisieren und in Azure Machine Learning Studio hochladen, aber dies erfordert mehrere Schritte.

	## Only one of the following two lines should be used
	## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
	## If in RStudio, use the second line with read.csv()
	cadairydata <- maml.mapInputPort(1)
	# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
	## Ensure the coding is consistent and convert column to a factor
	cadairydata$Month <- as.factor(cadairydata$Month)
	str(cadairydata) # Check the result
	## The following line should be executed only when running in
	## Azure Machine Learning Studio
	maml.mapOutputPort('cadairydata')

Führen wir nun diesen Code aus, und sehen wir und die Ausgabe an. Die Ausgabe von **Visualisieren** im Menü "R Device" wird in Abbildung 9 dargestellt.

![Zusammenfassung des Dataframes mit einer Faktor-Variable][10]

*Abbildung 9: Zusammenfassung des Dataframes mit einer Faktor-Variable.*

Der Typ von "Month" sollte nun "**Factor w/ 14 levels**" lauten. Dies ist ein Problem, weil es nur 12 Monate pro Jahr gibt. Sie können auch überprüfen, ob der Typ in **Visualisieren** des "Result Dataset"-Ports "**Categorical**" ist.

Das Problem ist, dass die Spalte "Month" nicht systematisch codiert wurde. In einigen Fällen wird ein Monat als "April" bezeichnet, während er in anderen Fällen als "Apr" abgekürzt wird. Wir können dieses Problem beheben, indem wir die Zeichenfolge auf drei Zeichen kürzen. Die Codezeile sieht nun folgendermaßen aus:

	## Ensure the coding is consistent and convert column to a factor
	cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Führen Sie das Experiment erneut aus, und **Visualisieren** Sie die Ausgabe am "R Device"-Port mit den in Abbildung 10 dargestellten Ergebnissen.

![Zusammenfassung des Dataframes mit korrekter Anzahl von Faktorebenen][11]

*Abbildung 10: Zusammenfassung des Dataframes mit korrekter Anzahl von Faktorebenen.*

Unsere Faktor-Variable verfügt jetzt über die gewünschten 12 Ebenen.

###Grundlegendes Filtern von Dataframes

R-Dataframes unterstützen leistungsstarke Filterfunktionen. Datasets können mithilfe von auf Zeilen oder Spalten angewendeten logischen Filtern in Teilmengen unterteilt werden. In vielen Fällen sind komplexe Filterkriterien erforderlich. Die Verweise in [Anhang B – Weitere Informationen](#appendixb) enthalten ausführliche Beispiele zum Filtern von Dataframes.

Wir sollten unser Dataset ein wenig filtern. Wenn Sie die Spalten im Dataframe "cadariydata" betrachten, sehen Sie zwei nicht benötigte Spalten. Die erste Spalte enthält lediglich eine Zeilennummer, die nicht sehr nützlich ist. Die zweite Spalte, "Year.Month", enthält redundante Informationen. Wir können diese Spalten ganz einfach mithilfe des folgenden R-Codes ausschließen.

> [AZURE.NOTE]Von nun an zeige ich Ihnen in diesem Abschnitt nur noch den zusätzlichen Code, den wir im [Execute R Script][execute-r-script]-Modul hinzufügen. Alle neuen Zeilen werden **vor** der `str()`-Funktion eingefügt. Wir verwenden diese Funktion zum Überprüfen unserer Ergebnisse in Azure Machine Learning Studio.

Wir fügen die folgende Zeile dem R-Code im [Execute R Script][execute-r-script]-Modul hinzu.

	# Remove two columns we do not need
	cadairydata <- cadairydata[, c(-1, -2)]

Führen Sie diesen Code in Ihrem Experiment aus, und überprüfen Sie das Ergebnis mit **Visualisieren** am "R Device"-Port. Die Ergebnisse werden in Abbildung 11 dargestellt.

![Zusammenfassung des Dataframes mit zwei entfernten Spalten][12]

*Abbildung 11: Zusammenfassung des Dataframes mit zwei entfernten Spalten.*

Gute Nachrichten! Wir haben die erwarteten Ergebnisse erhalten.

###Hinzufügen einer neuen Spalte

Beim Erstellen von Zeitreihenmodellen ist es praktisch, wenn eine Spalte vorhanden ist, die die Monate seit dem Start der Zeitreihe enthält. Wir erstellen eine neue Spalte "Month.Count".

Zur besseren Organisation des Codes erstellen wir unsere erste einfache Funktion: `num.month()`. Wir wenden diese Funktion dann an, um im Dataframe eine neue Spalte zu erstellen. Der neue Code lautet wie folgt:

	## Create a new column with the month count
	## Function to find the number of months from the first
	## month of the time series
	num.month <- function(Year, Month) {
	  ## Find the starting year
	  min.year  <- min(Year)

	  ## Compute the number of months from the start of the time series
	  12 * (Year - min.year) + Month - 1
	}

	## Compute the new column for the dataframe
	cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)

Führen Sie nun das aktualisierte Experiment aus, und verwenden Sie **Visualisieren** am "R Device"-Port, um die Ergebnisse anzuzeigen. Diese Ergebnisse werden in Abbildung 12 dargestellt.

![Zusammenfassung des Dataframes mit der zusätzlichen Spalte][13]

*Abbildung 12: Zusammenfassung des Dataframes mit der zusätzlichen Spalte.*

Es scheint alles zu funktionieren. Wir haben jetzt die neue Spalte mit den erwarteten Werten in unserem Dataframe.

###Werttransformationen

In diesem Abschnitt führen wir einige einfache Transformationen der Werte in einigen Spalten unseres Dataframes durch. Die R-Sprache unterstützt nahezu beliebige Werttransformationen. Die Verweise in [Anhang B – Weitere Informationen](#appendixb) enthalten ausführliche Beispiele.

Wenn Sie sich die Werte in den Zusammenfassungen unseres Dataframes ansehen, sollte Ihnen etwas unpassend vorkommen. Wird in Kalifornien mehr Speiseeis erzeugt, als Milch produziert wird? Nein, natürlich nicht, da dies sinnlos ist, wenn diese Tatsache die Eisliebhaber unter uns auch traurig stimmen mag. Die Einheiten sind unterschiedlich. Der Preis wird in Einheiten von US-Pfund angegeben, Milch in Einheiten von 1 M US-Pfund und Speiseeis in Einheiten von 1.000 US-Gallonen sowie Hüttenkäse in Einheiten von 1.000 US-Pfund. Wenn Speiseeis z. B. ca. 6,5 Pfund pro Gallone wiegt, können wir ganz einfach die Multiplikation durchführen, um diese Werte so zu konvertieren, dass sie alle in gleichen Einheiten von 1.000 Pfund angegeben werden.

Für unser Vorhersagemodell verwenden wir ein multiplikatives Modell für die Trend- und saisonale Anpassung dieser Daten. Eine logarithmische Transformation ermöglicht uns, ein lineares Modell zu verwenden, was diesen Prozess vereinfacht. Wir können die logarithmische Transformation in derselben Funktion anwenden, in der der Multiplikator angewendet wird.

Im folgenden Code definieren wir eine neue `log.transform()`-Funktion und wenden sie auf die Zeilen an, die die numerischen Werte enthalten. Die R-Funktion `Map()` wird verwendet, um die `log.transform()`-Funktion auf die ausgewählten Spalten des Dataframes anzuwenden. `Map()` ähnelt `apply()`, gestattet der Funktion aber mehr als eine Liste von Argumenten. Beachten Sie, dass eine Liste von Multiplikatoren das zweite Argument für die `log.transform()`-Funktion bereitstellt. Die `na.omit()`-Funktion wird praktisch zur Bereinigung verwendet, um sicherzustellen, dass im Dataframe keine Werte fehlen bzw. keine nicht definierten Werte enthalten sind.

	log.transform <- function(invec, multiplier = 1) {
	  ## Function for the transformation, which is the log
	  ## of the input value times a multiplier

	  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
	                       "ERROR: Arguments to function log.transform must be greate than zero",
	                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
	                       "ERROR: Invalid time seies value encountered in function log.transform"
	                       )

	  ## Check the input arguments
	  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
	  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
	  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

	  ## Wrap the multiplication in tryCatch
	  ## If there is an exception, print the warningmessage to
	  ## standard error and return NA
	  tryCatch(log(multiplier * invec),
	           error = function(e){warning(warningmessages[4]); NA})
	}


	## Apply the transformation function to the 4 columns
	## of the dataframe with production data
	multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
	cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

	## Get rid of any rows with NA values
	cadairydata <- na.omit(cadairydata)  

In der `log.transform()`-Funktion geschieht einiges. Der größte Teil dieses Codes sucht nach potenziellen Problemen mit den Argumenten oder behandelt Ausnahmen, die bei den Berechnungen immer noch auftreten können. Es sind nur einige Zeilen dieses Codes, in denen die tatsächlichen Berechnungen durchgeführt werden.

Ziel der defensiven Programmierung ist es, den Ausfall einer einzelnen Funktion zu verhindern, durch den die Fortsetzung der Verarbeitung verhindert werden könnte. Ein abruptes Fehlschlagen einer lang andauernden Analyse kann für die Benutzer sehr frustrierend sein. Um diese Situation zu vermeiden, müssen Standardrückgabewerte ausgewählt werden, die Schäden an der Downstreamverarbeitung begrenzen. Es wird auch eine Nachricht zur Benachrichtigung der Benutzer erzeugt, dass ein Fehler aufgetreten ist.

Wenn Sie mit der defensiven Programmierung in R nicht vertraut sind, kann dieser Code ein wenig verwirrend sein. Wir gehen die wichtigsten Schritte gemeinsam durch:

1. Ein Vektor aus vier Meldungen wird definiert. Diese dienen der Kommunikation von Informationen über einige der möglichen Fehler und Ausnahmen, die bei diesem Code auftreten können.

2. Für jeden Fall geben wir einen Wert von "NA" zurück. Es gibt viele weitere Möglichkeiten, die eventuell weniger Nebeneffekte haben. Wir könnten einen Vektor aus Nullen zurückgeben oder z. B. den ursprünglichen Eingabevektor.

3. Es werden Tests mit den Argumenten der Funktion ausgeführt. In jedem Fall wird beim Erkennen eines Fehlers ein Standardwert zurückgegeben und von der `warming()`-Funktion eine Meldung erzeugt. Wir verwenden `warning()` anstelle von `stop()`, da die letztere Funktion die Ausführung beendet – und genau das versuchen wir zu vermeiden. Beachten Sie, dass dieser Code in einem prozeduralen Stil geschrieben ist, da in diesem Fall ein funktionaler Ansatz zu komplex und undurchsichtig erschien.

4. Die logarithmischen Berechnungen sind in `tryCatch()` eingeschlossen, damit Ausnahmen keine abrupte Unterbrechung der Verarbeitung verursachen können. Ohne `tryCatch()` erzeugen die meisten von R-Funktionen ausgelösten Fehler ein Stoppsignal (Beenden), was genau dies bewirkt.

Führen Sie diesen R-Code in Ihrem Experiment aus, und sehen Sie sich die Ausgabe in der Datei "output.log" an. Sie zeigen die transformierten Werte der vier Spalten, wie in Abbildung 13 dargestellt, an, indem Sie im Menü "R Device" die Option **Visualisieren** auswählen.

![Zusammenfassung der transformierten Werte im Dataframe][14]

*Abbildung 13: Zusammenfassung der transformierten Werte im Dataframe.*

Wir sehen, dass die Werte transformiert wurden. Die Milchproduktion überschreitet nun erheblich die Produktion aller anderen Milchprodukte, wobei Sie sich in Erinnerung rufen müssen, dass wir jetzt eine logarithmische Skalierung verwenden.

An diesem Punkt sind unsere Daten bereinigt, und wir sind zur Modellierung bereit. Wenn Sie die Visualisierungszusammenfassung für die Ausgabe "Result Dataset" unseres [Execute R Script][execute-r-script]-Moduls betrachten, stellen Sie fest, dass die Spalte "Month" genau wie beabsichtigt "Categorical" mit 12 eindeutigen Werten ist.

##<a id="timeseries"></a>Zeitreihenobjekte und Korrelationsanalyse

In diesem Abschnitt behandeln wir ein paar grundlegende R-Zeitreihenobjekte und analysieren die Korrelationen zwischen einigen der Variablen. Unser Ziel ist es, ein Dataframe auszugeben, das die paarweisen Korrelationsinformationen an mehreren Verzögerungen enthält.

Der vollständige R-Code für diesen Abschnitt befindet sich in der ZIP-Datei, die Sie zuvor heruntergeladen haben.

###Zeitreihenobjekte in R

Wie bereits erwähnt, sind Zeitreihen Reihen von Datenwerten, die einen Zeitindex besitzen. R-Zeitreihenobjekte dienen zum Erstellen und Verwalten des Zeitindex. Es gibt mehrere Vorteile bei der Verwendung von Zeitreihenobjekten. Zeitreihenobjekte übernehmen für Sie zahlreiche Details der Verwaltung der Zeitreihen-Indexwerte, die im Objekt gekapselt sind. Darüber hinaus ermöglichen Zeitreihenobjekte die Verwendung der zahlreichen Zeitreihenmethoden zum Plotten, Drucken, Modellieren usw.

Die Zeitreihenklasse "POSIXct" wird häufig verwendet und ist relativ einfach. Diese Zeitreihenklasse misst die Zeit seit Beginn der Epoche, 1. Januar 1970. Wir verwenden in diesem Beispiel POSIXct-Zeitreihenobjekte. Andere weit verbreitete R-Zeitreihen-Objektklassen umfassen "zoo" und "xts" (für extensible time series, erweiterbare Zeitreihe). <!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

###	Beispiel für Zeitreihenobjekte

Beginnen wir mit unserem Beispiel. Ziehen Sie ein **neues** [Execute R Script][execute-r-script]-Modul per Drag & Drop in Ihr Experiment. Verbinden Sie den "Result Dataset1"-Ausgabeport des vorhandenen [Execute R Script][execute-r-script]-Moduls mit dem "Dataset1"-Eingabeport des neuen [Execute R Script][execute-r-script]-Moduls.

Wie bei den ersten Beispielen werden auch hier beim Durchlaufen des Beispiels an einigen Stellen nur die inkrementellen, zusätzlichen R-Codezeilen im jeweiligen Schritt gezeigt.

####	Lesen des Dataframes

Als ersten Schritt lesen wir einen Dataframe ein und stellen sicher, dass wir die erwarteten Ergebnisse erhalten. Der folgende Code sollte diese Aufgabe erledigen:

	# Comment the following if using RStudio
	cadairydata <- maml.mapInputPort(1)
	str(cadairydata) # Check the results

Führen Sie jetzt das Experiment aus. Die Ausgabe, die Sie am "R Device"-Port sehen, sollte wie in Abbildung 14 aussehen.

![Zusammenfassung des Dataframes im "Execute R Script"-Modul][15]

*Abbildung 14: Zusammenfassung des Dataframes im "Execute R Script"-Modul.*

Diese Daten haben die erwarteten Typen und Formate. Beachten Sie, dass die Spalte "Month" vom Typ "Faktor" ist und die erwartete Anzahl von Ebenen besitzt.

####Erstellen eines Zeitreihenobjekts

Wir müssen unserem Dataframe ein Zeitreihenobjekt hinzufügen. Ersetzen Sie den aktuellen Code durch den folgenden, der eine neue Spalte der "POSIXct"-Klasse hinzufügt.

	# Comment the following if using RStudio
	cadairydata <- maml.mapInputPort(1)

	## Create a new column as a POSIXct object
	Sys.setenv(TZ = "PST8PDT")
	cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

	str(cadairydata) # Check the results

Überprüfen Sie nun die "R Device"-Ausgabe. Diese sollte wie in Abbildung 15 aussehen.

![Zusammenfassung des Dataframes mit einem Zeitreihenobjekt][16]

*Abbildung 15: Zusammenfassung des Dataframes mit einem Zeitreihenobjekt.*

In der Zusammenfassung können wir sehen, dass die neue Spalte tatsächlich zur POSIXct-Klasse gehört.

###Untersuchen und Transformieren der Daten

Lassen Sie uns einige der Variablen in diesem DataSet untersuchen. Eine Streudiagrammmatrix ist eine gute Möglichkeit, um einen schnellen Einblick zu gewinnen. Wir ersetzen die `str()`-Funktion im vorherigen R-Code durch die folgende Zeile:

	pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Führen Sie diesen Code aus, und sehen Sie, was passiert. Die am "R Device"-Port erzeugte Grafik sollte wie in Abbildung 16 aussehen.

![Punktdiagrammmatrix der ausgewählten Variablen][17]

*Abbildung 16: Punktdiagrammmatrix der ausgewählten Variablen.*

Es gibt eine seltsam aussehende Struktur in den Beziehungen zwischen diesen Variablen. Möglicherweise wird diese von Trends in den Daten und durch die Tatsache erzeugt, dass wir die Variablen nicht standardisiert haben.

###Korrelationsanalyse

Um die Korrelationsanalyse durchzuführen, müssen wir Trends aus den Variablen entfernen und sie standardisieren. Wir könnten einfach die R-Funktion `scale()` verwenden, die Variablen sowohl zentriert als auch skaliert. Diese Funktion dürfte schneller ausgeführt werden. Ich möchte Ihnen aber ein Beispiel für defensive Programmierung in R zeigen.

Die unten dargestellte `ts.detrend()`-Funktion führt diesen beiden Vorgänge aus. Die folgenden zwei Codezeilen entfernen Trends aus den Daten und standardisieren dann die Werte.

	ts.detrend <- function(ts, Time, min.length = 3){
	  ## Function to de-trend and standardize a time series

	  ## Define some messages if they are NULL  
	  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
	                'ERROR: ts.detrend requires argument ts to be of type numeric',
	                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
	                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
	                'ERROR: Detrend regression has failed in ts.detrend',
	                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  	)
	  # Create a vector of zeros to return as a default in some cases
	  zerovec  <- rep(length(ts), 0.0)

	  # The input arguments are not of the same length, return ts and quit
	  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

	  # If the ts is not numeric, just return a zero vector and quit
	  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

	  # If the ts is too short, just return it and quit
	  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

	  ## Check that the Time variable is of class POSIXct
	  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

	  ## De-trend the time series by using a linear model
	  ts.frame  <- data.frame(ts = ts, Time = Time)
	  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
	           error = function(e){warning(messages[5]); zerovec})

	  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
	             ts <- ts/stdev},
	            error = function(e){warning(messages[6]); zerovec})

	  ts
	}  
	## Apply the detrend.ts function to the variables of interest
	df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

	## Plot the results to look at the relationships
	pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")

In der `ts.detrend()`-Funktion geschieht einiges. Der größte Teil dieses Codes sucht nach potenziellen Problemen mit den Argumenten oder behandelt Ausnahmen, die bei den Berechnungen immer noch auftreten können. Es sind nur einige Zeilen dieses Codes, in denen die tatsächlichen Berechnungen durchgeführt werden.

Wir haben unter [Werttransformationen](#valuetransformations) bereits ein Beispiel für die defensive Programmierung besprochen Beide Berechnungsblöcke werden in `tryCatch()` eingeschlossen. Bei einigen Fehlern ist es sinnvoll, den ursprünglichen Eingabevektor zurückzugeben, während in anderen Fällen ein Vektor aus Nullen zurückgegeben wird.

Beachten Sie, dass die für die Beseitigung von Trends verwendete lineare Regression eine Zeitreihenregression ist. Die Vorhersagevariable ist ein Zeitreihenobjekt.

Nachdem `ts.detrend()` definiert wurde, wenden wir die Funktion auf die für uns interessanten Variablen in unserem Dataframe an. Wir müssen die resultierende Liste, die von `lapply()` erstellt wurde, mithilfe von `as.data.frame()` in ein Dataframe umwandeln. Aufgrund der defensiven Aspekte von `ts.detrend()` verhindert ein Fehler bei der Verarbeitung einer der Variablen nicht die ordnungsgemäße Verarbeitung der anderen.

Die letzte Zeile des Codes erstellt ein paarweises Streudiagramm. Nach der Ausführung des R-Codes werden die Ergebnisse des Punktdiagramms in Abbildung 17 dargestellt.

![Paarweises Punktdiagramm der von Trends befreiten und standardisierten Zeitreihe][18]

*Abbildung 17: Paarweises Punktdiagramm der von Trends befreiten und standardisierten Zeitreihe.*

Sie können diese Ergebnisse mit den in Abbildung 17 dargestellten vergleichen. Nachdem Trends aus den Variablen entfernt und sie standardisiert wurden, sehen wir viel weniger Strukturen in den Beziehungen zwischen diesen Variablen.

Der Code zum Berechnen der Korrelationen als ccf-Objekte in R lautet wie folgt:

	## A function to compute pairwise correlations from a
	## list of time series value vectors
	pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
	  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
	}

	## A list of the pairwise indices
	corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

	## Compute the list of ccf objects
	cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

	cadairycorrelations

Das Ausführen des Codes erzeugt die in Abbildung 18 dargestellte Ausgabe.

![Liste der ccf-Objekte aus der paarweisen Korrelationsanalyse][19]

*Abbildung 18: Liste der ccf-Objekte aus der paarweisen Korrelationsanalyse.*

Es gibt einen Korrelationswert für jede Verzögerung. Keiner dieser Korrelationswerte ist groß genug, um signifikant zu sein. Wir können daher daraus schließen, dass wir jede Variable unabhängig modellieren können.

###Ausgeben eines Dataframes

Wir haben die paarweisen Korrelationen als Liste von ccf-Objekten in R berechnet. Dies stellt ein gewisses Problem dar, da der "Result Dataset"-Ausgabeport tatsächlich ein Dataframe erfordert. Darüber hinaus ist das ccf-Objekt selbst eine Liste, und wir benötigen nur die Werte im ersten Element dieser Liste, also die Korrelationen an den verschiedenen Verzögerungen.

Der folgende Code extrahiert die Verzögerungswerte aus der Liste der ccf-Objekte, die ihrerseits aufgelistet werden:

	df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

	c.names <- c("-1 lag", "0 lag", "+1 lag")
	r.names  <- c("Corr Cot Cheese - Ice Cream",
	              "Corr Cot Cheese - Milk Prod",
	              "Corr Cot Cheese - Fat Price",
	              "Corr Ice Cream - Mik Prod",
	              "Corr Ice Cream - Fat Price",
	              "Corr Milk Prod - Fat Price")

	## Build a dataframe with the row names column and the
	## correlation data frame and assign the column names
	outframe <- cbind(r.names, df.correlations)
	colnames(outframe) <- c.names
	outframe


	## WARNING!
	## The following line works only in Azure Machine Learning
	## When running in RStudio, this code will result in an error
	#maml.mapOutputPort('outframe')

Die erste Codezeile ist etwas schwieriger, doch einige Erläuterungen sollten Ihnen helfen, sie zu verstehen. Wenn wir von innen nach außen vorgehen, erhalten wir Folgendes:

1.  Der "**[[**"-Operator mit dem Argument "**1**" wählt den Vektor der Korrelationen an den Verzögerungen aus dem ersten Element der ccf-Objektliste aus.

2.  Die `do.call()`-Funktion wendet die `rbind()`-Funktion auf die Elemente der Liste an, die von `lapply()` zurückgegeben wird.

3.  Die `data.frame()`-Funktion wandelt das von `do.call()` erzeugte Ergebnis in ein Dataframe um.

Beachten Sie, dass sich die Zeilennamen in einer Spalte des Dataframes befinden. Auf diese Weise bleiben die Zeilennamen erhalten, wenn sie von [Execute R Script][execute-r-script] ausgegeben werden.

Das Ausführen des Codes erzeugt die in Abbildung 19 dargestellte Ausgabe, wenn Sie die Ausgabe am "Result Dataset"-Port **Visualisieren**. Die Zeilennamen sind wie vorgesehen in der ersten Spalte.

![Ergebnisausgabe der Korrelationsanalyse][20]

*Abbildung 19: Ergebnisausgabe der Korrelationsanalyse.*

##<a id="seasonalforecasting"></a>Zeitreihenbeispiel: saisonale Vorhersagen

Unsere Daten liegen nun in einer Form vor, die für die Analyse geeignet ist, und wir haben festgestellt, dass keine nennenswerten Korrelationen zwischen den Variablen bestehen. Lassen Sie uns nun fortfahren und ein Zeitreihen-Vorhersagemodell erstellen. Mit diesem Modell werden wir die kalifornische Milchproduktion für die 12 Monate von 2013 vorhersagen.

Unser Vorhersagemodell wird aus zwei Komponenten bestehen: einer Trendkomponente und einer saisonalen Komponente. Die vollständige Vorhersage ist das Produkt aus diesen beiden Komponenten. Dieser Typ von Modell ist auch als multiplikatives Modell bekannt. Die Alternative ist ein additives Modell. Wir haben bereits eine logarithmische Transformation auf die betreffenden Variablen angewendet, wodurch diese Analyse kontrollierbar wird.

Der vollständige R-Code für diesen Abschnitt befindet sich in der ZIP-Datei, die Sie zuvor heruntergeladen haben.

###	Erstellen des Dataframes für die Analyse

Beginnen Sie damit, dass Sie Ihrem Experiment ein **neues** [Execute R Script][execute-r-script]-Modul hinzufügen. Verbinden Sie den **Result Dataset**-Ausgabeport des vorhandenen [Execute R Script][execute-r-script]-Moduls mit dem **Dataset1**-Eingabeport des neuen Moduls. Das Ergebnis sollte in etwa wie in Abbildung 20 aussehen.

![Das Experiment mit hinzugefügtem neuem "Execute R Script"-Modul][21]

*Abbildung 20: Das Experiment mit hinzugefügtem neuem "Execute R Script"-Modul.*

Wie bei der gerade abgeschlossenen Korrelationsanalyse, müssen wir eine Spalte mit einem POSIXct-Zeitreihenobjekt hinzufügen. Der folgende Code soll genau diese Aufgabe erledigen:

	# If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
	cadairydata <- maml.mapInputPort(1)

	## Create a new column as a POSIXct object
	Sys.setenv(TZ = "PST8PDT")
	cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

	str(cadairydata)

Führen Sie diesen Code aus, und sehen Sie sich den "R Device"-Ausgabeport an. Das Ergebnis sollte wie in Abbildung 21 aussehen.

![Eine Zusammenfassung des Dataframes][22]

*Abbildung 21: Eine Zusammenfassung des Dataframes.*

Mit diesem Ergebnis sind wir bereit, um unsere Analyse zu starten.

###Erstellen eines Trainings-DataSets

Mit dem erzeugten Dataframe müssen wir ein Trainings-Dataset erstellen. Diese Daten enthalten dann alle Beobachtungen im Jahr 2013, außer den letzten 12, bei denen es sich um unser Testdataset handelt. Der folgende Code unterteilt das Dataframe in Teilmengen und erstellt grafische Darstellungen der Milchproduktions- und -preisvariablen. Anschließend erstellen wir grafische Darstellungen der vier Produktions- und Preisvariablen. Es wird eine anonyme Funktion verwendet, um einige Argumente zur grafischen Darstellung zu definieren und dann eine Iteration durch die Liste der anderen beiden Argumente mit `Map()` durchzuführen. Wenn Sie der Meinung sind, dass an dieser Stelle auch eine For-Schleife gut funktioniert hätte, liegen Sie richtig. Aber da R eine funktionale Sprache ist, zeigen wir Ihnen hier einen funktionalen Ansatz.

	cadairytrain <- cadairydata[1:216, ]

	Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
	               "Log CA Ice Cream Production, 1000s lb",
	               "Log CA Milk Production 1000s lb",
	               "Log North CA Milk Milk Fat Price per 1000 lb")

	Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

Das Ausführen des Codes erzeugt die Serie von Zeitreihengrafiken aus der "R Device"-Ausgabe, die in Abbildung 22 dargestellt sind. Beachten Sie, dass die Einheit der Zeitachse Daten sind, ein praktischer Vorteil der Zeitreihen-Plotmethode.

![Erste Zeitreihengrafik von kalifornischen Milchproduktions- und -preisdaten](./media/machine-learning-r-quickstart/unnamed-chunk-161.png)

![Zweite Zeitreihengrafik von kalifornischen Milchproduktions- und -preisdaten](./media/machine-learning-r-quickstart/unnamed-chunk-162.png)

![Dritte Zeitreihengrafik von kalifornischen Milchproduktions- und -preisdaten](./media/machine-learning-r-quickstart/unnamed-chunk-163.png)

![Vierte Zeitreihengrafik von kalifornischen Milchproduktions- und -preisdaten](./media/machine-learning-r-quickstart/unnamed-chunk-164.png)

*Abbildung 22: Zeitreihengrafiken von kalifornischen Milchproduktions- und -preisdaten.*

###	Ein Trendmodell

Nachdem wir nun ein Zeitreihenobjekt erstellt und uns die Daten angesehen haben, beginnen wir nun damit, ein Trendmodell für die kalifornischen Milchproduktionsdaten zu erstellen. Hierzu können wir eine Zeitreihenregression verwenden. Aus der Grafik wird jedoch deutlich, dass wir mehr als eine Steigung (slope) und ein konstantes Glied (intercept) benötigen, um den in den Trainingsdaten beobachteten Trend präzise zu modellieren.

Angesichts des geringen Umfangs der Daten erstellen wir das Modell für den Trend in RStudio, schneiden das resultierende Modell dann aus und fügen es in Azure Machine Learning ein. RStudio bietet eine interaktive Umgebung für diese Art von interaktiver Analyse.

Als einen ersten Versuch probieren wir es mit einer polynomialen Regression mit Potenzen bis 3. Es besteht die realistische Gefahr, diese Arten von Modellen zu stark anzupassen. Daher ist es am besten, Terme höherer Ordnung zu vermeiden. Die `I()`-Funktion unterdrückt die Interpretation des Inhalts (der Inhalt wird ohne Anpassungen interpretiert) und ermöglicht es Ihnen, eine literal interpretierte Funktion in einer Regressionsgleichung zu schreiben.

	milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
	summary(milk.lm)

Diese generiert Folgendes:

	##
	## Call:
	## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
	##     data = cadairytrain)
	##
	## Residuals:
	##      Min       1Q   Median       3Q      Max
	## -0.12667 -0.02730  0.00236  0.02943  0.10586
	##
	## Coefficients:
	##                   Estimate Std. Error t value Pr(>|t|)
	## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
	## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
	## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
	## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
	## ---
	## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
	##
	## Residual standard error: 0.0418 on 212 degrees of freedom
	## Multiple R-squared:  0.941,	Adjusted R-squared:  0.94
	## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

An den p-Werten (Pr(>|t|)) in dieser Ausgabe können wir erkennen, dass der quadrierte Term möglicherweise nicht signifikant ist. Wir verwenden die `update()`-Funktion zum Ändern dieses Modells, indem wir den quadrierten Term löschen.

	milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
	summary(milk.lm)

Diese generiert Folgendes:

	##
	## Call:
	## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
	##
	## Residuals:
	##      Min       1Q   Median       3Q      Max
	## -0.12597 -0.02659  0.00185  0.02963  0.10696
	##
	## Coefficients:
	##                   Estimate Std. Error t value Pr(>|t|)
	## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
	## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
	## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
	## ---
	## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
	##
	## Residual standard error: 0.0417 on 213 degrees of freedom
	## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
	## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Das sieht schon besser aus. Alle Terme sind signifikant. Der "2e-16"-Wert ist aber ein Standardwert und sollte nicht zu ernst genommen werden.

Als Funktionstest erstellen wir eine Zeitreihengrafik der kalifornischen Milchproduktionsdaten mit der angezeigten Trendkurve. Ich habe den folgenden Code im [Execute R Script][execute-r-script]-Modul von Azure Machine Learning (nicht in RStudio) hinzugefügt, um das Modell sowie eine Grafik davon zu erstellen. Das Ergebnis wird in Abbildung 23 dargestellt.

	milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

	plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
	lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![Kalifornische Milchproduktionsdaten mit angezeigtem Trendmodell](./media/machine-learning-r-quickstart/unnamed-chunk-18.png)

*Abbildung 23: Kalifornische Milchproduktionsdaten mit angezeigtem Trendmodell.*

Es sieht so aus, als ob das Trendmodell ziemlich gut den Daten angepasst ist. Darüber hinaus scheint es keine Anzeichen für Überanpassung zu geben, wie z. B. merkwürdige Ausreißer in der Modellkurve.

###Saisonales Modell

Mit dem uns jetzt zur Verfügung stehenden Trendmodell fahren wir fort und berücksichtigen die saisonalen Auswirkungen. Wir verwenden den Monat des Jahres als Platzhaltervariable im linearen Modell, um die Auswirkungen von Monat zu Monat zu erfassen. Beachten Sie, dass bei Einführung von Faktorvariablen in einem Modell das konstante Glied nicht berechnet werden darf. Wenn Sie dies versäumen, ist die Gleichung überspezifiziert, und R lässt einen der gewünschten Faktoren fallen, behält aber den konstanten Gliedterm bei.

Da wir ein zufriedenstellendes Trendmodell haben, können wir die `update()`-Funktion verwenden, um dem vorhandenen Modell die neuen Terme hinzuzufügen. Durch die "-1" in der "update"-Gleichung wird der konstante Gliedterm fallen gelassen. Für den Moment fahren wir in RStudio fort:

	milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
	summary(milk.lm2)

Diese generiert Folgendes:

	##
	## Call:
	## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
	##     data = cadairytrain)
	##
	## Residuals:
	##      Min       1Q   Median       3Q      Max
	## -0.06879 -0.01693  0.00346  0.01543  0.08726
	##
	## Coefficients:
	##                   Estimate Std. Error t value Pr(>|t|)
	## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
	## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
	## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
	## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
	## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
	## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
	## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
	## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
	## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
	## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
	## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
	## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
	## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
	## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
	## ---
	## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
	##
	## Residual standard error: 0.0263 on 202 degrees of freedom
	## Multiple R-squared:     1,	Adjusted R-squared:     1
	## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Wir sehen, dass das Modell keinen konstanten Gliedterm mehr und 12 signifikante Monatsfaktoren hat. Dies ist genau das, was wir sehen wollten.

Lassen Sie uns eine weitere Zeitreihengrafik der kalifornischen Milchproduktionsdaten erstellen, um zu sehen, wie gut das saisonale Modell funktioniert. Ich habe den folgenden Code im [Execute R Script][execute-r-script]-Modul von Azure Machine Learning hinzugefügt, um das Modell sowie eine Grafik davon zu erstellen.

	milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

	plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
	lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Das Ausführen des Codes in Azure Machine Learning erzeugt die in Abbildung 24 dargestellte Grafik.

![Kalifornische Milchproduktion mit Modell einschließlich saisonaler Auswirkungen](./media/machine-learning-r-quickstart/unnamed-chunk-20.png)

*Abbildung 24: Kalifornische Milchproduktion mit Modell, einschließlich saisonaler Auswirkungen.*

Die in Abbildung 24 dargestellte Anpassung an die Daten ist ziemlich vielversprechend. Sowohl der Trend als auch die saisonalen Auswirkungen (monatliche Variation) sehen vernünftig aus.

Als weiteren Test unseres Modells sehen wir uns die Residuen an. Der folgende Code berechnet die vorhergesagten Werte aus unseren beiden Modellen und die Restdaten (Residuen) für das saisonale Modell und erstellt dann eine Grafik dieser Restdaten für die Trainingsdaten.

	## Compute predictions from our models
	predict1  <- predict(milk.lm, cadairydata)
	predict2  <- predict(milk.lm2, cadairydata)

	## Compute and plot the residuals
	residuals <- cadairydata$Milk.Prod - predict2
	plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

Die Residuengrafik sehen Sie in Abbildung 25.

![Restdaten des saisonalen Modells für die Trainingsdaten](./media/machine-learning-r-quickstart/unnamed-chunk-21.png)

*Abbildung 25: Restdaten des saisonalen Modells für die Trainingsdaten.*

Die Restwerte sehen vernünftig aus. Es gibt keine bestimmte Struktur, mit Ausnahme der Auswirkungen der Rezession 2008 bis 2009, die unser Modell nicht besonders gut abbildet.

Die in Abbildung 25 dargestellte Grafik eignet sich zum Erkennen von zeitabhängigen Mustern in den Restdaten. Durch den verwendeten expliziten Ansatz der Berechnung und Darstellung der Residuale werden diese in zeitlicher Reihenfolge in der Grafik platziert. Wenn wir die Grafik mit `milk.lm$residuals` erstellt hätten, würde die Grafik nicht die zeitliche Reihenfolge widerspiegeln.

Sie können auch `plot.lm()` verwenden, um eine Reihe von Diagnosegrafiken zu erzeugen:

	## Show the diagnostic plots for the model
	plot(milk.lm2, ask = FALSE)

Dieser Code erzeugt eine Reihe von Diagnosegrafiken, die in Abbildung 26 dargestellt sind.

![Erste Diagnosegrafik für das saisonale Modell](./media/machine-learning-r-quickstart/unnamed-chunk-221.png)

![Zweite Diagnosegrafik für das saisonale Modell](./media/machine-learning-r-quickstart/unnamed-chunk-222.png)

![Dritte Diagnosegrafik für das saisonale Modell](./media/machine-learning-r-quickstart/unnamed-chunk-223.png)

![Vierte Diagnosegrafik für das saisonale Modell](./media/machine-learning-r-quickstart/unnamed-chunk-224.png)

*Abbildung 26: Diagnosegrafiken für das saisonale Modell.*

In diesen Grafiken werden einige hoch einflussreiche Größen identifiziert, aber nichts, was uns Sorgen bereiten sollte. Außerdem können wir in der "Normal Q-Q"-Grafik ablesen, dass die Residuale fast normal verteilt sind – eine wichtige Voraussetzung für lineare Modelle.

###Vorhersage und Modellevaluierung

Es gibt nur noch eine Sache zu tun, um unser Beispiel abzuschließen. Wir müssen Vorhersagen berechnen und den Fehler gegenüber den tatsächlichen Daten berechnen. Unsere Vorhersage erstellen wir für 12 Monate in 2013. Wir können ein Fehlermaß für diese Vorhersage gegenüber den tatsächlichen Daten berechnen, die nicht Bestandteil unseres Trainings-DataSets sind. Darüber hinaus können vergleichen wir die Leistung bei den Trainingsdaten aus 18 Jahren gegenüber den Testdaten aus 12 Monaten.

Eine Reihe von Metriken wird zum Messen der Leistung von Zeitreihenmodellen verwendet. In unserem Fall verwenden wir die mittlere quadratische Abweichung (RMS-Fehler). Die folgende Funktion berechnet den RMS-Fehler zwischen zwei Reihen:

	RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
	  ## Function to compute the RMS error or difference between two
	  ## series or vectors

	  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
	                "ERROR: Input vector to function RMS.error is too short",
	                "ERROR: Input vectors to function RMS.error must be of same length",
	                "WARNING: Funtion rms.error has received invald input time series.")

	  ## Check the arguments
	  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    	warning(messages[1])
	    return(NA)}

	  if(length(series1) < min.length) {
    	warning(messages[2])
	    return(NA)}

	  if((length(series1) != length(series2))) {
	   	warning(messages[3])
	    return(NA)}

	  ## If is.log is TRUE exponentiate the values, else just copy
	  if(is.log) {
	    tryCatch( {
	      temp1 <- exp(series1)
	      temp2 <- exp(series2) },
	      error = function(e){warning(messages[4]); NA}
    	)
	  } else {
	    temp1 <- series1
	    temp2 <- series2
	  }

	 ## Compute predictions from our models
	predict1  <- predict(milk.lm, cadairydata)
	predict2  <- predict(milk.lm2, cadairydata)

	## Compute the RMS error in a dataframe
	  tryCatch( {
	    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
	    error = function(e){warning(messages[4]); NA})
	}

Wie bei der `log.transform()`-Funktion, die wir im Abschnitt "Werttransformationen" besprochen haben, enthält diese Funktion ziemlich viel Code zur Fehlerprüfung und Behandlung von Ausnahmen. Die angewendeten Prinzipien sind identisch. Die Arbeit erfolgt an zwei Stellen innerhalb von `tryCatch()`. Zunächst werden die Zeitreihen potenziert, da wir mit logarithmierten Werten gearbeitet haben. Als Nächstes wird der tatsächliche RMS-Fehler berechnet.

Ausgestattet mit einer Funktion zum Messen des RMS-Fehlers, erstellen wir nun ein den RMS-Fehler enthaltendes Dataframe und geben es aus. Wir nehmen Terme für das Trendmodell alleine sowie das gesamte Modell mit saisonalen Faktoren auf. Der folgende Code erledigt die Aufgabe unter Verwendung zweier von uns erstellter linearer Modelle:

	## Compute the RMS error in a dataframe
	## Include the row names in the first column so they will
	## appear in the output of the Execute R Script
	RMS.df  <-  data.frame(
	rowNames = c("Trend Model", "Seasonal Model"),
	  Traing = c(
	  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
	  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
	  Forecast = c(
	    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
	    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
	)
	RMS.df

	## The following line should be executed only when running in
	## Azure Machine Learning Studio
	maml.mapOutputPort('RMS.df')

Das Ausführen des Codes erzeugt die in Abbildung 27 dargestellte Ausgabe am "Result Dataset"-Ausgabeport.

![Vergleich des RMS-Fehlers für die Modelle][26]

*Abbildung 27: Vergleich des RMS-Fehlers für die Modelle.*

Anhand dieser Ergebnisse können wir erkennen, dass der RMS-Fehler durch das Hinzufügen der saisonalen Faktoren zu dem Modell signifikant abnimmt. Es überrascht uns nicht allzu sehr, dass der RMS-Fehler für die Trainingsdaten ein wenig niedriger ist für die Vorhersage.

##<a id="appendixa"></a>ANHANG A – Anleitung für RStudio

RStudio ist sehr gut dokumentiert, weshalb ich in diesem Anhang einige Links zu den wichtigsten Abschnitten der RStudio-Dokumentation bereitstelle, um Ihnen einen schnellen Einstieg zu ermöglichen.

1.	Erstellen von Projekten

	Sie können Ihren R-Code mit RStudio in Projekten organisieren. Die Dokumentation zur Verwendung von Projekten finden Sie unter https://support.rstudio.com/hc/articles/200526207-Using-Projects.

	Ich empfehle Ihnen, diese Anweisungen zu befolgen und ein Projekt für die R-Codebeispiele in diesem Dokument zu erstellen.

2.	Bearbeiten und Ausführen von R-Code

	RStudio bietet eine integrierte Umgebung zum Bearbeiten und Ausführen von R-Code. Die Dokumentation hierzu finden Sie unter https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.

3.	Debuggen

	RStudio enthält leistungsfähige Debugfunktionen. Die Dokumentation zu diesen Funktionen finden Sie unter https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.

	Die Problembehandlungsfunktionen für Haltepunkte finden Sie unter https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

##<a id="appendixb"></a>ANHANG B – Weitere Informationen

Dieses Lernprogramm für die Programmierung in R behandelt die Grundlagen der Sprache R mit Azure Machine Learning Studio. Wenn Sie nicht mit R vertraut sind, finden Sie zwei Einführungen im CRAN.

- "R for Beginners" von Emmanuel Paradis ist ein guter Ausgangspunkt unter http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  

- "An Introduction to R" von W. N. Venables et al. geht etwas mehr in die Tiefe und ist zu finden unter http://cran.r-project.org/doc/manuals/R-intro.html.

Es gibt viele Bücher zu R, die Ihnen beim Einstieg helfen können. Hier finden Sie ein paar, die ich nützlich finde:

- "The Art of R Programming: A Tour of Statistical Software Design" von Norman Matloff ist eine ausgezeichnete Einführung in die Programmierung in R.  

- Das "R Cookbook" von Paul Teetor bietet einen Problem-Lösung-Ansatz für die Verwendung von R.

- "R in Action" von Robert Kabacoff ist eine weitere nützliche Einführung. Die begleitende Website "Quick R" ist eine nützliche Ressource unter http://www.statmethods.net/.

- "R Inferno" von Patrick Burns ist ein erstaunlich humorvolles Buch, das sich mit einer Reihe von verzwickten und komplizierten Themen beschäftigt, die bei der Programmierung in R vorkommen können. Das Buch ist kostenlos erhältlich unter http://www.burns-stat.com/documents/books/the-r-inferno/.

- Wenn Sie eine gründliche Erörterung komplexerer Themen in R benötigen, werfen Sie einen Blick in das Buch "Advanced R" von Hadley Wickham. Die Onlineversion dieses Buchs ist kostenlos erhältlich unter http://adv-r.had.co.nz/.

Einen Katalog mit R-Zeitreihenpaketen finden Sie in "CRAN Task View: Time Series Analysis" unter http://cran.r-project.org/web/views/TimeSeries.html. Informationen zu speziellen Zeitreihen-Objektpaketen finden Sie in der Dokumentation für das jeweilige Paket.

Das Buch "Introductory Time Series with R" von Paul Cowpertwait und Andrew Metcalfe bietet eine Einführung in die Verwendung von R für Zeitreihenanalysen. Außerdem gibt es noch jede Menge theoretische Texte, in denen Sie R-Beispiele finden können.

Einige weitere nützliche Internetressourcen:

- DataCamp: DataCamp unterrichtet R bequem über den Browser mit Videolektionen und Codierungsübungen. Es gibt interaktive Lernprogramme zu den neuesten R-Techniken und -Paketen. Bearbeiten Sie das kostenlose interaktive R-Lernprogramm unter https://www.datacamp.com/courses/introduction-to-r  

- Ein R-Crashkurs von Kelly Black von der Clarkson University: http://www.cyclismo.org/tutorial/R/

- Mehr als 60 R-Ressourcen: http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/machine-learning-r-quickstart/fig1.png
[2]: ./media/machine-learning-r-quickstart/fig2.png
[3]: ./media/machine-learning-r-quickstart/fig3.png
[4]: ./media/machine-learning-r-quickstart/fig4.png
[5]: ./media/machine-learning-r-quickstart/fig5.png
[6]: ./media/machine-learning-r-quickstart/fig6.png
[7]: ./media/machine-learning-r-quickstart/fig7.png
[8]: ./media/machine-learning-r-quickstart/fig8.png
[9]: ./media/machine-learning-r-quickstart/fig9.png
[10]: ./media/machine-learning-r-quickstart/fig10.png
[11]: ./media/machine-learning-r-quickstart/fig11.png
[12]: ./media/machine-learning-r-quickstart/fig12.png
[13]: ./media/machine-learning-r-quickstart/fig13.png
[14]: ./media/machine-learning-r-quickstart/fig14.png
[15]: ./media/machine-learning-r-quickstart/fig15.png
[16]: ./media/machine-learning-r-quickstart/fig16.png
[17]: ./media/machine-learning-r-quickstart/fig17.png
[18]: ./media/machine-learning-r-quickstart/fig18.png
[19]: ./media/machine-learning-r-quickstart/fig19.png
[20]: ./media/machine-learning-r-quickstart/fig20.png
[21]: ./media/machine-learning-r-quickstart/fig21.png
[22]: ./media/machine-learning-r-quickstart/fig22.png

[26]: ./media/machine-learning-r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

<!---HONumber=July15_HO3-->