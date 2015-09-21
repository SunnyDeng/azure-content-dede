<properties 
	pageTitle="Versionshinweise für Application Insights für Java" 
	description="Die neuesten Updates." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="awills"/>
 
# Versionshinweise für das Application Insights-SDK für Java

Das [Application Insights-SDK für Java](app-insights-java-get-started.md) sendet Telemetriedaten über Ihre Live-App an [Application Insights](http://azure.microsoft.com/services/application-insights/), wo Sie die Nutzung und Leistung analysieren können.

#### So installieren Sie das SDK in Ihrer Anwendung

Informationen dazu finden Sie unter [Erste Schritte mit Application Insights in einem Java-Webprojekt](app-insights-java-get-started.md).

#### So aktualisieren Sie auf das neueste SDK 

Nach dem Upgrade müssen Sie alle an "ApplicationInsights.xml" vorgenommenen Anpassungen wieder zusammenführen. Erstellen Sie eine Kopie dieser Datei für den Vergleich mit der neuen Datei.

*Bei Verwendung von Maven oder Gradle*

1. Wenn Sie eine bestimmte Versionsnummer in "pom.xml" oder "build.gradle" angegeben haben, aktualisieren Sie sie.
2. Aktualisieren Sie die Abhängigkeiten des Projekts.

*Andernfalls*

* Laden Sie die neueste Version von [Azure Libraries for Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) herunter, und ersetzen Sie die alte Version. 
 
Vergleichen Sie die alte und die neue Version der Datei "ApplicationInsights.xml". Viele Änderungen sind darauf zurückzuführen, dass Module hinzugefügt und entfernt wurden. Reaktivieren Sie alle Anpassungen, die Sie vorgenommen haben.

## Version 1.0.1
- Der [Java-Agent](app-insights-java-agent.md) unterstützt das Sammeln folgender Abhängigkeitsinformationen:
	- HTTP-Aufrufe über HttpClient, OkHttp und RestTemplate (Spring).
	- Aufrufe von Redis über den Jedis-Client. Wenn ein konfigurierbarer Schwellenwert übergeben wird, ruft das SDK auch die Argumente des Aufrufs ab.
	- JDBC-Aufrufe mit Oracle DB und Apache Derby DB-Clients.
	- Unterstützung des Abfragetyps "executeBatch" für vorbereitete Anweisungen. Das SDK zeigt die Anweisung mit der Anzahl von Batches an.
	- Bereitstellung des Abfrageplans für JDBC-Clients, der dafür Unterstützung bietet (MySql, PostgreSql). Der Abfrageplan wird nur bei Überschreiten eines konfigurierbaren Schwellenwerts abgerufen.

## Version 1.0.0
- Hinzufügen von Unterstützung für das Application Insights-Writer-Plug-In für CollectD.
- Hinzufügen von Unterstützung für den Application Insights-Java-Agent.
- Beheben eines Kompatibilitätsproblems mit der Unterstützung von HttpClient-Versionen 4.2 und höher.

## Version 0.9.6
- Erreichen von Kompatibilität des Java-SDK mit Servlet 2.5 und HttpClient vor Version 4.3.
- Hinzufügen von Unterstützung für Java EE-Interceptors.
- Entfernen redundanter Abhängigkeiten aus dem Logback-Appender.

## Version 0.9.5  

- Behebung des Problems, bei dem benutzerdefinierte Ereignisse aufgrund von Cookieanalysefehlern nicht mit Benutzern/Sitzungen korreliert wurden.  
- Verbesserte Logik zum Auflösen des Speicherorts der Konfigurationsdatei "ApplicationInsights.xml".
- Anonyme Benutzer und Sitzungscookies werden serverseitig nicht generiert. Um die Nachverfolgung von Benutzern und Sitzungen für Web-Apps zu implementieren, ist jetzt die Instrumentation mit dem JavaScript-SDK erforderlich – Cookies aus dem JavaScript-SDK werden weiterhin beachtet. Beachten Sie, dass diese Änderung zu einer erheblichen Anpassung von Benutzer- und Sitzungszahlen führen kann, da nun nur von Benutzern initiierte Sitzungen gezählt werden.

## Version 0.9.4

- Unterstützung für das Erfassen von Leistungskennzahlen von 32-Bit-Windows-Computern.
- Unterstützung für die manuelle Nachverfolgung von Abhängigkeiten mit einer neuen API mit der ```trackDependency```-Methode.
- Möglichkeit, ein Telemetrieelement als synthetisch zu markieren, indem dem gemeldeten Element eine ```SyntheticSource```-Eigenschaft hinzugefügt wird.
 

<!---HONumber=Sept15_HO2-->