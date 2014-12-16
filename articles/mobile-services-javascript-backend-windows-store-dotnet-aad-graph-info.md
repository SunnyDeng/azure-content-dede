<properties urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Zugreifen auf Informationen in Azure Active Directory Graph (Windows Store) | Mobile Developer Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="wesmc" />

# Zugriff auf Azure Active Directory Graph-Informationen

[WACOM.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]


Wie auch andere Identitätsanbieter, die mit Mobile Services angeboten werden, unterstützt der Azure Active Directory (AAD)-Anbieter eine umfassende [Graph-API], die für programmgesteuerten Zugriff auf das Verzeichnis verwendet werden kann. In diesem Lernprogramm aktualisieren Sie die ToDoList-App, um das App-Erlebnis des authentifizierten Benutzers basierend auf zusätzlichen Benutzerinformationen, die Sie aus dem Verzeichnis mithilfe der [Graph-API] abrufen, zu personalisieren.

>[WACOM.NOTE] Dieses Lernprogramms soll Ihre Kenntnisse bezüglich der Authentifizierung mit Azure Active Directory vertiefen. Es wird davon ausgegangen, dass Sie das Lernprogramm [Erste Schritte mit der Authentifizierung] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters abgeschlossen haben. In diesem Lernprogramm wird die Anwendung TodoItem weiter aktualisiert, die im Lernprogramm [Erste Schritte mit der Authentifizierung] verwendet wurde.



In diesem Lernprogramm werden die folgenden Schritte behandelt:


1. [Erzeugen eines Zugriffsschlüssels für die App-Registrierung in AAD] 
2. [Erstellen einer benutzerdefinierten API "GetUserInfo"] 
3. [Aktualisieren der App zur Verwendung der benutzerdefinierten API]
4. [Testen der App]

##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App "TodoList" hinzu.

+ [Benutzerdefinierte API - Lernprogramm]<br/>Erläutert das Aufrufen einer benutzerdefinierten API. 



## <a name="generate-key"></a>Erzeugen eines Zugriffsschlüssels für die App-Registrierung in AAD


Im Lernprogramm [Erste Schritte mit der Authentifizierung] haben Sie eine Registrierung für die integrierte Anwendung erstellt, als Sie den Schritt [Registrieren für die Verwendung einer Azure Active Directory-Anmeldung] ausgeführt haben. In diesem Abschnitt erzeugen Sie einen Schlüssel, der beim Lesen der Verzeichnisinformationen mit der Client-ID der integrierten Anwendung verwendet wird. 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-api"></a>Erstellen einer benutzerdefinierten API "GetUserInfo"

In diesem Abschnitt erstellen Sie die benutzerdefinierte API "GetUserInfo", welche die [Graph-REST-API] verwendet, um zusätzliche Informationen über den Benutzer aus AAD abzurufen.

Falls Sie noch nie benutzerdefinierte APIs mit Mobile Services verwendet haben, informieren Sie sich im [Benutzerdefinierte API - Lernprogramm], bevor Sie diesen Abschnitt abschließen.

1. Erstellen Sie im [Azure-Verwaltungsportal] die neue benutzerdefinierte API "GetUserInfo" für Ihren mobilen Dienst, und stellen Sie die Berechtigungen für die Get-Methode auf **Nur authentifizierte Benutzer** ein.

    ![][0]

2. Öffnen Sie den Skripteditor für die neue GetUserInfo-API, und fügen Sie die folgenden Variablen am Anfang des Skripts ein.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Fügen Sie der Funktion `getAADToken` die folgende Definition hinzu.

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

    Anhand der *Mandantendomäne*, der *Client-ID* der integrierten Anwendung sowie des *Anwendungsschlüssels* stellt diese Funktion ein Graph-Zugriffstoken bereit, das zum Lesen der Verzeichnisinformationen verwendet wird.

4. Fügen Sie die folgende `getUser`-Funktion hinzu, welche die Graph-API verwendet, um die Benutzerinformationen zurückzugeben.

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    Diese Funktion ist ein einfacher Wrapper für den [Get User]-Endpunkt der Graph-REST-API. Sie verwendet das Graph-Zugriffstoken, um die Benutzerinformationen mithilfe der Objekt-ID des Benutzers abzurufen.

5. Aktualisieren Sie die exportierte `get`-Methode wie folgt mithilfe der anderen Funktionen, um die Benutzerinformationen zurückzugeben.

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };


## <a name="update-app"></a>Aktualisieren der App zur Verwendung von GetUserInfo


In diesem Abschnitt aktualisieren Sie die `AuthenticateAsync`-Methode, die Sie im Lernprogramm [Erste Schritte mit der Authentifizierung] implementiert haben, um die benutzerdefinierte API aufzurufen und zusätzliche Informationen über den Benutzer aus AAD zurückzugeben. 

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]


 


## <a name="test-app"></a>Testen der App

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]




##<a name="next-steps"></a>Nächste Schritte

m nächsten Lernprogramm mit dem Titel [Rollenbasierte Zugriffssteuerung mit AAD in Mobile Services] verwenden Sie die rollenbasierte Zugriffssteuerung mit Azure Active Directory (AAD), um die Gruppenmitgliedschaft zu prüfen, bevor Sie Zugriff gewähren. 


<!-- Anchors. -->
[Erzeugen eines Zugriffsschlüssels für die App-Registrierung in AAD]: #generate-key
[Erstellen einer benutzerdefinierten API "GetUserInfo"]: #create-api
[Aktualisieren der App zur Verwendung der benutzerdefinierten API]: #update-app
[Testen der App]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[So registrieren Sie sich beim Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Benutzerdefinierte API - Lernprogramm]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
[Speichern von Serverskripts]: /de-de/documentation/articles/mobile-services-store-scripts-source-control/
[Registrieren für die Verwendung einer Azure Active Directory-Anmeldung]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph-API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph-REST-API]: http://msdn.microsoft.com/de-de/library/azure/hh974478.aspx
[Get User]: http://msdn.microsoft.com/de-de/library/azure/dn151678.aspx
[Rollenbasierte Zugriffssteuerung mit AAD in Mobile Services]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
