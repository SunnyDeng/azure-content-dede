<properties 
	pageTitle="Azure Mobile Engagement-Implementierung für Medien-App"
	description="Medien-App-Szenario zum Implementieren von Azure Mobile Engagement" 
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
	ms.date="02/29/2016"
	ms.author="piyushjo"/>

#Implementieren von Mobile Engagement per Medien-App

## Übersicht

Peter ist Manager für mobile Projekte bei einem großen Medienunternehmen. Er hat vor Kurzem eine neue App eingeführt, die über sehr hohe Downloadzahlen verfügt. Er hat seine Ziele in Bezug auf die Downloadzahlen erreicht, aber der Return On Investment (ROI) pro Benutzer erfüllt seine Anforderungen nicht.

Peter hat bereits herausgefunden, warum der ROI zu niedrig ist. Benutzer beenden die Nutzung der App häufig nach nur zwei Wochen und kehren dann nie mehr zurück. Er möchte erreichen, dass die App länger genutzt wird.

Nach einigen anfänglichen Tests hat Peter herausgefunden, dass die Benutzer seine App eher weiterverwenden, wenn er sich mit ihnen über Pushbenachrichtigungen vernetzt. Auch Benutzer, die inaktiv waren, kehren häufig zur App zurück, wenn er entsprechende Benachrichtigungen sendet. Peter trifft die Entscheidung, in eine Art Engagement-Programm für seine App zu investieren, bei dem eine erweiterte Zielgruppenansprache mit Pushbenachrichtigungen verwendet wird.

Peter hat vor Kurzem die Informationen unter [Azure Mobile Engagement – Leitfaden mit ersten Schritten und Best Practices](mobile-engagement-getting-started-best-practices.md) gelesen und möchte die Empfehlungen des Leitfadens implementieren.

##Ziele und KPIs

Es findet ein Treffen der wichtigsten Personen statt, die an der App von Peter beteiligt sind. Der Umsatz wird über Anzeigen generiert, die Benutzern beim Konsumieren der Medien angezeigt werden. Indem Peter die pro Benutzer konsumierten Inhalte erhöht, steigert er den Umsatz. Es wird ein Hauptziel vereinbart: Erhöhung des Umsatzes mit Anzeigen um 25 %. Es werden Business Key Performance Indicators (KPIs) erstellt, um dieses Ziel zu messen und voranzutreiben.

* Anzahl von Anzeigen, auf die pro Benutzer geklickt wird
* Anzahl der besuchten Artikelseiten (pro Benutzer/pro Sitzung/pro Woche/pro Monat…)
* Bevorzugte Kategorien

Basierend auf der Besprechung mit den wichtigsten Beteiligten hat Peter seine Business-KPIs definiert. Er hält sich dabei an Teil 1 von [Azure Mobile Engagement – Leitfaden mit ersten Schritten und Best Practices](mobile-engagement-getting-started-best-practices.md).

Anschließend erstellt er die folgenden Engagement-KPIs, um sicherzustellen, dass die Ziele erreicht werden:

* Überwachung der Nutzungsdauer für die folgenden Intervalle: täglich, wöchentlich, alle zwei Wochen und einmal im Monat.
* Anzahl der aktiven Benutzer
* App-Bewertung in App Stores

Basierend auf Empfehlungen vom IT-Team wurden die folgenden technischen KPIs hinzugefügt, um die folgenden Fragen zu beantworten:

* Wie sieht mein Benutzerpfad aus (welche Seite wird besucht, wie viel Zeit verbringen Benutzer auf dieser Seite)?
* Anzahl der Abstürze oder Fehler pro Sitzung?
* Welche Betriebssystemversionen nutzen meine Benutzer?
* Was ist die durchschnittliche Bildschirmgröße meiner Benutzer?
* Welche Internetverbindungen verwenden meine Benutzer?

Für jeden KPI klassifiziert er die erforderlichen Daten und zeichnet sie am richtigen Ort in seinem Playbook auf.

## Engagement-Programm und -Integration

Nachdem Peter die Definition der KPIs abgeschlossen hat, beginnt er mit der Phase der Engagement-Strategie, indem er vier Engagement-Programme und die damit verbundenen Ziele definiert: ![][1]

Anschließend steigt Peter noch tiefer ein und gibt die Details für die Pushbenachrichtigungen der einzelnen Programme an. Pushbenachrichtigungen werden anhand von fünf Elementen definiert:

