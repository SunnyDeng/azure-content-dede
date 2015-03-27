<properties 
   pageTitle="Azure Mobile Engagement - Benutzeroberfläche - Reach" 
   description="Übersicht über den Abschnitt "Reach" der Benutzeroberfläche von Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - Benutzeroberfläche

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Einführung</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navigation</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Startseite</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Mein Konto</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Analyse</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Überwachen</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Reach</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segmente</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Dashboard</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Einstellungen</a>
</div>

# <a name="Reach">Reach</a>
 
Der Abschnitt "Reach" der Benutzeroberfläche ist das Verwaltungstool für Pushkampagnen, mit dem Sie Kampagnen und Merkmale von Pushbenachrichtigungen erstellen, bearbeiten, aktivieren, beenden und überwachen sowie entsprechende Statistiken abrufen können. Der Zugriff darauf ist auch über die Reach-API (und einige Elemente der untergeordneten Push-API) möglich. Vor der Verwendung von Reach-Kampagnen müssen Sie unabhängig davon, ob Sie die APIs oder Benutzeroberfläche verwenden, Azure Mobile Engagement und Reach für jede Plattform mit dem SDK in Ihre Anwendung integrieren.

**Siehe auch:**

-  [API-Dokumentation - Reach-API][Link 4], [API-Dokumentation - Push-API][Link 4], [Handbuch zur Problembehandlung - Push/Reach][Link 2]
-  <a href="#ReachCampaign" title="Reach Campaign">Reach-Kampagne</a>, <a href="#ReachCriterion" title="Reach Criterion">Reach-Kriterium</a>, <a href="#ReachContent" title="Reach Content">Reach-Inhalt</a>
 
## Vier Arten von Pushbenachrichtigungen:
1.    Ankündigungen - Ermöglicht Ihnen das Senden von Werbebotschaften an Benutzer, über die diese zu einer anderen Stelle in Ihrer App umgeleitet oder zu einer Webseite oder einem Store außerhalb Ihrer App geleitet werden. 
2.    Umfragen - Sie können Informationen zu Endbenutzern erfassen, indem Sie ihnen Fragen stellen.
3.    Datenpushes - Ermöglicht Ihnen das Senden einer Binär- oder Base64-Datendatei. Die Informationen in einem Datenpush werden an Ihre Anwendung gesendet, um die aktuelle Benutzerumgebung in Ihrer App zu ändern. Die Anwendung muss die Daten in einem Datenpush verarbeiten können.
4.    Kacheln (nur Windows Phone) - Ermöglicht Ihnen das Verwenden des Microsoft-Pushbenachrichtigungsdiensts (MPNS), um systemeigene Windows-Pushübertragungen mit XML-Daten zu senden. (Unterstützt ab der SDK-Version 0.9.0. Die endgültige Nutzlast für Kacheln darf 32 KB nicht überschreiten.)

**Siehe auch:**

-  [Konzepte - Glossar][Link 6]

## Es werden drei Kategorien von Echtzeitstatistiken für jede Kampagne gezeigt: 
1.    Anzahl der Pushvorgänge - Die Anzahl der Pushvorgänge, die anhand der in der Kampagne angegebenen Kriterien gesendet wurden. 
2.    Geantwortet - Anzahl der Benutzer, die auf die Benachrichtigung reagiert haben, indem sie sie entweder außerhalb der App geöffnet oder innerhalb der App geschlossen haben. 
3.    Aktion ausgeführt - Die Anzahl der Benutzer, die auf den Link in der Benachrichtigung geklickt haben, um zu einer neue Stelle in der App, zu einem Store oder zu einem Webbrowser umgeleitet zu werden. 

> Hinweis: Ausführlichere Kampagnenstatistiken stehen über die Statistiken der Reach-API zur Verfügung.

**Siehe auch:**

-  [Konzepte - Glossar][Link 6][API-Dokumentation - Reach-API - Statistiken][Link 4]


## Kampagnendetails:
Sie können Kampagnen bearbeiten, klonen, löschen oder Kampagnen aktivieren, die noch nicht aktiviert wurden, indem Sie den Mauszeiger über deren Namen bewegen. Oder klicken Sie darauf, um sie zu öffnen. Sie können Kampagnen klonen, die bereits aktiviert wurden, indem Sie den Mauszeiger über deren Namen bewegen. Oder klicken Sie darauf, um sie zu öffnen. Eine einmal aktivierte Kampagne kann jedoch nicht geändert werden.
 
![Reach1][18]

