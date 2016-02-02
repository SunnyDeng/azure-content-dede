<properties
	pageTitle="Hybride lokale/Cloudanwendung (.NET) | Microsoft Azure"
	description="Erfahren Sie, wie eine lokale/Cloud-.NET-Hybridanwendung mithilfe von Microsoft Azure Service Bus Relay erstellen."
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
	ms.topic="get-started-article"
	ms.date="10/07/2015"
	ms.author="sethm"/>

# Hybride lokale/Cloud-.NET-Anwendung mit Azure Service Bus Relay

## Einführung

Die Entwicklung hybrider Cloudanwendungen mit Microsoft Azure ist dank Visual Studio 2013 und dem kostenlosen Azure SDK für .NET völlig unkompliziert. In diesem Artikel wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. In weniger als 30 Minuten verfügen Sie über eine Anwendung, die verschiedene Microsoft Azure-Ressourcen nutzt und aktiv in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen oder Anpassen eines vorhandenen Webdiensts zur Verwendung durch eine Weblösung
-   Verwenden von Azure Service Bus Relay zum Freigeben von Daten zwischen einer Azure-Anwendung und einem an einem anderen Ort gehosteten Webdienst

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Unterstützung hybrider Lösungen durch Service Bus Relay

Geschäftslösungen bestehen normalerweise aus einer Kombination von benutzerdefiniertem Code, der geschrieben wurde, um neue und individuelle Geschäftsanforderungen in Angriff zu nehmen und vorhandene Funktionen, die von bereits verwendeten Lösungen und Systemen bereitgestellt werden, zu integrieren.

Lösungsarchitekten setzen die Cloud inzwischen ein, um Skalierungsanforderungen leichter bewältigen zu können und die Betriebskosten zu senken. Dabei stellen sie fest, dass sich vorhandene Dienstressourcen, die sie als Bausteine für ihre Lösungen nutzen möchten, innerhalb der Unternehmensfirewall befinden und somit nicht problemlos von der Cloudlösung darauf zugegriffen werden kann. Viele interne Dienste werden nicht so erstellt oder gehostet, dass sie einfach am Rand des Unternehmensnetzwerks verfügbar gemacht werden können.

