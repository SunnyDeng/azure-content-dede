<properties
	pageTitle="Erstellen von Wiederherstellungsplänen" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Computer auf lokalen Servern zu Azure oder einem sekundären Rechenzentrum." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/07/2015" 
	ms.author="raynew"/>

# Erstellen von Wiederherstellungsplänen

Site Recovery ist Teil einer Lösung für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR), die Ihre lokalen physischen Server und Ihre virtuellen Computer mittels Orchestrierung und Automatisierung von Replikation und Failover zu Azure (oder zu einem sekundären lokalen Rechenzentrum) schützt. Eine Einführung in Site Recovery-Bereitstellungsszenarien finden Sie in der [Übersicht über Site Recovery](site-recovery-overview.md).

## Informationen zum Artikel

Dieser Artikel enthält Informationen zum Erstellen und Anpassen von Wiederherstellungsplänen.

Sollten Sie nach der Lektüre dieses Artikels noch Fragen haben, stellen Sie diese bitte im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Übersicht

Wiederherstellungspläne bestehen aus einer oder mehreren sortierten Gruppen, die geschützte virtuelle Computer oder Replikationsgruppen (für die SAN-Replikation) enthalten. Für Computer wird das Failover je nach der Gruppe durchgeführt, in der sie enthalten sind. Das Failover für virtuelle Computer in einer bestimmten Gruppe erfolgt parallel. Bei Wiederherstellungsplänen wird wie folgt vorgegangen:

- Definieren von Gruppen von Computern, für die das Failover und das Starten zusammen durchgeführt wird.
- Modellieren von Abhängigkeiten zwischen Computern, indem sie in einer Wiederherstellungsplangruppe zusammengefasst werden. Wenn Sie beispielsweise ein Failover durchführen und eine bestimmte Anwendung aufrufen möchten, gruppieren Sie die virtuellen Computer für diese Anwendung in derselben Wiederherstellungsplangruppe.
- Automatisieren und Erweitern des Failovers. Sie können für einen Wiederherstellungsplan ein Testfailover oder ein geplantes bzw. ungeplantes Failover durchführen. Sie können Wiederherstellungspläne mit Skripts, Azure-Automatisierung und manuellen Aktionen anpassen.

Wiederherstellungspläne werden im Site Recovery-Portal unter **Wiederherstellungspläne** angezeigt.


## Erstellen von Wiederherstellungsplänen

Die Art und Weise, wie Sie einen Wiederherstellungsplan erstellen, richtet sich nach Ihrer Site Recovery-Bereitstellung.

- **Hyper-V-Replikation (VMM)**: Wenn Sie eine Replikation von einem VMM-Standort zu einem sekundären lokalen Standort oder zu Azure per Hyper-V-Replikation durchführen, fügen Sie geschützte virtuelle Hyper-V-Computer aus einer VMM-Cloud einem Wiederherstellungsplan hinzu.
- **Hyper-V-Replikation (Hyper-V-Standort)**: Wenn Sie eine Replikation von einem Hyper-V-Standort (ohne VMM-Server) zu Azure durchführen, fügen Sie geschützte virtuelle Hyper-V-Computer aus einer Schutzgruppe einem Wiederherstellungsplan hinzu.
- **SAN-Replikation**: Wenn Sie die Replikation zu einem sekundären lokalen Standort per SAN-Replikation durchführen, fügen Sie dem Wiederherstellungsplan eine Replikationsgruppe hinzu, die virtuelle Computer enthält. Sie wählen eine Replikationsgruppe aus, anstatt bestimmte virtuelle Computer, da das Failover für alle virtuellen Computer in einer Replikationsgruppe zusammen durchgeführt werden muss (das Failover erfolgt zuerst auf der Speicherebene).
- **VMware-Replikation**: Wenn Sie die Replikation für virtuelle VMware-Computer zu Azure durchführen, fügen Sie einem Wiederherstellungsplan Replikationsgruppen hinzu, die virtuelle Computer enthalten.

