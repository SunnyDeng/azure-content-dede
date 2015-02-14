<properties 
	pageTitle="Analysieren von Sensordaten mit Apache Storm und Microsoft Azure HDInsight (Hadoop)" 
	description="Erfahren Sie, wie Sie  Apache Storm zum Verarbeiten von Sensordaten in Echtzeit mit HDInsight (Hadoop) verwenden." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Analysieren von Sensordaten mit Storm und HBase in HDInsight (Hadoop)

Erfahren Sie, wie Sie eine Lösung erstellen können, die ein Storm-Cluster auf HDInsight zum Verarbeiten von Sensordaten von Azure Event Hub verwendet. Während der Verarbeitung speichert die Storm-Topologie eingehende Daten in einem HBase-Cluster. Die Topologie verwendet außerdem SignalR, um Informationen über ein webbasiertes, in Azure Websites gehostetes Dashboard nahezu in Echtzeit anzuzeigen.

> [AZURE.NOTE] Eine vollständige Version dieses Projekts finden Sie unter [https://github.com/Blackmist/hdinsight-eventhub-example](https://github.com/Blackmist/hdinsight-eventhub-example)

## Voraussetzungen

* Ein Azure-Abonnement

* Visual Studio mit dem [Microsoft Azure SDK für .NET](http://azure.microsoft.com/de-de/downloads/archive-net-downloads/)

* [Java und JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE] Java, das JDK, Maven und Git stehen auch über den [Chocolatey NuGet](http://chocolatey.org/)-Paket-Manager zur Verfügung.

## Erstellen des Dashboards

Das Dashboard zeigt Sensorinformationen nahezu in Echtzeit an. In diesem Fall ist das Dashboard eine ASP.NET-Anwendung, die in einer Azure Website gehostet wird. Die Anwendung dient hauptsächlich als ein [SignalR](http://www.asp.net/signalr/overview/getting-started/introduction-to-signalr)-Hub, das während der Verarbeitung von Nachrichten Informationen von der Storm-Topologie empfängt.

Die Website enthält eine statische index.html-Datei, die sich ebenfalls mit SignalR verbindet, und verwendet D3.js zur grafischen Darstellung der Daten aus der Storm-Topologie.

> [AZURE.NOTE] Alternativ könnten Sie reine WebSockets anstelle von SignalR verwenden. WebSockets bietet jedoch keinen eingebauten Skalierungsmechanismus, falls Sie die Website horizontal skalieren müssen. SignalR kann über den Azure Service Bus skaliert werden ([http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus](http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus)).
>
> Ein Beispiel für die Kommunikation zwischen einer Storm-Topologie und einer Python-Website über reine WebSockets finden Sie im Projekt [Storm Tweet Sentiment D3 Visualization](https://github.com/P7h/StormTweetsSentimentD3Viz).

1. Erstellen Sie eine neue C#-Anwendung in Visual Studio mithilfe der Projektvorlage **ASP.NET-Webanwendung**. Geben Sie der neuen Anwendung den Namen **Dashboard**.

2. Wählen Sie im Fenster **Neues ASP.NET-Projekt** die Anwendungsvorlage **Leer** aus. Wählen Sie im Bereich **Windows Azure** die Optionen **In der Cloud hosten** und **Website** aus. Klicken Sie abschließend auf **OK**.

	> [AZURE.NOTE] Melden Sie sich bei Ihrem Azure-Abonnement an, wenn Sie dazu aufgefordert werden.

3. Geben Sie im Dialogfeld **Windows Azure-Website konfigurieren** einen **Websitenamen** und eine **Region** für Ihre Website ein, und klicken Sie dann auf **OK**. Daraufhin wird die Azure Website erstellt, die das Dashboard hosten wird.

4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen | SignalR-Hub-Klasse (v2)** aus. Geben Sie der Klasse den Namen **DashHub.cs**, und fügen Sie sie dem Projekt hinzu. Diese Klasse wird das SignalR-Hub enthalten, das für die Kommunikation zwischen HDInsight und der Dashboard-Website verwendet dient.

	> [AZURE.NOTE] In Visual Studio 2012 ist die Vorlage **SignalR-Hub-Klasse (v2)** nicht verfügbar. Fügen Sie in diesem Fall eine einfache **Klasse** mit dem Namen "DashHub" hinzu. Außerdem müssen Sie das SignalR-Paket manuell installieren, indem Sie **Extras | Bibliothekspaket-Manager | Paket-Manager-Konsole** öffnen und den folgenden Befehl ausführen:
	>
	> `install-package Microsoft.AspNet.SignalR`

5. Ersetzen Sie den Code in **DashHub.cs** durch den folgenden Code.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		using Microsoft.AspNet.SignalR;

		namespace dashboard
		{
		    public class DashHub : Hub
		    {
		        public void Send(string message)
		        {
		            // Call the broadcastMessage method to update clients.
		            Clients.All.broadcastMessage(message);
		        }
		    }
		}

6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen | OWIN Startup-Klasse** aus. Geben Sie der neuen Klasse den Namen **Startup.cs**.

	> [AZURE.NOTE] In Visual Studio 2012 ist die Vorlage **OWIN-Startup-Klasse** nicht verfügbar. Erstellen Sie in diesem Fall eine einfache **Klasse** mit dem Namen "Startup".

7. Ersetzen Sie den Inhalt von **Startup.cs** durch Folgendes.

		using System;
		using System.Threading.Tasks;
		using Microsoft.Owin;
		using Owin;

		[assembly: OwinStartup(typeof(dashboard.Startup))]

		namespace dashboard
		{
		    public class Startup
		    {
		        public void Configuration(IAppBuilder app)
		        {
		            // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=316888
		            app.MapSignalR();
		        }
		    }
		}

8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und dann auf **Hinzufügen | HTML-Seite**. Geben Sie der neuen Seite den Namen **index.html**. Diese Seite wird das Echtzeit-Dashboard für das Projekt enthalten. Sie empfängt Informationen vom DashHub und zeigt eine Grafik mithilfe von D3.js an.

9. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **index.html**, und wählen Sie **Als Startseite festlegen** aus.

10. Ersetzen Sie den Code in **index.html** durch den folgenden Code.

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Dashboard</title>
		    <style>

		        .x.axis line {
		            shape-rendering: auto;
		        }

		        .line {
		            fill: none;
		            stroke-width: 1.5px;
		        }

		    </style>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Reference d3.js.-->
		    <script src="http://d3js.org/d3.v3.min.js"></script>
		</head>
		<body>
		    <script>
		        $(function () {
		            //Huge thanks to Mike Bostok for his Path Transitions article - http://bost.ocks.org/mike/path/
		            var n = 243,                                 //number of x coordinates in the graph
		            duration = 750,                          //duration for transitions
		            deviceValue=[0,0,0,0,0,0,0,0,0,0],       //temp holding for each device value
		            now = new Date(Date.now() - duration),   //Now
		            //fill an array of arrays with dummy data to start the chart
		            //each item in the top-level array is a line
		            //each item in the line arrays represents the X coordinate across a graph
		            //The 'value' within each line array represents the Y coordinate for that point
		            data = [
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; })
		            ];

		            //Color scale for 10 items
		            var color = d3.scale.category10();
		            //The domain for color (the device IDs)
		            var devices = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];
		            //This will auto-generate colors for this range of IDs
		            color.domain(devices);

		            //set margins and figure out width/height
		            var margin = {top: 6, right: 0, bottom: 20, left: 40},
		                width = 960 - margin.right,
		                height = 240 - margin.top - margin.bottom;

		            //the time scale for the X axis
		            var x = d3.time.scale()
		                .domain([now - (n - 2) * duration, now - duration])
		                .range([0, width]);

		            //the numerical scale for the Y axis
		            var y = d3.scale.linear()
		                .domain([100, 0])
		                .range([0, height]);

		            //The line, which is really just a
		            //couple functions that we can pass data to
		            //in order to get back x/y coords.
		            var line = d3.svg.line()
		                .interpolate("basis")
		                .x(function (d, i) { return x(now - (n - 1 - i) * duration); })
		                .y(function (d, i) { return y(d.value); });

		            //Find the HTML body element and add a child SVG element
		            var svg = d3.select("body").append("svg")
		                .attr("width", width + margin.left + margin.right)
		                .attr("height", height + margin.top + margin.bottom)
		              .append("g")
		                .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

		            //Define a clipping path, because we need to clip
		            //the graph to render only the bits we want to see
		            //as it moves
		            svg.append("defs").append("clipPath")
		                .attr("id", "clip")
		              .append("rect")
		                .attr("width", width)
		                .attr("height", height);

		            //Append the x axis
		            var axis = svg.append("g")
		                .attr("class", "x axis")
		                .attr("transform", "translate(0," + height + ")")
		                .call(x.axis = d3.svg.axis().scale(x).orient("bottom"));

		            //append the y axis
		            var yaxis = svg.append("g")
		                .attr("class", "y axis")
		                .call(y.axis = d3.svg.axis().scale(y).orient("left").ticks(5));

		            //append the clipping path
		            var linegroup = svg.append("g")
		              .attr("clip-path", "url(#clip)");

		            //magic. Select all paths with a class of .line
		            //if they don't exist, make them.
		            //use the points in the line object to define
		            //the paths
		            //set the color to the cooresponding auto-generated coclor
		            var path = linegroup.selectAll(".line")
		              .data(data)
		              .enter().append("path")
		              .attr("class", "line")
		              .attr("d", line)
		              .style("stroke", function (d, i) { return color(i); });

		            //We need to transition the graph after all
		            //lines have been updated. There's no
		            //built-in for this, so this function
		            //does reference counting on end events
		            //for each line, then applies whatever
		            //callback when all are finished.
		            function endall(transition, callback) {
		                var n = 0;
		                transition
		                    .each(function () { ++n; })
		                    .each("end", function () { if (!--n) callback.apply(this, arguments); });
		            }

		            //wire up the SignalR client and listen for messages
		            var chat = $.connection.dashHub;
		            chat.client.broadcastMessage = function (message) {
		                //parse the JSON data
		                var incomingData = JSON.parse(message);
		                //stuff it in the global array slot for the device ID
		                deviceValue[incomingData.device] = incomingData.temperature;

		            };
		            //start listening
		            $.connection.hub.start();
		            //tick for D3 graphics
		            tick();

		            function tick() {
		                // update the domains
		                now = new Date();
		                x.domain([now - (n - 2) * duration, now - duration]);

		                //push the (presumably) fresh data deviceValue array onto
		                //the arrays that define the lines.
		                for (i = 0; i < 10; i++) {
		                    data[i].push({ value: deviceValue[i] });
		                    //data[1].push({ value: maxValue });
		                }
		                //slide the x-axis left
		                axis.transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .call(x.axis);

		                //Update the paths based on the updated line data
		                //and slide left
		                path
		                    .attr("d", line)
		                    .attr("transform", null)
		                .transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .attr("transform", "translate(" + x(now - (n - 1) * duration) + ",0)")
		                    .call(endall, tick);

		                // pop the old data point off the front
		                // of the arrays
		                for (var i = 0; i < data.length; i++) {
		                    data[i].shift();
		                };
		            };
		         })()
		        </script>
		    </body>
		</html>

	> [AZURE.NOTE] Der Paket-Manager installiert möglicherweise eine neuere Version der SignalR-Skripts. Stellen Sie sicher, dass die folgenden Skriptverweise mit den Versionen der Skriptdateien im Projekt übereinstimmen (die Verweise können abweichen, wenn Sie SignalR per NuGet hinzugefügt haben, anstatt ein Hub zu erstellen).

11. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und dann auf **Hinzufügen | HTML-Seite**. Geben Sie der neuen Seite den Namen **test.html**. Diese Seite kann Nachrichten senden und empfangen, um DashHub und Dashboard zu testen.

12. Ersetzen Sie den Code in **test.html** durch den folgenden Code.

		<!DOCTYPE html>
		<html>
		<head>
		    <title>Test</title>
		    <style type="text/css">
		        .container {
		            background-color: #99CCFF;
		            border: thick solid #808080;
		            padding: 20px;
		            margin: 20px;
		        }
		    </style>
		</head>
		<body>
		    <div class="container">
		        <input type="text" id="message" />
		        <input type="button" id="sendmessage" value="Send" />
		        <input type="hidden" id="displayname" />
		        <ul id="discussion"></ul>
		    </div>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Add script to update the page and send messages.-->
		    <script type="text/javascript">
		        $(function () {
		            // Declare a proxy to reference the hub.
		            var chat = $.connection.dashHub;
		            // Create a function that the hub can call to broadcast messages.
		            chat.client.broadcastMessage = function (message) {
		                // Html encode display the message.
		                var encodedMsg = $('<div />').text(message).html();
		                // Add the message to the page.
		                $('#discussion').append('<li>' + encodedMsg + '</li>');
		            };
		            // Set initial focus to message input box.
		            $('#message').focus();
		            // Start the connection.
		            $.connection.hub.start().done(function () {
		                $('#sendmessage').click(function () {
		                    // Call the Send method on the hub.
		                    chat.server.send($('#message').val());
		                    // Clear text box and reset focus for next comment.
		                    $('#message').val('').focus();
		                });
		            });
		        });
		    </script>
		</body>
		</html>

13. Klicken Sie für das Projekt auf **Alle speichern**.

14. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **Dashboard**, und wählen Sie **Veröffentlichen** aus. Wählen Sie die Website aus, die Sie für dieses Projekt erstellt haben, und klicken Sie auf **Veröffentlichen**.

15. Nach dem Veröffentlichen der Website sollte die Website mit einer beweglichen Zeitleiste automatisch geöffnet werden.

### Testen des Dashboards

1. Öffnen Sie die Seite **test.html** in dieser Website in einem neuen Browserfenster, um zu prüfen, ob SignalR funktionstüchtig ist und ob das Dashboard Diagrammlinien für die an SignalR gesendeten Daten enthält. Beispiel: **http://mydashboard.azurewebsites.net/test.html**.

2. Das Dashboard erwartet Daten im JSON-Format mit einem Wert für **device id** und **temperature**. Zum Beispiel **{"device":0, "temperature":80}**. Geben Sie einige Testwerte auf der Seite **test.html** ein, und verwenden Sie die Geräte-IDs 0 bis 9, während Sie das Dashboard in einer anderen Seite geöffnet haben. Beachten Sie, dass die Linien für die einzelnen Geräte-IDs in unterschiedlichen Farben gezeichnet werden.

## Konfigurieren des Ereignis-Hubs

Das Ereignis-Hub empfängt Nachrichten (Ereignisse) von den Sensoren. Führen Sie die folgenden Schritte aus, um ein neues Ereignis-Hub zu erstellen.

1. Klicken Sie im [Azure-Portal](https://manage.windowsazure.com) auf **NEU | Service Bus | Ereignis-Hub | Benutzerdefiniert erstellen**.

2. Geben Sie im Dialogfeld **Neues Ereignis-Hub hinzufügen** einen **Ereignis-Hub-Namen** ein, wählen Sie die **Region** aus, in der das Hub erstellt werden soll, und erstellen Sie einen neuen Namespace, oder wählen Sie einen vorhandenen Namespace aus. Klicken Sie zuletzt auf den **Pfeil**.

3. Geben Sie im Dialogfeld **Ereignis-Hub konfigurieren** die Werte für **Anzahl der Partitionen** und **Nachrichtenaufbewahrung** ein. Verwenden Sie für dieses Beispiel eine Anzahl von 10 Partitionen und eine Nachrichtenaufbewahrung von 1.

4. Wählen Sie nach der Erstellung des Ereignis-Hubs den Namespace aus, und klicken Sie auf **Ereignis-Hubs**. Wählen Sie zuletzt das Ereignis-Hub aus, das Sie zuvor erstellt haben.

5. Klicken Sie auf **Konfigurieren**, und erstellen Sie zwei neue Zugriffsrichtlinien mit den folgenden Daten.

	<table>
	<tr><th>Name</th><th>Berechtigungen</th></tr>
	<tr><td>devices</td><td>Senden</td></tr>
	<tr><td>storm</td><td>Empfangen</td></tr>
	</table>

	Klicken Sie nach dem Erstellen der Berechtigungen auf das **Speichern**-Symbol am unteren Seitenrand. Daraufhin werden die gemeinsam genutzten Zugriffsrichtlinien erstellt, die für den Austausch von Nachrichten mit diesem Hub verwendet werden.

6. Verwenden Sie nach dem Speichern der Richtlinien den **Freigegebener Generator für Zugriffsschlüssel** am unteren Seitenrand, um den Schlüssel für die Richtlinien **devices** und **storm** abzurufen. Speichern Sie diese Schlüssel für den späteren Gebrauch.

### Senden von Nachrichten an das Ereignis-Hub

Da nicht jeder Benutzer direkten Zugriff auf einfache Sensoren hat, wird eine .NET-Anwendung verwendet, um Zufallszahlen zu generieren. Die anhand der folgenden Schritte erstellte .NET-Anwendung generiert jede Sekunde Ereignisse für 10 Geräte, bis Sie die Anwendung durch einen Tastendruck beenden.

1. Erstellen Sie in Visual Studio ein neues **Windows Desktop**-Projekt, und wählen Sie die Projektvorlage **Konsolenanwendung** aus. Geben Sie dem Projekt den Namen **SendEvents**, und klicken Sie dann auf **OK**.

2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **SendEvents**, und wählen Sie **NuGet-Pakete verwalten** aus.

3. Suchen Sie unter **NuGet-Pakete verwalten** nach den folgenden Paketen, und installieren Sie diese.

	* **Microsoft Azure Service Bus**
	* **JSON.Net**

	Installieren Sie die Pakete, und **schließen** Sie anschließend den Paket-Manager.

4. Ersetzen Sie den Inhalt von **Program.cs** durch Folgendes.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.ServiceBus.Messaging;
		using Newtonsoft.Json;
		using Microsoft.ServiceBus;
		using System.Threading;

		namespace SendEvents
		{
		    class Program
		    {

		        static int numberOfDevices = 10;
		        static string eventHubName = "temperature";
		        static string eventHubNamespace = "namespace";
		        static string sharedAccessPolicyName = "devices";
		        static string sharedAccessPolicyKey = "key for devices policy";

		        static void Main(string[] args)
		        {
		            var settings = new MessagingFactorySettings()
		            {
		                TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(sharedAccessPolicyName, sharedAccessPolicyKey),
		                TransportType = TransportType.Amqp
		            };
		            var factory = MessagingFactory.Create(
		                 ServiceBusEnvironment.CreateServiceUri("sb", eventHubNamespace, ""), settings);

		            EventHubClient client = factory.CreateEventHubClient(eventHubName);

		            try
		            {

		                List<Task> tasks = new List<Task>();
		                // Send messages to Event Hub
		                Console.WriteLine("Sending messages to Event Hub {0}", client.Path);
		                Random random = new Random();
		                //for (int i = 0; i < numberOfMessages; ++i)
		                while(!Console.KeyAvailable)
		                {
		                    // One event per device
		                    for(int devices = 0; devices < numberOfDevices; devices++)
		                    {
		                        // Create the device/temperature metric
		                        Event info = new Event() {
		                            TimeStamp = DateTime.UtcNow,
		                            DeviceId = random.Next(numberOfDevices),
		                            Temperature = random.Next(100)
		                        };
		                        // Serialize to JSON
		                        var serializedString = JsonConvert.SerializeObject(info);
		                        Console.WriteLine(serializedString);
		                        EventData data = new EventData(Encoding.UTF8.GetBytes(serializedString))
		                        {
		                            PartitionKey = info.DeviceId.ToString()
		                        };

		                        // Send the metric to Event Hub
		                        tasks.Add(client.SendAsync(data));
		                    }
		                    // Sleep a second
		                    Thread.Sleep(1000);
		                };

		                Task.WaitAll(tasks.ToArray());
		            }
		            catch (Exception exp)
		            {
		                Console.WriteLine("Error on send: " + exp.Message);
		            }

		        }
		    }
		}

	Momentan erhalten sie eine Warnung in Zeilen, die auf die Ereignisklasse verweisen. Ignorieren Sie diese Warnungen fürs Erste.

5. Setzen Sie am Anfang der Datei **Program.cs** den Wert für die folgenden Variablen auf die entsprechenden Werte, die Sie für Ihr Ereignis-Hub im Azure-Verwaltungsportal abgerufen haben.

	<table>
	<tr><th>Variablenname</th><th>Wert</th></tr>
	<tr><td>eventHubName</td><td>Der Name Ihres Ereignis-Hubs. Beispiel: <strong>temperature</strong>.</td></tr>
	<tr><td>eventHubNamespace</td><td>Der Namespace Ihres Ereignis-Hubs. Beispiel: <strong>sensors-ns</strong>.</td></tr>
	<tr><td>sharedAccessPolicyName</td><td>Die Richtlinie, die Sie mit Sendezugriff erstellt haben. Beispiel: <strong>devices</strong>.</td></tr>
	<tr><td>sharedAccessPolicyKey</td><td>Der Schlüssel für die Richtlinie mit Sendezugriff.</td></tr>
	</table>

6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **SendEvents** und dann auf **Hinzufügen | Klasse**. Geben Sie der neuen Klasse den Namen **Event.cs**. Diese Klasse wird die an das Ereignis-Hub gesendeten Nachrichten beschreiben.

7. Ersetzen Sie den Inhalt von **Event.cs** durch Folgendes.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Runtime.Serialization;
		using System.Text;
		using System.Threading.Tasks;

		namespace SendEvents
		{
		    [DataContract]
		    public class Event
		    {
		    	[DataMember]
		    	public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public int DeviceId { get; set; }
		        [DataMember]
		        public int Temperature { get; set; }
		    }
		}

	Diese Klasse beschreibt die Daten, die wir senden: ein Zeitstempel, eine Geräte-ID und ein Temperaturwert.

8. Klicken Sie auf **Alle speichern**, und führen Sie die Anwendung aus, um das Ereignis-Hub mit Nachrichten zu füllen.

## Erstellen eines virtuellen Netzwerks in Azure

Sie müssen beide Server in einem virtuellen Netzwerk in Azure bereitstellen, damit die Topologie im Storm-Cluster direkt mit HBase kommunizieren kann.

1. Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Portal] an.

2. Klicken Sie unten auf der Seite auf **+NEU**, dann auf **Netzwerkdienste**, **Virtuelles Netzwerk** und anschließend auf **Schnellerfassung**.

3. Geben Sie folgende Werte ein bzw. wählen diese aus:

	- **Name**: Der Name des virtuellen Netzwerks.
	- **Adressraum**: Wählen Sie einen Adressraum für das virtuelle Netzwerk, der groß genug ist, um Adressen für alle Knoten im Cluster zur Verfügung zu stellen. Andernfalls schlägt die Bereitstellung fehl.
	- **Maximale Anzahl virtueller Computer**: Wählen Sie eine maximale Anzahl an virtuellen Computern aus.
	- **Speicherort**: Der Speicherort muss derselbe sein wie der des HBase-Clusters, den Sie erstellen.
	- **DNS-Server**: In diesem Artikel werden interne DNS-Server verwendet, die von Azure bereitgestellt werden. Daher können Sie **Keine** auswählen. Erweiterte Netzwerkkonfiguration mit benutzerdefinierten DNS-Servern wird ebenfalls unterstützt. Eine genaue Anleitung finden Sie unter [http://msdn.microsoft.com/library/azure/jj156088.aspx](http://msdn.microsoft.com/library/azure/jj156088.aspx).

4. Klicken Sie auf **Virtuelles Netzwerk erstellen**. Der Name des neuen virtuellen Netzwerks wird in der Liste angezeigt. Warten Sie, bis in der Statusspalte **Erstellt** angezeigt wird.

5. Klicken Sie im Hauptbereich auf das virtuelle Netzwerk, das Sie gerade erstellt haben.

6. Klicken Sie oben auf der Seite auf **DASHBOARD**.

7. Notieren Sie sich die **ID DES VIRTUELLEN NETZWERKS**. Sie finden diese unter **Auf einen Blick**. Sie benötigen diesen Wert für die Bereitstellung der Storm- und HBase-Cluster.

8. Klicken Sie oben auf der Seite auf **KONFIGURIEREN**.

9. Unten auf der Seite finden Sie den Namen des Standardsubnetzes. Dieser lautet **Subnet-1**. Klicken Sie auf die Schaltfläche **Subnetz hinzufügen**, um **Subnet-2** hinzuzufügen. Diese Subnetze werden die Storm- und HBase-Cluster enthalten.

	> [AZURE.NOTE] In diesem Artikel verwenden wir Cluster mit nur einem Knoten. Falls Sie Cluster mit mehreren Knoten erstellen, müssen Sie den Wert **CIDR (Anzahl Adressen)** für das Subnetz überprüfen, das Sie für das Cluster verwenden. Die Anzahl der Adressen muss größer als die Anzahl der Workerknoten plus Sieben sein (Gateway: 2, Hauptknoten: 2, Zookeeper: 3). Wenn Sie zum Beispiel einen HBase-Cluster mit 10 Knoten benötigen, muss die Anzahl der Adressen für das Subnetz größer als 17 (10 + 7) sein. Andernfalls schlägt die Bereitstellung fehl.
	>
	> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.

11. Klicken Sie unten in der Seite auf **Speichern**.

## Erstellen Sie den Storm-Cluster in HDInsight

1. Melden Sie sich beim [Azure-Verwaltungsportal][azureportal] an.

2. Klicken Sie links auf **HDInsight** und anschließend unten links auf **+NEU**.

3. Klicken Sie auf das HDInsight-Symbol in der zweiten Spalte, und wählen Sie **Benutzerdefiniert** aus.

4. Geben Sie auf der Seite **Clusterdetails** den Namen für das neue Cluster ein, und wählen Sie **Storm** als **Clustertyp** aus. Wählen Sie den Pfeil, um fortzufahren.

5. Geben Sie für die Anzahl der **Datenknoten** für diesen Cluster 1 ein. Wählen Sie unter **Region/Virtuelles Netzwerk** das zuvor erstellte virtuelle Azure-Netzwerk aus. Wählen Sie unter **Virtuelles Netzwerk - Subnetze** den Eintrag **Subnet-2** aus.

	> [AZURE.NOTE] Reduzieren Sie die **Clustergröße** auf 1, und löschen Sie den Cluster nach der Verwendung, um die Kosten für den in diesem Artikel verwendeten Cluster zu minimieren.

6. Geben Sie den **Benutzernamen** und das **Kennwort** für den Administrator ein, und klicken Sie auf den Pfeil, um fortzufahren.

7. Wählen Sie unter **Speicherkonto** entweder **Neuen Speicher erstellen** aus, oder verwenden Sie ein vorhandenes Speicherkonto. Wählen Sie den zu verwendenden **Kontonamen** und **Standardcontainer** aus, oder geben Sie sie ein. Klicken Sie auf das Häkchensymbol unten links, um das Storm-Cluster zu erstellen.

## Erstellen des HDInsight HBase-Clusters

1. Melden Sie sich beim [Azure-Verwaltungsportal][azureportal] an.

2. Klicken Sie links auf **HDInsight** und anschließend unten links auf **+NEU**.

3. Klicken Sie auf das HDInsight-Symbol in der zweiten Spalte, und wählen Sie **Benutzerdefiniert** aus.

4. Geben Sie in der Seite **Clusterdetails** den Namen für das neue Cluster ein, und wählen Sie **HBase** als **Clustertyp** aus. Wählen Sie den Pfeil, um fortzufahren.

5. Geben Sie für die Anzahl der **Datenknoten** für diesen Cluster 1 ein. Wählen Sie unter **Region/Virtuelles Netzwerk** das zuvor erstellte virtuelle Azure-Netzwerk aus. Wählen Sie unter **Virtuelles Netzwerk - Subnetze** den Eintrag **Subnet-1** aus.

	> [AZURE.NOTE] Reduzieren Sie die **Clustergröße** auf 1, und löschen Sie den Cluster nach der Verwendung, um die Kosten für den in diesem Artikel verwendeten Cluster zu minimieren.

6. Geben Sie den **Benutzernamen** und das **Kennwort** für den Administrator ein, und klicken Sie auf den Pfeil, um fortzufahren.

7. Wählen Sie unter **Speicherkonto** entweder **Neuen Speicher erstellen** aus, oder verwenden Sie ein vorhandenes Speicherkonto. Wählen Sie den zu verwendenden **Kontonamen** und **Standardcontainer** aus, oder geben Sie sie ein. Klicken Sie auf das Häkchensymbol unten links, um das Storm-Cluster zu erstellen.

	> [AZURE.NOTE] Sie sollten einen anderen Container als denjenigen verwenden, den Sie für das Storm-Cluster ausgewählt haben.

### Remotedesktop aktivieren

In diesem Lernprogramm verwenden wir Remotedesktop, um auf die Storm- und HBase-Cluster zuzugreifen. Führen Sie die folgenden Schritte aus, um Remotedesktop für beide Cluster zu aktivieren.

1. Melden Sie sich beim [Azure-Verwaltungsportal][azureportal] an.

2. Wählen Sie auf der linken Seite **HDInsight** aus, und wählen Sie anschließend Ihr Storm-Cluster in der Liste aus. Wählen Sie abschließend oben auf der Seite **Konfigurieren** aus.

3. Klicken Sie unten in der Seite auf **Remote aktivieren**. Geben Sie einen Benutzernamen, ein Kennwort und ein Ablaufdatum für den Remotedesktop ein, wenn Sie dazu aufgefordert werden. Klicken Sie auf das Häkchen, um Remotedesktop zu aktivieren.

Nachdem Sie Remotedesktop aktiviert haben, können Sie unten auf der Seite auf **Verbinden** klicken. Folgen Sie den Aufforderungen auf dem Bildschirm, um sich mit dem Cluster zu verbinden.

### Ermitteln des HBase DNS-Suffix

Sie müssen den vollqualifizierten Domänennamen (Fully Qualified Domain Name FQDN) für das HBase-Cluster verwenden, um vom Storm-Cluster aus in das HBase-Cluster schreiben zu können. Führen Sie die folgenden Schritte aus, um diese Informationen abzurufen.

1. Verbinden Sie sich per Remotedesktop mit dem HBase-Cluster.

2. Öffnen Sie nach dem Herstellen der Clusterverbindung die Hadoop-Befehlszeile, und führen Sie den Befehl **ipconfig** aus, um das DNS-Suffix abzurufen. Sie finden den Suffixwert unter **Verbindungsspezifisches DNS-Suffix**. Beispiel: **mycluster.b4.internal.cloudapp.net**. Speichern Sie diese Informationen.

## Entwickeln der Storm-Topologie

> [AZURE.NOTE] Die Schritte in diesem Abschnitt sollten in Ihrer lokalen Entwicklungsumgebung ausgeführt werden.

### Herunterladen und Erstellen externer Abhängigkeiten

Manche der in diesem Projekt verwendeten Abhängigkeiten müssen separat heruntergeladen und erstellt werden, bevor Sie diese im lokalen Maven-Repository in Ihrer Entwicklungsumgebung installieren können. Dieser Abschnitt behandelt das Herunterladen und Installieren.

* Das Ereignis-Hub-Spout liest Nachrichten aus dem Ereignis-Hub.

* Das Java-Client-SDK für SignalR

#### Herunterladen und Erstellen des Ereignis-Hub-Spouts

Wir verwenden **eventhubs-storm-spout**, um Daten vom Ereignis-Hub zu empfangen.

1. Verbinden Sie sich per Remotedesktop mit Ihrem Storm-Cluster, und kopieren Sie die Datei **%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** in Ihre lokale Entwicklungsumgebung. Diese Datei enthält das **events-storm-spout**.

2. Führen Sie den folgenden Befehl aus, um das Paket im lokalen Maven-Speicher zu installieren. Auf diese Weise können Sie das Paket später als Verweis im Storm-Projekt hinzufügen.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

#### Herunterladen und Erstellen des SignalR-Clients

Verwenden Sie das [SignalR-Client-SDK für Java](https://github.com/SignalR/java-client), um Nachrichten an das ASP.NET-Dashboard zu senden.

1. Öffnen Sie eine Eingabeaufforderung.

2. Wechseln Sie in das Verzeichnis, in dem Sie das SignalR-Client-SDK-Projekt herunterladen und speichern möchten.

3. Führen Sie den folgenden Befehl aus, um das Projekt von GitHub herunterzuladen.

	git clone https://github.com/SignalR/java-client

4. Wechseln Sie in das Verzeichnis **java-client\signalr-client-sdk**, und führen Sie die folgenden Befehle aus, um das Projekt in eine JAR-Datei zu kompilieren.

		cd java-client\signalr-client-sdk
		mvn package

	> [AZURE.NOTE] Wenn Sie eine Fehlermeldung erhalten, dass die **gson**-Abhängigkeit nicht heruntergeladen werden kann, entfernen Sie die folgenden Zeilen aus der Datei **java-client\signalr-client-sdk\pom.xml**.
	> 
<repository>
<id>central</id>
<name>Central</name>
<url>http://maven.eclipse.org/build</url>
</repository>
</repositories>

	> Wenn Sie diese Zeilen entfernen, ruft Maven die Datei aus dem zentralen Repository ab (das Standardverhalten). Verwenden Sie den Befehl `-U`, um Maven anzuweisen, das Repository erneut zu versuchen. Beispiel: `mvn package -U`

5. Führen Sie den folgenden Befehl aus, um das Paket im lokalen Maven-Speicher zu installieren. Auf diese Weise können Sie das Paket später als Verweis im Storm-Projekt hinzufügen.

		mvn install:install-file -Dfile=target/signalr-client-sdk-1.0.jar -DgroupId=microsoft.aspnet.signalr -DartifactId=signalr-client-sdk -Dversion=1.0 -Dpackaging=jar

### Gerüstbau für das Storm-Topologie-Projekt

Sie haben das Ereignis-Hub-Spout und den SignalR-Client im lokalen Repository installiert und können nun Maven verwenden, um das Gerüst für das Storm-Topologie-Projekt zu erstellen.

1. Öffnen Sie eine Eingabeaufforderung, Bash- oder Terminal-Sitzung, je nachdem, was Sie zum Ausführen von Befehlen in Ihrem System verwenden.

2. Wechseln Sie in das Verzeichnis, in dem Sie dieses Projekt erstellen möchten. Zum Beispiel in das Verzeichnis, in dem Sie all Ihre Code-Projekte ablegen.

3. Führen Sie den folgenden Maven-Befehl aus, um das Grundgerüst für Ihre Anwendung zu erstellen.

		mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.examples -DartifactId=TemperatureMonitor -DinteractiveMode=false

	Dieser Befehl führt die folgenden Aktionen aus:

	* Erstellen eines Verzeichnisses mit der angegebenen  *artifactId*. In diesem Fall **Temperature**.
	* Erstellen einer Datei **pom.xml** mit Maven-Informationen für dieses Projekt.
	* Erstellen einer **src**-Verzeichnisstruktur mit Basiscode und Tests.

### Hinzufügen von Abhängigkeiten und Plug-Ins

Fügen Sie anschließend der Datei **pom.xml** Verweise auf die Abhängigkeiten für das Projekt und auf die für Erstellung und Paketerstellung verwendeten Maven-Plug-Ins hinzu.

1. Öffnen Sie die Datei **pom.xml** in einem Texteditor, und fügen Sie dem Abschnitt **&lt;dependencies>** Folgendes hinzu. Sie können diesen Code am Ende des Abschnitts einfügen, nach der Abhängigkeit für junit.

		<dependency>
	      <groupId>org.apache.storm</groupId>
	      <artifactId>storm-core</artifactId>
	      <version>0.9.2-incubating</version>
	      <!-- keep storm out of the jar-with-dependencies -->
	      <scope>provided</scope>
	    </dependency>
	    <dependency>
	      <groupId>microsoft.aspnet.signalr</groupId>
	      <artifactId>signalr-client-sdk</artifactId>
	      <version>1.0</version>
	    </dependency>
	    <dependency>
	      <groupId>com.microsoft.eventhubs</groupId>
	      <artifactId>eventhubs-storm-spout</artifactId>
	      <version>0.9</version>
	    </dependency>
	    <dependency>
	      <groupId>com.google.code.gson</groupId>
	      <artifactId>gson</artifactId>
	      <version>2.2.2</version>
	    </dependency>
		<dependency>
      	  <groupId>com.github.ptgoetz</groupId>
      	  <artifactId>storm-hbase</artifactId>
      	  <version>0.1.2</version>
    	</dependency>
	    <dependency>
	      <groupId>com.netflix.curator</groupId>
	      <artifactId>curator-framework</artifactId>
	      <version>1.3.3</version>
	      <exclusions>
	        <exclusion>
	          <groupId>log4j</groupId>
	            <artifactId>log4j</artifactId>
	          </exclusion>
	        <exclusion>
	          <groupId>org.slf4j</groupId>
	            <artifactId>slf4j-log4j12</artifactId>
	        </exclusion>
	      </exclusions>
	      <scope>provided</scope>
	    </dependency>

	Dieser Code verweist auf die folgenden Abhängigkeiten:

	* eventhubs-storm-spout - das Ereignis-Hub-Spout
	* signalr-client-sdk - der SignalR-Client
	* gson - Dies ist eine Abhängigkeit des SignalR-Clients und wird außerdem beim Schreiben nach SignalR zur Erstellung von JSON verwendet
	* storm-core - enthält Basisfunktionen für Storm-Anwendungen
	* slf4j - enthält Protokollierungsfunktionen und wird von eventhubs-storm-spout verwendet
	* curator-framework - wird von eventhubs-storm-spout verwendet
	* storm-core - Basisklassen für Storm
	* storm-hbase - Klassen zum Schreiben nach HBase

	> [AZURE.NOTE] Manche dieser Abhängigkeiten sind mit dem Umfang **enthalten** markiert. Diese Abhängigkeiten sollten aus dem Maven-Repository heruntergeladen und für lokale Erstellung und Tests der Anwendung verwendet werden. Sie sind jedoch auch in Ihrer Laufzeitumgebung verfügbar und müssen daher nicht in die von diesem Projekt erstellte JAR-Datei kompiliert und integriert werden.

2. Fügen Sie am Ende der Datei **pom.xml** direkt vor dem Eintrag **&lt;/project>** Folgendes ein.

		  <build>
		    <plugins>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-compiler-plugin</artifactId>
		        <version>2.3.2</version>
		        <configuration>
		          <source>1.7</source>
		          <target>1.7</target>
		        </configuration>
		      </plugin>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-shade-plugin</artifactId>
		        <version>2.3</version>
		        <configuration>
		          <transformers>
		            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		            </transformer>
		          </transformers>
		        </configuration>
		        <executions>
		          <execution>
		            <phase>package</phase>
		            <goals>
		              <goal>shade</goal>
		            </goals>
		          </execution>
		        </executions>
		      </plugin>
		      <plugin>
		        <groupId>org.codehaus.mojo</groupId>
		        <artifactId>exec-maven-plugin</artifactId>
		        <version>1.2.1</version>
		        <executions>
		          <execution>
		          <goals>
		            <goal>exec</goal>
		          </goals>
		          </execution>
		        </executions>
		        <configuration>
		          <executable>java</executable>
		          <includeProjectDependencies>true</includeProjectDependencies>
		          <includePluginDependencies>false</includePluginDependencies>
		          <classpathScope>compile</classpathScope>
		          <mainClass>${storm.topology}</mainClass>
		        </configuration>
		      </plugin>
		    </plugins>
		    <resources>
		      <resource>
		        <directory>${basedir}/conf</directory>
		        <filtering>false</filtering>
		        <includes>
		          <include>Config.properties</include>
				  <include>hbase-site.xml</include>
		        </includes>
		      </resource>
		    </resources>
		  </build>

	Damit weisen Sie Maven an, bei der Erstellung des Projekts Folgendes auszuführen:

	* Einbeziehen der Ressourcendatei **/conf/Config.properties**. Diese Datei wird später erstellt und enthält Konfigurationsdaten für die Verbindung zum Azure Ereignis-Hub.
	* Einbeziehen der Ressourcendatei **/conf/hbase-site.xml**. Diese Datei wird später erstellt und enthält Informationen für die Verbindung zu HBase
	* Verwenden von **maven-compiler-plugin** zum Kompilieren der Anwendung.
	* Verwenden von **maven-shade-plugin**, um ein Uberjar oder Fatjar zu erstellen, das dieses Projekt sowie alle benötigten Abhängigkeiten enthält.
	* Verwenden von **exec-maven-plugin**, um die Anwendung lokal ohne Hadoop-Cluster ausführen zu können.

### Hinzufügen von Konfigurationsdateien

**eventhubs-storm-spout** liest Konfigurationsdaten aus der Datei **Config.properties**. Diese Datei enthält die Daten für die Verbindung zum Ereignis-Hub. Sie können eine Konfigurationsdatei angeben, wenn Sie die Topologie in einem Cluster starten. Durch die Integration im Projekt erhalten Sie jedoch eine bekannte Standardkonfiguration.

1. Erstellen Sie im Verzeichnis **TemperatureMonitor** ein neues Verzeichnis namens **conf**.

2. Erstellen Sie im Verzeichnis **conf** zwei neue Dateien:

	* **Config.properties** enthält Einstellungen für das Ereignis-Hub.
	* **hbase-site.xml** enthält Einstellungen für die Verbindung mit HBase.

3. Fügen Sie den folgenden Inhalt in die Datei **Config.properties** ein.

		eventhubspout.username = storm

		eventhubspout.password = <the key of the 'storm' policy>

		eventhubspout.namespace = <the event hub namespace>

		eventhubspout.entitypath = <the event hub name>

		eventhubspout.partitions.count = <the number of partitions for the event hub>

		# if not provided, will use storm's zookeeper settings
		# zookeeper.connectionstring=localhost:2181

		eventhubspout.checkpoint.interval = 10

		eventhub.receiver.credits = 1024

	Ersetzen Sie das **Kennwort** durch den Schlüssel für die **storm**-Richtlinie, die Sie zuvor im Ereignis-Hub erstellt haben.
	
	Ersetzen Sie den **Namespace** durch den Namespace Ihres Ereignis-Hubs.
	
	Ersetzen Sie den **Entitpath** durch den Namen Ihres Ereignis-Hubs.

4. Fügen Sie den folgenden Inhalt in die Datei **hbase-site.xml** ein.

		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!--
		/**
		 * Copyright 2010 The Apache Software Foundation
		 *
		 * Licensed to the Apache Software Foundation (ASF) under one
		 * or more contributor license agreements.  See the NOTICE file
		 * distributed with this work for additional information
		 * regarding copyright ownership.  The ASF licenses this file
		 * to you under the Apache License, Version 2.0 (the
		 * "License"); you may not use this file except in compliance
		 * with the License.  You may obtain a copy of the License at
		 *
		 *     http://www.apache.org/licenses/LICENSE-2.0
		 *
		 * Unless required by applicable law or agreed to in writing, software
		 * distributed under the License is distributed on an "AS IS" BASIS,
		 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		 * See the License for the specific language governing permissions and
		 * limitations under the License.
		 */
		-->
		<configuration>
		  <property>
		    <name>hbase.cluster.distributed</name>
		    <value>true</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.quorum</name>
		    <value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.property.clientPort</name>
		    <value>2181</value>
		  </property>
		</configuration>

5. Ersetzen Sie in der Datei **hbase-site.xml** den **suffix**-Wert für die Zookeeper-Einträge durch das DNS-Suffix, das Sie zuvor für HBase abgerufen haben. Beispiel: **zookeeper0.mycluster.b4.internal.cloudapp.net, zookeeper1.mycluster.b4.internal.cloudapp.net, zookeeper2.mycluster.b4.internal.cloudapp.net**.

6. Speichern Sie die Dateien.

### Hinzufügen von Hilfsklassen

Um die Serialisierung von und nach JSON zu unterstützen, benötigen Sie einige Hilfsklassen, in denen die Objektstruktur definiert wird.

1. Erstellen Sie im Verzeichnis **\temperaturemonitor\src\main\java\com\microsoft\examples** ein neues Verzeichnis mit dem Namen **helpers**.

2. Erstellen Sie im Verzeichnis **helpers** zwei neue Dateien:

	* **EventHubMessage.java** definiert das Format der Ereignis-Hub-Nachrichten.

	* **SignalRMessage.java** definiert das Format der an SignalR gesendeten Nachrichten.

3. Fügen Sie den folgenden Inhalt in die Datei **EventHubMessage.java** ein.

		package com.microsoft.examples;

		public class EventHubMessage {
		  String TimeStamp;
		  int DeviceId;
		  int Temperature;
		}

4. Fügen Sie den folgenden Inhalt in die Datei **SignalRMessage.java** ein.

		package com.microsoft.examples;

		public class SignalRMessage {
		  int device;
		  int temperature;
		}

5. Speichern und schließen Sie die Dateien.

### Hinzufügen von Bolts

Bolts sind in einer Topologie für die Verarbeitung zuständig. Diese Topologie verwendet drei Bolts. Einer dieser Bolts, hbase-bolt, wird bei der Projekterstellung automatisch heruntergeladen.

1. Erstellen Sie im Verzeichnis **\temperaturemonitor\src\main\java\com\microsoft\examples** ein neue Verzeichnis mit dem Namen **bolts**.

2. Erstellen Sie im Verzeichnis **bolts** zwei neue Dateien:

	* **ParserBolt.java** analysiert die eingehende Nachricht vom Ereignis-Hub und parst sie in einzelne Felder und sendet dann zwei Streams.
	* **DashboardBolt.java** protokolliert Informationen über SignalR an das Web-Dashboard.

3. Fügen Sie den folgenden Inhalt in die Datei **ParserBolt.java** ein.

		package com.microsoft.examples;

		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.tuple.Fields;
		import backtype.storm.tuple.Values;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		public class ParserBolt extends BaseBasicBolt {

		  //Declare output fields & streams
		  //hbasestream is all fields, and goes to hbase
		  //dashstream is just the device and temperature, and goes to the dashboard
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    declarer.declareStream("hbasestream", new Fields("timestamp", "deviceid", "temperature"));
		    declarer.declareStream("dashstream", new Fields("deviceid", "temperature"));
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    //Should only be one tuple, which is the JSON message from the spout
		    String value = tuple.getString(0);

			//Deal with cases where we get multiple
			//EventHub messages in one tuple
			String[] arr = value.split("}");
			for (String ehm : arr)
			{

			    //Convert it from JSON to an object
				EventHubMessage msg = new Gson().fromJson(ehm.concat("}"),EventHubMessage.class);

			    //Pull out the values and emit as a stream
			    String timestamp = msg.TimeStamp;
			    int deviceid = msg.DeviceId;
			    int temperature = msg.Temperature;
			    collector.emit("hbasestream", new Values(timestamp, deviceid, temperature));
			    collector.emit("dashstream", new Values(deviceid, temperature));
			}
		  }
		}

4. Fügen Sie den folgenden Inhalt in die Datei **DashboardBolt.java** ein.

		package com.microsoft.examples;

		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.task.TopologyContext;
		import backtype.storm.Config;
		import backtype.storm.Constants;

		import microsoft.aspnet.signalr.client.Action;
		import microsoft.aspnet.signalr.client.ErrorCallback;
		import microsoft.aspnet.signalr.client.LogLevel;
		import microsoft.aspnet.signalr.client.Logger;
		import microsoft.aspnet.signalr.client.MessageReceivedHandler;
		import microsoft.aspnet.signalr.client.hubs.HubConnection;
		import microsoft.aspnet.signalr.client.hubs.HubProxy;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		import java.util.Map;

		public class DashboardBolt extends BaseBasicBolt {
		  //Connection and proxy for SignalR hub
		  private HubConnection conn;
		  private HubProxy proxy;

		  //Declare output fields
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    //no stream output - we talk directly to SignalR
		  }

		  @Override
		  public void prepare(Map config, TopologyContext context) {

		    // Connect to the DashHub SignalR server
		    conn = new HubConnection("http://dashboard.azurewebsites.net/");
		    // Create the hub proxy
		    proxy = conn.createHubProxy("DashHub");
		    // Subscribe to the error event
		    conn.error(new ErrorCallback() {
		      @Override
		      public void onError(Throwable error) {
		        error.printStackTrace();
		      }
		    });
		    // Subscribe to the connected event
		    conn.connected(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("CONNECTED");
		      }
		    });
		    // Subscribe to the closed event
		    conn.closed(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("DISCONNECTED");
		      }
		    });
		    // Start the connection
		    conn.start()
		      .done(new Action<Void>() {
		        @Override
		        public void run(Void obj) throws Exception {
		          System.out.println("Done Connecting!");
		        }
		    });
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    try {
		      //Get the deviceid and temperature by field name
		      int deviceid = tuple.getIntegerByField("deviceid");
		      int temperature = tuple.getIntegerByField("temperature");
		      //Construct the SignalR message
		      SignalRMessage srMessage = new SignalRMessage();
		      srMessage.device = deviceid;
		      srMessage.temperature = temperature;
		      // send it as JSON
		      proxy.invoke("send", gson.toJson(srMessage));
		    } catch (Exception e) {
		       // LOG.error("Bolt execute error: {}", e);
		       collector.reportError(e);
		    }
		  }
		}

	Ersetzen Sie `http://dashboard.azurewebsites.net/` durch die Adresse Ihrer Azure Website, in der Sie das Dashboard zuvor veröffentlicht haben. Beispiel: http://mydashboard.azurewebsites.net.

