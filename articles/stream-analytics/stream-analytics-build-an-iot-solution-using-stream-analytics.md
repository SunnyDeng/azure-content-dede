<properties 
	pageTitle="Eine IOT-Lösung mithilfe von Stream Analytics erstellen | Microsoft Azure" 
	description="Tutorial zu den ersten Schritten für die Stream Analytics IOT-Lösung für ein Mauthäuschen--Szenario."
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/21/2016" 
	ms.author="jeffstok"
/>

# Erstellen einer IOT-Lösung mithilfe von Stream Analytics

## Einführung

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Stream Analytics in Echtzeit Einblicke in Ihre Datenerhalten. Azures Dienst für die Verarbeitung von Streams ermöglicht es Entwicklern, die vielen Daten in Bewegung problemlos zu meistern, indem er Datenströme wie Clickstreams, Protokolle und gerätegenerierte Ereignisse mit Verlaufsdaten oder Verweisdaten kombiniert, um Geschäftsinformationen einfach und schnell abzuleiten. Da Azure Stream Analytics ein vollständig verwalteter Dienst für die Datenstromberechnung in Echtzeit ist, der in Microsoft Azure gehostet wird, bietet er integrierte Resilienz, Skalierbarkeit sowie geringe Latenz und ist innerhalb von Minuten einsatzbereit.

Nach Abschluss dieses Tutorials, werden Sie in der Lage sein, folgende Aufgaben auszuführen:

-   Sich mit dem Azure Stream Analytics Portal vertraut zu machen.
-   Konfigurieren und Bereitstellen eines Streamingauftrags.
-   Formulieren von realen Problemen und Behebung dieser mithilfe der Stream Analytics-Abfragesprache.
-   Problemloses Entwickeln von Streaminglösungen für Ihre Kunden mit Azure Streaming Analytics
-   Verwenden der Überwachung und Protokollierung beim Beheben von Problemen.

## Voraussetzungen

Für den erfolgreichen Abschluss dieses Tutorials wird Folgendes vorausgesetzt:

-   Aktuelle [Azure PowerShell](../powershell-install-configure.md)-Version
-   Visual Studio 2015 oder die kostenlose [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   [Azure-Abonnement](https://azure.microsoft.com/pricing/free-trial/)
-   Administratorrechte auf dem Computer
-   Herunterladen von [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) aus dem Microsoft Download Center
-   Optional: Quellcode für TollApp-Ereignisgenerator in [GitHub](https://github.com/streamanalytics/samples/tree/master/TollApp)

## Einführung in das Szenario „Hallo Maut!“


Mautstellen sind weitverbreitet – wir stoßen auf vielen Autobahnen, Brücken und in Tunneln in der ganzen Welt auf sie. Jede Mautstelle verfügt über mehrere Mauthäuschen: manuelle – d. h., Sie halten an, um die Maut bei einem Mitarbeiter zu zahlen oder automatisiert – während Sie am Mauthäuschen vorbeifahren, scannt ein Sensor auf dem Dach der Mautkabine eine RFID-Karte, die an der Windschutzscheibe Ihres Fahrzeugs befestigt ist. Es ist einfach, die Durchfahrt von Fahrzeugen durch diese Mautstellen als einen Strom von Ereignissen zu visualisieren, über den interessante Vorgänge ausgeführt werden können.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## Eingehende Daten

Wir arbeiten mit zwei Datenströmen, die von Sensoren erzeugt werden, die in der Ein- und Ausfahrt der Mautstellen installiert sind sowie mit einem statischen Nachschlagedatensatz mit Daten zu Fahrzeugregistrierungen.

### Eingangsdatenstrom

Der Eingangsdatenstrom enthält Informationen über Fahrzeuge, die in Mautstellen einfahren.
  
  
| TollId | EntryTime | LicensePlate | Zustand | Stellen | Modell | Vehicle Type | Vehicle Weight | Toll | Tag |
|---------|-------------------------|--------------|-------|--------|---------|--------------|----------------|------|-----------|
| 1 | 2014-09-10 12:01:00.000 | JNB 7001 | NY | Honda | CRV | 1 | 0 | 7 | |
| 1 | 2014-09-10 12:02:00.000 | YXZ 1001 | NY | Toyota | Camry | 1 | 0 | 4 | 123456789 |
| 3 | 2014-09-10 12:02:00.000 | ABC 1004 | CT | Ford | Taurus | 1 | 0 | 5 | 456789123 |
| 2 | 2014-09-10 12:03:00.000 | XYZ 1003 | CT | Toyota | Corolla | 1 | 0 | 4 | |
| 1 | 2014-09-10 12:03:00.000 | BNJ 1007 | NY | Honda | CRV | 1 | 0 | 5 | 789123456 |
| 2 | 2014-09-10 12:05:00.000 | CDE 1007 | NJ | Toyota | 4x4 | 1 | 0 | 6 | 321987654 |
  

Nachfolgend finden Sie eine kurze Beschreibung der Spalten:
  
  
| TollId | Die Mauthäuschen-ID (TollId) bestimmt ein Mauthäuschen eindeutig. |
|--------------|----------------------------------------------------------------|
| EntryTime | Datum und Uhrzeit der Einfahrt des Fahrzeugs in das Mauthäuschen in UTC |
| LicensePlate | Nummernschild des Fahrzeugs |
| Zustand | Angabe des Bundesstaats (USA) |
| Stellen | Der Fahrzeughersteller |
| Modell | Modellnummer des Fahrzeugs |
| VehicleType | „1“ für PKWs und „2“ für Nutzfahrzeuge |
| WeightType | Fahrzeuggewicht in Tonnen; „0“ für Pkws |
| Toll | Die Mautgebühr in US-Dollar |
| Tag | Nummer des elektronischen Geräts („e-Tag“) im Fahrzeug, das die automatische Zahlung ermöglicht. Bei nicht-automatischer Zahlung bleibt diese Spalte leer. |


### Ausgangsdatenstrom

Der Ausgangsdatenstrom enthält Informationen über die Autos, die aus der Mautstelle ausfahren.
  
  
| **TollId** | **ExitTime** | **LicensePlate** |
|------------|------------------------------|------------------|
| 1 | 2014-09-10T12:03:00.0000000Z | JNB 7001 |
| 1 | 2014-09-10T12:03:00.0000000Z | YXZ 1001 |
| 3 | 2014-09-10T12:04:00.0000000Z | ABC 1004 |
| 2 | 2014-09-10T12:07:00.0000000Z | XYZ 1003 |
| 1 | 2014-09-10T12:08:00.0000000Z | BNJ 1007 |
| 2 | 2014-09-10T12:07:00.0000000Z | CDE 1007 |

Nachfolgend finden Sie eine kurze Beschreibung der Spalten:
  
  
| Column | Beschreibung |
|--------------|-----------------------------------------------------------------|
| TollId | Die Mauthäuschen-ID (TollId) bestimmt ein Mauthäuschen eindeutig. |
| ExitTime | Datum und Uhrzeit der Ausfahrt des Fahrzeugs aus einem Mauthäuschen in UTC |
| LicensePlate | Nummernschild des Fahrzeugs |

### Registrierungsdaten von Nutzfahrzeugen

Wir verwenden eine statische Momentaufnahme der Registrierungsdatenbank für Nutzfahrzeuge.
  
  
| LicensePlate | RegistrationId | Abgelaufen |
|--------------|----------------|---------|
| SVT 6023 | 285429838 | 1 |
| XLZ 3463 | 362715656 | 0 |
| BAC 1005 | 876133137 | 1 |
| RIV 8632 | 992711956 | 0 |
| SNY 7188 | 592133890 | 0 |
| ELH 9896 | 678427724 | 1 |                      

Nachfolgend finden Sie eine kurze Beschreibung der Spalten:
  
  
| Column | Beschreibung |
|--------------|-----------------------------------------------------------------|
| LicensePlate | Nummernschild des Fahrzeugs |
| RegistrationId | RegistrationId |
| Abgelaufen | „0“, wenn das Fahrzeug registriert ist und „1“, wenn das Fahrzeug nicht mehr registriert ist. |


## Einrichten der Umgebung für Azure Stream Analytics

Sie benötigen ein Microsoft Azure-Abonnement, um dieses Tutorial zu absolvieren. Microsoft bietet kostenlose Testversionen von Microsoft Azure-Diensten, wie unten beschrieben.

Wenn Sie nicht über ein Azure-Konto verfügen, können Sie eine kostenlose Testversion anfordern, indem Sie auf folgenden Link klicken: <http://azure.microsoft.com/pricing/free-trial/>.

Hinweis: Um sich für eine kostenlose Testversion zu registrieren, ist ein Mobilgerät, das Textnachrichten empfangen kann, sowie eine gültige Kreditkarte erforderlich.

Stellen Sie sicher, dass Sie am Ende dieser Übung die Anleitung zum „Bereinigen Ihres Azure-Kontos“ schrittweise befolgen, sodass Sie das Meiste aus Ihrer 200 US-Dollar Azure-Gutschrift herausholen können.

## Bereitstellen von Azure-Ressourcen, die für das Tutorial nötig sind

Dieses Tutorial erfordert zwei (2) Azure Event Hubs, um „Eingangs-“ und „Ausgangs“-Datenströme zu empfangen. Wir werden die Azure SQL-Datenbank verwenden, um die Ergebnisse der Stream Analytics-Aufträge auszugeben. Wir verwenden zusätzlich Azure Storage, um Verweisdaten über Fahrzeugregistrierungen zu speichern.

Das Skript „Setup.ps1“ im Ordner TollApp auf GitHub kann verwendet werden, um alle erforderlichen Ressourcen zu erstellen. Aus Zeitgründen empfehlen wir Ihnen, dieses Skript auszuführen. Weitere Informationen zur Konfiguration dieser Ressourcen im Azure-Portal finden Sie im Anhang „Konfigurieren von Tutorial-Ressourcen im Azure-Portal“

Laden Sie die unterstützenden [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip)-Ordner und -Dateien herunter, und speichern Sie sie.

Öffnen Sie **ALS ADMINISTRATOR** ein Microsoft Azure PowerShell-Fenster. Falls Sie Azure PowerShell noch nicht besitzen, können Sie diese Installationsanleitung befolgen: [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

Windows blockiert automatisch ps1-, dll- und exe-Dateien, die aus dem Internet heruntergeladen werden. Vor der Ausführung des Skripts müssen wir die Ausführungsrichtlinie festlegen. Stellen Sie sicher, dass das Azure PowerShell-Fenster mit Administratorberechtigung ausgeführt wird. Führen Sie „Set-ExecutionPolicy unrestricted“ aus. Wenn Sie dazu aufgefordert werden, geben Sie „Y“ ein.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Führen Sie „Get-ExecutionPolicy“ aus, um sicherzustellen, dass der Befehl erfolgreich ausgeführt wurde.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Wechseln Sie in das Verzeichnis mit den Skripten und der Generatoranwendung.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Geben Sie „.\\Setup.ps1“ ein, um Ihr Azure-Konto einzurichten, alle benötigten Ressourcen zu erstellen und zu konfigurieren und mit dem Generieren von Ereignissen zu beginnen. Das Skript wird nach dem Zufallsprinzip einen Bereich auswählen,um Ihre Ressourcen zu erstellen. Wenn Sie die Region explizit angeben möchten, können Sie den Parameter „-location“ wie im folgenden Beispiel übergeben:

**.\\Setup.ps1-location "Central US"**

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Das Skript wird die Seite „Anmelden“ für Microsoft Azure öffnen. Geben Sie Ihre Anmeldeinformationen ein.

Beachten Sie, dass, wenn Ihr Konto den Zugriff auf mehrere Abonnements ermöglicht, Sie aufgefordert werden, den Abonnementnamen einzugeben, den Sie für das Tutorial verwenden möchten.

Die Ausführung des Skripts kann mehrere Minuten dauern. Nach Abschluss sollte die Ausgabe wie im folgenden Screenshot aussehen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Außerdem sehen Sie ein weiteres Fenster, ähnlich dem im folgenden Screenshot. Diese Anwendung sendet Ereignisse an Ihren Event Hub und ist erforderlich, um die Übungen des Tutorials auszuführen. Sie sollten die Anwendung also vor dem Ende des Tutorials nicht anhalten oder dieses Fenster schließen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Sie sollten jetzt alle erstellten Ressourcen im Azure-Verwaltungsportal sehen können. Wechseln Sie zu <https://manage.windowsazure.com>, und melden Sie sich mit Ihren Anmeldeinformationen an.

### Event Hubs

Klicken Sie auf das Menüelement „Service Bus“ auf der linken Seite des Azure-Verwaltungsportals, um Event Hubs anzuzeigen, die vom Skript aus dem vorherigen Abschnitt erstellt wurden.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Sie sehen alle verfügbaren Namespaces in Ihrem Abonnement. Klicken Sie auf den, der mit „TollData“ beginnt. (TollData4637388511 in unserem Beispiel). Klicken Sie auf die Registerkarte „Event Hubs“.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Sie sehen zwei Event Hubs namens *entry* und *exit*, die in diesem Namespace erstellt wurden.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### Azure Storage-Container

Klicken Sie auf das Menüelement „Speicher“ auf der linken Seite des Azure-Verwaltungsportals, um Speichercontainer anzuzeigen, die im Lab verwendet werden.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

Klicken Sie auf den, der mit „tolldata“ beginnt. (tolldata4637388511 in unserem Beispiel). Öffnen Sie die Registerkarte „Container“, um die erstellten Container anzuzeigen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

Klicken Sie auf „tolldata“, um hochgeladene JSON-Dateien mit Fahrzeugregistrierungsdaten anzuzeigen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### Azure SQL-Datenbank

Klicken Sie auf das Menüelement „SQL-Datenbanken“ auf der linken Seite des Azure-Verwaltungsportals, um die Azure SQL-Datenbank anzuzeigen, die im Lab verwendet werden wird.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

Klicken Sie auf „TollDataDB“

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

Kopieren Sie den Servernamen ohne die Portnummer (z. B. „*servername*.database.windows.net“).

## Herstellen einer Verbindung mit der Datenbank über Visual Studio

Wir verwenden Visual Studio, um auf Abfrageergebnisse in der Ausgabedatenbank zuzugreifen.

Gehen Sie folgendermaßen vor, um über Visual Studio eine Verbindung mit der Azure-Datenbank (dem Ziel) herzustellen:

1) Öffnen Sie Visual Studio, klicken Sie auf „Tools“ und dann auf die Menüoption „Verbindung zu Datenbank...“.

2) Wenn Sie nach der Datenquelle gefragt werden, wählen Sie „Microsoft SQL Server“ aus.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3) Fügen Sie in das Feld „Servername“ den Namen des SQL Servers ein, den Sie im vorherigen Abschnitt aus dem Azure-Portal kopiert haben (d. h. „*servername*.database.windows.net“).

4) Wählen Sie „SQL Server-Authentifizierung“ im Feld „Authentifizierung“ aus

