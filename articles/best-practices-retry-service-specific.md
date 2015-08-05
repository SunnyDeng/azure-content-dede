<properties
   pageTitle="Wiederholen der spezifischen Dienstanleitung | Microsoft Azure"
   description="Spezifische Dienstanleitung für die Festlegung des Wiederholungsmechanismus."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Wiederholen der spezifischen Anleitung

![](media/best-practices-retry-service-specific/pnp-logo.png)

## Übersicht

Die meisten Azure-Dienste und Client-SDKs enthalten einen Wiederholungsmechanismus. Diese unterscheiden sich jedoch, da jeder Dienst unterschiedliche Merkmale und Anforderungen hat und somit jeder Wiederholungsmechanismus für einen bestimmten Dienst optimiert ist. Dieses Handbuch fasst die Wiederholungsmechanismusfunktionen für die Mehrzahl der Azure-Dienste zusammen und enthält Informationen, mit denen Sie die Wiederholungsmechanismus für diesen Dienst verwenden, anpassen oder erweitern können.

Allgemeine Anweisungen zum Behandeln von vorübergehenden Fehlern und für Wiederholungsverbindungen und Operationen für Dienste und Ressourcen finden Sie unter [Wiederholungsanleitung](best-practices-retry-general.md).

In der folgende Tabelle werden die Wiederholungsfunktionen für die in dieser Anleitung beschriebenen Azure-Dienste zusammengefasst.

