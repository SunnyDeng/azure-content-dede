<properties 
	pageTitle="Verwenden der Engagement-API unter Android" 
	description="Neuestes Android SDK - Verwenden der Engagement-API unter Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />

# Verwenden der Engagement-API unter Android

Dieses Dokument ist eine Ergänzung zum Dokument [Integrieren von Engagement unter Android](../mobile-engagement-android-integrate-engagement/): Es bietet detaillierte Informationen zum Melden der Anwendungsstatistiken mithilfe der Engagement-API.

Bedenken Sie, dass die einfachste Methode darin besteht, dass Ihre `Activity`-Unterklassen von der entsprechenden `EngagementActivity`-Klasse erben, wenn Engagement lediglich die Sitzungen, Aktivitäten, Abstürze und technischen Informationen Ihrer Anwendung melden soll.

Wenn Sie weitere Schritte (z. B. anwendungsabhängige Ereignisse, Fehler und Aufträge melden) unternehmen oder die Aktivitäten Ihrer Anwendung auf eine andere Weise als in den `EngagementActivity`-Klassen implementiert, melden möchten, dann müssen Sie die Engagement-API verwenden.

Die Engagement-API wird von der `EngagementAgent`-Klasse bereitgestellt. Eine Instanz dieser Klasse kann durch Aufrufen der statischen `EngagementAgent.getInstance(Context)`-Methode abgerufen werden (beachten Sie, dass das zurückgegebene `EngagementAgent`-Objekt ein Singleton ist).

## Engagement-Konzepte

Die folgenden Komponenten erweitern die allgemeinen [Mobile Engagement-Konzepte] (../mobile-engagement-concepts/)für die Android-Plattform.

### `Sitzung` und `Aktivität`

Wenn sich der Benutzer zwischen zwei *Aktivitäten* mehr als zwei Sekunden im Leerlauf befindet, dann wird diese Folge von *Aktivitäten* in zwei einzelne *Sitzungen* unterteilt. Diese paar Sekunden werden als "Sitzungszeitlimit" bezeichnet.

Eine *Aktivität* ist normalerweise einem Bildschirm der Anwendung zugeordnet, d. h. die *Aktivität* beginnt, wenn der Bildschirm angezeigt wird, und endet, wenn der Bildschirm geschlossen wird: dies ist der Fall, wenn das Engagement SDK mithilfe der `EngagementActivity`-Klassen integriert wird.

Andererseits können *Aktivitäten* auch manuell mithilfe der Engagement-API gesteuert werden. Dadurch kann ein vorhandener Bildschirm in verschiedene Teilkomponenten unterteilt werden, um weitere Details zur Verwendung dieses Bildschirms zu erhalten (um z. B. zu erfahren, wie häufig und wie lange Dialogfelder innerhalb dieses Bildschirms verwendet werden).

## Melden von Aktivitäten

> [AZURE.IMPORTANT] Wenn Sie die `EngagementActivity`-Klasse und ihre Varianten, wie unter "Integrieren von Engagement unter Android" beschrieben, verwenden, müssen Sie die Aktivitäten nicht gemäß der Beschreibung in diesem Abschnitt melden.

### Starten einer neuen Aktivität durch den Benutzer

			EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
			// Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Sie müssen `startActivity()` bei jeder Änderung der Benutzeraktivität aufrufen. Der erste Aufruf dieser Funktion startet eine neue Benutzersitzung.

Der beste Ort zum Aufrufen dieser Funktion ist die `onResume`-Rückruffunktion der einzelnen Aktivitäten.

### Beenden der aktuellen Aktivität durch den Benutzer

			EngagementAgent.getInstance(this).endActivity();

Sie müssen `endActivity()` mindestens einmal aufrufen, wenn der Benutzer seine letzte Aktivität beendet. Dadurch wird das Engagement-SDK darüber informiert, dass sich der Benutzer derzeit im Leerlauf befindet und die Benutzersitzung geschlossen werden muss, sobald das Sitzungszeitlimit abläuft (wenn Ihr Aufruf von `startActivity()` vor dem Sitzungszeitlimit abläuft, wird die Sitzung einfach fortgesetzt).

Der beste Ort zum Aufrufen dieser Funktion ist die `onPause`-Rückruffunktion der einzelnen Aktivitäten.

## Melden von Ereignissen

### Sitzungsereignisse

Mithilfe von Sitzungsereignissen werden in der Regel Aktionen gemeldet, die von einem Benutzer im Verlauf seiner Sitzung durchgeführt werden.

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

## Melden von Fehlern

### Sitzungsfehler

Sitzungsfehler werden in der Regel dazu verwendet, um die Fehler zu melden, die sich während seiner Sitzung auf den Benutzer auswirken.

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

Im Gegensatz zu Sitzungsfehlern können eigenständige Fehler außerhalb eines Sitzungskontextes auftreten.

**Beispiel:**

