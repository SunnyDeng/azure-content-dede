<properties 
	pageTitle="Azure Mobile Engagement – Leitfaden mit ersten Schritten und Best Practices"
	description="Leitfaden mit ersten Schritten für Azure Mobile Engagement und Best Practices für den Einstieg" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="12/11/2015"
	ms.author="wesmc"/>

# Azure Mobile Engagement – Leitfaden mit ersten Schritten und Best Practices

## Übersicht

**Der Bildschirm mobiler Geräte ist sehr unübersichtlich:** Im Jahr 2013 wurde in einer Studie festgestellt, dass auf durchschnittlichen mobilen Geräten 27 Anwendungen installiert waren. Benutzer verbringen in der Regel 30 Stunden pro Monat mit ihre Apps. Ein Großteil dieser Zeit wurde für soziale Netzwerke und Spiele aufgewendet (ca. 20 Stunden). Im Jahr 2014 konnten Benutzer auf dem Android-Markt zwischen ca. 1,5 Millionen Anwendungen wählen. Im Apple Store waren ca. 1,2 Millionen Apps enthalten. Die Verwendung der mobilen Apps nimmt weiterhin zu, und die Entwickler konkurrieren auf diesem wachsender Markt.

Der durchschnittliche mobile Benutzer installiert und deinstalliert Apps mit hoher Frequenz, abhängig von wechselnden Interessen und Erfahrungen mit Apps. Um den Erfolg einer App zu bestimmen, müssen Sie mehr wissen, als nur, wie viele Benutzer Ihre App installieren. Es ist wichtig zu wissen, wie nützlich Ihre App ist und ob sich dieser Nutzungstrend ändert. Die folgenden Fragen werden wichtig:

- Finden erste Benutzer Ihre App uninteressant oder veraltet? 
- Wie viele Benutzer verwenden Ihre App überhaupt nicht mehr? 
- Geht der Trend bei Einkäufen innerhalb der App nach oben oder unten?
- Schließen Benutzer Workflows aufgrund von Problemen mit der App oder eines Mangels an Interesse nicht ab? 
- Könnten Sie Ihre App nützlich und relevant halten, indem Sie neue Inhalte für Ihre Benutzer bereitstellen? 
- Wären diese neuen Inhalte für alle Benutzer identisch oder auf Benutzersegmente basierend auf dem Verhalten in Ihrer App abgestimmt? 
 
Antworten auf diese Fragen können den Lebenszyklus und den Ertrag Ihrer App erweitern. Sie können Ihnen auch helfen, Ihren Benutzerstamm zu definieren und zu erhalten.

Medienbezogene Apps weisen tendenziell die längste Nutzungsdauer bei Benutzern auf. Ein Grund hierfür ist, dass sie ständig neue Inhalte für Benutzer zur Verfügung stellen. Die frühe Einführung hilfreicher Pushbenachrichtigungen für ein bestimmtes Benutzersegment hat tendenziell große Auswirkungen auf die App-Nutzungsdauer.

Das Azure Mobile Engagement-Programm wurde entwickelt, um Ihnen zu helfen, den Lebenszyklus und die Nutzungsdauer Ihrer App zu erweitern, indem eine Methode zum Sammeln und Analysieren ausführlicher Informationen zur Verwendung der App bereitgestellt wird. Es hilft Ihnen dabei, Ihren Benutzerstamm anhand des Verhaltens zu klassifizieren und fokussierte Kampagnen für die Übermittlung von Pushbenachrichtigungen und Nachrichten innerhalb der App an identifizierte Benutzersegmente zu erstellen. Key Performance Indicators (KPIs) messen, wie aktiv die Benutzer mit verschiedenen Aspekten der App umgehen. Azure Mobile Engagement bietet die Methoden, mit denen Sie diese KPIs bestimmen können. Sie können damit die Rendite (ROI) erhöhen, indem Sie die Infrastruktur bereitstellen, die Sie zur Steigerung der Einbindung in die mobile App benötigen.

Um Azure Mobile Engagement optimal nutzen zu können, müssen Sie mit einem gut durchdachten Plan für die Einbindung beginnen. Ihr Plan hilft Ihnen, die genauen Daten zu identifizieren, die Sie benötigen, um Ihren Benutzerstamm zu segmentieren. Dies kann auf dem Verhalten und auf Erfahrungen mit der App basieren. Damit der Plan erfolgreich ist, empfiehlt es sich, die KPIs zu definieren, die die Ziele Ihrer App messen. Mit definierten eindeutigen Leistungsindikatoren können Sie die erforderliche Logik einfach in Ihre App einbetten, um genaue Daten zu sammeln, mit denen Sie die KPIs analysieren und auswerten können. Dieses Thema ist ein Handbuch mit bewährten Methoden zum Definieren von KPIs, die Sie mit dem Einbindungsplan verwenden.


