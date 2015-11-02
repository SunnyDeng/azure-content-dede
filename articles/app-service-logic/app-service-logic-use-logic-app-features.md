<properties 
	pageTitle="Verwenden von Logik-App-Features" 
	description="Erfahren Sie, wie die erweiterten Features von Logik-Apps verwendet werden." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="stepsic"/>
	
# Verwenden von Logik-App-Features

Im [vorherigen Thema][Create a new logic app] haben Sie Ihre erste Logik-App erstellt. Nun erfahren Sie, wie einen vollständigeren Prozess mithilfe von App Services-Logik-Apps erstellen können. In diesem Thema werden die folgenden neuen Logik-App-Konzepte vorgestellt:

- Bedingte Logik, die eine Aktion ausführt, wenn eine bestimmte Bedingung erfüllt ist.
- Sich wiederholende Aktionen.
- Codeansicht zum Bearbeiten einer vorhandenen Logik-App.
- Optionen zum Starten eines Workflows.

Bevor Sie dieses Thema durchgehen, sollten Sie die Schritte unter [Erstellen einer neuen Logik-App] ausgeführt haben. Navigieren Sie im [Azure-Portal] zu Ihrer Logik-App, und klicken Sie in der Übersicht auf **Trigger und Aktionen**, um die Definition der Logik-App zu bearbeiten.

## Referenzmaterial

Die folgenden Dokumente könnten hilfreich sein:

