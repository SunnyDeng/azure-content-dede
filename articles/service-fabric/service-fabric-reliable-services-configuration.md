<properties
   pageTitle="Übersicht über die Konfiguration von Azure Service Fabric Reliable Actors | Microsoft Azure"
   description="Erfahren Sie mehr über das Konfigurieren zustandsbehafteter Reliable Services in Azure Service Fabric."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/28/2015"
   ms.author="sumukhs"/>

# Konfigurieren zustandsbehafteter Reliable Services
Die Standardkonfiguration für zustandsbehaftete Reliable Services kann über das Konfigurationspaket (Config) oder in der Dienstimplementierung (Code) geändert werden.

+ **Config**: Die Konfiguration über das Konfigurationspaket erfolgt für jeden Dienst in der Anwendung durch Ändern der Datei „Settings.xml“, die im Stammverzeichnis des Visual Studio-Pakets im Ordner "Config" generiert wurde.
+ **Code**: Die Konfiguration über Code erfolgt durch Überschreiben von StatefulService.CreateReliableStateManager und Erstellen von ReliableStateManager mit einem ReliableStateManagerConfiguration-Objekt mit den entsprechenden Optionen.

Standardmäßig sucht die Service Fabric-Laufzeit in der Datei "Settings.xml" nach vordefinierten Abschnittsnamen und nutzt die Konfigurationswerte beim Erstellen der zugrunde liegenden Laufzeitkomponenten.

> [AZURE.NOTE]Löschen Sie **NICHT** die Abschnittsnamen der folgenden Konfigurationen in der Datei "settings.xml", die in der Visual Studio-Projektmappe generiert wird, sofern Sie die Konfiguration nicht per Code vornehmen möchten. Das Umbenennen des Konfigurationspakets oder der Abschnittsnamen erfordert eine Änderung des Codes beim Konfigurieren von ReliableStateManager.


## Replicator-Sicherheitskonfiguration
Replicator-Sicherheitskonfigurationen werden verwendet, um den während der Replikation verwendeten Kommunikationskanal zu sichern. Dies bedeutet, dass Dienste nicht ihren gegenseitigen Replikationsdatenverkehr erkennen können. Dadurch wird sichergestellt, dass die Daten nicht nur hochverfügbar, sondern auch sicher sind. Standardmäßig ermöglicht ein leerer Sicherheitskonfigurationsabschnitt keine Replikationssicherheit.

### Standardmäßiger Abschnittsname
ReplicatorSecurityConfig

> [AZURE.NOTE]Zum Ändern dieses Abschnittsnamens überschreiben Sie den ReplicatorSecuritySectionName-Parameter für den ReliableStateManagerConfiguration-Konstruktor beim Erstellen von ReliableStateManager für diesen Dienst.


## Replicator-Konfiguration
Replicator-Konfigurationen werden zum Konfigurieren des Replicators verwendet, der für die hohe Zuverlässigkeit des zustandsbehafteten Reliable Service verantwortlich ist. Er repliziert und speichert den Zustand zu diesem Zweck lokal. Die Standardkonfiguration wird von der Visual Studio-Vorlage generiert und sollte ausreichen. Dieser Abschnitt befasst sich mit zusätzlichen Konfigurationen, die zum Optimieren des Replicators verfügbar sind.

### Standardmäßiger Abschnittsname
ReplicatorConfig

> [AZURE.NOTE]Zum Ändern dieses Abschnittsnamens überschreiben Sie den ReplicatorSettingsSectionName-Parameter für den ReliableStateManagerConfiguration-Konstruktor beim Erstellen von ReliableStateManager für diesen Dienst.


### Konfigurationsnamen
|Name|Unit|Standardwert|Anmerkungen|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Sekunden|0,05|So lange wartet der Replicator auf dem sekundären Replicator nach dem Empfang eines Vorgangs, bevor er eine Bestätigung an den primären Replicator sendet. Alle anderen Bestätigungen, die für innerhalb dieses Intervalls verarbeitete Vorgänge gesendet werden, werden als eine einzelne Antwort gesendet.|
|ReplicatorEndpoint|–|N/V – RequiredParameter|Die IP-Adresse und der Port, die der primäre/sekundäre Replicator für die Kommunikation mit anderen Replicatoren in der Replikatgruppe verwendet. Dabei sollte im Dienstmanifest auf einen TCP-Ressourcenendpunkt verwiesen werden. Weitere Informationen zum Definieren von Endpunktressourcen im Dienstmanifest finden Sie unter [Dienstmanifestressourcen](service-fabric-service-manifest-resources.md). |
|MaxPrimaryReplicationQueueSize|Anzahl der Vorgänge|8192|Die maximale Anzahl der Vorgänge in der primären Warteschlange. Ein Vorgang wird freigegeben, nachdem der primäre Replicator eine Bestätigung von allen sekundären Replicatoren empfangen hat. Dieser Wert muss größer als 64 und eine Potenz von 2 sein.|
|MaxSecondaryReplicationQueueSize|Anzahl der Vorgänge|16384|Die maximale Anzahl der Vorgänge in der sekundären Warteschlange. Ein Vorgang wird freigegeben, nachdem sein Zustand durch Persistenz hochverfügbar gemacht wurde. Dieser Wert muss größer als 64 und eine Potenz von 2 sein.|
|CheckpointThresholdInMB|MB|200|Die Menge an Speicherplatz für Protokolldateien, nach dem der Status geprüft wird.|
|MaxRecordSizeInKB|KB|1024|Die maximale Datensatzgröße, die der Replicator in das Protokoll schreiben kann. Dieser Wert muss ein Vielfaches von 4 und größer als 16 sein.|
|OptimizeLogForLowerDiskUsage|Boolescher Wert|true|Wenn "true" festgelegt ist, wird das Protokoll so konfiguriert, dass die dedizierte Protokolldatei des Replikats mithilfe einer NTFS-Datei mit geringer Datendichte erstellt wird. Auf diese Weise verkleinert sich die Datei und damit der Speicherplatzbedarf. Wenn "false" festgelegt ist, wird die Datei mit festen Zuordnungen erstellt. Dies ermöglicht die beste Schreibleistung.|
|MaxRecordSizeInKB|KB|1024|Die maximale Datensatzgröße, die der Replicator in das Protokoll schreiben kann. Dieser Wert muss ein Vielfaches von 4 und größer als 16 sein.|
|SharedLogId|GUID|""|Gibt eine eindeutige GUID zum Identifizieren der freigegebenen Protokolldatei an, die mit diesem Replikat verwendet wird. In der Regel sollten Dienste diese Einstellung nicht verwenden. Wenn jedoch "SharedLogId" festgelegt ist, muss auch "SharedLogPath" angegeben sein.|
|SharedLogPath|Der vollständig qualifizierte Pfadname|""|Gibt den vollständig qualifizierten Pfad an, in dem die freigegebene Protokolldatei für dieses Replikat erstellt wird. In der Regel sollten Dienste diese Einstellung nicht verwenden. Wenn jedoch "SharedLogPath" festgelegt ist, muss auch "SharedLogId" angegeben sein.|


