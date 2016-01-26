<properties
   pageTitle="REST-Tutorial zu Service Bus-Relaymessaging | Microsoft Azure"
   description="Erstellen einer einfachen Service Bus Relay-Hostanwendung, die eine REST-basierte Schnittstelle bereitstellt."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="sethm" />

# Service Bus-REST-Lernprogramm

Dieses Lernprogramm beschreibt, wie Sie eine einfache Service Bus-Hostanwendung erstellen, die eine REST-basierte Schnittstelle verfügbar macht. REST ermöglicht einem Webclient, z. B. einem Webbrowser, den Zugriff auf die Service Bus-API über HTTP-Anforderungen.

Dieses Lernprogramm verwendet das WCF-REST-Programmiermodell (Windows Communication Foundation) zum Erstellen eines REST-Diensts für Service Bus. Weitere Informationen finden Sie unter [WCF-REST-Programmiermodell](https://msdn.microsoft.com/library/bb412169.aspx) und [Entwerfen und Implementieren von Diensten](https://msdn.microsoft.com/library/ms729746.aspx) in der WCF-Dokumentation.

## Schritt 1: Registrieren eines Azure-Kontos

Der erste Schritt umfasst die Einrichtung des Dienstnamespace und das Abrufen eines SAS-Schlüssels (Shared Access Signature). Ein Namespace stellt eine Anwendungsgrenze für jede Anwendung bereit, die über Service Bus zur Verfügung steht. Das System generiert automatisch einen SAS-Schlüssel, wenn ein Dienstnamespace erstellt wird. Dienstnamespace und SAS-Schlüssel bilden gemeinsam die Anmeldeinformationen, mit denen sich der Service Bus bei der Anwendung authentifiziert.

### So erstellen Sie einen Dienstnamespace und rufen einen SAS-Schlüssel an

1. Rufen Sie zum Erstellen eines Dienstnamespace das [klassische Azure-Portal][] auf. Klicken Sie auf der linken Seite auf **Service Bus** und anschließend auf **Erstellen**. Geben Sie einen Namen für den Namespace ein, und klicken Sie auf das Häkchen.

2. Klicken Sie im Hauptfenster des Portals auf den Namen des Dienstnamespace, den Sie im vorherigen Schritt erstellt haben.

3. Klicken Sie auf **Konfigurieren**, um die gemeinsam genutzten Zugriffsrichtlinien für Ihren Namespace anzuzeigen.

4. Notieren Sie den Primärschlüssel für die **RootManageSharedAccessKey**-Richtlinie, oder kopieren Sie ihn in die Zwischenablage. Sie verwenden diesen Wert später im Lernprogramm.

## Schritt 2: Definieren eines REST-basierten WCF-Dienstvertrags für die Verwendung mit Service Bus

Wie bei anderen Service Bus-Diensten müssen Sie den Vertrag definieren, wenn Sie einen Dienst im REST-Stil erstellen. Der Vertrag gibt an, welche Vorgänge der Host unterstützt. Ein Dienstvorgang ähnelt einer Webdienstmethode. Verträge werden durch die Definition einer C++-, C#- oder Visual Basic-Schnittstelle erstellt. Jede Methode in der Schnittstelle entspricht einem bestimmten Dienstvorgang. Das [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx)-Attribut muss auf jede Schnittstelle angewendet werden, und das [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx)-Attribut muss auf jeden Vorgang angewendet werden. Wenn eine Methode in einer Schnittstelle mit dem [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx)-Attribut kein [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx)-Attribut hat, wird diese Methode nicht bereitgestellt. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt.

Der Hauptunterschied zwischen einem grundlegenden Service Bus-Vertrag und einem Vertrag im REST-Stil ist das Hinzufügen einer Eigenschaft zum [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx)-Attribut: [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Mit dieser Eigenschaft können Sie eine Methode in der Schnittstelle einer Methode auf der anderen Seite der Schnittstelle zuordnen. In diesem Fall verwenden wir [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx), um eine Methode mit HTTP GET zu verknüpfen. Dadurch kann Service Bus Befehlen, die an die Schnittstelle gesendet werden, präzise abrufen und interpretieren.

### So erstellen Sie einen Service Bus-Vertrag mit einer Schnittstelle

1. Öffnen Sie Visual Studio als Administrator: Klicken Sie mit der rechten Maustaste im **Startmenü** auf das Programm, und klicken Sie dann auf Als **Administrator ausführen**.

2. Erstellen Sie ein neues Konsolenanwendungsprojekt. Klicken Sie auf das Menü **Datei**, wählen Sie **Neu** und dann **Projekt**. Klicken Sie im Dialogfeld **Neues Projekt** auf **Visual C#** (wenn **Visual C#** nicht angezeigt wird, suchen Sie unter **Andere Sprachen**), wählen Sie die Vorlage **Konsolenanwendung** aus, und nennen Sie sie **ImageListener**. Verwenden Sie den standardmäßigen **Speicherort**. Klicken Sie auf **OK**, um das Projekt zu erstellen.

3. Für ein C#-Projekt erstellt Visual Studio erstellt eine `Program.cs`-Datei. Diese Klasse enthält eine leere `Main()`-Methode, die erforderlich ist, damit ein Konsolenanwendungsprojekt ordnungsgemäß erstellt wird.

4. Hinzufügen eines Verweises auf **System.ServiceModel.dll** zum Projekt:

	a. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste unter dem Projektordner auf den Ordner **Verweise**, und klicken Sie dann auf **Verweis hinzufügen**.

	b. Klicken Sie im Dialogfeld **Verweis hinzufügen** auf die Registerkarte **.NET**, und führen Sie einen Bildlauf nach unten zu **System.ServiceModel** durch. Wählen Sie diese Option aus, und klicken Sie auf **OK**.

5. Wiederholen Sie den vorherigen Schritt zum Hinzufügen eines Verweises auf die **System.ServiceModel.Web.dll**-Assembly.

6. Fügen Sie `using`-Anweisungen für die Namespaces **System.ServiceModel**, **System.ServiceModel.Channels**, **System.ServiceModel.Web** und **System.IO** hinzu.

	```c
  	using System.ServiceModel;
  	using System.ServiceModel.Channels;
  	using System.ServiceModel.Web;
  	using System.IO;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) ist der Namespace, der den programmgesteuerten Zugriff auf die grundlegenden Funktionen von WCF ermöglicht. Service Bus verwendet viele Objekte und Attribute von WCF, um Dienstverträge zu definieren. Sie verwenden diesen Namespace in den meisten Ihrer Service Bus Relay-Anwendungen. Auf ähnliche Weise trägt [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) zur Definition des Kanals bei, der das Objekt darstellt, über das Sie mit Service Bus und dem Clientwebbrowser kommunizieren. Schließlich enthält [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) die Typen, mit denen Sie webbasierte Anwendungen erstellen können.

7. Benennen Sie den Namespace für das Programm vom Visual Studio-Standard in **Microsoft.ServiceBus.Samples** um.

 	```
	namespace Microsoft.ServiceBus.Samples
	{
		...
	```

8. Direkt nach der Namespacedeklaration definieren Sie eine neue Schnittstelle namens **IImageContract** und wenden das **ServiceContractAttribute**-Attribut auf die Schnittstelle mit einem Wert von `http://samples.microsoft.com/ServiceModel/Relay/` an. Der Namespacewert unterscheidet sich von dem Namespace, den Sie im gesamten Codebereich verwenden. Der Namespacewert dient als eindeutiger Bezeichner für diesen Vertrag und sollte über Versionsinformationen verfügen. Weitere Informationen finden Sie unter [Dienstversionsverwaltung](http://go.microsoft.com/fwlink/?LinkID=180498). Das explizite Angeben des Namespace verhindert, dass der Standardwert für den Namespace dem Vertragsnamen hinzugefügt wird.

	```
	[ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
	public interface IImageContract
	{
	}
	```

9. Deklarieren Sie innerhalb der `IImageContract`-Benutzeroberfläche eine Methode für den einzelnen Vorgang, den der `IImageContract`-Vertrag in der Schnittstelle bereitstellt, und wenden Sie das `OperationContractAttribute`-Attribut auf die Methode an, die Sie als Teil des öffentlichen Service Bus-Vertrags bereitstellen möchten.

	```
	public interface IImageContract
	{
		[OperationContract]
		Stream GetImage();
	}
	```

10. Wenden Sie neben dem **OperationContract**-Attribut auch das **WebGet**-Attribut an.

	```
	public interface IImageContract
	{
		[OperationContract, WebGet]
		Stream GetImage();
	}
	```

	Auf diese Weise kann Service Bus HTTP GET-Anforderungen an **GetImage** weiterleiten und die Rückgabewerte von **GetImage** in eine HTTP GETRESPONSE-Antwort übersetzen. Später in diesem Lernprogramm verwenden Sie einen Webbrowser für den Zugriff auf diese Methode und zum Anzeigen des Bilds im Browser.

11. Direkt nach der `IImageContract`-Definition deklarieren Sie einen Kanal, der von den beiden `IImageContract`- und `IClientChannel`-Schnittstellen erbt.

	```
	[ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	public interface IImageContract
	{
		[OperationContract, WebGet]
		Stream GetImage();
	}

	public interface IImageChannel : IImageContract, IClientChannel { }
	```

	Ein Kanal ist das WCF-Objekt, über das der Dienst und der Client gegenseitig Informationen übergeben. Später erstellen Sie den Kanal in Ihrer Hostanwendung. Service Bus verwendet dann diesen Kanal, um die HTTP GET-Anforderungen vom Browser an Ihre **GetImage**-Implementierung zu übergeben. Service Bus verwendet den Kanal auch, um den **GetImage**-Rückgabewert in eine HTTP GETRESPONSE für den Clientbrowser zu übersetzen.

12. Klicken Sie im Menü **Build** auf **Projektmappe erstellen**, um die Richtigkeit Ihrer bisherigen Arbeit zu überprüfen.

### Beispiel

Das folgende Codebeispiel zeigt eine Basisschnittstelle, die einen Service Bus-Vertrag definiert.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## Schritt 3: Implementieren eines REST-basierten WCF-Dienstvertrags für die Verwendung von Service Bus

Das Erstellen eines Service Bus-Diensts im REST-Stil erfordert, dass Sie zuerst den Vertrag erstellen, der über eine Schnittstelle definiert wird. Der nächste Schritt ist das Implementieren der Schnittstelle. Dies umfasst das Erstellen einer Klasse namens **ImageService**, die die benutzerdefinierte **IImageContract**-Schnittstelle implementiert. Nachdem Sie den Vertrag implementiert haben, konfigurieren Sie die Schnittstelle mithilfe einer "App.config"-Datei. Die Konfigurationsdatei enthält die erforderlichen Informationen für die Anwendung, wie z. B. den Namen des Diensts, den Namen des Vertrags und den Typ des Protokolls, das für die Kommunikation mit Service Bus verwendet wird. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt.

Wie bei den vorherigen Schritten gibt es nur wenige Unterschiede zwischen der Implementierung eines Vertrags im REST-Stil und eines grundlegenden Service Bus-Vertrags.

### So implementieren Sie einen Service Bus-Vertrag im REST-Stil

1. Erstellen Sie direkt nach der Definition der **IImageContract**-Schnittstelle eine neue Klasse namens **ImageService**. Die **ImageService**-Klasse implementiert die **IImageContract**-Schnittstelle.

	```
	class ImageService : IImageContract
	{
	}
	```
	Ähnlich wie bei anderen Schnittstellenimplementierungen können Sie die Definition in einer anderen Datei implementieren. In diesem Tutorial findet die Implementierung allerdings in derselben Datei wie die Schnittstellendefinition und die `Main()`-Methode statt.

2. Wenden Sie das [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx)-Attribut auf die **IImageService**-Klasse an, um anzugeben, dass die Klasse eine Implementierung eines WCF-Vertrags ist.

	```
	[ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class ImageService : IImageContract
	{
	}
	```

	Wie bereits erwähnt, ist dieser Namespace kein herkömmlicher Namespace. Stattdessen ist es Teil der WCF-Architektur, die den Vertrag identifiziert. Weitere Informationen finden Sie im Thema [Datenvertragsnamen](https://msdn.microsoft.com/library/ms731045.aspx) in der WCF-Dokumentation.

3. Fügen Sie dem Projekt ein JPG-Bild hinzu.

	Dies ist ein Bild, das der Dienst im empfangenden Browser anzeigt. Klicken Sie mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Hinzufügen**. Klicken Sie anschließend auf **Vorhandenes Element**. Verwenden Sie das Dialogfeld **Vorhandenes Element hinzufügen**, um zu einer geeigneten JPG-Datei zu navigieren, und klicken Sie auf **Hinzufügen**.

	Stellen Sie beim Hinzufügen der Datei sicher, dass in der Dropdownliste neben dem Feld **Dateiname** die Option **Alle Dateien** ausgewählt ist. Im Rest dieses Lernprogramms wird davon ausgegangen, dass der Name des Bilds "image.jpg" lautet. Wenn Sie eine andere Datei haben, müssen Sie das Bild umbenennen oder den Code entsprechend ändern.

4. Um sicherzustellen, dass der ausgeführte Dienst die Bilddatei finden kann, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Bilddatei. Legen Sie im Bereich **Eigenschaften** den Wert für **In Ausgabeverzeichnis kopieren** auf **Kopieren, wenn neuer** fest.

5. Fügen Sie dem Projekt Verweise zu den Assemblys **System.Drawing.dll**, **System.Runtime.Serialization.dll** und **Microsoft.ServiceBus.dll** hinzu, und fügen Sie auch die folgenden zugeordneten `using`-Anweisungen hinzu.

	```
	using System.Drawing;
	using System.Drawing.Imaging;
	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Web;
	```

6. Fügen Sie in der **ImageService**-Klasse den folgenden Konstruktor hinzu, der die Bitmap lädt und das Senden an den Clientbrowser vorbereitet.

	```
	class ImageService : IImageContract
	{
		const string imageFileName = "image.jpg";

		Image bitmap;

		public ImageService()
		{
			this.bitmap = Image.FromFile(imageFileName);
		}
	}
	```

7. Fügen Sie direkt nach dem vorherigen Code die folgenden **GetImage**-Methode in der **ImageService**-Klasse hinzu, um eine HTTP-Nachricht mit dem Bild zurückzugeben.

	```
	public Stream GetImage()
	{
		MemoryStream stream = new MemoryStream();
		this.bitmap.Save(stream, ImageFormat.Jpeg);

		stream.Position = 0;
		WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

		return stream;
	}
	```

	Diese Implementierung verwendet **MemoryStream** zum Abrufen des Bilds sowie zum Vorbereiten des Streamings an den Browser. Sie startet die Streamposition bei null, deklariert den Streaminhalt als JPEG und streamt dann die Informationen.

8. Klicken Sie im Menü **Build** auf **Projektmappe erstellen**.

### So definieren Sie die Konfiguration zum Ausführen des Webdiensts auf Service Bus

1. Klicken Sie mit der rechten Maustaste auf das **ImageListener**-Projekt. Klicken Sie dann auf **Hinzufügen** und auf **Neues Element**.

2. Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei **App.config**, die derzeit die folgenden XML-Elemente enthält:

	```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	</configuration>
	```

	Die Konfigurationsdatei entspricht einer WCF-Konfigurationsdatei und enthält den Dienstnamen, den Endpunkt (der Speicherort, den Service Bus für Clients und Hosts für die Kommunikation bereitstellt) und die Bindung (den Typ des Protokolls, das für die Kommunikation verwendet wird). Der Hauptunterschied besteht hier darin, dass der konfigurierte Dienstendpunkt auf eine [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx)-Bindung verweist, die nicht Teil von .NET Framework ist. Weitere Informationen zum Konfigurieren einer Service Bus-Anwendung finden Sie unter [Konfigurieren eines WCF-Diensts für die Registrierung mit Service Bus](https://msdn.microsoft.com/library/ee173579.aspx).


3. Fügen Sie der Datei "App.config" ein `<system.serviceModel>`-XML-Element hinzu. Dies ist ein WCF-Element, das einen oder mehrere Dienste definiert. Hier wird es verwendet, um den Dienstnamen und den Endpunkt zu definieren.

	```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<system.serviceModel>

		</system.serviceModel>

	</configuration>
	```

4. Fügen Sie innerhalb des `system.serviceModel`-Elements ein `<bindings>`-Element hinzu, das den folgenden Inhalt aufweist. Dies definiert die in der Anwendung verwendeten Bindungen. Sie können mehrere Bindungen definieren, aber in diesem Lernprogramm definieren Sie nur eine.

	```
	<bindings>
		<!-- Application Binding -->
		<webHttpRelayBinding>
			<binding name="default">
				<security relayClientAuthenticationType="None" />
			</binding>
		</webHttpRelayBinding>
	</bindings>
	```

	Dieser Schritt definiert eine Service Bus-[WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx)-Bindung mit auf **None** festgelegtem **relayClientAuthenticationType**. Diese Einstellung gibt an, dass ein Endpunkt mit dieser Bindung keine Clientanmeldeinformationen erfordert.

5. Fügen Sie nach dem `<bindings>`-Element ein `<services>`-Element hinzu. Ähnlich wie Bindungen können Sie mehrere Dienste in einer einzigen Konfigurationsdatei definieren. Für dieses Lernprogramm definieren Sie aber nur einen.

	```
	<services>
		<!-- Application Service -->
		<service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
			<endpoint name="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IImageContract"
					binding="webHttpRelayBinding"
					bindingConfiguration="default"
					behaviorConfiguration="sbTokenProvider"
					address="" />
		</service>
	</services>
	```

	Dieser Schritt konfiguriert einen Dienst, der die zuvor definierte standardmäßige **WebHttpRelayBinding**-Bindung verwendet. Darüber hinaus verwendet er den standardmäßigen **SbTokenProvider**-Anbieter, die im nächsten Schritt definiert wird.

6. Erstellen Sie nach dem `<services>`-Element ein `<behaviors>`-Element mit dem folgenden Inhalt, der „SAS\_KEY“ durch den *Shared Access Signature*-Schlüssel (SAS) ersetzt, den Sie in Schritt 1 aus dem [klassischen Azure-Portal][] abgerufen haben.

	```
	<behaviors>
		<endpointBehaviors>
			<behavior name="sbTokenProvider">
				<transportClientEndpointBehavior>
					<tokenProvider>
						<sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
					</tokenProvider>
				</transportClientEndpointBehavior>
			</behavior>
			</endpointBehaviors>
			<serviceBehaviors>
				<behavior name="default">
					<serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
				</behavior>
			</serviceBehaviors>
	</behaviors>
	```

7. Klicken Sie im Menü **Build** auf **Projektmappe erstellen**, um die gesamte Projektmappe zu erstellen.

### Beispiel

Der folgende Code zeigt die Vertrags- und Dienstimplementierung für einen REST-basierten Dienst, der mit der **WebHttpRelayBinding**-Bindung auf dem Service Bus ausgeführt wird.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Das folgende Beispiel zeigt die "App.config"-Datei, die dem Dienst zugeordnet ist.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <bindings>
      <!-- Application Binding -->
      <webHttpRelayBinding>
        <binding name="default">
          <!-- Turn off client authentication so that client does not need to present credential through browser or fiddler -->
          <security relayClientAuthenticationType="None" />
        </binding>
      </webHttpRelayBinding>
    </bindings>

    <services>
      <!-- Application Service -->
      <service name="Microsoft.ServiceBus.Samples.ImageService"
               behaviorConfiguration="default">
        <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
      </service>
    </services>

    <behaviors>
      <endpointBehaviors>
        <behavior name="sbTokenProvider">
          <transportClientEndpointBehavior>
            <tokenProvider>
              <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
            </tokenProvider>
          <transportClientEndpointBehavior>
        </behavior>
      </endpointBehaviors>
      <serviceBehaviors>
        <behavior name="default">
          <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
        </behavior>
      </serviceBehaviors>
    </behaviors>

  </system.serviceModel>
</configuration>
```

## Schritt 4: Hosten des REST-basierten WCF-Diensts zur Verwendung von Service Bus

Dieser Schritt beschreibt, wie ein Webdienst mithilfe einer Konsolenanwendung auf Service Bus ausgeführt wird. Eine vollständige Liste des Programmcodes, der in diesem Schritt geschrieben wird, wird in dem Beispiel nach dem Verfahren bereitgestellt.

### So erstellen Sie eine Basisadresse für den Dienst

1. Erstellen Sie in der `Main()`-Funktionsdeklaration eine Variable zum Speichern des Namespace Ihres Service Bus-Projekts.

	```
	string serviceNamespace = "InsertServiceNamespaceHere";
	```
	Service Bus verwendet den Namespace-Namen, um einen eindeutigen URI zu erstellen.

2. Erstellen Sie eine `Uri`-Instanz für die Basisadresse des Diensts, der auf dem Namespace basiert.

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
	```

### So erstellen und konfigurieren Sie den Webdiensthost

- Erstellen Sie den Webdiensthost mithilfe der URI-Adresse, die Sie weiter oben in diesem Abschnitt erstellt haben.

	```
	WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
	```
	Der Diensthost ist das WCF-Objekt, das die Hostanwendung instanziiert. In diesem Beispiel übergeben Sie den Typ des Hosts, den Sie erstellen möchten (**ImageService**), sowie die Adresse, an der Sie die Hostanwendung bereitstellen möchten.

### So führen Sie den Webdiensthost aus

1. Öffnen Sie den Dienst.

	```
	host.Open();
	```
	Der Dienst wird jetzt ausgeführt.

2. Zeigen Sie eine Meldung an, die angibt, dass der Dienst ausgeführt wird und wie er beendet wird.

	```
	Console.WriteLine("Copy the following address into a browser to see the image: ");
	Console.WriteLine(address + "GetImage");
	Console.WriteLine();
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

3. Schließen Sie danach den Diensthost.

	```c
	host.Close();
	```

## Beispiel

Das folgende Beispiel schließt den Dienstvertrag und die Implementierung aus früheren Schritten des Lernprogramms ein und hostet den Dienst in einer Konsolenanwendung. Kompilieren Sie den folgenden Code in eine ausführbare Datei namens „ImageListener.exe“.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### Kompilieren des Codes

Führen Sie nach dem Erstellen der Projektmappe Folgendes aus, um die Anwendung auszuführen:

1. Führen Sie den Dienst an einer Eingabeaufforderung aus (ImageListener\bin\Debug\ImageListener.exe).

2. Kopieren Sie die Adresse aus der Eingabeaufforderung in einen Browser, um das Bild anzuzeigen.

## Nächste Schritte

Da Sie jetzt eine Anwendung erstellt haben, die den Service Bus Relay-Dienst verwendet, finden Sie in den folgenden Artikeln weitere Informationen zum Relaymessaging:

- [Übersicht über die Architektur von Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md#relays)

- [Verwenden des Service Bus Relay-Diensts](service-bus-dotnet-how-to-use-relay.md)

[klassische Azure-Portal]: http://manage.windowsazure.com
[klassischen Azure-Portal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0121_2016-->