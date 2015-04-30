<properties 
	pageTitle="Hybride lokale/Cloudanwendung (.NET) - Azure" 
	description="Erfahren Sie, wie eine lokale/Cloud-.NET-Hybridanwendung mithilfe von Microsoft Azure Service Bus Relay erstellen." 
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
	ms.topic="hero-article" 
	ms.date="03/17/2015" 
	ms.author="sethm"/>






#Hybride lokale/Cloud-.NET-Anwendung mit Service Bus Relay

##Einführung

Die Entwicklung von Hybrid-Cloudanwendungen mit Microsoft Azure ist dank
Visual Studio 2013 und dem kostenlosen Azure SDK für .NET völlig unkompliziert. In diesem Leitfaden wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. In weniger als 30 Minuten verfügen Sie über eine Anwendung, die verschiedene Microsoft Azure-Ressourcen nutzt und aktiv in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen oder Anpassen eines vorhandenen Webdiensts zur Verwendung durch eine
    Weblösung
-   Verwenden von Azure Service Bus Relay zum Freigeben von Daten zwischen
    einer Azure-Anwendung und einem an einem anderen Ort gehosteten Webdienst

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

##Unterstützung hybrider Lösungen durch Service Bus Relay

Geschäftslösungen bestehen normalerweise aus einer Kombination von benutzerdefiniertem Code, der geschrieben wurde, um neue und individuelle Geschäftsanforderungen in Angriff zu nehmen und vorhandene Funktionen, die von bereits verwendeten Lösungen und Systemen bereitgestellt werden, zu integrieren.

Lösungsarchitekten setzen die Cloud inzwischen ein, um Skalierungsanforderungen leichter bewältigen zu können und die Betriebskosten zu senken. Dabei stellen sie fest, dass sich vorhandene Dienstressourcen, die sie als Bausteine für ihre Lösungen nutzen möchten, innerhalb der Unternehmensfirewall befinden und somit nicht problemlos von der Cloudlösung darauf zugegriffen werden kann. Viele interne Dienste werden nicht so erstellt oder gehostet, dass sie einfach am Rand des Unternehmensnetzwerks verfügbar gemacht werden können.

 *Service Bus Relay* wurde konzipiert, damit vorhandene
