<properties 
	pageTitle="Anmerkungen zu dieser Version: Microsoft HDInsight-Emulator für Azure | Microsoft Azure" 
	description="Erhalten Sie aktuellste Informationen über die neuesten Versionen des HDInsight Hadoop-Emulators." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>




# Anmerkungen zu dieser Version: Microsoft HDInsight-Emulator für Azure 



> [AZURE.NOTE]Die einfachste Art, die Versionsnummer zu prüfen, ist, unter "Programme hinzufügen/entfernen" nach dem Eintrag für **Microsoft HDInsight Emulator für Azure** (bei Version 1.0.0.0 oder höher) oder **Microsoft HDInsight Developer Preview** (bei niedrigeren Versionen als 1.0.0.0) zu suchen.

## Version 2.0.0.0, veröffentlicht am 29. August 2014

* Mit dieser Version kann der HDInsight-Emulator dieselben Hadoop-Projekte als Ziel verwenden, die momentan im Dienst in der Version 3.1 live sind.    

* Wie schon die Vorschauversionen dieses Produkt wurde diese Version für Entwicklungsszenarien erstellt und unterstützt dementsprechend nur Bereitstellungen mit einem Knoten.

### Neuerungen 
 
* [Aktualisierte Versionen der Hadoop-Komponenten](hdinsight-component-versioning.md) für Version 3.1 des Dienstes. Dazu gehören auch Hive 0.13 und die Tez-Unterstützung.

## Version 1.0.0.0, veröffentlicht am 28. Oktober 2013

* Dies ist die allgemein verfügbare Version des Microsoft HDInsight Emulators für Azure, zuvor bekannt als Microsoft HDInsight Developer Preview. 

* Wie schon die Vorschauversionen dieses Produkt wurde diese Version für Entwicklungsszenarien erstellt und unterstützt dementsprechend nur Bereitstellungen mit einem Knoten.

### Neuerungen 
 
* Es wurden Skripts hinzugefügt, um die Einstellung aller Apache Hadoop-Dienste auf automatischen oder manuellen Start zu vereinfachen. Die Standardeinstellung ist wie zuvor "automatic", aber nun können alle Dienste mit den unter "C:\\Hadoop" installierten Skripts "set-onebox-manualstart.cmd" und "set-onebox-autostart.cmd" geändert werden. 

* Die Anzahl der erforderlichen Installationsabhängigkeiten wurde deutlich reduziert, um schnellere Installationen zu ermöglichen.

* Diese Version bietet die Behebung eines Problems im Befehl zur Ausführung von Pig-Beispielen im Skript "RunSamples.ps1", das im Ordner "GettingStarted" installiert ist.

* Diese Version enthält ein Update für die Version 1.1. von Hortonworks Data Platform (HDP), die den Hortonworks Data Platform-Diensten entspricht, die Teil der Clusterversion 1.6 von Azure HDInsight sind.

## Version 0.11.0.0, veröffentlicht am 30. September 2013

### Neuerungen 
		
* Das HDInsight-Dashboard wurde entfernt. 

* Diese Version enthält ein Update für die Hortonworks Data Platform Developer Preview, das mit der Hortonworks Data Platform Preview in Azure HDInsight kompatibel ist.

## Version 0.10.0.0, veröffentlicht am 9. August 2013

### Neuerungen 
	
* Diese Version enthält ein Update für die Hortonworks Data Platform Developer Preview, das mit der Hortonworks Data Platform Preview in Azure HDInsight kompatibel ist.

## Version 0.8.0.0, veröffentlicht am 7. Juni 2013

### Neuerungen 

* Diese Version enthält ein Update für die Hortonworks Data Platform Developer Preview, das mit der Hortonworks Data Platform Preview in Azure HDInsight kompatibel ist.

## Version 0.6.0.0, veröffentlicht am 13. Mai 2013

### Neuerungen 

* Hive Server 2 wird installiert. Dies ist für die Version 0.9.2.0 des Microsoft ODBC-Treibers für Hive erforderlich, der zur selben Zeit veröffentlicht wurde wie dieses Update. 