## Reach-Feedback:
Sie können von der Detail- zur Statistikansicht einer geöffneten Kampagne wechseln, die bereits aktiviert wurde. Und Sie können von der einfachen zur erweiterten Ansicht der Statistiken wechseln, um (je nach Berechtigungen) detailliertere Informationen zu erhalten. Sie können auch die Reach-Feedbackinformationen aus einer vorherigen Kampagne als Zielkriterien in einer neuen Kampagne verwenden. Reach-Feedbackstatistiken können auch über die Reach-API mithilfe von "Stats" erfasst werden. Sie können auch die Zielgruppe Ihrer Pushkampagnen basierend auf früheren Kampagnen anpassen.


**Siehe auch:**

-  <a href="#ReachCampaign">Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkampagne</a>, [API-Dokumentation - Reach-API - Stats][Link 4]
![Reach2][19]

## <a name="ReachCampaign">Reach - Neue Pushkampagne:</a>
 
Sie können auf der Benutzeroberfläche im Abschnitt "Reach" eine neue Pushkampagne mit einer komplexen Formel erstellen, indem Sie alle Informationen angeben, die Sie zum Senden einer Pushbenachrichtigung benötigen. Die Optionen einer Pushkampagne variieren abhängig von den vier Kampagnentypen: Ankündigungen, Umfragen, Datenpushes und Kacheln (nur Windows Phone).

**Option gilt für:**

- Sprachen:    Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Kampagne:    Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Benachrichtigung:     Ankündigungen, Umfragen
- Inhalt:    Eindeutig für jeden Kampagnentyp
- Zielgruppe:     Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Zeitraum:     Ankündigungen, Umfragen, Kacheln
- Test:    Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
 
![Reach-Campaign1][20]

## Sprachen:
Über das Dropdownmenü "Sprachen" können Sie eine andere Version Ihres Pushvorgangs an Geräte senden, die für die Nutzung anderer Sprachen eingerichtet sind. Standardmäßig erhalten alle Geräte den gleichen Push unabhängig von der Sprache, die für sie festgelegt ist. Benutzer, deren Gerät auf eine andere Sprache festgelegt ist, erhalten die Version in der Standardsprache des Pushvorgangs. Viele der Optionen für Pushkampagnen ermöglichen Ihnen das Angeben alternativer Inhalte für jede weitere Sprache, die Sie auswählen. 
 
![Reach-Campaign2][21]

**Sprachunterschiede gelten für:**

- Sprachen:    Neben der Standardsprache können andere Sprachen ausgewählt werden.
- Kampagne:    Für alle Sprachen gleich
- Benachrichtigung:    Zusätzlich zur Standardsprache für jede Sprache eindeutig
- Inhalt:    Zusätzlich zur Standardsprache für jede Sprache eindeutig
- Zielgruppe:     Kann anhand eines gesonderten Sprachkriteriums gefiltert werden
- Zeitraum:     Für alle Sprachen gleich
- Test:    Kann nacheinander an jede Sprache gesendet werden
 
**Unterstützte Sprachen:**

- Arabisch (Ar) 
- Bulgarisch (bg) 
- Katalanisch (ca) 
- Chinesisch (zh) 
- Kroatisch (hr) 
- Tschechisch (cs) 
- Dänisch (da) 
- Niederländisch (nl) 
- Englisch (en) 
- Finnisch (fi) 
- Französisch (fr) 
- Deutsch (de) 
- Griechisch (el) 
- Hebräisch (he) 
- Hindi (hi) 
- Ungarisch (hu) 
- Indonesisch (id) 
- Italienisch (it) 
- Japanisch (ja) 
- Koreanisch (ko) 
- Lettisch (lv) 
- Litauisch (lt) 
- Malaiisch (Makrosprache) (ms) 
- Norwegisch, Bokmål (nb) 
- Polnisch (pl) 
- Portugiesisch (pt) 
- Rumänisch (ro) 
- Russisch (ru) 
- Serbisch (sr) 
- Slowakisch (sk) 
- Slowenisch (sl) 
- Spanisch (es) 
- Schwedisch (sv) 
- Tagalog (tl) 
- Thai (th) 
- Türkisch (tr) 
- Ukrainisch (uk) 
- Vietnamesisch (vi) 
 
## Kampagne:
Im Abschnitt "Kampagne" können Sie den Namen und die Kategorie der Kampagne festlegen. Sie können auch planen, den Zielgruppenabschnitt einer Pushkampagne zu ignorieren und stattdessen diese Kampagne über die Reach-API (und einige Elemente der untergeordneten Push-API) zu senden. Kategorien können mit einer benutzerdefinierten Benachrichtigungsvorlage verwendet werden, um App-interne Benachrichtigungen basierend auf vordefinierten Einstellungen zu steuern. Über die Reach-API können Sie eine Liste Ihrer vorhandenen "Kategorien" abrufen.