## Schritt 1: Definieren der KPIs entsprechend dem BET-Modell


Das richtige Definieren von KPIs kann eine schwierige Aufgabe sein. Apps, die für verschiedene Branchen entwickelt wurden, weisen eigene Merkmale und Ziele auf. Dies kann Ihren Ansatz erschweren. Um dies zu vermeiden, sollten Ziele und KPIs in drei Hauptkategorien unterteilt werden: **Business**, **Einbindung** und **technisch**. Dies nennen wir das **BET-Modell**.

Eine sorgfältige Planung weist im Allgemeinen Ziele bei KPIs auf, die in allen der folgenden Kategorien des BET-Modells den Erfolg messen.


#### Business-KPIs

Die Erstellung von Business-KPIs sollte am einfachsten sein. Sie haben diese wahrscheinlich bereits in irgendeiner Form bei der Planung Ihrer mobilen App definiert. Diese KPIs helfen im Allgemeinen dabei, Umsatz und Rendite Ihrer App zu messen. Die folgende Liste enthält einige Beispiele für Business-KPIs, die als Leitfaden beim Definieren der Leistungsindikatoren dienen können:

- Business-KPIs für Medien
	- Anzahl von Anzeigen, auf die geklickt wurde
	- Anzahl der Seitenaufrufe pro Benutzer
	- Anzahl der aktuellen Abonnements
- Business-KPIs für Spiele
	- Anzahl der Einkäufe in der App
	- Durchschnittlicher Umsatz pro Benutzer (ARPU)
	- Zeit pro Sitzung
	- Gespielte Tage und aktueller Level im Spiel
- Business-KPIs für E-Commerce
	- Tage, an denen die App verwendet wurde
	- Durchschnittlicher Umsatz pro Benutzer (ARPU)
	- Durchschnittlicher Betrag im Einkaufswagen beim Bezahlen
	- Produktkategorie mit den meisten Aufrufen und Käufen
- Business-KPIs für Banken und Versicherungen
	- Anzahl der Konten
	- Aktivierte Funktionen
	- Besuchte Angebotsseiten
	- Geklickte oder aktivierte Warnungen	   



#### Einbindungs-KPIs

Ein Einbindungs-KPI ist ein Leistungsindikator, mit dem die Einbindung der Benutzer gemessen wird. Mit Trends in diesem Bereich können Sie die Nutzungsdauer Ihrer App ermitteln. Hier sind einige Beispielleistungsindikatoren für diese Art von KPIs:

- Aktive Benutzer in den letzten 7 Tagen
- Anzahl der inaktiven Benutzer für die letzten 7 Tage
- Anzahl der Benutzer, die die App in 30 Tagen nicht verwendet haben  

Einige offensichtliche externe Faktoren können Indikatoren in diesem Bereich beeinflussen. Beispielsweise könnten Sie davon ausgehen, dass sich ein mobiles Gerät jederzeit bei einem Benutzer befindet. Dies muss nicht zutreffen. Eine Spiele-App weist möglicherweise tendenziell eine höhere Auslastung an Feiertagen auf, wenn ein Spieler mehr spielt, während er nicht bei der Arbeit oder in der Schule ist.

Gut definierte KPIs in dieser Kategorie helfen dabei, die Beziehung zwischen Ihrer App und Ihren Kunden zu messen.



#### Technische KPIs

Mit Leistungsindikatoren in dieser Kategorie können Sie bestimmen, ob sich Ihre App richtig verhält, ob sie hängt oder abgestürzt ist. Diese Indikatoren können die Integrität Ihrer App messen und Probleme bei der Verwendbarkeit bestimmen, die verhindern, dass Benutzer Ihre App nutzen. Für diese Kategorie gesammelte Informationen können auch Leistungsinformationen enthalten, die für Marketingteams relevant sind. Die Daten können auch für die Problembehandlung durch IT- und Supportteams nützlich sein, um nicht gemeldete Fehler zu identifizieren.
 
Hier sind einige Beispiele für technische KPIs:

- Informationen zu nicht behandelten oder behandelten Ausnahmen und deren Anzahl 
- Zeitstempel für den letzten Absturz
- Schaltfläche, auf die zuletzt geklickt wurde, oder zuletzt besuchte Seite
- Speicherverwendung der App
- App-Bildfrequenz
- Betriebssystemversion, mit der die App ausgeführt wird
- App-Version

