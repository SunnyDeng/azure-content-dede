<properties 
	pageTitle="Verwenden der Engagement-API auf iOS" 
	description="Neuestes iOS-SDK – Verwenden der Engagement-API auf iOS"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />


#Verwenden der Engagement-API auf iOS

Dieses Dokument ist ein Add-On zum Dokument "So integrieren Sie Engagement auf iOS". Es bietet detaillierte Informationen zur Verwendung der Engagement-API zur Bereitstellung von Anwendungsstatistiken.

Beachten Sie Folgendes: Wenn Sie über Engagement nur Berichte zu Anwendungssitzungen, Aktivitäten, Abstürzen und technischen Informationen bereitstellen möchten, ist es am einfachsten, für alle benutzerdefinierten `UIViewController`-Objekte eine Vererbung aus den entsprechenden `EngagementViewController`-Klassen zu konfigurieren.

Wenn Sie darüber hinaus noch mehr Meldungen wünschen, z. B. wenn Sie anwendungsspezifische Ereignisse, Fehler und Aufträge melden möchten, oder wenn die Aktivitäten Ihrer Anwendung anders als in den `EngagementViewController`-Klassen implementiert gemeldet werden sollen, dann müssen Sie die Engagement-API verwenden.

Die Engagement-API wird von der `EngagementAgent`-Klasse zur Verfügung gestellt. Eine Instanz dieser Klasse kann durch Aufruf der `[EngagementAgent shared]` statischen Methode abgerufen werden (beachten Sie, dass das zurückgegebene `EngagementAgent`-Objekt ein Singleton-Objekt ist).

Vor jeglichen API-Aufrufen muss das `EngagementAgent`-Objekt durch Aufrufen der Methode `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];` initialisiert werden.

##Engagement-Konzepte

In den folgenden Abschnitten werden die [Mobile Engagement-Konzepte](mobile-engagement-concepts.md) für die iOS-Plattform genauer dargestellt.

### `Session` und `Activity`

Eine *Aktivität* ist üblicherweise mit einem Bildschirm einer Anwendung verknüpft, d. h. die *Aktivität* startet, wenn der Bildschirm angezeigt wird und endet, wenn der Bildschirm geschlossen wird. Dies ist der Fall, wenn das Engagement-SDK über die `EngagementViewController`-Klassen integriert wird.

Aber *Aktivitäten* können auch manuell mithilfe der Engagement-API gesteuert werden. Auf diese Weise kann ein vorhandener Bildschirm in mehrere Unterabschnitte geteilt werden, um mehr Details über die Verwendung des Bildschirms zu erhalten (um beispielsweise zu erfahren, wie häufig und wie lange Dialoge in diesem Bildschirm verwendet werden).

##Berichterstellung für Aktivitäten

