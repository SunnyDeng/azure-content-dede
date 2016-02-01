<properties
   pageTitle="Dienstkommunikation mit der ASP.NET Web-API | Microsoft Azure"
   description="Erfahren Sie, wie Sie die Dienstkommunikation mithilfe der ASP.NET Web-API schrittweise per selbstgehostetem OWIN in der Reliable Services-API implementieren."
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

# Erste Schritte: Web-API-Dienste von Service Fabric mit selbstgehostetem OWIN

Bei Azure Service Fabric können Sie selbst entscheiden, wie Ihre Dienste mit Benutzern und miteinander kommunizieren sollen. In diesem Tutorial geht es hauptsächlich um die Implementierung der Dienstkommunikation mit der ASP.NET Web-API mit selbstgehostetem OWIN (Open Web Interface for .NET) in der Reliable Services-API von Service Fabric. Wir werden uns eingehend mit der austauschbaren Reliable Services-API für die Kommunikation beschäftigen. Außerdem verwenden wir die Web-API im Rahmen eines Schritt-für-Schritt-Beispiels, um Ihnen zu zeigen, wie Sie einen benutzerdefinierten Listener für die Kommunikation einrichten.


## Einführung in Web-APIs in Service Fabric

Die ASP.NET Web-API ist ein beliebtes und leistungsstarkes Framework zum Erstellen von HTTP-APIs auf Basis von .NET Framework. Falls Sie mit dem Framework noch nicht vertraut sind, helfen Ihnen die Informationen unter [Getting Started with ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) weiter.

In Service Fabric wird die bekannte und bewährte Web-API von ASP.NET verwendet. Der Unterschied besteht lediglich darin, wie Sie die Web-API-Anwendung *hosten*. Sie verwenden hierfür nicht Microsoft-Internetinformationsdienste. Zum besseren Verständnis des Unterschieds wird das Hosten nachfolgend in zwei Teile untergliedert:

 1. Web-API-Anwendung (einschließlich Controllern und Modellen)
 2. Host (der Webserver, in der Regel IIS)

Die Web-API-Anwendung selbst ändert sich nicht. Sie unterscheidet sich nicht von den Web-API-Anwendungen, die Sie bereits geschrieben haben, und es sollte problemlos möglich sein, den größten Teil Ihres Anwendungscodes einfach zu verschieben und zu nutzen. Falls Sie aber IIS zum Hosten verwendet haben, kann sich das Hosten der Anwendung etwas von der gewohnten Vorgehensweise unterscheiden. Bevor wir uns mit dem Hosten befassen, beginnen wir mit etwas Vertrautem: der Web-API-Anwendung.


## Erstellen der Anwendung

Erstellen Sie zunächst in Visual Studio 2015 eine neue Service Fabric-Anwendung mit einem einzelnen zustandslosen Dienst:

