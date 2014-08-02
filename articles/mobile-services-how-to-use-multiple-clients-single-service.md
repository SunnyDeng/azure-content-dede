<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerations for supporting multiple clients from a single mobile service" authors="krisragh" solutions="" manager="" editor="" />

Überlegungen zur Unterstützung mehrerer Clients in einem einzigen Mobile Service
================================================================================

Einer der Hauptvorteile von Azure Mobile Services für die Entwicklung mobiler Apps ist die Möglichkeit, mit einem einzigen Back-End-Dienst eine App auf verschiedenen Client-Plattformen zu unterstützen. Mobile Services bietet native Clientbibliotheken für alle großen Geräteplattformen an. Weitere Informationen finden Sie unter [Lernprogramme und Ressourcen](/en-us/develop/mobile/resources/).

Obwohl Mobile Services die Migration Ihrer nativen App auf verschiedene Clientplattformen mit einem einzigen Back-End-Dienst erleichtert, sollten Sie bei ihrer Planung einige Überlegungen anstellen. Dieser Artikel beschreibt die Implementierung von Pushbenachrichtigungen für all Ihre Clientplattformen. Er beschreibt außerdem, wie Sie ein Problem bei der vom Client angewiesenen einmaligen Anmeldung mit einem Microsoft-Konto in Windows Store- und Windows Phone-Apps umgehen können. Zuletzt finden Sie einige bewährte Methoden für die Wiederverwendung von Code in Visual Studio-Projekten.

Pushbenachrichtigungen
----------------------

Dieser Abschnitt beschreibt zwei Ansätze zum Senden von Pushbenachrichtigungen von Ihrem Mobile Service an Client-Apps auf verschiedenen Plattformen.

### Verwenden von Notification Hubs

Azure Notification Hubs ist ein Azure-Dienst und eine skalierbare Plattform für den Versand von Pushbenachrichtigungen von Ihrem Mobile Service (oder anderen Back-Ends) an Client-Apps auf allen großen Geräteplattformen. Weitere Informationen finden Sie unter [Azure Notification Hubs](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/).

Notification Hubs bieten eine konsistente und einheitliche Infrastruktur zur Erstellung und Verwaltung von Geräteregistrierungen und für den Versand von Pushbenachrichtigungen an Geräte auf allen großen Geräteplattformen. Weitere Informationen finden Sie unter [Erste Schritte mit Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/). Notification Hubs unterstützt plattformspezifische Vorlagenregistrierungen, über die Sie mit einem einzigen API-Aufruf eine Benachrichtigung an Ihre APP auf einer beliebigen registrierten Plattform senden können. Weitere Informationen finden Sie unter [Senden plattformübergreifender Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/).

Notification Hubs ist die empfohlene Lösung für den Versand von Benachrichtigungen von Ihrem Mobile Service an verschiedene Clientplattformen.

### Verwenden einer gemeinsamen Registrierungstabelle und eines Plattform-Bezeichners

Falls Sie Notification Hubs nicht verwenden möchten, können Sie dennoch Pushbenachrichtigungen an verschiedene Clients von Ihrem Mobile Service unterstützen, indem Sie einen gemeinsamen Mechanismus für die Geräteregistrierung anbieten. Sie können eine einzige Tabelle verwenden, um die plattformspezifischen Informationen für den Pushbenachrichtigungs-Dienst für Ihre unterstützte Plattform zu speichern. Die Lernprogramme **Erste Schritte mit Pushbenachrichtigungen** ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/)) verwenden eine **Registrations**-Tabelle und speichern die Channel-URI (Windows), Geräte-Token (iOS) bzw. Handle (Android) in einer Spalte namens *handle*.

**Hinweis**

Wenn Sie den "Add Push Notification"-Assistenten in Visual Studio 2013 zum Hinzufügen von Pushbenachrichtigungen zu einer Windows Store-App verwenden, erstellt der Assistent automatisch eine Tabelle namens **Channel**, um die Channel-URIs zu speichern. Im Lernprogramm **Erste Schritte mit Pushbenachrichtigungen in Visual Studio 2012** ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012)/[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012)) erfahren Sie, wie Sie Pushbenachrichtigungen mithilfe der **Registrations**-Tabelle aktivieren können.