> Warnung: Wenn Sie im Abschnitt "Kampagne" einer Reach-Kampagne die Option "Zielgruppe ignorieren, Push wird über die API an Benutzer gesendet" verwenden, wird die Kampagne NICHT automatisch gesendet. Sie müssen die Kampagne manuell über die Reach-API senden.
 
![Reach-Campaign3][22]
 
**Option gilt für:**

- Name:    Alle
- Kategorie:    Ankündigungen, Umfragen
- Zielgruppe ignorieren, Push wird Benutzern über die API gesendet    Alle
 
## Benachrichtigung:
Im Abschnitt "Benachrichtigung" können Sie grundlegende Einstellungen für Ihren Pushvorgang festlegen, so z. B.: Den Titel des Pushvorgangs, die Nachricht, ein App-internes Bild der App oder ob sie abweisbar ist. Viele Benachrichtigungseinstellungen sind spezifisch für die Plattform Ihres Geräts. Sie können auswählen, ob Ihr Pushvorgang "App-intern" oder "App-extern" oder in beiden Formen gesendet wird. (Beachten Sie, dass Benutzer auf der Betriebssystemebene ihrer Geräte Pushvorgänge "abonnieren" bzw. diese "Abonnements kündigen" können. Azure Mobile Engagement hat keine Möglichkeit, diese Einstellung außer Kraft zu setzen. Beachten Sie außerdem, dass die Reach-API Pushvorgänge "App-intern" und "App-extern" verarbeitet. Die Push-API kann verwendet werden, um auch App-externe Pushvorgänge zu verarbeiten.) Pushvorgänge können mit Bildern oder HTML-Inhalt angepasst werden, einschließlich Deep Links zum Verknüpfen mit Elementen außerhalb Ihrer App oder mit einer anderen Stelle in Ihrer App (Intent-Kategorien von Android-SDK 2.1.0 oder höher erforderlich). Sie können das Symbol oder iOS-Badge ändern und entweder Text oder Webinhalte (ein Popup mit HTML-Inhalt, URL-Link zu einer anderen Stelle innerhalb oder außerhalb der App) senden. Sie können Android-Geräte auch bei der Pushbenachrichtigung klingeln oder vibrieren lassen. (Denken Sie daran, dass Sie die ordnungsgemäßen SDK-Berechtigungen in Ihrer Android-Manifestdatei benötigen, damit ein Gerät klingelt oder vibriert.) Derzeit gibt es keinen Standard für Android-Bilder in Übergrößen, da die Bildschirmgrößen auf jedem Gerät anders sind, aber 400x100-Bilder funktionieren bei nahezu jeder Bildschirmgröße.

## Übermittlungstypen:
-    Nur App-extern: die Benachrichtigung wird übermittelt, wenn der Benutzer die Anwendung nicht verwendet.
- Die ausschließlich App-externe Benachrichtigung erfordert ein Zertifikat von Apple oder Google (APNS- oder GCM-Zertifikat).
- Nur App-intern: Die Benachrichtigung wird nur angezeigt, wenn die Anwendung ausgeführt wird.
- Die Benachrichtigung verwendet das Capptain-Übermittlungssystem, um den Benutzer zu erreichen. Sie können Layout und Anzeige Ihres Pushvorgangs vollständig visuell anpassen.
- Jederzeit: Diese Option stellt sicher, dass Sie eine Benachrichtigung unabhängig davon senden, ob die Anwendung ausgeführt wird oder nicht.

 
![Reach-Campaign4][23]

**Option gilt für:**

- Benachrichtigung:     Ankündigungen, Umfragen
 
## Inhalt:
Im Abschnitt "Inhalt" können Sie den Inhalt Ihrer Ankündigungen, Umfragen, Datenpushes und Kacheln (nur Windows Phone) ändern. Die Einstellung "Inhalt" von Pushkampagnen ist spezifisch für den Typ der Kampagne. 

**Siehe auch:**

- <a href="#ReachContent">Dokumentation zur Benutzeroberfläche - Reach - Pushübertragung von Inhalten</a>
 
![Reach-Campaign5][24]