Erstellen Sie einen Wiederherstellungsplan wie folgt:

1. Klicken Sie auf der Registerkarte Wiederherstellungspläne auf Erstellen. Geben Sie einen Namen für den Wiederherstellungsplan sowie die Quelle und das Ziel an. Der Quellserver muss virtuelle Computer aufweisen, die für Failover und Wiederherstellung aktiviert sind.

	- Wenn Sie von VMM zu VMM replizieren, müssen Sie unter **Quelltyp** die Option „VMM“ und die VMM-Quell- und -Zielserver auswählen. Klicken Sie auf **Hyper-V**, um Clouds anzuzeigen, die für die Verwendung des Hyper-V-Replikats konfiguriert sind. 
	- Wenn Sie per SAN von VMM zu VMM replizieren, müssen Sie unter **Quelltyp** die Option „VMM“ und die VMM-Quell- und -Zielserver auswählen. Klicken Sie auf **SAN**, um die Clouds anzuzeigen, die für die SAN-Replikation konfiguriert sind.
	- Wenn Sie von VMM zu Azure replizieren, wählen Sie unter **Quelltyp** die Option „VMM“ aus. Wählen Sie den VMM-Quellserver und als Ziel **Azure** aus.
	- Wenn Sie von einem Hyper-V-Standort replizieren, wählen Sie unter „Quelltyp“ die Option „Hyper-V“ aus. Wählen Sie den Standort als Quelle und als Ziel **Azure** aus.
- Wenn Sie von VMware oder einem physischen lokalen Server zu Azure replizieren, wählen Sie einen Konfigurationsserver als Quelle und als Ziel **Azure** aus.

2\. Wählen Sie unter **Virtuelle Computer auswählen** die virtuellen Computer (oder die Replikationsgruppe) aus, die Sie der Standardgruppe (Gruppe 1) im Wiederherstellungsplan hinzufügen möchten.

## Anpassen von Wiederherstellungsplänen

Nachdem Sie geschützte virtuelle Computer oder Replikationsgruppen der standardmäßigen Wiederherstellungsplangruppe hinzugefügt und den Plan erstellt haben, können Sie ihn anpassen:

- **Hinzufügen neuer Gruppen**: Sie können weitere Wiederherstellungsplangruppen hinzufügen. Gruppen werden in der Reihenfolge nummeriert, in der sie von Ihnen hinzugefügt werden. Sie können bis zu sieben Gruppen hinzufügen. Sie können diesen neuen Gruppen weitere Computer oder Replikationsgruppen hinzufügen. Beachten Sie, dass ein virtueller Computer oder eine Replikationsgruppe nur in eine Wiederherstellungsplangruppe eingefügt werden kann.
- ****Hinzufügen eines Skripts**: Sie können Skripts für die Ausführung vor oder nach einer Wiederherstellungsplangruppe hinzufügen. Hierbei wird für die Gruppe ein neuer Satz mit Aktionen hinzugefügt. Eine Gruppe von Vorabschritten für „Group 1“ wird beispielsweise mit dem folgenden Namen erstellt: „Group 1: Pre-steps“. Alle Vorabschritte werden in diesem Satz aufgelistet. Beachten Sie, dass Sie am primären Standort nur dann ein Skript hinzufügen können, wenn Sie einen VMM-Server bereitgestellt haben.
- **Hinzufügen einer manuellen Aktion**: Sie können manuelle Aktionen hinzufügen, die vor oder nach einer Wiederherstellungsgruppe ausgeführt werden. Wenn der Wiederherstellungsplan ausgeführt wird, wird er an dem Punkt angehalten, an dem Sie die manuelle Aktion eingefügt haben. In einem Dialogfeld werden Sie aufgefordert anzugeben, dass die manuelle Aktion abgeschlossen wurde.

### Erweitern von Wiederherstellungsplänen mit Skripts