1. Ziel: Was ist das Ziel der Benachrichtigung?
2. Wie wird das Ziel erreicht?
3. Zielperson: Wer erhält die Benachrichtigung?
4. Inhalt: Welcher Text und welches Format werden für die Benachrichtigung verwendet (In-App/außerhalb der App)
5. Zeitpunkt: Was ist der beste Zeitpunkt zum Senden der Pushbenachrichtigung?

	![][2]

Weitere Informationen finden Sie in den [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Gemäß Teil 2 des Whitepapers verwendet Peter den Zielabschnitt, um festzulegen, welche Daten er erfassen muss. Er schreibt seinen Markierungsplan gemeinsam mit dem IT-Team, um die Lösung zu implementieren. Nach einer Woche mit Implementierungsarbeit und Tests der Benutzerakzeptanz kann Peter seine Programme endlich starten.

##Programmergebnisse

Vier Monate später sieht sich Peter die Leistung der Programme an. Für das Willkommensprogramm und das wöchentliche Programm wurden die Ziele erreicht. Die Anzahl der Benutzer mit nur einer Sitzung hat abgenommen, es werden mehr Funktionen der App genutzt, und die Anzahl der Verbindungen pro Woche hat sich verdoppelt.

Das **Programm zur Inaktivität** hilft Peter dabei, die Neigungen der Benutzer besser zu verstehen. Anscheinend kehren 15 % der inaktiven Benutzer zur App zurück. Die meisten von ihnen bleiben aber nicht länger als einen Monat aktiv. Peter schwebt eine potenzielle Optimierung dieser Sequenz durch zusätzliche Benachrichtigungen und eine Erweiterung der Inhaltsauswahl vor.

Das **Programm für die Ermittlung** funktioniert nicht gut. Das Cross-Selling wird gesteigert, aber nicht so stark, dass die Ziele erreicht werden. Peter findet heraus, dass er nicht über genügend Daten verfügt, um die Zielgruppenansprache relevant zu gestalten und geeignete Inhalte vorzuschlagen. Er stoppt sein Programm und konzentriert sich auf das Senden von „Editorial-Pushbenachrichtigungen“ mit Azure Mobile Engagement. Seine Journalisten verfügen bereits über eine CMS-Lösung zum Senden von Pushbenachrichtigungen und möchten sie beibehalten.

Peter entscheidet sich für den Einsatz der Reach-API. Hierbei handelt es sich um eine HTTP-REST-API, die die Verwaltung von Reach-Kampagnen ohne Verwendung der AZME-Webschnittstelle ermöglicht. Bei diesem Ansatz kann Peter die benötigten Daten erfassen, und seine Journalisten können die CMS-Lösung weiterverwenden.

Um sicherzustellen, dass dieses Feature richtig funktioniert, bittet Peter das IT-Team, besonders auf die folgenden Punkte zu achten:

1. **Betriebssysteme**: Da alle Betriebssysteme eigene Regeln zum Verwalten von Pushbenachrichtigungen haben, entscheidet sich Peter dafür, alle Fälle aufzulisten und zu überprüfen, ob sie von den APIs verarbeitet werden können. Beispiel: Beim Android-Pushsystem sind große Bilder zulässig, was für iOS nicht der Fall ist.

2. **Zeitrahmen**: Peter wünscht sich eine API, mit der der Zeitrahmen festgelegt und das Ende von Kampagnen angegeben werden kann. Er möchte verhindern, dass Benutzer durch zu viele Benachrichtigungen gestört werden.

3. **Kategorien**: Das Marketingteam bereitet die Vorlage für die einzelnen Benachrichtigungsarten vor. Peter bittet das IT-Team, in der API Kategorien festzulegen.

Nach einigen Tests ist Peter zufrieden. Dank dieser API können die Journalisten weiterhin Pushbenachrichtigungen per CMS senden, und mit Azure Mobile Engagement werden alle Verhaltensdaten gesammelt.

Nach diesen ersten vier Monaten spiegeln die Ergebnisse eine gute Gesamtleistung wider und geben Peter und seinen Chefs Sicherheit. Der ROI pro Benutzer hat sich um 15 % erhöht, und der mobile Umsatz macht 17,5 % des Gesamtumsatzes aus. Dies ist eine Steigerung von 7,5 % innerhalb von vier Monaten.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

<!---HONumber=AcomDC_0302_2016-->