<properties 
	pageTitle="Erste Schritte mit Application Insights mit Java in Eclipse" 
	description="Verwenden Sie das Eclipse-Plug-In zum Hinzufügen einer Leistungs- und Nutzungsüberwachung zu Ihrer Java-Website mit Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2016" 
	ms.author="awills"/>
 
# Erste Schritte mit Application Insights mit Java in Eclipse

Das Application Insights SDK sendet Telemetriedaten aus Ihrer Java-Anwendung, damit Sie die Nutzung und Leistung analysieren können. Das Eclipse-Plug-In für Application Insights installiert das SDK automatisch in Ihrem Projekt, damit Sie ohne Konfiguration Telemetriedaten sowie eine API verwenden können, mit der Sie benutzerdefinierte Telemetrie programmieren können.


## Voraussetzungen

Derzeit funktioniert das Plug-In für Maven-Projekte und für dynamische Webprojekte in Eclipse. ([Hinzufügen von Application Insights zu anderen Arten von Java-Projekten][java].)

Sie benötigen Folgendes:

* Oracle JRE 1.6 oder höher
* Ein Abonnement für [Microsoft Azure](https://azure.microsoft.com/). (Sie können mit der [kostenlosen Testversion](https://azure.microsoft.com/pricing/free-trial/) beginnen.)
* [Eclipse IDE für Java EE-Entwickler](http://www.eclipse.org/downloads/), Indigo oder höher.
* Windows 7 oder höher oder Windows Server 2008 oder höher

## Installation des SDK in Eclipse (einmalig)

Sie müssen diesen Schritt nur einmal pro Computer durchführen. Bei diesem Schritt wird ein Toolkit installiert, mit dem dann das SDK jedem dynamischen Webprojekt hinzugefügt werden kann.

1. Klicken Sie in Eclipse auf "Hilfe" und dann auf "Neue Software installieren".

    ![Hilfe, Neue Software installieren](./media/app-insights-java-eclipse/0-plugin.png)

2. Das SDK befindet sich in http://dl.windowsazure.com/eclipse unter "Azure Toolkit".
3. Deaktivieren Sie **Contact all update sites...**.

    ![Deaktivieren Sie für das Application Insights SDK "Contact all update sites"](./media/app-insights-java-eclipse/1-plugin.png)

Führen für jedes Java-Projekt die verbleibenden Schritte aus.

## Erstellen einer Application Insights-Ressource in Azure

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Erstellen Sie eine neue Application Insights-Ressource.  

    ![Klicken Sie auf +, und wählen Sie "Application Insights"](./media/app-insights-java-eclipse/01-create.png)  
3. Legen Sie den Anwendungstyp auf "Java-Webanwendung" fest.  

    ![Geben Sie einen Namen ein, wählen Sie "Java-Web-App", und klicken Sie auf "Erstellen"](./media/app-insights-java-eclipse/02-create.png)  
4. Suchen Sie den Instrumentationsschlüssel der neuen Ressource. Sie müssen ihn in Kürze in Ihr Codeprojekt einfügen.  

    ![Klicken Sie in der Übersicht über neue Ressourcen auf "Eigenschaften", und kopieren Sie den Instrumentationsschlüssel](./media/app-insights-java-eclipse/03-key.png)


## Hinzufügen von Application Insights zu Ihrem Projekt

1. Fügen Sie Application Insights über das Kontextmenü Ihres Java-Webprojekts hinzu.

    ![Klicken Sie in der Übersicht über neue Ressourcen auf "Eigenschaften", und kopieren Sie den Instrumentationsschlüssel](./media/app-insights-java-eclipse/02-context-menu.png)

2. Fügen Sie den Instrumentationsschlüssel ein, den Sie aus dem Azure-Portal abgerufen haben.

    ![Klicken Sie in der Übersicht über neue Ressourcen auf "Eigenschaften", und kopieren Sie den Instrumentationsschlüssel](./media/app-insights-java-eclipse/03-ikey.png)


Der Schlüssel wird zusammen mit jedem Telemetrieelement übermittelt und weist Application Insights an, es in Ihrer Ressource anzuzeigen.

## Ausführen der Anwendung und Anzeigen von Metriken

Führen Sie Ihre Anwendung aus.

Kehren Sie zur Application Insights-Ressource in Microsoft Azure zurück.

HTTP-Anforderungsdaten werden auf dem Blatt "Übersicht" angezeigt. (Wenn sie nicht vorhanden sind, warten Sie einige Sekunden, und klicken Sie dann auf "Aktualisieren".)

![Serverantwort, Anforderungsanzahl und Fehler](./media/app-insights-java-eclipse/5-results.png)
 

Klicken Sie sich durch ein beliebiges Diagramm, um ausführlichere Metriken anzuzeigen.

![Anzahlen nach Name anfordern](./media/app-insights-java-eclipse/6-barchart.png)


[Weitere Informationen zu Metriken.][metrics]

 

Beim Anzeigen der Eigenschaften einer Anforderung können Sie die damit verbundenen Telemetrieereignisse erkennen, wie z. B. Anforderungen und Ausnahmen.
 
![Alle Ablaufverfolgungen für diese Anforderung](./media/app-insights-java-eclipse/7-instance.png)


## Clientseitige Telemetrie

Klicken Sie auf dem Blatt "Schnellstart" auf die Option zum Abrufen von Code, um Webseiten zu überwachen:

![Wählen Sie in Ihrer App auf dem Blatt "Übersicht" im Schnellstart die Option "Code abrufen", um Ihre Webseiten zu überwachen. Kopieren Sie das Skript.](./media/app-insights-java-eclipse/02-monitor-web-page.png)

Fügen Sie den Codeausschnitt im Kopf der HTML-Dateien ein.

#### Anzeigen von clientseitigen Daten

Öffnen Sie die aktualisierten Webseiten, und verwenden Sie sie. Warten Sie ein oder zwei Minuten, kehren Sie dann zurück zu Application Insights, und öffnen Sie das Blatt "Verwendung". (Führen Sie auf dem Blatt "Übersicht" einen Bildlauf nach unten durch, und klicken Sie auf "Verwendung".)

Seitenansichts-, Benutzer- und Sitzungsmetriken werden auf dem Blatt "Verwendung" angezeigt:

![Sitzungen, Benutzer und Seitenaufrufe](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[Weitere Informationen zum Einrichten clientseitiger Telemetrie.][usage]

## Veröffentlichen der Anwendung

Jetzt veröffentlichen Sie Ihre App auf dem Server, erlauben deren Benutzung und sehen sich an, wie die Telemetrie im Portal angezeigt wird.

* Stellen Sie sicher, dass die Firewall der Anwendung das Senden von Telemetrie an die folgenden Ports erlaubt:

 * dc.services.visualstudio.com:443
 * dc.services.visualstudio.com:80
 * f5.services.visualstudio.com:443
 * f5.services.visualstudio.com:80


* Installieren Sie auf Windows-Servern:

 * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Damit werden Leistungsindikatoren aktiviert).

## Ausnahmen und Anforderungsfehler

Nicht behandelte Ausnahmen werden automatisch gesammelt:

![](./media/app-insights-java-eclipse/21-exceptions.png)

Um Daten zu anderen Ausnahmen zu erfassen, haben Sie zwei Möglichkeiten:

* [Fügen Sie TrackException-Aufrufe in den Code ein](app-insights-api-custom-events-metrics.md#track-exception). 
* [Installieren Sie den Java-Agent auf dem Server](app-insights-java-agent.md). Sie geben die Methoden an, die Sie überwachen möchten.


## Überwachen von Methodenaufrufen und externen Abhängigkeiten

[Installieren Sie den Java-Agent](app-insights-java-agent.md), um die angegebenen internen Methoden und Aufrufe über JDBC mit Zeitdaten zu protokollieren.


## Leistungsindikatoren

Führen Sie auf dem Blatt "Übersicht" einen Bildlauf nach unten aus, und klicken Sie auf die Kachel **Server**. Ein Bereich mit Leistungsindikatoren wird angezeigt.


![Scrollen Sie nach unten aus und klicken Sie auf die Kachel „Server“.](./media/app-insights-java-eclipse/11-perf-counters.png)

### Anpassen der Erfassung von Leistungsindikatoren

Um die Erfassung der Standardgruppe von Leistungsindikatoren zu deaktivieren, fügen Sie unter dem Stammknoten der Datei „ApplicationInsights.xml“ den folgenden Code hinzu:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Erfassen weiterer Leistungsindikatoren

Sie können weitere Leistungsindikatoren angeben, die erfasst werden sollen.

#### JMX-Leistungsindikatoren (von der Java Virtual Machine bereitgestellt)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName` – Der im Application Insights-Portal angezeigte Name.
*	`objectName` – Der JMX-Objektname.
*	`attribute` – Das Attribut des abzurufenden JMX-Objektnamens
*	`type` (optional) – Der Typ des Attributs des JMX-Objekts:
 *	Standard: ein einfacher Typ wie "int" oder "long".
 *	`composite`: Die Leistungsindikatordaten haben das Format 'Attribut.Daten'.
 *	`tabular`: Die Leistungsindikatordaten haben das Format einer Tabellenzeile.



#### Windows-Leistungsindikatoren

Jeder [Windows-Leistungsindikator](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) gehört zu einer Kategorie (genauso wie ein Feld zu einer Klasse gehört). Kategorien können entweder global sein oder nummerierte oder benannte Instanzen haben.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName – Der im Application Insights-Portal angezeigte Name
*	categoryName – Die Leistungsindikatorkategorie (Leistungsobjekt), der dieser Leistungsindikator zugeordnet ist
*	counterName – Der Name des Leistungsindikators
*	instanceName – Der Name der Instanz der Leistungsindikatorkategorie oder eine leere Zeichenfolge (""), wenn die Kategorie eine einzelne Instanz enthält. Wenn "categoryName" auf "Process" festgelegt ist und der Leistungsindikator, den Sie erfassen möchten, aus dem aktuellen JVM-Prozess stammt, in dem Ihre Anwendung ausgeführt wird, geben Sie `"__SELF__"` an.

Ihre Leistungsindikatoren werden im [Metrik-Explorer][metrics] als benutzerdefinierte Metriken angezeigt.

![](./media/app-insights-java-eclipse/12-custom-perfs.png)


### Unix-Leistungsindikatoren

* [Installieren Sie collectd mit dem Application Insights-Plug-In](app-insights-java-collectd.md), um eine Vielzahl von System- und Netzwerkdaten abzurufen.

## Verfügbarkeitswebtests

Application Insights kann Ihre Website in regelmäßigen Abständen testen, um zu überprüfen, ob sie betriebsbereit ist und gut reagiert. [Zum Einrichten][availability] scrollen Sie nach unten und klicken auf „Verfügbarkeit“.

![Scrollen Sie nach unten, klicken Sie auf „Verfügbarkeit“ und dann auf „Webtest hinzufügen“.](./media/app-insights-java-eclipse/31-config-web-test.png)

Sie erhalten Diagramme von Reaktionszeiten sowie E-Mail-Benachrichtigungen, falls Ihre Website ausfällt.

![Beispiel für Webtest](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[Weitere Informationen zu Verfügbarkeitswebtests.][availability]

## Diagnoseprotokolle

Wenn Sie für die Ablaufverfolgung Logback oder Log4J (Version 1.2 bzw. 2.0) verwenden, werden Ihre Ablaufverfolgungsprotokolle automatisch an Application Insights gesendet. Hier können Sie sie durchsuchen und untersuchen.

[Weitere Informationen zu Diagnoseprotokolle][javalogs]

## Benutzerdefinierte Telemetrie 

Fügen Sie ein paar Codezeilen in Ihre Java-Anwendung ein, um herauszufinden, was Benutzer damit tun oder um Probleme zu diagnostizieren.

Sie können Code sowohl in das JavaScript der Webseite als auch in serverseitiges Java einfügen.

[Weitere Informationen zur benutzerdefinierten Telemetrie][track]



## Nächste Schritte

#### Erkennen und Diagnostizieren von Problemen

* [Fügen Sie Webclient-Telemetrie hinzu,][usage] um Leistungstelemetriedaten vom Webclient abzurufen.
* [Richten Sie Webtests ein][availability], um sicherzustellen, dass die Anwendung online und reaktionsfähig bleibt.
* [Durchsuchen Sie Ereignisse und Protokolle][diagnostic], um Probleme besser zu diagnostizieren.
* [Erfassen Sie Log4J- oder Logback-Ablaufverfolgungen.][javalogs]

#### Nutzung nachverfolgen

* [Fügen Sie Webclient-Telemetrie hinzu][usage], um Seitenaufrufe und grundlegende Benutzermetriken zu überwachen.
* [Verfolgen Sie benutzerdefinierte Ereignisse und Metriken nach][track], um zu erfahren, wie Ihre Anwendung sowohl auf dem Client als auch auf dem Server verwendet wird.


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=AcomDC_0302_2016-->