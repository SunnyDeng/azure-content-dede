<properties
   pageTitle="Konfigurieren der Azure-Diagnose zum Senden von Daten an Application Insights | Microsoft Azure"
   description="Aktualisieren Sie die öffentliche Konfiguration von Azure-Diagnose, um Daten an Application Insights zu senden."
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/16/2015"
   ms.author="saurabh" />

# Konfigurieren der Azure-Diagnose zum Senden von Daten an Application Insights

Azure-Diagnose speichert Daten in Azure Storage-Tabellen. Sie können auch alle oder eine Teilmenge der Daten an Application Insights weiterreichen, indem Sie „Senken“ und „Kanäle“ in Ihrer Konfiguration konfigurieren, wenn Sie die Azure-Diagnose-Erweiterung 1.5 oder höher verwenden.

In diesem Artikel wird beschrieben, wie Sie die öffentliche Konfiguration für die Azure-Diagnose-Erweiterung erstellen, sodass Daten an Application Insights gesendet werden.

## Konfigurieren von Application Insights als Senke

In der Azure-Diagnose-Erweiterung 1.5 wird erstmals das **<SinksConfig>**-Element in der öffentlichen Konfiguration verwendet. Damit wird die zusätzliche *Senke* definiert, an die die Azure-Diagnose-Daten gesendet werden können. Sie können die Details zur Application Insights-Ressource angeben, an die Sie die Azure-Diagnose-Daten als Teil von **<SinksConfig>** senden möchten. Ein Beispiel für **SinksConfig** sieht folgendermaßen aus:

	<SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig> 

Für das **Sink**-Element gibt das *name*-Attribut einen Zeichenfolgenwert an, der verwendet wird, um eindeutig auf die Senke zu verweisen. Das **ApplicationInsights**-Element gibt den Instrumentierungsschlüssel der Application Insights-Ressource an, an die die Azure-Diagnose-Daten gesendet werden. Wenn Sie keine vorhandene Application Insights-Ressource haben, finden Sie weitere Informationen zum Erstellen einer Ressource sowie zum Abrufen des Instrumentierungsschlüssels unter [Erstellen einer neuen Application Insights-Ressource](app-insights-create-new-resource.md).

Wenn Sie ein Clouddienstprojekt mit Azure SDK 2.8 entwickeln, wird dieser Instrumentierungsschlüssel beim Verpacken des Clouddienstprojekts automatisch in der öffentlichen Konfiguration ausgefüllt, basierend auf der **APPINSIGHTS\_INSTRUMENTATIONKEY**-Dienstkonfigurationseinstellung. Weitere Informationen finden Sie unter [Verwenden von Application Insights mit Azure-Diagnose zum Beheben von Clouddienstproblemen](cloud-services-dotnet-diagnostics-applicationinsights.md).

Mit dem **Channels**-Element können Sie ein oder mehrere **Channel**-Elemente für die Daten definieren, die an die Senke gesendet werden. Der Kanal verhält sich wie ein Filter und ermöglicht es Ihnen, bestimmte Protokolliergrade auszuwählen, die Sie an die Senke senden möchten. Sie können z. B. ausführliche Protokolle sammeln und sie an den Speicher senden. Sie können sich jedoch auch dazu entscheiden, einen Kanal mit dem Protokolliergrad „Fehler“ zu definieren, sodass beim Senden von Protokollen über diesen Kanal nur Fehlerprotokolle an diese Senke gesendet werden. Für einen **Kanal** (Channel) wird das *Name*-Attribut dazu verwendet, eindeutig auf diesen Kanal zu verweisen. Mit dem *logLevel*-Attribut können Sie den Protokolliergrad angeben, den der Kanal zulässt. Die verfügbaren Protokolliergrade, von den meisten bis zu den wenigsten Informationen, sind: - Verbose - Information - Warning - Error - Critical

