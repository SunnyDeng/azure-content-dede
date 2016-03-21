<properties
	pageTitle="Anmeldungen nach mehreren Fehlern"
	description="Ein Bericht, der die Benutzer ausweist, die sich nach mehreren aufeinander folgenden misslungenen Anmeldeversuchen erfolgreich angemeldet haben."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# Anmeldungen nach mehreren Fehlern
Dieser Bericht weist die Benutzer aus, die sich nach mehreren aufeinander folgenden misslungenen Anmeldeversuchen erfolgreich angemeldet haben. Mögliche Ursachen:

- Der Benutzer hat sein Kennwort vergessen</li><li>Der Benutzer ist Opfer eines erfolgreichen Brute-Force-Angriffs, bei dem das Kennwort erraten wurde.

Die Ergebnisse aus diesem Bericht zeigen Ihnen die Anzahl der aufeinander folgenden fehlgeschlagenen Anmeldeversuche vor der erfolgreichen Anmeldung und einen Zeitstempel zur ersten erfolgreichen Anmeldung an.

**Berichtseinstellungen**: Sie können die minimale Anzahl der aufeinanderfolgenden fehlerhaften Anmeldeversuche, die auftreten müssen, bevor es im Bericht angezeigt werden kann, konfigurieren. Beim Vornehmen von Änderungen an dieser Einstellung ist zu beachten, dass diese Änderungen nicht für vorhandene misslungene Anmeldungen gelten, die derzeit im vorhandenen Bericht enthalten sind. Sie gelten jedoch für alle künftigen Anmeldungen. Änderungen an diesem Bericht können nur von lizenzierten Administratoren vorgenommen werden.


![Anmeldungen nach mehreren Fehlern](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

<!---HONumber=AcomDC_0309_2016-->