<properties 
   pageTitle=".NET-Lernprogramm zu Service Bus-Brokermessaging | Microsoft Azure"
   description=".NET-Lernprogramm zu Brokermessaging"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/14/2015"
   ms.author="sethm" />

# .NET-Tutorial zu Service Bus-Brokermessaging

Azure Service Bus bietet zwei umfassende Messaginglösungen. Bei der ersten Lösung wird ein zentralisierter Relaydienst in der Cloud ausgeführt, der verschiedene Transportprotokolle und Webdienststandards unterstützt, z. B. SOAP, WS-* und REST. Der Client benötigt keine direkte Verbindung zum lokalen Dienst und muss auch nicht wissen, wo der Dienst angesiedelt ist, und für den lokalen Dienst sind keine geöffneten eingehenden Ports in der Firewall notwendig.

Bei der zweiten Messaginglösung werden Brokermessagingfunktionen aktiviert. Diese können Sie sich als asynchrone oder entkoppelte Nachrichtenfunktionen vorstellen, die unter Verwendung der Service Bus-Messaginginfrastruktur verschiedene Szenarien wie Veröffentlichung/Abonnements, vorübergehende Entkopplung und Lastenausgleich unterstützen. Entkoppelte Kommunikation hat viele Vorteile, beispielsweise können Clients und Server Verbindungen nach Bedarf herstellen und Vorgänge asynchron ausführen.

In diesem Lernprogramm sollen Sie einen Überblick erhalten und praktische Erfahrung mit Warteschlangen sammeln, einer der Kernkomponenten des Service Bus-Brokermessagings. Nachdem Sie die Themen dieses Lernprogramms durchgearbeitet haben, verfügen Sie über eine Anwendung, mit der eine Liste mit Nachrichten gefüllt wird, eine Warteschlange erstellt wird und Nachrichten an die Warteschlange gesendet werden. Im letzten Schritt werden die Nachrichten von der Anwendung aus der Warteschlange empfangen und angezeigt, und anschließend werden die Ressourcen bereinigt, und die Anwendung wird beendet. Ein entsprechendes Lernprogramm, in dem die Erstellung einer Anwendung beschrieben wird, die die Service Bus-Relaymessagingfunktionen verwendet, finden Sie unter [Lernprogramm zu Service Bus-Relaymessaging](service-bus-relay-tutorial.md).

## Einführung und Voraussetzungen

Warteschlangen liefern die Nachrichten im First In, First Out (FIFO)-Verfahren an einen oder mehrere Consumer. FIFO bedeutet, dass Nachrichten normalerweise in der zeitlichen Reihenfolge von den Consumern empfangen und verarbeitet werden, in der sie in die Warteschlange eingereiht wurden, und jede Nachricht wird nur von einem Nachrichtenconsumer empfangen und verarbeitet. Ein Hauptvorteil von Warteschlangen ist die *zeitliche Entkopplung* von Anwendungskomponenten. Anders ausgedrückt: Die Producer und Consumer müssen Nachrichten nicht gleichzeitig senden und empfangen, da sie dauerhaft in der Warteschlange gespeichert werden. Ein weiterer Vorteil ist der *Belastungsausgleich*, durch den Producer und Consumer die Möglichkeit haben, Nachrichten mit unterschiedlichen Raten zu senden und zu empfangen.

Es folgen einige administrative und vorbereitende Schritte, die Sie ausführen sollten, bevor Sie mit dem Lernprogramm beginnen. Der erste Schritt umfasst die Einrichtung des Dienstnamespace und das Abrufen eines SAS-Schlüssels (Shared Access Signature). Ein Dienstnamespace stellt eine Anwendungsgrenze für jede Anwendung, die über Service Bus zur Verfügung steht. Das System generiert automatisch einen SAS-Schlüssel, wenn ein Dienstnamespace erstellt wird. Dienstnamespace und SAS-Schlüssel bilden gemeinsam die Anmeldeinformationen, mit denen Service Bus gegenüber der Anwendung authentifiziert wird.

### Erstellen eines Dienstnamespace und Abrufen eines SAS-Schlüssels

1. Rufen Sie zum Erstellen eines Dienstnamespace das [klassische Azure-Portal][] auf. Klicken Sie auf der linken Seite auf **Service Bus** und anschließend auf **Erstellen**. Geben Sie einen Namen für den Namespace ein, und klicken Sie auf das Häkchen.