Fügen Sie eine Spalte *platform* zur Tabelle hinzu, um mehrere Clients in einer einzigen Registrierungstabelle zu unterstützen, und fügen Sie bei der Registrierung die Plattform des Clients in diese Spalte ein. Die folgende **Registrations**-Klassendefinition für eine Windows Store C\#- oder Windows Phone-App ordnet das *ChannelUri*-Feld des Clients zur *handle*-Spalte in der Registrierungstabelle zu:

     public class Registrations
        {
            [JsonProperty(PropertyName = "platform")]         
            public string Platform { 
                get
                {
                    return "windowsstore";
                    // return "windowsphone";
                }
                set {}
            }
            
            public string Id { get; set; }
        
            [JsonProperty(PropertyName = "handle")]
            public string ChannelUri { get; set; }
        }

Beachten Sie, dass das *Platform*-Feld auf diesem Windows-Gerät immer `windowsstore` (bzw. `windowsphone`) zurückgibt. Wenn das dynamische Schema (Standard) aktiviert ist, fügt Mobile Services eine platform-Spalte zur Registrierungstabelle hinzu, falls diese noch nicht existiert. Weitere Informationen finden Sie unter [Dynamisches Schema](http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx).

Verwenden Sie das platform-Feld in Ihrem Serverskript, das die Benachrichtigungen verschickt, um zu bestimmen, welche plattformspezifische Funktion Sie auf dem [push-Object](http://msdn.microsoft.com/en-us/library/windowsazure/jj554217.aspx) aufrufen müssen. Das folgende Skript ist eine abgewandelte Form des Serverskripts aus den **Erste Schritte mit Pushbenachrichtigungen**-Lernprogrammen ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/)), um verschiedene Clientplattformen zu aktivieren:

     function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });
        
            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            if (registration.platform === 'winstore') {
                                push.wns.sendToastText04(registration.handle, {
                                    text1: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'winphone') {
                                push.mpns.sendFlipTile(registration.handle, {
                                    title: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'ios') {
                                push.apns.send(registration.handle, {
                                    alert: "Toast: " + item.text,
                                    payload: {
                                        inAppMessage: item.text
                                    }
                                });
                            } else if (registration.platform === 'android') {
                                push.gcm.send(registration.handle, item.text, {
                                    success: pushCompleted
                                });
                            } else {
                                console.error("Unknown push notification platform");
                            }
                        });
                    }
                });
            }
        
            function pushCompleted(pushResponse) {
                console.log("Sent push:", pushResponse);
            }
        }

Windows App-Registrierung
-------------------------

Um die Clientauthentifizierung für einmaliges Anmelden mit einem Microsoft-Konto in Windows Store- und Windows Phone-Apps zu verwenden, müssen Sie die Windows Store-App zunächst im Windows Store-Dashboard registrieren. Dies liegt daran, dass Sie nach der Erstellung einer Live Connect-Registrierung für Windows Phone keine weitere Registrierung für Windows Store erstellen können. Weitere Informationen hierzu finden Sie im Thema **Authentifizieren Ihrer Windows Store-App mit einmaliger Anmeldung von Live Connect** ([Windows Store](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)).

Bewährte Methoden für die Wiederverwendung von Code in Visual Studio-Projekten
------------------------------------------------------------------------------

Mit portablen Klassenbibliotheken können Sie verwaltete Assemblys schreiben und erstellen, die auf mehreren Plattformen funktionieren, wie z. B. Windows Store und Windows Phone. Sie können Klassen mit Code erstellen, den Sie später in vielen verschiedenen Projekten verwenden, und aus verschiedenen Typen von Projekten auf diese Klassen verweisen.

Sie können die portable .NET Framework-Klassenbibliothek verwenden, um das Model-View-View Model (MVVM)-Schema zu implementieren und Assemblys auf verschiedenen Plattformen zu nutzen. Sie können die Model- und View Model-Klassen in einem Portable Class Library-Projekt in Visual Studio 2012 implementieren und anschließend die für verschiedene Plattformen angepassten Ansichten erstellen. Der plattformübergreifende Model-Code kann z. B. Daten aus einer Quelle wie einem Azure Mobile Service auf plattformunabhängige Weise abrufen. In der MSDN-Bibliothek finden Sie eine [Übersicht und Beispiele](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110)), eine Besprechung von [API-Unterschieden](http://msdn.microsoft.com/en-us/library/gg597392(v=vs.110)), ein Beispiel für [die Verwendung von portablen Klassenbibliotheken zur Implementierung des MVVM-Schemas](http://msdn.microsoft.com/en-us/library/hh563947(v=vs.110)) (in englischer Sprache), zusätzliche [empfohlene Anleitungen](http://msdn.microsoft.com/en-us/library/windowsphone/develop/jj714086(v=vs.105).aspx) und Informationen zur [Ressourcenverwaltung](http://msdn.microsoft.com/en-us/library/hh871422(v=vs.110)) in portablen Klassenbibliothek-Projekten.

