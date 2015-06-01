<properties 
	pageTitle="Fortlaufender Export von Telemetriedaten aus Application Insights" 
	description="Exportieren Sie Diagnose- und Nutzungsdaten in Microsoft Azure-Speicher, die Sie anschließendes daraus herunterladen." 
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
	ms.date="04/27/2015" 
	ms.author="awills"/>
 
# Exportieren von Telemetriedaten aus Application Insights

Möchten Sie Ihre Telemetriedaten einer angepassten Analyse unterziehen? Oder möchten Sie vielleicht eine E-Mail-Benachrichtigung zu Ereignissen mit bestimmten Eigenschaften erhalten? Der fortlaufende Export eignet sich hierfür ideal. Die Ereignisse, die Sie im Application Insights-Portal sehen, können im JSON-Format in Microsoft Azure-Speicher exportiert werden. Sie können Ihre Daten anschließend herunterladen und den Code schreiben, den Sie zu ihrer Verarbeitung benötigen.


## <a name="setup"></a> Einrichten des fortlaufenden Exports

Öffnen Sie im Application Insights-Portal auf dem Blatt "Übersicht" Ihrer Anwendung "Fortlaufender Export":

![Führen Sie einen Bildlauf nach unten aus, und klicken Sie auf "Fortlaufender Export".](./media/app-insights-export-telemetry/01-export.png)

Fügen Sie einen Export hinzu, und wählen Sie ein [Azure-Speicherkonto](storage-introduction.md), dem die Daten hinzugefügt werden sollen:

![Klicken Sie auf "Hinzufügen", "Exportziel", "Speicherkonto", und erstellen Sie dann einen neuen Speicher, oder wählen Sie einen vorhandenen Speicher aus.](./media/app-insights-export-telemetry/02-add.png)

Wählen Sie die Ereignistypen, die Sie exportieren möchten:

![Klicken Sie auf "Ereignistypen auswählen".](./media/app-insights-export-telemetry/03-types.png)


Nachdem Sie Ihren Exportvorgang erstellt haben, geht es gleich los. (Sie erhalten nur Daten, die nach Erstellung des Exportvorgangs eingehen.)


Wenn Sie die Ereignistypen später ändern möchten, bearbeiten Sie einfach den Export:

![Klicken Sie auf "Ereignistypen auswählen".](./media/app-insights-export-telemetry/05-edit.png)

Klicken Sie auf "Deaktivieren", um den Datenstrom zu beenden. Wenn Sie erneut auf "Aktivieren" klicken, wird der Datenstrom mit neuen Daten neu gestartet. Sie erhalten nicht die Daten, die im Portal eingegangen sind, während der Export deaktiviert war.

Um den Datenstrom dauerhaft zu beenden, löschen Sie den Exportvorgang. Dabei werden Ihre Daten nicht aus dem Speicher gelöscht.
#### Wer darf einen Export ergänzen oder ändern?

* Zum Ergänzen oder Ändern von Exporten benötigen Sie das Zugriffsrecht "Besitzer", "Mitwirkender" oder "Application Insights-Mitwirkender". [Weitere Informationen zu Rollen][roles].

## <a name="analyze"></a> Welche Ereignisse werden abgerufen?

Die exportierten Daten sind die rohen Telemetriedaten, die wir von Ihrer Anwendung empfangen. Es gelten diese Ausnahmen:

* Webtestergebnisse werden derzeit nicht einbezogen. 
* Wir fügen Standortdaten hinzu, die wir anhand der IP-Adresse des Clients berechnen.  

Berechnete Metriken sind nicht enthalten. Wir exportieren z. B. nicht die durchschnittliche CPU-Auslastung, doch wir exportieren die rohen Telemetriedaten, anhand derer der Durchschnitt berechnet wird.

## <a name="get"></a> Wie erhalten Sie diese?

Beim Öffnen Ihres Blobspeichers mit einem Tool wie z. B. [Server-Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx) sehen Sie einen Container mit einer Gruppe von Blobdateien. Der URI der einzelnen Dateien lautet "Anwendung-ID/Telemetrietyp/Datum/Uhrzeit".

![Überprüfen Sie den Blobspeicher mit einem geeigneten Tool](./media/app-insights-export-telemetry/04-data.png)

Datum und Uhrzeit werden in UTC angegeben und entsprechen dem Zeitpunkt, an dem die Telemetriedaten im Speicher abgelegt wurden, nicht dem Zeitpunkt ihrer Erzeugung. Wenn Sie also Code zum Herunterladen der Daten schreiben, kann sich dieser linear durch die Daten bewegen.

