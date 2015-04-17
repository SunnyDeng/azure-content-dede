<properties 
	pageTitle="Erstellen eines Geschäftsprozesses" 
	description="Erstellen eines Geschäftsprozesses" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service-logic" 
	documentationCenter=""/>



<tags
	ms.service="app-service-logic"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="stepsic"/>

# Entwickeln eines Geschäftsprozesses mithilfe von Datenfluss- und API-Apps

Sie haben einen Standarddatenfluss erstellt und möchten nun als Nächstes einen Geschäftsprozess mithilfe von Datenfluss-Apps entwickeln. Ich werde in diesem Artikel einige wichtige Themen behandeln:

- Hinzufügen bedingter Logik (z. B. eine Aktion nur ausführen, wenn eine bestimmte Bedingung erfüllt ist) und Hinzufügen von Schleifen
- BizTalk-API-Apps
- Verwenden der Codeansicht zum Bearbeiten eines Datenflusses
- Die verschiedenen Typen von Triggern

Klicken Sie zum Einstieg hier, um in Ihrem Abonnement ein Element mit bereits ausgefülltem Standarddatenfluss zu erstellen. Nachdem Sie diesen Datenfluss erstellt haben, klicken Sie auf "Trigger und Aktionen", um ihn zu bearbeiten.

## Hinzufügen von bedingter Logik und Schleifen

Obwohl der Standarddatenfluss einwandfrei funktioniert, werden Ihnen vielleicht schon eine Reihe von Problemen aufgefallen sein. Zunächst erhalten Sie nur den Top-Tweet und nicht alle Tweets zum Stichwort per E-Mail. Um eine Aktion für eine Liste von Elementen zu wiederholen, können Sie die "Repeat"-Eigenschaft verwenden.

### Wiederholen

Die "Repeat"-Eigenschaft verwendet eine Liste von Elementen und führt für jedes Element in der Liste eine Aktion aus. Zum Verwenden der "Repeat"-Eigenschaft klicken Sie auf das Symbol mit dem kreisförmigen Pfeil am oberen Rand der Aktion. Dadurch wird ein Textfeld geöffnet, in das Sie Folgendes eingeben:

    @triggers().outputs.body

Wie beim Standarddatenfluss wird dadurch eine Liste mit Tweets ausgegeben. Um den Tweet in der Aktion tatsächlich zu verwenden, ersetzen Sie die Nachricht hiermit:

    @repeatItem().message

"repeatItem()" ist eine Funktion, die jedes Element in der Liste ausgibt. Klicken Sie auf das Häkchen.

### Bedingt

Dieser Datenfluss ist leider noch nicht so wie gewünscht, da sehr viele E-Mail-Nachrichten zurückgegeben werden. Sie können weitere Logik hinzufügen, damit Sie nur dann eine E-Mail erhalten, wenn der Urheber des Tweets eine bestimmte Anzahl von Followern hat. Zu diesem Zweck klicken Sie am oberen Rand der Aktion auf das Rautensymbol. 

Geben Sie in das Textfeld Folgendes ein:

    @greaterThan(repeatItem().person.followers, 1000)

Dadurch wird nicht nur die "repeatItem()"-Funktion, sondern auch eine neue Funktion namens "greaterThan()" anzeigt. Diese Funktion vergleicht zwei Werte und lässt die Ausführung der Aktion nur zu, wenn der erste größer als der zweite ist. Beachten Sie die Syntax zum Abrufen der Follower. Fügen Sie für jede Eigenschaft, die Sie interessiert, einen Punkt gefolgt vom Eigenschaftennamen hinzu. Klicken Sie auf das Häkchen, um die Bedingung zu speichern.

## BizTalk-API-Apps

In Azure stehen verschiedene Kategorien von API-Apps zur Verfügung. Twitter und Office 365 sind hervorragende Connectors für dieses Szenario, aber es gibt auch eine Anzahl von BizTalk bereitgestellter API-Apps, die Sie in Ihrem Datenfluss nutzen können.

Klicken Sie im rechten Bereich auf den Dienst XXXXXX. Dadurch wird dieser von BizTalk bereitgestellte Dienst dem Designer hinzugefügt.

