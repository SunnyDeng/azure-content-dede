<properties
    pageTitle="Verwenden von Service Bus-Warteschlangen (.NET) - Azure"
    description="Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure. Die Codebeispiele wurden in C# mithilfe der .NET-API geschrieben."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor="mattshel"/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/26/2015"
    ms.author="sethm"/>





# Einsatz von Servicebus-Warteschlangen

<span>In diesem Leitfaden wird beschrieben, wie Sie Service Bus-Warteschlangen verwenden. Die Beispiele sind in C\# geschrieben und verwenden die .NET API. Die behandelten Szenarien umfassen das **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und
**Löschen der Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte]. </span>

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Wenn Sie eine Anwendung erstellen, die den Servicebus verwendet, müssen Sie zu der Servicebus-Assembly eine Referenz hinzufügen und die zugehörigen Namespaces einbinden.

## Hinzufügen des NuGet-Pakets "Service Bus"

Das NuGet-Paket **Service Bus** stellt die einfachste Möglichkeit dar, die Service Bus-API abzurufen und Ihre Anwendung mit allen Service Bus-Abhängigkeiten zu konfigurieren. Die NuGet Visual Studio-Erweiterung ermöglicht eine problemlose Installation und Aktualisierung von Bibliotheken und Tools in Visual Studio und Visual Studio Express. Das NuGet-Paket "Service Bus" stellt die einfachste Möglichkeit zum Abrufen der Service Bus-API und zum Konfigurieren der Anwendung mit allen Service Bus-Abhängigkeiten dar.

Gehen sie folgendermaßen vor, um das NuGet-Paket in der Anwendung zu installieren:

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf
    **NuGet-Pakete verwalten**.
2.  Suchen Sie nach "Service Bus", und wählen Sie das Element **Microsoft Azure Service Bus** aus. Klicken Sie auf **Installieren**, um die Installation abzuschließen, und schließen Sie das Dialogfeld dann.

    ![][7]

Sie können nun Code für Service Bus erstellen.


## Einrichten einer Service Bus-Verbindungszeichenfolge

Service Bus verwendet eine Verbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen. Sie können die Verbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren:

- Bei der Verwendung von Azure Cloud Services empfiehlt es sich, die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (`*.csdef`- und `*.cscfg`-Dateien) zu speichern.
- Bei der Verwendung von Azure-Websites oder Azure Virtual Machines sollten Sie Ihre Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems speichern (z. B.  `web.config`-Datei).

In beiden Fällen können Sie Ihre Verbindungszeichenfolge über die  `CloudConfigurationManager.GetSetting`-Methode abrufen, wie später in dieser Anleitung beschrieben.

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Cloud Services

Die Dienstkonfiguration ist in Azure-Cloud-Dienstprojekten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitzustellen.  Fügen Sie beispielsweise der Dienstdefinitionsdatei (`*.csdef`) eine Einstellung hinzu, wie nachfolgend gezeigt:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Anschließend geben Sie Werte in der Dienstkonfigurationsdatei (`*.cscfg`) an:

    <ServiceConfiguration serviceName="Azure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString"
                         value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und SAS-Schlüsselwerte (Shared Access Signature). Das Verfahren wurde im vorherigen Abschnitt beschrieben.

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Websites oder virtuellen Computern

Bei der Verwendung von Websites oder virtuellen Computern sollten Sie das .NET-Konfigurationssystem verwenden ( `web.config`).  Sie speichern die Verbindungszeichenfolge mit dem `<appSettings>`-Element:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und Schlüsselwerte. Das Verfahren wurde im vorherigen Abschnitt beschrieben.

## Erstellen von Warteschlangen

Sie können Verwaltungsvorgänge für Service Bus-Warteschlangen über die Klasse **NamespaceManager** durchführen. Die Klasse **NamespaceManager** stellt Methoden zum Erstellen, Aufzählen und Löschen von Warteschlangen zur Verfügung.

In diesem Beispiel wird ein **NamespaceManager**-Objekt mit der Azure-Klasse **CloudConfigurationManager** mit einer Verbindungszeichenfolge erstellt, die aus der Basisadresse eines Service Bus-Dienstnamespace und den entsprechenden
SAS-Anmeldeinformationen mit Berechtigungen für dessen Verwaltung besteht. Diese Verbindungszeichenfolge weist die folgende Form auf:

    Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey

Beispiel mit den Konfigurationseinstellungen aus dem vorherigen Abschnitt:

    // Create the queue if it does not exist already
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Es gibt Überladungen der **CreateQueue**-Methode, die es Ihnen ermöglichen, die Eigenschaften der Warteschlange zu optimieren, um beispielsweise den Standardwert für die Lebensdauer festzulegen, der auf an die Warteschlange gesendete Nachrichten angewendet wird. Diese Einstellungen werden mithilfe der Klasse **QueueDescription** angewendet. Im folgenden Beispiel wird dargelegt, wie eine Warteschlange mit der Bezeichnung "TestQueue" mit einer maximalen Größe von 5 GB und einer Standardnachricht "time-to-live" von 1 Minute erstellt wird:

    // Configure Queue Settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Queue with custom settings
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

**Hinweis:** Sie können die **QueueExists**-Methode in **NamespaceManager**-Objekten verwenden, um zu prüfen, ob eine Warteschlange mit einem bestimmten Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

## Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, erstellt Ihre Anwendung ein
**QueueClient**-Objekt mithilfe der Verbindungszeichenfolge.

