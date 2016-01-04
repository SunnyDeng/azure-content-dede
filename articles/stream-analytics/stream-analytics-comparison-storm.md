<properties
	pageTitle="Analyseplattformen: Vergleich von Apache Storm mit Stream Analytics | Microsoft Azure"
	description="Hier finden Sie anhand eines Vergleichs von Apache Storm mit Stream Analytics Hilfe beim Auswählen einer Cloudanalyseplattform. Lernen Sie die Funktionen und Unterschiede kennen."
	keywords="Analyseplattform, Analyseplattformen, Cloudanalyseplattform, Storm-Vergleich"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="11/06/2015"
	ms.author="jeffstok"/>

# Hilfe beim Auswählen einer Stream Analytics-Plattform: Vergleich von Apache Storm mit Azure Stream Analytics

Hier finden Sie anhand eines Vergleichs von Apache Storm mit Azure Stream Analytics Hilfe beim Auswählen einer Cloudanalyseplattform. Finden Sie heraus, welche Wertbeiträge Stream Analytics im Vergleich mit Apache Storm als verwalteter Dienst in Azure HDInsight bietet, damit Sie die richtige Lösung für die Anwendungsfälle in Ihrem Unternehmen auswählen können.

Beide Analyseplattformen bieten Vorteile einer PaaS-Lösung, es gibt aber einige charakteristische Hauptfunktionen, durch die sie sich unterscheiden. Funktionen sowie die Einschränkungen dieser Dienste sind im Folgenden aufgeführt, um Ihnen dabei zu helfen, die Lösung zu finden, die Sie benötigen, um Ihre Ziele zu erreichen.