Definieren Sie diese KPIs zum Messen der App-Leistung und zum Ermitteln potenzieller Fehler. Diese Indikatoren sollten dabei helfen, die Zeit zu verringern, die Sie benötigen, um ein Update für Ihre Kunden bereitzustellen. Sie könnten auch dazu dienen, ein Benutzersegment zu definieren, bei dem bestimmte Probleme aufgetreten sind. Diese Benutzersegmentierung können Sie zum Erstellen von Kampagnen verwenden, um Benachrichtigungen in Bezug auf verfügbare Updates und potenzielle Werbeaktionen zu übermitteln, damit die Kundenzufriedenheit wiederhergestellt wird.


#### Übung 1: Erstellen des KPI-Dashboards

Beim Definieren Ihrer Marketingstrategie sollten die KPIs eine Ansicht für alle Ihrer Hauptziele darstellen. Es sollten klar definierte Datenpunkte sein, mit denen Sie wichtigen Informationen zum Überwachen Ihrer App und des Verhaltens des Endbenutzers sammeln können.

Erstellen Sie ein KPI-Dashboard mit den folgenden Informationen

1.	Was sind die KPIs für die App?
2.	Welche Datenpunkte verwende ich, um die einzelnen KPIs darzustellen?
3.	Wo befinden sich diese Daten für meine Anwendung (Bildschirm, Einstellungen, System...)?
4.	Kann ich eine Einbindungssequenz für diesen KPI wiedergeben?

Beispiele und eine Anleitung finden Sie im Arbeitsblatt **KPI Builder** in [Media Playbook Template][Media Playbook link] (in englischer Sprache).



## Schritt 2: Ihr Einbindungsprogramm


Ein hervorragendes Einbindungsprogramm sollte als eine wichtige Komponente Ihrer App angesehen werden. Dies sollte unbedingt ein großartiges Willkommensprogramm beinhalten, das für einen Benutzer während der ersten Tage der App-Verwendung ausgeführt wird. Dies hat eine sehr positive Wirkung auf die Einbindung und die Nutzungsdauer Ihrer App. Studien belegen, dass die Mehrheit der Benutzer die Verwendung einer App in den ersten Tagen nach der Installation beendet. Sie sollten sich bemühen, die Kundenerwartungen zu erfüllen oder zu übertreffen, um früh das Kundeninteresse zu stärken, während der Benutzer noch auf Ihre App konzentriert ist. Stellen Sie sicher, dass Sie den Hauptnutzen und die Vorteile der App für Ihre Kunden darstellen.


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Pushbenachrichtigungen sind die beste Vorgehensweise für eine frühe Einbindung von Benutzern mobiler Geräte. Allerdings sollten Sie beim Segmentieren von Benutzern für Pushbenachrichtigungen sehr umsichtig vorgehen. Sobald ein Benutzer den Eindruck hat, er würde Spam oder irrelevante Benachrichtigungen erhalten, kann dies schwerwiegende Auswirkungen haben. Mit einigen Klicks kann ein Benutzer Ihre Anwendung löschen und sie nicht mehr nutzen. Der Benutzer sollte in der App einen hochgradig personalisierten Nutzen erhalten, statt generischen Spam zu empfangen.

Sobald Benutzer aktiv eingebunden sind, kann Ihr Einbindungsprogramm andere Aspekte der App unterstützen.

Sie könnten z. B. eine Kampagne einrichten, die die aktiven Benutzer auffordert, Ihre App zu bewerten. Da dieses Benutzersegment am aktivsten ist und die meisten Erfahrungen mit Ihrer App hat, können Sie die genaueste Bewertung erwarten. Sobald Sie eine hohe App-Bewertung haben, steigt die Anzahl der Downloads Ihrer App und verringern sich die Kosten für die Gewinnung neuer Kunden.



#### Einbindungssequenz


Ein globales Einbindungsprogramm umfasst verschiedene Einbindungssequenzen. Jede Sequenz zielt darauf ab, mehrere Ziele zu erreichen.


###### Lebenszyklus-Pushsequenz


Die Ziele für die Lebenszyklus-Pushsequenz unterscheiden sich je nach Lebenszyklus der App-Einbindung eines Benutzers. Ein bestimmter Benutzer kann neu, inaktiv oder sehr aktiv sein. In verschiedenen Phasen eines Einbindungslebenszyklus können Benutzer von neuen Inhalten in Form von Tipps und Links zur Dokumentation profitieren.

