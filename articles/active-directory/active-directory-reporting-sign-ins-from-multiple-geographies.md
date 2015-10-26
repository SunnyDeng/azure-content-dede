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
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Anmeldungen aus mehreren geografischen Regionen
<p>Dieser Bericht enthält erfolgreiche Anmeldungen eines Benutzers, wobei zwei Anmeldungen aus verschiedenen Regionen zu stammen scheinen und die Zeit zwischen den Anmeldungen so kurz ist, dass der Benutzer die Entfernung zwischen diesen Regionen nicht zurückgelegt haben kann. Mögliche Ursachen:</p><ul><li>Der Benutzer nutzt das Kennwort gemeinsam mit anderen Benutzern</li><li>Der Benutzer verwendet einen Remotedesktop, um einen Webbrowser für die Anmeldung zu starten</li><li>Ein Hacker hat sich aus einem anderen Land mit dem Konto eines Benutzers angemeldet</li><li>Der Benutzer verwendet ein VPN oder einen Proxy</li><li>Der Benutzer hat sich gleichzeitig über mehrere Geräte wie einen Desktop und ein Mobiltelefon angemeldet, und die IP-Adresse des Mobiltelefons ist ungewöhnlich.</li></ul><p>Die Ergebnisse in diesem Bericht enthalten die erfolgreichen Anmeldeereignisse sowie die Zeit zwischen den Anmeldungen, die Regionen, aus denen die Anmeldungen zu stammen scheinen, und die geschätzte Reisezeit zwischen diesen Regionen.</p><p>Die gezeigte Reisezeit ist lediglich eine Schätzung und kann von der tatsächlichen Reisezeit zwischen den Standorten abweichen.</p>


![Anmeldungen aus mehreren geografischen Regionen](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=Oct15_HO3-->