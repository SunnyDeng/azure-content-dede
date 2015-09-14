<properties
	pageTitle="Mobile Engagement-Konzepte | Microsoft Azure"
	description="Azure Mobile Engagement-Konzepte"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/10/2015"
	ms.author="piyushjo"/>

# Azure Mobile Engagement-Konzepte

Mobile Engagement definiert einige Konzepte, die alle unterstützten Plattformen gemeinsam haben. Diese Konzepte werden in diesem Artikel kurz beschrieben.

Dieser Artikel stellt einen guten Einstieg dar, wenn Sie mit Mobile Engagement noch nicht vertraut sind. Stellen Sie außerdem sicher, dass Sie die zur verwendeten Plattform gehörige Dokumentation lesen, da sie die in diesem Artikel beschriebenen Konzepte mit weiteren Details und Beispielen sowie mit möglichen Einschränkungen ergänzt.

## Geräte und Benutzer
Mobile Engagement identifiziert Benutzer, indem für jedes Gerät ein eindeutiger Bezeichner generiert wird. Dieser Bezeichner wird Gerätebezeichner (oder`deviceid`) genannt. Er wird so generiert, dass alle auf demselben Gerät ausgeführten Anwendungen denselben Gerätebezeichner teilen.

Das bedeutet, dass Mobile Engagement davon ausgeht, dass ein Gerät genau zu einem Benutzer gehört und Benutzer und Geräte somit äquivalente Konzepte darstellen.

## Sitzungen und Aktivitäten
Eine Sitzung wird durch eine Verwendung der Anwendung durch einen Benutzer definiert, die vom Starten bis zum Beenden der Anwendung durch den Benutzer reicht.

Eine Aktivität wird durch eine angegebene Teilkomponente der Anwendung definiert, die von einem Benutzer ausgeführt wird (es handelt sich normalerweise um einen Bildschirm, aber es kann sich auch um beliebige andere Komponenten handeln, die für die Anwendung geeignet sind).

Ein Benutzer kann jeweils nur eine Aktivität zurzeit ausführen.

Eine Aktivität wird über einen Namen identifiziert (begrenzt auf 64 Zeichen) und kann optional zusätzliche Daten einbetten (begrenzt auf 1024 Bytes).

Sitzungen werden automatisch aus der Folge der Aktivitäten berechnet, die vom Benutzer ausgeführt werden. Eine Sitzung beginnt, wenn der Benutzer seine erste Aktivität startet, und endet, wenn die letzte Aktivität abgeschlossen ist. Das bedeutet, dass eine Sitzung nicht explizit gestartet und beendet werden müssen. Aktivitäten dagegen werden explizit gestartet oder beendet. Wenn keine Aktivität gemeldet wird, wird auch keine Sitzung gemeldet.

## Ereignisse
Ereignisse werden dazu verwendet, um sofortige Aktionen (z. B. gedrückte Schaltflächen oder vom Benutzer gelesene Artikel) zu melden.

Ein Ereignis kann sich auf die aktuelle Sitzung oder einen aktiven Auftrag beziehen oder ein eigenständiges Ereignis darstellen.

Ein Ereignis wird über einen Namen identifiziert (begrenzt auf 64 Zeichen) und kann optional zusätzliche Daten einbetten (begrenzt auf 1024 Bytes).

## Fehler
Fehler werden dazu verwendet, um von der Anwendung erkannte Probleme ordnungsgemäß zu melden (z. B. falsche Benutzeraktionen oder Fehler beim API-Aufruf).

Ein Fehler kann sich auf die aktuelle Sitzung oder einen aktiven Auftrag beziehen oder einen eigenständigen Fehler darstellen.

Ein Fehler wird über einen Namen identifiziert (begrenzt auf 64 Zeichen) und kann optional zusätzliche Daten einbetten (begrenzt auf 1024 Bytes).

## Auftrag
Aufträge werden dazu verwendet, um Aktionen zu melden, die eine Dauer aufweisen (z. B. die Dauer von API-Aufrufen, die Anzeigedauer von Anzeigen, die Dauer von Hintergrundaufgaben oder Benutzeraktionen).

