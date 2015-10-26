<properties
	pageTitle="Verwenden von mehreren Clients mit einem einzelnen mobilen Dienst-Back-End | Azure Mobile Services"
	description="Erfahren Sie, wie Sie ein einzelnes mobiles Dienst-Back-End von mehreren Client-Apps verwenden, die auf verschiedene mobile Plattformen abzielen."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>
<tags
	ms.service="mobile-services" 
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="glenga"/>

# Unterstützen mehrerer Geräteplattformen durch einen einzelnen mobilen Dienst

Einer der Hauptvorteile von Azure Mobile Services bei der Entwicklung mobiler Apps ist die Möglichkeit, mit einem einzigen Back-End-Dienst eine App auf verschiedenen Clientplattformen zu unterstützen. Mobile Services bieten systemeigene Clientbibliotheken für alle großen Geräteplattformen. Dies ermöglicht die einfache App-Entwicklung mit nur einem Back-End-Dienst und plattformübergreifenden Entwicklertools. In diesem Thema werden Aspekte erörtert, wie Sie mithilfe eines einzelnen mobilen Dienst-Back-Ends Ihre App auf mehreren Clientplattformen ausführen können.

##<a id="push"></a>Plattformübergreifende Pushbenachrichtigungen

Mobile Services nutzen Azure Notification Hubs zum Senden von Pushbenachrichtigungen an Ihre Client-Apps auf allen großen Geräteplattformen. Notification Hubs bieten eine konsistente und einheitliche Infrastruktur zur Erstellung und Verwaltung von Geräteregistrierungen und für den Versand plattformübergreifender Pushbenachrichtigungen. Notification Hubs unterstützen das Senden von Pushbenachrichtigungen unter Verwendung der folgenden plattformspezifischen Benachrichtigungsdienste:

+ Apple Push Notification Service (APNS) für iOS-Apps
+ Google Cloud Messaging (GCM) für Android-Apps.
+ Windows-Benachrichtigungsdienst (WNS) für Windows Store, Windows Phone 8.1 Store und universelle Windows-Apps
+ Microsoft-Pushbenachrichtigungsdienst (MPNS) für Windows Phone Silverlight-Apps

Weitere Informationen finden Sie unter [Azure Notification Hubs].

Clientregistrierungen werden mithilfe der Registrierungsfunktion in der plattformspezifischen Mobile Services-Clientbibliothek oder mit REST-APIs von Mobile Services erstellt. Notification Hubs unterstützen zwei Arten von Geräteregistrierungen:

+ **Systemregistrierung**<br/>Systemregistrierungen sind speziell auf den plattformspezifischen Pushbenachrichtigungsdienst zugeschnitten. Beim Versand von Benachrichtigungen an Geräte, die mit einer Systemregistrierung registriert wurden, müssen Sie plattformspezifische APIs in Ihrem mobilen Dienst aufrufen. Zum Senden einer Benachrichtigung an Geräte auf verschiedenen Plattformen sind mehrere plattformspezifische Aufrufe erforderlich.

+ **Vorlagenregistrierung**<br/>Notification Hubs unterstützen auch plattformspezifische Vorlagenregistrierungen. Bei Verwendung von Vorlagenregistrierungen können Sie mit einem einzigen API-Aufruf eine Benachrichtigung an Ihre App auf einer beliebigen registrierten Plattform senden. Weitere Informationen finden Sie unter [Senden plattformübergreifender Benachrichtigungen an Benutzer].

Die Tabellen in den folgenden Abschnitten enthalten Links zu clientspezifischen Lernprogrammen, die die Implementierung von Pushbenachrichtigungen mithilfe mobiler .NET- und JavaScript-Back-End-Dienste veranschaulichen.

###.NET-Back-End

In einem mobilen .NET-Back-End-Dienst senden Sie Pushbenachrichtigungen, indem Sie die [SendAsync]-Methode für das aus der [ApiServices.Push](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx)-Eigenschaft abgerufene [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx)-Objekt aufrufen. Die gesendete Pushbenachrichtigung (system- oder vorlagenbasiert) richtet sich nach dem spezifischen, von [IPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) abgeleiteten Objekt, das an die [SendAsync]-Methode übergeben wird, wie aus der folgenden Tabelle ersichtlich:

|Plattform |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) | MPNS
|-----|-----|----|----|-----|
|Systemeigen|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx) |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx) |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

Mit dem folgenden Code wird eine Pushbenachrichtigung von einem .NET-Back-End-Dienst an alle iOS- und Windows Store-Geräteregistrierungen gesendet:

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";

	// Send push notifications to all registered iOS and Windows Store devices.
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

Um Beispiele zum Senden von Pushbenachrichtigungen an die anderen systemeigenen Clientplattformen zu erhalten, klicken Sie in der Überschrift der oben aufgeführten Tabelle auf die Plattformlinks.

Wenn Sie anstelle von Systemclientregistrierungen Vorlagenclientregistrierungen verwenden, können Sie mit einem einzigen Aufruf von [SendAsync] dieselbe Benachrichtigung senden, indem Sie ein [TemplatePushMessage]-Objekt angeben. Siehe folgendes Beispiel:

	// Create a new template message and add the 'message' parameter.
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload);

###JavaScript-Back-End

In einem mobilen JavaScript-Back-End-Dienst senden Sie Benachrichtigungen, indem Sie die **send**-Methode für das aus dem globalen [push-Objekt] abgerufene plattformspezifische Objekt aufrufen, wie aus der folgenden Tabelle ersichtlich:

