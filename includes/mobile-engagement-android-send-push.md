
###Aktualisieren der Manifestdatei zum Aktivieren von Benachrichtigungen

Kopieren Sie die nachfolgenden In-App-Messaging-Ressourcen in die Datei "Manifest.xml" zwischen die Tags `<application>` und `</application>`.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

###Geben Sie ein Symbol für Benachrichtigungen an.

Fügen Sie den folgenden XML-Codeausschnitt in die Datei "Manifest.xml" zwischen die Tags `<application>` und `</application>` ein.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Dies definiert das Symbol, das in System- und In-App-Benachrichtigungen angezeigt wird. Es ist für In-App-Benachrichtigungen optional, für Systembenachrichtigungen jedoch obligatorisch. Android weist Systembenachrichtigungen mit ungültigen Symbolen zurück.

Stellen Sie sicher, dass Sie ein Symbol in einem der **ziehbaren** Ordner verwenden (z. B. ``engagement_close.png``). **Mipmap**-Ordner werden nicht unterstützt.

>[AZURE.NOTE] Sie sollten das Symbol für das **Startprogramm** nicht verwenden. Es weist eine andere Auflösung auf und befindet sich in der Regel in den Mipmap-Ordnern, die nicht unterstützt werden.

Für reale Apps können Sie ein Symbol verwenden, das entsprechend den [Android-Entwurfsrichtlinien](http://developer.android.com/design/patterns/notifications.html) für Benachrichtigungen geeignet ist.

>[AZURE.TIP] Um sicherzustellen, dass Sie die richtigen Auflösungen für Symbole verwenden, betrachten Sie [diese Beispiele](https://www.google.com/design/icons). Scrollen Sie nach unten zum Abschnitt **Benachrichtigung**, klicken Sie auf ein Symbol, und klicken Sie dann auf `PNGS`, um den ziehbaren Symbolsatz herunterzuladen. Es wird für jede Version des Symbols angezeigt, welche ziehbaren Ordner für welche Auflösung verwendet werden.

###Aktivieren der App für den Empfang von GCM-Pushbenachrichtigungen

1. Fügen Sie in "Manifest.xml" Folgendes zwischen die Tags `<application>` und `</application>` ein, nachdem Sie die aus der Google Play-Konsole abgerufene `project number` ersetzt haben. \\n ist beabsichtigt, stellen Sie daher sicher, dass Sie die Projektnummer damit endet.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Fügen Sie den Code unten in die Datei „Manifest.xml“ zwischen den Tags `<application>` und `</application>` ein. Ersetzen Sie den Paketnamen <Your package name>.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. Fügen Sie die letzte Berechtigungsgruppe, die hervorgehoben ist, vor dem `<application>`-Tag hinzu. Ersetzen Sie `<Your package name>` durch den tatsächlichen Paketnamen der Anwendung.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

<!---HONumber=AcomDC_0330_2016-->