Ein Auftrag bezieht sich nicht auf eine Sitzung, da eine Aufgabe ohne Benutzerinteraktion im Hintergrund ausgeführt werden kann.

Ein Auftrag wird über einen Namen identifiziert (begrenzt auf 64 Zeichen) und kann optional zusätzliche Daten einbetten (begrenzt auf 1024 Bytes).

## Absturz
Abstürze werden vom Mobile Engagement-SDK automatisch ausgelöst, um Anwendungsfehler zu melden, bei denen von der Anwendung nicht erkannte Probleme zum Absturz führen.

## Anwendungsinformationen
Mithilfe von Anwendungsinformationen (oder App-Informationen) werden Benutzer gekennzeichnet, d. h. den Benutzern einer Anwendung werden einige Daten zugeordnet (dies ist vergleichbar mit Webcookies, mit der Ausnahme, dass App-Informationen serverseitig auf der Azure Mobile Engagement-Plattform gespeichert werden).

App-Informationen können mithilfe der API des Mobile Engagement SDKs oder der Geräte-API der Mobile Engagement-Plattform registriert werden.

App-Informationen bestehen aus einem Schlüssel-Wert-Paar, das einem Gerät zugeordnet ist. Der Schlüssel ist der Name der Anwendungsinformation (begrenzt auf 64 ASCII-Buchstaben [a-z, A-Z], Zahlen [0-9] und Unterstriche [\_]). Der Wert (begrenzt auf 1024 Zeichen) kann eine beliebige Zeichenfolge, eine ganze Zahl, ein Datum (JJJJ-MM-TT) oder ein boolescher Wert („true“ oder „false“) sein.

Einem Gerät können innerhalb der in den Mobile Engagement-Preisinformationen definierten Grenzen beliebig viele Anwendungsinformationen zugeordnet werden. Für einen angegebenen Schlüssel wird nur der letzte festgelegte Wert von Mobile Engagement nachverfolgt (kein Verlauf). Durch das Festlegen oder Ändern des Werts einer Anwendungsinformation wird Mobile Engagement dazu gezwungen, die Zielgruppenkriterien erneut auszuwerten, die für diese Anwendungsinformation (sofern vorhanden) festgelegt sind. Das bedeutet, die Anwendungsinformationen können dazu verwendet werden, um Echtzeit-Pushübertragungen auszulösen.

## Zusätzliche Daten
Zusätzliche Daten (oder Extras) sind beliebige Daten, die Ereignissen, Fehlern, Aktivitäten und Aufträgen zugeordnet werden können.

Zusätzliche Daten sind ähnlich wie JSON-Objekte strukturiert: sie bestehen aus einer Struktur von Schlüssel-Wert-Paaren. Schlüssel sind auf 64 ASCII-Buchstaben [a-z, A-Z], Zahlen [0-9] und Unterstriche [\_] begrenzt, und die Gesamtgröße der Extras ist auf 1024 Zeichen begrenzt (nach der JSON-Codierung durch das Mobile Engagement SDK).

Die gesamte Struktur aus Schlüssel-Wert-Paaren wird als JSON-Objekt gespeichert. Trotzdem wird nur die erste Ebene der Schlüssel-Wert-Paare zerlegt, damit einige erweiterte Funktionen wie Segmente direkt darauf zugreifen können (Sie können z. B. problemlos ein Segment namens „SciFi-Fans“ definieren, das aus allen Benutzern besteht, denen im letzten Monat mindestens 10 Mal das Ereignis „content\_viewed“ mit dem zusätzlichen Schlüssel „content\_type“ und dem Wert „scifi“ gesendet wurde). Es wird daher dringend empfohlen, nur zusätzliche Daten zu senden, die aus einfachen Listen von Schlüssel-Wert-Paaren bestehen, die skalare Werte verwenden (z. B. Zeichenfolgen, Datumsangaben, ganze Zahlen oder boolesche Werte).

## Nächste Schritte

- [Übersicht über das Windows Universal SDK für Azure Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md)
- [Übersicht über das Windows Phone Silverlight SDK für Azure Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK für Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)
- [Android SDK für Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)

<!---HONumber=September15_HO1-->