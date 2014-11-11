<properties linkid="dev-net-how-to-service-bus-relay" urlDisplayName="Service Bus Relay" pageTitle="How to use Service Bus relay (.NET) - Azure" metaKeywords="get started azure Service Bus Relay C# " description="Learn how to use the Azure Service Bus relay service to connect two applications hosted in different locations." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use the Service Bus Relay Service" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Verwenden des Service Bus Relay-Diensts

In diesem Leitfaden wird die Verwendung des Service Bus Relay-Diensts erläutert.
Die Beispiele sind in C# geschrieben und nutzen die
Windows Communication Foundation-API mit den Erweiterungen, die
in der Service Bus-Assembly enthalten sind. Diese ist Bestandteil der .NET-Bibliotheken für Azure. Weitere
Informationen zum Service Bus Relay finden Sie im
Abschnitt [Nächste Schritte][Nächste Schritte].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <span class="short-header">Was ist Service Bus Relay</span>Was ist Service Bus Relay

Der Service Bus **Relay**-Dienst ermöglicht Ihnen die Erstellung
von **Hybridanwendungen**, die sowohl in einem Azure-Rechenzentrum
als auch in Ihrer lokalen Unternehmensumgebung ausgeführt werden. Der Service Bus Relay-Dienst erleichtert
dies, indem er Ihnen die Möglichkeit bietet, WCF-Dienste
(Windows Communication Foundation), die sich in einem
Unternehmensnetzwerk befinden, sicher in der öffentlichen
Cloud bereitzustellen, ohne dass eine Firewallverbindung geöffnet werden
muss oder intrusive Änderungen an der unternehmensinternen Netzwerkinfrastruktur erforderlich werden.

![Relaykonzepte][Relaykonzepte]

Der Service Bus Relay-Dienst bietet Ihnen die Möglichkeit, WCF-Dienste
in Ihrer bestehenden Unternehmensumgebung zu hosten. Sie können das Lauschen auf
eingehende Sitzungen und Anforderungen für diese WCF-Dienste dann an den
Service Bus in Azure delegieren. Dadurch erhalten Sie die Möglichkeit, diese
Dienste für Anwendungscode in Azure, für mobile Mitarbeiter oder für
Extranetpartnerumgebungen verfügbar zu machen. Mit Service Bus können Sie differenziert und auf sichere Weise steuern,
welche Personen Zugriff auf diese Dienste erhalten. Service Bus stellt ein
leistungsstarkes und sicheres Verfahren zur Verfügung, um Anwendungsfunktionen
und Daten aus bestehenden Unternehmenslösungen verfügbar zu machen und in der Cloud zu nutzen.

In diesem Leitfaden wird gezeigt, wie mit dem Service Bus Relay ein mithilfe
der TCP-Channelbindung verfügbar gemachter WCF-Webdienst erstellt
wird, der eine geschützte Unterhaltung zwischen zwei Parteien implementiert.

## <span class="short-header">Erstellen eines Dienstnamespace</span>Erstellen eines Dienstnamespace

Um mit der Verwendung von Service Bus Relay in Azure zu
beginnen, müssen Sie zuerst einen Dienstnamespace erstellen. Ein Dienstnamespace ist eine Bereichseinheit
zur Adressierung von Servicebus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Dienstnamespace

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie im linken Navigationsbereich des Verwaltungsportals auf
    **Servicebus**.

3.  Klicken Sie im unteren Bereich des Verwaltungsportals auf **Erstellen**.

    ![][0]

4.  Geben Sie im Dialogfeld **Neuen Namespace hinzufügen** einen Namespacenamen ein.
    Das System prüft sofort, ob dieser Name verfügbar ist.

    ![][1]

5.  Wählen Sie nach der Bestätigung, dass der Name für den Namespace verfügbar ist,
    das Land oder die Region, wo dieser Namespace gehostet werden soll.
    (Stellen Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der
    Sie Ihre Rechnerressourcen einsetzen.)

    WICHTIG: Wählen Sie **dieselbe Region**, in der Sie auch Ihre Anwendung einsetzen möchten. Dies sorgt für die beste Leistung.

6.  Aktivieren Sie das Kontrollkästchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Eventuell müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto bereitgestellt werden.

    ![][2]

    Der neue Namespace wird anschließend im Verwaltungsportal angezeigt und in kurzer Zeit aktiviert. Fahren Sie erst fort, wenn der Status als **Aktiv** angezeigt wird.

