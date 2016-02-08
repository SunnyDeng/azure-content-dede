<properties
	pageTitle="Skalieren von durch Azure SQL-Datenbank gesicherten mobilen Diensten | Microsoft Azure"
	description="Erfahren Sie, wie Sie Skalierbarkeitsprobleme in Ihren von SQL-Datenbank unterstützten mobilen Diensten diagnostizieren und beheben können."
	services="mobile-services"
	documentationCenter=""
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>


<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="donnam;ricksal"/>

# Skalieren von durch Azure SQL-Datenbank gesicherten mobillen Diensten

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Azure Mobile Services erleichtert die ersten Schritte beim Erstellen einer Anwendung, die eine Verbindung zu einem cloudbasierten Back-End herstellt, welches Daten in einer SQL-Datenbank speichert. Wenn Ihre Anwendung wächst, lassen sich die Dienstinstanzen ganz einfach skalieren, indem Sie die Skalierungseinstellungen im Portal anpassen, um weitere Netzwerk- und Rechenkapazität hinzuzufügen. Das Skalieren der SQL-Datenbank, die die Dienste sichert, erfordert jedoch proaktive Planung und Überwachung, wenn der Dienst mehr Lasten empfängt. In diesem Dokument werden Sie durch eine Reihe bewährter Methoden geführt, mit denen Sie weiter die großartige Leistung Ihrer SQL-gesicherten mobilen Dienste gewährleisten.

Dieses Thema enthält Anleitungen für die folgenden grundlegenden Abschnitte:

1. [Diagnostizieren von Problemen](#Diagnosing)
2. [Indizieren](#Indexing)
3. [Schemadesign](#Schema)
4. [Abfragedesign](#Query)
5. [Dienstarchitektur](#Architecture)
6. [Erweiterte Fehlerbehebung](#Advanced)

<a name="Diagnosing"></a>
## Diagnostizieren von Problemen

Wenn Sie den Eindruck haben, dass Ihr mobiler Dienst bei Belastung Probleme aufweist, sollten Sie zunächst die Registerkarte **Dashboard** für Ihren Dienst im [klassischen Azure-Portal] prüfen. Dort können Sie u.a. Folgendes feststellen:

- Ihre Nutzungszähler, darunter die Zähler für **API-Aufrufe** und **Aktive Geräte**, überschreiten nicht das Kontingent.
- Der Status der **Endpunktüberwachung** gibt an, dass der Dienst läuft (nur verfügbar, wenn der Dienst die Stufe „Standard“ nutzt und die Endpunktüberwachung aktiviert ist).

Sollte einer der obigen Punkte nicht zutreffen, können Sie die Skalierungseinstellungen auf der Registerkarte *Skalieren* anpassen. Wenn dadurch das Problem nicht gelöst wird, können Sie im nächsten Schritt prüfen, ob die Azure SQL-Datenbank die Ursache des Problems ist. In den folgenden Abschnitten werden einige unterschiedliche Diagnoseansätze vorgestellt, mit denen festgestellt werden kann, wo das Problem liegen könnte.

### Auswahl der richtigen SQL-Datenbankstufe

Es ist wichtig, die verschiedenen verfügbaren Datenbankstufen zu verstehen, um sicherzustellen, dass Sie die passende Stufe für die Bedürfnisse Ihrer Anwendung gewählt haben. Azure SQL-Datenbank wird mit drei Tarifen angeboten:

- Basic
- Standard
- Premium


Einige Empfehlungen für die Auswahl der richtigen Datenbankstufe sind:

- **Basic** – zur Entwicklung oder für kleine Produktionsdienste, wenn jeweils nur eine einzige Datenbankabfrage erwartet wird.
- **Standard** — für Produktionsdienste, wenn mehrere gleichzeitige Datenbankabfragen erwartet werden.
- **Premium** – für umfangreiche Produktionsdienste mit zahlreichen gleichzeitigen Abfragen, hohen Spitzenlasten und erwarteter niedriger Latenz für jede Anforderung.

Weitere Informationen zur Verwendung der einzelnen Stufen finden Sie unter [Gründe für die Verwendung neuer Dienstebenen]

### Analysieren von Datenbankmetriken

Nachdem Sie sich mit den einzelnen Datenbankstufen vertraut gemacht haben, können die Datenbankleistungsmetriken erörtert werden, nach denen sich die Skalierung innerhalb und unter den Stufen richtet.

1. Starten Sie das [klassische Azure-Portal].
2. Wählen Sie auf der Registerkarte „Mobile Services“ den Dienst aus, mit dem Sie arbeiten möchten.
3. Wählen Sie die Registerkarte **Konfigurieren** aus.
4. Wählen Sie den Namen der **SQL-Datenbank** im Abschnitt **Datenbankeinstellungen** aus. Dadurch werden Sie zur Registerkarte „Azure SQL-Datenbank“ im Portal geführt.
5. Gehen Sie zur Registerkarte **Überwachen**.
6. Stellen Sie sicher, dass die relevanten Metriken angezeigt werden, indem Sie die Schaltfläche **Metriken hinzufügen** verwenden. Schließen Sie die folgenden Metriken ein:
    - *CPU-Prozentsatz* (nur für die Tarife Basic/Standard/Premium verfügbar)

    - *Daten-E/A (Prozentsatz)* (nur für die Tarife Basic/Standard/Premium verfügbar)
    - *Protokoll-E/A (Prozentsatz)* (nur für die Tarife Basic/Standard/Premium verfügbar)
    - *Speicher* 
7. Prüfen Sie die Metriken über dem Zeitfenster, wenn der Dienst Probleme aufwies. 

    ![Klassisches Azure-Portal – SQL-Datenbankmetriken][PortalSqlMetrics]

Wenn eine Metrik 80 % Auslastung für einen längeren Zeitraum überschreitet, kann dies auf ein Leistungsproblem hinweisen. Weitere Einzelheiten zur Datenbankauslastung finden Sie unter [Grundlegendes zur Ressourcenverwendung](http://msdn.microsoft.com/library/azure/dn369873.aspx#Resource).

Wenn die Metriken darauf hindeuten, dass die Datenbank hochgradig ausgelastet ist, könnten Sie als ersten Schritt zur Problemminderung **die Datenbank auf eine höhere Dienststufe skalieren**. Um Probleme sofort zu beheben, können Sie die Registerkarte **Skalieren** der Datenbank verwenden, um sie zu skalieren. Dies führt zu einer höheren Rechnung für Sie. ![Klassisches Azure-Portal – SQL-Datenbankskalierung][PortalSqlScale]

Ziehen Sie so bald wie möglich die folgenden Problemminderungsschritte in Erwägung:

- **Optimieren Sie die Datenbank.** Oft kann die Datenbankauslastung reduziert und das Skalieren auf eine höhere Stufe vermieden werden, indem Sie die Datenbank optimieren.
- **Prüfen Sie die Dienstarchitektur.** Häufig ist die Dienstlast nicht gleichmäßig über die Zeit verteilt, sondern enthält "Spitzen" mit hoher Beanspruchung. Anstatt die Datenbank zu skalieren, um die Spitzen zu verarbeiten und bei geringerer Beanspruchung eine zu niedrige Auslastung hinzunehmen, lässt sich die Dienstarchitektur häufig anpassen, um diese Spitzen zu vermeiden, bzw. so damit umzugehen, dass keine Datenbankprobleme entstehen.

In den weiteren Abschnitten dieses Dokuments werden die genauen Schritte zum Implementieren dieser Lösungen beschrieben.


### Konfigurieren von Warnungen

Häufig ist es hilfreich, Warnungen für wichtige Datenbankmetriken zu konfigurieren. Mit diesem proaktiven Schritt wird sichergestellt, dass Sie genügend Zeit haben, um im Fall einer Ressourcenerschöpfung zu reagieren.

1. Navigieren Sie für die Datenbank, für die Sie Warnungen einrichten möchten, zur Registerkarte **Überwachen**.
2. Stellen Sie wie im vorigen Abschnitt beschrieben sicher, dass die relevanten Metriken angezeigt werden.
3. Wählen Sie die Metrik, für die Sie eine Warnung einrichten möchten, und wählen Sie **Regel hinzufügen** aus.

    ![Azure-Verwaltungsportal – SQL-Warnung][PortalSqlAddAlert]

4. Geben Sie einen Namen und eine Beschreibung für die Warnung ein. ![Azure-Verwaltungsportal – Name und Beschreibung für SQL-Warnung][PortalSqlAddAlert2]

5. Geben Sie den Wert an, der als Warnschwellenwert verwendet werden soll. Empfohlen werden **80%**, damit noch Reaktionszeit bleibt. Geben Sie außerdem eine e-Mail-Adresse an, die Sie aktiv beobachten.
 
    ![Azure-Verwaltungsportal – Schwellenwert und E-Mail für SQL-Warnung][PortalSqlAddAlert3]


Weitere Informationen zum Diagnostizieren von SQL-Problemen finden Sie unter [Erweiterte Diagnose](#AdvancedDiagnosing) am Ende dieses Dokuments.

<a name="Indexing"></a>
## Indizieren

Wenn Probleme bei der Abfrageleistung auftreten, sollten Sie als erstes das Design Ihrer Indizes prüfen. Indizes sind wichtig, weil sie sich direkt darauf auswirken, wie das SQL-Modul eine Abfrage ausführt.

Wenn Sie z. B. oft ein Element nach einem bestimmten Feld abfragen müssen, könnten Sie einen Index für die betreffende Spalte hinzufügen. Andernfalls muss das SQL-Modul die Tabelle durchsuchen und jeden physischen Datensatz (oder zumindest die Abfragespalte) lesen. Diese Datensätze könnten über die ganze Festplatte verteilt sein.

Wenn Sie also häufig WHERE- oder JOIN-Anweisungen für bestimmte Spalten vornehmen, sollten Sie diese unbedingt indizieren. Weitere Informationen finden Sie im Abschnitt [Erstellen von Indizes](#CreatingIndexes).

Sind also Indizes gut und Tabellenscans schlecht, und sollten Sie daher sicherheitshalber jede Spalte in der Tabelle indizieren? Die schnelle Antwort lautet „wahrscheinlich nicht“. Indizes benötigen Speicherplatz und bedeuten ebenfalls Mehraufwand: Jedes Mal, wenn etwas in eine Tabelle eingefügt wird, müssen die Indexstrukturen für alle indizierten Spalten aktualisiert werden. Im Folgenden finden Sie Richtlinien für die Auswahl der Spaltenindizes.

### Richtlinien zum Spaltendesign

Wie oben erwähnt, ist es nicht immer vorteilhaft, einer Tabelle weitere Indizes hinzuzufügen, da auch Indizes hinsichtlich Leistung und Speicherbedarf aufwändig sein können.

#### Überlegungen zu Abfragen

- Sie sollten Indizes zu Spalten hinzufügen, die häufig für Vorhersagen (WHERE-Klauseln) und JOIN-Bedingungen verwendet werden. Ziehen Sie aber auch die Überlegungen zu Datenbanken unten in Betracht.
- Schreiben Sie Abfragen, die so viele Zeilen wie möglich in einer einzigen Anweisung einfügen oder ändern, anstelle von mehreren Abfragen zum Aktualisieren der gleichen Zeilen. Wenn nur eine Anweisung vorhanden ist, kann das Datenbankmodul die Indexpflege besser optimieren.

#### Überlegungen zu Datenbanken

Große Indexanzahlen in einer Tabelle beeinträchtigen die Leistung von INSERT-, UPDATE-, DELETE- und MERGE-Anweisungen, da alle Indizes bei Datenänderungen in der Tabelle entsprechend angepasst werden müssen.

- Bei Tabellen mit **häufigen Aktualisierungen** sollten Sie das Indizieren von häufig aktualisierten Spalten vermeiden.
- Für Tabellen, die **nicht häufig aktualisiert** werden, aber große Datenmengen enthalten, sollten Sie viele Indizes verwenden. Dadurch kann die Leistung von Abfragen verbessert werden, die keine Daten ändern (wie SELECT-Anweisungen), weil der Abfrageoptimierung mehr Optionen zum Ermitteln der besten Zugriffsmethode zur Verfügung stehen.

Das Indizieren von kleinen Tabellen ist nicht optimal, weil die Abfrageoptimierung möglicherweise länger zum Durchsuchen eines Index nach Daten als für einen einfachen Tabellenscan benötigt. In diesen Fällen kann es sein, dass Indizes für kleine Tabelle möglicherweise nie benötigt werden, aber trotzdem gepflegt werden müssen, wenn sich Tabellendaten ändern.


<a name="CreatingIndexes"></a>
### Erstellen von Indizes

#### JavaScript-Back-End

Gehen Sie wie folgt vor, um den Index für eine Spalte im JavaScript-Back-End festzulegen:

1. Öffnen Sie Ihren mobilen Dienst im [klassischen Azure-Portal].
2. Klicken Sie auf die Registerkarte **Daten**.
3. Wählen Sie die Tabelle aus, die Sie ändern möchten.
4. Klicken Sie auf die Registerkarte **Spalten**.
5. Wählen Sie die Spalte aus. Klicken Sie in der Befehlsleiste auf **Index festlegen**:

	![Mobile Services-Portal – Index festlegen][SetIndexJavaScriptPortal]

In dieser Ansicht können Sie auch Indizes entfernen.

#### .NET-Back-End

Um einen Index im Entity Framework zu definieren, verwenden Sie das Attribut `[Index]` für die Felder, die indiziert werden sollen. Zum Beispiel:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }

		[Index]
        public bool Complete { get; set; }
    }

Weitere Informationen zu Indizes finden Sie unter [Index Annotations in Entity Framework][]. Weitere Hinweise zum Optimieren von Indizes finden Sie unter [Erweiterte Indizierung](#AdvancedIndexing) am Ende dieses Dokuments.

<a name="Schema"></a>
## Schemadesign

Bei der Auswahl der Datentypen für die Objekte, die auch das Schema der SQL-Datenbank betreffen, sind einige Punkte zu beachten. Eine Optimierung des Schemas kann häufig erhebliche Leistungsverbesserungen erbringen, da SQL benutzerdefinierte optimierte Möglichkeiten zur Handhabung der Indizierung und Speicherung für verschiedene Datentypen bietet:

- **Verwenden Sie die bereitgestellte ID-Spalte**. Für jede Tabelle mit mobilen Diensten ist eine Standard-ID-Spalte als primärer Schlüssel definiert, und für diese Spalte ist ein Index festgelegt. Es ist nicht erforderlich, eine zusätzliche ID-Spalte zu erstellen.
- **Verwenden Sie die richtigen Datentypen in Ihrem Modell.** Wenn Sie wissen, dass eine bestimmte Eigenschaft in Ihrem Modell numerisch oder boolesch ist, müssen Sie sie im Modell entsprechend und nicht als Zeichenfolge definieren. Verwenden Sie im JavaScript-Back-End Zeichenfolgen wie `true` anstelle von `"true"` und `5` anstelle von `"5"`. Verwenden Sie im .NET-Back-End die Typen `int` und `bool` bei der Erklärung der Eigenschaften des Modells. So können in SQL die richtigen Schemata für diese Typen erstellt werden, wodurch die Abfragen effizienter werden.

<a name="Query"></a>
## Abfragedesign

Die folgenden Richtlinien sollten bei Datenbankabfragen beachtet werden:

- **Führen Sie immer Join-Vorgänge in der Datenbank aus.** Häufig müssen Sie Datensätze aus zwei oder mehr Tabellen kombinieren, wenn die kombinierten Datensätze ein gemeinsames Feld nutzen (auch als *Join* bezeichnet). Dieser Vorgang kann ineffizient sein, wenn er nicht richtig durchgeführt wird, da er das Abrufen aller Entitäten aus beiden Tabellen und dann Iterationen durch alle Entitäten bedeuten kann. Diese Art Vorgänge bleiben am besten der Datenbank selbst überlassen. Es geschieht jedoch leicht, dass sie irrtümlich auf dem Client oder im mobilen Dienstcode durchgeführt werden.
    - Führen Sie keine Joins im Anwendungscode durch.
    - Führen Sie keine Joins im Code des mobilen Diensts durch. Wenn Sie das JavaScript-Back-End verwenden, sollten Sie beachten, dass das [Tabellenobjekt](http://msdn.microsoft.com/library/windowsazure/jj554210.aspx) keine Joins bearbeitet. Verwenden Sie unbedingt direkt das [mssql.Objekt](http://msdn.microsoft.com/library/windowsazure/jj554212.aspx), damit der Join in der Datenbank erfolgt. Weitere Informationen finden Sie unter [Verknüpfen relationaler Tabellen](mobile-services-how-to-use-server-scripts.md#joins). Wenn Sie das .NET.-Back-End verwenden und über LINQ abfragen, werden Joins automatisch auf Datenbankebene vom Entity Framework bearbeitet.
- **Implementieren Sie Paging.** Bei Datenbankabfragen werden oft sehr viele Datensätze an den Client zurückgegeben. Um die Größe und Latenz der Vorgänge zu minimieren, sollten Sie Paging implementieren.

    - Standardmäßig begrenzt der mobile Dienst alle eingehenden Abfragen auf eine Seitengröße von 50. Sie können manuell bis zu 1.000 Datensätze anfordern. Weitere Informationen finden Sie unter „Daten seitenweise zurückgeben“ für [Windows Store](mobile-services-windows-dotnet-how-to-use-client-library.md#paging), [iOS](mobile-services-ios-how-to-use-client-library.md#paging), [Android](mobile-services-android-how-to-use-client-library.md#paging), [HTML/JavaScript](mobile-services-html-how-to-use-client-library#paging) und [Xamarin](partner-xamarin-mobile-services-how-to-use-client-library.md#paging).
    - Für Abfragen von Ihrem Mobildienstcode aus gibt es keine Standardseitengröße. Wenn Ihre Anwendung Paging nicht implementiert, oder wenn Sie eine defensive Maßnahme ergreifen möchten, können Sie Standardlimits für Ihre Abfragen festlegen. Verwenden Sie im JavaScript-Back-End den Operator **take** für das [Abfrageobjekt](http://msdn.microsoft.com/library/azure/jj613353.aspx). Wenn Sie das .NET-Back-End verwenden, können Sie die [Take-Methode] als Teil der LINQ-Abfrage verwenden.  


Weitere Informationen zum Verbessern des Abfragedesigns, darunter die Analyse von Abfrageplänen, finden Sie unter [Erweitertes Abfragedesign](#AdvancedQuery) am Ende dieses Dokuments.

<a name="Architecture"></a>
## Dienstarchitektur

Stellen Sie sich ein Szenario vor, in dem Sie eine Pushbenachrichtigung an alle Kunden senden möchten, damit sie neue Inhalte in Ihrer Anwendung einsehen. Wenn die Kunden die Benachrichtigung antippen, wird die Anwendung gestartet. Dadurch wird möglicherweise ein Aufruf Ihres mobilen Diensts und eine Abfrageausführung in der SQL-Datenbank ausgelöst. Da potenziell Millionen von Kunden diese Aktion innerhalb weniger Minuten ausführen, ergibt sich eine SQL-Spitzenlast, die ein Vielfaches über der stabilen Last Ihrer Anwendung liegt. Dieses Problem ließe sich beheben, indem Sie Ihre Anwendung während der Spitzenlast zu einer höheren SQL-Stufe skalieren und dann wieder herunterskalieren. Diese Lösung erfordert aber einen manuellen Eingriff und führt zu höheren Kosten. Häufig können geringfügige Anpassungen der Architektur des mobilen Diensts die Last, die von den Kunden an die SQL-Datenbank weitergegeben wird, wesentlich ausgewogener gestalten, und problematische Anforderungsspitzen werden vermieden. Diese Änderungen lassen sich oft ganz leicht und mit nur minimaler Beeinträchtigung der Kundenerfahrung implementieren. Hier einige Beispiele:

- **Verteilen Sie die Last über die Zeit.** Wenn Sie die Zeiten für bestimmte Vorgänge steuern (z. B. das Senden einer Pushbenachrichtigung), von denen eine Nachfragespitze erwartet wird, und wenn der genaue Zeitpunkt für das Ereignis nicht entscheidend ist, können Sie das Ereignis zeitlich verteilen. Im obigen Beispiel kann es vielleicht akzeptabel sein, die Anwendungskunden gruppenweise im Lauf eines Tages zu benachrichtigen anstatt alle praktisch zeitgleich. Teilen Sie Ihre Kunden in Gruppen auf, und senden Sie die Benachrichtigung nacheinander an die einzelnen Gruppen. Wenn Sie Notification Hubs verwenden, können Sie einfach eine zusätzliche Markierung hinzufügen, um die Gruppe zu verfolgen, und dann eine Pushbenachrichtigung an Markierung senden. Weitere Informationen zu Markierungen finden Sie unter [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten](../notification-hubs-windows-store-dotnet-send-breaking-news.md).
- **Verwenden Sie wo immer möglich BLOB- und Tabellenspeicher.** Häufig sind die Inhalte, die die Kunden während der Spitzenlast anzeigen, mehr oder weniger statisch und brauchen nicht in einer SQL-Datenbank gespeichert zu werden, da für den Inhalt wahrscheinlich keine relationalen Abfragekapazitäten erforderlich sind. In diesem Fall können Sie den Inhalt in BLOB- oder Tabellenspeicher ablegen. Sie können auf öffentliche BLOBs im BLOB-Speicher direkt vom Gerät aus zugreifen. Um auf sichere Weise auf BLOBs zuzugreifen oder Tabellenspeicher zu verwenden, müssen Sie eine benutzerdefinierte API für mobile Dienste nutzen, um den Speicherzugriffsschlüssel zu schützen. Weitere Informationen finden Sie unter [Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md).
- **Verwenden Sie ein In-Memory-Cache**. Eine weitere Alternative besteht im Speichern von Daten, auf die häufig während einer Datenverkehrsspitze zugegriffen wird, in einem In-Memory-Cache wie [Azure Cache](https://azure.microsoft.com/services/cache/). Das bedeutet, dass eingehende Anfragen die benötigten Informationen aus dem Speicher abrufen können, statt immer wieder die Datenbank abzufragen.

<a name="Advanced"></a>
## Erweiterte Fehlerbehebung
In diesem Abschnitt werden erweiterte Diagnoseaufgaben behandelt, die hilfreich sein können, wenn das Problem mit den bisherigen Schritten nicht vollständig behoben werden konnte.

### Voraussetzungen
Um einige der Diagnoseaufgaben in diesem Abschnitt durchführen zu können, benötigten Sie Zugriff auf ein Verwaltungstool für SQL-Datenbanken wie **SQL Server Management Studio** oder die in das **klassische Azure-Portal** integrierten Verwaltungsfunktionen.

SQL Server Management Studio ist eine kostenlose Windows-Anwendung, die die fortgeschrittensten Funktionen bietet. Wenn Sie keinen Zugang zu einem Windows-Computer haben (wenn Sie z. B. einen Mac benutzen), können Sie einen virtuellen Computer in Azure wie in [Erstellen eines virtuellen Windows Server-Computers](../virtual-machines-windows-tutorial.md) beschrieben bereitstellen und dann eine Remoteverbindung mit ihm herstellen. Wenn Sie den virtuellen Computer hauptsächlich für die Ausführung von SQL Server Management Studio benötigen, sollte eine **Basic A0**-Instanz (früher „Sehr klein“) ausreichend sein.

Das klassische Azure-Portal bietet integrierte Verwaltungsfunktionen, die weniger umfassend, aber ohne lokale Installation verfügbar sind.

Mit den folgenden Schritten werden Sie durch das Abrufen von Verbindungsinformationen für die SQL-Datenbank geführt, auf der Ihr mobiler Dienst beruht, sowie durch die Verwendung eines der beiden Tools zum Herstellen einer Verbindung. Sie können wählen, welches Tool Sie nutzen möchten.

#### Abrufen von SQL-Verbindungsinformationen
1. Starten Sie das [klassische Azure-Portal].
2. Wählen Sie auf der Registerkarte „Mobile Services“ den Dienst aus, mit dem Sie arbeiten möchten.
3. Wählen Sie die Registerkarte **Konfigurieren** aus.
4. Wählen Sie den Namen der **SQL-Datenbank** im Abschnitt **Datenbankeinstellungen** aus. Dadurch werden Sie zur Registerkarte „Azure SQL-Datenbank“ im Portal geführt.
5. Wählen Sie **Azure-Firewallregeln für diese IP-Adresse einrichten** aus.
6. Notieren Sie sich die Serveradresse im Abschnitt **Datenbankverbindung herstellen**, z. B.: *mcml4otbb9.database.windows.net*.

#### SQL Server Management Studio
1. Navigieren Sie zu [SQL Server-Editionen – Express](http://www.microsoft.com/server-cloud/products/sql-server-editions/sql-server-express.aspx).
2. Suchen Sie den Abschnitt **SQL Server Management Studio**, und klicken Sie auf die darunter liegende Schaltfläche **Download**.
3. Führen Sie die Setupschritte durch, bis die Anwendung erfolgreich ausgeführt werden kann:

    ![SQL Server Management Studio][SSMS]

4. Geben Sie im Dialogfeld **Verbindung mit dem Server herstellen** die folgenden Werte ein:
    - Servername: *Die zuvor erhaltene Serveradresse*
    - Authentifizierung: *SQL Server Authentication*
    - Anmeldename: *Der Anmeldename, den Sie beim Erstellen des Servers ausgewählt haben*
    - Kennwort: *Das Kennwort, das Sie beim Erstellen des Servers ausgewählt haben*
5. Jetzt sollte die Verbindung hergestellt sein.

#### SQL-Datenbankverwaltungsportal
1. Klicken Sie auf der Registerkarte „Azure SQL-Datenbank“ für Ihre Datenbank auf die Schaltfläche **Verwalten**.
2. Konfigurieren Sie die Verbindung mit den folgenden Werten:
    - Server: *Sollte auf den richtigen Wert voreingestellt sein*
    - Datenbank: *Leer lassen*
    - Benutzername: *Der Anmeldename, den Sie beim Erstellen des Servers ausgewählt haben*
    - Kennwort: *Das Kennwort, das Sie beim Erstellen des Servers ausgewählt haben*
3. Jetzt sollte die Verbindung hergestellt sein.

    ![Klassisches Azure-Portal – SQL-Datenbank][PortalSqlManagement]

<a name="AdvancedDiagnosing" />
### Erweiterte Diagnose

Mehrere Diagnoseaufgaben können problemlos direkt über das **klassische Azure-Portal** ausgeführt werden. Einige erweiterte Diagnoseaufgaben sind aber nur über **SQL Server Management Studio** oder das **Verwaltungsportal für SQL-Datenbank** möglich. Wir nutzen dynamischen Verwaltungssichten, eine Reihe von Sichten, die automatisch mit Diagnoseinformationen zu Ihrer Datenbank ausgefüllt werden. Dieser Abschnitt enthält eine Reihe von Abfragen, die anhand dieser Sichten ausgeführt werden können, um verschiedene Metriken zu prüfen. Weitere Informationen finden Sie unter [Überwachung von SQL Database mit dynamischen Verwaltungssichten][].

Nach den Schritten im vorigen Abschnitt zum Verbinden der Datenbank in SQL Server Management Studio wählen Sie die Datenbank in **Objekt-Explorer** aus. Erweitern Sie **Sichten** und dann **Systemsichten**. Es wird eine Liste der Verwaltungssichten angezeigt. Um die Abfragen unten auszuführen, wählen Sie **Neue Abfrage** bei ausgewählter Datenbank in **Objekt-Explorer**. Fügen Sie dann die Abfrage ein, und klicken Sie auf **Ausführen**.

![SQL Server Management Studio – dynamische Verwaltungssichten][SSMSDMVs]

Wenn Sie das SQL-Datenbankverwaltungsportal verwenden, können Sie alternativ zuerst die Datenbank auswählen und dann **Neue Abfrage** wählen.

![SQL-Datenbankverwaltungsportal – neue Abfrage][PortalSqlManagementNewQuery]

Um eine der nachstehenden Abfragen auszuführen, fügen Sie sie in das Fenster ein und klicken auf **Ausführen**.

![SQL-Datenbankverwaltungsportal – Abfrage ausführen][PortalSqlManagementRunQuery]

#### Erweiterte Metriken


Im Verwaltungsportal werden einige Metriken bereitgestellt, wenn die Stufen Basic, Standard und Premium verwendet werden. Diese und andere Metriken lassen sich unabhängig vom gewählten Tarif mühelos über die Verwaltungssicht **[sys.resource\_stats](http://msdn.microsoft.com/library/dn269979.aspx)** abrufen. Betrachten Sie die folgende Abfrage:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'todoitem_db'
    ORDER BY start_time DESC

> [AZURE.NOTE]
Führen Sie diese Abfrage für die **master**-Datenbank auf dem Server aus. Die Sicht **sys.resource\\_stats** ist nur für diese Datenbank vorhanden.

Das Ergebnis enthält folgende nützliche Metriken: CPU (% des Stufenlimits), Speicher (Megabyte), physische Datenlesevorgänge (% des Stufenlimits), Protokollschreibvorgänge (% des Stufenlimits), Speicher (% des Stufenlimits), Workerzähler, Sitzungszähler usw.

#### SQL-Verbindungsereignisse

Die Sicht **[sys.event\_log](http://msdn.microsoft.com/library/azure/jj819229.aspx)** enthält Einzelheiten der verbindungsbezogenen Ereignisse.

    select * from sys.event_log
    where database_name = 'todoitem_db'
    and event_type like 'throttling%'
    order by start_time desc

> [AZURE.NOTE]
Führen Sie diese Abfrage für die **master**-Datenbank auf dem Server aus. Die Sicht **sys.event\\_log** ist nur für diese Datenbank vorhanden.

<a name="AdvancedIndexing" ></a>
### Erweiterte Indizierung

Eine Tabelle oder Sicht kann die folgenden Indizierungstypen enthalten:

- **Gruppiert**. Ein gruppierter Index gibt an, wie viele Datensätze physisch auf der Festplatte gespeichert sind. Es darf nur einen gruppierten Index pro Tabelle geben, da die Datenzeilen selbst nur in einer Reihenfolge sortiert werden können.

- **Nicht gruppiert**. Nicht gruppierte Indizes werden getrennt von den Datenzeilen gespeichert und für Suchen basierend auf dem Indexwert verwendet. Alle nicht gruppierten Indizes in einer Tabelle verwenden die Schlüsselwerte aus dem gruppierten Index als Suchschlüssel.

Betrachten Sie analog dazu: ein Buch oder ein technisches Handbuch. Der Inhalt jeder Seite entspricht einem Datensatz, die Seitenzahl ist der gruppierte Index und der Themenindex am Ende des Buchs ist der nicht gruppierte Index. Jeder Eintrag im Themenindex verweist auf den gruppierten Index, die Seitenzahl.

> [AZURE.NOTE]
Standardmäßig legt das JavaScript-Back-End für Azure Mobile Services **\\_createdAt** als gruppierten Index fest. Wenn Sie diese Spalte entfernen oder einen anderen gruppierten Index wählen möchten, müssen Sie die [Richtlinien für das Design von gruppierten Indizes](#ClusteredIndexes) unten befolgen. Im .NET-Back-End definiert die Klasse `EntityData` `CreatedAt` als gruppierten Index mit der Anmerkung `[Index(IsClustered = true)]`.

<a name="ClusteredIndexes"></a>
#### Richtlinien für das Design von gruppierten Indizes

Jede Tabelle muss einen gruppierten Index für die Spalte (bzw. im Fall eines zusammengesetzten Schlüssels für die Spalten) mit den folgenden Eigenschaften haben:

- Narrow – verwendet einen kleinen Datentyp oder ist ein [zusammengesetzter Schlüssel][Primary and Foreign Key Constraints] aus einer kleinen Anzahl schmaler Spalten
- Eindeutig, oder überwiegend eindeutig
- Static – der Wert wird nicht oft geändert
- Ever-increasing
- (Optional) Fixed-width
- (Optional) nonnull

Der Grund für die Eigenschaft **narrow** ist, dass alle anderen Indizes einer Tabelle die Schlüsselwerte des gruppierten Index als Suchschlüssel verwenden. Im Beispiel eines Themenindex am Ende eines Buchs ist der gruppierte Index eine Seitenzahl, und es handelt sich um eine kleine Anzahl. Würden stattdessen die Kapitelüberschriften in den gruppierten Index eingeschlossen, dann wäre der Themenindex selbst wesentlich länger, weil auch die Schlüsselwerte länger wären (Kapitelname, Seitenzahl).

Der Schlüssel muss **static** und **ever-increasing** sein, damit der physische Speicherort der Datensätze nicht gepflegt werden muss (dies bedeutet entweder das physische Verschieben der Datensätze oder möglicherweise die Fragmentierung des Speichers, indem Seiten aufgeteilt werden, in denen die Datensätze gespeichert sind).

Der gruppierte Index ist vor allem nützlich für Abfragen, die Folgendes tun:

- Rückgabe eines Wertebereichs mit Operatoren wie BETWEEN, >, >=, < und <=.
	- Nachdem die Zeile mit dem ersten Wert anhand des gruppierten Index gefunden wurde, ist sichergestellt, dass die Zeilen mit nachfolgenden indizierten Werten physisch daneben liegen.
- Verwenden von JOIN-Klauseln; in der Regel sind dies Fremdschlüsselspalten.
- Verwenden von ORDER BY- oder GROUP BY-Klauseln.
	- Ein Index der Spalten, die durch die ORDER BY- oder GROUP BY-Klausel angegeben sind, kann vermeiden, dass das Datenbankmodul die Daten sortieren muss, da die Zeilen bereits sortiert sind. Dadurch wird die Abfrageleistung verbessert.

#### Erstellen von gruppierten Indizes in Entity Framework

Um den gruppierten Index im .NET-Back-End mit Entity Framework festzulegen, legen Sie die Eigenschaft `IsClustered` der Anmerkung fest. Dies ist z. B. die Definition von `CreatedAt` in `Microsoft.WindowsAzure.Mobile.Service.EntityData`:

	[Index(IsClustered = true)]
	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	[TableColumnAttribute(TableColumnType.CreatedAt)]
	public DateTimeOffset? CreatedAt { get; set; }

#### Erstellen von Indizes im Datenbankschema

Für das JavaScript-Back-End können Sie den gruppierten Index einer Tabelle nur ändern, indem Sie das Datenbankschema direkt ändern, entweder über SQL Server Management Studio oder das Azure SQL-Datenbankportal.

In den folgenden Handbüchern wird beschrieben, wie Sie einen gruppierten bzw. nicht gruppierten Index festlegen, indem Sie direkt das Datenbankschema ändern:

- [Erstellen und Ändern von PRIMARY KEY-Einschränkungen][]
- [Erstellen nicht gruppierter Indizes][]
- [Erstellen gruppierter Indizes][]
- [Erstellen eindeutiger Indizes][]

#### Suchen der ersten N fehlenden Indizes
Sie können SQL-Abfragen in dynamischen Verwaltungssichten schreiben, die genauere Einzelheiten zur Ressourcenauslastung einzelner Abfragen ergeben oder die Heuristik für die hinzuzufügenden Indizes angeben. Die folgende Abfrage bestimmt, welche 10 fehlenden Indizes die höchste erwartete kumulative Verbesserung ergeben, sortiert in absteigender Reihenfolge für Benutzerabfragen.

    SELECT TOP 10 *
    FROM sys.dm_db_missing_index_group_stats
    ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)
    DESC;

In der folgenden Beispielabfrage wird ein Join für diese Tabellen ausgeführt, um eine Liste der Spalte zu erhalten, die Bestandteil der einzelnen fehlenden Indizes sein sollen, und es wird ein „Indexvorteil“ berechnet, um zu bestimmen, ob ein bestimmter Index berücksichtigt werden sollte:

    SELECT * from
    (
        SELECT
        (user_seeks+user_scans) * avg_total_user_cost * (avg_user_impact * 0.01) AS index_advantage, migs.*
        FROM sys.dm_db_missing_index_group_stats migs
    ) AS migs_adv,
      sys.dm_db_missing_index_groups mig,
      sys.dm_db_missing_index_details mid
    WHERE
      migs_adv.group_handle = mig.index_group_handle and
      mig.index_handle = mid.index_handle
      AND migs_adv.index_advantage > 10
    ORDER BY migs_adv.index_advantage DESC;

Weitere Informationen finden Sie unter [Überwachung von SQL Database mit dynamischen Verwaltungssichten][] und [Fehlende Indizes in dynamischen Verwaltungssichten][].

<a name="AdvancedQuery" ></a>
### Erweitertes Abfragedesign 

Häufig ist es schwierig zu diagnostizieren, welche Abfragen für die Datenbank am aufwendigsten sind.


#### Suchen der ersten N Abfragen

Das folgende Beispiel gibt Informationen über die „Top-Fünf“-Abfragen gemessen an durchschnittlicher CPU-Zeit zurück. In diesem Beispiel werden die Abfragen entsprechend ihrem Abfragenhash zusammengefasst, sodass logisch äquivalente Abfragen nach ihrem kumulativen Ressourcenverbrauch gruppiert werden.

	SELECT TOP 5 query_stats.query_hash AS "Query Hash",
	    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
	    MIN(query_stats.statement_text) AS "Statement Text"
	FROM
	    (SELECT QS.*,
	    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
	    ((CASE statement_end_offset
	        WHEN -1 THEN DATALENGTH(st.text)
	        ELSE QS.statement_end_offset END
	            - QS.statement_start_offset)/2) + 1) AS statement_text
	     FROM sys.dm_exec_query_stats AS QS
	     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
	GROUP BY query_stats.query_hash
	ORDER BY 2 DESC;

Weitere Informationen finden Sie unter [Überwachung von SQL Database mit dynamischen Verwaltungssichten][]. Zusätzlich zur Ausführung der Abfrage finden Sie im **Verwaltungsportal für SQL-Datenbank** eine praktische Möglichkeit, diese Daten anzuzeigen. Wählen Sie **Zusammenfassung** für Ihre Datenbank, und klicken Sie dann auf **Abfrageleistung**:

![SQL-Datenbankverwaltungsportal – Abfrageleistung][PortalSqlManagementQueryPerformance]

#### Analysieren des Abfrageplans

Nachdem Sie aufwändige Abfragen identifiziert haben, oder wenn Sie Code mit neuen Abfragen bereitstellen und deren Leistung untersuchen möchten, leisten die Tools gute Unterstützung bei der Analyse des **Abfrageplans**. Anhand des Abfrageplans können Sie erkennen, welche Vorgänge die meiste CPU-Zeit und IO-Ressourcen beanspruchen, wenn eine bestimmte SQL-Abfrage ausgeführt wird. Um den Abfrageplan in **SQL Server Management Studio** zu analysieren, verwenden Sie die markierten Symbolleistenschaltflächen.

![SQL Server Management Studio – Abfrageplan][SSMSQueryPlan]

Um den Abfrageplan im **Verwaltungsportal für SQL-Datenbank** zu analysieren, verwenden Sie die markierten Symbolleistenschaltflächen.

![SQL-Datenbankverwaltungsportal – Abfrageplan][PortalSqlManagementQueryPlan]

## Weitere Informationen

- [Azure SQL-Datenbankdokumentation][]
- [Azure SQL-Datenbankleistung und -skalierung][]
- [Problembehandlung in Azure SQL-Datenbank][]

### Indizieren

- [Grundlagen zu Indizes][]
- [Allgemeine Richtlinien zum Indexdesign][]
- [Eindeutige Richtlinien zum Indexdesign][]
- [Richtlinien für das Design von gruppierten Indizes][]
- [Primär- und Fremdschlüsselbeschränkungen][]
- [Was kostet dieser Schlüssel?][]

### Entity Framework
- [Leistungsüberlegungen zu Entity Framework 5][]
- [Code First Data Annotations][]

<!-- IMAGES -->

[SSMS]: ./media/mobile-services-sql-scale-guidance/1.png
[PortalSqlManagement]: ./media/mobile-services-sql-scale-guidance/2.png
[PortalSqlMetrics]: ./media/mobile-services-sql-scale-guidance/3.png
[PortalSqlScale]: ./media/mobile-services-sql-scale-guidance/4.png
[PortalSqlAddAlert]: ./media/mobile-services-sql-scale-guidance/5.png
[PortalSqlAddAlert2]: ./media/mobile-services-sql-scale-guidance/6.png
[PortalSqlAddAlert3]: ./media/mobile-services-sql-scale-guidance/7.png
[SetIndexJavaScriptPortal]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png
[SSMSDMVs]: ./media/mobile-services-sql-scale-guidance/8.png
[PortalSqlManagementNewQuery]: ./media/mobile-services-sql-scale-guidance/9.png
[PortalSqlManagementRunQuery]: ./media/mobile-services-sql-scale-guidance/10.png
[PortalSqlManagementQueryPerformance]: ./media/mobile-services-sql-scale-guidance/11.png
[SSMSQueryPlan]: ./media/mobile-services-sql-scale-guidance/12.png
[PortalSqlManagementQueryPlan]: ./media/mobile-services-sql-scale-guidance/13.png

<!-- LINKS -->

[klassische Azure-Portal]: http://manage.windowsazure.com
[klassischen Azure-Portal]: http://manage.windowsazure.com

[Azure SQL-Datenbankdokumentation]: http://azure.microsoft.com/documentation/services/sql-database/
[Managing SQL Database using SQL Server Management Studio]: http://go.microsoft.com/fwlink/p/?linkid=309723&clcid=0x409
[Überwachung von SQL Database mit dynamischen Verwaltungssichten]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Azure SQL-Datenbankleistung und -skalierung]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409
[Problembehandlung in Azure SQL-Datenbank]: http://msdn.microsoft.com/library/azure/ee730906.aspx
[Gründe für die Verwendung neuer Dienstebenen]: http://msdn.microsoft.com/library/azure/dn369873.aspx#Reasons

[Take-Methode]: http://msdn.microsoft.com/library/vstudio/bb503062(v=vs.110).aspx

<!-- MSDN -->
[Erstellen und Ändern von PRIMARY KEY-Einschränkungen]: http://technet.microsoft.com/library/ms181043(v=sql.105).aspx
[Erstellen gruppierter Indizes]: http://technet.microsoft.com/library/ms186342(v=sql.120).aspx
[Erstellen eindeutiger Indizes]: http://technet.microsoft.com/library/ms187019.aspx
[Erstellen nicht gruppierter Indizes]: http://technet.microsoft.com/library/ms189280.aspx

[Primary and Foreign Key Constraints]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx
[Primär- und Fremdschlüsselbeschränkungen]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx
[Grundlagen zu Indizes]: http://technet.microsoft.com/library/ms190457(v=sql.105).aspx
[Allgemeine Richtlinien zum Indexdesign]: http://technet.microsoft.com/library/ms191195(v=sql.105).aspx
[Eindeutige Richtlinien zum Indexdesign]: http://technet.microsoft.com/library/ms187019(v=sql.105).aspx
[Richtlinien für das Design von gruppierten Indizes]: http://technet.microsoft.com/library/ms190639(v=sql.105).aspx

[Fehlende Indizes in dynamischen Verwaltungssichten]: http://technet.microsoft.com/library/ms345421.aspx

<!-- EF -->
[Leistungsüberlegungen zu Entity Framework 5]: http://msdn.microsoft.com/data/hh949853
[Code First Data Annotations]: http://msdn.microsoft.com/data/jj591583.aspx
[Index Annotations in Entity Framework]: http://msdn.microsoft.com/data/jj591583.aspx#Index

<!-- BLOG LINKS -->
[Was kostet dieser Schlüssel?]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/

<!---HONumber=AcomDC_0128_2016-->