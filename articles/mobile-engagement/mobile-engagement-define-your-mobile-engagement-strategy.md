<properties 
	pageTitle="Definieren der Mobile Engagement-Strategie" 
	description="Erfahren Sie mehr über die Integration und Optimierung von Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen." 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="kapiteir" />

# Definieren der Mobile Engagement-Strategie

Sie haben Ihre Anwendung aus einem bestimmten Grund geschrieben: damit sie von Ihren Benutzern genutzt wird!

Sie haben sicherlich viel Arbeit investiert, damit sie zu einer hervorragenden App wird, auf die die Benutzer nicht verzichten möchten. Sie haben wahrscheinlich auch einen beträchtlichen Betrag Ihres Marketingbudgets investiert, um Benutzer zu gewinnen. Aber nach der erfreulichen anfänglich hohen Anzahl von Benutzern tendieren diese möglicherweise langsam dazu, Ihre App nicht mehr zu benutzen. **Darum geht es bei Azure Mobile Engagement**: Die Bindung der Benutzer, die es Ihnen ermöglicht, Ihre App durch Testen und Erfahrungen schrittweise zu optimieren.

Unser Ansatz zur Optimierung der Beibehaltung und Nutzung basiert auf der Beteiligung von App-Benutzern durch Pushbenachrichtigungen und In-App-Nachrichten. Dies geschieht jedoch auf besondere Weise mithilfe von Nachrichten und Kommunikationsmitteln, die auf die Benutzer und deren jeweiliges Verhalten in Ihrer App abgestimmt sind. Unser Ziel ist es, dass Sie mit der richtigen Zielgruppe zur richtigen Zeit und am richtigen Ort kommunizieren.

Dazu müssen Sie jedoch **Ihre Benutzer verstehen**, dann Gruppen auf Basis ihres Verhaltens oder ihrer Merkmale (diese werden als Segmente bezeichnet) erstellen und anschließend relevante Kommunikationsmittel für die einzelnen Segmente erzeugen.

## Mobile Engagement dient Ihren Zielen

*Wir haben uns über Beibehaltung, Nutzung unterhalten. Aber wozu?*

**Die Erstellung einer Mobile Engagement-Strategie erfordert zunächst die Betrachtung der Ziele und Leistungskennzahlen (Key Performance Indicator, KPI) Ihrer App.**

Der Start mit der Definition dieser Ziele/Leistungskennzahlen hilft Ihnen beim Festlegen Ihrer Engagement-Anwendungsfälle mit dem richtigen Prisma.

Ihre Anwendungsfälle stellen eine einfache Liste von Kampagnen dar, die Sie durchführen möchten, um mit Ihren Benutzern zu kommunizieren. Diese Kampagnen reichen von einer einfachen Begrüßung bis hin zur sehr fortschrittlichen Hilfsprogrammbenachrichtigung, die von Ihrem IT-System ausgelöst wird. Ein durchdachter Anwendungsfall muss mindestens die drei Fragen *Was-Wer-Wann* umfassen:

1. Eine sehr kurze Ansprache (z. B. „Begrüßungskampagne“)
2. **Was**: Ein Nachrichtenbeispiel (z. B. „Wir freuen uns über Ihre Teilnahme! Denken Sie an die Registrierung, damit Sie den ersten Monat kostenlos nutzen können!“). Diese Nachricht ist keineswegs endgültig. Sie können sie jederzeit ändern, aber sie hilft Ihnen bei der Überlegung, was zu sagen ist.
3. **Wer**: Das Segment, das diese Nachricht erhält (z. B. „Alle Benutzer, die die App zum ersten Mal vor drei Tagen gestartet und die Anmeldeseite besucht, sich aber noch nicht angemeldet haben“).
	- Ja, das ist mit Azure Mobile Engagement sehr einfach umzusetzen :)
	- Diese Aussage muss wiederum nicht endgültig sein, da Sie Ihre Segmente jederzeit festlegen können. Es ist jedoch wichtig, dass die Segmentierungskriterien frühzeitig definiert werden, damit die Erfassung der richtigen Daten gewährleistet ist.
