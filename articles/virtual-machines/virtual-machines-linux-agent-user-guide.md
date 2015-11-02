<properties 
	pageTitle="Benutzerhandbuch für Linux-Agent | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Linux-Agent (waagent) zum Verwalten der Interaktion Ihres virtuellen Computers mit Azure Fabric Controller installieren und konfigurieren." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/30/2015" 
	ms.author="szark"/>



#Benutzerhandbuch für Azure Linux-Agent

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

##Einführung

Der Azure Linux-Agent (/usr/sbin/waagent) verwaltet die Interaktion eines virtuellen Computers mit dem Azure Fabric Controller. Er tut Folgendes:

* **Image-Bereitstellung**
  - Erstellen eines Benutzerkontos
  - Konfigurieren der SSH-Authentifizierungstypen
  - Bereitstellen von öffentlichen SSH-Schlüsseln und -Schlüsselpaaren
  - Legt den Hostnamen fest
  - Veröffentlicht den Hostnamen beim Plattform-DNS
  - Meldet den SSH-Hostschlüssel-Fingerabdruck an die Plattform
  - Verwaltet den Ressourcendatenträger
  - Formatiert und stellt den Ressourcendatenträger bereit
  - Konfiguriert den Auslagerungsbereich
* **Netzwerk**
  - Verwaltet von Routen zur Verbesserung der Kompatibilität mit Plattform-DHCP-Servern
  - Stellt die Stabilität des Netzwerkschnittstellennamens sicher
* **Kernel**
  - Konfiguriert ein virtuelles NUMA
  - Verbraucht Hyper-V-Entropie für /dev/random
  - Konfiguriert SCSI-Zeitüberschreitungen für das Stammgerät (das auch remote sein kann)
* **Diagnose**
  - Leitet die Konsole an den seriellen Port weiter
* **SCVMM-Bereitstellungen**
    - Erkennt und startet den VMM-Agenten für Linux bei Ausführung in einer Umgebung mit System Center Virtual Machine Manager 2012 R2
