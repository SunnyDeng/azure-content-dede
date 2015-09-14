<properties
	pageTitle="Sichern virtueller Azure-Computer – Problembehandlung | Microsoft Azure"
	description="Informationen zum Sichern und Wiederherstellen von virtuellen Azure-Computern"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="aashishr"/>


# Problembehandlung
Sie können die Problembehandlung für Fehler, die beim Verwenden von Azure Backup auftreten, mit den Informationen in der unten angegebenen Tabelle durchführen.

## Ermittlung

| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Ermittlung | Fehler beim Entdecken neuer Elemente – Microsoft Azure Backup hat einen internen Fehler festgestellt. Warten Sie einige Minuten, und versuchen Sie es dann erneut. | Wiederholen Sie den Ermittlungsvorgang nach 15 Minuten.
| Ermittlung | Fehler beim Entdecken neuer Elemente – Ein anderer Ermittlungsvorgang wird bereits ausgeführt. Bitte warten Sie, bis der aktuelle Ermittlungsvorgang abgeschlossen ist. | Keine |

## Registrieren
| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Registrieren | Die Azure VM-Rolle befindet sich nicht im Zustand zum Installieren der Erweiterung – Prüfen Sie, ob der virtuelle Computer den Zustand "Wird ausgeführt" aufweist. Für die Azure Recovery Services-Erweiterung muss der virtuelle Computer ausgeführt werden. | Starten Sie den virtuellen Computer, und wiederholen Sie den Registrierungsvorgang, wenn der Computer den Zustand "Wird ausgeführt" aufweist.|
| Registrieren | Für die Anzahl von Datenträgern für Daten, die dem virtuellen Computer zugeordnet sind, wurde die unterstützte Obergrenze überschritten. Trennen Sie einige Datenträger für Daten von diesem virtuellen Computer, und wiederholen Sie den Vorgang. Azure Backup unterstützt bis zu 16 Datenträger für Daten, die für die Sicherung an einen virtuellen Azure-Computer angeschlossen sind. | Keine |
| Registrieren | Für Microsoft Azure Backup ist ein interner Fehler aufgetreten. Warten Sie einige Minuten, und wiederholen Sie anschließend den Vorgang. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dieser Fehler kann aufgrund einer der folgenden nicht unterstützten Konfigurationen auftreten: <ol><li>Premium LRS <li>Multi-NIC <li>Load Balancer </ol> |
| Registrieren | Fehler bei der Registrierung aufgrund einer Zeitüberschreitung beim Installieren des Agents | Überprüfen Sie, ob die Betriebssystemversion des virtuellen Computers unterstützt wird. |
| Registrieren | Fehler beim Ausführen des Befehls – Für dieses Element wird ein anderer Vorgang ausgeführt. Warten Sie, bis der aktuelle Vorgang abgeschlossen ist. | Keine |
| Registrieren | Virtuelle Computer mit virtuellen Festplatten, die in Storage Premium gespeichert sind, werden für die Sicherung nicht unterstützt. | Keine |
| Registrieren | Der Agent für virtuelle Computer ist nicht auf dem virtuellen Computer vorhanden. Installieren Sie den VM-Agent (erforderliche Voraussetzung), und starten Sie den Vorgang erneut. | [Erfahren Sie mehr](#vm-agent) über die VM-Agent-Installation und die dazugehörige Überprüfung. |

## Sicherung

| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Sicherung | Zeitüberschreitung beim Kopieren von VHDs aus dem Sicherungstresor – Versuchen Sie, den Vorgang nach einigen Minuten zu wiederholen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dies passiert, wenn zu viele zu kopierende Daten vorhanden sind. Vergewissern Sie sich, dass Sie weniger als 16 Datenträger verwenden. |
| Sicherung | Die Kommunikation mit dem VM-Agent im Hinblick auf den Status der Momentaufnahme war nicht möglich. Zeitüberschreitung bei Momentaufnahme für VM-Unteraufgabe – Weitere Informationen zur Lösung dieses Problems finden Sie im Handbuch zur Problembehandlung. | Dieser Fehler wird ausgelöst, wenn ein Problem mit dem VM-Agent besteht oder der Netzwerkzugriff auf die Azure-Infrastruktur blockiert ist. <ul><li>Weitere Informationen zum [Debuggen von Problemen mit dem VM-Agent](#vm-agent) <li>Weitere Informationen zum [Debuggen von Netzwerkproblemen](#networking) </ul><br>Wenn der VM-Agent keine Probleme verursacht, starten Sie den virtuellen Computer neu. Gelegentlich kann ein falscher Status des virtuellen Computers Probleme verursachen. Durch einen Neustart des virtuellen Computers wird der Status zurückgesetzt. |
| Sicherung | Interner Fehler bei der Sicherung – Versuchen Sie, den Vorgang nach einigen Minuten zu wiederholen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dieser Fehler kann aus zwei Gründen auftreten: <ol><li> Es sind zu viele zu kopierende Daten vorhanden. <li>Der ursprüngliche virtuelle Computer wurde gelöscht, sodass keine Sicherung erstellt werden kann. Um die Sicherungsdaten für einen gelöschten virtuellen Computer beizubehalten und gleichzeitig die Sicherungsfehler zu vermeiden, heben Sie den Schutz für den virtuellen Computer auf und wählen die Option zum Beibehalten der Daten. So werden der Sicherungszeitplan und die wiederkehrenden Fehlermeldungen vermieden. |
| Sicherung | Fehler beim Installieren der Azure Recovery Services-Erweiterung auf dem ausgewählten Element – Der VM-Agent ist eine Voraussetzung für die Azure Recovery Services-Erweiterung. Installieren Sie den Azure-VM-Agent, und starten Sie den Registrierungsvorgang erneut. | <ol> <li>Überprüfen Sie, ob der VM-Agent richtig installiert wurde. <li>Stellen Sie sicher, dass das Flag für die VM-Konfiguration richtig festgelegt wurde.</ol> [Erfahren Sie mehr](#validating-vm-agent-installation) über die VM-Agent-Installation und die dazugehörige Überprüfung. |
| Sicherung | Fehler bei der Ausführung des Befehls – Für dieses Element wird ein anderer Vorgang ausgeführt. Warten Sie, bis der aktuelle Vorgang abgeschlossen ist, und wiederholen Sie anschließend Ihren Vorgang. | Ein vorhandener Sicherungs- oder Wiederherstellungsauftrag wird für den virtuellen Computer ausgeführt. Ein neuer Auftrag kann erst gestartet werden, wenn die Ausführung des aktuellen Auftrags abgeschlossen ist. |
| Sicherung | Die Erweiterungsinstallation ist mit dem Fehler "COM+ konnte keine Daten mit dem Microsoft Distributed Transaction Coordinator austauschen" fehlgeschlagen. | Dies bedeutet i. d. R., dass der COM+-Dienst nicht ausgeführt wird. Wenden Sie sich an den Microsoft Support, um Hilfe beim Beheben dieses Problems zu erhalten. |
| Sicherung | Der Momentaufnahmevorgang ist mit folgendem VSS-Vorgangsfehler fehlgeschlagen: "Dieses Laufwerk ist durch die BitLocker-Laufwerkverschlüsselung gesperrt. Das Laufwerk muss mithilfe der Systemsteuerung entsperrt werden." | Deaktivieren Sie BitLocker für alle Laufwerke auf dem virtuellen Computer, und überprüfen Sie, ob der VSS-Fehler behoben ist. |
| Sicherung | Virtuelle Computer mit virtuellen Festplatten, die in Storage Premium gespeichert sind, werden für die Sicherung nicht unterstützt. | Keine |
| Sicherung | Die Sicherung von virtuellen Computern mit Load Balancer-Konfiguration wird nicht unterstützt. | Keine |
| Sicherung | Die Sicherung von virtuellen Computern mit mehr als einer NIC wird nicht unterstützt. | Keine |
| Sicherung | Der virtuelle Azure-Computer wurde nicht gefunden. | Dies tritt auf, wenn der primäre virtuelle Computer gelöscht wurde, die Sicherungsrichtlinie jedoch weiterhin einen virtuellen Computer für die Sicherung sucht. Gehen Sie wie folgt vor, um diesen Fehler zu beheben: <ol><li>Erstellen Sie einen neuen virtuellen Computer mit demselben Namen und demselben Ressourcengruppennamen [Clouddienstnamen]. <br>(ODER) <li> Deaktivieren Sie den Schutz für den virtuellen Computer, damit keine Sicherungsaufträge erstellt werden.</ol> |
| Sicherung | Der Agent für virtuelle Computer ist nicht auf dem virtuellen Computer vorhanden. Installieren Sie den VM-Agent (erforderliche Voraussetzung), und starten Sie den Vorgang erneut. | [Erfahren Sie mehr](#vm-agent) über die VM-Agent-Installation und die dazugehörige Überprüfung. |

## Aufträge
| Vorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Auftrag abbrechen | Der Abbruch wird für diesen Auftragstyp nicht unterstützt. Warten Sie, bis der Auftrag abgeschlossen ist. | Keine |
| Auftrag abbrechen | Der Auftrag kann in diesem Status nicht abgebrochen werden. Warten Sie, bis der Auftrag abgeschlossen ist. <br>ODER<br> Der ausgewählte Auftrag kann in diesem Status nicht abgebrochen werden. Warten Sie, bis der Auftrag abgeschlossen ist.| Der Auftrag ist aller Wahrscheinlichkeit nach fast abgeschlossen. Warten Sie, bis der Auftrag abgeschlossen ist. |
| Auftrag abbrechen | Das Abbrechen des Auftrags ist nicht möglich, da sich dieser nicht in Bearbeitung befindet. Der Abbruch wird nur für Aufträge in Bearbeitung unterstützt. Wiederholen Sie den Abbruchversuch für einen Auftrag, der sich in Bearbeitung befindet. | Dies wird durch einen vorübergehenden Zustand verursacht. Warten Sie eine Minute, und wiederholen Sie den Abbruchvorgang. |
| Auftrag abbrechen | Fehler beim Abbrechen des Auftrags – Warten Sie, bis der Auftrag abgeschlossen ist. | Keine |


## Wiederherstellen
| Vorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Wiederherstellen | Cloudinterner Fehler bei der Wiederherstellung | <ol><li>Der Clouddienst, in dem Sie die Wiederherstellung durchführen möchten, ist mit DNS-Einstellungen konfiguriert. Prüfen Sie <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Wenn "Address" konfiguriert ist, bedeutet dies, dass DNS-Einstellungen konfiguriert wurden.<br> <li>Der Clouddienst, in dem Sie die Wiederherstellung durchführen möchten, ist mit ReservedIP konfiguriert, und vorhandene virtuelle Computer im Clouddienst befinden sich im Zustand "Beendet".<br>Mit den folgenden Powershell-Cmdlets können Sie prüfen, ob ein Clouddienst über eine reservierte IP-Adresse verfügt:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName</ol> |
| Wiederherstellen | Der ausgewählte DNS-Name ist bereits vergeben. Geben Sie einen anderen DNS-Namen an, und versuchen Sie es erneut. | Der DNS-Name bezieht sich hier auf den Clouddienstnamen (der i. d. R. auf ".cloudapp.net" endet). Dieser muss eindeutig sein. Wenn der vorliegende Fehler auftritt, müssen Sie während der Wiederherstellung einen anderen Namen für den virtuellen Computer auswählen. <br><br> Beachten Sie, dass dieser Fehler nur Benutzern des Azure-Portals angezeigt wird. Der Wiederherstellungsvorgang über PowerShell ist erfolgreich, da nur die Datenträger wiederhergestellt werden und kein virtueller Computer erstellt wird. Der Fehler tritt auf, wenn der virtuelle Computer nach Abschluss des Wiederherstellungsvorgangs für die Datenträger explizit von Ihnen erstellt wird. |
| Wiederherstellen | Die angegebene Konfiguration des virtuellen Netzwerks ist nicht korrekt. Geben Sie eine andere Konfiguration des virtuellen Netzwerks an, und versuchen Sie es erneut. | Keine |
| Wiederherstellen | Der angegebene Clouddienst verwendet eine reservierte IP, die nicht mit der Konfiguration des virtuellen Computers übereinstimmt, der wiederhergestellt werden soll. Geben Sie einen anderen Clouddienst an, der keine reservierte IP verwendet, oder wählen Sie einen anderen Wiederherstellungspunkt aus. | Keine |
| Wiederherstellen | Der Clouddienst hat den Grenzwert für die Anzahl der Eingabeendpunkte erreicht. Wiederholen Sie den Vorgang, indem Sie einen anderen Clouddienst angeben oder einen vorhandenen Endpunkt verwenden. | Keine |
| Wiederherstellen | Der Sicherungstresor und das Zielspeicherkonto befinden sich in unterschiedlichen Regionen. Stellen Sie sicher, dass sich das für den Wiederherstellungsvorgang angegebene Speicherkonto in derselben Azure-Region wie der Sicherungstresor befindet. | Keine |
| Wiederherstellen | Das für den Wiederherstellungsvorgang angegebene Speicherkonto wird nicht unterstützt. Es werden nur Basic-/Standard-Speicherkonten mit lokal redundanten oder georedundanten Replikationseinstellungen unterstützt. Wählen Sie ein unterstütztes Speicherkonto aus. | Keine |
| Wiederherstellen | Der Typ des für den Wiederherstellungsvorgang angegebenen Speicherkontos ist nicht online. Stellen Sie sicher, dass das für den Wiederherstellungsvorgang angegebene Speicherkonto online ist. | Dies kann durch einen vorübergehenden Fehler in Azure Storage oder einen Ausfall verursacht werden. Wählen Sie ein anderes Speicherkonto aus. |
| Wiederherstellen | Das Kontingent für Ressourcengruppen wurde erreicht. Löschen Sie einige Ressourcengruppen aus dem Vorschauportal, oder wenden Sie sich an den Azure-Support, um die Begrenzung zu erhöhen. | Keine |
| Wiederherstellen | Das ausgewählte Subnetz besteht nicht. Wählen Sie ein vorhandenes Subnetz aus. | Keine |


## Richtlinie
| Vorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Richtlinie erstellen | Fehler beim Erstellen der Richtlinie – Verringern Sie die Aufbewahrungsdauer, um mit der Richtlinienkonfiguration fortzufahren. | Keine |


## VM-Agent

### Einrichten des VM-Agents
Normalerweise ist der VM-Agent auf virtuellen Computern, die über den Azure-Katalog erstellt werden, bereits vorhanden. Auf virtuellen Computern, die aus lokalen Rechenzentren migriert werden, ist der VM-Agent aber nicht installiert. Für diese virtuellen Computer muss der VM-Agent explizit installiert werden. Erfahren Sie mehr über das [Installieren des VM-Agents auf einem vorhandenen virtuellen Computer](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Für virtuelle Windows-Computer:

- Laden Sie den [Agent-MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie ihn. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen.
- [Aktualisieren Sie die VM-Eigenschaft](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), um anzugeben, dass der Agent installiert wurde.


### Aktualisieren des VM-Agents
Das Aktualisieren des VM-Agents ist so einfach wie das Neuinstallieren der [Binärdateien für den VM-Agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Allerdings müssen Sie sicherstellen, dass kein Sicherungsvorgang ausgeführt wird, während der VM-Agent aktualisiert wird.


### Überprüfen der VM-Agent-Installation
So überprüfen Sie die Version des VM-Agents auf virtuellen Windows-Computern

1. Melden Sie sich beim virtuellen Azure-Computer an, und navigieren Sie zum Ordner *C:\\WindowsAzure\\Packages*. Dieser Ordner enthält die Datei "WaAppAgent.exe".
2. Klicken Sie mit der rechten Maustaste auf die Datei, wechseln Sie zu **Eigenschaften**, und wählen Sie dann die Registerkarte **Details** aus. Im Feld mit der Produktversion sollte 2.6.1198.718 oder eine höhere Version angegeben sein.

## Netzwerk
Wie bei allen Erweiterungen ist für die Backup-Erweiterung der Zugriff auf das öffentliche Internet erforderlich, damit sie funktioniert. Wenn kein Zugriff auf das öffentliche Internet besteht, kann dies zu unterschiedlichen Ergebnissen führen:

- Beim Installieren der Erweiterung kann ein Fehler auftreten.
- Bei den Sicherungsvorgängen (z. B. Datenträger-Momentaufnahme) kann ein Fehler auftreten.
- Beim Anzeigen des Status für den Sicherungsvorgang kann ein Fehler auftreten.

Die Notwendigkeit zur Auflösung von öffentlichen Internetadressen wird [hier](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx) beschrieben. Sie müssen die DNS-Konfigurationen für das VNET überprüfen und sicherstellen, dass die Azure-URIs aufgelöst werden können.

Nachdem die Namensauflösung richtig eingerichtet wurde, muss auch der Zugriff auf die Azure-IP-Adressen bereitgestellt werden. Führen Sie die folgenden Schritte aus, um die Blockierung des Zugriffs auf die Azure-Infrastruktur aufzuheben:

1. Beschaffen Sie sich die Liste mit den [IP-Adressen des Azure-Rechenzentrums](https://msdn.microsoft.com/library/azure/dn175718.aspx), die auf der Positivliste stehen sollen.
2. Heben Sie Blockierung für die IP-Adressen mit dem [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx)-Cmdlet auf. Führen Sie dieses Cmdlet auf dem virtuellen Azure-Computer in einem PowerShell-Fenster mit erhöhten Rechten aus (Als Administrator ausführen).

<!---HONumber=September15_HO1-->