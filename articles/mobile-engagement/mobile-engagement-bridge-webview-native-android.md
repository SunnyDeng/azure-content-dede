<properties 
	pageTitle="Erstellen einer Brücke zwischen Android WebView und dem nativem Mobile Engagement Android SDK" 
	description="Beschreibt das Erstellen einer Brücke zwischen WebView unter Javascript und dem nativen Mobile Engagement Android SDK."		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="piyushjo" />

#Erstellen einer Brücke zwischen Android WebView und dem nativem Mobile Engagement Android SDK

> [AZURE.SELECTOR]
- [Android-Brücke](mobile-engagement-bridge-webview-native-android.md)
- [iOS-Brücke](mobile-engagement-bridge-webview-native-ios.md)

Einige mobile Apps werden als Hybrid-Apps konzipiert, das heißt, die App selbst wird mit der nativen Android-Entwicklung entwickelt, aber einige oder sogar alle Bildschirme werden in einer Android WebView gerendert. Sie können das Mobile Engagement Android SDK dennoch innerhalb solcher Apps nutzen, und dieses Tutorial zeigt Ihnen, wie das geht. Der unten gezeigte Beispielcode basiert auf [dieser](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript) Android-Dokumentation. Darin wird beschrieben, wie mit diesem dokumentierten Ansatz dieselbe Strategie für häufig verwendete Methoden aus dem Mobile Engagement Android SDK implementiert werden kann, sodass eine WebView aus einer Hybrid-App auch Anforderungen zum Nachverfolgen von Ereignissen, Aufträgen, Fehlern oder App-Informationen initiieren und diese über unser Android SDK weiterreichen kann.

1. Zunächst müssen Sie sicherstellen, dass Sie unser [Erste-Schritte-Tutorial](mobile-engagement-android-get-started.md) zum Integrieren des Mobile Engagement Android SDK in der Hybrid-App durchgeführt haben. Anschließend sieht die Methode `OnCreate` wie folgt aus.  
    
		@Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	        engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
	        EngagementAgent.getInstance(this).init(engagementConfiguration);
	    }

2. Stellen Sie jetzt sicher, dass die Hybrid-App einen Bildschirm mit einer WebView enthält. Der entsprechende Code sollte in etwa wie der folgende aussehen, in dem wir eine lokale HTML-Datei **Sample.html** in die WebView in die Methode `onCreate` des Bildschirms laden.

	    private void SetWebView() {
	        WebView myWebView = (WebView) findViewById(R.id.webview);
	        myWebView.loadUrl("file:///android_asset/Sample.html");
		}

		protected void onCreate(Bundle savedInstanceState) {
			...
			...
			SetWebView();
	    }

3. Erstellen Sie jetzt eine Brückendatei namens **WebAppInterface**, mit der ein Wrapper über einige häufig verwendete Methoden des Mobile Engagement Android SDK erstellt wird. Verwenden Sie dazu den in der [Android-Dokumentation](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript) beschriebenen `@JavascriptInterface`-Ansatz:

		import android.content.Context;
		import android.os.Bundle;
		import android.util.Log;
		import android.webkit.JavascriptInterface;
		
		import com.microsoft.azure.engagement.EngagementAgent;
		
		import org.json.JSONArray;
		import org.json.JSONObject;
		
		public class WebAppInterface {
		    Context mContext;
		
		    /** Instantiate the interface and set the context */
		    WebAppInterface(Context c) {
		        mContext = c;
		    }
		
		    @JavascriptInterface
		    public void sendEngagementEvent(String name, String extras ){
		        EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
		    }
		
		    @JavascriptInterface
		    public void startEngagementJob(String name, String extras ){
		        EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
		    }
		
		    @JavascriptInterface
		    public void endEngagementJob(String name){
		        EngagementAgent.getInstance(mContext).endJob(name);
		    }
		
		    @JavascriptInterface
		    public void sendEngagementError(String name, String extras ){
		        EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
		    }
		
		    @JavascriptInterface
		    public void sendEngagementAppInfo(String appInfo){
		        EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
		    }
		
		    public Bundle ParseExtras(String input) {
		        Bundle extras = new Bundle();
		
		        try {
		            JSONObject jObject = new JSONObject(input);
		            extras.putString(jObject.names().getString(0),
		                    jObject.get(jObject.names().getString(0)).toString());
		        } catch (Exception e) {
		            e.printStackTrace();
		        }
		        return extras;
		    }
		}  

