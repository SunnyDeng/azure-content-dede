<properties title="Using load-balanced sets to clusterize MySQL on Linux" pageTitle="Using load-balanced sets to clusterize MySQL on Linux" description="An article that illustrates patterns to setup a load-balanced, high availability Linux cluster on Azure using MySQL as an example" metaKeywords="mysql, linux, cluster, azure, ha, high availability, corosync, pacemaker, drbd, heartbeat" services="virtual-machines" solutions="" documentationCenter="" authors="jparrel" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jparrel"></tags>

# Verwenden von Gruppen mit Lastenausgleich zum Erstellen von MySQL-Clustern unter Linux

-   [Vorbereitung][Vorbereitung]
-   [Einrichten des Clusters][Einrichten des Clusters]
-   [Einrichten von MySQL][Einrichten von MySQL]
-   [Einrichten von Corosync][Einrichten von Corosync]
-   [Einrichten von Pacemaker][Einrichten von Pacemaker]
-   [Tests][Tests]
-   [STONITH][STONITH]
-   [Einschränkungen][Einschränkungen]

## Einführung

Zweck dieses Artikels ist die Untersuchung und Veranschaulichung der verschiedenen möglichen Ansätze zum Bereitstellen hoch verfügbarer Linux-basierter Dienste in Microsoft Azure, wobei insbesondere die Hochverfügbarkeit von MySQL Server im Vordergrund steht. Ein Video zur Veranschaulichung dieses Ansatzes finden Sie auf [Channel 9][Channel 9].

Wir stellen eine MySQL-Hochverfügbarkeitslösung mit Einzelmaster und zwei Knoten ohne gemeinsame Nutzung basierend auf DRBD, Corosync und Pacemaker vor. MySQL wird stets immer nur auf einem Knoten ausgeführt. Lese- und Schreibvorgänge der DRBD-Ressource sind ebenfalls auf einen Knoten gleichzeitig beschränkt.

Eine Lösung mit virtueller IP-Adresse (VIP) wie LVS (Linux Virtual Server) ist nicht erforderlich, da wir die Gruppe mit Lastenausgleich von Microsoft Azure verwenden, um sowohl die Round-Robin-Funktionalität und Endpunkterkennung, das Entfernen und ordnungsgemäße Wiederherstellen der VIP zu ermöglichen. Die VIP ist eine global routingfähige IPv4-Adresse, die von Microsoft Azure zugewiesen wurde, als wir den Clouddienst zuerst erstellt haben.

Es gibt andere mögliche Architekturen für MySQL einschließlich NBD Cluster, Percona und Galera sowie mehrere Middlewarelösungen, von denen mindestens eine als virtueller Computer im [VM Depot][VM Depot] zur Verfügung steht. Solange diese Lösungen im Unicast- anstatt im Multicast- oder Broadcastmodus replizieren können und keinen gemeinsam genutzten Speicher oder mehrere Netzwerkschnittstellen benötigen, sollten diese Szenarien einfach in Microsoft Azure bereitzustellen sein.

Diese Clusterarchitekturen können auch auf ähnliche Weise auf andere Produkte wie PostgreSQL und OpenLDAP ausgedehnt werden. Dieses Lastenausgleichsverfahren ohne jegliche gemeinsame Nutzung wurde beispielsweise erfolgreich mit Multi-Master OpenLDAP getestet, was Sie sich in unserem Channel 9-Blog ansehen können.

## Vorbereitung

Sie benötigen ein Microsoft Azure-Konto mit gültigem Abonnement mit der Möglichkeit zum Erstellen von mindestens zwei virtuellen Computern (XS wurde bei diesem Beispiel verwendet), ein Netzwerk und ein Subnetz, eine Affinitätsgruppe und eine Verfügbarkeitsgruppe sowie die Möglichkeit, neue VHDs (Virtual Hard Disks, virtuelle Festplatten) in der Region des Clouddiensts zu erstellen und diese an virtuelle Linux-Computer anzufügen.

### Getestete Umgebung

-   Ubuntu 13.10
-   DRBD
-   MySQL Server
-   Corosync und Pacemaker

### Affinitätsgruppe

Sie erstellen eine Affinitätsgruppe für die Lösung, indem Sie sich beim Azure-Portal anmelden, zu **Einstellungen** wechseln und eine neue Affinitätsgruppe erstellen. Später zugewiesene Ressourcen werden dieser Affinitätsgruppe zugeordnet.

### Netzwerke

Ein neues Netzwerk wird erstellt, in dem ein Subnetz eingerichtet wird. Wir haben ein 10.10.10.0/24-Netzwerk mit nur einem enthaltenen /24-Subnetz gewählt.