5. Speichern und schließen Sie die Dateien.

### Definieren der Topologie

Die Topologie beschreibt den Datenfluss zwischen den Spouts und Bolts in einer Topologie sowie den Grad der Parallelität für die Topologie und die enthaltenen Komponenten.

1. Erstellen Sie im Verzeichnis **\temperaturemonitor\src\main\java\com\microsoft\examples** eine neue Datei mit dem Namen **Temperature.java**.

2. Öffnen Sie die Datei **Temperature.java**, und fügen Sie den folgenden Inhalt ein.

		package com.microsoft.examples;

		import backtype.storm.Config;
		import backtype.storm.LocalCluster;
		import backtype.storm.StormSubmitter;
		import backtype.storm.generated.StormTopology;
		import backtype.storm.topology.TopologyBuilder;
		import backtype.storm.tuple.Fields;
		import com.microsoft.eventhubs.spout.EventHubSpout;
		import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

		import java.io.FileReader;
		import java.util.Properties;

		//hbase
		import org.apache.storm.hbase.bolt.mapper.SimpleHBaseMapper;
		import org.apache.storm.hbase.bolt.HBaseBolt;
		import java.util.Map;
		import java.util.HashMap;
		import backtype.storm.tuple.Fields;

		public class Temperature
		{
		  protected EventHubSpoutConfig spoutConfig;
		  protected int numWorkers;

		  // Reads the configuration information for the Event Hub spout
		  protected void readEHConfig(String[] args) throws Exception {
		    Properties properties = new Properties();
		    if(args.length > 1) {
		      properties.load(new FileReader(args[1]));
		    }
		    else {
		      properties.load(Temperature.class.getClassLoader().getResourceAsStream(
		        "Config.properties"));
		    }

		    String username = properties.getProperty("eventhubspout.username");
		    String password = properties.getProperty("eventhubspout.password");
		    String namespaceName = properties.getProperty("eventhubspout.namespace");
		    String entityPath = properties.getProperty("eventhubspout.entitypath");
		    String zkEndpointAddress = properties.getProperty("zookeeper.connectionstring");
		    int partitionCount = Integer.parseInt(properties.getProperty("eventhubspout.partitions.count"));
		    int checkpointIntervalInSeconds = Integer.parseInt(properties.getProperty("eventhubspout.checkpoint.interval"));
		    int receiverCredits = Integer.parseInt(properties.getProperty("eventhub.receiver.credits"));
		    System.out.println("Eventhub spout config: ");
		    System.out.println("  partition count: " + partitionCount);
		    System.out.println("  checkpoint interval: " + checkpointIntervalInSeconds);
		    System.out.println("  receiver credits: " + receiverCredits);
		    spoutConfig = new EventHubSpoutConfig(username, password,
		      namespaceName, entityPath, partitionCount, zkEndpointAddress,
		      checkpointIntervalInSeconds, receiverCredits);

		    //set the number of workers to be the same as partition number.
		    //the idea is to have a spout and a partial count bolt co-exist in one
		    //worker to avoid shuffling messages across workers in storm cluster.
		    numWorkers = spoutConfig.getPartitionCount();

		    if(args.length > 0) {
		      //set topology name so that sample Trident topology can use it as stream name.
		      spoutConfig.setTopologyName(args[0]);
		    }
		  }

		  // Create the spout using the configuration
		  protected EventHubSpout createEventHubSpout() {
		    EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
		    return eventHubSpout;
		  }

		  // Build the topology
		  protected StormTopology buildTopology(EventHubSpout eventHubSpout, SimpleHBaseMapper mapper) {
		    TopologyBuilder topologyBuilder = new TopologyBuilder();
		    // Name the spout 'EventHubsSpout', and set it to create
		    // as many as we have partition counts in the config file
		    topologyBuilder.setSpout("EventHub", eventHubSpout, spoutConfig.getPartitionCount())
		      .setNumTasks(spoutConfig.getPartitionCount());
		    // Create the parser bolt, which subscribes to the stream from EventHub
		    topologyBuilder.setBolt("Parser", new ParserBolt(), spoutConfig.getPartitionCount())
		      .localOrShuffleGrouping("EventHub").setNumTasks(spoutConfig.getPartitionCount());
		    // Create the dashboard bolt, which subscribes to the stream from Parser
		    topologyBuilder.setBolt("Dashboard", new DashboardBolt(), spoutConfig.getPartitionCount())
		      .fieldsGrouping("Parser", "dashstream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    // Create the HBase bolt, which subscribes to the stream from Parser
		    // WARNING - uncomment the following two lines when deploying
			// leave commented when testing locally
			// topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
		    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    return topologyBuilder.createTopology();
		  }

		  protected void submitTopology(String[] args, StormTopology topology, Config config) throws Exception {
		    // Config config = new Config();
		    config.setDebug(false);

		    //Enable metrics
		    config.registerMetricsConsumer(backtype.storm.metric.LoggingMetricsConsumer.class, 1);

		    // Is this running locally, or on an HDInsight cluster?
		    if (args != null && args.length > 0) {
		      config.setNumWorkers(numWorkers);
		      StormSubmitter.submitTopology(args[0], config, topology);
		    } else {
		      config.setMaxTaskParallelism(2);

		      LocalCluster localCluster = new LocalCluster();
		      localCluster.submitTopology("test", config, topology);

		      Thread.sleep(5000000);

		      localCluster.shutdown();
		    }
		  }

		  // Loads the configuration, creates the spout, builds the topology,
		  // and then submits it
		  protected void runScenario(String[] args) throws Exception{
		    readEHConfig(args);
		    Config config = new Config();

		    //hbase configuration
		    Map<String, Object> hbConf = new HashMap<String, Object>();
		    if(args.length > 0) {
		      hbConf.put("hbase.rootdir", args[0]);
		    }
		    config.put("hbase.conf", hbConf);
		    SimpleHBaseMapper mapper = new SimpleHBaseMapper()
		          .withRowKeyField("deviceid")
		          .withColumnFields(new Fields("timestamp", "temperature"))
		          .withColumnFamily("cf");

		    EventHubSpout eventHubSpout = createEventHubSpout();
		    StormTopology topology = buildTopology(eventHubSpout, mapper);
		    submitTopology(args, topology, config);
		  }

		  public static void main(String[] args) throws Exception {
		    Temperature scenario = new Temperature();
		    scenario.runScenario(args);
		  }
		}

	> [AZURE.NOTE] Beachten Sie, dass die Zeilen für den **HBaseBolt** auskommentiert sind. Dies liegt daran, dass die Topologie im nächsten Schritt lokal ausgeführt wird. Da der HBaseBolt direkt mit HBase kommuniziert, werden Fehler zurückgegeben, wenn dieser Bolt aktiviert ist. Es sei denn, Sie haben ein virtuelles Netzwerk mit DNS-Server konfiguriert und Ihren lokalen Computer ebenfalls zum virtuellen Netzwerk hinzugefügt.

### Lokales Testen der Topologie

Führen Sie die folgenden Schritte aus, um die Datei zu kompilieren und auf Ihrem Entwicklungscomputer zu testen.

1. Starten Sie die .NET-Anwendung **SendEvent**, um mit dem Senden von Ereignissen zu beginnen, sodass Daten aus dem Ereignis-Hub gelesen werden können.

2. Öffnen Sie das Dashboard, das Sie zuvor in einer Azure Website bereitgestellt haben, in einem Webbrowser. Dort sehen Sie die Werte, die durch die Topologie fließen, in einer grafischen Darstellung

3. Führen Sie den folgenden Befehl aus, um die Topologie lokal zu starten

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Dieser Befehl startet die Topologie, liest Dateien aus dem Ereignis-Hub und sendet die Daten an das Dashboard, das in einer Azure Website ausgeführt wird. Im Web-Dashboard sollten nun Linien angezeigt werden.

4. Vergewissern Sie sich, dass dieser Schritt funktioniert, und beenden Sie die Topologie, indem Sie STRG-C drücken. Wählen Sie das Fenster der SendEvent-Anwendung aus und drücken Sie eine beliebige Taste, um die Anwendung zu beenden.

### Aktivieren des HBaseBolt und Vorbereiten von HBase

1. Öffnen Sie die Datei **Temperature.java**, und entfernen Sie den Kommentar (//) in den folgenden Zeilen:

		//topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	//  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	Der HBase-Bolt ist nun aktiviert.

2. Speichern Sie die Datei **Temperature.java**.

3. Verbinden Sie sich per Remotedesktop mit dem HBase-Cluster.

4. Starten Sie die HDInsight-Befehlszeile über den Desktop und geben Sie die folgenden Befehle ein.

		cd %hbase_home%
		bin\hbase shell

5. Geben Sie in der HBase-Befehlszeile den folgenden Befehl ein, um eine Tabelle zu erstellen, in der die Sensordaten gespeichert werden.

		create 'SensorData', 'cf'

6. Geben Sie den folgenden Befehl ein, um sich zu vergewissern, dass die Tabelle keine Daten enthält.

		scan 'SensorData'

Lassen Sie die HBase-Befehlszeile geöffnet.

## Verpacken und Bereitstellen der Topologie in HDInsight

Führen Sie die folgenden Schritte in Ihrer Entwicklungsumgebung aus, um die Temperature-Topologie in Ihrem  Storm-Cluster auszuführen.

1. Führen Sie den folgenden Befehl aus, um ein JAR-Paket aus Ihrem Projekt zu erstellen.

		mvn package

	Daraufhin wird eine Datei mit dem Namen **TemperatureMonitor-1.0-SNAPSHOT.jar** in Ihrem Projektverzeichnis **target** erstellt.

2. Starten Sie die .NET-Anwendung **SendEvents** auf Ihrem lokalen Entwicklungscomputer, um lesbare Ereignisse zu generieren.

3. Verbinden Sie sich per Remotedesktop mit Ihrem Storm-Cluster, und kopieren Sie die Datei **TemperatureMonitor-1.0-SNAPSHOT.jar** in das Verzeichnis **c:\apps\dist\storm&lt;Versionsnummer>**.

4. Klicken Sie auf das Symbol für die **HDInsight-Befehlszeile** auf dem Cluster-Desktop, um eine neue Befehlszeile zu öffnen, und führen Sie die folgenden Befehle aus, um die Topologie auszuführen.

		cd %storm_home%
		bin\storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature Temperature

5. Nachdem die Topologie gestartet wurde, kann es einige Sekunden dauern, bis die Daten im Web-Dashboard angezeigt werden.

6. Warten Sie, bis die Daten im Dashboard angezeigt werden, und wechseln Sie anschließend zur Remotedesktopsitzung für das HBase-Cluster.

7. Geben Sie in der HBase-Befehlszeile den folgenden Befehl ein.

		scan 'SensorData'

	Dieser Befehl gibt nun mehrere Datenzeilen zurück, die von der Storm-Topologie geschrieben wurden.

8. Um die Topologie zu beenden, wechseln Sie zur Remotedesktopsitzung für das Storm-Cluster, und geben Sie den folgenden Befehl in der HDInsight-Befehlszeile ein.

		bin\storm kill Temperature

	Die Topologie wird innerhalb einiger Sekunden beendet.

## Zusammenfassung

Sie haben gelernt, wie Sie Daten mithilfe von Storm aus einem Ereignis-Hub lesen, Daten in HBase speichern und Daten aus Storm mit SignalR und D3.js in einem externen Dashboard anzeigen können.

* Weitere Informationen zu Apache Storm finden Sie unter [https://storm.incubator.apache.org/](https://storm.incubator.apache.org/).

* Weitere Informationen zu HBase mit HDInsight finden Sie unter [Überblick zu HDInsight HBase](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-hbase-overview/).

* Weitere Informationen zu SignalR finden Sie unter [ASP.NET SignalR](http://signalr.net/).

* Weitere Informationen zu D3.js finden Sie unter [D3.js - Data Driven Documents](http://d3js.org/).

* Weitere Informationen zur Erstellung von Topologien in .NET finden Sie unter [Entwickeln von Anwendungen zur Verarbeitung von Datenströmen in C# mit Stream Computing Platform und Storm in HDInsight](/de-de/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/)

[azure-portal]: https://manage.windowsazure.com/

<!--HONumber=42-->