| **Service** | **Wiederholungsfunktionen** | **Richtlinienkonfiguration** | **Umfang** | **Telemetriefunktionen** |
|---------------------------------------|-----------------------------------------|------------------------------|--------------------------------------------------|------------------------
| **[AzureStorage](#azure-storage-retry-guidelines)** | Systemeigen in Client | Programmgesteuert | Clientvorgänge und einzelne Vorgänge | TraceSource |
| **[SQL-Datenbank mit Entity Framework](#sql-database-using-entity-framework-6-retry-guidelines)** | Systemeigen in Client | Programmgesteuert | Global pro AppDomain | Keine |
| **[SQL-Datenbank mit ADO.NET](#sql-database-using-ado-net-retry-guidelines)** | Topaz* | Deklarativ und programmatisch | Einzelne Anweisungen oder Codeblöcke | Benutzerdefiniert |
| **[Service Bus](#service-bus-retry-guidelines)** | Systemeigen in Client | Programmgesteuert | Namespace-Manager, Messaging Factory und Client | ETW |
| **[Cache](#cache-redis-retry-guidelines)** | Systemeigen in Client | Programmgesteuert | Client- | TextWriter |
| **[DocumentDB](#documentdb-pre-release-retry-guidelines)** | Systemeigen im Dienst | Nicht konfigurierbar | Global | TraceSource |
| **[Suchen](#search-retry-guidelines)** | Topaz* (mit benutzerdefinierter Strategie zur Erkennung) | Deklarativ und programmatisch | Codeblöcke | Benutzerdefiniert |
| **[Active Directory](#azure-active-directory-retry-guidelines)** | Topaz* (mit benutzerdefinierter Strategie zur Erkennung) | Deklarativ und programmatisch | Codeblöcke | Benutzerdefiniert |
*Topaz in den Anzeigenamen für den Anwendungsblock zur Handhabung vorübergehender Fehler, der Bestandteil von <a href="http://msdn.microsoft.com/library/dn440719.aspx">Enterprise Library 6.0</a> ist. Sie können für die meisten Dienste wie in diesem Handbuch beschrieben eine benutzerdefinierte Strategie zur Erkennung mit Topaz verwenden. Standardstrategien für Topaz werden Abschnitt [Strategien für den Anwendungsblock zur Handhabung vorübergehender Fehler (Topaz)](#transient-fault-handling-application-block-topaz-strategies) am Ende dieses Handbuchs gezeigt. Beachten Sie, dass der Block jetzt ein Open Source-Framework ist und ist nicht direkt von Microsoft unterstützt wird.

> [AZURE.NOTE]Für die meisten der integrierten Azure Mechanismen gibt es derzeit keine Möglichkeit, unterschiedliche Wiederholungsrichtlinien für verschiedene Typen von Fehler oder Ausnahmen anzuwenden, die über die in der Wiederholungsrichtlinie integrierte Funktionalität hinausgeht. Daher ist die beste gegenwärtige Anweisung zum Redaktionszeitpunkt eine Richtlinie zu konfigurieren, die die optimale durchschnittliche Leistung und Verfügbarkeit bietet. Eine Möglichkeit zur Optimierung der Richtlinie ist die Analyse von Protokolldateien, um den Typ der vorübergehenden Fehler zu bestimmen, die auftreten. Wenn z. B. die meisten Fehler mit der Netzwerkkonnektivität verknüpft sind, können Sie eine sofortige Wiederholung versuchen, anstatt langer für die erste Wiederholung zu warten.

## Azure Storage Wiederholungsrichtlinien

Azure-Speicherdienste umfassen Tabellen- und Blob-Speicher, Dateien und Speicher-Warteschlangen.

### Wiederholungsmechanismus

Wiederholungen treten auf individueller REST-Vorgangsebene auf und sind ein wesentlicher Bestandteil der Client-API-Implementierung. Das Client-Speicher SDK verwendet Klassen, die die [IExtendedRetryPolicy Schnittstelle](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx) implementieren.

Es gibt verschiedene Implementierungen der Schnittstelle. Speicherclients können Richtlinien auswählen, die speziell für den Zugriff auf Tabellen, Blobs und Warteschlangen entworfen wurden. Jede Implementierung verwendet eine andere Wiederholungsstrategie, die im Wesentlichen das Wiederholungsintervall und andere Details definiert.

Die integrierten Klassen bieten Unterstützung für linear (konstante Verzögerung) und exponentiell mit Zufallsgenerator-Wiederholungsintervallen. Es gibt auch eine Richtlinie für keine Wiederholung, wenn ein anderer Prozess Wiederholungen auf höherer Ebene behandelt. Allerdings können Sie Ihre eigenen Wiederholungsklassen implementieren, wenn Sie spezielle Anforderungen haben, die von den integrierten Klassen nicht bereitgestellt werden.

Abwechselnde Wiederholungen wechseln zwischen primären und sekundären Speicherdienststandorten, wenn Sie Lesezugriff auf georedundante Speicher (RA-GRS) verwenden und das Ergebnis der Anforderung ist ein wiederholbarer Fehler. Weitere Informationen finden Sie unter [Redundanzoptionen für Azure Storage](http://msdn.microsoft.com/library/azure/dn727290.aspx).

### Richtlinienkonfiguration (Azure-Speicher)

Wiederholungsrichtlinien werden programmgesteuert konfiguriert. Eine typische Vorgehensweise ist das Erstellen und Ausfüllen von **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions** oder **QueueRequestOptions**-Instanzen.

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

Die Anforderungsoptioneninstanz kann dann auf dem Client festgelegt werden und alle Operationen mit dem Client verwenden die angegebene Anforderungsoptionen.

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

Sie können die Client-Anforderungsoptionen überschreiben, indem Sie eine ausgefüllte Instanz der Anforderungsoptionsklasse als Parameter an Vorgangsmethoden übergeben.

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

Sie verwenden eine **OperationContext**-Instanz, um den auszuführenden Code anzugeben, wenn eine Wiederholung auftritt und ein Vorgang abgeschlossen wurde. Dieser Code kann Informationen über den Vorgang zur Verwendung in Protokollen und Telemetrie erfassen.

	// Set up notifications for an operation
	var context = new OperationContext();
	context.ClientRequestID = "some request id";
	context.Retrying += (sender, args) =>
	{
	  /* Collect retry information */
	};
	context.RequestCompleted += (sender, args) =>
	{
	  /* Collect operation completion information */
	};
	var stats = await client.GetServiceStatsAsync(null, context);

Zusätzlich zur Angabe, ob ein Fehler für die Wiederholung geeignet ist, geben die erweiterten Wiederholungsrichtlinien ein **RetryContext** -Objekt zurück, das folgendes angibt: die Anzahl der Wiederholungen, die Ergebnisse der letzten Anforderung, ob die nächste Wiederholung im primären oder sekundären Standort ausgeführt werden (siehe Tabelle unten). Die Eigenschaften des **RetryContext** Objekts können verwendet werden, um zu entscheiden, ob und wann eine Wiederholung versucht wird. Weitere Informationen finden Sie unter [IExtendedRetryPolicy.Evaluate-Methode](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).

Die folgende Tabelle zeigt die Standardeinstellungen für die integrierten Wiederholungsrichtlinien.

| **Context** | **Einstellung** | **Standardwert** | **Bedeutung** |
|--------------------------|-------------------------------------------------------------|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tabelle / Blob / Datei<br />QueueRequestOptions | MaximumExecutionTime<br /><br />ServerTimeout<br /><br /><br /><br /><br />LocationMode<br /><br /><br /><br /><br /><br /><br />RetryPolicy | 120 Sekunden<br /><br />Keine<br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br />ExponentialPolicy | Maximale Ausführungszeit für die Anforderung, einschließlich aller möglichen Wiederholungsversuche.<br />Server-Timeout-Intervall für die Anforderung (der Wert wird in Sekunden gerundet). Wenn nicht angegeben, wird der Standardwert für alle Anforderungen an den Server verwendet. In der Regel ist die beste Option, diese Einstellung auszulassen, sodass die Standardeinstellung des Servers verwendet wird.<br />Wenn das Speicherkonto mit der Replikationsoption des Lesezugriffs auf georedundanten Speicher (RA-GRS) erstellt wird, können Sie mit dem Speicherortmodus bestimmen, welche Stelle die Anforderung erhalten soll. Wenn zum Beispiel **PrimaryThenSecondary** angegeben ist, werden Anforderungen immer zuerst an den primären Standort gesendet. Wenn eine Anforderung fehlschlägt, wird sie an den sekundären Standort gesendet.<br />Details zu jeder Option finden Sie weiter unten. |
| Exponentielle Richtlinie | maxAttempt<br />deltaBackoff<br /><br /><br />MinBackoff<br /><br />MaxBackoff | 3<br />4 Sekunden<br /><br /><br />3 Sekunden<br /><br />30 Sekunden | Anzahl der Wiederholungsversuche.<br />Backoff-Intervall zwischen den Wiederholungen. Vielfache dieser Zeitspanne, einschließlich eines Zufallselements, werden für weitere Wiederholungsversuche verwendet.<br />Wird zu allen Wiederholungsintervallen, die aus deltaBackoff berechnet werden, hinzugefügt. Dieser Wert kann nicht geändert werden.<br />MaxBackoff wird verwendet, wenn das berechnete Wiederholungsintervall größer als MaxBackoff ist. Dieser Wert kann nicht geändert werden. |
| Lineare Richtlinie | maxAttempt<br />deltaBackoff | 3<br />30 Sekunden | Anzahl der Wiederholungsversuche.<br />Backoff-Intervall zwischen den Wiederholungen. |

### Gebrauchsanleitung Wiederholungen
Beachten Sie die folgenden Richtlinien beim Zugriff auf Azure-Speicherdienste mit der Speicherclient-API:

* Verwenden der integrierten Wiederholungsrichtlinien aus dem Microsoft.WindowsAzure.Storage.RetryPolicies-Namespace, wo sie für Ihre Anforderungen geeignet sind. In den meisten Fällen sind diese Richtlinien ausreichend.
* Verwenden der **ExponentialRetry** Richtlinie in Batchvorgängen, Hintergrundaufgaben oder nicht interaktiven Szenarios. In diesen Szenarien können Sie in der Regel dem Dienst mehr Zeit für die Wiederherstellung zur Verfügung stellen - dadurch erhöht sich die Wahrscheinlichkeit, dass der Vorgang schließlich erfolgreich ist.
* Geben Sie die Eigenschaft **MaximumExecutionTime** der **RequestOptions** Parameter an, um die Gesamtausführungszeit zu beschränken. Berücksichtigen Sie bei der Auswahl eines Timeoutwerts aber den Typ und die Größe des Vorgangs.
* Wenn Sie eine benutzerdefinierte Wiederholung implementieren müssen, vermeiden Sie das Erstellen von Wrappern um die Speicherclienten-Klassen. Verwenden Sie stattdessen die Funktionen zum Erweitern der vorhandenen Richtlinien über die **IExtendedRetryPolicy** Schnittstelle.
* Bei Verwendung des Lesezugriffs auf georedundante Speicher (RA-GRS) können Sie **LocationMode** verwenden, um zu bestimmen, dass Wiederholungsversuche nicht auf die sekundäre schreibgeschützte Kopie des Speichers zugreifen sollen, wenn der primäre Zugriff fehlschlägt. Bei Verwendung dieser Option müssen Sie jedoch sicherstellen, dass die Anwendung erfolgreich mit Daten arbeiten kann, die möglicherweise veraltet sind, wenn die Replikation vom primären Speicher noch nicht abgeschlossen wurde.

Erwägen Sie, mit den folgenden Einstellungen für Wiederholungsvorgänge zu beginnen. Hierbei handelt es sich um allgemeine Einstellungen und Sie sollten die Vorgänge überwachen und die Werte entsprechend Ihrem Szenario optimieren.

| **Context** | **Beispiel-Ziel E2E<br />Maximale Wartezeit** | **Wiederholungsrichtlinie** | **Einstellungen** | **Werte** | **So funktioniert's** |
|----------------------|-----------------------------------|------------------|-------------------------|-------------|-----------------------------------------------------------------------------|
| Interaktiv, Benutzeroberfläche<br />oder Vordergrund | 2 Sekunden | Linear | maxAttempt<br />deltaBackoff | 3<br />500 ms | Versuch 1 - Verzögerung 500 ms<br />Versuch 2 - Verzögerung 500 ms<br />Versuch 3 - Verzögerung 500 ms |
| Hintergrund<br />oder Batch | 30 Sekunden | Exponentiell | maxAttempt<br />deltaBackoff | 5<br />4 Sekunden | Versuch 1 - Verzögerung ca. 3 ms<br />Versuch 2 - Verzögerung ca. 7 ms<br />Versuch 3 - Verzögerung ca. 15 ms |

## Telemetrie

Wiederholungsversuche werden in einer **TraceSource** protokolliert. Sie müssen einen **TraceListener** konfigurieren, um die Ereignisse zu erfassen und diese in ein geeignetes Zielprotokoll zu schreiben. Sie können **TextWriterTraceListener** oder **XmlWriterTraceListener** zum Schreiben der Daten in eine Protokolldatei verwenden, **EventLogTraceListener** zum Schreiben in ein Windows-Ereignisprotokoll oder **EventProviderTraceListener** zum Schreiben von Daten in das ETW-Subsystem. Sie können auch automatisches Leeren des Puffers und den Ausführlichkeitsgrad der Ereignisse konfigurieren, die protokolliert werden (z. B. Fehler, Warnung, Information und ausführlich). Weitere Informationen finden Sie unter [Clientseitige Protokollierung mit der .NET Storage Client Library](http://msdn.microsoft.com/library/azure/dn782839.aspx).

Vorgänge können eine **OperationContext**-Instanz erhalten, die ein **Erneuter Versuch**-Ereignis verfügbar macht, das zum Anfügen von benutzerdefinierter Telemetrielogik verwendet werden kann. Weitere Informationen finden Sie unter [OperationContext.Ereignis Erneuter Versuch](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).

## Beispiele (Azure-Speicher)

Im folgenden Codebeispiel wird veranschaulicht, wie Sie zwei **TableRequestOptions** -Instanzen mit verschiedenen Wiederholungseinstellungen erstellen; eine für interaktive Anforderungen und eine für Anforderungen im Hintergrund. Im Beispiel werden dann diese beiden Richtlinien auf dem Client festgelegt, sodass sie für alle Anforderungen gelten und außerdem wird die interaktive Strategie für eine bestimmte Anforderung festgelegt, damit sie die auf den Client angewandten Standardeinstellungen überschreibt.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

## Weitere Informationen

- [Empfehlungen zur Azure Storage-Clientbibliothek Wiederholungsrichtlinie](http://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
- [Storage Client Library 2.0 – Implementieren von Wiederholungsrichtlinien](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## SQL-Datenbank mit Entity Framework 6 Wiederholungsrichtlinien

SQL-Datenbank ist eine gehostete SQL-Datenbank, die in unterschiedlichen Größen und als Standard (freigegeben) und Premium (nicht freigegebenen)-Dienst verfügbar ist. Entity Framework ist eine objektrelationale Zuordnung, die .NET Entwicklern die Arbeit mit relationalen Daten mithilfe von domänenspezifischen Objekten ermöglicht. Es entfällt die Notwendigkeit für den Großteil des Datenzugriffs-Codes, den Entwickler normalerweise schreiben müssen.

## Wiederholungsmechanismus

Beim Zugriff auf SQL-Datenbank mit Entity Framework 6.0 und höher über einen Mechanismus namens [Verbindungsstabilität / Wiederholungslogik](http://msdn.microsoft.com/data/dn456835.aspx) wird Wiederholungsunterstützung geboten. Eine vollständige Spezifikation finden Sie im [.NET Entity Framework-Wiki](https://entityframework.codeplex.com/wikipage?title=Connection%20Resiliency%20Spec) auf Codeplex. Die wichtigsten Features des Wiederholungsmechanismus sind:

* Die primäre Abstraktion ist die **IDbExecutionStrategy**-Schnittstelle. Diese Benutzeroberfläche:
  * Definiert die synchrone und asynchrone Methoden zum **Ausführen***.
  * Definiert Klassen, die direkt verwendet oder in einem Datenbankkontext als eine Standardstrategie konfiguriert werden können, einem Anbieternamen oder einem Anbieternamen und Servernamen zugeordnet werden können. Wenn sie für einen Kontext konfiguriert ist, treten Wiederholungen auf der Ebene der einzelnen Datenbankvorgängen auf, von denen es möglicherweise mehrere für einen angegebenen Kontext gibt.
  * Definiert, wann und wie versucht wird, einen fehlgeschlagene Verbindung erneut herzustellen.
* Sie umfasst mehrere integrierte Implementierungen für die **IDbExecutionStrategy**-Schnittstelle:
  * Standard - keine Wiederholung.
  * Standard für SQL-Datenbank (automatisch) - kein erneuter Versuch, es werden jedoch Ausnahmen überprüft und in der SQL-Datenbank-Strategie mit der Empfehlung zur Verwendung eingebunden.
  * Standardeinstellung für SQL-Datenbank - exponentiell (von der Basisklasse geerbt) plus SQL-Datenbank Erkennungslogik.
* Sie implementiert eine exponentielle Backoff-Strategie, die einen Zufallsgenerator enthält.
* Die integrierten Wiederholungsklassen sind zustandsbehaftet und nicht threadsicher. Sie können jedoch wiederverwendet werden, nachdem der aktuelle Vorgang abgeschlossen ist.
* Wenn die angegebene Wiederholungsanzahl überschritten wird, werden die Ergebnisse in einer neue Ausnahme umschlossen. Sie bringt die aktuelle Ausnahme nicht zum Vorschein.

## Richtlinienkonfiguration (SQL-Datenbank mit Entity Framework 6)

Beim Zugriff auf SQL-Datenbank mit Entity Framework 6.0 und höher wird Wiederholungsunterstützung geboten. Wiederholungsrichtlinien werden programmgesteuert konfiguriert. Die Konfiguration kann nicht pro Vorgang geändert werden.

Wenn Sie eine Strategie im Kontext als Standard konfigurieren, geben Sie eine Funktion an, die bei Bedarf eine neue Strategie erstellt. Der folgende Code zeigt, wie Sie eine Wiederholungskonfigurationsklasse erstellen können, die die **DbConfiguration**-Basisklasse erweitert.

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

Sie können diese dann mit der **SetConfiguration**-Methode der **DbConfiguration**-Instanz als Standardwiederholungsstrategie für alle Vorgänge festlegen, wenn die Anwendung gestartet wird. In der Standardeinstellung erkennt EF die Konfigurationsklasse automatisch und verwenden diese.

	DbConfiguration.SetConfiguration(new BloggingContextConfiguration());

Sie können die Wiederholungskonfigurationsklasse für einen Kontext angeben, indem Sie die Kontextklasse mit einem **DbConfigurationType**-Attribut kommentieren. Wenn Sie jedoch über nur eine Konfigurationsklasse verfügen, wird EF diese verwenden, ohne dass der Kontext kommentieren werden muss.

	[DbConfigurationType(typeof(BloggingContextConfiguration))]
	public class BloggingContext : DbContext
	{ ...

Wenn Sie unterschiedliche Wiederholungsstrategien für bestimmte Vorgänge verwenden oder Wiederholungen für bestimmte Vorgänge deaktivieren müssen, können Sie eine Konfigurationsklasse erstellen, mit der Sie Strategien anhalten oder austauschen können, indem ein Flag in **CallContext** setzen. Die Konfigurationsklasse kann dieses Flag verwenden, um Strategien zu wechseln oder die Strategie zu deaktivieren, die Sie bereitstellen und eine Standardstrategie verwenden. Weitere Informationen finden Sie unter [Ausführungsstrategie anhalten](http://msdn.microsoft.com/dn307226#transactions_workarounds) auf der Seite Ausführungsstrategien wiederholen (EF6 oder höher).

Eine andere Technik für die Verwendung von bestimmten Wiederholungsstrategien für einzelne Vorgänge ist das Erstellen einer Instanz der erforderlichen Strategieklasse und das Liefern der gewünschten Einstellungen mithilfe von Parametern. Rufen Sie dann die **ExecuteAsync**-Methode auf.

	var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
	var blogs = await executionStrategy.ExecuteAsync(
	    async () =>
	    {
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Acquire some values asynchronously and return them
	        }
	    },
	    new CancellationToken()
	);

Die einfachste Möglichkeit zum Verwenden einer **DbConfiguration**-Klasse ist es, diese in der gleichen Assembly wie die **DbContext** Klasse zu platzieren. Dies ist jedoch nicht geeignet, wenn der gleiche Kontext in verschiedenen Szenarios benötigt wird, wie z. B. bei anderen interaktiven Strategien und Hintergrundwiederholungsstrategien. Wenn die unterschiedlichen Kontexte in separaten AppDomains ausgeführt werden, können Sie die integrierte Unterstützung für das Angeben von Konfigurationsklassen in der Konfigurationsdatei verwenden oder explizit mithilfe von Code festlegen. Wenn die unterschiedlichen Kontexte in derselben AppDomain ausgeführt werden müssen, ist eine benutzerdefinierte Lösung erforderlich.

Weitere Informationen finden Sie unter [Codebasierte Konfiguration (EF6 oder höher)](http://msdn.microsoft.com/data/jj680699.aspx).

Die folgende Tabelle zeigt die Standardeinstellungen für die integrierte Wiederholungsrichtlinie bei Verwendung von EF6.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable4.png)
## Gebrauchsanleitung Wiederholungen

Beachten Sie den Zugriff auf SQL-Datenbank mit EF6 die folgenden Richtlinien:

* Wählen Sie die entsprechende Dienstoption (freigegeben oder Premium). Bei einer freigegebenen Instanz kann es möglicherweise zu längeren Verbindungsverzögerungen als üblich und Drosselung durch die Verwendung des gemeinsam genutzten Servers durch andere Mandanten kommen. Wenn Vorgänge mit vorhersagbarer Leistung und zuverlässig geringe Latenz erforderlich sind, sollten Sie die Premium-Option wählen.
* Eine Strategie mit festgelegtem Intervall wird für die Verwendung mit Azure SQL-Datenbank nicht empfohlen. Verwenden Sie stattdessen eine exponentielle Backoff-Strategie, da der Dienst möglicherweise überlastet ist und längere Verzögerungen mehr Zeit für die Wiederherstellung einräumen.
* Wählen Sie einen geeigneten Wert für die Verbindungs- und Befehls-Timeouts bei der Definition von Verbindungen. Stützen Sie das Timeout auf Ihren Geschäftslogikentwurf und auf Tests. Sie müssen diesen Wert möglicherweise mit der Zeit ändern, wenn sich die Datenmengen oder Geschäftsprozesse ändern. Ein zu kurzes Timeout kann zu vorzeitigen Fehlern bei Verbindungen führen, wenn die Datenbank ausgelastet ist. Ein zu langes Timeout kann verhindern, dass die Wiederholungslogik ordnungsgemäß funktioniert, da sie vor der Erkennung einer fehlgeschlagenen Verbindung zu lange wartet. Der Wert für das Timeout ist eine Komponente der End-to-End-Latenz, auch es nicht leicht ist zu ermitteln, wie viele Befehle beim Speichern des Kontexts ausgeführt werden. Sie können das Standardtimeout ändern, indem Sie die Einstellung der **CommandTimeout**-Eigenschaft der **DbContext**-Instanz festlegen.
* Entity Framework unterstützt Wiederholungskonfigurationen, die in Konfigurationsdateien definiert sind. Für maximale Flexibilität in Azure sollten Sie allerdings die Konfiguration in der Anwendung programmgesteuert erstellen. Die einzelnen Parameter für die Wiederholungsrichtlinien, wie z. B. die Anzahl der Wiederholungen und die Wiederholungsintervalle können in der Dienstkonfigurationsdatei gespeichert und zur Laufzeit verwendet werden, um die entsprechenden Richtlinien zu erstellen. Dadurch können die Einstellungen darin geändert werden, wodurch die Anwendung neu gestartet werden muss.

Erwägen Sie, mit den folgenden Einstellungen für Wiederholungsvorgänge zu beginnen. Sie können die Verzögerung zwischen den Wiederholungen nicht angeben (sie ist als eine exponentielle Sequenz festgelegt und generiert). Sofern Sie keine benutzerdefinierte Wiederholungsstrategie erstellen, können Sie wie hier gezeigt nur die maximalen Werte angeben. Hierbei handelt es sich um allgemeine Einstellungen und Sie sollten die Vorgänge überwachen und die Werte entsprechend Ihrem Szenario optimieren.

| **Context** | **Beispiel-Ziel E2E<br />Maximale Wartezeit** | **Wiederholungsrichtlinie** | **Einstellungen** | **Werte** | **So funktioniert's** |
|----------------------|-----------------------------------|--------------------|------------------------|--------------|-----------------------------------------------------------------------------------------------------------------------------|
| Interaktiv, Benutzeroberfläche<br />oder Vordergrund | 2 Sekunden | Exponentiell | MaxRetryCount<br />MaxDelay | 3<br />750 ms | Versuch 1 - Verzögerung 0 Sek<br />Versuch 2 - Verzögerung 750 ms<br />Versuch 3 - Verzögerung 750 ms |
| Hintergrund<br /> oder Batch | 30 Sekunden | Exponentiell | MaxRetryCount<br />MaxDelay | 5<br />12 Sekunden | Versuch 1 - Verzögerung 0 Sek<br />Versuch 2 - Verzögerung ca. 1 Sek<br />Versuch 3 - Verzögerung ca. 3 Sek<br />Versuch 4 - Verzögerung ca. 7 Sek<br />Versuch 5 - Verzögerung 12 Sek |

> [AZURE.NOTE]Die End-to-End-Latenzziele setzen das Standardtimeout für Verbindungen mit dem Dienst voraus. Wenn Sie längere Verbindungstimeouts angeben, wird die End-to-End-Latenz durch diese zusätzliche Zeit für jeden Wiederholungsversuch erweitert.

## Beispiele (SQL-Datenbank mit Entity Framework 6)

Im folgenden Codebeispiel wird eine einfachen Datenzugriffslösung definiert, die Entity Framework verwendet. Eine bestimmte Wiederholungsstrategie wird durch die Definition einer Instanz einer Klasse mit dem Namen **BlogConfiguration** festgelegt, die **DbConfiguration** erweitert.

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
	public class BlogConfiguration : DbConfiguration
	{
	    public BlogConfiguration()
	    {
	        // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
	        // These values could be loaded from configuration rather than being hard-coded.
	        this.SetExecutionStrategy(
	                "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
	    }
	}

	// Specify the configuration type if more than one has been defined.
	// [DbConfigurationType(typeof(BlogConfiguration))]
	public class BloggingContext : DbContext
	{
	    // Definition of content goes here.
	}

	class EF6CodeSamples
	{
	    public async static Task Samples()
	    {
	        // Execution strategy configured by DbConfiguration subclass, discovered automatically or
	        // or explicitly indicated through configuration or with an attribute. Default is no retries.
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Add, edit, delete blog items here, then:
	            await db.SaveChangesAsync();
	        }
	    }
	}
}
```

Weitere Beispiele zur Verwendung des Entity Framework Wiederholungsmechanismus finden Sie unter [Verbindungsstabilität / Wiederholungslogik](http://msdn.microsoft.com/data/dn456835.aspx).

## Weitere Informationen

* [Anleitung zu Leistung und Flexibilität von Azure SQL-Datenbanken](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## SQL-Datenbank mit ADO.NET Wiederholungsrichtlinien

SQL-Datenbank ist eine gehostete SQL-Datenbank, die in unterschiedlichen Größen und als Standard (freigegeben) und Premium (nicht freigegebenen)-Dienst verfügbar ist.

### Wiederholungsmechanismus

SQL-Datenbank hat keine integrierte Unterstützung für Wiederholungen, wenn auf diese mit ADO.NET zugegriffen wird. Allerdings können die Rückgabecodes von Anforderungen verwendet werden, um zu bestimmen, warum eine Anforderung fehlgeschlagen ist. Die Seite [Azure SQL-Datenbank-Drosselung ](http://msdn.microsoft.com/library/dn338079.aspx) erläutert, wie Drosselung Verbindungen verhindern kann, zeigt Rückgabecodes für bestimmte Situationen und wie Sie diese und Wiederholungsvorgänge behandeln können.

Sie können den Anwendungsblock zur Handhabung vorübergehender Fehler (Topaz) mit dem NuGet-Paket EnterpriseLibrary.TransientFaultHandling.Data (Klasse **SqlAzureTransientErrorDetectionStrategy**) verwenden, um einen Wiederholungsmechanismus für die SQL-Datenbank zu implementieren.

Der Block bietet außerdem die **ReliableSqlConnection** -Klasse, die die alte ADO.NET 1.0-API (** IDbConnection ** anstelle von **DbConnection**) implementiert und Wiederholungen und Verbindungsverwaltung intern ausführt. Obwohl das praktisch ist, müssen Sie zum Aufrufen von Operationen mit Wiederholungen einen anderen Satz von Methodenverwenden, es handelt sich nicht um einen einfachen direkten Ersatz. Es unterstützt asynchrone Ausführung nicht, die bei der Implementierung und Verwendung von Azure-Diensten empfohlen wird. Da diese Klasse ADO.NET 1.0 verwendet, zieht er darüber hinaus keinen Nutzen aus den neusten Verbesserungen und Updates von ADO.NET.

### Richtlinienkonfiguration (SQL-Datenbank mit ADO.NET)

Der Anwendungsblock zur Handhabung vorübergehender Fehler unterstützt sowohl die dateibasierte als auch die programmgesteuerte Konfiguration. Im Allgemeinen sollten Sie für maximale Flexibilität programmgesteuerte Konfiguration verwenden (für weitere Informationen siehe Hinweise im folgenden Abschnitt). Der folgende Code würde einmal beim Start der Anwendung ausgeführt werden, er erstellt einen **RetryManager** und füllt diesen mit Liste von vier Wiederholungsstrategien aus, die für die Verwendung mit Azure SQL-Datenbanken geeignet sind. Außerdem stellt er die Standardstrategien für den **RetryManager** ein. Hierbei handelt es sich um die Strategien, die für Verbindungen und Befehle verwendet werden, wenn bei der Erstellung einer Verbindung oder eines Befehl keine Alternative angegeben wird.

```csharp
RetryManager.SetDefault(new RetryManager(
	new List<RetryStrategy> { new ExponentialBackoff(name: "default", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql connection", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql command", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "alt sql", retryCount: 5,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true), },
	"default",
	new Dictionary<string, string> {
	    {
	    RetryManagerSqlExtensions.DefaultStrategyConnectionTechnologyName, "default sql connection"
	    },
	    {
	    RetryManagerSqlExtensions.DefaultStrategyCommandTechnologyName, "default sql command"}
	    }));
```

Informationen zur Verwendung der von Ihnen konfigurierten Wiederholungsrichtlinien beim Zugriff auf die Azure SQL-Datenbank finden Sie unter dem Abschnitt [Beispiele](#examples-sql-database-using-ado-net-) weiter unten.

Standardstrategien für den Anwendungsblock zur Handhabung vorübergehender Fehler werden im Abschnitt [Strategien für den Anwendungsblock zur Handhabung vorübergehender Fehler (Topaz)](#transient-fault-handling-application-block-topaz-strategies) am Ende dieses Handbuchs gezeigt.

### Gebrauchsanleitung Wiederholungen

Beachten Sie den Zugriff auf SQL-Datenbank mit ADO.NET die folgenden Richtlinien:

* Wählen Sie die entsprechende Dienstoption (freigegeben oder Premium). Bei einer freigegebenen Instanz kann es möglicherweise zu längeren Verbindungsverzögerungen als üblich und Drosselung durch die Verwendung des gemeinsam genutzten Servers durch andere Mandanten kommen. Wenn Vorgänge mit vorhersagbarerer Leistung und zuverlässig geringe Latenz erforderlich sind, sollten Sie die Premium-Option wählen.
* Stellen Sie sicher, dass Sie Wiederholungen auf der entsprechenden Ebene oder im entsprechenden Bereich durchführen, um nicht-idempotente Operationen zu vermeiden, die Dateninkonsistenzen verursachen. Im Idealfall sollten alle Vorgänge idempotent sein, sodass sie ohne Inkonsistenzen wiederholt werden können. Wenn dies nicht der Fall ist, sollte die Wiederholung auf einer Ebene oder in einem Bereich durchgeführt werden, die ermöglichen, dass alle zugehörigen Änderungen rückgängig gemacht werden können, wenn ein Vorgang fehlschlägt; zum Beispiel im Transaktionsbereich. Weitere Informationen finden Sie unter [Clouddienst-Grundlagen Datenzugriffsebene - Handhabung vorübergehender Fehler](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).
* Eine Strategie mit festgelegtem Intervall wird für die Verwendung mit Azure SQL-Datenbank nicht empfohlen, mit Ausnahme von interaktiven Szenarien, wo nur wenige Wiederholungen in sehr kurzen Intervallen vorkommen. Ziehen Sie stattdessen eine exponentielle Backoff-Strategie für die meisten Szenarien in Betracht.
* Wählen Sie einen geeigneten Wert für die Verbindungs- und Befehls-Timeouts bei der Definition von Verbindungen. Ein zu kurzes Timeout kann zu vorzeitigen Fehlern bei Verbindungen führen, wenn die Datenbank ausgelastet ist. Ein zu langes Timeout kann verhindern, dass die Wiederholungslogik ordnungsgemäß funktioniert, da sie vor der Erkennung einer fehlgeschlagenen Verbindung zu lange wartet. Der Wert für das Timeout ist eine Komponente der End-to-End-Latenz. Sie wird effektiv der Wiederholungsverzögerung hinzugefügt, die in der Wiederholungsrichtlinie für jeden Wiederholungsversuch festgelegt ist.
* Schließen Sie die Verbindung nach einer bestimmten Anzahl von Wiederholungen, selbst wenn Sie eine exponentielle Backoff-Wiederholungslogik verwenden, und wiederholen Sie den Vorgang auf einer neuen Verbindung. Das mehrmalige Wiederholen des gleichen Vorgangs für dieselbe Verbindung kann ein Faktor sein, der zu Verbindungsproblemen beiträgt. Beispiele für dieses Verfahrens finden Sie unter [Clouddienst-Grundlagen Datenzugriffsebene - Handhabung vorübergehender Fehler](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).
* Wenn Verbindungspooling verwendet wird (Standardeinstellung), besteht die Möglichkeit, dass auch nach dem Schließen und erneuten Öffnen einer Verbindung dieselbe Verbindung aus dem Pool ausgewählt wird. Wenn dies der Fall ist, ist ein Verfahren zu Behebung das Aufrufen der **ClearPool**-Methode der **SqlConnection**-Klasse, um die Verbindung als nicht wiederverwendbar zu markieren. Allerdings sollten Sie dies erst nach mehreren fehlgeschlagenen Verbindungsversuchen tun und nur, wenn die spezifische Klasse des vorübergehende Fehler wie z. B. SQL-Timeouts (Fehlercode: -2) im Zusammenhang mit fehlerhaften Verbindungen auftritt.
* Wenn der Datenzugriffscode als **TransactionScope**-Instanzen initiierte Transaktionen verwendet, sollte die Wiederholungslogik erneut eine Verbindung herstellen und einen neuen Transaktionsbereich initiieren. Aus diesem Grund sollte der wiederholbare Codeblock den gesamten Bereich der Transaktion umfassen.
* Der Anwendungsblock zur Handhabung vorübergehender Fehler unterstützt Wiederholungskonfigurationen, die vollständig in Konfigurationsdateien definiert sind. Für maximale Flexibilität in Azure sollten Sie allerdings die Konfiguration in der Anwendung programmgesteuert erstellen. Die einzelnen Parameter für die Wiederholungsrichtlinien, wie z. B. die Anzahl der Wiederholungen und die Wiederholungsintervalle können in der Dienstkonfigurationsdatei gespeichert und zur Laufzeit verwendet werden, um die entsprechenden Richtlinien zu erstellen. Dadurch können die Einstellungen darin geändert werden, wodurch die Anwendung neu gestartet werden muss.

Erwägen Sie, mit den folgenden Einstellungen für Wiederholungsvorgänge zu beginnen. Hierbei handelt es sich um allgemeine Einstellungen und Sie sollten die Vorgänge überwachen und die Werte entsprechend Ihrem Szenario optimieren.

| **Context** | **Beispiel-Ziel E2E<br />Maximale Wartezeit** | **Wiederholungsstrategie** | **Einstellungen** | **Werte** | **So funktioniert's** |
|----------------------|-----------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interaktiv, Benutzeroberfläche<br />oder Vordergrund | 2 Sek | FixedInterval | Anzahl der Wiederholungen<br />Wiederholungsintervall<br />Erster schneller Wiederholungsversuch | 3<br />500 ms<br />true | Versuch 1 - Verzögerung 0 Sek<br />Versuch 2 - Verzögerung 500 ms<br />Versuch 3 - Verzögerung 500 ms |
| Hintergrund<br />oder Batch | 30 Sek | ExponentialBackoff | Anzahl der Wiederholungen<br />Minimales Backoff<br />Maximales Backoff<br />Delta Backoff-<br />Erste schnelle Wiederholung | 5<br />0 Sek<br />60 Sek<br />2 Sek<br />false | Versuch 1 - Verzögerung 0 Sek<br />Versuch 2 - Verzögerung ca. 2 Sek<br />Versuch 3 - Verzögerung ca. 6 Sek<br />Versuch 4 - Verzögerung ca. 14 Sek<br />Versuch 5 - Verzögerung 30 Sek |

> [AZURE.NOTE]Die End-to-End-Latenzziele setzen das Standardtimeout für Verbindungen mit dem Dienst voraus. Wenn Sie längere Verbindungstimeouts angeben, wird die End-to-End-Latenz durch diese zusätzliche Zeit für jeden Wiederholungsversuch erweitert.

### Beispiele (SQL-Datenbank mit ADO.NET)

Dieser Abschnitt beschreibt die Verwendung des Anwendungsblocks zur Handhabung vorübergehender Fehler für den Zugriff auf die Azure SQL-Datenbank mit einem Satz von Wiederholungsrichtlinien, die Sie im **RetryManager** konfiguriert haben (wie im vorherigen Abschnitt [Richtlinienkonfiguration](#policy-configuration-sql-database-using-ado-net-) gezeigt). Die einfachste Methode zur Verwendung des Blocks ist über die **ReliableSqlConnection**-Klasse oder durch Aufruf der Erweiterungsmethoden wie z. B. **OpenWithRetry** für eine Verbindung (siehe [Anwendungsblock zur Handhabung vorübergehender Fehler](http://msdn.microsoft.com/library/hh680934.aspx) für weitere Informationen).

Allerdings unterstützten in der aktuellen Version des Anwendungsblocks zur Handhabung vorübergehender Fehler diese Ansätze asynchrone Vorgänge nicht direkt für SQL-Datenbank. Die bewährte Praxis verlangt, dass Sie nur asynchrone Methoden verwenden, um auf Azure-Dienste wie z. B. SQL-Datenbank zuzugreifen, daher sollten Sie die folgenden Methoden in Betracht ziehen, um den Anwendungsblock zur Handhabung vorübergehender Fehler mit SQL-Datenbank zu verwenden.

Sie können die vereinfachte asynchrone Unterstützung in Version 5 der C#-Sprache verwenden, um asynchrone Versionen der Methoden zu erstellen, die vom Block zur Verfügung gestellt werden. Der folgende Code zeigt z. B. die Erstellung einer asynchronen Version der **ExecuteReaderWithRetry**-Erweiterungsmethode. Die Änderungen und Erweiterungen des ursprünglichen Codes werden hervorgehoben. Der Quellcode für Topaz steht auf GitHub unter [Anwendungsblock zur Handhabung vorübergehender Fehler ("Topaz")](http://topaz.codeplex.com/SourceControl/latest) zur Verfügung.

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command, RetryPolicy cmdRetryPolicy,
RetryPolicy conRetryPolicy)
{
	GuardConnectionIsNotNull(command);

	// Check if retry policy was specified, if not, use the default retry policy.
	return await (cmdRetryPolicy ?? RetryPolicy.NoRetry).ExecuteAsync(async () =>
	{
	    var hasOpenConnection = await EnsureValidConnectionAsync(command, conRetryPolicy).ConfigureAwait(false);

	    try
	    {
	        return await command.ExecuteReaderAsync().ConfigureAwait(false);
	    }
	    catch (Exception)
	    {
	        if (hasOpenConnection && command.Connection != null && command.Connection.State == ConnectionState.Open)
	        {
	            command.Connection.Close();
	        }

	        throw;
	    }
	}).ConfigureAwait(false);
}
```

Diese neue asynchrone Erweiterungsmethode kann auf die gleiche Weise wie die synchronen Versionen innerhalb des Blocks verwendet werden.

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

var retryPolicy =
	RetryManager.Instance.GetRetryPolicy<SqlDatabaseTransientErrorDetectionStrategy>("alt sql");
using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync(retryPolicy))
{
	// Do something with the values
}
```

Dieser Ansatz behandelt jedoch nur einzelne Vorgänge oder Befehle und keine Blöcke von Anweisungen, in denen es genau definierte Transaktionsgrenzen geben kann. Darüber hinaus befasst er sich nicht mit dem Entfernen fehlerhafter Verbindungen aus dem Verbindungspool, so dass sie für nachfolgende Versuche nicht aktiviert sind. Ein synchrones Beispiel für das Lösen dieser Probleme finden Sie im [Clouddienst-Grundlagen Datenzugriffsebene - vorübergehende Fehlerbehandlung](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Timeouts_amp_Connection_Management). Zusätzlich zu erneuten Versuchen mit beliebigen Sequenzen von Datenbank-Anweisungen löscht der den Verbindungspool, um ungültige Verbindungen zu entfernen und instrumentiert den gesamten Prozess. Der in diesem Beispiel gezeigte Code wird synchron ausgeführt, es ist aber relativ einfach, diesen in asynchronen Code zu konvertieren.

### Weitere Informationen

Ausführliche Informationen zur Verwendung des Anwendungsblocks zur Handhabung von vorübergehenden Fehlern finden Sie unter:

* [Verwenden des Anwendungsblocks zur Handhabung von vorübergehenden Fehlern mit SQL Azure ](http://msdn.microsoft.com/library/hh680899.aspx)
* [Hartnäckigkeit, das Geheimnis aller Erfolge: Verwenden des Anwendungsblocks für die Behandlung vorübergehender Fehler](http://msdn.microsoft.com/library/dn440719.aspx)
* [Clouddienst-Grundlagen Datenzugriffsebene - Handhabung vorübergehender Fehler](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).

Allgemeine Hinweise, um das Beste aus SQL-Datenbank herauszuholen, finden Sie unter:

* [Anleitung zu Leistung und Flexibilität von Azure SQL-Datenbanken](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)
* [Minimieren von Verbindungspoolfehlern in SQL Azure](http://blogs.msdn.com/b/adonet/archive/2011/11/05/minimizing-connection-pool-errors-in-sql-azure.aspx)

## Wiederholungsrichtlinien Service Bus

Service Bus ist ein Cloud Messaging-Plattform, die lose gekoppelten Nachrichtenaustausch mit verbesserter Skalierbarkeit und Stabilität für die Komponenten einer Anwendung bietet, egal ob in der Cloud oder lokal gehostet.

### Wiederholungsmechanismus

Service Bus implementiert Wiederholungen mithilfe von Implementierungen der [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx)-Basisklasse. Alle Service Bus-Clients machen eine **RetryPolicy**-Eigenschaft verfügbar, die auf eine der Implementierungen der **RetryPolicy**-Basisklasse festgelegt werden kann. Die integrierten Implementierungen sind:

* Die [RetryExponential Class](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx). Dies macht Eigenschaften verfügbar, die das Backoff-Intervall, die Anzahl der Wiederholungsversuche und die **TerminationTimeBuffer**-Eigenschaft steuern, die verwendet wird, um die Gesamtzeit für die Ausführung des Vorgangs zu beschränken.
* Die [NoRetry-Klasse](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx). Dies wird verwendet, wenn Wiederholungen auf der Ebene des Service Bus-API Levels nicht erforderlich sind, z. B. wenn Wiederholungen von einem anderen Prozess als Teil eines Batches oder als Vorgang mit mehreren Schritten verwaltet werden.

Service Bus-Aktionen können einen Reihe von Ausnahmen zurückgeben, wie im [Anhang: Messagingausnahmen](http://msdn.microsoft.com/library/hh418082.aspx) aufgeführt. Die Liste enthält Informationen darüber, ob diese für die Wiederholung des Vorgangs geeignet ist. Angenommen, eine [ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) gibt an, dass der Client für eine bestimmte Zeitspanne warten sollte, bevor der Vorgang wiederholt wird. Das Auftreten einer **ServerBusyException** bewirkt auch, dass Service Bus in einen anderen Modus wechselt, in dem den berechneten Wiederholungsverzögerungen zusätzliche 10 Sekunden hinzugefügt werden. Dieser Modus wird nach kurzer Zeit zurückgesetzt.

Die vom Service Bus zurückgegebenen Ausnahmen machen die **IsTransient** -Eigenschaft verfügbar, die angibt, ob der Client den Vorgang wiederholen soll. Die integrierte **RetryExponential**-Richtlinie stützt sich auf die **IsTransient**-Eigenschaft in der **MessagingException**-Klasse, die die Basisklasse für alle Service Bus-Ausnahmen ist. Bei der Erstellung benutzerdefinierter Implementierungen der **RetryPolicy**-Basisklasse können Sie eine Kombination aus Ausnahmetyp und **IsTransient**-Eigenschaft verwenden, um eine genauere Kontrolle über Wiederholungsaktionen zu ermöglichen. Sie könnten z. B. eine **QuotaExceededException** erkennen und Maßnahmen ergreifen, um die Warteschlange zu leeren, bevor erneut versucht wird, über sie eine Nachricht zu senden.

### Richtlinienkonfiguration (Service Bus)

Wiederholungsrichtlinien werden programmgesteuert festgelegt und können als Standardrichtlinie für einen **NamespaceManager** und für eine **MessagingFactory** oder einzeln für jeden Messaging Client festgelegt werden. Um die Standard-Wiederholungsrichtlinie für die Messaging-Sitzung festzulegen, legen Sie die **RetryPolicy** von **NamespaceManager** fest.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                             maxBackoff: TimeSpan.FromSeconds(30),
	                                                             deltaBackoff: TimeSpan.FromSeconds(2),
	                                                             terminationTimeBuffer: TimeSpan.FromSeconds(5),
	                                                             maxRetryCount: 3);

Beachten Sie, das in diesem Code aus Gründen der Übersichtlichkeit benannte Parameter verwendet werden. Alternativ können Sie die Namen auslassen, da keiner der Parameter optional ist.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(TimeSpan.FromSeconds(0.1),
	                 TimeSpan.FromSeconds(30), TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(5), 3);

Legen Sie zum Festlegen der Standard Wiederholungsrichtlinie für alle Clients einer Messaging-Factory die **RetryPolicy** von **MessagingFactory** fest.

	messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                    maxBackoff: TimeSpan.FromSeconds(30),
	                                                    deltaBackoff: TimeSpan.FromSeconds(2),
	                                                    terminationTimeBuffer: TimeSpan.FromSeconds(5),
	                                                    maxRetryCount: 3);

Um die Standard-Wiederholungsrichtlinie für einen Messaging Client festzulegen oder die Standardrichtlinie zu überschreiben, legen Sie seine **RetryPolicy**-Eigenschaft mit einer Instanz der erforderlichen Richtlinienklasse fest:

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                        maxBackoff: TimeSpan.FromSeconds(30),
	                                        deltaBackoff: TimeSpan.FromSeconds(2),
	                                        terminationTimeBuffer: TimeSpan.FromSeconds(5),
	                                        maxRetryCount: 3);
```

Die Wiederholungsrichtlinie kann auf der individuellen Vorgangsebene festgelegt werden. Sie gilt für alle Vorgänge des Messagin-Client. Die folgende Tabelle zeigt die Standardeinstellungen für die integrierte Wiederholungsrichtlinie.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable7.png)

### Gebrauchsanleitung Wiederholungen

Berücksichtigen Sie bei Verwendung von Service Bus die folgenden Richtlinien:

* Implementieren Sie bei Verwendung der integrierten **RetryExponential**-Implementierung keinen Fallbackvorgang, da die Richtlinie auf Server Busy-Ausnahmen reagiert und automatisch in einen entsprechenden Wiederholungsmodus wechselt.
* Service Bus unterstützt eine Funktion namens Paired Namespace, mit der automatisches Failover zu einer Backup-Warteschlange in einem separaten Namespace implementiert wird, falls die Warteschlange im primären Namespace fehlschlägt. Nachrichten aus der sekundären Warteschlange können an die primäre Warteschlange gesendet werden, wenn diese wiederhergestellt wird. Mit dieser Funktion können vorübergehender Fehler behandelt werden. Weitere Informationen finden Sie unter [Asynchronen Nachrichtenmuster und hohe Verfügbarkeit](http://msdn.microsoft.com/library/azure/dn292562.aspx).

Erwägen Sie, mit den folgenden Einstellungen für Wiederholungsvorgänge zu beginnen. Hierbei handelt es sich um allgemeine Einstellungen und Sie sollten die Vorgänge überwachen und die Werte entsprechend Ihrem Szenario optimieren.


![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable8.png)

### Telemetrie

Service Bus protokollieren Wiederholungen als ETW-Ereignisse mit einer **EventSource**. Sie müssen einen **EventListener** an die Ereignisquelle anfügen, um die Ereignisse zu erfassen und in Performance-Viewer anzuzeigen oder diese in ein geeignetes Ziel-Protokoll schreiben. Dazu können Sie den [Anwendungsblock Semantic Logging](http://msdn.microsoft.com/library/dn775006.aspx) verwenden. Die Wiederholungsereignisse sind im folgenden Format:

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### Beispiele (Service Bus)

Das folgende Codebeispiel zeigt, wie Sie die Wiederholungsrichtlinie festlegen für:

* Einen Namespace-Manager. Die Richtlinie gilt für alle Vorgänge dieses Managers und kann nicht durch einzelne Vorgänge überschrieben werden.
* Eine Messaging-Factory. Die Richtlinie gilt für alle Clients, die von dieser Factory erstellt wurden, und kann bei der Erstellung einzelner Clients nicht überschrieben werden.
* Ein einzelner Messaging Client. Nachdem ein Client erstellt wurde, können Sie die Wiederholungsrichtlinie für diesen festlegen. Die Richtlinie gilt für alle Vorgänge auf dem Client.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
	class ServiceBusCodeSamples
	{
		private const string connectionString =
		    @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
		        SharedAccessKeyName=RootManageSharedAccessKey;
		        SharedAccessKey=C99..........Mk=";

		public async static Task Samples()
		{
		    const string QueueName = "TestQueue";

		    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

		    var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

		    // The namespace manager will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for all operations on the namespace manager.
		        namespaceManager.Settings.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                deltaBackoff: TimeSpan.FromSeconds(1.75),
		                terminationTimeBuffer: TimeSpan.FromSeconds(4),
		                maxRetryCount: 3);

		        // Policies cannot be specified on a per-operation basis.
		        if (!await namespaceManager.QueueExistsAsync(QueueName))
		        {
		            await namespaceManager.CreateQueueAsync(QueueName);
		        }
		    }


		    var messagingFactory = MessagingFactory.Create(
		        namespaceManager.Address, namespaceManager.Settings.TokenProvider);
		    // The messaging factory will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for clients created from it.
		        messagingFactory.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                deltaBackoff: TimeSpan.FromSeconds(2),
		                terminationTimeBuffer: TimeSpan.FromSeconds(5),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await messagingFactory.AcceptMessageSessionAsync();
		    }


		    {
		        var client = messagingFactory.CreateQueueClient(QueueName);
		        // The client inherits the policy from the factory that created it.


		        // Set different values for the retry policy on the client.
		        client.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0.1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                deltaBackoff: TimeSpan.FromSeconds(2),
		                terminationTimeBuffer: TimeSpan.FromSeconds(5),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await client.AcceptMessageSessionAsync();
		    }
		}
	}
}
```

## Weitere Informationen

* [Asynchronen Nachrichtenmuster und hohe Verfügbarkeit](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## Wiederholungsrichtlinien Cache (Redis)

Azure Redis Cache ist ein schneller Datenzugriff und ein Cache Service mit niedriger Latenz, der auf dem beliebten Open Source-Redis Cache basiert. Er ist sicher, von Microsoft verwaltet und ist von jeder Anwendung in Azure zugänglich.

Die Anleitung in diesem Abschnitt beruht auf der Verwendung des StackExchange.Redis-Clients für den Zugriff auf den Cache. Eine Liste der anderer geeigneter Clients finden Sie auf der [Redis-Website](http://redis.io/clients), diese haben möglicherweise unterschiedliche Wiederholungsmechanismen.

Beachten Sie, dass der StackExchange.Redis Client Multiplex über eine einzige Verbindung verwendet. Die empfohlene Verwendung ist das Erstellen einer Instanz des Clients beim Starten der Anwendung und die Verwendung dieser Instanz für alle Vorgänge im Cache. Aus diesem Grund wird die Verbindung mit dem Cache nur einmal hergestellt und die Anleitungen in diesem Abschnitt beziehen sich auf die Wiederholungsrichtlinie für die anfängliche Verbindung - und nicht für jeden Vorgang, der auf den Cache zugreift.

### Wiederholungsmechanismus

Der StackExchange.Redis-Client verwendet eine Verbindungs-Manager-Klasse, die über eine Reihe von Optionen konfiguriert ist. Diese Optionen umfassen eine **ConnectRetry**-Eigenschaft, die angibt, wie oft eine fehlgeschlagene Verbindung zum Cache wiederholt wird. Die Wiederholungsrichtlinie wird jedoch nur für den anfänglichen Verbindungsvorgang verwendet und wartet zwischen den Wiederholungen nicht.

### Richtlinienkonfiguration (Azure Redis Cache)

Wiederholungsrichtlinien werden programmgesteuert durch Festlegen der Optionen für den Client konfiguriert, bevor eine Verbindung mit dem Cache hergestellt wird. Dies kann durch das Erstellen einer Instanz der **ConfigurationOptions**-Klasse, das Auffüllen deren Eigenschaften und die Übergabe an die **Connect** Methode erreicht werden.

```csharp
var options = new ConfigurationOptions { EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000 };
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Beachten Sie, dass die **ConnectTimeout**-Eigenschaft die maximale Wartezeit in Millisekunden angibt und nicht die Verzögerung zwischen Wiederholungen.

Alternativ können Sie die Optionen als Zeichenfolge angeben und diese an die **Connect** Methode übergeben.

```csharp
	var options = "localhost,connectRetry=3,connectTimeout=2000";
	ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Es ist auch möglich, Optionen direkt beim Herstellen der Verbindung mit dem Cache anzugeben.

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

Die folgende Tabelle zeigt die Standardeinstellungen für die integrierte Wiederholungsrichtlinie.

| **Context** | **Einstellung** | **Standardwert**<br />(v 1.0.331) | **Bedeutung** |
|----------------------|-----------------------------------------|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConfigurationOptions | ConnectRetry<br /><br />ConnectTimeout<br /><br />SyncTimeout | 3<br /><br />maximal 5000 ms plus SyncTimeout<br />1000 | Die Anzahl der Wiederholungen von Verbindungsversuchen während des Erstverbindungsvorgangs.<br />Zeitlimit (ms) für Verbindungen. Keine Verzögerung zwischen den Wiederholungsversuchen.<br />Zeit (ms), um synchrone Vorgänge zu ermöglichen. |

> [AZURE.NOTE]SyncTimeout trägt zur End-to-End-Latenz eines Vorgangs bei. Allerdings wird im Allgemeinen die Verwendung von synchronen Vorgängen nicht empfohlen. Weitere Informationen finden Sie unter [Pipelines und Multiplexers](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).

## Gebrauchsanleitung Wiederholungen

Berücksichtigen Sie bei Verwendung von Azure Redis Cache die folgenden Richtlinien :

* Der StackExchange Redis-Client verwaltet seine eigene Wiederholungen, aber nur beim Herstellen einer Verbindung mit dem Cache beim ersten Starten der Anwendung. Sie können das Verbindungstimeout und die Anzahl der Wiederholungsversuche für die Herstellung dieser Verbindung konfigurieren, die Wiederholungsrichtlinie gilt jedoch nicht für Vorgänge für den Cache.
* Der Wiederholungsmechanismus hat keine Verzögerung zwischen Wiederholungsversuchen. Nach dem angegebene Verbindungstimeout und für die angegebene Anzahl von Malen wiederholt er einfach eine fehlgeschlagene Verbindung.
* Anstatt eine große Anzahl von Wiederholungsversuchen zu verwenden, sollten Sie auf die ursprüngliche Datenquelle zurückgreifen.

## Telemetrie

Sie können Informationen zu Verbindungen (aber nicht für andere Vorgänge) mit einem **TextWriter** sammeln.

```csharp
var writer = new StringWriter();
...
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Ein Beispiel für die dadurch generierte Ausgabe ist unten dargestellt.

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

## Beispiele (Azure Redis Cache)

Das folgende Codebeispiel zeigt, wie Sie Einstellung für das Verbindungstimeout und die Anzahl der Wiederholungen beim Initialisieren des StackExchange.Redis-Clients für den Zugriff auf Azure Redis Cache beim Start der Anwendung konfigurieren können. Beachten Sie, dass das Verbindungstimeout die Zeitdauer ist, die Sie bereit sind, auf die Verbindung mit dem Cache zu warten; es handelt sich nicht um die Verzögerung zwischen den Wiederholungsversuchen.

In diesem Beispiel wird veranschaulicht, wie die Konfiguration mit einer Instanz der **ConfigurationOptions** eingestellt wird.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using object-based configuration.
	                var options = new ConfigurationOptions
	                                    {
	                                        EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000  // The maximum waiting time (ms), not the delay for retries.
	                                    };
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

Dieses Beispiel zeigt, wie die Konfiguration durch Angeben der Optionen als Zeichenfolge festgelegt wird.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using string-based configuration.
	                var options = "localhost,connectRetry=3,connectTimeout=2000";
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

Weitere Beispiele finden Sie unter [Konfiguration](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) auf der Projektwebsite.

## Weitere Informationen

* [Redis-Website](http://redis.io/)

## Wiederholungsrichtlinien von DocumentDB (Vorabversion)

DocumentDB ist eine vollständig verwaltete Dokumentendatenbank mit umfangreichen Abfrage- und Indexierungsfunktionen über ein schemafreies JSON-Datenmodell. Sie bietet konfigurierbare und zuverlässige Leistung, systemeigene JavaScript-Transaktionsverarbeitung und ist für die Cloud mit elastischer Skalierung konzipiert.

## Wiederholungsmechanismus

Die Vorabversion des DocumentDB-Clients enthält einen internen und nicht konfigurierbaren Wiederholungsmechanismus (dies kann sich in zukünftigen Versionen ändern). Die Standardeinstellungen variieren je nach Kontext der Verwendung. Einige Vorgänge verwenden eine exponentielle Backoff-Strategie mit hartcodierten Parametern. Andere geben nur an, wie viele Wiederholungsversuche unternommen werden sollen und verwenden die Wiederholungsverzögerung in der [DocumentClientException](http://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)-Instanz, die vom Dienst zurückgegeben wird. Es wird eine Verzögerung von fünf Sekunden verwendet, wenn keine Verzögerung angegeben wird.

## Richtlinienkonfiguration (DocumentDB)

Keine. Alle zur Implementierung von Wiederholungen verwendeten Klassen sind intern. Die Wiederholungsparameter sind entweder Konstanten oder werden unter Verwendung von Parametern zu Klassenkonstruktor festgelegt.

Die folgende Tabelle zeigt die Standardeinstellungen für die integrierte Wiederholungsrichtlinie.

| **Context** | **Einstellungen** | **Werte** | **So funktioniert's** |
|------------------------|---------------------------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RetryPolicy (intern) | MaxRetryAttemptsOnQuery<br /><br />MaxRetryAttemptsOnRequest | 3<br /><br />0 | Die Anzahl der Wiederholungsversuche für Dokument-Abfragen. Dieser Wert kann nicht geändert werden.<br />Die Anzahl der Wiederholungsversuche für andere Anforderungen. Dieser Wert kann nicht geändert werden. |

## Gebrauchsanleitung Wiederholungen

Berücksichtigen Sie Bei Verwendung von DocumentDB die folgenden Richtlinien :

* Sie können die Standard-Wiederholungsrichtlinie nicht ändern.
* Weitere Informationen zu den Standardeinstellungen finden Sie unter [TBD].

## Telemetrie

Wiederholungsversuche werden als unstrukturierte Ablaufverfolgungsmeldungen über eine .NET **TraceSource** protokolliert. Sie müssen einen **TraceListener** konfigurieren, um die Ereignisse zu erfassen und diese in ein geeignetes Zielprotokoll zu schreiben.

## Durchsuchen von Wiederholungsrichtlinien

Azure Search kann dafür verwendet werden, nützliche und anspruchsvolle Suchfunktionen zu einer Website oder Anwendung hinzuzufügen, Suchergebnisse schnell und einfach zu optimieren und umfassende und fein abgestimmte Rangmodelle zu erstellen.

### Wiederholungsmechanismus

Es gibt keinen integrierten Wiederholungsmechanismus für die Suche, wie es in der Regel bei HTTP-Anforderungen der Fall ist. Zum Implementieren von Wiederholungen können Sie eine generische Implementierung eines REST-Client verwenden und anhand der Antwort vom Dienst Entscheidungen dazu treffen, wann und ob der Vorgang wiederholt werden soll. Weitere Informationen finden Sie im Abschnitt [Allgemeine REST-und Wiederholungsrichtlinien](#general-rest-and-retry-guidelines) weiter unten in dieser Anleitung.

### Gebrauchsanleitung Wiederholungen

Berücksichtigen Sie Bei Verwendung von Azure Search die folgenden Richtlinien :

* Verwenden Sie den vom Dienst zurückgegebenen Statuscode, um den Typ des Fehlers zu ermitteln. Die Statuscodes werden unter [HTTP-Statuscodes (Azure Search)](http://msdn.microsoft.com/library/dn798925.aspx) definiert. Der Statuscode 503 (Dienst nicht verfügbar) gibt an, dass der Dienst stark ausgelastet ist und die Anforderung nicht sofort verarbeitet werden kann. Sie dürfen den Vorgang erst wiederholen, nachdem Sie dem Dienst Zeit für die Wiederherstellung gegeben haben. Eine Wiederholung nach zu kurzem Verzögerungsintervall verlängert wahrscheinlich die Nichtverfügbarkeit.
* Allgemeine Informationen zu Wiederholungen von REST-Vorgängen finden Sie im Abschnitt [Allgemeine REST-und Wiederholungsrichtlinien](#general-rest-and-retry-guidelines) weiter unten in dieser Anleitung.

## Weitere Informationen

* [Azure Search REST-API](http://msdn.microsoft.com/library/dn798935.aspx)

## Wiederholungsrichtlinien für Azure Active Directory

Azure Active Directory (AD) ist eine umfassende Cloud-Lösung für die Identitäts- und Zugriffsverwaltung, die zentrale Verzeichnisdienste, eine erweiterte Identitätsgovernance, Sicherheit und Zugriffsverwaltung von Anwendungen kombiniert. Azure AD bietet Entwicklern auf der Grundlage von zentralen Richtlinien und Regeln auch eine Plattform für die Identitätsverwaltung zur Bereitstellung einer Zugriffssteuerung für deren Anwendungen.

### Wiederholungsmechanismus

Es gibt keinen integrierten Wiederholungsmechanismus für Azure Active Directory in der Active Directory-Authentifizierungsbibliothek (ADAL). Sie können den Anwendungsblock zur Handhabung von vorübergehenden Fehlern verwenden, um eine Wiederholungsstrategie zu implementieren, die einen benutzerdefinierten Erkennungsmechanismus für die Ausnahmen, die vom Active Directory zurückgegeben werden, enthält.

### Richtlinienkonfiguration (Azure Active Directory)

Bei Verwendung des Anwendungsblocks zur Handhabung von vorübergehenden Fehlern mit Azure Active Directory erstellen Sie eine **RetryPolicy**-Instanz, basierend auf einer Klasse, über die die Erkennungsstrategie definiert wird, die Sie verwenden möchten.

```csharp
var policy = new RetryPolicy<AdalDetectionStrategy>(new ExponentialBackoff(retryCount: 5,
	                                                                 minBackoff: TimeSpan.FromSeconds(0),
	                                                                 maxBackoff: TimeSpan.FromSeconds(60),
	                                                                 deltaBackoff: TimeSpan.FromSeconds(2)));
```

Anschließend rufen Sie die **ExecuteAction** oder **ExecuteAsync**-Methode der Wiederholungsrichtlinie auf und übergeben den Vorgang, der ausgeführt werden soll.

```csharp
var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));
```

Die Klasse der Erkennungsstrategie erhält Ausnahmen, wenn ein Fehler auftritt, und muss erkennen, ob es sich eher um einen vorübergehender oder um einen dauerhaften Fehler handelt. In der Regel geschieht dies durch Untersuchung des Ausnahmetyps und des Statuscode. Die Antwort, dass ein Dienst nicht verfügbar ist, gibt beispielsweise an, dass ein Wiederholungsversuch erfolgen soll. Der Anwendungsblock zur Handhabung von vorübergehenden Fehlern enthält keine Erkennungsstrategieklasse, die für die Verwendung mit dem ADAL-Client geeignet ist, aber im Abschnitt [Beispiele](#examples-azure-active-directory-) weiter unten wird ein Beispiel für eine benutzerdefinierte Erkennungsstrategie gegeben. Die Verwendung einer benutzerdefinierten Erkennungsstrategie unterscheidet sich nicht von der Verwendung der mit dem Block gelieferten Strategie.

Standardstrategien für den Anwendungsblock zur Handhabung vorübergehender Fehler werden im Abschnitt [Strategien für den Anwendungsblock zur Handhabung vorübergehender Fehler (Topaz)](#transient-fault-handling-application-block-topaz-strategies) am Ende dieses Handbuchs gezeigt.

## Gebrauchsanleitung Wiederholungen

Berücksichtigen Sie Bei Verwendung von Azure Active Directory die folgenden Richtlinien:

* Wenn Sie die REST-API für Azure Active Directory verwenden, sollten Sie den Vorgang nur wiederholen, wenn das Ergebnis ein Fehler im Bereich 5xx ist (z. B. 500 Interner Serverfehler, 502 ungültiges Gateway, 503 Dienst nicht verfügbar und 504 Gateway-Timeout). Bei anderen Fehlern den Vorgang nicht wiederholen.
* Bei Verwendung der Active Directory-Authentifizierungsbibliothek (ADAL), sind HTTP-Codes nicht leicht zugänglich. Sie müssen eine benutzerdefinierte Erkennungsstrategie erstellen, die eine Logik zum Überprüfen der Eigenschaften der ADAL-spezifischen Ausnahmen beinhaltet. Siehe Abschnitt [Beispiele](#examples-azure-active-directory-) weiter hinten.
* Eine exponentielle Backoff-Richtlinie wird für die Verwendung in Batch-Szenarien mit Azure Active Directory empfohlen.

Erwägen Sie, mit den folgenden Einstellungen für Wiederholungsvorgänge zu beginnen. Hierbei handelt es sich um allgemeine Einstellungen und Sie sollten die Vorgänge überwachen und die Werte entsprechend Ihrem Szenario optimieren.


| **Context** | **Beispiel-Ziel E2E<br />Maximale Wartezeit** | **Wiederholungsstrategie** | **Einstellungen** | **Werte** | **So funktioniert's** |
|----------------------|----------------------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interaktiv, Benutzeroberfläche<br />oder Vordergrund | 2 Sek | FixedInterval | Anzahl der Wiederholungen<br />Wiederholungsintervall<br />Erster schneller Wiederholungsversuch | 3<br />500 ms<br />true | Versuch 1 - Verzögerung 0 Sek<br />Versuch 2 - Verzögerung 500 ms<br />Versuch 3 - Verzögerung 500 ms |
| Hintergrund oder <br />Batch | 60 Sekunden | ExponentialBackoff | Anzahl der Wiederholungen<br />Minimales Backoff<br />Maximales Backoff<br />Delta Backoff-<br />Erste schnelle Wiederholung | 5<br />0 Sek<br />60 Sek<br />2 Sek<br />false | Versuch 1 - Verzögerung 0 Sek<br />Versuch 2 - Verzögerung ca. 2 Sek<br />Versuch 3 - Verzögerung ca. 6 Sek<br />Versuch 4 - Verzögerung ca. 14 Sek<br />Versuch 5 - Verzögerung 30 Sek |

## Beispiele (Azure Active Directory)

Das folgende Codebeispiel veranschaulicht die Verwendung des Anwendungsblocks zur Handhabung vorübergehender Fehler (Topaz), um eine benutzerdefinierte vorübergehende Fehlererkennungsstrategie, die für die Verwendung mit dem ADAL-Client geeignet ist, zu definieren. Mit dem Code wird wie in der Codeauflistung unten definiert eine neue **RetryPolicy**-Instanz basierend auf einer benutzerdefinierten Erkennungsstrategie des Typs **AdalDetectionStrategy** erstellt. Benutzerdefinierte Erkennungsstrategien für Topaz implementieren die **ITransientErrorDetectionStrategy**-Schnittstelle und geben „true“ zurück, wenn eine Wiederholung versucht wird, oder **false**, wenn der Fehler nicht vorläufig ist und eine Wiederholung nicht versucht werden soll.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Net;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

	namespace RetryCodeSamples
	{
	    class ActiveDirectoryCodeSamples
	    {
	        public async static Task Samples()
	        {
	            var authority = "[some authority]";
	            var resourceId = “[some resource id]”;
	            var clientId = “[some client id]”;

	            var authContext = new AuthenticationContext(authority);

	            var uc = new UserCredential(“[user]", "[password]");

	            // Use Topaz with a custom detection strategy to manage retries.
	            var policy =
	                new RetryPolicy<AdalDetectionStrategy>(
	                    new ExponentialBackoff(
	                        retryCount: 5,
	                        minBackoff: TimeSpan.FromSeconds(0),
	                        maxBackoff: TimeSpan.FromSeconds(60),
	                        deltaBackoff: TimeSpan.FromSeconds(2)));

	            var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));

	            // Get the access token
	            var accessToken = result.AccessToken;

	            // Use the result, probably to authorize an API call.
	        }
	    }

	    // TODO: This is sample code that needs validation from the WAAD team!
	    // based on existing detection strategies
	    public class AdalDetectionStrategy : ITransientErrorDetectionStrategy
	    {
	        private static readonly WebExceptionStatus[] webExceptionStatus =
	            new[]
	            {
	                WebExceptionStatus.ConnectionClosed,
	                WebExceptionStatus.Timeout,
	                WebExceptionStatus.RequestCanceled
	            };

	        private static readonly HttpStatusCode[] httpStatusCodes =
	            new[]
	            {
	                HttpStatusCode.InternalServerError,
	                HttpStatusCode.GatewayTimeout,
	                HttpStatusCode.ServiceUnavailable,
	                HttpStatusCode.RequestTimeout
	            };

	        public bool IsTransient(Exception ex)
	        {
	            var adalException = ex as AdalException;
	            if (adalException == null)
	            {
	                return false;
	            }

	            if (adalException.ErrorCode == AdalError.ServiceUnavailable)
	            {
	                return true;
	            }

	            var innerWebException = adalException.InnerException as WebException;
	            if (innerWebException != null)
	            {
	                if (webExceptionStatus.Contains(innerWebException.Status))
	                {
	                    return true;
	                }

	                if (innerWebException.Status == WebExceptionStatus.ProtocolError)
	                {
	                    var response = innerWebException.Response as HttpWebResponse;
	                    return response != null && httpStatusCodes.Contains(response.StatusCode);
	                }
	            }

	            return false;
	        }
	    }
	}

Informationen zum Wiederholen von Active Directory Graph-API-Vorgängen und den zurückgegebenen Fehlercodes finden Sie unter:

* [Codebeispiel: Wiederholungslogik](http://msdn.microsoft.com/library/azure/dn448547.aspx)
* [Azure AD Graph-Fehlercodes](http://msdn.microsoft.com/library/azure/hh974480.aspx)

## Weitere Informationen

* [Implementierung einer benutzerdefinierten Erkennungsstrategie](http://msdn.microsoft.com/library/hh680940.aspx) (Topaz)
* [Implementierung einer benutzerdefinierten Wiederholungsstrategie](http://msdn.microsoft.com/library/hh680943.aspx) (Topaz)
* [Tokenausstellung und Wiederholungsrichtlinien](http://msdn.microsoft.com/library/azure/dn168916.aspx)

## Allgemeine Richtlinien für REST und Wiederholung

Berücksichtigen Sie beim Zugriff auf die Dienste von Azure oder von Drittanbietern Folgendes:

* Verwenden Sie einen systematischen Ansatz zur Verwaltung von Wiederholungen, vielleicht als wiederverwendbaren Code, sodass Sie eine einheitliche Methodik für alle Clients und alle Lösungen anwenden können.
* Erwägen Sie die Verwendung eines Wiederholungsframeworks, wie z. B. den Anwendungsblock zur Handhabung vorübergehender Fehler, um Wiederholungen zu verwalten, wenn der Zieldienst oder der Client nicht über integrierte Wiederholungsmechanismen verfügt. Dies hilft Ihnen bei der Implementierung eines konsistenten Wiederholungsverhaltens, und kann eine geeignete Standardwiederholungsstrategie für den Zieldienst bereitstellen. Sie müssen jedoch möglicherweise einen benutzerdefinierten Wiederholungscode für Dienste erstellen, die kein Standardverhalten aufweisen und nicht auf Ausnahmen vertrauen, um vorübergehende Fehler anzuzeigen, oder wenn Sie eine **Retry-Response**-Antwort verwenden möchten, um das Wiederholungsverhalten zu verwalten.
* Die vorübergehende Erkennungslogik hängt von der tatsächlichen Client-API ab, die Sie verwenden, um die REST-Aufrufe aufzurufen. Einige Clients, wie z. B. die neuere **HttpClient**-Klasse, lösen keine Ausnahmen für abgeschlossene Anforderungen mit einem nicht erfolgreichen HTTP-Statuscode aus. Dies verbessert die Leistung, verhindert jedoch die Verwendung des Anwendungsblocks zur Handhabung vorübergehender Fehler. In diesem Fall können Sie den Aufruf der REST-API mit einem Code umschließen, der Ausnahmen für nicht erfolgreiche HTTP-Statuscodes erzeugt, die dann vom Block verarbeitet werden können. Alternativ können Sie einen anderen Mechanismus für die Durchführung der Wiederholungen verwenden.
* Der vom Dienst zurückgegebene HTTP-Statuscode kann dabei helfen, zu erkennen, ob der Fehler vorübergehend ist. Möglicherweise müssen Sie die Ausnahmen, die von einem Client oder dem Wiederholungsframework erzeugt wurden untersuchen, um auf den Statuscode zuzugreifen oder um den entsprechenden Ausnahmetyp bestimmen zu können. Die folgenden HTTP-Codes geben in der Regel an, dass eine Wiederholung geeignet ist:
  * 408 Anforderungstimeout
  * 500 Interner Serverfehler
  * 502 Ungültiges Gateway
  * 503 Dienst nicht verfügbar
  * 504 Gateway-Timeout
* Wenn Sie Ihre Wiederholungslogik auf Ausnahmen basieren, wird in der Regel durch Folgendes angezeigt, dass ein vorübergehender Fehler besteht, da keine Verbindung hergestellt werden konnte:
  * WebExceptionStatus.ConnectionClosed
  * WebExceptionStatus.ConnectFailure
  * WebExceptionStatus.Timeout
  * WebExceptionStatus.RequestCanceled
* Im Fall des Status „Dienst nicht verfügbar“ weist der Dienst möglicherweise auf die entsprechende Verzögerung vor der Wiederholung im **Retry-After**-Antwort-Header oder in einem anderen benutzerdefinierten Header (wie im DocumentDB-Dienst) hin. Dienste können auch zusätzliche Informationen als benutzerdefinierte Header oder in den Inhalt der Antwort eingebettet senden. Der Anwendungsblock zur Handhabung vorübergehender Fehler kann keine Standard- oder benutzerdefinierte „Retry-after“-Header verwenden.
* Führen Sie keine Wiederholung für Statuscodes durch, die Clientfehler (Fehler im Bereich 4xx) darstellen, mit Ausnahme eines Anforderungstimeouts 408.
* Testen Sie Ihre Wiederholungsstrategien und Mechanismen gründlich unter verschiedenen Bedingungen, wie z. B. verschiedenen Netzwerkzuständen und sich ändernden Systemlastverteilungen.

## Wiederholungsstrategien

Im Folgenden sind die typischen Arten von Wiederholungsstrategie-Intervallen dargestellt:

* **Exponentiell**: eine Wiederholungsrichtlinie, mit der eine angegebene Anzahl von Wiederholungsversuchen durchgeführt wird, bei der ein zufälliger exponentieller Backoff-Ansatz zur Ermittlung des Intervalls zwischen den Wiederholungsversuchen verwendet wird. Beispiel:

		var random = new Random();

		var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
		            random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
		            (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
		var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
		               this.maxBackoff.TotalMilliseconds);
		retryInterval = TimeSpan.FromMilliseconds(interval);

* **Inkrementell**: eine Wiederholungsstrategie mit einer angegebenen Anzahl von Wiederholungsversuchen und ein inkrementelles Zeitintervall zwischen den Wiederholungen. Beispiel:

		retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
		               (this.increment.TotalMilliseconds * currentRetryCount));

* **LinearRetry** Eine Wiederholungsstrategie, bei der eine angegebene Anzahl von Wiederholungen durchgeführt wird, mit einem angegebenen festen Zeitintervall zwischen den Wiederholungen. Beispiel:

		retryInterval = this.deltaBackoff;

## Weitere Informationen

* [Trennschalter-Strategien](http://msdn.microsoft.com/library/dn589784.aspx)

## Strategien des Anwendungsblocks zur Handhabung von vorübergehenden Fehlern (Topaz)

Der Anwendungsblock zur Handhabung von vorübergehenden Fehlern verfügt über die folgenden Standardstrategien.

| **Strategie** | **Einstellung** | **Standardwert** | **Bedeutung** |
|-------------------------|-----------------------------------------------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Exponentiell** | retryCount<br />minBackoff<br /><br />maxBackoff<br /><br />deltaBackoff<br /><br />fastFirstRetry | 10<br />1 Sekunde<br /><br />30 Sekunden<br /><br />10 Sekunden<br /><br />true | Die Anzahl der Wiederholungsversuche.<br />Die minimale Backoff-Zeit. Der höhere dieser Werte oder das berechnete Backoff wird als Wiederholungsverzögerung verwendet.<br />Die minimale Backoff-Zeit. Der niedrigere dieser Werte oder das berechnete Backoff wird als Wiederholungsverzögerung verwendet.<br />Der Wert, der zur Berechnung eines zufälligen Deltas für die exponentielle Verzögerung zwischen Wiederholungen verwendet wird.<br />Gibt an, ob der erste Wiederholungsversuch sofort erfolgt. |
| **Inkrementell** | retryCoretryCount<br />initialInterval<br />increment<br /><br />fastFirstRetry<br />| 10<br />1 Sekunde<br />1 Sekunde<br /><br />true | Die Anzahl der Wiederholungsversuche.<br />Das anfängliche Intervall, das für die erste Wiederholung angewendet wird.<br />Der inkrementelle Zeitwert, der zur Berechnung der progressiven Verzögerung zwischen Wiederholungen verwendet wird.<br />Gibt an, ob der erste Wiederholungsversuch sofort erfolgt. |
| **Linear (festgelegtes Intervall)** | retryCount<br />retryInterval<br />fastFirstRetry<br /> | 10<br />1 Sekunde<br />true | Die Anzahl der Wiederholungsversuche.<br />Die Verzögerung zwischen Wiederholungen.<br />Gibt an, ob der erste Wiederholungsversuch sofort erfolgt. |
Beispiele für die Verwendung des Anwendungsblocks zur Handhabung von vorübergehenden Fehlern finden Sie in den Abschnitten „Beispiele“ weiter vorne in diesem Handbuch für die Azure SQL-Datenbank mit ADO.NET und Azure Active Directory.

<!---HONumber=July15_HO4-->