- [Verwaltungs- und Laufzeit-REST-APIs](https://msdn.microsoft.com/library/azure/dn948513.aspx): hier wird auch erklärt, wie Logik-Apps direkt aufgerufen werden.
- [Sprachreferenz](https://msdn.microsoft.com/library/azure/dn948512.aspx): eine umfassende Liste aller Funktionen und Ausdrücke, die unterstützt werden.
- [Trigger- und Aktionstypen](https://msdn.microsoft.com/library/azure/dn948511.aspx): die verschiedenen Arten von Aktionen und die dazugehörigen Eingaben.
- [Übersicht über App Service](app-service-value-prop-what-is.md): Beschreibung, welche Komponenten für das Erstellen welcher Lösung ausgewählt werden.

## Hinzufügen von bedingter Logik und einer Wiederholung

Wenngleich der ursprüngliche Datenfluss funktioniert, gibt es einige Bereiche, die verbessert werden könnten. Erstens sendet Ihnen die Aktion nur den zurückgegeben Top-Tweet zurück. Logischerweise möchten Sie lieber alle Tweets mit dem Stichwort erhalten. Zum Wiederholen einer Aktion für eine Liste von Elementen, z. B. zurückgegebener Tweets, müssen Sie die `repeat`-Eigenschaft verwenden.

### Wiederholen
Die "Repeat"-Eigenschaft verwendet eine Liste von Elementen und führt für jedes Element in der Liste eine Aktion aus. Die folgenden Schritte dienen zum Aktualisieren der vorhandenen Aktion für die Verwendung von Wiederholungen, was bei einer Liste von Tweets sinnvoller ist.

1. Kehren Sie zum Workflow zurück, den Sie erstellt haben, und klicken Sie in **Essentials** auf den Link **Definition**. 

2. Zum Bearbeiten der Aktion **Dropbox-Connector** klicken Sie auf das Stiftsymbol.

3. Klicken Sie auf das Zahnradsymbol, und wählen Sie **Für eine Liste wiederholen**.
 
2. Klicken Sie neben dem Feld **Wiederholen** auf `...`, und wählen Sie **Text** aus. Dies führt zur Eingabe von:

    	@body('twitterconnector')

	in das Textfeld. Diese Funktion gibt eine Liste mit Tweets aus.

3. Wählen Sie den gesamten Text im Textfeld **Inhalt** aus, und löschen Sie ihn. Klicken Sie dann auf `...`, und wählen Sie **Tweet-Text** aus. Dies fügt die **repeatItem()**-Funktion hinzu, die jedes Element in der Liste zurückgibt.

Beachten Sie, dass die Ausgaben von wiederholten Aktionen besonders sind. Wenn Sie auf die Ergebnisse des Dropbox-Vorgangs verweisen möchten, führen Sie *nicht* den normalen Befehl `@actions('dropboxconnector').outputs.body` aus, sondern: `@actions('dropboxconnector').outputs.repeatItems`. Dadurch wird eine Liste mit der Häufigkeit der Ausführung des Vorgangs zusammen mit den jeweiligen Ausgaben zurückgegeben. Z. B. gibt `@first(actions('dropboxconnector').outputs.repeatItems).outputs.body.FilePath` den Pfad der ersten hochgeladenen Datei zurück.

### Bedingt
Diese Logik-App führt weiter zu zahlreichen Dateien, die in Dropbox hochgeladen werden. In den folgenden Schritten wird zusätzliche Logik hinzugefügt, um sicherzustellen, dass Sie nur eine Datei erhalten, wenn der Tweet eine bestimmte Anzahl von Retweets hat.

1. Klicken Sie am oberen Rand der Aktion auf das Zahnradsymbol, und wählen Sie **Eine zu erfüllende Bedingung hinzufügen** aus.

2. Geben Sie in das Textfeld Folgendes ein:

    	@greater(repeatItem().Retweet_Count , 5)
    
	Diese Funktion **Größer als** vergleicht zwei Werte und lässt die Ausführung der Aktion nur zu, wenn der erste Wert größer als der zweite ist. Für den Zugriff auf eine bestimmte Eigenschaft geben Sie einen Punkt (.) gefolgt vom Namen der Eigenschaft an, wie z. B. `.Retweet_Count`.

3. Klicken Sie auf das Häkchen, um die Dropbox-Aktion zu speichern.

## Verwenden der Codeansicht zum Bearbeiten einer Logik-App

Außer im Designer können Sie die Definition einer Logik-App wie folgt direkt bearbeiten.

1. Klicken Sie auf der Befehlsleiste auf die Schaltfläche **Codeansicht**. 

	Dadurch wird ein vollständiger Editor geöffnet, der die Definition zeigt, die Sie gerade bearbeitet haben.

	![Codeansicht](./media/app-service-logic-use-logic-app-features/codeview.png)

    Mithilfe des Text-Editors können Sie eine beliebige Anzahl von Aktionen innerhalb der gleichen oder zwischen Logik-Apps kopieren und einfügen. Sie können auch auf einfache Weise in der gesamten Definition Abschnitte hinzufügen oder entfernen und auch Definitionen mit anderen Benutzern gemeinsam nutzen.

2. Nachdem Sie in der Codeansicht Änderungen vorgenommen haben, klicken Sie auf **Speichern**.

### Parameter
Es gibt einige Funktionen von Logik-Apps, die nur in der Codeansicht verwendet werden können. Ein Beispiel dafür sind Parameter. Parameter erleichtern das Wiederverwenden von Werten in der gesamten Logik-App. Wenn Sie z. B. eine E-Mail-Adresse haben, die in verschiedenen Aktionen verwendet werden soll, sollten Sie sie als Parameter definieren.

Der folgende Code aktualisiert die vorhandene Logik-App so, dass Parameter für den Abfrageausdruck verwendet werden.

1. Suchen Sie in der Codeansicht das `parameters : {}`-Objekt, und fügen Sie das folgende "Topic"-Objekt hinzu:

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. Führen Sie einen Bildlauf zur `twitterconnector`-Aktion durch, suchen Sie den Abfragewert, und ersetzen Sie ihn durch `#@{parameters('topic')}`. Außerdem können Sie die **Concat**-Funktion verwenden, um zwei oder mehr Zeichenfolgen zu verknüpfen, z. B. ist `@concat('#',parameters('topic'))` identisch mit der oben genannten.
 
3. Wechseln Sie abschließend zur Aktion `dropboxconnector`, und fügen Sie den "Topic"-Parameter wie folgt hinzu:

    	/tweets/@{parameters('topic')}/@{repeatItem().TweetID}.txt

Parameter sind eine gute Möglichkeit, Werte auszuwählen, die Sie wahrscheinlich häufig ändern. Sie sind besonders nützlich, wenn Sie Parameter in verschiedenen Umgebungen überschreiben müssen. Weitere Informationen zum Überschreiben von Parametern auf Grundlage der Umgebung finden Sie in unserer [REST-API-Dokumentation](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

Wenn Sie nun auf **Speichern** klicken, werden Ihnen stündlich alle neuen Tweets mit mehr als 5 Retweets in einen Ordner namens **Tweets** in Ihrer Dropbox übermittelt.

Mehr Informationen zu Logik-App-Definitionen finden Sie unter [Logik-App-Definitionen erstellen](app-service-logic-author-definitions.md).

## Starten des Workflows einer Logik-App
Es gibt mehrere Möglichkeiten zum Starten des Workflows, der in der Logik-App definiert ist. Ein Workflow kann bei Bedarf stets im [Azure-Portal] gestartet werden.

### Wiederholungstrigger
Ein Wiederholungstrigger wird in einem von Ihnen angegebenen Intervall ausgeführt. Wenn der Trigger bedingte Logik aufweist, bestimmt der Trigger, ob der Workflow ausgeführt werden muss oder nicht. Ein Trigger gibt an, dass er ausgeführt werden sollte, indem der Statuscode `200` zurückgegeben wird. Wenn er nicht ausgeführt werden muss, wird der Statuscode `202` zurückgegeben.

### Rückruf mit REST-APIs
Dienste können ein Logik-App-Endgerät zum Starten eines Workflows aufrufen. Sie finden das Endgerät, auf das Sie zugreifen möchten, indem Sie in Ihrer Logik-App über die Befehlsleistenschaltfläche **Einstellungen** zum Blatt **Eigenschaften** navigieren.

Über diesen Rückruf können Sie eine Logik-App in Ihrer benutzerdefinierten Anwendung aufrufen. Sie müssen die **Standardauthentifizierung** verwenden. Der Benutzername `default` wird für Sie erstellt, und das Kennwort ist das Feld **Primärer Zugriffsschlüssel** auf dem Blatt **Eigenschaften**. Zum Beispiel:

        POST https://<< your endpoint >>/run?api-version=2015-02-01-preview
        Content-type: application/json
        Authorization: Basic << base-64 encoded string of default:<access key> >>
        {
            "name" : "nameOfTrigger",
            "outputs" : { "property" : "value" }
        }

Sie können Ausgaben an den Workflow übergeben und im Workflow darauf verweisen. Wenn Sie beim oben genannten Trigger `@triggers().outputs.property` hinzufügen, erhalten Sie `value`.

Weitere Informationen finden Sie in der [REST-Dokumentation](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

### Manuelle Ausführung
Sie können eine Logik-App definieren, die keine Trigger hat. In diesem Fall muss der Workflow bei Bedarf gestartet werden. Diese Art von Logik-App eignet sich am besten für einen Prozess, der nur zeitweise ausgeführt werden soll. Um eine Logik-App ohne Trigger zu erstellen, aktivieren Sie im Designer im Feld **Logik starten** die Option **Diese Logik manuell ausführen**.

Um eine Logik-App bei Bedarf zu starten, klicken Sie auf der Befehlsleiste auf die Schaltfläche **Jetzt ausführen**.

<!-- Shared links -->
[Create a new logic app]: app-service-logic-create-a-logic-app.md
[Erstellen einer neuen Logik-App]: app-service-logic-create-a-logic-app.md
[Azure-Portal]: https://portal.azure.com

<!---HONumber=Oct15_HO4-->