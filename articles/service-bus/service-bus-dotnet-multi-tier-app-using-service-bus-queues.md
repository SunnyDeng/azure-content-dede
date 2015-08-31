<properties
	pageTitle=".NET-Anwendungen mit mehreren Ebenen | Microsoft Azure"
	description="Ein Lernprogramm, das Ihnen hilft, eine Anwendung mit mehreren Ebenen in Azure zu erstellen, die Service Bus-Warteschlangen für die Kommunikation zwischen Ebenen verwendet. Beispiele in .NET."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/02/2015"
	ms.author="sethm"/>

# .NET-Anwendungen mit mehreren Ebenen unter Verwendung von Azure Service Bus-Warteschlangen

## Einführung

Mit Visual Studio 2013 und dem kostenlosen Microsoft Azure SDK für .NET können Sie schnell und einfach für Azure entwickeln. Falls Sie Visual Studio 2013 noch nicht haben, installiert das SDK Visual Studio Express automatisch, sodass Sie kostenlos mit der Entwicklung für Azure beginnen können. In diesem Artikel wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms haben Sie eine Anwendung in Ihrer lokalen Umgebung, die verschiedene Azure-Ressourcen verwendet und demonstriert, wie Anwendungen mit mehreren Ebenen funktionieren.

Sie erhalten Informationen zu folgenden Themen:

-   Vorbereitung Ihres Computers für die Azure-Entwicklung mit einem einzigen Download und einer einzigen Installation
-   Einsatz von Visual Studio für die Azure-Entwicklung
-   Erstellung von Anwendungen mit mehreren Ebenen in Azure mit Web- und Arbeiterrollen
-   Kommunikation zwischen Ebenen mithilfe von Service Bus-Warteschlangen

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

