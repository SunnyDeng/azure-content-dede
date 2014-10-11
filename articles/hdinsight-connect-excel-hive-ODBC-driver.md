<properties linkid="manage-services-hdinsight-connect-excel-with-hive-ODBC" urlDisplayName="Connect Excel to HDInsight" pageTitle="Connect Excel to Hadoop with the Hive ODBC Driver | Azure" metaKeywords="" description="Learn how to set up and use the Microsoft Hive ODBC driver for Excel to query data in an HDInsight cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with the Microsoft Hive ODBC Driver" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Verbinden von Excel über den Microsoft Hive ODBC Driver mit Hadoop

Eine wichtige Funktion der Big Data-Lösung von Microsoft ist die Integration von Microsoft Business Intelligence (BI)-Komponenten in Apache Hadoop-Cluster, die über Azure HDInsight bereitgestellt werden. Ein Beispiel für diese Integration ist die Möglichkeit, Excel mit dem Hive-Data Warehouse eines Hadoop-Clusters in HDInsight über den Microsoft Hive Open Database Connectivity (ODBC) Driver zu verbinden.

Es ist ebenfalls möglich, die zu einem HDInsight-Cluster gehörigen Daten und andere Datenquellen, einschließlich anderer Hadoop-Cluster (nicht aus HDInsight), zu verbinden. Hierbei verwenden Sie in Excel das Add-In Microsoft Power Query für Excel. Informationen zur Installation und Verwendung von Power Query finden Sie unter [Verbinden von Excel mit HDInsight über Power Query][].

**Voraussetzungen**:

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

-   Einen HDInsight-Cluster. Hinweise zu dessen Konfiguration finden Sie unter [Erste Schritte mit Azure HDInsight][].
-   Einen Computer, auf dem Windows 8, Windows 7, Windows Server 2012 oder Windows Server 2008 R2 läuft.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.

## Themen in diesem Artikel

1.  [Installieren des Microsoft Hive ODBC Driver][]
2.  [Erstellen einer Hive ODBC-Datenquelle][]
3.  [Importieren von Daten aus einem HDInsight-Cluster in Excel][]
4.  [Nächste Schritte][]

## <span id="InstallHiveODBCDriver"></span></a>Installieren des Microsoft Hive ODBC Driver

Laden Sie aus dem [Download Center][] den Microsoft Hive ODBC Driver herunter, und installieren Sie diesen.

Dieser Treiber kann unter 32- oder 64-Bit-Versionen von Windows 7, Windows 8, Windows Server 2008 R2 und Windows Server 2012 installiert werden und erlaubt eine Verbindung zu Azure HDInsight (Version 1.6 und höher) und Azure HDInsight Emulator (Version 1.0.0.0 und höher). Installieren Sie die Version, die der Version der Anwendung entspricht, in der Sie den ODBC-Treiber verwenden. In diesem Lernprogramm wird der Treiber aus Office Excel verwendet.

## <span id="CreateHiveODBCDataSource"></span></a>Erstellen einer Hive ODBC-Datenquelle

Die folgenden Schritte zeigen Ihnen, wie Sie eine Hive ODBC-Datenquelle erstellen können.

1.  Drücken Sie unter Windows 8 die Windows-Taste, um den Startbildschirm zu öffnen, und geben Sie dann **Datenquellen** ein.
2.  Klicken Sie auf **ODBC-Datenquellen einrichten (32-Bit)** oder **ODBC-Datenquellen einrichten (64-Bit)**, je nach verwendeter Office-Version. Wenn Sie Windows 7 verwenden, wählen Sie aus **Verwaltung** **ODBC-Datenquellen (32-Bit)** oder **ODBC-Datenquellen (64-Bit)** aus. Hierdurch wird das Dialogfeld **ODBC-Datenquellen-Administrator** aufgerufen.

    ![ODBC-Datenquellen-Administrator][]

3.  Klicken Sie aus Benutzer-DNS auf **Hinzufügen**, um den Assistenten **Neue Datenquelle erstellen** zu öffnen.
4.  Wählen Sie **Microsoft Hive ODBC Driver**, und klicken Sie dann auf **Fertig stellen**. Hierdurch wird das Dialogfeld **Microsoft Hive ODBC Driver DNS Setup** geöffnet.