Beispielsweise benötigt ein neuer Benutzer Hilfe bei der Orientierung in einer App oder profitiert von einem Anreiz für neue Benutzer wie dem folgenden, wenn er erstmals die App startet.

*"Wir freuen uns, Sie zu begrüßen! Denken Sie daran, sich zu registrieren, damit der erste Monat kostenlos ist!"*


###### Pushsequenz nach Verhalten

Die Pushsequenz nach Verhalten zielt darauf ab, die Verwendung anhand des für die App erfassten Benutzerverhaltens zu steigern.

Beispielsweise könnte ein sehr aktiver Benutzer einer Football-App davon profitieren, wenn er die folgende Pushbenachrichtigung erhält:

*"John, Sie sind ein großer Footballfan! Melden Sie sich bei unserem NFL-Bereich an, und gewinnen Sie ein kostenloses Ticket für den SuperBowl!"*


###### Informierende Pushsequenz

Benutzer schätzen relevante Nachrichten, die auf ihre Interessen abzielen. Eine informierende Pushsequenz verbessert die Einbindung durch Senden von Informationen auf der Grundlage der Interessen, die ein Benutzer deutlich gezeigt hat. Möglicherweise ist dies explizit bei der Auswahl der eigenen Interessen durch den Benutzer in der App geschehen. Sie könnten auch implizit basierend auf Daten bestimmt werden, die bei der Interaktion des Benutzers mit der App gesammelt wurden.

Beispielsweise kann der Benutzer einer E-Commerce-App regelmäßig eine bestimmte Kaffeemarke kaufen, was Sie mit einem KPI erfasst haben. Die folgende Information kann die Einbindung dieses Benutzers in die App verbessern.
 
*"Hallo Wes, für eine Ihrer bevorzugten Kaffeemarken gibt es in der ersten Woche im September 2015 ein Angebot mit einer Preissenkung um 25 %. Wir schätzen Sie als Kunden und möchten sicherstellen, dass Sie das Angebot kennen."*

###### Pushsequenz zur Nutzungsdauer

Diese Sequenz zielt darauf ab, Benutzer mit Kampagnen mit wiederholten Pushbenachrichtigungen zu binden, mit denen die Nutzung der App zur Gewohnheit werden soll. Dies kann die Nutzungsdauer der App verlängern, wenn der Benutzer die Interaktionen genießt.

Beispielsweise kann der Benutzer einer Sport-App die folgende Pushbenachrichtigung wöchentlich auf Grundlage der Lieblingsmannschaften des Benutzers erhalten:

*"Stimmen Sie ab, ob die New York Yankees das Spiel diese Woche gegen die Toronto Blue Jays gewinnen, Sie haben die Chance, 200 Punkte damit zu gewinnen!"*


#### Der 3W-Ansatz

Die Verwendung der verschiedenen Pushsequenzen hilft Ihnen, die Endbenutzer einzubinden. Allerdings benötigen Sie weiterhin den 3W-Ansatz zum Personalisieren der Benachrichtigungen. Der 3W-Ansatz sollte das Wer, Was und Wann für jede Benachrichtigung berücksichtigen. Wenn Sie diese drei Fragen eindeutig beantworten, sollten Ihre Benachrichtigungen für die Einbindung richtig ausgerichtet sein.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### Wer: Das Benutzersegment, das Nachrichten erhält

Pushbenachrichtigungen an Ihre Benutzer sollten als sehr sensibler Kommunikationskanal betrachtet werden. Stellen Sie sicher, dass die Benachrichtigungen, die Sie an ein Benutzersegment senden möchten, gut an den Interessen dieses Benutzersegments ausgerichtet sind. Eine falsch geroutete Benachrichtigung hat sehr wahrscheinlich negative Auswirkungen auf einen Benutzer. Sie kann als Spam angesehen werden und zur Deinstallation Ihrer App führen.

Verwenden Sie eine Kombination aus bestimmten Kriterien der Technik und des Verhaltens beim Definieren der Benutzersegmente, die Benachrichtigungen erhalten sollen. Ein einfaches Beispiel für das Definieren eines Benutzersegments ist die folgende Aussage:

"Alle Benutzer, die vor drei Tagen eine mobile Anwendung zum ersten Mal gestartet haben und die Anmeldeseite zweimal besucht haben, ohne sich tatsächlich anzumelden."
 
Mit dieser Aussage können Sie die Daten identifizieren, die Sie sammeln müssen, um ein bestimmtes Szenario zu unterstützen.


