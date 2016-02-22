<properties
   pageTitle="Übertragen öffentlicher Daten in Azure Event Hubs mithilfe von Pull | Microsoft Azure"
   description="Übersicht über den Event Hubs-Import aus einem Webbeispiel"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="spyros;sethm" />

# Übertragen öffentlicher Daten in Azure Event Hubs mithilfe von Pull

In typischen IoT-Szenarien (Internet der Dinge) können Sie Geräte so programmieren, dass Daten mithilfe des Pushverfahrens nach Azure, entweder an einen Azure Event Hub oder an einen IoT Hub, übertragen werden. Beide Hubs sind Einstiegspunkte in Azure zum Speichern, Analysieren und Visualisieren mit einer Vielzahl von Tools, die auf Microsoft Azure zur Verfügung gestellt werden. Für beide müssen Daten jedoch per Push übertragen werden, die im JSON-Format vorliegen und auf bestimmte Weise gesichert sind. Dadurch ergibt sich die folgende Frage: Wie gehen Sie vor, wenn Sie Daten aus öffentlichen oder privaten Quellen importieren möchten, die als Webdienst oder Feed verfügbar gemacht werden, und Sie keine Möglichkeit haben, die Art der Veröffentlichung zu ändern? Nehmen Sie zum Beispiel das Wetter, Straßenverkehrsdaten oder Aktienkurse – Sie können NOAA, WSDOT oder NASDAQ kaum dazu überreden, einen Pushvorgang an Ihren Event Hub zu konfigurieren. Um dieses Problem zu lösen, haben wir ein kleines Open-Source-Cloudbeispiel geschrieben, das Sie ändern und bereitstellen können. Damit können Sie die Daten per Pullvorgang aus solchen Quellen abrufen und per Push an den Event Hub übertragen. Dort können Sie sie nach Belieben weiterverwenden, natürlich im Rahmen der Lizenzbedingungen der Datenquelle. Die Anwendung finden Sie [hier](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Beachten Sie, dass der Code in diesem Beispiel nur dazu dient, die Vorgehensweise zum Abrufen von Daten aus typischen Webfeeds und zum Schreiben der Daten in einen Azure Event Hub zu veranschaulichen. Es handelt sich NICHT um eine Produktionsanwendung, und es wurden keine Bemühungen unternommen, sie für die Nutzung in einer solchen Umgebung geeignet zu machen. Dieser Code stellt lediglich ein Beispiel für Entwickler dar. Darüber hinaus ist dieses Beispiel NICHT als eine Empfehlung zu werten, Daten eher per **Pull** als per **Push** nach Azure zu übertragen. Bevor Sie sich für eine End-to-End-Architektur entscheiden, sollten Sie Sicherheit, Leistung, Funktionalität und Kostenfaktoren überprüfen.

## Anwendungsstruktur

Die Anwendung ist in C# geschrieben, und die [Beispielbeschreibung](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) enthält alle Informationen, die Sie zum Ändern, Erstellen und Veröffentlichen der Anwendung benötigen. In den folgenden Abschnitten können Sie sich einen allgemeinen Überblick über die Funktionsweise der Anwendung verschaffen.

Zu Beginn gehen wir davon aus, dass Sie Zugriff auf einen Datenfeed haben. Beispielsweise möchten Sie die Verkehrsdaten aus dem Washington State Department of Transportation oder die Wetterdaten aus NOAA per Pull abrufen, um entweder benutzerdefinierte Berichte anzuzeigen oder die Daten mit anderen Daten in Ihrer Anwendung zu kombinieren. Sie müssen auch einen Azure Event Hub eingerichtet haben und die für den Zugriff erforderliche Verbindungszeichenfolge kennen.

Beim Starten der GenericWebToEH-Lösung wird eine Konfigurationsdatei (App.config) gelesen, um eine Reihe von Informationen abzurufen:

1. Die URL oder eine Liste von URLs für die Website, auf der die Daten veröffentlicht werden. Dies ist im Idealfall eine Website, die die Daten im JSON-Format veröffentlicht, z. B. die [hier](http://www.wsdot.wa.gov/Traffic/api/) aufgeführten WSDOT-Verweise. 
2. Anmeldeinformationen für die URL, falls erforderlich. Viele öffentliche Quellen benötigen keine Anmeldeinformationen, oder Sie können die Anmeldeinformationen in die URL-Zeichenfolge aufnehmen. Bei anderen ist es erforderlich, dass Sie die Informationen separat angeben. (Beachten Sie, dass Sie in dieser Anwendung nur einen Satz von Anmeldeinformationen angeben können. Daher funktioniert sie nur dann, wenn Sie nur eine URL, keine Liste mit URLs angeben.)
3. Die Service Bus-Verbindungszeichenfolge und der Name des Event Hubs in diesem Service Bus-Namespace, an den Sie die Daten per Push übertragen werden. Diese Informationen finden Sie im klassischen Azure-Portal.
4. Ein Ruheintervall in Millisekunden zwischen den Vorgängen zum Datenabruf von der öffentlichen Website. Zu dieser Einstellung müssen einige Aspekte berücksichtigt werden. Wenn Sie zu selten Daten abrufen, können Ihnen Daten entgehen; wenn Sie andererseits zu häufig Daten abrufen, erhalten Sie eventuell wiederholt dieselben Daten oder werden womöglich als schädlicher Bot blockiert. Ziehen Sie in Betracht, wie oft die Datenquelle aktualisiert wird – Wetter- oder Verkehrsdaten werden vielleicht alle 15 Minuten aktualisiert, aber Aktienkurse vielleicht alle paar Sekunden, je nachdem, wo Sie diese abrufen. 
5. Ein Flag, um der Anwendung mitzuteilen, ob die Daten im JSON- oder im XML-Format eingehen. Da die Daten an einen Event Hub übertragen werden sollen, umfasst die Anwendung ein Modul zum Konvertieren vom XML- ins JSON-Format vor der Übertragung.

Nach dem Lesen der Konfigurationsdatei wechselt die Anwendung in eine Schleife: Zugreifen auf die öffentliche Website, ggf. Konvertieren der Daten, Schreiben der Daten an den Event Hub und Warten auf ein Verstreichen des Ruheintervalls, bevor der Vorgang von Neuem beginnt. Genauer:

  * Lesen der öffentliche Website. Für den Empfang sendebereiter Daten wird die Instanz der RawXMLWithHeaderToJsonReader-Klasse von „Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs“ aus verwendet. Sie liest den Quelldatenstrom mit der GetData()-Methode und unterteilt ihn dann mit GetXmlFromOriginalText in kleinere Teile (Datensätze). Diese Methode liest XML-Format sowie wohlgeformten JSON-Code und JSON-Arrays. Die Verarbeitung wird anschließend anhand der MergeToXML-Konfiguration über die Datei „App.config“ (Standardeinstellung = leer) gestartet.
  * Das Empfangen und Senden von Daten wird als Schleife in der Process()-Methode in der Datei „Program.cs“ implementiert. Nach dem Empfang der Ausgabeergebnisse aus GetData() reiht die Methode getrennte Werte in die Warteschlange für den Event Hub ein.

## Nächste Schritte

Zum Bereitstellen der Lösung klonen Sie die Anwendung [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/), oder laden Sie sie herunter, bearbeiten Sie die Datei „App.config“, erstellen Sie die Anwendung, und veröffentlichen Sie sie. Nachdem Sie die Anwendung veröffentlicht haben, können Sie ihre Ausführung im klassischen Azure-Portal unter Cloud Services verfolgen und einige der Konfigurationseinstellungen (z. B. das Event Hub-Ziel und das Ruheintervall) auf der Registerkarte **Konfigurieren** ändern.

Weitere Event Hubs-Beispiele finden Sie im [Azure-Beispielkatalog](https://azure.microsoft.com/documentation/samples/?service=event-hubs) und unter [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).

<!---HONumber=AcomDC_0211_2016-->