<properties
  pageTitle="Speichern und Anzeigen von Diagnosedaten im Azure-Speicher | Microsoft Azure"
  description="Azure-Diagnosedaten in den Azure-Speicher abrufen und anzeigen"
  services="cloud-services"
  documentationCenter=".net"
  authors="rboucher"
  manager="jwhit"
  editor="tysonn" />
<tags
  ms.service="cloud-services"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/21/2015"
  ms.author="robb" />

# Speichern und Anzeigen von Diagnosedaten im Azure-Speicher

Diagnosedaten werden nicht dauerhaft gespeichert, wenn sie nicht in den Microsoft Azure-Speicheremulator oder den Azure-Speicher übertragen werden. Sobald die Daten gespeichert wurden, können sie mit einem der verschiedenen verfügbaren Tools angezeigt werden.

## Festlegen eines Speicherkontos

Legen Sie das Speicherkonto fest, das Sie in der Datei „ServiceConfiguration.cscfg“ verwenden möchten. Die Kontoinformationen werden als Verbindungszeichenfolge in einer Konfigurationseinstellung definiert. Das folgende Beispiel zeigt die Standardverbindungszeichenfolge, die für einen neuen Clouddienst in Visual Studio erstellt wurde:


```
	<ConfigurationSettings>
	   <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	</ConfigurationSettings>
```

Sie können diese Verbindungszeichenfolge zum Bereitstellen von Kontoinformationen für ein Azure-Speicherkonto ändern.

Je nach Art der gesammelten Diagnosedaten verwendet die Azure-Diagnose entweder den Blob-Dienst oder den Tabellenspeicherdienst. Die folgende Tabelle zeigt die beibehaltenen Datenquellen und deren Format.

|Datenquelle|Speicherformat|
|---|---|
|Azure-Protokolle|Tabelle|
|IIS 7.0-Protokolle|Blob|
|Infrastrukturprotokolle der Azure-Diagnose|Tabelle|
|Ablaufprotokolle für fehlgeschlagene Anforderungen|Blob|
|Windows-Ereignisprotokolle|Tabelle|
|Leistungsindikatoren|Tabelle|
|Absturzabbilder|Blob|
|Benutzerdefinierte Fehlerprotokolle|Blob|

## Übertragen von Diagnosedaten

Bei SDK 2.5 und höher kann die Anforderung zum Übertragen von Diagnosedaten über die Konfigurationsdatei erfolgen. Sie können Diagnosedaten in geplanten Intervallen wie in der Konfiguration festgelegt übertragen.

Bei SDK 2.4 und früher kann die Anforderung zum Übertragen von Diagnosedaten sowohl über die Konfigurationsdatei als auch programmgesteuert erfolgen. Der programmgesteuerte Ansatz ermöglicht Ihnen auch bedarfsgesteuerte Übertragungen.


>[AZURE.IMPORTANT]Wenn Sie Diagnosedaten in ein Azure-Speicherkonto übertragen, fallen Kosten für die von den Diagnosedaten genutzten Speicherressourcen an.

## Speichern von Diagnosedaten

Protokolldaten werden im Blob- oder Tabellenspeicher mit den folgenden Namen gespeichert:

**Tabellen**

- **WadLogsTable** – Mit dem Ablaufverfolgungslistener in Code erstellte Protokolle

- **WADDiagnosticInfrastructureLogsTable** – Diagnosemonitor und Konfigurationsänderungen

- **WADDirectoriesTable** – Vom Diagnosemonitor überwachte Verzeichnisse. Dies umfasst IIS-Protokolle, Protokolle zu IIS-Anforderungsfehlern und benutzerdefinierte Verzeichnisse. Der Speicherort der Blob-Protokolldatei ist im Feld „Container“ festgelegt, ihr Name im Feld „RelativePath“. Das Feld „AbsolutePath“ gibt den Speicherort und Namen der Datei wie auf dem virtuellen Azure-Computer vorhanden an.

- **WADPerformanceCountersTable** – Leistungsindikatoren

- **WADWindowsEventLogsTable** – Windows-Ereignisprotokolle

**Blobs**

- **wad-control-container** – (Nur für SDK 2.4 und früher) Enthält die XML-Konfigurationsdateien, die die Azure-Diagnose steuern.

- **wad-iis-failedreqlogfiles** – Enthält Informationen aus den Protokollen zu IIS-Anforderungsfehlern.

- **wad-iis-logfiles** – Enthält Informationen zu IIS-Protokollen.

- **"custom"** – Ein benutzerdefinierter Container basierend auf Konfigurationsverzeichnissen, die vom Diagnosemonitor überwacht werden. Der Name dieses Blob-Containers wird in WADDirectoriesTable festgelegt.

## Tools zum Anzeigen von Diagnosedaten
Mehrere Tools stehen zum Anzeigen der Daten nach der Übertragung an den Speicher zur Verfügung. Zum Beispiel:

- **Server-Explorer in Visual Studio** – Wenn Sie die Azure-Tools für Microsoft Visual Studio installiert haben, können den Azure-Speicherknoten im Server-Explorer zum Anzeigen schreibgeschützter Blob- und Tabellendaten aus Ihren Azure-Speicherkonten verwenden. Sie können Daten aus dem lokalen Speicheremulatorkonto und auch aus den für Azure erstellten Speicherkonten anzeigen. Weitere Informationen finden Sie unter [Durchsuchen von Speicherressourcen mit Server-Explorer](https://msdn.microsoft.com/library/ff683677.aspx).

- **Azure-Speicher-Explorer von Neudesic** – [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/) ist ein praktisches Tool mit grafischer Benutzeroberfläche zum Überprüfen und Ändern der Daten in Azure-Speicherprojekten, einschließlich der Protokolle Ihrer Azure-Anwendungen. Informationen zum Herunterladen des Tools finden Sie unter [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/).

- Azure Diagnostics Manager von Cerebrata – [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) ist ein auf Windows (WPF) basierender Client zum Verwalten von Azure-Diagnosen. Hiermit können Sie von den unter Azure ausgeführten Anwendungen erfasste Diagnosedaten anzeigen, herunterladen und verwalten. Informationen zum Herunterladen des Tools finden Sie unter [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx).

## Nächste Schritte

[Verfolgen des Ablaufs in einer Cloud Services-Anwendung mit der Azure-Diagnose](cloud-services-dotnet-diagnostics-trace-flow.md)

<!---HONumber=Nov15_HO2-->