## Beispielkonfiguration über Code
```csharp
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            new ReliableStateManagerReplicatorSettings
            {
                RetryInterval = TimeSpan.FromSeconds(3)
            }));
}
```


## Beispiel einer Konfigurationsdatei
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


## Anmerkungen
"BatchAcknowledgementInterval" steuert die Replikationslatenz. Der Wert "0" ergibt die geringstmögliche Latenz, allerdings auf Kosten des Durchsatzes (da eine größer Anzahl von Bestätigungsnachrichten gesendet und verarbeitet werden muss, von denen jede weniger Bestätigungen enthält). Je größer der Wert für "BatchAcknowledgementInterval" ist, um so höher ist der Gesamtdurchsatz der Replikation, zu Lasten einer höheren Vorgangslatenz. Daraus ergibt sich direkt die Latenz von Transaktions-Commits.

Der Wert für "MaxStreamSizeInMB" bestimmt die Menge des Speicherplatzes, den der Replikator zum Speichern von Zustandsinformationen in der dedizierten Protokolldatei des Replikats verwenden kann. Durch die Erhöhung dieses Werts auf einen höheren Wert als den Standardwert kann die Neukonfiguration durch die partielle Statusübermittlung, die aufgrund der Verfügbarkeit eines umfangreicheren Vorgangsverlaufs im Protokoll stattfindet, beschleunigt werden, wenn ein neues Replikat der Gruppe hinzugefügt wird. Die Wiederherstellung eines Replikats nach einem Absturz kann dadurch allerdings mehr Zeit in Anspruch nehmen.

Mit der Einstellung "OptimizeForLowerDiskUsage" kann der Speicherplatz für die Protokolldatei "überdimensioniert" werden. Dies ermöglicht es aktiven Replikaten, weitere Zustandsinformationen in ihren Protokolldateien zu speichern. Inaktive Replikate hingegen belegen weniger Speicherplatz. Obwohl dadurch mehr Replikate auf einem Knoten gehostet werden können, als andernfalls aufgrund von mangelndem Speicherplatz möglich wäre, werden die Zustandsinformationen schneller in die Protokolldateien geschrieben, wenn Sie "OptimizeForLowerDiskUsage" auf "false" setzen.

Die Einstellung "MaxRecordSizeInKB" definiert die maximale Größe eines Datensatzes, der vom Replicator in die Protokolldatei geschrieben werden kann. In den meisten Fällen ist die Standarddatensatzgröße 1024 KB optimal. Wenn durch den Dienst jedoch größere Datenelemente in die Zustandsinformationen einbezogen werden, muss dieser Wert möglicherweise erhöht werden. Es nutzt wenig, für "MaxRecordSizeInKB" einen Wert unter 1024 festzulegen, da kleinere Datensätze nur den für sie erforderlichen Speicherplatz belegen. Eine Änderung sollte nur in seltenen Fällen erforderlich sein.

Die Einstellungen "SharedLogId" und "SharedLogPath" werden immer zusammen verwendet. Sie ermöglichen einem Dienst, ein separates freigegebenes Protokoll aus dem freigegebenen Standardprotokoll für den Knoten zu verwenden. Zur Optimierung der Effizienz sollten so viele Dienste wie möglich dasselbe freigegebene Protokoll angeben. Freigegebene Protokolldateien sollten auf Datenträgern gespeichert werden, die ausschließlich für die freigegebene Protokolldatei verwendet werden. Dies reduziert Konflikte durch die Bewegungen des Lesekopfs. Eine Änderung sollte nur in seltenen Fällen erforderlich sein.

<!---HONumber=AcomDC_1203_2015-->