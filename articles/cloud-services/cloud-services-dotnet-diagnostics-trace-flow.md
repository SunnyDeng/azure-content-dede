<properties
	pageTitle="Verfolgen des Ablaufs einer Cloud Services-Anwendung mit der Azure-Diagnose | Microsoft Azure"
	description="Fügen Sie in einer Azure-Anwendung Ablaufverfolgungsmeldungen hinzu, um Debuggen, Leistungsmessung, Überwachung, Datenverkehrsanalysen und mehr zu ermöglichen."
	services="cloud-services"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/17/2015"
	ms.author="robb"/>



# Verfolgen des Ablaufs einer Cloud Services-Anwendung mit der Azure-Diagnose

Die Ablaufverfolgung ist eine Möglichkeit, die Ausführung der Anwendung zu überwachen, während sie ausgeführt wird. Sie können die Klassen [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx) und [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) verwenden, um Informationen zu Fehlern und zur Anwendungsausführung in Protokollen, Textdateien oder auf anderen Geräten zur späteren Analyse aufzuzeichnen. Weitere Informationen zur Ablaufverfolgung finden Sie unter [Ablaufverfolgung und Instrumentation von Anwendungen](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## Verwenden von Ablaufverfolgungsanweisungen und -schaltern

Implementieren Sie die Ablaufverfolgung in der Cloud Services-Anwendung durch Hinzufügen von [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) zur Anwendungskonfiguration und durch Aufrufe von „System.Diagnostics.Trace“ oder „System.Diagnostics.Debug“ in Ihrem Anwendungscode. Verwenden Sie die Konfigurationsdatei *app.config* für Workerrollen und *web.config* für Webrollen. Wenn Sie einen neuen gehosteten Dienst mit einer Visual Studio-Vorlage erstellen, wird die Azure-Diagnose dem Projekt automatisch hinzugefügt, und „DiagnosticMonitorTraceListener“ wird in die entsprechende Konfigurationsdatei für die Rollen, die Sie hinzufügen, aufgenommen.

Informationen zum Platzieren von Ablaufverfolgungsanweisungen finden Sie unter [Vorgehensweise: Hinzufügen von Ablaufverfolgungsanweisungen zum Anwendungscode](https://msdn.microsoft.com/library/zd83saa2.aspx).

Durch die Platzierung von [Ablaufverfolgungsschaltern](https://msdn.microsoft.com/library/3at424ac.aspx) in Ihrem Code können Sie steuern, ob und in welchem Umfang Ablaufverfolgung durchgeführt wird. Auf diese Weise können Sie den Status der Anwendung in einer Produktionsumgebung überwachen. Dies ist besonders wichtig in einer Geschäftsanwendung, die mehrere Komponenten auf mehreren Computern verwendet. Weitere Informationen finden Sie unter [Vorgehensweise: Konfigurieren von Ablaufverfolgungsschaltern](https://msdn.microsoft.com/library/t06xyy08.aspx).

## Konfigurieren des Ablaufverfolgungslisteners in einer Azure-Anwendung

Für „Trace“, „Debug“ und „TraceSource“ müssen Sie „Listener“ einrichten, die Meldungen sammeln und aufzeichnen, die gesendet werden. Listener sammeln und speichern Ablaufverfolgungsmeldungen und leiten sie weiter. Sie leiten die Ablaufverfolgungsausgabe an ein entsprechendes Ziel weiter, beispielsweise ein Protokoll, ein Fenster oder eine Textdatei. Die Azure-Diagnose verwendet die [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx)-Klasse.

Bevor Sie das folgende Verfahren ausführen, müssen Sie den Azure-Diagnosemonitor initialisieren. Informationen hierzu finden Sie unter [Aktivieren der Diagnose in Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Beachten Sie, dass die Konfiguration des Listeners automatisch hinzugefügt wird, wenn Sie die von Visual Studio bereitgestellten Vorlagen verwenden.


### Hinzufügen eines Ablaufverfolgungslisteners

1. Öffnen Sie die Datei „web.config“ oder „app.config“ für Ihre Rolle.
2. Fügen Sie der Datei folgenden Code hinzu:

	```
	<system.diagnostics>
		<trace>
			<listeners>
				<add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
		          Microsoft.WindowsAzure.Diagnostics,
		          Version=1.0.0.0,
		          Culture=neutral,
		          PublicKeyToken=31bf3856ad364e35"
		          name="AzureDiagnostics">
			  	  <filter type="" />
				</add>
			</listeners>
		</trace>
	</system.diagnostics>
	```
3. Speichern Sie die config-Datei.

Weitere Informationen zu Listenern finden Sie unter [Ablaufverfolgungslistener](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Nachdem Sie die Schritte zum Hinzufügen des Listeners abgeschlossen haben, können Sie Ihrem Code Ablaufverfolgungsanweisungen hinzufügen.


### So fügen Sie dem Code eine Ablaufverfolgungsanweisung hinzu

1. Öffnen Sie eine Quelldatei für die Anwendung. Beispielsweise die <RoleName>.cs-Datei für die Workerrolle oder die Webrolle.
2. Fügen Sie die folgende using-Anweisung hinzu, wenn sie noch nicht vorhanden ist: ```
	    using System.Diagnostics;
	```
3. Fügen Sie Ablaufverfolgungsanweisungen überall ein, wo Sie Informationen über den Zustand der Anwendung erfassen möchten. Sie können eine Vielzahl von Methoden zum Formatieren der Ausgabe der Ablaufverfolgungsanweisung verwenden. Weitere Informationen finden Sie unter [Vorgehensweise: Hinzufügen von Ablaufverfolgungsanweisungen zum Anwendungscode](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Speichern Sie die Quelldatei.

<!---HONumber=Nov15_HO2-->