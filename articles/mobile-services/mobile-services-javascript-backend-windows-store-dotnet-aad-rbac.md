<properties 
	pageTitle="Rollenbasierte Zugriffssteuerung in Mobile Services und Azure Active Directory (Windows Store) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie den Zugriff basierend auf Azure Active Directory-Rollen in Ihrer Windows Store-Anwendung steuern." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="wesmc"/>

# Rollenbasierte Zugriffssteuerung in Mobile Services und Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##Übersicht

Rollenbasierte Zugriffssteuerung (RBAC) besteht im Zuweisen von Berechtigungen für Rollen, die Benutzer innehaben können, um Beschränkungen für die Möglichkeiten bestimmter Benutzer zu definieren. In diesem Lernprogramm erfahren Sie, wie Sie grundlegende RBAC für Azure Mobile Services hinzufügen können.

In diesem Lernprogramm wird die rollenbasierte Zugriffssteuerung erläutert, welche die Mitgliedschaft der einzelnen Benutzer in der Gruppe „Sales“ prüft, die in Azure Active Directory (AAD) definiert ist. Die Zugriffsprüfung erfolgt mit JavaScript im Mobile Services-Backe-Ed mithilfe der [Graph-API] für Azure Active Directory. Nur Benutzer, die der Rolle „Sales“ angehören, dürfen Daten abfragen.


>[AZURE.NOTE]Dieses Lernprogramm ist dazu vorgesehen, Ihre Kenntnisse bezüglich der Authentifizierung um Autorisierungsmethoden zu erweitern. Es wird davon ausgegangen, dass Sie das Lernprogramm [Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters abgeschlossen haben. In diesem Lernprogramm wird die TodoItem-Anwendung weiter aktualisiert, die im Lernprogramm [Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App] verwendet wurde.

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 für Windows 8.1
* Abschluss des Lernprogramms [Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters.
* Abschluss des Lernprogramms [Speichern von Serverskripts] zur Verwendung eines Git-Repositorys zum Speichern von Serverskripts
 


##Erstellen der Gruppe „Sales“ mit Mitgliedschaften

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]


##Erzeugen eines Schlüssels für die integrierte Anwendung


Im Lernprogramm [Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App] haben Sie eine Registrierung für die integrierte Anwendung erstellt, als Sie den Schritt [Registrieren für die Verwendung einer Azure Active Directory-Anmeldung] ausgeführt haben. In diesem Abschnitt erzeugen Sie einen Zugriffsschlüssel, der beim Lesen der Verzeichnisinformationen mit der Client-ID der integrierten Anwendung verwendet wird.

Wenn Sie das Lernprogramm [Zugriff auf Azure Active Directory Graph-Informationen] abgeschlossen haben, haben Sie diesen Schritt bereits durchgeführt und können diesen Abschnitt überspringen.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]





##Hinzufügen eines gemeinsam genutzten Skripts zum mobilen Dienst zur Prüfung der Mitgliedschaft

In diesem Abschnitt verwenden Sie Git, um eine gemeinsam genutzte Skriptdatei namens *rbac.js* zu Ihrem mobilen Dienst hinzuzufügen. Diese gemeinsam genutzte Skriptdatei enthält die Funktionen, welche die [Graph-API] verwenden, um die Gruppenmitgliedschaft des Benutzers zu prüfen.

Wenn Sie mit der Bereitstellung von Skripts mit Git für Ihren mobilen Dienst noch nicht vertraut sind, verwenden Sie das Lernprogramm [Speichern von Serverskripts], bevor Sie diesen Abschnitt abschließen.