4. **Wann**: Der Zeitablauf Ihrer Kampagne. Hierbei kann sich um ein angegebenes Datum oder eine bestimmte Aktion handeln, die auf Grundlage eines Auslösers erfolgt. Mobile Engagement bietet eine bedeutende Anzahl von Möglichkeiten zur passenden Terminierung Ihrer Kommunikation.

Nach der Definition von Anwendungsfällen und Segmenten erhalten Sie eine Anleitung zum Definieren der Daten, die innerhalb einer Anwendung erfasst werden müssen. Dies ist die Aufgabe eines *„Markierungsplans“*. Mithilfe eines Markierungsplans können Sie sicherstellen, dass die Datensammlung für die Entwickler angegeben ist. Daher sind die Entwickler in der Lage, Mobile Engagement mit dem richtigen Setup einzubetten, damit Sie in Ihren Kampagnen mit den richtigen Daten arbeiten. Es ist außerdem sehr wichtig, dass Tests ausgeführt werden, um sicherzustellen, dass die Integration richtig abläuft und die erforderlichen Informationen erfasst.

Nach der Veröffentlichung von Anwendungen können Sie Ihre Analysen als Vermarkter auf Basis der Integration in Echtzeit anzeigen, Ihre Zielgruppe segmentieren und dann mit dem Senden intelligent ausgerichteter Pushbenachrichtigungen beginnen, um sich mit Endbenutzern innerhalb und außerhalb der App zu beschäftigen.

### Erste Anwendungsfälle
1. Begrüßungsstrategie: Erstellen Sie verschiedene Pushbenachrichtigungskampagnen, die auf dem Verhalten der Endbenutzer beim Anwendungsstart basieren, um nach der ersten Sitzung am „D+2/5/10/15“ erneut Kontakt aufzunehmen und die Beibehaltung für die erste Ausführung zu erhöhen.	
2. Preisen Sie neue Inhalte an (Feature, Artikel/Video, Produkt usw.), die auf dem Verhalten der Endbenutzer basieren, damit die Informationen nur an Benutzer gesendet werden, bei denen die Wahrscheinlichkeit der Inanspruchnahme höher ist.	
3. Bewerten Sie die Anwendung: Richten Sie diesen Vorgang auf weniger als 1 % der Benutzerbasis aus, die Ihre App im Store wahrscheinlich mit fünf Sternen bewerten wird.	
4. Ankurbeln der Abonnements: Bewerben Sie nützliche Inhalte bei Endbenutzern, denen diese bisher noch nicht bekannt sind, um die Anzahl der Abonnements zu steigern.
5. Lernprogramm: Vermeiden Sie obligatorische Lernprogramme. Erstellen Sie doch einfach großartige In-App-Lernprogramme, und lösen Sie diese durch In-App-Nachrichten aus, wenn ein Benutzer z. B. ein Feature nicht verwendet oder er Probleme bei der Verwendung hat.

## Warum benötigen Sie Analysen für die Erfassung?

Wie Sie an diesem Punkt möglicherweise erkennen, ist die reine Übertragung einer Pushbenachrichtigung nicht ausreichend. Das Hauptkonzept von Mobile Engagement besteht darin, Vermarkter und Entwickler dabei zu unterstützen, mit den richtigen Endbenutzern zum richtigen Zeitpunkt und am richtigen Ort Kontakt aufzunehmen. Mit Kenntnis dieser drei Hauptkonzepte ist es entscheidend, die Analysen von Ihrer Anwendung zu erfassen und sie dann zum Segmentieren Ihrer Zielgruppe zu verwenden. Diese Vorgehensweise ist noch wirksamer, wenn Verhaltenssegmente die Daten aus Ihrer anderen Datenbank, Ihrem CRM oder aus einer kanalübergreifenden Maßnahme ergänzen. Mobile Engagement ermöglicht das Abrufen von Daten aus beliebigen Quellen und verwendet dann diese Daten, um sie auf die richtige Zielgruppe auszurichten.