Service Bus Relay wurde konzipiert, damit vorhandene WCF (Windows Communication Foundation)-Webdienste sicher von Lösungen aufgerufen werden können, die sich außerhalb des Unternehmensumkreises befinden, ohne dass aufwändige Änderungen an der Netzwerkinfrastruktur des Unternehmens ausgeführt werden müssen. Service Bus Relay-Dienste dieser Art werden weiterhin in der vorhandenen Umgebung gehostet, das Überprüfen auf eingehende Sitzungen und Anforderungen wird jedoch an den in der Cloud gehosteten Servicebus delegiert. Dieser schützt die Dienste durch die SAS-Authentifizierung ([Shared Access Signature](https://msdn.microsoft.com/library/dn170478.aspx)) außerdem vor unbefugten Zugriffen.

## Lösungszenario

In diesem Lernprogramm erstellen Sie eine ASP.NET MVC-Website, mit der Sie eine Produktliste auf der Produktbestandsseite anzeigen können.

![][0]

In diesem Lernprogramm wird davon ausgegangen, dass Sie über Produktinformationen in einem vorhandenen lokalen System verfügen, auf das über Service Bus Relay zugegriffen werden kann. Dies wird durch einen Webdienst simuliert, der in einer einfachen Konsolenanwendung ausgeführt und durch einen im Arbeitsspeicher befindlichen Produktsatz unterstützt wird. Sie können diese Konsolenanwendung auf Ihrem eigenen Computer ausführen und die Webrolle in Azure bereitstellen. Dabei werden Sie feststellen, dass die im Azure-Datencenter ausgeführte Webrolle tatsächlich Aufrufe an Ihren Computer sendet, obwohl dieser sich mit ziemlicher Sicherheit hinter mindestens einer Firewall und einer NAT (Network Address Translation)-Ebene befindet.

Im Folgenden wird ein Bildschirmfoto der Startseite der vollständigen Webanwendung dargestellt.

![][1]

## Einrichten der Entwicklungsumgebung

Bevor Sie mit der Entwicklung der Azure-Anwendung beginnen können, müssen Sie über die entsprechenden Tools verfügen und die Entwicklungsumgebung einrichten.

1.  Installieren Sie das Azure-SDK für .NET auf [Tools und SDK herunterladen][].

2. 	Klicken Sie für Ihre Visual Studio-Version auf **Installieren des SDK**. Für die Schritte in diesem Lernprogramm wird Visual Studio 2013 verwendet.

	![][42]

4.  Wenn Sie aufgefordert werden, das Installationsprogramm auszuführen oder zu speichern, klicken Sie auf **Ausführen**.

    ![][2]

5.  Klicken Sie im **Webplattform-Installer** auf **Installieren**, und setzen Sie die Installation fort.

    ![][3]

6.  Nach Abschluss der Installation haben Sie alles zur Hand, was Sie benötigen, um mit der Entwicklung der App zu beginnen. Das SDK enthält Tools, mit denen Sie ganz leicht Azure-Anwendungen in Visual Studio entwickeln können. Wenn Visual Studio nicht installiert ist, wird außerdem die kostenlose Version Visual Studio Express vom SDK installiert.

## Erstellen eines Dienstnamespaces

Um mit der Verwendung von Service Bus-Features in Azure beginnen zu können, müssen Sie zuerst einen Dienstnamespace erstellen. Ein Namespace ist ein Bereichscontainer für die Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung.

Sie können Namespaces und Service Bus-Nachrichtenentitäten im [klassischen Azure-Portal][] oder im Server-Explorer von Visual Studio verwalten. Neue Namespaces lassen sich jedoch nur im Portal erstellen.

### Erstellen eines Namespace im klassischen Azure-Portal:

1.  Melden Sie sich am [klassischen Azure-Portal][] an.

2.  Klicken Sie im linken Navigationsbereich des Portals auf **Service Bus**.

3.  Klicken Sie im unteren Bereich des Portals auf **Erstellen**.

    ![][5]

4.  Geben Sie im Dialogfeld **Neuen Namespace hinzufügen** einen Namen für den Namespace ein. Das System prüft sofort, ob dieser Name verfügbar ist.
	![][6]

5.  Wählen Sie nach der Bestätigung, dass der Name für den Namespace verfügbar ist, das Land oder die Region, wo dieser Namespace gehostet werden soll. (Stellen Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der sie Ihre Rechnerressourcen einsetzen.)

    > [AZURE.IMPORTANT] Wählen Sie *dieselbe Region*, in der Sie auch Ihre Anwendung einsetzen möchten. Dies sorgt für die beste Leistung.

6.	Übernehmen Sie für die weiteren Felder im Dialogfeld die Standardwerte (**Messaging** und **Standardstufe**), und klicken Sie anschließend auf das Häkchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Ggf. müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto durch das System bereitgestellt werden.

	![][38]

Der erstellte Namespace wird im klassischen Azure-Portal angezeigt, obwohl bis zu seiner Aktivierung einen Moment dauern kann. Warten Sie, bis der Status **Aktiv** lautet, bevor Sie fortfahren.

## Abrufen der Standard-Anmeldeinformationen für den Namespace

Um Verwaltungsvorgänge für den neuen Namespace auszuführen, z. B. das Erstellen von Messaging-Entitäten, müssen Sie die Anmeldeinformationen für den Namespace abrufen.

1.  Klicken Sie im Hauptfenster auf den Namen Ihres Dienstnamespaces.

	![][39]

2.  Klicken Sie auf **Verbindungsinformationen**.

	![][40]

3.  Im Bereich **Zugriff auf die Verbindungsinformationen** finden Sie die Verbindungszeichenfolge, die den SAS-Schlüssel und den Schlüsselnamen enthält.

	![][45]

4.  Notieren Sie diese Anmeldeinformationen, oder kopieren Sie sie in die Zwischenablage.

## Erstellen eines lokalen Servers

Zunächst erstellen Sie ein (falsches) lokales Produktkatalogsystem. Dies ist relativ einfach; Sie können es sich als ein echtes lokales Produktkatalogsystem mit einer vollständigen Dienstoberfläche vorstellen, das integriert werden soll.

Zu Anfang ist das Projekt eine Visual Studio-Konsolenanwendung. Mithilfe des Service Bus NuGet-Pakets werden die Servicebusbibliotheken und -konfigurationseinstellungen hinzugefügt. Die NuGet Visual Studio-Erweiterung ermöglicht eine problemlose Installation und Aktualisierung von Bibliotheken und Tools in Visual Studio und Visual Studio Express. Das Service Bus NuGet-Paket stellt die einfachste Möglichkeit zum Abrufen der Service Bus-API und Konfigurieren der Anwendung mit allen Service Bus-Abhängigkeiten dar. Weitere Informationen zum Verwenden von NuGet und dem Service Bus-Paket finden Sie unter [Verwenden des NuGet Service Bus-Pakets][].

### Erstellen des Projekts

1.  Starten Sie entweder Microsoft Visual Studio 2013 oder Microsoft Visual Studio Express mit Administratorrechten. Um Visual Studio mit Administratorrechten zu starten, klicken Sie mit der rechten Maustaste auf **Microsoft Visual Studio 2013 (oder Microsoft Visual Studio Express)** und klicken Sie anschließend auf **Als Administrator ausführen**.

2.  Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.

    ![][10]

3.  Klicken Sie in **Installierte Vorlagen** unter **Visual C#** auf **Konsolenanwendung**. Geben Sie in das Feld **Name** den Namen **ProductsServer** ein:

    ![][11]

4.  Klicken Sie auf **OK**, um das Projekt **ProductsServer** zu erstellen.

5.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **ProductsServer**, und klicken Sie dann auf **Eigenschaften**.

6.  Klicken Sie links auf die Registerkarte **Anwendung**, und stellen Sie dann sicher, dass in der Liste **Zielframework** die Option **.NET Framework 4** oder **.NET Framework 4.5** angezeigt wird. Falls dies nicht der Fall ist, wählen Sie diese Option aus der Liste aus, und klicken Sie dann auf **Ja**, wenn Sie aufgefordert werden, das Projekt erneut zu laden.

    ![][12]

7.  Wenn Sie den NuGet-Paket-Manager für Visual Studio bereits installiert haben, fahren Sie mit dem nächsten Schritt fort. Besuchen Sie andernfalls [NuGet][], und klicken Sie auf [Install NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Befolgen Sie die Anweisungen, um den NuGet-Paket-Manager zu installieren, und starten Sie Visual Studio dann neu.

7.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

8.  Klicken Sie in der linken Spalte des Dialogfelds **NuGet** auf **Online**.

9. 	Klicken Sie in der rechten Spalte auf das Feld **Search**, geben Sie "**Service Bus**" ein, und wählen Sie dann das Element **Microsoft Azure Service Bus** aus. Klicken Sie auf **Installieren**, um die Installation abzuschließen. Schließen Sie danach dieses Dialogfeld.

    ![][13]

    Beachten Sie, dass nun auf die erforderlichen Clientassemblys verwiesen wird.

9.  Fügen Sie eine neue Klasse für den Produktvertrag hinzu. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsServer**. Klicken Sie auf **Hinzufügen**, und dann auf **Klasse**.

    ![][14]

10. Geben Sie in das Feld **Name** den Namen **ProductsContract.cs** ein. Klicken Sie anschließend auf **Hinzufügen**.

11. Ersetzen Sie in **ProductsContract.cs** die Namespacedefinition durch den folgenden Code, mit dem der Vertrag für den Dienst definiert wird.

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties.
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

12. Ersetzen Sie in "Program.cs" die Namespacedefinition durch den folgenden Code, mit dem der Profildienst und der dafür vorgesehene Host hinzugefügt werden.

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface.
            class ProductsService : IProducts
            {

                // Populate array of products for display on website.
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
                // when the list of products is retrieved.
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application.
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

13. Doppelklicken Sie im Projektmappen-Explorer auf die Datei **App.config**, um sie im Visual Studio-Editor zu öffnen. Ersetzen Sie den Inhalt von **&lt;system.ServiceModel&gt;** durch den folgenden XML-Code. Achten Sie darauf, *yourServiceNamespace* durch den Namen Ihres Dienstnamespace und *yourKey* durch den SAS-Schlüssel zu ersetzen, den Sie zuvor aus dem klassischen Azure-Portal abgerufen haben:

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

14. Drücken Sie F6, oder klicken Sie im Menü **Build** auf **Projektmappe erstellen**, um die Anwendung zu erstellen und die Richtigkeit Ihrer bisherigen Arbeit zu prüfen.

## Erstellen einer ASP.NET MVC-Anwendung

In diesem Abschnitt erstellen Sie eine einfache ASP.NET-Anwendung, in der von Ihrem Produktdienst abgerufene Daten angezeigt werden.

### Erstellen des Projekts

1.  Stellen Sie sicher, dass Visual Studio mit Administratorberechtigungen ausgeführt wird. Falls dies nicht der Fall ist, klicken Sie zum Starten von Visual Studio mit Administratorrechten mit der rechten Maustaste auf **Microsoft Visual Studio 2013 (oder Microsoft Visual Studio Express)** und klicken Sie anschließend auf **Als Administrator ausführen**. Für den ebenfalls in diesem Artikel behandelten Microsoft Azure-Serveremulator muss Visual Studio mit Administratorrechten gestartet werden.

2.  Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.

3.  Klicken Sie in **Installierte Vorlagen** unter **Visual C#** auf **ASP.NET-Webanwendung**. Nennen Sie das Projekt **ProductsPortal**. Klicken Sie dann auf **OK**.

    ![][15]

4.  Klicken Sie in der Liste **Vorlage auswählen** auf **MVC**, und dann auf **OK**.

    ![][16]

5.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Modelle**, und klicken Sie anschließend auf **Hinzufügen** und dann auf **Klasse**. Geben Sie in das Feld **Name** den Namen **Product.cs** ein. Klicken Sie anschließend auf **Hinzufügen**.

    ![][17]

### Ändern der Webanwendung

1.  Ersetzen Sie in der Datei "Product.cs" in Visual Studio die vorhandene Namespacedefinition durch den folgenden Code.

        // Declare properties for the products inventory.
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  Ersetzen Sie in der Datei "HomeController.cs" in Visual Studio die vorhandene Namespacedefinition durch den folgenden Code.

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory.
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product>
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  Erweitern Sie im Projektmappen-Explorer den Ordner „Views\\Shared“:

    ![][18]

4.  Doppelklicken Sie auf **\_Layout.cshtml**, um die Datei im Visual Studio-Editor zu öffnen.

5.  Ändern Sie alle Vorkommnisse von **My ASP.NET Application** in **LITWARE's Products**.

6. Entfernen Sie die Links **Home**, **About** und **Contact**. Löschen Sie im folgenden Beispiel den hervorgehobenen Code.

	![][41]

7.  Erweitern Sie im Projektmappen-Explorer den Ordner „Views\\Home“:

    ![][20]

8.  Doppelklicken Sie auf **Index.cshtml**, um die Datei im Visual Studio-Editor zu öffnen. Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code.

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

9.  Drücken Sie **F6** oder **STRG+UMSCHALT+B** zum Erstellen des Projekts, um die Richtigkeit Ihrer bisherigen Arbeit zu prüfen.


### Lokales Ausführen der Anwendung

Führen Sie die Anwendung aus, um sicherzustellen, dass sie funktioniert.

1.  Stellen Sie sicher, dass **ProductsPortal** das aktive Projekt ist. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Als Startprojekt festlegen** aus.
2.  Drücken Sie in **Visual Studio** die Taste F5.
3.  Die Anwendung sollte ausgeführt in einem Browser angezeigt werden.

    ![][21]

## Fertigstellen der Anwendung zur Bereitstellung für Azure

Sie können die Anwendung für einen Azure-Cloud-Dienst oder eine Azure-Website bereitstellen. Weitere Informationen zum Unterschied zwischen Websites und Cloud-Diensten finden Sie unter [Azure-Ausführungsmodelle][executionmodels]. Informationen zum Bereitstellen der Anwendung für eine Azure-Website finden Sie unter [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website](https://azure.microsoft.com/develop/net/tutorials/get-started/). Dieser Abschnitt enthält ausführliche Schritte für die Bereitstellung der Anwendung für einen Azure-Cloud-Dienst.

Zur Bereitstellung der Anwendung für einen Cloud-Dienst fügen Sie der Projektmappe ein Bereitstellungsprojekt für das Cloud-Dienstprojekt hinzu. Das Bereitstellungsprojekt enthält Konfigurationsinformationen, die benötigt werden, um die Anwendung ordnungsgemäß in der Cloud auszuführen.

1.  Um die Anwendung in der Cloud bereitstellen zu können, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsPortal**,klicken Sie auf **Konvertieren** und dann auf **Konvertieren zu Microsoft Azure-Clouddienstprojekt**.

    ![][22]

2.  Drücken Sie F5, um die Anwendung zu testen.

3.  Der Azure-Serveremulator wird gestartet. Der Serveremulator nutzt den lokalen Computer, um die in Azure ausgeführte Anwendung zu emulieren. Sie können in der Taskleiste prüfen, ob der Emulator gestartet wurde.

       ![][23]

4.  Im Browser wird weiterhin die lokal ausgeführte Anwendung angezeigt. Die Darstellung und Funktion unterscheiden sich nicht von der Ausführung als reguläre ASP.NET MVC 4-Anwendung.

## Zusammensetzen der Einzelteile

Im nächsten Schritt wird der lokale Produktserver mit der ASP.NET MVC-Webanwendung zusammengeführt.

1.  Falls noch nicht geöffnet, öffnen Sie in Visual Studio das Projekt **ProductsPortal**, das Sie im Abschnitt "Erstellen einer ASP.NET MVC-Anwendung" erstellt haben, erneut.

2.  Fügen Sie das NuGet-Paket den Projektverweisen hinzu, wie im Schritt "Erstellen eines lokalen Servers" beschrieben. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3.  Suchen Sie nach "Service Bus", und wählen Sie das Element **Microsoft Azure Service Bus** aus. Schließen Sie die Installation ab, und schließen Sie das Dialogfeld.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsPortal**. Klicken Sie auf **Hinzufügen** und dann auf **Vorhandenes Element**.

5.  Navigieren Sie im Konsolenprojekt **ProductsServer** zur Datei **ProductsContract.cs**. Klicken Sie, um "ProductsContract.cs" zu markieren. Klicken Sie auf den Pfeil nach unten neben **Hinzufügen** und dann auf **Als Link hinzufügen**.

	![][24]

6.  Öffnen Sie nun die Datei **HomeController.cs** im Visual Studio-Editor, und ersetzen Sie die Namespace-Definition durch den folgenden Code. Achten Sie darauf, *yourServiceNamespace* durch den Namen Ihres Dienstnamespaces und *yourKey* durch Ihren SAS-Schlüssel zu ersetzen. Der Client kann daraufhin den lokalen Dienst aufrufen und das Ergebnis des Aufrufs zurückgeben.

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
                    // Declare the channel factory.
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication.
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
                            // Return a view of the products inventory.
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name,
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }
7.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **ProductsPortal**. Klicken Sie auf **Hinzufügen**, und dann auf **Vorhandenes Projekt**.

8.  Navigieren Sie zum Projekt **ProductsServer**, und doppelklicken Sie dann auf die Projektmappendatei **ProductsServer.csproj**, um sie hinzuzufügen.

9.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **ProductsPortal**, und klicken Sie dann auf **Eigenschaften**.

10. Klicken Sie auf der linken Seite auf **Startprojekt**. Klicken Sie auf der rechten Seite auf **Mehrere Startprojekts**. Stellen Sie sicher, dass **ProductsServer**, **ProductsPortal.Azure** und **ProductsPortal** in dieser Reihenfolge mit der Aktion **Start** für **ProductsServer** und **ProductsPortal.Azure** und **Keine** für **ProductsPortal** angezeigt werden.

      ![][25]

11. Klicken Sie im Dialogfeld **Eigenschaften** auf der linken Seite auf **ProjectDependencies**.

12. Klicken Sie in der Liste **Projekte** auf **ProductsServer**. Stellen Sie sicher, dass **ProductsPortal** nicht ausgewählt und **ProductsPortal.Azure** ausgewählt ist. Klicken Sie dann auf **OK**:

    ![][26]

## Ausführen der Anwendung

1.  Klicken Sie im Menü **Datei** in Visual Studio auf **Save All**.

2.  Drücken Sie F5, um die Anwendung zu erstellen und auszuführen. Der lokale Server (die Konsolenanwendung **ProductsServer**) sollte zuerst gestartet werden, danach die Anwendung **ProductsWeb** in einem Browserfenster, wie im folgenden Screenshot dargestellt. Dieses Mal werden Sie feststellen, dass in der Produktbestandsliste Daten aus dem lokalen Produktdienstsystem aufgeführt werden.

    ![][1]

## Bereitstellen der Anwendung in Azure

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsPortal**, und klicken Sie dann auf **In Microsoft Azure veröffentlichen**.

2.  Sie müssen sich möglicherweise anmelden, damit alle Ihre Abonnements angezeigt werden.

    Klicken Sie auf **Sign in to see more subscriptions**:

    ![][27]

3.  Melden Sie sich mit Ihrem Microsoft-Konto an.

8.  Klicken Sie auf **Weiter**. Wenn das Abonnement noch keine gehosteten Dienste enthält, werden Sie aufgefordert, einen zu erstellen. Der gehostete Dienst dient als Container für Ihre Anwendung im Microsoft Azure-Abonnement. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie die Region aus, für die die Anwendung optimiert werden soll. (Benutzer aus dieser Region können schnellere Ladezeiten beim Zugriff erwarten.)

9.  Wählen Sie den gehosteten Dienst aus, in dem Sie die Anwendung veröffentlichen möchten. Behalten Sie die unten angezeigten Standardwerte bei den restlichen Einstellungen bei. Klicken Sie auf **Weiter**.

    ![][33]

10. Klicken Sie auf der letzten Seite auf **Veröffentlichen**, um die Bereitstellung zu starten.

    ![][34]
 Dies dauert etwa 5-7 Minuten. Da es sich hierbei um Ihre erste Veröffentlichung handelt, wird von Azure ein virtueller Computer (VM) bereitgestellt, eine Sicherheitshärtung durchgeführt, eine Webrolle auf der VM zum Hosten der Anwendung erstellt, der Code für die Webrolle bereitgestellt und schließlich der Lastenausgleich und das Netzwerk konfiguriert, damit Ihre Anwendung öffentlich verfügbar ist.

11. Während der Veröffentlichung können Sie die Aktivität im Fenster **Azure-Aktivitätsprotokoll** überwachen, das normalerweise unten in Visual Studio oder Visual Web Developer verankert ist.

    ![][35]

12. Nach Abschluss der Bereitstellung können Sie die Website anzeigen, indem Sie im Überwachungsfenster auf den Link **Website-URL** klicken.

    ![][36]

    Da die Website vom lokalen Server abhängt, müssen Sie die Anwendung **ProductsServer** für die Website lokal ausführen, damit diese ordnungsgemäß funktioniert. Wenn Sie Anforderungen auf der Cloudwebsite durchführen, werden bei der lokalen Konsolenanwendung eingehende Anforderungen angezeigt, wie im folgenden Screenshot durch die Ausgabe "GetProducts called" angegeben.

    ![][37]

Weitere Informationen zum Unterschied zwischen Websites und Cloud-Diensten finden Sie unter [Azure-Ausführungsmodelle][executionmodels].

## Beenden und Löschen der Anwendung

Nach der Bereitstellung der Anwendung empfiehlt es sich, sie zu deaktivieren, damit Sie andere Anwendungen im Rahmen der kostenlosen 750 Stunden/Monat (31 Tage/Monat) Serverzeit erstellen und bereitstellen können.

Bei Azure werden Webrolleninstanzen pro genutzter Serverzeitstunde berechnet. Serverzeit wird genutzt, sobald die Anwendung bereitgestellt wird, selbst wenn die Instanzen nicht ausgeführt werden und sich im Status Beendet befinden. Ein kostenloses Konto umfasst 750 Stunden/Monat (31 Tage/Monat) dedizierte Serverzeit für virtuelle Computer zum Hosten dieser Webrolleninstanzen.

In den folgenden Schritten erfahren Sie, wie Sie die Anwendung beenden und löschen.

1.  Melden Sie sich beim [klassischen Azure-Portal][] an, klicken Sie auf **Cloud Services** und dann auf den Namen des Diensts.

2.  Klicken Sie auf die Registerkarte **Dashboard** und dann auf **Beenden**, um die Anwendung vorübergehend anzuhalten. Sie können sie erneut starten, indem Sie auf **Start** klicken. Klicken Sie auf **Löschen**, um die Anwendung vollständig aus Azure zu löschen. Sie kann danach nicht wiederhergestellt werden.

	![][43]

## Nächste Schritte  

Weitere Informationen zum Service Bus finden Sie in den folgenden Ressourcen:

* [Azure Service Bus][sbwacom]  
* [Verwenden von Servicebus-Warteschlangen][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Tools und SDK herunterladen]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-42-webpi.png


  [klassischen Azure-Portal]: http://manage.windowsazure.com
  [5]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png



  [Verwenden des NuGet Service Bus-Pakets]: https://msdn.microsoft.com/library/azure/dn741354.aspx
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg

  [20]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png


  [30]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [45]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md
  [executionmodels]: ../cloud-services/fundamentals-application-models.md

<!---HONumber=AcomDC_0128_2016-->