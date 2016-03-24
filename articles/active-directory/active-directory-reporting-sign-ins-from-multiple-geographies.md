<properties
	pageTitle="Anmeldungen aus mehreren geografischen Regionen"
	description="Ein Bericht, der auf Benutzer hinweist, bei denen zwei Anmeldungen aus verschiedenen Regionen zu stammen scheinen und die Zeit zwischen den Anmeldungen es dem jeweiligen Benutzer unmöglich macht, die Entfernung zwischen diesen Regionen reisend zurückgelegt zu haben."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# Anmeldungen aus mehreren geografischen Regionen

Dieser Bericht enthält erfolgreiche Anmeldungen eines Benutzers, wobei zwei Anmeldungen aus verschiedenen Regionen zu stammen scheinen, und die Zeit zwischen den Anmeldungen so kurz ist, dass der Benutzer die Entfernung zwischen diesen Regionen nicht zurückgelegt haben kann. Mögliche Ursachen:

- Der Benutzer hat sein Kennwort für andere Benutzer freigegeben.

- Der Benutzer verwendet einen Remotedesktop, um einen Webbrowser für die Anmeldung zu starten.

- Ein Hacker hat sich von einem anderen Land aus in dem Konto eines Benutzers angemeldet.

- Der Benutzer verwendet eine VPN-Verbindung oder einen Proxy.

- Der Benutzer ist über mehrere Geräte gleichzeitig angemeldet, z. B. über einen Desktop und ein Mobiltelefon, und die IP-Adresse des Mobiltelefons ist ungewöhnlich.

Die Ergebnisse aus diesem Bericht zeigen Ihnen die erfolgreichen Anmeldeereignisse, die Dauer zwischen den Anmeldungen, die Regionen, aus denen die Anmeldungen scheinbar erfolgten, und die geschätzte Reisezeit zwischen diesen Regionen, an. Die angezeigte Reisezeit ist lediglich eine Schätzung und unterscheidet sich möglicherweise von der tatsächlichen Reisezeit zwischen den Orten.


![Anmeldungen aus mehreren geografischen Regionen](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=AcomDC_0309_2016-->