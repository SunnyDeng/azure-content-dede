<properties 
	pageTitle="Zugreifen auf Informationen in Azure Active Directory Graph (Windows Store) | Mobile Developer Center" 
	description="Erfahren Sie, wie Sie über die Graph-API in Ihrer Windows Store-Anwendung auf Azure Active Directory-Informationen zugreifen." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="wesmc"/>

# Zugriff auf Azure Active Directory Graph-Informationen

[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]


Wie auch andere Identitätsanbieter, die mit Mobile Services angeboten werden, unterstützt der Azure Active Directory (AAD)-Anbieter eine umfassende [Graph-API], die für programmgesteuerten Zugriff auf das Verzeichnis verwendet werden kann. In diesem Lernprogramm aktualisieren Sie die ToDoList-App, um das App-Erlebnis des authentifizierten Benutzers basierend auf zusätzlichen Benutzerinformationen, die Sie aus dem Verzeichnis mithilfe der [Graph-API] abrufen, zu personalisieren.

>[AZURE.NOTE] Dieses Lernprogramm soll Ihre Kenntnisse bezüglich der Authentifizierung mit Azure Active Directory vertiefen. Es wird davon ausgegangen, dass Sie das Lernprogramm [Erste Schritte mit der Authentifizierung] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters abgeschlossen haben. Dieses Lernprogramm aktualisiert die Anwendung "TodoItem" weiter, die im Lernprogramm [Erste Schritte mit der Authentifizierung] verwendet wurde.



In diesem Lernprogramm werden die folgenden Schritte behandelt:


1. [Generieren eines Zugriffsschlüssels für die App-Registrierung in AAD] 
2. [Erstellen einer benutzerdefinierten GetUserInfo-API] 
3. [Aktualisieren der App zur Verwendung der benutzerdefinierten API]
4. [Testen der App]

##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App "TodoList" hinzu.

+ [Lernprogramm zur benutzerdefinierten API]<br/>Erläutert das Aufrufen einer benutzerdefinierten API. 



## <a name="generate-key"></a>Generieren eines Zugriffsschlüssels für die App-Registrierung in AAD


Im Lernprogramm [Erste Schritte mit der Authentifizierung] haben Sie eine Registrierung für die integrierte Anwendung erstellt, als Sie den Schritt [Registrieren für die Verwendung einer Azure Active Directory-Anmeldung] durchgeführt haben. In diesem Abschnitt erzeugen Sie einen Zugriffsschlüssel, der beim Lesen der Verzeichnisinformationen mit der Client-ID der integrierten Anwendung verwendet wird. 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-api"></a>Erstellen einer benutzerdefinierten GetUserInfo-API

In diesem Abschnitt erstellen Sie die benutzerdefinierte GetUserInfo-API, welche die [Graph-REST-API] verwendet, um zusätzliche Informationen über den Benutzer aus AAD abzurufen.

Falls Sie noch nie benutzerdefinierte APIs mit Mobile Services verwendet haben, informieren Sie sich im [Lernprogramm zur benutzerdefinierten API], bevor Sie diesen Abschnitt abschließen.

1. Erstellen Sie im [Azure-Verwaltungsportal]die neue benutzerdefinierte GetUserInfo-API für Ihren mobilen Dienst, und stellen Sie die Berechtigungen für die Get-Methode auf **Nur authentifizierte Benutzer** ein.

    ![][0]

2. Öffnen Sie den Skripteditor für die neue GetUserInfo-API, und fügen Sie die folgenden Variablen am Anfang des Skripts ein.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Fügen Sie der `getAADToken`-Funktion die folgende Definition hinzu.

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

    Anhand der *tenant_domain*, der integrierten *client id* der Anwendung, und dem *key* der Anwendung stellt diese Funktion ein Graph-Zugriffstoken bereit, das zum Lesen der Verzeichnisinformationen verwendet wird.

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

    Diese Funktion ist ein einfacher Wrapper für den[Get User]-Endpunkt der Graph-REST-API. Sie verwendet das Graph-Zugriffstoken, um die Benutzerinformationen mithilfe der Objekt-ID des Benutzers abzurufen.

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

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]


 


## <a name="test-app"></a>Testen der App

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]




##<a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm, [Rollenbasierte Zugriffssteuerung mit AAD in Mobile Services], verwenden Sie rollenbasierte Zugriffssteuerung mit Azure Active Directory (AAD), um die Gruppenmitgliedschaft zu prüfen, bevor Sie Zugriff gewähren. 


<!-- Anchors. -->
[Generieren eines Zugriffsschlüssels für die App-Registrierung in AAD]: #generate-key
[Erstellen einer benutzerdefinierten GetUserInfo-API]: #create-api
[Aktualisieren der App zur Verwendung der benutzerdefinierten API]: #update-app
[Testen der App]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Registrieren mit Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Lernprogramm zur benutzerdefinierten API]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
[Speichern von Serverskripts]: /de-de/documentation/articles/mobile-services-store-scripts-source-control/
[Registrieren für die Verwendung einer Azure Active Directory-Anmeldung]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph-API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph-REST-API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Rollenbasierte Zugriffssteuerung mit AAD in Mobile Services]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/



<!--HONumber=42-->
