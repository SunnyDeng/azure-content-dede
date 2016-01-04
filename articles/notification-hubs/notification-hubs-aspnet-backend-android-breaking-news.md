<properties
	pageTitle="Lernprogramm zu aktuellen Nachrichten mit Notification Hubs - Android"
	description="Erfahren Sie mehr über die Verwendung von Azure Service Bus Notification Hubs zum Senden von Benachrichtigungen zu aktuellen Nachrichten an Android-Geräte."
	services="notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/15/2015" 
	ms.author="wesmc"/>


# Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##Übersicht

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Benachrichtigungen zu aktuellen Nachrichten an eine Android-Anwendung senden können. Sie werden anschließend in der Lage sein, sich für Kategorien aktueller Nachrichten zu registrieren, die Sie interessieren, und nur Pushbenachrichtigungen für diese Kategorien zu empfangen. Dieses Szenario ist ein häufiges Muster für viele Anwendungen, bei denen Benachrichtigungen an Benutzergruppen gesendet werden müssen, die zuvor Interesse daran bekundet haben, zum Beispiel RSS-Reader, Apps für Musikliebhaber, etc.

Übertragungsszenarien werden durch das Einfügen von einem oder mehreren _Tags_ möglich, wenn eine Registrierung im Notification Hub erstellt wird. Wenn Benachrichtigungen an ein Tag gesendet werden, erhalten alle Geräte, die für das Tag registriert wurden, diese Benachrichtigung. Da es sich bei Tags um Zeichenfolgen handelt, müssen diese nicht im Voraus bereitgestellt werden. Weitere Informationen zu Tags finden Sie unter [Notification Hubs – Weiterleitung und Tagausdrücke](notification-hubs-routing-tag-expressions.md).


##Voraussetzungen

Dieses Thema baut auf die App auf, die Sie in [Erste Schritte mit Notification Hubs][get-started] erstellt haben. Bevor Sie dieses Lernprogramm beginnen, müssen Sie [Erste Schritte mit Notification Hubs][get-started] abgeschlossen haben.

##Hinzufügen der Kategorieauswahl zur App

Der erste Schritt besteht daraus, Benutzeroberflächenelemente zur vorhandenen Hauptaktivität hinzuzufügen, welche dem Benutzer die Auswahl der Kategorien für die Registrierung ermöglichen. Die durch den Benutzer ausgewählten Kategorien werden auf dem Gerät gespeichert. Wenn die App gestartet wird, wird eine Geräteregistrierung in Ihrem Notification Hub mit den ausgewählten Kategorien als Tags erstellt.

1. Öffnen Sie die Datei „res/layout/activity\_main.xml“, und ersetzen Sie den Inhalt wie folgt:

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