## <span class="short-header">Abrufen der Verwaltungsanmeldeinformationen</span>Abrufen der standardmäßigen Verwaltungsanmeldeinformationen für den Namespace

Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Relayverbindung für den neuen Namespace, müssen Sie die Anmeldeinformationen für den Namespace abrufen.

1.  Klicken Sie im linken Navigationsbereich auf den Knoten **Servicebus**,
    um die Liste verfügbarer Namespaces anzuzeigen:
    ![][0]

2.  Wählen Sie in der angezeigten Liste den Namespace, den Sie gerade erstellt haben:
    ![][3]

3.  Klicken Sie auf **Verbindungsinformationen**.
    ![][4]

4.  Suchen Sie im Dialogfeld **Access connection information** nach den Einträgen **Default Issuer** und **Standardschlüssel**. Notieren Sie sich diese Werte, da Sie diese Informationen später benötigen, um Operationen mit dem Namespace durchzuführen.

## <span class="short-header">Abrufen des NuGet-Pakets</span>Abrufen des Servicesbus-NuGet-Pakets

Das Service Bus **NuGet**-Paket ist die einfachste Möglichkeit
zum Abrufen der Service Bus-API und Konfigurieren der Anwendung mit
allen Service Bus-Abhängigkeiten. Die NuGet Visual Studio-Erweiterung
ermöglicht eine problemlose Installation und Aktualisierung von Bibliotheken und Tools in Visual Studio
und Visual Studio Express 2012 für das Web. Das Service Bus NuGet-Paket ist die einfachste Möglichkeit
zum Abrufen der Service Bus-API und Konfigurieren der Anwendung
mit allen Service Bus-Abhängigkeiten.

Gehen sie folgendermaßen vor, um das NuGet-Paket in der Anwendung zu installieren:

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **Manage NuGet Packages**.
2.  Suchen Sie nach WindowsAzure", und wählen Sie das Element **Azure Service Bus** aus. Klicken Sie auf **Installieren**, um die Installation abzuschließen, und schließen Sie das Dialogfeld.

    ![][5]

## <span class="short-header">Bereitstellen und Nutzen eines SOAP-Webdiensts</span>Verwenden von Service Bus zum Bereitstellen und Nutzen eines SOAP-Webdiensts mit TCP

Um einen bestehenden WCF-SOAP-Webdienst für die externe
Nutzung verfügbar zu machen, müssen Sie Änderungen an den Dienstbindungen
und Adressen vornehmen. Dies kann Änderungen an der Konfigurationsdatei oder
Codeänderungen erforderlich machen, je nachdem, wie Ihre WCF-Dienste
eingerichtet und konfiguriert sind. Beachten Sie, dass WCF Ihnen die Möglichkeit
bietet, mehrere Netzwerkendpunkte über denselben Dienst zu verwalten,
sodass Sie die bestehenden internen Endpunkte beibehalten können,
während Sie gleichzeitig Service Bus-Endpunkte für den externen Zugriff hinzufügen.

Bei dieser Aufgabe erstellen Sie einen einfachen
WCF-Dienst und fügen ihm einen Service Bus-Listener hinzu. Für diese Übung wird eine gewisse Vertrautheit mit Visual Studio 2012 vorausgesetzt. Es werden daher nicht alle Details der Projekterstellung erläutert. Stattdessen steht der Code im Vordergrund.

Bevor Sie die Schritte unten ausführen, richten Sie zunächst mit dem folgenden
Verfahren Ihre Umgebung ein:

1.  Erstellen Sie in Visual Studio eine Konsolenanwendung, die die beiden
    Projekte "Client" und "Service" innerhalb der Projektmappe enthält.
2.  Legen Sie als Zielframework für die beiden Projekte .NET Framework 4 fest.
3.  Fügen Sie das Paket **Azure Service Bus NuGet** beiden Projekten hinzu.
    Damit werden den Projekten alle benötigten Assemblyverweise hinzugefügt.

### Erstellen des Diensts

Erstellen Sie zunächst den Dienst selbst Ein WCF-Dienst besteht aus mindestens
drei verschiedenen Teilen:

