<properties 
	pageTitle="Verwenden mehrerer Clients mit einzelnem Mobile Service-Back-End | Azure Mobile" 
	description="Erfahren Sie, wie Sie ein einzelnes mobiles Dienst-Back-End von mehreren Client-Apps verwenden, die auf verschiedene mobile Plattformen, einschließlich Windows Store und Windows Phone, abzielen." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>
<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="glenga"/>

# Unterstützen mehrerer Geräteplattformen durch einen einzelnen mobilen Dienst
 
Einer der Hauptvorteile von Azure Mobile Services bei der Entwicklung mobiler Apps ist die Möglichkeit, mit einem einzigen Back-End-Dienst eine App auf verschiedenen Clientplattformen zu unterstützen. Mobile Services bieten systemeigene Clientbibliotheken für alle großen Geräteplattformen. Dies ermöglicht die einfache App-Entwicklung mit nur einem Back-End-Dienst und plattformübergreifenden Entwicklertools. In diesem Thema werden Aspekte erörtert, wie Sie mithilfe eines einzelnen mobilen Dienst-Back-Ends Ihre App auf mehreren Clientplattformen ausführen können. Allgemeinere Informationen zu Mobile Services finden Sie im [Mobile Services-Entwicklungscenter](/documentation/services/mobile-services/).

## <a id="push"></a>Plattformübergreifende Pushbenachrichtigungen

Mobile Services nutzen Azure Notification Hubs zum Senden von Pushbenachrichtigungen an Ihre Client-Apps auf allen großen Geräteplattformen. Notification Hubs bieten eine konsistente und einheitliche Infrastruktur zur Erstellung und Verwaltung von Geräteregistrierungen und für den Versand plattformübergreifender Pushbenachrichtigungen. Notification Hubs unterstützen das Senden von Pushbenachrichtigungen unter Verwendung der folgenden plattformspezifischen Benachrichtigungsdienste:

+ Apple Push Notification Service (APNS) für iOS-Apps
+ Google Cloud Messaging (GCM) für Android-Apps.
+ Windows-Benachrichtigungsdienst (WNS) für Windows Store, Windows Phone 8.1 Store und universelle Windows-Apps 
+ Microsoft-Pushbenachrichtigungsdienst (MPNS) für Windows Phone Silverlight-Apps

>[AZURE.NOTE]Die Verwendung von WNS zum Senden von Pushbenachrichtigungen an Windows Phone Silverlight 8.1-Apps wird von Notification Hubs derzeit nicht unterstützt. Zum Senden von Benachrichtigungen an Silverlight- und Windows Phone 8.0- und 7.0-Apps muss MPNS verwendet werden.

Weitere Informationen finden Sie unter [Azure Notification Hubs].

Clientregistrierungen werden mithilfe der Registrierungsfunktion in der plattformspezifischen Mobile Services-Clientbibliothek oder mit REST-APIs von Mobile Services erstellt. Notification Hubs unterstützen zwei Arten von Geräteregistrierungen:

+ **Systemregistrierung**<br/>Systemregistrierungen sind speziell auf den plattformspezifischen Pushbenachrichtigungsdienst zugeschnitten. Beim Versand von Benachrichtigungen an Geräte, die mit einer Systemregistrierung registriert wurden, müssen Sie plattformspezifische APIs in Ihrem mobilen Dienst aufrufen. Zum Senden einer Benachrichtigung an Geräte auf verschiedenen Plattformen sind mehrere plattformspezifische Aufrufe erforderlich.   
  
+ **Vorlagenregistrierung**<br/>Notification Hubs unterstützen auch plattformspezifische Vorlagenregistrierungen. Bei Verwendung von Vorlagenregistrierungen können Sie mit einem einzigen API-Aufruf eine Benachrichtigung an Ihre App auf einer beliebigen registrierten Plattform senden. Weitere Informationen finden Sie unter [Senden plattformübergreifender Benachrichtigungen an Benutzer].

>[AZURE.NOTE]Der Versuch, eine Nachricht an eine systemeigene Geräteplattform zu senden, für die keine Geräteregistrierung vorhanden ist, verursacht einen Fehler. Beim Versand von Vorlagenbenachrichtigungen tritt dieser Fehler nicht auf.

Die Tabellen in den folgenden Abschnitten enthalten Links zu clientspezifischen Lernprogrammen, die die Implementierung von Pushbenachrichtigungen mithilfe mobiler .NET- und JavaScript-Back-End-Dienste veranschaulichen.  

### .NET-Back-End

