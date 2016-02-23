<properties 
   pageTitle="Tutorial zu Service Bus-Relaymessaging | Microsoft Azure"
   description="Erstellen Sie eine Service Bus-Clientanwendung und einen Service Bus-Dienst mithilfe von Service Bus-Relaymessaging."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Tutorial zu Service Bus-Relaymessaging

Dieses Tutorial beschreibt das Erstellen einer einfachen Service Bus-Clientanwendung und eines Service Bus-Clientdiensts mithilfe der Service Bus-Relayfunktionen. Ein entsprechendes Tutorial, in dem die [Brokermessagingfunktionen](service-bus-messaging-overview.md#Brokered-messaging) von Service Bus beschrieben werden, finden Sie unter .[NET-Tutorial zu Service Bus-Brokermessaging](service-bus-brokered-tutorial-dotnet.md).

Dieses Tutorial vermittelt Ihnen Grundkenntnisse zu den Schritten, die zum Erstellen einer Service Bus-Client- und -Dienstanwendung erforderlich sind. Ein Dienst ist wie sein Gegenstück in WCF ein Konstrukt, das mindestens einen Endpunkt bereitstellt. Dabei stellt jeder Endpunkt mindestens einen Dienstvorgang zur Verfügung. Der Endpunkt eines Diensts gibt eine Adresse an, unter der der Dienst gefunden werden kann, eine Bindung, die die Information enthält, dass ein Client mit dem Dienst kommunizieren muss, und einen Vertrag, der die Funktionen definiert, die der Dienst für seine Clients bereitstellt. Der Hauptunterschied zwischen einem WCF- und einem Service Bus-Dienst besteht darin, dass der Endpunkt in der Cloud und nicht lokal auf Ihrem Computer bereitgestellt wird.

Nachdem Sie die Themen in diesem Tutorial nacheinander durchgearbeitet haben, verfügen Sie über einen ausgeführten Dienst und über einen Client, der die Vorgänge des Diensts aufrufen kann. Im ersten Thema wird die Einrichtung eines Kontos beschrieben. Die nächsten drei Schritte beschreiben das Definieren eines Diensts, der einen Vertrag verwendet, das Implementieren des Diensts und das Konfigurieren des Diensts mithilfe von Code. Außerdem wird erläutert, wie der Dienst gehostet und ausgeführt wird. Der erstellte Dienst ist selbstgehostet, und der Client und der Dienst werden auf dem gleichen Computer ausgeführt. Sie können den Dienst mithilfe von Code oder einer Konfigurationsdatei konfigurieren.

Die letzten drei Schritte beschreiben das Erstellen einer Clientanwendung, das Konfigurieren der Clientanwendung sowie das Erstellen und Verwenden eines Clients, der auf die Funktionen des Hosts zugreifen kann.

Bei allen Themen in diesem Abschnitt wird davon ausgegangen, dass Sie Visual Studio als Entwicklungsumgebung verwenden.

## Registrieren für ein Konto

Der erste Schritt umfasst die Einrichtung des Service Bus-Dienstnamespace und das Abrufen eines SAS-Schlüssels (Shared Access Signature). Ein Dienstnamespace stellt eine Anwendungsgrenze für jede Anwendung zur Verfügung, die durch Service Bus bereitgestellt wird. Die Kombination aus dem Dienstnamespace und dem SAS-Schlüssel stellt Anmeldeinformationen für Service Bus zur Authentifizierung des Zugriffs auf eine Anwendung zur Verfügung.

1. Rufen Sie zum Erstellen eines Dienstnamespace das [klassische Azure-Portal][] auf. Klicken Sie auf der linken Seite auf **Service Bus** und anschließend auf **Erstellen**. Geben Sie einen Namen für den Namespace ein, und klicken Sie auf das Häkchen.

	>[AZURE.NOTE] Sie müssen für Client- und Dienstanwendungen nicht denselben Dienstnamespace verwenden.

1. Klicken Sie im Hauptfenster des Portals auf den Namen des Namespace, den Sie im vorherigen Schritt erstellt haben.

2. Klicken Sie auf **Konfigurieren**, um die Standardrichtlinien für den gemeinsamen Zugriff für Ihren Namespace anzuzeigen.

3. Notieren Sie den Primärschlüssel für die **RootManageSharedAccessKey**-Richtlinie, oder kopieren Sie ihn in die Zwischenablage. Sie verwenden diesen Wert später im Tutorial.

## Definieren eines WCF-Dienstvertrags für die Verwendung mit Service Bus

Der Dienstvertrag gibt an, welche Vorgänge (Webdienstterminologie für Methoden oder Funktionen) der Dienst unterstützt. Verträge werden durch die Definition einer C++-, C#- oder Visual Basic-Schnittstelle erstellt. Jede Methode in der Schnittstelle entspricht einem bestimmten Dienstvorgang. Auf jede Schnittstelle muss das [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx)-Attribut und auf jeden Vorgang das [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx)-Attribut angewendet werden. Wenn eine Methode in einer Schnittstelle das [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx)-Attribut, nicht jedoch das [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx)-Attribut besitzt, wird diese Methode nicht bereitgestellt. Der Code für diese Aufgaben wird im Beispiel im Anschluss an das Verfahren bereitgestellt. Eine ausführlichere Darstellung von Verträgen und Diensten finden Sie in der WCF-Dokumentation unter [Entwerfen und Implementieren von Diensten](https://msdn.microsoft.com/library/ms729746.aspx).

### So erstellen Sie einen Service Bus-Vertrag mit einer Schnittstelle

1. Öffnen Sie Visual Studio als Administrator, indem Sie mit der rechten Maustaste im **Startmenü** auf das Programm klicken und dann **Als Administrator ausführen** auswählen.

1. Erstellen Sie ein neues Konsolenanwendungsprojekt. Klicken Sie auf das Menü **Datei**, wählen Sie **Neu** aus, und klicken Sie auf **Projekt**. Klicken Sie im Dialogfeld **Neues Projekt** auf **Visual C#** (wenn **Visual C#** nicht angezeigt wird, suchen Sie unter **Andere Sprachen**). Klicken Sie auf die Vorlage **Konsolenanwendung**, und geben Sie ihr den Namen **EchoService**. Verwenden Sie den standardmäßigen **Speicherort**. Klicken Sie auf **OK**, um das Projekt zu erstellen.

1. Fügen Sie dem Projekt einen Verweis auf `System.ServiceModel.dll` hinzu: Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste unter dem Projektordner auf den Ordner **Verweise**, und klicken Sie dann auf **Verweis hinzufügen**. Klicken Sie im Dialogfeld **Verweis hinzufügen** auf die Registerkarte **.NET**, und führen Sie einen Bildlauf nach unten bis zu **System.ServiceModel** durch. Wählen Sie diesen Eintrag aus, und klicken Sie auf **OK**.

1. Doppelklicken Sie im Projektmappen-Explorer auf die Datei "Program.cs", um sie im Editor zu öffnen.

1. Fügen Sie eine Anweisung `using` für den Namespace "System.ServiceModel" hinzu.

	```
	using System.ServiceModel;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) ist der Namespace, der den programmgesteuerten Zugriff auf die grundlegenden Funktionen von WCF ermöglicht. Service Bus verwendet viele Objekte und Attribute von WCF, um Dienstverträge zu definieren.

1. Ändern Sie den Namespacenamen vom Standardnamen **EchoService** zu **Microsoft.ServiceBus.Samples**.

	>[AZURE.IMPORTANT] In diesem Tutorial wird der C#-Namespace **Microsoft.ServiceBus.Samples** verwendet. Hierbei handelt es sich um den Namespace des durch den Vertrag verwalteten Typs, der im Schritt [Konfigurieren des WCF-Clients](#configure-the-wcf-client) in der Konfigurationsdatei verwendet wird. Sie können beim Erstellen dieses Beispiels jeden gewünschten Namespace angeben, das Tutorial funktioniert jedoch nur, wenn Sie anschließend die Namespaces des Vertrags und des Diensts in der Anwendungskonfigurationsdatei entsprechend ändern. Der in der Datei "App.config" angegebene Namespace muss mit dem in Ihren C#-Dateien angegebenen Namespace identisch sein.

1. Definieren Sie direkt nach der Namespacedeklaration `Microsoft.ServiceBus.Samples` (jedoch innerhalb des Namespace) eine neue Schnittstelle namens `IEchoContract`, und wenden Sie das Attribut `ServiceContractAttribute` mit dem Namespacewert ****http://samples.microsoft.com/ServiceModel/Relay/** auf die Schnittstelle an. Der Namespacewert unterscheidet sich von dem Namespace, den Sie im gesamten Codebereich verwenden. Der Namespacewert wird stattdessen als eindeutiger Bezeichner für diesen Vertrag verwendet. Das explizite Angeben des Namespace verhindert, dass der Standardwert für den Namespace dem Vertragsnamen hinzugefügt wird.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	}
	```

	>[AZURE.NOTE] In der Regel enthält der Dienstvertragsnamespace ein Benennungsschema, das Versionsinformationen umfasst. Durch das Einbeziehen von Versionsinformationen im Dienstvertragsnamespace können Dienste größere Änderungen isolieren, indem ein neuer Dienstvertrag mit einem neuen Namespace definiert und an einem neuen Endpunkt bereitgestellt wird. Auf diese Weise können Clients weiterhin den alten Dienstvertrag verwenden, ohne dass sie aktualisiert werden müssen. Versionsinformationen können aus einer Datumsangabe oder einer Buildnummer bestehen. Weitere Informationen finden Sie unter [Dienstversionsverwaltung](http://go.microsoft.com/fwlink/?LinkID=180498). In diesem Tutorial enthält das Benennungsschema des Dienstvertragsnamespace keine Versionsinformationen.

1. Deklarieren Sie innerhalb der `IEchoContract`-Benutzeroberfläche eine Methode für den einzelnen Vorgang, den der `IEchoContract`-Vertrag in der Schnittstelle bereitstellt, und wenden Sie das `OperationContractAttribute`-Attribut auf die Methode an, die Sie als Teil des öffentlichen Service Bus-Vertrags bereitstellen möchten.

	```
	[OperationContract]
	string Echo(string text);
	```

1. Deklarieren Sie außerhalb des Vertrags einen Kanal mit Vererbung von `IEchoChannel` und an die Schnittstelle `IClientChannel`, wie im folgenden Beispiel gezeigt:

	```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    publicinterface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

	Ein Kanal ist das WCF-Objekt, über das der Host und der Client einander Informationen übergeben. Später schreiben Sie Code für den Kanal, um ein Echo der Informationen zwischen den beiden Anwendungen zu erzeugen.

1. Klicken Sie im Menü **Build** auf **Projektmappe erstellen**, oder drücken Sie F6, um die Richtigkeit Ihrer bisherigen Arbeitsschritte zu überprüfen.

### Beispiel

Das folgende Codebeispiel zeigt eine Basisschnittstelle, die einen Service Bus-Vertrag definiert.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nachdem nun die Schnittstelle erstellt ist, können Sie die Schnittstelle implementieren.

## Implementieren des WCF-Vertrags für die Verwendung von Service Bus

Zum Erstellen eines Service Bus-Diensts müssen Sie zuerst den Vertrag erstellen, der über eine Schnittstelle definiert wird. Weitere Informationen zum Erstellen der Schnittstelle finden Sie im vorherigen Schritt. Der nächste Schritt ist das Implementieren der Schnittstelle. Dies umfasst das Erstellen einer Klasse namens `EchoService`, welche die benutzerdefinierte `IEchoContract`-Schnittstelle implementiert. Nachdem Sie die Schnittstelle implementiert haben, konfigurieren Sie die Schnittstelle mithilfe einer "App.config"-Konfigurationsdatei. Die Konfigurationsdatei enthält die erforderlichen Informationen für die Anwendung, wie z. B. den Namen des Diensts, den Namen des Vertrags und den Typ des Protokolls, das für die Kommunikation mit Service Bus verwendet wird. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt. Eine allgemeinere Darstellung der Implementierung eines Dienstvertrags finden Sie in der WCF-Dokumentation (Windows Communication Foundation) unter [Implementieren von Dienstverträgen](https://msdn.microsoft.com/library/ms733764.aspx).

1. Erstellen Sie eine neue Klasse namens `EchoService` direkt unterhalb der Definition der `IEchoContract`-Schnittstelle. Die `EchoService`-Klasse implementiert die `IEchoContract`-Schnittstelle. 

	```
	class EchoService : IEchoContract
	{
	}
	```
	
	Ähnlich wie bei anderen Schnittstellenimplementierungen können Sie die Definition in einer anderen Datei implementieren. In diesem Tutorial befindet sich die Implementierung allerdings in derselben Datei wie die Schnittstellendefinition und die `Main`-Methode.

1. Wenden Sie das Attribut [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) an, das den Dienstnamen und -namespace angibt.

	```[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class EchoService : IEchoContract
	{
	}
	```

1. Implementieren Sie die `Echo`-Methode, die in der `IEchoContract`-Schnittstelle in der `EchoService`-Klasse definiert ist.

	```
	public string Echo(string text)
	{
    	Console.WriteLine("Echoing: {0}", text);
    	return text;
	}
	```

1. Klicken Sie auf **Build** und dann auf **Projektmappe erstellen**, um die Richtigkeit Ihrer bisherigen Arbeitsschritte zu überprüfen.

### So definieren Sie die Konfiguration für den Diensthost

1. Die Konfigurationsdatei ist einer WCF-Konfigurationsdatei sehr ähnlich. Sie enthält den Dienstnamen, den Endpunkt (den Speicherort, den Service Bus für Clients und Hosts zur Kommunikation bereitstellt) und die Bindung (den Typ des Protokolls, das für die Kommunikation verwendet wird). Der Hauptunterschied besteht darin, dass dieser konfigurierte Dienstendpunkt auf eine [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx)-Bindung verweist, die nicht Bestandteil von .NET Framework ist. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) ist eine der Bindungen, die durch Service Bus definiert werden.

1. Klicken Sie im **Projektmappen-Explorer** auf die Datei "App.config", die derzeit die folgenden XML-Elemente enthält:

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	</configuration>
	```

1. Fügen Sie der Datei "App.config" ein `<system.serviceModel>`-XML-Element hinzu. Dies ist ein WCF-Element, das einen oder mehrere Dienste definiert. In diesem Beispiel wird es verwendet, um den Dienstnamen und -endpunkt zu definieren.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
  
	  </system.serviceModel>
	</configuration>
	```

1. Fügen Sie innerhalb der `<system.serviceModel>`-Tags ein `<services>`-Element hinzu. Sie können mehrere Service Bus-Anwendungen in einer einzigen Konfigurationsdatei definieren. In diesem Tutorial wird jedoch nur eine Anwendung definiert.
 
	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <services>

	    </services>
	  </system.serviceModel>
	</configuration>
	```

1. Fügen Sie im `<services>`-Element ein `<service>`-Element hinzu, um den Namen des Diensts zu definieren.

	```
	<servicename="Microsoft.ServiceBus.Samples.EchoService">
	</service>
	```

1. Definieren Sie im `<service>`-Element den Speicherort des Endpunktvertrags sowie den Bindungstyp für den Endpunkt.

	```
	<endpointcontract="Microsoft.ServiceBus.Samples.IEchoContract"binding="netTcpRelayBinding"/>
	```

	Der Endpunkt definiert, wo der Client nach der Hostanwendung sucht. Dieser Schritt wird später in diesem Tutorial verwendet, um einen URI zu erstellen, der den Host über Service Bus uneingeschränkt verfügbar macht. Die Bindung deklariert, dass TCP als Protokoll für die Kommunikation mit Service Bus verwendet wird.


1. Fügen Sie direkt hinter dem `<services>`-Element die folgende Bindungserweiterung ein:
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. Klicken Sie im Menü **Build** auf **Projektmappe erstellen**, um die Richtigkeit Ihrer bisherigen Arbeitsschritte zu überprüfen.

### Beispiel

Der folgende Code zeigt die Implementierung des Dienstvertrags.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Der folgende Code zeigt das grundlegende Format der Datei "App.config", die dem Diensthost zugeordnet ist.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Hosten und Ausführen eines Basiswebdiensts für die Registrierung bei Service Bus

Dieser Schritt beschreibt die Ausführung eines Service Bus-Basisdiensts.

### So erstellen Sie die Service Bus-Anmeldeinformationen

1. Installieren Sie das [NuGet-Paket für Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

1. Erstellen Sie in `Main()` zwei Variablen zum Speichern des Namespace und des SAS-Schlüssels, die aus dem Konsolenfenster gelesen werden.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS key: ");
	string sasKey = Console.ReadLine();
	```

	Der SAS-Schlüssel wird später für den Zugriff auf Ihr Service Bus-Projekt verwendet. Der Namespace wird als Parameter an `CreateServiceUri` übergeben, um einen Dienst-URI zu erstellen.

4. Deklarieren Sie mithilfe eines [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx)-Objekts, dass ein SAS-Schlüssel als Anmeldeinformationstyp verwendet wird. Fügen Sie den folgenden Code direkt unter dem Code hinzu, den Sie im letzten Schritt hinzugefügt haben.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

### So erstellen Sie eine Basisadresse für den Dienst

1. Erstellen Sie nach dem Code, den Sie im letzten Schritt hinzugefügt haben, eine `Uri`-Instanz für die Basisadresse des Diensts. Dieser URI gibt das Service Bus-Schema, den Namespace und den Pfad der Dienstschnittstelle an.

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

	"sb" ist eine Abkürzung für das Service Bus-Schema und zeigt an, dass TCP als Protokoll verwendet wird. Dies wurde zuvor bereits in der Konfigurationsdatei durch Angeben von [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) als Bindung festgelegt.
	
	Für dieses Tutorial lautet der URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### So erstellen und konfigurieren Sie den Diensthost

1. Legen Sie den Verbindungsmodus auf `AutoDetect` fest.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

	Der Verbindungsmodus beschreibt das Protokoll, das der Dienst für die Kommunikation mit Service Bus verwendet: HTTP oder TCP. Mithilfe der Standardeinstellung `AutoDetect` versucht der Dienst, eine Verbindung mit Service Bus über TCP herzustellen, wenn verfügbar. Wenn TCP nicht verfügbar ist, wird HTTP verwendet. Beachten Sie, dass sich diese Vorgehensweise von dem Protokoll unterscheidet, das der Dienst für die Clientkommunikation angibt. Dieses Protokoll wird durch die verwendete Bindung festgelegt. Ein Dienst kann z. B. die Bindung [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) verwenden, die angibt, dass ihr Endpunkt (für Service Bus bereitgestellt) über HTTP mit Clients kommuniziert. Der gleiche Dienst kann **ConnectivityMode.AutoDetect** angeben, sodass der Dienst über TCP mit Service Bus kommuniziert.

1. Erstellen Sie den Diensthost unter Verwendung des URIs, den Sie zuvor in diesem Abschnitt erstellt haben.

	```
	ServiceHost host = new ServiceHost(typeof(EchoService), address);
	```

	Der Diensthost ist das WCF-Objekt, das den Dienst instanziiert. Hier wird der Diensttyp übergeben, den Sie erstellen möchten (ein `EchoService`-Typ), sowie die Adresse, unter der der Dienst bereitgestellt werden soll.

1. Fügen Sie am Anfang der Datei "Program.cs" Verweise auf [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) und [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx) hinzu.

	```
	using System.ServiceModel.Description;
	using Microsoft.ServiceBus.Description;
	```

1. Konfigurieren Sie in `Main()` den Endpunkt so, dass öffentlicher Zugriff ermöglicht wird.

	```
	IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
	```

	Durch diesen Schritt wird Service Bus darüber informiert, dass Ihre Anwendung durch Untersuchen des Service Bus-ATOM-Feeds für Ihr Projekt öffentlich angezeigt werden kann. Wenn Sie **DiscoveryType** auf **private** festlegen, ist ein Client trotzdem in der Lage, auf den Dienst zuzugreifen. Der Dienst wird jedoch nicht angezeigt, wenn der Client den Service Bus-Namespace durchsucht. Der Client muss stattdessen bereits vorher den Endpunktpfad kennen.

1. Wenden Sie die Dienstanmeldeinformationen auf die in der Datei "App.config" definierten Dienstendpunkte an:

	```
	foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
	{
	    endpoint.Behaviors.Add(serviceRegistrySettings);
	    endpoint.Behaviors.Add(sasCredential);
	}
	```

	Wie bereits im vorherigen Schritt erwähnt wurde, können Sie in der Konfigurationsdatei mehrere Dienste und Endpunkte deklarieren. Wenn dies der Fall ist, durchläuft der Code die Konfigurationsdatei und sucht nach jedem Endpunkt, auf den die Anmeldeinformationen angewendet werden sollen. Für dieses Tutorial weist die Konfigurationsdatei jedoch nur einen Endpunkt auf.

### So öffnen Sie den Diensthost

1. Öffnen Sie den Dienst.

	```
	host.Open();
	```

1. Informieren Sie den Benutzer, dass der Dienst ausgeführt wird, und erläutern Sie, wie der Dienst heruntergefahren wird.

	```
	Console.WriteLine("Service address: " + address);
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

1. Schließen Sie danach den Diensthost.

	```
	host.Close();
	```

1. Drücken Sie F6, um das Projekt zu erstellen.

### Beispiel

Das folgende Beispiel umfasst den Dienstvertrag und die Implementierung aus früheren Schritten des Tutorial und hostet den Dienst in einer Konsolenanwendung. Kompilieren Sie den folgenden Code in eine ausführbare Datei namens "EchoService.exe".

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## Erstellen eines WCF-Clients für den Dienstvertrag

Der nächste Schritt besteht darin, eine Service Bus-Basisclientanwendung zu erstellen und den Dienstvertrag zu definieren, der in späteren Schritten implementiert wird. Beachten Sie, dass viele dieser Schritte den Schritten ähneln, die zum Erstellen eines Diensts verwendet werden: Definieren eines Vertrags, Bearbeiten einer App.config-Datei, Verwenden von Anmeldeinformationen zum Herstellen einer Verbindung mit Service Bus usw. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt.

1. Erstellen Sie ein neues Projekt in der aktuellen Visual Studio-Projektmappe für den Client, indem Sie folgendermaßen vorgehen:
	1. Klicken Sie im Projektmappen-Explorer in der gleichen Projektmappe, die den Dienst enthält, mit der rechten Maustaste auf die aktuelle Projektmappe (nicht auf das Projekt), und klicken Sie auf **Hinzufügen**. Klicken Sie dann auf **Neues Projekt**.
	2. Klicken Sie im Dialogfeld **Neues Projekt hinzufügen** auf **Visual C#** (wenn **Visual C#** nicht angezeigt wird, suchen Sie unter **Andere Sprachen**), wählen Sie die Vorlage **Konsolenanwendung** aus, und nennen Sie sie **EchoClient**.
	3. Klicken Sie auf **OK**. <br />

1. Doppelklicken Sie im Projektmappen-Explorer im Projekt **EchoClient** auf die Datei "Program.cs", um sie im Editor zu öffnen.

1. Ändern Sie den Namespacenamen vom Standardnamen `EchoClient` in `Microsoft.ServiceBus.Samples`.

1. Fügen Sie dem Projekt einen Verweis auf "System.ServiceModel.dll" hinzu:
	1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste unter dem Projekt **EchoClient** auf **Verweise**. Klicken Sie dann auf **Verweis hinzufügen**.
	2. Da Sie bereits im ersten Schritt dieses Tutorials einen Verweis auf diese Assembly hinzugefügt haben, wird sie nun auf der Registerkarte **Zuletzt verwendet** angezeigt. Klicken Sie auf **Zuletzt verwendet**, und wählen Sie **System.ServiceModel.dll** aus der Liste aus. Klicken Sie dann auf **OK**. Wenn **System.ServiceModel.dll** auf der Registerkarte **Zuletzt verwendet** nicht angezeigt wird, klicken Sie auf die Registerkarte **Durchsuchen**, und navigieren Sie zu **C:\\Windows\\Microsoft.NET\\Framework\\v3.0\\Windows Communication Foundation**. Wählen Sie die Assembly dann dort aus. <br />

1. Fügen Sie der Program.cs-Datei eine `using`-Anweisung für den Namespace [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) hinzu.

	```
	using System.ServiceModel;
	```

1. Wiederholen Sie die vorherigen Schritte, um Ihrem Projekt einen Verweis auf "Microsoft.ServiceBus.dll" und den Namespace [Microsoft.ServiceBus](https://msdn.microsoft.com/library/microsoft.servicebus.aspx) hinzuzufügen.

1. Fügen Sie dem Namespace die Dienstvertragsdefinition hinzu, wie im folgenden Beispiel gezeigt. Beachten Sie, dass diese Definition mit der im Projekt **Service** verwendeten Definition identisch ist. Sie sollten diesen Code am Anfang des Namespace `Microsoft.ServiceBus.Samples` hinzufügen.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	    [OperationContract]
	    string Echo(string text);
	}

	publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

1. Drücken Sie F6, um den Client zu erstellen.

### Beispiel

Der folgende Code zeigt den aktuellen Status der Datei "Program.cs" im Projekt "EchoClient".

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## Konfigurieren des WCF-Clients

In diesem Schritt erstellen Sie eine App.config-Datei für eine Clientbasisanwendung, die auf den zuvor in diesem Tutorial erstellten Dienst zugreift. Diese App.config-Datei definiert den Vertrag, die Bindung und den Namen des Endpunkts. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt.

1. Doppelklicken Sie im Projektmappen-Explorer im Clientprojekt auf **App.config**, um die Datei zu öffnen, die derzeit die folgenden XML-Elemente enthält:

	```
	<?xmlversion="1.0"?>
	<configuration>
	  <startup>
	    <supportedRuntimeversion="v4.0"sku=".NETFramework,Version=v4.0"/>
	  </startup>
	</configuration>
	```

1. Fügen Sie der Datei "App.config" ein XML-Element für `system.serviceModel` hinzu.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	
	  </system.serviceModel>
	</configuration>
	```
	
	Dieses Element deklariert, dass Ihre Anwendung Endpunkte im WCF-Stil verwendet. Wie bereits ausgeführt wurde, ist die Konfiguration einer Service Bus-Anwendung in großen Teilen mit der Konfiguration einer WCF-Anwendung identisch. Der Hauptunterschied ist der Speicherort, auf den die Konfigurationsdatei verweist.

1. Fügen Sie im Element "system.serviceModel" ein Element `<client>` hinzu.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <client>
	    </client>
	  </system.serviceModel>
	</configuration>
	```

	In diesem Schritt wird deklariert, dass Sie eine Clientanwendung im WCF-Stil definieren.

1. Definieren Sie im Element `client` den Namen, den Vertrag und den Bindungstyp für den Endpunkt.

	```
	<endpointname="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IEchoContract"
					binding="netTcpRelayBinding"/>
	```

	In diesem Schritt werden der Name des Endpunkts, der im Dienst definierte Vertrag und die Verwendung von TCP durch die Clientanwendung für die Kommunikation mit Service Bus definiert. Der Endpunktname wird im nächsten Schritt verwendet, um diese Endpunktkonfiguration mit dem Dienst-URI zu verknüpfen.

1. Fügen Sie direkt nach dem <client>-Element die folgende Bindungserweiterung ein.
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. Klicken Sie auf **Datei** und anschließend auf **Alles speichern**.

## Beispiel

Der folgende Code zeigt die App.config-Datei für den Echo-Client.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Implementieren des WCF-Clients für den Aufruf von Service Bus

In diesem Schritt implementieren Sie eine Clientbasisanwendung, die auf den zuvor in diesem Tutorial erstellten Dienst zugreift. Ähnlich wie der Dienst führt der Client viele derselben Vorgänge aus, um auf Service Bus zuzugreifen:

1. Festlegen des Verbindungsmodus.
1. Erstellen des URIs, der den Hostdienst ermittelt.
1. Definieren der Sicherheitsanmeldeinformationen.
1. Anwenden der Anmeldeinformationen auf die Verbindung.
1. Öffnen der Verbindung.
1. Ausführen der anwendungsspezifischen Aufgaben.
1. Schließen der Verbindung.

Einer der Hauptunterschiede besteht jedoch darin, dass die Clientanwendung einen Kanal für die Verbindung mit Service Bus verwendet, während der Dienst einen Aufruf an **ServiceHost** verwendet. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt.

### So implementieren Sie eine Clientanwendung

1. Legen Sie den Verbindungsmodus auf **AutoDetect** fest. Fügen Sie in der Methode `Main()` der Clientanwendung den folgenden Code hinzu.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

1. Definieren Sie Variablen zum Speichern der Werte für den Dienstnamespace und den SAS-Schlüssel, die aus der Konsole gelesen werden.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS Key: ");
	string sasKey = Console.ReadLine();
	```

1. Erstellen Sie den URI, der den Speicherort des Hosts in Ihrem Service Bus-Projekt definiert.

	```
	Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

1. Erstellen Sie das Objekt für die Anmeldeinformationen für Ihren Dienstnamespace-Endpunkt.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

1. Erstellen Sie die Kanalfactory, welche die in der App.config-Datei beschriebene Konfiguration lädt.

	```
	ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
	```

	Bei einer Kanalfactory ist handelt es sich um ein WCF-Objekt, das einen Kanal erstellt, über den der Dienst und die Clientanwendungen kommunizieren.

1. Wenden Sie die Service Bus-Anmeldeinformationen an.

	```
	channelFactory.Endpoint.Behaviors.Add(sasCredential);
	```

1. Erstellen und öffnen Sie den Kanal zum Dienst.

	```
	IEchoChannel channel = channelFactory.CreateChannel();
	channel.Open();
	```

1. Schreiben Sie die grundlegende Benutzeroberfläche und die Funktionen für das Echo.

	```
	Console.WriteLine("Enter text to echo (or [Enter] to exit):");
	string input = Console.ReadLine();
	while (input != String.Empty)
	{
	    try
	    {
	        Console.WriteLine("Server echoed: {0}", channel.Echo(input));
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error: " + e.Message);
	    }
	    input = Console.ReadLine();
	}
	```

	Beachten Sie, dass der Code die Instanz des Kanalobjekts als Proxy für den Dienst verwendet.

1. Schließen Sie den Kanal und die Factory.

	```
	channel.Close();
	channelFactory.Close();
	```

## So führen Sie die Clientanwendung aus

1. Drücken Sie F6, um die Projektmappe erstellen. Auf diese Weise werden das Clientprojekt und das Dienstprojekt erstellt, die Sie in einem früheren Schritt dieses Tutorials erstellt haben. Außerdem wird für jedes Projekt eine ausführbare Datei erstellt.

1. Bevor Sie die Clientanwendung ausführen, stellen Sie sicher, dass die Dienstanwendung ausgeführt wird.

	Sie sollten nun über eine ausführbare Datei für die Echo-Dienstanwendung namens "EchoService.exe" verfügen, die unter Ihrem Dienstprojektordner unter \\bin\\Debug\\EchoService.exe (für die Debugkonfiguration) oder \\bin\\Release\\EchoService.exe (für die Veröffentlichungskonfiguration) gespeichert ist. Doppelklicken Sie auf diese Datei, um die Dienstanwendung zu starten.

1. Ein Konsolenfenster wird geöffnet, und Sie werden aufgefordert, den Namespace anzugeben. Geben Sie in diesem Konsolenfenster den Dienstnamespace ein, und drücken Sie die EINGABETASTE.

1. Als Nächstes werden Sie aufgefordert, Ihren SAS-Schlüssel einzugeben. Geben Sie den SAS-Schlüssel ein, und drücken Sie die EINGABETASTE.

	Im Folgenden wird eine Beispielausgabe des Konsolenfensters gezeigt. Beachten Sie, dass die hier bereitgestellten Werte nur Beispiele sind.

	`Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

	Die Dienstanwendung wird gestartet und gibt die Adresse, an der sie lauscht, wie im folgenden Beispiel gezeigt im Konsolenfenster aus.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
    
1. Führen Sie die Clientanwendung aus. Sie sollten nun über eine ausführbare Datei für die Echo-Cientanwendung namens "EchoClient.exe" verfügen, die in Ihrem Clientprojektverzeichnis unter .\\bin\\Debug\\EchoClient.exe (für die Debugkonfiguration) oder .\\bin\\Release\\EchoClient.exe (für die Veröffentlichungskonfiguration) gespeichert ist. Doppelklicken Sie auf diese Datei, um die Clientanwendung zu starten.

1. Ein Konsolenfenster wird geöffnet, und Sie werden zur Eingabe der gleichen Informationen aufgefordert, die Sie zuvor für die Dienstanwendung eingegeben haben. Führen Sie die vorherigen Schritte aus, um die gleichen Werte für die Clientanwendung für den Dienstnamespace, den Ausstellernamen und den geheimen Ausstellerschlüssel einzugeben.

1. Nachdem Sie diese Werte eingegeben haben, öffnet der Client einen Kanal zum Dienst und fordert Sie auf, Text einzugeben, wie im folgenden Beispiel für die Konsolenausgabe gezeigt.

	`Enter text to echo (or [Enter] to exit):`

	Geben Sie Text ein, der an die Dienstanwendung gesendet werden soll, und drücken Sie die EINGABETASTE. Dieser Text wird über den Echo-Dienstvorgang an den Dienst gesendet und im Dienstkonsolenfenster ähnlich wie in der folgenden Beispielausgabe angezeigt.

	`Echoing: My sample text`

	Die Clientanwendung empfängt den Rückgabewert des `Echo`-Vorgangs (den ursprünglichen Text) und gibt diesen in ihrem Konsolenfenster aus. Im Folgenden wird eine Beispielausgabe des Clientkonsolenfensters gezeigt.

	`Server echoed: My sample text`

1. Sie können auf diese Weise weitere Textnachrichten vom Client an den Dienst senden. Wenn Sie fertig sind, drücken Sie die EINGABETASTE in den Client- und Dienstkonsolenfenstern, um beide Anwendungen zu beenden.

## Beispiel

Das folgende Beispiel zeigt, wie eine Clientanwendung erstellt wird, wie die Dienstvorgänge aufgerufen werden und wie der Client geschlossen wird, nachdem der Vorgangsaufruf abgeschlossen wurde.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

Stellen Sie sicher, dass der Dienst ausgeführt wird, bevor Sie den Client starten.

## Nächste Schritte

Dieses Tutorial zeigte das Erstellen einer Service Bus-Clientanwendung und eines Service Bus-Clientdiensts mithilfe der Service Bus-Relayfunktionen. Ein ähnliches Tutorial, in dem die [Brokermessagingfunktionen](service-bus-messaging-overview.md#Brokered-messaging) von Service Bus beschrieben werden, finden Sie unter [.NET-Tutorial zu Service Bus-Brokermessaging](service-bus-brokered-tutorial-dotnet.md).

Weitere Informationen zu Service Bus finden Sie in den folgenden Themen.

- [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md)
- [Service Bus – Grundlagen](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-Architektur](service-bus-architecture.md)

[klassische Azure-Portal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0218_2016-->