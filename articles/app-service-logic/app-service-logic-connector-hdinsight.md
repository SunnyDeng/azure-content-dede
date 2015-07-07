<properties 
   pageTitle="HDInsight-Connector" 
   description="Verwenden des HDInsight-Connectors" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Microsoft HDInsight-Connector #

Connectors können in Logik-Apps als Teil eines Datenflusses verwendet werden, um Daten im Rahmen eines Datenflusses abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. Mithilfe des HDInsight-Connectors können Sie einen Hadoop-Cluster auf Azure erstellen und verschiedene Hadoop-Aufträge übermitteln, wie z. B. Hive, Pig, MapReduce und Streaming MapReduce. Der Azure HDInsight-Dienst verwendet Apache Hadoop-Cluster in der Cloud und stellt ein Software-Framework zur Verwaltung, Analyse und Berichterstellung für große Datenmengen bereit. Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind. Mit dem HDInsight-Connector können Sie einen Cluster erstellen oder löschen, einen Auftrag übermitteln und warten, bis der Auftrag abgeschlossen ist.

###Grundlegende Aktionen
		
- Cluster erstellen
- Auf die Erstellung des Clusters warten
- Pig-Auftrag übermitteln
- Hive-Auftrag übermitteln
- MapReduce-Auftrag übermitteln
- Beendigung des Auftrags abwarten
- Cluster löschen


## Erstellen einer Instanz der HDInsight-Connector-API-App ##

Zur Verwendung des HDInsight-Connectors müssen Sie zunächst eine Instanz der HDInsight-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1. Öffnen Sie den Azure Marketplace mit der Option "+NEU" auf der linken Seite im Azure-Portal, und wählen Sie "Marketplace" aus.
2. Wechseln Sie zu "API-Apps", und suchen Sie nach "HDInsight-Connector". Wählen Sie diesen aus und klicken Sie auf "Erstellen".
3. Geben Sie die allgemeinen Details wie Name, App Service-Plan usw. auf dem ersten Blatt an.
4. Geben Sie als Teil der Paket-Einstellungen den HDInsight-Cluster-Benutzernamen und das Kennwort ein. Klicken Sie dann auf "OK".
5. Klicken Sie auf "Erstellen".


 ![][1]

## Zertifikatkonfiguration (optional) ##

Hinweis: Dieser Schritt ist nur erforderlich, wenn Sie Verwaltungsvorgänge in der Logik-App ausführen möchten (Erstellen und Löschen von Clustern).

Suchen Sie nach der gerade erstellten HDInsight-Connector-API-App, und Sie sehen, dass die Komponente "Sicherheit" 0 anzeigt. Dies bedeutet, dass kein Verwaltungszertifikat hochgeladen ist.

![][2]

Führen Sie folgende Schritte aus, um das Verwaltungszertifikat in Ihre API-App hochzuladen: 1. Klicken Sie auf die Komponente "Sicherheit". 2. Klicken Sie im Blatt "Sicherheit" auf "ZERTIFIKAT HOCHLADEN". 3. Suchen und wählen Sie die Zertifikatdatei im nächsten Blatt aus. 4. Klicken Sie nach der Auswahl des Zertifikats auf "OK".

Sobald das Zertifikat erfolgreich hochgeladen wurde, werden die Zertifikatdetails angezeigt.

![][3]

Hinweis: Für den Fall, dass Sie das Zertifikat ändern möchten, können Sie ein anderes Zertifikat hochladen, um das vorhandene zu ersetzen.

## Verwendung in einer Logik-App ##

Der HDInsight-Connector kann in der Logik-App nur als eine Aktion verwendet werden. Nehmen wir eine einfache Logik-App, die einen Cluster erstellt, einen "Hive"-Auftrag ausführt und den Cluster nach Abschluss des Auftrags löscht.


- Klicken Sie in der Karte "Logik starten" auf "Diese Logik manuell ausführen".
- Wählen Sie die aus dem Katalog erstellte HDInsight-Connector-API-App aus, sodass alle verfügbaren Aktionen angezeigt werden.

![][5]


- Wählen Sie "Cluster erstellen" aus, geben Sie alle erforderlichen Clusterparameter an, und klicken Sie auf ✓.

![][6]



- Die Aktion wird jetzt in der Logik-App als konfiguriert angezeigt. Die Ausgaben der Aktion werden angezeigt und können in nachfolgenden Aktionen als Eingabe verwendet werden. 

![][7]



- Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie die Aktion "Auf die Erstellung des Clusters" warten aus, geben Sie alle erforderlichen Parameter an, und klicken Sie auf ✓.

![][8]



- Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie die Aktion "Hive-Auftrag übermitteln" aus, geben Sie alle erforderlichen Parameter an, und klicken Sie auf ✓.

![][9]



- Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie die Aktion "Beendigung des Auftrags abwarten" aus, geben Sie alle erforderlichen Parameter an, und klicken Sie auf ✓.

![][10]



- Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie die Aktion "Cluster löschen" aus, geben Sie alle erforderlichen Parameter an, und klicken Sie auf ✓.

![][11]

- Speichern Sie die Logik-App mithilfe des "Speichern"-Befehls am oberen Rand der Designeroberfläche.

Sie können auf "Jetzt ausführen" klicken, um die Logik-App zu starten und das Szenario manuell zu testen.

<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.jpg
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.jpg
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.jpg
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.jpg
 

<!---HONumber=62-->