2. Öffnen Sie die Datei „res/values/strings.xml“, und fügen Sie die folgenden Zeilen hinzu:

	    <string name="button_subscribe">Subscribe</string>
	    <string name="label_world">World</string>
	    <string name="label_politics">Politics</string>
	    <string name="label_business">Business</string>
	    <string name="label_technology">Technology</string>
	    <string name="label_science">Science</string>
	    <string name="label_sports">Sports</string>

	Das Layout von „main\_activity.xml“ sollte jetzt wie folgt aussehen:

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

		    public Notifications(Context context, String senderId, String hubName, 
									String listenConnectionString) {
		        this.context = context;
		        this.senderId = senderId;
		
		        gcm = GoogleCloudMessaging.getInstance(context);
		        hub = new NotificationHub(hubName, listenConnectionString, context);
		    }

			public void storeCategoriesAndSubscribe(Set<String> categories)
			{
				SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			    settings.edit().putStringSet("categories", categories).commit();
			    subscribeToCategories(categories);
			}

			public Set<String> retrieveCategories() {
				SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
				return settings.getStringSet("categories", new HashSet<String>());
			}

		    public void subscribeToCategories(final Set<String> categories) {
		        new AsyncTask<Object, Object, Object>() {
		            @Override
		            protected Object doInBackground(Object... params) {
		                try {
		                    String regid = gcm.register(senderId);
		
		                    String templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";
		
		                    hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
								categories.toArray(new String[categories.size()]));
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


4. Entfernen Sie in der Klasse **MainActivity** die privaten Felder für **NotificationHub** und **GoogleCloudMessaging**, und fügen Sie ein Feld für **Notifications** hinzu:

		// private GoogleCloudMessaging gcm;
		// private NotificationHub hub;
		private Notifications notifications;

5. Entfernen Sie dann in der **onCreate**-Methode die Initialisierung des **hub**-Felds und der **registerWithNotificationHubs**-Methode. Fügen Sie dann die folgenden Zeilen hinzu, die eine Instanz der Klasse **Notifications** initialisieren.


	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	        MyHandler.mainActivity = this;
	
	        NotificationsManager.handleNotifications(this, SENDER_ID,
	                MyHandler.class);
	
	        notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);
	
	        notifications.subscribeToCategories(notifications.retrieveCategories());
	    }

	In `HubName` und `HubListenConnectionString` sollten die Platzhalter `<hub name>` und `<connection string with listen access>` bereits durch den Namen Ihres Notification Hubs und die Verbindungszeichenfolge für *DefaultListenSharedAccessSignature*, die Sie zuvor erhalten haben, ersetzt sein.

	> [AZURE.NOTE]Da Anmeldenamen, die mit einer Client-App verteilt werden, nicht sehr sicher sind, sollten Sie nur den Schlüssel für den Abhörzugriff mit Ihrer Client-App verteilen. Der Abhörzugriff ermöglicht der App, sich für Benachrichtigungen zu registrieren, aber es können keine vorhandenen Registrierungen geändert und keine Benachrichtigungen versendet werden. Der Vollzugriffsschlüssel wird in einem gesicherten Back-End-Dienst für das Versenden von Benachrichtigungen und das Ändern vorhandener Benachrichtigungen verwendet.


6. Fügen Sie dann die folgenden Importe und die `subscribe`Methode zum Behandeln des Click-Ereignisses für die Abonnementschaltfläche hinzu:
		
		import android.widget.CheckBox;
		import java.util.HashSet;
		import java.util.Set;

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

##Registrieren für Benachrichtigungen

Durch diese Schritte findet beim Starten eine Registrierung beim Notification Hub statt, wobei die im lokalen Speicher gespeicherten Kategorien verwendet werden.

> [AZURE.NOTE]Da sich die durch Google Cloud Messaging (GCM) zugeteilte Registrierungs-ID jederzeit ändern kann, sollten Sie sich regelmäßig für Benachrichtigungen registrieren, um Benachrichtigungsfehler zu vermeiden. Dieses Beispiel registriert sich jedes Mal für Benachrichtigungen, wenn die App gestartet wird. Für häufig ausgeführte Anwendungen (öfters als einmal täglich) können Sie die Registrierung wahrscheinlich überspringen, wenn weniger als ein Tag seit der letzten Registrierung vergangen ist, um Bandbreite einzusparen.


1. Fügen Sie den folgenden Code am Ende der **onCreate**-Methode in der **MainActivity**-Klasse ein:

		notifications.subscribeToCategories(notifications.retrieveCategories());

	Dadurch wird sichergestellt, dass bei jedem Start der App die Kategorien vom lokalen Speicher abgerufen werden und eine Registrierung für diese Kategorien abgefragt wird.

2. Aktualisieren Sie dann die `onStart()`-Methode der `MainActivity`-Klasse wie folgt:

    @Override protected void onStart() { super.onStart(); isVisible = true;

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

##Senden von Benachrichtigungen mit Tags

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##Ausführen der App und Erzeugen von Benachrichtigungen

1. Erstellen Sie die App in Android Studio, und starten Sie sie auf einem Gerät oder in einem Emulator.

	Die Benutzeroberfläche der App bietet verschiedene Umschaltmöglichkeiten, mit denen Sie die Kategorien auswählen können, die Sie abonnieren möchten.

2. Aktivieren Sie eine oder mehrere Kategorien, und klicken Sie dann auf **Subscribe**.

	Die App konvertiert die ausgewählten Kategorien in Tags, und fordert eine neue Geräteregistrierung für die ausgewählten Tags vom Notification Hub an. Die registrierten Kategorien werden zurückgegeben und in einer Popupbenachrichtigung angezeigt.

4. Senden Sie eine neue Benachrichtigung, indem Sie die .NET-Konsolen-App ausführen. Alternativ können Sie mithilfe der Registerkarte „Debuggen“ des Notification Hub im [klassischen Azure-Portal] Vorlagenbenachrichtigungen mit Tags senden.

	Die Benachrichtigungen für die ausgewählten Kategorien werden als Popupbenachrichtigungen angezeigt.

##Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie aktuelle Nachrichten nach Kategorie übermittelt werden. Sie können nun eines der folgenden Lernprogramme durchführen, die andere komplexe Notification Hub-Szenarios zeigen:

+ [Verwenden von Notification Hubs zum Übermitteln von lokalisierten aktuellen Nachrichten]

	Hier erfahren Sie, wie Sie die App zu aktuellen Nachrichten für das Versenden von lokalisierten Benachrichtigungen erweitern.





<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-get-started.md
[Verwenden von Notification Hubs zum Übermitteln von lokalisierten aktuellen Nachrichten]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[klassischen Azure-Portal]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!---HONumber=AcomDC_1217_2015-->