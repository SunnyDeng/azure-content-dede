<properties
	pageTitle="Erkennung, Eingrenzung und Diagnose"
	description="Analysieren von Abstürzen und Erkennen und Diagnostizieren von Leistungsproblemen in Ihren Anwendungen"
	authors="alancameronwills"
	services="application-insights"
    documentationCenter=""
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="11/06/2015"
	ms.author="awills"/>

# Erkennung, Eingrenzung und Diagnose mit Application Insights

*Application Insights befindet sich in der Vorschau.*


Mithilfe von Application Insights können Sie die Leistung und Verwendung Ihrer App nach ihrer Liveschaltung ermitteln. Wenn ein Problem vorliegt, informiert Application Insights Sie darüber, sodass Sie die Auswirkungen bewerten und die Ursache ermitteln können.

Hier beschreibt ein Team, das Webanwendungen entwickelt, seine Erfahrungen:

* *"Vor ein paar Tagen haben wir einen 'kleineren' Hotfix bereitgestellt. Wir haben keinen umfassenden Testdurchlauf ausgeführt, doch leider gerieten einige unerwartete Änderungen in die Nutzlast, die eine Inkompatibilität zwischen dem Front-End und dem Back-End verursacht haben. Sofort nahmen die Serverausnahmen zu, unsere Warnungen wurden ausgelöst, und wir wurden auf die Situation aufmerksam gemacht. Nach ein paar Klicks im Application Insights-Portal hatten wir genügend Informationen über die Aufruflisten für Ausnahmen gesammelt, um das Problem einzugrenzen. Wir haben die Änderung umgehend rückgängig gemacht und dadurch den Schaden begrenzt. Dank Application Insights war dieser Teil des DevOps-Zyklus sehr einfach umsetzbar."*

Sehen wir uns an, wie ein typisches Webentwicklungsteam Application Insights zur Leistungsüberwachung nutzt. Wir begleiten das Team der Fabrikam Bank, das das Onlinebankingsystem (OBS) entwickelt.

![Beispiel der Website einer Bank](./media/app-insights-detect-triage-diagnose/03-bank.png)

Das Team arbeitet an ungefähr folgendem Zyklus:

![DevOps-Zyklus](./media/app-insights-detect-triage-diagnose/00-devcycle.png)

Anforderungen werden in den Entwicklungs-Backlog (Aufgabenliste) geladen. Sie arbeiten mit kurzen Sprints, in denen häufig Software – in der Regel in Form von Verbesserungen und Erweiterungen der vorhandenen Anwendung – bereitgestellt wird. Die Live-App wird regelmäßig mit neuen Features aktualisiert. Während die App live geschaltet ist, überwacht das Team sie mithilfe von Application Insights in Hinblick auf Leistung und Verwendung. Diese Analyse wird zurück in ihr Entwicklungs-Backlog geladen.

Das Team verwendet Application Insights zum genauen Überwachen der Live-Webanwendung in Bezug auf: * Leistung. Es möchte nachvollziehen können, wie die Antwortzeiten in Abhängigkeit von der Anforderungsanzahl variieren, wie viele CPU-, Netzwerk-, Datenträgerressourcen und andere Ressourcen verwendet werden und wo Engpässe auftreten. * Fehler. Wenn Ausnahmen oder Fehler bei Anforderungen auftreten oder ein Leistungsindikator den zulässigen Bereich überschreitet, muss das Team sofort darüber informiert werden, um entsprechende Maßnahmen ergreifen zu können. * Verwendung. Sobald eine neue Funktion veröffentlicht wird, möchte das Team wissen, inwieweit sie genutzt wird und ob Benutzer Probleme mit dieser Funktion haben.



Konzentrieren wir uns auf den Feedbackteil des Zyklus:

![Erkennung-Eingrenzung-Diagnose](./media/app-insights-detect-triage-diagnose/01-pipe1.png)



## Erkennen schlechter Verfügbarkeit


Marcela Markova ist im OBS-Team eine Spezialistin für Tests und für die Überwachung der Onlineleistung zuständig. Sie richtet mehrere [Webtests][availability] ein:

* Einen einzelnen URL-Test für die Hauptangebotsseite der App, http://fabrikambank.com/onlinebanking/. Sie legt Kriterien für den HTTP-Code 200 und den Text "Willkommen!" fest. Wenn dieser Test misslingt, ist etwas mit dem Netzwerk oder den Servern ernsthaft nicht in Ordnung, oder es liegt ggf. ein Bereitstellungsproblem vor. (Oder jemand hat ohne ihr Wissen die Begrüßungsmeldung auf der Seite geändert.)