### Übergeben von Daten zwischen Aktionen

Um mehr als eine Aktion in einem Datenfluss zu verwenden, müssen Sie Daten zwischen Aktionen übergeben. Dazu nutzen Sie die "actions()"-Funktion. 

Nachdem Sie die Aktion "XXXXXX" hinzugefügt haben, geben Sie Folgendes ein:

    @actions('SendMail').outputs.YYYYYY

Dadurch wird die "YYYYYY"-Eigenschaft aus den Ausgaben an die "SendMail"-Aktion übergeben. Sie können den Namen einer Aktion stets bestimmen, indem Sie direkt unter dem Symbol für diese Aktion nachsehen. Gleichermaßen können Sie alle Ausgaben für diese Aktion am unteren Rand des Dialogfelds sehen.

## Verwenden der Codeansicht zum Bearbeiten eines Datenflusses

Außer im Designer können Sie die Definition eines Datenflusses direkt manuell bearbeiten. Zu diesem Zweck klicken Sie auf der Befehlsleiste auf die Schaltfläche "Codeansicht". Dadurch wird ein vollständiger Editor geöffnet, der die Definition des Datenflusses zeigt, den Sie gerade bearbeitet haben.

Ein Text-Editor kann bestimmte Vorgänge vereinfachen. Sie können z. B. eine beliebige Anzahl von Aktionen innerhalb des gleichen Datenflusses (oder sogar zwischen Datenflüssen) kopieren und einfügen. Sie können im Datenfluss auch auf einfache Weise ganze Abschnitte auf einmal hinzufügen oder entfernen.

### Parameter

Es gibt auch einige Funktionen, die nur in der Codeansicht verfügbar gemacht werden, beispielsweise Parameter. Parameter erleichtern die Wiederverwendung von Werten in einem Datenfluss. Wenn Sie z. B. eine E-Mail-Adresse haben, die in verschiedenen Aktionen verwendet werden soll, können Sie sie als Parameter definieren.

Zum Definieren von Parametern wechseln Sie zum "parameters {}"-Objekt und fügen ihm Folgendes hinzu:
    "emailAddress" : {
	    "type" : "string",
	    "defaultValue" : "me@example.com"
    }

Führen Sie einen Bildlauf zur Aktion "E-Mail senden" durch. Ersetzen Sie hier den Platzhalter "emailAddress" durch die gewünschte E-Mail-Adresse:

    @parameters('emailAddress')

Parameter sind eine gute Möglichkeit, Werte auszuwählen, die Sie wahrscheinlich häufig ändern.

## Verschiedene Typen von Triggern

Es gibt mehrere Möglichkeiten, Datenflüsse zu starten. Jeder hat ein etwas anderes Verhalten, weshalb Sie den richtigen für Ihre Szenarien wählen müssen.

### Abfragetrigger

Einige Trigger fragen eine API-App in einem bestimmten Intervall ab. Sie können dieses Intervall definieren, müssen jedoch Sie auch ein Feld namens "triggerState" übergeben. Der Triggerstatus enthält Informationen, die an den Aufruf des Triggers übergeben werden, damit er weiß, war er bei der letzten Abfrage zurückgegeben hat. Der Triggerstatus entspricht meist dieser Zeichenfolge:

    @triggers().outputs.body.triggerState

### Rückruftrigger

Andere Trigger richten einen Rückruf an den Datenfluss, wenn sie eine neue Anforderung empfangen. In diesem Fall müssen Sie dem Trigger den Rückruf-URI Ihres Datenflusses zur Verfügung stellen. Kopieren Sie hierfür den Zugriffsendpunkt aus dem Fenster "Einstellungen" vom Blatt "Eigenschaften", wenn Sie zu einem Datenfluss navigieren. 

### Manueller Aufruf

Schließlich ist es möglich, einen Datenfluss nur manuell ausführen. Im Portal gibt es die Schaltfläche "Ausführen", auf die Sie klicken können, um einen Datenfluss zu starten. 

<!--HONumber=49-->