1. Erstellen Sie eine neue Skriptdatei namens *rbac.js* im Verzeichnis *./service/shared/* des lokalen Repositorys für Ihren mobilen Dienst.
2. Fügen Sie das folgende Skript am Anfang der Datei ein, welches die Funktion `getAADToken` definiert. Anhand der *Mandantendomäne*, der *Client-ID* der integrierten Anwendung sowie des *Anwendungsschlüssels* stellt diese Funktion ein Graph-Zugriffstoken bereit, das zum Lesen der Verzeichnisinformationen verwendet wird.

    >[AZURE.NOTE]Sie sollten das Token zwischenspeichern, statt für jede Zugriffsprüfung ein neues Token zu erstellen. Aktualisieren Sie den Zwischenspeicher, wenn bei der Verwendung des Tokens ein Authentication_ExpiredToken-Fehler 401 ausgegeben wird, wie in der [Graph-API-Fehlerreferenz] beschrieben ist. Aus Gründen der Vereinfachung wird dies im Code unten nicht dargestellt, aber es minimiert den zusätzlichen Netzwerkverkehr mit Active Directory.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;
         
        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
                });
        }


3. Fügen Sie den folgenden Code zu *rbac.js* hinzu, um die Funktion `getGroupId` zu definieren. Diese Funktion verwendet das Zugriffstoken, um die Gruppen-ID basierend auf dem Gruppennamen abzurufen, der in einem Filter verwendet wird.
 
    >[AZURE.NOTE]Dieser Code schlägt die Active Directory-Gruppe nach dem Namen nach. In vielen Fällen ist es empfehlenswert, die Gruppen-ID als App-Einstellung des mobilen Diensts zu speichern und nur diese Gruppen-ID zu verwenden. Der Gruppenname kann sich ändern, die ID bleibt dagegen immer gleich. Mit der Änderung des Gruppennamens ist jedoch meist eine Änderung des Rollenumfangs verbunden, daher kann also trotzdem eine Aktualisierung des mobilen Dienstcodes erforderlich sein.

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
	              "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }


4. Fügen Sie den folgenden Code zu *rbac.js* hinzu, um die Funktion `isMemberOf` zu definieren, welche den [IsMemberOf]-Endpunkt der Graph-REST-API aufruft.

    Diese Funktion ist ein einfacher Wrapper für den [IsMemberOf]-Endpunkt der Graph-REST-API. Sie verwendet das Graph-Zugriffstoken, um basierend auf der Gruppen-ID zu prüfen, ob die Verzeichnisobjekt-ID des Benutzers Mitglied der Gruppe ist.

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

    

7. Fügen Sie die folgende exportierte Funktion `checkGroupMembership` zu *rbac.js* hinzu.

    Diese Funktion fasst die Verwendung der anderen Skriptfunktionen ein und wird aus dem freigegebenen Skript exportiert, sodass sie von anderen Skripts zur Durchführung der tatsächlichen Zugriffsprüfung aufgerufen werden kann. Anhand des Benutzerobjekts des mobilen Diensts und der Gruppen-ID ruft das Skript die Objekt-ID aus Azure Active Directory für die Benutzer-ID ab und überprüft die Mitgliedschaft in der Gruppe.

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
		        else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
       	                        if (err) errorHandler(err);
               	                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

8. Speichern Sie die Änderungen an *rbac.js*.

##Hinzufügen einer rollenbasierten Zugriffsprüfung für Datenbankvorgänge


Als Sie das Lernprogramm [Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App] abgeschlossen haben, sollten Sie bereits die Tabellenvorgänge zur Verwendung von Authentifizierung eingerichtet haben, wie unten dargestellt.

![][3]

Für jeden Datenbankvorgang, der Authentifizierung erfordert, können wir Skripts hinzufügen, die das Benutzerobjekt für die Zugriffsprüfung verwenden.

Die folgenden Schritte zeigen, wie Sie rollenbasierte Zugriffssteuerung mithilfe von Skripts zu jedem Tabellenvorgang im mobilen Dienst hinzufügen. Für jeden Tabellenvorgang wird ein Skript ausgeführt, welches das freigegebene Skript *rbac.js* verwendet.

1. Erstellen Sie eine neue Skriptdatei namens *todoitem.insert.js* im Verzeichnis *./service/table/* des lokalen Git-Repositorys für Ihren mobilen Dienst. Fügen Sie das folgende Skript in diese Datei ein:

        function insert(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2. Erstellen Sie eine neue Skriptdatei namens *todoitem.read.js* im Verzeichnis *./service/table/* des lokalen Git-Repositorys für Ihren mobilen Dienst. Fügen Sie das folgende Skript in diese Datei ein:

        function read(query, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3. Erstellen Sie eine neue Skriptdatei namens *todoitem.update.js* im Verzeichnis *./service/table/* des lokalen Git-Repositorys für Ihren mobilen Dienst. Fügen Sie das folgende Skript in diese Datei ein:

        function update(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4. Erstellen Sie eine neue Skriptdatei namens *todoitem.delete.js* im Verzeichnis *./service/table/* des lokalen Git-Repositorys für Ihren mobilen Dienst. Fügen Sie das folgende Skript in diese Datei ein:

        function del(id, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5. Fügen Sie an der Befehlszeilenschnittstelle für Ihr Git-Repository die Verfolgung der hinzugefügten Skriptdateien mit dem folgenden Befehl hinzu:

        git add .

6. Schreiben Sie an der Befehlszeilenschnittstelle für Ihr Git-Repository die Aktualisierungen mit dem folgenden Befehl fest:

        git commit -m "Added role based access control to table operations"
  
7. Stellen Sie an der Befehlszeilenschnittstelle für Ihr Git-Repository die Aktualisierungen für Ihr lokales Git-Repository mit dem folgenden Befehl für den mobilen Dienst bereit. Bei diesem Befehl wird davon ausgegangen, dass Sie die Aktualisierungen in der Verzweigung *master* vorgenommen haben.

        git push origin master

8. Sie sollten im [Azure-Verwaltungsportal] nun in der Lage sein, zu den Tabellenvorgängen für den mobilen Dienst zu navigieren und die Aktualisierungen dort anzuzeigen, wie unten dargestellt.

    ![][4]

##Testen des Clientzugriffs

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/users.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/group-membership.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
[4]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
[5]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group-id.png
[6]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/client-id-and-key.png

<!-- URLs. -->
[Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Speichern von Serverskripts]: mobile-services-store-scripts-source-control.md
[Registrieren für die Verwendung einer Azure Active Directory-Anmeldung]: mobile-services-how-to-register-active-directory-authentication.md
[Graph-API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph-API-Fehlerreferenz]: http://msdn.microsoft.com/library/azure/hh974480.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Zugriff auf Azure Active Directory Graph-Informationen]: mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info.md

<!---HONumber=July15_HO2-->