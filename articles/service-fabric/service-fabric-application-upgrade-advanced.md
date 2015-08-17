<properties
   pageTitle="Service Fabric-Anwendungsupgrade: Weiterführende Themen"
   description="Dieser Artikel behandelt einige weiterführende Themen in Bezug auf Upgrades von Service Fabric-Anwendungen."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>

# Service Fabric-Anwendungsupgrade: Weiterführende Themen

## Upgrademodus "Manual"

> [AZURE.NOTE]Der nicht überwachte manuelle Modus sollte ausschließlich bei einem fehlgeschlagenen oder angehaltenen Upgrade in Betracht gezogen werden. Der überwachte Modus ("Monitored") ist der für Service Fabric-Anwendungen empfohlene Modus.

Service Fabric umfasst mehrere Upgrademodi zur Unterstützung von Entwicklungs- und Produktionsclustern. Die einzelnen Bereitstellungsoptionen sind jeweils ideal geeignet für die verschiedenen Umgebungen. Das überwachte parallele Anwendungsupgrade ist das typische in Produktionsumgebungen verwendete Upgrade. Wenn die Upgraderichtlinie angegeben ist, stellt Service Fabric sicher, dass die Anwendung fehlerfrei ist, bevor das Upgrade fortgesetzt wird. In bestimmten Situationen, in denen spezifischere oder komplexere Integritätsevaluierungsrichtlinien erforderlich sind oder ein außergewöhnliches Upgrade ausgeführt werden muss (in der Anwendung sind bereits Daten verloren gegangen usw.), kann der Anwendungsadministrator den manuellen parallelen Upgrademodus verwenden, um den Status des Upgrades in den verschiedenen Upgradedomänen vollständig kontrollieren zu können. Schließlich eignet sich der automatische parallele Upgrademodus in Entwicklungs- oder Testumgebungen zur Bereitstellung schneller Iterationszyklen während der Entwicklung von Diensten.

**Manuell** – Das Anwendungsupgrade wird bei der aktuellen Upgradedomäne beendet und der Upgrademodus in den nicht überwachten manuellen Modus geändert. Der Administrator muss **MoveNextApplicationUpgradeDomainAsync** manuell aufrufen, um das Upgrade fortzusetzen, oder durch Initiieren eines neuen Upgrades einen Rollback auslösen. Wenn das Upgrade in den manuellen Modus wechselt, wird dieser Modus beibehalten, bis ein neues Upgrade initiiert wird. Der **GetApplicationUpgradeProgressAsync**-Befehl gibt "FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING" zurück.

## Upgrade mit einem Diff-Paket

Eine Service Fabric-Anwendung kann durch Bereitstellen eines vollständigen und eigenständigen Anwendungspakets aktualisiert werden. Eine Anwendung kann auch mithilfe eines Diff-Pakets aktualisiert werden, das nur die aktualisierten Anwendungsdateien sowie aktualisierte Anwendungsmanifest- und Dienstmanifestdateien enthält.

Ein vollständiges Anwendungspaket enthält alle zum Starten und Ausführen einer Service Fabric-Anwendung erforderlichen Dateien. Ein Diff-Paket enthält dagegen nur die Dateien, die sich zwischen der letzten Bereitstellung und dem aktuellen Upgrade geändert haben, sowie die vollständigen Anwendungs- und Dienstmanifestdateien. Bei allen Verweisen im Anwendungsmanifest oder Dienstmanifest, die Service Fabric im Buildlayout nicht finden kann, werden die entsprechenden Verweise im ImageStore gesucht (Link folgt).

Vollständige Anwendungspakete werden für die Erstinstallation einer Anwendung im Cluster benötigt. Für nachfolgende Upgrades kann entweder ein vollständiges Anwendungspaket oder ein Diff-Paket verwendet werden.

Situationen, in denen sich ein Diff-Paket anbietet:

* Ein Diff-Paket wird gegenüber einem umfangreichen Anwendungspaket bevorzugt, das auf mehrere Dienstmanifestdateien und/oder mehrere Code-, Konfigurations- oder Datenpakete verweist.

* Ein Diff-Paket wird in einem Bereitstellungssystem bevorzugt, das das Buildlayout direkt aus dem Anwendungsbuildprozess generiert. In diesem Fall weisen neu erstellte Assemblys eine andere Prüfsumme auf, auch wenn sich der Code nicht geändert hat. Bei Verwendung eines vollständigen Anwendungspakets müssten Sie die Version in allen Codepaketen ändern. Bei Verwendung eines Diff-Pakets stellen Sie dagegen nur die geänderten Dateien und die Manifestdateien bereit, in denen sich die Version geändert hat.

## Nächste Schritte

[Lernprogramm für Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md)

[Anwendungsupgradeparameter](service-fabric-application-upgrade-parameters.md)

[Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)

[Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=August15_HO6-->