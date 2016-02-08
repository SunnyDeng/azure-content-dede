<properties
   pageTitle="Azure Privileged Identity Management: Verwenden des Überwachungsprotokolls"
   description="Erfahren Sie, wie Sie in der Erweiterung Azure Privileged Identity Management das Überwachungsprotokoll verwenden."
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

# Azure Privileged Identity Management: Verwenden des Überwachungsprotokolls

Im Privileged Identity Management (PIM)-Überwachungsprotokoll werden alle Benutzerzuweisungen und Aktivierungen in einem angegebenen Zeitraum angezeigt.

## Navigieren zum Überwachungsprotokoll
Sie können das Überwachungsprotokoll aufrufen, indem Sie im PIM-Dashboard auf **Überwachungsverlauf** klicken.

## Der Graph des Überwachungsprotokolls
Sie können mithilfe des Überwachungsprotokolls die Gesamtzahl der Aktivierungen, die maximale Anzahl der Aktivierungen pro Tag und die durchschnittliche Anzahl der Aktivierungen pro Tag in einem Liniendiagramm anzeigen. Sie können auch die Daten nach Rolle filtern, wenn der Überwachungsverlauf mehrere Rollen enthält.

Mit der Schaltfläche **Zeit**, **Aktion** oder **Rolle** können Sie nach Zeit, Aktion bzw. Rolle sortieren.

## Die Überwachungsprotokollliste
Die Überwachungsprotokollliste enthält die folgenden Spalten:

- **Anforderer** – die Person, von der die Rollenaktivierung angefordert wurde.
- **Benutzer** – der Benutzer der Rollenaktivierung.
- **Rolle** – die dem Benutzer zugewiesene Rolle.
- **Aktion** – die für die Rolle/den Benutzer ausgeführten Aktionen.
- **Zeit** – der Zeitpunkt, zu dem die Aktion erfolgt ist.
- **Argumentation** – wenn während der Aktivierung Text in das Feld "Grund" eingegeben wurde, wird er hier angezeigt.
- **Ablauf** – wenn das Ablaufjahr 9999 ist, verfügt der Benutzer dauerhaft über die Rolle.

## Filtern des Überwachungsprotokolls

Sie können auch die im Überwachungsprotokoll angezeigten Informationen filtern, indem Sie auf die Schaltfläche **Filter** klicken. Das Blatt **Diagrammparameter aktualisieren** wird angezeigt.

### Ändern des Datumsbereichs
Sie ändern den Zeitbereich des Überwachungsprotokolls durch Klicken auf die Schaltfläche **Heute**, **Letzte Woche**, **Letzter Monat** oder **Benutzerdefiniert**. Wenn Sie auf **Benutzerdefiniert** klicken, werden die Datumsfelder **Von** und **Bis** angezeigt, um den Datumsbereich für das Protokoll anzugeben. Sie können die Datumsangaben im Format MM/TT/JJJJ eingeben oder auf das Symbol **Kalender** klicken und das Datum im Kalender auswählen.

### Ändern der Rollen im Protokoll

Aktivieren oder deaktivieren Sie das Kontrollkästchen **Rolle** neben jeder Rolle, die im Protokoll enthalten bzw. nicht enthalten sein soll.

Wenn Sie alle Filter für das Überwachungsprotokoll festgelegt haben, klicken Sie auf "Aktualisieren", um die Daten im Protokoll zu filtern. Wenn die Daten nicht sofort angezeigt werden, aktualisieren Sie die Seite.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->