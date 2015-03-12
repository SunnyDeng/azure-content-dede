<properties 
	pageTitle="Lernprogramm zu aktuellen Nachrichten mit Notification Hubs - Android" 
	description="Erfahren Sie mehr über die Verwendung von Azure Service Bus Notification Hubs zum Senden von Benachrichtigungen zu aktuellen Nachrichten an Android-Geräte." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="ricksal"/>


# Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten
<div class="dev-center-tutorial-selector sublanding">     	
	<a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
	<a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android" class="current">Android</a>
</div>

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Benachrichtigungen zu aktuellen Nachrichten an eine Android-Anwendung senden können. Sie werden anschließend in der Lage sein, sich für Kategorien aktueller Nachrichten zu registrieren, die Sie interessieren, und nur Pushbenachrichtigungen für diese Kategorien zu empfangen. Dieses Szenario ist ein häufiges Muster für viele Anwendungen, bei denen Benachrichtigungen an Benutzergruppen gesendet werden müssen, die zuvor Interesse daran bekundet haben, zum Beispiel RSS-Reader, Apps für Musikliebhaber, etc. 

Übertragungsszenarien werden durch das Einfügen von einem oder mehreren Tags möglich, wenn eine Registrierung im Notification Hub erstellt wird. Wenn Benachrichtigungen an ein Tag gesendet werden, erhalten alle Geräte, die für das Tag registriert wurden, diese Benachrichtigung. Da es sich bei Tags um Zeichenfolgen handelt, müssen diese nicht im Voraus bereitgestellt werden. Weitere Informationen zu Tags finden Sie unter [Leitfaden zu Notification Hubs]. 

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung dieses Szenarios behandelt:

1. [Hinzufügen der Kategorieauswahl zur App]
2. [Registrieren für Benachrichtigungen]
3. [Senden von Benachrichtigungen von Ihrem Back-End aus]
4. [Ausführen der Anwendung und Erzeugen von Benachrichtigungen]

Dieses Thema baut auf der App auf, die Sie in [Erste Schritte mit Notification Hubs][get-started] erstellt haben. Bevor Sie dieses Lernprogramm beginnen, müssen Sie [Erste Schritte mit Notification Hubs][get-started] abgeschlossen haben.

##<a name="adding-categories"></a>Hinzufügen der Kategorieauswahl zur App

Der erste Schritt besteht daraus, Benutzeroberflächenelemente zur vorhandenen Hauptaktivität hinzuzufügen, welche dem Benutzer die Auswahl der Kategorien für die Registrierung ermöglichen. Die durch den Benutzer ausgewählten Kategorien werden auf dem Gerät gespeichert. Beim Starten der App wird eine Geräteregistrierung in Ihrem Notification Hub mit den ausgewählten Kategorien als Tags erstellt. 

1. Öffnen Sie die Datei "res/layout/activity_main.xml", und ersetzen Sie den Inhalt wie folgt:
			
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    tools:context="com.example.breakingnews.MainActivity"
		    android:orientation="vertical">
		
		        <CheckBox
		            android:id="@+id/worldBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_world" />
		        <CheckBox
		            android:id="@+id/politicsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_politics" />
		        <CheckBox
		            android:id="@+id/businessBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_business" />
		        <CheckBox
		            android:id="@+id/technologyBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_technology" />
		        <CheckBox
		            android:id="@+id/scienceBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_science" />
		        <CheckBox
		            android:id="@+id/sportsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_sports" />
			    <Button
			        android:layout_width="wrap_content"
			        android:layout_height="wrap_content"
			        android:onClick="subscribe"
			        android:text="@string/button_subscribe" />
		</LinearLayout>

2. Öffnen Sie die Datei "res/values/strings.xml", und fügen Sie die folgenden Zeilen hinzu:

	    <string name="button_subscribe">Subscribe</string>
	    <string name="label_world">World</string>
	    <string name="label_politics">Politics</string>
	    <string name="label_business">Business</string>
	    <string name="label_technology">Technology</string>
	    <string name="label_science">Science</string>
	    <string name="label_sports">Sports</string>

	Das Layout von "main_activity.xml" sollte jetzt wie folgt aussehen:

	![][A1]