* Einen umfassenden mehrstufigen Test, der eine Anmeldung ausführt, eine aktuelle Kontoauflistung abruft und einige wichtige Details auf jeder Seite überprüft. Dieser Test bestätigt, dass die Verbindung mit der Kontendatenbank funktioniert. Sie verwendet eine fiktive Kunden-ID, von denen einige für Testzwecke vorgehalten werden.


Nach dem Einrichten dieser Tests ist Marcela sicher, dass das Team schnell über Systemausfälle informiert wird.


Ausfälle bzw. Fehler werden im Diagramm des Webtests als rote Punkte angezeigt:

![Anzeigen von Webtests, die im vorherigen Zeitraum erfolgt sind](./media/app-insights-detect-triage-diagnose/04-webtests.png)


Aber noch wichtiger ist, dass eine Warnung zu allen Fehlern per E-Mail an das Entwicklungsteam gesendet wird. Auf diese Weise wissen sie vor fast allen Kunden um das Problem.


## Überwachen von Leistungsmetriken


Die Übersichtsseite in Application Insights enthält ein Diagramm, das eine Vielzahl [wichtiger Metriken][perf] zeigt.

![Verschiedene Metriken](./media/app-insights-detect-triage-diagnose/05-perfMetrics.png)

Die Browser-Seitenladezeit wird von Telemetriedaten abgeleitet, die direkt von Webseiten gesendet werden. Die Serverantwortzeit, die Anzahl von Serveranforderungen und die Anzahl der Anforderungsfehler werden alle auf dem Webserver gemessen und von dort an Application Insights gesendet.


Der Zähler "Anforderungsfehler" bezieht sich auf Fälle, bei denen Benutzern ein Fehler angezeigt wurde, meist im Anschluss an eine im Code ausgelöste Ausnahme. Vielleicht wird den Benutzern auf dem Bildschirm die Meldung "Es tut uns leid, dass wir Ihre Daten zur Zeit nicht aktualisieren können" oder aber (im absolut peinlichsten Fall) ein Stapelabbild des Webservers gezeigt.


Marcela wirft von Zeit zu Zeit gern einen Blick auf diese Diagramme. Treten keine Anforderungsfehler auf, ist das vielversprechend. Wenn sie jedoch als Bereich des Diagramms die letzte Woche angibt, werden gelegentliche Fehler angezeigt. Dies ist bei einem ausgelasteten Server akzeptabel. Doch wenn es bei den Anforderungsfehlern oder einigen anderen Metriken wie der Serverantwortzeit zu plötzlichen Spitzen kommt, möchte Marcela sofort davon wissen. Dabei kann es sich um ein unvorhergesehenes Problem handeln, das von einer bestimmten Codeversion, einem Fehler in einer Abhängigkeit, z. B. einer Datenbank, oder einer nicht ordnungsgemäßen Reaktion auf eine hohe Anforderungslast verursacht werden kann.

#### Warnungen

Also legt sie zwei [Warnungen][metrics] fest: eine für Antwortzeiten, die einen üblichen Schwellenwert überschreiten, und eine zweite für eine Rate fehlerhafter Anforderungen, die größer als die aktuelle Grundlage sind.


In Verbindung mit der Warnung zur Verfügbarkeit bieten diese ihr die Sicherheit, dass sie informiert wird, sobald etwas Ungewöhnliches passiert.


Es ist auch möglich, Warnungen für eine Vielzahl anderer Metriken festzulegen. Beispielsweise können Sie E-Mails empfangen, wenn die Anzahl der Ausnahmen hoch wird oder der verfügbare Arbeitsspeicher zur Neige geht oder es zu einer Spitze bei den Clientanforderungen kommt.



![Blatt "Warnung hinzufügen"](./media/app-insights-detect-triage-diagnose/07-alerts.png)




## Erkennen von Ausnahmen


Mit nur wenigen Einrichtungsschritten werden [Ausnahmen](app-insights-asp-net-exceptions.md) automatisch an Application Insights gemeldet. Die Ausnahmen können auch explizit aufgezeichnet werden, indem Aufrufe von [TrackException()](app-insights-api-custom-events-metrics.md#track-exception) in den Code eingefügt werden:

    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }


Das Team der Fabrikam-Bank hat lässt sich bei einer Ausnahme stets Telemetriedaten senden, es sei denn, es gibt eine offensichtliche Problemlösung.

Die Strategie des Teams ist sogar noch umfassender: es lässt sich Telemetriedaten in allen Fällen senden, in denen der Kunde nicht den gewünschten Schritt ausführen kann, ob aufgrund einer Ausnahme im Code oder nicht. Wenn z. B. das externe Interbank-Überweisungssystem die Meldung "Diese Transaktion kann nicht abgeschlossen werden" aus bestimmten betrieblichen Gründen (ohne Fehlverhalten des Kunden) zurückgibt, wird auch dieses Ereignis nachverfolgt.

    var successCode = AttemptTransfer(transferAmount, ...);
    if (successCode < 0)
    {
       var properties = new Dictionary <string, string>
            {{ "Code", returnCode, ... }};
       var measurements = new Dictionary <string, double>
         {{"Value", transferAmount}};
       telemetry.TrackEvent("transfer failed", properties, measurements);
    }

"TrackException" wird verwendet, um Ausnahmen zu melden, da eine Kopie des Stapels gesendet wird. "TrackEvent" wird verwendet, um andere Ereignisse zu melden. Sie können alle Eigenschaften anfügen, die für die Diagnose hilfreich sein können.

Ausnahmen und Ereignisse werden auf dem Blatt [Diagnosesuche][diagnostic] gezeigt. Sie können eine Detailsuche durchführen, um die weiteren Eigenschaften und die Stapelüberwachung anzuzeigen.

![Verwenden Sie bei der Diagnosesuche Filter, um bestimmte Datentypen anzeigen](./media/app-insights-detect-triage-diagnose/appinsights-333facets.png)

## Überwachen der Benutzeraktivität

Wenn die Antwortzeit durchweg gut ist und nur wenige Ausnahmen auftreten, kann das Entwicklungsteam überlegen, wie es die Benutzererfahrung verbessern und mehr Benutzer ermutigen kann, die gewünschten Ziele zu erreichen.


Die Websitenavigation von Benutzern folgt üblicherweise einem Trichtermodell: Viele Kunden sehen sich die Zinssätze verschiedener Darlehen an. Einige füllen das Angebotsformular aus. Und von denjenigen, die ein Angebot erhalten, nehmen einige wenige anschließend tatsächlich ein Darlehen auf.

![](./media/app-insights-detect-triage-diagnose/12-funnel.png)

Ein Unternehmen kann nun untersuchen, an welcher Stelle die meisten Kunden den Vorgang abbrechen, und so ermitteln, wie sich mehr Benutzer durch den gesamten Trichter schleusen lassen. In manchen Fällen tritt ein Fehler auf der Benutzeroberfläche auf. Beispielsweise ist die Schaltfläche „Weiter“ schwer zu finden, oder die Anweisungen sind nicht eindeutig zu erkennen. Wahrscheinlicher sind jedoch geschäftliche Gründe (wie etwa zu hohe Zinssätze für Darlehen).

Unabhängig von den Gründen kann das Team mithilfe der Daten ermitteln, welche Aktionen Benutzer ausführen. Es kann weitere Aufrufe zur Nachverfolgung einfügen, um zusätzliche Einzelheiten zu ermitteln. Mithilfe von „TrackEvent()“ können alle Benutzeraktionen (angefangen von einzelnen Klicks auf Schaltflächen bis hin zu bedeutenderen Ereignissen wie etwa das Abzahlen eines Darlehens) nachverfolgt werden.

Das Team gewöhnt sich daran, Informationen zur Benutzeraktivität zu erhalten. Heute entwickelt das Team beim Entwerfen eines neuen Features ein Feedbackkonzept, um Informationen zur Nutzung zu erhalten. Es arbeitet von Anfang an Aufrufe zur Nachverfolgung in das Feature ein. Es verwendet das Feedback dazu, das Feature in jedem Entwicklungszyklus zu verbessern.


## Proaktive Überwachung  


Marcela sitzt nicht bloß herum und wartet auf Warnungen. Schon bald nach jeder erneuten Bereitstellung befasst sie sich mit [Antwortzeiten][perf] – sowohl mit der Gesamtanzahl und der Tabelle mit den langsamsten Anforderungen als auch mit der Anzahl der Ausnahmen.