In diesem Lernprogramm werden Sie eine Anwendung mit mehreren Ebenen in einem Azure-Cloud-Dienst erstellen und ausführen. Als Front-End dient eine ASP.NET MVC-Webrolle, und als Backend eine Workerrolle. Dieselbe Anwendung mit mehreren Ebenen kann auch mit einem Webprojekt als Front-End erstellt und auf einer Azure-Website anstelle eines Cloud-Diensts bereitgestellt werden. Hinweise zu den Unterschieden für Front-Ends mit Azure-Websites finden Sie im Abschnitt [Nächste Schritte](#nextsteps).

Der folgende Screenshot zeigt die fertige Anwendung.

![][0]

> [AZURE.NOTE]Azure bietet ebenfalls Funktionen für Speicher-Warteschlangen. Weitere Informationen zu Speicher- und Service Bus-Warteschlangen in Azure finden Sie unter [Windows Azure-Warteschlangen und Windows Azure Service Bus-Warteschlangen – Vergleich und Gegenüberstellung][sbqueuecomparison].

## Szenario-Übersicht: Kommunikation zwischen Rollen

Um eine Bestellung zur Verarbeitung zu übermitteln, muss die Front-End-GUI in ihrer Funktion als Webrolle mit der Logikkomponente in der mittleren Ebene interagieren, die eine Arbeiterrolle erfüllt. Dieses Beispiel verwendet die verwaltete Servicebus-Messaging-Funktion für die Kommunikation zwischen Ebenen.

Durch die verwaltete Messaging-Funktion zwischen Web- und mittlerer Ebene werden die Komponenten voneinander entkoppelt. Im Gegensatz zur direkten Kommunikation (z. B. per TCP oder HTTP) verbindet sich die Webebene nicht direkt mit der mittleren Ebene, sondern schiebt Arbeitseinheiten in Form von Nachrichten zum Servicebus, der diese zuverlässig aufbewahrt, bis die mittlere Ebene diese konsumieren und verarbeiten kann.

Service Bus bietet zwei Entitäten für das Brokermessaging: Warteschlangen und Themen. Mit Warteschlangen wird jede Nachricht von einem einzelnen Empfänger konsumiert. Themen unterstützen das Veröffentlichungs- und Abonnementsmuster, mit dem jede veröffentlichte Nachricht den für das entsprechende Thema registrierten Abonnements zugänglich gemacht wird. Jedes Abonnement pflegt eine eigene Nachrichten-Warteschlange. Abonnements können mit Filterregeln konfiguriert werden. Diese sorgen dafür, dass nur Nachrichten in der Abonnement-Warteschlange landen, welche die Filterregeln erfüllen. Das folgende Beispiel verwendet Service Bus-Warteschlangen.

![][1]

Dieser Kommunikationsmechanismus bietet verschiedene Vorteile gegenüber direkten Nachrichten:

-   **Zeitliche Entkopplung.** Dank des asynchronen Nachrichtenmusters müssen Producer und Consumer nicht gleichzeitig online sein. Der Servicebus speichert die Nachrichten zuverlässig, bis der Consumer diese entgegennehmen kann. Auf diese Weise können die Komponenten verteilter Anwendungen voneinander entkoppelt werden, z. B. zu Wartungszwecken oder bei einem Komponentenausfall, ohne das Gesamtsystem zu beeinträchtigen. Außerdem genügt es unter Umständen, wenn die konsumierende Anwendung nur zu bestimmten Tageszeiten online ist.

-   **Belastungsausgleich.** In vielen Anwendungen schwankt die Systemlast mit der Zeit, während die Bearbeitungszeit pro Arbeitseinheit normalerweise konstant ist. Durch die Einführung einer Warteschlange zwischen Nachrichtenproducer und Consumer muss der Consumer (Arbeiter) anstatt der Spitzenlast nur die durchschnittliche Last verarbeiten können. Die Länge der Warteschlange schwankt ebenso wie die eingehende Last. Dies ermöglicht direkte Einsparungen bei der Infrastruktur, die zur Bearbeitung der Anwendungslast benötigt wird.

-   **Lastenausgleich.** Mit zunehmender Last können zusätzliche Arbeitsprozesse zur Verarbeitung der Warteschlange eingesetzt werden. Jede Nachricht wird nur von einem der Arbeitsprozesse verarbeitet. Außerdem ermöglicht dieser entnahmebasierte Lastenausgleich eine optimale Auslastung der Arbeiter, selbst wenn sich deren Rechenleistung stark unterscheidet, da jeder Arbeiter die Nachrichten mit seinem eigenen Maximaldurchsatz aus der Warteschlange entnimmt. Dieses Schema nennt man auch das "Konkurrierende Consumer"-Schema.

    ![][2]

In den folgenden Abschnitten wird der Code für die Implementierung dieser Architektur behandelt.

## Einrichten der Entwicklungsumgebung

Bevor Sie mit der Entwicklung Ihrer Azure-Anwendung beginnen können, laden Sie die Tools herunter und richten Sie Ihre Entwicklungsumgebung ein:

1.  Klicken Sie auf den folgenden Link, um das Azure SDK für .NET zu installieren.

    [Tools und SDK herunterladen][]

2. 	Klicken Sie den korrekten Link für Ihre Visual Studio-Version an. Für die Schritte in diesem Lernprogramm wird Visual Studio 2013 verwendet.

	![][32]

4. 	Wenn Sie aufgefordert werden, die Installationsdatei auszuführen oder zu speichern, klicken Sie auf **Ausführen**.

    ![][3]

5.  Klicken Sie im Webplattform-Installer auf **Installieren**, und setzen Sie die Installation fort.

    ![][33]

6.  Sobald die Installation abgeschlossen ist, haben Sie alles zur Hand, was Sie benötigen, um mit der Entwicklung der App zu beginnen. Das SDK enthält Tools, mit denen Sie Azure-Anwendungen in Visual Studio entwickeln können. Falls Sie Visual Studio nicht installiert haben, wird das kostenlose Visual Studio Express für das Web installiert.

## Einrichten des Service Bus-Namespace

Der nächste Schritt ist die Einrichtung des Dienstnamespace und das Abrufen eines SAS (Shared Access Signature)-Schlüssels. Ein Dienstnamespace stellt eine Anwendungsgrenze für jede Anwendung, die über Service Bus zur Verfügung steht. Das System generiert einen SAS-Schlüssel, wenn ein Dienstnamespace erstellt wird. Dienstnamespace und SAS-Schlüssel bilden gemeinsam die Anmeldeinformationen, mit denen sich der Servicebus gegenüber der Anwendung authentifiziert.

> [AZURE.NOTE]Namespaces und Entitäten für die Service Bus-Nachrichten können auch im Visual Studio Server-Explorer verwaltet werden, neue Namespaces können dagegen nur im Azure-Portal erstellt werden.

### Einrichten eines Namespace über das Azure-Portal

1.  Melden Sie sich beim [Azure-Portal][] an.

2.  Klicken Sie im linken Navigationsbereich des Azure-Portals auf **Service Bus**.

3.  Klicken Sie im unteren Bereich des Azure-Portals auf **Erstellen**.

    ![][6]

4.  Geben Sie auf der Seite **Neuen Namespace hinzufügen** einen Namen für den Namespace ein. Das System überprüft sofort, ob dieser Name verfügbar ist.

    ![][7]

5.  Wählen Sie nach der Bestätigung, dass der Name für den Namespace verfügbar ist, das Land oder die Region, wo dieser Namespace gehostet werden soll. (Stellen Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der sie Ihre Rechnerressourcen einsetzen.) Stellen Sie außerdem sicher, dass Sie **Messaging** im Namespace **Typ**-Feld und **Standard** im **Messaging-Ebene**-Feld auswählen.

    > [AZURE.IMPORTANT]Wählen Sie **dieselbe Region**, in der Sie auch Ihre Anwendung bereitstellen möchten. Dies sorgt für die beste Leistung.

6.  Aktivieren Sie das Kontrollkästchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Eventuell müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto bereitgestellt werden.

	![][27]

7.  Klicken Sie im Hauptfenster auf den Namen Ihres Dienstnamespaces.

	![][30]

8. Klicken Sie auf **Verbindungsinformationen**.

	![][31]

9.  Im Bereich **Zugriff auf die Verbindungsinformationen** finden Sie die Verbindungszeichenfolge, die den SAS-Schlüssel und den Schlüsselnamen enthält.

    ![][35]

10.  Notieren Sie diese Anmeldeinformationen, oder kopieren Sie sie in die Zwischenablage.

## Erstellen einer Webrolle

In diesem Abschnitt lernen Sie, das Front-End Ihrer Anwendung zu erstellen. Zunächst erstellen Sie die verschiedenen Seiten, aus denen Ihre Anwendung besteht. Anschließend fügen Sie den Code hinzu, mit dem Elemente an die Servicebus-Warteschlange übergeben und der Status der Warteschlange angezeigt wird.

### Erstellen des Projekts

1.  Starten Sie entweder Microsoft Visual Studio 2013 oder Microsoft Visual Studio Express mit Administratorrechten. Um Visual Studio mit Administratorrechten zu starten, klicken Sie mit der rechten Maustaste auf **Microsoft Visual Studio 2013 (oder Microsoft Visual Studio Express)** und klicken Sie anschließend auf **Als Administrator ausführen**. Für den ebenfalls in diesem Artikel behandelten Azure-Serveremulator muss Visual Studio mit Administratorrechten gestartet werden.

    Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.

    ![][8]

2.  Klicken Sie im Menü **Installierte Vorlagen** unter **Visual C\#** auf **Cloud** und anschließend auf **Azure-Clouddienst**. Geben Sie dem Projekt den Namen **MultiTierApp**. Klicken Sie dann auf **OK**.

    ![][9]

3.  Doppelklicken Sie in den **.NET Framework 4.5**-Rollen auf **ASP.NET-Webrolle**.

    ![][10]

4.  Zeigen Sie auf **WebRole1** unter **Azure-Clouddienst-Lösung**, klicken Sie auf das Stiftsymbol und geben Sie der Webrolle den Namen **FrontendWebRole**. Klicken Sie dann auf **OK**. (Achten Sie darauf „Frontend“ mit kleinem „e“ einzugeben, nicht als „FrontEnd“.)

    ![][11]

5.  Klicken Sie im Dialogfeld **Neues ASP.NET-Projekt** in der Liste **Vorlage auswählen** auf **MVC** und anschließend auf **OK**.

    ![][12]

6.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise** und anschließend auf **NuGet-Pakete verwalten** oder auf **Bibliothekspaketverweis hinzufügen**.

7.  Wählen Sie im linken Bereich des Dialogfelds **Online** aus. Suchen Sie nach "**Service Bus**", und wählen Sie das Element **Microsoft Azure Service Bus** aus. Schließen Sie die Installation ab, und schließen Sie das Dialogfeld.

    ![][13]

8.  Beachten Sie, dass die benötigten Clientassemblys nun referenziert sind und einige neue Codedateien hinzugefügt wurden.

9.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Modelle**, und klicken Sie anschließend auf **Hinzufügen** und dann auf **Klasse**. Geben Sie in das Feld **Name** den Namen **OnlineOrder.cs** ein. Klicken Sie anschließend auf **Hinzufügen**.

### Schreiben des Codes für Ihre Webrolle

In diesem Abschnitt erstellen Sie die verschiedenen Seiten, aus denen Ihre Anwendung besteht.

1.  Ersetzen Sie in der Datei "OnlineOrder.cs" in Visual Studio die bestehende Namespacedefinition durch den folgenden Code:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  Doppelklicken Sie im **Projektmappen-Explorer** auf **Controllers\\HomeController.cs**. Fügen Sie die folgenden **using**-Anweisungen am Anfang Ihrer Datei hinzu, um die Namespaces für Ihr neu erstelltes Modell sowie Service Bus einzuschließen.

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Ersetzen Sie in der Datei "HomeController.cs" in Visual Studio die bestehende Namespacedefinition ebenfalls durch den folgenden Code. Dieser Code enthält die Methoden für die Übermittlung von Elementen in die Warteschlange.

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application.
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit.
                // Controller method for a view you will create for the submission
                // form.
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit.
                // Controller method for handling submissions from the submission
                // form.
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and
				// cross-site request forgery.  
    			[ValidateAntiForgeryToken]
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.

                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  Klicken Sie im Menü **Build** auf **Projektmappe erstellen**, um die Richtigkeit Ihrer bisherigen Arbeit zu überprüfen.

5.  Anschließend erstellen Sie die Ansicht für die zuvor erstellte **Submit()**-Methode. Klicken Sie mit der rechten Maustaste innerhalb der **Submit()**-Methode, und wählen Sie dann **Ansicht** hinzufügen aus.

    ![][14]

6.  Ein Dialogfeld zum Erstellen der Ansicht wird geöffnet. Wählen Sie in der Liste **Vorlage** die Option **Erstellen** aus. Klicken Sie in der Liste **Modellklasse** auf die Klasse **OnlineOrder**.

    ![][15]

7.  Klicken Sie auf **Hinzufügen**.

8.  Ändern Sie nun den angezeigten Namen Ihrer Anwendung. Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei **Views\\Shared\\\_Layout.cshtml**, um diese im Visual Studio-Editor zu öffnen.

9.  Ersetzen Sie alle Vorkommnisse von **My ASP.NET Application** mit **LITWARE's Products**.

10. Entfernen Sie die Links **Home**, **About** und **Contact**. Löschen Sie den hervorgehobenen Code:

	![][28]

11. Erweitern Sie anschließend die Übermittlungsseite um einige Informationen zur Warteschlange. Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei **Views\\Home\\Submit.cshtml**, um diese im Visual Studio-Editor zu öffnen. Fügen Sie die folgende Zeile nach **&lt;h2>Submit&lt;/h2>** hinzu. **ViewBag.MessageCount** ist momentan leer. Sie werden diesen Bereich später ausfüllen.

        <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>

12. Sie haben nun Ihre GUI implementiert. Drücken Sie **F5**, um Ihre Anwendung auszuführen und zu prüfen, ob diese korrekt angezeigt wird.

    ![][17]

### Schreiben des Codes für die Übermittlung von Elementen an die Service Bus-Warteschlange

Sie werden nun den Code für die Übermittlung von Elementen in die Warteschlange hinzufügen. Erstellen Sie zunächst die Klasse mit den Verbindungsinformationen für die Servicebus-Warteschlange. Anschließend initialisieren Sie Ihre Verbindung in "Global.aspx.cs". Zuletzt aktualisieren Sie den zuvor in "HomeController.cs" erstellten Übermittlungscode, um die Elemente an die Service Bus-Warteschlange zu übermitteln.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **FrontendWebRole** (klicken Sie auf das Projekt, nicht auf die Rolle). Klicken Sie auf **Hinzufügen** und anschließend auf **Klasse**.

2.  Geben Sie der Klasse den Namen "QueueConnector.cs". Klicken Sie auf **Hinzufügen**, um die Klasse zu erstellen.

3.  Sie werden nun den Code hinzufügen, der Ihre Verbindungsinformationen kapselt und die Verbindung zur Servicebus-Warteschlange initialisiert. Fügen Sie in "QueueConnector.cs" den folgenden Code hinzu, und geben Sie Werte für **Namespace** (Ihren Dienstnamespace) und **yourKey** ein. Hierbei handelt es sich um den SAS-Schlüssel, den Sie weiter oben über das [Azure-Portal][Azure portal] erhalten haben.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Azure portal.
                public const string Namespace = "your service bus namespace";

                // The name of your queue.
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations.
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey");
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls.
                    ServiceBusEnvironment.SystemConnectivity.Mode =
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to
                    // management operations.
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already.
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue.
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address,
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

    Später in diesem Lernprogramm erfahren Sie, wie Sie den Namen Ihres **Namespace** und Ihren SAS-Schlüsselwert in einer Konfigurationsdatei speichern können.

4.  Nun stellen Sie sicher, dass Ihre **Initialize**-Methode aufgerufen wird. Doppelklicken Sie im **Projektmappen-Explorer** auf **Global.asax\\Global.asax.cs**.

5.  Fügen Sie die folgende Zeile am Ende der Methode **Application\_Start** hinzu.

        FrontendWebRole.QueueConnector.Initialize();

6.  Zuletzt aktualisieren Sie den zuvor erstellten Webcode, um die Elemente an die Warteschlange zu übermitteln. Doppelklicken Sie im **Projektmappen-Explorer** auf **Controllers\\HomeController.cs**.

7.  Aktualisieren Sie die **Submit()**-Methode wie folgt, um die Anzahl der Nachrichten in der Warteschlange abzurufen.

        public ActionResult Submit()
        {
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Aktualisieren Sie die **Submit(OnlineOrder order)**-Methode wie folgt, um Bestellinformationen an die Warteschlange zu übermitteln.

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order.
                var message = new BrokeredMessage(order);

                // Submit the order.
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  Führen Sie die Anwendung nun erneut aus. Bei jeder Übermittlung einer Bestellung steigt der Nachrichtenzähler an.

    ![][18]

## Cloud-Konfigurations-Manager

Die **GetSettings** -Methode in der **Microsoft.WindowsAzure.Configuration.CloudConfigurationManager** -Klasse ermöglicht es Ihnen, Konfigurationseinstellungen aus dem Konfigurationsspeicher für Ihre Plattform zu lesen. Wenn Ihr Code z. B. in einer Web- oder Workerrolle ausgeführt wird, liest die **GetSettings**-Methode die Datei "ServiceConfiguration.cscfg", und wenn Ihr Code in einer Standardkonsolenanwendung ausgeführt wird, liest die **GetSettings**-Methode die Datei "App.config".

Wenn Sie eine Verbindungszeichenfolge für Ihren Servicebus-Namespace in einer Konfigurationsdatei speichern, können Sie die **GetSettings**-Methode verwenden, um eine Verbindungszeichenfolge zu lesen, die Sie zum Instanziiern eines **NamespaceMananger**-Objekts verwenden können. Sie können eine **NamespaceMananger**-Instanz verwenden, um den Service Bus-Namespace programmgesteuert zu konfigurieren. Sie können die gleiche Verbindungszeichenfolge verwenden, um ein Clientobjekt zu instanziieren (z. B. **QueueClient**, **TopicClient**, und **EventHubClient**-Objekt), das Sie nutzen können, um Vorgänge wie das Senden und Empfangen von Nachrichten während des laufenden Betriebs durchzuführen.

### Verbindungszeichenfolge

Sie können die Konfigurationsdaten als Verbindungszeichenfolge angeben, um einen Client zu instanziieren (z. B. einen Service Bus-**QueueClient**). Auf der Clientseite existiert die `CreateFromConnectionString()` Methode, die diesen Clienttyp anhand der Verbindungszeichenfolge instanziiert. Als Beispiel dient der folgende Konfigurationsabschnitt.

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=RootManageSharedAccessKey;SharedSecretValue=[yourKey]" />
	</ConfigurationSettings>

Der folgende Code ruft die Verbindungszeichenfolge ab, erstellt eine Warteschlange und initialisiert die Verbindung zur Warteschlange.

	QueueClient Client;

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString);

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus queue.
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

Der Code im folgenden Abschnitt verwendet die **CloudConfigurationManager**-Klasse.

## Erstellen einer Workerrolle

Sie werden nun die Workerrolle zur Verarbeitung der übermittelten Nachrichten erstellen. Dieses Beispiel verwendet die Visual Studio-Projektvorlage **Workerrolle mit Service Bus-Warteschlange**. Verwenden Sie zunächst den Server-Explorer in Visual Studio, um die benötigten Anmeldeinformationen zu erhalten.

1. Stellen Sie sicher, dass Sie Visual Studio mit Ihrem Azure-Konto verbunden haben.

2.  Klicken Sie in Visual Studio im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Rollen** im Projekt **MultiTierApp**.

3.  Klicken Sie auf **Hinzufügen** und anschließend auf **Neues Workerrollenprojekt**. Das Dialogfeld **Neues Rollenprojekt hinzufügen** wird geöffnet.

	![][26]

4.  Klicken Sie im Dialogfeld **Neues Rollenprojekt hinzufügen** auf **Workerrolle mit Service Bus-Warteschlange**.

	![][23]

5.  Geben Sie in das Feld **Name** den Namen **OrderProcessingRole** für das Projekt ein. Klicken Sie anschließend auf **Hinzufügen**.

6.  Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den Namen Ihres Dienstnamespace, und klicken Sie dann auf **Eigenschaften**. Der erste Eintrag im Bereich **Eigenschaften** in Visual Studio enthält eine Verbindungszeichenfolge, die mit dem Namespace-Endpunkt und den benötigten Anmeldeinformationen ausgefüllt ist. Sehen Sie sich als Beispiel den folgenden Screenshot an. Doppelklicken Sie auf **Verbindungszeichenfolge** und drücken Sie **Strg+C**, um die Zeichenfolge in die Zwischenablage zu kopieren.

	![][24]

7.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die in Schritt 5 erstellte **OrderProcessingRole** (klicken Sie auf **OrderProcessingRole** unter **Rollen**, nicht auf die Klasse). Klicken Sie anschließend auf **Eigenschaften**.

8.  Klicken Sie auf der Registerkarte **Einstellungen** im Dialogfeld **Eigenschaften** in das Textfeld **Wert** für **Microsoft.ServiceBus.ConnectionString**, und fügen Sie den in Schritt 6 kopierten Endpunktwert ein.

	![][25]

9.  Erstellen Sie die **OnlineOrder**-Klasse, um die Nachrichten abzubilden, während diese aus der Warteschlange verarbeitet werden. Sie können dabei eine zuvor erstellte Klasse wiederverwenden. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **OrderProcessingRole** (klicken Sie auf das Projekt, nicht auf die Rolle). Klicken Sie auf **Hinzufügen** und anschließend auf **Vorhandenes Element**.

10. Durchsuchen Sie den Unterordner nach **FrontendWebRole\\Models**, und doppelklicken Sie dann auf **OnlineOrder.cs**, um die Klasse zum Projekt hinzuzufügen.

11. Ändern Sie in **WorkerRole.cs** den Wert der Variablen **QueueName** von `"ProcessingQueue"` in `"OrdersQueue"`, wie es im folgenden Code gezeigt ist.

		// The name of your queue.
		const string QueueName = "OrdersQueue";

12. Fügen Sie die folgende using-Anweisung am Anfang der Datei "WorkerRole.cs" hinzu.

		using FrontendWebRole.Models;

13. Fügen Sie in der `Run()`-Funktion im `OnMessage`-Aufruf den folgenden Code in der `try`-Klausel hinzu.

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder.
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

14. Ihre Anwendung ist nun fertig. Sie können die vollständige Anwendung testen, indem Sie das MultiTierApp-Projekt im Projektmappen-Explorer mit der rechten Maustaste anklicken und dann **Set as Startup Project** auswählen und F5 drücken. Beachten Sie, dass die Nachrichtenzahl nicht ansteigt, da die Workerrolle die Elemente in der Warteschlange verarbeitet und als abgeschlossen markiert. Sie können die Ausgabe Ihrer Workerrolle in der Azure-Serveremulator-GUI anzeigen. Klicken Sie dazu mit der rechten Maustaste in den Infobereich Ihrer Taskleiste und wählen Sie **Serveremulator-GUI anzeigen**.

    ![][19]

    ![][20]

## Nächste Schritte  

Weitere Informationen zum Service Bus finden Sie in den folgenden Ressourcen:

* [Azure-Servicebus][sbmsdn]  
* [Servicebus-Dienstseite][sbwacom]  
* [Verwenden von Servicebus-Warteschlangen][sbwacomqhowto]  

Weitere Informationen über Szenarien mit mehreren Ebenen und die Bereitstellung von Anwendungen in Cloud-Diensten finden Sie unter:

* [.NET-Anwendungen mit mehreren Ebenen mithilfe von Speichertabellen, Warteschlangen und Blobs][mutitierstorage]  

Sie können das Front-End einer Anwendung mit mehreren Ebenen anstelle eines Azure-Cloud-Diensts auch als Azure-Website implementieren. Weitere Informationen zum Unterschied zwischen Websites und Cloud-Diensten finden Sie unter [Azure-Ausführungsmodelle][executionmodels].

Um die Anwendung aus diesem Lernprogramm als Standard-Webprojekt anstelle einer Cloud-Dienst-Webrolle zu erstellen, führen Sie die Schritte in diesem Lernprogramm mit den folgenden Unterschieden aus:

1. Wählen Sie bei der Projekterstellung die Projektvorlage **ASP.NET MVC Webanwendung** in der Kategorie **Web** anstelle der Vorlage **Cloud-Dienst** in der Kategorie **Cloud**. Führen Sie anschließend dieselben Schritte zur Erstellung der MVC-Anwendung durch, bis Sie zum Abschnitt **Cloud-Konfigurations-Manager** gelangen.

2. Erstellen Sie die Workerrolle in einer neuen, separaten Lösung, analog zu den ursprünglichen Anweisungen für die Webrolle. Nun erstellen Sie jedoch nur die Workerrolle im Cloud-Dienst-Projekt. Befolgen Sie anschließend dieselben Anweisungen für die Erstellung der Workerrolle.

3. Sie können Front-End und Back-End entweder separat testen oder beide simultan in separaten Visual Studio-Instanzen ausführen.

Informationen zum Bereitstellen des Front-Ends für eine Azure-Website finden Sie unter [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website](http://azure.microsoft.com/develop/net/tutorials/get-started/). Informationen zur Bereitstellung des Back-Ends für einen Azure-Cloud-Dienst finden Sie unter [.NET-Anwendungen mit mehreren Ebenen mithilfe von Speichertabellen, Warteschlangen und Blobs][mutitierstorage].


  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: service-bus-azure-and-service-bus-queues-compared-contrasted.md
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Tools und SDK herunterladen]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png



  [Azure portal]: http://manage.windowsazure.com
  [Azure-Portal]: http://manage.windowsazure.com
  [6]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [35]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/multi-web-45.png
  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [executionmodels]: ../cloud-services/fundamentals-application-models.md

<!---HONumber=August15_HO8-->