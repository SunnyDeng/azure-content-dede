<properties
	pageTitle="Azure Notification Hubs"
	description="Erfahren Sie mehr über das Verwenden von Pushbenachrichtigungen in Azure. Die Codebeispiele wurden in C# mithilfe der .NET-API geschrieben."
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/24/2015"
	ms.author="wesmc"/>


#Azure Notification Hubs

##Übersicht

Azure Notification Hubs bieten eine einfach zu bedienende, plattformübergreifende und skalierte Pushinfrastruktur, über die Sie mobile Pushbenachrichtigungen von beliebigen Back-Ends (in der Cloud oder lokal) an beliebige mobile Plattformen senden können.

Mit Notification Hubs können Sie problemlos plattformübergreifende, personalisierte Pushbenachrichtigungen senden und dabei die Details der verschiedenen Plattformbenachrichtigungssysteme (PNS) berücksichtigen. Mit einem einzigen API-Aufruf können Sie einzelne Benutzer oder vollständige Zielgruppensegmente mit Millionen von Benutzern auf allen Geräten erreichen.

Sie können Notification Hubs für Unternehmens-und Verbraucherszenarien verwenden. Beispiel:

- Senden Sie Benachrichtigungen zu aktuellen Nachrichten mit geringer Latenz an Millionen von Benutzern (Notification Hubs unterstützen Bing-Anwendungen, die auf allen Windows- und Windows Phone-Geräten vorinstalliert sind).
- Senden Sie standortbasierte Gutscheine an Benutzersegmente.
- Senden Sie Ereignisbenachrichtigungen an Benutzer oder Gruppen für Sport-/Finanz-/Spieleanwendungen.
- Benachrichtigen Sie die Benutzer über Unternehmensereignisse wie neue Nachrichten/E-Mails und Vertriebsleads.
- Senden Sie einmal zu verwendende Kennwörter für die Multi-Factor Authentication.



##Was sind Pushbenachrichtigungen?

Smartphones und Tablets können ihre Benutzer "benachrichtigen", wenn ein bestimmtes Ergebnis eingetreten ist. Diese Benachrichtigungen können viele Formen aufweisen.