###### Was: Die Nachricht, die Sie senden

**Ton**

Verwenden Sie für die Einbindung einen Ton, der für Ihre segmentierten Benutzer geeignet ist. Dies ist definitiv eine gute Möglichkeit, Kontakt mit den Endbenutzern aufzunehmen und das Interesse eines Benutzers an Ihrer App zu fördern.

**Umleitung**

Eine Pushbenachrichtigung kann für mehr verwendet werden, als die Anwendung zu öffnen. Wenn die Benachrichtigung einen Kontext bietet wie eine Nachrichtensendung oder eine Werbeaktion für ein Produkt, kann diese Benachrichtigung direkt per Deep-Link mit den richtigen Inhalten in der Anwendung verknüpft sein. Um dies zu unterstützen, müssen Sie ein URL-Schema erstellen, damit die Anwendung die Umleitung verarbeiten kann. Wenn Sie Ihre Einbindungssequenzen bearbeiten, ist dies ein wichtiger Schritt, der nicht vergessen werden darf.

Die Umleitung kann auch für andere Systeme verwaltet werden. Mit einer Aktions-URL ist es beispielsweise möglich, Endbenutzer zu vielen anderen Systemen umzuleiten, einschließlich der folgenden:

- Website
- Postfach mit bereits eingerichteter E-Mail-Adresse
- SMS-Postfach
- Einwähldienst
- App Store zur Bewertung der Anwendung 

Dies bietet viele Möglichkeiten für die Einbindung von Endbenutzern und die Erstellung automatischer Regeln zur Verbesserung der Leistung.


**Format/Inhalt**

Verschiedene Typen und Formate von Pushbenachrichtigungen:

1. **Ankündigungen**: Sie können in verschiedenen Situationen Werbebotschaften an Benutzer senden (außerhalb der App, in der App oder jederzeit).
2. **Umfragen**: Sie können Informationen zu Endbenutzern erfassen, indem Sie ihnen Fragen stellen. Diese Antworten sind dann beim Erstellen von Kriterien verfügbar, um Endbenutzer zu erreichen.
3. **Datenpushes**: Sie können eine Binär- oder Base64-Datendatei zum Aktualisieren der App senden. Die Informationen in einem Datenpush werden an Ihre Anwendung gesendet, um die Benutzerumgebung in Ihrer App zu personalisieren. Die Anwendung muss so konzipiert sein, dass sie Daten in einem Datenpush unterstützt.
4. **Kacheln (nur Windows Phone)**: Sie können den Microsoft-Pushbenachrichtigungsdienst (MPNS) verwenden, um systemeigene Pushbenachrichtigungen mit XML-Daten zu senden. (Wird seit SDK-Version 0.9.0 unterstützt. Die endgültige Nutzlast für Kacheln darf 32 KB nicht überschreiten.) Die Nachricht wird direkt auf der entsprechenden Kachel angezeigt.
5. **Webansicht**: Eine Webansicht ist ein Popup mit Webinhalt. Dieses Popupfenster wird angezeigt, nachdem der Benutzer auf die Pushbenachrichtigung geklickt hat. Eine Webansicht ermöglicht es Ihnen, dem Benutzer weitere Interaktionen zu ermöglichen.
 
>[AZURE.NOTE]Stellen Sie sicher, dass der Inhalt, den Sie als Pushbenachrichtigungen senden, den Richtlinien der jeweiligen Plattform (iOS, Android, Windows) für das Entwickeln von Apps und das Senden von Pushbenachrichtigungen entspricht.

 


###### Wann: Der Zeitablauf Ihrer Kampagne.

Wenn ist der beste Zeitpunkt zum Aktivieren einer Kampagne, die Pushbenachrichtigungen auslöst? Sollte sie manuell oder automatisch ausgelöst werden? Sollte sie wiederholt werden? Das Bestimmen der richtigen Zeit oder Häufigkeit ist entscheidend für die Einbindung von Benutzern mit den besten Ergebnissen. Für jede Einbindungssequenz und jedes Szenario müssen Sie angeben, wann der beste Zeitpunkt zum Senden von Pushbenachrichtigungen ist. Hier sind einige mögliche Beispiele:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Wenn Sie täglich viele Benachrichtigungen senden, muss ernsthaft in Betracht gezogen werden, dass die Benutzer Ihre Kommunikation als Spam wahrnehmen könnten.

