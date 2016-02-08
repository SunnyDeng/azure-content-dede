<properties
	pageTitle="Häufig gestellte Fragen zu virtuellen Computern | Microsoft Azure"
	description="Hier finden Sie Antworten auf die häufigsten Fragen zu virtuellen Azure-Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/16/2015"
	ms.author="cynthn"/>

# Häufig gestellte Fragen zu virtuellen Azure-Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Dieser Artikel enthält die am häufigsten gestellten Fragen zu virtuellen Azure-Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden.

## Was kann ich auf einem virtuellen Azure-Computer ausführen?

Alle Abonnenten können Serversoftware auf einem virtuellen Azure-Computer ausführen. Sie können die neuesten Versionen von Windows Server sowie eine Vielzahl von Linux-Distributionen ausführen. Supportinformationen finden Sie unter:

• Virtuelle Windows-Computer: [Unterstützung für Microsoft-Serversoftware für virtuelle Azure-Computer](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• Virtuelle Linux-Computer: [Linux auf von Azure unterstützten Verteilungen](http://go.microsoft.com/fwlink/p/?LinkId=393551)

Für Windows-Client-Images stehen bestimmte Versionen von Windows 7 und Windows 8.1 für MSDN-Abonnenten von Azure-Vorteilen und Abonnenten von MSDN-Entwicklung und Tests mit nutzungsbasierter Zahlung für Entwicklungs- und Testzwecke bereit. Weitere Informationen, u. a. Anleitungen und Einschränkungen, finden Sie unter [Windows-Client-Images für MSDN-Abonnenten](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## Wie viel Speicher kann mit einem virtuellen Computer verwendet werden?

Jeder Datenträger kann bis zu 1 TB groß sein. Die Anzahl der Datenträger, die Sie verwenden können, hängt von der Größe des virtuellen Computers ab. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](virtual-machines-size-specs.md).

Azure-Speicherkonten bieten Speicher für Betriebssystem-Datenträger und Datenträger für Daten. Bei jedem Datenträger handelt es sich um eine VHD-Datei, die als Seiten-Blob gespeichert wird. Ausführliche Informationen zu Preisen finden Sie unter [Speicherpreisübersicht](http://go.microsoft.com/fwlink/p/?LinkId=396819).

## Welche Arten von virtuellen Festplatten kann ich verwenden?

Azure unterstützt nur virtuelle Festplatten mit fester Größe im VHD-Format. Wenn Sie einen Datenträger im VHDX-Format in Azure verwenden möchten, konvertieren Sie ihn mit Hyper-V-Manager oder dem Cmdlet [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656). Laden Sie danach mit dem Cmdlet [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (im Dienstverwaltungsmodus) die virtuelle Festplatte in ein Speicherkonto in Azure hoch, damit Sie sie mit virtuellen Computern verwenden können.

- Anweisungen für Linux finden Sie unter [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält](virtual-machines-linux-create-upload-vhd.md).

- Anweisungen für Windows finden Sie unter [Erstellen und Hochladen einer Windows Server-VHD nach Azure](virtual-machines-create-upload-vhd-windows-server.md).

## Sind diese virtuellen Computer mit Hyper-V-Computern identisch?

In vielerlei Hinsicht ähneln sie Hyper-V-Computer der "ersten Generation", sie sind jedoch nicht genau gleich. Beide Typen stellen virtualisierte Hardware bereit, und die virtuellen Festplatten im VHD-Format sind kompatibel. Dies bedeutet, dass Sie sie zwischen Hyper-V und Azure verschieben können. Drei wichtige Unterschiede, die für Benutzer von Hyper-V manchmal überraschend sind:

- Azure stellt keinen Konsolenzugriff auf einem virtuellen Computer bereit. Es gibt keine Möglichkeit, auf einen virtuellen Computer zuzugreifen, bevor dieser vollständig gestartet wurde.
- Azure-VMs der meisten [Größen](virtual-machines-size-specs.md) verfügen nur über einen virtuellen Netzwerkadapter. Dies bedeutet, dass sie auch nur eine externe IP-Adresse besitzen können. (Bei den Größen A8 und A9 wird ein zweiter Netzwerkadapter für die Anwendungskommunikation zwischen Instanzen in begrenzten Szenarien verwendet.)
- Virtuelle Azure-Computer unterstützen keine Hyper-V-VM-Funktionen der zweiten Generation. Ausführliche Informationen zu diesen Funktionen finden Sie unter [Spezifikationen des virtuellen Computers für Hyper-V in Windows Server 2012 R2](http://technet.microsoft.com/library/dn592184.aspx) und [Virtuelle Computer der Generation 2 (Übersicht)](https://technet.microsoft.com/library/dn282285.aspx).

## Können diese virtuellen Computer meine vorhandene lokale Netzwerkinfrastruktur verwenden?

Für virtuelle Computer, die im klassischen Bereitstellungsmodus erstellt wurden, können Sie Azure Virtual Network verwenden, um Ihre vorhandene Infrastruktur zu erweitern. Dieser Ansatz entspricht dem Einrichten einer Filiale. Sie können in Azure virtuelle private Netzwerke (VPN) bereitstellen und verwalten und diese sicher mit Ihrer IT-Infrastruktur vor Ort verbinden. Weitere Informationen finden Sie unter [Virtuelle Netzwerke im Überblick](../virtual-network/virtual-networks-overview.md).

Sie müssen beim Erstellen des virtuellen Computers das Netzwerk angeben, zu dem der virtuelle Computer gehören soll. Sie können einen vorhandenen virtuellen Computer nicht einem virtuellen Netzwerk hinzufügen. Allerdings können Sie dieses Problem umgehen, indem Sie die virtuelle Festplatte (VHD) vom vorhandenen virtuellen Computer trennen und dann hiermit einen neuen virtuellen Computer mit der gewünschten Netzwerkkonfiguration erstellen.

## Wie kann ich auf meinen virtuellen Computer zugreifen?

Sie müssen über Remotedesktopverbindung (virtuelle Windows-Computer) oder SSH (Secure Shell, für virtuelle Linux-Computer) eine Remoteverbindung herstellen, um sich bei dem virtuellen Computer anzumelden. Anweisungen dazu finden Sie unter:

- [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](virtual-machines-log-on-windows-server.md) Es werden maximal zwei gleichzeitige Verbindungen unterstützt, es sei denn, der Server wurde als Host für eine Remotedesktopdienste-Sitzung konfiguriert.  
- [Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-how-to-log-on.md) SSH erlaubt standardmäßig maximal 10 gleichzeitige Verbindungen. Sie können diese Anzahl erhöhen, indem Sie die Konfigurationsdatei bearbeiten.

Wenn bei Remotedesktop oder SSH Probleme auftreten, installieren und verwenden Sie die [VMAccess](virtual-machines-extensions-features.md)-Erweiterung, um das Problem zu beheben.

Zusätzliche Optionen für virtuelle Windows-Computer:

- Suchen Sie den virtuellen Computer im klassischen Azure-Portal, und klicken Sie auf der Befehlsleiste auf **Remotezugriff zurücksetzen**.
- Lesen Sie [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md).
- Verwenden Sie Windows PowerShell-Remoting zum Herstellen der Verbindung mit dem virtuellen Computer, oder erstellen Sie zusätzliche Endpunkte, damit andere Ressourcen die Verbindung mit dem virtuellen Computer herstellen können. Weitere Informationen finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](virtual-machines-set-up-endpoints.md).

Wenn Sie mit Hyper-V vertraut sind, suchen Sie möglicherweise nach einem ähnlichen Tool wie VMConnect. Azure bietet kein ähnliches Tool, da der Konsolenzugriff auf einem virtuellen Computer nicht unterstützt wird.

## Kann ich den temporären Datenträger (Laufwerk D: bei Windows oder/dev/sdb1 unter Linux) zum Speichern von Daten verwenden?

Sie sollten den temporären Datenträger (standardmäßig Laufwerk D: bei Windows oder/dev/sdb1 unter Linux) nicht zum Speichern von Daten verwenden. Diese bieten nur einen temporären Speicher, sodass das Risiko eines Verlusts von Daten besteht, die nicht wiederhergestellt werden können. Dies kann vorkommen, wenn ein virtueller Computer auf einen anderen Host verschoben wird. Gründe für das Verschieben eines virtuellen Computers sind eine Änderung Größe des virtuellen Computers, Aktualisieren des Hosts oder ein Hardwarefehler auf dem Host.

## Wie kann ich den Laufwerkbuchstaben des temporären Datenträgers ändern?

Sie können auf einem virtuellen Windows-Computer den Laufwerkbuchstaben ändern, indem Sie die Auslagerungsdatei verschieben und die Laufwerkbuchstaben neu zuweisen. Sie müssen dabei jedoch sicherstellen, dass Sie die Schritte in einer bestimmten Reihenfolge ausführen. Anweisungen finden Sie unter [Ändern des Datenträgerbuchstabens des temporären Windows-Datenträgers](virtual-machines-windows-change-drive-letter.md).

## Wie kann ich ein Upgrade des Gastbetriebssystems ausführen?

Der Begriff des Upgrades bezeichnet im Allgemeinen die Umstellung auf eine neuere Version des Betriebssystems, wobei die Hardware beibehalten wird. Für virtuelle Azure-Computer unterscheidet sich der Prozess für die Umstellung auf eine neuere Version für Linux und Windows:

- Verwenden Sie für virtuelle Linux-Computer die für die Distribution geeigneten Paketverwaltungstools und -verfahren.
- Für einen virtuellen Windows-Computer müssen Sie den Server z. B. mit den Windows Server-Migrationstools migrieren. Versuchen Sie nicht, das Gastbetriebssystem zu aktualisieren, während es sich in Azure befindet. Dies wird nicht unterstützt, da der Zugriff auf den virtuellen Computer verloren gehen kann. Wenn während des Upgrades Probleme auftreten, können Sie möglicherweise keine Remotedesktopsitzung mehr starten und die Probleme nicht beheben. 

Allgemeine Informationen zu Tools und Verfahren zum Migrieren von Windows Server finden Sie unter [Migrieren von Rollen und Features zu Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=396940).



## Wie lauten der Standardbenutzername und das Kennwort auf dem virtuellen Computer?

Die von Azure bereitgestellten Images verfügen nicht über einen vorkonfigurierten Benutzernamen und ein Kennwort. Wenn Sie die virtuellen Computer mit einem dieser Images erstellen, müssen Sie einen Benutzernamen und ein Kennwort angeben, mit denen Sie sich beim virtuellen Computer anmelden.

Wenn Sie den Benutzernamen oder das Kennwort vergessen und den VM-Agent installiert haben, können Sie die [VMAccess](virtual-machines-extensions-features.md)-Erweiterung installieren und verwenden, um das Problem zu beheben.

Weitere Details:


- Wenn Sie bei Linux-Images das klassische Azure-Portal verwenden, ist „azureuser“ als Standardbenutzername angegeben. Sie können diesen Namen aber ändern, indem Sie den virtuellen Computer mit der Option „Aus Katalog“ und nicht mit „Schnellerfassung“ erstellen. Mit "Aus Katalog" können Sie auch entscheiden, ob Sie ein Kennwort und/oder einen SSH-Schlüssel für die Anmeldung verwenden möchten. Bei dem Benutzerkonto handelt es sich um einen Benutzer ohne Berechtigungen mit sudo-Zugriff zum Ausführen von privilegierten Befehlen. Das root-Konto ist deaktiviert.


- Für Windows-Images müssen Sie einen Benutzernamen und ein Kennwort bereitstellen, wenn Sie den virtuellen Computer erstellen. Das Konto wird der Gruppe "Administratoren" hinzugefügt.

## Kann Azure Antivirenprogramme auf meinen virtuellen Computern ausführen?

Azure bietet verschiedene Optionen für Virenschutz-Softwarelösungen. Ihre Verwaltung ist jedoch Ihnen überlassen. Sie benötigen z. B. möglicherweise ein separates Abonnement für Antischadsoftware und müssen entscheiden, wann Scans ausgeführt und Updates installiert werden. Sie können die Unterstützung für Antiviruslösungen mit einer VM-Erweiterung für Microsoft Antimalware, Symantec Endpoint Protection oder TrendMicro Deep Security Agent beim Erstellen eines virtuellen Windows-Computers oder zu einem späteren Zeitpunkt hinzufügen. Für die Erweiterungen für Symantec und TrendMicro können Sie ein kostenloses Testabonnement für einen begrenzten Zeitraum oder ein vorhandenes Enterprise-Abonnement verwenden. Microsoft Antimalware ist kostenlos. Einzelheiten dazu finden Sie hier:

- [Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [Installieren und Konfigurieren von Trend Micro Deep Security als Dienst auf einem virtuellen Azure-Computer](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [Deploying Antimalware Solutions on Azure Virtual Machines (Bereitstellen von Antischadsoftware-Lösungen auf virtuellen Azure-Computern, in englischer Sprache)](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## Welche Optionen sind für Sicherung und Wiederherstellung verfügbar?

Die Azure-Sicherung ist als Vorschau in bestimmten Regionen verfügbar. Weitere Informationen finden Sie unter [Sichern eines virtuellen Azure-Computers](backup-azure-vms.md). Andere Lösungen sind von zertifizierten Partnern erhältlich. Aktuelle Angebote finden Sie auf dem Azure Marketplace.

Eine weitere Option ist die Verwendung der Momentaufnahmefunktionen von Blob-Speichern. Hierfür müssen Sie den virtuellen Computer vor jedem Vorgang herunterfahren, für den eine Blob-Momentaufnahme benötigt wird. Dadurch werden ausstehende Schreibvorgänge gespeichert und das Dateisystem in einen konsistenten Zustand versetzt.

## Wie berechnet Azure die Gebühren für meinen virtuellen Computer?

Bei Azure wird ein Stundenpreis basierend auf Größe und Betriebssystem des virtuellen Computers berechnet. Angefangene Stunden werden nur nach den Minuten der Nutzung abgerechnet. Wenn Sie den virtuellen Computer mithilfe eines VM-Images mit einer bestimmten vorinstallierte Software erstellen, können zusätzliche Softwarekosten auf Stundenbasis anfallen. Azure berechnet die Gebühren für den Speicher für das Betriebssystem des virtuellen Computers und für die Datenträger getrennt. Temporärer Speicherplatz ist kostenlos.

Es fallen Gebühren an, wenn der Status des virtuellen Computers "Wird ausgeführt" oder "Angehalten" lautet. Beim Status "Beendet (Zuordnung aufgehoben)" werden keine Gebühren berechnet. Um einen virtuellen Computer in den Status "Beendet (Zuordnung aufgehoben)"zu versetzen, führen Sie einen der folgenden Schritte aus:

- Fahren Sie den virtuellen Computer über das klassische Azure-Portal herunter, oder löschen Sie ihn darüber.
- Verwenden Sie das Stop-AzureVM-Cmdlet, das im Azure PowerShell-Modul verfügbar ist.
- Verwenden Sie den Vorgang "Shutdown Role" in der Dienstverwaltungs-REST-API, und geben Sie "StoppedDeallocated" für das PostShutdownAction-Element an.

Ausführliche Informationen finden Sie unter [Virtuelle Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/).

## Startet Azure meinen virtuellen Computer zur Wartung neu?

Azure startet Ihren virtuellen Computer in einigen Fällen im Rahmen regelmäßiger, geplanter Wartungsupdates in den Azure-Rechenzentren neu.

Nicht geplante Wartungsereignisse können eintreten, wenn Azure ein schwerwiegendes Hardwareproblem erkennt, das Ihren virtuellen Computer betrifft. Bei ungeplanten Ereignissen migriert Azure den virtuellen Computer automatisch auf einen fehlerfreien Host und startet den virtuellen Computer neu.

Für alle eigenständigen virtuellen Computer (d. h. virtuelle Computer, die nicht Teil einer Verfügbarkeitsgruppe sind) benachrichtigt Azure den Dienstadministrator des Abonnements per E-Mail mindestens eine Woche vor der geplanten Wartung, da die virtuellen Computer während des Updates möglicherweise neu gestartet werden. Bei Anwendungen, die auf den virtuellen Computer ausgeführt werden, kann es zu Ausfällen kommen.

Sie können auch über das klassische Azure-Portal oder Azure PowerShell die Neustartprotokolle anzeigen, wenn der Neustart aufgrund einer geplanten Wartung durchgeführt wurde. Weitere Informationen finden Sie unter [Anzeigen von Neustartprotokollen für virtuelle Computer](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Um Redundanz bereitzustellen, platzieren Sie zwei oder mehr ähnlich konfigurierte virtuelle Computer in der gleichen Verfügbarkeitsgruppe. Dadurch wird sichergestellt, dass während geplanter oder ungeplanter Wartungen mindestens ein virtueller Computer verfügbar ist. Azure garantiert für diese Konfiguration bestimmte Verfügbarkeitsstufen für virtuelle Computer. Weitere Details finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](virtual-machines-manage-availability.md).

## Zusätzliche Ressourcen

[Über virtuelle Azure-Computer](virtual-machines-about.md)

[Verschiedene Möglichkeiten zum Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-choices-create-vm.md)

[Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=AcomDC_0128_2016-->