## Vergleich von Storm mit Stream Analytics: allgemeine Funktionen ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Open Source</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Nein, Azure Stream Analytics ist eine Microsoft-eigenes Angebot.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Ja, Apache Storm ist durch Apache lizenzierte Technologie.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Von Microsoft unterstützt</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Ja
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Ja
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Hardwareanforderungen</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Es bestehen keine Hardwareanforderungen. Azure Stream Analytics ist ein Azure-Dienst.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Es bestehen keine Hardwareanforderungen. Apache Storm ist ein Azure-Dienst.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Einheit der obersten Ebene</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Mit Azure Stream Analytics stellen Kunden Streamingaufträge bereit und überwachen diese.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Mit Apache Storm auf HDInsight stellen Kunden ein ganze Cluster bereit und überwachen dieses. Das Cluster kann mehrere Storm-Aufträge sowie andere Workloads (inkl. Batch) hosten.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Preis</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Stream Analytics wird nach verarbeiteter Datenmenge und Anzahl der erforderlichen Streamingeinheiten (pro Stunde, die der Auftrag ausgeführt wird) abgerechnet.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Weitere Preisinformationen finden Sie hier.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Für Apache Storm auf HDInsight basiert die Einheit des Kaufs auf einem Cluster und wird basierend auf der Zeit, die der Cluster läuft und unabhängig von den bereitgestellten Aufträgen in Rechnung gestellt.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/hdinsight/">Weitere Preisinformationen finden Sie hier.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Erstellung auf den Analyseplattformen ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funktionen: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Ja, es ist eine benutzerfreundliche SQL-Sprachunterstützung verfügbar.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Nein, Benutzer müssen Code in Java C# schreiben oder Trident-APIs verwenden.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funktionen: Zeitoperatoren</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Fensteraggregate und temporäre Joins werden standardmäßig unterstützt.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Zeitoperatoren müssen vom Benutzer implementiert werden.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Entwicklungserfahrung</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Interaktive Erstellung und Debuggen über Azure-Portal für Beispieldaten.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Entwicklung, Debuggen und Überwachung wird über Visual Studio für .NET Benutzer zur Verfügung gestellt, während Entwickler für Java und anderen Sprachen die IDE ihrer Wahl verwenden können.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Debugging-Unterstützung</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Stream Analytics bietet grundlegenden Auftragsstatus und Vorgangsprotokolle als eine Möglichkeit zum Debuggen; derzeit bietet es allerdings keine Flexibilität bezüglich was/wie viel in den Protokollen enthalten ist, z. B. ausführlicher Modus.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Für Debuggingzwecke sind detaillierte Protokolle verfügbar. Es gibt zwei Möglichkeiten, einem Benutzer Protokolle vorzulegen, über Visual Studio oder der Benutzer kann über RDP in den Cluster, um auf Protokolle zuzugreifen.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unterstützung für UDF (benutzerdefinierte Funktionen)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Es gibt derzeit keine Unterstützung für benutzerdefinierte Funktionen.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Benutzerdefinierte Funktionen können in C#, Java oder der von Ihnen gewünschten Sprache geschrieben werden.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Erweiterbar - benutzerdefinierter Code </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Es gibt keine Unterstützung für erweiterbarem Code in Stream Analytics.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Ja, es kann benutzerdefinierter Code in C#, Java oder anderen unterstützten Sprachen auf Storm geschrieben werden.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Datenquellen und Ausgaben ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
		<tr>
            <td width="174" valign="top">
				<p>
				 <strong>Datenquellen Eingabe</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Die unterstützten Eingabequellen sind Azure Event Hubs und Azure-Blobs.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Für Event Hubs, Service Bus, Kafka usw. stehen Connectors zur Verfügung. Nicht unterstützte Connectors können über benutzerdefinierten Code implementiert werden.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Datenformate Eingabe</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Unterstützte Eingabeformate sind Avro, JSON und CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Jedes Format kann über benutzerdefinierten Code implementiert werden.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Ausgaben</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Streaming-Job kann mehrere Ausgaben haben. Unterstützte Ausgaben: Azure Event Hubs, Azure-Blob-Speicher, Azure-Tabellen, Azure SQL-Datenbank und PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Unterstützung für viele Ausgaben in einer Topologie, jede Ausgabe kann eine benutzerdefinierte Logik für die Downstreamverarbeitung haben. Standardmäßig enthält Storm Connectors für PowerBI, Ereignis-Hubs von Azure, Azure-Blob-Speicher, Azure-DocumentDB, SQL und HBase. Nicht unterstützte Connectors können über benutzerdefinierten Code implementiert werden.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Datenverschlüssselungsformate</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Stream Analytics erfordert die Verwendung des Datenformats UTF-8.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Jedes Datenverschlüsselungsformat kann über benutzerdefinierten Code implementiert werden.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Verwaltung und Abläufe ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Auftragbereitstellungsmodell</strong>
                </p>
                <p>
                    - <strong>Azure-Portal</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Bereitstellung wird über Azure-Portal, PowerShell und REST-APIs implementiert.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Bereitstellung wird über Azure-Portal, PowerShell, Visual Studio und REST-APIs implementiert.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Überwachung (Statistiken, Indikatoren usw.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Überwachung wird über Azure Portal und REST-APIs implementiert.
                </p>
                <p>
                    Der Benutzer kann auch Azure-Warnungen konfigurieren.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Überwachung wird über die Storm-Benutzeroberfläche und REST-APIs implementiert.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Skalierbarkeit</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Anzahl der Streaming-Einheiten für jeden Auftrag. Jede Streaming-Einheit verarbeitet bis zu 1 MB/s. Standardmäßig maximal 50 Einheiten. Aufruf zum Anheben der Beschränkung.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Anzahl der Knoten im HDI Storm-Cluster. Unbegrenzte Anzahl von Knoten (obere Grenze durch Ihr Azure-Kontingent definiert). Aufruf zum Anheben der Beschränkung.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Datenverarbeitungslimits</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Benutzer können die Anzahl der Streaming-Einheiten hoch- oder herunterskalieren, um die Datenverarbeitung zu erhöhen bzw. Kosten zu optimieren.
                </p>
                <p>
                    Hochskalieren auf 1 GB/s
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Benutzer kann die Clustergröße hoch- oder herunterskalieren, um den Anforderungen gerecht zu werden.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Anhalten/Fortsetzen</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Anhalten und von letzter Stoppstelle aus fortsetzen.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Anhalten und fortsetzen basierend auf dem Wasserzeichen von der letzten Stoppstelle aus.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Dienst- und Framework-Update</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Automatisches Patchen ohne Ausfallzeiten.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Automatisches Patchen ohne Ausfallzeiten.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Geschäftskontinuität durch einen hoch verfügbaren Dienst mit garantierten SLAs</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    SLA von 99,9 % Verfügbarkeit
                </p>
                <p>
                    Automatische Wiederherstellung nach Fehlern
                </p>
                <p>
                    Wiederherstellung von statusbehafteten Zeitoperatoren ist integriert.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    SLA von 99,9 % Verfügbarkeit des Storm-Clusters. Apache Storm ist eine fehlertolerante Streaming-Plattform, aber es liegt in der Verantwortung des Kunden, sicherzustellen, dass seine Streamingaufträge ohne Unterbrechung ausgeführt werden.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Erweiterte Funktionen ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Verspäteter Eingang und Störereignisbehandlung</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Integrierte konfigurierbare Richtlinien für Neuanordnung, Löschen von Ereignissen oder Anpassen der Ereigniszeit.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Benutzer muss die Logik zum Behandeln dieses Szenarios implementieren.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Verweisdaten</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Referenzdaten von Azure-Blobs sind mit einer maximalen Größe von 100 MB des In-Memory-Suchcaches im Arbeitsspeicher verfügbar. Das Aktualisieren von Verweisdaten wird vom Dienst verwaltet.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Keine Beschränkungen für die Datengröße. Connectors sind für HBase, DocumentDB, SQL Server und Azure verfügbar. Nicht unterstützte Connectors können über benutzerdefinierten Code implementiert werden.
                </p>
                <p>
                    Das Aktualisieren von Verweisdaten muss von benutzerdefiniertem Code behandelt werden.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integration mit Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Durch Konfiguration veröffentlichter Azure Machine Learning-Modelle als Funktionen während der ASA-Auftragserstellung <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(private Vorschau)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Über Storm Bolts verfügbar.
                </p>
            </td>
        </tr>
    </tbody>
</table>

<!---HONumber=AcomDC_1217_2015-->