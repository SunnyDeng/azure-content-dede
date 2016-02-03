<properties
	pageTitle="Fehlerbehandlung bei der Sicherung virtueller Azure-Computer | Microsoft Azure"
	description="Fehlerbehandlung bei der Sicherung und Wiederherstellung virtueller Azure-Computer"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="trinadhk;jimpark;aashishr"/>


# Problembehandlung bei der Sicherung virtueller Azure-Computer
Sie können die Problembehandlung für Fehler, die beim Verwenden von Azure Backup auftreten, mit den Informationen in der unten angegebenen Tabelle durchführen.

## Ermittlung

| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Ermittlung | Fehler beim Entdecken neuer Elemente – Microsoft Azure Backup hat einen internen Fehler festgestellt. Warten Sie einige Minuten, und versuchen Sie es dann erneut. | Wiederholen Sie den Ermittlungsvorgang nach 15 Minuten.
| Ermittlung | Fehler beim Entdecken neuer Elemente – Ein anderer Ermittlungsvorgang wird bereits ausgeführt. Bitte warten Sie, bis der aktuelle Ermittlungsvorgang abgeschlossen ist. | Keine |

## Registrieren
| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Registrieren | Für die Anzahl von Datenträgern für Daten, die dem virtuellen Computer zugeordnet sind, wurde die unterstützte Obergrenze überschritten. Trennen Sie einige Datenträger für Daten von diesem virtuellen Computer, und wiederholen Sie den Vorgang. Azure Backup unterstützt bis zu 16 Datenträger für Daten, die für die Sicherung an einen virtuellen Azure-Computer angeschlossen sind. | Keine |
| Registrieren | Für Microsoft Azure Backup ist ein interner Fehler aufgetreten. Warten Sie einige Minuten, und wiederholen Sie anschließend den Vorgang. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dieser Fehler kann aufgrund einer der folgenden nicht unterstützten Konfigurationen auftreten: <ul><li>Premium LRS</ul>. |
| Registrieren | Fehler bei der Registrierung aufgrund einer Zeitüberschreitung beim Installieren des Agents | Überprüfen Sie, ob die Betriebssystemversion des virtuellen Computers unterstützt wird. |
| Registrieren | Fehler beim Ausführen des Befehls – Für dieses Element wird ein anderer Vorgang ausgeführt. Warten Sie, bis der aktuelle Vorgang abgeschlossen ist. | Keine |
| Registrieren | Virtuelle Computer mit virtuellen Festplatten, die in Storage Premium gespeichert sind, werden für die Sicherung nicht unterstützt. | Keine |
| Registrieren | Der Agent für virtuelle Computer ist nicht auf dem virtuellen Computer vorhanden. Installieren Sie den VM-Agent (erforderliche Voraussetzung), und starten Sie den Vorgang erneut. | [Erfahren Sie mehr](#vm-agent) über die VM-Agent-Installation und die dazugehörige Überprüfung. |

## Sicherung

| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Sicherung | Zeitüberschreitung beim Kopieren von VHDs aus dem Sicherungstresor – Versuchen Sie, den Vorgang nach einigen Minuten zu wiederholen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dies passiert, wenn zu viele zu kopierende Daten vorhanden sind. Vergewissern Sie sich, dass Sie weniger als 16 Datenträger verwenden. |
| Sicherung | Die Kommunikation mit dem VM-Agent im Hinblick auf den Status der Momentaufnahme war nicht möglich. Zeitüberschreitung bei Momentaufnahme für VM-Unteraufgabe – Weitere Informationen zur Lösung dieses Problems finden Sie im Handbuch zur Problembehandlung. | Dieser Fehler wird ausgelöst, wenn ein Problem mit dem VM-Agent besteht oder der Netzwerkzugriff auf die Azure-Infrastruktur blockiert ist. <ul> <li>Informationen zum [Debuggen von VM-Agent-Problemen](#vm-agent) <li>Informationen zum [Debuggen von Netzwerkproblemen](#networking) <li>Wenn der VM-Agent problemlos ausgeführt wird, helfen Ihnen folgende Informationen weiter: [Problembehandlung für Probleme mit VM-Momentaufnahmen](#Troubleshoot-VM-Snapshot-Issues)</ul><br>Wenn der VM-Agent keine Probleme verursacht, sollten Sie die VM neu starten. Gelegentlich kann ein falscher Status des virtuellen Computers Probleme verursachen. Durch einen Neustart des virtuellen Computers wird der Status zurückgesetzt. |
| Sicherung | Interner Fehler bei der Sicherung – Versuchen Sie, den Vorgang nach einigen Minuten zu wiederholen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dieser Fehler kann aus zwei Gründen auftreten: <ol><li> Es sind zu viele zu kopierende Daten vorhanden. <li>Der ursprüngliche virtuelle Computer wurde gelöscht, sodass keine Sicherung erstellt werden kann. Um die Sicherungsdaten für einen gelöschten virtuellen Computer beizubehalten und gleichzeitig die Sicherungsfehler zu vermeiden, heben Sie den Schutz für den virtuellen Computer auf und wählen die Option zum Beibehalten der Daten. So werden der Sicherungszeitplan und die wiederkehrenden Fehlermeldungen vermieden. |
| Sicherung | Fehler beim Installieren der Azure Recovery Services-Erweiterung auf dem ausgewählten Element – Der VM-Agent ist eine Voraussetzung für die Azure Recovery Services-Erweiterung. Installieren Sie den Azure-VM-Agent, und starten Sie den Registrierungsvorgang erneut. | <ol> <li>Überprüfen Sie, ob der VM-Agent richtig installiert wurde. <li>Stellen Sie sicher, dass das Flag für die VM-Konfiguration richtig festgelegt wurde.</ol> [Erfahren Sie mehr](#validating-vm-agent-installation) über die VM-Agent-Installation und die dazugehörige Überprüfung. |
| Sicherung | Fehler bei der Ausführung des Befehls – Für dieses Element wird ein anderer Vorgang ausgeführt. Warten Sie, bis der aktuelle Vorgang abgeschlossen ist, und wiederholen Sie anschließend Ihren Vorgang. | Ein vorhandener Sicherungs- oder Wiederherstellungsauftrag wird für den virtuellen Computer ausgeführt. Ein neuer Auftrag kann erst gestartet werden, wenn die Ausführung des aktuellen Auftrags abgeschlossen ist. |
| Sicherung | Die Erweiterungsinstallation ist mit dem Fehler "COM+ konnte keine Daten mit dem Microsoft Distributed Transaction Coordinator austauschen" fehlgeschlagen. | Dies bedeutet i. d. R., dass der COM+-Dienst nicht ausgeführt wird. Wenden Sie sich an den Microsoft Support, um Hilfe beim Beheben dieses Problems zu erhalten. |
| Sicherung | Der Momentaufnahmevorgang ist mit folgendem VSS-Vorgangsfehler fehlgeschlagen: "Dieses Laufwerk ist durch die BitLocker-Laufwerkverschlüsselung gesperrt. Das Laufwerk muss mithilfe der Systemsteuerung entsperrt werden." | Deaktivieren Sie BitLocker für alle Laufwerke auf dem virtuellen Computer, und überprüfen Sie, ob der VSS-Fehler behoben ist. |
| Sicherung | Virtuelle Computer mit virtuellen Festplatten, die in Storage Premium gespeichert sind, werden für die Sicherung nicht unterstützt. | Keine |
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
| Wiederherstellen | Cloudinterner Fehler bei der Wiederherstellung | <ol><li>Der Clouddienst, in dem Sie die Wiederherstellung durchführen möchten, ist mit DNS-Einstellungen konfiguriert. Prüfen Sie <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Wenn "Address" konfiguriert ist, bedeutet dies, dass DNS-Einstellungen konfiguriert wurden.<br> <li>Der Clouddienst, in dem Sie die Wiederherstellung durchführen möchten, ist mit ReservedIP konfiguriert, und vorhandene virtuelle Computer im Clouddienst weisen den Status „Beendet“ auf.<br>Mithilfe des folgenden PowerShell-Cmdlets können Sie überprüfen, ob ein Clouddienst über eine reservierte IP-Adresse verfügt:<br>$deployment = Get-AzureDeployment-ServiceName "Servicename"-Slot "Production" $DEP ReservedIPName <br><li>Sie versuchen, eine virtuelle Maschine mit der folgenden speziellen Netzwerkkonfigurationen im selben Clouddienst wiederherzustellen. <br>- Virtuelle Computer unter der Konfiguration des Load Balancers (intern und extern)<br>- Virtuelle Computer mit mehreren reservierten IP-Adressen<br> Virtuelle Computer mit mehreren NICs<br>Wählen Sie einen neuen Clouddienst in der Benutzeroberfläche aus, oder wechseln Sie zu [Überlegungen zur Wiederherstellung](backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) für virtuelle Computer mit speziellen Netzwerkkonfigurationen</ol> |
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

Für virtuelle Linux-Computer:

- Installieren Sie den neuesten [Linux-Agent](https://github.com/Azure/WALinuxAgent) aus GitHub.
- [Aktualisieren Sie die VM-Eigenschaft](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), um anzugeben, dass der Agent installiert wurde.


### Aktualisieren des VM-Agents
Für virtuelle Windows-Computer:

- Das Aktualisieren des VM-Agents ist so einfach wie das Neuinstallieren der [Binärdateien für den VM-Agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Allerdings müssen Sie sicherstellen, dass kein Sicherungsvorgang ausgeführt wird, während der VM-Agent aktualisiert wird.

Für virtuelle Linux-Computer:

- Folgen Sie den Anweisungen unter [Aktualisieren des virtuellen Linux-Agents](../virtual-machines-linux-update-agent.md).


### Überprüfen der VM-Agent-Installation
So überprüfen Sie die Version des VM-Agents auf virtuellen Windows-Computern

1. Melden Sie sich beim virtuellen Azure-Computer an, und navigieren Sie zum Ordner *C:\\WindowsAzure\\Packages*. Dieser Ordner enthält die Datei "WaAppAgent.exe".
2. Klicken Sie mit der rechten Maustaste auf die Datei, wechseln Sie zu **Eigenschaften**, und wählen Sie dann die Registerkarte **Details** aus. Im Feld mit der Produktversion sollte 2.6.1198.718 oder eine höhere Version angegeben sein.

## Problembehandlung für Probleme mit VM-Momentaufnahmen
Bei der VM-Sicherung werden Momentaufnahmenbefehle an den zugrunde liegenden Speicher ausgegeben. Wenn Sie bei der Ausführung von Momentaufnahmenaufgaben keinen Zugriff auf Speicherung oder Verzögerung haben, ist der Sicherungsvorgang unter Umständen nicht erfolgreich. Es kann die unten angegebenen Gründe haben, wenn für Momentaufnahmenaufgaben ein Fehler auftritt.

1. Netzwerkzugriff auf Storage wird per NSG blockiert<br> Lesen Sie weitere Informationen zum [Aktivieren des Netzwerkzugriffs](backup-azure-vms-prepare.md#2-network-connectivity) auf Storage durch das Setzen von IPs auf eine Positivliste oder per Proxyserver.
2.  VMs mit konfigurierter SQL Server-Sicherung können zu Verzögerungen bei der Momentaufnahmenaufgabe führen <br> Standardmäßig wird bei der VM-Sicherung eine vollständige VSS-Sicherung auf Windows-VMs ausgegeben. Auf VMs, auf denen SQL-Server ausgeführt werden und die SQL Server-Sicherung konfiguriert ist, kann dies eine Verzögerung bei der Ausführung von Momentaufnahmen zur Folge haben. Legen Sie den folgenden Registrierungsschlüssel fest, wenn bei Ihnen aufgrund von Problemen mit Momentaufnahmen Sicherungsfehler auftreten.

	```
	[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
	"USEVSSCOPYBACKUP"="TRUE"
	```
3.  Der VM-Status wird falsch gemeldet, da die VM im RDP heruntergefahren ist. <br> Wenn Sie die virtuelle Maschine im RDP heruntergefahren haben, sollten Sie im Portal überprüfen, ob der VM-Status richtig wiedergegeben wird. Falls nicht, beenden Sie die VM im Portal, indem Sie im VM-Dashboard die Option „Herunterfahren“ verwenden.
4.  Viele VMs desselben Clouddiensts sind so konfiguriert, dass die Sicherung zur selben Zeit durchgeführt wird.<br> Die bewährte Methode besteht darin, für die VMs eines Clouddiensts unterschiedliche Sicherungszeitpläne zu verwenden.
5.  Die VM wird bei hoher CPU-/Arbeitsspeicherauslastung ausgeführt.<br> Wenn die virtuelle Maschine bei hoher CPU-Auslastung (> 90 %) oder Arbeitsspeicherauslastung ausgeführt wird, wird die Momentaufnahmenaufgabe in die Warteschlange eingereiht und verzögert und erreicht schließlich die Zeitüberschreitung. Versuchen Sie es in diesem Fall mit bedarfsgesteuerten Sicherungen.

<br>

## Netzwerk
Wie bei allen Erweiterungen ist für die Backup-Erweiterung der Zugriff auf das öffentliche Internet erforderlich, damit sie funktioniert. Wenn kein Zugriff auf das öffentliche Internet besteht, kann dies zu unterschiedlichen Ergebnissen führen:

- Beim Installieren der Erweiterung kann ein Fehler auftreten.
- Bei den Sicherungsvorgängen (z. B. Datenträger-Momentaufnahme) kann ein Fehler auftreten.
- Beim Anzeigen des Status für den Sicherungsvorgang kann ein Fehler auftreten.

Die Notwendigkeit zur Auflösung von öffentlichen Internetadressen wird [hier](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx) beschrieben. Sie müssen die DNS-Konfigurationen für das VNET überprüfen und sicherstellen, dass die Azure-URIs aufgelöst werden können.

Nachdem die Namensauflösung richtig eingerichtet wurde, muss auch der Zugriff auf die Azure-IP-Adressen bereitgestellt werden. Führen Sie einen der folgenden Schritte aus, um die Blockierung des Zugriffs auf die Azure-Infrastruktur aufzuheben:

1. Aufnehmen der IP-Bereiche des Azure-Rechenzentrums in eine Positivliste
    - Beschaffen Sie sich die Liste mit den [IP-Adressen des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653), die auf der Positivliste stehen sollen.
    - Heben Sie Blockierung für die IP-Adressen mit dem Cmdlet [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) auf. Führen Sie dieses Cmdlet auf dem virtuellen Azure-Computer in einem PowerShell-Fenster mit erhöhten Rechten aus (als Administrator).
    - Fügen Sie der NSG (falls in der Organisation vorhanden) Regeln für den Zugriff auf die IP-Adressen hinzu.
2. Erstellen eines Pfads für HTTP-Datenverkehr
    - Wenn Netzwerkeinschränkung bestehen (beispielsweise eine Netzwerksicherheitsgruppe) sollte ein HTTP-Proxyserver zum Weiterleiten des Datenverkehrs bereitgestellt werden. Schritte zum Bereitstellen eines HTTP-Proxy-Servers finden Sie [hier](backup-azure-vms-prepare.md#2-network-connectivity).
    - Fügen Sie der NSG (falls in der Organisation vorhanden) Regeln für den Zugriff auf das Internet über den HTTP-Proxy hinzu.

>[AZURE.NOTE]Für die VM-Sicherung mithilfe von IaaS muss im Gastbetriebssystem die DHCP-Option aktiviert sein. Wenn Sie eine statische private IP-Adresse benötigen, sollten Sie diese über die Plattform konfigurieren. Die DHCP-Option innerhalb des virtuellen Computers sollte aktiviert bleiben. Weitere Informationen zum Festlegen einer statischen internen IP-Adresse finden Sie [hier](virtual-networks-reserved-private-ip.md).

<!---HONumber=AcomDC_0121_2016-->