WCF (Windows Communication Foundation)-Webdienste sicher von Lösungen aufgerufen werden können, die sich außerhalb des Unternehmensumkreises befinden, ohne dass aufwändige Änderungen an der Netzwerkinfrastruktur des Unternehmens ausgeführt werden müssen. Service Bus Relay-Dienste dieser Art werden weiterhin innerhalb der vorhandenen Umgebung gehostet, das Lauschen auf eingehende Sitzungen und Anforderungen wird jedoch an den in der Cloud gehosteten Service Bus delegiert. Dieser schützt die Dienste durch die [Shared Access Signature](https://msdn.microsoft.com/library/dn170478.aspx)(SAS)-Authentifizierung außerdem vor unbefugtem Zugriff.

##Das Lösungszenario

In diesem Lernprogramm erstellen Sie eine ASP.NET MVC-Website, mit der Sie eine Produktliste auf der Produktbestandsseite anzeigen können.

![][0]

In diesem Lernprogramm wird davon ausgegangen, dass Sie über Produktinformationen in einem vorhandenen lokalen System verfügen, auf das über Service Bus Relay zugegriffen werden kann. Dies wird durch einen Webdienst simuliert, der in einer einfachen Konsolenanwendung ausgeführt und durch einen im Arbeitsspeicher befindlichen Produktsatz unterstützt wird. Sie können diese Konsolenanwendung auf Ihrem eigenen Computer ausführen und die Webrolle in Azure bereitstellen. Dabei werden Sie feststellen, dass die im Azure-Datencenter ausgeführte Webrolle tatsächlich Aufrufe an Ihren Computer sendet, obwohl dieser sich mit ziemlicher Sicherheit hinter mindestens einer Firewall und einer NAT (Network Address Translation)-Ebene befindet.

Im Folgenden wird ein Bildschirmfoto der Startseite der vollständigen Webanwendung dargestellt.

![][1]

##Einrichten der Entwicklungsumgebung

Bevor Sie mit der Entwicklung der Azure-Anwendung beginnen können, müssen Sie über die entsprechenden Tools verfügen und die Entwicklungsumgebung einrichten.

1.  Klicken Sie auf den folgenden Link, um das Azure SDK für .NET zu installieren:

    [Herunterladen von Tools und eines SDK][]

2. 	Klicken Sie für Ihre Visual Studio-Version auf **Installieren des SDK**. Die Schritte in diesem Lernprogramm verwenden Visual Studio 2013:

	![][42]

4.  Wenn Sie aufgefordert werden, das Installationsprogramm auszuführen oder zu speichern, klicken Sie auf **Ausführen**:

    ![][2]

5.  Klicken Sie im **Webplattform-Installer** auf **Installieren**, und setzen Sie die Installation fort:

    ![][3]

6.  Nach Abschluss der Installation haben Sie alles zur Hand, was
    Sie benötigen, um mit der Entwicklung zu beginnen. Das SDK enthält Tools, mit denen Sie
    schnell und einfach Azure-Anwendungen in Visual Studio entwickeln können. Wenn Sie
    Visual Studio nicht installiert haben, wird außerdem die kostenlose Version installiert,
    Visual Studio Express.

## Erstellen eines Dienstnamespaces

Um mit der Verwendung von Service Bus-Features in Azure beginnen zu können, müssen Sie zuerst einen Dienstnamespace erstellen. Ein Namespace ist ein Bereichscontainer für die Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung. 

Sie können Namespaces und Service Bus-Nachrichtenentitäten im [Azure-Verwaltungsportal][] oder im Server-Explorer von Visual Express verwalten, neue Namespaces lassen sich jedoch nur im Portal erstellen.

### So erstellen Sie einen Namespace im Portal:

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][] an.

2.  Klicken Sie im linken Navigationsbereich des Verwaltungsportals auf
    **Service Bus**.

3.  Klicken Sie im unteren Bereich des Verwaltungsportals auf **Erstellen**.   
    ![][5]

4.  Geben Sie im Dialogfeld **Neuen Namespace hinzufügen** einen Namen für den Namespace ein.
    Das System überprüft sofort, ob dieser Name verfügbar ist.   
    ![][6]

5.  Wählen Sie nach der Bestätigung, dass der Name für den Namespace verfügbar ist, das
    Land oder die Region, wo dieser Namespace gehostet werden soll. (Stellen
    Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der sie Ihre Rechnerressourcen
    einsetzen).

    WICHTIG: Wählen Sie **dieselbe Region** aus, in der Sie auch Ihre
    Anwendung einsetzen möchten. Dies sorgt für die beste Leistung.

6.	Lassen Sie die anderen Felder im Dialog bei ihren Standardwerten (**Messaging** und **Standardebene**), und klicken Sie anschließend auf das Häkchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Ggf. müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto durch das System bereitgestellt werden.

	![][38]

Der erstellte Namespace wird im Verwaltungsportal angezeigt, obwohl es einen Moment dauern kann, ihn zu aktivieren. Warten Sie, bis der Status **Aktiv** lautet, bevor Sie fortfahren.

## Abrufen der Standard-Anmeldeinformationen für den Namespace

Um Verwaltungsvorgänge für den neuen Namespace auszuführen, z. B. das Erstellen von Messaging-Entitäten, müssen Sie die Anmeldeinformationen für den Namespace abrufen.

1.  Klicken Sie im Hauptfenster auf den Namen Ihres Dienstnamespaces.   

	![][39]
  

2.  Klicken Sie auf **Verbindungsinformationen**.   

	![][40]


3.  Im Bereich **Zugriff auf die Verbindungsinformationen** finden Sie die Verbindungszeichenfolge, die den SAS-Schlüssel und den Schlüsselnamen enthält.   

	![][45]
    

4.  Notieren Sie diese Anmeldeinformationen, oder kopieren Sie sie in die Zwischenablage.

## Verwalten eines Servicenamespace mit dem Server-Explorer von Visual Studio

