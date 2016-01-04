<properties
 pageTitle="Schreiben von Daten in Power BI aus Apache Storm | Microsoft Azure"
 description="Schreiben Sie Daten in Power BI aus einer C#-Topologie auf einem Apache-Storm-Cluster in HDInsight. Erstellen Sie außerdem Berichte und Echtzeit-Dashboards in Power BI."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="12/04/2015"
 ms.author="larryfr"/>

# Verwenden von Power BI (Preview) zur Visualisierung von Daten aus einer Apache Storm-Topologie

Mit der Power BI-Vorschau können Sie Daten visuell als Berichte oder Dashboards anzeigen. Mithilfe der Power BI-REST-API können Sie problemlos Daten aus einer Topologie unter Apache Storm-Cluster in HDInsight auf Power BI verwenden.

In diesem Dokument erfahren Sie, wie Sie mit Power BI aus Daten, die von einer Storm-Topologie erstellt wurden, einen Bericht und ein Dashboard erstellen.

## Voraussetzungen

- Ein Azure-Abonnement. Siehe [How to get Azure Free trial for testing Hadoop in HDInsight](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (in englischer Sprache).

* Einen Azure Active Directory-Benutzer mit [Power BI](https://powerbi.com)-Zugriff

* Visual Studio (eine der folgenden Versionen):

    * Visual Studio 2012 mit [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 mit [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) oder [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * Visual Studio 2015 [CTP6](http://visualstudio.com/downloads/visual-studio-2015-ctp-vs)

* Informationen zum Installieren der HDInsight Tools für Visual Studio finden Sie unter [Erste Schritte mit den HDInsight Tools für Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

## So funktioniert's

Dieses Beispiel enthält eine C#-Storm-Topologie, die nach dem Zufallsprinzip einen Satz generiert, ihn in Wörter teilt, die Wörter zählt und die Wörter und die Wortzahl an die Power BI-REST-API sendet. Das Nuget-Paket [PowerBi.Api.Client](https://github.com/Vtek/PowerBI.Api.Client) wird für die Kommunikation mit Power BI verwendet.

Die folgenden Dateien in diesem Projekt implementieren die spezifische Power BI-Funktionalität:

* **PowerBiBolt.cs**: implementiert den Storm-Bolt, der Daten an Power BI sendet

* **Data.cs**: Beschreibt das Datenobjekt/die Zeile, die an Power BI gesendet wird

> [AZURE.WARNING]Power BI scheint das Erstellen von mehreren Datensätzen mit demselben Namen zu ermöglichen. Dies kann auftreten, wenn der Datensatz nicht vorhanden ist und Ihre Topologie mehrere Instanzen des Power BI Bolts erstellt. Um dies zu vermeiden, müssen Sie entweder den Parallelitätshinweis des Bolts auf 1 festlegen (wie in diesem Beispiel) oder den Datensatz vor dem Bereitstellen der Topologie erstellen.
>
> Die in dieser Lösung enthaltene **CreateDataset**-Konsolenanwendung dient als Beispiel zum Erstellen des Datensatzes außerhalb der Topologie.

## Registrieren einer Power BI-Anwendung

1. Führen Sie die Schritte im [Power BI-Schnellstart](https://msdn.microsoft.com/library/dn931989.aspx) durch, um sich bei Power BI anzumelden.

2. Führen Sie die Schritte in [Registrieren einer Anwendung](https://msdn.microsoft.com/library/dn877542.aspx) durch, um eine Anwendungsregistrierung zu erstellen. Diese wird beim Zugriff auf die Power BI-REST-API benötigt.

    > [AZURE.IMPORTANT]Speichern Sie die **Client-ID** für die Anwendungsregistrierung.

## Das Beispiel herunterladen

Herunterladen des [HDInsight C# Storm Power BI-Beispiels](https://github.com/Blackmist/hdinsight-csharp-storm-powerbi). Zum Herunterladen verzweigen/klonen Sie es entweder mit [Git](http://git-scm.com/) oder verwenden Sie den **Download**-Link zum Herunterladen einer ZIP-Datei aus dem Archiv.

## Das Beispiel konfigurieren

1. Öffnen Sie das Beispiel in Visual Studio. Öffnen Sie vom **Projektmappen-Explorer** die Datei **SCPHost.exe.config** und suchen Sie dann das Element **<OAuth .../>**. Geben Sie Werte für die folgenden Eigenschaften dieses Elements an:

    * **Client-ID**: Die Client-ID für die Anwendungsregistrierung, die Sie zuvor erstellt haben.

    * **Benutzer**: Ein Azure Active Directory-Konto, das Zugriff auf Power BI hat.

    * **Kennwort**: Das Kennwort für das Azure Active Directory-Konto.

2. (Optional). Der von diesem Projekt verwendete Standardname für den Datensatz ist **Wörter**. Um dies zu ändern, klicken Sie mit der rechten Maustaste auf das Projekt **WordCount** im **Projektmappen-Explorer**, wählen Sie **Eigenschaften** aus und anschließend **Einstellungen**. Ändern Sie den Eintrag **DatasetName** in den gewünschten Wert.

2. Speichern und schließen Sie die Dateien.

## Bereitstellen des Beispiels

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **WordCount** und wählen Sie **An Storm in HDInsight übermitteln** aus. Wählen Sie den HDInsight-Cluster aus dem Dropdown-Dialogfeld **Storm-Cluster** aus.

    > [AZURE.NOTE]Es kann einige Sekunden dauern, bis die Dropdown-Liste **Storm-Cluster** mit Servernamen befüllt wird.
    >
    > Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen für Ihr Azure-Abonnement ein. Wenn Sie über mehrere Abonnements verfügen, melden Sie sich bei dem Abonnement an, das Ihren Storm-Cluster in HDInsight enthält.

2. Sobald die Topologie erfolgreich übermittelt wurde, sollten die Storm-Topologien für den Cluster angezeigt werden. Wählen Sie die WordCount-Topologie aus der Liste aus, um Informationen zur aktiven Topologie anzuzeigen.

    ![Die Topologien mit der ausgewählten WordCount-Topologie](./media/hdinsight-storm-power-bi-topology/topologysummary.png)

    > [AZURE.NOTE]Sie können die Storm-Topologien auch im Server-Explorer anzeigen, indem Sie Azure, HDInsight erweitern und dann mit der rechten Maustaste auf einen Storm-Cluster in HDInsight klicken und Storm-Topologien anzeigen auswählen.

3. Beim Anzeigen der **Topologie-Zusammenfassung** blättern Sie solange, bis Sie zum Abschnitt **Bolts** gelangen. Beachten Sie in diesem Abschnitt die Spalte **Ausgeführt** für den **PowerBI**-Bolt. Zum Aktualisieren verwenden Sie die Schaltfläche "Aktualisieren" am oberen Rand der Seite, bis der Wert sich in einen anderen als 0 (Null) ändert. Wenn diese Zahl größer wird, bedeutet dies, dass Elemente in Power BI geschrieben werden.

## Erstellen eines Berichts

1. Besuchen Sie in einem Browser [https://PowerBI.com](https://powerbi.com). Melden Sie sich mit Ihrem Konto an.

2. Erweitern Sie im linken Bereich der Seite **Datensätze**. Wählen Sie den Eintrag **Wörter**. Dies ist der Datensatz, der von der Beispiels-Topologie erstellt wurde.

    ![Datensatzeintrag von Wörtern](./media/hdinsight-storm-power-bi-topology/words.png)

3. Im Bereich **Felder** erweitern Sie **WordCount**. Ziehen Sie die **Count-** und **Word-**Einträge in den mittleren Bereich der Seite. Dadurch wird ein neues Diagramm erstellt, das einen Balken für jedes Wort anzeigt. Dieser gibt an, wie oft das Wort aufgetreten ist.

    ![Wortzählungs-Diagramm](./media/hdinsight-storm-power-bi-topology/wordcountchart.png)

4. Wählen Sie oben links auf der Seite **Speichern**, um einen neuen Bericht zu erstellen. Geben Sie **Word Count** als den Namen des Berichts ein.

5. Wählen Sie das Power BI-Logo, um zurück zum Dashboard zu gelangen. Der Bericht **Word Count** erscheint jetzt unter **Berichte**.

## Erstellen Sie ein Live-Dashboard

1. Wählen Sie neben **Dashboard** das Symbol **+**, um ein neues Dashboard zu erstellen. Nennen Sie das neue Dashboard **Live Word Count**.

2. Wählen Sie den Bericht **Word Count**, den Sie zuvor erstellt haben. Sobald es angezeigt wird, wählen Sie das Diagramm und wählen Sie dann das Heftzweckensymbol oben rechts im Diagramm. Sie erhalten eine Benachrichtigung, dass es dem Dashboard angeheftet wurde.

    ![Diagramm mit angezeigtem Heftzweckensymbol](./media/hdinsight-storm-power-bi-topology/pushpin.png)

2. Wählen Sie das Power BI-Logo, um zurück zum Dashboard zu gelangen. Wählen Sie das Dashboard **Live Word Count**. Es enthält jetzt das Wortzählungsdiagramm, das aktualisiert wird, wenn neue Einträge aus der WordCount-Topologie auf HDInsight an Power BI gesendet werden.

    ![Das Live-Dashboard](./media/hdinsight-storm-power-bi-topology/dashboard.png)

## Beenden Sie die WordCount-Topologie

Die Topologie wird weiterhin ausgeführt, bis Sie sie beenden oder das Storm-Cluster in HDInsight löschen. Führen Sie folgende Schritte aus, um die Topologie anzuhalten.

1. Öffnen Sie in Visual Studio das Fenster **Topologie-Zusammenfassung** für die WordCount-Topologie. Wenn dieTopologie-Zusammenfassung nicht bereits geöffnet ist, gehen Sie zum **Server-Explorer**, erweitern Sie die **Azure**- und **HDInsight**-Einträge, klicken Sie mit der rechten Maustaste auf das Storm-Cluster in HDInsight, und wählen Sie **Storm-Topologien anzeigen** aus. Wählen Sie abschließend die **WordCount**-Topologie.

2. Wählen Sie die Schaltfläche **Beenden**, um die **WordCount**-Topologie anzuhalten.

    ![Schaltfläche Beenden in der Topologie-Zusammenfassung](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Daten aus einer Storm-Topologie an Power BI mithilfe von REST gesendet werden. Informationen zum Arbeiten mit anderen Azure-Technologien finden Sie hier:

* [Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_1210_2015-->