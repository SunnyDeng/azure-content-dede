<properties linkid="hdinsight-emulator-release-notes" urlDisplayName="HDInsight Emulator release notes" pageTitle="Release notes: Microsoft HDInsight Emulator for Azure | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Get late-breaking information about the most recent releases of the HDInsight Emulator." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Release notes: Microsoft HDInsight Emulator for Azure" authors="cgronlun" />

Versionsinformationen: Microsoft HDInsight Emulator für Azure
=============================================================

> [WACOM.NOTE]
> Die einfachste Art, die Versionsnummer zu prüfen, ist, unter "Programme hinzufügen/entfernen" nach dem Eintrag für "Microsoft HDInsight Emulator für Azure" (bei Version 1.0.0.0 oder höher) oder "Microsoft HDInsight Developer Preview" (bei niedrigeren Versionen als 1.0.0.0) zu suchen.

V1.0.0.0, veröffentlicht am 28.10.2013
--------------------------------------

-   Dies ist die allgemein verfügbare Version des Microsoft HDInsight Emulators für Azure, zuvor bekannt als Microsoft HDInsight Developer Preview.

-   Wie schon die Vorabveröffentlichungen wurde dieses Produkt für Entwicklungsszenarien erstellt und unterstützt dementsprechend nur Umgebungen mit einem Knoten.

### Neuerungen

-   Es wurden Skripts hinzugefügt, um die Einstellung aller Apache Hadoop-Dienste auf automatischen oder manuellen Start zu vereinfachen. Die Standardeinstellung ist wie zuvor automatisch, aber nun können alle Dienste mit den unter C:\\Hadoop installierten Skripts set-onebox-manualstart.cmd und set-onebox-autostart.cmd geändert werden.

-   Die Anzahl der erforderlichen Installationsabhängigkeiten wurde deutlich reduziert, um schnellere Installationen zu ermöglichen.

-   Behebung eines Problems im Befehl zur Ausführung von Pig-Beispielen im Skript RunSamples.ps1, das im Ordner GettingStarted installiert ist.

-   Diese Version enthält ein Update für die Version 1.1. der Hortonworks Data Platform, die zu den Hortonworks Data Platform-Diensten passt, die Teil der Cluster-Version 1.6 von Azure HDInsight sind.

V0.11.0.0, veröffentlicht am 30.09.2013
---------------------------------------

### Neuerungen

-   Das HDInsight-Dashboard wurde entfernt.

-   Diese Version enthält ein Update für die Hortonworks Data Platform Developer Preview, das mit der Hortonworks Data Platform Preview in Azure HDInsight kompatibel ist.

V0.10.0.0, veröffentlicht am 09.08.2013
---------------------------------------

### Neuerungen

-   Diese Version enthält ein Update für die Hortonworks Data Platform Developer Preview, das mit der Hortonworks Data Platform Preview in Azure HDInsight kompatibel ist.

V0.8.0.0, veröffentlicht am 07.06.2013
--------------------------------------

### Neuerungen

-   Diese Version enthält ein Update für die Hortonworks Data Platform Developer Preview, das mit der Hortonworks Data Platform Preview in Azure HDInsight kompatibel ist.

V0.6.0.0, veröffentlicht am 13.05.2013
--------------------------------------

### Neuerungen

-   Hive Server 2 wird installiert. Dies ist für die neue Version 0.9.2.0 des Microsoft ODBC-Treibers für Hive erforderlich, der zur selben Zeit veröffentlicht wurde wie dieses Update.

-   Alle Dienste sind auf automatischen Start gesetzt, sodass nach einem Neustart des Computers nicht mehr jede einzelne Anwendung neu gestartet werden muss.

V0.4.0.0, veröffentlicht am 25.03.2013
--------------------------------------

### Neuerungen

-   Neue Veröffentlichung von Microsoft HDInsight Developer Preview und von Hortonworks Data Platform für Windows Developer Preview.

-   Enthält Apache Hadoop, Hive, Pig, Sqoop, Oozie, HCatalog und Templeton.

-   Neues Microsoft HDInsight-Dashboard mit folgenden Funktionen:

-   Verbindung mit mehreren Clustern sowohl in lokalen Installationen als auch in Remote-Anwendungen über den Azure HDInsight-Dienst.

