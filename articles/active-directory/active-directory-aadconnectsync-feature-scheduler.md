<properties
   pageTitle="Azure AD Connect Sync: Scheduler | Microsoft Azure"
   description="Dieses Thema beschreibt das integrierte Schedulerfeature in Azure AD Connect Sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/26/2016"
   ms.author="andkjell"/>

# Azure AD Connect Sync: Scheduler
Dieses Thema beschreibt den integrierten Scheduler in Azure AD Connect Sync (auch bekannt als Synchronisierungsmodul).

Diese Funktion wurde mit Build 1.1.105.0 eingeführt (veröffentlicht im Februar 2016).

## Übersicht
Azure AD Connect Sync synchronisiert Änderungen in Ihrem lokalen Verzeichnis mithilfe eines Schedulers. Es gibt zwei Schedulerprozesse: einen für die Kennwortsynchronisierung und einen anderen für die Objekt-/Attributsynchronisierung und für Wartungstasks. In diesem Thema wird letzterer behandelt.

In früheren Versionen befand sich der Scheduler für Objekte und Attribute außerhalb des Synchronisierungsmoduls und des Windows-Taskplaners, oder es wurde ein separater Windows-Dienst zum Auslösen des Synchronisierungsprozesses verwendet. Ab den 1.1-Versionen ist der Scheduler in das Synchronisierungsmodul integriert und kann angepasst werden. Die neue Standardeinstellung für die Synchronisierungsfrequenz ist 30 Minuten.

Der Scheduler ist für zwei Tasks zuständig:

- **Synchronisierungszyklus**. Der Prozess zum Importieren, Synchronisieren und Exportieren von Änderungen.
- **Wartungstasks**. Erneuern von Schlüsseln und Zertifikaten für das Zurücksetzen von Kennwörtern und für den Geräteregistrierungsdienst (Device Registration Service, DRS). Löschen alter Einträge im Vorgangsprotokoll.

Der Scheduler selbst wird immer ausgeführt, kann jedoch so konfiguriert werden, dass er nur einen oder keinen dieser Tasks ausführt. Wenn Sie beispielsweise einen eigenen Prozess für den Synchronisierungszyklus verwenden, können Sie diesen Task im Scheduler deaktivieren, den Wartungstask aber weiterhin ausführen.

## Schedulerkonfiguration
Um die aktuellen Konfigurationseinstellungen anzuzeigen, wechseln Sie zu PowerShell, und führen Sie `Get-ADSyncScheduler` aus. Folgendes wird angezeigt:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

