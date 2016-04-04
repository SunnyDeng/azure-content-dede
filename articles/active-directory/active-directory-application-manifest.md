<properties
   pageTitle="Grundlegendes zum Azure Active Directory-Anwendungsmanifest | Microsoft Azure"
   description="Sie erhalten ausführliche Informationen zur Verwendung des Azure Active Directory-Anwendungsmanifests, das die Identitätskonfiguration einer Anwendung in einem Azure AD-Mandanten darstellt und für die OAuth-Autorisierung, Zustimmungsoberfläche und andere Zwecke genutzt wird."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/16/2016"
   ms.author="dkershaw;bryanla"/>

# Grundlegendes zum Azure Active Directory-Anwendungsmanifest

Anwendungen, die in Azure Active Directory (AD) integriert werden, müssen bei einem Azure AD-Mandanten registriert werden, um eine dauerhafte Identitätskonfiguration für die Anwendung bereitzustellen. Diese Konfiguration wird zur Laufzeit geprüft und ermöglicht so Szenarien, in denen eine Anwendung die Authentifizierung/Autorisierung über Azure AD abwickelt/vermittelt. Weitere Informationen zum Azure AD-Anwendungsmodell finden Sie im Artikel [Hinzufügen, Aktualisieren und Entfernen einer Anwendung][ADD-UPD-RMV-APP].

## Aktualisieren der Identitätskonfiguration für eine Anwendung

Es gibt verschiedene Optionen zum Aktualisieren der Eigenschaften für die Identitätskonfiguration einer Anwendung, die sich im Hinblick auf Funktionen und Komplexität unterscheiden. Es stehen u. a. folgende Optionen zur Verfügung:

- Die **Webbenutzeroberfläche im klassischen [Azure-Portal][AZURE-CLASSIC-PORTAL]** ermöglicht es Ihnen, die gängigsten Eigenschaften einer Anwendung zu aktualisieren. Dies ist die schnellste und am wenigsten fehleranfällige Möglichkeit zum Aktualisieren der Anwendungseigenschaften, sie bietet jedoch im Gegensatz zu den zwei weiteren Methoden keinen vollständigen Zugriff auf alle Eigenschaften.
- Für einige erweiterte Szenarien, in denen Sie Eigenschaften aktualisieren müssen, die nicht im klassischen Azure-Portal verfügbar gemacht werden, können Sie das **Anwendungsmanifest** ändern. Dies ist der Schwerpunkt des vorliegenden Artikels, ausführliche Informationen erhalten Sie ab dem nächsten Abschnitt.
- Sie können zum Aktualisieren Ihrer Anwendung auch **eine Anwendung schreiben, die die [Graph-API][GRAPH-API] nutzt**. Dieser Ansatz erfordert den meisten Aufwand. Dies kann eine attraktive Option sein, wenn Sie Verwaltungssoftware schreiben oder die Anwendungseigenschaften regelmäßig und in automatisierter Weise aktualisieren müssen.

## Verwenden des Anwendungsmanifests zum Aktualisieren der Identitätskonfiguration einer Anwendung
Über das [klassische Azure-Portal][AZURE-CLASSIC-PORTAL] können Sie die Identitätskonfiguration Ihrer Anwendung verwalten, indem Sie eine JSON-Dateidarstellung herunter- und hochladen. Diese wird als Anwendungsmanifest bezeichnet. Im Verzeichnis wird keine wirkliche Datei gespeichert. Das Anwendungsmanifest ist lediglich ein für die Azure AD-Graph-API-Anwendungsentität ausgeführter HTTP-GET-Vorgang, und der Upload ist ein HTTP-PATCH-Vorgang für die Anwendungsentität.

Um deshalb das Format und die Eigenschaften des Anwendungsmanifests verstehen zu können, müssen Sie die Graph-API-Dokumentation zur [Anwendungsentität][APPLICATION-ENTITY] zu Rate ziehen. Nachfolgend werden einige Beispiele für Aktualisierungen aufgeführt, die Sie über ein Anwendungsmanifest durchführen können:

- Deklarieren von Berechtigungsbereichen (oauth2Permissions), die von Ihrer Web-API verfügbar gemacht werden. Informationen zum Implementieren von Benutzeridentitätswechseln mithilfe des delegierten Berechtigungsbereichs "oauth2Permissions" finden Sie im Thema "Bereitstellen von Web-APIs für andere Anwendungen" des Artikels [Integrieren von Anwendungen in Azure Active Directory][INTEGRATING-APPLICATIONS-AAD]. Wie bereits erwähnt, werden alle Eigenschaften von Anwendungsentitäten im Graph-API-Referenzartikel [Entity and complex type reference][APPLICATION-ENTITY] dokumentiert, darunter auch die oauth2Permissions-Eigenschaft. Hierbei handelt es sich um eine Auflistung des Typs [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- Deklarieren von Anwendungsrollen (appRoles), die von Ihrer App verfügbar gemacht werden. Die appRoles-Eigenschaft der Anwendungsentität ist eine Auflistung vom Typ [AppRole][APPLICATION-ENTITY-APP-ROLE]. Ein Implementierungsbeispiel finden Sie unter [Roles based access control in cloud applications using Azure AD][RBAC-CLOUD-APPS-AZUREAD] (in englischer Sprache).
- Deklarieren Sie bekannte Clientanwendungen (knownClientApplications), mit deren Hilfe Sie die Zustimmung der angegebenen Clientanwendungen logisch an die Ressourcen-/Web-API binden können.
- Fordern Sie bei Azure AD die Ausgabe eines Anspruchs für die Gruppenmitgliedschaften des angemeldeten Benutzers an (groupMembershipClaims). HINWEIS: Durch eine zusätzliche Konfiguration kann ein Anspruch für die Verzeichnisrollenmitgliedschaften des Benutzers ausgegeben werden. Ein Implementierungsbeispiel finden Sie im Artikel [Authorization in Cloud Applications using AD Groups][AAD-GROUPS-FOR-AUTHORIZATION] (in englischer Sprache).
- Ermöglichen Sie für Ihre Anwendung die Unterstützung von impliziten OAuth 2.0-Code Grant-Datenflüssen (oauth2AllowImplicitFlow). Diese Art von Code Grant-Datenflüssen wird mit eingebetteten JavaScript-Webseiten oder Single-Page-Anwendungen (SPA) verwendet.
- Aktivieren Sie die Verwendung von X509-Zertifikaten als geheimer Schlüssel (keyCredentials). Beispiele für die Implementierung finden Sie in den Artikeln [Builddienst und Daemon-Apps in Office 365][O365-SERVICE-DAEMON-APPS] und [Entwicklerhandbuch für die Authentifizierung mit Azure-Ressourcen-Manager-API][DEV-GUIDE-TO-AUTH-WITH-ARM].

Das Anwendungsmanifest bietet außerdem eine gute Möglichkeit, den Status Ihrer Anwendungsregistrierung nachzuverfolgen. Da dieser im JSON-Format verfügbar ist, kann die Dateidarstellung in Ihrer Quellcodeverwaltung geprüft werden, gemeinsam mit dem Quellcode der Anwendung.

## Schritt-für-Schritt-Beispiel
Nun gehen wir die erforderlichen Schritte zum Aktualisieren der Identitätskonfiguration Ihrer Anwendung über das Anwendungsmanifest durch. Anhand eines der obigen Beispiele zeigen wir, wie Sie für eine Ressourcenanwendung einen neuen Berechtigungsbereich deklarieren:

1. Navigieren Sie zum [klassischen Azure-Portal][AZURE-CLASSIC-PORTAL], und melden Sie sich mit einem Konto an, das über Berechtigungen als Dienstadministrator oder Co-Administrator verfügt.

2. Scrollen Sie nach der Authentifizierung nach unten, und wählen Sie im linken Navigationsbereich (1) die Azure-Erweiterung "Active Directory" aus. Klicken Sie dann auf den Azure AD-Mandanten, bei dem Ihre Anwendung registriert ist (2).

    ![Auswählen des Azure AD-Mandanten][SELECT-AZURE-AD-TENANT]

3. Wenn die Verzeichnisseite angezeigt wird, klicken Sie im oberen Bereich der Seite auf "Anwendungen" (1), um eine Liste der beim Mandanten registrierten Anwendungen anzuzeigen. Suchen Sie anschließend in der Liste nach der Anwendung, die Sie aktualisieren möchten, und klicken Sie darauf (2).

    ![Auswählen des Azure AD-Mandanten][SELECT-AZURE-AD-APP]

4. Nachdem Sie die Hauptseite der Anwendung ausgewählt haben, beachten Sie das Feature "Manifest verwalten" unten auf der Seite (1). Wenn Sie auf diesen Link klicken, werden Sie entweder zum Herunterladen oder zum Hochladen der JSON-Manifestdatei aufgefordert. Klicken Sie auf "Manifest herunterladen" (2). Es wird daraufhin ein Dialogfeld zum Bestätigen des Downloads angezeigt. Bestätigen Sie den Download durch Klicken auf "Manifest herunterladen" (3), und öffnen Sie dann entweder die Datei, oder speichern Sie sie lokal (4).

    ![Verwalten des Manifests, Option zum Herunterladen][MANAGE-MANIFEST-DOWNLOAD]

    ![Herunterladen des Manifests][DOWNLOAD-MANIFEST]

5. In diesem Beispiel wurde die Datei lokal gespeichert, sodass die JSON-Datei in einem Editor geöffnet, geändert und anschließend gespeichert werden kann. Nachfolgend sehen Sie, wie die JSON-Struktur im Visual Studio-JSON-Editor aussieht. Beachten Sie, dass sie dem zuvor erwähnten Schema für die [Anwendungsentität][APPLICATION-ENTITY] folgt:

    ![Aktualisieren der JSON-Manifestdatei][UPDATE-MANIFEST]

    Angenommen, wir möchten eine neue Berechtigung mit dem Namen „Employees.Read.All“ für unsere Ressourcenanwendung (API) implementieren oder verfügbar machen. Hierfür fügen Sie einfach der Auflistung „oauth2Permissions“ ein neues bzw. zweites Element hinzu. Beispiel:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    Der Eintrag muss eindeutig sein, sodass Sie eine neue globale eindeutige ID (GUID) für die `"id"`-Eigenschaft generieren müssen. Da wir `"type": "User"` angegeben haben, kann diese Berechtigung hier die Zustimmung über jedes Konto erhalten, das mit dem Azure AD-Mandanten authentifiziert wurde, unter dem die Ressourcen-/API-Anwendung registriert ist. Der Clientanwendung wird so die Berechtigung für den Zugriff im Namen des Kontos gewährt. Die Zeichenfolgen für die Beschreibung und den Anzeigenamen werden während des Zustimmungsvorgangs und für die Anzeige im klassischen Azure-Portal verwendet.

6. Wenn Sie die Aktualisierung des Manifests abgeschlossen haben, gehen Sie zur Azure AD-Anwendungsseite im Azure-Portal zurück, klicken Sie erneut auf "Manifest verwalten" (1), wählen Sie diese Mal jedoch die Option "Manifest hochladen" (2). Ähnlich wie beim Download wird ein zweites Dialogfeld angezeigt, indem Sie zur Eingabe des Speicherorts der JSON-Datei aufgefordert werden. Klicken Sie auf "Nach Datei suchen..." (3), und verwenden Sie anschließend das Dialogfeld "Datei für Upload auswählen", um die JSON-Datei (4) auszuwählen. Klicken Sie dann auf "Öffnen". Sobald das Dialogfeld geschlossen wurde, klicken Sie auf das OK-Symbol (ein Häkchen) (5), und Ihr Manifest wird hochgeladen.

    ![Verwalten des Manifests, Option zum Hochladen][MANAGE-MANIFEST-UPLOAD]

    ![Hochladen der JSON-Manifestdatei][UPLOAD-MANIFEST]

    ![Hochladen der JSON-Manifestdatei – Bestätigung][UPLOAD-MANIFEST-CONFIRM]

Nachdem das Manifest gespeichert wurde, können Sie einer registrierten Clientanwendung Zugriff auf die neue Berechtigung gewähren, die wir oben hinzugefügt haben. Jetzt können Sie aber die Webbenutzeroberfläche des klassischen Azure-Portals verwenden, anstatt das Manifest der Clientanwendung zu bearbeiten:

1. Navigieren Sie zuerst zur Seite „Konfigurieren“ der Clientanwendung, der Sie den Zugriff auf die neue API hinzufügen möchten, und klicken Sie auf die Schaltfläche „Anwendung hinzufügen“.
2. Anschließend wird die Liste mit den registrierten Ressourcenanwendungen (APIs) des Mandanten angezeigt. Klicken Sie auf das Pluszeichen neben dem Namen der Ressourcenanwendung, um sie auszuwählen.  
3. Klicken Sie anschließend unten rechts auf das Häkchen.
4. Wenn Sie zurück zum Abschnitt „Anwendung hinzufügen“ auf der Konfigurationsseite Ihres Clients wechseln, ist die neue Ressourcenanwendung in der Liste aufgeführt. Wenn Sie rechts von dieser Zeile mit der Maus auf den Abschnitt „Delegierte Berechtigungen“ zeigen, wird eine Dropdownliste angezeigt. Klicken Sie auf die Liste, und wählen Sie dann die neue Berechtigung aus, um sie der angeforderten Liste mit den Berechtigungen des Clients hinzuzufügen. Hinweis: Diese neue Berechtigung wird in der Identitätskonfiguration der Clientanwendung in der Auflistungseigenschaft „RequiredResourceAccess“ gespeichert.

![Berechtigungen für andere Anwendungen][PERMS-TO-OTHER-APPS]

![Berechtigungen für andere Anwendungen][PERMS-SELECT-APP]

![Berechtigungen für andere Anwendungen][PERMS-SELECT-PERMS]

Das ist alles. Ihre Anwendungen werden jetzt mit der neuen Identitätskonfiguration ausgeführt.

## Nächste Schritte
Verwenden Sie den unten angezeigten DISQUS-Kommentarabschnitt, um uns Feedback zu senden und uns bei der Verbesserung unserer Inhalte zu unterstützen.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=AcomDC_0323_2016-->