-   Weitere Informationen zum HDInsight-Dienst erhalten Sie unter http://www.windowsazure.com/de-de/manage/services/hdinsight.

-   Konfiguration von ASV in einem lokalen Cluster

-   Detaillierte Anweisungen finden Sie unten.

-   Verfassen und Bearbeiten von Hive-Abfragen in der neuen interaktiven Hive-Konsole.

-   Anzeigen und Herunterladen von Auftragsverläufen und -ergebnissen.

### Versionsinformationen

Portnummern

-   Der Zugriff auf die REST API-Endpunkte einer lokalen HDInsight-Installation und des Azure HDInsight-Dienstes erfolgt über verschiedene Portnummern für dieselben Dienste:

    Lokal:
    Oozie: http://localhost:11000/oozie/v1/admin/status 
    Templeton: http://localhost:50111/templeton/v1/status 
    ODBC: Verwenden Sie in einer DNS-Konfiguration oder -Verbindungszeichenfolge Port 10000.

    HDInsight-Dienst:
    Oozie: http://ServerFQDN:563/oozie/v1/admin/status 
    Templeton: http://ServerFQDN:563/templeton/v1/status
    ODBC: Verwenden Sie in einer DNS-Konfiguration oder -Verbindungszeichenfolge Port 563.

-   Konfiguration von ASV in einem lokalen Cluster:

    Im Dashboard finden Sie einen lokalen Standard-Cluster namens "local (hdfs)". Wenn Sie möchten, dass ASV ihr Speicher für Ihre lokale Installation ist, gehen Sie folgendermaßen vor:

    1.  Fügen Sie das Konto-Tag in die Datei core-site.xml unter C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf ein:
     

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>Der Name des Standarddateisystems. Entweder die literale Zeichenfolge "local" oder ein Host-Port für NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>Ein Basisverzeichnis für andere temporäre Verzeichnisse.</description>
                </property>
          
      Beispiel:

			<property>
    			<name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
				<value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
    		</property>

			<property>
				<name>fs.default.name</name>
				<!-- cluster variant -->
				<value>asv://MyASVContainer@MyASVAccount</value>
				<description>Der Name des Standarddateisystems.  Entweder die literale Zeichenfolge "local" oder ein Hostport für NDFS.</description>
                  <final>true</final>
              </property>

    1.  Öffnen Sie die Hadoop-Befehlsshell auf Ihrem Desktop im Modus mit erhöhten Rechten, und führen Sie den folgenden Befehl aus:

            %HADOOP\_NODE%\\stop-onebox.cmd && %HADOOP\_NODE%\\start-onebox.cmd

    1.  Greifen Sie auf jede Datei in diesem Konto mit dem vollständigen URI zu: asv://{container}@{account}/{path} (oder asvs://, wenn Sie HTTPS für den Zugriff auf die Daten verwenden möchten). Beispiel:

            hadoop fs -lsr

            asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

    1.  Löschen Sie den aktuell angemeldeten lokalen Cluster, und melden Sie ihn mit den neuen ASV-Anmeldeinformationen erneut an.

V0.3.0.0, veröffentlicht am 13.12.2012
--------------------------------------

-   Auf der Dashboard-Website ist nun eine anonyme Authentifizierung möglich. Windows-Anmeldeinformationen sind nicht mehr erforderlich. Dadurch ist das Problem mit der Anmeldungsaufforderung behoben, auf das in den Informationen zur vorherigen Version hingewiesen wurde.

-   Mehrere Probleme in Sqoop mit dem Export und einigen Importtypen behoben.

### Probleme in der Version

-   Das Laden der JavaScript-Konsole schlägt fehl, Weitere Angaben dazu finden Sie in den Versionsinformationen für Version 0.2.0.0.
-   In der Sqoop-Befehlszeile werden die unten gezeigten Warnungen angezeigt. Dieses Problem wird in einem späteren Update behoben und kann zurzeit ignoriert werden.

          c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
          Setting HBASE_HOME to 
          Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
          Please set HBASE_HOME to the root of your HBase installation. 
          Setting ZOOKEEPER_HOME to 
          Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
          Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
          Sqoop 1.4.2 
          git commit id 3befda0a456124684768348bd652b0542b002895 
          Compiled by  on Thu 11/29/2012- 3:26:26.10