5) Geben Sie als ANMELDENAMEN „tolladmin“ und als ANMELDEKENNWORT „123toll!“ an.

6) Wählen Sie als Datenbank „TollDataDB“ aus.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
    
7) Klicken Sie auf „OK“.

8) Öffnen Sie den Server-Explorer.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
  
9) Werfen Sie einen Blick auf die vier Tabellen, die in der Datenbank TollDataDB erstellt wurden.
  
![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)
  
## Ereignisgenerator – TollApp-Beispielprojekt

Das PowerShell-Skript beginnt automatisch damit, Ereignisse zu senden, indem es die TollApp-Beispielanwendung verwendet. Sie müssen keine zusätzlichen Schritte ausführen.

Wenn Sie jedoch an Details zur Implementierung interessiert sind, finden Sie den Quellcode der TollApp-Anwendung in GitHub unter [samples/TollApp](https://github.com/streamanalytics/samples/tree/master/TollApp).

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

##Erstellen eines Stream Analytics-Auftrags

Öffnen Sie Stream Analytics im Azure-Portal und klicken Sie auf „Neu“ in der unteren linken Ecke der Seite, um einen neuen Analytics-Auftrag zu erstellen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

Klicken Sie auf „Schnellerfassung“. Wählen Sie die Region aus, in der das Skript Ihre anderen Ressourcen erstellt.

Wählen Sie „Neues Speicherkonto erstellen“ für die Einstellung „Regionales Überwachungskonto für Speicher“, und geben sie diesem einen eindeutigen Namen. Azure Stream Analytics verwendet dieses Konto, um Überwachungsinformationen für alle Ihre zukünftigen Aufträge zu speichern.

Klicken Sie auf „Stream Analytics-Auftrag erstellen“ am unteren Ende der Seite.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## Definieren von Eingabequellen

Klicken Sie auf den erstellten Analytics-Auftrag im Portal.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

Öffnen Sie die Registerkarte „Eingaben“, um die Quelldaten zu definieren.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

Klicken Sie auf „Eingabe hinzufügen“.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

Wählen Sie „Datenstrom“ auf der ersten Seite.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

Wählen Sie „Event Hub“ auf der zweiten Seite des Assistenten.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

Geben Sie „EntryStream“ als Eingabealias ein.

Klicken Sie auf die Dropdownliste „Event Hub“und wählen Sie denjenigen aus, der mit „TollData“ beginnt (z. B. TollData9518658221).

Wählen Sie „Eintrag“ als Event Hub-Name und „alle“ als Name der Event Hub-Richtlinie.

Ihre Einstellungen sehen nun folgendermaßen aus:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

Wechseln Sie auf die nächste Seite. Wählen Sie die Werte „JSON“ und „UTF8-Codierung“.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

Klicken Sie auf „OK“ am unteren Rand des Dialogfelds, um den Assistenten zu beenden.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

Sie müssen die gleichen Schritte durchführen, um die zweite Event Hub-Eingabe für den Datenstrom mit Beendigungsereignissen zu erstellen. Stellen Sie sicher, dass Sie auf der 3. Seite die Werte wie im unten gezeigten Screenshot eingegeben haben.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

Sie haben nun zwei Eingabedatenströme definiert:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

Als nächstes werden wir die „Referenz“-Dateieingabe für die BLOB-Datei mit den Fahrzeugregistrierungsdaten hinzufügen.

Klicken Sie auf „Eingabe hinzufügen“. Wählen Sie „Verweisdaten“

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

Wählen Sie auf der nächsten Seite das Speicherkonto, das mit "tolldata" beginnt. Der Name des Containers sollte „tolldata“ sein und der BLOB-Name unter dem Musterpfad sollte „registration.json“ sein. Für diesen Dateinamen wird Groß-/Kleinschreibung berücksichtigt und er sollte komplett in Kleinbuchstaben geschrieben sein.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

Legen Sie die auf der nächsten Seite die Werte wie nachstehend gezeigt fest und klicken Sie auf „OK“, um den Assistenten zu beenden.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Jetzt sind alle Eingaben definiert.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## Definieren der Ausgabe

Wechseln Sie zur Registerkarte „Ausgabe“, und klicken Sie auf „Ausgabe hinzufügen“.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

Wählen Sie „Sql-Datenbank“

Wählen Sie den Namen des Servers, der im Abschnitt „Herstellen einer Verbindung mit der Datenbank über Visual Studio“ verwendet wurde. Der Name der Datenbank sollte „TollDataDB“ sein.

Geben Sie „tolladmin“ als den Benutzernamen und „123toll!“ als das Kennwort ein. Als Tabellenname sollte „TollDataRefJoin“ festgelegt werden.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## Azure Stream Analytics-Abfragen

Die Registerkarte „Abfrage“ enthält eine SQL-Abfrage, die die Transformation der eingehenden Daten ausführt.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

In diesem Tutorial werden wir versuchen, einige geschäftliche Fragen zu beantworten, die im Zusammenhang mit Mautdaten stehen und wir werden versuchen, Stream Analytics-Abfragen zu erstellen, die in Azure Stream Analytics verwendet werden können und so relevante Antworten liefern.

Bevor wir unseren ersten Azure Stream Analytics-Auftrag starten, betrachten wir einige Szenarien und die Abfragesyntax.

## Einführung in die Azure Stream Analytics-Abfragesprache
-----------------------------------------------------

Angenommen, wir müssen die Fahrzeuge zählen, die ein Mauthäuschen passieren. Da es sich um einen kontinuierlichen Strom von Ereignissen handelt, ist es wichtig, dass wir einen „Zeitraum“ definieren. Daher müssen wir unsere Frage ändern, die dann lauten sollte: „Wie viele Fahrzeuge passieren das Mauthäuschen in einem Zeitraum von 3 Minuten?“. Dies wird für gewöhnlich als „Rollierende Anzahl“ bezeichnet.

Sehen wir uns die Beantwortung dieser Frage durch die Azure Stream Analytics-Abfrage an.

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Wie Sie sehen können, verwendet Azure Stream Analytics eine SQL-ähnliche Abfragesprache mit einigen zusätzlichen Erweiterungen, um die Angabe zeitlicher Aspekte der Abfrage zu aktivieren.

Weitere Informationen finden Sie unter anderem in den Artikeln zu [Zeitmanagement](https://msdn.microsoft.com/library/azure/mt582045.aspx) und [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx)-Konstrukten, die in der Abfrage von MSDN verwendet werden.

## Testen von Azure Stream Analytics-Abfragen

Da wir nun unsere erste Azure Stream Analytics-Abfrage geschrieben haben, können wir sie mithilfe der Beispieldatendateien testen, die in Ihrem TollApp-Ordner unter folgendem Dateipfad liegen.

**..\\TollApp\\TollApp\\Data**

Dieser Ordner enthält die folgenden Dateien:

1.  Entry.json

2.  Exit.json

3.  Registration.json

## Frage 1 – Wie viele Fahrzeuge passieren ein Mauthäuschen?

Öffnen Sie das Azure-Verwaltungsportal, und navigieren Sie zu Ihrem erstellten Azure Stream Analytics-Auftrag. Öffnen Sie die Registerkarte „Abfrage“ und fügen Sie die Abfrage aus dem vorherigen Abschnitt ein.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

Um diese Abfrage mit Beispieldaten zu überprüfen, klicken Sie auf die Schaltfläche „Test“. Navigieren Sie im sich öffnenden Dialogfeld zu „Entry.json“, einer Datei mit Beispieldaten aus dem EntryTime-Ereignisstrom.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

Überprüfen Sie, ob die Abfrage wie erwartet ausgegeben wird.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## Frage 2 – Wie lange benötigt ein einzelnes Auto, um das Mauthäuschen zu passieren?

Wir möchten für jedes Auto, das die Mautstelle passiert, die durchschnittlich benötigte Zeit erfahren, um die Effizienz und das Kundenerlebnis bewerten zu können.

Dafür müssen wir den Datenstrom, der die Einfahrtszeit (EntryTime) enthält, mit dem Datenstrom zusammenfügen, der die Ausfahrtzeit (ExitTime) enthält. Wir werden die Datenströme über die Spalten TollId und LicencePlate zusammenfügen. Der JOIN-Operator erfordert die Angabe eines zeitlichen Spielraums, der den zulässigen Zeitunterschied zwischen den verknüpften Ereignissen beschreibt. Wir verwenden die DATEDIFF-Funktion, um anzugeben, dass Ereignisse nicht mehr als 15 Minuten auseinander liegen sollen. Wir werden die DATEDIFF-Funktion zusätzlich auf Ausfahrts- und Einfahrtszeiten anwenden, um die genaue Zeit zu berechnen, die ein Auto in der Mautstelle verbringt. Beachten Sie den Unterschied bei der Verwendung der DATEDIFF-Funktion in einer SELECT-Anweisung im Vergleich zu einer JOIN-Bedingung.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

Aktualisieren Sie die Abfrage auf der Registerkarte „Abfrage“ Ihres Auftrags, um sie zu testen:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

Klicken Sie auf „Test“, und geben Sie Beispieleingabedateien für Einfahrts- und Ausfahrtszeit (EntryTime und ExitTime) an.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

Klicken Sie auf das Kontrollkästchen, um die Abfrage zu testen und die Ausgabe anzuzeigen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## Frage 3 – Welche Nutzfahrzeuge sind nicht mehr registriert?

Azure Stream Analytics kann statische Momentaufnahmen von Daten mit temporären Datenströmen verknüpfen. Um diese Funktion zu demonstrieren, werden wir folgende Beispielfrage verwenden.

Wenn ein Nutzfahrzeug bei einer Mautfirma registriert ist, kann es das Mauthäuschen passieren, ohne für eine Kontrolle angehalten zu werden. Wir werden die Nachschlagetabellen für Nutzfahrzeugzulassung verwenden, um alle Nutzfahrzeuge mit ausgelaufener Registrierung zu identifizieren.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Beachten Sie, dass das Testen einer Abfrage mit Verweisdaten verlangt, dass eine Eingabequelle für Verweisdaten definiert ist, was wir in Schritt 5 getan haben.

Um diese Abfrage zu testen, fügen Sie die Abfrage in die Registerkarte „Abfrage“ ein, klicken Sie auf „Test“ und geben Sie zwei (2) Eingabequellen an:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

Betrachten Sie die Ausgabe der Abfrage.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## Starten des Stream Analytics-Auftrags


Da wir unsere erste Azure Stream Analytics-Abfrage geschrieben haben, ist es nun an der Zeit die Konfiguration zu beenden und den Auftrag zu starten. Speichern Sie die Abfrage aus Frage 3, die eine Ausgabe entsprechend dem Schema der Ausgabetabelle **TollDataRefJoin** erzeugt.

Navigieren Sie zum Dashboard des Auftrags und klicken Sie auf „Start“.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Ändern Sie im sich öffnenden Dialogfeld die Zeitangabe für den Ausgabestart in „Benutzerdefinierte Zeit“. Stellen Sie die Stundenangabe um eine Stunde zurück. Dadurch wird sichergestellt, dass wir alle Ereignisse vom Event Hub verarbeiten.Dies schließt auch die Ereignisse seit dem Beginn der Ereigniserstellung (am Anfang dieses Tutorials) ein. Klicken Sie auf das Häkchen, um den Auftrag zu starten.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Das Starten des Auftrags kann einige Minuten dauern. Sie können den Status auf der obersten Seite für Stream Analytics sehen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## Überprüfen Sie die Ergebnisse in Visual Studio

Öffnen Sie Visual Studio Server-Explorer und klicken Sie mit der rechten Maustaste auf die TollDataRefJoin-Tabelle. Wählen Sie „Tabellendaten anzeigen“, um die Ausgabe des Auftrags anzuzeigen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## Horizontales Skalieren von Azure Stream Analytics-Aufträgen

Azure Stream Analytics soll flexibel skalieren und mit hohen Datenlasten umgehen können. Die Azure Stream Analytics-Abfrage kann eine **PARTITION BY**-Klausel verwenden, um das System darauf hinzuweisen, dass dieser Schritt horizontal hochskaliert wird. „PartitionId“ ist eine spezielle Spalte, die vom System hinzugefügt wurde und mit der Partitions-ID der Eingabe (Event Hub) übereinstimmt.

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId    

Beenden Sie den aktuellen Auftrag, aktualisieren Sie die Abfrage in der Registerkarte „Abfrage“ und öffnen Sie die Registerkarte „Skalieren“.

Streaming-Einheiten definieren die Rechenleistung, die der Auftrag empfangen kann.

Verschieben Sie den Schieberegler auf 6.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

Wechseln Sie zur Registerkarte „Ausgabe“ und ändern Sie den Namen der SQL-Tabelle in „TollDataTumblingCountPartitioned“

Wenn Sie den Auftrag jetzt starten, kann Azure Stream Analytics die Arbeit über mehrere Compute-Ressourcen verteilen und einen besseren Durchsatz erzielen. Bitte beachten Sie, dass die TollApp-Anwendung auch Ereignisse sendet, die von der TollId partitioniert werden.

## Überwachung

Die Registerkarte „Überwachung“ enthält Statistiken zum laufenden Auftrag.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Sie können auf die Vorgangsprotokolle über die Registerkarte „Dashboard“ zugreifen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Um weitere Informationen zu einem bestimmten Ereignis anzuzeigen, wählen Sie das Ereignis aus und klicken Sie auf die Schaltfläche „Details“.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## Zusammenfassung

In diesem Tutorial haben wir Ihnen eine Einführung in den Azure Stream Analytics-Dienst gegeben. Wir haben Ihnen gezeigt, wie man Eingaben und Ausgaben für den Stream Analytics-Auftrag konfiguriert. Mit dem Mautdatenszenario haben wir die häufigsten Probleme erläutert, die im Bereich der Daten in Bewegung vorkommen und aufgezeigt, wie diese mithilfe einfacher SQL-ähnlicher Abfragen in Azure Stream Analytics behoben werden können. Wir haben SQL-Erweiterungskonstrukte für die Arbeit mit temporären Datenströmen beschrieben. Wir haben gezeigt, wie Datenströme zusammengefügt werden können und wie der Datenstrom mit statischen Verweisdaten erweitert werden kann. Es wurde erläutert, wie eine Abfrage horizontal hochskaliert werden kann, um einen höheren Durchsatz zu erzielen.

Dieses Tutorial bietet zwar einen guten einleitenden Überblick, es ist jedoch nicht allumfassend. Weitere Abfragemuster, die die SAQL-Sprache verwenden, finden Sie [hier](stream-analytics-stream-analytics-query-patterns.md). Weitere Informationen zu Azure Stream Analytics finden Sie in der [Onlinedokumentation](https://azure.microsoft.com/documentation/services/stream-analytics/).

## Bereinigung Ihres Azure-Kontos

Beenden Sie den Stream Analytics-Auftrag über das Azure-Portal.

Das Setup.ps1-Skript erstellt zwei (2) Azure Event Hubs und den Azure SQL-Datenbankserver. Die folgenden Anleitungen helfen Ihnen dabei, Ressourcen am Ende des Tutorials zu bereinigen.

Geben Sie im Fenster „PowerShell“ „.\\Cleanup.ps1“ ein. Dieser Befehl startet das Skript, das die in diesem Tutorial verwendeten Ressourcen löscht.

Beachten Sie, dass Ressourcen anhand des Namens identifiziert werden. Stellen Sie sicher, dass Sie jedes Element sorgfältig überprüfen, bevor Sie das Entfernen bestätigen.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!---HONumber=AcomDC_0323_2016-->