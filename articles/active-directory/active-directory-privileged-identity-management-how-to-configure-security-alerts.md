<properties
   pageTitle="Konfigurieren von Sicherheitswarnungen | Microsoft Azure"
   description="Erfahren Sie, wie Sie Sicherheitswarnungen für die Erweiterung Azure Privileged Identity Management konfigurieren."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: Konfigurieren von Sicherheitswarnungen

## Übersicht über Sicherheitswarnungen
Azure Privileged Identity Management (PIM) bietet die folgenden konfigurierbaren Warnungen. Sicherheitswarnungen können im Abschnitt „Warnungen“ des PIM-Dashboards angezeigt werden.

| Warnung | Trigger |
| ------------- | ------------- |
| **Verdacht auf unzulässige permanente Aktivierung** | Ein Administrator hat seine temporäre Rolle außerhalb von PIM aktiviert. |
| **Verdächtige Aktivierungserneuerung privilegierter Rollen** | Es sind zu viele erneute Aktivierungen derselben Rolle in der in den Einstellungen festgelegten Zeit erfolgt. |
| **Verdächtige Verwendung von globalem Honeytoken-Administrator ** | Die Verwendung eines Honeypot-Benutzers wurde ermittelt.|
| **Für Rollenaktivierung ist schwache Authentifizierung konfiguriert** | Die Einstellungen enthalten Rollen ohne MFA. |
| **Redundante Administratoren vergrößern die Angriffsfläche** | Es sind temporäre Administratoren vorhanden, die ihre Rollen nicht innerhalb der in den Einstellungen angegebenen Anzahl von Tagen aktiviert haben. |
| **Eine zu hohe Anzahl von Administratoren vergrößert die Angriffsfläche** | In den Einstellungen sind mehr globale Administratoren als zulässig festgelegt. |

## Konfigurieren von Sicherheitswarnungen

### Konfigurieren der Warnung „Verdächtige Aktivierungserneuerung privilegierter Rollen“
1. Wählen Sie im Abschnitt **Aktivität** des Dashboards **Sicherheitswarnungen** aus. Das Blatt **Aktive Sicherheitswarnungen** wird angezeigt.
2. Klicken Sie auf **Einstellungen**.
3. Legen Sie den **Zeitrahmen für Aktivierungserneuerung** mit dem Schieberegler oder durch Eingabe der Anzahl von Minuten im Textfeld fest. Die maximal zulässige Anzahl beträgt 100 Minuten.
4. Legen Sie die **Anzahl der Aktivierungserneuerungen** im Zeitrahmen fest. Verwenden Sie hierzu den Schieberegler, oder geben Sie im Textfeld die Anzahl der Erneuerungen ein. Die maximale Anzahl von Erneuerungen beträgt 100.
5. Klicken Sie auf **Speichern**.

### Konfigurieren der Warnung „Redundante Administratoren vergrößern die Angriffsfläche“
1. Wählen Sie im Abschnitt **Aktivität** des Dashboards **Sicherheitswarnungen** aus. Das Blatt **Aktive Sicherheitswarnungen** wird angezeigt.
2. Klicken Sie auf **Einstellungen**.
3. Wählen Sie die zulässige Anzahl von Tagen ohne Rollenaktivierung aus, indem Sie den Schieberegler einstellen oder im Textfeld die Anzahl der Tage eingeben.
4. Klicken Sie auf **Speichern**.

### Konfigurieren der Warnung „Eine zu hohe Anzahl von Administratoren vergrößert die Angriffsfläche“

Diese Warnung weist zwei Einstellungen auf, die die Warnung auslösen können. "Minimale Anzahl globaler Administratoren" löst die Warnung aus, wenn die zulässige Anzahl von Administratoren überschritten wird. Die Warnung wird auch ausgelöst, wenn der prozentuale Anteil globaler Administratoren an allen Typen von Administratoren höher als der Prozentsatz in den Einstellungen ist.

1. Wählen Sie im Abschnitt **Aktivität** des Dashboards **Sicherheitswarnungen** aus. Das Blatt **Aktive Sicherheitswarnungen** wird angezeigt.
2. Klicken Sie auf **Einstellungen**.
3. Legen Sie die **Minimale Anzahl globaler Administratoren** fest, indem Sie den Schieberegler einstellen oder im Textfeld die Zahl eingeben.
4. Legen Sie den **Prozentsatz globaler Administratoren** fest, indem Sie den Schieberegler einstellen oder im Textfeld die Zahl eingeben.
5. Klicken Sie auf **Speichern**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->