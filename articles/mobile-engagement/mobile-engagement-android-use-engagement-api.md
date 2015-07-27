<properties 
	pageTitle="Verwenden der Engagement-API unter Android" 
	description="Neuestes Android SDK – Verwenden der Engagement-API unter Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede"
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />

#Verwenden der Engagement-API unter Android

Dieses Dokument ist eine Ergänzung zum Dokument [Integrieren von Engagement unter Android](mobile-engagement-android-integrate-engagement.md). Es bietet tiefergehende Details zur Verwendung der Engagement-API, um Ihre Anwendungsstatistik zu melden.

Bedenken Sie, dass die einfachste Methode darin besteht, dass Ihre `Activity`-Unterklassen von der entsprechenden `EngagementActivity`-Klasse erben, wenn Engagement lediglich die Sitzungen, Aktivitäten, Abstürze und technischen Informationen Ihrer Anwendung melden soll.

Wenn Sie darüber hinaus noch mehr Meldungen wünschen, z. B. wenn Sie anwendungsspezifische Ereignisse, Fehler und Aufträge melden möchten, oder wenn die Aktivitäten Ihrer Anwendung anders als in den `EngagementActivity`-Klassen implementiert gemeldet werden sollen, dann müssen Sie die Engagement-API verwenden.

Die Engagement-API wird von der `EngagementAgent`-Klasse zur Verfügung gestellt. Eine Instanz dieser Klasse kann durch Aufruf der `EngagementAgent.getInstance(Context)` statischen Methode abgerufen werden (beachten Sie, dass das zurückgegebene `EngagementAgent`-Objekt ein Singleton-Objekt ist).

##Engagement-Konzepte

In den folgenden Abschnitten werden die [Mobile Engagement-Konzepte](mobile-engagement-concepts.md) für die Android-Plattform genauer dargestellt.

### `Session` und `Activity`

Wenn sich der Benutzer zwischen zwei *Aktivitäten* mehr als zwei Sekunden im Leerlauf befindet, dann wird diese Folge von *Aktivitäten* in zwei einzelne *Sitzungen* unterteilt. Diese paar Sekunden werden als „Sitzungszeitlimit“ bezeichnet.

Eine *Aktivität* ist üblicherweise mit einem Bildschirm einer Anwendung verknüpft, d. h. die *Aktivität* startet, wenn der Bildschirm angezeigt wird und endet, wenn der Bildschirm geschlossen wird. Dies ist der Fall, wenn das Engagement-SDK über die `EngagementActivity`-Klassen integriert wird.

Aber *Aktivitäten* können auch manuell mithilfe der Engagement-API gesteuert werden. Auf diese Weise kann ein vorhandener Bildschirm in mehrere Unterabschnitte geteilt werden, um mehr Details über die Verwendung des Bildschirms zu erhalten (um beispielsweise zu erfahren, wie häufig und wie lange Dialoge in diesem Bildschirm verwendet werden).

##Berichterstellung für Aktivitäten

> [AZURE.IMPORTANT]Wenn Sie die `EngagementActivity`-Klasse und ihre Varianten, wie unter „Integrieren von Engagement unter Android“ beschrieben, verwenden, müssen Sie die Aktivitäten nicht gemäß der Beschreibung in diesem Abschnitt melden.

### Benutzer startet eine neue Aktivität

			EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
			// Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Sie müssen jedes Mal `startActivity()` aufrufen, wenn sich die Benutzeraktivität ändert. Der erste Aufruf dieser Funktion startet eine neue Benutzersitzung.

Der beste Ort zum Aufrufen dieser Funktion ist die `onResume`-Rückruffunktion der einzelnen Aktivitäten.

### Der Benutzer beendet seine aktuelle Aktivität

			EngagementAgent.getInstance(this).endActivity();

Sie müssen `endActivity()` mindestens einmal aufrufen, wenn der Benutzer seine letzte Aktivität beendet. Dadurch wird das Engagement-SDK darüber informiert, dass sich der Benutzer derzeit im Leerlauf befindet und die Benutzersitzung geschlossen werden muss, sobald das Sitzungszeitlimit abläuft (wenn Ihr Aufruf `startActivity()` von vor dem Sitzungszeitlimit abläuft, wird die Sitzung einfach fortgesetzt).

Der beste Ort zum Aufrufen dieser Funktion ist die `onPause`-Rückruffunktion der einzelnen Aktivitäten.