Damit Sie bei der Kontaktaufnahme zu Ihrer Zielgruppe möglichst kontextbezogen agieren, ist es ausschlaggebend, dass Sie das Verhalten der Endbenutzer kennen, d. h. deren Status in Echtzeit. Mithilfe der Datensammlung können sich die Vermarkter auf die wichtigen Faktoren beim Überprüfen von Anwendungsfällen konzentrieren und somit ihre Ziele für Ihre Mobile Engagement-Strategie erreichen. Das Erreichen der oben festgelegten Ziele ist auch der Grund dafür, dass die bewährte Methode nicht die Erfassung beliebiger Daten für die Analyse darstellt, sondern es werden nur die Daten gesammelt, die es Ihnen gestatten, sich auf die entscheidenden Informationen und Anwendungsfälle zu konzentrieren. Dies ist eine geeignete Methode, die Lösung zu starten, zu überprüfen, zu testen und zu verwenden sowie intelligente Pushbenachrichtigungen zu adressieren und die Beibehaltung einer Anwendung zu erhöhen, damit sie zu einem erfolgreichen Abschluss kommt.

>[AZURE.NOTE]Denken Sie an Folgendes: Zu viele Daten sind der Daten Tod!

### Anwendungsfälle und bewährte Methoden

Nachfolgend werden einige wichtige Anwendungsfälle kurz beschrieben, die Ihnen bei Ihren ersten Schritten helfen können.

#### Medien

Erfassen Sie die Art von Inhalten, die vom Endbenutzer konsumiert wird, und segmentieren Sie dann die Zielgruppe auf Grundlage dieses Verhaltens, um bestimmte Arten von Inhalten nur auf eine Zielgruppe auszurichten, bei der die Wahrscheinlichkeit höher ist, dass sie diese Inhalte auch konsumiert. Dadurch wird vermieden, dass eine große Benutzerbasis mit unerwünschten Informationen belästigt wird. Zudem wird eine verbesserte Beibehaltung gewährleistet.

#### M-Commerce

Erfassen Sie die innerhalb der Anwendung und der Zielgruppe am häufigsten besuchten Produktkategorien, um einen Rabatt oder ein neues Produkt in dieser Kategorie zu bewerben, bei der die Wahrscheinlichkeit für den Endbenutzer höher ist, dass dieser einen Kauf tätigt. Streben Sie höhere Umsätze an. Ziel ist es auch hier wieder, den Benutzer nicht mit unerwünschten Informationen zu belästigen!

#### Spiele

Erfassen Sie die Spielstufe eines Endbenutzers und die in einem bestimmten Zeitraum mit einem Spiel verbrachte Zeit, um die Zielgruppe zu ermitteln, die möglicherweise stockt und durch ein Bonusangebot wahrscheinlich eher zum Wechsel auf das nächste Level angeregt werden kann.

Kommunizieren Sie bestimmte Ereignisse mit einem Anreiz für die Benutzer, die einige Zeit nicht gespielt haben, um zu versuchen, diese zurückzugewinnen.

#### Einzelhandel

Ermitteln Sie, welche Produkte oder Marken von einer Zielgruppe auf Basis von Vorlieben oder dem Verhalten eher konsumiert werden, und führen Sie die Zielgruppe zum Store, um die Umsätze zu erhöhen.

#### Konten

Ermitteln Sie, ob Endbenutzer beim ersten Start der Anwendung ein Konto erstellt haben. Streben Sie die Bereitstellung einer Begrüßungsstrategie mit gezielten Pushbenachrichtigungen an, und erhöhen Sie die Anzahl von Kontoabonnements.

### Vorgehensweise zum Erstellen eines geeigneten Markierungsplans?

Ein Markierungsplan muss wie eine Beschreibung des Benutzerpfades oder eine Art von Workflow der Anwendung sein und alle erforderlichen Anzeichen (Daten) aufweisen, die erfasst werden müssen, damit ausreichend Analysen verfügbar sind, um das Benutzerverhalten zu verstehen und die Benutzerbasis entsprechend zu segmentieren. Dies ist kein technischer Prozess. Daher sind die Vermarkter in der Lage, die auf Basis Ihrer Mobile Engagement-Strategie zu erfassenden Daten anzugeben.
 
Zumindest sollten alle Bildschirme (in Mobile Engagement auch als `Activities` bezeichnet) einer Anwendung gekennzeichnet werden. Dies ermöglicht es Ihnen, den Benutzerpfad zu verstehen.