Das folgende Beispiel veranschaulicht die Meldung eines Fehlers, sobald auf dem Mobiltelefon nicht ausreichend Arbeitsspeicher zur Verfügung steht, während Ihr Anwendungsprozess aktiv ist.

			public MyApplication extends EngagementApplication {
			
			  @Override
			  protected void onApplicationProcessLowMemory() {
			    EngagementAgent.getInstance(this).sendError("low_memory", null);
			  }
			}

## Melden von Aufträgen

### Beispiel

Angenommen, Sie möchten die Dauer des Anmeldeprozesses melden:
			
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

### Melden von Fehlern während eines Auftrags

Fehler können einem aktiven Auftrag zugeordnet werden, anstatt sie einer aktuellen Benutzersitzung zuzuordnen.

**Beispiel:**

Angenommen, Sie möchten einen Fehler während des Anmeldeprozesses melden:

			[...]
			public void signIn(Context context, ...) {

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

Ereignisse können einem aktiven Auftrag zugeordnet werden, anstatt sie einer aktuellen Benutzersitzung zuzuordnen.

**Beispiel:**

Angenommen, wir verwenden ein soziales Netzwerk sowie einen Auftrag, um die Gesamtzeit zu melden, die der Benutzer mit dem Server verbunden ist. Der Benutzer kann im Hintergrund mit dem Server verbunden bleiben, auch wenn er eine andere Anwendung verwendet oder sich das Mobiltelefon im Ruhemodus befindet und somit keine Sitzung besteht.

Der Benutzer kann Nachrichten von seinen Freunden empfangen. Hierbei handelt es sich um ein Auftragsereignis.
			
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

## Zusätzliche Parameter

Ereignissen, Fehlern, Aktivitäten und Aufträgen können beliebige Daten angehängt werden.

Diese Daten können strukturiert werden. Sie verwenden die "Bundle"-Klasse von Android (sie funktionieren in Android-Vorhaben eigentlich wie zusätzliche Parameter). Beachten Sie, dass ein Bündel Arrays oder andere Bündelinstanzen enthalten kann.

> [AZURE.IMPORTANT] Wenn Sie verpackbare oder serialisierbare Parameter einfügen, stellen Sie sicher, dass ihre `toString()`-Methode implementiert ist, damit eine lesbare Zeichenfolge zurückgegeben wird. Serialisierbare Klassen, die nicht flüchtige Felder enthalten, die nicht serialisiert werden können, führen zu einem Absturz von Android, wenn Sie `bundle.putSerializable("key",value);` aufrufen.

> [AZURE.WARNING] Spärliche Arrays in zusätzlichen Parametern werden nicht unterstützt, d. h. sie werden nicht als Array serialisiert. Sie sollten sie in Standardarrays konvertieren, bevor sie in zusätzlichen Parametern verwendet werden.

### Beispiel

			Bundle extras = new Bundle();
			extras.putString("video_id", 123);
			extras.putString("ref_click", "http://foobar.com/blog");
			EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### Grenzwerte

#### Schlüssel

Jeder Schlüssel im `Bundle` muss dem folgenden regulären Ausdruck entsprechen:

`^[a-zA-Z][a-zA-Z_0-9]*`

Das bedeutet, die Schlüssel müssen mit mindestens einem Buchstaben beginnen, gefolgt von Buchstaben, Ziffern oder Unterstrichen (\_).

#### Größe

Extras sind auf **1024** Zeichen pro Aufruf begrenzt (nach der JSON-Codierung durch den Engagement-Dienst).

Im vorherigen Beispiel hatte der an den Server gesendete JSON-Code eine Länge von 58 Zeichen:

			{"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## Melden von Anwendungsinformationen

Sie können Nachverfolgungsinformationen (oder beliebige andere anwendungsabhängige Informationen) mithilfe der `sendAppInfo()`-Funktion manuell melden.

Beachten Sie, dass diese Informationen inkrementell gesendet werden können, d. h. nur der neueste Wert bleibt für einen Schlüssel für ein angegebenes Gerät erhalten.

Wie bei Ereigniszusätzen wird die "Bundle"-Klasse zum Abstrahieren von Anwendungsinformationen verwendet. Beachten Sie, dass Arrays oder Teilbündel als einfache Zeichenfolgen behandelt werden (mithilfe der JSON-Serialisierung).

### Beispiel

Hier folgt ein Codebeispiel zum Senden von Geschlecht und Geburtstag des Benutzers:

			Bundle appInfo = new Bundle();
			appInfo.putString("status", "premium");
			appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
			EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### Grenzwerte

#### Schlüssel

Jeder Schlüssel im `Bundle` muss dem folgenden regulären Ausdruck entsprechen:

`^[a-zA-Z][a-zA-Z_0-9]*`

Das bedeutet, die Schlüssel müssen mit mindestens einem Buchstaben beginnen, gefolgt von Buchstaben, Ziffern oder Unterstrichen (\_).

#### Größe

Anwendungsinformationen sind auf **1024** Zeichen pro Aufruf begrenzt (nach der JSON-Codierung durch den Engagement-Dienst).

Im vorherigen Beispiel hatte der an den Server gesendete JSON-Code eine Länge von 44 Zeichen:

			{"expiration":"2016-12-07","status":"premium"}

<!--HONumber=47-->