![Erstellen einer neuen Service Fabric-Anwendung](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![Erstellen eines einzelnen zustandslosen Diensts](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

Dadurch erhalten Sie einen leeren zustandslosen Dienst, der die Web-API-Anwendung hostet. Wir richten die Anwendung von Grund auf neu ein, um zu sehen, wie sie zusammengesetzt ist.

Der erste Schritt besteht darin, einige NuGet-Pakete für die Web-API abzurufen. Wir verwenden das Paket Microsoft.AspNet.WebApi.OwinSelfHost. Dieses Paket enthält die erforderlichen Web-API-Pakete und die *Host*pakete. Dies ist für einen späteren Teil wichtig.

![Erstellen der Web-API mit dem NuGet-Paket-Manager](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Nachdem Sie die Pakete installiert haben, können Sie mit dem Erstellen der grundlegenden Projektstruktur der Web-API beginnen. Wenn Sie bereits mit Web-APIs gearbeitet haben, sollte Ihnen die Projektstruktur vertraut vorkommen. Erstellen Sie zuerst die grundlegenden Web-API-Verzeichnisse:

 + App\_Start
 + Controllers
 + Models

Fügen Sie dem Verzeichnis "App\_Start" die grundlegenden Konfigurationsklassen der Web-API hinzu. Fürs Erste fügen wir einfach eine leere Konfigurationsdatei für einen Medientypformatierer hinzu:

**FormatterConfig.cs**

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

**DefaultController.cs**

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

Speichern Sie schließlich im Projektverzeichnis eine Startklasse, um das Routing, die Formatierungsprogramme und jegliche sonstigen Konfigurationseinstellungen zu registrieren. Dies gilt auch, wenn die Web-API mit dem *Host* verbunden wird, worauf wir später noch zurückkommen. Erstellen Sie beim Einrichten der Startup-Klasse eine Schnittstelle mit dem Namen IOwinAppBuilder für die Startup-Klasse, um die Konfigurationsmethode zu definieren. Für die technische Funktionsweise der Web-API ist dies nicht erforderlich, aber Sie können die Startup-Klasse damit später flexibler verwenden.

**Startup.cs**

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

**IOwinAppBuilder.cs**

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

Soweit der Anwendungsteil. Wir haben bisher nur das grundlegende Projektlayout der Web-API eingerichtet. Im Vergleich zu den Web-API-Projekten, die Sie unter Umständen in der Vergangenheit geschrieben haben, oder zur grundlegenden Web-API-Vorlage sollten bisher nicht viele Unterschiede zu erkennen sein. Ihre Geschäftslogik wird wie gewohnt in den Controllern und Modellen ausgeführt.

Jetzt geht es darum, die Anwendung zu hosten, um sie tatsächlich ausführen zu können.


## Diensthosting

In Service Fabric wird Ihr Dienst in einem *Diensthostprozess* in Form einer ausführbaren Datei für den Dienstcode ausgeführt. Wenn Sie einen Dienst mit der Reliable Services-API schreiben, wird Ihr Dienstprojekt einfach in eine ausführbare Datei kompiliert, mit der der Diensttyp registriert und Ihr Code ausgeführt wird. Dies gilt für die meisten Fälle, in denen Sie unter Service Fabric einen Dienst in .NET schreiben. Sie sollten Folgendes sehen, wenn Sie die Datei „Program.cs“ im Projekt mit dem zustandslosen Dienst öffnen:

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

Weitere Informationen zum Diensthostprozess und zur Dienstregistrierung würden den Rahmen dieses Artikels sprengen. Vorerst ist es aber wichtig zu wissen, dass *Ihr Dienstcode in seinem eigenen Prozess ausgeführt wird*.

## Selbsthosten der Web-API per OWIN-Host

Angesichts der Tatsache, dass der Anwendungscode für die Web-API in einem eigenen Prozess gehostet wird, stellt sich nun die Frage, wie Sie ihn mit einem Webserver verknüpfen. Rufen Sie [OWIN](http://owin.org/) auf. OWIN ist einfach ein Vertrag zwischen Web-Anwendungen und Webservern für .NET. Bisher war die Webanwendung bei ASP.NET (bis MVC 5) über System.Web eng mit IIS gekoppelt. Bei der Web-API wird aber OWIN implementiert, sodass Sie eine Webanwendung schreiben können, die vom Host-Webserver entkoppelt ist. Aus diesem Grund können Sie einen *selbstgehosteten* OWIN-Webserver verwenden, den Sie in Ihrem eigenen Prozess starten können. Dies passt perfekt zu dem Service Fabric-Hostingmodell, das wir gerade beschrieben haben.

In diesem Artikel wird als OWIN-Host für die Web-API-Anwendung Katana verwendet. Katana ist eine Open-Source-Owin-Host-Implementierung.

> [AZURE.NOTE] Weitere Informationen zu Katana finden Sie auf der [Katana-Website](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Eine Kurzübersicht über die Verwendung von Katana zum Selbsthosten der Web-API finden Sie unter [Use OWIN to Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## Einrichten des Webservers

Die Reliable Services-API stellt einen Einstiegspunkt für die Kommunikation bereit, den Sie mit Kommunikationsstapeln verknüpfen können, über die Benutzer und Clients eine Verbindung mit dem Dienst herstellen können:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Der Webserver (und alle anderen Kommunikationsstapel, die Sie in Zukunft verwenden, z. B. WebSockets) sollte für die richtige Integration in das System die ICommunicationListener-Schnittstelle verwenden. Die Gründe hierfür werden in den folgenden Schritten verdeutlicht.

Erstellen Sie zunächst eine Klasse namens OwinCommunicationListener, mit der ICommunicationListener implementiert wird:

**OwinCommunicationListener.cs**

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

 - *OpenAsync*: Startet das Überwachen von Anforderungen.
 - *CloseAsync*: Beendet das Überwachen von Anforderungen, schließt sämtliche gesendeten Anforderungen ab und fährt ordnungsgemäß herunter.
 - *Abort*: Bricht sämtliche Prozesse ab und beendet die Anwendung sofort.

Fügen Sie zunächst private Klassenmember für das Präfix eines URL-Pfads und die zuvor erstellte Startup-Klasse hinzu. Diese werden durch den Konstruktor initialisiert und später beim Einrichten der Überwachungs-URL verwendet. Fügen Sie auch private Klassenmember zum Speichern der Überwachungsadresse hinzu, die während der Initialisierung erstellt wird, und um den Serverhandle zu speichern, wenn der Server gestartet wird.

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

## Implementieren von OpenAsync

Zum Einrichten des Webservers benötigen Sie zwei Angaben:

 - *Präfix für URL-Pfad*: Obwohl diese Angabe optional ist, sollten Sie dies jetzt einrichten, damit Sie mehrere Webdienste in Ihrer Anwendung sicher hosten können.
 - *Port*:

Bevor Sie einen Port für den Webserver wählen, ist es wichtig zu verstehen, dass Service Fabric eine Anwendungsebene bereitstellt, die als Puffer zwischen der Anwendung und dem zugrunde liegenden Betriebssystem fungiert. Service Fabric bietet somit eine Möglichkeit zum Konfigurieren von *Endpunkten* für Ihre Dienste. Mit Service Fabric wird sichergestellt, dass Endpunkte für den Dienst verfügbar sind. Sie müssen diese dann nicht selbst in der Umgebung des zugrunde liegenden Betriebssystems konfigurieren. Es ist einfach, die Service Fabric-Anwendung in unterschiedlichen Umgebungen zu hosten, ohne dass Sie Änderungen an Ihrer Anwendung vornehmen müssen. (Beispielsweise können Sie eine Anwendung in Azure oder in Ihrem eigenen Rechenzentrum hosten.)

Konfigurieren Sie einen HTTP-Endpunkt in PackageRoot\\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

Dieser Schritt ist wichtig, da der Diensthostprozess mit eingeschränkten Anmeldeinformationen (Netzwerkdienst unter Windows) ausgeführt wird. Dies bedeutet, dass für Ihren Dienst kein Zugriff zum Einrichten eines eigenen HTTP-Endpunkts besteht. Dank der Endpunktkonfiguration kann Service Fabric die richtige Zugriffssteuerungsliste (Access Control List, ACL) für die URL einrichten, unter der der Dienst lauscht. Service Fabric ist auch der standardmäßige Ort zum Konfigurieren von Endpunkten.


In „OwinCommunicationListener.cs“ können Sie mit der Implementierung von OpenAsync beginnen. Hier starten Sie den Webserver. Zunächst rufen Sie die Endpunktinformationen ab, und erstellen Sie die URL, die der Dienst überprüft.

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

Die OpenAsync-Implementierung ist einer der wichtigsten Gründe, warum der Webserver (oder ein Kommunikationsstapel) als ICommunicationListener implementiert und nicht einfach direkt mit `RunAsync()` im Dienst geöffnet wird. Der Rückgabewert von "OpenAsync" ist die vom Webserver überwachte Adresse. Bei der Rückgabe der Adresse an das System wird diese vom Dienst registriert. Die von Service Fabric bereitgestellte API ermöglicht es Clients und anderen Diensten, die Adresse anschließend anhand des Dienstnamens anzufordern. Dies ist wichtig, weil die Dienstadresse nicht statisch ist. Dienste werden im Cluster zum Zwecke des Lastenausgleichs von Ressourcen und der Verfügbarkeit hin und her verschoben. Dies ist der Mechanismus, mit dem Clients die Überwachungsadresse für einen Dienst auflösen können.

Vor diesem Hintergrund startet OpenAsync den Webserver und gibt die von ihm überwachte Adresse zurück. Beachten Sie, dass die Überwachung auf „http://+“ erfolgt, das Pluszeichen (+) aber vor der OpenAsync-Rückgabe der Adresse durch die IP-Adresse oder den FQDN des Knotens ersetzt wird, auf dem sie sich aktuell befindet. Die Adresse, die mit dieser Methode zurückgegeben wird, wird beim System registriert. Sie wird auch Clients und anderen Diensten angezeigt, wenn diese nach der Adresse eines Diensts fragen. Damit Clients eine ordnungsgemäße Verbindung herstellen können, muss die Adresse eine tatsächliche IP oder FQDN enthalten.

```csharp

    ...

    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
    string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

    return Task.FromResult(publishAddress);
}

```

Hiermit wird auf die Startup-Klasse verwiesen, die im Konstruktor an OwinCommunicationListener übergeben wurde. Diese Startinstanz wird vom Webserver verwendet, um die Web-API-Anwendung zu starten.

Die Zeile `ServiceEventSource.Current.Message()` wird später im Fenster mit den Diagnoseereignissen angezeigt, wenn Sie die Anwendung ausführen, um zu bestätigen, dass das Starten des Webservers erfolgreich war.

## Implementieren von CloseAsync und Abort

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

In diesem Implementierungsbeispiel wird der Webserver sowohl mit „CloseAsync“ als auch mit „Abort“ einfach beendet. Sie können sich auch dafür entscheiden, einen besser koordinierten Vorgang zum Herunterfahren des Webservers in CloseAsync durchzuführen. Beispielsweise kann beim Herunterfahren gewartet werden, bis laufende Anforderungen vor der Rückgabe abgeschlossen werden.

## Starten der Website

Sie können nun eine Instanz von OwinCommunicationListener erstellen und zurückgeben, um den Webserver zu starten. Überschreiben Sie in der Service-Klasse (Service.cs) die `CreateServiceInstanceListeners()`-Methode:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new[]
    {
        new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
    };
}

```

Dies der Punkt, an dem die Web-API-*Anwendung* und der OWIN-*Host* schließlich zusammentreffen. Für den Host (OwinCommunicationListener) wird eine Instanz der *Anwendung* (Web-API per Start) bereitgestellt. Service Fabric verwaltet dann deren Lebenszyklus. Dieses Muster kann in der Regel bei jedem Kommunikationsstapel verwendet werden.

## Korrektes Zusammenfügen

In diesem Beispiel müssen Sie für die `RunAsync()`-Methode keine Schritte ausführen, sodass die Außerkraftsetzung einfach entfernt werden kann.

Die endgültige Implementierung des Diensts ist normalerweise sehr einfach. Es muss nur der Listener für die Kommunikation erstellt werden:

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

Vollständige `OwinCommunicationListener`-Klasse:

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

Nachdem Sie nun alles zusammengestellt haben, sollte Ihr Projekt wie eine typische Web-API-Anwendung mit den Endpunkten der Reliable Services-API und einem OWIN-Host aussehen:


![Web-API mit Reliable Services-API-Einstiegspunkten und OWIN-Host](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Ausführen und Verbinden über einen Webbrowser

Falls Sie dies nicht bereits getan haben, [richten Sie die Entwicklungsumgebung ein](service-fabric-get-started.md).


Sie können jetzt den Dienst erstellen und bereitstellen. Drücken Sie zum Erstellen und Bereitstellen der Anwendung in Visual Studio **F5**. Im Fenster für Diagnoseereignisse sollte per Meldung darauf hingewiesen werden, dass der Webserver unter **http://localhost:80/webapp/api** geöffnet wurde.


![Visual Studio-Fenster mit Diagnoseereignissen](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Wenn der Port bereits von einem anderen Prozess auf Ihrem Computer geöffnet wurde, wird hier ggf. ein Fehler angezeigt. Dies gibt an, dass der Listener nicht geöffnet werden konnte. Wenn dies der Fall ist, verwenden Sie einen anderen Port für die Endpunktkonfiguration in „ServiceManifest.xml“.


Sobald der Dienst ausgeführt wird, öffnen Sie einen Browser und navigieren zu [http://localhost/webapp/api/values](http://localhost/webapp/api/values), um dies zu testen.

## Skalieren

Zum horizontalen Hochskalieren zustandsloser Web-Apps werden in der Regel weitere Computer hinzugefügt, um die Web-Apps darauf bereitzustellen. Das Orchestrierungsmodul von Service Fabric kann dies für Sie übernehmen, wenn einem Cluster neue Knoten hinzugefügt werden. Beim Erstellen von Instanzen eines zustandslosen Diensts können Sie angeben, wie viele Instanzen Sie erstellen möchten. Service Fabric ordnet die Anzahl der Instanzen auf Knoten im Cluster an. Und es wird sichergestellt, dass nicht mehr als eine Instanz auf jedem Knoten erstellt wird. Sie können durch die Angabe **-1** für die Instanzanzahl auch festlegen, dass Service Fabric stets auf jedem Knoten eine Instanz erstellen soll. Dadurch wird sichergestellt, dass bei jedem Hinzufügen von Knoten zum Skalieren Ihres Clusters eine Instanz des zustandslosen Diensts auf den neuen Knoten erstellt wird. Dieser Wert ist eine Eigenschaft der Dienstinstanz, die beim Erstellen einer Dienstinstanz festgelegt wird. Hierfür können Sie PowerShell verwenden:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Sie können diesen Schritt auch ausführen, wenn Sie einen Standarddienst in einem zustandslosen Visual Studio-Projekt definieren:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Weitere Informationen zum Erstellen von Anwendungs- und Dienstinstanzen finden Sie unter [Bereitstellen einer Anwendung](service-fabric-deploy-remove-applications.md).

## ASP.NET 5

In ASP.NET 5 bleiben das Konzept und das Programmiermodell, bei dem die *Anwendung* in Webanwendungen vom *Host* getrennt wird, unverändert. Es kann auch auf andere Formen der Kommunikation angewendet werden. Auch wenn der *Host* in ASP.NET 5 ggf. anders aussieht, bleibt die Web-API-*Anwendung*sschicht gleich. Hierin befindet sich der Großteil der Anwendungslogik.

## Nächste Schritte

[Debuggen der Service Fabric-Anwendung mithilfe von Visual Studio](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0121_2016-->