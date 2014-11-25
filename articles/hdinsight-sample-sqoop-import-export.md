<properties linkid="manage-services-hdinsight-sample-sqoop-import-export" urlDisplayName="HDInsight Samples" pageTitle="Sqoop Import-Export sample | Windows Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run Sqoop import and export on HDInsight." umbracoNaviHide="0" disqusComments="1" writer="bradsev" editor="cgronlun" manager="paulettm" title="Sqoop Import-Export sample" />

Sqoop Import-Export-Beispiel
============================

In diesem Beispiel lernen Sie, wie Sie Daten aus einer SQL-Datenbank in Azure mit Apache Sqoop in ein Hadoop HDFS-Cluster in Azure importieren können.

Obwohl Hadoop die beste Wahl für die Verarbeitung unstrukturierter und halbstrukturierter Daten wie z. B. Protokolle und Dateien ist, besteht oft auch Bedarf für die Verarbeitung strukturierter Dateien in relationalen Datenbanken.

Sqoop ist ein Tool zum Übertragen von Daten zwischen Hadoop-Clustern und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL, MySQL oder Oracle in das Hadoop Distributed File System (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Hive transformieren und die Daten anschließend wieder in ein RDBMS exportieren. In diesem Beispiel verwenden Sie eine SQL-Datenbank als relationale Datenbank.

Sqoop ist ein Open Source-Softwareprodukt von Cloudera, Inc. Die Entwicklung von Sqoop wechselte im Jahr 2011 von gitHub zur [Apache Sqoop](http://sqoop.apache.org/)-Website.

Sqoop wird in Azure HDInsight über die Hadoop-Befehlszeile im Hauptknoten des HDFS-Clusters bereitgestellt. Sie können per Remotedesktop unter Hadoop im Azure-Portal auf den Hauptknoten des Clusters für diese Bereitstellung zugreifen.

**Sie erhalten Informationen zu folgenden Themen:**

-   Verwenden von Azure PowerShell zum Ausführen eines MapReduce-Programms unter Azure HDInsight zur Analyse von Daten in einer Datei.

**Voraussetzungen**: Sie benötigen ein Azure-Konto und müssen den HDInsight-Dienst für Ihr Abonnement aktiviert haben. Sie haben Azure PowerShell und die PowerShell-Tools für Azure HDInsight installiert und für die Verwendung mit Ihrem Konto konfiguriert. Anweisungen hierzu finden Sie unter [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/)

Sie benötigen außerdem die öffentliche IP-Adresse Ihres aktuellen Aufenthaltsorts für die Konfiguration der Firewall Ihrer SQL-Datenbank. Öffnen Sie dazu die Website [WhatIsMyIP](http://www.whatismyip.com/) und notieren Sie Ihre IP-Adresse. Später werden Sie außerdem die öffentliche IP-Adresse des Hauptknotens Ihres Hadoop-Clusters brauchen. Sie können diese IP-Adresse auf dieselbe Weise herausfinden.

**Zusammenfassung** In diesem Thema wird die Ausführung des Beispiels demonstriert und der Java-Code für das MapReduce-Programm vorgestellt. Außerdem erhalten Sie eine Zusammenfassung der vermittelten Lerninhalte und einen Ausblick auf nächste Schritte. Der Artikel enthält die folgenden Abschnitte:

1.  [Einrichten einer SQL-Datenbank](#set-up-sql)
2.  [Verwenden von Sqoop in HDInsight, um Daten in ein Cluster zu importieren](#java-code)
3.  [Zusammenfassung](#summary)
4.  [Nächste Schritte](#next-steps)

Einrichten einer SQL-Datenbank
------------------------------

TBD: Screenshots für diesen Abschnitt - dies ist immer noch der RDP-Ansatz.

**Erstellen einer SQL-Datenbank**

1.  Melden Sie sich am Azure-Verwaltungsportal an.
2.  Klicken Sie links unten auf NEW, klicken Sie auf DATA SERVICES und anschließend auf SQL DATABASE. Melden Sie sich mit Ihrem Azure-Konto an. Klicken Sie oben links in der Seite auf das **Database**-Symbol, um einen Datenbankserver zu erstellen.

3.  Klicken Sie auf der Seite **Getting Started** auf die Option **Create a new SQL Database Server**.

4.  Wählen Sie die Art des Abonnements (z. B. **Pay-As-You-Go**) für Ihr Konto im Fenster **Create Server** aus und klicken Sie auf **Next**.

5.  Wählen Sie eine geeignete **Region** im Fenster **Create Server** aus und klicken Sie auf **Next**.

6.  Geben Sie Benutzername und Kennwort des Serverebenenprinzipals für Ihren SQL-Datenbankserver ein und klicken Sie auf **Next**.

7.  Klicken Sie auf **Add**, um eine Firewall-Regel anzugeben, die den Zugriff auf die SQL-Datenbank von Ihrem aktuellen Standort aus erlaubt, um die AdventureWorks-Datenbank hochladen zu können. Die Firewall gewährt Zugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Verwenden Sie die zuvor ermittelte IP-Adresse, um die Regel zu erstellen. Geben Sie einen Regelnamen wie gezeigt ein, verwenden Sie jedoch Ihre eigene IP-Adresse und nicht die hier zu Demonstrationszwecken gezeigte. (Sie müssen außerdem die öffentliche IP-Adresse des Hauptknotens Ihres Hadoop-Clusters hinzufügen. Tun Sie dies jetzt, falls Sie die Adresse bereits kennen.) Klicken Sie anschließend auf die Schaltfläche **Finish**.

**Importieren von AdventureWorks2012**

1.  Laden Sie die AdventureWorks2012-Datenbank über den Link "Recommended Downloads" auf der Adventure Works für SQL Database-Website auf Ihren lokalen Computer herunter.

2.  Entzippen Sie die Datei, öffnen Sie eine Administrator-Eingabeaufforderung und navigieren Sie zum AdventureWorks-Ordner unterhalb des AdventureWorks2012ForSQLAzure-Ordners.

3.  Geben Sie Folgendes ein, um CreateAdventureWorksForSQLAzure.cmd auszuführen:

CreateAdventureWorksForSQLAzure.cmd servername username password

    Für einen SQL-Datenbankserver namens b1gl33p, einen Administrator namens "Fred" und das Kennwort "Secret" würden Sie Folgendes eingeben:

    CreateAdventureWorksForSQLAzure.cmd b1gl33p.database.windows.net Fred@b1gl33p Secret

    Dieses Skript erstellt die Datenbank, installiert das Schema und füllt die Datenbank mit Beispieldaten.

1.  Kehren Sie zum **Azure-Portal** zurück, klicken Sie links auf Ihr Abonnement (**Pay-As-You-Go** im folgenden Beispiel) und wählen Sie Ihre Datenbank aus (hier mit dem Namen wq6xlbyoq0). AventureWorks2012 sollte nun in der Spalte **Database Name** aufgelistet werden. Wählen Sie den Eintrag aus, und klicken Sie oben auf **Manage**.

2.  Geben Sie die Anmeldeinformationen für die SQL-Datenbank ein und klicken Sie auf **Log on**.

3.  Daraufhin öffnet sich die Web-Schnittstelle für die AdventureWorks-Datenbank in Ihrer SQL-Datenbank. Klicken Sie oben auf das **New Query**-Symbol, um den Abfrageeditor zu öffnen.

4.  Da Sqoop momentan eckige Klammern zum Tabellennamen hinzufügt, benötigen wir ein Synonym, um zweigeteilte Namen für SQL Server-Tabellen zu unterstützen. Führen Sie dazu die folgende Abfrage aus:

`CREATE SYNONYM [Sales.SalesOrderDetail] FOR Sales.SalesOrderDetail`

1.  Führen Sie die folgende Abfrage aus und sehen Sie sich das Ergebnis an.

`select top 200 * from [Sales.SalesOrderDetail] `

Verwenden von Sqoop in HDInsight, um Daten in ein Cluster zu importieren
------------------------------------------------------------------------

1.  Blättern Sie auf Ihrer Kontoseite nach unten zum "Open Ports"-Symbol im Bereich "Your Cluster" und klicken Sie auf das Symbol, um den ODBC-Serverport im Hauptknoten Ihres Clusters zu öffnen.

2.  Kehren Sie zu Ihrer Kontoseite zurück, blättern Sie nach unten zum Bereich "Your Cluster" und klicken Sie auf das Symbol **Remotedesktop**, um den Hauptknoten Ihres Clusters zu öffnen. TBD: aktualisieren für PS-Szenario.

3.  Klicken Sie auf **Open**, wenn Sie zum Öffnen der .rdp-Datei aufgefordert werden.

4.  Wählen Sie im Remotedesktopverbindungs-Fenster "Connect" aus.

5.  Geben Sie Ihre Anmeldeinformationen für das Hadoop-Cluster (nicht für Hadoop in Ihrem Azure-Konto) im **Windows Security**-Fenster ein und klicken Sie auf **OK**.

6.  Öffnen Sie die Website WhatIsMyIP im Internet Explorer, um die öffentliche IP-Adresse des Hauptknotens Ihres Clusters herauszufinden. Kehren Sie zur SQL-Datenbank-Verwaltungsseite zurück und fügen Sie eine Firewallregel hinzu, die Ihrem Hadoop-Cluster Zugriff auf die SQL-Datenbank gewährt. Die Firewall gewährt Zugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.

7.  Doppelklicken Sie auf das Hadoop-Befehlszeilensymbol oben links auf dem Desktop, um die Befehlszeile zu öffnen. Navigieren Sie zum Ordner "c:\\Apps\\dist\\sqoop\\bin" und führen Sie den folgenden Befehl aus:

    `sqoop import --connect "jdbc:sqlserver://[serverName].database.windows.net;username=[userName]@[serverName];password=[password];database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

    Für die folgenden Beispielwerte: \* Servername: wq6xlbyoq0 \* Benutzername: HadoopOnAzureSqoopAdmin \* Kennwort: Pa\$\$w0rd

    Lautet der Sqoop-Befehl:

    `sqoop import --connect "jdbc:sqlserver://wq6xlbyoq0.database.windows.net;username=HadoopOnAzureSqoopAdmin@wq6xlbyoq0;password=Pa$$w0rd;;database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

8.  Kehren Sie zur **Accounts** für Hadoop im Azure-Portal zurück und öffnen Sie die interaktive Konsole. Führen Sie den \#lsr-Befehl in der JavaScript-Konsole aus, um die Dateien und Ordner in Ihrem HDFS-Cluster aufzulisten. TBD Update für GA.

9.  Führen Sie den \#tail-Befehl aus, um die ausgewählten Ergebnisse aus der Datei part-m-0000 anzuzeigen. `tail /user/RAdmin/data/SalesOrderDetail/part-m-00000`

Zusammenfassung
---------------

Sie sind nun in der Lage, Daten mithilfe von Apache Sqoop zwischen einem von Azure HDInsight verwalteten Hadoop-Cluster und einer relationalen Datenbank zu übertragen.

Nächste Schritte
----------------

Lernprogramme mit anderen Beispielen und Anweisungen zur Verwendung von Pig-, Hive- und MapReduce-Jobs in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Themen:

-   [Beispiel: Pi-Schätzung](/de-de/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Beispiel: Wortzählung](/de-de/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Beispiel: C\# Streaming](/de-de/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Beispiel: 10-GB-Graysort](/de-de/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Lernprogramm: Verwenden von Pig](/de-de/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Lernprogramm: Verwenden von Hive](/de-de/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Lernprogramm: Verwenden von MapReduce](/de-de/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Dokumentation des Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx)