![Antwortzeitdiagramm und Raster von Serverantwortzeiten.](./media/app-insights-detect-triage-diagnose/09-dependencies.png)

Sie kann die Auswirkung jeder Bereitstellung auf die Leistung bewerten, indem üblicherweise jede Woche mit der Vorwoche verglichen wird. Sobald es eine plötzliche Verschlechterung gibt, wendet sie sich an die entsprechenden Entwickler.


## Eingrenzung


Die Eingrenzung, d. h. das Bewerten des Schweregrads und Ausmaßes eines Problems, ist der erste Schritt nach der Erkennung. Müssen wir das Team um Mitternacht herbeirufen? Oder kann es bis zur nächsten praktischen Lücke im Rückstand unbehandelt bleiben? Es gibt einige wichtigen Fragen bei der Eingrenzung.


Wie ist das Ausmaß? Die Diagramme auf dem Blatt "Übersicht" setzen das Problem in Perspektive. Beispielsweise hat die Fabrikam-Anwendung vier Webtestwarnungen in einer Nacht generiert. Als sich das Team das Diagramm am Morgen ansah, konnte es einige rote Punkte erkennen, wenngleich für die meisten Tests die Zeichen auf grün standen. Nach einer Detailsuche im Verfügbarkeitsdiagramm war klar, dass all diese zwischenzeitlichen Probleme von einem bestimmten Test herrührten. Es handelte sich offensichtlich um ein Netzwerkproblem, das sich auf nur eine Route auswirkte und sich wahrscheinlich von selbst beheben sollte.


Im Gegensatz dazu ist ein drastischer und stabiler Anstieg im Diagramm der Anzahl von Ausnahmen oder Dauer von Antwortzeiten natürlich etwas, das Grund zur Panik gibt.


Eine nützliche Eingrenzungstaktik ist das eigenständige Ausprobieren. Wenn bei Ihnen das gleiche Problem auftritt, wissen Sie, dass es real ist.


Welcher Anteil der Benutzer ist betroffen? Um eine grobe Antwort zu erhalten, teilen Sie die Fehlerrate durch die Anzahl der Sitzungen.


![Diagramme fehlerhafter Anforderungen und Sitzungen](./media/app-insights-detect-triage-diagnose/10-failureRate.png)

Vergleichen Sie bei langsamen Antwortzeiten die Tabelle mit den am langsamsten beantworteten Anforderungen mit der Nutzungsfrequenz jeder Seite.


Wie wichtig ist das Szenario einer Blockierung? Wenn es sich um ein funktionales Problem handelt, das eine bestimmte User Story blockiert, wie wichtig ist es wirklich? Wenn Kunden ihre Rechnungen nicht zahlen können, ist es ernsthaft. Wenn sie die Farbeinstellungen ihres Bildschirms nicht ändern können, kann es möglicherweise warten. Die Details eines Ereignisses oder einer Ausnahme bzw. die Identität der langsamen Seite sagen Ihnen, wo Kunden Probleme haben.


## Diagnose


Eine Diagnose ist nicht ganz dasselbe wie ein Debugging. Bevor Sie die Ablaufverfolgung im Code starten, müssen Sie eine grobe Vorstellung haben, warum, wo und wann das Problem auftritt.


**Wann tritt es auf?** Die von den Ereignis- und Metrikdiagrammen bereitgestellte Verlaufsansicht vereinfacht das Korrelieren von Auswirkungen mit möglichen Ursachen. Wenn es bei den Antwortzeiten oder Ausnahmeraten zwischenzeitliche Spitzen gibt, untersuchen Sie die Anzahl der Anforderungen. Wenn die Spitze zur selben Zeit auftritt, liegt wahrscheinlich ein Ressourcenproblem vor. Müssen Sie mehr CPU oder Arbeitsspeicher zuweisen? Oder gibt es eine Abhängigkeit, die die Last nicht bewältigen kann?


**Sind wir es?** Wenn es bei einem bestimmten Typ von Anforderung einen plötzlichen Leistungsabfall gibt, z. B. wenn der Kunde einen Kontoauszug wünscht, besteht die Möglichkeit, dass die Ursache ein externes Subsystem und nicht Ihre Webanwendung ist. Wählen Sie im Metrik-Explorer die Raten "Abhängigkeitsfehler" und "Abhängigkeitsdauer" aus, und vergleichen Sie deren Verlauf in den letzten Stunden oder Tagen mit dem erkannten Problem. Wenn es korrelierende Änderungen gibt, ist vielleicht ein externes Subsystem Schuld.


