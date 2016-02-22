<properties 
	pageTitle="Verwenden von AMQP 1.0 mit der .NET-Programmierschnittstelle für Service Bus | Microsoft Azure" 
	description="Erfahren Sie mehr über die Verwendung von AMQP (Advanced Message Queuing Protocol) 1.0 mit der Azure .NET Service Bus-API." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="sethm"/>

# Verwenden von AMQP 1.0 mit der .NET-Programmierschnittstelle für Service Bus

AMQP (Advanced Message Queuing Protocol) 1.0 ist ein effizientes, zuverlässiges Messagingprotokoll auf Wire-Ebene, mit dem Sie robuste und plattformübergreifende Messaginganwendungen erstellen können.

Unterstützung für AMQP 1.0 im Service Bus bedeutet, dass Sie die gebrokerten Messagingfunktionen für Warteschlangen und Veröffentlichen/Abonnieren mithilfe eines effizienten binären Protokolls auf unterschiedlichen Plattformen nutzen können. Zudem können Sie Anwendungen erstellen, deren Komponenten mit einer Mischung aus Sprachen, Frameworks und Betriebssystemen erstellt wurden.

In diesem Artikel wird erklärt, wie Sie die Brokermessagingfunktionen (Warteschlangen und Veröffentlichen/Abonnieren) für Service Bus aus .NET-Anwendungen mithilfe der .NET-API für Service Bus verwenden. In einem [separaten Artikel](service-bus-java-how-to-use-jms-api-amqp.md) wird erklärt, wie Sie dieselbe Aufgabe mithilfe der standardmäßigen JMS-API (Java Message Service) durchführen. Sie können diese beiden Anleitungen verwenden, um weitere Informationen zur plattformübergreifenden Nachrichtenübermittlung unter Verwendung von AMQP 1.0 zu erhalten.

## Erste Schritte mit Service Bus

In diesem Artikel wird davon ausgegangen, dass Sie bereits einen Service Bus-Namespace haben, der eine Warteschlange mit dem Namen "queue1" enthält. Falls nicht, können Sie den Namespace und die Warteschlange im [klassischen Azure-Portal](http://manage.windowsazure.com) erstellen. Weitere Informationen zum Erstellen von Namespaces und Warteschlangen für Service Bus finden Sie unter [Verwenden von Service Bus-Warteschlangen](service-bus-dotnet-how-to-use-queues.md).

## Herunterladen des Servicebus-SDKs

Unterstützung für AMQP 1.0 ist für die Servicebus-SDK-Version 2.1 oder eine höhere Version verfügbar. Sie können das neueste SDK von NuGet unter [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/) herunterladen.

## Programmieren von .NET-Anwendungen

Mithilfe eines dedizierten SOAP-basierten Protokolls kommuniziert die Servicebus .NET-Clientbibliothek standardmäßig mit dem Servicebus-Dienst. Wenn Sie anstatt des Standardprotokolls AMQP 1.0 verwenden möchten, ist eine explizite Konfiguration der Verbindungszeichenfolge für Servicebus erforderlich, die im nächsten Abschnitt beschrieben wird. Mit Ausnahme dieser Änderung bleibt der Anwendungscode bei Verwenden von AMQP 1.0 grundsätzlich unverändert.

In der aktuellen Version gibt es ein paar API-Features, die bei Verwendung von AMQP nicht unterstützt werden. Diese nicht unterstützten Features sind weiter unten im Abschnitt [Nicht unterstützte Features und Einschränkungen](#unsupported-features-and-restrictions) aufgeführt. Darüber hinaus haben einige der erweiterten Konfigurationseinstellungen eine unterschiedliche Bedeutung, wenn AMQP zum Einsatz kommt. Keine dieser Einstellungen wird in diesem Artikel verwendet. Weitere Informationen finden Sie in der [Übersicht zu Service Bus mit AMQP](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

### Konfigurieren über "App.config"

Es ist empfehlenswert, für Anwendungen die Konfigurationsdatei "App.config" zu verwenden, um die Einstellungen zu speichern. Bei Service Bus-Anwendungen können Sie die Datei "App.config" verwenden, um die Service Bus-Verbindungszeichenfolge (**ConnectionString**) zu speichern. Diese Beispielanwendung verwendet auch "App.config", um den Name der Servicebus-Nachrichtenentität zu speichern, den sie verwendet.

Nachfolgend ist eine beispielhafte "App.config"-Datei dargestellt:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  	<appSettings>
	    <add key="Microsoft.ServiceBus.ConnectionString"
       	     value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    	<add key="EntityName" value="queue1" />
	</appSettings>
</configuration>
```

### Konfigurieren der Service Bus-Verbindungszeichenfolge

Beim Wert der Einstellung **Microsoft.ServiceBus.ConnectionString** handelt es sich um die Service Bus-Verbindungszeichenfolge, die zum Konfigurieren der Verbindung mit Service Bus verwendet wird. Das Format sieht folgendermaßen aus:

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

`[namespace]` und `[SAS key]` werden aus dem [klassischen Azure-Portal][] bezogen. Weitere Informationen finden Sie unter [Verwenden von Service Bus-Warteschlangen][].

Wenn AMQP zum Einsatz kommt, wird `;TransportType=Amqp` an die Verbindungszeichenfolge angehängt, sodass die Clientbibliothek angewiesen wird, mithilfe von AMQP 1.0 eine Verbindung mit Service Bus herzustellen.

### Konfigurieren des Entitätsnamens

In dieser Beispielanwendung wird die Einstellung `EntityName` im Abschnitt **appSettings** der Datei "App.config" verwendet, um die Bezeichnung der Warteschlange zu konfigurieren, mit der die Anwendung Nachrichten austauscht.

### Eine einfache .NET-Anwendung, die eine Servicebus-Warteschlange nutzt

Im folgenden Beispiel werden Nachrichten an eine Servicebus-Warteschlange gesendet und Nachrichten von dieser empfangen.

```
// SimpleSenderReceiver.cs
	
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
	
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
	
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
	                
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
	
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
	
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
	
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
	
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
	
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
	
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
	
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
	
        public void RequestStop()
        {
            _shouldStop = true;
        }
	
        private volatile bool _shouldStop;
    }
}
```

### Ausführen der Anwendung

Das Ergebnis nach Ausführen der Anwendung sieht folgendermaßen aus:

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
	
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## Plattformübergreifendes Messaging mit JMS und .NET

In diesem Thema wurde gezeigt, wie Nachrichten mithilfe von .NET an Service Bus gesendet werden. Darüber hinaus wurde auch der Empfang dieser Nachrichten mithilfe von .NET erläutert. Ein wesentlicher Vorteil von AMQP 1.0 besteht darin, dass Anwendungen aus Komponenten erstellt werden können, die in unterschiedlichen Sprachen geschrieben wurden. Weiterhin werden Nachrichten zuverlässig und sicher ausgetauscht.

Mithilfe der zuvor beschriebenen beispielhaften .NET-Anwendung und einer ähnlichen Java-Anwendung aus der separaten Anleitung [Verwenden der JMS (Java Message Service)-API mit Servicebus & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md) können Nachrichten zwischen .NET und Java ausgetauscht werden.

Weitere Informationen zum plattformübergreifenden Messaging mit Service Bus und AMQP 1.0 finden Sie in der [Übersicht über Service Bus mit AMQP 1.0](service-bus-amqp-overview.md).

### Messaging von JMS nach .NET

So funktioniert das Messaging von JMS nach .NET:

* Starten Sie die .NET-Beispielanwendung ohne Befehlszeilenargumente.
* Starten Sie anschließend die beispielhafte Java-Anwendung mit dem Befehlszeilenargument "sendonly". Die Anwendung empfängt in diesem Modus keine Nachrichten aus der Warteschlange, sie sendet lediglich Nachrichten.
* Drücken Sie mehrmals die **Eingabetaste** in der Java-Anwendungskonsole, sodass die Nachrichten gesendet werden.
* Diese Nachrichten werden anschließend von der .NET-Anwendung empfangen.

### Ausgabe der JMS-Anwendung

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### Ausgabe der .NET-Anwendung

```
> SimpleSenderReceiver.exe	
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## Messaging von .NET nach JMS

