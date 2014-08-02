<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Linux Agent guide" pageTitle="Linux Agent User Guide for Azure" metaKeywords="" description="Learn how to install and configure Linux Agent (waagent) to manage your virtual machine's interaction with Azure Fabric Controller." metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure Linux Agent User Guide" authors="" solutions="" manager="" editor="" />

Benutzerhandbuch für Azure Linux-Agent
======================================

Einführung
----------

Der Azure Linux-Agent (waagent) verwaltet die Interaktion virtueller Computer mit dem Azure Fabric Controller. Er stellt die folgenden Funktionen für Linux-IaaS-Bereitstellungen zur Verfügung:

-   **Imagebereitstellung**
    -   Erstellen eines Benutzerkontos
    -   Konfigurieren von SSH-Authentifizierungstypen
    -   Bereitstellen von öffentlichen SSH-Schlüsseln und -Schlüsselpaaren
    -   Festlegen des Hostnamens
    -   Veröffentlichen des Hostnamens beim Plattform-DNS
    -   Mitteilen des SSH-Hostschlüssel-Fingerabdrucks an die Plattform
    -   Verwalten von Ressourcendatenträgern
    -   Formatieren und Bereitstellen des Ressourcendatenträgers
    -   Konfigurieren von Auslagerungsbereich
-   **Netzwerk**
    -   Verwalten von Routen zur Verbesserung der Kompatibilität mit Plattform-DHCP-Servern
    -   Sicherstellen der Stabilität des Netzwerkschnittstellennamens
-   **Kernel**
    -   Konfigurieren von virtuellem NUMA
    -   Verbrauchen von Hyper-V-Entropie für /dev/random
    -   Konfigurieren von SCSI-Zeitüberschreitungen für das Stammgerät (das auch remote sein kann)
-   **Diagnose**
    -   Konsolenumleitung an den seriellen Port
-   **SCVMM-Bereitstellungen**
    -   Erkennen und Starten des VMM-Agenten für Linux bei Ausführung in einer Umgebung mit System Center Virtual Machine Manager 2012 R2

Der Informationsfluss von der Plattform zum Agenten erfolgt über zwei Kanäle:

-   Über eine beim Start angeschlossene DVD für IaaS-Bereitstellungen. Diese DVD enthält eine OVF-kompatible Konfigurationsdatei mit allen Bereitstellungsinformationen außer den tatsächlichen SSH-Schlüsselpaaren.

-   Über einen TCP-Endpunkt, der eine REST-API verfügbar macht, um die Bereitstellungs- und Topologiekonfiguration abzurufen.

### Beziehen des Linux-Agent

Sie können den neuesten Linux-Agent direkt von folgenden Quellen beziehen:

-   [Von den verschiedenen Distributionsanbietern, die Linux auf Azure unterstützen](http://support.microsoft.com/kb/2805216)
-   oder aus dem [Github Open Source Repository für den Azure Linux-Agent](https://github.com/WindowsAzure/WALinuxAgent)

### Unterstützte Linux-Distributionen

-   CentOS 6.2+
-   Debian 7.0+
-   Ubuntu 12.04+
-   openSUSE 12.3+
-   SLES 11 SP2+
-   Oracle Linux 6.4+

Andere unterstützte Systeme:

-   FreeBSD 9+ (WALinuxAgent v2.0.0+)

### Anforderungen

Waagent erfordert zur ordnungsgemäßen Funktion bestimmte Systempakete:

-   Python 2.5+
-   Openssl 1.0+
-   Openssh 5.3+
-   Dateisystem-Dienstprogramme: sfdisk, fdisk, mkfs
-   Kennworttools: chpasswd, sudo
-   Textverarbeitungstools: sed, grep
-   Netzwerktools: ip-route

Installation
------------

Für Installation und Upgrades des Azure Linux-Agent sollte nach Möglichkeit ein RPM- oder DEB-Paket aus dem Paket-Repository der jeweiligen Distribution installiert werden.

Bei einer manuellen Installation kopieren Sie waagent in das Verzeichnis "/usr/sbin/waagent", und führen Sie den folgenden Befehl zur Installation aus:

    # sudo chmod 755 /usr/sbin/waagent
    # /usr/sbin/waagent -install -verbose

Die Protokolldatei des Agenten wird in "/var/log/waagent.log" gespeichert.

Befehlszeilenoptionen
---------------------

### Kennzeichen

-   verbose: Ausführlichkeit des angegebenen Befehls erhöhen
-   force: Interaktive Bestätigung für einige Befehle überspringen

### Befehle

-   help: Listet die unterstützten Befehle und Kennzeichen auf

-   install: Manuelle Installation des Agenten
	-   Überprüft das System auf erforderliche abhängige Objekte

	-   Erstellt das SysV-Initialisierungsskript (/etc/init.d/waagent), die logrotate-Konfigurationsdatei (/etc/logrotate.d/waagent) und konfiguriert das Image für die Ausführung des Initialisierungsskripts beim Start

	-   Schreibt eine Beispielkonfigurationsdatei in "/etc/waagent.conf"

	-   Vorhandene Konfigurationsdateien werden in "/etc/waagent.conf.old" verschoben

	-   Erkennt die Kernelversion und wendet bei Bedarf die VNUMA-Problemumgehung an

	-   Verschiebt udev-Regeln, die einen Konflikt mit der Netzwerkfunktion (/lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) verursachen könnten, in "/var/lib/waagent/"

-   uninstall: Entfernt waagent und zugehörige Dateien
	-   Hebt die Registrierung des Initialisierungsskripts im System auf und löscht das Skript

	-   Löscht die logrotate-Konfiguration und die waagent-Konfigurationsdatei in "/etc/waagent.conf"

	-   Stellt alle während der Installation verschobenen udev-Regeln wieder her

	-   Die automatische Wiederherstellung der VNUMA-Problemumgehung wird nicht unterstützt; bearbeiten Sie ggf. die GRUB-Konfigurationsdatei manuell, um NUMA erneut zu aktivieren.

-   deprovision: Versucht, das System zu bereinigen und für eine erneute Bereitstellung vorzubereiten. Bei diesem Vorgang wird Folgendes gelöscht:
	-   Alle SSH-Hostschlüssel (sofern Provisioning.RegenerateSshHostKeyPair in der Konfigurationsdatei auf "y" festgelegt ist)

	-   Namenserverkonfiguration in "/etc/resolv.conf"

	-   Stammkennwort aus "/etc/shadow" (sofern Provisioning.DeleteRootPassword in der Konfigurationsdatei auf "y" festgelegt ist)

	-   Zwischengespeicherte DHCP-Clientleases

	-   Setzt den Hostnamen auf "localhost.localdomain" zurück

**Warnung:** Die Ausführung von "deprovision" garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden und dieses für eine erneute Bereitstellung genutzt werden kann.

-   deprovision+user: Führt alle vorstehend für "-deprovision" aufgeführten Aktionen aus und löscht darüber hinaus das zuletzt bereitgestellte Benutzerkonto (aus "/var/lib/waagent" abgerufen) und zugehörige Daten. Dieser Parameter sollte verwendet werden, um die Bereitstellung eines Image aufzuheben, das zuvor für Bereitstellungen auf Azure verwendet wurde, sodass es erfasst und erneut verwendet werden kann.

-   version: Zeigt die Version von waagent an

-   serialconsole: Konfiguriert GRUB so, dass ttyS0 (der erste serielle Port) als Startkonsole markiert wird. Hierdurch wird sichergestellt, dass Kernelstartprotokolle an den seriellen Port gesendet und für das Debuggen verfügbar gemacht werden.

-   daemon: Führt waagent als Daemon aus, um die Interaktion mit der Plattform zu verwalten. Dieses Argument wird waagent im waagent-Initialisierungsskript mitgeteilt.

Konfiguration
-------------

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

Die verschiedenen Konfigurationsoptionen werden nachfolgend ausführlich erläutert. Konfigurationsoptionen weisen einen von drei Datentypen auf: Boolean, String oder Integer. Die Konfigurationsoptionen vom Typ "Boolean" können als "y" oder "n" angegeben werden. Für einige Konfigurationseinträge des Typs "String" kann das spezielle Schlüsselwort "None" verwendet werden, wie nachfolgend beschrieben.

**Role.StateConsumer:**

Typ: String
 Standardwert: None

Sofern ein Pfad zu einer ausführbaren Programmdatei angegeben ist, wird diese aufgerufen, wenn waagent das Image bereitgestellt hat und in Kürze der Status "Ready" (Bereit) an das Fabric gemeldet wird. An das Programm wird das Argument "Ready" übergeben. Der Agent wartet nicht auf die Rückkehr des Programms, sondern fährt direkt fort.

**Role.ConfigurationConsumer:**

Typ: String
 Standardwert: None

Sofern ein Pfad zu einer ausführbaren Programmdatei angegeben ist, wird das Programm aufgerufen, wenn das Fabric anzeigt, dass eine Konfigurationsdatei für den virtuellen Computer verfügbar ist. Der Pfad zur XML-Konfigurationsdatei wird als Argument an die ausführbare Datei übergeben. Diese kann mehrmals aufgerufen werden, bei jeder Änderung der Konfigurationsdatei. Eine Beispieldatei steht im Anhang zur Verfügung. Der aktuelle Pfad dieser Datei lautet "/var/lib/waagent/HostingEnvironmentConfig.xml".

**Role.TopologyConsumer:**

Typ: String
 Standardwert: None

Sofern ein Pfad zu einer ausführbaren Programmdatei angegeben ist, wird das Programm aufgerufen, wenn das Fabric anzeigt, dass ein neues Netzwerktopologie-Layout für den virtuellen Computer verfügbar ist. Der Pfad zur XML-Konfigurationsdatei wird als Argument an die ausführbare Datei übergeben. Diese kann mehrmals, bei jeder Änderung der Netzwerktopologie (z. B. aufgrund einer Dienstreparatur), aufgerufen werden. Eine Beispieldatei steht im Anhang zur Verfügung. Der aktuelle Speicherort dieser Datei lautet "/var/lib/waagent/SharedConfig.xml".

**Provisioning.Enabled:**

Typ: Boolean
 Standardwert: y

Hiermit kann der Benutzer die Bereitstellungsfunktion im Agenten aktivieren oder deaktivieren. Gültige Werte sind "y" und "n". Bei deaktivierter Bereitstellung werden die SSH-Host- und -Benutzerschlüssel im Image beibehalten, und alle in der Azure-Bereitstellungs-API angegebenen Konfigurationen werden ignoriert.

**Provisioning.DeleteRootPassword:**

Typ: Boolean
 Standardwert: n

Bei aktivierter Option wird das Stammkennwort in der /etc/shadow-Datei bei der Bereitstellung gelöscht.

**Provisioning.RegenerateSshHostKeyPair:**

Typ: Boolean
 Standardwert: y

Bei aktivierter Option werden alle SSH-Hostschlüsselpaare (ecdsa, dsa und rsa) bei der Bereitstellung aus "/etc/ssh/" gelöscht. Außerdem wird ein einzelnes neues Schlüsselpaar generiert.

Der Verschlüsselungstyp für das neue Schlüsselpaar kann mit dem Provisioning.SshHostKeyPairType-Eintrag konfiguriert werden. Beachten Sie, dass einige Distributionen SSH-Schlüsselpaare für alle fehlenden Verschlüsselungstypen neu erstellen, wenn der SSH-Daemon neu gestartet wird (z. B. bei einem Systemneustart).

**Provisioning.SshHostKeyPairType:**

Typ: String
 Standardwert: rsa

Diese Option kann auf einen Verschlüsselungsalgorithmustyp festgelegt werden, der vom SSH-Daemon auf dem virtuellen Computer unterstützt wird. Typischerweise werden die Werte "rsa", "dsa" und "ecdsa" unterstützt. Beachten Sie, dass "putty.exe" unter Windows keine Unterstützung für "ecdsa" bietet. Wenn Sie also beabsichtigen, mithilfe von "putty.exe" unter Windows eine Verbindung zu einer Linux-Bereitstellung herzustellen, verwenden Sie "rsa" oder "dsa".

**Provisioning.MonitorHostName:**

Typ: Boolean
 Standardwert: y

Bei aktivierter Option überwacht waagent den virtuellen Linux-Computer auf Änderungen des Hostnamens (wie vom Befehl "hostname" zurückgegeben) und aktualisiert die Netzwerkkonfiguration im Image automatisch entsprechend der Änderung. Um die Namensänderung per Push an die DNS-Server zu übertragen, wird die Netzwerkfunktion auf dem virtuellen Computer neu gestartet. Dadurch geht die Internetverbindung verloren.

**ResourceDisk.Format:**

Typ: Boolean
 Standardwert: y

Bei aktivierter Option wird der von der Plattform bereitgestellte Ressourcendatenträger von waagent formatiert und bereitgestellt, falls der Benutzer in "ResourceDisk.Filesystem" einen anderen Dateisystemtyp als "ntfs" anfordert. Auf dem Datenträger wird eine einzelne Linux-Partition (83) verfügbar gemacht. Diese Partition wird nicht formatiert, falls sie erfolgreich bereitgestellt werden kann.

**ResourceDisk.Filesystem:**

Typ: String
 Standardwert: ext4

Diese Option gibt den Dateisystemtyp für den Ressourcendatenträger an. Die unterstützten Werte sind je nach Linux-Distribution verschieden. Bei der Zeichenfolge X sollte mkfs.X im Linux-Image vorhanden sein. Für SLES 11-Images sollte normalerweise "ext3" verwendet werden. Für FreeBSD-Images sollte hier "ufs2" verwendet werden.

**ResourceDisk.MountPoint:**

Typ: String
 Standardwert: /mnt/resource

Diese Option gibt den Pfad an, in dem der Ressourcendatenträger bereitgestellt wird.

**ResourceDisk.EnableSwap:**

Typ: Boolean
 Standardwert: n

Bei aktivierter Option wird eine Auslagerungsdatei (/swapfile) auf dem Ressourcendatenträger erstellt und dem Systemauslagerungsbereich hinzugefügt.

**ResourceDisk.SwapSizeMB:**

Typ: Integer
 Standardwert: 0

Die Größe der Auslagerungsdatei in Megabyte.

**LBProbeResponder:**

Typ: Boolean
 Standardwert: y

Bei aktivierter Option antwortet waagent auf Lastenausgleichstests der Plattform (sofern vorhanden).

**Logs.Verbose:**

Typ: Boolean
 Standardwert: n

Bei aktivierter Option wird ein ausführlicheres Protokoll erstellt. Waagent schreibt das Protokoll in "/var/log/waagent.log" und nutzt die logrotate-Funktion des Systems zu Rotieren von Protokollen.

**OS.RootDeviceScsiTimeout:**

Typ: Integer
 Standardwert: 300

Hiermit wird die SCSI-Zeitüberschreitung auf dem Betriebssystemdatenträger und Datenlaufwerken in Sekunden konfiguriert. Wird kein Wert festgelegt, gelten die Systemstandardwerte.

**OS.OpensslPath:**

Typ: String
 Standardwert: None

Hiermit kann ein alternativer Pfad für die openssl-Binärdatei zur Verwendung für kryptografische Vorgänge angegeben werden.

Anhang
------

### Beispiel einer Rollenkonfigurationsdatei

    <
      xml version="1.0" encoding="utf-8"
      >
    <HostingEnvironmentConfig version="1.0.0.0" goalStateIncarnation="1">
      <StoredCertificates>
        <StoredCertificate name="Stored0Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" certificateId="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" storeName="My" configurationLevel="System" />
      </StoredCertificates>
      <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
        <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
        <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
      </Deployment>
      <Incarnation number="1" instance="LinuxVM_IN_2" guid="{5c87ab8b-2f6a-4758-9f74-37e68c3e957b}" />
      <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" hostingEnvironmentVersion="1" software="" softwareType="ApplicationPackage" entryPoint="" parameters="" settleTimeSeconds="10" />
      <HostingEnvironmentSettings name="full" Runtime="rd_fabric_stable.111026-1712.RuntimePackage_1.0.0.9.zip">
        <CAS mode="full" />
        <PrivilegeLevel mode="max" />
        <AdditionalProperties><CgiHandlers></CgiHandlers></AdditionalProperties></HostingEnvironmentSettings>
        <ApplicationSettings>
          <Setting name="__ModelData" value="<m role="LinuxVM" xmlns="urn:azure:m:v1"><r name="LinuxVM"><e name="HTTP" /><e name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" /><e name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput" /><e name="SSH" /></r></m>" />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="..." />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2015-11-06T23:59:59.0000000-08:00" />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="rdos" />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
          <Setting name="startpage" value="Hello World!" />
          <Setting name="Certificate|Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" value="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" />
        </ApplicationSettings>
        <ResourceReferences>
          <Resource name="DiagnosticStore" type="directory" request="Microsoft.Cis.Fabric.Controller.Descriptions.ServiceDescription.Data.Policy" sticky="true" size="1" path="a99549a92e38498f98cf2989330cd2f1.LinuxVM.DiagnosticStore<?xml version="1.0" encoding="utf-8"?>
    <HostingEnvironmentConfig version="1.0.0.0" goalStateIncarnation="1">
      <StoredCertificates>
        <StoredCertificate name="Stored0Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" certificateId="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" storeName="My" configurationLevel="System" />
      </StoredCertificates>
      <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
        <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
        <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
      </Deployment>
      <Incarnation number="1" instance="LinuxVM_IN_2" guid="{5c87ab8b-2f6a-4758-9f74-37e68c3e957b}" />
      <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" hostingEnvironmentVersion="1" software="" softwareType="ApplicationPackage" entryPoint="" parameters="" settleTimeSeconds="10" />
      <HostingEnvironmentSettings name="full" Runtime="rd_fabric_stable.111026-1712.RuntimePackage_1.0.0.9.zip">
        <CAS mode="full" />
        <PrivilegeLevel mode="max" />
        <AdditionalProperties><CgiHandlers></CgiHandlers></AdditionalProperties></HostingEnvironmentSettings>
        <ApplicationSettings>
          <Setting name="__ModelData" value="<m role="LinuxVM" xmlns="urn:azure:m:v1"><r name="LinuxVM"><e name="HTTP" /><e name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" /><e name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput" /><e name="SSH" /></r></m>" />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="..." />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2015-11-06T23:59:59.0000000-08:00" />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="rdos" />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
          <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
          <Setting name="startpage" value="Hello World!" />
          <Setting name="Certificate|Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" value="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" />
        </ApplicationSettings>
        <ResourceReferences>
          <Resource name="DiagnosticStore" type="directory" request="Microsoft.Cis.Fabric.Controller.Descriptions.ServiceDescription.Data.Policy" sticky="true" size="1" path="a99549a92e38498f98cf2989330cd2f1.LinuxVM.DiagnosticStore\" disableQuota="false" />
        </ResourceReferences>
      </HostingEnvironmentConfig>
    quot; disableQuota="false" />
        </ResourceReferences>
      </HostingEnvironmentConfig>

### Beispiel einer Rollentopologiedatei

    <?xml version="1.0" encoding="utf-8"?>
    <SharedConfig version="1.0.0.0" goalStateIncarnation="2">
      <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
        <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
        <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
      </Deployment>
      <Incarnation number="1" instance="LinuxVM_IN_1" guid="{a7b94774-db5c-4007-8707-0b9e91fd808d}" />
      <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" settleTimeSeconds="10" />
      <LoadBalancerSettings timeoutSeconds="32" waitLoadBalancerProbeCount="8">
        <Probes>
          <Probe name="LinuxVM" />
          <Probe name="03F7F19398C4358108B7ED059966EEBD" />
          <Probe name="47194D0E3AB3FCAD621CAAF698EC82D8" />
        </Probes>
      </LoadBalancerSettings>
      <OutputEndpoints>
        <Endpoint name="LinuxVM:Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" type="SFS">
          <Target instance="LinuxVM_IN_0" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
          <Target instance="LinuxVM_IN_1" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
          <Target instance="LinuxVM_IN_2" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
        </Endpoint>
      </OutputEndpoints>
      <Instances>
        <Instance id="LinuxVM_IN_1" address="10.115.38.202">
          <FaultDomains randomId="1" updateId="1" updateCount="2" />
          <InputEndpoints>
            <Endpoint name="HTTP" address="10.115.38.202:80" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:80" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
              <LocalPorts>
                <LocalPortRange from="80" to="80" />
              </LocalPorts>
            </Endpoint>
            <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.38.202:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
              <LocalPorts>
                <LocalPortRange from="3389" to="3389" />
              </LocalPorts>
              <RemoteInstances>
                <RemoteInstance instance="LinuxVM_IN_0" />
                <RemoteInstance instance="LinuxVM_IN_2" />
              </RemoteInstances>
            </Endpoint>
            <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput" address="10.115.38.202:20000" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:3389" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
              <LocalPorts>
                <LocalPortRange from="20000" to="20000" />
              </LocalPorts>
            </Endpoint>
            <Endpoint name="SSH" address="10.115.38.202:22" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:22" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
              <LocalPorts>
                <LocalPortRange from="22" to="22" />
              </LocalPorts>
            </Endpoint>
          </InputEndpoints>
        </Instance>
        <Instance id="LinuxVM_IN_0" address="10.115.58.82">
          <FaultDomains randomId="0" updateId="0" updateCount="2" />
          <InputEndpoints>
            <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.82:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
              <LocalPorts>
                <LocalPortRange from="3389" to="3389" />
              </LocalPorts>
            </Endpoint>
          </InputEndpoints>
        </Instance>
        <Instance id="LinuxVM_IN_2" address="10.115.58.148">
          <FaultDomains randomId="0" updateId="2" updateCount="2" />
          <InputEndpoints>
            <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.148:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
              <LocalPorts>
                <LocalPortRange from="3389" to="3389" />
              </LocalPorts>
            </Endpoint>
          </InputEndpoints>
        </Instance>
      </Instances>
    </SharedConfig>
