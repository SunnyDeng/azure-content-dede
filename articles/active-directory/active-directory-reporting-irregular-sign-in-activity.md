<properties pageTitle="Irreguläre Anmeldeaktivität" description="Ein Bericht, der Anmeldungen enthält, die von unseren Algorithmen für maschinelles Lernen als anomal eingestuft wurden."" services="active-directory" documentationCenter="" authors="kenhoff" manager="ilanas" editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="kenhoff"/>

# Irreguläre Anmeldeaktivitäten

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| <p>Dieser Bericht enthält Anmeldungen, die von unseren Algorithmen für maschinelles Lernen als "anomal" identifiziert wurden. Gründe, warum ein Anmeldeversuch als irregulär gekennzeichnet wird, sind unerwartete Anmeldeorte, die Tageszeit und Orte oder eine Kombination dieser Faktoren. Dies kann ein Hinweis sein, dass ein Hacker versucht hat, sich mit diesem Konto anzumelden. Der Algorithmus für maschinelles Lernen klassifiziert Ereignisse als "anomal" oder "verdächtig", wobei "verdächtig" für eine höhere Wahrscheinlichkeit einer Sicherheitsverletzung steht.</p><p>Die Ergebnisse dieses Berichts enthalten diese Anmeldungen samt Klassifizierung, Ort und Zeitstempel jeder Anmeldung.</p><p>Eine E-Mail-Benachrichtigung wird an die globalen Administratoren gesendet, wenn wir in einem Zeitraum von maximal 30 Tagen mindestens 10 anomale Anmeldeereignisse verzeichnen. Fügen Sie aad-alerts-noreply@mail.windowsazure.com unbedingt der Liste sicherer Absender hinzu.</p> | Verzeichnis > Registerkarte "Berichte" |

<!---HONumber=August15_HO6-->