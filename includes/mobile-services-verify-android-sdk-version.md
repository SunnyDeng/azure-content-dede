Aufgrund ständiger Weiterentwicklung stimmt die Version des in Eclipse installierten Android-SDK unter Umständen nicht mit der Version im Code überein. In diesem Lernprogramm wird von Version 21 des Android-SDK ausgegangen. Das war die neueste Version zum Zeitpunkt der Verfassung. Mit neuen Ausgaben des SDK kann die Versionsnummer größer werden. Es empfiehlt sich, die neueste verfügbare Version zu benutzen.

Zwei Symptome nicht übereinstimmender Versionen sind:

1. Achten Sie auf Meldungen im unteren Feld der Eclipse-Konsole. Möglicherweise sehen Sie Fehlermeldungen in der Form von **Unable to resolve target 'android-n'**.

2. Standard-Android-Objekte im Code, die auf Grundlage von import-Anweisungen aufgelöst werden sollten, können Fehlermeldungen verursachen.

Wenn eines dieser Symptome auftritt, stimmt die Version des in Eclipse installierten Android-SDK möglicherweise nicht mit dem SDK-Ziel des heruntergeladenen Projekts überein.  Zur Überprüfung der Version nehmen Sie die folgenden Änderungen vor:


1. Klicken Sie in Eclipse auf **Window** und dann auf **Android SDK Manager**. Wenn nicht die neueste Version der SDK-Plattform installiert ist, so klicken Sie zur Installation. Notieren Sie sich die Versionsnummer.

2. Öffnen Sie die Projektdatei **AndroidManifest.xml**. Vergewissern Sie sich im Element **uses-sdk**, dass für **targetSdkVersion** die neueste installierte Version festgelegt ist. Das Tag **uses-sdk** könnte wie folgt aussehen:
 
	 	    <uses-sdk
	 	        android:minSdkVersion="8"
	 	        android:targetSdkVersion="21" />
	
3. Klicken Sie im Eclipse-Paket-Explorer mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Properties** und dann in der linken Spalte **Android** aus. Stellen Sie sicher, dass für **Project Build Target** dieselbe SDK-Version wie für **targetSdkVersion** festgelegt ist.

<!--HONumber=35.1-->
