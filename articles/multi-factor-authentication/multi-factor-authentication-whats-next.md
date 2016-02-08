<properties 
	pageTitle="Azure Multi-Factor Authentication – Nächste Schritte" 
	description="Dies ist die Seite ";Azure Multi-Factor Authentication";, auf der beschrieben wird, was die nächsten Schritte mit Multi-Factor Authentication sind. Dazu gehören Berichte, Betrugswarnung, Einmalumgehung, benutzerdefinierte Sprachnachrichten, Zwischenspeicherung, vertrauenswürdige IP-Adressen und App-Kennwörter." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/21/2016" 
	ms.author="billmath"/>

# Konfigurieren von Azure Multi-Factor Authentication

Der folgende Artikel bietet Unterstützung bei der Verwaltung der Azure Multi-Factor Authentication, nachdem Sie nun bereit für die Ausführung sind. Er umfasst eine Vielzahl von Themen, die Ihnen dabei helfen, Azure Multi-Factor Authentication optimal zu nutzen. Bitte beachten Sie, dass nicht alle Funktionen in jeder Version von Multi-Factor Authentication verfügbar sind.

Die Konfiguration für einige der nachstehend genannten Funktionen finden Sie im Azure Multi-Factor Authentication-Verwaltungsportal. Sie können mit Hilfe des Azure-Portals auf zweierlei Art und Weise auf dieses Portal zugreifen. Einerseits über einen Multi-Factor Authentication-Anbieter, bei Verwendung einer verbrauchsbasierten MFA. Andererseits über die Diensteinstellungen der MFA. Für die zweite Option ist entweder ein Multi-Factor Authentication-Anbieter, oder eine Azure MFA-, Azure AD Premium- oder Enterprise Mobility Suite-Lizenz erforderlich.

Melden Sie sich als Administrator am Azure-Portal an und wählen Sie die Option „Active Directory“, um über einen Multi-Factor Authentication-Anbieter auf das MFA-Verwaltungsportal zuzugreifen. Klicken Sie auf die Registerkarte „**Anbieter für mehrstufige Authentifizierung**“, wählen Sie Ihr Verzeichnis aus und klicken Sie auf unten auf „**Verwalten**“.

Melden Sie sich als Administrator am Azure-Portal an und wählen Sie die Option „Active Directory“, um über die Seite mit den Diensteinstellungen für die MFA auf das MFA-Verwaltungsportal zuzugreifen. Klicken Sie auf Ihr Verzeichnis und anschließend auf die Registerkarte „**Konfigurieren**“. Klicken Sie im Abschnitt „Multi-Factor Authentication“ auf „**Diensteinstellungen verwalten**“. Klicken Sie am unteren Rand der Seite mit den Einstellungen für den MFA-Dienst auf den Link „**Portal aufrufen**“.


