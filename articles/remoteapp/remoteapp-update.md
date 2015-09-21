<properties
   pageTitle="Aktualisieren Ihrer Azure RemoteApp-Sammlung | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie Ihre Azure RemoteApp-Sammlung aktualisieren."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="09/03/2015"
   ms.author="elizapo"/>

# Aktualisieren einer Sammlung in Azure RemoteApp

Irgendwann kommt der Punkt, an dem eine Aktualisierung der Apps oder des Image in Ihrer Azure RemoteApp-Sammlung erforderlich wird. Wenn Sie eines der Images aus Ihrem Azure RemoteApp-Abonnement verwenden (ganz gleich, ob in einer Cloud- oder in einer Hybridsammlung), werden sämtliche Aktualisierungen von Azure RemoteApp abgewickelt, und Sie müssen sich um nichts weiter kümmern.

Wenn Sie dagegen ein benutzerdefiniertes (von Grund auf neu erstelltes oder angepasstes) Image verwenden, müssen Sie das Image und die Apps selbst pflegen.

In diesem Artikel erfahren Sie, wie Sie Ihre Sammlung aktualisieren. Es ist ganz einfach:

1. Aktualisieren Sie das Image, das Sie in Ihrer Sammlung verwendet haben. Wenden Sie alle erforderlichen Patches oder Updates an, und speichern Sie es anschließend unter einem neuen Namen.
2. [Laden Sie das Image auf RemoteApp hoch](remoteapp-uploadimage.md), oder [importieren](remoteapp-image-on-azurevm.md) Sie es.
3. Klicken Sie auf der Sammlungsseite auf **Aktualisieren**.
4. Wählen Sie in der Liste **Vorlagenimage** das neue Image aus.
4. Nun wird es etwas kniffliger: Sie müssen entscheiden, wie mit den Benutzern verfahren werden soll, die zurzeit eine App aus der Sammlung verwenden. Folgende Optionen stehen zur Auswahl:
	- **Benutzern nach dem Update 60 Minuten geben**. Nach Abschluss der Aktualisierung werden alle aktiven Benutzer in einer Meldung aufgefordert, ihre Dateien zu speichern und sich ab- und wieder anzumelden. Nach 60 Minuten werden alle aktiven Benutzer, die sich nicht abgemeldet haben, automatisch abgemeldet. Die Benutzer können sich sofort wieder anmelden. 
	- **Benutzer sofort abmelden**. Bei dieser Option werden alle Benutzer ohne Vorwarnung automatisch abgemeldet, sobald die Aktualisierung abgeschlossen ist. Wenn Sie diese Option auswählen, gehen den Benutzern unter Umständen Daten verloren. Sie können sich aber umgehend wieder bei der App anmelden.

1. Klicken Sie auf das Häkchen, um die Aktualisierung zu starten.

 

<!---HONumber=Sept15_HO2-->