## Zielgruppe:
Im Abschnitt "Zielgruppe" können Sie eine Standardliste von Elementen zum Beschränken Ihrer Kampagne definieren. Sie können die Kampagne auch basierend auf angepassten Kriterien beschränken. Der Standardsatz von Optionen zum Einschränken der Zielgruppe ermöglicht Ihnen einen Push an entweder alte oder neue Benutzer oder nur an systemeigene Pushbenutzer. Sie können auch ein Kontingent festlegen, um die Anzahl der Benutzer zu beschränken, die den Push empfangen. Sie können den Ausdruck bearbeiten, wie Ihre Kampagne gefiltert wird, um mindestens ein Kriterium für Zielbenutzer einzuschließen. Sie können einen Ausdruck für die Zielgruppe manuell eingeben. Ein solcher Ausdruck muss explizit das Verhältnis zwischen Kriterien definieren. Ein Kriterium ist ein Bezeichner, der mit einem Großbuchstaben beginnen muss und keine Leerzeichen enthalten darf. Die Beziehung zwischen den Kriterien kann mit den Operatoren  'und', 'oder', 'nicht' und als '(',')' beschrieben werden. Beispiel: "Kriterium1 oder (Kriterium1 und nicht Kriterium2)".

> Hinweis: Bei einer großen Zielgruppe für Kampagnen kann die Zielsuche auf Serverseite langsam sein, insbesondere dann, wenn Sie versuchen, mehrere Kampagnen gleichzeitig zu starten.

- Starten Sie, sofern möglich, immer nur eine Kampagne gleichzeitig.
- Maximal sollten nicht mehr als vier Kampagnen gleichzeitig gestartet werden.
- Übertragen Sie den Push nur an Ihre aktiven Benutzer (Kontrollkästchen "Nur Benutzer erfassen, die über systemeigenen Push erreicht werden können" und "Nur aktive Benutzer erfassen"), damit nur die Benutzer, die die App noch installiert haben und nutzen, durchsucht werden müssen.
Nachdem die Zielgruppe definiert wurde, können Sie auf die Schaltfläche "Simulieren" klicken, um herauszufinden, wie viele Benutzer diesen Push empfangen. Dadurch wird die Anzahl bekannter Benutzer berechnet, die potenziell zu dieser Zielgruppe gehören (hierbei handelt es sich um eine Schätzung, die auf einer Zufallsstichprobe von Benutzern basiert). Denken Sie daran, dass Benutzer, die die Anwendung deinstalliert haben, ebenfalls Teil dieser Zielgruppe sind, aber nicht erreicht werden können.

**Siehe auch:**

- <a href="#ReachCriterion">Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkriterien</a>

![Reach-Campaign6][25]

## Bearbeitungsausdruck
 
![Reach-Campaign7][26]
 
**Die Option "Zielgruppe begrenzen" gilt für:**

- Nur eine Teilmenge der Benutzer erfassen:    Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Nur vorhandene Benutzer erfassen:    Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Nur neue Benutzer erfassen:    Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Nur Benutzer im Leerlauf erfassen:    Ankündigungen, Umfragen, Kacheln
- Nur aktive Benutzer erfassen:    Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Nur Benutzern mit aktiviertem systemeigenen Push erfassen:     Ankündigungen, Umfragen
 
## Zeitraum:
Sie können im Abschnitt "Zeitraum" festlegen, wann der Push gesendet werden soll. Oder Sie können den Zeitraum leer lassen, um die Kampagne sofort zu starten. Denken Sie daran, dass bei Verwenden der Zeitzone der Endbenutzer die Kampagne für Ihre Endbenutzer in Asien einen Tag früher als erwartet starten kann. Senden Sie nacheinander kleine Batches von Pushdaten, bis alle Zeitzonen in der Welt mit dem Zeitraum übereinstimmen, den Sie für die Kampagne festgelegt haben. Das Verwenden der Zeitzone der Endbenutzer kann auch Verzögerungen bei Kampagnen verursachen, da vor dem Starten des Pushvorgangs die Uhrzeit vom Telefon abgerufen werden muss.

> Hinweis: Kampagnen ohne Enddatum können Pushes lokal zwischenspeichern und sie weiter anzeigen, nachdem Sie Kampagnen manuell abgeschlossen haben. Um dieses Verhalten zu vermeiden, geben Sie eine spezifische Endzeit für Kampagnen an.

**Siehe auch:**

- [Vorgehensweisen - Zeitplanung][Link 3] 
 
![Reach-Campaign8][27]

**Einstellungen gelten für:**

- Zeitraum:     Ankündigungen, Umfragen, Kacheln
 
