<properties 
	pageTitle="Datenaufbewahrung und -speicherung in Application Insights" 
	description="Hinweis zur Datenaufbewahrung und Datenschutzrichtlinie" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="awills"/>

# Datensammlung, -aufbewahrung und -speicherung in Application Insights 

*Application Insights befindet sich in der Vorschau.*

## Übersicht

Dieser Artikel beantwortet Fragen zu den von [Visual Studio Application Insights][start] gesammelten Daten sowie zu deren Verarbeitung und Speicherung.

Application Insights ist ein Azure-Dienst in der Vorschau. In der Vorschau sind wir darum bemüht, Ihre Daten mittels der im Whitepaper [Sicherheit, Datenschutz und Compliance in Azure](http://go.microsoft.com/fwlink/?linkid=392408) beschriebenen Richtlinien zu schützen.


## Sammlung

#### Wie sammelt Application Insights Daten?

Application Insights SDKs und Agents, die Sie mit Ihrer Anwendung kombinieren, senden Daten an den Application Insights-Dienst. Die Daten werden vom Dienst verarbeitet, um Ihnen Berichte, Warnungen und andere Funktionen zur Verfügung zu stellen. Dies kann Daten umfassen, die Sie mithilfe der API erfassen, z. B. in Eigenschaften und benutzerdefinierten Ereignissen.

#### Wie viele Daten können erfasst werden? 

**Pro Sekunde**: Es können bis zu 500 Datenpunkte pro Sekunde und pro Instrumentationsschlüssel (d. h. pro Anwendung) erfasst werden. Beim [Tarif "Free"][pricing] gelten 100 Datenpunkte/s als Obergrenze.

**Pro Monat**: Es können pro Kalendermonat, abhängig von Ihrem [Tarif](http://azure.microsoft.com/pricing/details/application-insights/), zwischen 5 und 15 Millionen Datenpunkte erfasst werden. Mit Ausnahme des [Tarifs "Free"][pricing] können Sie zusätzliche Kapazitäten erwerben, wenn Sie den Grenzwert erreichen.


Ein *Datenpunkt* ist ein Telemetrie-Element, z. B.:

* API `Track...`-Aufrufe wie `TrackEvent` oder `trackPageView`.
* Telemetrie-Elemente, die von SDK-Modulen gesendet werden, z. B. zur Meldung einer Anforderung oder eines Absturzes.
* Leistungsindikatordaten – ein Punkt für jede Messung.

*Woher weiß ich, wie viele Datenpunkte meine App sendet?*

* Öffnen Sie "Settings/Quota and Pricing", um das Diagramm mit dem Datenvolumen anzuzeigen.
* Oder fügen Sie im Metrik-Explorer ein neues Diagramm hinzu, und wählen Sie **Datenpunktvolumen** als Metrik aus. Aktivieren Sie "Gruppierung", und gruppieren Sie nach **Datentyp**.


#### Wie lange werden Daten aufbewahrt? 

Das hängt von Ihrem [Tarif](http://azure.microsoft.com/pricing/details/application-insights/) ab.

Rohdatenpunkte (also Elemente, die Sie bei der Diagnosesuche überprüfen können): zwischen 7 und 30 Tagen.

Aggregierte Daten (d. h. Zählungen, Mittelwerte und andere statistischen Daten, die im Metrik-Explorer angezeigt werden) werden im Maß von 1 Minute für 30 Tage und 1 Stunde oder 1 Tag (abhängig vom Typ) für mindestens 13 Monate aufbewahrt.

#### Welche Grenzen gibt es für die verschiedenen Datentypen?

1.	Bis zu 200 eindeutige Metriknamen und 200 eindeutige Eigenschaftennamen für Ihre Anwendung. Zu den Metriken gehören Daten, die über TrackMetric gesendet werden, sowie Messungen für andere Datentypen wie z. B. Ereignisse. [Metriken und Eigenschaftennamen][api] gelten global pro Instrumentationsschlüssel und werden nicht auf den Datentyp begrenzt.
2.	[Eigenschaften][apiproperties] können nur zur Filterung und zur Gruppierung verwendet werden, solange sie weniger als 100 eindeutige Werte für jede Eigenschaft aufweisen. Sobald es mehr als 100 eindeutige Werte gibt, kann die Eigenschaft zwar noch zur Suche und Filterung, jedoch nicht mehr für Filter verwendet werden.
3.	Standardeigenschaften wie Anforderungsname und Seiten-URL sind auf 1000 eindeutige Werte pro Woche beschränkt. Nach 1000 eindeutigen Werten werden zusätzliche Werte als "Andere Werte" gekennzeichnet. Der ursprüngliche Wert kann nach wie vor für die Volltextsuche und die Filterung verwendet werden.


## Access

#### Wer kann die Daten sehen?

Die Daten sind für Sie und, wenn Sie über ein Unternehmenskonto verfügen, für die Teammitglieder sichtbar.

Sie und Ihre Teammitglieder können die Daten exportieren und an andere Speicherorte kopieren oder Sie an andere Personen weiterleiten.

#### Wie verwendet Microsoft die Daten, die meine App an Application Insights sendet?

Microsoft verwendet die Daten nur dazu, Ihnen den Dienst bereitstellen zu können.


## Standort

#### Wo werden die Daten gespeichert? 

* In den USA. 

#### Können sie auch an anderen Orten, z. B. in Europa, gespeichert werden? 

* Bisher nicht. 

## Sicherheit 

#### Wie sicher sind meine Daten? 

Die Daten werden auf Microsoft Azure-Servern gespeichert. Für Konten im Azure-Vorschauportal sind die Kontobeschränkungen im Dokument [Sicherheit, Datenschutz und Compliance in Azure](http://go.microsoft.com/fwlink/?linkid=392408) beschrieben. Für Konten im Visual Studio Online-Portal gilt das Dokument [Visual Studio Online-Datenschutz](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf).

Der Zugriff auf Ihre Daten durch Microsoft-Mitarbeiter ist eingeschränkt. Wir werden nur mit Ihrer Erlaubnis und ausschließlich dann auf Ihre Daten zugreifen, wenn es erforderlich ist, Sie bei der Verwendung von Application Insights zu unterstützen.

Aggregierte Daten in den Anwendungen unserer Kunden (z. B. Datenraten und die durchschnittliche Größe von Ablaufverfolgungen) werden zur Verbesserung von Application Insights verwendet.

#### Könnte die Telemetrie eines anderen Benutzers meine Application Insights-Daten beeinträchtigen?

Sie könnten zusätzliche Telemetrie mit dem Instrumentationsschlüssel im Code Ihrer Webseiten an Ihr Konto senden. Bei genügend zusätzlichen Daten würden die Metriken die Leistung und Nutzung Ihrer App nicht ordnungsgemäß darstellen.

Wenn Sie Code für andere Projekte freigeben, sollten Sie Ihren Instrumentationsschlüssel entfernen.

## Verschlüsselung

#### Werden die Daten auf den Application Insights-Servern verschlüsselt? 

Derzeit nicht innerhalb der Server.

Alle Daten werden bei der Übertragung zwischen Datencentern verschlüsselt.

#### Werden die Daten während der Übertragung von meiner Anwendung zu Application Insights-Servern verschlüsselt?

Ja, wir verwenden HTTPS zum Senden von Daten an das Portal aus nahezu allen SDKs, darunter Webserver, Geräte und HTTPS-Webseiten. Die einzige Ausnahme sind Daten, die von einfachen HTTP-Webseiten gesendet werden.

## Personenbezogene Informationen

#### Können personenbezogene Informationen an Application Insights gesendet werden? 

Ja.

Allgemeine Hinweise:

* Der Großteil der standardmäßigen Telemetrie (d. h. Telemetrie, die ohne das Schreiben von Code gesendet wird) enthält keine expliziten personenbezogenen Informationen. Allerdings können möglicherweise Rückschlüsse aus einer Ereignisauflistung auf einzelne Personen gezogen werden.
* Ausnahme- und Ablaufverfolgungsmeldungen können personenbezogene Informationen enthalten.
* Benutzerdefinierte Telemetrie, z. B. Aufrufe wie "TrackEvent", die Sie mithilfe der API oder von Protokollnachverfolgungen in Code schreiben, kann alle von Ihnen ausgewählten Daten enthalten.


Die Tabelle am Ende dieses Dokuments enthält detaillierte Beschreibungen der gesammelten Daten.



#### Bin ich für die Einhaltung von Gesetzen und Vorschriften in Bezug auf personenbezogene Informationen verantwortlich?

Ja. Sie müssen sicherstellen, dass bei der Sammlung und Nutzung der Daten die Gesetze und Vorschriften sowie die Microsoft Online Services-Bedingungen eingehalten werden.

Informieren Sie Ihre Kunden angemessen darüber, welche Daten Ihre Anwendung erfasst und wie die Daten verwendet werden.

#### Können meine Benutzer Application Insights abschalten?

Nicht direkt. Wir bieten keinen Schalter, mit dem Ihre Benutzer Application Insights abschalten können.

Sie können jedoch eine solche Funktion in Ihrer Anwendung implementieren. Alle SDKs enthalten eine API-Einstellung, mit der die Telemetrie-Erfassung deaktiviert wird.

#### Meine Anwendung erfasst unbeabsichtigt vertrauliche Informationen. Kann Application Insights ein Scrubbing für diese Daten durchführen, damit sie nicht aufbewahrt werden?

Die Daten werden von Application Insights weder gefiltert noch gelöscht. Verwalten Sie die Daten entsprechend, und vermeiden Sie es, solche Daten an Application Insights zu senden.



## Von Application Insights gesendete Daten

Die SDKs sind je nach Plattform unterschiedlich, und es gibt verschiedene Komponenten, die Sie installieren können. (Weitere Informationen finden Sie unter [Erste Schritte mit Application Insights][start].) Jede Komponente sendet unterschiedliche Daten.

#### Klassen von in verschiedenen Szenarien gesendeten Daten

Aktion | Gesammelte Datenklassen (siehe nächste Tabelle)
---|---
[Hinzufügen des Application Insights SDK zu Ihrem .NET-Webprojekt][greenbrown] | ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Exceptions**<br/>Session<br/>users
[Installieren des Statusmonitors unter IIS][redfield]<br/>[Hinzufügen einer AI-Erweiterung zu Azure-VM oder Web-App][azure]|Dependencies<br/>ServerContext<br/>Inferred<br/>Perf counters
[Hinzufügen des Application Insights SDK zu einer Java-Web-App][java]|ServerContext<br/>Inferred<br/>Request<br/>Session<br/>users
[Hinzufügen des JavaScript SDK zur Webseite][client]|ClientContext <br/>Inferred<br/>Page<br/>ClientPerf
[Hinzufügen des SDK zur Windows Store-App][windows]|DeviceContext<br/>Users<br/>Crash data
[Definieren von Standardeigenschaften][apiproperties]|**Properties** für alle standardmäßigen und benutzerdefinierten Ereignisse
[Aufrufen von TrackMetric][api]|Numerische Werte<br/>**Properties**
[Aufrufen von Track*][api]|Event name<br/>**Properties**
[Aufrufen von TrackException][api]|**Exceptions**<br/>Stack dump<br/>**Properties**
SDK kann keine Daten sammeln. Beispiel: <br/> – kann nicht auf Leistungsindikatoren zugreifen<br/> – Ausnahme in Telemetrie-Initialisierung | SDK-Diagnose
 

Weitere Informationen zu [SDKs für andere Plattformen][platforms] finden Sie in den entsprechenden Dokumenten.



#### Die Klassen der gesammelten Daten

Gesammelte Datenklasse | Umfasst (keine vollständige Liste) 
---|---
**Eigenschaften**|**Alle Daten – bestimmt durch Code**
DeviceContext |ID, IP, Gebietsschema, Gerätemodell, Netzwerk, Netzwerktyp, OEM-Name, Bildschirmauflösung, Rolleninstanz, Rollenname, Gerätetyp
ClientContext |Betriebssystem, Gebietsschema, Sprache, Netzwerk, Fensterauflösung
Session | Sitzungs-ID
ServerContext |Computername, Gebietsschema, Betriebssystem, Gerät, Benutzersitzung, Benutzerkontext, Vorgang 
Inferred |Geolocation anhand IP-Adresse, Zeitstempel, Betriebssystem, Browser
Metriken | Metrikname und -wert
Ereignisse | Ereignisname und -wert
PageViews | URL und Seitenname oder Bildschirmname
Client perf | URL-/Seitenname, Browserladezeit
Requests |URL, Dauer, Antwortcode
Abhängigkeiten|Typ (SQL, HTTP,...), Verbindungszeichenfolge oder URI Sync/Async, Dauer, Erfolg, SQL-Anweisung (mit Statusmonitor)
**Exceptions** | Typ, **Meldung**, Aufrufliste, Quelldatei und Zeilennummer, Thread-ID
Crashes | Prozess-ID, übergeordnete Vorgangs-ID, Thread-ID des Absturzes, Anwendungs-Patch, ID, Build, Typ der Ausnahme, Adresse, Ursache; abgeblendete Symbole und Registerkarten, binäre Start- und Endadressen, binärer Name und Pfad, CPU-Typ
Trace | **Meldung** und Schweregrad
Perf counters | Prozessorzeit, verfügbarer Speicher, Anforderungsrate, Ausnahmerate, private Bytes verarbeiten, E/A-Rate, Anforderungsdauer, Länge der Anforderungswarteschlange
Availability | Webtest-Antwortcode, Dauer der einzelnen Testschritte
SDK-Diagnose | Ablaufverfolgungsmeldung oder Ausnahme 

Sie können [einige der Daten durch Bearbeiten von ApplicationInsights.config abschalten][config]


## Guthaben

Dieses Produkt enthält GeoLite2-Daten, die von MaxMind erstellt wurden und unter [http://www.maxmind.com](http://www.maxmind.com) verfügbar sind.

## <a name="video"></a>Videos

#### Einführung

> [AZURE.VIDEO application-insights-introduction]

#### Erste Schritte

> [AZURE.VIDEO getting-started-with-application-insights]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=July15_HO4-->