-   Definition eines Vertrags, der beschreibt, welche Nachrichten
    ausgetauscht werden und welche Vorgänge aufgerufen werden sollen
-   Implementierung des besagten Vertrags
-   Host, der diesen Dienst hostet und eine Reihe von
    Endpunkten verfügbar macht

Die Codebeispiele in diesem Abschnitt beziehen sich auf
jede dieser Komponenten.

Der Vertrag definiert einen einzelnen Vorgang, **AddNumbers**, der zwei
Zahlen hinzufügt und das Ergebnis zurückgibt. Die Schnittstelle **IProblemSolverChannel**
 ermöglicht dem Client die einfachere Verwaltung der Proxylebensdauer. Das Erstellen einer solchen Schnittstelle wird als eine bewährte Methode betrachtet. Es ist sinnvoll, die Vertragsdefinition
in einer separaten Datei abzulegen, sodass Sie von beiden Projekten
aus ("Client" und "Service") auf diese Datei verweisen können. Sie
können den Code aber auch in beide Projekte kopieren:

        using System.ServiceModel;
     
        [ServiceContract(Namespace = "urn:ps")]
        interface IProblemSolver
        {
            [OperationContract]
            int AddNumbers(int a, int b);
        }
     
        interface IProblemSolverChannel : IProblemSolver, IClientChannel {}

Nachdem der Vertrag etabliert wurde, ist die Implementierung ein Kinderspiel:

        class ProblemSolver : IProblemSolver
        {
            public int AddNumbers(int a, int b)
            {
                return a + b;
            }
        }

**Programmgesteuertes Konfigurieren eines Diensthosts**

Nachdem der Vertrag erstellt und die Implementierung durchgeführt
wurde, kann nun der Dienst gehostet werden. Das Hosting erfolgt innerhalb eines
**System.ServiceModel.ServiceHost**-Objekts, das die Verwaltung
der Dienstinstanzen übernimmt und die Endpunkte hostet, die auf
Nachrichten lauschen. Der folgende Code konfiguriert den Dienst sowohl mit einem
regulären lokalen Endpunkt als auch mit einem Service Bus-Endpunkt, um die Darstellung interner und externer Endpunkte nebeneinander zu veranschaulichen. Ersetzen Sie die
Zeichenfolge "\*\*namespace\*\*" durch Ihren Namespacenamen und "\*\*key\*\*"
durch den Ausstellerschlüssel, den Sie im obigen Setupschritt erhalten haben.

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");

    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedSecretTokenProvider( "owner", "**key**")});

    sh.Open();

    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();

    sh.Close();

In dem Beispiel erstellen Sie zwei Endpunkte für dieselbe
Vertragsimplementierung. Einer ist ein lokaler Endpunkt, und der andere wird über Service Bus projiziert. Die Hauptunterschiede sind die Bindungen:
**NetTcpBinding** für den lokalen Endpunkt und
**NetTcpRelayBinding** für den Service Bus-Endpunkt und die Adressen. Der lokale Endpunkt verfügt über
eine lokale Netzwerkadresse mit einem bestimmten Port. Der Service Bus-Endpunkt hat eine
Endpunktadresse, die sich aus der Zeichenfolge "sb", Ihrem
Namespacenamen und dem Pfad "solver" zusammensetzt. Das Ergebnis ist der URI
"sb://[serviceNamespace].servicebus.windows.net/solver", der den Dienstendpunkt
als Service Bus-TCP-Endpunkt mit einem vollqualifizierten externen
DNS-Namen angibt. Wenn Sie den Code, der gemäß der obigen Erläuterung die
Platzhalter ersetzt, in die **Main**-Funktion der Anwendung "Service" einfügen, erhalten Sie einen funktionierenden Dienst. Wenn Ihr Dienst exklusiv Service Bus abhören soll, entfernen Sie die lokale Endpunktdeklaration.

**Konfigurieren eines Diensthosts in der Datei "App.config"**

Sie können den Host auch mithilfe der Datei **App.config** konfigurieren. Der Hostingcode
des Diensts sieht in diesem Fall folgendermaßen aus:

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