V0.2.0.0, veröffentlicht am 03.12.2012
--------------------------------------

-   Einführung semantischer Versionierung in MSI

-   Behebung mehrerer in MSDN-Foren gemeldeter Installationsprobleme, insbesondere mit der Installation des HDInsight-Dashboards

-   Startmenüelemente für bessere Auffindbarkeit hinzugefügt

-   Behebung des Problems mit mehrzeiligen Eingaben in der Hive-Konsole

-   Kleinere inhaltliche Updates bei den ersten Schritten

### Probleme in der Version

-   Laden der JavaScript-Konsole schlägt fehl

    -   In manchen Installationen schlägt das Laden der JavaScript-Konsole fehl, und auf der Seite wird ein HTTP 404-Fehler angezeigt. Navigieren Sie direkt zu http://localhost:8080, um dieses Problem zu umgehen und die Konsole zu verwenden.
-   Bei der Navigation zum HDInsight-Dashboard wird eine Anmeldeaufforderung ausgegeben

    -   Es wurde einige Male berichtet, dass beim Versuch, das HDInsight-Dashboard zu öffnen, ein Anmeldungs-Dialogfeld geöffnet wird. In diesem Falle können Sie die Anmeldeinformationen Ihres aktuellen Benutzers eingeben und sollten dann in der Lage sein, zum Dashboard zu navigieren.

V1.0.0.0, veröffentlicht am 23.10.2012
--------------------------------------

-   Erste Version

### Probleme in der Version

-   Hive-Konsole

    -   Wenn ein Zeilenumbruch in den übermittelten Hive-Befehl eingefügt wird, kommt es zu einem "Syntaxfehler". Entfernen Sie alle Zeilenumbrüche, und die Abfrage sollte wie gewünscht ausgeführt werden.

Allgemeine bekannte Probleme
----------------------------

-   Ablauf des Hadoop-Benutzerkennworts

    Das Kennwort für den Hadoop-Benutzer kann ablaufen. Dies hängt von den AD-Richtlinien ab, die für diesen Computer gelten. Mit dem folgenden PowerShell-Skript wird festgelegt, dass das Kennwort nicht abläuft. Dieses Skript kann in einer Administrator-Eingabeaufforderung ausgeführt werden.

          $username = "hadoop"
          $ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

          $computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
          $users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

          foreach($user in $users)
          {
              if($user.Name -eq $username)
              {
                  $user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
                $user.SetInfo()
         
                $user.PasswordExpired = 0
                $user.SetInfo()
         
                Write-Host "$username user maintenance completed. "
              }
          }

-   Temp-Verzeichnis\>

    hadoop.tmp.dir verweist auf den falschen Speicherort: Statt auf C:\\hadoop\\hdfs verweist es auf c:\\hdfs. Dieser Fehler wird mit dem nächsten Update von HDP behoben.

-   Betriebssystem-Einschränkungen

    HDInsight Server muss auf einem 64-Bit-Betriebssystem installiert werden.

-   HDInsight kann nicht in WebPI-Suchergebnissen gefunden werden.

    Dies liegt normalerweise an einer Betriebssystem-Einschränkung. HDInsight erfordert ein 64-Bit-Betriebssystem mit mindestens Windows 7 Service Pack 1, Windows Server 2008 R2 Service Pack1, Windows 8 oder Windows Server 2012.

-   Dokumentation der Administrator-Eingabeaufforderung

    -   Wenn Sie Befehle wie **hadoop mradmin** oder **hadoop defadmin** ausführen möchten, müssen Sie dies als Hadoop-Benutzer tun.

    -   Öffnen Sie eine Hadoop-Eingabeaufforderung, und führen Sie den folgenden Befehl aus, um auf einfache Weise eine Shell zu erstellen, die Sie als Hadoop-Benutzer ausführen können:

            start-hadoopadminshell

    -   Dadurch wird eine neue Befehlsshell geöffnet, die mit Hadoop-Administratorrechten läuft

Nächste Schritte
----------------

-   [Erste Schritte mit dem HDInsight Emulator](/de-de/documentation/articles/hdinsight-get-started-emulator/)

