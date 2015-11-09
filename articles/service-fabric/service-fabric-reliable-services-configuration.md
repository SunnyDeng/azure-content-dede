<properties
   pageTitle="Konfigurieren zustandsbehafteter Reliable Services"
   description="Erfahren Sie mehr über das Konfigurieren zustandsbehafteter Reliable Services in Service Fabric."
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
   ms.date="08/26/2015"
   ms.author="sumukhs"/>

# Konfigurieren zustandsbehafteter Reliable Services
Die Standardkonfiguration für zustandsbehaftete Reliable Services kann geändert werden. Bearbeiten Sie zu diesem Zweck für jeden Dienst in der Anwendung die Datei "settings.xml", die im Stammverzeichnis des Visual Studio-Pakets im Ordner "Config" generiert wurde.

Die Service Fabric-Laufzeit sucht in der Datei "settings.xml" nach vordefinierten Abschnittsnamen und nutzt die Konfigurationswerte beim Erstellen der zugrunde liegenden Laufzeitkomponenten.

> [AZURE.NOTE]Löschen bzw. ändern Sie **NICHT** die Abschnittsnamen der folgenden Konfigurationen in der Datei "settings.xml", die in der Visual Studio-Projektmappe generiert wird.

## Replicator-Sicherheitskonfiguration
Replicator-Sicherheitskonfigurationen werden verwendet, um den während der Replikation verwendeten Kommunikationskanal zu sichern. Dies bedeutet, dass Dienste nicht ihren gegenseitigen Replikationsdatenverkehr erkennen können. Dadurch wird sichergestellt, dass die Daten nicht nur hochverfügbar, sondern auch sicher sind. Standardmäßig ermöglicht ein leerer Sicherheitskonfigurationsabschnitt keine Replikationssicherheit.

### Name des Abschnitts
ReplicatorSecurityConfig

## Replicator-Konfiguration
Replicator-Konfigurationen werden zum Konfigurieren des Replicators verwendet, der für die hohe Zuverlässigkeit des zustandsbehafteten Reliable Service verantwortlich ist. Er repliziert und speichert den Zustand zu diesem Zweck lokal. Die Standardkonfiguration wird von der Visual Studio-Vorlage generiert und sollte ausreichen. Dieser Abschnitt befasst sich mit zusätzlichen Konfigurationen, die zum Optimieren des Replicators verfügbar sind.

### Name des Abschnitts
ReplicatorConfig

### Konfigurationsnamen

|Name|Unit|Standardwert|Anmerkungen|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Sekunden|0,05|So lange wartet der Replicator auf dem sekundären Replicator nach dem Empfang eines Vorgangs, bevor er eine Bestätigung an den primären Replicator sendet. Alle anderen Bestätigungen, die für innerhalb dieses Intervalls verarbeitete Vorgänge gesendet werden, werden als eine einzelne Antwort gesendet.|
|ReplicatorEndpoint|–|N/V – RequiredParameter|Die IP-Adresse und der Port, die der primäre/sekundäre Replicator für die Kommunikation mit anderen Replicatoren in der Replikatgruppe verwendet. Dabei sollte im Dienstmanifest auf einen TCP-Ressourcenendpunkt verwiesen werden. Weitere Informationen zum Definieren von Endpunktressourcen im Dienstmanifest finden Sie unter [Dienstmanifestressourcen](service-fabric-service-manifest-resources.md). |
|RetryInterval|Sekunden|5|Der Zeitraum, nach dem der Replicator eine Nachricht erneut überträgt, wenn er keine Bestätigung für einen Vorgang erhält.|
|MaxReplicationMessageSize|Byte|50 MB|Die maximale Größe der Replikationsdaten, die in einer einzelnen Nachricht übertragen werden können.|
|MaxPrimaryReplicationQueueSize|Anzahl der Vorgänge|1024|Die maximale Anzahl der Vorgänge in der primären Warteschlange. Ein Vorgang wird freigegeben, nachdem der primäre Replicator eine Bestätigung von allen sekundären Replicatoren empfangen hat. Dieser Wert muss größer als 64 und eine Potenz von 2 sein.|
|MaxSecondaryReplicationQueueSize|Anzahl der Vorgänge|2048|Die maximale Anzahl der Vorgänge in der sekundären Warteschlange. Ein Vorgang wird freigegeben, nachdem sein Zustand durch Persistenz hochverfügbar gemacht wurde. Dieser Wert muss größer als 64 und eine Potenz von 2 sein.|
|MaxStreamSizeInMB|MB|1024|Die Menge an reserviertem Speicherplatz für Protokolldateien. Die Größe in Bytes muss größer als 16 mal die MaxRecordSize in Byte sein.|
|MaxRecordSizeInKB|KB|1024|Die maximale Datensatzgröße, die der Replicator in das Protokoll schreiben kann. Dieser Wert muss ein Vielfaches von 4 und größer als 16 sein.|
|OptimizeForLocalSSD|Boolescher Wert|false|Wenn "true" festgelegt ist, wird das Protokoll so konfiguriert, dass Zustandsinformationen direkt in die dedizierte Protokolldatei des Replikats geschrieben werden. Dies optimiert die Leistung, wenn Protokolldateien auf Solid-State-Datenträgern gespeichert sind oder die E/A-Rate des VM-Datenträgers stark eingeschränkt ist. Wenn "false" festgelegt ist, werden die Zustandsinformationen zunächst in der freigegebenen Protokolldatei bereitgestellt und dann in die dedizierte Protokolldatei übertragen.|
|OptimizeLogForLowerDiskUsage|Boolescher Wert|false|Wenn "true" festgelegt ist, wird das Protokoll so konfiguriert, dass die dedizierte Protokolldatei des Replikats mithilfe einer NTFS-Datei mit geringer Datendichte erstellt wird. Auf diese Weise verkleinert sich die Datei und damit der Speicherplatzbedarf. Wenn "false" festgelegt ist, wird die Datei mit festen Zuordnungen erstellt. Dies ermöglicht die beste Schreibleistung.|
|SharedLogId|GUID|""|Gibt eine eindeutige GUID zum Identifizieren der freigegebenen Protokolldatei an, die mit diesem Replikat verwendet wird. In der Regel sollten Dienste diese Einstellung nicht verwenden. Wenn jedoch "SharedLogId" festgelegt ist, muss auch "SharedLogPath" angegeben sein.|
|SharedLogPath|Der vollständig qualifizierte Pfadname|""|Gibt den vollständig qualifizierten Pfad an, in dem die freigegebene Protokolldatei für dieses Replikat erstellt wird. In der Regel sollten Dienste diese Einstellung nicht verwenden. Wenn jedoch "SharedLogPath" festgelegt ist, muss auch "SharedLogId" angegeben sein.|