1. Klicken Sie im Hauptfenster des [klassischen Azure-Portals][] auf den Namen des Namespaces, den Sie im vorherigen Schritt erstellt haben.

1. Klicken Sie auf **Konfigurieren**.

1. Notieren Sie sich im Abschnitt **Shared Access Signature-Generator** den Primärschlüssel, der der **RootManagerSharedAccessKey**-Richtlinie zugeordnet ist, oder kopieren Sie ihn in die Zwischenablage. Sie verwenden diesen Wert später im Lernprogramm.

Im nächsten Schritt erstellen Sie ein Visual Studio-Projekt und schreiben zwei Hilfsfunktionen, mit denen eine kommagetrennte Liste von Nachrichten in ein stark typisiertes [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)-.NET-[List](https://msdn.microsoft.com/library/6sh2ey19.aspx)-Objekt geladen wird.

### Erstellen eines Visual Studio-Projekts

1. Öffnen Sie Visual Studio als Administrator: Klicken Sie mit der rechten Maustaste im Startmenü auf das Programm, und klicken Sie dann auf **Als Administrator ausführen**.

1. Erstellen Sie ein neues Konsolenanwendungsprojekt. Klicken Sie auf das Menü **Datei**, wählen Sie **Neu** aus, und klicken Sie auf **Projekt**. Klicken Sie im Dialogfeld **Neues Projekt** auf **Visual C#** (wenn **Visual C#** nicht angezeigt wird, suchen Sie unter **Andere Sprachen**), klicken Sie auf die Vorlage **Konsolenanwendung**, und vergeben Sie den Namen **QueueSample**. Verwenden Sie den standardmäßigen **Speicherort**. Klicken Sie auf **OK**, um das Projekt zu erstellen.

1. Verwenden Sie den NuGet-Paket-Manager, um Ihrem Projekt die Service Bus-Bibliotheken hinzuzufügen:
	1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektordner, und klicken Sie dann auf **NuGet-Pakete verwalten**.
	2. Suchen Sie im Dialogfeld **Nuget-Pakete verwalten** online nach **Service Bus**, und klicken Sie auf **Installieren**. <br />
1. Doppelklicken Sie im Projektmappen-Explorer auf die Datei „Program.cs“, um sie im Visual Studio-Editor zu öffnen. Ändern Sie den Namespacenamen vom Standardnamen `QueueSample` in `Microsoft.ServiceBus.Samples`.

	```
	Microsoft.ServiceBus.Samples
	{
	    …
	```

1. Ändern Sie die `using`-Anweisungen wie im folgenden Code gezeigt.

	```
	using System;
	using System.Collections.Generic;
	using System.Data;
	using System.IO;
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	```

1. Erstellen Sie eine Textdatei mit dem Namen „Data.csv“, und kopieren Sie den folgenden kommgetrennten Text in die Datei.

	```
	IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
	1,Package lost,1,1,Basic,5,1,FALSE
	2,Package damaged,1,1,Basic,5,1,FALSE
	3,Product defective,1,2,Premium,5,2,FALSE
	4,Product damaged,2,2,Premium,5,2,FALSE
	5,Package lost,2,2,Basic,5,2,TRUE
	6,Package lost,3,2,Basic,5,2,FALSE
	7,Package damaged,3,7,Premium,5,3,FALSE
	8,Product defective,3,2,Premium,5,3,FALSE
	9,Product damaged,4,6,Premium,5,3,TRUE
	10,Package lost,4,8,Basic,5,3,FALSE
	11,Package damaged,5,4,Basic,5,4,FALSE
	12,Product defective,5,4,Basic,5,4,FALSE
	13,Package lost,6,8,Basic,5,4,FALSE
	14,Package damaged,6,7,Premium,5,5,FALSE
	15,Product defective,6,2,Premium,5,5,FALSE
	```

	Speichern und schließen Sie die Datei „Data.csv“, und merken Sie sich den Speicherort, an dem Sie sie abgelegt haben.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Namen Ihres Projekts (hier **QueueSample**), klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **Vorhandenes Element**.

1. Navigieren Sie zur Datei „Data.csv“, die Sie in Schritt 6 erstellt haben. Klicken Sie auf die Datei und dann auf **Hinzufügen**. Stellen Sie sicher, dass in der Liste mit den Dateitypen **Alle Dateien (*.*)** ausgewählt ist.

### Erstellen einer Funktion zum Analysieren einer Liste mit Nachrichten

1. Deklarieren Sie vor der `Main()`-Methode zwei Variablen: Eine mit dem Typ **DataTable**, die die Liste mit den Nachrichten in „Data.csv“ enthält. Die andere sollte den Typ „List-Objekt“ haben und für [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) stark typisiert sein. Letztere ist die Liste mit den Brokernachrichten, die in den nachfolgenden Schritten des Lernprogramms verwendet werden.

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    public class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList;
	```

1. Definieren Sie außerhalb von `Main()` eine `ParseCSV()`-Methode, mit der die Liste mit den Nachrichten in der Datei „Data.csv“ analysiert wird und die Nachrichten wie hier gezeigt in eine [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx)-Tabelle geladen werden. Die Methode gibt ein **DataTable**-Objekt zurück.

	```
	static DataTable ParseCSVFile()
	{
	    DataTable tableIssues = new DataTable("Issues");
	    string path = @"..\..\data.csv";
	    try
	    {
	        using (StreamReader readFile = new StreamReader(path))
	        {
	            string line;
	            string[] row;
	
	            // create the columns
	            line = readFile.ReadLine();
	            foreach (string columnTitle in line.Split(','))
	            {
	                tableIssues.Columns.Add(columnTitle);
	            }
	
	            while ((line = readFile.ReadLine()) != null)
	            {
	                row = line.Split(',');
	                tableIssues.Rows.Add(row);
	            }
	        }
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error:" + e.ToString());
	    }
	
	    return tableIssues;
	}
	```

1. Fügen Sie in der `Main()`-Methode eine Anweisung hinzu, mit der die `ParseCSVFile()`-Methode aufgerufen wird:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	
	}
	```

### Erstellen einer Funktion zum Laden der Liste mit den Nachrichten

1. Definieren Sie außerhalb von `Main()` eine `GenerateMessages()`-Methode, die das von `ParseCSVFile()` zurückgegebene **DataTable**-Objekt verwendet und die Tabelle in eine stark typisierte Liste mit Brokernachrichten lädt. Die Methode gibt dann wie im folgenden Beispiel das **List**-Objekt zurück. 

	```
	static List<BrokeredMessage> GenerateMessages(DataTable issues)
	{
	    // Instantiate the brokered list object
	    List<BrokeredMessage> result = new List<BrokeredMessage>();
	
	    // Iterate through the table and create a brokered message for each row
	    foreach (DataRow item in issues.Rows)
	    {
	        BrokeredMessage message = new BrokeredMessage();
	        foreach (DataColumn property in issues.Columns)
	        {
	            message.Properties.Add(property.ColumnName, item[property]);
	        }
	        result.Add(message);
	    }
	    return result;
	}
	```

1. Fügen Sie in `Main()` direkt unterhalb des Aufrufs von `ParseCSVFile()` eine Anweisung hinzu, mit der die `GenerateMessages()`-Methode mit dem Rückgabewert von `ParseCSVFile()` als Argument aufgerufen wird:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	}
	```

### Abrufen von Benutzeranmeldeinformationen

1. Erstellen Sie zuerst drei globale Zeichenfolgenvariablen für diese Werte. Deklarieren Sie diese Variablen direkt nach den vorherigen Variablendeklarationen, z. B.:

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    public class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList; 

	        // Add these variables
			private static string ServiceNamespace;
	        private static string sasKeyName = "RootManageSharedAccessKey";
	        private static string sasKeyValue;
	        …
	```

