<properties
	pageTitle="FAQ: Azure AD-Kennwortverwaltung | Microsoft Azure"
	description="Häufig gestellte Fragen (FAQ) zur Kennwortverwaltung in Azure AD, einschließlich Kennwortzurücksetzung, Registrierung, Berichten und Rückschreibung in das lokale Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="kbrint"
	editor="billmath"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/18/2015" 
	ms.author="asteen"/>

# Häufig gestellte Fragen zur Kennwortverwaltung
Im Folgenden werden einige häufig gestellte Fragen rund um die Kennwortverwaltung aufgeführt.

Wenn Sie eine Frage haben, die Sie nicht beantworten können, oder zu einem bestimmten Problem Hilfe benötigen, können Sie hier weiterlesen und nachsehen, ob wir das Thema bereits behandelt haben. Falls dies nicht der Fall sein sollte: Stellen Sie gerne jede Frage, die hier in den [Azure AD-Foren](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) noch nicht aufgeführt ist, und wir melden uns so bald wie möglich.

Diese FAQ sind in folgende Abschnitte unterteilt:

- [**Fragen zum Registrieren für die Kennwortzurücksetzung**](#password-reset-registration)
- [**Fragen zum Zurücksetzen des Kennworts**](#password-reset)
- [**Fragen zu Kennwortverwaltungsberichten**](#password-management-reports)
- [**Fragen zur Kennwortrückschreibung**](#password-writeback)

## Registrieren für die Kennwortzurücksetzung
 - **F: Können meine Benutzer ihre eigenen Daten zur Kennwortzurücksetzung registrieren?**

 > **A:** Ja, solange die Kennwortzurücksetzung aktiviert ist und die Benutzer lizenziert sind, können diese zum Portal für die Registrierung der Kennwortzurücksetzung unter http://aka.ms/ssprsetup wechseln und ihre Authentifizierungsinformationen registrieren, die beim Zurücksetzen des Kennworts verwendet werden. Benutzer können sich auch registrieren, indem sie in den Zugriffsbereich unter http://myapps.microsoft.com wechseln und auf die Profilregisterkarte und dann auf die Option "Für die Kennwortzurücksetzung registrieren" klicken. Weitere Informationen zur Konfiguration Ihrer Benutzer für die Kennwortzurücksetzung finden Sie unter "So konfigurieren Sie Benutzer für das Zurücksetzen von Kennwörtern".

 - **F: Kann ich im Namen meiner Benutzer Daten zur Kennwortzurücksetzung definieren?**

 > **A:** Ja, dies ist über DirSync oder PowerShell bzw. über das [Azure-Verwaltungsportal](https://manage.windowsazure.com) oder das Office-Verwaltungsportal möglich. Weitere Informationen zu diesem Feature finden Sie im Blogbeitrag "Improved Privacy for Azure AD MFA and Password Reset Phone Numbers" (in englischer Sprache) und unter "Verwendung von Daten für die Kennwortzurücksetzung".

 - **F: Kann ich Daten für Sicherheitsfragen vom lokalen Standort aus synchronisieren?**

 > **A:** Nein, das ist zurzeit noch nicht möglich, aber wir ziehen es in Betracht.

 - **F: Können meine Benutzer Daten so registrieren, dass anderen Benutzern diese Daten nicht angezeigt werden?**

 > **A:** Ja. Wenn Benutzer Daten beim Portal zur Registrierung für die Kennwortzurücksetzung registrieren, werden diese in privaten Authentifizierungsfeldern gespeichert, die nur für globale Administratoren und den Benutzer selbst sichtbar sind. Weitere Informationen zu diesem Feature finden Sie im Blogbeitrag "Improved Privacy for Azure AD MFA and Password Reset Phone Numbers" (in englischer Sprache) und unter "Verwendung von Daten für die Kennwortzurücksetzung".

 - **F: Müssen meine Benutzer registriert werden, damit sie das Zurücksetzen von Kennwörtern verwenden können?**

 > **A:** Nein, wenn Sie genügend Authentifizierungsinformationen in ihrem Auftrag definieren, müssen Benutzer sich nicht registrieren. Das Zurücksetzen des Kennworts funktioniert, solange Sie in den entsprechenden Feldern im Verzeichnis ordnungsgemäß formatierte Daten gespeichert haben. Weitere Informationen finden Sie unter "Verwendung von Daten für die Kennwortzurücksetzung".

 - **F: Kann ich die Felder "Telefon für Authentifizierung", "E-Mail-Adresse zur Authentifizierung" oder "Alternatives Telefon für Authentifizierung" im Namen meiner Benutzer synchronisieren oder festlegen?**

 > **A:** Derzeit nicht, aber wir ziehen diese Möglichkeit in Betracht.

 - **F: Wie erkennt das Registrierungsportal, welche Optionen meinen Benutzern angezeigt werden sollen?**

 > **A:** Das Portal zum Registrieren der Kennwortzurücksetzung zeigt nur die Optionen an, die Sie auf der Registerkarte "Konfigurieren" Ihres Verzeichnisses im Abschnitt zur Kennwortzurücksetzungsrichtlinie für Ihre Benutzer aktiviert haben. Wenn Sie also z. B. die Sicherheitsfragen nicht aktivieren, können Benutzer sich für diese Option nicht registrieren.

 - **F: Wann gilt ein Benutzer als registriert?**

 > **A:** Ein Benutzer gilt als registriert, wenn für ihn mindestens N Authentifizierungsinformationen definiert wurden, wobei N die Anzahl erforderlicher Authentifizierungsmethoden bezeichnet, die Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) festgelegt haben. Weitere Informationen hierzu finden Sie unter "Anpassen der Richtlinie zum Zurücksetzen des Benutzerkennworts".


## Zurücksetzen des Kennworts

 - **F: Wie lange muss ich warten, bis eine E-Mail, eine SMS oder ein Anruf von der Kennwortzurücksetzung eintrifft?**

 > **A:** E-Mail, SMS-Nachrichten und Telefonanrufe sollten in weniger als 1 Minute, normalerweise innerhalb von 5-20 Sekunden eingehen. Wenn Sie in diesem Zeitraum keine Benachrichtigung erhalten, überprüfen Sie den Junk-Ordner, stellen Sie sicher, dass die Nummer / E-Mail-Adresse richtig ist und dass die Authentifizierungsdaten im Verzeichnis richtig formatiert sind. Weitere Informationen zur Formatierung von Telefonnummern und E-Mail-Adressen für die Kennwortzurücksetzung finden Sie unter "Verwendung von Daten für die Kennwortzurücksetzung".

 - **F: Welche Sprachen werden von der Kennwortzurücksetzung unterstützt?**

 > **A:** Die Benutzeroberfläche der Kennwortzurücksetzung, der SMS-Nachrichten und der Sprachanrufe wurden in denselben 40 Sprachen lokalisiert, die in Office 365 unterstützt werden. Diese sind: Arabisch, Bulgarisch, vereinfachtes Chinesisch, traditionelles Chinesisch, Kroatisch, Tschechisch, Dänisch, Niederländisch, Englisch, Estnisch, Finnisch, Französisch, Deutsch, Griechisch, Hebräisch, Hindi, Ungarisch, Indonesisch, Italienisch, Japanisch, Kasachisch, Koreanisch, Lettisch, Litauisch, Malaiisch (Malaysia), Norwegisch (Bokmål), Polnisch, Portugiesisch (Brasilien), Portugiesisch (Portugal), Rumänisch, Russisch, Serbisch (Lateinisch), Slowakisch, Slowenisch, Spanisch, Schwedisch, Thai, Türkisch, Ukrainisch und Vietnamesisch.

 - **F: Welche Teile der Oberfläche zum Zurücksetzen von Kennwörtern werden angepasst, wenn ich organisationsspezifisches Branding auf der Registerkarte "Konfigurieren" für mein Verzeichnis festlege?**

 > **A:** Das Portal zur Kennwortzurücksetzung zeigt das Logo Ihrer Organisation und erlaubt Ihnen zudem, den Link "Wenden Sie sich an Ihren Administrator" so zu konfigurieren, dass er auf eine benutzerdefinierte E-Mail-Adresse oder eine URL verweist. E-Mails, die von der Kennwortzurücksetzung gesendet werden, enthalten das Logo, die Farben (in diesem Fall rot) sowie den Namen Ihrer Organisation im Nachrichtentext der E-Mail. Zudem weisen sie einen angepassten "Von"-Namen auf. Unten sehen Sie ein Beispiel mit allen organisationsspezifisch angepassten Elementen. Weitere Informationen finden Sie unter "Anpassen des Aussehens und Verhaltens der Kennwortzurücksetzung".

  ![][001]

 - **F: Wie informiere ich meine Benutzer, wo sie ihre Kennwörter zurücksetzen können?**

 > **A:** Sie können Ihre Benutzer direkt an https://passwordreset.microsoftonline.com verweisen, oder Sie können sie auffordern, auf den Link "Sie können nicht auf Ihr Konto zugreifen" zu klicken, der auf jedem Anmeldebildschirm für die Schul- oder Arbeits-ID angezeigt wird. Sie können diese Links an jeder Stelle veröffentlichen (oder URL-Umleitungen darauf erstellen), die für die Benutzer leicht zugänglich ist.

 - **F: Kann ich diese Seite von einem mobilen Gerät aus verwenden?**

 > **A:** Ja, diese Seite funktioniert auf mobilen Geräten.

 - **F: Wird das Entsperren lokaler Active Directory-Konten unterstützt, wenn Benutzer ihre Kennwörter zurücksetzen?**

 > **A:** Ja, wenn ein Benutzer sein Kennwort zurücksetzt und das Rückschreiben von Kennwörtern in allen Versionen von Azure AD Connect oder in den Versionen Azure AD Sync 1.0.0485.0222 oder höher bereitgestellt wurde, wird das Konto des Benutzers automatisch entsperrt, sobald der Benutzer sein Kennwort zurücksetzt.

 - **F: Wie kann ich die Kennwortzurücksetzung direkt in die Anmeldeoberfläche auf den Desktops meiner Benutzer integrieren?**

 > **A:** Dies ist zurzeit noch nicht möglich. Wenn Sie diese Funktion jedoch unbedingt benötigen und ein Azure AD Premium-Kunde sind, können Sie Microsoft Identity Manager ohne zusätzliche Kosten installieren und die darin enthaltene lokale Lösung zur Kennwortzurücksetzung bereitstellen, um diese Anforderung zu erfüllen.

 - **F: Kann ich für verschiedene Gebietsschemas unterschiedliche Sicherheitsfragen festlegen?**

 > **A:** Nein, das ist zurzeit noch nicht möglich, aber wir ziehen es in Betracht.

 - **F: Wie viele Fragen können wir für die Authentifizierungsoption mit Sicherheitsfragen konfigurieren?**

 > **A:** Sie können bis zu 20 benutzerdefinierte Sicherheitsfragen im [Azure-Verwaltungsportal](https://manage.windowsazure.com) konfigurieren.

 - **F: Wie lang dürfen die Sicherheitsfragen sein?**

 > **A:** Sicherheitsfragen können zwischen 3 und 200 Zeichen lang sein.

 - **F: Wie lang dürfen die Antworten auf Sicherheitsfragen sein?**

 > **A:** Antworten dürfen 3 bis 40 Zeichen lang sein.

 - **F: Werden doppelte Antworten auf Sicherheitsfragen abgelehnt?**

 > **A:** Ja, doppelte Antworten auf Sicherheitsfragen werden abgelehnt.

 - **F: Kann ein Benutzer sich für dieselbe Sicherheitsfrage mehrmals registrieren?**

 > **A:** Nein, sobald ein Benutzer sich für eine bestimmte Frage registriert, kann er sich kein zweites Mal für dieselbe Frage registrieren.

 - **F: Ist es möglich, eine Mindestanzahl von Sicherheitsfragen für die Registrierung und Zurücksetzung festzulegen?**

 > **A:** Ja, es kann ein Grenzwert für die Registrierung und ein anderer für die Zurücksetzung festgelegt werden. Drei bis fünf Fragen können für die Registrierung und drei bis fünf Fragen für die Zurücksetzung verlangt werden.

 - **F: Wie werden die Sicherheitsfragen während der Zurücksetzung ausgewählt, wenn ein Benutzer mehr als die maximale Anzahl von Fragen registriert, die für die Zurücksetzung erforderlich sind?**

 > **A:** Aus der Gesamtzahl von Fragen, für die sich ein Benutzer registriert hat, werden N Sicherheitsfragen nach dem Zufallsprinzip ausgewählt, wobei N für die minimale Anzahl von Fragen steht, die für das Zurücksetzen von Kennwörtern erforderlich ist. Wenn für einen Benutzer beispielsweise fünf Sicherheitsfragen registriert wurden, aber nur drei für das Zurücksetzen erforderlich sind, werden aus diesen fünf nach dem Zufallsprinzip drei ausgewählt und dem Benutzer zum Zeitpunkt der Zurücksetzung angezeigt. Wenn der Benutzer die Fragen falsch beantwortet, wird der Auswahlvorgang erneut durchgeführt, um eine wiederholte Fragestellung zu verhindern.

 - **F: Werden Benutzer daran gehindert, Kennwörter innerhalb kurzer Zeit mehrmals zurückzusetzen?**

 > **A:** Ja, es wurden verschiedene Sicherheitsfunktionen in die Kennwortzurücksetzung integriert. Benutzer dürfen innerhalb einer Stunde nur fünfmal versuchen, das Kennwort zurückzusetzen, bevor sie für 24 Stunden gesperrt werden. Benutzer dürfen innerhalb einer Stunde nur fünfmal versuchen, eine Telefonnummer zu validieren, bevor sie für 24 Stunden gesperrt werden. Benutzer dürfen innerhalb einer Stunde nur fünfmal versuchen, die einmalige Anmeldung durchzuführen, bevor sie für 24 Stunden gesperrt werden.

 - **F: Wie lange ist die Einmalkennung per E-Mail und SMS gültig?**

 > **A:** Die Sitzungsdauer für das Zurücksetzen von Kennwörtern beträgt 105 Minuten. Dies bedeutet, dass der Benutzer vom Beginn des Vorgangs zur Kennwortzurücksetzung 105 Minuten Zeit hat, um das Kennwort zurückzusetzen. Die E-Mail- und SMS-Einmalkennungen sind nach Ablauf dieses Zeitraums ungültig.


## Berichte zur Kennwortverwaltung

 - **F: Wie lange dauert es, bis Daten in den Berichten zur Kennwortverwaltung angezeigt werden?**

 > **A:** Daten sollten innerhalb von 5 bis 10 Minuten in den Berichten zur Kennwortverwaltung angezeigt werden. In manchen Fällen kann es bis zu einer Stunde dauern.

 - **F: Wie kann ich die Berichte zur Kennwortverwaltung filtern?**

 > **A:** Sie können die Berichte zur Kennwortverwaltung filtern, indem Sie auf die kleine Lupe ganz rechts neben den Spaltenbeschriftungen am Anfang des Berichts klicken (siehe Screenshot). Wenn Sie umfangreichere Filterfunktionen benötigen, können Sie den Bericht in Excel exportieren und eine Pivottabelle erstellen.

  ![][002]

 - **F: Wie weit reichen die Berichte zur Kennwortverwaltung zurück?**

 > **A:** Die Berichte zur Kennwortverwaltung zeigen Vorgänge der letzten 30 Tage an. Wir untersuchen gerade, wie wir diesen Zeitraum verlängern können. Wenn Sie diese Daten archivieren möchten, können Sie die Berichte in regelmäßigen Abständen herunterladen und an einem anderen Speicherort speichern.

 - **F: Gibt es eine maximale Anzahl von Zeilen, die in den Berichten zur Kennwortverwaltung angezeigt werden können?**

 > **A:** Ja, jeder der Berichte zur Kennwortverwaltung kann maximal 1.000 Zeilen umfassen, unabhängig davon, ob sie auf der Benutzeroberfläche angezeigt oder heruntergeladen werden. Wir untersuchen gerade, wie dieser Grenzwert erhöht werden kann.


## Rückschreiben von Kennwörtern
 - **F: Wie funktioniert das Rückschreiben von Kennwörtern im Detail?**

 > **A:** Unter "Rückschreiben von Kennwörtern" finden Sie eine detaillierte Erläuterung der Vorgänge bei einer Kennwortrückschreibung sowie zum Datenfluss durch das System zurück in die lokale Umgebung. Unter "Sicherheitsmodell für das Rückschreiben von Kennwörtern" im Abschnitt "Rückschreiben von Kennwörtern" erfahren Sie, wie wir sicherstellen, dass das Rückschreiben von Kennwörtern ein sehr sicherer Dienst ist.

 - **F: Wie lange dauert es, bis das Rückschreiben von Kennwörtern funktioniert? Gibt es eine Synchronisierungsverzögerung wie bei der Kennworthashsynchronisierung?**

 > **A:** Die Kennwortrückschreibung findet sofort statt. Sie ist eine synchrone Pipeline, die grundlegend anders funktioniert als die Kennworthashsynchronisierung. Durch die Kennwortrückschreibung erhalten Benutzer in Echtzeit Feedback über den Erfolg der Kennwortzurücksetzung oder -änderung. Die durchschnittliche Zeit für das erfolgreiche Rückschreiben eines Kennworts liegt bei unter 500 ms.

 - **F: Für welche Arten von Konten funktioniert das Rückschreiben von Kennwörtern?**

 > **A:** Das Rückschreiben von Kennwörtern funktioniert für Benutzer mit Verbundauthentifizierung und mit Kennworthashsynchronisierung.

 - **F: Werden die Kennwortrichtlinien meiner Domäne durch die Kennwortrückschreibung durchgesetzt?**

 > **A:** Ja, die Kennwortrückschreibung setzt das Kennwortalter, den Verlauf, die Komplexität, Filter und alle anderen Einschränkungen durch, die Sie in Ihrer lokalen Domäne für Kennwörter festlegen können.

 - **Q: Ist die Kennwortrückschreibung sicher? Wie kann ich sicher sein, dass ich nicht gehackt werde?**

 > **A:** Ja, die Kennwortrückschreibung ist extrem sicher. Weitere Informationen zu den vier Sicherheitsstufen, die durch den Dienst zur Kennwortrückschreibung implementiert werden, finden Sie unter "Sicherheitsmodell für das Rückschreiben von Kennwörtern" im Abschnitt "Rückschreiben von Kennwörtern".


<br/> <br/> <br/>

**Zusätzliche Ressourcen**


* [Was ist die Kennwortverwaltung?](active-directory-passwords.md)
* [Funktionsweise der Kennwortverwaltung](active-directory-passwords-how-it-works.md)
* [Erste Schritte mit der Kennwortverwaltung](active-directory-passwords-getting-started.md)
* [Anpassen der Kennwortverwaltung](active-directory-passwords-customize.md)
* [Best Practices für die Kennwortverwaltung](active-directory-passwords-best-practices.md)
* [Operative Einblicke durch Berichte zur Kennwortverwaltung](active-directory-passwords-get-insights.md)
* [Problembehandlung für die Kennwortverwaltung](active-directory-passwords-troubleshoot.md)
* [Weitere Informationen](active-directory-passwords-learn-more.md)
* [Kennwortverwaltung auf MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"

<!---HONumber=Oct15_HO3-->