### Virtuelle Computer

Der erste virtuelle Ubuntu 13.10-Computer wird mithilfe eines Endorsed Ubuntu Gallery-Images erstellt und `hadb01` genannt. Ein neuer Clouddienst mit dem Namen **hadb** wird erstellt. Wir nennen ihn so, um die gemeinsame Nutzung und den Lastenausgleich zu veranschaulichen, durch die/den sich der Dienst auszeichnet, wenn wir weitere Ressourcen hinzufügen. Der Erstellung von `hadb01` erfolgt ohne Zwischenfälle und wird im Portal abgeschlossen. Ein Endpunkt für SSH wird automatisch erstellt, und unser erstelltes Netzwerk wird ausgewählt. Wir haben uns auch für die Erstellung einer neuen Verfügbarkeitsgruppe für die virtuellen Computer entschieden.

Nachdem der erste virtuelle Computer erstellt wurden (technisch gesehen bei Erstellung des Clouddiensts), fahren wir mit der Erstellung des zweiten virtuellen Computers, `hadb02`, fort. Für den zweiten virtuellen Computer verwenden wir auch einen virtuellen Ubuntu 13.10-Computer aus der Galerie mithilfe des Portals, entscheiden uns aber für den vorhandenen Clouddienst`hadb.cloudapp.net`, anstatt einen neuen zu erstellen. Netzwerk und Verfügbarkeitsgruppe werden automatisch für uns ausgewählt. Auch ein SSH-Endpunkt wird erstellt.

Nachdem beide virtuellen Computer erstellt wurden, notieren wir den SSH-Port für `hadb01` (TCP 22) und `hadb02` (von Azure automatisch zugewiesen).

### Angefügter Speicher

Wir fügen an beide virtuellen Computer einen neuen Datenträger an und erstellen dabei neue 5-GB-Datenträger. Die Datenträger werden im VHD-Container gehostet, der für die Systemdatenträger unseres Betriebssystems verwendet wird. Nachdem Datenträger erstellt und angefügt wurden, ist es nicht erforderlich, Linux neu zu starten, da der Kernel das neue Gerät erkennt (im Allgemeinen `/dev/sdc`. Sie können `dmesg` auf die Ausgabe überprüfen).

Auf jedem virtuellen Computer fahren wir mit dem Erstellen einer neuen Partition mithilfe von `cfdisk` (primäre Linux-Partition) fort und erstellen die neue Partitionstabelle. **Legen Sie auf dieser Partition kein Dateisystem an**.

## Einrichten des Clusters

Auf beiden virtuellen Ubuntu-Computern müssen wir APT zum Installieren von Corosync, Pacemaker und DRBD verwenden. Dazu verwenden wir `apt-get`:

    sudo apt-get install corosync pacemaker drbd8utils.

**Installieren Sie zu diesem Zeitpunkt nicht MySQL**. Debian- und Ubuntu-Installationsskripts initialisieren ein MySQL-Datenverzeichnis für `/var/lib/mysql`. Doch da das Verzeichnis durch ein DRBD-Dateisystem ersetzt wird, müssen wir dies später tun.

An dieser Stelle müssen wir auch (mithilfe von `/sbin/ifconfig`) überprüfen, ob beide virtuellen Computer Adressen im Subnetz 10.10.10.0/24 verwenden und sich gegenseitig nach Name pingen können. Nach Wunsch können Sie auch `ssh-keygen` und `ssh-copy-id` nutzen, um sicherzustellen, dass beide virtuellen Computer über SSH kommunizieren können, ohne dass ein Kennwort erforderlich ist.

### Einrichten von DRBD

Wir erstellen eine DRBD-Ressource, die die zugrunde liegende `/dev/sdc1`-Partition zum Erstellen einer `/dev/drbd1`-Ressource verwendet, die mithilfe von ext3 formatiert und auf sowohl primären als auch sekundären Knoten verwendet werden kann. Öffnen Sie hierzu `/etc/drbd.d/r0.res`, und kopieren Sie die folgende Ressourcendefinition. Tun Sie dies auf beiden virtuellen Computern:

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Initialisieren Sie anschließend die Ressource mithilfe von `drbdadm` auf beiden virtuellen Computern:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

Und erzwingen Sie schließlich auf dem primären Knoten (`hadb01`) den (primären) Besitz der DRBD-Ressource:

    sudo drbdadm primary --force r0

