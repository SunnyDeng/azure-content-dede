<properties
   pageTitle="Azure Privileged Identity Management: Starten einer Sicherheitsüberprüfung"
   description="Erfahren Sie, wie Sie mit der Erweiterung Azure Privileged Identity Management eine Sicherheitsüberprüfung für privilegierte Identitäten erstellen."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: Starten einer Sicherheitsüberprüfung

## Starten einer Sicherheitsüberprüfung
Letztendlich können Sie Sicherheitsüberprüfungen an anderen Stellen im Azure-Portal durchführen. Dieses Dokument beschreibt die Schritte zum Starten einer Sicherheitsüberprüfung innerhalb der PIM-Schnittstelle (**Privileged Identity Management**).

Möglicherweise gibt es Benutzer, die Sie nicht erkennen, oder vielleicht hat ein Benutzer seine Stelle oder das Projekt gewechselt und benötigt in der neuen Position keinen privilegierten Zugriff mehr. Um die Risiken im Zusammenhang mit diesen "veralteten" Rollenzuweisungen zu verringern, können Sie und andere Administratoren die den Benutzern erteilten Rollen überprüfen, indem Sie eine Sicherheitsüberprüfung starten.

## Möglichkeiten zum Starten einer Sicherheitsüberprüfung
> [AZURE.NOTE] Wenn Sie noch kein PIM-Dashboard im Azure-Portal erstellt haben, finden Sie die Schritte unter [Erste Schritte mit Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

Vom Azure-PIM-Dashboard aus können Sie eine Ansicht über einen der folgenden Pfade starten:

- Dashboard > Sicherheitüberprüfungen > Überprüfen > Schaltfläche "Überprüfen"
- Dashboard > Rollen > Schaltfläche "Überprüfen"
- Dashboard > Klicken Sie auf die Rolle, die in der Rollenliste überprüft werden soll > Schaltfläche "Überprüfen"

## Starten einer Sicherheitsüberprüfung

Klicken Sie auf die Schaltfläche **Überprüfen**. Die Blätter **Rollenüberprüfung starten** und **Eine Rolle für die Überprüfung auswählen** werden angezeigt, und das Element **Eine Rolle für die Überprüfung auswählen** ist ausgewählt.

### Auswählen der zu überprüfenden Rolle

1. Wählen Sie auf dem Blatt **Eine Rolle für die Überprüfung auswählen** in der Liste der Rollen die gewünschte Rolle aus. Sie können jeweils nur eine Rolle auswählen. Das Blatt **Eine Rolle für die Überprüfung auswählen** wird durch das Blatt **Prüfer auswählen** ersetzt. Bei der Auswahl der Prüfer stehen Ihnen zwei Optionen zur Verfügung:
  - Ich: Verwenden Sie diese Funktion, wenn Sie eine Vorschau zur Funktionsweise von Sicherheitsüberprüfungen anzeigen möchten, ohne andere Administratoren einzubeziehen.
  - Selbstüberprüfung durch Rollenmitglieder: Mit diesem Feature können Benutzer ihre eigenen Rollenzuweisungen überprüfen.
2. Wählen Sie eine der beiden Optionen aus, um die Arbeit mit den Überprüfungsdetails zu beginnen. Das Blatt **Standardeinstellungen ändern** wird angezeigt.

### Eigene Überprüfung

1. Benennen Sie die Überprüfung, indem Sie den Namen der Überprüfung im Feld **Name** eingeben. Es empfiehlt sich, der Überprüfung einen eindeutigen Namen zu geben, der die Überprüfung beschreibt und deren Verfolgung vereinfacht.
2. Geben Sie ein Startdatum für die Überprüfung im Feld **Startdatum** ein.
3. Geben Sie ein Enddatum für die Überprüfung im Feld **Enddatum** ein. Im Folgenden sind einige Aspekte aufgeführt, die Sie beim Festlegen des Enddatums für die Überprüfung beachten sollten:
  - Wie viele Personen werden überprüft?
  - Wie schnell können die Benutzer die Erweiterung hinzufügen und die Überarbeitung abschließen?
4. Klicken Sie auf dem Blatt **Standardeinstellungen ändern** auf die Schaltfläche **OK**. Das Blatt wird geschlossen.
5. Klicken Sie auf dem Blatt **Rollenüberprüfung starten** auf die Schaltfläche **OK**. Das Blatt wird geschlossen. Im Hauptmenü des Azure-Portals wird eine Benachrichtigung angezeigt. Aktualisieren Sie das Dashboard, indem Sie auf die Schaltfläche **Aktualisieren** klicken. Die Sicherheitsüberprüfung wird im Abschnitt **Sicherheitsüberprüfungen** angezeigt.
6. Benachrichtigen Sie die Personen in der Rolle, dass sie die Erweiterung hinzufügen und anschließend [ihren eigenen Administratorzugriff überprüfen](active-directory-privileged-identity-management-how-to-perform-security-review.md) sollen.  

### Überprüfung mit Option "Ich"

Wenn Sie die Option "Ich" als Prüfer ausgewählt haben, fahren Sie mit der Sicherheitsüberprüfung fort. Weitere Informationen zum Abschließen der Überprüfung finden Sie unter [Azure Privileged Identity Management: Durchführen einer Sicherheitsüberprüfung](active-directory-privileged-identity-management-how-to-perform-security-review.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Inhaltsverzeichnis zu PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->