<properties
   pageTitle="Dienstkommunikation mit der ASP.NET Web-API | Microsoft Azure"
   description="Erfahren Sie, wie die Dienstkommunikation mithilfe der ASP.NET Web-API schrittweise mit einem selbstgehosteten OWIN-Server in der Reliable Services-API implementiert wird."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Erste Schritte mit Web-API-Diensten von Microsoft Azure Service Fabric mit selbstgehostetem OWIN-Server

Service Fabric ermöglicht Ihnen, selbst zu entscheiden, wie Ihre Dienste mit Benutzern und miteinander kommunizieren sollen. Dieses Lernprogramm konzentriert sich auf die Implementierung der Dienstkommunikation mithilfe der ASP.NET Web-API mit selbstgehostetem OWIN-Server in der *Reliable Services*-API von Service Fabric. Sie erhalten eine ausführliche Erläuterung der austauschbaren Kommunikations-API von *Reliable Services* sowie eine Schritt-für-Schritt-Anleitung zum Einrichten eines benutzerdefinierten Kommunikationslisteners mit der in diesem Beispiel verwendeten Web-API. Ein vollständiges Beispiel eines Kommunikationslisteners für eine Web-API sehen Sie am [Beispiel einer Webanwendung für Service Fabric auf GitHub](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication).


## Einführung in die Web-API in Service Fabric

Die ASP.NET Web-API ist ein beliebtes und leistungsstarkes Framework zum Erstellen von HTTP-APIs auf .NET Framework. Wenn Sie nicht bereits mit Web-APIs vertraut sind, erfahren Sie unter [www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) mehr darüber.

In Service Fabric wird die bekannte und bewährte Web-API von ASP.NET verwendet. Die Web-API-Anwendung werden jedoch unterschiedlich *gehostet* (Sie verwenden nicht IIS). Zum besseren Verständnis des Unterschieds wird das Hosten nachfolgend in zwei Teile untergliedert:

 1. Die Web-API-Anwendung (Ihre Controller, Modelle usw.)
 2. Der Host (der Webserver, in der Regel IIS)

Die Web-API-Anwendung an sich bleibt gleich. Sie unterscheidet sich nicht von den Web-API-Anwendungen, die Sie bisher möglicherweise geschrieben haben. Den Großteil Ihres Anwendungscodes sollten Sie einfach direkt verschieben können. Das Hosten der Anwendung erfolgt möglicherweise ein wenig anders, als Sie es vom Hosten auf IIS gewohnt sind. Bevor wir uns jedoch mit dem Hosten befassen, beginnen wir mit dem vertrauteren Teil: der Web-API-Anwendung.


## Erstellen der Anwendung

Erstellen Sie zunächst in Visual Studio 2015 eine neue Reliable Services-Anwendung mit einem einzelnen zustandslosen Dienst:

