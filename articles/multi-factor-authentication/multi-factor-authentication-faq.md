<properties 
	pageTitle="Azure Multi-Factor Authentication – Häufig gestellte Fragen" 
	description="Enthält eine Liste mit häufig gestellten Fragen und Antworten im Zusammenhang mit Azure Multi-Factor Authentication (MFA). MFA ist eine Methode zum Überprüfen der Identität, für die mehr als nur ein Benutzername und ein Kennwort erforderlich ist. Sie bietet eine zusätzliche Sicherheitsebene für Benutzeranmeldungen und -transaktionen." 
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
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication – Häufig gestellte Fragen


In diesem Abschnitt mit häufig gestellten Fragen werden Fragen zur Azure Multi-Factor Authentication beantwortet. In diesem Abschnitt mit häufig gestellten Fragen werden Fragen zur Verwendung des Diensts beantwortet, z. B. zum Abrechnungsmodell und zu den Anwendungsmöglichkeiten.

## Allgemein

**Wo erhalte ich Hilfe zur Azure Multi-Factor Authentication?**

[Durchsuchen der Microsoft Knowledge Base (KB)](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- Suchen Sie in der Microsoft Knowledge Base (KB) nach technischen Lösungen für häufige Probleme und nach Unterstützung für Microsoft Azure Multi-Factor Authentication-Server (PhoneFactor).

[Microsoft Azure Active Directory-Foren](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- Sie können in der Community nach technischen Fragen und Antworten suchen oder eine eigene Frage stellen, indem Sie [hier](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required) klicken.

[Zurücksetzen des Kennworts](mailto:phonefactorsupport@microsoft.com)

- Kunden der älteren PhoneFactor-Version, die Fragen zum Zurücksetzen des Kennworts haben oder hierbei Hilfe benötigen, können den unten angegebenen Link verwenden und eine Supportanfrage erstellen.

[Microsoft Azure Multi-Factor Authentication-Server (PhoneFactor) – Kundensupport](https://support.microsoft.com/oas/default.aspx?prid=14947)

- Verwenden Sie diesen Link, wenn Sie mit einem Microsoft-Supportmitarbeiter Kontakt aufnehmen möchten. Sie werden gebeten, einige Fragen zu beantworten, sodass wir die für Sie geeigneten Supportoptionen ermitteln können. Mögliche Supportoptionen sind E-Mail, Online-Übermittlung oder Telefonsupport.



**F: Wie werden Benutzerdaten vom Azure Multi-Factor Authentication-Server behandelt?**

Bei lokaler Verwendung des Multi-Factor Authentication (MFA)-Servers werden die Daten eines Benutzers auf den lokalen Servern gespeichert. Daten werden nicht dauerhaft in der Cloud gespeichert. Wenn der Benutzer eine zweistufige Authentifizierung durchführt, sendet der MFA-Server Daten an den Azure MFA-Clouddienst, um die Authentifizierung auszuführen. Wenn diese Authentifizierungsanforderungen an den Clouddienst gesendet werden, werden in der Anforderung und den Protokollen die folgenden Felder gesendet, damit sie in den Authentifizierungs-/Verwendungsberichten des Kunden verfügbar sind. Einige Felder sind optional und können für den Multi-Factor Authentication-Server aktiviert oder deaktiviert werden. Für die Kommunikation zwischen MFA-Server und MFA-Clouddienst wird SSL/TLS über den ausgehenden Port 443 verwendet. Die Felder lauten:

- Eindeutige ID: Benutzername oder interne ID des MFA-Servers
- Vor- und Nachname: optional
- E-Mail-Adresse: optional
- Rufnummer: für eine Anruf- oder SMS-Authentifizierung
- Gerätetoken: für die Authentifizierung einer mobilen App
- Authentifizierungsmodus 
- Authentifizierungsergebnis 
- Name des MFA-Servers 
- MFA-Server-IP 
- Client-IP – falls verfügbar



Zusätzlich zu den obigen Feldern werden auch das Authentifizierungsergebnis (Erfolg/Ablehnung) und der Grund für etwaige Ablehnungen mit den Authentifizierungsdaten gespeichert. Sie stehen in den Authentifizierungs-/Verwendungsberichten zur Verfügung.




## Abrechnung

**F: Entstehen durch Telefonanrufe oder Textnachrichten für die Benutzerauthentifizierung zusätzliche Kosten für meine Organisation?**

Diese Gebühren werden vollständig durch die Kosten abgedeckt, die pro Benutzer bzw. pro Authentifizierung für den Dienst erhoben werden. Für Organisationen entstehen keine zusätzlichen Kosten für individuelle Telefonanrufe oder Textnachrichten, die bei Azure Multi-Factor Authentication an ihre Endbenutzer gesendet werden. Für die Telefonbesitzer können ggf. Roaming-Gebühren oder andere Kosten anfallen, die vom jeweiligen Netzbetreiber für empfangene Telefonanrufe oder Textnachrichten erhoben werden.

**F: Wie werden der Organisation die Kosten für Azure Multi-Factor Authentication in Rechnung gestellt?**

Azure Multi-Factor Authentication ist als eigenständiger Dienst mit Abrechnungsoptionen „Pro Benutzer“ und „Pro Authentifizierung“ oder im Paket mit Azure Active Directory Premium, Enterprise Mobility Suite und Enterprise Cloud Suite verfügbar. Der eigenständige Dienst wird basierend auf der Nutzung abgerechnet. Die Abrechnung erfolgt entweder monatlich über Azure-Zahlungsverpflichtungen oder über eine jährliche Lizenz pro Benutzer, die sie im Rahmen eines Microsoft Enterprise Agreement, das offene Volumenlizenzprogramm, das Programm für Microsoft Cloud-Lösungsanbieter oder auch über Direktbezug erhalten.

>[AZURE.IMPORTANT]
Die Regionen in Australien sind für Kunden mit Geschäftspräsenz in Australien und Neuseeland verfügbar.

Abrechnungsmodell | Preis
------------- | ------------- |
Abrechnung pro Benutzer (Azure-Zahlungsverpflichtung)| 1,40 US-Dollar pro Monat (unbegrenzte Authentifizierungen)
Abrechnung pro Authentifizierung (Azure-Zahlungsverpflichtung)|1,40 US-Dollar für 10 Authentifizierungen
Jahreslizenz für einen Benutzer (Direktbezug)|1,40 US-Dollar pro Monat (unbegrenzte Authentifizierungen)
Jahreslizenz für einen Benutzer (Volumenlizenzprogramm)|Wenden Sie sich an Ihren [Enterprise Agreement-Vertreter](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx).

**F: Werden die Kosten für das Abrechnungsmodell „pro Benutzer“ basierend auf der Anzahl der Benutzer ermittelt, für die Multi-Factor Authentication aktiviert wurde, oder basierend auf der Anzahl der Benutzer, die Überprüfungen durchführen?**

Die Abrechnung basiert auf der Anzahl der Benutzer, für die Multi-Factor Authentication aktiviert wurde.

**Q: Wie funktioniert die Abrechnung für die Multi-Factor Authentication?**

Vor Verwendung der Nutzungs- und Abrechnungsmodelle „Abrechnung pro Benutzer“ und „Abrechnung pro Authentifizierung“ muss der Administrator bei Erstellung eines Multi-Factor Authentication-Anbieters im klassischen Microsoft Azure-Portal den entsprechenden Nutzungstyp auswählen. Hierbei handelt es sich um eine Ressource, die wie virtuelle Computer, Websites etc. mit dem Azure-Abonnement des Unternehmens abgerechnet wird. Bei Verwendung des Lizenzmodells werden Lizenzen für Azure Multi-Factor Authentication erworben und wie bei Office 365 und anderen Produkten mit Abonnementmodell, den Benutzern zugewiesen.

**Q:Gibt es eine kostenlose Version von Azure Multi-Factor Authentication für Administratoren?**

Ein Teil der Funktionen von Azure Multi-Factor Authentication, genannt „MFA für Azure-Administratoren“ wird Azure Global-Administratoren kostenlos zur Verfügung gestellt, wenn mit dem entsprechenden Azure Active Directory kein Azure Multi-Factor Authentication-Anbieter für verbrauchsbasierende Abrechnung verknüpft wurde. Bei Verwendung des Multi-Factor Authentication-Anbieters wird bei allen Administratoren und Benutzern in diesem Verzeichnis, für die MFA aktiviert ist, auf die Vollversion von Azure Multi-Factor Authentication upgegradet.

**Q:Gibt es eine kostenlose Version von Azure Multi-Factor Authentication für Benutzer von Office 365?**

Ein Teil der Funktionen von Azure Multi-Factor Authentication, genannt „MFA für Office 365“ wird Benutzern mit zugewiesener Lizenz für Office 365 kostenlos zur Verfügung gestellt, wenn mit dem entsprechenden Azure Active Directory kein Azure Multi-Factor Authentication-Anbieter für verbrauchsbasierende Abrechnung verknüpft wurde. Bei Verwendung des Multi-Factor Authentication-Anbieters wird bei allen Administratoren und Benutzern in diesem Verzeichnis, für die MFA aktiviert ist, auf die Vollversion von Azure Multi-Factor Authentication upgegradet.

**Q: Ist der Wechsel zwischen den Verbrauchsmodellen „Pro Benutzer“ und „Pro Authentifizierung“ jederzeit möglich?**

Das Abrechnungsmodell wird während der Ressourcenerstellung ausgewählt und kann nicht mehr geändert werden, nachdem die Ressource bereitgestellt wurde. Es ist jedoch möglich, eine neue Multi-Factor Authentication zu erstellen, um die ursprüngliche Authentifizierung zu ersetzen. Beachten Sie, dass die Benutzereinstellungen und Konfigurationsoptionen nicht auf die neue Ressource übertragen werden können.

**Q: Kann ich jederzeit zwischen der verbrauchsbasierten Abrechnung und den Lizenzmodellen wechseln?**

Sie können jederzeit Lizenzen für Azure Multi-Factor Authentication, Azure Active Directory Premium , Enterprise Mobility Suite und Enterprise Cloud Suite erwerben. Wenn Lizenzen einem Verzeichnis hinzugefügt werden, für das bereits ein Multi-Factor Authentication-Anbieter für die Abrechnung pro Benutzer eingerichtet wurde, so wird die verbrauchsbasierte Abrechnung um die Anzahl der im Besitz befindlichen Lizenzen reduziert. Wurde allen Benutzern, bei denen MFA aktiviert ist, eine Lizenz zugewiesen, kann der Administrator den Azure Multi-Factor Authentication-Anbieter löschen. Unternehmen können die Abrechnung pro Authentifizierung nicht mit dem Lizenzmodell kombinieren. Wenn ein Multi-Factor Authentication-Anbieter für die Abrechnung nach Anzahl der Authentifizierungen mit einem Verzeichnis verknüpft ist, werden dem Unternehmen alle MFA-Verifizierungsanfragen in Rechnung gestellt. Dies gilt ungeachtet eventuell im Besitz befindlicher Lizenzen.

**Q: Muss mein Unternehmen Identitäten verwenden und synchronisieren, um Azure Multi-Factor Authentication benutzen zu dürfen? **

Bei der Verwendung des verbrauchsbasierten Abrechnungsmodells ist Azure Active Directory nicht erforderlich. Es steht Ihnen frei, einen Multi-Factor Authentication-Anbieter mit einem Verzeichnis zu verknüpfen. Besteht keine Verknüpfung zu einem Verzeichnis, können Organisationen die Azure Multi-Factor Authentication-Server oder das lokale SDK verwenden. Werden Lizenzen verwendet, ist Azure Active Directory erforderlich, da die Lizenzen nach dem Kauf dem Verzeichnis hinzugefügt und den Benutzern in diesem zugewiesenwerden.


## Benutzerfreundlichkeit

**F: Was kann ich tun, wenn ich auf meinem Telefon keine Antwort erhalte oder wenn ich mein Telefon vergessen habe?**

Wenn Sie zuvor ein alternatives Telefon als Ersatzanschluss konfiguriert haben, versuchen Sie es erneut, und wählen Sie auf der Anmeldeseite an der entsprechenden Aufforderung den Ersatzanschluss aus. Wenn Sie keine andere Methode konfiguriert haben, wenden Sie sich an Ihren Administrator, und bitten Sie ihn, die für Ihr primäres Telefon angegebene Nummer (mobil oder Büro) zu aktualisieren.


**F: Wie gehe ich als Administrator vor, wenn ein Benutzer meldet, dass sein Konto gesperrt wurde?**

Sie können das Benutzerkonto zurücksetzen, indem Sie das erneute Durchführen des Registrierungsprozesses erzwingen. Weitere Informationen finden Sie unter [Verwalten von Benutzereinstellungen mit Azure Multi-Factor Authentication (MFA) in der Cloud](multi-factor-authentication-manage-users-and-devices.md).

**F: Wie gehe ich vor, wenn ein Gerät eines Benutzers verloren gegangen ist oder gestohlen wurde, auf dem App-Kennwörter verwendet werden?**

Sie können alle App-Kennwörter des Benutzers löschen, um nicht autorisierte Zugriffe zu verhindern. Sobald ein Ersatzgerät verfügbar ist, kann der Benutzer neue Kennwörter erstellen. Weitere Informationen finden Sie unter [Verwalten von Benutzereinstellungen mit Azure Multi-Factor Authentication (MFA) in der Cloud](multi-factor-authentication-manage-users-and-devices.md).

**F: Wie gehe ich vor, wenn die Anmeldung eines Benutzers in Nicht-Browser-Apps fehlschlägt?**

- Benutzer, für die Multi-Factor Authentication aktiviert wurde, benötigen ein App-Kennwort für die Anmeldung in einigen Nicht-Browser-Apps.
- Die betreffenden Benutzer müssen die Anmeldeinformationen in der Anwendung löschen, die Anwendung neu starten und sich dann mit ihrem Benutzernamen und dem App-Kennwort anmelden. 

Informationen zum Erstellen von App-Kennwörtern finden Sie in der [Hilfe zu App-Kennwörtern](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Moderne Authentifizierung für Office 2013-Clients
>
> Office 2013-Clients (einschließlich Outlook) unterstützen jetzt neue Authentifizierungsprotokolle und bieten die Möglichkeit zum Aktivieren von Multi-Factor Authentication. Damit sind nach der Aktivierung keine App-Kennwörter mehr für Office 2013-Clients erforderlich. Weitere Informationen finden Sie unter [Office 2013 modern authentication public preview announced](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/) („Öffentliche Preview für moderne Authentifizierung in Office 2013“, in englischer Sprache).

**F: Was kann ich tun, wenn ich keine Textnachricht erhalte oder beim Antworten auf die Textnachricht ein Timeout auftritt?**

Der Azure Multi-Factor Authentication-Dienst sendet Textnachrichten über SMS-Aggregatoren. Viele Faktoren können die Zuverlässigkeit der Nachrichtenzustellung und der Empfangsbestätigung beeinflussen, z. B. der verwendete Aggregator, das Bestimmungsland, der Mobilfunkanbieter und die Signalstärke. Aus diesem Grund kann nicht gewährleistet werden, dass Textnachricht und Antwort-SMS beim Verwenden dieses bidirektionalen Verfahrens immer zuverlässig übermittelt bzw. empfangen werden. Daher wird, wenn möglich, die Verwendung des unidirektionalen SMS-Verfahrens empfohlen, bei dem nur eine einzelne Textnachricht versendet wird. Dieses Verfahren ist zuverlässiger und verhindert, dass für die Benutzer zusätzliche SMS-Gebühren entstehen, wenn sie z. B. auf eine SMS antworten, die aus einem anderen Land gesendet wurde.

Außerdem ist die Überprüfung per Textnachricht in einigen Ländern zuverlässiger, z. B. in den USA oder Kanada. Benutzer, bei denen Probleme mit dem zuverlässigen Empfang der Textnachrichten für Azure Multi-Factor Authentication auftreten, sollten stattdessen die Benachrichtigung in der mobilen App oder Telefonanrufe als Methode auswählen. Die mobile App bietet den zusätzlichen Vorteil, dass Benachrichtigungen sowohl über Mobilfunk- als auch über WLAN-Verbindungen empfangen werden können. Die Kennung der mobilen App wird selbst dann angezeigt, wenn das Gerät gar keinen Netzempfang hat. Die Azure Authenticator-App ist für [Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) und [iOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) verfügbar.

**F: Kann ich Hardwaretoken mit dem Azure MFA-Server verwenden?**

Bei Verwendung des Azure MFA-Servers können OATH TOTP-Token von Drittanbietern importiert und für MFA verwendet werden. Wir unterstützen derzeit den Import von OATH TOTP-Token in einem älteren PSKC-Format, das Gemalto für seine Token generieren kann, und unterstützen das Importieren von Token im CSV-Format. Beim Importieren von Token im CSV-Format muss die CSV-Datei eine Seriennummer, einen geheimen Schlüssel im Base32-Format und ein Zeitintervall (in der Regel 30 Sekunden) enthalten.

Wenn also die „ActiveIdentity“-Token OATH TOTP-Token sind und Sie die Datei mit dem geheimen Schlüssel einer CSV-Datei hinzufügen können, die in den Azure MFA-Server importiert werden kann, können Sie diese anschließend nutzen. OATH-Token können mit AD FS, RADIUS, wenn das Clientsystem Access-Challenge-Antworten verarbeiten kann, und der IIS-Formularauthentifizierung verwendet werden.

**Q: Kann ich den Azure MFA-Server verwenden, um Terminaldienste zu sichern?**

Ja! Ab Windows Server 2012 R2 ist hierfür jedoch ein RD-Gateway erforderlich.

Unter Windows Server 2012 R2 kann der Azure MFA-Server dank Sicherheitsänderungen nicht mehr so in das LSA-Sicherheitspaket einhaken, wie es noch unter Windows Server 2012 und früheren Versionen möglich war. Folgen Sie bei der Verwendung von Terminaldiensten unter Windows 2012 oder einer älteren Version einfach den Anweisungen im Abschnitt [Sichern einer Anwendung mit der Windows-Authentifizierung](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure).

Wenn Sie Windows Server 2012 R2 verwenden, benötigen Sie jedoch ein RD-Gateway.

## Fehler

**F: Wie gehe ich vor, wenn bei der Authentifizierung über mobile App-Benachrichtigungen der Fehler „Authentifizierungsanforderung erfolgte nicht für ein aktives Konto“ angezeigt wird?**

- Öffnen Sie [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/), und melden Sie sich mit Ihrem Organisationskonto an.
- Falls erforderlich, klicken Sie auf "Weitere Überprüfungsoptionen", und wählen Sie eine andere Option zum Abschließen der Kontoüberprüfung aus.
- Klicken Sie auf "Zusätzliche Sicherheitsüberprüfung".
- Entfernen Sie das vorhandene Konto aus Ihrer mobilen Anwendung.
- Klicken Sie auf "Konfigurieren", und folgen Sie den Anweisungen, um die mobile Anwendung neu zu konfigurieren.




**F: Wie gehe ich vor, wenn der Fehler „0x800434D4L“ angezeigt wird, während ich versuche, mich mit einer Nicht-Browser-Anwendung anzumelden?**

Derzeit kann die zusätzliche Sicherheitsüberprüfung nur für Anwendungen und Dienste verwendet werden, auf die Sie über Ihren Browser zugreifen können. Nicht-Browser-Anwendungen (auch bezeichnet als Rich Client-Anwendungen), die auf dem lokalen Computer installiert sind, z. B. Windows Powershell, können nicht zusammen mit Konten verwendet werden, bei denen eine zusätzliche Sicherheitsüberprüfung erforderlich ist. In diesem Fall wird von der Anwendung der Fehler "0x800434D4L" ausgegeben.

Als Problemumgehung können Sie zusätzlich zu Ihrem normalen Konto ein separates Benutzerkonto für Administratoraufgaben erstellen. Anschließend können Sie die Postfächer beider Konten miteinander verknüpfen, sodass Sie für die Anmeldung bei Outlook lediglich ihr normales Konto benötigen. Weitere Informationen hierzu finden Sie unter [Einem Administrator das Öffnen und Anzeigen des Inhalts des Postfachs eines Benutzers ermöglichen]http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1).

<!---HONumber=AcomDC_0218_2016-->