Um diese Daten programmgesteuert herunterzuladen, verwenden Sie die [Blobspeicher-REST-API](storage-dotnet-how-to-use-blobs.md#configure-access) oder [Azure PowerShell-Cmdlets](http://msdn.microsoft.com/library/azure/dn806401.aspx).

Oder erwägen Sie den Einsatz von [DataFactory](http://azure.microsoft.com/services/data-factory/), in der Sie Pipelines zum Verwalten von Daten nach Maß einrichten können.

Zunächst schreiben wir pro Stunde ein neues Blob (wenn Ereignisse empfangen werden). Deshalb müssen Sie stets die Daten der vorangegangenen Stunde verarbeiten und den Abschluss der aktuellen Stunde abwarten.

[Codebeispiel][exportcode]


## <a name="format"></a> Wie sehen die Daten aus?

* Jedes Blob ist eine Textdatei, die mehrere durch '\\n' getrennte Zeilen enthält.
* Jede Zeile ist ein unformatiertes JSON-Dokument. Wenn Sie sich die Daten anschauen möchten, probieren Sie einen Viewer wie z. B. Notepad ++ mit dem JSON-Plug-In:

![Zeigen Sie die Telemetriedaten mit einem geeigneten Tool an](./media/app-insights-export-telemetry/06-json.png)

Zeiten werden mithilfe von Teilstrichen dargestellt: 10.000 Teilstriche = 1 ms. Diese Werte zeigen beispielsweise einen Zeitraum von 10 ms für das Senden einer Anforderung aus dem Browser, von 30 ms für den Empfang und von 1,8 s für die Verarbeitung der Seite im Browser:

	"sendRequest": {"value": 10000.0},
	"receiveRequest": {"value": 30000.0},
	"clientProcess": {"value": 17970000.0}



## Wie sollen die Daten verarbeitet werden?

Im kleinen Rahmen können Sie Code zum Aufteilen Ihrer Daten schreiben, sie in eine Kalkulationstabelle einlesen usw. Zum Beispiel:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }


Oder Sie können die Daten in eine SQL-Datenbank verschieben – siehe das [Codebeispiel][exportcode].

Bei größeren Dimensionen sollten Sie [HDInsight](http://azure.microsoft.com/services/hdinsight/)-Hadoop-Cluster in der Cloud erwägen. HDInsight bietet eine Vielzahl von Technologien für die Verwaltung und Analyse riesiger Datenmengen.

## <a name="delete"></a>Löschen der alten Daten
Beachten Sie, dass Sie für die Verwaltung Ihrer Speicherkapazität und das Löschen der alten Daten nach Bedarf verantwortlich sind.

## Wenn Sie Ihren Speicherschlüssel erneut generieren...

Wenn Sie den Schlüssel zu Ihrem Speicher ändern, funktioniert der fortlaufende Export nicht mehr. In Ihrem Azure-Konto wird eine Benachrichtigung angezeigt.

Öffnen Sie das Blatt "Fortlaufender Export", und bearbeiten Sie den Export. Bearbeiten Sie das Exportziel, aber lassen Sie den gleichen Speicher ausgewählt. Klicken Sie auf "OK", um zu bestätigen.

![Bearbeiten Sie den fortlaufenden Exports, und öffnen und schließen Sie das Exportziel.](./media/app-insights-export-telemetry/07-resetstore.png)

Der fortlaufende Export wird neu gestartet.


## Codebeispiel

[Verschieben exportierter Daten in eine SQL-Datenbank][exportcode]

## Fragen und Antworten

* *Ich möchte aber nur ein Diagramm einmalig herunterladen.*  
 
    Wir arbeiten separat an dieser Möglichkeit.

* *Ich habe einen Export eingerichtet, aber es sind keine Daten im meinem Speicher.*

    Hat Application Insights Telemetriedaten aus Ihrer App empfangen, seit Sie den Export eingerichtet haben? Sie erhalten nur neue Daten.

* *Ich habe versucht, einen Export einrichten, aber der Zugriff wurde verweigert.*

    Wenn das Konto im Besitz Ihrer Organisation ist, müssen Sie der Gruppe "Besitzer" oder "Mitwirkende" angehören.

    <!-- Your account has to be either a paid-for account, or in the free trial period. -->

* *Kann ich Daten direkt in meinen eigenen lokalen Datenspeicher exportieren?*

    Nein, leider nicht. Unser Exportmodul benötigt einen großen geöffneten Eingabekanal, über den die Daten übertragen werden.

* *Gibt es Begrenzung der Menge von Daten, die im Speicher abgelegt werden können?*

    Nein. Wir übertragen Daten immer weiter, bis Sie den Export löschen. Wir hören auf, wenn wir uns den Grenzwerten Ihres Blobspeichers nähern, der aber ziemlich groß ist. Es liegt an Ihnen, wie viel Speicher Sie belegen.

* *Ich habe den Schlüssel zu meinem Speicher neu generiert oder den Namen des Containers geändert, und jetzt funktioniert der Export nicht mehr.*

    Bearbeiten Sie den Export, und öffnen Sie das Blatt "Exportziel". Lassen Sie denselben Speicher wie zuvor ausgewählt, und klicken Sie auf "OK", um zu bestätigen. Der Export wird neu gestartet. Wenn die Änderung in den vergangenen Tagen erfolgt ist, gehen keine Daten verloren.

* *Kann ich den Export anhalten?*

    Ja. Klicken Sie auf "Deaktivieren".


<!--Link references-->

[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[roles]: app-insights-resources-roles-access-control.md


<!--HONumber=54-->