Sie können Ihrem Wiederherstellungsplan ein Skript hinzufügen:

Falls Sie über einen VMM-Quellstandort verfügen, können Sie ein Skript auf dem VMM-Server erstellen und in Ihren Wiederherstellungsplan einfügen. Wenn Sie zu Azure replizieren, können Sie Azure-Automatisierungsrunbooks in Ihren Wiederherstellungsplan integrieren.

#### Erstellen eines VMM-Skripts

Beachten Sie Folgendes, bevor Sie beginnen:

- Schreiben Sie Skripts mit Windows PowerShell.
- VMM-Cmdlets werden in einem Windows PowerShell-Modul bereitgestellt. Das VMM-Modul von Windows PowerShell wird installiert, wenn Sie die VMM-Konsole installieren. Das VMM-Modul kann mit dem folgenden Skriptbefehl in das Skript geladen werden: Import-Module -Name virtualmachinemanager. [Weitere Details](hhttps://technet.microsoft.com/library/hh875013.aspx)
- Stellen Sie sicher, dass Ihre VMM-Bereitstellung mindestens einen Bibliothekserver enthält. Standardmäßig befindet sich der Bibliotheksfreigabepfad für einen VMM-Server lokal auf dem VMM-Server mit dem Ordnernamen MSCVMMLibrary.
- Wenn Ihr Bibliotheksfreigabepfad remote vorhanden ist (oder lokal, aber nicht für MSCVMMLibrary freigegeben), konfigurieren Sie die Freigabe wie folgt (hier dient „\\libserver2.contoso.com\\share\\“ als Beispiel):
	- Öffnen Sie den Registrierungs-Editor.
	- Navigieren Sie zu HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft System Center Virtual Machine Manager Server\\DRAdapter\\Registration.
	- Bearbeiten Sie den Wert unter ScriptLibraryPath.
	- Platzieren Sie den Wert als „\\libserver2.contoso.com\\share“. Geben Sie den vollqualifizierten Domänennamen vollständig ein.
	- Geben Sie die Berechtigungen für den Speicherort der Freigabe an.

- Skripts in einem Wiederherstellungsplan werden im Kontext des VMM-Dienstkontos ausgeführt. Stellen Sie sicher, dass dieses Konto über Leseberechtigungen auf der Remotefreigabe verfügt, auf der sich das Skript befindet. Testen Sie die Ausführung des Skripts auf Berechtigungsebene des VMM-Dienstkontos.
- 	Stellen Sie sicher, dass Sie das Skript mit einem Benutzerkonto testen, für das die gleichen Berechtigungen wie für das VMM-Dienstkonto gelten. So wird dafür gesorgt, dass eigenständige getestete Skripts genauso wie in Wiederherstellungsplänen ausgeführt werden.
- 	Legen Sie für die Ausführungsrichtlinie auf dem VMM-Server wie folgt eine Umleitung fest:
	- Öffnen Sie die Windows PowerShell-Konsole (64 Bit) mit erweiterten Berechtigungen.
	- Geben Sie Folgendes ein: **Set-executionpolicy bypass**. [Weitere Details](https://technet.microsoft.com/library/ee176961.aspx)
- Achten Sie darauf, dass Sie try/catch-Blöcke verwenden, damit die Ausnahmen ordnungsgemäß behandelt werden. Falls im Skript eine Ausnahme auftritt, wird die Ausführung gestoppt, und für die Aufgabe wird ein Fehler angezeigt. Wenn ein Fehler auftritt, wird der verbleibende Teil des Skripts nicht ausgeführt. Falls dies beim Ausführen eines ungeplanten Failovers auftritt, wird der Wiederherstellungsplan fortgesetzt. Falls dies beim Ausführen eines geplanten Failovers auftritt, wird der Wiederherstellungsplan gestoppt. Korrigieren Sie in diesem Fall das Skript, überprüfen Sie, ob es wie erwartet ausgeführt wird, und führen Sie den Wiederherstellungsplan dann erneut aus.
- Der Write-Host-Befehl funktioniert in einem Wiederherstellungsplanskript nicht, und die Ausführung des Skripts ist nicht erfolgreich. Wenn Sie eine Ausgabe erstellen möchten, erstellen Sie ein Proxyskript, mit dem wiederum Ihr Hauptskript ausgeführt wird. Stellen Sie sicher, dass die gesamte Ausgabe mit dem Befehl „>>“ markiert wird.
- Die Zeitüberschreitung für das Skript wird erreicht, falls die Rückgabe nicht innerhalb von 600 Sekunden erfolgt.
- Falls etwas in STDERR geschrieben wird, wird das Skript als nicht erfolgreich klassifiziert. Diese Informationen werden in den Details zur Skriptausführung angezeigt.

Erstellen Sie das Skript wie folgt:

1. Erstellen Sie einen neuen Ordner in der Bibliotheksfreigabe, z. B.: <VMMServerName>\\MSSCVMMLibrary\\RPScripts. Platzieren Sie diese Datei auf dem VMM-Quell- und -Zielserver.
2. Erstellen Sie das Skript (z. B. RPScript), und überprüfen Sie, ob es wie erwartet funktioniert.
3. Platzieren Sie das Skript unter „<VMMServerName>\\MSSCVMMLibrary“ auf dem VMM-Quell- und -Zielserver.

#### Erstellen eines Azure-Automatisierungsrunbooks

Sie können Ihren Wiederherstellungsplan erweitern, indem Sie als Teil des Plans ein Azure-Automatisierungsrunbook ausführen. [Weitere Informationen](site-recovery-runbook-automation.md)


### Hinzufügen von benutzerdefinierten Einstellungen zu einem Wiederherstellungsplan

1. Öffnen Sie den Wiederherstellungsplan, den Sie anpassen möchten.
2. Klicken Sie, um virtuelle Computer oder eine neue Gruppe hinzuzufügen.
3. Klicken Sie zum Hinzufügen eines Skripts oder einer manuellen Aktion in der Liste **Schritt** auf einen beliebigen Eintrag, und klicken Sie anschließend auf **Skript** oder **Manuelle Aktion**. Geben Sie an, ob Sie das Skript oder die Aktion vor oder nach dem ausgewählten Eintrag hinzufügen möchten. Verwenden Sie die Befehlsschaltflächen **Nach oben** und **Nach unten**, um die Position des Skripts nach oben oder unten zu verschieben.
4. Wählen Sie beim Hinzufügen eines VMM-Skripts die Option **Failover zu VMM-Skript** aus, und geben Sie unter **Skriptpfad** den relativen Pfad zur Freigabe ein. Geben Sie für unser Beispiel, in dem sich die Freigabe unter „\<VMMServerName>\\MSSCVMMLibrary\\RPScripts“ befindet, also den folgenden Pfad an: \\RPScripts\\RPScript.PS1.
5. Wenn Sie ein Azure-Automatisierungsrunbook hinzufügen, geben Sie das **Azure Automation-Konto** an, unter dem sich das Runbook befindet, und wählen das gewünschte **Azure-Runbookskript** aus.
5. Führen Sie ein Failover für den Wiederherstellungsplan aus, um sicherzustellen, dass das Skript wie erwartet funktioniert.


## Ausführen eines Failovers

Sie können unterschiedliche Arten von Failover-Wiederherstellungsplänen ausführen, z. B. ein Testfailover zum Überprüfen Ihrer Umgebung. Außerdem können Sie ein geplantes oder ungeplantes Failover ausführen. Weitere Informationen zu Failovern und zum Abrufen einer Anleitung zum Ausführen unterschiedlicher Arten von Failovern finden Sie [hier](site-recovery-failover.md).


 

<!---HONumber=Oct15_HO2-->