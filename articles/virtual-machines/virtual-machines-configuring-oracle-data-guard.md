<properties title="Configuring Oracle Data Guard for Azure" pageTitle="Konfigurieren von Oracle Data Guard für Azure" description="Bearbeiten Sie ein Lernprogramm für das Einrichten und Implementieren von Oracle Data Guard auf Azure Virtual Machines für hohe Verfügbarkeit und Notfallwiederherstellung." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Konfigurieren von Oracle Data Guard für Azure
Dieses Lernprogramm zeigt, wie Sie Oracle Data Guard für virtuelle Computer in einer Azure-Umgebung für hohe Verfügbarkeit und Notfallwiederherstellung einrichten und implementieren. Das Lernprogramm konzentriert sich auf die unidirektionale Replikation für Oracle-Datenbanken ohne RAC.

Oracle Data Guard unterstützt Datenschutz und Notfallwiederherstellung für Oracle Database. Es handelt sich um eine einfache, leistungsfähige und gebrauchsfertige Lösung für Notfallwiederherstellung, Datenschutz und hohe Verfügbarkeit des gesamten Oracle Database-Systems.

In diesem Lernprogramm wird davon ausgegangen, dass Sie bereits theoretische und praktische Kenntnisse zu Konzepten der Hochverfügbarkeit und Notfallwiederherstellung von Oracle Database besitzen. Weitere Informationen finden Sie auf der [Oracle-Website](http://www.oracle.com/technetwork/database/features/availability/index.html) sowie im [Oracle Data Guard Concepts and Administration Guide](http://docs.oracle.com/cd/E11882_01/server.112/e17022/create_ps.htm) ("Handbuch zu Konzepten und Verwaltung von Oracle Data Guard", in englischer Sprache).

Das Lernprogramm geht zudem davon aus, dass Sie die folgenden Voraussetzungen bereits implementiert haben:

- Sie haben bereits den Abschnitt mit Überlegungen zu Hochverfügbarkeit und Notfallwiederherstellung im Thema [Images virtueller Oracle-Computer – verschiedene Überlegungen](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md) gelesen. Beachten Sie, dass Azure derzeit eigenständige Oracle-Datenbankinstanzen, aber keine Oracle Real Application Clusters (Oracle RAC) unterstützt.

- Sie haben zwei virtuelle Computer (VMs) in Azure mit demselben von der Plattform bereitgestellten Image von Oracle Enterprise Edition unter Windows Server erstellt. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Oracle-Datenbank 12c-Computers in Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md) und [Azure Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/). Stellen Sie sicher, dass sich die virtuellen Computer in [demselben Clouddienst](virtual-machines-load-balance.md) und im demselben [virtuellen Netzwerk](azure.microsoft.com/documentation/services/virtual-network/) befinden, damit sie über die statische private IP-Adresse aufeinander zugreifen können. Darüber hinaus wird empfohlen, die virtuellen Computern in derselben [Verfügbarkeitsgruppe](virtual-machines-manage-availability.md) zu platzieren, damit sie von Azure in eigenen Fehlerdomänen und Upgradedomänen angeordnet werden können. Beachten Sie, dass Oracle Data Guard nur mit Oracle Database Enterprise Edition verfügbar ist. Jeder Computer muss mindestens 2 GB Arbeitsspeicher und 5 GB Speicherplatz aufweisen. Aktuelle Informationen zu den von der Plattform bereitgestellten VM-Größen finden Sie unter [Größen virtueller Computer für Azure](http://msdn.microsoft.com/library/dn197896.aspx). Wenn Sie zusätzliche Datenträgervolumes für die virtuellen Computer benötigen, können Sie zusätzliche Datenträger anfügen. Entsprechende Informationen finden Sie unter [Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Computer](storage-windows-attach-disk.md).

- Sie haben im Azure-Portal die Namen der virtuellen Computer auf Machine1 für den primären virtuellen Computer und Machine2 für den virtuellen Standbycomputer festgelegt.

- Sie haben festgelegt, dass auf dem primären virtuellen Computer und auf dem virtuellen Standbycomputer die **ORACLE\_HOME**-Umgebungsvariable auf denselben Oracle-Stamminstallationspfad verweist, z. B. `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Sie melden Sie sich beim Windows-Server als Mitglied der Gruppe **Administratoren** oder als Mitglied der Gruppe **ORA\_DBA** an.

In diesem Lernprogramm lernen Sie Folgendes:

Implementieren der physischen Standbydatenbankumgebung

1. Erstellen einer primären Datenbank

2. Vorbereiten der primären Datenbank für die Erstellung der Standbydatenbank

	1. Aktivieren von erzwungener Protokollierung

	2. Erstellen einer Kennwortdatei

	3. Konfigurieren eines Standby Redo Logs

	4. Archivierung aktivieren

	5. Festlegen der Initialisierungsparameter für die primäre Datenbank

Erstellen einer physischen Standbydatenbank

1. Erstellen einer Initialisierungsparameterdatei für die Standbydatenbank

2. Konfigurieren von Listener und TNSNAMES für die Unterstützung der Datenbank auf dem primären und Standbycomputer

	1. Konfigurieren von "listener.ora" auf beiden Servern zum Speichern von Einträgen für beide Datenbanken

	2. Konfigurieren von "tnsnames.ora" auf dem primären virtuellen Computer und virtuellen Standbycomputer zum Speichern von Einträgen für die primäre und Standbydatenbank

	3. Starten des Listeners und Testen von "tnsping" auf beiden virtuellen Computern für beide Dienste

3. Starten der Standbyinstanz im Status "NOMOUNT"

4. Verwenden von RMAN zum Klonen der Datenbank und Erstellen einer Standbydatenbank

5. Starten der physischen Standbydatenbank im verwalteten Wiederherstellungsmodus

6. Überprüfen der physischen Standbydatenbank

> [AZURE.IMPORTANT]Dieses Lernprogramm wurde mit der folgenden Hardware- und Softwarekonfiguration eingerichtet und getestet:
>
>| | **Primäre Datenbank** | **Standbydatenbank** |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Oracle-Version** | Oracle11g Enterprise Release (11.2.0.4.0) | Oracle11g Enterprise Release (11.2.0.4.0) |
>| **Computername** | Machine1 | Machine2 |
>| **Betriebssystem** | Windows 2008 R2 | Windows 2008 R2 |
>| **Oracle SID** | TEST | TEST\_STBY |
>| **Arbeitsspeicher** | Mindestens 2 GB | Mindestens 2 GB |
>| **Speicherplatz** | Mindestens 5 GB | Mindestens 5 GB |

Nachfolgende Versionen von Oracle-Datenbanken und Oracle Data Guard enthalten möglicherweise einige zusätzlichen Änderungen, die Sie implementieren müssen. Bestimmte Informationen zur neuesten Version finden Sie in der Dokumentation zu [Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) und [Oracle Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) auf der Oracle-Website.

##Implementieren der physischen Standbydatenbankumgebung
### 1\. Erstellen einer primären Datenbank

- Erstellen Sie auf dem primären virtuellen Computer die primäre Datenbank "TEST". Informationen hierzu finden Sie unter "Erstellen und Konfigurieren einer Oracle-Datenbank".
- Stellen Sie an der SQL*Plus-Eingabeaufforderung als Benutzer SYS mit der Rolle SYSDBA eine Verbindung mit der Datenbank her, und führen Sie die folgende Anweisung aus, um den Namen der Datenbank anzuzeigen:

		SQL> select name from v$database;
		
		The result will display like the following:
		
		NAME
		---------
		TEST
- Fragen Sie anschließend die Namen der Datenbankdateien aus der Systemansicht "dba\_data\_files" ab:

		SQL> select file_name from dba_data_files; 
		FILE_NAME 
		------------------------------------------------------------------------------- 
		C:\ <YourLocalFolder>\TEST\USERS01.DBF
		C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
		C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
		C:<YourLocalFolder>\TEST\SYSTEM01.DBF
		C:<YourLocalFolder>\TEST\EXAMPLE01.DBF

### 2\. Vorbereiten der primären Datenbank für die Erstellung der Standbydatenbank

Es wird empfohlen, vor dem Erstellen einer Standbydatenbank sicherzustellen, dass die primäre Datenbank ordnungsgemäß konfiguriert ist. Sie müssen die folgenden Schritte ausführen:

1. Aktivieren von erzwungener Protokollierung
2. Erstellen einer Kennwortdatei
3. Konfigurieren eines Standby Redo Logs
4. Archivierung aktivieren
5. Festlegen der Initialisierungsparameter für die primäre Datenbank

#### Aktivieren von erzwungener Protokollierung

Zum Implementieren einer Standbydatenbank muss in der primären Datenbank die erzwungene Protokollierung aktiviert werden. Diese Option stellt sicher, dass selbst bei einem Vorgang ohne Protokollierung die erzwungene Protokollierung Vorrang hat und alle Vorgänge in den Redo Logs protokolliert werden. Deshalb stellen wir sicher, dass alle Vorgänge in der primären Datenbank protokolliert werden und die Replikation in die Standbydatenbank alle Vorgänge in der primären Datenbank umfasst. Führen Sie die Anweisung "ALTER DATABASE" zum Aktivieren der erzwungenen Protokollierung aus:

	SQL> ALTER DATABASE FORCE LOGGING;

	Database altered.

#### Erstellen einer Kennwortdatei

Um archivierte Protokolle vom primären Server an den Standbyserver zu versenden und auf diesem anzuwenden, muss das SYS-Kennwort auf dem primären und Standbyserver identisch sein. Deshalb erstellen Sie in der primären Datenbank eine Kennwortdatei und kopieren sie auf den Standbyserver.

>[AZURE.IMPORTANT]Bei Verwendung von Oracle Database 12c ist der neue Benutzer **SYSDG** vorhanden, mit dem Sie Oracle Data Guard verwalten können. Weitere Informationen finden Sie unter[Änderungen in der Version Oracle Database 12c](http://docs.oracle.com/cd/E16655_01/server.121/e10638/release_changes.htm).

Stellen Sie außerdem sicher, dass in Machine1 die Umgebung "ORACLE\_HOME" bereits definiert ist. Wenn sie noch nicht definiert ist, definieren Sie sie im Dialogfeld "Umgebungsvariablen" als Umgebungsvariable. Um auf dieses Dialogfeld zuzugreifen, starten Sie das Hilfsprogramm **System**, indem Sie in der **Systemsteuerung** auf das Symbol "System" doppelklicken. Klicken Sie dann auf die Registerkarte **Erweitert**, und wählen Sie **Umgebungsvariablen** aus. Klicken Sie unter **Systemvariablen** auf die Schaltfläche **Neu**, um die Umgebungsvariablen festzulegen. Nachdem Sie die Umgebungsvariablen eingerichtet haben, schließen Sie die vorhandene Windows-Eingabeaufforderung, und öffnen Sie eine neue Eingabeaufforderung.

Führen Sie die folgende Anweisung aus, um zum Verzeichnis "Oracle\_Home", z. B. "C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\database", zu wechseln.

	cd %ORACLE_HOME%\database

Erstellen Sie dann mit dem Hilfsprogramm für die Kennwortdateierstellung [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm) eine Kennwortdatei. Führen Sie an der gleichen Windows-Eingabeaufforderung auf Machine1 den folgenden Befehl aus, indem Sie den Kennwortwert als Kennwort von **SYS** festlegen:

	ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Mit diesem Befehl wird im Verzeichnis "ORACLE\_HOME\\database" eine Kennwortdatei mit dem Namen "PWDTEST.ora" erstellt. Kopieren Sie diese Datei manuell in das Verzeichnis "%ORACLE\_HOME%\\database" auf Machine2.

#### Konfigurieren eines Standby Redo Logs

Anschließend müssen Sie ein Standby Redo Log so konfigurieren, dass die primäre Datenbank die Redo Logs ordnungsgemäß empfangen kann, wenn aus ihr eine Standbydatenbank wird. Wenn Sie die Standby Redo Logs im Voraus erstellen, können sie in der Standbydatenbank automatisch erstellt werden. Es ist wichtig, die Standby Redo Logs (SRL) mit derselben Größe wie die Online Redo Logs zu konfigurieren. Die Größe der aktuellen Standby-Redo-Log-Dateien muss genau mit der Größe der Online-Redo-Log-Dateien der aktuellen primären Datenbank übereinstimmen.

Führen an der SQL*PLUS-Eingabeaufforderung auf Machine1 die folgende Anweisung aus. "v$logfile" ist eine Systemansicht, die Informationen über Redo-Log-Dateien enthält.

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE    MEMBER                                                       IS_
	---------- ------- ------- ------------------------------------------------------------ ---
	3         ONLINE  C:<YourLocalFolder>\TEST\REDO03.LOG               NO
	2         ONLINE  C:<YourLocalFolder>\TEST\REDO02.LOG               NO
	1         ONLINE  C:<YourLocalFolder>\TEST\REDO01.LOG               NO
	Next, query the v$log system view, displays log file information from the control file. 
	SQL> select bytes from v$log; 
	BYTES
	----------
	52428800
	52428800
	52428800


Beachten Sie, dass 52428800 50 MB bedeutet.

Führen Sie dann im SQL*Plus-Fenster die folgenden Anweisungen aus, um einer Standbydatenbank eine neue Standby-Redo-Log-Dateigruppe hinzuzufügen, und geben Sie mit der GROUP-Klausel eine Nummer an, die die Gruppe bezeichnet. Durch die Verwendung von Gruppennummern lässt sich das Verwalten von Standby-Redo-Log-Dateigruppen vereinfachen:

	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
	Database altered.

Rufen Sie anschließend die folgende Systemansicht auf, um Informationen über Redo-Log-Dateien aufzulisten. Durch diesen Vorgang wird außerdem überprüft, ob die Standby-Redo-Log-Dateigruppen erstellt wurden:

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE MEMBER IS_
	---------- ------- ------- --------------------------------------------- ---
	3         ONLINE C:<YourLocalFolder>\TEST\REDO03.LOG NO
	2         ONLINE C:<YourLocalFolder>\TEST\REDO02.LOG NO
	1         ONLINE C:<YourLocalFolder>\TEST\REDO01.LOG NO
	4         STANDBY C:<YourLocalFolder>\TEST\REDO04.LOG
	5         STANDBY C:<YourLocalFolder>\TEST\REDO05.LOG NO
	6         STANDBY C:<YourLocalFolder>\TEST\REDO06.LOG NO
	6 rows selected.

#### Archivierung aktivieren

Aktivieren Sie dann die Archivierung, um die primäre Datenbank in den Modus "ARCHIVELOG" zu versetzen und die automatische Archivierung zu aktivieren. Sie können den Modus "ARCHIVELOG" aktivieren, indem Sie die Datenbank bereitstellen und dann den Befehl "archivelog" ausführen.

Melden Sie sich zunächst als SYSDBA an. Führen Sie an der Windows-Eingabeaufforderung Folgendes aus:

	sqlplus /nolog
	
	connect / as sysdba

Fahren Sie dann an der SQL*Plus-Eingabeaufforderung die Datenbank herunter:

	SQL> shutdown immediate;
	Database closed.
	Database dismounted.
	ORACLE instance shut down.

Führen Sie dann den Befehl "startup mount" aus, um die Datenbank bereitzustellen. Dadurch wird sichergestellt, dass Oracle die Instanz der angegebenen Datenbank zuordnet.

	SQL> startup mount;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.

Führen Sie anschließend Folgendes aus:

	SQL> alter database archivelog; 
	Database altered.

Führen Sie dann die Anweisung "alter database" mit der Klausel "open" aus, um die Datenbank für die normale Verwendung verfügbar zu machen:

	SQL> alter database open;
	
	Database altered.

#### Festlegen der Initialisierungsparameter für die primäre Datenbank

Zum Konfigurieren von Data Guard müssen Sie zunächst in einer regulären PFILE-Datei (Initialisierungsparameter-Textdatei) die Standbyparameter erstellen und konfigurieren. Wenn die PFILE-Datei erstellt ist, müssen Sie sie in eine Serverparameterdatei (SPFILE) konvertieren.

Sie können die Data Guard-Umgebung mit den Parametern in der Datei "INIT.ORA" steuern. In diesem Lernprogramm müssen Sie die Datei "INIT.ORA" der primären Datenbank aktualisieren, damit sie beide Rollen enthalten kann: primäre Datenbank und Standbydatenbank.

	SQL> create pfile from spfile;
	File created.

Anschließend müssen Sie die PFILE-Datei bearbeiten, um die Standbyparameter hinzuzufügen. Öffnen Sie hierzu im Verzeichnis "% ORACLE\_HOME%\\database" die Datei "INITTEST. ORA". Fügen Sie dann die folgenden Anweisungen an die Datei "INITTEST.ORA" an. Beachten Sie, dass die Datei "INIT.ORA" gemäß der Namenskonvention "INIT<Datenbankname>.ORA" benannt werden muss.
	
	db_name='TEST' 
	db_unique_name='TEST' 
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
	LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
	LOG_ARCHIVE_DEST_STATE_1=ENABLE
	LOG_ARCHIVE_DEST_STATE_2=ENABLE 
	REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE 
	LOG_ARCHIVE_FORMAT=%t_%s_%r.arc 
	LOG_ARCHIVE_MAX_PROCESSES=30
	# Standby role parameters --------------------------------------------------------------------
	fal_server=TEST_STBY
	fal_client=TEST
	standby_file_management=auto
	db_file_name_convert='TEST_STBY','TEST'
	log_file_name_convert='TEST_STBY','TEST'
	# ---------------------------------------------------------------------------------------------


Der vorangegangene Anweisungsblock enthält drei wichtige Setupelemente: –**LOG\_ARCHIVE\_CONFIG...:** Mit dieser Anweisung definieren Sie die eindeutigen Datenbank-IDs. – **LOG\_ARCHIVE\_DEST\_1...:** Mit dieser Anweisung definieren Sie den Speicherort des lokalen Archivordners. Es wird empfohlen, mit dieser Anweisung ein neues Verzeichnis für die Archivierungsanforderungen der Datenbank zu erstellen und den lokalen Archivspeicherort explizit anzugeben, statt den Standardordner "% ORACLE\_HOME%\\database\\archive" von Oracle zu verwenden. – **LOG\_ARCHIVE\_DEST\_2.... LGWR ASYNC...:** Sie definieren einen asynchronen Protokollschreiberprozess (LGWR), um Transaktionswiederholungsdaten zu erfassen und an Standbyziele zu übertragen. Hier gibt "DB\_UNIQUE\_NAME" einen eindeutigen Namen für die Datenbank auf dem Zielstandbyserver an.

Sobald die neue Parameterdatei erstellt ist, müssen Sie aus ihr die SPFILE-Datei erstellen.

Fahren Sie zunächst die Datenbank herunter:

	SQL> shutdown immediate;
	
	Database closed.
	
	Database dismounted.
	
	ORACLE instance shut down.

Führen Sie dann den Befehl "startup nomount" wie folgt aus:

	SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes

Erstellen Sie jetzt eine SPFILE-Datei:

	SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

	File created.

Fahren Sie dann die Datenbank herunter:

	SQL> shutdown immediate;
	
	ORA-01507: database not mounted

Starten Sie dann eine Instanz mithilfe des Befehls "startup":

	SQL> startup;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.
	Database opened.

##Erstellen einer physischen Standbydatenbank
In diesem Abschnitt werden die Schritte beschrieben, die Sie auf Machine2 ausführen müssen, um die physische Standbydatenbank zu erstellen.

Zunächst müssen Sie über das Azure-Portal eine Remotedesktopverbindung mit Machine2 herstellen.

Erstellen Sie dann auf dem Standbyserver (Machine2) alle erforderlichen Ordner für die Standbydatenbank, z. B. "C:\\<LokalerOrdner>\\TEST". Stellen Sie in diesem Lernprogramm sicher, dass die Ordnerstruktur der Ordnerstruktur auf Machine1 entspricht, damit alle erforderlichen Dateien, z. B. Steuerungsdatei, Datendateien, Redo-Log-Dateien sowie die Dateien in den Verzeichnissen "UDUMP", "BDUMP" und "CDUMP", erhalten bleiben. Definieren Sie außerdem auf Machine2 die Umgebungsvariablen "ORACLE\_HOME" und "ORACLE\_BASE". Wenn sie noch nicht definiert sind, definieren Sie sie im Dialogfeld "Umgebungsvariablen". Um auf dieses Dialogfeld zuzugreifen, starten Sie das Hilfsprogramm **System**, indem Sie in der **Systemsteuerung** auf das Symbol "System" doppelklicken. Klicken Sie dann auf die Registerkarte **Erweitert**, und wählen Sie **Umgebungsvariablen** aus. Klicken Sie unter **Systemvariablen** auf die Schaltfläche **Neu**, um die Umgebungsvariablen festzulegen. Nachdem Sie die Umgebungsvariablen eingerichtet haben, müssen Sie die vorhandene Windows-Eingabeaufforderung schließen und eine neue Eingabeaufforderung öffnen, damit die Änderungen angezeigt werden.

Führen Sie anschließend die folgenden Schritte aus:

1. Erstellen einer Initialisierungsparameterdatei für die Standbydatenbank

2. Konfigurieren von Listener und TNSNAMES für die Unterstützung der Datenbank auf dem primären und Standbycomputer

	1. Konfigurieren von "listener.ora" auf beiden Servern zum Speichern von Einträgen für beide Datenbanken

	2. Konfigurieren von "tnsnames.ora" auf dem primären virtuellen Computer und virtuellen Standbycomputer zum Speichern von Einträgen für die primäre und Standbydatenbank

	3. Starten des Listeners und Testen von "tnsping" auf beiden virtuellen Computern für beide Dienste

3. Starten der Standbyinstanz im Status "NOMOUNT"

4. Verwenden von RMAN zum Klonen der Datenbank und Erstellen einer Standbydatenbank

5. Starten der physischen Standbydatenbank im verwalteten Wiederherstellungsmodus

6. Überprüfen der physischen Standbydatenbank

### 1\. Erstellen einer Initialisierungsparameterdatei für die Standbydatenbank

In diesem Abschnitt wird veranschaulicht, wie Sie eine Initialisierungsparameterdatei für die Standbydatenbank erstellen. Kopieren Sie zu diesem Zweck zunächst die Datei "INITTEST.ORA" manuell von Machine1 auf Machine2. Auf beiden Computern sollte die Datei "INITTEST.ORA" im Ordner "%ORACLE\_HOME%\\database" angezeigt werden. Ändern Sie dann die Datei "INITTEST.ORA" auf Machine2 wie folgt, um die Datenbank als Standbydatenbank einzurichten:
	
	db_name='TEST'
	db_unique_name='TEST_STBY'
	db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
	db_file_name_convert=’TEST’,’TEST_STBY’
	log_file_name_convert='TEST','TEST_STBY'
	
	
	job_queue_processes=10
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
	LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) 
	LOG_ARCHIVE_DEST_STATE_1='ENABLE'
	LOG_ARCHIVE_DEST_STATE_2='ENABLE'
	LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
	LOG_ARCHIVE_MAX_PROCESSES=30


Der vorherige Anweisungsblock enthält zwei wichtige Setupelemente:

-	***.LOG\_ARCHIVE\_DEST\_1:** Sie müssen den Ordner "C:\\OracleDatabase\\TEST\_STBY\\archives" auf Machine2 manuell erstellen.
-	***.LOG\_ARCHIVE\_DEST\_2:** Dies ist ein optionaler Schritt. Sie legen diese Einstellung fest, da sie möglicherweise erforderlich ist, wenn der primäre Computer gewartet wird und aus dem Standbycomputer eine primäre Datenbank wird.

Anschließend müssen Sie die Standbyinstanz starten. Geben Sie auf dem Standbydatenbankserver an einer Windows-Eingabeaufforderung den folgenden Befehl ein, um durch das Erstellen eines neuen Windows-Diensts eine Oracle-Instanz zu erstellen:

	oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

Beachten Sie, dass mit dem Befehl **oradim** eine Oracle-Instanz erstellt, jedoch nicht gestartet wird. Sie finden ihn im Verzeichnis "C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\BIN".

##Konfigurieren von Listener und TNSNAMES für die Unterstützung der Datenbank auf dem primären und Standbycomputer
Bevor Sie eine Standbydatenbank erstellen, müssen Sie sicherstellen, dass die primäre und Standbydatenbank in der Konfiguration miteinander kommunizieren können. Zu diesem Zweck müssen Sie den Listener und TNSNAMES manuell oder mit dem Netzwerkkonfigurationsprogramm NETCA konfigurieren. Dies ist eine obligatorische Aufgabe, wenn Sie das Hilfsprogramm Wiederherstellungs-Manager (RMAN) verwenden.

### Konfigurieren von "listener.ora" auf beiden Servern zum Speichern von Einträgen für beide Datenbanken

Stellen Sie eine Remotedesktopverbindung mit Machine1 her, und bearbeiten Sie die Datei "listener.ora" wie unten gezeigt. Wenn Sie die Datei "listener.ora" bearbeiten, stellen Sie immer sicher, dass die öffnende und schließende Klammer in der gleichen Spalte angeordnet sind. Sie finden die Datei "listener.ora" im folgenden Ordner: "C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\".

	# listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora
	
	# Generated by Oracle configuration tools.
	
	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )
	
	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )

Stellen Sie anschließend eine Remotedesktopverbindung mit Machine2 her, und bearbeiten Sie die Datei "listener.ora" Datei wie folgt: # listener.ora Network Configuration File: "C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\network\\admin\\listener.ora"
	
	# Generated by Oracle configuration tools.
	
	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test_stby)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )
	
	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )


### Konfigurieren von "tnsnames.ora" auf dem primären virtuellen Computer und virtuellen Standbycomputer zum Speichern von Einträgen für die primäre und Standbydatenbank

Stellen Sie eine Remotedesktopverbindung mit Machine1 her, und bearbeiten Sie die Datei "tnsnames.ora" wie unten gezeigt. Sie finden die Datei "tnsnames.ora" im folgenden Ordner: "C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\".

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )
	
	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )

Stellen Sie eine Remotedesktopverbindung mit Machine2 her, und bearbeiten Sie die Datei "tnsnames.ora" wie folgt:
	
	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )
	
	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )


### Starten des Listeners und Testen von "tnsping" auf beiden virtuellen Computern für beide Dienste

Öffnen Sie auf dem primären Computer und virtuellen Standbycomputer eine neue Windows-Eingabeaufforderung, und führen Sie die folgenden Anweisungen aus:

	C:\Users\DBAdmin>tnsping test
	
	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test)))
	OK (0 msec)
	

	C:\Users\DBAdmin>tnsping test_stby
	
	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test_stby)))
	OK (260 msec)


##Starten der Standbyinstanz im Status "NOMOUNT"
Sie müssen die Umgebung so einrichten, dass die Standbydatenbank auf dem virtuellen Standbycomputer (MACHINE2) unterstützt wird.

Kopieren Sie zunächst die Kennwortdatei manuell vom primären Computer (Machine1) auf den Standbycomputer (Machine2). Dies ist erforderlich, da das **SYS**-Kennwort auf beiden Computern identisch sein muss.

Öffnen Sie dann auf Machine2 die Windows-Eingabeaufforderung, und legen Sie wie folgt fest, dass die Umgebungsvariablen auf die Standbydatenbank verweisen:

	SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
	SET ORACLE_SID=TEST_STBY

Starten Sie dann die Standbydatenbank im Status "NOMOUNT", und generieren Sie dann eine SPFILE-Datei.

Starten Sie die Datenbank:

	SQL>shutdown immediate;
	
	SQL>startup nomount
	ORACLE instance started.
	
	Total System Global Area  747417600 bytes
	Fixed Size                  2179496 bytes
	Variable Size             473960024 bytes
	Database Buffers          264241152 bytes
	Redo Buffers                7036928 bytes


##Verwenden von RMAN zum Klonen der Datenbank und Erstellen einer Standbydatenbank
Sie können das Hilfsprogramm Wiederherstellungs-Manager (RMAN) verwenden, um die physische Standbydatenbank mithilfe einer beliebigen Sicherungskopie der primären Datenbank zu erstellen.

Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Standbycomputer (MACHINE2) her, und führen Sie das Hilfsprogramm RMAN aus, indem Sie eine vollständige Verbindungszeichenfolge für die TARGET-Instanz (primäre Datenbank, Machine1) und AUXILIARY-Instanz (Standbydatenbank, Machine2) erstellen.

>[AZURE.IMPORTANT]Verwenden Sie nicht die Betriebssystemauthentifizierung, da sich auf dem Standbyservercomputer noch keine Datenbank befindet.

	C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY
	
	RMAN>DUPLICATE TARGET DATABASE
	  FOR STANDBY
	  FROM ACTIVE DATABASE
	  DORECOVER
	    NOFILENAMECHECK;

##Starten der physischen Standbydatenbank im verwalteten Wiederherstellungsmodus
Dieses Lernprogramm veranschaulicht das Erstellen einer physischen Standbydatenbank. Informationen zum Erstellen einer logischen Standbydatenbank finden Sie in der Oracle-Dokumentation.

Öffnen Sie eine SQL*Plus-Eingabeaufforderung, und aktivieren Sie auf dem virtuellen Standbycomputer oder -server (MACHINE2) Data Guard wie folgt:

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Wenn Sie die Standbydatenbank im Modus **MOUNT** öffnen, wird der Versand des Archivprotokolls fortgesetzt, und der verwaltete Wiederherstellungsprozess wendet das Protokoll weiter auf die Standbydatenbank an. Dadurch wird sichergestellt, dass die Standbydatenbank mit der primären Datenbank aktualisiert bleibt. Beachten Sie, dass während dieser Zeit kein Zugriff auf die Standbydatenbank für Berichtszwecke möglich ist.

Wenn Sie die Standbydatenbank im Modus **READ ONLY** öffnen, wird der Versand des Archivprotokolls fortgesetzt. Der verwaltete Wiederherstellungsprozess wird jedoch beendet. Dadurch wird die Standbydatenbank zunehmend veraltet, bis der verwaltete Wiederherstellungsprozess fortgesetzt wird. Sie können während dieser Zeit zu Berichtszwecken auf die Standbydatenbank zugreifen, jedoch geben die Daten möglicherweise nicht die neuesten Änderungen wieder.

Im Allgemeinen wird empfohlen, die Standbydatenbank im Modus **MOUNT** auszuführen, damit bei einem Ausfall der primären Datenbank die Daten in der Standbydatenbank aktuell bleiben. Sie können jedoch abhängig von den Anforderungen der Anwendung die Standbydatenbank zu Berichtszwecken im Modus **READ ONLY** ausführen. Die folgenden Schritte veranschaulichen, wie Data Guard mithilfe von SQL*Plus im schreibgeschützten Modus aktiviert wird:

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE OPEN READ ONLY;


##Überprüfen der physischen Standbydatenbank
In diesem Abschnitt wird veranschaulicht, wie Sie als Administrator die Hochverfügbarkeitskonfiguration überprüfen.

Öffnen Sie ein SQL*Plus-Eingabeaufforderungsfenster, und überprüfen Sie das archivierte Redo Log auf dem virtuellen Standbycomputer (Machine2):

	SQL> show parameters db_unique_name;
	
	NAME                                TYPE       VALUE
	------------------------------------ ----------- ------------------------------
	db_unique_name                      string     TEST_STBY
	
	SQL> SELECT NAME FROM V$DATABASE
	
	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;
	
	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	45                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   NO
	47                    23-FEB-14   23-FEB-14   NO

Öffnen Sie ein SQL*Plus-Eingabeaufforderungsfenster, und wechseln Sie die Protokolldateien auf dem primären Computer (Machine1):

	SQL> alter system switch logfile; 
	System altered.
	
	SQL> archive log list
	Database log mode              Archive Mode
	Automatic archival             Enabled
	Archive destination            C:\OracleDatabase\archive
	Oldest online log sequence     69
	Next log sequence to archive   71
	Current log sequence           71

Überprüfen Sie das archivierte Redo Log auf dem virtuellen Standbycomputer (Machine2):

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;
	
	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   YES
	48                    23-FEB-14   23-FEB-14   YES
	
	49                    23-FEB-14   23-FEB-14   YES
	50                    23-FEB-14   23-FEB-14   IN-MEMORY

Überprüfen Sie den virtuellen Standbycomputer (Machine2) auf Lücken:

	SQL> SELECT * FROM V$ARCHIVE_GAP;
	no rows selected.

Als alternatives Überprüfungsverfahren kann ein Failover auf die Standbydatenbank durchgeführt und dann getestet werden, ob ein Failback zur primären Datenbank möglich ist. Verwenden Sie zum Aktivieren der Standbydatenbank als primäre Datenbank die folgenden Anweisungen:

	SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
	SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Wenn Sie für die ursprüngliche primäre Datenbank nicht Flashback aktiviert haben, wird empfohlen, die ursprüngliche primäre Datenbank zu löschen und als Standbydatenbank neu zu erstellen.

Es wird empfohlen, für die primäre und Standbydatenbank "flashback database" zu aktivieren. Wenn ein Failover erfolgt, kann die primäre Datenbank in dem vor dem Failover vorhandenen Zustand wiederhergestellt und schnell in eine Standbydatenbank umgewandelt werden.

##Zusätzliche Ressourcen
[Images des virtuellen Oracle-Computers für Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=August15_HO6-->