## Test:
Sie können im Abschnitt "Test" diesen Pushvorgang an Ihr eigenes Testgerät senden, bevor Sie die Kampagne speichern. Wenn Sie bestimmte Sprachen für diese Kampagne konfiguriert haben, können Sie den Push in der jeweiligen Sprache testen. Sie können ein Testgerät in "Mein Konto" einrichten.
> Hinweis: Es werden keine serverseitigen Daten protokolliert, wenn Sie die Schaltfläche zum Testen von Pushvorgängen verwenden. Daten werden nur für reale Pushkampagnen protokolliert.

**Siehe auch:**

- [Dokumentation zur Benutzeroberfläche- Mein Konto][Link 14]
 
![Reach-Campaign9][28]

## <a name="ReachCriterion">Reach - Neues Pushkriterium (für Zielgruppenadressierung)</a>

Die Zielgruppenadressierung anhand spezifischer Kriterien mithilfe der neuen Schaltfläche "Neue Kriterien" ist eines der leistungsstärksten Konzepte in Azure Mobile Engagement. Denn Sie können relevante Pushbenachrichtigungen senden, auf die die Kunden reagieren, anstatt Empfänger mit Spam zu belästigen. Sie können Ihre Zielgruppe basierend auf Standardkriterien einschränken und Pushvorgänge simulieren, um zu bestimmen, wie viele Personen die Benachrichtigung erhalten sollen.

**Siehe auch:**

- <a href="#ReachCampaign">Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkampagne</a>

## Die Kriterien für die Zielgruppe können Folgendes umfassen:

- **Technische Informationen: ** Sie können die Zielgruppe basierend auf den gleichen technischen Informationen bestimmen, die Sie in den Bereichen "Analyse" und "Überwachen" sehen können. **Siehe auch:** [Dokumentation zur Benutzeroberfläche - Analyse][Link 15],  [Dokumentation zur Benutzeroberfläche - Überwachen][Link 16]
- **Ort:** Anwendungen, die "Real time location reporting" mit Geofencing nutzen, können die Geolocation als Kriterium der Zielgruppenadressierung anhand eines GPS-Standorts verwenden. Der Aufruf von "Lazy Area Location Reporting" kann auch verwendet werden, um eine Zielgruppe anhand des Mobiltelefonstandorts zu adressieren ("Real time location reporting" und "Lazy Area Location Reporting" müssen über das SDK aktiviert werden). **Siehe auch:** [SDK-Dokumentation - iOS - Integration][Link 5], [SDK-Dokumentation - Android - Integration][Link 5]
- **Reach-Feedback:** Sie können Ihre Zielgruppe basierend auf deren Feedback auf frühere Reach-Benachrichtigungen über Ankündigungen, Umfragen und Datenpushes adressieren. Dadurch können Sie Ihre Zielgruppe nach zwei oder drei Reach-Kampagnen besser als beim ersten Mal adressieren. Sie können das Feedback auch zum Herausfiltern von Benutzern verwenden, die bereits eine Benachrichtigung mit ähnlichem Inhalt empfangen haben, indem Sie eine Kampagne so einrichten, dass Benutzer, die bereits eine vorherige Kampagne empfangen haben, von der Kampagne ausgeschlossen werden. Sie können sogar Benutzer, die in eine bestimmte Kampagne eingeschlossen sind, die noch aktiv ist, vom Empfangen neuer Pushübertragungen ausschließen. **Siehe auch:** <a href="#ReachContent">Dokumentation zur Benutzeroberfläche - Reach - Pushübertragung von Inhalten</a>
- **Nachverfolgen der Installation:** Sie können Informationen basierend darauf nachverfolgen, wo Ihre Benutzer Ihre App installiert haben. **Siehe auch: **[Dokumentation zur Benutzeroberfläche - Einstellungen][Link 20]
- **Benutzerprofil:** Sie können die Zielgruppenadressierung basierend auf den Standardbenutzerinformationen und auf den benutzerdefinierten App-Infos durchführen, die Sie erstellt haben. Dies schließt aktuell angemeldete Benutzer und Benutzer ein, die auf spezifische Fragen geantwortet haben, die Sie zur Festlegung in der App selbst verwenden, statt nur Antworten aus vorherigen Kampagnen zu nutzen. Alle für Ihre App definierten App-Infos werden in dieser Liste angezeigt.
- Segmente: Sie können auch die Zielgruppe basierend auf Segmenten adressieren, die Sie auf Grundlage bestimmter Benutzerverhalten, die mehrere Kriterien umfassen, erstellt haben. Alle Segmente, die Sie für die App definiert haben, werden in dieser Liste angezeigt. **Siehe auch:** [Dokumentation zur Benutzeroberfläche - Segmente][Link 18]
- **App-Info:** Benutzerdefinierte App-Infotags können im Abschnitt "Einstellungen" zum Nachverfolgen des Benutzerverhaltens erstellt werden kann. **Siehe auch: **[Dokumentation zur Benutzeroberfläche - Einstellungen][Link 20]
## Beispiel: 
Wenn Sie eine Ankündigung per Push nur an die Teilmenge der Benutzer verteilen möchten, die eine App-interne Kaufaktion ausgeführt haben.