![Diagramme von Abhängigkeitsfehlern und Dauer von Aufrufen von Abhängigkeiten](./media/app-insights-detect-triage-diagnose/11-dependencies.png)

Bei einigen Problemen mit langsamen Abhängigkeiten ist die Geolocation die Ursache. Die Fabrikam Bank nutzt virtuelle Computer in Azure und hat festgestellt, dass sich ihre Web- und Kontoserver aus Versehen in unterschiedlichen Ländern befinden. Eine deutliche Verbesserung wurde erreicht, indem einer davon in die richtige Zone verlagert wurde.


**Was haben wir gemacht?** Wenn das Problem nicht einer Abhängigkeit zuzuschreiben ist und nicht immer vorhanden war, wird es wahrscheinlich durch eine zuletzt erfolgte Änderung verursacht. Die von den Metrik- und Ereignisdiagrammen zur Verfügung gestellte Verlaufsansicht erleichtert das Korrelieren plötzlicher Änderungen mit Bereitstellungen Dadurch kann die Suche nach der Problemursache eingeengt werden.


**Was passiert?** Einige Probleme treten nur selten auf und können schwierig aufzuspüren sein, wenn Tests offline erfolgen. Alles, was wir tun können, ist versuchen, den Fehler zu erfassen, wenn er online auftritt. Sie können das Stapelabbild in den Ausnahmeberichten prüfen. Darüber hinaus können Sie die Ablaufverfolgungsaufrufe mit Ihrem bevorzugten Protokollierungsframework oder mit "TrackTrace()" oder "TrackEvent()" schreiben.


Bei Fabrikam war ein zwischenzeitliches Problem bei Überweisungen zwischen Konten, jedoch nur bei bestimmten Kontotypen aufgetreten. Um besser zu verstehen, was passiert ist, hat das Team "TrackTrace()"-Aufrufe an wichtigen Punkten im Code eingefügt, wobei der Kontotyp jedem Aufruf als Eigenschaft angefügt wird. Dadurch wurde das Herausfiltern bloß dieser Ablaufverfolgungen bei der Diagnosesuche erleichtert. Außerdem hat das Team Parameterwerte als Eigenschaften und Kennzahlen an die Ablaufverfolgungsaufrufe angefügt.


## Fehlerkorrektur


Nachdem Sie das Problem diagnostiziert haben, arbeiten Sie einen Plan zu seiner Korrektur aus. Vielleicht müssen Sie eine zuletzt erfolgte Änderung rückgängig machen oder können das Probleme ohne diesen Schritt angehen. Nachdem die Korrektur erfolgt ist, informiert Application Insights Sie, ob Sie erfolgreich waren.


Das Entwicklungsteam der Fabrikam Bank befolgt nun einen strukturierteren Ansatz bei der Leistungsmessung als vor dem Einsatz von Application Insights.

* Es legt auf der Übersichtsseite von Application Insights Leistungsziele in Bezug auf bestimmte Kennzahlen fest.

* Das Team integriert von Anfang an Leistungsmessungen in die Anwendung, z. B. in Form von Metriken, die den Benutzerfortschritt in "Trichtern" messen.




## Verwendung

Application Insights kann auch genutzt werden, um zu erfahren, welche Aktionen Benutzer in einer Anwendung ausführen. Sobald sie reibungslos läuft, möchte das Team wissen, welche Funktionen am häufigsten genutzt werden, was Benutzer mögen, womit sie Schwierigkeiten haben und wie oft sie zurückkehren. Dadurch können die anstehenden Aufgaben mit Prioritäten versehen werden. Und außerdem kann das Team das Messen des Erfolg der einzelnen Features im Rahmen des Entwicklungszyklus planen. [Weitere Informationen][usage]

## Ihre Anwendungen

Ein Team kann also Application Insights nicht nur nutzen, um einzelne Probleme zu beheben, sondern auch um seine Softwareentwicklung zu verbessern. Ich hoffe, dass ich Ihnen einige Ideen vermitteln konnte, wie Sie Application Insights die Leistung Ihrer Anwendungen verbessern können.

## Video

[AZURE.VIDEO performance-monitoring-application-insights]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[usage]: app-insights-web-track-usage.md
 

<!---HONumber=Nov15_HO3-->