1. Erstellen Sie als Nächstes eine Funktion, die den Dienstnamespace und SAS-Schlüssel akzeptiert und speichert. Fügen Sie diese Methode außerhalb von `Main()` hinzu. Beispiel:

	```
	static void CollectUserInput()
	{
	    // User service namespace
	    Console.Write("Please enter the namespace to use: ");
	    ServiceNamespace = Console.ReadLine();
	
	    // Issuer key
	    Console.Write("Enter the SAS key to use: ");
	    sasKeyValue = Console.ReadLine();
	}
	```

1. Fügen Sie in `Main()` direkt unterhalb des Aufrufs von `GenerateMessages()` eine Anweisung hinzu, mit der die `CollectUserInput()`-Methode aufgerufen wird:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	    
	    // Collect user input
	    CollectUserInput();
	}
	```

### Erstellen Sie die Lösung.

In Visual Studio können Sie im Menü **Erstellen** auf **Projektmappe erstellen** klicken, oder drücken Sie F6, um die Genauigkeit der bisherigen Arbeit zu bestätigen.

## Erstellen von Verwaltungsanmeldeinformationen

In diesem Schritt definieren Sie die Verwaltungsvorgänge, die Sie zum Erstellen von Shared Access Signature (SAS)-Anmeldeinformationen zur Autorisierung Ihrer Anwendung verwenden.

1. Aus Gründen der Übersichtlichkeit sind in diesem Lernprogramm alle Warteschlangenvorgänge in einer separaten Methode angeordnet. Erstellen Sie in der `Program`-Klasse unterhalb der `Main()`-Methode die `Queue()`-Methode. Beispiel:
 
	```
	public static void Main(string[] args)
	{
	…
	}
	static void Queue()
	{
	}
	```

1. Der nächste Schritt ist die Erstellung von SAS-Anmeldeinformationen mit einem [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx)-Objekt. Bei der Erstellungsmethode werden der SAS-Schlüsselname und der Wert verwendet, die über die `CollectUserInput()`-Methode abgerufen wurden. Fügen Sie den folgenden Code in die Methode `Queue()` ein:

	```
	static void Queue()
	{
	    // Create management credentials
	    TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
	}
	```
### Erstellen des Namespace-Managers

1. Erstellen Sie ein neues Namespace-Verwaltungsobjekt mit einem URI, der den Namespacenamen und die Verwaltungsanmeldeinformationen aus dem vorherigen Schritt als Argumente enthält. Fügen Sie diesen Code direkt unterhalb des Codes hinzu, den Sie im vorherigen Schritt hinzugefügt haben:
	
	```
	NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", <namespaceName>, string.Empty), credentials);
	```

### Beispiel

Ihr Code sollte jetzt in etwa wie folgt aussehen:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

Im nächsten Schritt erstellen Sie die Warteschlange, an die Nachrichten gesendet werden.

## Senden von Nachrichten an die Warteschlange

In diesem Schritt erstellen Sie eine Warteschlange und senden dann die Nachrichten, die in der Liste mit den Brokernachrichten enthalten sind, an diese Warteschlange.

### Erstellen der Warteschlange und Senden von Nachrichten an die Warteschlange

1. Erstellen Sie zuerst die Warteschlange. Geben Sie ihr beispielsweise den Namen `myQueue`, und deklarieren Sie sie direkt nach den Verwaltungsvorgängen, die Sie im letzten Schritt hinzugefügt haben:

	```
	QueueDescription myQueue;
	myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
	```

1. Erstellen Sie in der `Queue()`-Methode ein Messagingfactoryobjekt mit einem neu erstellten Service Bus-URI als Argument. Fügen Sie den folgenden Code direkt nach den Verwaltungsvorgängen hinzu, die Sie im letzten Schritt hinzugefügt haben:

	```
	MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
	```

1. Erstellen Sie als Nächstes das Warteschlangenobjekt mithilfe der [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)-Klasse. Fügen Sie den folgenden Code direkt nach dem Code hinzu, den Sie im letzten Schritt hinzugefügt haben:

	```
	QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
	```

1. Fügen Sie als Nächstes Code hinzu, mit dem eine Schleife durch die Liste mit den bisher erstellten Brokernachrichten durchgeführt wird und diese jeweils an die Warteschlange gesendet werden. Fügen Sie den folgenden Code direkt nach der `CreateQueueClient()`-Anweisung aus dem vorherigen Schritt hinzu:
	
	```
	// Send messages
	Console.WriteLine("Now sending messages to the queue.");
	for (int count = 0; count < 6; count++)
	{
	    var issue = MessageList[count];
	    issue.Label = issue.Properties["IssueTitle"].ToString();
	    myQueueClient.Send(issue);
	    Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
	}
	```

## Empfangen von Nachrichten aus der Warteschlange

In diesem Schritt rufen Sie die Liste mit den Nachrichten aus der Warteschlange ab, die Sie im vorherigen Schritt erstellt haben.

### Erstellen eines Empfängers und Empfangen von Nachrichten aus der Warteschlange

Durchlaufen Sie in der `Queue()`-Methode die Warteschlange, und empfangen Sie die Nachrichten mit der [Microsoft.ServiceBus.Messaging.QueueClient.Receive](https://msdn.microsoft.com/library/azure/hh322678.aspx)-Methode, indem Sie jede Nachricht in der Konsole ausgeben. Fügen Sie den folgenden Code direkt unterhalb des Codes hinzu, den Sie im vorherigen Schritt hinzugefügt haben:

	```
	Console.WriteLine("Now receiving messages from Queue.");
	BrokeredMessage message;
	while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
	    {
	        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
	        message.Complete();
	
	        Console.WriteLine("Processing message (sleeping...)");
	        Thread.Sleep(1000);
	    }
	```

### Beenden der `Queue()`-Methode und Bereinigen der Ressourcen

Fügen Sie direkt nach dem vorherigen Code den folgenden Code hinzu, um die Messagingfactory und Warteschlangenressourcen zu bereinigen:

	```
	factory.Close();
	myQueueClient.Close();
	namespaceClient.DeleteQueue("IssueTrackingQueue");
	```

### Aufrufen der `Queue()`-Methode

Der letzte Schritt ist das Hinzufügen einer Anweisung, mit der die `Queue()`-Methode von `Main()` aufgerufen wird. Fügen Sie am Ende von Main() die folgende hervorgehobene Codezeile hinzu:
	
	```
	public static void Main(string[] args)
	{
	    // Collect user input
	    CollectUserInput();
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	
	    // Add this call
	    Queue();
	}
	```

### Beispiel

Der folgende Code enthält die vollständige **QueueSample**-Anwendung.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);

      QueueDescription myQueue;
      myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");

      MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
      QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

      // Send messages
      Console.WriteLine("Now sending messages to the Queue.");
      for (int count = 0; count < 6; count++)
      {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        myQueueClient.Send(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
      }

      Console.WriteLine("Now receiving messages from Queue.");
      BrokeredMessage message;
      while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
      {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
      }

      factory.Close();
      myQueueClient.Close();
      namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## Erstellen und Ausführen der QueueSample-Anwendung

Nachdem Sie die obigen Schritte abgeschlossen haben, können Sie die **QueueSample**-Anwendung erstellen und ausführen.

### Erstellen der QueueSample-Anwendung

Klicken Sie in Visual Studio im Menü **Erstellen** auf **Projektmappe erstellen**, oder drücken Sie F6. Überprüfen Sie beim Auftreten von Fehlern, ob Ihr Code fehlerfrei ist. Verwenden Sie hierzu das vollständige Beispiel, das am Ende des vorherigen Schritts angegeben ist.

### Ausführen der QueueSample-Anwendung

1. Vor dem Ausführen der Anwendung müssen Sie sicherstellen, dass Sie einen Dienstnamespace erstellt und einen SAS-Schlüssel abgerufen haben. Dies ist unter [Einführung und Voraussetzungen](#introduction-and-prerequisites) beschrieben.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zum [klassischen Azure-Portal][].

1. Klicken Sie im linken Bereich auf **Service Bus**.

1. Klicken Sie auf den Namen des Namespace, den Sie verwenden möchten. Klicken Sie unten auf der Seite auf **Verbindungsinformationen**. Notieren Sie sich die Verbindungszeichenfolge mit dem SAS-Schlüssel, oder kopieren Sie sie in die Zwischenablage.

1. Klicken Sie in Visual Studio im Menü **Debuggen** auf **Debuggen starten**, oder drücken Sie F5. Geben Sie bei Aufforderung den Namen des Dienstnamespace und den Schlüssel aus dem vorherigen Schritt ein.

## Nächste Schritte

In diesem Lernprogramm wurde gezeigt, wie Sie eine Service Bus-Clientanwendung und den dazugehörigen Dienst mit den Service Bus-Brokermessagingfunktionen erstellen. Ein ähnliches Lernprogramm, in dem die [Relaymessagingfunktionen](service-bus-messaging-overview.md#Relayed-messaging) von Service Bus beschrieben werden, finden Sie unter [Lernprogramm zu Service Bus-Relaymessaging](service-bus-relay-tutorial.md).

Weitere Informationen zu [Service Bus](https://azure.microsoft.com/services/service-bus/) finden Sie in den folgenden Themen.

- [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md)
- [Service Bus – Grundlagen](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-Architektur](service-bus-architecture.md)

[klassische Azure-Portal]: http://manage.windowsazure.com
[klassischen Azure-Portal]: http://manage.windowsazure.com
[klassischen Azure-Portals]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0128_2016-->