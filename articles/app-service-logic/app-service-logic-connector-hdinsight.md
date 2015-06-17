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

Connectors können in Logik-Apps als Teil eines Datenflusses verwendet werden, um Daten im Rahmen eines Datenflusses abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. Mithilfe des HDInsight-Connectors können Sie einen Hadoop-Cluster auf Azure erstellen und verschiedene Hadoop-Aufträge übermitteln, wie z. B. Hive, Pig, MapReduce und Streaming MapReduce. Der Azure HDInsight-Dienst verwendet Apache Hadoop-Cluster in der Cloud und stellt ein Software-Framework zur Verwaltung, Analyse und Berichterstellung für große Datenmengen bereit. Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind. Mit dem HDInsight-Connector können Sie einen Cluster einrichten, einen Auftrag übermitteln und warten, bis der Auftrag abgeschlossen ist.

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

1. Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2. Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "HDInsight-Connector".
3. Geben Sie die generischen Details wie Name, App Service-Plan usw. auf dem ersten Blatt an.
4. Geben Sie als Teil der Paket-Einstellungen den HDInsight-Cluster-Benutzernamen und das Kennwort ein.


 ![][1]

## Zertifikatkonfiguration (optional) ##

Hinweis: Dieser Schritt ist erforderlich, nur, wenn Sie Verwaltungsvorgänge ausführen möchten (erstellen und Löschen von Clustern) in der Logik app.

Navigieren Sie zu der soeben erstellten API-App über "Durchsuchen" -> "API-Apps" -> <Name of the API App just created>. Sie stellen das folgende Verhalten fest. 'Sicherheit' Komponente zeigt 0 - ist dies bedeutet, dass es keine Management Zertifikat hochgeladen.

![][2]

Um Ihre App API Verwaltungszertifikat hochzuladen, müssen Sie die folgenden 1 ausführen. Klicken Sie auf die Komponente "Sicherheit" 2. Klicken Sie auf dem Upload Zertifikat im Blade "Sicherheit", das 3 geöffnet wird. Durchsuchen Sie, und wählen Sie die Zertifikatsdatei im nächsten Blade 4. Klicken Sie nach der Auswahl des Zertifikats auf "OK".

Sobald das Zertifikat erfolgreich hochgeladen wurde, werden die Zertifikatdetails angezeigt.

![][3]

Hinweis: für den Fall, dass Sie das Zertifikat ändern möchten, können Sie ein anderes Zertifikat hochladen, das um das vorhandene zu ersetzen.

## Verwendung in einer Logik-App ##

Der HDInsight-Connector kann in der Logik-App nur als eine Aktion verwendet werden. Lassen Sie uns nehmen Sie eine einfache Logik app die Cluster erstellt, führt einen "Hive" Auftrag und löscht den Cluster am Ende der Beendigung des Auftrags in Anspruch.


- Beim Erstellen/Bearbeiten einer Logik-App wählen Sie die erstellte HDInsight-Connector-API-App als Aktion aus, die dann die verfügbaren Aktionen anzeigt.

![][5]


- Wählen Sie Cluster erstellen, geben Sie alle erforderlichen Clusterparameter und klicken Sie auf die ✓.

![][6]



- Die Aktion wird jetzt in der Logik-App als konfiguriert angezeigt. Die Ausgaben der Aktion werden angezeigt und können in nachfolgenden Aktionen als Eingabe verwendet werden. 

![][7]



- Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie 'Warten, für die Erstellung des Clusters' Aktion aus, geben Sie alle erforderlichen Parameter, und klicken Sie auf ✓.

![][8]



- Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie 'Hive-Job übermitteln'-Aktion, geben Sie alle erforderlichen Parameter, und klicken Sie auf ✓.

![][9]



- Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie 'Warten, für die Beendigung des Auftrags' Aktion aus, geben Sie alle erforderlichen Parameter, und klicken Sie auf ✓.

![][10]



- Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie 'Hive-Job übermitteln'-Aktion, geben Sie alle erforderlichen Parameter, und klicken Sie auf ✓.

![][11]


Klicken Sie auf "Ausführen", um die Logik app manuell zum Testen des Szenarios zu starten.

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
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->