So funktioniert die Nachrichtenübermittlung von .NET nach JMS:

* Starten Sie die beispielhafte .NET-Anwendung mit dem Befehlszeilenargument "sendonly". Die Anwendung empfängt in diesem Modus keine Nachrichten aus der Warteschlange, sie sendet lediglich Nachrichten.
* Starten Sie anschließend die beispielhafte Java-Anwendung ohne Befehlszeilenargumente.
* Drücken Sie mehrmals die **Eingabetaste** in der .NET-Anwendungskonsole, sodass die Nachrichten gesendet werden.
* Diese Nachrichten werden anschließend von der Java-Anwendung empfangen.

#### Ausgabe der .NET-Anwendung

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### Ausgabe der JMS-Anwendung

```
> java SimpleSenderReceiver	
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## Nicht unterstützte Funktionen und Einschränkungen

Die folgenden Funktionen der .NET-Servicebus-API werden bei der Verwendung von AMQP aktuell nicht unterstützt:

* Transaktionen
* Senden mithilfe eines Übertragungsziels
* Empfangen per Nachrichtensequenznummer
* Durchsuchen von Nachrichten und Sitzungen
* Sitzungsstatus
* Batchbasierte APIs
* Skaliertes Empfangen
* Ändern der Laufzeit für die Abonnementregeln
* Verlängern der Sitzungssperre
* Einige geringfügige Unterschiede bezüglich des Verhaltens

Weitere Informationen finden Sie im in der [Übersicht über Service Bus mit AMQP](service-bus-amqp-dotnet.md). Dieses Thema enthält eine detaillierte Liste mit nicht unterstützten APIs.

## Zusammenfassung

In diesem Thema wurde gezeigt, wie Sie auf die Brokermessagingfunktionen (Warteschlangen und Veröffentlichen/Abonnieren) für Service Bus aus .NET mithilfe von AMQP 1.0 und der .NET-API für Service Bus zugreifen.

Sie können Servicebus AMQP 1.0 auch in anderen Sprachen verwenden, einschließlich Java, C, Python und PHP. Komponenten, die mit diesen Sprachen geschrieben wurden, können mit AMQP 1.0 in Service Bus Nachrichten zuverlässig und bei voller Vertraulichkeit austauschen. Weitere Informationen finden Sie im in der [Übersicht über Service Bus mit AMQP](service-bus-amqp-dotnet.md).

## Nächste Schritte

Nachdem Sie sich einen Überblick über Service Bus und AMQP mit .NET verschafft haben, finden Sie unter den folgenden Links weitere Informationen.

* [AMQP 1.0-Unterstützung in Azure Service Bus](service-bus-amqp-overview.md)
* [Verwenden der JMS-Programmierschnittstelle (Java Message Service) mit Service Bus & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Verwenden von Service Bus-Warteschlangen](service-bus-dotnet-how-to-use-queues.md)
 
[klassischen Azure-Portal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0211_2016-->