## Beispiel einer Konfigurationsdatei

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="MaxStreamSizeInMB" Value="512" />
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

Der Wert für "MaxStreamSizeInMB" bestimmt die Menge des Speicherplatzes, den der Replicator zum Speichern von Zustandsinformationen in der dedizierten Protokolldatei des Replikats verwenden kann. Hinweis: Der Zustand der gespeicherten Informationen wird verwendet, um einem anderen Replikat die Anpassung an den Zustand eines primären Replikats zu ermöglichen. Daher empfiehlt sich im Allgemeinen eine größere Protokolldatei, wodurch sich das andere Replikat schneller den Zustand des primären Replikats aneignen kann. Größere Protokolldateien belegen jedoch mitunter auch mehr Speicherplatz, sodass weniger Replikate auf einem bestimmten Knoten gehostet werden können.

Mit der Einstellung "OptimizeForLowerDiskUsage" kann der Speicherplatz für die Protokolldatei "überdimensioniert" werden. Dies ermöglicht es aktiven Replikaten, weitere Zustandsinformationen in ihren Protokolldateien zu speichern. Inaktive Replikate hingegen belegen weniger Speicherplatz. Obwohl dadurch mehr Replikate auf einem Knoten gehostet werden können, als andernfalls aufgrund von mangelndem Speicherplatz möglich wäre, werden die Zustandsinformationen schneller in die Protokolldateien geschrieben, wenn Sie "OptimizeForLowerDiskUsage" auf "false" setzen.

Mit der Einstellung "OptimizeForLocalSSD" werden Zustandsinformationen vor der Übertragung in die dedizierte Protokolldatei nicht in die freigegebene Protokolldatei geschrieben. Die Einstellung sollte in der Regel festgelegt werden, wenn der lokale Speicherplatz für die dedizierte Protokolldatei ein Solid-State-Datenträger ist, da das Minimieren der Bewegung des Lesekopfs entfällt. Sie kann auch verwendet werden, wenn die VM auf dem lokalen Datenträger zahlreiche E/A-Prozesse ausführt und die E/A-Rate des lokalen Datenträgers durch den VM-Host erheblich eingeschränkt ist.

Die Einstellung "MaxRecordSizeInKB" definiert die maximale Größe eines Datensatzes, der vom Replicator in die Protokolldatei geschrieben werden kann. In den meisten Fällen ist die Standarddatensatzgröße 1024 KB optimal. Wenn durch den Dienst jedoch größere Datenelemente in die Zustandsinformationen einbezogen werden, muss dieser Wert möglicherweise erhöht werden. Es nutzt wenig, für "MaxRecordSizeInKB" einen Wert unter 1024 festzulegen, da kleinere Datensätze nur den für sie erforderlichen Speicherplatz belegen. Eine Änderung sollte nur in seltenen Fällen erforderlich sein.

Die Einstellungen "SharedLogId" und "SharedLogPath" werden immer zusammen verwendet. Sie ermöglichen einem Dienst, ein separates freigegebenes Protokoll aus dem freigegebenen Standardprotokoll für den Knoten zu verwenden. Zur Optimierung der Effizienz sollten so viele Dienste wie möglich dasselbe freigegebene Protokoll angeben. Freigegebene Protokolldateien sollten auf Datenträgern gespeichert werden, die ausschließlich für die freigegebene Protokolldatei verwendet werden. Dies reduziert Konflikte durch die Bewegungen des Lesekopfs. Eine Änderung sollte nur in seltenen Fällen erforderlich sein.

<!---HONumber=Nov15_HO1-->