In einem mobilen .NET-Back-End-Dienst senden Sie Pushbenachrichtigungen, indem Sie die [SendAsync]-Methode für das aus der [ApiServices.Push](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx)-Eigenschaft abgerufene [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx)-Objekt aufrufen. Die gesendete Pushbenachrichtigung (system- oder vorlagenbasiert) richtet sich nach dem spezifischen, von [IPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) abgeleiteten Objekt, das an die [SendAsync]-Methode übergeben wird, wie aus der folgenden Tabelle ersichtlich: 

|Plattform |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-dotnet-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|Systemeigen|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx)   |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx)     |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

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
 
### JavaScript-Back-End

In einem mobilen JavaScript-Back-End-Dienst senden Sie Benachrichtigungen, indem Sie die **send**-Methode für das aus dem globalen [push-Objekt] abgerufene plattformspezifische Objekt aufrufen, wie aus der folgenden Tabelle ersichtlich: 

|Plattform |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|Systemeigen|[apns-Objekt](http://msdn.microsoft.com/library/azure/jj839711.aspx)   |[gcm-Objekt](http://msdn.microsoft.com/library/azure/dn126137.aspx)     |[wns-Objekt](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [mpns-Objekt](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

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

## <a id="xplat-app-dev"></a>Plattformübergreifende App-Entwicklung
Zur Entwicklung systemeigener mobiler Geräte-Apps für alle großen mobilen Geräteplattformen benötigen Sie (bzw. Ihr Unternehmen) mindestens Kenntnisse in den Programmiersprachen Objective-C, Java und C# oder JavaScript. Da die übergreifende Entwicklung für diese verschiedenartigen Plattformen aufwändig ist, wählen einige Entwickler einen vollständig webbrowserbasierten Ansatz für ihre Apps. Diese webbasierten Lösungen haben jedoch nur eingeschränkten Zugriff auf systemeigene Ressourcen und bieten nicht die Funktionsvielfalt, die Nutzer heute auf ihren mobilen Endgeräten erwarten.  

Einige plattformübergreifende Tools gewährleisten eine funktionsreichere systemeigene Benutzeroberfläche auf mobilen Geräten, nutzen jedoch noch eine gemeinsame Codebasis - typischerweise JavaScript. Mobile Services erleichtern die Erstellung und Verwaltung eines Back-End-Diensts für die plattformübergreifende App-Entwicklung durch Schnellstart-Lernprogramme für die folgenden Entwicklungsplattformen: 

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Mit Appcelerator können Sie unter Verwendung von JavaScript eine einzelne App entwickeln, die für die Ausführung als systemeigene App auf allen mobilen Geräteplattformen kompiliert ist. Das Ergebnis sind eine attraktive Benutzeroberfläche, Zugriff auf alle systemeigenen Geräteressourcen und eine systemeigene App-Leistung. Weitere Informationen finden Sie im [Appcelerator-Lernprogramm][Appcelerator].
 
+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (eine Distribution des Apache Cordova-Projekts) ist ein kostenloses Open Source-Framework, mit dem Sie unter Verwendung von standardisierten Web-APIs, HTML und JavaScript eine einzelne, auf Android-, iOS- und Windows-Geräten ausführbare App entwickeln können. PhoneGap bietet eine auf Webansichten basierende Benutzeroberfläche. Die Lösung optimiert die Nutzererfahrung durch den Zugriff auf systemeigene Geräteressourcen wie Pushbenachrichtigungen, Beschleunigungsmesser, Kamera, Speicher, Geolocation und In-App-Browser. Weitere Informationen finden Sie im [PhoneGap-Schnellstart-Lernprogramm][PhoneGap]. 
	
	Visual Studio unterstützt jetzt auch die Erstellung plattformübergreifender Cordova-Apps mithilfe der Erweiterung für hybride Multi-Device-Apps für Visual Studio. Diese Software steht als Vorabversion zur Verfügung. Weitere Informationen finden Sie unter [Erste Schritte mit hybriden Multi-Device-Apps unter Verwendung von HTML und JavaScript](http://msdn.microsoft.com/library/dn771545.aspx). 

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch umfasst mehrere für Touchscreens optimierte Steuerelemente, die eine mit systemeigenen Lösungen vergleichbare Nutzererfahrung bieten und zahlreiche mobile Geräte auf Grundlage einer einzelnen HTML- und JavaScript-Codebasis unterstützen. Sencha Touch kann zusammen mit PhoneGap- oder Cordova-Bibliotheken verwendet werden, um Benutzern den Zugriff auf systemeigene Geräteressourcen zu ermöglichen. Weitere Informationen finden Sie im [Sencha Touch-Schnellstart-Lernprogramm][Sencha].

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Mit Xamarin erstellen Sie sowohl für iOS- als auch für Android-Geräte vollständig systemeigene Apps mit systemeigener Benutzeroberfläche und Zugriff auf alle Geräteressourcen. Xamarin-Apps werden in C# anstatt in Objective-C und Java programmiert. So können .NET-Entwickler Apps für iOS und Android veröffentlichen und Code aus Windows-Projekten gemeinsam nutzen. Xamarin bietet mittels C#-Code eine vollständig systemeigene Benutzeroberfläche auf iOS- und Android-Geräten. Auf diese Weise lässt sich Mobile Services-Code aus Windows-Apps auf iOS- und Android-Geräten wiederverwenden. Weitere Informationen finden Sie unter [Xamarin-Entwicklung](#xamarin) weiter unten. 

	Sie können Xamarin-Apps entweder mit Xamarin Studio oder Visual Studio 2013 erstellen. Weitere Informationen finden Sie unter [Plattformübergreifende Entwicklung in Visual Studio](http://msdn.microsoft.com/library/dn771552.aspx).


## <a id="shared-vs"></a>Gemeinsame Nutzung und Wiederverwendung von Code in Visual Studio-Projekten

Mobile Services enthalten eine .NET-Clientbibliothek in Form einer portablen .NET Framework-Klassenbibliothek, die die Entwicklung auf allen Windows-Plattformen unterstützt. Weitere Informationen finden Sie unter [Verwenden eines .NET-Clients mit Mobile Services]. Auf diese Weise kann derselbe Mobile Services-Code, z. B. für den Datenzugriff oder die Authentifizierung, in mehreren C#-Projekten einfach wiederverwendet werden.

Eine allgemeine Herangehensweise für die gemeinsame Nutzung und Wiederverwendung von C#-Code zwischen Projekten besteht darin, das Model-View-ViewModel (MVVM)-Muster zu implementieren und Assemblys plattformübergreifend gemeinsam zu nutzen. Sie können die Model- und View Model-Klassen in einem Projekt für eine portable Klassenbibliothek in Visual Studio implementieren und anschließend die an verschiedene Plattformen angepassten Ansichten erstellen. Der plattformübergreifende Model-Code kann z. B. Daten aus einer Quelle wie Ihrem mobilen Dienst auf plattformunabhängige Weise abrufen. Die MSDN-Bibliothek bietet eine <a href="http://msdn.microsoft.com/library/gg597391(v=vs.110)">Übersicht und ein Beispiel</a>, eine Beschreibung von <a href="http://msdn.microsoft.com/library/gg597392(v=vs.110)">API-Unterschieden</a>, ein Beispiel zur <a href="http://msdn.microsoft.com/library/hh563947(v=vs.110)">Verwendung portabler Klassenbibliotheken zum Implementieren des MVVM-Musters</a>, eine zusätzliche <a href="http://msdn.microsoft.com/library/windowsphone/develop/jj714086(v=vs.105).aspx">Anleitung</a>und Informationen zum <a href="http://msdn.microsoft.com/library/hh871422(v=vs.110)">Verwalten von Ressourcen</a> in portablen Klassenbibliotheksprojekten.

Neben diesen allgemeinen Erläuterungen bietet Visual Studio auch spezifische Möglichkeiten, Code aus mobilen Diensten übergreifend über mehrere Client-App-Projekte wiederzuverwenden, wie in den folgenden Abschnitten beschrieben. Allgemeinere Informationen zur Verwendung von Visual Studio 2013 zum Erstellen plattformübergreifender Apps finden Sie unter [Plattformübergreifende Entwicklung in Visual Studio](http://msdn.microsoft.com/library/dn771552.aspx).  

### Universelle Windows-Apps

Visual Studio 2013 Update 2 bietet erstmalig Unterstützung für universelle Windows-App-Projekte. Universelle Apps sind Lösungen, die sowohl Windows Store 8.1- und Windows Phone Store 8.1-App-Projekte als auch ein Projekt mit freigegebenem Code enthalten. In diesen Projekten wird freigegebener Code behandelt, als wäre er Teil von Windows Store- und von Windows Phone-Projekten. Weitere Informationen finden Sie unter [Erstellen universeller Windows-Apps für alle Windows-Geräte]. Universelle Windows-Apps können in C#/XAML und JavaScript/HTML geschrieben werden. 

Auf der Schnellstartregisterkarte "Mobiler Service" im [Azure-Verwaltungsportal] wird die TodoList-Beispiel-App standardmäßig in der Version einer universellen Windows-App generiert, um Sie bei Ihren ersten Schritten zu unterstützen. Sie können entweder eine C#/XAML- oder eine JavaScript/HTML-Version des Projekts herunterladen. Weitere Informationen finden Sie unter [Erste Schritte mit Mobile Services](mobile-services-windows-store-get-started.md). 

>[AZURE.NOTE]Die C#-Version des Schnellstart-App-Projekts aus dem Portal nutzt zwar die CodeBehind-Seite "MainPage.xaml.cs", verwendet aber kein Anzeigemodell. Ein Beispiel der TodoList-App als Projekt für eine universelle Windows-App in C#, das MVVM verwendet, finden Sie unter [Projekt für eine universelle Windows-App für Azure Mobile Services unter Verwendung von MVVM]. 

### <a id="xamarin"></a>Xamarin-Entwicklung

Mit Xamarin können Sie Ihre Kenntnisse in der Visual Studio- und C#-Entwicklung nutzen und Apps für iOS und Android in Visual Studio oder Xamarin Studio entwickeln. Xamarin verwendet eine plattformübergreifende Implementierung von .NET Framework, die die Entwicklung von iOS- und Android-Apps mit C#-Code ermöglicht. Mithilfe von Xamarin können Sie vorhandenen Code aus Windows-Projekten nutzen, der die .NET-Clientbibliothek von Mobile Services für den Zugriff auf Ihren mobilen Dienst verwendet.  Weitere Informationen finden Sie unter [Plattformübergreifende Entwicklung in Visual Studio](http://msdn.microsoft.com/library/dn771552.aspx).

In den Xamarin-Schnellstart-Lernprogrammen ([iOS](partner-xamarin-mobile-services-ios-get-started.md)/[Android](partner-xamarin-mobile-services-android-get-started.md))) erfahren Sie, wie Sie Ihre ersten Xamarin-Apps erstellen, die Mobile Services verwenden.


### Windows Store- und Windows Phone Silverlight-Apps

In Windows Phone 8.1 können Sie Apps mit der früheren Silverlight-basierten XAML entwickeln oder die Windows-Runtime-basierte XAML verwenden, die die Entwicklung universeller Windows-Apps unterstützt. Weitere Informationen zu Windows Phone 8.1 Silverlight-Apps und Windows Phone Store 8.1-Apps finden Sie unter [Nächste Schritte für Windows Phone 8-Entwickler]. 

Die .NET-Clientbibliothek von Mobile Services unterstützt sowohl Windows Phone Store 8.1-Apps als auch Windows Phone Silverlight 8.1-Apps. Da Windows-Runtime- und Windows Phone Silverlight-Apps nicht im selben Projekt erstellt werden können, sollten Sie eine Wiederverwendungsstrategie für Code wie den oben beschriebenen PCL- und MVVM-Ansatz in Erwägung ziehen.

>[AZURE.NOTE]Um die Clientauthentifizierung für einmaliges Anmelden mit einem Microsoft-Konto in Windows-Runtime- und Windows Phone Silverlight-Apps zu verwenden, müssen Sie die Windows-Runtime-App zunächst im Windows Store-Dashboard registrieren. Dies liegt daran, dass Sie nach der Erstellung einer Live Connect-Registrierung für Windows Phone keine weitere Registrierung für Windows Store erstellen können. Weitere Informationen hierzu finden Sie im Thema **Authentifizieren Ihrer Windows Store-App mit einmaliger Anmeldung von Live Connect** ([Windows Store][SSO Windows Store]/[Windows Phone][SSO Windows Phone]).


<!-- URLs -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[Azure Notification Hubs]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Lernprogramme und Ressourcen]: /develop/mobile/resources/
[Erste Schritte mit Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Senden plattformübergreifender Benachrichtigungen an Benutzer]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Erste Schritte mit push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Erste Schritte mit push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Erste Schritte mit push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Erste Schritte mit push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Erste Schritte mit push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamisches Schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[Verwenden eines .NET-Clients mit Mobile Services]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[push-Objekt]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]:http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: /documentation/articles/mobile-services-javascript-backend-phonegap-get-started/
[Sencha]: /documentation/articles/partner-sencha-mobile-services-get-started/
[Appcelerator]: /documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[Nächste Schritte für Windows Phone 8-Entwickler]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[Erstellen universeller Windows-Apps für alle Windows-Geräte]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Projekt für eine universelle Windows-App für Azure Mobile Services unter Verwendung von MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!--HONumber=47-->
