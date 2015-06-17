<properties 
   pageTitle="Beheben von Problemen mit der Bereitstellung von StorSimple-Geräten"
   description="Beschreibt die Diagnose und Problembehandlung von Fehlern, die beim Bereitstellen von StorSimple auftreten."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/06/2015"
   ms.author="v-sharos" />

#Beheben von Problemen mit der Bereitstellung von StorSimple-Geräten

##Übersicht

Dieser Artikel bietet hilfreiche Anleitungen zur Behandlung von Problemen bei der Microsoft Azure StorSimple-Bereitstellung . Er beschreibt allgemeine Probleme, mögliche Ursachen und empfohlene Schritte zum Beheben von Problemen bei der StorSimple-Konfiguration. Diese Informationen gelten sowohl für lokale physische als auch für virtuelle StorSimple-Geräte.

> [AZURE.NOTE]Gerätekonfigurationsbezogene Probleme können bei der ersten Bereitstellung oder im späteren Betrieb auftreten. In diesem Artikel liegt der Fokus auf der Problembehandlung bei der erstmaligen Bereitstellung. Um Probleme mit betriebsbereiten Geräten zu beheben, navigieren Sie zu [Troubleshoot operational device issues](storsimple-troubleshoot-operational-device.md) (in englischer Sprache).

Dieser Artikel beschreibt weiterhin die Tools für die Problembehandlung bei StorSimple-Bereitstellungen und bietet eine schrittweise Anleitung zur Problembehandlung.

##Probleme bei der erstmaligen Bereitstellung

Wenn bei der ersten Bereitstellung des Geräts ein Problem auftritt, berücksichtigen Sie Folgendes:

- Wenn Sie Probleme mit einem physischen Gerät beheben, stellen Sie sicher, dass die Hardware installiert und konfiguriert ist, wie unter [Hardwareinstallation Ihres Geräts](https://msdn.microsoft.com/library/azure/dn772375.aspx) beschrieben.
- Überprüfen Sie die Voraussetzungen für die Bereitstellung. Vergewissern Sie sich, dass alle erforderlichen Informationen aus der [Bereitstellungsprüfliste](storsimple-deployment-walkthrough.md#pre-installation checklist) vorliegen.
- Überprüfen Sie die StorSimple-Versionshinweise, um festzustellen, ob das Problem dort beschrieben wird. Die Versionshinweise enthalten Problemumgehungen für bekannte Installationsprobleme. 

Während der Bereitstellung der Geräte treten die meisten Probleme im Zusammenhang mit der Ausführung des Setup-Assistenten sowie der Registrierung des Geräts über Windows PowerShell für StorSimple auf. (Windows PowerShell für StorSimple wird verwendet, um das StorSimple-Gerät zu registrieren und zu konfigurieren. Weitere Informationen über die Geräteregistrierung finden Sie unter [Registrieren Ihres Geräts](https://msdn.microsoft.com/library/azure/dn757742.aspx).)

In den folgenden Abschnitten erhalten Sie Hilfestellung bei Problemen, die bei der erstmaligen Konfiguration des StorSimple-Geräts auftreten können.

##Schritte bei der erstmaligen Ausführung des Setup-Assistenten

In den folgenden Schritten werden die Schritte des Setup-Assistenten zusammengefasst. Ausführliche Informationen zum Setup finden Sie unter [StorSimple deployment walkthrough](storsimple-deployment-walkthrough.md) (in englischer Sprache).

1. Führen Sie das Cmdlet [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) aus, um den Setup-Assistenten zu starten, der Sie durch die verbleibenden Schritte führt. 
2. Konfigurieren des Netzwerks: im Setup-Assistenten können Sie die Netzwerkeinstellungen für die DATA 0-Netzwerkschnittstelle auf dem StorSimple-Gerät konfigurieren. Dies umfasst die folgenden Einstellungen:
  - Virtuelle IP-Adresse (VIP), Subnetzmaske und Gateway – Das Cmdlet [Set HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) wird im Hintergrund ausgeführt. Dieses Cmdlet konfiguriert IP-Adresse, Subnetzmaske und Gateway für die DATA 0-Netzwerkschnittstelle auf dem StorSimple-Gerät.
  - Primärer DNS-Server – das Cmdlet [Set HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) wird im Hintergrund ausgeführt. Konfiguriert die DNS-Einstellungen für Ihre StorSimple-Lösung.
  - NTP-Server – Das Cmdlet [Set HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) wird im Hintergrund ausgeführt. Konfiguriert die NTP-Servereinstellungen für Ihre StorSimple-Lösung.
  - Optionaler Webproxy – Das Cmdlet [Set HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) wird im Hintergrund ausgeführt. Bestimmt und aktiviert die Webproxy-Konfiguration für Ihre StorSimple-Lösung.
3. Einrichten der Kennwörter: Der nächste Schritt besteht in der Einrichtung der Kennwörter für den Geräteadministrator und für StorSimple-Momentaufnahme-Manager.
  - Das Geräteadministratorkennwort wird zur Anmeldung am Gerät verwendet. Das Standardkennwort für das Gerät lautet *Password1*.
  - Das StorSimple-Momentaufnahme-Manager-Kennwort ist erforderlich, wenn Sie ein Gerät für die Verwendung mit dem StorSimple-Momentaufnahme-Manager konfigurieren. Sie müssen zunächst das Kennwort im Setup-Assistenten festlegen. Anschließend können Sie es im StorSimple-Manager-Dienst ändern und erneut festlegen. Mit diesem Kennwort wird das Gerät im StorSimple-Momentaufnahme-Manager authentifiziert.
 
    > [AZURE.IMPORTANT]Kennwörter werden vor der Registrierung erfasst, aber erst nach erfolgreicher Registrierung des Geräts angewendet. Wenn beim Zuweisen des Kennworts ein Fehler auftritt, werden Sie aufgefordert, das Kennwort erneut anzugeben, bis die erforderlichen Kennwörter (gemäß den Komplexitätsanforderungen) erfasst wurden.

4. Registrieren des Geräts: Im letzten Schritt wird das Gerät im StorSimple-Manager-Dienst in Microsoft Azure registriert. Die Registrierung erfordert ein [Abrufen den Dienstregistrierungsschlüssels](https://msdn.microsoft.com/library/azure/cd4dee49-6ae8-4ff0-b79b-74b2027cb694#sec03) aus dem Azure-Verwaltungsportal sowie dessen Angabe im Setup-Assistenten. Nachdem das Gerät erfolgreich registriert wurde, wird ein Verschlüsselungsschlüssel für Dienstdaten bereitgestellt. Achten Sie darauf, dass Sie diesen Verschlüsselungsschlüssel an einem sicheren Ort speichern, da alle nachfolgenden Geräte mit dem Dienst registriert werden müssen.

##Häufige Fehler bei der Gerätebereitstellung

Die folgenden Tabellen enthalten häufige Fehler, die auftreten können, wenn Sie:

- die erforderlichen Netzwerkeinstellungen konfigurieren .
- die optionalen Webproxy-Einstellungen konfigurieren.
- die Kennwörter für den Geräteadministrator und für den StorSimple-Momentaufnahme-Manager einrichten. 
- das Gerät registrieren. 

###Fehler, die beim Konfigurieren der erforderlichen Netzwerkeinstellungen auftreten

| Nr.| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
| ---| ------------- | --------------- | ------------------ |
| 1 | Invoke-HcsSetupWizard: Dieser Befehl kann nur auf dem aktiven Controller ausgeführt werden. | Die Konfiguration wurde für den passiven Controller ausgeführt.| Führen Sie diesen Befehl auf dem aktiven Controller aus. Weitere Informationen finden Sie unter [Identifizieren eines aktiven Controllers auf Ihrem Gerät](https://msdn.microsoft.com/library/azure/dn790262.aspx).|
| 2 | Invoke-HcsSetupWizard: Gerät ist nicht bereit. | Es gibt Probleme mit der DATA 0-Netzwerkverbindung.| Überprüfen Sie die physische DATA 0-Netzwerkkonnektivität.|
| 3 | Invoke-HcsSetupWizard: Es liegt ein IP-Adressenkonflikt mit einem anderen System im Netzwerk vor (Ausnahme von HRESULT: 0x80070263). | Die für DATA 0 angegebene IP-Adresse wird bereits von einem anderen System verwendet. | Geben Sie eine neue IP-Adresse an, die nicht belegt ist.|
| 4 | Invoke-HcsSetupWizard: Fehler in einer Clusterressource. (Ausnahme von HRESULT:0x800713AE). | Doppelte VIP. Die angegebene IP-Adresse wird bereits verwendet.| Geben Sie eine neue IP-Adresse an, die nicht belegt ist.|
| 5 | Invoke-HcsSetupWizard: Ungültige IPv4-Adresse. | Die IP-Adresse hat das falsche Format.| Überprüfen Sie das Format, und geben Sie die IP-Adresse erneut an. Weitere Informationen finden Sie unter [Ipv4 Addressing][1] (in englischer Sprache). |
| 6 | Invoke-HcsSetupWizard: Ungültige IPv6-Adresse. | Die IP-Adresse hat das falsche Format.| Überprüfen Sie das Format, und geben Sie die IP-Adresse erneut an. Weitere Informationen finden Sie unter [Ipv6 Addressing][2] (in englischer Sprache).|
| 7 | Invoke-HcsSetupWizard: Es sind keine Endpunkte mehr von der Endpunktzuordnung verfügbar. (Ausnahme von HRESULT: 0x800706D9) | Die Clusterfunktionalität ist nicht funktionsfähig. | [Wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx) für weitere Schritte.

###Fehler, die beim Konfigurieren der optionalen Webproxy-Einstellungen auftreten

| Nr.| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
| ---| ------------- | --------------- | ------------------ |
| 1 | Invoke-HcsSetupWizard: Ungültiger Parameter (Ausnahme von HRESULT: 0x80070057) | Einer der Parameter für die Proxyeinstellungen ist ungültig.| Der URI wird nicht im richtigen Format bereitgestellt. Verwenden Sie das folgende Format: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | Invoke-HcsSetupWizard: RPC-Server nicht verfügbar (Ausnahme von HRESULT: 0x800706ba) | Im Folgenden sind die Hauptursachen aufgeführt:<ol><li>Der Cluster ist nicht aktiv.</li><li>Der passive Controller kann nicht mit dem aktiven Controller kommunizieren, und der Befehl wird vom passiven Controller ausgeführt.</li></ol> | Je nach Hauptursache:<ol><li>[Wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx), um sicherzustellen, dass der Cluster aktiv ist.</li><li>Führen Sie den Befehl auf dem aktiven Controller aus. Wenn Sie den Befehl auf dem passiven Controller ausführen möchten, müssen Sie sicherstellen, dass der passive Controller mit dem aktiven Controller kommunizieren kann. Sie müssen sich an den [Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx) wenden, wenn diese Verbindung beeinträchtigt ist.</li></ol> |
| 3 | Invoke-HcsSetupWizard: RPC-Serverfehler (Ausnahme von HRESULT: 0x800706be) | Cluster ist nicht verfügbar. | [Wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx), um sicherzustellen, dass der Cluster aktiv ist.|
| 4 | Invoke-HcsSetupWizard: Clusterressource wurde nicht gefunden (Ausnahme von HRESULT: 0x8007138f) | Die Clusterressource wurde nicht gefunden. Dies kann auftreten, wenn die Installation fehlerhaft verlaufen ist. | Möglicherweise müssen Sie das Gerät auf die werksseitigen Standardeinstellungen zurücksetzen. [Wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx), um eine Clusterressource zu erstellen.|
| 5 | Invoke-HcsSetupWizard: Clusterressource ist nicht online (Ausnahme von HRESULT: 0x8007138c)| Die Clusterressourcen sind nicht online. | [Wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx) für weitere Schritte.|

###Fehler, die beim Einrichten der Kennwörter für den Geräteadministrator und für den StorSimple-Momentaufnahme-Manager auftreten.

Das Standardkennwort für den Geräteadministrator lautet *Password1*. Dieses Kennwort läuft nach der ersten Anmeldung ab. Aus diesem Grund müssen Sie den Setup-Assistenten verwenden, um es zu ändern. Sie müssen ein neues Kennwort für den Geräteadministrator angeben, wenn Sie das Gerät zum ersten Mal registrieren.

Wenn Sie die StorSimple-Momentaufnahme-Manager-Software auf dem Windows Server-Host zum Verwalten des Geräts verwenden, müssen Sie auch ein Kennwort für den StorSimple-Momentaufnahme-Manager während der erstmaligen Registrierung bereitstellen.

Stellen Sie sicher, dass Ihre Kennwörter die folgenden Anforderungen erfüllen:

- Ihr Kennwort muss zwischen 8 und 15 Zeichen lang sein.
- Ihr StorSimple-Momentaufnahme-Manager-Kennwort sollte 14 oder 15 Zeichen lang sein.
- Kennwörter sollten 3 von 4 folgenden Zeichentypen enthalten: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen. 
- Ihr Kennwort muss sich von den letzten 24 Kennwörtern unterscheiden.

Bedenken Sie außerdem, dass die Kennwörter jedes Jahr ablaufen und erst geändert werden können, nachdem das Gerät erfolgreich registriert wurde. Wenn die Registrierung aus irgendeinem Grund fehlschlägt, werden die Kennwörter nicht geändert. Weitere Informationen zum Einrichten der Kennwörter für den Geräteadministrator und für den StorSimple-Momentaufnahme-Manager finden Sie unter [Kennwörter](https://msdn.microsoft.com/library/azure/00fb2b46-e29e-4c4d-90a8-1dbb7db3672e#sec_02).

Beim Einrichten der Geräteadministrator- und StorSimple-Momentaufnahme-Manager-Kennwörter können einer oder mehrere der folgenden Fehler auftreten.

| Nr.| Fehlermeldung | Empfohlene Maßnahme |
| ---| ------------- | ------------------ | 
| 1 | Das Kennwort überschreitet die maximale Länge. |Verwenden Sie ein Kennwort, das die folgenden Anforderungen erfüllt:<ul><li>Ihr Geräteadministratorkennwort muss zwischen 8 und 15 Zeichen lang sein.</li><li>Ihr StorSimple-Momentaufnahme-Manager-Kennwort muss 14 oder 15 Zeichen lang sein.</li></ul> | 
| 2 | Das Kennwort erfüllt nicht die erforderliche Länge. | Verwenden Sie ein Kennwort, das die folgenden Anforderungen erfüllt:<ul><li>Ihr Geräteadministratorkennwort muss zwischen 8 und 15 Zeichen lang sein.</li><li>Ihr StorSimple-Momentaufnahme-Manager-Kennwort muss 14 oder 15 Zeichen lang sein.</lu></ul> |
| 3 | Das Kennwort muss Kleinbuchstaben enthalten. | Kennwörter sollten drei der folgenden vier Zeichentypen enthalten: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen. Stellen Sie sicher, dass Ihr Kennwort diese Anforderungen erfüllt. |
| 4 | Das Kennwort muss Zahlen enthalten. | Kennwörter sollten drei der folgenden vier Zeichentypen enthalten: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen. Stellen Sie sicher, dass Ihr Kennwort diese Anforderungen erfüllt. |
| 5 | Das Kennwort muss Sonderzeichen enthalten. | Kennwörter sollten drei der folgenden vier Zeichentypen enthalten: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen. Stellen Sie sicher, dass Ihr Kennwort diese Anforderungen erfüllt. |
| 6 | Kennwörter sollten drei der folgenden vier Zeichentypen enthalten: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen. | Ihr Kennwort enthält nicht die erforderlichen Zeichentypen. Stellen Sie sicher, dass Ihr Kennwort diese Anforderungen erfüllt. |
| 7 | Parameter passt nicht zur Bestätigung. | Stellen Sie sicher, dass Ihr Kennwort alle Anforderungen erfüllt und dass Sie es richtig eingegeben haben. |
| 8 | Ihr Kennwort darf nicht mit dem Standardwert übereinstimmen. | Das Standardkennwort lautet *Password1*. Sie müssen dieses Kennwort ändern, nachdem Sie sich zum ersten Mal angemeldet haben. |
| 9 | Das eingegebene Kennwort entspricht nicht dem Gerätekennwort. Geben Sie das Kennwort erneut ein. | Überprüfen Sie das Kennwort, und geben Sie es erneut ein. |

Kennwörter werden vor der Registrierung erfasst, aber erst nach erfolgreicher Registrierung des Geräts angewendet. Der Workflow zur Kennwortwiederherstellung erfordert, dass das Gerät registriert wurde.

> [AZURE.IMPORTANT]Nach einem Fehler beim Anwenden eines Kennworts versucht die Software im Allgemeinen wiederholt, das Kennwort zu erfassen, bis dies erfolgreich ist. In seltenen Fällen kann das Kennwort nicht angewendet werden. In diesem Fall können Sie das Gerät registrieren und den Vorgang fortsetzen, jedoch kann das Kennwort nicht geändert werden. Sie erhalten keinen Hinweis darauf, welches Kennwort nicht geändert wurde: das Kennwort für den Geräteadministrator oder das Kennwort für den StorSimple-Momentaufnahme-Manager. Tritt dieser Fall ein, empfehlen wir, beide Kennwörter zu ändern.

Sie können die Kennwörter über das Verwaltungsportal vom StorSimple-Manager-Dienst zurücksetzen. Weitere Informationen finden Sie unter:

- [Konfigurieren des Geräteadministratorkennworts](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec09)
- [Konfigurieren des StorSimple-Momentaufnahme-Managers](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)

###Fehler, die bei der Registrierung des Geräts auftreten.

Der StorSimple-Manager-Dienst in Microsoft Azure wird zur Registrierung des Geräts verwendet. Während der Geräteregistrierung können eines oder mehrere der folgenden Probleme auftreten.

| Nr.| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
| ---| ------------- | --------------- | ------------------ |
| 1 | Fehler 350027: Fehler bei der Registrierung des Geräts im StorSimple-Manager. | | Warten Sie einige Minuten, und versuchen Sie es dann erneut. Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx).|
| 2 | Fehler 350013: Fehler bei der Registrierung des Geräts. Dies kann aufgrund falscher Dienstregistrierungsschlüssel auftreten. | | Registrieren Sie das Gerät erneut mit dem korrekten Dienstregistrierungsschlüssel. Weitere Informationen finden Sie unter [Abrufen des Dienstregistrierungsschlüssels](https://msdn.microsoft.com/library/azure/cd4dee49-6ae8-4ff0-b79b-74b2027cb694#sec03). |
| 3 | Fehler 350063: Authentifizierung des StorSimple-Manager-Diensts erfolgreich, aber Fehler bei der Registrierung. Wiederholen Sie den Vorgang nach einiger Zeit. | Dieser Fehler zeigt an, dass die Authentifizierung mit ACS erfolgreich war, der Registrierungsaufruf an den Dienst jedoch fehlgeschlagen ist. Dies kann durch eine sporadische Netzwerkstörung verursacht werden. | Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx). |
| 4 | Fehler 350049: Der Dienst war während der Registrierung nicht erreichbar. | Wenn der Aufruf an den Dienst erfolgt, wird eine Webausnahme empfangen. In einigen Fällen wird dies möglicherweise behoben, indem der Vorgang später wiederholt wird. | Überprüfen Sie die IP-Adresse und DNS-Namen, und wiederholen Sie den Vorgang. Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx). | 
| 5 | Fehler 350031: Das Gerät wurde bereits registriert. | | Keine Aktion erforderlich. |
| 6 | Fehler 350016: Fehler bei der Geräteregistrierung. | |Stellen Sie sicher, dass der Registrierungsschlüssel korrekt ist. |
| 7 | Invoke-HcsSetupWizard: Fehler beim Registrieren des Geräts. Dies kann durch falsche IP-Adressen oder DNS-Namen auftreten. Überprüfen Sie Ihre Netzwerkeinstellungen, und versuchen Sie es erneut. Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx). (Fehler 350050) | Stellen Sie sicher, dass Ihr Gerät außerhalb des Netzwerks erfolgreich Ping-Befehle ausführen kann. Wenn keine Verbindung mit dem externen Netzwerk besteht, kann die Registrierung mit diesem Fehler fehlschlagen. Dieser Fehler kann eine Kombination aus einem oder mehreren der folgenden Ursachen sein:<ul><li>Falsche IP</li><li>Falsches Subnetz</li><li>Falsches Gateway</li><li>Falsche DNS-Einstellungen</li></ul> | Lesen Sie die Schritte in der [schrittweisen Anleitung zur Problembehandlung](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Invoke-HcsSetupWizard: Der aktuelle Vorgang ist aufgrund eines internen Dienstfehlers [0x1FBE2] fehlgeschlagen. Wiederholen Sie den Vorgang nach einiger Zeit. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft Support. | Dies ist ein allgemeiner Fehler, der für alle für den Benutzer nicht sichtbaren Fehler vom Dienst oder Agent ausgegeben wird. Der häufigste Grund ist möglicherweise, dass die ACS-Authentifizierung fehlgeschlagen ist. Eine mögliche Ursache für diesen Fehler ist, dass Probleme mit der NTP-Serverkonfiguration vorliegen und die Uhrzeit auf dem Gerät nicht richtig festgelegt ist. | Passen Sie die Zeiteinstellung (im Fall von Problemen) an, und wiederholen Sie den Registrierungsvorgang. Wenn das Problem weiterhin auftritt, [wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx), um Informationen zu den nächsten Schritten zu erhalten. |
| 9 | Warnung: Das Gerät konnte nicht aktiviert werden. Die Kennwörter für den Geräteadministrator und für den StorSimple-Momentaufnahme-Manager wurden nicht geändert. | Wenn die Registrierung fehlschlägt, werden die Geräteadministrator- und StorSimple-Momentaufnahme-Manager-Kennwörter nicht geändert. |

##Tools zur Problembehandlung für StorSimple-Bereitstellungen

StorSimple umfasst mehrere Tools, mit denen sich Probleme mit der StorSimple-Lösung beheben lassen. Das umfasst:

- Supportpakete und Geräteprotokolle 
- Cmdlets, die speziell für die Problembehandlung entwickelt wurden 

##Für die Problembehandlung verfügbare Supportpakete und Geräteprotokolle

Ein Supportpaket enthält alle relevanten Protokolle, die das Microsoft Support-Team bei der Behandlung von Geräteproblemen unterstützen können. Sie können Windows PowerShell für StorSimple verwenden, um ein verschlüsseltes Supportpaket zu generieren, das Sie für den Support freigeben können.

###So zeigen Sie die Protokolle oder den Inhalt des Supportpakets an

1. Erstellen Sie mithilfe von Windows PowerShell für StorSimple ein Supportpaket wie unter [Generieren eines Supportpakets](https://msdn.microsoft.com/library/azure/dn772348.aspx) beschrieben.

2. Laden Sie das [Script to decrypt a support package for Windows PowerShell for StorSimple](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) (Skript zum Entschlüsseln eines Supportpakets für Windows PowerShell für StorSimple, in englischer Sprache) lokal auf den Clientcomputer herunter.

3. Verwenden Sie diese schrittweise Anleitung zum [Bearbeiten eines Supportpakets](https://msdn.microsoft.com/library/azure/dn762117.aspx), um das Supportpaket zu öffnen und zu entschlüsseln.

4. Die entschlüsselten Protokolle des Supportpakets haben das ETW/ETVX-Format. Sie können die folgenden Schritte ausführen, um diese Dateien in der Windows-Ereignisanzeige anzuzeigen:
  1. Führen Sie den Befehl **eventvwr** auf dem Windows-Client aus. Dadurch wird die Ereignisanzeige gestartet.
  2. Klicken Sie im Bereich **Aktionen** auf **Gespeichertes Protokoll öffnen**, und zeigen Sie auf die Protokolldateien im ETVX/ETW-Format (das Supportpaket). Die Datei kann jetzt angezeigt werden. Nach dem Öffnen der Datei können Sie die Datei als Text speichern, indem Sie mit der rechten Maustaste klicken.
   
    > [AZURE.IMPORTANT]Sie können auch das Cmdlet **Get-WinEvent** verwenden, um diese Datei in Windows PowerShell zu öffnen. Weitere Informationen finden Sie in der Windows PowerShell-Cmdlet-Referenzdokumentation unter [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx).

5. Wenn die Protokolle in der Ereignisanzeige geöffnet sind, suchen Sie nach den folgenden Protokollen, die Probleme im Zusammenhang mit der Gerätekonfiguration enthalten:

  - hcs_pfconfig/Operational Log
  - hcs_pfconfig/Config

6. Suchen Sie in den Protokolldateien nach Zeichenfolgen im Zusammenhang mit den Cmdlets, die vom Setup-Assistenten aufgerufen wurden. Eine Liste der Cmdlets finden Sie unter [Schritte bei der erstmaligen Ausführung des Setup-Assistenten](#first-time-setup-wizard-process). 

7. Wenn Sie die Ursache des Problems nicht ermitteln können, wenden Sie sich für die nächsten Schritte [an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx). Führen Sie die Schritte unter [Starten einer Supportsitzung](https://msdn.microsoft.com/library/azure/dn757804.aspx) aus, wenn Sie den Microsoft Support kontaktieren.

##Cmdlets für die Problembehandlung

Verwenden Sie die folgenden Windows PowerShell-Cmdlets, um Verbindungsfehler zu erkennen.

- Get-NetAdapter: Verwenden Sie dieses Cmdlet, um den Status der Netzwerkschnittstellen zu ermitteln. 

- Test-Connection: Verwenden Sie dieses Cmdlet, um die Netzwerkkonnektivität innerhalb und außerhalb des Netzwerks zu überprüfen.

- Test-HcsmConnection: Verwenden Sie dieses Cmdlet zum Überprüfen der Verbindung eines erfolgreich registrierten Geräts.

##Problembehandlung mit dem Cmdlet "Get-NetAdapter"

Beim Konfigurieren von Netzwerkschnittstellen für eine erste Gerätebereitstellung ist der Hardwarestatus nicht in der Benutzeroberfläche des StorSimple-Manager-Diensts verfügbar, da das Gerät noch nicht für den Dienst registriert ist. Darüber hinaus wird der Status des Geräts auf der Seite "Hardwarestatus" nicht immer richtig dargestellt, insbesondere dann, wenn Probleme mit der Dienstsynchronisierung vorliegen. In diesen Fällen können Sie das Cmdlet "Get-NetAdapter" verwenden, um den Zustand und Status der Netzwerkschnittstellen zu bestimmen.

###So zeigen Sie eine Liste aller Netzwerkadapter auf Ihrem Gerät an

1. Starten Sie Windows PowerShell für StorSimple, und geben Sie **Get-NetAdapter** ein. 

2. Verwenden Sie die Ausgabe des Cmdlets "Get-NetAdapter" und die folgenden Richtlinien, um den Status der Netzwerkschnittstelle festzustellen.
  - Wenn die Schnittstelle aktiviert ist und fehlerfrei funktioniert, wird der Status **IfIndex** als **Up** angezeigt.
  - Wenn die Schnittstelle fehlerfrei funktioniert, aber nicht physisch (durch ein Netzwerkkabel) verbunden ist, wird **IfIndex** als **Disabled** angezeigt.
  - Wenn die Schnittstelle fehlerfrei funktioniert, jedoch nicht aktiviert ist, wird der Status **IfIndex** als **NotPresent** angezeigt.
  - Wenn die Schnittstelle nicht vorhanden ist, wird sie nicht in dieser Liste angezeigt. Der Benutzeroberfläche des StorSimple-Manager-Diensts zeigt diese Schnittstelle in einem fehlerhaften Zustand an.

Weitere Informationen zur Verwendung dieses Cmdlets finden Sie unter [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) in der Windows PowerShell-Cmdlet-Referenz.

In den folgenden Abschnitten sind Beispiele für die Ausgabe des Cmdlets "Get-NetAdapter" aufgeführt.

 In diesen Beispielen ist "Controller 0" der passive Controller und wurde wie folgt konfiguriert:

- Auf dem Gerät sind die Netzwerkschnittstellen DATA 0, DATA 1, DATA 2 und DATA 3 vorhanden.
- Die Netzwerkschnittstellenkarten DATA 4 und DATA 5 sind nicht vorhanden. Daher sind sie nicht in der Ausgabe aufgeführt.
- DATA 0 ist aktiviert.

Controller 1 ist der aktive Controller und wurde wie folgt konfiguriert:

- Die Netzwerkschnittstellen DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 und DATA 5 sind auf dem Gerät vorhanden.
- DATA 0 ist aktiviert.

**Beispielausgabe – Controller 0**

Im folgenden finden Sie die Ausgabe von Controller 0 (passiver Controller). DATA 1, DATA 2 und DATA 3 sind nicht verbunden. DATA 4 und DATA 5 sind nicht aufgeführt, da sie nicht auf dem Gerät vorhanden sind.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Beispielausgabe – Controller 1**

Im Folgenden finden Sie die Ausgabe von Controller 1 (aktiver Controller). Nur die DATA 0-Netzwerkschnittstelle ist auf dem Gerät konfiguriert und funktioniert.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
##Problembehandlung mit dem Cmdlet "Test-Connection"

Mit dem Cmdlet "Test-Connection" können Sie bestimmen, ob Ihr StorSimple-Gerät mit dem externen Netzwerk eine Verbindung herstellen kann. Wenn alle Netzwerkparameter, einschließlich DNS, im Setup-Assistenten korrekt konfiguriert sind, können Sie das Cmdlet "Test-Connection" verwenden, um einen Ping-Befehl an eine bekannte Adresse außerhalb des Netzwerks, z. B. outlook.com, zu senden.

Im Folgenden sind einige Ausgabebeispiele für das Cmdlet "Test-Connection" aufgeführt.

> [AZURE.NOTE]Im ersten Beispiel wird das Gerät mit einem falschen DNS konfiguriert. Im zweiten Beispiel ist der DNS richtig.
 
**Beispielausgabe – falsche DNS**

Im folgenden Beispiel erfolgt keine Ausgabe für die IPv4- und IPv6-Adressen. Dies weist darauf hin, dass der DNS nicht aufgelöst wird. Dies bedeutet, dass keine Verbindung mit dem externen Netzwerk besteht und eine korrekter DNS bereitgestellt werden muss.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Beispielausgabe – richtiger DNS**

Im folgenden Beispiel gibt der DNS die IPv4-Adresse zurück. Dies bedeutet, dass der DNS ordnungsgemäß konfiguriert ist. Dadurch wird bestätigt, dass eine Verbindung mit dem externen Netzwerk besteht.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

##Problembehandlung mit dem Cmdlet "Test-HcsmConnection"

Verwenden Sie das Cmdlet "Test-HcsmConnection" für ein Gerät, das bereits verbunden und mit dem StorSimple-Manager-Dienst registriert ist. Mit diesem Cmdlet können Sie die Verbindung zwischen einem registrierten Gerät und dem entsprechenden StorSimple-Manager-Dienst überprüfen. Sie können diesen Befehl in Windows PowerShell für StorSimple ausführen.

###So führen Sie das Cmdlet "Test-HcsmConnection" aus

1. Stellen Sie sicher, dass das Gerät registriert ist.

2. Überprüfen Sie den Gerätestatus. Wenn das Gerät deaktiviert ist, sich im Wartungsmodus oder offline befindet, werden möglicherweise die folgenden Fehler angezeigt:

   - ErrorCode.CiSDeviceDecommissioned – gibt an, dass das Gerät deaktiviert wurde.
   - ErrorCode.DeviceNotReady – gibt an, dass sich das Gerät im Wartungsmodus befindet.
   - ErrorCode.DeviceNotReady – gibt an, dass das Gerät nicht online ist.

3. Stellen Sie sicher, dass der StorSimple-Manager-Dienst ausgeführt wird (mittels Cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx)). Wenn der Dienst nicht ausgeführt wird, werden möglicherweise die folgenden Fehler angezeigt:

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError – gibt an, dass bei der Ausführung von "Get-ClusterResource" eine Ausnahme aufgetreten ist.

4. Überprüfen Sie das ACS-Token (Access Control Service). Wenn eine Webausnahme ausgelöst wird, könnte dies auf ein Gatewayproblem, fehlende Proxyauthentifizierung, einen falschen DNS oder einen Authentifizierungsfehler zurückzuführen sein. Es werden möglicherweise die folgenden Fehler angezeigt:

   - ErrorCode.CiSApplianceGateway – weist auf eine HttpStatusCode.BadGateway-Ausnahme hin: Der Namensauflösungsdienst konnte den Hostnamen nicht auflösen. 
   - ErrorCode.CiSApplianceProxy – weist auf eine HttpStatusCode.ProxyAuthenticationRequired-Ausnahme (HTTP-Statuscode 407) hin: Der Client konnte nicht beim Proxyserver authentifiziert werden. 
   - ErrorCode.CiSApplianceDNSError – weist auf eine WebExceptionStatus.NameResolutionFailure-Ausnahme hin: Der Namensauflösungsdienst konnte den Hostnamen nicht auflösen.
   - ErrorCode.CiSApplianceACSError – gibt an, dass der Dienst einen Authentifizierungsfehler zurückgegeben hat, während eine Verbindung vorliegt.
   
   Wenn keine Webausnahme ausgelöst wird, überprüfen Sie Folgendes:

   - ErrorCode.CiSApplianceFailure – weist auf einen Gerätefehler hin.

5. Überprüfen Sie die Verbindung des Cloud-Diensts. Wenn der Dienst eine Webausnahme auslöst, werden möglicherweise die folgenden Fehler angezeigt:

  - ErrorCode.CiSApplianceGateway – weist auf eine HttpStatusCode.BadGateway-Ausnahme hin: Ein zwischengeschalteter Proxyserver hat von einem anderen Proxy oder dem ursprünglichen Server eine ungültige Anforderung erhalten.
  - ErrorCode.CiSApplianceProxy – weist auf eine HttpStatusCode.ProxyAuthenticationRequired-Ausnahme (HTTP-Statuscode 407) hin: Der Client konnte nicht beim Proxyserver authentifiziert werden. 
  - ErrorCode.CiSApplianceDNSError – weist auf eine WebExceptionStatus.NameResolutionFailure-Ausnahme hin: Der Namensauflösungsdienst konnte den Hostnamen nicht auflösen.
  - ErrorCode.CiSApplianceACSError – gibt an, dass der Dienst einen Authentifizierungsfehler zurückgegeben hat, während eine Verbindung vorliegt.
  
  Wenn keine Webausnahme ausgelöst wird, überprüfen Sie Folgendes: ErrorCode.CiSApplianceSaasServiceError – bedeutet, dass ein Problem mit dem StorSimple-Manager-Dienst vorliegt.
 
6. Überprüfen Sie die Azure Service Bus-Verbindung. ErrorCode.CiSApplianceServiceBusError – gibt an, dass das Gerät nicht mit dem Service Bus verbunden werden kann.
 
Die Protokolldateien "CiSCommandletLog0Curr.errlog" und "CiSAgentsvc0Curr.errlog" enthalten weitere Informationen, wie z. B. Details der Ausnahme.

Weitere Informationen zur Verwendung des Cmdlets finden Sie unter [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) in der Windows PowerShell-Referenzdokumentation.

> [AZURE.IMPORTANT]Sie können dieses Cmdlet für den aktiven und den passiven Controller ausführen.
 
Im Folgenden sind einige Ausgabebeispiele für das Cmdlet "Test-HcsmConnection" aufgeführt.

**Beispielausgabe – erfolgreich registriertes Gerät**

Das erste Beispiel stammt von einem Gerät, das erfolgreich im StorSimple-Manager-Dienst registriert wurde und bei dem keine Verbindungsprobleme vorliegen.

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Beispielausgabe – Gerät ist offline**

Dieses Beispiel stammt von einem Gerät mit dem Status **Offline** im Verwaltungsportal.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

Das Gerät konnte unter Verwendung der aktuellen Webproxy-Konfiguration keine Verbindung herstellen. Dies kann durch ein Problem mit der Webproxy-Konfiguration oder mit der Netzwerkverbindung verursacht sein. In diesem Fall sollten Sie sicherstellen, dass Ihre Webproxy-Einstellungen korrekt sind und der Webproxyserver online und erreichbar ist.

##Schrittweise Beispielanleitung zur StorSimple-Problembehandlung

Im folgenden Beispiel finden Sie schrittweise Anleitungen zur Problembehandlung bei einer StorSimple-Bereitstellung. In diesem Beispielszenario schlägt die Geräteregistrierung mit einer Fehlermeldung fehl, dass die Netzwerkeinstellungen oder der DNS-Name falsch ist.

Die zurückgegebene Fehlermeldung lautet:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Der Fehler kann eine der folgenden Ursachen haben:

- Falsche Hardwareinstallation
- Fehlerhafte Netzwerkschnittstellen
- Falsche IP-Adresse, Subnetzmaske, Gateway, falscher primärer DNS-Server oder Webproxy
- Falscher Registrierungsschlüssel
- Falsche Firewalleinstellungen

###So ermitteln und beheben Sie das Problem mit der Geräteregistrierung

1. Überprüfen Sie Ihre Gerätekonfiguration: Führen Sie auf dem aktiven Controller **Invoke-HcsSetupWizard** aus.

     >[AZURE.NOTE]Der Setup-Assistent muss auf dem aktiven Controller ausgeführt werden. Um sicherzustellen, dass Sie mit dem aktiven Controller verbunden sind, überprüfen Sie das Banner in der seriellen Konsole. Das Banner zeigt an, ob eine Verbindung mit Controller 0 oder 1 besteht und ob der Controller aktiv oder passiv ist. Weitere Informationen finden Sie unter [Identifizieren eines aktiven Controllers auf Ihrem Gerät](https://msdn.microsoft.com/library/azure/dn790262.aspx).
 
2. Stellen Sie sicher, dass das Gerät richtig verkabelt ist: Überprüfen Sie das Netzwerkkabel an der Rückseite des Geräts. Die Verkabelung ist von Modell zu Modell unterschiedlich. Weitere Informationen finden Sie unter [Verkabeln eines Geräts vom Typ 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) oder [Verkabeln eines Geräts vom Typ 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx).

     >[AZURE.NOTE]Bei Verwendung von 10-GbE-Netzwerkanschlüsssen, müssen Sie die bereitgestellten QSFP-SFP-Adapter und SFP-Kabel verwenden. Weitere Informationen finden Sie unter der [list of cables, switches, and transceivers recommended by the OEM supplier for Mellanox ports](http://www.mellanox.com/page/cables?mtag=cable_overview) (in englischer Sprache).
 
3. Überprüfen Sie die Integrität der Netzwerkschnittstelle:

   - Verwenden Sie das Cmdlet "Get-NetAdapter", um den Status der Netzwerkschnittstellen für DATA 0 zu ermitteln. 
   - Wenn der Link nicht funktioniert, zeigt der Status **IfIndex** an, dass die Schnittstelle ausgefallen ist. Sie müssen dann die Netzwerkverbindung zwischen Anschluss des Geräts und Switch überprüfen. Sie müssen außerdem fehlerhafte Kabel ausfindig machen. 
   - Wenn Sie vermuten, dass der DATA 0-Anschluss am aktiven Controller einen Fehler verursacht hat, können Sie dies nachprüfen, indem Sie eine Verbindung mit dem DATA 0-Anschluss an Controller 1 herstellen. Um dies zu bestätigen, trennen Sie das Netzwerkkabel von der Rückseite des Geräts von Controller 0, schließen das Kabel an Controller 1 an und führen dann das Cmdlet "Get-NetAdapter" erneut aus. Wenn der DATA 0-Anschluss an einem Controller einen Fehler verursacht, [wenden Sie sich für die nächsten Schritte an den Microsoft-Support](https://msdn.microsoft.com/library/azure/dn757750.aspx). Möglicherweise müssen Sie den Controller in Ihrem System ersetzen.
 
4. Überprüfen Sie die Verbindung mit dem Switch:
   - Stellen Sie sicher, dass die DATA 0-Netzwerkschnittstellen an Controller 0 und Controller 1 im primären Gehäuse sich im gleichen Subnetz befinden. 
   - Überprüfen Sie den Hub oder Router. In der Regel sollten Sie beide Controller mit dem gleichen Hub oder Router verbinden. 
   - Stellen Sie sicher, dass die für die Verbindung verwendeten Switches über DATA 0 für beide Controller im gleichen vLAN verfügen.
   
5. Ausschließen etwaiger Benutzerfehler:

  - Führen Sie den Setup-Assistenten erneut aus (führen Sie **Invoke-HcsSetupWizard**) aus, und geben Sie die Werte erneut ein, um sicherzustellen, dass keine Fehler vorliegen. 
  - Überprüfen Sie den verwendeten Registrierungsschlüssel. Der gleiche Registrierungsschlüssel kann für die Verbindung mehrerer Geräte mit dem StorSimple-Manager-Dienst verwendet werden. Verwenden Sie das Verfahren in [Abrufen des Dienstregistrierungsschlüssels](https://msdn.microsoft.com/library/azure/cd4dee49-6ae8-4ff0-b79b-74b2027cb694#sec03), um sicherzustellen, dass Sie den richtigen Registrierungsschlüssel nutzen.

    > [AZURE.IMPORTANT]Wenn Sie mehrere Dienste ausführen, müssen Sie sicherstellen, dass der Registrierungsschlüssel für den entsprechenden Dienst verwendet wird, um das Gerät zu registrieren. Wenn Sie ein Gerät mit dem falschen StorSimple-Manager-Dienst registriert haben, wenden Sie sich für die nächsten Schritte [an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx). Sie müssen das Gerät möglicherweise auf die Werkseinstellungen zurücksetzen (kann zu Datenverlusten führen), um anschließend eine Verbindung mit dem gewünschten Dienst herzustellen.

6. Verwenden Sie das Cmdlet "Test-Connection", um sicherzustellen, dass eine Netzwerkverbindung mit dem externen Netzwerk besteht. Weitere Informationen finden Sie unter [Problembehandlung mit dem Cmdlet "Test-Connection"](#troubleshoot-with-the-test-connection-cmdlet).

7. Überprüfen Sie auf Firewallstörungen. Wenn Sie überprüft haben, dass virtuelle IP-Adresse (VIP), Subnetzmaske, Gateway und DNS-Einstellungen richtig sind, und es liegen weiterhin Verbindungsprobleme vor, blockiert Ihre Firewall möglicherweise die Kommunikation zwischen dem Gerät und dem externen Netzwerk. Stellen Sie sicher, dass die Ports 80 und 443 auf Ihrem StorSimple-Gerät für die ausgehende Kommunikation verfügbar sind. Weitere Informationen finden Sie unter der [Netzwerkanforderungen für das StorSimple-Gerät](https://msdn.microsoft.com/library/azure/dn772371.aspx).

8. Überprüfen Sie die Protokolle. Wechseln Sie zu [Für die Problembehandlung verfügbare Supportpakete und Geräteprotokolle](#support-packages-and-device logs-for-troubleshooting).

9. Wenn die vorhergehenden Schritte das Problem nicht beheben, [wenden Sie sich für Hilfe an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx).

##Nächste Schritte
[Problembehandlung bei einem betriebsbereiten Gerät](../storsimple-troubleshoot-an-operational-device.md)

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx
<!--HONumber=52-->
 