* Alle Dienste sind auf automatischen Start festgelegt, sodass nach einem Neustart des Computers nicht mehr jede einzelne Anwendung neu gestartet werden muss.

## Version 0.4.0.0, veröffentlicht am 25. März 2013

### Neuerungen 

* Neue Veröffentlichung von Microsoft HDInsight Developer Preview und von Hortonworks Data Platform für Windows Developer Preview. 

* Enthält Apache Hadoop, Hive, Pig, Sqoop, Oozie, HCatalog und Templeton.

* Neues HDInsight-Dashboard mit folgenden Features:
 
	* Verbindung mit mehreren Clustern sowohl in lokalen als auch in Remote-Installationen über den Azure HDInsight-Dienst. Weitere Informationen zum HDInsight-Dienst finden Sie unter [http://azure.microsoft.com/de-de/documentation/services/hdinsight/](http://azure.microsoft.com/documentation/services/hdinsight/).
 
	* Konfigurieren von Azure-Blobspeicher für den lokalen Cluster. Detaillierte Anweisungen finden Sie unten.

	* Verfassen und Bearbeiten von Hive-Abfragen in der neuen interaktiven Hive-Konsole.

	* Anzeigen und Herunterladen von Auftragsverläufen und -ergebnissen.

### Versionshinweise 

* Der Zugriff auf die REST-API-Endpunkte einer lokalen HDInsight-Installation und des Azure HDInsight-Diensts erfolgt über verschiedene Portnummern für dieselben Dienste: 

	Lokal: Oozie – http://localhost:11000/oozie/v1/admin/status Templeton – http://localhost:50111/templeton/v1/status ODBC: Verwenden Sie Port 10000 in der DSN-Konfiguration oder Verbindungszeichenfolge.

	HDInsight-Dienst: Oozie – http://ServerFQDN:563/oozie/v1/admin/status Templeton – http://ServerFQDN:563/templeton/v1/status ODBC – Verwenden Sie Port 563 in der DSN-Konfiguration oder-Verbindungszeichenfolge.


* Konfigurieren von Azure-Blobspeicher für den lokalen Cluster:

	Im Dashboard finden Sie einen lokalen Standard-Cluster namens "local (hdfs)". Wenn Sie Azure-Blobspeicher als Speicher für Ihre lokale Installation wünschen, gehen Sie folgendermaßen vor:

	1. Fügen Sie das Konto-Tag in die Datei core-site.xml unter C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf ein:       

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>A base for other temporary directories.</description>
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
				<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
				<final>true</final>
			</property>
        

	2. Öffnen Sie die Hadoop-Befehlsshell auf Ihrem Desktop im Modus mit erhöhten Rechten, und führen Sie den folgenden Befehl aus:
	 
	 		%HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

	3. Greifen Sie auf alle Dateien in diesem Konto über den vollständigen URI zu: asv://{container}@{account}/{path} (oder asvs://, wenn Sie für den Zugriff auf die Daten HTTPS verwenden möchten). Beispiel:
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. Löschen Sie den aktuell registrierten lokalen Cluster, und registrieren Sie ihn mit den Anmeldeinformationen für den neuen Azure-Blobspeicher neu.


## Version 0.3.0.0, veröffentlicht am 13. Dezember 2012 

* Auf der Dashboardwebsite ist nun eine anonyme Authentifizierung ohne Windows-Anmeldeinformationen möglich. Dadurch ist das Problem mit der Anmeldeaufforderung behoben, auf das in den Anmerkungen zur vorherigen Version hingewiesen wurde. 

* Mehrere Probleme in Sqoop mit dem Export und einigen Importtypen behoben.

### Probleme in der Version 

* Die JavaScript-Konsole kann nicht geladen werden. Siehe die Details in den Anmerkungen zu Version 0.2.0.0. 
* In der Sqoop-Befehlszeile werden die unten gezeigten Warnungen angezeigt. Dieses Problem wird in einem späteren Update behoben und kann zurzeit ignoriert werden. 
	
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

## Version 0.2.0.0, veröffentlicht am 3. Dezember 2012

* Einführung der semantischen Versionierung in Windows Installer 

* Behebung mehrerer in MSDN-Foren gemeldeter Installationsprobleme, insbesondere mit der Installation des HDInsight-Dashboards

* Elemente im Menü **Start** für bessere Auffindbarkeit hinzugefügt

* Behebung des Problems mit mehrzeiligen Eingaben in der Hive-Konsole

* Kleinere inhaltliche Aktualisierungen bei den ersten Schritten

### Probleme in der Version 

* Die JavaScript-Konsole kann nicht geladen werden. 

	* In manchen Installationen schlägt das Laden der JavaScript-Konsole fehl, und auf der Seite wird ein HTTP 404-Fehler angezeigt. Navigieren Sie direkt zu http://localhost:8080, um dieses Problem zu umgehen und die Konsole zu verwenden. 

* Bei der Navigation zum HDInsight-Dashboard wird eine Anmeldeaufforderung angezeigt.

	* Es wurde einige Male berichtet, dass beim Versuch, das HDInsight-Dashboard zu öffnen, ein Anmeldungsdialog geöffnet wird. In diesem Falle können Sie die Anmeldeinformationen Ihres aktuellen Benutzers eingeben und sollten dann in der Lage sein, zum Dashboard zu navigieren. 


## Version 1.0.0.0, veröffentlicht am 23. Oktober 2012 

* Erste Version 

### Probleme in der Version 

* Hive-Konsole 

	* Wenn ein Zeilenumbruch in den übermittelten Hive-Befehl eingefügt wird, kommt es zu einem Syntaxfehler. Entfernen Sie alle Zeilenumbrüche, und die Abfrage sollte wie gewünscht ausgeführt werden. 



## Allgemeine bekannte Probleme

* Ablauf des Hadoop-Benutzerkennworts 

	Das Kennwort für den Hadoop-Benutzer kann ablaufen. Dies hängt von den Active Directory-Richtlinien ab, die für diesen Computer gelten. Mit dem folgenden Windows PowerShell-Skript wird festgelegt, dass das Kennwort nicht abläuft. Dieses Skript kann an einer Administratoreingabeaufforderung ausgeführt werden:

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


* Temp-Verzeichnis
	
	Die Datei "hadoop.tmp.dir" verweist auf den falschen Speicherort. Statt auf C:\\hadoop\\hdfs verweist es auf c:\\hdfs. Dieser Fehler wird mit dem nächsten Update von HDP behoben.

* Betriebssystemeinschränkungen

	Der HDInsight-Server muss auf einem 64-Bit-Betriebssystem installiert werden.

* Suchergebnisse für Web Platform Installer (WebPI)

	HDInsight kann nicht in WebPI-Suchergebnissen gefunden werden. Dies liegt normalerweise an einer Betriebssystem-Einschränkung. HDInsight erfordert ein 64-Bit-Betriebssystem mit mindestens Windows 7 Service Pack 1, Windows Server 2008 R2 Service Pack 1, Windows 8 oder Windows Server 2012.

* Dokumentation der Administratoreingabeaufforderung

	* Wenn Sie Befehle wie **hadoop mradmin** oder **hadoop defadmin** ausführen möchten, müssen Sie dies als Hadoop-Benutzer tun. 

	* Öffnen Sie eine Hadoop-Eingabeaufforderung, und führen Sie den folgenden Befehl aus, um auf einfache Weise eine Shell zu erstellen, die Sie als Hadoop-Benutzer ausführen können:
	 
	 		start-hadoopadminshell

	* Dadurch wird eine neue Befehlsshell geöffnet, die mit Hadoop-Administratorrechten ausgeführt wird.

##<a name="nextsteps"></a> Nächste Schritte

- [Erste Schritte mit dem HDInsight-Emulator][hdinsight-hadoop-emulator-get-started]




[hdinsight-hadoop-emulator-get-started]: hdinsight-get-started-emulator.md


<!--HONumber=54-->