Führen Sie die [hier](http://http://msdn.microsoft.com/library/ff687127.aspx) im Abschnitt **Verbinden mit Azure in Visual Studio** beschriebenen Schritte aus, um Namespaces zu verwalten und Verbindungsinformationen über Visual Studio anstatt aus dem Verwaltungsportal abzurufen. Wenn Sie sich bei Azure anmelden, wird der Knoten **Service Bus** unterhalb der **Microsoft Azure**-Baumdarstellung im Server-Explorer automatisch mit allen Namespaces ausgefüllt, die Sie bereits erstellt haben. Klicken Sie mit der rechten Maustaste auf einen Namespace und klicken Sie anschließend auf **Eigenschaften**, um die Verbindungszeichenfolge und andere Metadaten dieses Namespace im Bereich **Eigenschaften** in Visual Studio anzuzeigen. 

![][44]

Notieren Sie sich den Wert **SharedAccessKey**, oder kopieren Sie ihn in die Zwischenablage.


## Erstellen eines lokalen Servers

Zunächst erstellen Sie ein (falsches) lokales Produktkatalogsystem. Dies ist relativ einfach; Sie können es sich als ein echtes lokales Produktkatalogsystem mit einer vollständigen Dienstoberfläche vorstellen, das integriert werden soll.

Zu Anfang ist das Projekt eine Visual Studio-Konsolenanwendung. Mithilfe des Service Bus NuGet-Pakets werden die Servicebusbibliotheken und -konfigurationseinstellungen hinzugefügt. Die NuGet Visual Studio-Erweiterung ermöglicht eine problemlose Installation und Aktualisierung von Bibliotheken und Tools in Visual Studio und Visual Studio Express. Das NuGet-Paket "Service Bus" stellt die einfachste Möglichkeit zum Abrufen der Service Bus-API und zum Konfigurieren der Anwendung mit allen Service Bus-Abhängigkeiten dar. Weitere Informationen zum Verwenden von NuGet und dem Servicebus-Paket finden Sie unter [Verwenden des NuGet Servicebus-Pakets][].

### Erstellen des Projekts

1.  Starten Sie Microsoft Visual
    Studio 2013 oder Microsoft Visual Studio Express mit Administratorberechtigungen. Zum
    Sie mit der rechten Maustaste auf
    **Microsoft Visual Studio 2013 (oder Microsoft Visual Studio Express)**, und klicken Sie dann auf **Als Administrator ausführen**.

2.  Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und dann
    auf **Projekt**.

    ![][10]

3.  Klicken Sie in **Installierte Vorlagen** unter **Visual C#** auf 
    **Konsolenanwendung**. Geben Sie im Feld **Name** den Namen
    **ProductsServer** ein:

    ![][11]

4.  Klicken Sie auf **OK**, um das Projekt **ProductsServer** zu erstellen.

5.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **ProductsServer** und dann
    auf **Eigenschaften**.

6.  Klicken Sie links auf die Registerkarte **Anwendung**, und stellen Sie sicher, dass die Option **.NET
    Framework 4** oder **.NET Framework 4.5** in der Dropdownliste **Zielframework:** angezeigt wird. Wählen Sie die Option andernfalls in der Dropdownliste aus, und klicken Sie auf **Ja**,
    wenn Sie aufgefordert werden, das Projekt erneut zu laden.

    ![][12]

7.  Wenn Sie den NuGet-Paket-Manager für Visual Studio bereits installiert haben, fahren Sie mit dem nächsten Schritt fort. Besuchen Sie andernfalls [NuGet][], und klicken Sie auf [Install NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Befolgen Sie die Anweisungen, um den NuGet-Paket-Manager zu installieren, und starten Sie Visual Studio dann neu.

7.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise** und dann auf
    **NuGet-Pakete verwalten**.

8.  Klicken Sie in der linken Spalte des NuGet-Dialogfelds auf **Online**.

9. 	Klicken Sie in der rechten Spalte auf das Feld **Suche**, geben Sie "**Service Bus**" ein, und wählen Sie das Element **Microsoft
    Azure Service Bus** aus. Klicken Sie auf **Installieren**, um die
    Installation abzuschließen, und schließen Sie das Dialogfeld.

    ![][13]

    Beachten Sie, dass nun auf die erforderlichen Clientassemblys verwiesen wird.

9.  Fügen Sie eine neue Klasse für den Produktvertrag hinzu. Klicken Sie im **Projektmappen-Explorer**
    mit der rechten Maustaste auf das Projekt **ProductsServer**, auf **Hinzufügen** und dann auf
    **Klasse**.

    ![][14]

10. Geben Sie in das Feld **Name** den Namen **ProductsContract.cs** ein. Geben
    Klicken Sie auf **Hinzufügen**.

11. Ersetzen Sie in der Datei **ProductsContract.cs** die Namespacedefinition durch
    den folgenden Code, mit dem der Vertrag für den Dienst definiert wird:

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

12. Ersetzen Sie in Program.cs die Namespacedefinition durch den folgenden
    Code, mit dem der Profildienst und der dafür vorgesehene Host hinzugefügt werden:

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {
                
                // Populate array of products for display on Website
                ProductData[] products = 
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock", 
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper", 
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors", 
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well", 
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application 
                // when the list of products is retrieved
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

13. Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei **App.config**,
    um sie im **Visual Studio**-Editor zu öffnen. Ersetzen Sie den Inhalt von
    **&lt;system.ServiceModel&gt;** durch den folgenden XML-Code. Ersetzen Sie
     *yourServiceNamespace* durch den Namen Ihres Dienstnamespaces
    und *yourKey* durch den Schlüssel, den Sie zuvor
    aus dem Azure-Verwaltungsportal abgerufen haben:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. Drücken Sie **F6**, oder klicken Sie im Menü **Build** auf **Projektmappe erstellen**,  um die Anwendung zu erstellen und die Richtigkeit Ihrer bisherigen Arbeit zu prüfen.

## Erstellen einer ASP.NET MVC-Anwendung

In diesem Abschnitt erstellen Sie eine einfache ASP.NET-Anwendung, in der von Ihrem Produktdienst abgerufene Daten angezeigt werden.

### Erstellen des Projekts

1.  Stellen Sie sicher, dass Visual Studio mit Administratorberechtigungen ausgeführt wird. Falls nicht, klicken
    Sie mit der rechten Maustaste auf
    **Microsoft Visual Studio 2013 (oder Microsoft Visual Studio Express)** und dann auf **Als Administrator ausführen**. Für den weiter unten beschriebenen Microsoft Azure-Serveremulator muss
    Visual Studio mit Administratorberechtigungen gestartet worden sein.

2.  Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und dann
    auf **Projekt**.

3.  Klicken Sie in **Installierte Vorlagen** unter **Visual C#** auf **ASP.NET-Webanwendung**. Nennen Sie das Projekt **ProductsPortal**. Geben
    Klicken Sie auf **OK**.

    ![][15]

4.  Klicken Sie in der Liste **Vorlage auswählen** auf **MVC**
    und dann auf **OK**.

    ![][16]

5.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Modelle**, auf **Hinzufügen**
    und dann auf **Klasse**. Geben Sie im Feld **Name** den Namen
    **Product.cs** ein. Klicken Sie anschließend auf **Hinzufügen**.

    ![][17]

### Ändern der Webanwendung

1.  Ersetzen Sie in der Datei Product.cs in Visual Studio die vorhandene
    Namespacedefinition durch den folgenden Code:

        // Declare properties for the products inventory
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  Ersetzen Sie in der Datei "HomeController.cs" in Visual Studio die vorhandene
    Namespacedefinition durch den folgenden Code:

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product> 
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  Erweitern Sie im **Projektmappen-Explorer** Views\Shared:

    ![][18]

4.  Doppelklicken Sie als Nächstes auf "Layout.cshtml", um die Datei im Visual Studio-Editor zu öffnen.

5.  Ändern Sie alle Vorkommnisse von **My ASP.NET Application** in **LITWARE's Products**.

6. Entfernen Sie die Links **Home**, **About** und **Contact**. Löschen Sie den hervorgehobenen Code:

	![][41]

7.  Erweitern Sie im **Projektmappen-Explorer** Views\Home:

    ![][20]

8.  Doppelklicken Sie auf Index.cshtml, um die Datei im Visual Studio-Editor zu öffnen.
    Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code:
	
		@model IEnumerable<ProductsWeb.Models.Product>

		@{
    		ViewBag.Title = "Index";
		}

		<h2>Prod Inventory</h2>

		<table>
    		<tr>
        		<th>
            		@Html.DisplayNameFor(model => model.Name)
        		</th>
                <th></th>
        		<th>
            		@Html.DisplayNameFor(model => model.Quantity)
        		</th>
    		</tr>
	
		@foreach (var item in Model) {
    		<tr>
        		<td>
            		@Html.DisplayFor(modelItem => item.Name)
        		</td>
        		<td>
            		@Html.DisplayFor(modelItem => item.Quantity)
        		</td>
    		</tr>	
		}

		</table>


9.  Drücken Sie **F6** oder
    **STRG+UMSCHALT+B** zum Erstellen des Projekts, um die Richtigkeit Ihrer bisherigen Arbeit zu prüfen.


### Lokales Ausführen der Anwendung

Führen Sie die Anwendung aus, um sicherzustellen, dass sie funktioniert.

1.  Stellen Sie sicher, dass **ProductsPortal** das aktive Projekt ist. Klicken Sie mit der rechten Maustaste auf
    den Projektnamen im **Projektmappen-Explorer**, und wählen Sie **Als
    Startprojekt festlegen** aus.
2.  Drücken Sie in **Visual Studio** die Taste **F5**.
3.  Die Anwendung sollte ausgeführt in einem Browser angezeigt werden:

    ![][21]

## Fertigstellen der Anwendung zur Bereitstellung für Azure

Sie können die Anwendung für einen Azure-Cloud-Dienst oder eine Azure-Website bereitstellen. Weitere Informationen zum Unterschied zwischen Websites und Cloud-Diensten finden Sie unter [Azure-Ausführungsmodelle][executionmodels]. Informationen zum Bereitstellen der Anwendung für eine Azure-Website finden Sie unter [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website](http://azure.microsoft.com/develop/net/tutorials/get-started/). Dieser Abschnitt enthält ausführliche Schritte für die Bereitstellung der Anwendung für einen Azure-Cloud-Dienst.

Zur Bereitstellung der Anwendung für einen Cloud-Dienst fügen Sie der Projektmappe ein Bereitstellungsprojekt für das Cloud-Dienstprojekt hinzu. Das Bereitstellungsprojekt enthält Konfigurationsinformationen, die benötigt werden, um die Anwendung ordnungsgemäß in der Cloud auszuführen.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **ProductsPortal**, um die Anwendung in der Cloud bereitstellen zu können. Klicken Sie auf **Konvertieren** und dann auf **Convert to Microsoft Azure Cloud Service Project**.

    ![][22]

2.  Drücken Sie **F5**, um die Anwendung zu testen.

3.  Der Azure-Serveremulator wird gestartet. Der Serveremulator
        nutzt den lokalen Computer, um die in Azure ausgeführte Anwendung
        zu emulieren. Sie können in der Taskleiste prüfen, ob
        der Emulator gestartet wurde:

       ![][23]

4.  Im Browser wird weiterhin die lokal ausgeführte Anwendung angezeigt.
        Die Darstellung und Funktion unterscheiden sich nicht von der Ausführung
        als reguläre ASP.NET MVC 4-Anwendung.

## Zusammensetzen der Einzelteile

Im nächsten Schritt wird der lokale Produktserver mit der ASP.NET MVC-Webanwendung zusammengeführt.

1.  Falls noch nicht geschehen, öffnen Sie das Projekt
        **ProductsPortal** in Visual Studio, das Sie im Abschnitt "Erstellen einer
        ASP.NET MVC-Anwendung" erstellt haben, erneut.

2.  Fügen Sie das NuGet-Paket den Projektverweisen hinzu, wie im Schritt "Erstellen eines lokalen Servers"
        beschrieben. Geben Sie in
        Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf
        **NuGet-Pakete verwalten**.

3.  Suchen Sie nach "Service Bus", und wählen Sie das Element **Microsoft Azure Service Bus** aus. Schließen Sie die Installation ab und schließen Sie das Dialogfeld.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsPortal**. Klicken Sie auf **Hinzufügen** und dann auf **Vorhandenes Element**.

5.  Navigieren Sie im Konsolenprojekt **ProductsServer** zur Datei **ProductsContract.cs**. Klicken Sie, um "ProductsContract.cs" zu markieren. Klicken Sie auf den Pfeil nach unten neben **Hinzufügen** und dann auf **Link hinzufügen**.

	![][24]

6.  Öffnen Sie nun die Datei **HomeController.cs** im Visual Studio-Editor, und ersetzen Sie die Namespace-Definition durch den folgenden Code. Ersetzen Sie *yourServiceNamespace* durch den Namen Ihres Dienstnamespaces und *yourKey* durch Ihren SAS-Schlüssel. Der Client kann daraufhin den lokalen Dienst aufrufen und das Ergebnis des Aufrufs zurückgeben.

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication 
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(), 
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior { 
                            TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                                "RootManageSharedAccessKey", "yourKey") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name, 
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }
7.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **ProductsPortal**,
        auf **Hinzufügen** und dann auf **Vorhandenes Projekt**.

8.  Navigieren Sie zum Projekt **ProductsServer**, und doppelklicken Sie dann auf
        die Projektmappendatei **ProductsServer.csproj**, um sie hinzuzufügen.

9.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **ProductsPortal**,
        und klicken Sie dann auf **Eigenschaften**.

10. Klicken Sie auf der linken Seite auf **Startprojekt**. Klicken Sie
        rechts auf **Mehrere Startprojekte**. Stellen Sie sicher, dass
        **ProductsServer**, **ProductsPortal.Azure** und
        **ProductsPortal** genau in dieser Reihenfolge angezeigt werden, wobei **Start** als
        Aktion für **ProductsServer** und **ProductsPortal.Azure**
        und **Keine** als Aktion für **ProductsPortal** festgelegt sein sollten. Weitere
        Beispiel:

      ![][25]

11. Klicken Sie im Dialogfeld "Eigenschaften" auf **ProjectDependencies** auf
        der linken Seite.

12. Klicken Sie in der Dropdownliste **Projekte** auf
        **ProductsServer**. Stellen Sie sicher, dass **ProductsPortal** deaktiviert
        und **ProductsPortal.Azure** aktiviert ist. Klicken Sie dann auf **OK**:

    ![][26]

## Ausführen der Anwendung

1.  Klicken Sie im Menü **Datei** in Visual Studio auf **Alle speichern**.

2.  Drücken Sie **F5**, um die Anwendung zu erstellen und auszuführen. Der lokale
        Server (die Konsolenanwendung **ProductsServer**) sollte zuerst gestartet
        werden, danach die Anwendung **ProductsWeb** in einem
        Browserfenster, wie im folgenden Screenshot abgebildet. Dieses Mal werden
        Sie feststellen, dass in der Produktbestandsliste Daten aus dem
        lokalen Produktdienstsystem aufgeführt werden.

    ![][1]

## Bereitstellen der Anwendung in Azure

1.  Klicken Sie mit der rechten Maustaste auf das Projekt **ProductsPortal** im 
        **Projektmappen-Explorer**, und klicken Sie dann auf **Publish to Microsoft Azure**.

2.  Sie müssen sich möglicherweise anmelden, damit alle Ihre Abonnements angezeigt werden.

    Klicken Sie auf **Anmelden, um mehr Abonnements anzuzeigen**:

    ![][27]

3.  Melden Sie sich mit Ihrem Microsoft-Konto an.


8.  Klicken Sie auf **Weiter**. Wenn Ihr Abonnement noch keine gehosteten
        Dienste enthält, werden Sie aufgefordert, einen zu erstellen. Der gehostete Dienst
        dient als Container für Ihre Anwendung in Ihrem 
        Speicherkonto zu erstellen. Geben Sie einen Namen für
        Ihre Anwendung ein, und wählen Sie die Region aus, für die die Anwendung
        optimiert werden soll. (Benutzer aus dieser Region können schnellere Ladezeiten
        beim Zugriff erwarten.)

9.  Wählen Sie den gehosteten Dienst aus, in dem Sie die Anwendung
        veröffentlichen möchten. Behalten Sie die unten angezeigten Standardwerte bei
        den restlichen Einstellungen bei. Klicken Sie auf **Weiter**:

    ![][33]

10. Klicken Sie auf der letzten Seite auf **Veröffentlichen**, um die Bereitstellung zu
        starten:

    ![][34]

Dies dauert etwa 5-7 Minuten. Da es sich hierbei
        um Ihre erste Veröffentlichung handelt, wird von Azure ein
        virtueller Computer (VM) bereitgestellt, eine Sicherheitshärtung durchgeführt, eine Webrolle
        auf der VM zum Hosten der Anwendung erstellt, der Code für die
        Webrolle bereitgestellt und schließlich der Lastenausgleich und
        das Netzwerk konfiguriert, um Ihre Anwendung öffentlich verfügbar zu machen.

11. Während der Veröffentlichung können Sie die
        Aktivität im Fenster **Azure-Aktivitätsprotokoll** überwachen -
        normalerweise unten in Visual Studio oder Visual Web
        Entwickler:

    ![][35]

12. Nach Abschluss der Bereitstellung können Sie die Website anzeigen,
        indem Sie auf den Link **Website-URL** im Überwachungsfenster klicken.

    ![][36]

    Die Website hängt vom lokalen Server ab, deshalb müssen Sie
        die Anwendung **ProductsServer** für die Website lokal ausführen,
        damit diese ordnungsgemäß funktioniert. Wenn Sie Anforderungen auf der Cloudwebsite
        durchführen, werden bei der lokalen Konsolenanwendung eingehende
        Anforderungen angezeigt, wie im folgenden Screenshot durch
        die Ausgabe "GetProducts called" angegeben.

    ![][37]

Weitere Informationen zum Unterschied zwischen Websites und Cloud-Diensten finden Sie unter [Azure-Ausführungsmodelle][executionmodels].

## Beenden und Löschen der Anwendung

Nach der Bereitstellung der Anwendung empfiehlt es sich, sie zu deaktivieren, damit Sie andere Anwendungen im Rahmen der kostenlosen 750 Stunden/Monat (31 Tage/Monat) Serverzeit erstellen und bereitstellen können.

Azure berechnet Webrolleninstanzen pro Stunde verbrauchter Serverzeit. Die Serverzeit wird verbraucht, sobald Ihre Anwendung bereitgestellt wurde, selbst wenn die Instanzen nicht ausgeführt werden und gestoppt wurden.
Ein kostenloses Konto umfasst 750 Stunden/Monat (31 Tage/Monat) dedizierte Serverzeit für virtuelle Computer zum Hosten dieser Webrolleninstanzen.

In den folgenden Schritten erfahren Sie, wie Sie die Anwendung beenden und löschen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie auf **Cloud Services** und dann auf den Namen des Diensts.

2.  Klicken Sie auf die Registerkarte **Dashboard** und dann auf **Beenden**, um die Anwendung vorübergehend anzuhalten. Durch
        Klicken auf "Start" können Sie sie erneut starten. Klicken Sie auf **Löschen**, um die Anwendung vollständig aus Azure zu löschen. Sie kann danach nicht wiederhergestellt werden.

	![][43]

## Nächste Schritte  

Weitere Informationen zum Service Bus finden Sie in den folgenden Ressourcen:  
  
* [Azure Service Bus][sbmsdn]  
* [Servicebus-Anleitungen][sbwacom]  
* [Einsatz von Servicebus-Warteschlangen][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Herunterladen von Tools und eines SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [Verwenden des NuGet Servicebus-Pakets]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  
  
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [44]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png
  [45]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/

<!--HONumber=52-->