* **VM-Erweiterung**
    - Fügt von Microsoft und Partnern erstellte Komponenten in einen virtuellen Linux-Computer ein, um die Automatisierung der Software und der Konfiguration zu ermöglichen
    - Referenzimplementierung einer VM-Erweiterung unter [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


##Kommunikation

Der Informationsfluss von der Plattform zum Agenten erfolgt über zwei Kanäle:

* Über eine beim Start angeschlossene DVD für IaaS-Bereitstellungen. Diese DVD enthält eine OVF-kompatible Konfigurationsdatei mit allen Bereitstellungsinformationen außer den tatsächlichen SSH-Schlüsselpaaren.

* Über ein TCP-Endgerät, der eine REST-API verfügbar macht, um die Bereitstellungs- und Topologiekonfiguration abzurufen.

###Beziehen des Linux-Agenten
Sie können den neuesten Linux-Agent direkt von folgenden Quellen beziehen:

- [Von den verschiedenen Verteilungsanbietern, die Linux auf Azure unterstützen](http://support.microsoft.com/kb/2805216)
- oder aus dem [Github-Open-Source-Repository für den Azure Linux-Agent](https://github.com/Azure/WALinuxAgent)


## Anforderungen
Die folgenden Systeme funktionieren nachweislich mit dem Azure Linux-Agent. **Bitte beachten Sie, dass diese Liste von der offiziellen Liste der unterstützten Systemen auf der Microsoft Azure Platform abweichen kann.** Weitere Informationen finden Sie unter [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

###Unterstützte Linux-Verteilungen

* CoreOS
* CentOS 6.2+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+

Andere unterstützte Systeme:

* FreeBSD 9+ (Azure Linux-Agent v2.0.10+)


Der Linux-Agent erfordert zur ordnungsgemäßen Funktion bestimmte Systempakete:

* Python 2.6+
* Openssl 1.0+
* Openssh 5.3+
* Dienstprogramme für das Dateisystem: sfdisk, fdisk, mkfs, parted
* Kennworttools: chpasswd, sudo
* Textverarbeitungstools: sed, grep
* Netzwerktools: ip-route


##Installation

Für die Installation und Upgrades des Azure Linux-Agents sollte nach Möglichkeit ein RPM- oder DEB-Paket aus dem Paketrepository Ihrer Verteilung installiert werden.

Bei einer manuellen Installation kopieren Sie "waagent" in das Verzeichnis "/usr/sbin/waagent", und führen Sie den folgenden Befehl zur Installation aus:

	# sudo chmod 755 /usr/sbin/waagent
	# sudo /usr/sbin/waagent -install -verbose

Die Protokolldatei des Agenten wird in "/var/log/waagent.log" gespeichert.


##Befehlszeilenoptionen

###Kennzeichen

- verbose: Ausführlichkeit des angegebenen Befehls erhöhen
- force: Interaktive Bestätigung für einige Befehle überspringen

###Befehle

- help: Listet die unterstützten Befehle und Kennzeichen auf

- install: Manuelle Installation des Agenten
 * Überprüft das System auf erforderliche abhängige Objekte

 * Erstellt das SysV-Initialisierungsskript (/etc/init.d/waagent), die logrotate-Konfigurationsdatei (/etc/logrotate.d/waagent) und konfiguriert das Image für die Ausführung des Initialisierungsskripts beim Start

 * Schreibt eine Beispielkonfigurationsdatei in "/etc/waagent.conf"

 * Vorhandene Konfigurationsdateien werden in "/etc/waagent.conf.old" verschoben

 * Erkennt die Kernelversion und wendet bei Bedarf die VNUMA-Problemumgehung an

 * Verschiebt udev-Regeln, die einen Konflikt mit der Netzwerkfunktion (/lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) verursachen könnten, in "/var/lib/waagent/"

- uninstall: Entfernt waagent und zugehörige Dateien
 * Hebt die Registrierung des Initialisierungsskripts im System auf und löscht das Skript

 * Löscht die logrotate-Konfiguration und die waagent-Konfigurationsdatei in "/etc/waagent.conf"

 * Stellt alle während der Installation verschobenen udev-Regeln wieder her

 * Die automatische Wiederherstellung der VNUMA-Problemumgehung wird nicht unterstützt; bearbeiten Sie ggf. die GRUB-Konfigurationsdatei manuell, um NUMA erneut zu aktivieren.

- deprovision: Versucht, das System zu bereinigen und für eine erneute Bereitstellung vorzubereiten. Bei diesem Vorgang wird Folgendes gelöscht:
 * Alle SSH-Hostschlüssel (sofern Provisioning.RegenerateSshHostKeyPair in der Konfigurationsdatei auf "y" festgelegt ist)

 * Namenserverkonfiguration in "/etc/resolv.conf"

 * Stammkennwort aus "/etc/shadow" (sofern Provisioning.DeleteRootPassword in der Konfigurationsdatei auf "y" festgelegt ist)

 * Zwischengespeicherte DHCP-Clientleases

 * Setzt den Hostnamen auf "localhost.localdomain" zurück

 **Warnung:** Die Ausführung von "deprovision" garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden und dieses für eine erneute Bereitstellung genutzt werden kann.

- deprovision+user: Führt alle vorstehend für "-deprovision" aufgeführten Aktionen aus und löscht darüber hinaus das zuletzt bereitgestellte Benutzerkonto (aus "/var/lib/waagent" abgerufen) und zugehörige Daten. Dieser Parameter sollte verwendet werden, um die Bereitstellung eines Image aufzuheben, das zuvor für Bereitstellungen auf Azure verwendet wurde, sodass es erfasst und erneut verwendet werden kann.

- version: Zeigt die Version von waagent an

- serialconsole: Konfiguriert GRUB so, dass ttyS0 (der erste serielle Port) als Startkonsole markiert wird. Hierdurch wird sichergestellt, dass Kernelstartprotokolle an den seriellen Port gesendet und für das Debuggen verfügbar gemacht werden.

- daemon: Führt waagent als Daemon aus, um die Interaktion mit der Plattform zu verwalten. Dieses Argument wird waagent im waagent-Initialisierungsskript mitgeteilt.

##Konfiguration

Eine Konfigurationsdatei (/etc/waagent.conf) steuert die Aktionen von waagent. Nachfolgend sehen Sie ein Beispiel einer Konfigurationsdatei:
	
	#
	# Azure Linux Agent Configuration	
	#
	Role.StateConsumer=None 
	Role.ConfigurationConsumer=None 
	Role.TopologyConsumer=None
	Provisioning.Enabled=y
	Provisioning.DeleteRootPassword=n
	Provisioning.RegenerateSshHostKeyPair=y
	Provisioning.SshHostKeyPairType=rsa
	Provisioning.MonitorHostName=y
	ResourceDisk.Format=y
	ResourceDisk.Filesystem=ext4
	ResourceDisk.MountPoint=/mnt/resource 
	ResourceDisk.EnableSwap=n 
	ResourceDisk.SwapSizeMB=0
	LBProbeResponder=y
	Logs.Verbose=n
	OS.RootDeviceScsiTimeout=300
	OS.OpensslPath=None

Die verschiedenen Konfigurationsoptionen werden nachfolgend ausführlich erläutert. Konfigurationsoptionen weisen einen von drei Datentypen auf: Boolean, String oder Integer. Die Konfigurationsoptionen vom Typ "Boolesch" können als "j" oder "n" angegeben werden. Für einige Konfigurationseinträge des Typs "Zeichenfolge" kann das spezielle Schlüsselwort "Keine" verwendet werden, wie nachfolgend beschrieben.

**Role.StateConsumer:**

Typ: String Standardwert: Keiner

Sofern ein Pfad zu einer ausführbaren Programmdatei angegeben ist, wird diese aufgerufen, wenn waagent das Image bereitgestellt hat und in Kürze der Status "Bereit" an das Fabric gemeldet wird. An das Programm wird das Argument "Ready" übergeben. Der Agent wartet nicht auf die Rückkehr des Programms, sondern fährt direkt fort.

**Role.ConfigurationConsumer:**

Typ: String Standardwert: Keiner

Sofern ein Pfad zu einer ausführbaren Programmdatei angegeben ist, wird das Programm aufgerufen, wenn das Fabric anzeigt, dass eine Konfigurationsdatei für den virtuellen Computer verfügbar ist. Der Pfad zur XML-Konfigurationsdatei wird als Argument an die ausführbare Datei übergeben. Diese kann mehrmals aufgerufen werden, bei jeder Änderung der Konfigurationsdatei. Eine Beispieldatei steht im Anhang zur Verfügung. Der aktuelle Pfad dieser Datei lautet "/var/lib/waagent/HostingEnvironmentConfig.xml".

**Role.TopologyConsumer:**

Typ: String Standardwert: Keiner

Sofern ein Pfad zu einer ausführbaren Programmdatei angegeben ist, wird das Programm aufgerufen, wenn das Fabric anzeigt, dass ein neues Netzwerktopologie-Layout für den virtuellen Computer verfügbar ist. Der Pfad zur XML-Konfigurationsdatei wird als Argument an die ausführbare Datei übergeben. Diese kann mehrmals, bei jeder Änderung der Netzwerktopologie (z. B. aufgrund einer Dienstreparatur), aufgerufen werden. Eine Beispieldatei steht im Anhang zur Verfügung. Der aktuelle Speicherort dieser Datei lautet "/var/lib/waagent/SharedConfig.xml".

**Provisioning.Enabled:**

Typ: Boolean Standardwert: y

Hiermit kann der Benutzer die Bereitstellungsfunktion im Agenten aktivieren oder deaktivieren. Gültige Werte sind "y" und "n". Bei deaktivierter Bereitstellung werden die SSH-Host- und -Benutzerschlüssel im Image beibehalten, und alle in der Azure-Bereitstellungs-API angegebenen Konfigurationen werden ignoriert.

**Hinweis:** Dieser Parameter ist für Ubuntu-Cloud-Images, die Cloud-init für die Bereitstellung verwenden, standardmäßig auf „n“ gesetzt.

**Provisioning.DeleteRootPassword:**

Typ: Boolean Standardwert: n

Bei aktivierter Option wird das Stammkennwort in der Datei "/etc/shadow" bei der Bereitstellung gelöscht.

**Provisioning.RegenerateSshHostKeyPair:**

Typ: Boolean Standardwert: y

Bei aktivierter Option werden alle SSH-Hostschlüsselpaare (ecdsa, dsa und rsa) bei der Bereitstellung aus "/etc/ssh/" gelöscht. Außerdem wird ein einzelnes neues Schlüsselpaar generiert.

Der Verschlüsselungstyp für das neue Schlüsselpaar kann mit dem Provisioning.SshHostKeyPairType-Eintrag konfiguriert werden. Beachten Sie, dass einige Verteilungen SSH-Schlüsselpaare für alle fehlenden Verschlüsselungstypen neu erstellen, wenn der SSH-Daemon neu gestartet wird (z. B. bei einem Systemneustart).

**Provisioning.SshHostKeyPairType:**

Typ: String Standardwert: rsa

Diese Option kann auf einen Verschlüsselungsalgorithmustyp festgelegt werden, der vom SSH-Daemon auf dem virtuellen Computer unterstützt wird. Typischerweise werden die Werte "rsa", "dsa" und "ecdsa" unterstützt. Beachten Sie, dass "putty.exe" unter Windows keine Unterstützung für "ecdsa" bietet. Wenn Sie also beabsichtigen, mithilfe von "putty.exe" unter Windows eine Verbindung zu einer Linux-Bereitstellung herzustellen, verwenden Sie "rsa" oder "dsa".

**Provisioning.MonitorHostName:**

Typ: Boolean Standardwert: y

Bei aktivierter Option überwacht waagent den virtuellen Linux-Computer auf Änderungen des Hostnamens (wie vom Befehl "hostname" zurückgegeben) und aktualisiert die Netzwerkkonfiguration im Image automatisch entsprechend der Änderung. Um die Namensänderung per Push an die DNS-Server zu übertragen, wird die Netzwerkfunktion auf dem virtuellen Computer neu gestartet. Dadurch geht die Internetverbindung verloren.

**ResourceDisk.Format:**

Typ: Boolean Standardwert: y

Bei aktivierter Option wird der von der Plattform bereitgestellte Ressourcendatenträger von waagent formatiert und bereitgestellt, falls der Benutzer in "ResourceDisk.Filesystem" einen anderen Dateisystemtyp als "ntfs" anfordert. Auf dem Datenträger wird eine einzelne Linux-Partition (83) verfügbar gemacht. Diese Partition wird nicht formatiert, falls sie erfolgreich bereitgestellt werden kann.

**ResourceDisk.Filesystem:**

Typ: String Standardwert: ext4

Diese Option gibt den Dateisystemtyp für den Ressourcendatenträger an. Die unterstützten Werte sind je nach Linux-Distribution verschieden. Bei der Zeichenfolge X sollte mkfs.X im Linux-Image vorhanden sein. Für SLES 11-Images sollte normalerweise "ext3" verwendet werden. Für FreeBSD-Images sollte hier "ufs2" verwendet werden.

**ResourceDisk.MountPoint:**

Typ: String Standardwert: /mnt/resource

Diese Option gibt den Pfad an, in dem der Ressourcendatenträger bereitgestellt wird. Beachten Sie, dass der Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird.

**ResourceDisk.EnableSwap:**

Typ: Boolean Standardwert: n

Bei aktivierter Option wird eine Auslagerungsdatei (/swapfile) auf dem Ressourcendatenträger erstellt und dem Systemauslagerungsbereich hinzugefügt.

**ResourceDisk.SwapSizeMB:**

Typ: Integer Standardwert: 0

Die Größe der Auslagerungsdatei in Megabyte.

**LBProbeResponder:**

Typ: Boolean Standardwert: y

Bei aktivierter Option antwortet waagent auf Lastenausgleichstests der Plattform (sofern vorhanden).

**Logs.Verbose:**

Typ: Boolean Standardwert: n

Bei aktivierter Option wird ein ausführlicheres Protokoll erstellt. Waagent schreibt das Protokoll in "/var/log/waagent.log" und nutzt die logrotate-Funktion des Systems zu Rotieren von Protokollen.

**OS.RootDeviceScsiTimeout:**

Typ: Integer Standardwert: 300

Hiermit wird die SCSI-Zeitüberschreitung auf dem Betriebssystemdatenträger und Datenlaufwerken in Sekunden konfiguriert. Wird kein Wert festgelegt, gelten die Systemstandardwerte.

**OS.OpensslPath:**

Typ: String Standardwert: Keiner

Hiermit kann ein alternativer Pfad für die openssl-Binärdatei zur Verwendung für kryptografische Vorgänge angegeben werden.



##Ubuntu-Cloud-Images

Beachten Sie, dass Cloud Ubuntu-Images für zahlreiche Konfigurationsaufgaben, die andernfalls vom Azure Linux-Agent verwaltet würden, [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) verwenden. Beachten Sie die folgenden Unterschiede:


- **Provisioning.Enabled** ist für Ubuntu-Cloud-Images, die für Bereitstellungsaufgaben Cloud-Init verwenden, standardmäßig auf "n" gesetzt.

- Die folgenden Konfigurationsparameter wirken sich nicht auf Ubuntu-Cloud-Images aus, die Ressourcendatenträger und Auslagerungsbereiche mit Cloud-Init verwalten:

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Berücksichtigen Sie zum Konfigurieren des Bereitstellungspunkts und des Auslagerungsbereichs für den Ressourcendatenträger von Ubuntu-Cloud-Images die folgenden Ressourcen:

 - [Ubuntu-Wiki: Konfigurieren von Swap-Partitionen](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Einfügen benutzerdefinierter Daten in einen virtuellen Azure-Computer](virtual-machines-how-to-inject-custom-data.md)

 

<!---HONumber=Oct15_HO4-->