In Windows Store- und Windows Phone-Anwendungen kann die Benachrichtigung die Form eines _Popups_ haben: Ein Fenster ohne Modus wird mit einem Ton angezeigt, um auf eine neue Benachrichtigung hinzuweisen. Andere Benachrichtigungstypen werden unterstützt, einschließlich _Kachel-_, _unformatierten_ und _Signal_-Benachrichtigungen. Weitere Informationen zu den Benachrichtigungstypen, die auf Windows-Geräten unterstützt werden, finden Sie unter [Kacheln, Signale und Benachrichtigungen](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

Auf Apple iOS-Geräten benachrichtigt der Push den Benutzer in ähnlicher Weise mit einem Dialogfeld, in dem der Benutzer aufgefordert wird, die Benachrichtigung anzuzeigen oder zu schließen. Durch Klicken auf **Ansicht** wird die Anwendung angezeigt, die die Meldung erhalten hat. Weitere Informationen zu iOS-Benachrichtigungen finden Sie unter [iOS Notifications](http://go.microsoft.com/fwlink/?LinkId=615245) (in englischer Sprache).

Pushbenachrichtigungen dienen zur stromsparenden Anzeige aktueller Informationen auf Mobilgeräten. Benachrichtigungen können von Back-End-Systemen an mobile Geräte gesendet werden, auch wenn die entsprechenden Apps auf einem Gerät nicht aktiv sind. Pushbenachrichtigungen sind eine integrale Komponente für Privatanwender-Apps, in denen sie verwendet werden, um die Einbindung und Nutzung von Apps zu erhöhen. Benachrichtigungen sind auch für Unternehmen nützlich, um die Mitarbeiter besser über aktuelle geschäftliche Ereignisse zu informieren.

Hier einige spezielle Beispiele für Szenarios mit mobilen Einbindungen:

1.  Aktualisierung einer Kachel in Windows 8 oder Windows Phone mit aktuellen Finanzinformationen.
2.  Benachrichtigung eines Benutzers einer Workflow-App in einem Unternehmen, dass ihm eine bestimmte Arbeitsaufgabe zugewiesen wurde, mit einem Toast.
3.  Anzeige eines Badges mit der Anzahl aktueller Verkäufe in einer CRM-App (wie z. B. Microsoft Dynamics CRM).

##Funktionsweise von Pushbenachrichtigungen

Pushbenachrichtigungen werden über plattformspezifische Infrastrukturen ausgegeben, die _Platform Notification Systems_ (PNS) genannt werden. PNS bieten Barebone-Funktionen (d. h. keine Broadcast-Unterstützung und keine Personalisierung) und haben keine gemeinsame Schnittstelle. Wenn ein Entwickler z. B. eine Benachrichtigung an eine Windows Store-App senden möchte, muss er Kontakt mit dem WNS (Windows Notification Service) aufnehmen. Um diese Nachricht dann noch an ein iOS-Gerät zu senden, muss er Kontakt mit dem APNS (Apple Push Notification Service) aufnehmen und die Nachricht ein zweites Mal senden. Azure Notification Hubs sind nützlich, da sie eine gemeinsame Schnittstelle sowie andere Funktionen zur Unterstützung von Pushbenachrichtigungen auf jeder Plattform anbieten.

Von einer höheren Ebene betrachtet folgen alle Plattform-Benachrichtigungssysteme demselben Muster:

1.  Die Client-App nimmt Kontakt mit dem PNS auf, um einen _Handle_ abzurufen. Der Typ des Handles hängt vom System ab. Beim WNS handelt es sich um einen URI oder "Benachrichtigungskanal". Beim APNS handelt es sich um Token.
2.  Die Client-App speichert diesen Handle zur späteren Verwendung im _Back-End_ der App. Beim WNS ist das Back-End normalerweise ein Clouddienst. In Apple wird das System _Anbieter_ genannt.
3.  Um eine Pushbenachrichtigung zu senden, nimmt das Back-End der App über den Handle Kontakt mit dem PNS auf, um eine bestimmte Instanz der Client-App anzuzielen.
4.  Das PNS leitet die Benachrichtigung an das vom Handle angegebene Gerät weiter.

![][0]

##Die Herausforderungen von Pushbenachrichtigungen

Diese Systeme sind zwar sehr leistungsstark, aber der App-Entwickler hat immer noch sehr viel Arbeit, selbst wenn er nur allgemeine Pushbenachrichtigungs-Szenarios wie Broadcast-Übertragungen oder das Senden von Pushbenachrichtigungen an nach Segmenten eingeteilte Benutzer implementieren möchte.

Pushbenachrichtigungen gehören zu den am häufigsten nachgefragten Funktionen in Clouddiensten für mobile Apps. Der Grund dafür ist, dass die zu ihrer Funktion erforderliche Infrastruktur ziemlich komplex ist und mit der Hauptgeschäftslogik der App oft ziemlich wenig zu tun hat. Folgende Herausforderungen stellen sich beim Aufbau einer bedarfsgesteuerten Push-Infrastruktur:

- **Plattformabhängigkeit.** Um Benachrichtigungen an Geräte auf verschiedenen Plattformen zu senden, müssen im Back-End mehrere Schnittstellen kodiert werden. Nicht nur die kleinen Details sind verschieden, sondern die Darstellung der Benachrichtigung (als Kachel, Toast oder Badge) ist auch plattformabhängig. Die Unterschiede können zu komplexen und schwer zu verwaltenden Back-End-Codes führen.

- **Skalierung.** Bei der Skalierung dieser Infrastruktur sind zwei Aspekte zu berücksichtigen:
	+ Nach PNS-Richtlinien müssen Gerätetokens bei jedem Start einer App aktualisiert werden. Dies führt zu großen Datenverkehrsmengen (und damit vielen Datenbankzugriffen), nur um die Gerätetokens auf dem neuesten Stand zu halten. Wenn die Anzahl der Geräte wächst (möglicherweise in den Millionenbereich), sind die Kosten der Erstellung und Wartung dieser Infrastruktur beträchtlich.

	+ Die meisten PNS unterstützten keine Broadcast-Übertragung an mehrere Geräte. Daraus folgt, dass ein Broadcast an Millionen von Geräten zu Millionen von Anrufen beim PNS führt. Die Skalierung dieser Anfragen ist kein geringes Problem, da App-Entwickler normalerweise die Gesamtlatenz niedrig halten möchten. (Das heißt, das z. B. das letzte Gerät, das eine Nachricht erhält, diese Nachricht nicht 30 Minuten nach dem Absenden erhalten sollte, da dies aus mehreren Gründen dem Zweck der Pushbenachrichtigungen zuwiderläuft.)
- **Routing.** PNS ermöglichen das Senden einer Nachricht an ein Gerät. Allerdings werden Benachrichtigungen in den meisten Apps auf Benutzer und/oder Interessengruppen gezielt (z. B. alle Mitarbeiter, die einem bestimmten Kundenkonto zugeordnet sind.) Um die Benachrichtigungen an die korrekten Geräte zu senden, muss nun das Back-End der App eine Registrierung führen, die die Interessengruppen den Geräte-Tokens zuordnet. Dieser Aufwand kommt noch zur Markteinführungszeit und zu den Wartungskosten einer App hinzu.

##Warum Notification Hubs verwenden?

Notification Hubs verringern die Komplexität: Sie müssen die Herausforderungen von Pushbenachrichtigungen nicht mehr bewältigen. Stattdessen können Sie einen Notification Hub verwenden. Notification Hubs verwenden eine vollständige plattformübergreifende und skalierte Infrastruktur für Pushbenachrichtigungen und reduzieren den pushspezifischen Code, der auf dem App-Back-End ausgeführt wird, beträchtlich. Notification Hubs bieten die gesamte Funktionalität einer Pushinfrastruktur. Die Geräte sind nur für die Registrierung ihrer PNS-Handles verantwortlich, während das Back-End plattformunabhängige Nachrichten an Benutzer oder Interessengruppen sendet, wie in der folgenden Abbildung veranschaulicht.

![][1]


Notification Hubs bieten eine benutzerfreundliche Infrastruktur für Pushbenachrichtigungen mit folgenden Vorteilen:

- **Mehrere Plattformen.**
	+  Unterstützung für alle wichtigen mobilen Plattformen. Benachrichtigungshubs können Pushbenachrichtigungen an Windows Store-, iOS-, Android- und Windows Phone-Apps senden.

	+  Benachrichtigungshubs bieten eine gemeinsame Schnittstelle zum Senden von Benachrichtigungen an alle unterstützten Plattformen. Plattformspezifische Protokolle sind nicht erforderlich. Dieses App-Back-End kann Benachrichtigungen in plattformspezifischen oder plattformunabhängigen Formaten senden. Die Anwendung kommuniziert nur mit Notification Hubs.

	+  Verwaltung von Gerätehandles. Notification Hubs verwalten die Handleregistrierung und Feedback von PNSs.

- **Arbeitet mit jedem Back-End**: Cloud oder lokal, .NET, PHP, Java, Node usw.

- **Skalierung.** Benachrichtigungshubs lassen sich für Millionen von Geräten skalieren, ohne dass die Architektur umgebaut oder partitioniert werden muss.


- **Umfangreiche Bereitstellungsmuster**:

	- *Broadcast*: Ermöglicht eine fast simultane Übertragung an Millionen von Geräten mit einem einzigen API-Aufruf.

	- *Unicast/Multicast*: Push an Tags, die einzelne Benutzer darstellen, einschließlich aller Geräte; oder an eine größere Gruppe, z. B. unabhängig von Formfaktoren (Tablet oder Telefon).

	- *Segmentierung*: Push an komplexe Segmente, die durch Tagausdrücke definiert sind (z. B. Geräte in New York, die den Yankees folgen).

	Jedes Gerät kann beim Senden seines Handles an einen Notification Hub ein oder mehrere _Tags_ angeben. Weitere Informationen zu [Tags]. Tags müssen nicht vorab bereitgestellt oder entfernt werden. Tags bieten einen einfachen Weg, um Benachrichtigungen an Benutzer oder Interessengruppen zu senden. Da Tags jede beliebige App-spezifische Kennung (wie Benutzer- oder Gruppen-IDs) enthalten können, befreien Sie das App-Back-End von der Last, Geräte-Handles speichern und verwalten zu müssen.

- **Personalisierung**: Jedes Gerät kann eine oder mehrere Vorlagen aufweisen, um eine Lokalisierung und Personalisierung pro Gerät zu erreichen, ohne dass dies Auswirkungen auf den Back-End-Code hat.

- **Sicherheit**: Gemeinsamer geheimer Zugriffsschlüssel (Shared Access Secret, SAS) oder Verbundauthentifizierung.

- **Umfangreiche Telemetrie**: Verfügbar im Portal und programmgesteuert.


##Integration in Mobile App Service-Apps

Um eine nahtlose und vereinheitlichte Verwendung in Azure-Diensten zu ermöglichen, verfügt [Mobile App Service-Apps] über eine integrierte Unterstützung von Pushbenachrichtigungen mithilfe von Notification Hubs. [Mobile App Service-Apps] bietet eine hochgradig skalierbare, global verfügbare Entwicklungsplattform für mobile Anwendungen für Unternehmensentwickler und Systemintegratoren, die umfassende Funktionen für mobile Entwickler bereitstellt.

Mobile Apps-Entwickler können Notification Hubs mit dem folgenden Workflow verwenden:

1. Abrufen des PNS-Gerätehandle
2. Registrieren des Geräts und der [Vorlagen] mit Notification Hubs über die praktische Registrierungs-API des Mobile Apps-Client-SDK
    + Beachten Sie, dass Mobile Apps aus Sicherheitsgründen alle Tags für Registrierungen entfernt. Arbeiten Sie direkt von Ihrem Back-End aus mit Notification Hubs, um Tags Geräten zuzuordnen.
3. Senden von Benachrichtigungen von Ihrem App-Back-End mit Notification Hubs

Hier einige Vorteile, die sich aus dieser Integration für Entwickler ergeben: - **Mobile Apps-Client-SDKs.** Diese plattformübergreifenden SDKs bieten einfache APIs für die Registrierung und die Kommunikation mit dem Notification Hub, der automatisch mit der mobilen App verknüpft wird. Entwickler müssen keine Anmeldeinformationen für Notification Hubs durchsuchen und keinen zusätzlichen Dienst verwenden. + Die SDKs markieren das angegebene Gerät automatisch mit der für Mobile Apps authentifizierten Benutzer-ID, um den Push an das Benutzerszenario zu ermöglichen. + Die SDKs verwenden die Installations-ID von Mobile Apps automatisch als GUID für die Registrierung bei Notification Hubs, sodass Entwickler nicht mehrere Dienst-GUIDs verwalten müssen. - **Installationsmodell.** Mobile Apps verwendet das neueste Notification Hubs-Pushmodell zur Darstellung aller einem Gerät zugeordneten Pusheigenschaften in einer JSON-Installation, das auf Push Notification Services ausgerichtet ist und sich einfach verwenden lässt. - **Flexibilität.** Entwickler können auch bei eingerichteter Integration immer mit Notification Hubs direkt arbeiten. - **Integration im [Azure-Portal].** Push als Funktion ist in Mobile Apps visuell dargestellt, sodass Entwickler problemlos mit dem zugeordneten Notification Hub über Mobile Apps arbeiten können.



##Nächste Schritte

In den folgenden Themen finden Sie weitere Informationen zu Notification Hubs:

+ **[Wie Kunden Notification Hubs verwenden]**

+ **[Lernprogramme und Anleitungen zu Notification Hubs]**

+ **Erste-Schritte-Lernprogramme zu Notification Hubs** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

Die relevanten .NET-API-Referenzen für Pushbenachrichtigungen befinden sich hier:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Wie Kunden Notification Hubs verwenden]: http://azure.microsoft.com/services/notification-hubs
  [Lernprogramme und Anleitungen zu Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [Mobile App Service-Apps]: https://azure.microsoft.com/de-DE/documentation/articles/app-service-mobile-value-prop/
  [Vorlagen]: https://msdn.microsoft.com/de-DE/library/azure/dn530748.aspx
  [Azure-Portal]: https://portal.azure.com
  [tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

<!---HONumber=AcomDC_1125_2015-->