3. Erstellen Sie nun eine Klasse **Notifications** im selben Paket wie die Klasse **MainActivity**.

		import java.util.HashSet;
		import java.util.Set;
		
		import android.content.Context;
		import android.content.SharedPreferences;
		import android.os.AsyncTask;
		import android.util.Log;
		import android.widget.Toast;
		
		import com.google.android.gms.gcm.GoogleCloudMessaging;
		import com.microsoft.windowsazure.messaging.NotificationHub;		
		
		public class Notifications {
			private static final String PREFS_NAME = "BreakingNewsCategories";
			private GoogleCloudMessaging gcm;
			private NotificationHub hub;
			private Context context;
			private String senderId;
			
			public Notifications(Context context, String senderId) {
				this.context = context;
				this.senderId = senderId;
				
				gcm = GoogleCloudMessaging.getInstance(context);
		        hub = new NotificationHub(<hub name>, <connection string with listen access>, context);
			}
			
			public void storeCategoriesAndSubscribe(Set<String> categories)
			{
				SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			    settings.edit().putStringSet("categories", categories).commit();
			    subscribeToCategories(categories);
			}
			
			public void subscribeToCategories(final Set<String> categories) {
				new AsyncTask<Object, Object, Object>() {
					@Override
					protected Object doInBackground(Object... params) {
						try {
							String regid = gcm.register(senderId);
					        hub.register(regid, categories.toArray(new String[categories.size()]));
						} catch (Exception e) {
							Log.e("MainActivity", "Failed to register - " + e.getMessage());
							return e;
						}
						return null;
					}
		
					protected void onPostExecute(Object result) {
						String message = "Subscribed for categories: "
								+ categories.toString();
						Toast.makeText(context, message,
								Toast.LENGTH_LONG).show();
					}
				}.execute(null, null, null);
			}
			
		}

	Diese Klasse verwendet den lokalen Speicher, um Nachrichtenkategorien zu speichern, die das Gerät empfangen soll. Sie enthält zudem Methoden zum Registrieren dieser Kategorien.

4. Ersetzen Sie im Code oben die Platzhalter `<hub name>` und `<connection string with listen access>` durch den Namen Ihres Notification Hub und die Verbindungszeichenfolge für *DefaultListenSharedAccessSignature*, die Sie zuvor erhalten haben.

	> [AZURE.NOTE] Da Anmeldenamen, die mit einer Client-App verteilt werden, nicht sehr sicher sind, sollten Sie nur den Schlüssel für den Abhörzugriff mit Ihrer Client-App verteilen. Der Abhörzugriff ermöglicht der App, sich für Benachrichtigungen zu registrieren, aber es können keine vorhandenen Registrierungen geändert und keine Benachrichtigungen versendet werden. Der Vollzugriffsschlüssel wird in einem gesicherten Back-End-Dienst für das Versenden von Benachrichtigungen und das Ändern vorhandener Benachrichtigungen verwendet.

4. Entfernen Sie in der Klasse **MainActivity** die privaten Felder für **NotificationHub** und **GoogleCloudMessaging**, und fügen Sie ein Feld für **Notifications** hinzu:

		// private GoogleCloudMessaging gcm;
		// private NotificationHub hub;
		private Notifications notifications;
 
5. Entfernen Sie dann in der **onCreate**-Methode die Initialisierung des **hub**-Felds und der **registerWithNotificationHubs**-Methode. Fügen Sie dann die folgenden Zeilen hinzu, die eine Instanz der Klasse **Notifications** initialisieren. Die Methode sollte die folgenden Zeilen enthalten:

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
	
			NotificationsManager.handleNotifications(this, SENDER_ID,
					MyHandler.class);
	
			notifications = new Notifications(this, SENDER_ID);
		}

6. Fügen Sie anschließend die folgende Methode hinzu:
	
	    public void subscribe(View sender) {
			final Set<String> categories = new HashSet<String>();
	
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			if (world.isChecked())
				categories.add("world");
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			if (politics.isChecked())
				categories.add("politics");
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			if (business.isChecked())
				categories.add("business");
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			if (technology.isChecked())
				categories.add("technology");
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			if (science.isChecked())
				categories.add("science");
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			if (sports.isChecked())
				categories.add("sports");
	
			notifications.storeCategoriesAndSubscribe(categories);
	    }
	
	Diese Methode erstellt eine Liste von Kategorien und verwendet die **Notifications**-Klasse zum Speichern der Liste im lokalen Speicher sowie zum Registrieren der entsprechenden Tags bei Ihrem Notification Hub. Wenn Kategorien geändert werden, wird die Registrierung mit neuen Kategorien neu erstellt.

Die App kann jetzt verschiedene Kategorien in einem lokalen Speicher auf dem Gerät speichern und beim Notification Hub registrieren, wenn der Benutzer die Auswahl der Kategorien ändert. 

##<a name="register"></a>Registrieren für Benachrichtigungen

Durch diese Schritte findet beim Starten eine Registrierung beim Notification Hub statt, wobei die im lokalen Speicher gespeicherten Kategorien verwendet werden. 

> [AZURE.NOTE] Da sich die durch Google Cloud Messaging (GCM) zugeteilte Registrierungs-ID jederzeit ändern kann, sollten Sie sich regelmäßig für Benachrichtigungen registrieren, um Benachrichtigungsfehler zu vermeiden. Dieses Beispiel registriert sich jedes Mal für Benachrichtigungen, wenn die App gestartet wird. Für häufig ausgeführte Anwendungen (öfters als einmal täglich) können Sie die Registrierung wahrscheinlich überspringen, wenn weniger als ein Tag seit der letzten Registrierung vergangen ist, um Bandbreite einzusparen.