Eine Aktivität kann `Events` einbetten, die Aktionsinformationen sammeln (z. B. das Aktivieren einer Schaltfläche). Dies ermöglicht die Erfassung von Interaktionen innerhalb der Anwendung. Daher wissen die Vermarkter, welche Bildschirme die Benutzer besuchen und was sie dabei tun.

`Jobs` sind Aktionen mit einer Dauer. Es ist für Vermarkter sehr hilfreich zu verstehen, wie lange es für einen Benutzer dauert, ein Konto zu erstellen oder sich anzumelden. Dies könnte auch für Entwickler zur Überwachung nützlich sein, wie lange der Aufruf eines Webdiensts dauert.

`Errors` können ebenfalls überwacht werden, um zu erfahren, ob die Benutzer in Ihrer App auf Probleme stoßen. Beispielsweise, ob häufig Verbindungsprobleme auftreten.

Zu sämtlichen Datentypen können Parameter hinzugefügt werden (`extra-information` in Mobile Engagement), um die Erfassung dynamischer Daten von der Anwendung zu ermöglichen. Das ist wichtig, um eine differenzierte Segmentierung zu ermöglichen. Die Benutzer könnten z. B. auf Basis des Inhaltstyps segmentiert werden, den sie konsumiert haben. Der Inhaltstyp entspricht dieser dynamischen Information von `activity` oder von `event`.

`App-information` sind Daten, die in Echtzeit den Status der Anwendung oder des Benutzers vermitteln. Diese sind auch für die Kategorisierung einer Zielgruppenbasis und die schnelle Ausrichtung auf diese Basis nützlich. Es kann sich dabei z. B. um den Status „true“ oder „false“ für die Frage handeln, ob sich der Benutzer anmeldet bzw. um das Ablaufdatum seines Abonnements.

#### Beispiele für Kennzeichen

*Anwendungsfall: Segmentieren Sie das Zielverhalten, um den richtigen Endbenutzer mit den richtigen Inhalten der Pushbenachrichtigung zu erfassen*

1.	Senden Sie eine Pushbenachrichtigung, um eine Produktkategorie zu bewerben: Erfassen Sie die Verhaltensdaten, um die Zielgruppe auf Basis der Produktkategorie zu segmentieren, die x Mal in einem angegebenen Zeitraum besucht wurde, oder auf Basis eines bestimmten Artikels, der zum Warenkorb hinzugefügt wurde. Die erfassten Daten gestatten die Segmentierung und das anschließende Senden der Pushbenachrichtigung an die geeignete Zielgruppe.
2.	Bewerten der App: Erfassen Sie die Daten auf Basis der Inhalte, die von der Zielgruppe im sozialen Netzwerk geteilt werden. Ist darauf ausgerichtet, die Zielgruppe durch Ermittlung der „Repräsentanten“ der App zu segmentieren, bei denen es sich um die beste Zielgruppe für die App handelt, die mithilfe einer In-App-Pushbenachrichtigung zur Bewertung der App im Store mit fünf Sternen aufgefordert werden kann.

	![][1]

*Anwendungsfall: Deklarative Daten* 
1. Alarmmeldungen segmentieren: Erfassen Sie deklarative Daten, um die Zielgruppe auf Basis ihrer Präferenzen zu segmentieren. Es können Pushbenachrichtigungen zu einem bestimmten Thema gesendet werden, das für eine bestimmte Zielgruppe von großem Interesse ist. 
2. Segmentieren Sie die Zielgruppe auf Basis des Anmeldestatus. Erfassen Sie entsprechende Daten, um zu erfahren, ob ein Benutzer verbunden ist oder er ein Konto erstellt hat. Helfen Sie bei der Ausrichtung auf Endbenutzer, die sich noch nicht angemeldet haben, und senden Sie eine Pushbenachrichtigung, um den Benutzer zur Konvertierung zu motivieren.![][2]

### Nächste Schritte
- Weitere Informationen zu den grundlegenden Konzepten von Mobile Engagement finden Sie unter [Mobile Engagement-Konzepte].
- Weitere Informationen zur Implementierung finden Sie unter [Lernprogramme].

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Mobile Engagement-Konzepte]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Lernprogramme]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

<!--HONumber=54--> 