<properties
	pageTitle="Vorbereiten der Umgebung für die Sicherung virtueller Azure-Computer | Microsoft Azure"
	description="Bereiten Sie die Umgebung für die Sicherung virtueller Azure-Computer vor."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/19/2016"
	ms.author="trinadhk; jimpark; markgal;"/>

# Vorbereiten der Umgebung für die Sicherung virtueller Azure-Computer
Bevor Sie einen virtuellen Azure-Computer sichern, müssen Sie die folgenden Voraussetzungen erfüllen, um die Umgebung vorzubereiten. Wenn Sie dies bereits erledigt haben, können Sie mit dem [Sichern virtueller Azure-Computer](backup-azure-vms.md) beginnen. Andernfalls führen Sie die unten stehenden Schritte durch, um sicherzustellen, dass Ihre Umgebung vorbereitet ist.


## 1\. Sicherungstresor

![Sicherungstresor](./media/backup-azure-vms-prepare/step1.png)

Zum Sichern Ihrer virtuellen Azure-Computer müssen Sie zunächst einen Sicherungstresor erstellen. Bei einem Tresor handelt es sich um eine Entität, in der alle Sicherungen und Wiederherstellungspunkte gespeichert werden, die im Laufe der Zeit erstellt wurden. Der Tresor enthält auch die Sicherungsrichtlinien, die auf die zu sichernden virtuellen Computer angewendet werden.

In der Abbildung sind die Beziehungen zwischen den verschiedenen Azure Backup-Entitäten dargestellt: ![Azure Backup-Entitäten und ihre Beziehungen](./media/backup-azure-vms-prepare/vault-policy-vm.png)

So erstellen Sie einen Sicherungstresor

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com/) an.

2. Klicken Sie auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor** > **Schnellerfassung**. Wenn mit Ihrem Organisationskonto mehrere Abonnements verknüpft sind, wählen Sie das Abonnement aus, das mit dem Sicherungstresor verknüpft werden soll. In jedem Azure-Abonnement können Sie mehrere Sicherungstresore für die zu schützenden virtuellen Computer erstellen.

3. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird. Dieser muss für jedes Abonnement eindeutig sein.

4. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Der Tresor muss sich in der gleichen Region wie die zu schützenden virtuellen Computer befinden. Wenn Sie über virtuelle Computer in verschiedenen Regionen verfügen, erstellen Sie einen Tresor in jeder dieser Regionen. Es müssen keine Speicherkonten zum Speichern der Sicherungsdaten angegeben werden. Dies erfolgt automatisch über den Sicherungstresor und den Azure Backup-Dienst.

    ![Erstellen des Sicherungstresors](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. Klicken Sie auf **Tresor erstellen**. Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Unten im Portal können Sie anhand der Benachrichtigungen den Status prüfen.

    ![Popupbenachrichtigung zur Erstellung des Tresors](./media/backup-azure-vms-prepare/creating-vault.png)

6. In einer Meldung wird bestätigt, dass der Tresor erfolgreich erstellt wurde. Er wird auf der Seite **Recovery Services** als **Aktiv** aufgelistet. Stellen Sie nach der Erstellung des Tresors sicher, dass Sie eine geeignete Speicherredundanzoption auswählen. Weitere Informationen finden Sie unter [Festlegen der Speicherredundanzoption im Sicherungstresor](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Liste der Sicherungstresore](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. Klicken Sie auf den Sicherungstresor, um die Seite **Schnellstart** zu öffnen, auf der die Anweisungen für die Sicherung von virtuellen Azure-Computern angezeigt werden.

    ![Anweisungen zur Sicherung von virtuellen Computern auf der Dashboard-Seite](./media/backup-azure-vms-prepare/vmbackup-instructions.png)



## 2\. Netzwerkverbindung

![Netzwerkverbindung](./media/backup-azure-vms-prepare/step2.png)

Die Sicherungserweiterung benötigt eine Verbindung mit den öffentlichen Azure-IP-Adressen, um einwandfrei zu funktionieren, da sie Befehle an einen Azure Storage-Endpunkt (HTTP-URL) sendet, um die Momentaufnahmen des virtuellen Computers zu verwalten. Ohne die richtige Internetverbindung tritt bei diesen HTTP-Anfragen vom virtuellen Computer ein Timeout auf, und der Sicherungsvorgang funktioniert nicht.

### Netzwerkeinschränkungen mit NSGs

Wenn für Ihre Bereitstellung Zugriffsbeschränkungen gelten (z. B. durch eine Netzwerksicherheitsgruppe, NSG), müssen Sie weitere Schritte ausführen, um sicherzustellen, dass der Sicherungsdatenverkehr zum Sicherungstresor nicht beeinträchtigt wird.

Es gibt zwei Möglichkeiten, einen Pfad für den Sicherungsdatenverkehr bereitzustellen:

1. Aufnehmen der [IP-Bereiche des Azure-Datencenters](http://www.microsoft.com/de-DE/download/details.aspx?id=41653) in eine Positivliste
2. Bereitstellen eines HTTP-Proxys zum Weiterleiten des Datenverkehrs

Hierbei muss zwischen Verwaltbarkeit, Feinsteuerung und Kosten abgewogen werden.

|Option|Vorteile|Nachteile|
|------|----------|-------------|
|OPTION 1: Positivliste mit IP-Bereichen| Keine zusätzlichen Kosten.<br><br>Verwenden Sie zum Öffnen des Zugriffs in einer Netzwerksicherheitsgruppe das <i>Set-AzureNetworkSecurityRule</i>-Cmdlet. | Komplexe Verwaltung, da sich die betroffenen IP-Bereiche im Laufe der Zeit ändern.<br>Ermöglicht den Zugriff auf Azure insgesamt, nicht nur auf Storage.|
|OPTION 2: HTTP-Proxy| Feinsteuerung im Proxy über Speicher-URLs ist zulässig.<br>Zentraler Punkt für Internetzugriff auf virtuelle Computer.<br>Unterliegt keinen Azure-IP-Adressänderungen.| Zusätzliche Kosten für die Ausführung eines virtuellen Computers mit Proxysoftware.|

### Verwenden eines HTTP-Proxys für die Sicherung von virtuellen Computern
Beim Sichern eines virtuellen Computers werden die Befehle für die Snapshotverwaltung von der Sicherungserweiterung per HTTPS-API an Azure Storage gesendet. Dieser Datenverkehr muss von der Erweiterung über den Proxy geleitet werden, da nur der Proxy so konfiguriert wird, dass Zugriff auf das öffentliche Internet besteht.

>[AZURE.NOTE]Für die zu verwendende Proxysoftware gibt es keine Empfehlung. Stellen Sie sicher, dass Sie einen Proxy wählen, der mit den unten stehenden Konfigurationsschritten kompatibel ist.

Im folgenden Beispiel muss der virtuelle App-Computer so konfiguriert werden, dass der virtuelle Proxycomputer für den gesamten HTTP-Datenverkehr verwendet wird, der für das öffentliche Internet bestimmt ist. Der virtuelle Proxycomputer muss so konfiguriert werden, dass eingehender Datenverkehr von den virtuellen Computern im virtuellen Netzwerk zugelassen wird. Außerdem benötigt die Netzwerksicherheitsgruppe (mit dem Namen *NSG-lockdown*) eine neue Sicherheitsregel, mit der ausgehender Internetdatenverkehr vom virtuellen Proxycomputer zugelassen wird.

![NSG mit HTTP-Proxybereitstellung – Diagramm](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

**A) Zulassen ausgehender Netzwerkverbindungen:**

1. Führen Sie für Windows-Computer den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten aus:

	```
	netsh winhttp set proxy http://<proxy IP>:<proxy port>
	```

	Mit diesem Befehl wird eine computerweite Proxykonfiguration eingerichtet und für den gesamten ausgehenden HTTP/HTTPS-Datenverkehr verwendet.

2. Fügen Sie für Linux-Computer der Datei ```/etc/environment``` die folgende Zeile hinzu:

 	```
 	http_proxy=http://<proxy IP>:<proxy port>
 	```

	Fügen Sie der Datei ```/etc/waagent.conf``` die folgenden Zeilen hinzu:

	```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

**B) Zulassen von eingehenden Verbindungen auf dem Proxyserver:**

1. Öffnen Sie die Windows-Firewall auf dem Proxyserver. Klicken Sie mit der rechten Maustaste auf **Eingehende Regeln**, und klicken Sie dann auf **Neue Regel...**.

	![Firewall öffnen](./media/backup-azure-vms-prepare/firewall-01.png)

	![Neue Regel erstellen](./media/backup-azure-vms-prepare/firewall-02.png)
2. Wählen Sie im **Assistenten für neue eingehende Regel** für **Regeltyp** die Option **Benutzerdefiniert**, und klicken Sie auf **Weiter**. Wählen Sie auf der Seite zum Auswählen des **Programms** die Option **Alle Programme**, und klicken Sie auf **Weiter**.

3. Verwenden Sie auf der Seite **Protokoll und Ports** die Eingaben der unten stehenden Tabelle, und klicken Sie auf **Weiter**:

	![Neue Regel erstellen](./media/backup-azure-vms-prepare/firewall-03.png)

| Eingabefeld | Wert |
| --- | --- |
| Protokolltyp | TCP |
| Lokaler Port | Wählen Sie in der Dropdownliste den Eintrag **Bestimmte Ports** aus. Geben Sie den konfigurierten ```<Proxy Port>``` in das Textfeld ein. |
| Remoteport | Wählen Sie in der Dropdownliste den Eintrag **Alle Ports** aus. |

Klicken Sie sich im Assistenten bis zum Ende durch, und geben Sie dieser Regel einen Namen.

**C) Hinzufügen einer Ausnahmeregel zur NSG:**

Geben Sie an einer Azure PowerShell-Eingabeaufforderung den folgenden Befehl ein:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

Mit diesem Befehl wird der Netzwerksicherheitsgruppe eine Ausnahme hinzugefügt, mit der TCP-Datenverkehr von allen Ports unter 10.0.0.5 an alle Internetadressen über Port 80 (HTTP) oder 443 (HTTPS) zugelassen wird. Wenn Sie einen bestimmten Port im öffentlichen Internet benötigen, müssen Sie diesen auch zu ```-DestinationPortRange``` hinzufügen.

*Stellen Sie sicher, dass Sie die Namen im Beispiel durch die Details Ihrer Bereitstellung ersetzen.*

## 3\. VM-Agent

![VM-Agent](./media/backup-azure-vms-prepare/step3.png)

Bevor Sie einen virtuellen Azure-Computer sichern können, müssen Sie zunächst sicherstellen, dass der Azure VM-Agent auf dem virtuellen Computer ordnungsgemäß installiert ist. Da es sich beim VM-Agent zum Zeitpunkt der Erstellung des virtuellen Computers um eine optionale Komponente handelt, müssen Sie sicherstellen, dass das Kontrollkästchen für den VM-Agent aktiviert ist, bevor der virtuelle Computer bereitgestellt wird.

### Manuelle Installation und Aktualisierung

Der VM-Agent ist auf virtuellen Computern, die über den Azure-Katalog erstellt werden, bereits vorhanden. Auf virtuellen Computern, die aus lokalen Datencentern migriert werden, ist der VM-Agent jedoch nicht installiert. Für diese virtuellen Computer muss der VM-Agent explizit installiert werden. Erfahren Sie mehr über das [Installieren des VM-Agents auf einem vorhandenen virtuellen Computer](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Vorgang** | **Windows** | **Linux** |
| --- | --- | --- |
| Installieren des VM-Agents | <li>Laden Sie den [Agent-MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie ihn. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen. <li>[Aktualisieren Sie die VM-Eigenschaft](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), um anzugeben, dass der Agent installiert wurde. | <li>Installieren Sie den neuesten [Linux-Agent](https://github.com/Azure/WALinuxAgent) aus GitHub. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen. <li> [Aktualisieren Sie die VM-Eigenschaft](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), um anzugeben, dass der Agent installiert wurde. |
| Aktualisieren des VM-Agents | Das Aktualisieren des VM-Agents ist so einfach wie das Neuinstallieren der [Binärdateien für den VM-Agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Stellen Sie sicher, dass kein Sicherungsvorgang ausgeführt wird, während der VM-Agent aktualisiert wird. | Folgen Sie den Anweisungen unter [Aktualisieren des virtuellen Linux-Agents](../virtual-machines-linux-update-agent.md). <br><br>Stellen Sie sicher, dass kein Sicherungsvorgang ausgeführt wird, während der VM-Agent aktualisiert wird. |
| Überprüfen der VM-Agent-Installation | <li>Navigieren Sie auf dem virtuellen Azure-Computer zum Ordner *C:\\WindowsAzure\\Packages*. <li>Dieser Ordner enthält die Datei „WaAppAgent.exe“.<li> Klicken Sie mit der rechten Maustaste auf die Datei, wechseln Sie zu **Eigenschaften**, und wählen Sie dann die Registerkarte **Details** aus. Im Feld mit der Produktversion sollte 2.6.1198.718 oder eine höhere Version angegeben sein. | - |


Erfahren Sie mehr über den [VM-Agent](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) und [seine Installation](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Backup-Erweiterung

Um den virtuellen Computer zu sichern, wird mit dem Azure Backup-Dienst eine Erweiterung für den VM-Agent installiert. Der Azure Backup-Dienst installiert nahtlos und ohne zusätzlichen Benutzereingriff Upgrades und Patches für die Backup-Erweiterung.

Die Sicherungserweiterung wird installiert, wenn der virtuelle Computer (Virtual Machine, VM) ausgeführt wird. Ein ausgeführter virtueller Computer bietet zudem die größte Chance auf einen anwendungskonsistenten Wiederherstellungspunkt. Der Azure Backup-Dienst setzt die Sicherung des virtuellen Computers jedoch auch dann fort, wenn der virtuelle Computer ausgeschaltet wurde und die Erweiterung nicht installiert werden konnte (auch als „Offline-VM“ bezeichnet). In diesem Fall ist der Wiederherstellungspunkt *absturzkonsistent*, wie oben beschrieben.


## Einschränkungen

- Die Sicherung von virtuellen Computern auf Grundlage des Azure-Ressourcen-Managers (d. h. IaaS V2) wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mit mehr als 16 Datenträgern wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mithilfe von Storage Premium wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mit einer reservierten IP-Adresse und ohne definierten Endpunkt wird nicht unterstützt.
- Das Ersetzen eines vorhandenen virtuellen Computers während der Wiederherstellung wird nicht unterstützt. Löschen Sie zuerst den vorhandenen virtuellen Computer und alle zugeordneten Datenträger, und stellen Sie dann die Daten aus der Sicherung wieder her.
- Regionsübergreifende Sicherungs- und Wiederherstellungsvorgänge werden nicht unterstützt.
- Die Sicherung von virtuellen Computern mithilfe des Azure Backup-Diensts wird in allen öffentlichen Azure-Regionen unterstützt (Informationen hierzu finden Sie in der [Liste der Azure-Regionen](http://azure.microsoft.com/regions/#services)). Wenn die gewünschte Region derzeit nicht unterstützt wird, wird sie bei der Erstellung des Tresors in der Dropdownliste nicht angezeigt.
- Die Sicherung virtueller Computer mithilfe des Azure Backup-Diensts wird nur für bestimmte Betriebssystemversionen unterstützt:
  - **Linux**: Die [Liste der von Azure unterstützten Verteilungen](../virtual-machines/virtual-machines-linux-endorsed-distributions.md) finden Sie hier. Andere Bring-Your-Own-Linux-Verteilungen sollten ebenfalls funktionieren, sofern der VM-Agent auf dem virtuellen Computer verfügbar ist.
  - **Windows Server**: Versionen, die älter als Windows Server 2008 R2 sind, werden nicht unterstützt.
- Das Wiederherstellen eines virtuellen Domänencontrollercomputers, der Teil einer Konfiguration mit mehreren Domänencontrollern ist, wird nur über PowerShell unterstützt. Weitere Informationen hierzu finden Sie unter [Wiederherstellen von Multi-DC-Domänencontrollern](backup-azure-restore-vms.md#restoring-domain-controller-vms).
- Das Wiederherstellen virtueller Computer mit den folgenden besonderen Netzwerkkonfigurationen wird nur über PowerShell unterstützt. Virtuelle Computer, die mit dem Wiederherstellungsworkflow der Benutzeroberfläche erstellt werden, weisen diese Netzwerkkonfigurationen nach dem Abschluss des Wiederherstellungsvorgangs nicht auf. Weitere Informationen finden Sie unter [Wiederherstellen von VMs mit speziellen Netzwerkkonfigurationen](backup-azure-restore-vms.md#restoring-vms-with-special-netwrok-configurations).
	- Virtuelle Computer unter Load Balancer-Konfiguration (intern und extern)
	- Virtuelle Computer mit mehreren reservierten IP-Adressen
	- Virtuelle Computer mit mehreren Netzwerkadaptern

## Fragen?
Wenn Sie Fragen haben oder Anregungen zu gewünschten Funktionen mitteilen möchten, [senden Sie uns Ihr Feedback](http://aka.ms/azurebackup_feedback).

## Nächste Schritte

- [Planen der Sicherungsinfrastruktur für virtuelle Computer](backup-azure-vms-introduction.md)
- [Sichern virtueller Computer](backup-azure-vms.md)
- [Verwalten der Sicherung virtueller Computer](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0121_2016-->