<properties
	pageTitle="Anmeldungen aus unbekannten Quellen"
	description="Ein Bericht, der auf Benutzer hinweist, die sich von einer anonymen Proxy-IP-Adresse aus erfolgreich bei Ihrem Verzeichnis angemeldet haben."
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

# Anmeldungen aus unbekannten Quellen

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| <p>Dieser Bericht enthält die Benutzer, die sich erfolgreich bei Ihrem Verzeichnis angemeldet haben, während Ihnen eine Client-IP-Adresse zugewiesen war, die von Microsoft als IP-Adresse eines anonymen Proxys erkannt wurde. Diese Proxys werden häufig von Benutzern verwendet, die die IP-Adresse ihres Computers verbergen möchten, und können in böswilliger Absicht genutzt werden, weshalb Hacker mitunter mit diesen Proxys arbeiten. </p><p> Die Ergebnisse in diesem Bericht geben an, wie oft sich ein Benutzer bei Ihrem Verzeichnis von dieser Adresse aus angemeldet hat, und weisen die IP-Adresse des Proxys aus.</p> | Verzeichnis > Registerkarte "Berichte" |

![Anmeldungen aus unbekannten Quellen](./media/active-directory-reporting-sign-ins-from-unknown-sources/signInsFromUnknownSources.PNG)

<!---HONumber=July15_HO4-->