Azure Mobile Engagement bietet zwei Möglichkeiten, um zu verhindern, dass Ihre Kommunikation als Spam wahrgenommen wird. Verwenden Sie eine differenzierte Segmentierung, um sicherzustellen, dass Sie nicht die gleichen Benutzer erreichen. Darüber hinaus bietet Azure Mobile Engagement ein "Kontingentfeature". Dieses Feature kann die für eine Kampagne gesendeten Benachrichtigungen begrenzen. Beispiel: Durch Festlegen eines Standardkontingents auf 5 pro Woche wird sichergestellt, dass ein Benutzer, der Teil des Benutzersegments der Kampagne ist, für diese Woche nicht mehr als 5 Benachrichtigungen erhält.





#### Übung 2: Erstellen des Einbindungsprogramms

Wenden Sie ein wenig Zeit auf, Ihre Ziele zusammenzufassen und Kampagnen zu definieren, die mit bestimmten Sequenzen wiedergeben werden. Stellen Sie sicher, dass Sie den 3W-Ansatz auf Benachrichtigungen in Ihren Kampagnen anwenden.

Beispiele und eine Anleitung finden Sie im Arbeitsblatt **Engagement Program** in [Media Playbook Template][Media Playbook link] (in englischer Sprache).


## Schritt 3: App-Integration


#### Erstellen eines Markierungsplans

Um Azure Mobile Engagement in Ihre App zu integrieren, müssen Sie einen Markierungsplan erstellen. Der Markierungsplan ist ein Eckpfeiler des Projekts. Er definiert die Beziehung zwischen Marketingspezifikationen, dem Workflow der Anwendung und echten Markierungsdaten, die in der App gesammelt werden, um KPIs zu messen. Er gibt an, welche Analysen im Portal angezeigt werden können. Außerdem hilft er dabei, die Benutzersegmente zu definieren und zum Einbinden der Endbenutzer abgestimmte Pushbenachrichtigungen zu senden. Wenn Ihr Markierungsplan definiert ist, können Sie mit dem Azure Mobile Engagement-SDK ganz einfach den Code hinzufügen, um ihn in Ihre App zu integrieren.

Mit einem Markierungsplan sollte nicht alles in einer Anwendung markiert werden. Er sollte nur Markierungsdaten umfassen, die Bestandteil Ihrer Strategie für die mobile Einbindung sind. Diese sind wahrscheinlich von den Anwendungen abhängig. Die [Media Playbook Template][Media Playbook link], die von Azure Mobile Engagement bereitgestellt wird, unterstützt Sie beim Erstellen eines Markierungsplans mit einer bestimmten Methode. Verwenden Sie das Arbeitsblatt **Tag Plan** als Leitfaden für die Erstellung Ihres Markierungsplans.

Wenn Sie einen Markierungsabschnitt im Arbeitsblatt definieren, seien Sie äußerst spezifisch. Dies ist sehr wichtig, um ein Durcheinander zu vermeiden. Geben Sie jedes erwartete Szenario an, in dem die einzelnen Markierungen gesendet werden. Nehmen Sie den Namen der Aktivität auf, in die die einzelnen Markierungen eingebettet sind. Dies sollte alles im Abschnitt **Informative** des Arbeitsblatts enthalten sein. Das Arbeitsblatt mit dem Markierungsplan sollte die Hauptreferenz für die Testüberprüfung sein.

Im Abschnitt **Data to collect** sollte Ihr Entwicklungsteam die Typen, Namen, Werte und Schlüssel/Wert-Paare mit zusätzlichen Informationen finden, die für jede Markierung erforderlich sind, die in die Anwendung eingebettet wird.

Es wird empfohlen, den Markierungsplan mit allen Teams durchzugehen, die dem Projekt zugeordnet sind. Nehmen Sie erforderliche Korrekturen vor, und vergewissern Sie sich, dass die Marketing- und Entwicklungsteams alles verstanden haben.

Das Arbeitsblatt **Statement of work** können alle Projektbeteiligten als Leitfaden verwenden.


#### Datentypen

Dies sind gängige Datentypen, die von Azure Mobile Engagement unterstützt werden.

###### Geräte und Benutzer

Azure Mobile Engagement identifiziert Benutzer, indem für jedes Gerät ein eindeutiger Bezeichner generiert wird. Dieser Bezeichner wird Gerätebezeichner (oder "deviceid") genannt. Er wird so generiert, dass alle auf demselben Gerät ausgeführten Anwendungen denselben Gerätebezeichner aufweisen.

###### Sitzungen und Aktivitäten