Funktion| Beschreibung| Inhalt
:------------- | :------------- | :------------- | 
[Betrugswarnung](#fraud-alert)|Die Betrugswarnung kann so konfiguriert und eingerichtet werden, dass Ihre Benutzer betrügerische Versuche, auf ihre Ressourcen zuzugreifen, melden können.|Einrichtung, Konfiguration und Betrugsberichte
[Einmalumgehung](#one-time-bypass) |Mit einer Einmalumgehung kann sich ein Benutzer ein einziges Mal authentifizieren, indem er die mehrstufige Authentifizierung "umgeht".|Einrichten und Konfigurieren einer Einmalumgehung
[Benutzerdefinierte Sprachnachrichten](#custom-voice-messages) |Mit benutzerdefinierten Sprachnachrichten können Sie Ihre eigenen Aufzeichnungen oder Begrüßungen mit mehrstufiger Authentifizierung verwenden. |Einrichten und Konfigurieren von benutzerdefinierten Begrüßungen und Nachrichten
[Zwischenspeichern](#caching)|Durch Zwischenspeichern können Sie einen bestimmten Zeitraum festlegen, sodass nachfolgende Authentifizierungsversuche automatisch erfolgreich sind. |Einrichten und Konfigurieren des Zwischenspeicherns zur Authentifizierung
[Vertrauenswürdige IPs](#trusted-ips)|Vertrauenswürdige IP-Adressen ist ein Feature der mehrstufigen Authentifizierung, mit der Administratoren eines verwalteten oder verbundenen Mandanten die mehrstufige Authentifizierung für Benutzer umgehen können, die sich vom lokalen Intranet des Unternehmens aus anmelden.|Konfigurieren und Einrichten von IP-Adressen, die von der mehrstufigen Authentifizierung ausgenommen sind	
[App-Kennwörter](#app-passwords)|Mit App-Kennwörtern kann eine Anwendung, die die mehrstufige Authentifizierung nicht erkennt, diese umgehen und weiter ausgeführt werden.|Informationen zu App-Kennwörter.
[Multi-Factor Authentication für gespeicherte Geräte und Browser (Öffentliche Vorschau) aussetzen](#suspend-multi-factor-authentication-for-remembered-devices-and-browsers-public-preview)|Dadurch können Sie die Multi-Factor Authentication für eine festgelegte Anzahl von Tagen aussetzen, nachdem ein Benutzer erfolgreich angemeldet wurde.|Informationen zum Aktivieren dieser Funktion und zum Einrichten der Anzahl von Tagen.




## Betrugswarnung
Die Betrugswarnung kann so konfiguriert und eingerichtet werden, dass Ihre Benutzer betrügerische Versuche, auf ihre Ressourcen zuzugreifen, melden können. Benutzer können entweder mit der mobilen App oder über ihre Rufnummer den Betrug melden.

### Einrichten und Konfigurieren der Betrugswarnung

1.	Melden Sie sich unter http://azure.microsoft.com an.
2.	Folgen Sie den Anweisungen im oberen Teil dieser Seite, um zum MFA-Verwaltungsportal zu gelangen.
3.	Klicken Sie im Abschnitt „Konfigurieren“ des Azure Multi-Factor Authentication-Verwaltungsportals auf „Einstellungen“.
4.	Aktivieren Sie das Kontrollkästchen „Benutzern gestatten, Betrugswarnungen zu übermitteln“ im Abschnitt „Betrugswarnung“ auf der Seite „Einstellungen“.
5.	Wenn ein Benutzer gesperrt werden soll, so aktivieren Sie das Kontrollkästchen "Benutzer sperren", sobald ein Betrug gemeldet wurde.
6.	Geben Sie im Textfeld zu „**Code zur Meldung von Betrug während der Begrüßung**“ einen Nummerncode ein, der bei jeder Anrufüberprüfung genutzt werden kann. Wenn ein Benutzer diesen Code anstelle des #-Zeichens oder zusätzlich zu diesem eingibt, wird eine Betrugswarnung gemeldet. 
7.	Klicken Sie unten auf "Speichern".

>[AZURE.NOTE]
In Microsofts Standardansage wird der Benutzer aufgefordert, zum Senden einer Betrugswarnung die Zeichenfolge „0#“ einzugeben. Wenn Sie einen anderen Code als „0“ verwenden, müssen Sie eine benutzerdefinierte Ansage mit den passenden Anweisungen aufnehmen.


<center>![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)</center>

### Melden einer Betrugswarnung
Eine Betrugswarnung kann auf zwei Arten gemeldet werden. Entweder über die mobile App oder telefonisch.

### Melden einer Betrugswarnung mit der mobilen App



1. Wenn eine Überprüfung an Ihr Telefon gesendet wird, klicken Sie darauf, sodass die Multi-Factor Authentication-App gestartet wird.
2. Zum Melden eines Betrugs klicken Sie auf "Abbrechen und Betrug melden". Hierdurch wird ein Feld mit dem Hinweis angezeigt, dass der IT-Support Ihres Unternehmens benachrichtigt wird. 
3. Klicken Sie auf "Betrug melden".
4. Klicken Sie in der App auf "Schließen".

<center>![Cloud](./media/multi-factor-authentication-whats-next/report1.png)</center>

### Melden einer Betrugswarnung per Telefon

1. Beantworten Sie den Überprüfungsanruf auf Ihr Telefon.</li>
2. Zur Betrugsmeldung geben Sie den Code ein, der konfiguriert wurde, um der telefonischen Betrugsmeldung zu entsprechen, und anschließend das #-Zeichen. Sie werden benachrichtigt, dass eine Betrugswarnung gesendet wurde.
3. Beenden Sie den Anruf.

### Anzeigen des Betrugsberichts

1. Melden Sie sich an bei [http://azure.microsoft.com](https://azure.microsoft.com/).
2. Wählen Sie im linken Bereich "Active Directory" aus.
3. Wählen Sie oben "Anbieter für mehrstufige Authentifizierung" aus. Hierdurch wird eine Liste der Anbieter für mehrstufige Authentifizierung angezeigt.
4. Wenn Sie mehr als einen Anbieter für mehrstufige Authentifizierung haben, wählen Sie denjenigen aus, bei dem Sie den Betrugswarnbericht anzeigen möchten, und klicken Sie am unteren Rand der Seite auf "Verwalten". Wenn Sie nur einen Anbieter haben, klicken Sie direkt auf "Verwalten". Dadurch wird das Multi-Factor Authentication-Verwaltungsportal von Azure geöffnet.
5. Klicken Sie im Azure Multi-Factor Authentication-Verwaltungsportal auf der linken Seite unter "Einen Bericht anzeigen" auf "Betrugswarnung".
6. Geben Sie den Datumsbereich an, der im Bericht angezeigt werden soll. Sie können auch bestimmte Benutzernamen, Telefonnummern und einen bestimmten Benutzerstatus angeben.
7. Klicken Sie auf "Ausführen". Hierdurch wird ein Bericht angezeigt, der dem Folgenden ähnelt. Wenn Sie den Bericht exportieren möchten, können Sie auch auf "Exportieren nach CSV" klicken.

## Einmalumgehung

Mit einer Einmalumgehung kann sich ein Benutzer ein einziges Mal authentifizieren, indem er die mehrstufige Authentifizierung "umgeht". Die Umgehung ist vorübergehend und läuft nach der angegebenen Anzahl von Sekunden ab. Falls also die mobile Anwendung oder das Telefon keine Benachrichtigung oder einen Telefonanruf empfängt, können Sie eine Einmalumgehung aktivieren, damit der Benutzer auf die gewünschte Ressource zugreifen kann.

### Erstellen Sie einer Einmalumgehung

1.	Melden Sie sich unter http://azure.microsoft.com an.
2.	Folgen Sie den Anweisungen im oberen Teil dieser Seite, um zum MFA-Verwaltungsportal zu gelangen.
3.	Wenn im Azure Multi-Factor Authentication-Verwaltungsportal links neben dem Namen Ihres Mandanten oder Azure MFA-Anbieters ein Pluszeichen („+“) angezeigt wird, klicken Sie darauf, um die verschiedenen Replikationsgruppen des MFA-Servers und die Azure Standardgruppe anzuzeigen. Klicken Sie auf die passende Gruppe.
4.	Klicken Sie unter „Benutzerverwaltung“ auf „**Einmalumgehung**“![Cloud](./media/multi-factor-authentication-whats-next/create1.png).
5.	Klicken Sie auf der Seite „Einmalumgehung“ auf „**Neue Einmalumgehung**“.
6.	Geben Sie den Benutzernamen, die Anzahl der Sekunden, die die Umgehung vorhanden ist und den Grund für die Umgehung, und klicken Sie dann auf „**Umgehen**“.![Cloud](./media/multi-factor-authentication-whats-next/create2.png)
7.	An diesem Punkt muss sich der Benutzer anmelden, bevor die Einmalumgehung abläuft.



### Anzeigen des Berichts für die Einmalumgehung

1. Melden Sie sich an bei [http://azure.microsoft.com](https://azure.microsoft.com/).
2. Wählen Sie im linken Bereich "Active Directory" aus.
3. Wählen Sie oben "Anbieter für mehrstufige Authentifizierung" aus. Hierdurch wird eine Liste der Anbieter für mehrstufige Authentifizierung angezeigt.
4. Wenn Sie mehr als einen Anbieter für mehrstufige Authentifizierung haben, wählen Sie denjenigen aus, bei dem Sie den Betrugswarnbericht anzeigen möchten, und klicken Sie am unteren Rand der Seite auf "Verwalten". Wenn Sie nur einen Anbieter haben, klicken Sie direkt auf "Verwalten". Dadurch wird das Multi-Factor Authentication-Verwaltungsportal von Azure geöffnet.
5. Klicken Sie im Azure Multi-Factor Authentication-Verwaltungsportal auf der linken Seite unter "Einen Bericht anzeigen" auf "Einmalumgehung".
6. Geben Sie den Datumsbereich an, der im Bericht angezeigt werden soll. Sie können auch bestimmte Benutzernamen, Telefonnummern und einen bestimmten Benutzerstatus angeben.
7. Klicken Sie auf "Ausführen". Hierdurch wird ein Bericht angezeigt, der dem Folgenden ähnelt. Wenn Sie den Bericht exportieren möchten, können Sie auch auf "Exportieren nach CSV" klicken.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## Benutzerdefinierte Sprachnachrichten

Mit benutzerdefinierten Sprachnachrichten können Sie Ihre eigenen Aufzeichnungen oder Begrüßungen mit mehrstufiger Authentifizierung verwenden. Diese können zusätzlich zu den Microsoft-Aufzeichnungen verwendet werden oder diese ersetzen.

Bevor Sie beginnen, sollten Sie Folgendes beachten:

- Die aktuell unterstützten Dateiformate sind WAV und MP3.
- Die maximale Dateigröße beträgt 5 MB.
- Es wird empfohlen, dass Authentifizierungsnachrichten nicht länger als 20 Sekunden dauern. Längere Nachrichten können bewirken, dass die Überprüfung fehlschlägt, da der Benutzer nicht antworten kann, bevor die Nachricht beendet ist und für die Überprüfung ein Timeout auftritt.



### Einrichten von benutzerdefinierten Sprachnachrichten in Multi-Factor Authentication
1.	Erstellen Sie eine Sprachnachricht in einem der unterstützten Dateiformate.
2.	Melden Sie sich unter http://azure.microsoft.com an.
3.	Folgen Sie den Anweisungen im oberen Teil dieser Seite, um zum MFA-Verwaltungsportal zu gelangen.
4.	Klicken Sie im Abschnitt „Konfigurieren“ des Azure Multi-Factor Authentication-Verwaltungsportals auf „Sprachnachrichten“.
5.	Klicken Sie im Abschnitt „Sprachnachrichten“ auf „**Neue Sprachnachricht**“.![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)
6.	Klicken Sie auf der Seite „Konfigurieren: Neue Sprachnachrichten“ auf „**Audiodateien verwalten**“.![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)
7.	Klicken Sie auf der Seite „Konfigurieren: Neue Audiodateien“ auf „**Audiodatei hochladen**“.![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)
8.	Klicken Sie auf der Seite „Konfigurieren: Audiodatei hochladen“ auf „**Durchsuchen**“, und navigieren Sie zu Ihrer Sprachnachricht. Klicken Sie dann auf „**Öffnen**“.![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)
9.	Fügen Sie eine Beschreibung hinzu, und klicken Sie auf "Hochladen".
10.	Sobald dies abgeschlossen ist, sehen Sie eine Meldung, dass Sie die Datei erfolgreich hochgeladen haben.
11.	Klicken Sie auf der linken Seite auf "Sprachnachrichten".
12.	Klicken Sie im Abschnitt "Sprachnachrichten" auf "Neue Sprachnachricht".
13.	Wählen Sie eine Sprache aus der Dropdownliste "Sprache".
14.	Wenn diese Nachricht für eine bestimmte Anwendung gedacht ist, geben Sie dies im Feld "Anwendung" ein.
15.	Wählen Sie in "Nachrichtentyp" den Nachrichtentyp aus, der von der neuen benutzerdefinierten Nachricht überschrieben wird.
16.	Wählen Sie aus der Dropdownliste "Audiodatei" Ihre Audiodatei aus.
17.	Klicken Sie auf **Erstellen**. Sie sehen eine Meldung, die besagt, dass Sie erfolgreich eine Sprachnachricht erstellt haben.![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## Zwischenspeichern in Azure Multi-Factor Authentication

Durch Zwischenspeichern können Sie einen bestimmten Zeitraum festlegen, sodass nachfolgende Authentifizierungsversuche automatisch erfolgreich sind. Dies wird hauptsächlich verwendet, wenn lokale Systeme, wie z. B. VPNs, mehrere Verifizierungsanfragen senden, während die erste Anfrage noch bearbeitet wird. Dadurch werden die nachfolgenden Anforderungen automatisch erfolgreich ausgeführt, nachdem die laufende Verifizierungsanfrage für den Benutzer erfolgreich ausgeführt wurde. Beachten Sie, dass das Zwischenspeichern nicht für Anmeldungen bei Azure AD gedacht ist.


### Einrichten der Zwischenspeicherung in Azure Multi-Factor Authentication

1.	Melden Sie sich unter http://azure.microsoft.com an.
2.	Folgen Sie den Anweisungen im oberen Teil dieser Seite, um zum MFA-Verwaltungsportal zu gelangen.
3.	Klicken Sie im Abschnitt „Konfigurieren“ des Azure Multi-Factor Authentication-Verwaltungsportals auf „Zwischenspeichern“.
4.	Klicken Sie auf der Seite "Zwischenspeicherung konfigurieren" auf "Neuer Cache".
5.	Wählen Sie den Cache-Typ und die Aufbewahrungsdauer aus. Klicken Sie auf "Erstellen".

<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## Vertrauenswürdige IPs

Vertrauenswürdige IP-Adressen ist ein Feature der mehrstufigen Authentifizierung, mit der Administratoren eines verwalteten oder verbundenen Mandanten die mehrstufige Authentifizierung für Benutzer umgehen können, die sich vom lokalen Intranet des Unternehmens aus anmelden. Die Funktionen sind für Azure AD-Mandanten verfügbar, die Azure AD Premium, Enterprise Mobility Suite oder Azure Multi-Factor Authentication-Lizenzen verfügen.

 
Typen von Azure AD-Mandanten| Verfügbare vertrauenswürdige IP-Optionen
:------------- | :------------- | 
Verwaltet|Bestimmte IP-Adressbereiche – Administratoren können einen Bereich von IP-Adressen angeben, die die mehrstufige Authentifizierung für Benutzer umgehen können, die sich vom Intranet des Unternehmens aus anmelden.
Im Verbund|<li>Alle verbundene Benutzer – Alle Verbundbenutzer, die sich von innerhalb der Organisation anmelden, umgehen die mehrstufige Authentifizierung mithilfe eines von AD FS ausgestellten Anspruchs.</li><li>Bestimmte IP-Adressbereiche – Administratoren können einen Bereich von IP-Adressen angeben, die die mehrstufige Authentifizierung für Benutzer umgehen können, die sich vom Intranet des Unternehmens aus anmelden.

Diese Umgehung funktioniert nur von innerhalb des Intranets eines Unternehmens. Wenn Sie beispielsweise nur alle Verbundbenutzer ausgewählt haben, und sich ein Benutzer von außerhalb des Unternehmens anmeldet, muss dieser Benutzer sich mit der Multi-Factor Authentication authentifizieren. Dies gilt auch, wenn der Benutzer einen AD FS-Anspruch vorweisen kann. Die folgende Tabelle beschreibt, wann die mehrstufige Authentifizierung und App-Kennwörter innerhalb und außerhalb Ihres Unternehmensnetzwerks erforderlich sind, wenn vertrauenswürdige IP-Adressen aktiviert sind.


|Vertrauenswürdige IP-Adressen aktiviert| Vertrauenswürdige IP-Adressen deaktiviert
:------------- | :------------- | :------------- | 
Innerhalb des Unternehmensnetzwerks|Für Browserflüsse, für die KEINE mehrstufige Authentifizierung erforderlich ist.|Für Browserflüsse, für die eine mehrstufige Authentifizierung erforderlich ist.
|Für Rich Client-Apps funktionieren normale Kennwörter, wenn der Benutzer keine App-Kennwörter erstellt hat. Sobald ein App-Kennwort erstellt wurde, muss dieses angegeben werden.|Für Rich Client-Apps, für die App-Kennwörter erforderlich sind.
Außerhalb des Unternehmensnetzwerks|Für Browserflüsse, für die eine mehrstufige Authentifizierung erforderlich ist.|Für Browserflüsse, für die eine mehrstufige Authentifizierung erforderlich ist.
|Für Rich Client-Apps, für die App-Kennwörter erforderlich sind.|Für Rich Client-Apps, für die App-Kennwörter erforderlich sind.

### Aktivieren von vertrauenswürdigen IP-Adressen

1. Melden Sie sich im Azure-Verwaltungsportal an.
2. Klicken Sie im linken Bereich auf "Active Directory".
3. Klicken Sie unter "Verzeichnis" auf das Verzeichnis, für das Sie eine vertrauenswürdige IP-Anmeldung einrichten möchten.
4. Klicken Sie im ausgewählten Verzeichnis auf "Konfigurieren".
5. Klicken Sie im Abschnitt "Multi-Factor Authentication" auf "Diensteinstellungen verwalten".
6. Wählen Sie auf der Seite „Diensteinstellungen“ unter „Vertrauenswürdige IPs“ entweder:

	- Für Anforderungen von verbundene Benutzern, die aus meinem Intranet stammen – Alle Verbundbenutzer, die sich vom Unternehmensnetzwerk aus anmelden, umgehen die mehrstufige Authentifizierung mithilfe eines von AD FS ausgestellten Anspruchs. 
	- Für Anforderungen aus einem bestimmten Bereich öffentlicher IPs – Geben Sie mithilfe der CIDR-Notation die IP-Adressen in die Textfelder ein. Beispiel: xxx.xxx.xxx.0/24 für IP-Adressen im Bereich xxx.xxx.xxx. 1 – xxx.xxx.xxx. 254 oder xxx.xxx.xxx.xxx/32 für eine einzelne IP-Adresse. Sie können bis zu zwölf IP-Adressbereiche eingeben.

7. Klicken Sie auf "Speichern".
8. Sobald die Updates angewendet wurden, klicken Sie auf "Schließen".



![Cloud](./media/multi-factor-authentication-whats-next/trustedips2.png)



 
## App-Kennwörter

In einigen Anwendungen wie Office 2010 oder älter und Apple Mail können Sie keine mehrstufige Authentifizierung verwenden. Um diese Anwendungen zu verwenden, müssen Sie "App-Kennwörter" anstelle Ihres herkömmlichen Kennworts angeben. Mit App-Kennwörtern kann eine Anwendung die die mehrstufige Authentifizierung umgehen und weiter ausgeführt werden.

>[AZURE.NOTE] Moderne Authentifizierung für Office 2013-Clients
>
> Office 2013-Clients (einschließlich Outlook) unterstützen jetzt neue Authentifizierungsprotokolle und bieten die Möglichkeit zum Aktivieren von Multi-Factor Authentication. Damit sind nach der Aktivierung keine App-Kennwörter mehr für Office 2013-Clients erforderlich. Weitere Informationen finden Sie unter [Office 2013 modern authentication public preview announced](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/) ("Öffentliche Preview für moderne Authentifizierung in Office 2013", in englischer Sprache).



### Wichtige Informationen zu App-Kennwörtern

Es folgt eine Liste mit wichtigen Informationen, die Sie über App-Kennwörter wissen sollten.

Authentifizierungsvorgang|Bei Browser-Apps|Bei Nicht-Browser-Apps
:------------- | :------------- | :------------- 
|<ul><li>Die 1. Stufe der Authentifizierung wird lokal durchgeführt.</li><li>Die 2. Stufe ist eine telefonbasierte Methode, die von Cloud-Identität ausgeführt wird.</li>|<ul><li>Administratoren und Benutzer können App-Kennwörtern zur Anmeldung nutzen.

- Benutzer können über mehrere App-Kennwörter verfügen, was die Angriffsfläche für Diebstahl vergrößert. Da App-Kennwörter schwer zu merken sind, könnten Personen eher dazu neigen, sich diese aufzuschreiben. Dies wird nicht empfohlen und sollte unterbunden werden, da nur ein Faktor für die Anmeldung mit einem App-Kennwort erforderlich ist.
- Anwendungen, die Kennwörter zwischenspeichern und in lokalen Szenarios nutzen, könnten fehlschlagen, weil das App-Kennwort außerhalb der Organisations-ID möglicherweise nicht bekannt ist. Ein Beispiel sind Exchange-E-Mails, die lokal vorhanden sind, bei denen sich die archivierten Nachrichten jedoch in der Cloud befinden. Das gleiche Kennwort funktioniert nicht.
- Das eigentliche Kennwort wird automatisch erzeugt und nicht vom Benutzer festgelegt. Ein Grund hierfür ist, dass automatisch generierte Kennwörter sicherer und für einen Angreifer schwerer zu erraten sind.
- Derzeit können pro Benutzer maximal 40 Kennwörter festgelegt werden. Sie werden dazu aufgefordert, eines der vorhandenen App-Kennwörter zu löschen, damit das neue Kennwort erstellt werden kann.
- Nach der Aktivierung der mehrstufigen Authentifizierung in einem Benutzerkonto können App-Kennwörter mit den meisten Nicht-Browserclients wie Outlook und Lync verwendet werden, wobei administrative Aufgaben auch dann nicht mithilfe von App-Kennwörtern über Nicht-Browseranwendungen wie Windows Power Shell durchgeführt werden können, wenn der Benutzer über ein Administratorkonto verfügt. Stellen Sie sicher, dass Sie zum Ausführen von PowerShell-Skripts ein Dienstkonto mit einem sicheren Kennwort erstellen und für dieses Konto die mehrstufige Authentifizierung nicht aktivieren.

>[AZURE.WARNING]  App-Kennwörter funktioniert nicht in Hybridumgebungen, in denen Clients sowohl mit lokalen AutoErmittlung-Endpunkten als auch mit solchen in der Cloud kommunizieren.
>
>Bitte beachten Sie, dass App-Kennwörter nicht in Hybridumgebungen funktionieren, in denen Clients sowohl mit lokalen AutoErmittlung-Endpunkten als auch mit solchen in der Cloud kommunizieren, da für solche Umgebungen lokal Domänenkennwörter und in der Cloud App-Kennwörter zur Authentifizierung erforderlich sind.


### Benennungsrichtlinien für App-Kennwörter
Es wird empfohlen, dass die Namen der App-Kennwörter auf das Gerät hinweisen, auf dem sie verwendet werden. Wenn Sie z. B. einen Laptop haben, der über Nicht-Browser-Apps wie Outlook, Word und Excel verfügt, müssen Sie nur ein App-Kennwort mit dem Namen Laptop erstellen und dieses App-Kennwort in all diesen Anwendungen verwenden. Obwohl Sie gesonderte Kennwörter für jede dieser Anwendungen erstellen können, wird dies nicht empfohlen. Die empfohlene Vorgehensweise besteht darin, ein App-Kennwort pro Gerät zu verwenden.


<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### App-Kennwörter im Verbund (SSO)
Azure AD unterstützt den Verbund mit lokalen Windows Server Active Directory-Domänendiensten (AD DS). Wenn Ihre Organisation im Verbund (SSO) mit Azure AD besteht und Sie Multi-Factor Authentication verwenden wollen, dann sind die folgenden Informationen wichtig, die Sie bei der Verwendung von App-Kennwörtern beachten sollten. Dies gilt nur für Verbundkunden (SSO).

- Das App-Kennwort wird von Azure AD überprüft, und somit wird der Verbund umgangen. Der Verbund wird nur beim Einrichten des App-Kennworts aktiv verwendet.
- Bei Verbundbenutzern (SSO) gehen wir nie zum Identitätsanbieter (IdP), im Gegensatz zum passiven Ablauf. Die Kennwörter werden in der Organisations-ID gespeichert. Wenn der Benutzer das Unternehmen verlässt, muss diese Information zur Organisations-ID übertragen werden, und zwar mithilfe von DirSync in Echtzeit. Das Deaktivieren oder Löschen von Konten kann bis zu drei Stunden bei der Synchronisierung dauern, sodass das Deaktivieren bzw. Löschen des App-Kennworts verzögert wird.
- Lokale Einstellungen für die Clientzugriffssteuerung werden vom App-Kennwort nicht berücksichtigt.
- Für das App-Kennwort ist keine lokale Authentifizierung-Protokollierung / -Überwachungsfunktion verfügbar.
- Weitere Schulungen für Endbenutzer sind für den Microsoft Lync 2013-Client erforderlich. Die erforderlichen Schritte finden Sie unter "Gewusst wie: Ändern des Kennworts in Ihrer E-Mail-Adresse zum App-Kennwort".
- Bestimmte erweiterte Architekturentwürfe erfordert möglicherweise, dass eine Kombination aus Organisationsbenutzername und Kennwörter sowie App-Kennwörter bei der Verwendung der Multi-Factor Authentication mit Clients erforderlich ist, je nachdem, wo die Authentifizierung stattfindet. Bei Clients, die eine lokale Infrastruktur authentifizieren, verwenden Sie einen Organisationsbenutzernamen und -kennwort. Für Clients, die bei Azure AD authentifizieren, verwenden Sie das App-Kennwort.

Nehmen wir beispielsweise an, Sie verfügen über eine Architektur, die Folgendes umfasst:

- Sie werden Ihre lokale Instanz von Active Directory mit Azure AD in Verbund setzen.
- Sie verwenden Exchange online.
- Sie verwenden Lync, das speziell lokal ist.
- Sie verwenden Azure Multi-Factor Authentication,


<center>![Proofup](./media/multi-factor-authentication-whats-next/federated.png)</center>

 In diesen Fällen müssen Sie Folgendes tun:

- Verwenden Sie beim Anmelden bei Lync den Benutzernamen und das Kennwort Ihres Unternehmens.
- Verwenden Sie ein App-Kennwort beim Versuch, über einen Outlook-Client auf das Adressbuch zuzugreifen, der die Verbindung mit Exchange Online herstellt.

### Erstellung von App-Kennwörtern erlauben
Standardmäßig können keine Benutzer App-Kennwörter erstellen. Dieses Feature muss aktiviert sein. Damit Benutzer die Möglichkeit bekommen, App-Kennwörter zu erstellen, gehen Sie wie folgt vor.

#### Benutzern das Erstellen von App-Kennwörtern erlauben



1. Melden Sie sich im Azure-Verwaltungsportal an.
2. Klicken Sie im linken Bereich auf "Active Directory".
3. Klicken Sie unter "Verzeichnis" auf das Verzeichnis für den Benutzer, dem Sie die Erlaubnis erteilen möchten.
4. Klicken Sie oben auf "Benutzer".
5. Klicken Sie unten auf der Seite auf "Verwalten der Multi-Factor Authentication". Die Seite für die mehrstufige Authentifizierung wird geöffnet.
6. Klicken Sie oben auf der Seite "Multi-Factor Authentication" auf "Diensteinstellungen".
7. Stellen Sie sicher, dass das Optionsfeld neben "Benutzern die Erstellung von App-Kennwörtern zum Anmelden bei Nicht-Browser-Anwendungen erlauben" aktiviert ist.

<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>

### Erstellen von App-Kennwörtern
Benutzer können App-Kennwörter während ihrer ersten Registrierung erstellen. Sie erhalten am Ende des Registrierungsprozesses eine Option, die sie dazu befähigt.

Darüber hinaus können Benutzer App-Kennwörter auch später erstellen, durch Ändern ihrer Einstellungen im Azure-Verwaltungsportal, im Office 365-Portal oder durch

### Erstellen von App-Kennwörtern im Office 365-Portal
--------------------------------------------------------------------------------


1. Melden Sie sich beim Office 365-Portal an.
2. Wählen Sie in der oberen rechten Ecke das Einstellungs-Widget aus.
3. Wählen Sie links "Zusätzliche Sicherheitsüberprüfung".
4. Wählen Sie auf der rechten Seite **Aktualisieren meiner Telefonnummern für die Kontosicherheit**.
5. Wählen Sie auf der Seite „Proofup“ oben „App-Kennwörter“.
6. Klicken Sie auf **Erstellen**.
7. Geben Sie einen Namen für das App-Kennwort an, und klicken Sie auf **Weiter**.
8. Kopieren Sie das App-Kennwort in die Zwischenablage, und fügen Sie es in Ihrer Anwendung hinzu.

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### Erstellen von App-Kennwörtern im Azure-Portal
--------------------------------------------------------------------------------
1. Melden Sie sich beim Azure-Verwaltungsportal an.
3. Klicken Sie oben mit der rechten Maustaste auf Ihren Benutzernamen, und wählen Sie "Zusätzliche Sicherheitsüberprüfung" aus.
5. Wählen Sie auf der Seite „Proofup“ oben „App-Kennwörter“.
6. Klicken Sie auf **Erstellen**.
7. Geben Sie einen Namen für das App-Kennwort an, und klicken Sie auf **Weiter**.
8. Kopieren Sie das App-Kennwort in die Zwischenablage, und fügen Sie es in Ihrer Anwendung hinzu.


<center>![Cloud](./media/multi-factor-authentication-whats-next/app2.png)</center>

### Erstellen von App-Kennwörtern ohne Office 365- oder Azure-Abonnement
--------------------------------------------------------------------------------
1. Melden Sie sich an bei [https://myapps.microsoft.com](https://myapps.microsoft.com).	
2. Wählen Sie oben das Profil aus.
3. Klicken Sie auf Ihren Benutzernamen, und wählen Sie "Zusätzliche Sicherheitsüberprüfung".
5. Wählen Sie auf der Seite „Proofup“ oben „App-Kennwörter“
6. Klicken Sie auf **Erstellen**.
7. Geben Sie einen Namen für das App-Kennwort an, und klicken Sie auf **Weiter**.
8. Kopieren Sie das App-Kennwort in die Zwischenablage, und fügen Sie es in Ihrer Anwendung hinzu.

<center>![Cloud](./media/multi-factor-authentication-whats-next/myapp.png)</center>

## Multi-Factor Authentication für gespeicherte Geräte und Browser (Öffentliche Vorschau) aussetzen

Das Aussetzen der mehrstufigen Authentifizierung für gespeicherte Geräte und Browser ist ein Feature, mit dem Sie Benutzern die Möglichkeit zum Aussetzen der Authentifizierung für eine festgelegte Anzahl von Tagen nach der Durchführung einer erfolgreichen Multi-Factor Authentication geben können. Dies ist ein kostenloses Feature für alle Benutzer der Multi-Factor Authentication, das die Benutzerfreundlichkeit für alle Benutzer verbessert. Da Benutzer damit die mehrstufige Authentifizierung aussetzen können, kann dieses Feature möglicherweise die Kontosicherheit verringern.

Um sicherzustellen, dass die Benutzerkonten geschützt sind, sollten Sie die Multi-Factor Authentication für deren Geräte in folgenden Fällen wiederherstellen:

- Wenn das Unternehmenskonto gefährdet ist.
- Wenn ein gespeichertes Gerät verloren ist oder gestohlen wurde.

> [AZURE.NOTE] Dieses Feature wird als ein Cookie Browsercache implementiert. Es funktioniert nicht, wenn Browsercookies nicht aktiviert sind.

### Vorgehensweise beim Aktivieren/Aussetzen der Multi-Factor Authentication für gespeicherte Geräte und Festlegen

<ol>
<li>Melden Sie sich im Azure-Verwaltungsportal an.</li>
<li>Klicken Sie im linken Bereich auf "Active Directory".</li>
<li>Klicken Sie unter Active Directory auf das Verzeichnis, in dem Sie das Aussetzen von Multi-Factor Authentication für gespeicherte Geräte einrichten möchten.</li>
<li>Klicken Sie im ausgewählten Verzeichnis auf "Konfigurieren".</li>
<li>Klicken Sie im Abschnitt "Multi-Factor Authentication" auf "Diensteinstellungen verwalten".</li>
<li>Auf der Seite "Einstellungen" unter "Geräteeinstellungen von Benutzern verwalten", **Aktivieren/Deaktivieren Sie mehrstufige Authentifizierung anhalten, indem ein Gerät gespeichert wird**.</li>
![Aussetzen von Geräten](./media/multi-factor-authentication-manage-users-and-devices/suspend.png) <li>Legen Sie die Anzahl der Tage fest, an denen Sie die Aussetzung zulassen möchten. Der Standardwert ist 14 Tage.</li> <li>Klicken Sie auf "Speichern".</li> <li>Klicken Sie auf "Schließen".</li>

<!---HONumber=AcomDC_0128_2016-->