- **AllowedSyncCycleInterval**. Die höchste von Azure AD zugelassene Synchronisierungshäufigkeit. Eine höhere Synchronisierungsfrequenz als hier angegeben wird nicht unterstützt.
- **CurrentlyEffectiveSyncCycleInterval**. Der momentan verwendete Zeitplan. Er besitzt den gleichen Wert wie CustomizedSyncInterval (falls festgelegt), falls er keine höhere Häufigkeit als AllowedSyncInterval aufweist. Wenn Sie CustomizedSyncCycleInterval ändern, tritt diese Änderung nach dem nächsten Synchronisierungszyklus in Kraft.
- **CustomizedSyncCycleInterval**. Wenn der Scheduler mit einer beliebigen anderen Häufigkeit als der Standardfrequenz von 30 Minuten ausgeführt werden soll, konfigurieren Sie diese Einstellung. In der oben gezeigten Abbildung wurde der Scheduler stattdessen auf einmal pro Stunde festgelegt. Wenn Sie diese Einstellung auf einen niedrigeren Wert als AllowedSyncInterval festlegen, wird stattdessen AllowedSyncInterval verwendet.
- **NextSyncCyclePolicyType**. Entweder „Delta“ oder „Initial“. Legt fest, ob bei der nächsten Ausführung nur Deltaänderungen verarbeitet werden sollen oder ob bei der nächsten Ausführung ein vollständiger Import mit Synchronisierung stattfinden soll. Dabei werden auch neue oder geänderte Regeln erneut verarbeitet.
- **NextSyncCycleStartTimeInUTC**. Der nächste Zeitpunkt, zu dem der Scheduler den nächsten Synchronisierungszyklus startet.
- **PurgeRunHistoryInterval**. Der Zeitraum, für den die Vorgangsprotokolle aufbewahrt werden sollen. Diese können im Synchronization Service Manager überprüft werden. Standardmäßig werden sie sieben Tage lang gespeichert.
- **SyncCycleEnabled**. Gibt an, ob der Scheduler die Import-, Synchronisierungs- und Exportprozesse als Teil seiner Operation ausführt.
- **MaintenanceEnabled**. Zeigt an, ob der Wartungsprozess aktiviert ist. Dabei werden die Zertifikate/Schlüssel aktualisiert und das Vorgangsprotokoll bereinigt.
- **IsStagingModeEnabled**. Zeigt, ob der [Stagingmodus](active-directory-aadconnectsync-operations.md#staging-mode) aktiviert ist.

All diese Eigenschaften können über `Set-ADSyncScheduler` geändert werden: Der Parameter IsStagingModeEnabled kann nur mit dem Installations-Assistenten festgelegt werden.

Die Konfiguration des Schedulers wird in Azure AD gespeichert. Wenn Sie über einen Stagingserver verfügen, wirken sich alle Änderungen am primären Server auch auf den Stagingserver aus (mit Ausnahme von „IsStagingModeEnabled“).

## Starten des Schedulers
Der Scheduler wird standardmäßig alle 30 Minuten ausgeführt. In einigen Fällen möchten Sie vielleicht einen Synchronisierungszyklus zwischen den geplanten Zyklen ausführen, oder Sie müssen einen anderen Typ ausführen.

**Deltasynchronisierungszyklus** – ein Deltasynchronisierungszyklus umfasst die folgenden Schritte:

- Deltaimport auf allen Connectors
- Deltasynchronisierung auf allen Connectors
- Export auf allen Connectors

Es kann vorkommen, dass eine dringende Änderung sofort synchronisiert werden muss. In diesem Fall müssen Sie einen Zyklus manuell ausführen. Wenn Sie einen Zyklus manuell ausführen müssen, führen Sie in PowerShell folgenden Befehl aus: `Start-ADSyncSyncCycle -PolicyType Delta`.

**Vollständiger Synchronisierungszyklus** – wenn Sie eine der folgenden Konfigurationsänderungen vorgenommen haben, müssen Sie einen vollständigen Synchronisierungszyklus ausführen (auch als Erstsynchronisierung bezeichnet):

- Sie haben weitere Objekte oder Attribute hinzugefügt, die aus einem Quellverzeichnis importiert werden müssen.
- Sie haben Änderungen an den Synchronisierungsregeln vorgenommen.
- Sie haben die [Filterung](active-directory-aadconnectsync-configure-filtering.md) geändert, sodass eine andere Anzahl von Objekten einbezogen werden soll.

Wenn Sie eine dieser Änderungen vorgenommen haben, müssen Sie einen vollständigen Synchronisierungszyklus ausführen, damit das Synchronisierungsmodul die Möglichkeit hat, die Connectorbereiche neu zu konsolidieren. Ein vollständiger Synchronisierungszyklus umfasst die folgenden Schritte:

- Vollständiger Import auf allen Connectors
- Vollständige Synchronisierung auf allen Connectors
- Export auf allen Connectors

Um einen vollständigen Synchronisierungszyklus zu initiieren, führen Sie an einer PowerShell-Eingabeaufforderung den Befehl `Start-ADSyncSyncCycle -PolicyType Initial` aus. Dadurch wird ein vollständiger Synchronisierungszyklus gestartet.

## Beenden des Schedulers
Wenn der Scheduler gerade einen Synchronisierungszyklus ausführt, müssen Sie diesen beenden. Dies ist zum Beispiel der Fall, wenn Sie beim Starten des Installations-Assistenten den folgenden Fehler erhalten:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Während ein Synchronisierungszyklus ausgeführt wird, sind Änderungen an der Konfiguration nicht möglich. Sie können warten, bis der Scheduler den Prozess beendet hat. Sie können den Prozess aber auch beenden, um Ihre Änderungen sofort vorzunehmen. Das Beenden des aktuellen Zyklus ist ungefährlich, und noch nicht verarbeitete Änderungen werden bei der nächsten Ausführung verarbeitet.

1. Weisen Sie den Scheduler zunächst mit dem PowerShell-Cmdlet `Stop-ADSyncSyncCycle` an, den aktuellen Zyklus zu beenden.
2. Durch das Beenden des Schedulers wird der aktuelle Task des aktuellen Connectors nicht unterbrochen. Um ein Beenden des Connectors zu erzwingen, führen Sie folgende Aktionen aus: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Starten Sie den **Synchronisierungsdienst** über das Startmenü. Wechseln Sie zu **Connectors**, markieren Sie den Connector mit dem Status **Wird ausgeführt**, und wählen Sie unter „Aktionen“ die Option **Beenden** aus.

Der Scheduler ist noch immer aktiv und wird bei der nächsten Gelegenheit wieder gestartet.

## Scheduler und Installations-Assistent
Wenn Sie den Installations-Assistenten starten, wird der Scheduler vorübergehend unterbrochen. Der Grund dafür ist die Annahme, dass Sie Konfigurationsänderungen vornehmen möchten, und diese können nicht angewendet werden, solange das Synchronisierungsmodul aktiv ausgeführt wird. Lassen Sie daher den Installations-Assistenten nicht geöffnet, da er alle Synchronisierungsaktionen auf dem Synchronisierungsmodul verhindert.

## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0302_2016-->