### Benutzer startet eine neue Aktivität

			[[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Sie müssen jedes Mal `startActivity()` aufrufen, wenn sich die Benutzeraktivität ändert. Der erste Aufruf dieser Funktion startet eine neue Benutzersitzung.

### Der Benutzer beendet seine aktuelle Aktivität

			[[EngagementAgent shared] endActivity];

> [AZURE.WARNING]Sie sollten diese Funktion **NIE** selbst aufrufen – es sei denn, Sie möchten eine Verwendung Ihrer Anwendung in mehrere Sitzungen unterteilen. Durch einen Aufruf dieser Funktion wird die Sitzung sofort beendet, sodass ein nachfolgender Aufruf von `startActivity()` eine neue Sitzung startet. Diese Funktion wird durch das SDK automatisch aufgerufen, wenn Ihre Anwendung geschlossen wird.

##Berichterstellung für Ereignisse

### Sitzungsereignisse

Sitzungsereignisse werden normalerweise verwendet, um die Aktionen eines Benutzers während seiner Sitzung zu melden.

**Beispiel ohne zusätzliche Daten:**

			@implementation MyViewController {
			   [...]
			   - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
			   {
			    [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
			        ...
			    [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
			        ...
			   }
			   [...]
			}

**Beispiel mit zusätzlichen Daten:**

			@implementation MyViewController {
			   [...]
			   - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
			   {
			    [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
			        ...
			    NSMutableDictionary* extras = [NSMutableDictionary dictionary];
			    [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
			    [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
			    [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
			        ...
			   }
			   [...]
			}

### Eigenständige Ereignisse

Im Gegensatz zu Sitzungsereignissen können eigenständige Ereignisse außerhalb des Kontexts einer Sitzung verwendet werden.

**Beispiel:**

			[[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

##Melden von Fehlern

### Sitzungsfehler

Sitzungsfehler werden normalerweise zum Melden der Fehler verwendet, die Auswirkungen auf den Benutzer während seiner Sitzung haben.

**Beispiel:**

			/** The user has entered invalid data in a form */
			@implementation MyViewController {
			  [...]
			  -(void)onMyFormSubmitted:(MyForm*)form {
			    [...]
			    /* The user has entered an invalid email address */
			    [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
			    [...]
			  }
			  [...]
			}

### Eigenständige Fehler

Im Gegensatz zu Sitzungsfehlern können eigenständige Fehler außerhalb des Kontexts einer Sitzung verwendet werden.

**Beispiel:**

			[[EngagementAgent shared] sendError:@"something_failed" extras:nil];

##Berichterstellung für Aufträge

**Beispiel:**

Angenommen, Sie möchten die Dauer des Anmeldevorgangs melden:

			[...]
			-(void)signIn 
			{
			  /* Start job */
			  [[EngagementAgent shared] startJob:@"sign_in" extras:nil];
			
			  [... sign in ...]
			
			  /* End job */
			  [[EngagementAgent shared] endJob:@"sign_in"];
			}
			[...]

### Berichterstellung zu Fehlern während eines Auftrags

Fehler können mit einem ausgeführten Auftrag in Zusammenhang stehen anstatt mit der aktuellen Benutzersitzung.

**Beispiel:**

Angenommen, Sie möchten einen Fehler während des Anmeldevorgangs melden:

			[...]
			-(void)signin
			{
			  /* Start job */
			  [[EngagementAgent shared] startJob:@"sign_in" extras:nil];
			
			  BOOL success = NO;
			  while (!success) {
			    /* Try to sign in */
			    NSError* error = nil;
			    [self trySigin:&error];
			    success = error == nil;
			
			    /* If an error occured report it */
			    if(!success)
			    {
			      [[EngagementAgent shared] sendJobError:@"sign_in_error"
			                     jobName:@"sign_in"
			                      extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];
			
			      /* Retry after a moment */
			      [NSThread sleepForTimeInterval:20];
			    }
			  }
			
			  /* End job */
			  [[EngagementAgent shared] endJob:@"sign_in"];
			};
			[...]

### Ereignisse während eines Auftrags

Ereignisse können statt mit der aktuellen Benutzersitzung in Zusammenhang mit einem ausgeführten Auftrag stehen.

**Beispiel:**

Angenommen, wir verfügen über ein soziales Netzwerk und verwenden einen Auftrag, um die Zeit insgesamt zu melden, die der Benutzer mit dem Server verbunden ist. Der Benutzer kann Nachrichten von Freunden empfangen, hierbei handelt es sich um ein Auftragsereignis.

			[...]
			- (void) signin
			{
			  [...Sign in code...]
			  [[EngagementAgent shared] startJob:@"connection" extras:nil];
			}
			[...]
			- (void) signout
			{
			  [...Sign out code...]
			  [[EngagementAgent shared] endJob:@"connection"];
			}
			[...]
			- (void) onMessageReceived 
			{
			  [...Notify user...]
			  [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
			}
			[...]

##Zusätzliche Parameter

Ereignissen, Fehlern, Aktivitäten und Aufträgen können beliebige Daten zugeordnet werden.

Diese Daten können strukturiert werden und die iOS-Klasse "NSDictionary" nutzen.

Zusätzliche Daten können `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` oder weitere `NSDictionary` Instanzen enthalten.

> [AZURE.NOTE]Der zusätzliche Parameter wird im JSON-Format serialisiert. Wenn Sie andere Objekte als die oben beschriebene übergeben möchten, müssen Sie die folgende Methode in Ihrer Klasse implementieren:
>
			 -(NSString*)JSONRepresentation; 
>
> Die Methode sollte eine JSON-Darstellung des Objekts zurückgeben.

### Beispiel

			NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
			[extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
			[extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
			[[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### Grenzen

#### Schlüssel

Jeder Schlüssel in `NSDictionary` muss mit dem folgenden regulären Ausdruck übereinstimmen:

`^[a-zA-Z][a-zA-Z_0-9]*`

Das bedeutet, dass Schlüssel mit mindestens einem Buchstaben, gefolgt von Buchstaben, Ziffern oder Unterstrichen (_) beginnen müssen.

#### Größe

Zusätzliche Daten sind auf **1024** Zeichen pro Aufruf beschränkt (nach Codierung in JSON durch den Engagement-Agent).

Im vorherigen Beispiel enthält die an den Server gesendete JSON 58 Zeichen:

			{"ref_click":"http://foobar.com/blog","video_id":"123"}

##Informationen zur Berichterstellung

Sie können Berichte zur Nachverfolgung (oder zu anderen anwendungsspezifischen Informationen) mithilfe der `sendAppInfo:`-Funktion manuell erstellen.

Beachten Sie, dass diese Informationen inkrementell gesendet werden können: Nur der letzte Wert für einen bestimmten Schlüssel wird für ein bestimmtes Gerät gespeichert.

Wie bei den zusätzlichen Daten zu Ereignissen wird die `NSDictionary`-Klasse zum Abstrahieren der Anwendungsinformationen verwendet. Hierbei werden Arrays oder Teilwörterbücher als flache Zeichenfolgen behandelt (unter Verwendung der JSON-Serialisierung).

**Beispiel:**

			NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
			[appInfo setObject:@"female" forKey:@"gender"];
			[appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
			[[EngagementAgent shared] sendAppInfo:appInfo];

### Grenzen

#### Schlüssel

Jeder Schlüssel in `NSDictionary` muss mit dem folgenden regulären Ausdruck übereinstimmen:

`^[a-zA-Z][a-zA-Z_0-9]*`

Das bedeutet, dass Schlüssel mit mindestens einem Buchstaben, gefolgt von Buchstaben, Ziffern oder Unterstrichen (_) beginnen müssen.

#### Größe

Anwendungsinformationen sind beschränkt auf **1024** Zeichen pro Aufruf (einmal im JSON-Format vom Agent Engagement codiert).

Im vorherigen Beispiel enthält das an den Server gesendete JSON 44 Zeichen:

			{"birthdate":"1983-12-07","gender":"female"}


<!--HONumber=54-->