4. Nachdem wir die obige Brückendatei erstellt haben, müssen wir sicherstellen, dass sie unserer Webview zugeordnet wird. Zu diesem Zweck müssen Sie Ihre `SetWebview`-Methode folgendermaßen bearbeiten:

	    private void SetWebView() {
	        WebView myWebView = (WebView) findViewById(R.id.webview);
	        myWebView.loadUrl("file:///android_asset/Sample.html");
	        WebSettings webSettings = myWebView.getSettings();
	        webSettings.setJavaScriptEnabled(true);
	        myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
	    }

5. Im oben gezeigten Codeausschnitt haben wir `addJavascriptInterface` aufgerufen, um unsere Brückenklasse unserer WebView zuzuordnen. Außerdem haben wir ein Handle namens **EngagementJs** erstellt, um die Methoden aus der Brückendatei aufzurufen.

6. Nun erstellen Sie die folgende Datei **Sample.html** in Ihrem Projekt in einem Ordner namens **assets**, der in die Webview geladen wird und in dem wir die Methoden aus der Brückendatei aufrufen werden.

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
		              log('window.onerror: ' + err);
		            }
		
		            send = function(inputId)
		            {
		                var input = document.getElementById(inputId);
		                if(input)
		                {
		                    var value = input.value;
		                    // Example of how extras info can be passed with the Engagement logs
		                    var extras = '{"CustomerId":"MS290011"}';
		
		                    if(value && value.length > 0)
		                    {
		                        switch(inputId)
		                        {
		                            case "event":
		                            EngagementJs.sendEngagementEvent(value, extras);
		                            break;
		
		                            case "job":
		                            EngagementJs.startEngagementJob(value, extras);
		                            window.setTimeout( function()
		                            {
		                              EngagementJs.endEngagementJob(value);
		                            }, 10000 );
		                            break;
		
		                            case "error":
		                            EngagementJs.sendEngagementError(value, extras);
		                            break;
		
		                            case "appInfo":
		                            EngagementJs.sendEngagementAppInfo({"customer_name":value});
		                            break;
		                        }
		                    }
		                }
		            }
		        </script>
		    </head>
		    <body>
		        <h1>Bridge Tester</h1>
		        <div id='engagement'>
		            <h2>Event</h2>
		            <input type="text" id="event" size="35">
		            <button onclick="send('event')">Send</button>
		
		            <h2>Job</h2>
		            <input type="text" id="job" size="35">
		            <button onclick="send('job')">Send</button>
		
		            <h2>Error</h2>
		            <input type="text" id="error" size="35">
		            <button onclick="send('error')">Send</button>
		
		            <h2>AppInfo</h2>
		            <input type="text" id="appInfo" size="35">
		            <button onclick="send('appInfo')">Send</button>
		        </div>
		    </body>
		</html>

8. Beachten Sie die folgenden Hinweise zur oben gezeigten HTML-Datei:

	- 	Sie enthält eine Reihe von Eingabefeldern, in denen Sie Daten als Namen für das Ereignis, den Auftrag, den Fehler oder die AppInfo bereitstellen können. Beim Klicken auf die Schaltfläche daneben erfolgt ein Aufruf an das JavaScript, das schließlich die Methoden aus der Brückendatei aufruft, um diesen Aufruf an das Mobile Engagement Android SDK zu übergeben. 
	- 	Wir versehen Ereignisse, Aufträge und sogar Fehler mit einigen statischen Zusatzinformationen, um zu veranschaulichen, wie dies zu erreichen wäre. Diese zusätzlichen Informationen werden als JSON-Zeichenfolge gesendet, die (wie in der Datei `WebAppInterface` zu sehen ist) analysiert und in ein Android `Bundle` eingefügt und beim Senden von Ereignissen, Aufträgen und Fehlern übergeben wird. 
	- 	Ein Mobile Engagement-Auftrag mit dem im Eingabefeld angegebenen Namen wird gestartet, für 10 Sekunden ausgeführt und beendet. 
	- 	Eine Mobile Engagement-AppInfo oder ein Tag wird mit „customer\_name“ als statischem Schlüssel und mit dem eingegebenen Wert als Wert für das Tag übergeben. 
 
9. Wenn Sie die App ausführen, erhalten Sie folgende Ausgabe. Geben Sie jetzt einen Namen für ein Testereignis wie folgt ein, und klicken Sie darunter auf **Send**.

	![][1]

10. Wenn Sie nun zur Registerkarte **Monitor** Ihrer App wechseln und unter **Ereignisse > Details** nachsehen, wird dieses Ereignis zusammen mit den statischen App-Informationen angezeigt, die gesendet werden.

	![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png

<!---HONumber=AcomDC_0302_2016-->