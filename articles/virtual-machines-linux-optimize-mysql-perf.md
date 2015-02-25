<properties pageTitle="Optimieren der MySQL-Leistung auf virtuellen Azure Linux-Computern" description="Erfahren Sie, wie Sie MySQL bei Ausführung auf einem virtuellen Azure-Computer unter Linux optimieren können." services="virtual-machines" documentationCenter="" authors="NingKuang" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/27/2014" ms.author="ningk"/>

#Optimieren der MySQL-Leistung auf virtuellen Azure Linux-Computern 

Es gibt viele Faktoren, die die MySQL-Leistung unter Azure beeinträchtigen, sowohl in Bezug auf die Auswahl der virtuellen Hardware als auch auf die Softwarekonfiguration. Dieser Artikel befasst sich mit der Leistungsoptimierung über die Speicher-, System- und Datenbankkonfiguration.

##Verwenden von RAID auf einem virtuellen Azure-Computer 
Speicher ist der wichtigste Faktor, der sich auf die Leistung der Datenbank in Cloud-Umgebungen auswirkt.  Im Vergleich zu einem einzelnen Datenträger kann RAID schnelleren Zugriff über Parallelität bereitstellen.  Unter [Standard-RAID-Stufen](http://en.wikipedia.org/wiki/Standard_RAID_levels) finden Sie weitere Details.   

Datenträger-E/A-Durchsatz und E/A-Antwortzeiten in Azure können durch RAID erheblich verbessert werden. Unsere Labortests zeigen, dass der Datenträger-E/A-Durchsatz bei Verdoppelung der Anzahl der RAID-Datenträger (von 2 auf 4, von 4 auf 8 usw.) durchschnittlich verdoppelt und die E/A-Antwortzeiten auf die Hälfte reduziert werden können. In [Anhang A](#AppendixA) finden Sie nähere Informationen.  

Zusätzlich zur Datenträger-E/A verbessert sich die MySQL-Leistung, wenn Sie die RAID-Stufe erhöhen.  In [Anhang B](#AppendixB) finden Sie nähere Informationen.  

Sie sollten auch die Segmentgröße berücksichtigen. Im Allgemeinen erzielen Sie mit größeren Segmenten niedrigere Gemeinkosten, insbesondere bei großen Schreibvorgängen. Eine zu große Segmentgröße kann jedoch mit zusätzlichen Gemeinkosten verbunden sein, und Sie können die Vorteile des RAID nicht nutzen. Die aktuelle Standardgröße beträgt 512 KB. Diese hat sich für die meisten allgemeinen Produktionsumgebungen als optimal erwiesen. In [Anhang C](#AppendixC) finden Sie nähere Informationen.   

Bitte beachten Sie, dass es hinsichtlich der Anzahl der Festplatten, die Sie für verschiedene Arten von virtuellen Computern hinzufügen können, Beschränkungen gibt. Ausführliche Informationen zu diesen Grenzen finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Im RAID-Beispiel in diesem Artikel benötigen Sie 4 angefügte Datenträger; Sie können jedoch auch ein RAID mit weniger Datenträgern einrichten.  

In diesem Artikel wird angenommen, dass Sie bereits einen virtuellen Linux-Computer erstellt und MySQL installiert und konfiguriert haben. Weitere Informationen zu den ersten Schritten finden Sie unter "Installieren von MySQL unter Azure".  
  
###Einrichtung von RAID unter Azure
Die folgenden Schritte beschreiben, wie Sie RAID mithilfe des Windows Azure-Verwaltungsportals auf Azure erstellen können. Sie können RAID auch mithilfe von Windows PowerShell-Skripten einrichten. 
In diesem Beispiel wird RAID 0 mit 4 Datenträgern konfiguriert.  

####Schritt 1: Hinzufügen eines Datenträgers zum virtuellen Computer  

Klicken Sie auf der Seite "Virtuelle Computer" im Azure-Verwaltungsportal auf den virtuellen Computer, dem Sie einen Datenträger hinzufügen möchten. In diesem Beispiel wird der virtuelle Computer "mysqlnode1" verwendet.  

![][1]

Klicken Sie auf der Seite für den virtuellen Computer auf **Dashboard**.  

![][2]
 

Klicken Sie in der Taskleiste auf **Anfügen**.
 
![][3]

Klicken Sie dann auf **Leeren Datenträger anfügen**.  

![][4]
 
Für Datenträger sollte für **Hostcacheeinstellungen** die Einstellung **Keine** festgelegt werden.  

Dadurch wird ein leerer Datenträger zu Ihrem virtuellen Computer hinzugefügt. Wiederholen Sie diesen Schritt drei weitere Male, bis Sie über 4 Datenträger für RAID verfügen.  

Sie sehen die hinzugefügten Laufwerke auf dem virtuellen Computer im Kernel-Message-Protokoll. Verwenden Sie hierzu unter Ubuntu beispielsweise den folgenden Befehl:  

	sudo grep SCSI /var/log/dmesg

####Schritt 2: Erstellen von RAID mit den zusätzlichen Datenträgern
Die detaillierten Schritte zur Einrichtung von RAID finden Sie in diesem Artikel:  

[http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-linux-configure-RAID/](http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-linux-configure-RAID/)

>[AZURE.NOTE] Führen Sie bei Verwendung des XFS-Dateisystems nach der RAID-Erstellung die nachfolgend aufgeführten Schritte aus:

Verwenden Sie den folgenden Befehl, um XFS auf Debian, Ubuntu oder Linux Mint zu installieren:  

	apt-get -y install xfsprogs  

Verwenden Sie den folgenden Befehl, um XFS auf Fedora, CentOS oder RHEL zu installieren:  

	yum -y install xfsprogs  xfsdump 


####Schritt 3: Einrichten eines neuen Speicherpfads
Verwenden Sie den folgenden Befehl:  

	root@mysqlnode1:~# mkdir -p /RAID0/mysql

####Schritt 4: Kopieren der ursprünglichen Daten an den neuen Speicherpfad
Verwenden Sie den folgenden Befehl:  

	root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####Schritt 5: Ändern der Berechtigungen, sodass MySQL Lese- und Schreibzugriff auf den Datenträger erhält
Verwenden Sie den folgenden Befehl:  

	root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##Anpassen des E/A-Scheduling-Algorithmus
Linux implementiert vier Arten von E/A-Scheduling-Algorithmen:  

-	NOOP-Algorithmus (Kein Vorgang)
-	Deadline-Algorithmus (Frist)
-	Completely Fair Queuing-Algorithmus (CFQ)
-	Budget Period-Algorithmus (Budgetzeitraum, antizipiert)  

Sie können verschiedene E/A-Scheduler in verschiedenen Szenarien auswählen, um die Leistung zu optimieren. In einer vollständig wahlfreien Zugriffsumgebung unterscheidet sich die Leistung beim CFQ- und beim Deadline-Algorithmus nicht wesentlich. Im Allgemeinen wird für die MySQL-Datenbankumgebung der Stabilität halber die Einstellung "Deadline" empfohlen. Bei einer großen Zahl an sequenziellen E/A kann die Datenträger-E/A-Leistung mit CFQ beeinträchtigt werden.   

SSD und andere Geräte erzielen mit NOOP oder Deadline eine bessere Leistung als der Standard-Scheduler.   

Ab Kernel 2.5 ist der Standard-E/A-Scheduling-Algorithmus "Deadline". CFQ wurde ab Kernel 2.6.18 der Standard-E/A-Scheduling-Algorithmus.  Sie können diese Einstellung zur Kernel-Startzeit angeben oder sie bei laufendem System dynamisch ändern.  

Im folgenden Beispiel wird veranschaulicht, wie der Scheduler überprüft und der NOOP-Algorithmus als Standard-Scheduler festgelegt wird.  

Für Debian-Verteilungen:

###Schritt 1. Anzeigen des aktuellen E/A-Scheduler
Verwenden Sie den folgenden Befehl:  

	root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler 

Sie sehen die folgende Ausgabe; diese gibt den aktuellen Scheduler an.  

	noop [deadline] cfq 


###Schritt 2: Ändern des aktuellen Geräts (/dev/sda) des E/A-Scheduling-Algorithmus
Verwenden Sie die folgenden Befehle:  

	azureuser@mysqlnode1:~$ sudo su -
	root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mysqlnode1:~# update-grub

>[AZURE.NOTE] Nur für /dev/sda ist diese Einstellung nicht sinnvoll. Sie muss auf allen Datenträgern festgelegt werden, auf denen sich die Datenbank befindet.  

Die folgende Ausgabe zeigt an, dass grub.cfg erfolgreich neu erstellt wurde und dass der Standard-Scheduler zu NOOP aktualisiert wurde.  

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

Bei Redhat-Verteilungen benötigen Sie nur den folgenden Befehl:   

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##Konfigurieren der Einstellungen für Systemdateivorgänge
Eine bewährte Methode ist, das Protokollierungsfeature "atime" im Dateisystem zu deaktivieren. "Atime" ist die Zeit des letzten Dateizugriffs. Wenn auf eine Datei zugegriffen wird, erfasst das Dateisystem den Zeitstempel im Protokoll. Diese Informationen werden jedoch nur selten verwendet. Sie können sie deaktivieren, wenn Sie sie nicht benötigen. Hierdurch verringert sich die Datenträgerzugriffszeit insgesamt.  
 
Für die Deaktivierung der atime-Protokollierung müssen Sie die Dateisystemkonfigurationsdatei "/etc/fstab" ändern und die Option **noatime** hinzufügen.  

Bearbeiten Sie z. B. die "vim/etc/fstab"-Datei, indem Sie "noatime" hinzufügen (siehe unten).  

	# CLOUD_IMG: This file was created/modified by the Cloud Image build process
	UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
	#Add the "noatime" option below to disable atime logging
	UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
	/dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Stellen Sie dann das Dateisystem mit dem folgenden Befehl erneut bereit:  

	mount -o remount /RAID0

Testen Sie das geänderte Ergebnis. Beachten Sie, dass die Zugriffszeit beim Ändern der Testdatei nicht aktualisiert wird.  

Beispiel vorher:		

![][5]
 
Beispiel nachher:

![][6]

##Erhöhen der maximalen Anzahl von Systemhandles für hohe Parallelität
MySQL ist eine Datenbank mit hoher Parallelität. Die Standardzahl von gleichzeitigen Handles beträgt 1024 für Linux. Dies ist nicht immer ausreichend. **Gehen Sie folgendermaßen vor, um die Zahl von maximalen gleichzeitigen Handles des Systems zur Unterstützung hoher Parallelität von MySQL zu erhöhen**.

###Schritt 1: Ändern der Datei "limits.conf"
Fügen Sie die folgenden vier Zeilen in die Datei "/etc/security/limits.conf" ein, um die Anzahl von maximal zulässigen gleichzeitigen Handles zu erhöhen. Beachten Sie, dass 65536 die maximale Anzahl ist, die das System unterstützen kann.   

	* soft nofile 65536
	* hard nofile 65536
	* soft nproc 65536
	* hard nproc 65536

###Schritt 2: Aktualisieren des Systems mit den neuen Grenzen
Führen Sie die folgenden Befehle aus:  

	ulimit -SHn 65536
	ulimit -SHu 65536 

###Schritt 3: Sicherstellen, dass die Grenzwerte beim Systemstart aktualisiert werden
Fügen Sie die folgenden Startbefehle in die Datei "/etc/rc.local" ein, damit diese bei jedem Systemstart wirksam werden.  

	echo "ulimit -SHn 65536" >>/etc/rc.local
	echo "ulimit -SHu 65536" >>/etc/rc.local

##MySQL-Datenbankoptimierung 
Sie können die gleiche Leistungsoptimierungsstrategie für die Konfiguration von MySQL auf Azure wie für einen lokalen Computer verwenden.  

Die wichtigsten E/A-Optimierungsregeln sind:   

-	Cache vergrößern
-	E/A-Antwortzeit reduzieren

Um MySQL-Server-Einstellungen zu optimieren, können Sie die Datei "my.cnf" aktualisieren. Dabei handelt es sich um die Standardkonfigurationsdatei für Server und Clientcomputer.  

Die folgenden Konfigurationselemente sind die Hauptfaktoren, die die MySQL-Leistung beeinflussen:  

-	**innodb_buffer_pool_size**: Der Pufferpool enthält gepufferte Daten und den Index. Dies ist normalerweise auf 70 % des physischen Arbeitsspeichers festgelegt.
-	**innodb_log_file_size**: Dies ist die Größe der Wiederholen-Protokolldatei. Sie verwenden die Wiederholen-Protokolle, um sicherzustellen, dass die Schreibvorgänge nach einem Systemabsturz schnell, zuverlässig und wiederherstellbar sind. Dies ist auf 512 MB festgelegt. Damit verfügen Sie über ausreichend Speicherplatz für die Protokollierung von Schreibvorgängen.
-	**max_connections**: Manchmal schließen Anwendungen Verbindungen nicht ordnungsgemäß. Mit einem größeren Wert erhält der Server mehr Zeit für die Wiederverwendung von Leerlaufverbindungen. Es sind maximal 10.000 Verbindungen möglich, aber das empfohlene Maximum beträgt 5000 Verbindungen.
-	**Innodb_file_per_table**: Mit dieser Einstellung wird die Fähigkeit von InnoDB zur Speicherung von Tabellen in separaten Dateien aktiviert bzw. deaktiviert. Durch Aktivieren der Option wird sichergestellt, dass mehrere erweiterte Verwaltungsvorgänge effizient angewendet werden können. Vom Leistungsstandpunkt aus betrachtet, kann die Übermittlung von Tabellenspeicherplatz hierdurch beschleunigt und die Leistung beim Umgang mit Trümmerteilen optimiert werden. Daher wird empfohlen, diese Einstellung zu aktivieren.</br>
	Ab MySQL 5.6 ist diese Option standardmäßig aktiviert. Daher ist keine Aktion erforderlich. Bei den früheren Versionen (vor 5.6) ist die Option standardmäßig deaktiviert. Daher müssen Sie diese aktivieren. Die Option sollte vor dem Laden neuer Daten angewendet werden, weil sie sich nur auf neu erstellte Tabellen auswirkt.
-	**innodb_flush_log_at_trx_commit**: Der Standardwert ist 1; der Gültigkeitsbereich ist auf 0~2 eingestellt. Der Standardwert ist die am besten geeignete Option für eine eigenständige MySQL-DB. Die Einstellung 2 ermöglicht die meiste Datenintegrität und eignet sich für den Master im MySQL-Cluster. Die Einstellung 0 lässt Datenverluste zu. Dies kann die  Zuverlässigkeit beeinträchtigen. In einigen Fällen wird die Leistung verbessert. Daher eignet sich die Einstellung für Slaves im MySQL-Cluster.
-	**Innodb_log_buffer_size**: Der Protokollpuffer lässt zu, dass Transaktionen ausgeführt werden, ohne dass das Protokoll vor dem Übernehmen der Transaktionen auf den Datenträger übertragen werden muss. Wenn jedoch ein großes binäres Objekt oder Textfeld vorhanden ist, wird der Zwischenspeicher sehr schnell aufgebraucht. Dadurch werden häufige Datenträger-E/A ausgelöst. Es ist besser, die Puffergröße zu erhöhen, wenn die Zustandsvariable Innodb_log_waits nicht 0 ist.
-	**query_cache_size**: Die beste Möglichkeit ist, diese Einstellung von Anfang an zu deaktivieren. Legen Sie die query_cache_size auf 0 fest (in MySQL 5.6 ist dies nun die Standardeinstellung) und verwenden Sie anderen Methoden zur Beschleunigung von Abfragen.  
  
In [Anhang D](AppendixD) finden Sie einen Vergleich der Leistung nach der Optimierung.


##Aktivieren des Protokolls für langsame MySQL-Abfragen zur Analyse des Leistungsengpasses
Das Protokoll für langsame MySQL-Abfragen kann Ihnen dabei helfen, langsame MySQL-Abfragen zu identifizieren. Nach der Aktivierung des Protokolls für langsame MySQL-Abfragen können Sie die MySQL-Tools wie **mysqldumpslow** zur Identifizierung von Leistungsengpässen verwenden.  

Beachten Sie, dass diese Option standardmäßig nicht aktiviert ist. Die Aktivierung des Protokolls für langsame Abfragen kann einige CPU-Ressourcen beanspruchen. Daher wird empfohlen, dass Sie diese Option nur vorübergehend zur Problembehebung bei Leistungsengpässen aktivieren.

###Schritt 1: Ändern der Datei "my.cnf" durch Hinzufügen der folgenden Zeilen am Ende   

	long_query_time = 2
	slow_query_log = 1
	slow_query_log_file = /RAID0/mysql/mysql-slow.log

###Schritt 2: Neustart des MySQL-Servers
	service  mysql  restart

###Schritt 3: Überprüfen der Wirksamkeit der Einstellung mit dem Befehl "show"
 
![][7]   
   
![][8]
 
In diesem Beispiel sehen Sie, dass die Funktion für langsame Abfragen aktiviert wurde. Anschließend können Sie mit dem Tool **mysqldumpslow** Leistungsengpässe ermitteln und die Leistung optimieren, z. B. das Hinzufügen von Indizes.





##Anhang

Im folgenden Beispiel werden Leistungstestdaten für eine gezielte Lab-Umgebung erstellt. Diese liefern allgemeine Hintergrundinformationen zum Leistungsdatentrend bei unterschiedlichen Ansätzen zur Leistungsoptimierung. Je nach Umgebung oder Produktversionen können die Ergebnisse jedoch variieren. 

<a name="AppendixA"></a>Anhang A: **Datenträgerleistung (IOPS) mit verschiedenen RAID-Stufen** 


![][9]
 
**Test-Befehle:**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE.NOTE: Die Arbeitsauslastung dieses Tests verwendet 64 Threads, die die Obergrenze des RAID erreichen sollen.

<a name="AppendixB"></a>Anhang B:
**MySQL-Leistungsvergleich (Durchsatz) bei verschiedenen RAID-Stufen**   
(XFS-Dateisystem)

 
![][10]  
![][11]

**Test-Befehle:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write -engine=innodb

**MySQL-Leistungsvergleich (OLTP) bei verschiedenen RAID-Stufen**  
![][12]

**Test-Befehle:**

	time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Anhang C:
**Vergleich der Datenträgerleistung (IOPS) bei verschiedenen Blockgrößen**  
(XFS-Dateisystem)

 
![][13]

**Test-Befehle:**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Für diesen Test wird eine Dateigröße von 30 GB bzw. 1 GB mit einem RAID 0 (4 Festplatten)-XFS-Dateisystem verwendet.


<a name="AppendixD"></a>Anhang D:  
**Vergleich des MySQL-Leistung (Durchsatz) vor und nach der Optimierung**  
(XFS-Dateisystem)

  
![][14]

**Test-Befehle:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write -engine=innodb,misam

**Die Konfigurationseinstellung für Standard- und optimierte Konfiguration lautet wie folgt:**

|Parameter|Standard|Optimierung
|-----------|-----------|-----------
|**innodb_buffer_pool_size**	|None	|7G
|**innodb_log_file_size**	|5M	|512M
|**max_connections**	|100	|5000
|**innodb_file_per_table**	|0	|1
|**innodb_flush_log_at_trx_commit**	|1	|2
|**innodb_log_buffer_size**	|8M	|128M
|**query_cache_size**	|16M	|0


Weitere und detailliertere Konfigurationsparameter für die Optimierung finden Sie in den offiziellen MySQL-Anweisungen.  

[http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Testumgebung**  

|Hardware	|Details
|-----------|-------
|CPU	|AMD Opteron(tm)-Prozessor 4171 HE/4 Kerne
|Arbeitsspeicher	|14G
|Datenträger	|10G/Datenträger
|Betriebssystem	|Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-14.png




<!--HONumber=42-->