## Senden von Daten an die Application Insights-Senke
Nachdem die Application Insights-Senke definiert wurde, können Sie Daten an diese Senke senden, indem Sie das *sinks*-Attribut den Elementen unter dem **DiagnosticMonitorConfiguration**-Knoten hinzufügen. Durch Hinzufügen des *sinks*-Element für jeden Knoten geben Sie an, dass die von jedem Knoten und den untergeordneten Knoten gesammelten Daten an die angegebene Senke gesendet werden sollen.

Wenn Sie beispielsweise alle Daten senden möchten, die von der Azure-Diagnose gesammelt werden, können Sie das *sink*-Attribut direkt dem **DiagnosticMonitorConfiguration**-Knoten hinzufügen. Legen Sie den Wert für *sinks* auf den Namen der Senke fest, der in der **SinkConfig** angegeben wurde.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

Wenn Sie nur Fehlerprotokolle an die Application Insights-Senke senden möchten, können Sie den *sinks*-Wert auf den Namen der Senke festlegen, gefolgt von dem Kanalnamen, wobei die beiden Namen durch einen Punkt (.) voneinander getrennt werden. Um beispielsweise nur Fehlerprotokolle an die Application Insights-Senke zu senden, verwenden Sie den MyTopDiagdata-Kanal, der in der SinksConfig oben definiert wurde.
 
	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

Wenn Sie nur ausführliche Anwendungsprotokolle an Application Insights senden möchten, würden Sie dem **Logs**-Knoten das *sinks*-Attribut hinzufügen.
	
	<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

Sie können auch mehrere Senken in der Konfiguration auf unterschiedlichen Ebenen der Hierarchie einschließen. In diesem Fall verhält sich die auf der obersten Ebene der Hierarchie angegebene Senke wie eine globale Einstellung, und die auf der einzelnen Elementebene angegebene Senke verhält sich wie eine Korrektur der globalen Einstellung.

Hier ist ein vollständiges Beispiel für die öffentliche Konfigurationsdatei, die alle Fehler an Application Insights sendet (angegeben unter dem **DiagnosticMonitorConfiguration**-Knoten) und zusätzlich Protokolle des Verbosegrads für die Anwendungsprotokolle (angegeben unter dem **Logs**-Knoten).

    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
           sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
        <DiagnosticInfrastructureLogs />
        <PerformanceCounters>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
      </DiagnosticMonitorConfiguration>

    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>

![Öffentliche Diagnosekonfiguration](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

Für diese Funktionalität gibt es einige Einschränkungen, die es zu beachten gilt.

- Kanäle sind nur für die Arbeit mit Protokolltyp und nicht mit Leistungsindikatoren vorgesehen. Wenn Sie einen Kanal mit einem Leistungsindikatorelement angeben, wird es ignoriert. 
- Der Protokolliergrad für einen Kanal darf den Protokolliergrad für die von der Azure-Diagnose erfassten Daten nicht überschreiten. Beispiel: Sie können keine Anwendungsprotokollfehler im Logs-Element sammeln und können nicht versuchen, ausführliche Protokolle an die Application Insight-Synchronisierung zu senden. Das *ScheduledTransferLogLevelFilter*-Attribut muss immer genauso viele oder mehr Protokolle sammeln wie bzw. als die Protokolle, die Sie an eine Senke senden möchten. 
- Sie können keine von der Azure-Diagnose-Erweiterung gesammelten Blobdaten an Application Insights senden. Zum Beispiel alle Daten unter dem *Directories*-Knoten. Bei Absturzabbildern wird das tatsächliche Absturzabbild weiterhin an den Blobspeicher gesendet, und es wird nur eine Benachrichtigung, dass das Absturzabbild generiert wurde, an Application Insights gesendet. 


## Nächste Schritte

- Verwenden Sie [PowerShell](cloud-services-diagnostics-powershell.md), um die Azure-Diagnose-Erweiterung für Ihre Anwendung zu aktivieren. 
- Verwenden Sie [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md), um die Azure-Diagnose-Erweiterung für Ihre Anwendung zu aktivieren. 

<!---HONumber=Nov15_HO4-->