Eine Sitzung ist eine Instanz der App, die von einem Benutzer ausgeführt wird. Die Sitzung dauert von dem Zeitpunkt, an dem der Benutzer die App startet, bis sie beendet wird.

Eine Aktivität ist eine logische Gruppierung einer Reihe von Aktionen, die in der App während einer Sitzung ausgeführt werden können. Es ist in der Regel ein bestimmter Bildschirm in der App, aber es kann alles sein, was von der Logik der Anwendung definiert wird. Zumindest sollten Sie jeden Bildschirm oder jede Aktivität für die App markieren. Dies ermöglicht es Ihnen, den Benutzerpfad zu verstehen.


###### Ereignisse

Ereignisse werden verwendet, um Benutzerinteraktionen mit der App zu melden. Es können direkte Aktionen wie die gemeinsame Nutzung von Inhalten oder das Starten eines Videos sein. Durch das Markieren von Ereignissen erhalten Sie Datensammlungen, die zeigen, wie Benutzer mit der Anwendung interagieren.

###### Aufträge

Aufträge werden verwendet, um Aktionen zu melden, die eine Dauer aufweisen. Beispiele hierfür sind:

- Ausführung von API-Aufrufen
- Anzeigezeiten für Anzeigen
- Dauer von Hintergrundaufgaben 
- Dauer des Einkaufs
- Anzeige eines Videos


###### Fehler

Fehler werden verwendet, um von der App erkannte Probleme zu melden. Dazu gehören falsche Benutzeraktionen oder API-Aufruffehler.

###### Anwendungsinformationen

Anwendungsinformationen (App-Info) werden zum Markieren von Daten im Zusammenhang mit einer Benutzeroberfläche einer Anwendung verwendet. Sie werden durch eine Benutzerinteraktion mit der Anwendung generiert.

Für einen angegebenen App-Info-Schlüssel wird nur der letzte Wert von Azure Mobile Engagement nachverfolgt (kein Verlauf). App-Info zeigt den Status der App oder der Endbenutzer an. Beispiele: Anmeldestatus oder die bevorzugte Produktgruppe eines Benutzers.

###### Absturzdaten

Vom Mobile Engagement-SDK automatisch gesammelte Absturzdaten melden Anwendungsfehler, die von der Anwendung nicht verarbeitet werden. Beispiel: Eine aufgetretene, nicht behandelte Ausnahme.


###### Zusätzliche Daten

Ereignisse, Fehler, Aktivitäten und Aufträge können mit Parametern erweitert werden. Dies sind zusätzliche Informationen, die ein Entwickler als spezifische Daten aus der Anwendung bereitstellen kann. Das ist wichtig zum Definieren einer differenzierten Segmentierung.

Beispielsweise können Sie mit dem Wert einer Markierung "Artikel" Endbenutzer basierend darauf segmentieren, wer diesen bestimmten Artikel angezeigt hat. Möglicherweise ist dies jedoch nicht ausreichend. Es kann besser sein, wenn die gleiche Markierung "Artikel" auch zusätzliche Informationen wie "Nachrichtenkategorie" innerhalb einer Aktivität enthalten würde. Dies wäre nützlich, um die bevorzugten Kategorien für den Benutzer dynamisch zu bestimmen.

Zusätzliche Informationen werden als Schlüssel/Wert-Paar gemeldet. Im Beispiel für diese Medienanwendung wären die zusätzlichen Informationen für "Nachrichtenkategorie" der Wert für diese Kategorie. Beispiele: "Sport", "Wirtschaft" oder "Politik".





#### Markierungs- und SDK-Integration 

Ausführliche Anweisungen für die Integration des Azure Mobile Engagement-SDK in Ihre App finden Sie in der Dokumentation [Engagement-SDK-Integration](mobile-engagement-windows-store-integrate-engagement.md) auf der Azure-Website. Wählen Sie aus den Links am oberen Rand der Seite die Zielplattform aus.

Es wird empfohlen, Projekte für zwei Apps auf der Basis von Azure Mobile Engagement zu erstellen. Eine für Entwicklungs- und Teststaging und die andere für Produktionsstaging. Ihr IT-Team kann dann die Heraufstufung vom Teststaging zur Produktion vornehmen, wenn der Benutzerakzeptanztest erfolgreich ist.



#### Benutzerakzeptanztest (UAT)

Beim Benutzerakzeptanztest (UAT) soll sichergestellt werden, dass alles wie vorgesehen funktioniert. Workflows können abgeschlossen werden und sammeln alle erforderlichen Daten anhand Ihres Markierungsplans:
 
