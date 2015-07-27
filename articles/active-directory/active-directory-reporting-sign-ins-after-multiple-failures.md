<properties
	pageTitle="Anmeldungen nach mehreren Fehlern"
	description="Ein Bericht, der die Benutzer ausweist, die sich nach mehreren aufeinander folgenden misslungenen Anmeldeversuchen erfolgreich angemeldet haben."
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="ilanas"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="kenhoff"/>

# Anmeldungen nach mehreren Fehlern

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Dieser Bericht weist die Benutzer aus, die sich nach mehreren aufeinander folgenden misslungenen Anmeldeversuchen erfolgreich angemeldet haben. Mögliche Ursachen: <ul><li>Benutzer hat sein Kennwort vergessen</li><li>Benutzer ist Opfer eines erfolgreichen Brute-Force-Angriffs zum Erraten des Kennworts</li></ul><p>Die Ergebnisse dieses Berichts enthalten die Anzahl aufeinander folgender misslungener Anmeldeversuche vor der erfolgreichen Anmeldung und den Zeitstempel der ersten erfolgreichen Anmeldung</p><p><b>Berichtseinstellungen</b>: Sie können die minimale Anzahl aufeinander folgender misslungener Anmeldeversuche konfigurieren, die auftreten muss, bevor sie im Bericht ausgewiesen wird. Beim Vornehmen von Änderungen an dieser Einstellung ist zu beachten, dass diese Änderungen nicht für vorhandene misslungene Anmeldungen gelten, die derzeit im vorhandenen Bericht enthalten sind. Sie gelten jedoch für alle künftigen Anmeldungen. Änderungen an diesem Bericht können nur von lizenzierten Administratoren vorgenommen werden. | Verzeichnis > Registerkarte "Berichte" |

![Anmeldungen nach mehreren Fehlern](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

<!---HONumber=July15_HO3-->