##Berichterstellung für Ereignisse

### Sitzungsereignisse

Sitzungsereignisse werden normalerweise verwendet, um die Aktionen eines Benutzers während seiner Sitzung zu melden.

**Beispiel ohne zusätzliche Daten:**

			public MyActivity extends EngagementActivity {
			   [...]
			   @Override
			   public boolean onPrepareOptionsMenu(Menu menu) {
			      getEngagementAgent().sendSessionEvent("menu_shown", null);
			   }
			   [...]
			}

**Beispiel mit zusätzlichen Daten:**

			public MyActivity extends EngagementActivity {
			  [...]
			  @Override
			  public boolean onMenuItemSelected(int featureId, MenuItem item) {
			    Bundle extras = new Bundle();
			    extras.putInt("id", item.getItemId());
			    getEngagementAgent().sendSessionEvent("menu_selected", extras);
			  }
			  [...]
			}

### Eigenständige Ereignisse

Im Gegensatz zu Sitzungsereignissen können eigenständige Ereignisse außerhalb eines Sitzungskontextes auftreten.

**Beispiel:**

Angenommen, Sie möchten Ereignisse melden, die beim Auslösen eines Übertragungsempfängers auftreten:

			/** Triggered by Intent.ACTION_BATTERY_LOW */
			public BatteryLowReceiver extends BroadcastReceiver {
			  [...]
			  @Override
			  public void onReceive(Context context, Intent intent) {
			    EngagementAgent.getInstance(context).sendEvent("battery_low", null);
			  }
			  [...]
			}

##Melden von Fehlern

### Sitzungsfehler

Sitzungsfehler werden normalerweise zum Melden der Fehler verwendet, die Auswirkungen auf den Benutzer während seiner Sitzung haben.

**Beispiel:**

			/** The user has entered invalid data in a form */
			public MyActivity extends EngagementActivity {
			  [...]
			  public void onMyFormSubmitted(MyForm form) {
			    [...]
			    /* The user has entered an invalid email address */
			    getEngagementAgent().sendSessionError("sign_up_email", null);
			    [...]
			  }
			  [...]
			}

### Eigenständige Fehler

Im Gegensatz zu Sitzungsfehlern können eigenständige Fehler außerhalb des Kontexts einer Sitzung auftreten.

**Beispiel:**

Das folgende Beispiel veranschaulicht die Meldung eines Fehlers, sobald auf dem Mobiltelefon nicht ausreichend Arbeitsspeicher zur Verfügung steht, während Ihr Anwendungsprozess aktiv ist.

			public MyApplication extends EngagementApplication {
			
			  @Override
			  protected void onApplicationProcessLowMemory() {
			    EngagementAgent.getInstance(this).sendError("low_memory", null);
			  }
			}

##Berichterstellung für Aufträge

### Beispiel

