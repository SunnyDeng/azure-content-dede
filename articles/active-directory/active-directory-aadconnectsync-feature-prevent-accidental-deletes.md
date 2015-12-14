<properties
   pageTitle="Azure AD Connect-Synchronisierung: Verhindern von versehentlichen Löschungen | Microsoft Azure"
   description="In diesem Thema wird die Funktion zum Verhindern von versehentlichen Löschungen (Verhindern versehentlicher Löschvorgänge) in Azure AD Connect beschrieben."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/20/2015"
   ms.author="andkjell"/>

# Azure AD Connect-Synchronisierung: Verhindern von versehentlichen Löschvorgängen
In diesem Thema wird die Funktion zum Verhindern von versehentlichen Löschungen (Verhindern versehentlicher Löschvorgänge) in Azure AD Connect beschrieben.

Bei der Installation von Azure AD Connect wird die Funktion zum Schutz vor unbeabsichtigtem Löschen standardmäßig aktiviert und so konfiguriert, das Exporte mit mehr als 500 Löschungen unterbunden werden. Diese Funktion dient zum Schutz vor unbeabsichtigten Konfigurationsänderungen und Änderungen an Ihrem lokalen Verzeichnis, die sich auf eine große Anzahl von Benutzern auswirken würden.

Der Standardwert von 500 Objekten kann mit PowerShell mithilfe von `Enable-ADSyncExportDeletionThreshold` geändert werden. Sie sollten diesen Wert entsprechend der Größe Ihres Unternehmens konfigurieren. Da die Synchronisierungsplanung alle 3 Stunden ausgeführt wird, entspricht der Wert der Anzahl von Löschvorgängen, die innerhalb von 3 Stunden erfolgen.

Wenn diese Funktion aktiviert ist und zu viele Löschungen in Azure AD exportiert werden sollen, wird der Export nicht fortgesetzt, und Sie erhalten Sie die folgende E-Mail-Nachricht:

![Versehentliche Löschungen – E-Mail](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

Wenn Sie diese Nachricht unerwartet erhalten haben, untersuchen Sie die Grunde dafür, und ergreifen Sie die nötigen Maßnahmen, um das Problem zu beheben. Führen Sie die folgenden Schritte aus, um zu ermitteln, welche Objekte gelöscht werden sollen:

1. Starten Sie den **Synchronisierungsdienst** über das Startmenü.
2. Gehen Sie zu **Connectors**.
3. Wählen Sie den Connector mit dem Typ **Azure Active Directory**.
4. Klicken Sie unter **Aktionen** wählen Sie auf der rechten Seite **Suche Connectorbereich**.
5. Wählen Sie im Popupfenster unter **Bereich** die Option **Getrennt seit** aus, und wählen Sie einen Zeitpunkt in der Vergangenheit. Klicken Sie auf **Suchen**. Dadurch wird eine Übersicht über alle zu löschenden Objekte angezeigt. Klicken Sie auf die einzelnen Objekte, um zusätzliche Informationen dazu zu erhalten. Sie können auch auf **Spalteneinstellung** klicken, um zusätzliche Attribute hinzuzufügen, die im Raster angezeigt werden.

![Connectorbereich durchsuchen](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Wenn alle Löschvorgänge gewünscht sind, gehen Sie folgendermaßen vor:

1. Um den Schutz vorübergehend zu deaktivieren und diese Löschvorgänge zuzulassen, führen Sie folgendes PowerShell-Cmdlet aus: `Disable-ADSyncExportDeletionThreshold`. Geben Sie bei der Eingabeaufforderung für die Anmeldeinformationen das Konto und das Kennwort eines globalen Azure AD-Administrators ein. ![Anmeldeinformationen](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Lassen Sie Azure Active Directory Connector weiterhin ausgewählt, wählen Sie die Aktion **Ausführen** und anschließend **Exportieren** aus.
3. Führen Sie zum erneuten Aktivieren des Schutzes folgendes PowerShell-Cmdlet aus: `Enable-ADSyncExportDeletionThreshold`.

## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->