![Erstellen einer neuen Service Fabric-Anwendung](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![Erstellen eines einzelnen zustandslosen Diensts](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

Dadurch erhalten Sie einen leeren zustandslosen Dienst, der die Web-API-Anwendung hostet. Wir richten die Anwendung von Grund auf neu ein, um zu sehen, wie sie zusammengesetzt ist.

Der erste Schritt besteht darin, einige NuGet-Pakete für die Web-API abzurufen. Wir verwenden das Paket **Microsoft.AspNet.WebApi.OwinSelfHost**. Dieses Paket enthält die erforderlichen Web-API-Pakete und die *Hostpakete*, was später wichtig ist.

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Nachdem Sie die Pakete installiert haben, können Sie mit dem Ausbau der grundlegenden Projektstruktur der Web-API beginnen. Wenn Sie bereits mit Web-APIs gearbeitet haben, sollte Ihnen die Projektstruktur vertraut sein. Erstellen Sie zuerst die grundlegenden Web-API-Verzeichnisse:

 + App\_Start
 + Controllers
 + Models

Fügen Sie dem Verzeichnis "App\_Start" die grundlegenden Konfigurationsklassen der Web-API hinzu. Fürs Erste fügen wir einfach eine leere Konfigurationsdatei für einen Medientypformatierer hinzu:

 + FormatterConfig.cs

```csharp

namespace WebApiService
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

Speichern Sie im Verzeichnis "Controller" einen Standardcontroller:

 + DefaultController.cs

```csharp

namespace WebApiService.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    [RoutePrefix("api")]
    public class DefaultController : ApiController
    {
        // GET api/values
        [Route("values")]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        [Route("values/{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        [Route("values")]
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        [Route("values/{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        [Route("values/{id}")]
        public void Delete(int id)
        {
        }
    }
}

```

Speichern Sie schließlich im Projektverzeichnis eine Startklasse, um das Routing, die Formatierungsprogramme und jegliche sonstigen Konfigurationseinstellungen zu registrieren. Dies gilt auch, wenn die Web-API mit dem *Host* verbunden wird, worauf wir später zurückkommen. Erstellen Sie beim Einrichten der Startklasse eine Schnittstelle namens *IOwinAppBuilder* dafür, um die Konfigurationsmethode zu definieren. Für die technische Funktionsweise der Web-API ist dies nicht erforderlich, aber Sie können die Startklasse damit später flexibler verwenden.

 + Startup.cs

```csharp

namespace WebApiService
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            config.MapHttpAttributeRoutes();
            FormatterConfig.ConfigureFormatters(config.Formatters);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs

```csharp

namespace WebApiService
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

Soweit der Anwendungsteil. Wir haben bisher nur das grundlegende Projektlayout der Web-API eingerichtet. Im Vergleich zu den Web-API-Projekten, die Sie möglicherweise in der Vergangenheit geschrieben haben, oder zur grundlegenden Web-API-Vorlage sollte bisher nicht viel Unterschied sein. Ihre Geschäftslogik wird wie gewohnt in den Controllern und Modellen ausgeführt.

Jetzt geht es darum, die Anwendung zu hosten, um sie tatsächlich ausführen zu können.


## Diensthosting

In Service Fabric wird Ihr Dienst in einem *Diensthostprozess* in Form einer ausführbaren Datei ausgeführt, die Ihren Dienstcode ausführt. Beim Schreiben eines Diensts mithilfe der Reliable Services-API (und in der Tat in den meisten Fällen, in denen Sie einen Dienst für Service Fabric in .NET schreiben), wird das Dienstprojekt einfach in eine .EXE-Datei kompiliert, die den Diensttyp registriert und den Code ausführt. Tatsächlich sollten Sie Folgendes sehen, wenn Sie **Program.cs** im zustandslosen Dienstprojekt öffnen:

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType("WebApiServiceType", typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

Wenn das für Sie wie ein Einstiegspunkt in eine Konsolenanwendung aussieht, dann haben Sie Recht, weil es ein Einstiegspunkt ist.

Die Details über den Diensthostprozess und die Dienstregistrierung gehen über den Umfang dieses Artikels hinaus. Wichtig für jetzt zu wissen ist jedoch, dass der **Dienstcode in einem eigenen Prozess ausgeführt wird**.

## Selbstgehostete Web-API mit einem OWIN-Host

Angesichts der Tatsache, dass der Anwendungscode für die Web-API in einem eigenen Prozess gehostet wird, stellt sich nun die Frage, wie wir ihn mit einem Webserver verknüpfen. Rufen Sie [OWIN](http://owin.org/) auf. OWIN ist einfach ein Vertrag zwischen Web-Anwendungen und Webservern für .NET. Bis MVC 5 war die Webanwendung in ASP.NET über System.Web eng mit IIS gekoppelt. Die Web-API implementiert jedoch OWIN, wodurch Sie eine Webanwendung schreiben können, die vom Host-Webserver entkoppelt ist. Dies ermöglicht Ihnen die Verwendung eines *selbstgehosteten* OWIN-Webservers, den Sie in Ihrem eigenen Prozess starten können. Damit eignet er sich optimal für das soeben beschriebene Service Fabric-Hostingmodell.

In diesem Artikel wird als OWIN-Host für die Web-API-Anwendung Katana verwendet. Katana ist eine Open-Source-Owin-Host-Implementierung.

> [AZURE.NOTE]Weitere Informationen zu Katana erhalten Sie auf der [Katana-Website](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Einen schnellen Überblick über die Verwendung von Katana zum Selbsthosten der Web-API finden Sie im Artikel [Use OWIN to Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api) (Selbsthosting der ASP.NET Web-API 2, in englischer Sprache).


## Einrichten des Webservers

Die Reliable Services-API stellt einen Einstiegspunkt für die Kommunikation bereit, den Sie mit Kommunikationsstapeln verknüpfen können, damit Benutzer und Clients eine Verbindung mit dem Dienst herstellen können:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Der Webserver – und alle anderen Kommunikationsstapel, die Sie in Zukunft möglicherweise verwenden werden (z. B. WebSockets) – sollte für die ordnungsgemäße Integration in das System die Schnittstelle ICommunicationListener verwenden. Die Gründe hierfür werden in den folgenden Schritten verdeutlicht.

Erstellen Sie zunächst eine Klasse namens "OwinCommunicationListener", die ICommunicationListener implementiert:

 + OwinCommunicationListener.cs:

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

Die Schnittstelle "ICommunicationListener" stellt drei Methoden zum Verwalten eines Kommunikationslisteners für Ihren Dienst bereit:

 + **OpenAsync**: startet das Überwachen von Anforderungen.
 + **CloseAsync**: beendet das Überwachen von Anforderungen, schließt sämtliche gesendeten Anforderungen ab und fährt ordnungsgemäß herunter.
 + **Abort**: bricht sämtliche Prozesse ab und beendet die Anwendung sofort.

Fügen Sie zunächst private Klassenmember für das Präfix eines URL-Pfads und die zuvor erstellte **Startklasse** hinzu. Diese werden durch den Konstruktor initialisiert und später beim Einrichten der Überwachungs-URL verwendet. Fügen Sie auch private Klassenmember zum Speichern der Überwachungsadresse und des Serverhandles hinzu, die während der Initialisierung bzw. später beim Starten des Servers erstellt werden.

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;
    private readonly ServiceInitializationParameters serviceInitializationParameters;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
    {
        this.startup = startup;
        this.appRoot = appRoot;
        this.serviceInitializationParameters = serviceInitializationParameters;
    }        

    ...

```

### Implementierung

Zum Einrichten des Webservers benötigen wir verschiedene Informationen:

 + **Präfix für URL-Pfad**: Obwohl dies optional ist, sollte es jetzt eingerichtet werden, damit Sie mehrere Webdienste sicher in Ihrer Anwendung hosten können.
 + **Port**:

Bevor Sie einen Port für den Webserver wählen, ist es wichtig zu verstehen, dass Service Fabric eine Anwendungsebene bereitstellt, die als Puffer zwischen der Anwendung und dem zugrunde liegenden Betriebssystem fungiert. Service Fabric bietet somit eine Möglichkeit zum Konfigurieren von *Endpunkten* für Ihre Dienste. Service Fabric stellt sicher, dass der Endpunkt für den Dienst verfügbar ist, damit Sie ihn nicht selbst mit der zugrunde liegenden Betriebssystemumgebung konfigurieren müssen. Dies ermöglicht es Ihnen, die Service Fabric-Anwendung auf einfache Weise in verschiedenen Umgebungen zu hosten, ohne Änderungen an Ihrer Anwendung vorzunehmen. (Sie können z. B. die gleiche Anwendung in Azure oder in Ihrem eigenen Rechenzentrum hosten.)

Konfigurieren Sie einen HTTP-Endpunkt in PackageRoot\\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

Dieser Schritt ist wichtig, da der Diensthostprozess mit eingeschränkten Anmeldeinformationen (Netzwerkdienst unter Windows) ausgeführt wird. Das bedeutet, dass der Dienst keinen Zugriff hat, um eigenständig einen HTTP-Endpunkt einrichten zu können. Mithilfe der Endpunktkonfiguration kann Service Fabric die ACL (Access Control List, Zugriffssteuerungsliste) für die vom Dienst zu überwachende URL ordnungsgemäß einrichten. Gleichzeitig bietet sie einen Standardort zum Konfigurieren von Endpunkten.


In "OwinCommunicationListener.cs" können wir mit der Implementierung von OpenAsync beginnen. Hier starten wir den Webserver. Zunächst rufen Sie die Endpunktinformationen ab, und erstellen Sie die URL, die der Dienst überprüft.

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
    ...

```

Beachten Sie, dass hier "http://+" verwendet wird. Auf diese Weise stellen Sie sicher, dass der Webserver alle verfügbaren Adressen überwacht, einschließlich localhost, FQDN und der IP-Adresse des Computers.

Das Implementieren von "OpenAsync" ist einer der wichtigsten Gründe, warum der Webserver (oder ein Kommunikationsstapel) als ICommunicationListener implementiert und nicht einfach direkt mit "RunAsync()" im Dienst geöffnet wird. Der Rückgabewert von "OpenAsync" ist die vom Webserver überwachte Adresse. Bei der Rückgabe der Adresse an das System wird diese vom Dienst registriert. Die von Service Fabric bereitgestellte API ermöglicht es Clients oder andere Diensten, die Adresse anschließend anhand des Dienstnamens anzufordern. Dies ist wichtig, da die Dienstadresse nicht statisch ist. Der Grund dafür ist, dass die Dienste zwecks Ressourcenausgleich und Verfügbarkeit im Cluster verschoben werden. Dies ist der Mechanismus, mit dem Clients die Überwachungsadresse für einen Dienst auflösen können.

Vor diesem Hintergrund startet "OpenAsync" den Webserver und gibt die von ihm überwachte Adresse zurück. Beachten Sie, dass die Überwachung auf "http://+" erfolgt, das "+" jedoch vor der Rückgabe der Adresse durch die IP-Adresse oder den FQDN des Knotens ersetzt wird, auf dem sie sich aktuell befindet. Der Grund dafür ist, dass die mit der Methode zurückgegebene Adresse im System registriert ist. Diese Adresse erhalten Clients und andere Dienste, wenn sie die Dienstadresse anfordern. Damit Clients eine ordnungsgemäße Verbindung herstellen können, muss die Adresse eine tatsächliche IP oder FQDN enthalten.

```csharp

    ...

    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
    string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

    return Task.FromResult(publishAddress);
}

```

Diese verweist auf die **Startklasse**, die im Konstruktor an OwinCommunicationListener übergeben wurde. Diese Startinstanz wird vom Webserver verwendet, um die Web-API-Anwendung zu starten.

Die Zeile "ServiceEventSource.Current.Message()" wird später im Diagnoseereignisfenster angezeigt, wenn Sie die Anwendung ausführen. Sie dient als Hinweis, dass der Webserver erfolgreich gestartet wurde.

### CloseAsync und Abort

Implementieren Sie abschließend sowohl "CloseAsync" als auch "Abort", um den Webserver zu beenden. Sie können den Webserver beenden, indem Sie den während der Ausführung von "OpenAsync" erstellten Serverhandle löschen.

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

In diesem Implementierungsbeispiel wird der Webserver einfach mit "CloseAsync" und "Abort" beendet. Sie können den Webserver optional auch besser koordiniert herunterfahren. Warten Sie beispielsweise vor der Rückgabe, bis die gesendeten Anforderungen abgeschlossen sind.

## Starten der Website

Sie können nun eine Instanz von OwinCommunicationListener erstellen und zurückgeben, um den Webserver zu starten. Überschreiben Sie in der Dienstklasse (Service.cs) die Methode **CreateServiceInstanceListeners()**:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new[]
    {
        new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
    };
}

```

Hierbei treffen die Web-API-*Anwendung* und der *OWIN-Host* schließlich aufeinander: Der *Host* (**OwinCommunicationListener**) erhält eine Instanz der *Anwendung* (Web-API über **Start**), und Service Fabric verwaltet den Lebenszyklus. Dieses Muster kann in der Regel bei jedem Kommunikationsstapel verwendet werden.

## Zusammenfügen des Gesamtbilds

In diesem Beispiel benötigen Sie die Methode "RunAsync()" nicht, sodass die Außerkraftsetzung einfach entfernt werden kann.

Die endgültige Dienstimplementierung sollte einfach sein, da nur der Kommunikationslistener erstellt werden muss:

```csharp

namespace WebApiService
{
    using System.Collections.Generic;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;
    using Microsoft.ServiceFabric.Services.Runtime;

    public class WebApiService : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            return new[]
            {
                new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
            };
        }
    }
}

```

Und die vollständige `OwinCommunicationListener`-Klasse:

```csharp

namespace WebApiService
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private readonly ServiceInitializationParameters serviceInitializationParameters;
        private IDisposable serverHandle;
        private string listeningAddress;
        
        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
        {
            this.startup = startup;
            this.appRoot = appRoot;
            this.serviceInitializationParameters = serviceInitializationParameters;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');

            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
            string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

            return Task.FromResult(publishAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            ServiceEventSource.Current.Message("Close");

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            ServiceEventSource.Current.Message("Abort");

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

Nachdem nun alle Komponenten vorhanden sind, sollte Ihr Projekt wie eine typische Web-API-Anwendung mit den Endpunkten der Reliable Services-API und einem OWIN-Host aussehen:


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Ausführen und Verbinden über einen Webbrowser

Falls Sie dies nicht bereits getan haben, [richten Sie die Entwicklungsumgebung ein](service-fabric-get-started.md).


Sie können jetzt den Dienst erstellen und bereitstellen. Drücken Sie zum Erstellen und Bereitstellen der Anwendung in Visual Studio **F5**. Im Fenster für Diagnoseereignisse sollte eine Meldung angezeigt sein, dass der Webserver auf **http://localhost:80/webapp/api** geöffnet wurde


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]Wenn der Port bereits von einem anderen Prozess auf Ihrem Computer geöffnet wurde, wird möglicherweise eine Fehlermeldung angezeigt, dass der Listener nicht geöffnet werden konnte. Wenn dies der Fall ist, verwenden Sie einen anderen Port in der Endpunktkonfiguration in ServiceManifest.xml.


Sobald der Dienst ausgeführt wird, öffnen Sie einen Browser, und navigieren Sie zu [http://localhost/webapp/api/values](http://localhost/webapp/api/values), um dies zu testen.

## Skalieren

Zum Skalieren zustandsloser Webanwendungen werden in der Regel weitere Computer hinzufügen, und die Webanwendung wird darauf ausgedehnt. Das Orchestrierungsmodul von Service Fabric kann dies für Sie übernehmen, wenn einem Cluster neue Knoten hinzugefügt werden. Beim Erstellen von Instanzen eines zustandslosen Diensts können Sie angeben, wie viele Instanzen Sie erstellen möchten. Service Fabric platziert diese Anzahl von Instanzen entsprechend auf Knoten im Cluster. Dies gewährleistet, dass jeweils nur eine Instanz auf einem Knoten erstellt wird. Sie können durch die Angabe von "-1" für die Instanzanzahl auch festlegen, dass Service Fabric stets auf jedem Knoten eine Instanz erstellen. Dadurch wird sichergestellt, dass bei jedem Hinzufügen von Knoten zum Skalieren Ihres Clusters eine Instanz des zustandslosen Diensts auf den neuen Knoten erstellt wird. Dieser Wert ist eine Eigenschaft der Dienstinstanz. Er wird beim Erstellen einer Serviceinstanz über PowerShell festgelegt:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Oder beim Definieren eines Standarddiensts in einem Visual Studio-Projekt mit einem zustandslosen Dienst:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Weitere Informationen zum Erstellen von Anwendungen und Dienstinstanzen finden Sie im Artikel über das [Bereitstellen und Entfernen von Anwendungen](service-fabric-deploy-remove-applications.md).

## ASP.NET 5

In ASP.NET 5 bleiben das Konzept und das Programmiermodell, bei dem die *Anwendung* in Webanwendungen vom *Host* getrennt wird, unverändert. Es kann auch auf andere Formen der Kommunikation angewendet werden. Obgleich der *Host* in ASP.NET 5 abweichen kann, bleibt zudem auch die *Web-API-Anwendung* gleich, in der sich der Großteil der Anwendungslogik tatsächlich befindet.

## Nächste Schritte

[Debuggen einer Service Fabric-Anwendung in Visual Studio](service-fabric-debugging-your-application.md)

<!----HONumber=Nov15_HO4-->