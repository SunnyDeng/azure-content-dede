<properties 
	pageTitle="Erstellen einer Brücke zwischen iOS WebView und dem nativem Mobile Engagement iOS SDK" 
	description="Beschreibt das Erstellen einer Brücke zwischen WebView unter Javascript und dem nativen Mobile Engagement iOS SDK."		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="piyushjo" />

#Erstellen einer Brücke zwischen iOS WebView und dem nativem Mobile Engagement iOS SDK

> [AZURE.SELECTOR]
- [Android-Brücke](mobile-engagement-bridge-webview-native-android.md)
- [iOS-Brücke](mobile-engagement-bridge-webview-native-ios.md)

Einige mobile Apps werden als Hybrid-Apps konzipiert, das heißt, die App selbst wird mit der nativen iOS Objective-C-Entwicklung entwickelt, aber einige oder sogar alle Bildschirme werden in einer iOS WebView gerendert. Sie können das Mobile Engagement iOS SDK dennoch innerhalb solcher Apps nutzen, und dieses Tutorial zeigt Ihnen, wie das geht.

Zu diesem Zweck gibt es zwei Ansätze, die jedoch nicht dokumentiert sind:

- Der erste wird unter diesem [Link](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) beschrieben. Dazu gehört das Registrieren eines `UIWebViewDelegate` in Ihrer Webansicht sowie das Abfangen und sofortige Abbrechen einer Standortänderung in Javascript. 
- Der zweite Ansatz basiert auf dieser [WWDC 2013-Sitzung](https://developer.apple.com/videos/play/wwdc2013/615). Dieser Ansatz ist sauberer als der erste, und wir werden ihn in dieser Anleitung verwenden. Beachten Sie, dass dieser Ansatz nur für iOS7 und höher funktioniert. 

Führen Sie für die iOS-Beispielbrücke die folgenden Schritte aus:

1. Zunächst müssen Sie sicherstellen, dass Sie unser [Erste-Schritte-Tutorial](mobile-engagement-ios-get-started.md) zum Integrieren des Mobile Engagement iOS SDK in der Hybrid-App durchgeführt haben. Optional können Sie auch die Testprotokollierung wie folgt aktivieren, damit die SDK-Methoden angezeigt werden, während wir sie von der WebView aus auslösen. 
    
		- (BOOL)application:(UIApplication ​*)application didFinishLaunchingWithOptions:(NSDictionary *​)launchOptions {
		   ....
     		[EngagementAgent setTestLogEnabled:YES];
		   ....
		}

2. Stellen Sie jetzt sicher, dass die Hybrid-App einen Bildschirm mit einer WebView enthält. Fügen Sie ihn dem `Main.storyboard` der App hinzu.

3. Ordnen Sie diese WebView Ihrem **ViewController** zu, indem Sie die WebView aus der View Controller Scene auf den `ViewController.h`-Bearbeitungsbildschirm ziehen und direkt unterhalb der `@interface`-Zeile platzieren.

4. Sofort wird ein Dialogfeld zur Eingabe eines Namens geöffnet. Geben Sie den Namen **webView** an. Ihre Datei `ViewController.h` sollte folgendermaßen aussehen:

		#import <UIKit/UIKit.h>
		#import "EngagementViewController.h"
		
		@interface ViewController : EngagementViewController
		@property (strong, nonatomic) IBOutlet UIWebView *webView;
		
		@end

5. Wir aktualisieren die Datei `ViewController.m` später. Zunächst erstellen wir die Brückendatei, die einen Wrapper über einige häufig verwendete Methoden des Mobile Engagement iOS SDK erstellt. Erstellen Sie eine neue Headerdatei namens **EngagementJsExports.h**, die den in der oben genannten [Sitzung](https://developer.apple.com/videos/play/wwdc2013/615) beschriebenen `JSExport`-Mechanismus verwendet, um die nativen iOS-Methoden verfügbar zu machen.

		#import <Foundation/Foundation.h>
		#import <JavaScriptCore/JavascriptCore.h>
		
		@protocol EngagementJsExports <JSExport>
		
		+ (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
		+ (void) startEngagementJob:(NSString*) name :(NSString*)extras;
		+ (void) endEngagementJob:(NSString*) name;
		+ (void) sendEngagementError:(NSString*) name :(NSString*)extras;
		+ (void) sendEngagementAppInfo:(NSString*) appInfo;
		
		@end

		@interface EngagementJs : NSObject <EngagementJsExports>

		@end

6. Als Nächstes erstellen wir den zweiten Teil der Brückendatei. Erstellen Sie eine Datei namens **EngagementJsExports.m** mit der Implementierung, die die eigentlichen Wrapper erstellt, indem die Methoden des Mobile Engagement iOS SDK aufgerufen werden. Beachten Sie auch, dass wir die aus dem WebView-JavaScript übergebenen `extras` analysieren und diese in ein `NSMutableDictionary` -Objekt einfügen, das mit den Engagement SDK-Methodenaufrufen übergeben wird.

		#import <UIKit/UIKit.h>
		#import "EngagementAgent.h"
		#import "EngagementJsExports.h"
		
		@implementation EngagementJs
		
		+(void) sendEngagementEvent:(NSString​*)name :(NSString*​)extras {
		   NSMutableDictionary* extrasInput = [self ParseExtras:extras];
		   [[EngagementAgent shared] sendEvent:name extras:extrasInput];
		}
		
		+ (void) startEngagementJob:(NSString*) name :(NSString*)extras {
		   NSMutableDictionary* extrasInput = [self ParseExtras:extras];
		   [[EngagementAgent shared] startJob:name extras:extrasInput];
		}
		
		+ (void) endEngagementJob:(NSString*) name {
		   [[EngagementAgent shared] endJob:name];
		}
		
		+ (void) sendEngagementError:(NSString*) name :(NSString*)extras {
		   NSMutableDictionary* extrasInput = [self ParseExtras:extras];
		   [[EngagementAgent shared] sendError:name extras:extrasInput];
		}
		
		+ (void) sendEngagementAppInfo:(NSString*) appInfo {
		   NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
		   [[EngagementAgent shared] sendAppInfo:appInfoInput];
		}
		
		+ (NSMutableDictionary*) ParseExtras:(NSString*) input {
		   NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
		   NSError* error = nil;
		   NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
		   
		   return extras;
		}
		
		@end

5. Jetzt kommen wir zur Datei **ViewController.m** zurück und aktualisieren sie mit dem folgenden Code:
		
		#import <JavaScriptCore/JavaScriptCore.h>
		#import "ViewController.h"
		#import "EngagementJsExports.h"
		
		@interface ViewController ()
		
		@end
		
		@implementation ViewController
		
		- (void)viewDidLoad
		{
		   self.webView.delegate = self;
		   [super viewDidLoad];
		   [self loadWebView];
		}
		
		- (void)loadWebView {
		   NSBundle* mainBundle = [NSBundle mainBundle];
		   NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
		   
		   NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
		   [self.webView loadRequest:urlReq];
		}
		
		- (void)webViewDidFinishLoad:(UIWebView*)wv
		{
		   JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
		   
		   context[@"EngagementJs"] = [EngagementJs class];
		}
		
		- (void)webView:(UIWebView​*)wv didFailLoadWithError:(NSError*​)error
		{
		   NSLog(@"Error for WEBVIEW: %@", [error description]);
		}
		
		- (void)didReceiveMemoryWarning {
		   [super didReceiveMemoryWarning];
		   // Dispose of any resources that can be recreated.
		}
		
		@end

6. Beachten Sie die folgenden Hinweise zur oben gezeigten Datei **ViewController.m**:

	- In der `loadWebView`-Methode laden wir eine lokale HTML-Datei namens **LocalPage.html**, deren Code wir als Nächstes untersuchen werden. 
	- In der `webViewDidFinishLoad`-Methode erfassen wir den `JsContext` und ordnen ihn unserer Wrapperklasse zu. Dadurch können wir unsere Wrapper-SDK-Methoden über das Handle **EngagementJs** aus der WebView aufrufen. 

7. Erstellen Sie eine Datei namens **LocalPage.html** mit dem folgenden Code:

		<!doctype html>
		<html>
		   <head>
		       <style type='text/css'>
		           html { font-family:Helvetica; color:#222; }
		           h1 { color:steelblue; font-size:22px; margin-top:16px; }
		           h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
		       </style>
		       
		       <script type="text/javascript">
		       
		       window.onerror = function(err)
		       {
		           alert('window.onerror: ' + err);
		       }
		       
		       function send(inputId)
		       {
		           var input = document.getElementById(inputId);
		           if(input)
		           {
		               var value = input.value;
		               // Example of how extras info can be passed with the Engagement logs
		               var extras = '{"CustomerId":"MS290011"}';
		           }
		           
		           if(value && value.length > 0)
		           {
		               switch(inputId)
		               {
		                   case "event":
		                   EngagementJs.sendEngagementEvent(value, extras);
		                   break;
		                   
		                   case "job":
		                   EngagementJs.startEngagementJob(value, extras);
		                   window.setTimeout(
		                   function(){
		                       EngagementJs.endEngagementJob(value);
		                   }, 10000 );
		                   break;
		
		                   case "error":
		                   EngagementJs.sendEngagementError(value, extras);
		                   break;
		                   
		                   case "appInfo":
		                   var appInfo = '{"customer_name":"' + value + '"}';
		                   EngagementJs.sendEngagementAppInfo(appInfo);
		                   break;
		               }
		           }
		       }
		       </script>
		       
		   </head>
		   <body>
		       <h1>Bridge Tester</h1>
		       
		       <div id='engagement'>
		           
		           <br/>
		           <h2>Event</h2>
		           <input type="text" id="event" size="35">
		           <button onclick="send('event')">Send</button>
		           
		           <br/>
		           <h2>Job</h2>
		           <input type="text" id="job" size="35">
		           <button onclick="send('job')">Send</button>
		           
		           <br/>
		           <h2>Error</h2>
		           <input type="text" id="error" size="35">
		           <button onclick="send('error')">Send</button
		           
		           <br/>
		           <h2>AppInfo</h2>
		           <input type="text" id="appInfo" size="35">
		           <button onclick="send('appInfo')">Send</button>
		       
		       </div>
		   </body>
		</html>

8. Beachten Sie die folgenden Hinweise zur oben gezeigten HTML-Datei:

	- 	Sie enthält eine Reihe von Eingabefeldern, in denen Sie Daten als Namen für das Ereignis, den Auftrag, den Fehler oder die AppInfo bereitstellen können. Beim Klicken auf die Schaltfläche daneben erfolgt ein Aufruf an das JavaScript, das schließlich die Methoden aus der Brückendatei aufruft, um diesen Aufruf an das Mobile Engagement iOS SDK zu übergeben. 
	- 	Wir versehen Ereignisse, Aufträge und sogar Fehler mit einigen statischen Zusatzinformationen, um zu veranschaulichen, wie dies zu erreichen wäre. Diese zusätzlichen Informationen werden als JSON-Zeichenfolge gesendet, die (wie in der Datei `EngagementJsExports.m` zu sehen ist) analysiert und beim Senden von Ereignissen, Aufträgen und Fehlern übergeben wird. 
	- 	Ein Mobile Engagement-Auftrag mit dem im Eingabefeld angegebenen Namen wird gestartet, für 10 Sekunden ausgeführt und beendet. 
	- 	Eine Mobile Engagement-AppInfo oder ein Tag wird mit „customer\_name“ als statischem Schlüssel und mit dem eingegebenen Wert als Wert für das Tag übergeben. 
 
9. Wenn Sie die App ausführen, erhalten Sie folgende Ausgabe. Geben Sie jetzt einen Namen für ein Testereignis wie folgt ein, und klicken Sie daneben auf **Send**.

	![][1]

10. Wenn Sie nun zur Registerkarte **Monitor** Ihrer App wechseln und unter **Ereignisse > Details** nachsehen, wird dieses Ereignis zusammen mit den statischen App-Informationen angezeigt, die gesendet werden.

	![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png

<!---HONumber=AcomDC_0302_2016-->