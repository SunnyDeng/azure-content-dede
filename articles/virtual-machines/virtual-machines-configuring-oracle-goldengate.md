<properties
	pageTitle="Konfigurieren von Oracle GoldenGate auf virtuellen Computern | Microsoft Azure"
	description="Bearbeiten Sie ein Lernprogramm für das Einrichten und Implementieren von Oracle GoldenGate auf Azure Windows Server-VMs für hohe Verfügbarkeit und Notfallwiederherstellung."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />
#Konfigurieren von Oracle-GoldenGate für Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Verwalten einer Ressource, die mit dem klassischen Bereitstellungsmodell erstellt wurde.

Dieses Lernprogramm zeigt, wie Sie Oracle GoldenGate für virtuelle Computer in Azure-Umgebung für hohe Verfügbarkeit und Notfallwiederherstellung einrichten. Das Lernprogramm konzentriert sich auf [bidirektionale Replikation](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) für nicht-RAC Oracle-Datenbanken und erfordert, dass beide Standorte aktiv sind.

Oracle-GoldenGate unterstützt die Datenverteilung und Datenintegration. Es wird Ihnen ermöglicht, eine Datenverteilung und Datensynchronisierungslösung durch die Konfiguration der Oracle-Oracle-Replikation einzurichten, und eine flexible Lösung mit hoher Verfügbarkeit geboten. Oracle-GoldenGate ergänzt Oracle Data Guard dank seiner Replikationsfunktionen, um die unternehmensweite Verteilung von Informationen sowie Upgrades und Migrationen ohne Ausfallzeiten zu ermöglichen. Ausführliche Informationen finden Sie unter [Verwenden von Oracle-GoldenGate mit Oracle Data Guard](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle-GoldenGate enthält die folgenden Hauptkomponenten: Extrahieren, Datapump, Replikat, Pfad- oder Extrahierungsdateien, Prüfpunkte, Manager und Collector. Um die bidirektionale Replikation zwischen zwei Standorten zu erhalten, müssen Sie alle Komponenten an beiden Standorten erstellen und starten. Ausführliche Informationen zur Architektur von Oracle-GoldenGate finden Sie unter [Oracle GoldenGate Handbuch](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

In diesem Lernprogramm wird davon ausgegangen, dass Sie bereits theoretische und praktische Kenntnisse zu Konzepten der Hochverfügbarkeit und Notfallwiederherstellung der Oracle-Datenbank sowie [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) besitzen. Weitere Informationen finden Sie auf der [Oracle-Website](http://www.oracle.com/technetwork/database/features/availability/index.html).

Das Lernprogramm geht zudem davon aus, dass Sie die folgenden Voraussetzungen bereits implementiert haben:

- Sie haben bereits den Abschnitt mit Überlegungen zu Hochverfügbarkeit und Notfallwiederherstellung im Thema [Images virtueller Oracle-Computer – verschiedene Überlegungen](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md) gelesen. Beachten Sie, dass Azure derzeit eigenständige Oracle-Datenbankinstanzen, aber keine Oracle Real Application Clusters (Oracle RAC) unterstützt.

- Sie haben die Oracle-GoldenGate-Software von der Website [Downloads von Oracle](http://www.oracle.com/us/downloads/index.html) heruntergeladen. Sie haben das Produkt Pack Oracle Fusion-Middleware – Datenintegration ausgewählt. Sie haben dann Oracle GoldenGate auf Oracle v11.2.1 Media Pack für Microsoft Windows x64 (64-Bit) für eine Oracle-Datenbank 11g aktiviert. Laden Sie als Nächstes Oracle GoldenGate V11.2.1.0.3 für Oracle 11g 64-Bit unter Windows 2008 (64 Bit) herunter.

- Sie haben zwei virtuelle Computer (VMs) in Azure mit dem von der Plattform bereitgestellten Abbild von Oracle Enterprise Edition auf Windows Server erstellt. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Oracle-Datenbank 12c-Computers in Azure](#z3dc8d3c097cf414e9048f7a89c026f80) und [Azure Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/). Stellen Sie sicher, dass sich die virtuellen Computer im [gleichen Clouddienst](virtual-machines-load-balance.md) und im gleichen [virtuellen Netzwerk](http://azure.microsoft.com/documentation/services/virtual-network/) befinden, um sicherzustellen, dass sie über die permanente private IP-Adresse aufeinander zugreifen können.

- Sie haben die Namen der virtuellen Computer für Standort A als "MachineGG1" und als "MachineGG2" für Standort B im Azure-Portal festgelegt.

- Sie haben die Testdatenbanken "TestGG1" für Standort A und "TestGG2" für Standort B erstellt.

- Sie melden Sie sich beim Windows-Server als Mitglied der Gruppe "Administratoren" oder als Mitglied der Gruppe **ORA\_DBA** an.

In diesem Lernprogramm lernen Sie Folgendes:

1. Einrichten der Datenbank an Standort A und Standort B  

	1. Durchführen der anfänglichen Datenlast

2. Vorbereiten von Standort A und Standort B für die Datenbankreplikation

3. Erstellen aller erforderlichen Objekte zur Unterstützung der DDL-Replikation

4. Konfigurieren von GoldenGate-Manager an Standort A und Standort B

5. Erstellen von Extrahierungsgruppen- und Datapump-Prozessen an Standort A und Standort B

	1. Erstellen von Extrahierungs- und Datapump-Prozessen an Standort A und Standort B

	2. Erstellen Sie eine GoldenGate Prüfpunkt-Tabelle an Standort B

	3. Hinzufügen von REPLICAT an Standort B

	4. Erstellen von Extrahierungs- und Datapump-Prozessen an Standort B

	5. Erstellen einer GoldenGate Prüfpunkt-Tabelle an Standort A

	6. Hinzufügen von REPLICAT an Standort A

	7. Hinzufügen von trandata für Standort A und Standort B

	8. Starten von Extrahierungs- und Datapump-Prozessen an Standort A

	9. Starten von Extrahierungs- und Datapump-Prozessen an Standort B

	10. Starten von REPLIKAT-Prozess an Standort A

	11. Starten von REPLIKAT-Prozess an Standort B

6. Überprüfen des bidirektionalen Replikationsprozesses

>[AZURE.IMPORTANT]Dieses Lernprogramm wurde mit der folgenden Softwarekonfiguration eingerichtet und getestet:
>
>| | **Standort A Datenbank** | **Standort B Datenbank** |
>|------------------------|----------------------------------|----------------------------------|
>| **Oracle-Version** | Oracle11g Version 2 – (11.2.0.1) | Oracle11g Version 2 – (11.2.0.1) |
>| **Computername** | MachineGG1 | MachineGG2 |
>| **Betriebssystem** | Windows 2008 R2 | Windows 2008 R2 |
>| **Oracle SID** | TESTGG1 | TESTGG2 |
>| **Replikationsschema** | SCOTT | SCOTT |

Nachfolgende Versionen von Oracle-Datenbanken und Oracle-GoldenGate enthalten möglicherweise einige zusätzlichen Änderungen, die Sie implementieren müssen. Bestimmte Informationen zur neuesten Version finden Sie in der Dokumentation zu [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) und [Oracle-Datenbank](http://www.oracle.com/us/corporate/features/database-12c/index.html) auf der Oracle-Website. Für eine Quelldatenbank Version 11.2.0.4 und höher wird z. B. die Erfassung von DDL vom Server Logmining asynchron ausgeführt und erfordert keine Installation besonderer Trigger, Tabellen oder anderer Datenbankobjekte. Oracle-GoldenGate-Upgrades können ohne Unterbrechung von Benutzeranwendungen ausgeführt werden. Die Verwendung eines DDL-Triggers und unterstützender Objekte ist beim Extrahieren im integrierten Modus mit einer Oracle 11 g-Quelldatenbank, die älter als Version 11.2.0.4 ist, erforderlich. Ausführliche Anweisungen finden Sie unter [Installieren und Konfigurieren von Oracle GoldenGate für Oracle-Datenbank](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##1\. Einrichten der Datenbank an Standort A und Standort B
In diesem Abschnitt wird erläutert, wie Sie die Datenbankvoraussetzungen für Standort A und Standort B ausführen. Sie müssen alle Schritte in diesem Abschnitt an beiden Standorten ausführen: Standort A und Standort B.

Verwenden Sie zuerst Remotedesktop an Standort A und Standort B über das Verwaltungsportal. Öffnen Sie eine Windows-Eingabeaufforderung, und erstellen Sie ein Basisverzeichnis für Oracle-GoldenGate-Setup-Dateien:

	mkdir C:\OracleGG

Extrahieren und installieren Sie dann die Oracle-GoldenGate-Software in diesem Ordner. Nach diesem Schritt können Sie die GoldenGate-Software-Befehlsinterpreter (GGSCI) initiieren, indem Sie folgenden Befehl ausführen:

	C:\OracleGG\.\ggsci

Sie können [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) verwenden, um mehrere Befehle auszuführen, die Oracle-GoldenGate konfigurieren, steuern und überwachen.

Führen Sie dann den folgenden Befehl aus, um alle Unterordner aus dem Installationspaket zu erstellen:

	GGSCI (Hostname) 1> CREATE SUBDIRS

Geben Sie in der Befehlszeile Folgendes ein, um die GGSCI-Eingabeaufforderung zu beenden:

	GGSCI (Hostname) 1> EXIT

Anschließend müssen Sie einen Datenbankbenutzer erstellen, der von den Prozessen Oracle GoldenGate Manager, Extrahierung und Replikation verwendet wird. Beachten Sie, dass Sie entweder einzelne Benutzer für jeden Prozess erstellen oder nur einen allgemeinen Benutzer konfigurieren können. In diesem Lernprogramm erstellen wir einen Benutzer, der als ggate aufgerufen wird. Anschließend werden diesem Benutzer die erforderlichen Berechtigungen erteilt. Beachten Sie, dass Sie die folgenden Vorgänge an Standort A und Standort B ausführen müssen.

Öffnen Sie das SQL* Plus-Eingabeaufforderungsfenster mit Administratorrechten für die Datenbank, z. B. **SYSDBA** an Standort A und Standort B:

	Enter username: / as sysdba

Und führen Sie folgendes aus:

	SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata<DBNAME><DBNAME>ggs_data01.dbf' size 200m;
	SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
	      grant connect, resource to ggate;
	      grant select any dictionary, select any table to ggate;
	      grant create table to ggate;
	      grant flashback any table to ggate;
	      grant execute on dbms_flashback to ggate;
	      grant execute on utl_file to ggate;
	      grant create any table to ggate;
	      grant insert any table to ggate;
	      grant update any table to ggate;
	      grant delete any table to ggate;
	      grant drop any table to ggate;

Suchen Sie als Nächstes die Datei INIT <DatabaseSID>. ORA im Ordner %ORACLE\_HOME%\\database an Standort A und Standort B, und fügen Sie die folgenden Datenbankparameter zu INITTEST.ora hinzu:

	UNDO\_MANAGEMENT=AUTO
	UNDO\_RETENTION=86400

Eine vollständige Liste aller Oracle GoldenGate GGSCI-Befehle finden Sie unter [Referenz für Oracle GoldenGate für Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### Durchführen der anfänglichen Datenlast

Sie können die anfänglichen Datenlast in der Datenbank durch das Befolgen mehrerer Methoden ausführen. Beispielsweise können Sie die [Direktes Laden von Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) oder regulären Export und Import-Dienstprogramme zum Exportieren von Tabellendaten von Standort A an Standort B nutzen.

Um den Replikationsprozess von Oracle GoldenGate zu demonstrieren, veranschaulicht dieses Lernprogramms das Erstellen einer Tabelle an Standort A und Standort B mit den folgenden Befehlen.

Öffnen Sie zunächst das SQL*Plus-Eingabeaufforderungsfenster, und führen Sie den folgenden Befehl aus, um eine Bestandstabelle für die Datenbanken von Standort A und Standort B zu erstellen:

	create table scott.inventory
	(prod_id number,
	prod_category varchar2(20),
	qty_in_stock number,
	last_dml timestamp default systimestamp);

Fügen Sie als Nächstes eine Einschränkung zur neu erstellten Tabelle der Datenbanken von Standort A und Standort B hinzu:

	alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Gewähren Sie dann alle Berechtigungen für die neue Bestandstabelle an Standort A und Standort B dem Benutzer ggate:

	grant all on scott.inventory to ggate;

Erstellen und aktivieren Sie als Nächstes einen Datenbanktrigger, INVENTORY\_CDR\_TRG, auf der neu erstellten Tabelle, um sicherzustellen, dass alle Transaktionen in der neuen Tabelle erfasst werden, wenn der Benutzer nicht ggate ist. Führen Sie diesen Vorgang an Standort A und Standort B aus.

	CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
	BEFORE UPDATE
	ON SCOTT.INVENTORY
	REFERENCING NEW AS New OLD AS Old
	FOR EACH ROW
	BEGIN
	IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
	THEN
	:NEW.LAST_DML := SYSTIMESTAMP;
	END IF;
	END;
	/


##2\. Vorbereiten von Standort A und Standort B für die Datenbankreplikation
Dieser Abschnitt beschreibt das Vorbereiten von Standort A und Standort B für die Datenbankreplikation. Sie müssen alle Schritte in diesem Abschnitt an beiden Standorten ausgeführt: Standort A und Standort B.

Verwenden Sie zuerst Remotedesktop an Standort A und Standort B über das Azure-Portal. Wechseln Sie die Datenbank unter Verwendung der SQL* Plus-Eingabeaufforderung in den Archivelog-Modus:

	sql>shutdown immediate
	sql>startup mount
	sql>alter database archivelog;
	sql>alter database open;


Aktivieren Sie dann die minimale [ergänzende Protokollierung](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) wie folgt:

	SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Bereiten Sie als Nächstes die Datenbank zur Unterstützung der Replikation von DDL (Data Definition Language) vor:

	SQL> alter system set recyclebin=off scope=spfile;

Fahren Sie anschließend die Datenbank herunter und starten Sie sie neu:

	sql>shutdown immediate
	sql>startup


##3\. Erstellen aller erforderlichen Objekte zur Unterstützung der DDL-Replikation
Dieser Abschnitt enthält die Skripte, die Sie verwenden müssen, um alle erforderlichen Objekte zur Unterstützung der DDL-Replikation zu erstellen. Sie müssen die Skripte in diesem Abschnitt sowohl an Standort A als auch an Standort B ausführen.

Öffnen Sie eine Windows-Eingabeaufforderung, und navigieren Sie zu den Oracle-GoldenGate-Ordner, z. B. C:\\OracleGG. Starten Sie SQL* Plus-Eingabeaufforderung mit Administratorrechten für die Datenbank, z. B. **SYSDBA** auf Standort A und Standort B.

Führen Sie dann die folgende Skripte aus:

	SQL> @marker_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @ddl_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @role_setup.sql
	Enter GoldenGate schema name: ggate
	SQL> grant ggs_ggsuser_role to ggate;
	 Grant succeeded.
	 SQL> @ddl_enable
	 Trigger altered.
	 SQL> @ddl_pin ggate


Das Oracle GoldenGate-Werkzeug erfordert für DDL (Data Definition Language)-Unterstützung eine Anmeldung auf Tabellenebene. Aktivieren Sie darum ergänzende Protokollierung auf Tabellenebene mit dem Befehl ADD TRANDATA. Öffnen Sie das Oracle GoldenGate Befehlsinterpreterfenster, melden Sie sich bei der Datenbank an und führen Sie den Befehl ADD TRANDATA aus:

	GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

	GGSCI(Hostname) 6> add trandata scott.inventory

##4\. Konfigurieren von GoldenGate-Manager an Standort A und Standort B
Der Oracle-GoldenGate-Manager führt eine Reihe von Funktionen durch, wie das Starten anderer GoldenGate-Prozesse, Pfadprotokolldateiverwaltung und Berichterstellung.

Sie müssen den Oracle-GoldenGate-Manager-Prozess an Standort A und Standort B konfigurieren. Führen Sie zu diesem Zweck folgende Schritte an Standort A und Standort B durch.

Öffnen Sie eine Windows- Eingabeaufforderung und initiieren Sie den Befehlsinterpreter von Oracle GoldenGate:

	cd C:\OracleGG\
	c:\OracleGG>ggsci
	Oracle GoldenGate Command Interpreter for Oracle
	Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
	Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
	Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Protokolliert die GGSCI-Sitzung in einer Datenbank, sodass Sie Befehle ausführen können, die Auswirkungen auf die Datenbank haben:

	GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

Zeigt den Status und die Verzögerung (sofern relevant) für alle Manager-, Extrahierungs- und Replikationsprozesse auf einem System:

	GGSCI (HostName) 2> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

Öffnen Sie die Parameterdatei mit dem Befehl EDIT PARAMS, und fügen Sie dann die folgende Informationen hinzu:

	GGSCI (HostName) 3> edit params mgr
	PORT 7809
	USERID ggate, PASSWORD ggate
	PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Zeigt den Status und die Verzögerung (sofern relevant) für alle Manager-, Extrahierungs- und Replikationsprozesse auf einem System:

	GGSCI (HostName) 46> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

Protokolliert die GGSCI-Sitzung in einer Datenbank, sodass Sie Befehle ausführen können, die Auswirkungen auf die Datenbank haben:

	GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

	Successfully logged into database.

Starten Sie den Manager-Prozess:

	GGSCI (HostName) 48> start manager
	Manager started.

##5\. Erstellen von Extrahierungsgruppen- und Datapump-Prozessen an Standort A und Standort B

###Erstellen von Extrahierungs- und Datapump-Prozessen an Standort A und Standort B

Sie müssen die Prozesse Extrahieren und Datapump an Standort A und Standort B erstellen. Remotedesktop an Standort A und Standort B über das Verwaltungsportal. GGSCI-Befehlsinterpreterfenster öffnen. Führen Sie die folgenden Befehle an Standort A aus:

	GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
	EXTRACT added.
	GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
	EXTTRAIL added.
	GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
	EXTRACT added.
	GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
	RMTTRAIL added.

Öffnen Sie die Parameterdatei mit dem Befehl EDIT PARAMS, und fügen Sie dann die folgende Informationen hinzu: GGSCI (MachineGG1) 18> edit params ext1 EXTRACT ext1 USERID ggate, PASSWORD ggate EXTTRAIL C:\\OracleGG\\dirdat\\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS ( ON UPDATE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml), ON DELETE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml));

Öffnen Sie die Parameterdatei mit dem Befehl EDIT PARAMS, und fügen Sie dann die folgende Informationen hinzu:

	GGSCI (MachineGG1) 15> edit params dpump1
	EXTRACT dpump1
	 USERID ggate, PASSWORD ggate
	 RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
	 RMTTRAIL C:\OracleGG\dirdat\ab
	 PASSTHRU
	 TABLE scott.inventory;

###Erstellen Sie eine GoldenGate Prüfpunkt-Tabelle an Standort B

Als Nächstes müssen Sie eine Prüfpunkt-Tabelle an Standort B hinzufügen. Zu diesem Zweck müssen Sie ein GoldenGate-Befehlsinterpreterfenster öffnen und folgendes ausführen:

	C:\OracleGG\ggsci
	GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

Fügen Sie dann die Prüfpunkt-Tabelle der Datenbank hinzu, wobei ggate der Besitzer ist:

	GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
	Successfully created checkpoint table ggate.checkpointtable.

Fügen Sie den Namen der Prüfpunkt-Tabelle zur Datei "GLOBALS" auf dem Zielserver hinzu, in diesem Schritt handelt es sich dabei um Standort B. Bearbeiten Sie die Datei "GLOBALS" an Standort B:

	GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Fügen Sie dann den Parameter CHECKPOINTTABLE" an die vorhandene Datei "GLOBALS" an:

	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

Speichern Sie als letzten Schritt und schließen Sie die Parameterdatei "GLOBALS".


###Hinzufügen von REPLICAT an Standort B
Dieser Abschnitt beschreibt, wie Sie einen REPLICAT-Prozess "REP2" an Standort B hinzufügen.

Verwenden Sie den Befehl ADD REPLICAT zum Erstellen einer Replikatgruppe an Standort B:

	GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Öffnen Sie die Parameterdatei mit dem Befehl EDIT PARAMS, und fügen Sie dann die folgende Informationen hinzu:

	GGSCI (MachineGG2) 10> edit params rep2
	REPLICAT rep2
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

###Erstellen von Extrahierungs- und Datapump-Prozessen an Standort B

In diesem Abschnitt wird beschrieben, wie Sie einen neuen Extrahierungsprozess "EXT2" und einen neuen Datapump-Prozess "DPUMP2" an Standort B erstellen:

	GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
	 EXTRACT added.
	GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
	 EXTTRAIL added.
	GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
	 EXTRACT added.
	GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
	 RMTTRAIL added.

Öffnen Sie die Parameterdatei mit dem Befehl EDIT PARAMS, und fügen Sie dann die folgende Informationen hinzu:

	GGSCI (MachineGG2) 31> edit params ext2
	EXTRACT ext2
	USERID ggate, PASSWORD ggate
	EXTTRAIL C:\OracleGG\dirdat\ac
	TRANLOGOPTIONS EXCLUDEUSER ggate
	TABLE scott.inventory,
	GETBEFORECOLS (
	ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
	ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Öffnen Sie die Parameterdatei mit dem Befehl EDIT PARAMS, und fügen Sie dann die folgende Informationen hinzu:

	GGSCI (MachineGG2) 32> edit params dpump2
	EXTRACT dpump2
	USERID ggate, PASSWORD ggate
	RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
	RMTTRAIL C:\OracleGG\dirdat\ad
	PASSTHRU
	TABLE scott.inventory;

###Erstellen einer GoldenGate Prüfpunkt-Tabelle an Standort A

Öffnen Sie das Oracle GoldenGate Befehlsinterpreterfenster, und erstellen Sie eine Prüfpunkt-Tabelle:

	GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

	GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

	Successfully created checkpoint table ggate.checkpointtable.

Außerdem müssen Sie den Namen der Prüfpunkt-Tabelle zur Datei "GLOBALS" an Standort A hinzufügen.

Öffnen Sie das Oracle GoldenGate Befehlsinterpreterfenster, und bearbeiten Sie die Datei "GLOBALS" an Standort A:

	GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
	Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

Speichern Sie und schließen Sie die Parameterdatei "GLOBALS".

###Hinzufügen von REPLICAT an Standort A

Dieser Abschnitt beschreibt wie Sie einen REPLICAT-Prozess "REP1" an Standort A hinzufügen.

Der folgende Befehl erstellt eine Replikatgruppe rep1 mit dem Namen eines Pfads und der zugehörigen Prüfpunkt-Tabelle.

	GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
	 REPLICAT added.

Öffnen Sie die Parameterdatei mit dem Befehl EDIT PARAMS, und fügen Sie dann die folgende Informationen hinzu:

	GGSCI (MachineGG1) 10> edit params rep1
	REPLICAT rep1
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

### Hinzufügen von trandata für Standort A und Standort B

Aktivieren Sie ergänzende Protokollierung auf Tabellenebene mit dem Befehl ADD TRANDATA. Öffnen Sie das Oracle GoldenGate Befehlsinterpreterfenster, melden Sie sich bei der Datenbank an und führen Sie den Befehl ADD TRANDATA aus.

Remotedesktop auf MachineGG1, öffnen Sie den Oracle GoldenGate-Befehlsinterpreter geöffnet, und führen Sie folgendes aus:

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Remotedesktop auf MachineGG2, öffnen Sie den Oracle GoldenGate-Befehlsinterpreter geöffnet, und führen Sie folgendes aus:

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Zeigt Informationen zum Status der ergänzenden Protokollierung der Tabellenebene:

	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###Hinzufügen von trandata für Standort A und Standort B

Aktivieren Sie ergänzende Protokollierung auf Tabellenebene mit dem Befehl ADD TRANDATA. Öffnen Sie das Oracle GoldenGate Befehlsinterpreterfenster, melden Sie sich bei der Datenbank an und führen Sie den Befehl ADD TRANDATA aus.

Remotedesktop auf MachineGG1, öffnen Sie den Oracle GoldenGate-Befehlsinterpreter geöffnet, und führen Sie folgendes aus:

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Remotedesktop auf MachineGG2, öffnen Sie den Oracle GoldenGate-Befehlsinterpreter geöffnet, und führen Sie folgendes aus:

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
	Display information about the state of table-level supplemental logging:
	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###Starten von Extrahierungs- und Datapump-Prozessen an Standort A

Starten Sie den Extrahierungsprozess ext1 an Standort A:

	GGSCI (MachineGG1) 31> start extract ext1
	Sending START request to MANAGER …
	EXTRACT EXT1 starting

Starten Sie den Datapumpprozess dpump1 an Standort A:

	GGSCI (MachineGG1) 23> start extract dpump1
	Sending START request to MANAGER …
	EXTRACT DPUMP1 starting
Anzeigen von Informationen der Extrahierungsgruppe ext1: GGSCI (MachineGG1) 32> info extract ext1 EXTRACT EXT1 Zuletzt gestartet 2013-11-25 08:03 Status LÄUFT Prüfpunktverzögerung 00:00:00 (vor 00:00:02 aktualisiert) Protokoll Prüfpunkt Oracle Redo-Protokolle 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Zeigt den Status und die Verzögerung (sofern relevant) für alle Manager-, Extrahierungs- und Replikationsprozesse auf einem System:

	GGSCI (MachineGG1) 16> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
	EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###Starten von Extrahierungs- und Datapump-Prozessen an Standort B

Starten Sie den Extrahierungsprozess ext2 an Standort B:

	GGSCI (MachineGG2) 22> start extract ext2
	Sending START request to MANAGER …
	EXTRACT EXT2 starting

Starten Sie den Datapumpprozess dpump2 an Standort B:

	GGSCI (MachineGG2) 23> start extract dpump2
	Sending START request to MANAGER …
	EXTRACT DPUMP2 starting

Zeigt den Status und die Verzögerung (sofern relevant) für alle Manager-, Extrahierungs- und Replikationsprozesse auf einem System:

	GGSCI (ActiveGG2orcldb) 6> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
	EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### Starten von REPLIKAT-Prozess an Standort A

Dieser Abschnitt beschreibt wie Sie einen REPLICAT-Prozess "REP1" an Standort A starten.

Starten des Replikatprozesses an Standort A:

	GGSCI (MachineGG1) 38> start replicat rep1
	Sending START request to MANAGER …
	REPLICAT REP1 starting

Anzeigen des Status einer Replikatgruppe:

	GGSCI (MachineGG1) 39> status replicat rep1
	 REPLICAT REP1: RUNNING

###Starten von REPLIKAT-Prozess an Standort B

Dieser Abschnitt beschreibt wie Sie einen REPLICAT-Prozess "REP2" an Standort B starten.

Starten des Replikatprozesses an Standort B:

	GGSCI (MachineGG2) 26> start replicat rep2
	Sending START request to MANAGER …
	REPLICAT REP2 starting

Anzeigen des Status einer Replikatgruppe:

	GGSCI (MachineGG2) 27> status replicat rep2
	REPLICAT REP2: RUNNING

##6\. Überprüfen des bidirektionalen Replikationsprozesses

Legen Sie zum Überprüfen der Konfiguration von Oracle-GoldenGate eine Zeile in der Datenbank an Standort A an. Remote Desktop an Standort A. Öffnen Sie SQL* Plus-Eingabeaufforderung, und führen Sie folgendes aus: SQL> select name from v$database;

	NAME
	———
	TESTGG

	SQL> insert into inventory values  (100,’TV’,100,sysdate);

	1 row created.

	SQL> commit;

	Commit complete.

Überprüfen Sie anschließend, ob diese Zeile an Standort B repliziert wird. Zu diesem Zweck Remotedesktop an Standort B. Öffnen Sie das SQL Plus-Fenster führen Sie folgendes aus:

	SQL> select name from v$database;

	NAME
	———
	TESTGG

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13

Fügen Sie einen neuen Datensatz am Standort B hinzu:

	SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
	1 row created.

	SQL> commit;

	Commit complete.

Remotedesktop an Standort A und überprüfen Sie, ob die Replikation stattgefunden hat:

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13
	101 DVD 10 22-MAR-13

##Zusätzliche Ressourcen
[Oracle Virtual Machine images for Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md) (Images von virtuellen Oracle-Computern für Azure; in englischer Sprache)

<!---HONumber=Oct15_HO1-->