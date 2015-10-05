<properties
   pageTitle="Der Sicherheits-Assistent von Azure Privileged Identity Management"
   description="Bei der ersten Verwendung der Erweiterung Azure Privileged Identity Management wird ein Sicherheits-Assistent angezeigt. Dieser Artikel beschreibt die Schritte zur Verwendung des Assistenten."
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

# Der Sicherheits-Assistent von Azure Privileged Identity Management

Beim ersten Ausführen von Azure Privileged Identity Management wird ein Assistent angezeigt. Der Assistent bietet Ihnen Einblick in die Sicherheitsrisiken privilegierter Identitäten und hilft Ihnen, diese Risiken mithilfe von Privileged Identity Management zu reduzieren.

Er enthält drei zu lesende Abschnitte: **IHRE ADMINISTRATOREN SETZEN SIE MÖGLICHERWEISE RISIKEN AUS, VERRINGERN SIE DIE ANGRIFFSFLÄCHE IHRER ADMINISTRATOREN** und **DEFINIEREN SIE TEMPORÄRE ADMINISTRATOREINSTELLUNGEN**. Jeder Abschnitt bietet einen Überblick über die Konzepte und eine Erläuterung von durchzuführenden Maßnahmen.

Alle globalen Administratoren sind zunächst permanente Administratoren. Wenn Sie auf **IHRE ADMINISTRATOREN SETZEN SIE MÖGLICHERWEISE RISIKEN AUS** klicken, wird eine Liste der globalen Administratorrollen und deren aktuelle Anzahl angezeigt.

Durch Klicken auf **VERRINGERN SIE DIE ANGRIFFSFLÄCHE IHRER ADMINISTRATOREN** können Sie wählen, ob die Administratoren in temporäre Administratoren geändert, als permanente Administratoren beibehalten oder aus der Rolle entfernt werden.

Mit **DEFINIEREN SIE TEMPORÄRE ADMINISTRATOREINSTELLUNGEN** können Sie bestimmen, wie lange ein temporärer Administrator über Berechtigungen verfügt, Benachrichtigungen aktivieren und Multi-Factor Authentication als erforderlich festlegen.

## Ändern globaler Administratorrollen in temporäre oder permanente Rollen

Ihnen stehen drei Optionen zum Ändern des Zeitfensters eines globalen Administrators zur Verfügung.

1.  Klicken Sie auf die Schaltfläche **Alle als temporär festlegen**, um alle globalen Administratoren als temporäre Administratoren festzulegen.

2.  Klicken Sie auf die Schaltfläche **Alle als permanent festlegen**, um alle globalen Administratoren als permanente Administratoren festzulegen.

3.  Wählen Sie für jeden globalen Administrator **Als permanent belassen**, **Als temporär festlegen** oder **Aus Rolle entfernen** aus.

4.  Klicken Sie auf **OK**.

5.  Klicken Sie auf **Senden**.

## Ändern des Aktivierungszeitraums für eine globale Administratorrolle

1.  Ziehen Sie den Schieberegler **Aktivierungszeitraum** nach links oder rechts, um den Aktivierungszeitraum zu verlängern oder zu verringern. Der Aktivierungszeitraum kann bis zu 72 Stunden betragen.

2.  Geben Sie rechts vom Schieberegler im Feld **Stunden** die Anzahl der Stunden ein.

## Aktivieren von Benachrichtigungen

Damit Administratoren eine E-Mail empfangen können, wenn Rollen aktiviert werden, klicken Sie zum Aktivieren von Benachrichtigungen auf die Schaltfläche **Aktivieren**. Sie können diese Funktion auch deaktivieren.

## Erfordern von Multi-Factor Authentication

Wenn Sie möchten, dass Administratoren zum Anmelden bei ihren Konten und zum Anfordern einer Erweiterung ihrer Rolle Multi-Factor Authentication (MFA) verwenden müssen, klicken Sie zum Aktivieren von MFA auf die Schaltfläche **Aktivieren**. Sie können diese Funktion auch deaktivieren.

Klicken Sie hier, um weitere Informationen zu MFA und PIM zu erhalten. PLACEHOLDER: NEED LINK TO MFA DOC.

Wählen Sie die Rollen aus, auf die diese Einstellungen angewendet werden. Klicken Sie auf **OK**.

> [AZURE.WARNING]Zu diesem Zeitpunkt müssen Sie über mindestens zwei Sicherheitsadministratoren verfügen. Wenn nur ein Sicherheitsadministrator vorhanden und nicht als permanenter Administrator festgelegt ist und für diesen Benutzer nicht MFA eingerichtet ist, kann dieser Benutzer bei Ablauf der Rollenzuweisung PIM nicht verwalten.

Klicken Sie auf die Schaltfläche **OK**, wenn Sie die Änderungen abgeschlossen haben.

Nachdem Sie Änderungen vorgenommen haben, wird der Assistent nicht mehr angezeigt. Sie können ihn jedoch erneut aufrufen, indem Sie unter "Identitäten verwalten" auf die Schaltfläche "Assistent" klicken.

## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->