- Informationsmarkierungen sind entsprechend der dokumentierten AZME-Konzepte vorhanden.
- Alle benötigten Informationen werden gesammelt (einschließlich der Werte für zusätzliche Informationen und App-Informationen)
- Bezeichnungen stimmen mit Ihrem Markierungsplan überein.
- Es werden keine doppelten Markierungen gesendet.

Testen Sie alle Typen von Benachrichtigungsverhalten gründlich, die Sie in Ihre App eingebettet haben.

- Ankündigungen, Umfragen, Datenpushes aus der App und innerhalb der App
- Text-/Webanzeigen
- Badge-Update, Kategorien



#### Einrichtung

Das Einrichten von Azure Mobile Engagement ist sehr einfach. Die gesamte Dokumentation im Zusammenhang mit der Benutzeroberfläche steht auf der Azure Mobile Engagement-Website unter [Durch die Benutzeroberfläche navigieren](mobile-engagement-user-interface-navigation.md) zur Verfügung.

Es wird empfohlen, dass Sie zunächst die richtigen Rollen und Rollenmitgliedschaften für die Benutzer des Projekts einrichten. Dadurch können Sie den entsprechenden Zugriff auf die Plattform für alle Benutzer verwalten. Mögliche Rollen:

- Administratoren
- Entwickler
- Besucher 

Danach: – Registrieren Sie Ihre Geräte-ID, um Ihr eigenes Gerät zu testen. – Wechseln Sie zu den Einstellungen Ihres Kontos, und richten Sie die Zeitzone ein, damit Diagramme und die Zustellzeit von Benachrichtigungen auf Ihre Zeitzone festgelegt sind. – Wechseln Sie zu den Einstellungen der Anwendung, und registrieren Sie die "App-Info", die Sie für die Endbenutzer benötigen, unter "Reach".

Weitere Informationen zum Ausführen der ersten Kampagne mit Pushbenachrichtigungen finden Sie unter [Erste Schritte bei der Verwendung und Verwaltung von Push-Vorgängen, um Ihre Endbenutzern zu erreichen](mobile-engagement-how-tos.md).



## Zusammenfassung


Einbindungsprogramme sind iterativ und sollten kontinuierlich verbessert werden, während Sie testen, was für Ihre App am besten geeignet ist.

Zu Beginn, während Sie Erfahrungen mit Einbindungsstrategien sammeln, sollten Sie nicht versuchen, eine vollständige, globale Einbindungsstrategie zu erstellen. Gehen Sie Schritt für Schritt vor, und identifizieren Sie die KPIs und deren Nutzung. Die Einbindungsstrategie ist für jede Anwendung einzigartig.

Nachdem Sie einige Erfahrungen gesammelt haben, könnten Sie Ihren Einbindungsprogrammen Folgendes hinzufügen:

- Überwachung: Sie gewinnen Benutzer, und wahrscheinlich definieren Sie Quellen für die Datensammlung. Azure Mobile Engagement kann mit den Quellen für die Datensammlung verknüpft werden. Dadurch können Sie die Leistung jeder Quelle überwachen. Diese Informationen sind interessant, um Ihre Anschaffungsinvestition zu maximieren. 

- A/B-Tests: Dies ist ein wesentlicher Teil des Einbindungsprogramms. Jede App verfügt über eigene Besonderheiten. Mit A/B-Tests können Sie Ihr Einbindungsprogramm verbessern.

- Geolocation: Dies ist eine große Chance für Marken. Dank dieser Funktion können Sie Benutzer am richtigen Ort und zum richtigen Zeitpunkt erreichen. Sie sollten überprüfen, ob Sie genügend Endbenutzerdaten zum Verhalten gesammelt haben, bevor Sie beginnen, Geolocation zu verwenden.

- Datenpush: Ein Datenpush ist ein unsichtbarer Push. Ein Datenpush ermöglicht das Anpassen Ihrer Anwendung basierend auf dem Endbenutzerverhalten. Wenn sich ein Benutzersegment z. B. häufig über Hightechprodukte informiert, kann der Besitzer der App einen Datenpush senden, um die entsprechende Startseite mit Hightechinhalt zu personalisieren.



## Nächste Schritte

- [Erstellen Sie ein Azure Mobile Engagement-Konto](mobile-engagement-create-account.md).
- Besuchen Sie [Definieren der Mobile Engagement-Strategie](mobile-engagement-define-your-mobile-engagement-strategy.md), um weitere Informationen zum Definieren der Mobile Engagement-Strategie zu erhalten.



  

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

<!---HONumber=AcomDC_1217_2015-->