5.  Geben Sie folgende Werte ein bzw. wählen diese aus:

    <table border="1">
    <tr><td><strong>Eigenschaft</strong></td><td><strong>Beschreibung</strong></td></tr>                                                                                                                                                                                                                                                             <tr><td>Name der Datenquelle </td><td> Geben Sie einen Namen für die Datenquelle an.                                                                                                                                                                                                                                </td></tr>
    <tr><td> Host</td><td> Geben Sie <hdinsightclustername>.azurehdinsight.net ein. Beispiel: myHDICluster.azurehdinsight.net</td></tr>
    <tr><td> Port </td><td> Verwenden Sie <strong>443</strong>. (Dieser Port wurde von 563 zu 443 geändert.)</td></tr>
    <tr><td> Datenbank</td><td>Verwenden Sie <strong>Standard</strong>.                                                                                                                                                                                                                                                  </td></tr>
    <tr><td> Hive-Servertyp</td><td>Wählen Sie <strong>Hive Server 2</strong> aus. </td></tr>
    <tr><td> Mechanismus</td><td> Wählen Sie <strong>Azure HDInsight Service</strong> aus.</td></tr>
    <tr><td> HTTP-Pfad </td><td> Lassen Sie dieses Feld leer.  </td></tr>
    <tr><td> Benutzername </td><td> Geben Sie Ihren Benutzernamen für den HDInsight-Cluster an. Es handelt sich hierbei um den Benutzernamen, der beim Bereitstellungsprozess des Clusters erstellt wurde. Wenn Sie die Schnellerfassungsoption gewählt haben, lautet der standardmäßige Benutzername <strong>Admin</strong>.</td></tr>
    <tr><td> Kennwort </td><td> Geben Sie Ihr Benutzerkennwort für den HDInsight-Cluster an.</td></tr>
	</table>

    Sie müssen einige wichtige Parameter berücksichtigen, wenn Sie auf <strong>Erweiterte Optionen</strong> klicken:

    <table border="1">
    <tr><td> Use Native Query </td><td> Wenn diese Option ausgewählt ist, versucht der ODBC-Treiber NICHT, TSQL in HiveQL zu konvertieren. Verwenden Sie diese Option nur, wenn Sie sich absolut sicher sind, dass Sie reine HiveQL-Anweisungen absenden. Wenn Sie eine Verbindung zu SQL Server oder Azure SQL Database herstellen, sollten Sie die Option nicht aktivieren. </td></tr>
    <tr><td> Rows fetched per block</td><td>Wenn Sie viele Datensätze abrufen, ist es möglicherweise erforderlich, diesen Parameter zu optimieren, um optimale Leistung zu garantieren. </td></tr>
    <tr><td>Default string column length, 
      Binary column length,         
      Decimal column scale</td><td> Längen und Genauigkeiten der Datentypen können beeinflussen, wie die Daten zurückgegeben werden. Bei zu geringer Genauigkeit und/oder Abschneiden werden falsche Informationen zurückgegeben.</td></tr>
	</table>

    ![Erweiterte Optionen][]

6.  Klicken Sie auf **Test**, um die Datenquelle zu testen. Wenn die Datenquelle korrekt konfiguriert wurde, wird *TESTS ERFOLGREICH ABGESCHLOSSEN.* angezeigt.
7.  Klicken Sie auf **OK**, um das Testdialogfeld zu schließen. Die neue Datenquelle sollte jetzt im **ODBC-Datenquellen-Administrator** aufgeführt werden.
8.  Klicken Sie auf **OK**, um den Assistenten zu beenden.

## <span id="ImportData"></span></a>Importieren von Daten aus einem HDInsight-Cluster in Excel

In den folgenden Schritten wird beschrieben, wie Sie mithilfe der ODBC-Datenquelle, die Sie in den vorangegangenen Schritten erstellt haben, Daten aus einer Hive-Tabelle in eine Excel-Arbeitsmappe importieren.

