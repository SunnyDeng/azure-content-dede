<properties
   pageTitle="Azure Privileged Identity Management: Hinzufügen einer Rolle zu einem Benutzer"
   description="Erfahren Sie, wie Sie mit der Erweiterung Azure Privileged Identity Management privilegierten Identitäten Rollen hinzufügen."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management: Hinzufügen oder Entfernen einer Benutzerrolle

## Hinzufügen oder Entfernen einer Benutzerrolle
Es gibt mehrere Möglichkeiten, zum Blatt **Verwaltete Benutzer hinzufügen** der PIM-Benutzeroberfläche zu navigieren. Die Klickreihenfolge für jede Möglichkeit wird unten aufgeführt:

## Pfade zum Hinzufügen oder Entfernen der Rolle eines Benutzers
- "Dashboard" > "Benutzer in Administratorrollen" > "Hinzufügen" oder "Entfernen"
- "Dashboard" > "Rollenzusammenfassung" > "Liste sämtlicher Benutzer" > "Hinzufügen" oder "Entfernen"
- "Dashboard" > klicken Sie auf eine Benutzerrolle in der Rollentabelle (z. B. "Globaler Administrator") > "Hinzufügen" oder "Entfernen"

## Hinzufügen einer Rolle zu einem Benutzer aus dem Abschnitt "Benutzer in Administratorrollen" oder dem Abschnitt "Rollenzusammenfassung" in der "Liste sämtlicher Benutzer"
Nachdem Sie zum Blatt **Verwaltete Benutzer hinzufügen** navigiert sind ...

1. Klicken Sie auf **Rolle auswählen**. Wenn Sie an diese Stelle navigiert sind, indem Sie auf eine Benutzerrolle in der Rollentabelle geklickt haben, ist die Rolle bereits ausgewählt.
2. Wählen Sie eine Rolle aus der Rollenliste aus. Beispielsweise wird für **Kennwortadministrator** das Blatt **Benutzer auswählen** geöffnet.
3. Geben Sie im Suchfeld den Namen des Benutzers ein, dem die Rolle hinzugefügt werden soll. Wenn der Benutzer im Verzeichnis enthalten ist, werden während der Eingabe sein Konto sowie weitere Benutzer mit ähnlichen Namen angezeigt.
4. Wählen Sie den Benutzer in der Liste aus, und klicken Sie auf **Fertig**.
5. Klicken Sie zum Speichern der Auswahl auf **OK**. Wenn Sie nicht auf "OK" klicken, wird die Auswahl nicht gespeichert. Der von Ihnen ausgewählte Benutzer wird in der Liste angezeigt, und die Rolle ist temporär.
6. Wenn die Rolle permanent sein soll, klicken Sie auf den Benutzer in der Liste. Die Informationen des Benutzers werden auf einem neuen Blatt angezeigt. Wählen Sie im Menü mit den Benutzerinformationen **Als permanent festlegen** aus.
7. Klicken Sie auf **Aktivieren**, um das Aktivieren dieser Rolle für den Benutzer anzufordern. Geben Sie im Textfeld **Anforderungsgrund** den Grund für die Aktivierung ein. Jetzt wird die Rolle für diesen Benutzer automatisch aktiviert, und an globale Administratoren wird eine Benachrichtigung gesendet.

## Entfernen einer Rolle eines Benutzers
1. Navigieren Sie auf einem der oben beschriebenen Pfad zu dem Benutzer in der Benutzerrollenliste.
2. Klicken Sie auf den Benutzer in der Benutzerliste.
3. Klicken Sie auf **Entfernen**. Eine Bestätigungsmeldung wird angezeigt.
4. Klicken Sie auf "Ja", um die Rolle des Benutzers zu entfernen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->