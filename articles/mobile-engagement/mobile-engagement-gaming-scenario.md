	<properties 
	pageTitle="Azure Mobile Engagement implementation for Gaming App"
	description="Gaming app scenario to implement Azure Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="11/23/2015"
	ms.author="piyushjo"/>

#Implementieren von Mobile Engagement mit Spiele-App

## Übersicht

Ein Gaming-Startup hat eine neue Rollenspiel-/Strategiespiel-App rund ums Angeln veröffentlicht. Das Spiel ist seit sechs Monate erhältlich. Das Spiel ist ein großer Erfolg und wurde Millionen Mal heruntergeladen. Die Beibehaltungsdauer ist im Vergleich zu anderen Spiele-Apps von Startups sehr hoch. Bei der vierteljährlichem Besprechung vereinbarten die Beteiligten, dass der durchschnittliche Umsatz pro Benutzer (ARPU) erhöht werden müsse. Premium-Pakete im Spiel sind als Sonderangebote verfügbar. Mit diesen Spielpaketen können Benutzer die Darstellung und Leistung ihrer Angelschnüre und Köder oder ihrer Ausrüstung im Spiel aktualisieren. Die Verkaufszahlen für Pakete sind jedoch sehr niedrig. Daher entscheiden sie sich dafür, zuerst die Kundenzufriedenheit mit einem Analysetool zu analysieren und dann ein Engagementprogramm mit erweiterter Segmentierung zu entwickeln, um die Verkaufszahlen zu erhöhen.

Auf der Grundlage des [Azure Mobile Engagement – Leitfaden mit ersten Schritten und Best Practices](mobile-engagement-getting-started-best-practices.md) entwickeln sie eine Engagementstrategie.

##Ziele und KPIs

Die wichtigsten Beteiligten für das Spiel treffen sich. Alle vereinbaren ein Hauptziel: die Steigerung der Umsatzzahlen für Premium-Pakete um 15 %. Sie erstellen Business Key Performance Indicators (KPIs), um dieses Ziel zu messen und voranzutreiben.

* Auf welchem Level des Spiels werden diese Pakete gekauft?
* Wie hoch ist der Umsatz pro Benutzer, pro Sitzung, pro Woche und pro Monat?
* Was sind die bevorzugten Kauftypen?

In Teil 1 des [Leitfadens mit ersten Schritten](mobile-engagement-getting-started-best-practices.md) wird erläutert, wie Sie Ziele und KPIs definieren.

Nachdem die Business-KPIs definiert wurden, kann der Mobile-Produktmanager Engagement-KPIs aufstellen, um neue Benutzertrends und die Beibehaltung zu bestimmen.

* Überwachen von Beibehaltung und Nutzung in den folgenden Intervallen: täglich, alle zwei Tage, wöchentlich, monatlich und alle drei Monate
* Anzahl aktiver Benutzer
* Die Appbewertung im Store

Basierend auf Empfehlungen vom IT-Team, wurden die folgenden technischen KPIs hinzugefügt, um die folgenden Fragen zu beantworten:

* Wie sieht mein Benutzerpfad aus (welche Seite wird besucht, wie viel Zeit verbringen Benutzer auf dieser Seite)?
* Anzahl der Abstürze oder Fehler pro Sitzung
* Welche Betriebssystemversionen verwenden meine Benutzer?
* Was ist die durchschnittliche Bildschirmgröße meiner Benutzer?
* Wie verbinden sich meine Benutzer mit dem Internet?

Der Mobile-Produktmanager gibt für jeden KPI die benötigten Daten sowie den Speicherort der Daten im Playbook an.

## Engagementprogramm und -integration

Vor dem Erstellen eines erweiterten Engagementprogramms, sollte der verantwortliche Mobile-Projektleiter genau verstehen, wie und wann die Produkte von den Benutzern genutzt werden.

Nach drei Monaten hat der Mobile-Projektleiter genügend Daten zur Verbesserung der App-internen Pushbenachrichtigungsumsätze erfasst. Die folgenden Daten wurden erfasst:

* Der erste Kauf erfolgt in der Regel auf Level 14. In 90 % der Fälle werden neue legendäre Waffen für 3 US-Dollar gekauft.
* In 80 % der Fälle verwenden Benutzer, die einen Kauf getätigt haben, das Produkt weiterhin und tätigen auch weitere Einkäufe.
* Benutzer, die Level 20 abgeschlossen haben, beginnen mehr als 10 US-Dollar pro Woche auszugeben.
* Benutzer neigen dazu Premium-Pakete auf Level 16, 24 und 32 zu kaufen.

Dank dieser Analyse beschließt der Mobile-Projektleiter, bestimmte Pushbenachrichtigungssequenzen zu erstellen, um App-interne Käufe zu erhöhen. Er erstellt drei Pushsequenzen, die er folgendermaßen nennt: Welcome Program, Sales Program und Inactive Program. Weitere Informationen finden Sie in den [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->

<!---HONumber=AcomDC_1203_2015-->