1.  Öffnen Sie eine neue oder bereits vorhandene Arbeitsmappe in Excel.
2.  Klicken Sie in der Registerkarte **Daten** auf die Kachel **Externe Daten abrufen**. Klicken Sie auf **Aus anderen Quellen**, und klicken Sie dann auf **Vom Datenverbindungs-Assistenten**, um den **Datenverbindungs-Assistenten** zu starten.

    ![Öffnen Sie den Datenverbindungs-Assistenten.][]

3.  Wählen Sie als Datenquelle **ODBC DSN** aus, und klicken Sie dann auf **Weiter**.
4.  Wählen Sie aus den ODBC-Datenquellen den Datenquellnamen aus, den Sie im vorherigen Schritt erstellt haben, und klicken Sie auf **Weiter**.
5.  Geben Sie im Assistenten erneut das Kennwort für den Cluster ein, und klicken Sie dann auf **Test**, um die Konfiguration zu prüfen.
6.  Klicken Sie auf **OK**, um das Testdialogfeld zu schließen.
7.  Klicken Sie auf **OK**. Warten Sie, bis sich das Dialogfeld **Datenbank und Tabelle wählen** öffnet. Dies kann einige Zeit dauern.
8.  Wählen Sie die Tabelle, die Sie importieren möchten, und klicken Sie dann auf **Weiter**. Die Tabelle *hivesampletable* ist eine Beispieltabelle, die in HDInsight-Clustern enthalten ist. Sie können sie auswählen, wenn Sie noch keine Tabelle erstellt haben. Weitere Informationen zum Ausführen von Hive-Abfragen und Erstellen von Hive-Tabellen finden Sie unter [Verwenden von Hive mit HDInsight][].
9.  Klicken Sie auf **Fertig stellen**.
10. Im Dialogfeld **Daten importieren** können Sie die Abfrage ändern oder spezifizieren. Klicken Sie hierfür auf **Eigenschaften**. Dies kann einige Zeit dauern.
11. Klicken Sie auf die Registerkarte **Definition**, und fügen Sie dann **LIMIT 200** zur Hive-SELECT-Anweisung im Textfeld **Befehlstext** hinzu. Diese Modifikation begrenzt die zurückgegebenen Datensätze auf 200.

    ![Verbindungseigenschaften][]

12. Klicken Sie auf **OK**, um das Dialogfeld Verbindungseigenschaften zu schließen.
13. Klicken Sie auf **OK**, um das Dialogfeld **Daten importieren** zu schließen.
14. Geben Sie das Kennwort erneut ein, und klicken Sie dann auf **OK**. Es dauert einige Zeit, bis die Daten in Excel importiert werden.

## <span id="nextsteps"></span></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie den Microsoft Hive ODBC-Treiber verwenden, um Daten aus dem HDInsight-Dienst nach Excel zu übertragen. Ebenso können Sie Daten aus dem HDInsight-Dienst in eine SQL-Datenbank übertragen. Es ist außerdem möglich, Daten in einen HDInsight-Dienst hochzuladen. Weitere Informationen finden Sie unter:

-   [Analysieren von Daten zu Flugverspätungen mit HDInsight][]
-   [Hochladen von Daten zu HDInsight][]
-   [Verwenden von Sqoop mit HDInsight][]

  [Verbinden von Excel mit HDInsight über Power Query]: ../hdinsight-connect-excel-power-query/
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
  [Installieren des Microsoft Hive ODBC Driver]: #InstallHiveODBCDriver
  [Erstellen einer Hive ODBC-Datenquelle]: #CreateHiveODBCDataSource
  [Importieren von Daten aus einem HDInsight-Cluster in Excel]: #ImportData
  [Nächste Schritte]: #nextsteps
  [Download Center]: http://go.microsoft.com/fwlink/?LinkID=286698
  [ODBC-Datenquellen-Administrator]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
  [Erweiterte Optionen]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
  [Öffnen Sie den Datenverbindungs-Assistenten.]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [Verbindungseigenschaften]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
  [Analysieren von Daten zu Flugverspätungen mit HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Hochladen von Daten zu HDInsight]: ../hdinsight-upload-data/
  [Verwenden von Sqoop mit HDInsight]: ../hdinsight-use-sqoop/