1. Wechseln Sie zur Seite mit den Anwendungseinstellungen, und wählen Sie im Menü "App-Info" den Eintrag "Neue App-Info" aus.
2. Registrieren Sie eine neue boolesche App-Info namens "InAppPurchase".
3. Lassen Sie in Ihrer Anwendung diese App-Info auf "true" festlegen, wenn der Benutzer einen App-internen Kaufvorgang erfolgreich abschließt (mithilfe der Funktion "sendAppInfo("inAppPurchase", ...)".
4. Wenn Sie das nicht in Ihrer Anwendung vornehmen möchten, können Sie dies in Ihrem Back-End mithilfe der Device-API tun.
5. Sie müssen dann lediglich Ihre Ankündigung mit einem Kriterium erstellen, das Ihre Zielgruppe auf Benutzer begrenzt, bei denen "inAppPurchase" auf "true" festgelegt ist.
 
> Hinweis: Für eine Zielgruppenadressierung, die auf anderen Kriterien als App-Infotags basiert, muss Azure Mobile Engagement Informationen von den Geräten Ihrer Benutzer erfassen, ehe der Push gesendet wird, was eine Verzögerung verursachen kann. Komplexe Pushkonfigurationsoptionen (wie die Aktualisierung von Badges) können auch Pushvorgänge verzögern. Eine "einmalige" Kampagne mithilfe der Push-API ist die absolut schnellste Pushmethode in Azure Mobile Engagement. Das ausschließliche Verwenden von App-Infotags als Pushkriterien für eine Reach-Kampagne (entweder über die Reach-API oder die Benutzeroberfläche) ist die zweitschnellste Methode, da App-Infotags auf Serverseite gespeichert werden. Das Verwenden anderer Kriterien für die Zielgruppenadressierung für eine Pushkampagne ist die flexibelste, aber langsamste Pushmethode, da Azure Mobile Engagement die Geräte abfragen muss, um die Kampagne senden zu können.
 
![Reach-Criterion1][29] 

**Kriteriumoptionen gelten für:**

- **Technische Informationen**     
- Name der Firmware:    Name der Firmware
- Firmwareversion:    Firmwareversion
- Gerätemodell:    Gerätemodell
- Gerätehersteller:    Gerätehersteller
- Anwendungsversion:    Anwendungsversion
- Name des Netzbetreibers:    Name des Netzbetreibers, nicht definiert
- Land des Netzbetreibers:    Land des Netzbetreibers, nicht definiert
- Netzwerktyp:    Netzwerktyp
- Gebietsschema:    Gebietsschema
- Bildschirmgröße:    Bildschirmgröße
- **Standort**      
- Letzter bekannter Bereich:    Land, Region, Ort
- Geofencing in Echtzeit:    Liste der interessanten Orte (POIs) (Name, Aktionen), kreisförmiger POI (Name, Breite, Länge, Radius in Metern)
- **Reach-Feedback**     
- Ankündigungsfeedback:    Ankündigung, Feedback
- Umfragenfeedback:    Umfrage, Feedback
- Antwortfeedback der Umfrage:    Antwortfeedback der Umfrage, Frage, Auswahl
- Datenpush-Feedback:    Datenpush, Feedback
- **Nachverfolgung der Installation**     
- Store:    Store, nicht definiert
- Quelle:    Quelle, nicht definiert
- **Benutzerprofil**
- Geschlecht:    Männlich oder weiblich, nicht definiert.
- Geburtsdatum:    Operator, Datum, nicht definiert
- Abonnieren:    True oder false, nicht definiert
- **App-Info**      
- Zeichenfolge:    Zeichenfolge, nicht definiert
- Datum:    Operator, Datum, nicht definiert
- Ganze Zahl:    Operator, Nummer, nicht definiert
- Boolescher Wert:    True oder false, nicht definiert
- **Segment**    
- Name von Segmenten (in Dropdownliste), Ausschluss (Zielbenutzer, die nicht Teil dieses Segments sind).

## <a name="ReachContent">Reach - Pushübertragung von Inhalten (für jeden Kampagnentyp)</a>
 
Im Abschnitt "Inhalt" einer neuen Reach-Kampagne können Sie den Inhalt Ihrer Ankündigungen, Umfragen, Datenpushes und Kacheln (nur Windows Phone) ändern. Die Einstellung "Inhalt" von Pushkampagnen ist spezifisch für den Typ der Kampagne. 
 
## Inhaltstypen
- Ankündigungen
- Umfragen
- Datenpush
- Kacheln (nur Windows Phone)
 
## Inhalt von Ankündigungen
 
 ![Reach-Content1][30] 

## Wählen Sie den Typ der Ankündigung:
-    Nur Benachrichtigung: Dies ist eine einfache Standardbenachrichtigung. Das bedeutet, dass, wenn ein Benutzer darauf klickt, keine zusätzliche Ansicht angezeigt wird, sondern nur die zugeordnete Aktion erfolgt.
-    Textankündigung: Dies ist eine Benachrichtigung, die den Benutzer auffordert, einen Blick auf eine Textansicht zu werfen.
-    Webankündigung: Dies ist eine Benachrichtigung, die den Benutzer auffordert, einen Blick auf eine Webansicht zu werfen.

**Siehe auch:**

- [Anleitungen - Ankündigungen][Link 3] 

**Informationen zu Ankündigungen von Webansichten:**

Vorkommen des Musters "{deviceid}" im HTML- oder JavaScript-Code, den Sie hier bereitstellen, werden automatisch durch den Bezeichner des Geräts ersetzt, das die Ankündigung anzeigt. Dies ist eine einfache Möglichkeit zum Abrufen von Azure Mobile Engagement-Gerätekennungen in einem externen Webdienst, der in Ihrem Backoffice gehostet wird.
Wenn Sie eine Vollbildwebansicht (ohne die Standardschaltflächen "Aktion" und "Beenden", die wir bereitstellen) erstellen möchten, können Sie die folgenden Funktionen aus dem JavaScript-Code Ihrer Ankündigung einer Webansicht verwenden: 

-    Durchführen der Ankündigungsaktion: ReachContent.actionContent()
-    Beenden der Ankündigung: ReachContent.exitContent()
 
## Wählen Ihrer Aktion:

**Informationen zu Aktions-URLs:**

Jede URL, die vom Betriebssystem eines Zielgeräts interpretiert werden kann, kann als Aktions-URL verwendet werden.
Dedizierte URLs, die Ihre Anwendung ggf. unterstützt (z. B. damit Benutzer zu einem bestimmten Bildschirm wechseln), können auch in einer Aktions-URL verwendet werden.
Jedes Vorkommen des Musters {deviceid} wird automatisch durch die Kennung des Geräts ersetzt, das die Aktion ausführt. Dies ist eine einfache Möglichkeit zum Abrufen von Azure Mobile Engagement-Gerätekennungen über einen externen Webdienst, der in Ihrem Backoffice gehostet wird.

- **Android- + iOS-Aktionen**
    - Öffnen einer Webseite
    - http://[Website-Domäne] 
    - Beispiel:http://www.azure.com
    - Senden einer E-Mail
    - mailto:[E-Mail-Empfänger]?subject=[Betreff]&body=[Nachricht] 
    - Beispiel: mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Senden einer SMS
    - sms:[telefonnummer] 
    - Beispiel: sms:2125551212
    - Wählen einer Telefonnummer
    - tel:[Telefonnummer] 
    - Beispiel: tel:2125551212
- **Aktionen nur für Android**
    - Herunterladen einer Anwendung aus dem Play Store
    - market://details?id=[App-Paket] 
    - Beispiel: market://details?id=com.microsoft.office.word
    - Starten einer Geolocationsuche
    - geo:0,0?q=[Suchabfrage] 
    - Beispiel: geo:0,0?q=starbucks,paris
- **Aktionen nur für iOS**
    - Herunterladen einer Anwendung aus dem App Store
    - http://itunes.apple.com/[Land]/app/[App-Nname]/id[App-ID]?mt=8 
    - Beispiel:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Windows-Aktionen
    - Öffnen einer Webseite
    - http://[Website-Domäne] 
    - Beispiel:http://www.azure.com
    - Senden einer E-Mail
    - mailto:[E-Mail-Empfänger]?subject=[Betreff]&body=[Nachricht] 
    - Beispiel: mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Senden einer SMS (Skype Store App erforderlich)
    - sms:[telefonnummer] 
    - Beispiel: sms:2125551212
    - Wählen einer Telefonnummer (Skype Store App erforderlich)
    - tel:[Telefonnummer] 
    - Beispiel: tel:2125551212
    - Herunterladen einer Anwendung aus dem Play Store
    - ms-windows-store:PDP?PFN=[app package ID] 
    - Beispiel: ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Starten einer Bingmaps-Suche
    - bingmaps:?q=[Suchabfrage] 
    - Beispiel: bingmaps:?q=starbucks,paris
    - Verwenden eines benutzerdefinierten Schemas
    - [Benutzerdefiniertes Schema]://[Parameter für benutzerdefiniertes Schema] 
    - Beispiel: MeinBenutzerdefiniertesProtokoll://MeineBenutzerdefiniertenParameter
    - Verwenden von Paketdaten (Store App für das Lesen von Erweiterungen erforderlich)
    - [][Ordnerdaten].[Erweiterung] 
    - Beispiel: MeineOrdnerDaten.txt
 
## Erstellen einer Nachverfolgungs-URL:
-    Im Abschnitt "Einstellungen" der Dokumentation zur Benutzeroberfläche finden Sie Anweisungen zum Erstellen einer Nachverfolgungs-URL, mit deren Hilfe Benutzer eine Ihrer anderen Anwendungen herunterladen können.
 
## Festlegen der Texte Ihrer Ankündigung
Füllen Sie den Titel, Inhalt und die Schaltflächentexte Ihrer Ankündigung aus. 
Sie können eine Zielgruppe einer künftigen Kampagne basierend auf dem Reach-Feedback dazu adressieren, wie Benutzer auf diese Kampagne geantwortet haben. Die Zielgruppenadressierung kann basieren auf dem Feedback, dass diese Kampagne einfach nur per Push übertragen, beantwortet, umgesetzt oder beendet wurde.

**Siehe auch:**
- <a href="#ReachCriterion">Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkriterien</a>

## Inhalt von Umfragen
 
![Reach-Content2][31] 
Füllen Sie Titel, Beschreibung und Schaltflächentext der Umfrage aus. 
Fügen Sie dann Optionen und Antworten zum Beantworten Ihrer Fragen hinzu.
Sie können eine Zielgruppe einer künftigen Kampagne basierend auf dem Reach-Feedback dazu adressieren, wie Benutzer auf diese Kampagne geantwortet haben. Die Zielgruppenadressierung kann darauf basieren, ob diese Kampagne einfach nur per Push übertragen, beantwortet, umgesetzt oder beendet wurde. Die Zielgruppenadressierung kann auch auf Antwortfeedback auf eine Umfrage basieren, wobei die Fragen und Antwortoptionen als Kriterien verwendet werden.

**Siehe auch:**

- <a href="#ReachCriterion">Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkriterien</a>
 
## Inhalt von Datenpushes
 
![Reach-Content3][32] 

## Auswählen des Datentyps
- Text
- Binärdaten
- Base64-Daten

## Definieren des Inhalts Ihrer Daten
- Wenn Sie die Pushübertragung von Textdaten ausgewählt haben, kopieren Sie den Text, und fügen Sie in das Feld "Inhalt" ein.
- Wenn Sie die Pushübertragung von Binär- oder Base64-Daten ausgewählt haben, verwenden Sie die Schaltfläche "Datei hochladen" zum Hochladen Ihrer Datei.
- Sie können eine Zielgruppe einer künftigen Kampagne basierend auf dem Reach-Feedback dazu adressieren, wie Benutzer auf diese Kampagne geantwortet haben. Die Zielgruppenadressierung kann darauf basieren, ob diese Kampagne einfach nur per Push übertragen, beantwortet, umgesetzt oder beendet wurde.

**Siehe auch:**

- <a href="#ReachCriterion">Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkriterien</a>

## Inhalt von Kacheln (nur Windows Phone)

![Reach-Content4][33]

## Definieren des Inhalts Ihrer Kachel
Die Kachelnutzlast ist der Text, der auf der Kachel Ihrer App auf Windows Phone-Geräten angezeigt wird.
Ein Kachelpushvorgang ist die MPNS-Version (Microsoft Push Notification Service, Pushbenachrichtigungsdienst) eines systemeigenen Push für Windows Phone. Der Kachelpushtyp ist der einzige Pushtyp, der keine Antwort liefert, weshalb die Zielgruppe künftiger Kampagnen nicht auf den Ergebnissen einer Kachelpushkampagne basieren kann. 

**Siehe auch:**

- [API-Dokumentation - Reach-API - Systemeigener Push][Link 4]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
