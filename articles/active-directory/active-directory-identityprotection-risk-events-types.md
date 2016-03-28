<properties
	pageTitle="Arten von Risikoereignisse, die von Azure Active Directory Identity Protection erkannt werden | Microsoft Azure"
	description="Dieses Thema bietet eine ausführliche Übersicht über die verfügbaren Typen von Risikoereignisse in Azure Active Directory Protection"
	services="active-directory"
	keywords="Azure Active Directory Identity Protection, Cloud App Discovery, Verwalten von Anwendungen, Sicherheit, Risiko, Risikostufe, Sicherheitsrisiko, Sicherheitsrichtlinie"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="markvi"/>

#Arten von Risikoereignisse, die von Azure Active Directory Identity Protection erkannt werden 

Risikoereignisse in Azure Active Directory Identity Protection sind Ereignisse, die:

- als verdächtig gekennzeichnet wurden

- andeuten, dass eine Identität möglicherweise kompromittiert wurde.

In diesem Thema erhalten Sie eine ausführliche Übersicht über die verfügbaren Arten von Risikoereignissen.


## Kompromittierte Anmeldeinformationen

Kompromittierte Anmeldeinformationen sind Anmeldeinformationen, für die von unseren Microsoft-Sicherheitsexperten herausgefunden wird, dass sie öffentlich im Dark Web gepostet werden. Diese Anmeldeinformationen sind dort normalerweise in Klartext zu finden. Sie werden mit Azure AD-Anmeldeinformationen verglichen. Wenn sich eine Übereinstimmung ergibt, werden sie in Identity Protection als „Kompromittierte Anmeldeinformationen“ gemeldet.

Risikoereignisse vom Typ „Kompromittierte Anmeldeinformationen“ werden als Risikoereignisse mit dem Schweregrad „Hoch“ eingestuft, da es sich hierbei um einen eindeutigen Hinweis darauf handelt, dass der Benutzername und das Kennwort für Angreifer zur Verfügung stehen.

## Unmöglicher Ortswechsel zu atypischen Orten

Mit dieser Art von Risikoereignis werden zwei Anmeldungen identifiziert, die von weit entfernten Orten durchgeführt wurden und bei denen mindestens einer der Orte aufgrund des bisherigen Verhaltens atypisch für den Benutzer ist. Außerdem ist der Zeitraum zwischen den beiden Anmeldungen kürzer als der Zeitraum, der für die Reise des Benutzers vom ersten zum zweiten Ort erforderlich wäre. Dies deutet darauf hin, dass ein anderer Benutzer die gleichen Anmeldeinformationen verwendet.

Bei diesem Machine Learning-Algorithmus werden offensichtliche „ *falsch positive Ergebnisse* “ ignoriert, die zum Zustand des unmöglichen Ortswechsels beitragen, z. B. VPNs und regelmäßig von anderen Benutzern der Organisation verwendete Orte. Das System verfügt über einen anfänglichen Lernzeitraum von 14 Tagen, in dem das Anmeldeverhalten des neuen Benutzers erlernt wird.

Der unmögliche Ortswechsel ist normalerweise ein zuverlässiger Hinweis darauf, dass sich ein Hacker erfolgreich anmelden konnte. Es kann aber zu falsch positiven Ergebnissen kommen, wenn ein Benutzer mit einem neuen Gerät unterwegs ist oder eine VPN-Verbindung nutzt, die von anderen Personen der Organisation normalerweise nicht verwendet wird. Eine weitere Quelle für falsch positive Ergebnisse sind Anwendungen, von denen Server-IPs fälschlicherweise als Client-IPs übergeben werden. Hierbei kann der Eindruck entstehen, dass Anmeldungen über das Rechenzentrum durchgeführt werden, in dem das Back-End der Anwendung gehostet wird (häufig sind dies Microsoft-Rechenzentren, die den Eindruck entstehen lassen, dass Anmeldungen über im Besitz von Microsoft befindliche IP-Adressen abgewickelt werden). Aufgrund dieser falsch positiven Ergebnisse ergibt sich als Risikostufe für das Risikoereignis „Mittel“.

##Anmeldungen von infizierten Geräten

Bei dieser Art von Risikoereignis werden Anmeldungen von Geräten identifiziert, die mit Schadsoftware infiziert sind und für die bekannt ist, dass sie aktiv mit einem Botserver kommunizieren. Dies wird ermittelt, indem IP-Adressen den Benutzergeräts mit IP-Adressen korreliert werden, die in Kontakt mit einem Botserver gestanden haben.