Die Endpunktdefinitionen werden in die Datei **App.config** verschoben. Beachten Sie, dass das **NuGet**-Paket der Datei "App.config" bereits eine Reihe von Definitionen hinzugefügt hat, bei denen es sich um erforderliche Konfigurationserweiterungen für Service Bus handelt. Der folgende Codeausschnitt,
der das genaue Äquivalent des obigen Codes ist, sollte direkt unter dem **system.serviceModel**-Element angezeigt werden. In dem Codeausschnitt wird davon ausgegangen, dass der C#-Namespace des Projekts den Namen "Service" hat.
Ersetzen Sie die Platzhalter durch den Namespace und den Schlüssel des Service Bus-Diensts.

    <services>
        <service name="Service.ProblemSolver">
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://**namespace**.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

Nachdem Sie diese Änderungen vorgenommen haben, startet der Dienst wie zuvor, jedoch mit zwei aktiven Endpunkten: einem lokalen Endpunkt und einem, der in der Cloud abhört.

### Erstellen des Clients

**Programmgesteuertes Konfigurieren eines Clients**

Um den Dienst zu nutzen, können Sie einen WCF-Client mit einem
**ChannelFactory**-Objekt erstellen. Der Service Bus-Dienst verwendet ein mit Access
Control Service (ACS) implementiertes anspruchsbasiertes Sicherheitsmodell. Die
**TokenProvider**-Klasse stellt einen Sicherheitstokenanbieter mit
integrierten Factory-Methoden dar, die einige bekannte Tokenanbieter zurückgeben. Im
folgenden Beispiel wird das **SharedSecretTokenProvider**-Objekt verwendet, um
freigegebene geheime Anmeldeinformationen aufzunehmen
und die Übernahme der entsprechenden Token vom Access Control Service zu verwalten. Die Name und der Schlüssel wurden
aus dem Portal abgerufen, wie im vorherigen Abschnitt erläutert.

Verweisen Sie zuerst auf oder kopieren Sie den **IProblemSolver**-Vertragscode
aus dem Dienst in Ihr Clientprojekt.

Ersetzen Sie anschließend den Code in der **Main**-Methode des Clients, und ersetzen Sie dabei den Platzhaltertext erneut durch den Service Bus-Dienstnamespace und -Schlüssel.

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver")));

    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedSecretTokenProvider("owner","**key**") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Sie können jetzt den Client und den Dienst kompilieren
und diese ausführen (führen Sie zuerst den Dienst aus). Der Client
ruft den Dienst auf und gibt "9" aus. Der Client und der Server können auf verschiedenen Computern ausgeführt werden, selbst über Netzwerke hinweg, und die Kommunikation wird weiterhin funktionieren. Der Clientcode kann ebenfalls in der Cloud oder lokal ausgeführt werden.

**Konfigurieren eines Clients in der Datei "App.config"**

Sie können den Client auch mithilfe der Datei **App.config** konfigurieren. Der
Clientcode hierfür sieht folgendermaßen aus:

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Die Endpunktdefinitionen werden in die Datei **App.config** verschoben. Der folgende
Codeausschnitt, der mit dem oben aufgeführten Code identisch
ist, sollte direkt unter dem **system.serviceModel**-Element angezeigt werden. Wie zuvor, müssen
Sie auch hier die Platzhalter durch den Service Bus-Dienstnamespace und
-Schlüssel ersetzen.

    <client>
        <endpoint name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://**namespace**.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

## <span class="short-header">Nächste Schritte</span>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des Service Bus-**Relay**-Diensts
vertraut sind, finden Sie unter diesen Links weitere Informationen.

-   Erstellen eines Diensts: [Erstellen eines Diensts für Service Bus][Erstellen eines Diensts für Service Bus]
-   Erstellen des Clients: [Erstellen einer Service Bus-Clientanwendung][Erstellen einer Service Bus-Clientanwendung].
-   Service Bus-Beispiele: Download von [Azure-Beispielen][Azure-Beispielen].

  [Nächste Schritte]: #next_steps
  [create-account-note]: ../includes/create-account-note.md
  [Relaykonzepte]: ./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [0]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png
  [1]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png
  [2]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png
  [3]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png
  [4]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-06.png
  [5]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png
  [Erstellen eines Diensts für Service Bus]: http://msdn.microsoft.com/de-de/library/windowsazure/ee173564.aspx
  [Erstellen einer Service Bus-Clientanwendung]: http://msdn.microsoft.com/de-de/library/windowsazure/ee173543.aspx
  [Azure-Beispielen]: http://code.msdn.microsoft.com/windowsazure