Der folgende Code zeigt, wie ein **QueueClient**-Objekt für die oben erstellte Warteschlange "TestQueue" mithilfe des API-Aufrufs **CreateFromConnectionString** erstellt wird:

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client =
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

Die Nachrichten, die an die Service Bus-Warteschlangen gesendet (und von diesen empfangen) werden, sind Instanzen der **BrokeredMessage**-Klasse. **BrokeredMessage**-Objekte weisen eine Reihe von Standardeigenschaften (wie **Label** und **TimeToLive**), ein Wörterbuch zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften und einen Bestand beliebiger Anwendungsdaten auf. Eine Anwendung kann den Nachrichtentext festlegen, indem sie ein beliebiges serialisierbares Objekt an den Konstruktor von
**BrokeredMessage** übergibt. Anschließend wird das Objekt mit dem geeigneten **DataContractSerializer** serialisiert. Alternativ kann ein
**System.IO.Stream** bereitgestellt werden.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten senden an
"TestQueue", **QueueClient** den Sie im vorherigen Codeabschnitt erhalten haben:

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;

       // Send message to the queue
       Client.Send(message);
     }

Die Servicebus-Warteschlangen unterstützen Nachrichten mit einer maximalen Größe von 256 KB (die Kopfzeile, welche die Standard- und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB aufweisen). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einer Warteschlange

Die einfachste Methode zum Empfangen von Nachrichten aus einer Warteschlange besteht in der Verwendung eines
**QueueClient**-Objekts. Diese Objekte können in zwei verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des Modus **ReceiveAndDelete** ist der Nachrichtenempfang ein Single-Shot-Vorgang. Wenn Service Bus also eine Leseanforderung für eine Nachricht in einer Warteschlange erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurückgegebenen. **Der ReceiveAndDelete**-Modus ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Im Modus **PeekLock** (dem Standardmodus) ist der Empfangsprozess ein aus zwei Phasen bestehender Vorgang, der die Unterstützung von Anwendungen ermöglicht, die fehlende Nachrichten nicht tolerieren können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von **Complete** für die empfangene Nachricht durch. Wenn Service
Bus den **Complete**-Aufruf erkennt, kennzeichnet der Dienst die Nachricht als verarbeitet und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie Nachrichten mit dem Standardmodus **PeekLock** empfangen und verarbeitet werden können. Zum Angeben eines anderen
**ReceiveMode**-Werts können Sie eine andere Überladung für
**CreateFromConnectionString** verwenden. Dieses Beispiel verwendet den **OnMessage**-Rückruf zum Verarbeiten von Nachrichten, während diese in "TestQueue" eintreffen.

    string connectionString =
      CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    QueueClient Client =
      QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    // Callback to handle received messages
    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from queue
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Test Property: " +
            message.Properties["TestProperty"]);

            // Remove message from queue
            message.Complete();
        }
            catch (Exception)
        {
            // Indicates a problem, unlock message in queue
            message.Abandon();
        }
    }, options);

Das folgende Beispiel konfiguriert den **OnMessage**-Rückruf mithilfe eines **OnMessageOptions**-Objekts. **AutoComplete** ist auf **false** eingestellt, um die manuelle Steuerung für den Aufruf von **Complete** für die empfangene Nachricht zu aktivieren.
**AutoRenewTimeout** ist auf eine Minute eingestellt, wodurch der Client bis zu eine Minute auf eine Nachricht wartet, bis der Aufruf abläuft und der Client einen neuen Aufruf startet, um auf Nachrichten zu prüfen. Dadurch wird die Häufigkeit verringert, mit der der Client fakturierbare Aufrufe startet, die keine Nachrichten abrufen.

## Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **Abandon**-Methode zu der empfangenen Nachricht aufrufen (anstelle der **Complete**-Methode). Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Nachricht ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt Service
Bus die Nachricht automatisch und macht sie verfügbar, damit sie erneut empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der Anforderung **Complete** abstürzt, wird die Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als
**At Least Once Processing** (mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, unter bestimmten Umständen jedoch ggf. auch erneut übermittelt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies erfolgt häufig mit der
**MessageId**-Eigenschaft der Nachricht, die über mehrere Übermittlungsversuche hinweg konstant bleibt.

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.][]
-   Erstellen einer funktionierenden Anwendung, die Nachrichten an
    und aus einer Service Bus-Warteschlange sendet: [.NET-Lernprogramm zu Service Bus-Brokermessaging].

  [Nächste Schritte]: #next-steps
  [Was sind Service Bus-Warteschlangen?]: #what-queues
  [Erstellen eines Dienstnamespaces]: #create-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-creds
  [Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus]: #configure-app
  [Gewusst wie: Einrichten einer Service Bus-Verbindungszeichenfolge]: #set-up-connstring
  [Gewusst wie: Konfigurieren Ihrer Verbindungszeichenfolge]: #config-connstring
  [Gewusst wie: Erstellen einer Warteschlange]: #create-queue
  [Gewusst wie: Senden von Nachrichten an eine Warteschlange]: #send-messages
  [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange]: #receive-messages
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #handle-crashes
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Warteschlangen, Themen und Abonnements.]: http://msdn.microsoft.com/library/hh367516.aspx
  [.NET-Lernprogramm zu Service Bus-Brokermessaging]: http://msdn.microsoft.com/library/hh367512.aspx

<!--HONumber=47-->
