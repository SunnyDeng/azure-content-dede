<properties
   pageTitle="Grundlegendes zum Azure Active Directory-Anwendungsmanifest | Microsoft Azure"
   description="Bietet ausführliche Informationen zum Azure AD-Anwendungsmanifest, das Bestandteil jeder Anwendungskonfiguration in einem Azure AD-Mandanten ist."
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
   ms.date="09/03/2015"
   ms.author="dkershaw;bryanla"/>

# Grundlegendes zum Azure Active Directory-Anwendungsmanifest

Anwendungen, die in Azure Active Directory (AD) integriert werden, müssen bei einem Azure AD-Mandanten registriert werden, um eine dauerhafte Identitätskonfiguration für die Anwendung bereitzustellen. Diese Konfiguration wird zur Laufzeit geprüft und ermöglicht so Szenarien, in denen eine Anwendung die Authentifizierung/Autorisierung über Azure AD abwickelt/vermittelt. Weitere Informationen zum Azure AD-Anwendungsmodell finden Sie im Artikel [Hinzufügen, Aktualisieren und Entfernen einer Anwendung][ADD-UPD-RMV-APP].

## Aktualisieren der Identitätskonfiguration für eine Anwendung

Es gibt verschiedene Optionen zum Aktualisieren der Eigenschaften für die Identitätskonfiguration einer Anwendung, die sich im Hinblick auf Funktionen und Komplexität unterscheiden. Es stehen u. a. folgende Optionen zur Verfügung:

- Die **Webbenutzeroberfläche im [Azure-Portal][AZURE-PORTAL]** ermöglicht es Ihnen, die gängigsten Eigenschaften einer Anwendung zu aktualisieren. Dies ist die schnellste und am wenigsten fehleranfällige Möglichkeit zum Aktualisieren der Anwendungseigenschaften, sie bietet jedoch im Gegensatz zu den zwei weiteren Methoden keinen vollständigen Zugriff auf alle Eigenschaften.
- Für einige erweiterte Szenarien, in denen Sie Eigenschaften aktualisieren müssen, die nicht im Azure-Portal verfügbar gemacht werden, können Sie das **Anwendungsmanifest** ändern. Dies ist der Schwerpunkt des vorliegenden Artikels, ausführliche Informationen erhalten Sie ab dem nächsten Abschnitt.
- Sie können zum Aktualisieren Ihrer Anwendung auch **eine Anwendung schreiben, die die [Graph-API][GRAPH-API] nutzt**. Dieser Ansatz erfordert den meisten Aufwand. Dies kann eine attraktive Option sein, wenn Sie Verwaltungssoftware schreiben oder die Anwendungseigenschaften regelmäßig und in automatisierter Weise aktualisieren müssen.

## Verwenden des Anwendungsmanifests zum Aktualisieren der Identitätskonfiguration einer Anwendung
Über das [Azure-Portal][AZURE-PORTAL] können Sie die Identitätskonfiguration Ihrer Anwendung verwalten, indem Sie eine JSON-Dateidarstellung herunter- und hochladen. Diese wird als Anwendungsmanifest bezeichnet. Im Verzeichnis wird keine wirkliche Datei gespeichert. Das Anwendungsmanifest ist lediglich ein für die Azure AD-Graph-API-Anwendungsentität ausgeführter HTTP-GET-Vorgang, und der Upload ist ein HTTP-PATCH-Vorgang für die Anwendungsentität.

Um deshalb das Format und die Eigenschaften des Anwendungsmanifests verstehen zu können, müssen Sie die Graph-API-Dokumentation zur [Anwendungsentität][APPLICATION-ENTITY] zu Rate ziehen. Nachfolgend werden einige Beispiele für Aktualisierungen aufgeführt, die Sie über ein Anwendungsmanifest durchführen können:

- Deklarieren von Berechtigungsbereichen (oauth2Permissions), die von Ihrer Web-API verfügbar gemacht werden. Informationen zum Implementieren von Benutzeridentitätswechseln mithilfe des delegierten Berechtigungsbereichs "oauth2Permissions" finden Sie im Thema "Bereitstellen von Web-APIs für andere Anwendungen" des Artikels [Integrieren von Anwendungen in Azure Active Directory][INTEGRATING-APPLICATIONS-AAD]. Wie bereits erwähnt, werden alle Eigenschaften von Anwendungsentitäten im Artikel [Entity and Complex Type reference][APPLICATION-ENTITY] der Graph-API-Referenz (in englischer Sprache) dokumentiert, darunter auch "oauth2Permissions" mit dem Typ [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- Deklarieren von Anwendungsrollen (appRoles), die von Ihrer App verfügbar gemacht werden. Ein Implementierungsbeispiel finden Sie unter [Roles based access control in cloud applications using Azure AD][RBAC-CLOUD-APPS-AZUREAD] (in englischer Sprache).
- Deklarieren bekannter Clientanwendungen.
- Fordern Sie bei Azure AD die Ausgabe eines Anspruchs für die Gruppenmitgliedschaften des angemeldeten Benutzers an. HINWEIS: Durch eine zusätzliche Konfiguration kann ein Anspruch für die Verzeichnisrollenmitgliedschaften des Benutzers ausgegeben werden. Ein Implementierungsbeispiel finden Sie im Artikel [Authorization in Cloud Applications using AD Groups][AAD-GROUPS-FOR-AUTHORIZATION] (in englischer Sprache).
- Ermöglichen Sie die Unterstützung von impliziten OAuth 2.0-Code Grant-Datenflüssen (für eingebettete JavaScript-Webseiten oder SPAs [Single Page Applications]) durch Ihre Anwendung.
- Aktivieren Sie die Verwendung von X509-Zertifikaten als geheimer Schlüssel. Ein Implementierungsbeispiel finden Sie im Artikel [Build service and daemon apps in Office 365][O365-SERVICE-DAEMON-APPS] (in englischer Sprache).

Das Anwendungsmanifest bietet außerdem eine gute Möglichkeit, den Status Ihrer Anwendungsregistrierung nachzuverfolgen. Da dieser im JSON-Format verfügbar ist, kann die Dateidarstellung in Ihrer Quellcodeverwaltung geprüft werden, gemeinsam mit dem Quellcode der Anwendung.

### Schritt-für-Schritt-Beispiel
Gehen wir nun die erforderlichen Schritte zum Aktualisieren der Identitätskonfiguration Ihrer Anwendung über das Anwendungsmanifest durch:

1. Navigieren Sie zum [Azure-Portal][AZURE-PORTAL], und melden Sie sich mit einem Konto an, das über Berechtigungen als Dienstadministrator oder Co-Administrator verfügt.


2. Scrollen Sie nach der Authentifizierung nach unten, und wählen Sie im linken Navigationsbereich (1) die Azure-Erweiterung "Active Directory" aus. Klicken Sie dann auf den Azure AD-Mandanten, bei dem Ihre Anwendung registriert ist (2).

	![Auswählen des Azure AD-Mandanten][SELECT-AZURE-AD-TENANT]


3. Wenn die Verzeichnisseite angezeigt wird, klicken Sie im oberen Bereich der Seite auf "Anwendungen" (1), um eine Liste der beim Mandanten registrierten Anwendungen anzuzeigen. Suchen Sie anschließend in der Liste nach der Anwendung, die Sie aktualisieren möchten, und klicken Sie darauf (2).

	![Auswählen des Azure AD-Mandanten][SELECT-AZURE-AD-APP]


4. Nachdem Sie die Hauptseite der Anwendung ausgewählt haben, beachten Sie das Feature "Manifest verwalten" unten auf der Seite (1). Wenn Sie auf diesen Link klicken, werden Sie entweder zum Herunterladen oder zum Hochladen der JSON-Manifestdatei aufgefordert. Klicken Sie auf "Manifest herunterladen" (2). Es wird daraufhin ein Dialogfeld zum Bestätigen des Downloads angezeigt. Bestätigen Sie den Download durch Klicken auf "Manifest herunterladen" (3), und öffnen Sie dann entweder die Datei, oder speichern Sie sie lokal (4).

	![Verwalten des Manifests, Option zum Herunterladen][MANAGE-MANIFEST-DOWNLOAD]

	![Herunterladen des Manifests][DOWNLOAD-MANIFEST]


5. In diesem Beispiel wurde die Datei lokal gespeichert, sodass die JSON-Datei in einem Editor geöffnet, geändert und anschließend gespeichert werden kann. Nachfolgend sehen Sie, wie die JSON-Struktur im Visual Studio-JSON-Editor aussieht. Beachten Sie, dass sie dem zuvor erwähnten Schema für die [Anwendungsentität][APPLICATION-ENTITY] folgt:

	![Aktualisieren der JSON-Manifestdatei][UPDATE-MANIFEST]


6. Wenn Sie die Aktualisierung des Manifests abgeschlossen haben, kehren Sie zur Azure AD-Anwendungsseite im Azure-Portal zurück, klicken Sie erneut auf "Manifest verwalten" (1), wählen Sie diese Mal jedoch die Option "Manifest hochladen" (2). Ähnlich wie beim Download wird ein zweites Dialogfeld angezeigt, indem Sie zur Eingabe des Speicherorts der JSON-Datei aufgefordert werden. Klicken Sie auf "Nach Datei suchen..." (3), und verwenden Sie anschließend das Dialogfeld "Datei für Upload auswählen", um die JSON-Datei (4) auszuwählen. Klicken Sie dann auf "Öffnen". Sobald das Dialogfeld geschlossen wurde, klicken Sie auf das OK-Symbol (ein Häkchen) (5), und Ihr Manifest wird hochgeladen.

	![Verwalten des Manifests, Option zum Hochladen][MANAGE-MANIFEST-UPLOAD]

	![Hochladen der JSON-Manifestdatei][UPLOAD-MANIFEST]

	![Hochladen der JSON-Manifestdatei – Bestätigung][UPLOAD-MANIFEST-CONFIRM]

Das ist alles. Jetzt kann Ihre Anwendung mit der neuen Anwendungskonfiguration ausgeführt werden, basierend auf den Änderungen, die Sie im Manifest durchgeführt haben.

## Nächste Schritte
Verwenden Sie den unten angezeigten DISQUS-Kommentarabschnitt, um uns Feedback zu senden und uns bei der Verbesserung unserer Inhalte zu unterstützen.

<!--Image references-->
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-PORTAL]: https://manage.windowsazure.com
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=Sept15_HO3-->