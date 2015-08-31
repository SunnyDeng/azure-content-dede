<properties
   pageTitle="HDInsight-Connector"
   description="Verwenden des HDInsight-Connectors in Azure App Service"
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
   ms.date="08/19/2015"
   ms.author="sameerch"/>


# Microsoft HDInsight-Connector #

Connectors können in Logik-Apps verwendet werden, um Daten im Rahmen eines Datenflusses abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. Mithilfe des HDInsight-Connectors können Sie einen Hadoop-Cluster auf Azure erstellen und verschiedene Hadoop-Aufträge übermitteln, z. B. Hive, Pig, MapReduce und Streaming MapReduce. Der Azure HDInsight-Dienst verwendet Apache Hadoop-Cluster in der Cloud und stellt ein Software-Framework zur Verwaltung, Analyse und Berichterstellung für große Datenmengen bereit. Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind. Mit dem HDInsight-Connector können Sie einen Cluster erstellen oder löschen, einen Auftrag übermitteln und warten, bis der Auftrag abgeschlossen ist.

### Grundlegende Aktionen

- Cluster erstellen
- Auf die Erstellung des Clusters warten
- Pig-Auftrag übermitteln
- Hive-Auftrag übermitteln
- MapReduce-Auftrag übermitteln
- Beendigung des Auftrags abwarten
- Cluster löschen


## Erstellen der HDInsight-Connector-API-App ##

Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "HDInsight-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Geben Sie den Namen, die App Service-Plan und andere Eigenschaften ein.
4. Geben Sie in den Paket-Einstellungen den HDInsight-Cluster-Benutzernamen und das Kennwort ein. Wählen Sie **OK**.
5. Klicken Sie auf **Erstellen**: ![][1]  

## Zertifikatkonfiguration (optional) ##

> [AZURE.NOTE]Dieser Schritt ist nur erforderlich, wenn Sie Verwaltungsvorgänge in der Logik-App ausführen möchten (Erstellen und Löschen von Clustern).

Suchen Sie nach der gerade erstellten HDInsight-Connector-API-App, und Sie sehen, dass die Komponente "Sicherheit" 0 anzeigt. Dies bedeutet, dass kein Verwaltungszertifikat hochgeladen ist: ![][2]

Führen Sie folgende Schritte aus, um das Verwaltungszertifikat in Ihre API-App hochzuladen:

1. Wählen Sie die Komponente "Sicherheit".
2. Wählen Sie im Blatt "Sicherheit" **ZERTIFIKAT HOCHLADEN**.
3. Suchen und wählen Sie die Zertifikatdatei im nächsten Blatt aus.
4. Wählen Sie nach der Auswahl des Zertifikats **OK**.

Sobald das Zertifikat erfolgreich hochgeladen wurde, werden die Zertifikatdetails angezeigt: ![][3]

> [AZURE.NOTE]Wenn Sie das Zertifikat ändern möchten, können Sie ein anderes Zertifikat hochladen. Dadurch wird das vorhandene ersetzt.

## Verwenden des Connectors in einer Logik-App ##

Der HDInsight-Connector kann in der Logik-App nur als eine Aktion verwendet werden. Nehmen wir eine einfache Logik-App, die einen Cluster erstellt, einen "Hive"-Auftrag ausführt und den Cluster nach Abschluss des Auftrags löscht.


1. Klicken Sie in der Karte "Logik starten" auf "Diese Logik manuell ausführen".
2. Wählen Sie die aus dem Katalog erstellte HDInsight-Connector-API-App aus. Die verfügbaren Aktionen werden hier aufgeführt: ![][5]

3. Wählen Sie "Cluster erstellen" aus, geben Sie alle erforderlichen Clusterparameter ein, und wählen Sie ✓: ![][6]

4. Die Aktion wird jetzt in der Logik-App als konfiguriert angezeigt. Die Ausgaben der Aktion werden angezeigt und können in nachfolgenden Aktionen als Eingabe verwendet werden: ![][7]

5. Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie die Aktion "Auf die Erstellung des Clusters warten" aus, geben Sie alle erforderlichen Parameter ein, und wählen Sie ✓: ![][8]

6. Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie die Aktion "Hive-Auftrag übermitteln" aus, geben Sie alle erforderlichen Parameter ein, und wählen Sie ✓: ![][9]

7. Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie die Aktion "Beendigung des Auftrags abwarten" aus, geben Sie alle erforderlichen Parameter ein, und wählen Sie ✓: ![][10]

8. Wählen Sie denselben HDInsight-Connector aus dem Katalog als Aktion aus. Wählen Sie die Aktion "Cluster löschen" aus, geben Sie alle erforderlichen Parameter ein, und wählen Sie ✓: ![][11]

9. Speichern Sie die Logik-App mithilfe des "Speichern"-Befehls am oberen Rand des Designers.

Wählen Sie **Jetzt ausführen**, um die Logik-App manuell zu starten und das Szenario zu testen.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


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

<!---HONumber=August15_HO8-->