Wenn Sie den Inhalt von "/proc/drbd" (`sudo cat /proc/drbd`) auf beiden virtuellen Computern untersuchen, sollten Sie `Primary/Secondary` auf `hadb01` und `Secondary/Primary` auf `hadb02` erkennen, was an dieser Stelle mit der Lösung übereinstimmt. Der 5-GB-Datenträger wird für Kunden kostenlos über das 10.10.10.0/24-Netzwerk synchronisiert.

Nachdem der Datenträger synchronisiert wurde, können Sie auf `hadb01` das Dateisystem erstellen. Zu Testzwecken haben wir ext2 verwendet, doch bei Befolgen der folgenden Anweisung wird ein ext3-Dateisystem erstellt:

    mkfs.ext3 /dev/drbd1

### Bereitstellen der DRBD-Ressource

Auf `hadb01` können wir nun die DRBD-Ressourcen bereitstellen. Debian und Derivative nutzen `/var/lib/mysql` als MySQL-Datenverzeichnis. Da wir MySQL nicht installiert haben, erstellen wir das Verzeichnis und stellen die DRBD-Ressource bereit. Auf `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## Einrichten von MySQL

Nun können Sie MySQL auf `hadb01` installieren:

    sudo apt-get install mysql-server

Für `hadb02` gibt es zwei Optionen. Sie können "mysql-server" jetzt installieren, wodurch "/var/lib/mysql" erstellt wird, es mit einem neuen Datenverzeichnis füllen und dann mit dem Entfernen des Inhalts fortfahren. Auf `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

Die zweite Option ist ein Failover auf `hadb02` und das anschließende Installieren von "mysql-server" dort (Installationsskripts erkennen die vorhandene Installation und lassen sie unverändert).

Auf `hadb01`:

    sudo drbdadm secondary –force r0