1. Fügen Sie der **Notifications**-Klasse folgenden Code hinzu:

		public Set<String> retrieveCategories() {
			SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			return settings.getStringSet("categories", new HashSet<String>());
		}

	Dadurch werden die in der Klasse definierten Kategorien zurückgegeben.

2. Fügen Sie nun diesen Code am Ende der Methode **onCreate** in der Klasse **MainActivity** ein:

		notifications.subscribeToCategories(notifications.retrieveCategories());

	Dadurch wird sichergestellt, dass bei jedem Start der App die Kategorien vom lokalen Speicher abgerufen werden und eine Registrierung für diese Kategorien abgefragt wird. Die Methode **InitNotificationsAsync** wurde als Teil des Lernprogramms [Erste Schritte mit Notification Hubs] erstellt, wird in diesem Thema jedoch nicht benötigt.

3. Fügen Sie anschließend die folgende Methode zu **MainActivity** hinzu:

		@Override
		protected void onStart() {
			super.onStart();
			
			Set<String> categories = notifications.retrieveCategories();
			
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			world.setChecked(categories.contains("world"));
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			politics.setChecked(categories.contains("politics"));
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			business.setChecked(categories.contains("business"));
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			technology.setChecked(categories.contains("technology"));
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			science.setChecked(categories.contains("science"));
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			sports.setChecked(categories.contains("sports"));
		}

	Dadurch wird die Hauptaktivität basierend auf dem Status der zuvor gespeicherten Kategorien aktualisiert. 

Die App kann ist jetzt vollständig und kann verschiedene Kategorien in einem lokalen Speicher auf dem Gerät speichern und beim Notification Hub registrieren, wenn der Benutzer die Auswahl der Kategorien ändert. Als nächstes wird ein Back-End definiert, das Kategoriebenachrichtigungen an diese App senden kann.

<h2><a name="send"></a>Senden von Benachrichtigungen von Ihrem Back-End aus</h2>

[AZURE.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>Ausführen der Anwendung und Erzeugen von Benachrichtigungen

1. Erstellen Sie die App in Eclipse, und starten Sie sie auf einem Gerät oder in einem Emulator.
	
	Die Benutzeroberfläche der App bietet verschiedene Umschaltmöglichkeiten, mit denen Sie die Kategorien auswählen können, die Sie abonnieren möchten. 

2. Aktivieren Sie eine oder mehrere Kategorien, und klicken Sie dann auf **Abonnieren**.

	Die App konvertiert die ausgewählten Kategorien in Tags, und fordert eine neue Geräteregistrierung für die ausgewählten Tags vom Notification Hub an. Die registrierten Kategorien werden zurückgegeben und in einem Dialogfeld angezeigt.

4. Verwenden Sie eine dieser Möglichkeiten, um eine neue Benachrichtigung vom Back-End zu versenden:

	+ **.NET-Konsolenanwendung:** Starten Sie die Konsolenanwendung.

	+ **Java/PHP:** Führen Sie Ihre App/Ihr Skript aus.

	Die Benachrichtigungen für die ausgewählten Kategorien werden als Popupbenachrichtigungen angezeigt.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie aktuelle Nachrichten nach Kategorie übermittelt werden. Sie können nun eins der folgenden Lernprogramme durchführen, die andere komplexe Notification Hub-Szenarien zeigen:

+ [Verwenden von Notification Hubs zum Übermitteln von lokalisierten aktuellen Nachrichten]

	Hier erfahren Sie, wie Sie die App zu aktuellen Nachrichten für das Versenden von lokalisierten Benachrichtigungen erweitern. 

+ [Benachrichtigen von Benutzern mit Notification Hubs]

	Hier erfahren Sie, wie Sie Pushbenachrichtigungen an bestimmte authentifizierte Benutzer versenden. Dies ist eine gute Möglichkeit, um Benachrichtigungen nur an bestimmte Benutzer zu versenden.


<!-- Anchors. -->
[Hinzufügen der Kategorieauswahl zur App]: #adding-categories
[Registrieren für Benachrichtigungen]: #register
[Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
[Ausführen der Anwendung und Erzeugen von Benachrichtigungen]: #test-app
[Nächste Schritte]: #next-steps

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: /de-de/documentation/articles/notification-hubs-android-get-started/
[Verwenden von Notification Hubs zum Übermitteln von lokalisierten aktuellen Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-localized-dotnet/ 
[Benachrichtigen von Benutzern mit Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users
[Mobile Service]: /de-de/develop/mobile/tutorials/get-started/
[Leitfaden zu Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs-Anleitung für Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[wns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!--HONumber=45--> 