Angenommen, Sie möchten die Dauer des Anmeldevorgangs melden:
			
			[...]
			public void signIn(Context context, ...) {
			
			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has started */
			  engagementAgent.startJob("sign_in", null);
			
			  [... sign in ...]
			
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Berichterstellung zu Fehlern während eines Auftrags

Fehler können mit einem ausgeführten Auftrag in Zusammenhang stehen anstatt mit der aktuellen Benutzersitzung.

**Beispiel:**

Angenommen, Sie möchten einen Fehler während des Anmeldeprozesses melden:

[...] public void signIn(Context context, ...) {

			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has been started */
			  engagementAgent.startJob("sign_in", null);
			
			  /* Try to sign in */
			  while(true)
			    try {
			      trySignin();
			      break;
			    }
			    catch(Exception e) {
			      /* Report the error to Engagement */
			      engagementAgent.sendJobError("sign_in_error", "sign_in", null);
			
			      /* Retry after a moment */
			      sleep(2000);
			    }
			  [...]
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Melden von Ereignissen während eines Auftrags

Ereignisse können statt mit der aktuellen Benutzersitzung in Zusammenhang mit einem ausgeführten Auftrag stehen.

**Beispiel:**

Angenommen, wir verfügen über ein soziales Netzwerk und verwenden einen Auftrag, um die Zeit insgesamt zu melden, die der Benutzer mit dem Server verbunden ist. Der Benutzer kann im Hintergrund mit dem Server verbunden bleiben, auch wenn er eine andere Anwendung verwendet oder sich das Mobiltelefon im Ruhemodus befindet und somit keine Sitzung besteht.

Der Benutzer kann Nachrichten von Freunden empfangen, hierbei handelt es sich um ein Auftragsereignis.
			
			[...]
			public void signin(Context context, ...) {
			  [...Sign in code...]
			  EngagementAgent.getInstance(context).startJob("connection", null);
			}
			[...]
			public void signout(Context context) {
			  [...Sign out code...]
			  EngagementAgent.getInstance(context).endJob("connection");
			}
			[...]
			public void onMessageReceived(Context context) {
			  [...Notify in status bar...]
			  EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
			}
			[...]

##Zusätzliche Parameter

Ereignissen, Fehlern, Aktivitäten und Aufträgen können beliebige Daten zugeordnet werden.

Diese Daten können strukturiert werden. Sie verwenden die „Bundle“-Klasse von Android (sie funktionieren in Android-Vorhaben eigentlich wie zusätzliche Parameter). Beachten Sie, dass ein Bündel Arrays oder andere Bündelinstanzen enthalten kann.

> [AZURE.IMPORTANT]Wenn Sie verpackbare oder serialisierbare Parameter einfügen, stellen Sie sicher, dass ihre `toString()`-Methode implementiert ist, damit eine lesbare Zeichenfolge zurückgegeben wird. Serialisierbare Klassen, die nicht flüchtige Felder enthalten, die nicht serialisiert werden können, führen zu einem Absturz von Android, wenn Sie `bundle.putSerializable("key",value);` aufrufen.

> [AZURE.WARNING]Spärliche Arrays in zusätzlichen Parametern werden nicht unterstützt, d. h. sie werden nicht als Array serialisiert. Sie sollten sie in Standardarrays konvertieren, bevor sie in zusätzlichen Parametern verwendet werden.

### Beispiel

			Bundle extras = new Bundle();
			extras.putString("video_id", 123);
			extras.putString("ref_click", "http://foobar.com/blog");
			EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### Grenzen

#### Schlüssel

Jeder Schlüssel in `Bundle` muss mit dem folgenden regulären Ausdruck übereinstimmen:

`^[a-zA-Z][a-zA-Z_0-9]*`

Das bedeutet, dass Schlüssel mit mindestens einem Buchstaben, gefolgt von Buchstaben, Ziffern oder Unterstrichen (_) beginnen müssen.

#### Größe

Extras sind auf **1024** Zeichen pro Aufruf begrenzt (nach der JSON-Codierung durch den Engagement-Dienst).

Im vorherigen Beispiel enthält die an den Server gesendete JSON 58 Zeichen:

			{"ref_click":"http://foobar.com/blog","video_id":"123"}

##Informationen zur Berichterstellung

Sie können Berichte zur Nachverfolgung (oder zu anderen anwendungsspezifischen Informationen) mithilfe der `sendAppInfo()`-Funktion manuell erstellen.

Beachten Sie, dass diese Informationen inkrementell gesendet werden können: Nur der letzte Wert für einen bestimmten Schlüssel wird für ein bestimmtes Gerät gespeichert.

Wie bei Ereigniszusätzen wird die „Bundle“-Klasse zum Abstrahieren von Anwendungsinformationen verwendet. Beachten Sie, dass Arrays oder Teilbündel als einfache Zeichenfolgen behandelt werden (mithilfe der JSON-Serialisierung).

### Beispiel

Hier folgt ein Codebeispiel zum Senden von Geschlecht und Geburtstag des Benutzers:

			Bundle appInfo = new Bundle();
			appInfo.putString("status", "premium");
			appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
			EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### Grenzen

#### Schlüssel

Jeder Schlüssel in `Bundle` muss mit dem folgenden regulären Ausdruck übereinstimmen:

`^[a-zA-Z][a-zA-Z_0-9]*`

Das bedeutet, dass Schlüssel mit mindestens einem Buchstaben, gefolgt von Buchstaben, Ziffern oder Unterstrichen (_) beginnen müssen.

#### Größe

Anwendungsinformationen sind auf **1024** Zeichen pro Aufruf begrenzt (nach der JSON-Codierung durch den Engagement-Dienst).

Im vorherigen Beispiel enthält die an den Server gesendete JSON 44 Zeichen:

			{"expiration":"2016-12-07","status":"premium"}
 

<!---HONumber=July15_HO3-->