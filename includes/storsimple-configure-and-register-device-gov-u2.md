<!--author=SharS last changed: 02/22/2016-->

### So konfigurieren und registrieren Sie das Gerät

1. Greifen Sie an der seriellen Konsole Ihres StorSimple-Geräts auf die Windows PowerShell-Benutzeroberfläche zu. Anweisungen dazu finden Sie unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](#use-putty-to-connect-to-the-device-serial-console). **Achten Sie darauf, dass Sie die Vorgehensweise genau befolgen. Andernfalls sind Sie nicht in der Lage, auf die Konsole zuzugreifen.**

2. Drücken Sie in der Sitzung, die geöffnet wird, einmal die EINGABETASTE, um eine Eingabeaufforderung zu öffnen.

3. Sie werden aufgefordert, die Sprache auszuwählen, die für Ihr Gerät festgelegt werden soll. Geben Sie die Sprache an, und drücken Sie dann die EINGABETASTE.

    ![StorSimple – Konfigurieren und Registrieren des Geräts 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)

4. Wählen Sie im Menü der seriellen Konsole, das angezeigt wird, Option 1 aus, d. h. die Anmeldung mit Vollzugriff.

    ![StorSimple – Registrieren des Geräts 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
  
5. Führen Sie die folgenden Schritte aus, um die mindestens erforderlichen Netzwerkeinstellungen für Ihr Gerät zu konfigurieren.

    > [AZURE.IMPORTANT] Diese Konfigurationsschritte müssen für den aktiven Controller des Geräts ausgeführt werden. Das Menü der seriellen Konsole gibt den Zustand des Controllers in der Bannermeldung an. Wenn Sie nicht mit dem aktiven Controller verbunden sind, trennen Sie die Verbindung, und stellen Sie dann eine Verbindung mit dem aktiven Controller her.

      1. Geben Sie an der Eingabeaufforderung Ihr Kennwort ein. Das Standardkennwort für das Gerät lautet **Password1**.

      2. Geben Sie den folgenden Befehl ein:

           `Invoke-HcsSetupWizard`

      3. Es wird ein Installations-Assistent angezeigt, der Sie beim Konfigurieren der Netzwerkeinstellungen für das Gerät unterstützt. Geben Sie die folgenden Informationen an:

       - IP-Adresse für die Netzwerkschnittstelle DATA 0
       - Subnetzmaske
       - Gateway
       - IP-Adresse für den primären DNS-Server
       - IP-Adresse für den sekundären DNS-Server
 
        > [AZURE.NOTE] Sie müssen ggf. einige Minuten warten, bis die Subnetzmaske und die DNS-Einstellungen übernommen werden.

      4. (Optional) Konfigurieren Sie Ihren Webproxyserver.

      > [AZURE.IMPORTANT] Die Webproxykonfiguration ist optional. Achten Sie jedoch bei Verwendung eines Webproxys darauf, dass dieser nur hier konfiguriert werden kann. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](storsimple-configure-web-proxy.md).

6. Drücken Sie STRG+C, um den Setup-Assistenten zu beenden.
 
7. Installieren Sie die Updates wie folgt:
      1. Verwenden Sie das folgende Cmdlet, um IP-Adressen für beide Controller festzulegen:

         `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`

      2. Führen Sie an der Eingabeaufforderung Folgendes aus: `Get-HcsUpdateAvailability`. Sie sollten eine Benachrichtigung über Updates erhalten.

      3. Führen Sie `Start-HcsUpdate` aus. Sie können diesen Befehl auf einem beliebigen Knoten ausführen. Die Updates werden auf dem ersten Domänencontroller angewendet, und für den Controller wird ein Failover ausgeführt. Anschließend werden die Updates auf dem anderen Domänencontroller angewendet.

      Sie können den Fortschritt des Updateauftrags verfolgen, indem Sie `Get-HcsUpdateStatus` ausführen.

       Die folgende Beispielausgabe zeigt den Status des Updates.
  
        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     Die folgende Beispielausgabe zeigt an, dass das Update abgeschlossen ist.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

      Es kann bis zu elf Stunden dauern, bis alle Updates einschließlich der Windows-Updates angewendet wurden.

10. Führen Sie das folgende Cmdlet aus, um das Gerät auf das Microsoft Azure-Portal für Behörden zu verweisen. (Es wird standardmäßig auf das öffentliche klassische Azure-Portal verwiesen.) Beide Controller werden dadurch neu gestartet. Es wird empfohlen, dass Sie zwei PuTTY-Sitzungen verwenden, um gleichzeitig eine Verbindung mit beiden Controllern herzustellen, sodass Sie sehen können, wenn jeder Controller neu gestartet wird.

     `Set-CloudPlatform -AzureGovt_US`

    Es wird eine Bestätigungsmeldung angezeigt. Übernehmen Sie den Standardwert (**Y**).

11. Führen Sie das folgende Cmdlet aus, um die Installation fortzusetzen:

     `Invoke-HcsSetupWizard`

     ![Fortsetzen des Installations-Assistenten](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)

    Wenn Sie das Setup fortsetzen, wird die Assistenten-Version aus Update 2 verwendet.

12. Akzeptieren Sie die Netzwerkeinstellungen. Es wird eine Validierungsmeldung angezeigt, nachdem Sie alle Einstellungen akzeptiert haben.
 
13. Aus Sicherheitsgründen läuft das Administratorkennwort für das Gerät nach der ersten Sitzung ab, und Sie müssen es jetzt ändern. Geben Sie, wenn Sie dazu aufgefordert werden, ein Administratorkennwort für das Gerät an. Ein gültiges Administratorkennwort für das Gerät muss zwischen 8 und 15 Zeichen lang sein. Das Kennwort muss aus drei der folgenden Zeichenkategorien bestehen: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen.

	<br/>![StorSimple – Registrieren des Geräts 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)

14. Der letzte Schritt im Installations-Assistenten besteht im Registrieren Ihres Geräts beim StorSimple-Manager-Dienst. Zu diesem Zweck benötigen Sie den Dienstregistrierungsschlüssel, den Sie unter [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](storsimple-get-service-registration-key-gov.md) abgerufen haben. Nachdem Sie den Registrierungsschlüssel bereitgestellt haben, müssen Sie ggf. einige Minuten warten, bis das Gerät registriert wurde.

      > [AZURE.NOTE] Sie können jederzeit STRG+C drücken, um den Installations-Assistenten zu beenden. Wenn Sie alle Netzwerkeinstellungen (IP-Adresse für DATA 0, Subnetzmaske und Gateway) angegeben haben, werden Ihre Einträge beibehalten.

	![Fortschritt der StorSimple-Registrierung](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)

15. Nachdem das Gerät registriert wurde, wird ein Schlüssel für die Dienstdatenverschlüsselung angezeigt. Kopieren Sie diesen Schlüssel, und bewahren Sie ihn an einem sicheren Ort auf. **Dieser Schlüssel ist mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte bei StorSimple-Manager-Dienst erforderlich.** Weitere Informationen zu diesem Schlüssel finden Sie unter [StorSimple-Sicherheit](../articles/storsimple/storsimple-security.md).
	
	![StorSimple – Registrieren des Geräts 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)

      > [AZURE.IMPORTANT] Markieren Sie zum Kopieren des Texts aus dem Fenster der seriellen Konsole den Text einfach. Sie sollten damit in der Lage sein, ihn in die Zwischenablage oder einen beliebigen Texteditor zu kopieren.
      > 
      > Verwenden Sie NICHT STRG+C zum Kopieren des Schlüssels für die Dienstdatenverschlüsselung. STRG+C bewirkt, dass der Installations-Assistent beendet wird. Dies führt dazu, dass das Geräteadministratorkennwort nicht geändert und das Gerät auf das Standardkennwort zurückgesetzt wird.

16. Schließen Sie die serielle Konsole.

17. Kehren Sie zum Azure-Portal für Behörden zurück, und führen Sie die folgenden Schritte aus:
  1. Doppelklicken auf Ihren StorSimple-Manager-Dienst, um auf die Seite **Schnellstart** zuzugreifen.
  2. Klicken Sie auf **View connected devices**.
  3. Vergewissern Sie sich auf der Seite **Geräte**, dass das Gerät erfolgreich mit dem Dienst verbunden wurde, indem Sie seinen Status überprüfen. Als Gerätestatus sollte **Online** angezeigt werden.
   
    	![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov-u2/HCS_DeviceOnline-gov-include.png) 
  
        Wenn der Gerätestatus „Offline“ ist, warten Sie ein paar Minuten, bis das Gerät online geschaltet wird. 

        Wenn das Gerät nach einigen Minuten immer noch offline ist, müssen Sie sicherstellen, dass Ihr Firewallnetzwerk wie unter [Netzwerkanforderungen für Ihr StorSimple-Gerät](../articles/storsimple/storsimple-system-requirements.md). 

        Stellen Sie sicher, dass Port 9354 für die ausgehende Kommunikation geöffnet ist, da dieser Port von Service Bus für die Kommunikation zwischen dem StorSimple Manager-Dienst und Geräten verwendet wird.
     
        

<!---HONumber=AcomDC_0224_2016-->