Auf `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Wenn derzeit für DRBD keine Failover geplant ist, ist die erste Option einfacher, aber weniger elegant. Nach diesen Einrichtungsschritten können Sie die Arbeit mit Ihrer MySQL-Datenbank aufnehmen. Auf `hadb02` (bzw. auf dem gemäß DRBD aktiven Server):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**Warnung**: Durch diese letzte Anweisung wird die Authentifizierung für den Benutzer "root" in dieser Tabelle erfolgreich deaktiviert. Dies muss durch die GRANT-Anweisungen für Ihre Produktionsumgebung ersetzt werden und dient hier nur zur Veranschaulichung.

Sie müssen auch den Netzwerkbetrieb für MySQL aktivieren, wenn Sie Abfragen außerhalb der virtuellen Computer ausführen möchten, was der Zweck dieser Anleitung ist. Öffnen Sie auf beiden virtuellen Computern `/etc/mysql/my.cnf`, und navigieren Sie zu `bind-address`, wobei 127.0.0.1 in 0.0.0.0 geändert wird. Führen Sie nach Speichern der Datei den Befehl `sudo service mysql restart` auf Ihrem derzeitigen primären Knoten aus.

### Erstellen der MySQL-Gruppe mit Lastenausgleich

Wir begeben uns zurück zum Azure-Portal, und navigieren zum virtuellen Computer `hadb01` und dann zu "Endpunkte". Wir erstellen einen neuen Endpunkt, wählen in der Dropdownliste "MySQL (TCP 3306)" aus, und setzen ein Häkchen in das Feld *Neue Gruppe mit Lastenausgleich erstellen*. Wir nennen unseren Lastenausgleichsendpunkt `lb-mysql`. Wir lassen die meisten Optionen außer "Zeit" unverändert. Diesen Wert verringern wir in 5 (Sekunden, Mindestwert).

Nach dem Erstellen des Endpunkts wechseln wir zu `hadb02`, dann zu "Endpunkte" und erstellen einen neuen Endpunkt. Aber wir wählen `lb-mysql` und dann "MySQL" in der Dropdownliste. Für diesen Schritt können Sie auch die Befehlszeilen-Schnittstelle von Azure nutzen.

Zu diesem Zeitpunkt verfügen wir über alles, was wir für einen manuellen Betrieb des Clusters benötigen.

### Testen der Gruppe mit Lastenausgleich

Tests können mithilfe eines externen Computers, eines beliebigen MySQL-Clients und von Anwendungen (z. B. phpMyAdmin ausgeführt als Azure-Website) erfolgen. In diesem Fall haben wir das MySQL-Befehlszeilentool auf einem anderen Linux-Computer verwendet:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### Manuelles Failover

Sie können Failover simulieren, indem Sie MySQL herunterfahren, den DRBD-Primärknoten wechseln und MySQL neu starten.

Zunächst auf hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Dann auf hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Nach dem manuellen Failover können Sie Ihre Remoteabfrage wiederholen, die ordnungsgemäß funktionieren sollte.

## Einrichten von Corosync

Corosync ist die zugrunde liegende Clusterinfrastruktur, die erforderlich ist, damit Pacemaker funktioniert. Für Benutzer von Heartbeat, Version 1 und 2, (und anderen Methoden wie Ultramonkey) bietet Corosync eine Aufteilung der CRM-Funktionen, während die Funktionalität von Pacemaker mehr der von Heartbeat ähnelt.

Die Haupteinschränkung für Corosync in Azure ist, dass Corosync Multicast- gegenüber Broadcast- oder Unicast-Kommunikationen bevorzugt, das Microsoft Azure-Netzwerk jedoch nur Unicast unterstützt.

Glücklicherweise bietet Corosync einen funktionierenden Unicast-Modus. Die einzige Einschränkung ist, da alle Knoten nicht *automatisch* miteinander kommunizieren, dass Sie die Knoten in Ihren Konfigurationsdateien einschließlich ihrer IP-Adressen definieren müssen. Wir können die Corosync-Beispieldateien für Unicast verwenden, wobei wir lediglich die Bindungsadresse, Knotenlisten und das Protokollierungsverzeichnis ändern (Ubuntu verwendet `/var/log/corosync`, während in den Beispieldateien `/var/log/cluster` verwendet wird) und Quorumtools aktivieren.

**Beachten Sie die nachstehende `transport: udpu`-Anweisung und die manuell definierten IP-Adressen für die Knoten**.

Auf `/etc/corosync/corosync.conf` für beide Knoten:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Wir kopieren diese Konfigurationsdatei auf beide virtuellen Computer und starten Corosync auf beiden Knoten:

    sudo service start corosync

Kurz nach Start des Diensts sollte das Cluster im aktuellen Ring eingerichtet und das Quorum gebildet worden sein. Wir können diese Funktionalität durch Überprüfen von Protokollen oder mit diesem Befehl prüfen:

    sudo corosync-quorumtool –l

Eine der nachstehenden Abbildung ähnliche Ausgabe sollte folgen:

![corosync-quorumtool -l sample output][corosync-quorumtool -l sample output]

## Einrichten von Pacemaker

Pacemaker verwendet den Cluster zum Überwachen von Ressourcen, Bestimmen des Zeitpunkts des Herunterfahrens von Primärknoten und Umschalten dieser Ressourcen auf Sekundärknoten. Ressourcen können u. a. mithilfe einer Gruppe verfügbarer Skripts oder von LSB-Skripts (init-like) definiert werden.

Wir wollen, dass Pacemaker die Zuständigkeit für die DRBD-Ressource, den Bereitstellungspunkt und den MySQL-Dienst übernimmt. Wenn Pacemaker DRBD ein- und ausschalten, dessen Bereitstellung vornehmen und aufheben und MySQL in der richtigen Reihenfolge starten/beenden kann, sollte der Primärknoten einmal ausfallen, ist unsere Einrichtung fertig.

Wenn Sie Pacemaker erstmals installieren, sollte Ihre Konfiguration so einfach wie im folgenden Beispiel sein:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Überprüfen Sie sie durch Ausführen von `sudo crm configure show`. Erstellen Sie nun eine Datei (z. B. `/tmp/cluster.conf`) mit den folgenden Ressourcen:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

Laden Sie sie in die Konfiguration (was nur auf einem Knoten erfolgen muss):

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Vergewissern Sie sich auch, dass Pacemaker beim Startvorgang auf beiden Knoten gestartet wird:

    sudo update-rc.d pacemaker defaults

Überprüfen Sie nach ein paar Sekunden und mithilfe von `sudo crm_mon –L`, ob einer Ihrer Knoten zum Master des Clusters geworden ist, auf dem alle Ressourcen ausgeführt werden. Mit den Befehlen "mount" und "ps" können Sie prüfen, ob die Ressourcen ausgeführt werden.

Das folgende Bildschirmfoto zeigt `crm_mon` mit einem angehaltenen Knoten (Anhalten über STRG+C).

![Angehaltener Knoten "crm\_mon"][Angehaltener Knoten "crm\_mon"]

Und dieses Bildschirmfoto zeigt beide Knoten mit einem Master und einem Slave:

![crm\_mon – Master/Slave in Betrieb][crm\_mon – Master/Slave in Betrieb]

## Tests

Wir sind nun für eine automatische Failoversimulation bereit. Hierfür gibt es zwei Möglichkeiten: weich und hart. Der "weiche" Weg ist das Nutzen der Herunterfahrfunktion des Clusters: `` crm_standby -U `uname -n` -v on ``. Wird diese auf den Master angewendet, übernimmt der Slave. Denken Sie daran, diese wieder auf "off" zurückzusetzen (crm\_mon informiert Sie ansonsten, dass ein Knoten im Standbybetrieb ist).

Der "harte" Weg ist das Herunterfahren des primären virtuellen Computers (hadb01) über das Portal oder Ändern der Ausführungsebene auf dem virtuellen Computer (z. B. Anhalten, Herunterfahren). Wir unterstützen Corosync und Pacemaker, indem wir signalisieren, dass der Master ausgeschaltet wird. Wir können dies testen (was für Wartungszeiten nützlich ist), dieses Szenario aber auch erzwingen, indem wir lediglich den virtuellen Computer sperren.

## STONITH

Es sollte möglich sein, das Herunterfahren eines virtuellen Computers mithilfe der Azure-Befehlszeilentools für Linux anstatt mit einem STONITH-Skript zu bewirken, das ein physisches Gerät steuert. Sie können `/usr/lib/stonith/plugins/external/ssh` als Basis verwenden und STONITH in der Konfiguration des Clusters aktivieren. Die Azure-Befehlszeilen-Schnittstelle muss global installiert sein, und die Veröffentlichungseinstellungen/das Profil müssen bzw. muss für den Benutzer des Clusters geladen sein.

Beispielcode für die Ressource steht auf [GitHub][GitHub] zur Verfügung. Sie müssen die Konfiguration des Clusters ändern, indem Sie `sudo crm configure` Folgendes hinzufügen:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Hinweis:** Das Skript führt keine Überprüfung auf Betriebs-/Ausfallzeiten durch. Die ursprüngliche SSH-Ressource wurde 15 Ping-Prüfungen unterzogen, doch die Wiederherstellungszeit für einen virtuellen Azure-Computer ist ggf. variabler.

## Einschränkungen

Es gelten die folgenden Einschränkungen:

-   Das DRBD-Ressourcenskript von Linbit, das DRBD als Ressource in Pacemaker verwaltet, verwendet `drbdadm down` beim Herunterfahren eines Knotens, auch wenn der Knoten bloß auf Standby umschaltet. Dies ist nicht ideal, da der Slave die DRBD-Ressource nicht synchronisiert, während der Master Schreibvorgänge empfängt. Wenn für den Master kein ordnungsgemäßes Failover erfolgt, kann der Slave einen älteren Dateisystemstatus übernehmen. Es gibt zwei potenzielle Lösungsmöglichkeiten:
-   Erzwingen von `drbdadm up r0` auf allen Clusterknoten mithilfe einer lokalen (nicht im Cluster eingebundenen) Überwachungsfunktion oder
-   Bearbeiten des DRBD-Skripts von Linbit in `/usr/lib/ocf/resource.d/linbit/drbd` dergestalt, dass `down` nicht aufgerufen wird.
-   Der Lastenausgleich benötigt mindestens 5 Sekunden für eine Reaktion, weshalb Anwendungen clusterfähig und toleranter für Timeouts sein müssen. Andere Architektur sind ggf. ebenfalls hilfreich, z. B. anwendungsinterne Warteschlangen, Middleware für Abfragen usw.
-   Eine MySQL-Optimierung ist notwendig, um sicherzustellen, dass Schreibvorgänge in einem geeigneten Tempo erfolgen und Caches so oft wie möglich auf Datenträger geschrieben werden, um Arbeitsspeicherverluste zu minimieren.
-   Die Schreibleistung hängt von den Verbindungen der virtuellen Computer untereinander im virtuellen Switch ab, da dieser Mechanismus von DRBD zum Replizieren des Geräts genutzt wird.

  [Vorbereitung]: #getting-ready
  [Einrichten des Clusters]: #setting-up-the-cluster
  [Einrichten von MySQL]: #setting-up-mysql
  [Einrichten von Corosync]: #setting-up-corosync
  [Einrichten von Pacemaker]: #setting-up-pacemaker
  [Tests]: #testing
  [STONITH]: #stonith
  [Einschränkungen]: #limitations
  [Channel 9]: http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL
  [VM Depot]: http://vmdepot.msopentech.com
  [corosync-quorumtool -l sample output]: media/virtual-machines-linux-mysql-cluster/image001.png
  [Angehaltener Knoten "crm\_mon"]: media/virtual-machines-linux-mysql-cluster/image002.png
  [crm\_mon – Master/Slave in Betrieb]: media/virtual-machines-linux-mysql-cluster/image003.png
  [GitHub]: https://github.com/bureado/aztonith
