<properties 
	pageTitle="Data retention and storage in Application Insights" 
	description="Retention and privacy policy statement" 
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
	ms.date="11/14/2014" 
	ms.author="awills"/>

# Datenaufbewahrung und -speicherung in Application Insights 

*Application Insights befindet sich in der Vorschauphase.*


Dieser Artikel beantwortet Fragen dazu, wie die Telemetriedaten gespeichert werden, die die Anwendung an Application Insights sendet.

Application Insights speichert Daten auf Microsoft Azure-Servern in den Vereinigten Staaten von Amerika. Laden Sie das [Whitepaper zu Sicherheit, Datenschutz und Kompatibilität](http://go.microsoft.com/fwlink/?LinkId=392408) herunter, um weitere Informationen zur Microsoft Azure-Richtlinie zu erhalten. Nachfolgend werden einige Besonderheiten zu Application Insights beantwortet. 

#### Wie viele Daten werden gespeichert? 

Während sich Application Insights in dieser kostenlosen Vorschauphase befindet, wird Folgendes für die einzelnen Konten gespeichert: 

* Bis zu 500 Telemetrienachrichten pro Sekunde (oder 30.000 pro Minute). 

* Bis zu 10 Millionen Seitenaufrufe oder Ereignisse pro Monat. 

*Was geschieht, wenn die Grenzwerte überschritten werden?* 

* Beim Überschreiten der kurzfristigen Limits werden einige Nachrichten nach der Zählung gelöscht. Diese werden in der Diagnosesuche nicht angezeigt. Ereigniszähler werden korrigiert. Durchschnittliche Metriken basieren auf den behaltenen Ereignissen, die gültig sein sollten. 

* Beim Überschreiten der monatlichen Limits wird die Aufzeichnung der Ereignisse beendet, sodass Ihre Metriken nach einem bestimmten Zeitpunkt Nullen anzeigen. 

####Wie lange werden die Telemetriedaten gespeichert? 

* In der Diagnosesuche sichtbare Instanzdaten werden sieben Tage gespeichert. Einzelne Seitenaufrufe, Ereignisse, Protokollmeldungen, Ablaufverfolgungen und Ausnahmen. 

* Im Metrik-Explorer sichtbare aggregierte Daten werden für 13 Monate gespeichert. Statistiken zu Metriken, Ereignissen und Ausnahmen (Anzahl der Typen, der fehlgeschlagenen Funktion usw.) werden für 30 Tage mit einem Zeitkorn von einer Minute (oder weniger) und mit einem Zeitkorn von einer Stunde für 13 Monate beibehalten. 

####Wo werden die Daten gespeichert? 

* In den USA. 

Besteht die Möglichkeit, die Daten in Europa oder an einem anderen Ort zu speichern? 

* Bisher nicht. 

####Wie sicher sind meine Telemetriedaten? 

Die Daten werden auf Microsoft Azure-Servern gespeichert. Für Konten im Azure-Vorschauportal sind die Kontobeschränkungen im Azure-Dokument zu Sicherheit, Datenschutz und Kompatibilität beschrieben. Für Konten im Visual Studio Online-Portal gilt das Dokument zum Visual Studio Online-Datenschutz. 

Der Zugriff auf Ihre Daten durch Microsoft-Mitarbeiter ist eingeschränkt. Wir werden nur mit Ihrer Erlaubnis und ausschließlich dann auf Ihre Daten zugreifen, wenn es erforderlich ist, Sie bei der Verwendung von Application Insights zu unterstützen. 

####Werden die Daten auf den Application Insights-Servern verschlüsselt? 

Derzeit nicht. 

####Können personenbezogene Informationen an Application Insights gesendet werden? 

Ja. Sowohl von Ihrem Code in benutzerdefinierten Telemetriedaten an Application Insights gesendete als auch in den Standardtelemetriedaten enthaltene personenbezogene Daten. Die Datenschutzerklärung zu Azure gilt für Application Insights. 

Es gibt verschiedene Möglichkeiten, wie die Daten an das Portal gesendet werden können. Sie können dann in der Diagnosesuche angezeigt werden. Mitglieder Ihres Unternehmens können die Daten auch exportieren und herunterladen. 

* Wenn Sie den Statusmonitor installieren oder Application Insights zu Ihrem Projekt hinzufügen, werden beim Auftreten von Ausnahmen oder Leistungswarnungen entsprechende Stapelüberwachungen erfasst. Diese können gegenwärtige Parameterdaten wie SQL-Daten einbeziehen. 

* Wenn Sie Telemetrieaufrufe wie "TrackEvent" in den Code einfügen oder Meldungen des Protokollframeworks erfassen, können die Eigenschaften personenbezogene Informationen enthalten. Zudem gelten die Nutzungsbedingungen der Microsoft Online Services. Für Application Insights müssen Sie insbesondere die Gesetze und Bestimmungen einhalten, die für Datenschutz und Datensammlung gelten. Sie sollten ermitteln, ob Benachrichtigungen oder andere Genehmigungen erforderlich sind, bevor Sie die Informationen erfassen. 


####Welche Telemetriedaten werden bei der Verwendung von Application Insights gesendet? 

Sie können verschiedene Komponenten installieren. (Weitere Informationen finden Sie unter [Application Insights - Erste Schritte][start].) 

<table>
<tr><th>Einrichtung</th><th>An das Portal gesendete Telemetriedaten</th><th>Können vertrauliche Daten enthalten sein?</th></tr>
<tr><td><a href="../app-insights-start-monitoring-app-health-usage/">Hinzufügen von Application Insights zu Ihrem Webprojekt</a></td>
  <td>Metriken: Anzahl von Serveranforderungen, Serverantwortzeit</td>
  <td>Nein</td></tr>
<tr><td></td>
  <td>Ausnahmeberichte vom Server</td><td>Stapelabbilder können Parameterwerte enthalten</td></tr>
<tr><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Nachverfolgen von benutzerdefinierten Ereignissen und Metriken</a></td>
  <td>Durch Ihren Code angefügte Ereignisse mit Eigenschaften</td>
  <td>Ja, wenn der Code personenbezogene Informationen in Eigenschaften oder Titeln sendet</td></tr>
<tr><td><a href="../app-insights-search-diagnostic-logs/#trace">Protokollieren und Nachverfolgen von Telemetriedaten</a></td><td>Protokollieren und Nachverfolgen von Meldungen</td><td>Protokollmeldungen können personenbezogene Informationen enthalten</td></tr>
<tr><td><a href="../app-insights-web-track-usage/">Einfügen des AI-Skripts in Ihre Webseiten</a></td>
  <td>Anonymisierte Benutzer- und Konto-ID</td><td>Nein</td></tr>
<tr><td></td><td>Start und Ende der anonymen Benutzersitzung</td><td>Nein</td></tr>
<tr><td></td><td>Seiten-URI, Ladezeiten, Sitzungszeitaufnahme</td><td>Nein</td></tr>
<tr><td><a href="../app-insights-monitor-performance-live-website-now/">Installieren des Statusmonitors auf dem Server</a></td>
  <td>Abhängigkeitsaufrufe und Laufzeiten</td>
  <td>Aufrufdaten können Parameter enthalten, z. B. SQL-Felder</td></tr>
<tr><td></td><td>Zähler für CPU, Arbeitsspeicher, Netzwerk und andere Ressourcen</td><td>Nein</td></tr>
<tr><td><a href="../app-insights-monitor-web-app-availability/">Webtests</a></td><td>Verfügbarkeit und Antwortzeiten (über das Netz)</td><td>Nein</td></tr>
</table>

## <a name="video"></a>Videos

#### Einführung

> [AZURE.VIDEO application-insights-introduction]

#### Erste Schritte

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



<!--HONumber=35.2-->