Bei diesem Risikoereignis werden IP-Adressen identifiziert, keine Benutzergeräte. Wenn sich hinter einer einzelnen IP-Adresse mehrere Geräte verbergen und nur einige davon von einem Botnetzwerk kontrolliert werden, kann es sein, dass dieses Ereignis bei Anmeldungen von anderen Geräten fälschlicherweise ausgelöst wird. Dies der Grund dafür, warum dieses Risikoereignis die Einstufung „Niedrig“ erhält.

Sie sollten den Benutzer kontaktieren und alle Geräte des Benutzers überprüfen. Es ist auch möglich, dass das persönliche Gerät eines Benutzers infiziert ist oder dass (wie bereits erwähnt) eine andere Person unter der gleichen IP-Adresse wie der Benutzer ein infiziertes Gerät verwendet hat. Infizierte Geräte sind häufig mit Schadsoftware infiziert, die von Antivirensoftware noch nicht identifiziert wurde. Dies kann auch auf schlechte Angewohnheiten des Benutzers hinweisen, die dazu geführt haben, dass das Gerät infiziert wurde.

Weitere Informationen dazu, wie Sie Infektionen durch Schadsoftware behandeln können, finden Sie im [Microsoft-Center zum Schutz vor Schadsoftware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


## Anmeldungen von anonymen IP-Adressen

Bei dieser Art von Risikoereignis werden Benutzer identifiziert, die sich erfolgreich unter einer IP-Adresse angemeldet haben, die als anonyme Proxy-IP-Adresse identifiziert wurde. Diese Proxys werden von Personen verwendet, die die IP-Adresse ihres Geräts verbergen möchten, und können in böswilliger Absicht eingesetzt werden.

Es wird empfohlen, dass Sie sich sofort an den Benutzer wenden, um zu überprüfen, ob dieser die anonyme IP-Adresse verwendet hat. Die Risikostufe für diese Art von Risikoereignis lautet „Mittel“, da eine anonyme IP-Adresse kein starker Hinweis auf eine Kompromittierung eines Kontos ist.

## Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten

Bei dieser Art von Risikoereignis werden IP-Adressen identifiziert, für die eine hohe Zahl fehlgeschlagener Anmeldeversuche beobachtet wurde, und zwar mit mehreren Benutzerkonten innerhalb eines kurzen Zeitraums. Dies entspricht Datenverkehrsmustern von IP-Adressen, die von Angreifern verwendet werden, und es ist ein starker Hinweis darauf, dass Konten entweder bereits kompromittiert sind oder dass dies kurz bevorsteht. Dies ist ein Machine Learning-Algorithmus, bei dem offensichtliche „*falsch positive Ergebnisse*“ ignoriert werden, z. B. IP-Adressen, die regelmäßig von anderen Benutzern in der Organisation verwendet werden. Das System benötigt einen anfänglichen Lernzeitraum von 14 Tagen, in dem es das Anmeldeverhalten eines neuen Benutzers und neuen Mandanten erlernt.

Es wird empfohlen, sich an den Benutzer zu wenden und zu überprüfen, ob dieser sich tatsächlich mit einer IP-Adresse angemeldet hat, die als verdächtig gekennzeichnet war. Die Risikostufe für diesen Ereignistyp lautet „Mittel“, da sich hinter einer IP-Adresse mehrere Geräte verbergen können, von denen nur einige Geräte für die verdächtige Aktivität verantwortlich sind.


## Anmeldungen von unbekannten Standorten

Bei dieser Art von Risikoereignis handelt es sich um ein Verfahren zur Echtzeitauswertung von Anmeldungen, bei dem die letzten Standorte (IP, Breiten-/Längengrad und ASN) berücksichtigt werden, um neue oder unbekannte Orte zu ermitteln. Im System werden Informationen zu den vorherigen Standorten gespeichert, die von einem Benutzer genutzt wurden, und diese werden als „vertraute“ Standorte angesehen. Das Risikoereignis wird ausgelöst, wenn die Anmeldung von einem Standort aus erfolgt, der in der Liste der vertrauten Standorte noch nicht enthalten ist. Das System benötigt einen anfänglichen Lernzeitraum von 14 Tagen, in dem neue Standorte nicht als unbekannte Orte gekennzeichnet werden. Außerdem ignoriert das System Anmeldungen von vertrauten Geräten und von Standorten aus, die geografisch nahe an einem bekannten Speicherort liegen. <br> Unbekannte Standorte können ein starker Hinweis darauf sein, dass ein Angreifer versucht, eine gestohlene Identität zu verwenden. Zu falsch positiven Ergebnissen kann es kommen, wenn ein Benutzer auf Reisen ist, ein neues Gerät ausprobiert oder eine neue VPN-Verbindung verwendet. Aufgrund dieser falsch positiven Ergebnisse ergibt sich als Risikostufe für diese Art von Ereignis „Mittel“.





## Weitere Informationen

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!----HONumber=AcomDC_0309_2016-->