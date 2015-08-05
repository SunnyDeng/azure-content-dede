<properties 
	pageTitle="Operative Einblicke: Kennwortverwaltung in Azure AD | Microsoft Azure" 
	description="Dieser Artikel beschreibt, wie Sie Berichte verwenden, um einen Einblick in die Vorgänge zur Kennwortverwaltung in Ihrem Unternehmen zu erhalten." 
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
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# Operative Einblicke durch Berichte zur Kennwortverwaltung
In diesem Abschnitt wird beschrieben, wie Sie Berichte zur Kennwortverwaltung in Azure Active Directory verwenden können, um die Verwendung der Kennwortzurücksetzung und -änderung durch Benutzer in Ihrer Organisation zu verfolgen.

- [**Übersicht über Berichte zur Kennwortverwaltung**](#overview-of-password-management-reports)
- [**Anzeigen von Berichten für die Kennwortverwaltung**](#how-to-view-password-management-reports)
- [**Anzeigen der Aktivität zur Registrierung für die Kennwortzurücksetzung in Ihrer Organisation**](#view-password-reset-registration-activity)
- [**Anzeigen der Aktivität zur Kennwortzurücksetzung in Ihrer Organisation**](#view-password-reset-activity)

## Übersicht über Berichte zur Kennwortverwaltung
Sobald Sie die Kennwortzurücksetzung bereitgestellt haben, besteht einer der nächsten Schritte üblicherweise darin, herauszufinden, wie diese in Ihrer Organisation verwendet wird. Beispielsweise möchten Sie einen Einblick gewinnen, wie Benutzer sich für die Kennwortzurücksetzung registrieren oder wie viele Kennwortzurücksetzungen in den letzten paar Tagen stattgefunden haben. Im Folgenden sind einige der häufigsten Fragen aufgeführt, die Sie anhand der Berichte zur Kennwortverwaltung beantworten können, die derzeit im [Azure-Verwaltungsportal](https://manage.windowsazure.com) zur Verfügung stehen:

- Wie viele Personen haben sich für die Kennwortzurücksetzung registriert?
- Wer hat sich für das Zurücksetzen von Kennwörtern registriert?
- Welche Daten registrieren die Benutzer?
- Wie viele Personen haben ihre Kennwörter in den letzten 7 Tagen zurückgesetzt?
- Welche Methoden werden von Benutzern und Administratoren am häufigsten zum Zurücksetzen von Kennwörtern eingesetzt?
- Welche Probleme treten häufig für Benutzer oder Administratoren bei dem Versuch auf, das Kennwort zurückzusetzen?
- Welche Administratoren setzen häufig ihre eigenen Kennwörter zurück?
- Gibt es verdächtige Aktivitäten beim Zurücksetzen des Kennworts?


## Anzeigen von Berichten für die Kennwortverwaltung
Zum Finden der Berichte für die Kennwortverwaltung führen Sie die folgenden Schritte aus:

1.	Klicken Sie im **Azure-Verwaltungsportal** auf die [Active Directory-Erweiterung](https://manage.windowsazure.com).
2.	Wählen Sie Ihr Verzeichnis aus der Liste aus, die im Portal angezeigt wird.
3.	Klicken Sie auf die Registerkarte **Berichte**.
4.	Sehen Sie im Abschnitt **Aktivitätsprotokolle** nach.
5.	Wählen Sie entweder den Bericht **Aktivität "Zurücksetzen des Kennworts"** oder den Bericht **Aktivität "Registrierung für Zurücksetzen des Kennworts"** aus.

    ![][001]

## Anzeigen der Aktivität "Registrierung für Zurücksetzen des Kennworts"

Der Bericht "Aktivität "Registrierung für Zurücksetzen des Kennworts"" zeigt alle Registrierungen für die Kennwortzurücksetzung, die in Ihrer Organisation erfolgt sind. Eine Registrierung für die Kennwortzurücksetzung wird in diesem Bericht für jeden Benutzer angezeigt, der erfolgreich Authentifizierungsinformationen beim Registrierungsportal für die Kennwortzurücksetzung registriert hat ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

- **Max. Zeitraum**: 1 Monat
- **Maximale Anzahl von Zeilen**: unbegrenzt
- **Zum Herunterladen**: Ja, über eine CSV-Datei

    ![][002]

### Beschreibung der Berichtsspalten
In der folgende Liste werden alle Berichtsspalten im Detail beschrieben:

- **Benutzer** – Der Benutzer, der versucht hat, sich für die Kennwortzurücksetzung zu registrieren.
- **Rolle** – Die Rolle des Benutzers im Verzeichnis.
- **Datum und Uhrzeit** – Datum und Uhrzeit des Versuchs.
- **Registrierte Daten** – Die Authentifizierungsdaten, die vom Benutzer während der Registrierung für die Kennwortzurücksetzung bereitgestellt wurden.

### Beschreibung der Berichtswerte
Die folgende Tabelle beschreibt die verschiedenen Werte, die für die einzelnen Spalten zulässig sind:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Spalte</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Zulässige Werte und ihre Bedeutung</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Registrierte Daten</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Alternative E-Mail-Adresse</strong> – Benutzer hat für die Authentifizierung eine alternative E-Mail-Adresse oder eine E-Mail-Adresse zur Authentifizierung verwendet.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Bürotelefon</strong> – Benutzer hat seine Bürotelefonnummer zur Authentifizierung verwendet.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Mobiltelefon</strong> – Benutzer hat sein Mobiltelefon oder sein Telefon für Authentifizierung zum Authentifizieren verwendet.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Sicherheitsfragen</strong> – Benutzer hat Sicherheitsfragen zur Authentifizierung verwendet.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Eine beliebige Kombination der oben genannten (z. B. alternative E-Mail-Adresse und Mobiltelefon)</strong> – Tritt auf, wenn eine Richtlinie für die zweistufige Überprüfung angegeben ist, und zeigt, welche beiden Methoden der Benutzer zur Authentifizierung seiner Anforderung zum Zurücksetzen des Kennworts verwendet hat.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Anzeigen der Aktivität "Zurücksetzen des Kennworts"

Dieser Bericht zeigt alle Versuche der Kennwortzurücksetzung an, die in Ihrer Organisation erfolgt sind.

- **Max. Zeitraum**: 1 Monat
- **Maximale Anzahl von Zeilen**: unbegrenzt
- **Zum Herunterladen**: Ja, über eine CSV-Datei

    ![][003]

### Beschreibung der Berichtsspalten
In der folgende Liste werden alle Berichtsspalten im Detail beschrieben:

1. **Benutzer** – Der Benutzer, der versucht hat, ein Kennwort zurückzusetzen (basierend auf dem Feld "Benutzer-ID", das bereitgestellt wird, wenn der Benutzer ein Kennwort zurückzusetzen versucht).
2. **Rolle** – Die Rolle des Benutzers im Verzeichnis.
3. **Datum und Uhrzeit** – Datum und Uhrzeit des Versuchs.
4. **Verwendete Methode(n)** – Die Authentifizierungsmethoden, die der Benutzer für diesen Zurücksetzungsvorgang verwendet hat.
5. **Ergebnis** – Das Endergebnis des Vorgangs zum Zurücksetzen des Kennworts.
6. **Details** – Die Einzelheiten dazu, warum die Kennwortzurücksetzung zu dem entsprechenden Wert geführt hat. Enthält auch alle Maßnahmen, die Sie ergreifen können, um einen unerwarteten Fehler zu beheben.

### Beschreibung der Berichtswerte
Die folgende Tabelle beschreibt die verschiedenen Werte, die für die einzelnen Spalten zulässig sind:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Spalte</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Zulässige Werte und ihre Bedeutung</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Verwendete Methoden</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Alternative E-Mail-Adresse</strong> – Benutzer hat für die Authentifizierung eine alternative E-Mail-Adresse oder eine E-Mail-Adresse zur Authentifizierung verwendet.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Bürotelefon</strong> – Benutzer hat seine Bürotelefonnummer zur Authentifizierung verwendet.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Mobiltelefon</strong> – Benutzer hat sein Mobiltelefon oder sein Telefon für Authentifizierung zum Authentifizieren verwendet.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Sicherheitsfragen</strong> – Benutzer hat Sicherheitsfragen zur Authentifizierung verwendet.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Eine beliebige Kombination der oben genannten (z. B. alternative E-Mail-Adresse und Mobiltelefon)</strong> – Tritt auf, wenn eine Richtlinie für die zweistufige Überprüfung angegeben ist, und zeigt, welche beiden Methoden der Benutzer zur Authentifizierung seiner Anforderung zum Zurücksetzen des Kennworts verwendet hat.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Ergebnis</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Vorzeitig beendet</strong> – Der Benutzer hat die Kennwortzurücksetzung gestartet, den Vorgang jedoch mittendrin beendet und nicht abgeschlossen.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Blockiert</strong> – Das Konto des Benutzers wurde an der Kennwortzurücksetzung gehindert, weil die Seite zur Kennwortzurücksetzung oder eine einzige Überprüfungsmethode zur Kennwortzurücksetzung in einem Zeitraum von 24 Stunden zu häufig verwendet wurde.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Abgebrochen</strong> – Der Benutzer hat die Kennwortzurücksetzung gestartet, aber dann auf die Schaltfläche "Abbrechen" geklickt, um die Sitzung mittendrin abzubrechen. <br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Administrator kontaktiert</strong> – Beim Benutzer ist während der Sitzung ein Problem aufgetreten, das er nicht auflösen konnte. Daher hat der Benutzer auf den Link "Wenden Sie sich an Ihren Administrator" geklickt, statt die Kennwortzurücksetzung abzuschließen.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Fehler</strong> – Der Benutzer konnte ein Kennwort nicht zurücksetzen, wahrscheinlich weil der Benutzer nicht für die Verwendung dieses Features konfiguriert wurde (z. B. keine Lizenz, fehlende Informationen für die Authentifizierung, Kennwort lokal verwaltet ohne Aktivierung der Rückschreibungsfunktion).<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Erfolgreich</strong> – Kennwortzurücksetzung war erfolgreich.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Details</p>
              </td>
              <td>
                <p>Beachten Sie die folgende Tabelle.</p>
              </td>
            </tr>
          </tbody></table>

### Zulässige Werte für die Spalte "Details"
Nachfolgend finden Sie die Liste der Ergebnistypen, die Sie im Bericht zur Aktivität "Zurücksetzen des Kennworts" erwarten können:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Details</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Ergebnistyp</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Abschluss der Überprüfung per E-Mail vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Abschluss der Überprüfung per Mobiltelefon-SMS vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Abschluss der Überprüfung per Mobiltelefon-Sprachanruf vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Abschluss der Überprüfung per Bürotelefon-Sprachanruf vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Abschluss der Überprüfung per Sicherheitsfragen vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Eingabe der Benutzer-ID vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach dem Start der Überprüfung per E-Mail vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach dem Start der Überprüfung per Mobiltelefon-SMS vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach dem Start der Überprüfung per Mobiltelefon-Sprachanruf vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach dem Start der Überprüfung per Bürotelefon-Sprachanruf vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach dem Start der Überprüfung per Sicherheitsfragen vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat vor Auswahl eines neuen Kennworts vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat während Auswahl eines neuen Kennworts vorzeitig beendet.</p>
              </td>
              <td>
                <p>Vorzeitig beendet</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat zu viele ungültige E-Mail-Überprüfungscodes eingegeben und ist für 24 Stunden blockiert.</p>
              </td>
              <td>
                <p>Blockiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat zu viele ungültige SMS-Überprüfungscodes eingegeben und ist für 24 Stunden blockiert.</p>
              </td>
              <td>
                <p>Blockiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat die Überprüfung per Mobiltelefon-Sprachanruf zu oft versucht und ist für 24 Stunden blockiert.</p>
              </td>
              <td>
                <p>Blockiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat die Überprüfung per Bürotelefon-Sprachanruf zu oft versucht und ist für 24 Stunden blockiert.</p>
              </td>
              <td>
                <p>Blockiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat zu häufig versucht, die Sicherheitsfragen zu beantworten, und ist für 24 Stunden blockiert.</p>
              </td>
              <td>
                <p>Blockiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat zu oft versucht, eine Telefonnummer zu überprüfen, und ist für 24 Stunden blockiert.</p>
              </td>
              <td>
                <p>Blockiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat vor Übergabe der erforderlichen Authentifizierungsmethoden abgebrochen.</p>
              </td>
              <td>
                <p>Abgebrochen</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat vor Übermittlung eines neuen Kennworts abgebrochen.</p>
              </td>
              <td>
                <p>Abgebrochen</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Versuch der Überprüfung per E-Mail den Administrator kontaktiert.</p>
              </td>
              <td>
                <p>Administrator kontaktiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Versuch der Überprüfung per Mobiltelefon-SMS den Administrator kontaktiert.</p>
              </td>
              <td>
                <p>Administrator kontaktiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Versuch der Überprüfung per Mobiltelefon-Sprachanruf den Administrator kontaktiert.</p>
              </td>
              <td>
                <p>Administrator kontaktiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Versuch der Überprüfung per Bürotelefon-Sprachanruf den Administrator kontaktiert.</p>
              </td>
              <td>
                <p>Administrator kontaktiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat nach Versuch der Überprüfung per Sicherheitsfrage den Administrator kontaktiert.</p>
              </td>
              <td>
                <p>Administrator kontaktiert</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Die Kennwortzurücksetzung ist für diesen Benutzer nicht aktiviert. Aktivieren Sie die Kennwortzurücksetzung auf der Konfigurationsregisterkarte, um dieses Problem zu beheben.</p>
              </td>
              <td>
                <p>Fehler</p>
              </td>
            </tr>
            <tr>
              <td>
                <p> Der Benutzer hat keine Lizenz. Sie können dem Benutzer eine Lizenz hinzufügen, um dieses Problems zu beheben. </p>
              </td>
              <td>
                <p>Fehler</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat versucht, die Zurücksetzung von einem Gerät ohne aktivierte Cookies durchzuführen.</p>
              </td>
              <td>
                <p>Fehler</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Im Benutzerkonto sind nicht genügend Authentifizierungsmethoden definiert. Fügen Sie Authentifizierungsinformationen hinzu, um dieses Problem zu beheben.</p>
              </td>
              <td>
                <p>Fehler</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Das Kennwort des Benutzers wird lokal verwaltet. Sie können die Kennwortrückschreibung aktivieren, um dieses Problem zu beheben.</p>
              </td>
              <td>
                <p>Fehler</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Wir konnten den Dienst zum Zurücksetzen Ihres lokalen Kennworts nicht erreichen. Überprüfen Sie das Ereignisprotokoll des Synchronisierungscomputers.</p>
              </td>
              <td>
                <p>Fehler</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Beim Zurücksetzen des lokalen Kennworts des Benutzers ist ein Problem aufgetreten. Überprüfen Sie das Ereignisprotokoll des Synchronisierungscomputers.</p>
              </td>
              <td>
                <p>Fehler</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Der Benutzer ist kein Mitglied der Benutzergruppe für Kennwortzurücksetzung. Fügen Sie diesen Benutzer dieser Gruppe hinzu, um dieses Problem zu beheben.</p>
              </td>
              <td>
                <p>Fehler</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Die Kennwortzurücksetzung wurde für diesen Mandanten vollständig deaktiviert. Informationen zum Beheben dieses Problems finden Sie unter <a href="http://aka.ms/ssprtroubleshoot">http://aka.ms/ssprtroubleshoot</a>.</p>
              </td>
              <td>
                <p>Fehler</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer hat das Kennwort erfolgreich zurückgesetzt.</p>
              </td>
              <td>
                <p>Erfolgreich</p>
              </td>
            </tr>
          </tbody></table>

<br/> <br/> <br/>

**Zusätzliche Ressourcen**


* [Was ist die Kennwortverwaltung?](active-directory-passwords.md)
* [Funktionsweise der Kennwortverwaltung](active-directory-passwords-how-it-works.md)
* [Erste Schritte mit der Kennwortverwaltung](active-directory-passwords-getting-started.md)
* [Anpassen der Kennwortverwaltung](active-directory-passwords-customize.md)
* [Best Practices für die Kennwortverwaltung](active-directory-passwords-best-practices.md)
* [Häufig gestellte Fragen zur Kennwortverwaltung](active-directory-passwords-faq.md)
* [Problembehandlung für die Kennwortverwaltung](active-directory-passwords-troubleshoot.md)
* [Weitere Informationen](active-directory-passwords-learn-more.md)
* [Kennwortverwaltung auf MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"
 

<!---HONumber=July15_HO4-->