|Plattform |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|Systemeigen|[apns-Objekt](http://msdn.microsoft.com/library/azure/jj839711.aspx) |[gcm-Objekt](http://msdn.microsoft.com/library/azure/dn126137.aspx) |[wns-Objekt](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [mpns-Objekt](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

Mit dem folgenden Code wird eine Pushbenachrichtigung an alle Android- und Windows Phone-Registrierungen gesendet:

	// Define a push notification for GCM.
	var gcmPayload =
    '{"data":{"message" : item.text }}';

	// Define the payload for a Windows Phone toast notification.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text +
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Send push notifications to all registered Android and Windows Phone 8.0 devices.
	push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

Um Beispiele zum Senden von Pushbenachrichtigungen an die anderen systemeigenen Clientplattformen zu erhalten, klicken Sie in der Überschrift der oben aufgeführten Tabelle auf die Plattformlinks.

Wenn Sie anstelle von Systemclientregistrierungen Vorlagenclientregistrierungen verwenden, können Sie mit einem einzigen Aufruf der **send**-Funktion für das globale [push-Objekt] dieselbe Benachrichtigung senden, indem Sie eine Vorlagennachricht-Nutzlast angeben, wie im Folgenden veranschaulicht:

	// Create a new template message with the 'message' parameter.
	var templatePayload = { "message": item.text };

	// Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

##<a id="xplat-app-dev"></a>Plattformübergreifende App-Entwicklung
Zur Entwicklung systemeigener mobiler Geräte-Apps für alle großen mobilen Geräteplattformen benötigen Sie (bzw. Ihr Unternehmen) mindestens Kenntnisse in den Programmiersprachen Objective-C, Java und C# oder JavaScript. Da die übergreifende Entwicklung für diese verschiedenartigen Plattformen aufwändig ist, wählen einige Entwickler einen vollständig webbrowserbasierten Ansatz für ihre Apps. Diese webbasierten Lösungen haben jedoch nur eingeschränkten Zugriff auf systemeigene Ressourcen und bieten nicht die Funktionsvielfalt, die Nutzer heute auf ihren mobilen Endgeräten erwarten.

Einige plattformübergreifende Tools gewährleisten eine funktionsreichere systemeigene Benutzeroberfläche auf mobilen Geräten, nutzen jedoch noch eine gemeinsame Codebasis – typischerweise JavaScript. Mobile Services erleichtern die Erstellung und Verwaltung eines Back-End-Diensts für die plattformübergreifende App-Entwicklung durch Schnellstart-Lernprogramme für die folgenden Entwicklungsplattformen:

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Mit Appcelerator können Sie unter Verwendung von JavaScript eine einzelne App entwickeln, die für die Ausführung als systemeigene App auf allen mobilen Geräteplattformen kompiliert ist. Das Ergebnis sind eine attraktive Benutzeroberfläche, Zugriff auf alle systemeigenen Geräteressourcen und eine systemeigene App-Leistung. Weitere Informationen finden Sie im [Appcelerator-Lernprogramm][Appcelerator].

+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (eine Distribution des Apache Cordova-Projekts) ist ein kostenloses Open Source-Framework, mit dem Sie unter Verwendung von standardisierten Web-APIs, HTML und JavaScript eine einzelne, auf Android-, iOS- und Windows-Geräten ausführbare App entwickeln können. PhoneGap bietet eine auf Webansichten basierende Benutzeroberfläche. Die Lösung optimiert die Nutzererfahrung durch den Zugriff auf systemeigene Geräteressourcen wie Pushbenachrichtigungen, Beschleunigungsmesser, Kamera, Speicher, Geolocation und In-App-Browser. Weitere Informationen finden Sie im [PhoneGap-Schnellstart-Lernprogramm][PhoneGap].

	Visual Studio unterstützt jetzt auch die Erstellung plattformübergreifender Cordova-Apps mithilfe der Erweiterung für hybride Multi-Device-Apps für Visual Studio. Diese Software steht als Vorabversion zur Verfügung. Weitere Informationen finden Sie unter [Erste Schritte mit hybriden Multi-Device-Apps unter Verwendung von HTML und JavaScript](http://msdn.microsoft.com/library/dn771545.aspx).

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch umfasst mehrere für Touchscreens optimierte Steuerelemente, die eine mit systemeigenen Lösungen vergleichbare Nutzererfahrung bieten und zahlreiche mobile Geräte auf Grundlage einer einzelnen HTML- und JavaScript-Codebasis unterstützen. Sencha Touch kann zusammen mit PhoneGap- oder Cordova-Bibliotheken verwendet werden, um Benutzern den Zugriff auf systemeigene Geräteressourcen zu ermöglichen. Weitere Informationen finden Sie im [Sencha Touch-Schnellstart-Lernprogramm][Sencha].

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Mit Xamarin erstellen Sie sowohl für iOS- als auch für Android-Geräte vollständig systemeigene Apps mit systemeigener Benutzeroberfläche und Zugriff auf alle Geräteressourcen. Xamarin-Apps werden in C# anstatt in Objective-C und Java programmiert. So können .NET-Entwickler Apps für iOS und Android veröffentlichen und Code aus Windows-Projekten gemeinsam nutzen. Xamarin bietet mittels C#-Code eine vollständig systemeigene Benutzeroberfläche auf iOS- und Android-Geräten. Auf diese Weise lässt sich Mobile Services-Code aus Windows-Apps auf iOS- und Android-Geräten wiederverwenden. Weitere Informationen finden Sie unter [Xamarin-Entwicklung](#xamarin) weiter unten.


<!-- URLs -->
[Azure Management portal]: https://manage.windowsazure.com
[Azure Notification Hubs]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Senden plattformübergreifender Benachrichtigungen an Benutzer]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[How to use a .NET client with Mobile Services]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[push-Objekt]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[What's next for Windows Phone 8 developers]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[Building universal Windows apps for all Windows devices]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Universal Windows app project for Azure Mobile Services using MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!---HONumber=Oct15_HO3-->