<properties 
	pageTitle="Problembehandlung: Azure AD-Kennwortverwaltung | Microsoft Azure" 
	description="Allgemeine Problembehandlungsschritte für die Azure AD-Kennwortverwaltung, einschließlich Zurücksetzen, Ändern und Zurückschreiben von Kennwörtern und dem Registrieren von Informationen. Außerdem erfahren Sie, welche Informationen Sie zum Anfordern von Hilfe benötigen." 
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

# Problembehandlung für die Kennwortverwaltung
Wir helfen Ihnen, wenn Sie Probleme bei der Kennwortverwaltung haben. Die meisten Probleme, auf die Sie eventuell stoßen, können mithilfe weniger Problembehandlungsschritte gelöst werden:

* [**Erforderliche Informationen beim Anfordern von Hilfe**](#information-to-include-when-you-need-help)
* [**Probleme mit der Konfiguration der Kennwortverwaltung im Azure-Verwaltungsportal**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Probleme mit Berichten zur Kennwortverwaltung im Azure-Verwaltungsportal**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Probleme mit dem Registrierungsportal für die Kennwortzurücksetzung**](#troubleshoot-the-password-reset-registration-portal)
* [**Probleme mit dem Kennwortzurücksetzungsportal**](#troubleshoot-the-password-reset-portal)
* [**Probleme bei der Kennwortrückschreibung**](#troubleshoot-password-writeback)
  - [Ereignisprotokoll-Fehlercodes für die Kennwortrückschreibung](#password-writeback-event-log-error-codes)
  - [Konnektivitätsprobleme bei der Kennwortrückschreibung](#troubleshoot-password-writeback-connectivity)

Wenn Sie die nachstehenden Schritte zur Problembehandlung ausgeführt haben und weiterhin Fehler auftreten, können Sie eine Frage im [Azure AD-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) stellen oder sich an den Support wenden. Wir bemühen uns, möglichst schnell eine Lösung für Ihr Problem zu finden.

## Erforderliche Informationen beim Anfordern von Hilfe

Wenn Ihr Problem mithilfe der folgenden Anleitungen nicht gelöst wird, können Sie sich an einen unserer Supportmitarbeiter wenden. Hierbei sollten Sie die folgenden Informationen bereithalten:

 - **Allgemeine Beschreibung des Fehlers** – Welche Fehlermeldung wird dem Benutzer angezeigt? Wenn keine Fehlermeldung angezeigt wird, beschreiben Sie das beobachtete unerwartete Verhalten so genau wie möglich.
 - **Seite** – Auf welcher Seite haben Sie sich befunden, als der Fehler aufgetreten ist (einschließlich URL)?
 - **Datum / Uhrzeit / Zeitzone** – An welchem Tag und zu welcher Uhrzeit genau haben Sie den Fehler beobachtet (einschließlich Zeitzone)?  

 - **Unterstützungscode** – Welcher Unterstützungscode wurde generiert, als der Fehler aufgetreten ist? Um den Unterstützungscode zu ermitteln, reproduzieren Sie den Fehler und klicken im unteren Bildschirmbereich auf den Link "Unterstützungscode". Senden Sie die Ergebnis-GUID anschließend an den Supportmitarbeiter. 
   - Wenn Sie sich auf einer Seite ohne Unterstützungscode befinden, drücken Sie F12, und suchen Sie nach SID und CID. Senden Sie beide Ergebnisse an den Supportmitarbeiter.

    ![][001]

 - **Benutzer-ID** – Wie lautet die ID des Benutzers, der den Fehler beobachtet hat (z. B. user@contoso.com)?)?
 - **Informationen zum Benutzer** – Handelt es sich um einen Verbundbenutzer, um einen Benutzer mit Kennworthashsynchronisierung, oder um einen Benutzer, der ausschließlich über die Cloud zugreift? Ist dem Benutzer eine AAD Premium- oder eine AAD Basic-Lizenz zugewiesen?
 - **Anwendungsereignisprotokoll** – Wenn Sie die Kennwortrückschreibung verwenden und der Fehler in Ihrer lokalen Infrastruktur auftritt, zippen Sie eine Kopie des Anwendungsereignisprotokolls von Ihrem Azure AD Connect-Server, und fügen Sie die ZIP-Datei an Ihre Anfrage an.

Diese Informationen helfen uns dabei, Ihr Problem so schnell wie möglich zu beheben.


## Problembehandlung: Konfiguration der Kennwortverwaltung im Azure-Verwaltungsportal
Wenn beim Konfigurieren der Kennwortzurücksetzung ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fehlerbeschreibung</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welchen Fehler beobachtet der Benutzer?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Mir wird der Abschnitt <strong>Richtlinie zum Zurücksetzen des Benutzerkennworts</strong> auf der Registerkarte <strong>Konfigurieren</strong> im Azure-Verwaltungsportal wird nicht angezeigt.</p>
            </td>
            <td>
              <p>Der Abschnitt <strong>Richtlinie zum Zurücksetzen des Benutzerkennworts</strong> auf der Registerkarte <strong>Konfigurieren</strong> im Azure-Verwaltungsportal ist nicht sichtbar.</p>
            </td>
            <td>
              <p>Dieser Fehler kann auftreten, wenn dem Administrator, der den Vorgang ausführt, keine AAD Premium- oder AAD Basic-Lizenz zugewiesen wurde. </p>
              <p>Weisen Sie dem betreffenden Administratorkonto zur Problembeseitigung eine AAD Premium- oder AAD Basic-Lizenz zu, indem Sie auf die Registerkarte <strong>Lizenzen</strong> navigieren. Versuchen Sie es anschließend erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Ich sehe keine der Konfigurationsoptionen im Abschnitt <strong>Richtlinie zum Zurücksetzen des Benutzerkennworts</strong>, die in der Dokumentation beschrieben werden.</p>
            </td>
            <td>
              <p>Der Abschnitt <strong>Richtlinie zum Zurücksetzen des Benutzerkontos</strong> ist sichtbar, aber die einzige Option, die hier angezeigt wird, lautet <strong>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</strong>.</p>
            </td>
            <td>
              <p>Die verbleibenden Optionen werden angezeigt, wenn Sie das Umschaltfeld <strong>Benutzer, für die das Zurücksetzen des Kennworts aktiviert sind</strong> auf <strong>Ja</strong> festlegen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Eine bestimmte Konfigurationsoption wird mir nicht angezeigt.</p>
            </td>
            <td>
              <p>Beispielsweise wird mir nicht die Option <strong>Anzahl der Tage, bevor Benutzer ihre Kontaktdaten bestätigen müssen</strong> angezeigt, wenn ich durch den Abschnitt <strong>Richtlinie zum Zurücksetzen des Benutzerkontos</strong> (oder andere Beispiele für dasselbe Problem).</p>
            </td>
            <td>
              <p>Viele Elemente der Benutzeroberfläche werden erst angezeigt, wenn sie benötigt werden. Versuchen Sie, alle Optionen auf der Seite zu aktivieren, um sie anzuzeigen.</p>
              <p>Weitere Informationen zu allen verfügbaren Steuerelementen finden Sie unter <a href="../active-directory-passwords-customize#password-management-behavior">Anpassen der Richtlinie zum Zurücksetzen von Benutzerkennwörtern</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Die Konfigurationsoption <strong>Kennwörter in lokales Active Directory zurückschreiben</strong> wird mir nicht angezeigt.</p>
            </td>
            <td>
              <p>Die Option <strong>Kennwörter in lokales Active Directory zurückschreiben</strong> ist auf der Registerkarte <strong>Konfigurieren</strong> im Azure-Verwaltungsportal nicht sichtbar.</p>
            </td>
            <td>
              <p>Diese Option wird nur angezeigt, wenn Sie Azure AD Connect heruntergeladen und die Kennwortrückschreibung konfiguriert haben. Nachdem Sie dies getan haben, wird die Option angezeigt, und Sie können das Zurückschreiben von Kennwörtern aus der Cloud aktivieren oder deaktivieren.</p>
              <p>Weitere Informationen zur Vorgehensweise finden Sie unter <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Aktivieren/Deaktivieren der Kennwortrückschreibung</a>.</p>
            </td>
          </tr>
        </tbody></table>

## Problembehandlung: Berichte zur Kennwortverwaltung im Azure-Verwaltungsportal
Wenn beim Konfigurieren der Berichte für die Kennwortverwaltung ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fehlerbeschreibung</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welchen Fehler beobachtet der Benutzer?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Ich sehe keine Berichte zur Kennwortverwaltung.</p>
            </td>
            <td>
              <p>Die Berichte <strong>Aktivität "Kennwort zurücksetzen"</strong> und <strong>Registrierung für Zurücksetzen des Kennworts</strong> werden nicht im Abschnitt <strong>Aktivitätsprotokoll</strong> auf der Registerkarte <strong>Berichte</strong> angezeigt.</p>
            </td>
            <td>
              <p>Dieser Fehler kann auftreten, wenn dem Administrator, der den Vorgang ausführt, keine AAD Premium- oder AAD Basic-Lizenz zugewiesen wurde. </p>
              <p>Weisen Sie dem betreffenden Administratorkonto zur Problembeseitigung eine AAD Premium- oder AAD Basic-Lizenz zu, indem Sie auf die Registerkarte <strong>Lizenzen</strong> navigieren. Versuchen Sie es anschließend erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Benutzerregistrierungen werden mehrfach angezeigt.</p>
            </td>
            <td>
              <p>Wenn ein Benutzer eine alternative E-Mail-Adresse, ein Mobiltelefon und Sicherheitsfragen registriert, werden diese Optionen nicht als eine Zeile, sondern in separaten Zeilen angezeigt.</p>
            </td>
            <td>
              <p>Wenn sich ein Benutzer registriert, kann nicht davon ausgegangen werden, dass für alle Optionen auf der Registrierungsseite Daten konfiguriert werden. Deshalb werden alle Daten, die separat registriert werden, getrennt protokolliert.</p>
              <p>Wenn Sie diese Daten aggregieren möchten, können Sie den Bericht herunterladen und die Daten als Pivottabelle in Excel öffnen, um flexibler zu sein.</p>
            </td>
          </tr>
        </tbody></table>

## Problembehandlung: Registrierungsportal für die Kennwortzurücksetzung
Wenn beim Registrieren eines Benutzers für die Kennwortzurücksetzung ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fehlerbeschreibung</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welchen Fehler beobachtet der Benutzer?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Das Verzeichnis ist nicht für die Kennwortzurücksetzung aktiviert.</p>
            </td>
            <td>
              <p>Ihr Administrator hat die Verwendung dieser Funktion nicht für Sie aktiviert.</p>
            </td>
            <td>
              <p>Legen Sie im Azure-Verwaltungsportal auf der Registerkarte für die Verzeichniskonfiguration die Option <strong>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</strong> auf <strong>Ja</strong> fest, und klicken Sie auf <strong>Speichern</strong>. Dem Administrator, der diesen Vorgang ausführt, muss eine Azure AD Premium- oder Basic-Lizenz zugewiesen sein.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Dem Benutzer ist keine AAD Premium- oder AAD Basic-Lizenz zugewiesen.</p>
            </td>
            <td>
              <p>Ihr Administrator hat die Verwendung dieser Funktion nicht für Sie aktiviert.</p>
            </td>
            <td>
              <p>Weisen Sie dem Benutzer auf der Registerkarte <strong>Lizenzen</strong> im Azure-Verwaltungsportal eine Azure AD Premium- oder Azure AD Basic-Lizenz zu. Dem Administrator, der diesen Vorgang ausführt, muss eine Azure AD Premium- oder Basic-Lizenz zugewiesen sein.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Fehler beim Verarbeiten der Anforderung.</p>
            </td>
            <td>
              <p>Benutzern wird eine Fehlermeldung mit dem folgenden Text angezeigt:</p>
              <p>
                
              </p>
              <p>Fehler beim Verarbeiten der Anforderung. </p>
              <p>Diese Fehlermeldung wird beim Zurücksetzen eines Kennworts angezeigt.</p>
            </td>
            <td>
              <p>Dieser Fehler kann verschiedene Ursachen haben, im Allgemeinen wird dieser Fehler jedoch entweder durch einen Dienstausfall oder einen nicht behebbaren Konfigurationsfehler verursacht. </p>
              <p>Wenn Ihnen dieser Fehler angezeigt wird und dies Auswirkungen auf Ihre Geschäftstätigkeit hat, wenden Sie sich an den Support, damit wir umgehend Hilfe leisten können. Lesen Sie die Informationen im Abschnitt <a href="#information-to-include-when-you-need-help">Erforderliche Informationen beim Anfordern von Hilfe</a>. Dort wird beschrieben, welche Informationen Sie für den Supportmitarbeiter bereithalten sollen, um eine schnelle Problemlösung zu ermöglichen.</p>
            </td>
          </tr>
        </tbody></table>

## Problembehandlung: Kennwortzurücksetzungsportal
Wenn beim Zurücksetzen eines Kennworts für einen Benutzer ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fehlerbeschreibung</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welchen Fehler beobachtet der Benutzer?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Das Verzeichnis ist nicht für die Kennwortzurücksetzung aktiviert.</p>
            </td>
            <td>
              <p>Ihr Konto ist nicht für die Kennwortzurücksetzung aktiviert.</p>
              <p>Ihr Administrator hat Ihr Konto nicht zur Verwendung mit diesem Dienst eingerichtet. </p>
              <p>
                
              </p>
              <p>Wenn Sie möchten, können wir einen Administrator in Ihrer Organisation kontaktieren, um Ihr Kennwort für Sie zurückzusetzen.</p>
            </td>
            <td>
              <p>Legen Sie im Azure-Verwaltungsportal auf der Registerkarte für die Verzeichniskonfiguration die Option <strong>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</strong> auf <strong>Ja</strong> fest, und klicken Sie auf <strong>Speichern</strong>. Dem Administrator, der diesen Vorgang ausführt, muss eine Azure AD Premium- oder Basic-Lizenz zugewiesen sein.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Dem Benutzer ist keine AAD Premium- oder AAD Basic-Lizenz zugewiesen.</p>
            </td>
            <td>
              <p>Wir können Kennwörter für Nicht-Administratorkonten nicht automatisch zurücksetzen, aber wir können einen Administrator kontaktieren, der diese Zurücksetzung für Sie vornimmt.</p>
            </td>
            <td>
              <p>Weisen Sie dem Benutzer auf der Registerkarte <strong>Lizenzen</strong> im Azure-Verwaltungsportal eine Azure AD Premium- oder Azure AD Basic-Lizenz zu. Dem Administrator, der diesen Vorgang ausführt, muss eine Azure AD Premium- oder Basic-Lizenz zugewiesen sein.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Das Verzeichnis ist für die Kennwortzurücksetzung aktiviert, aber für den Benutzer wurden keine Authentifizierungsinformationen konfiguriert oder diese sind fehlerhaft.</p>
            </td>
            <td>
              <p>Ihr Konto ist nicht für die Kennwortzurücksetzung aktiviert.</p>
              <p>Ihr Administrator hat Ihr Konto nicht zur Verwendung mit diesem Dienst eingerichtet. </p>
              <p>
                
              </p>
              <p>Wenn Sie möchten, können wir einen Administrator in Ihrer Organisation kontaktieren, um Ihr Kennwort für Sie zurückzusetzen.</p>
            </td>
            <td>
              <p>Stellen Sie sicher, dass für den Benutzer ordnungsgemäß formatierte Kontaktdaten im Verzeichnis vorliegen, bevor Sie fortfahren. Im Abschnitt <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Welche Daten werden bei der Kennwortzurücksetzung verwendet?</a> finden Sie Informationen dazu, wie Authentifizierungsinformationen im Verzeichnis konfiguriert werden, damit Benutzern dieser Fehler nicht angezeigt wird.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Das Verzeichnis ist für die Kennwortzurücksetzung aktiviert, der Benutzer hat jedoch nur eine Information in Form registrierter Kontaktdaten konfiguriert, während die konfigurierte Richtlinie eine Überprüfung in zwei Schritten erfordert. </p>
            </td>
            <td>
              <p>Ihr Konto ist nicht für die Kennwortzurücksetzung aktiviert.</p>
              <p>Ihr Administrator hat Ihr Konto nicht zur Verwendung mit diesem Dienst eingerichtet. </p>
              <p>
                
              </p>
              <p>Wenn Sie möchten, können wir einen Administrator in Ihrer Organisation kontaktieren, um Ihr Kennwort für Sie zurückzusetzen.</p>
            </td>
            <td>
              <p>Stellen Sie sicher, dass für den Benutzer mindestens zwei ordnungsgemäß konfigurierte Kontaktmethoden vorliegen (z.&#160;B. Mobiltelefon und Bürotelefon), bevor Sie fortfahren. Im Abschnitt <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Welche Daten werden bei der Kennwortzurücksetzung verwendet?</a> finden Sie Informationen dazu, wie Authentifizierungsinformationen im Verzeichnis konfiguriert werden, damit Benutzern dieser Fehler nicht angezeigt wird.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Das Verzeichnis ist für die Kennwortzurücksetzung aktiviert, und der Benutzer ist ordnungsgemäß konfiguriert, aber der Benutzer kann nicht kontaktiert werden. </p>
            </td>
            <td>
              <p>Leider ist bei der Kontaktaufnahme mit Ihnen ein unerwarteter Fehler aufgetreten.</p>
            </td>
            <td>
              <p>Dieses Verhalten kann durch einen temporären Dienstfehler oder falsch konfigurierte Kontaktdaten verursacht werden, die nicht ordnungsgemäß erkannt wurden. Wenn der Benutzer 10&#160;Sekunden wartet, werden ein Link "Erneut versuchen" und ein Link "Kontaktieren Sie Ihren Administrator" angezeigt. Durch Klicken auf "Erneut versuchen" erfolgt ein neuer Anruf, während beim Klicken auf "Kontaktieren Sie Ihren Administrator" eine formale E-Mail an Benutzer-, Kennwort- oder globale Administratoren gesendet wird (in dieser Reihenfolge), in der eine Kennwortzurücksetzung für dieses Benutzerkonto angefordert wird.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Benutzer erhalten die SMS oder den Telefonanruf für die Kennwortzurücksetzung nicht.</p>
            </td>
            <td>
              <p>Der Benutzer klickt auf "SMS schicken" oder "Anrufen", aber anschließend geschieht nichts.</p>
            </td>
            <td>
              <p>Dieser Fehler kann durch eine falsch formatierte Telefonnummer im Verzeichnis verursacht werden. Stellen Sie sicher, dass die Telefonnummer das Format "+ccc xxxyyyzzzzXeeee" aufweist. Weitere Informationen zur Formatierung von Telefonnummern für die Kennwortzurücksetzung finden Sie unter <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Welche Daten werden bei der Kennwortzurücksetzung verwendet?</a>.</p>
              <p>Beachten Sie, dass Anrufe zur Kennwortzurücksetzung nicht an Durchwahlnummern weitergeleitet werden können, selbst wenn diese im Verzeichnis angegeben werden (die Durchwahlnummern werden vor Absetzen des Anrufs abgeschnitten). Verwenden Sie eine Nummer ohne Durchwahl, oder integrieren Sie die Durchwahl in die Telefonnummer in Ihrer Nebenstellenanlage.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Der Benutzer erhält keine E-Mails für die Kennwortzurücksetzung.</p>
            </td>
            <td>
              <p>Der Benutzer klickt auf "E-Mail an mich senden", aber anschließend geschieht nichts.</p>
            </td>
            <td>
              <p>Die häufigste Ursache für diesen Fehler ist der, dass die Nachricht durch einen Spamfilter zurückgewiesen wird. Prüfen Sie Ihren Spam- oder Junk-E-Mail-Ordner oder den Ordner "Gelöschte Elemente" auf die E-Mail.</p>
              <p>Stellen Sie außerdem sicher, dass Sie die Nachricht im richtigen Posteingang suchen. Viele Benutzer verfügen über ähnliche E-Mail-Adressen und prüfen das falsche Postfach auf die Nachricht. Wenn das Problem durch keine dieser Optionen gelöst werden kann, ist die E-Mail-Adresse im Verzeichnis möglicherweise falsch formatiert. Prüfen Sie die E-Mail-Adresse auf ihre Richtigkeit, und versuchen Sie es erneut. Weitere Informationen zur Formatierung von E-Mail-Adressen für die Kennwortzurücksetzung finden Sie unter <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Welche Daten werden bei der Kennwortzurücksetzung verwendet?</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Ich habe eine Richtlinie für die Kennwortzurücksetzung eingerichtet, aber wenn ein Administrator die Kennwortzurücksetzung verwendet, wird diese Richtlinie nicht angewendet.</p>
            </td>
            <td>
              <p>Beim Zurücksetzen von Kennwörtern für Administratorkonten werden unabhängig davon, welche Richtlinie im Abschnitt <strong>Richtlinie zum Zurücksetzen des Benutzerkennworts</strong> auf der Registerkarte <strong>Konfigurieren</strong> eingerichtet wurde, dieselben Optionen für die Kennwortzurücksetzung angezeigt – E-Mail-Adresse und Mobiltelefon.</p>
            </td>
            <td>
              <p>Die im Abschnitt <strong>Richtlinie zum Zurücksetzen des Benutzerkennworts</strong> auf der Registerkarte <strong>Konfigurieren</strong> konfigurierten Optionen gelten nur für Endbenutzer in Ihrer Organisation.</p>
              <p>Microsoft verwaltet und kontrolliert die Richtlinie für das Zurücksetzen von Administratorkennwörtern, um höchste Sicherheit zu gewährleisten.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Benutzer werden daran gehindert, mehrmals am Tag ihr Kennwort zurückzusetzen.</p>
            </td>
            <td>
              <p>Benutzern wird eine Fehlermeldung mit dem folgenden Text angezeigt:</p>
              <p>
                
              </p>
              <p>Verwenden Sie eine andere Option.</p>
              <p>Es wurden zu viele Versuche unternommen, die Kontoüberprüfung auszuführen (in der letzten Stunde). Aus Sicherheitsgründen müssen Sie 24&#160;Stunden warten, bevor Sie es erneut versuchen können. </p>
              <p>Wenn Sie möchten, können wir einen Administrator in Ihrer Organisation kontaktieren, um Ihr Kennwort für Sie zurückzusetzen.</p>
            </td>
            <td>
              <p>Microsoft implementiert einen automatischen Drosselungsmechanismus, um Benutzer daran zu hindern, ihre Kennwörter innerhalb eines kurzen Zeitraums zu häufig zurückzusetzen. Dieser Mechanismus greift in den folgenden Fällen:</p>
              <ol class="ordered">
                <li>
										Der Benutzer versucht, innerhalb von einer Stunde fünfmal, eine Telefonnummer zu überprüfen.&lt;br>&lt;br></li>
                <li>
										Der Benutzer versucht innerhalb von einer Stunde fünfmal, die Überprüfung mithilfe von Sicherheitsfragen zu verwenden.&lt;br>&lt;br></li>
                <li>
										Der Benutzer versucht innerhalb von einer Stunde fünfmal, ein Kennwort für dasselbe Benutzerkonto zurückzusetzen.&lt;br>&lt;br></li>
              </ol>
              <p>Weisen Sie den Benutzer zur Problembehandlung an, 24&#160;Stunden abzuwarten, bevor der nächste Versuch unternommen wird. Nach Ablauf dieses Zeitraums kann der Benutzer sein Kennwort wieder zurücksetzen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Der Benutzer erhält bei der Überprüfung seiner Telefonnummer eine Fehlermeldung.</p>
            </td>
            <td>
              <p>Beim Versuch, eine Telefonnummer als Authentifizierungsmethode zu verwenden, wird dem Benutzer die folgende Fehlermeldung angezeigt:</p>
              <p>
                
              </p>
              <p>Es wurde eine falsche Telefonnummer angegeben.</p>
            </td>
            <td>
              <p>Dieser Fehler tritt auf, wenn die eingegebene Telefonnummer nicht mit der registrierten Telefonnummer übereinstimmt.</p>
              <p>Stellen Sie sicher, dass der Benutzer beim Versuch, eine Telefonnummer für die Kennwortzurücksetzung zu verwenden, die vollständige Telefonnummer eingibt – einschließlich Landes- und Ortskennzahl.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Fehler beim Verarbeiten der Anforderung.</p>
            </td>
            <td>
              <p>Benutzern wird eine Fehlermeldung mit dem folgenden Text angezeigt:</p>
              <p>
                
              </p>
              <p>Fehler beim Verarbeiten der Anforderung. </p>
              <p>Diese Fehlermeldung wird beim Zurücksetzen eines Kennworts angezeigt.</p>
            </td>
            <td>
              <p>Dieser Fehler kann verschiedene Ursachen haben, im Allgemeinen wird dieser Fehler jedoch entweder durch einen Dienstausfall oder einen nicht behebbaren Konfigurationsfehler verursacht. </p>
              <p>Wenn Ihnen dieser Fehler angezeigt wird und dies Auswirkungen auf Ihre Geschäftstätigkeit hat, wenden Sie sich an den Support, damit wir umgehend Hilfe leisten können. Lesen Sie die Informationen im Abschnitt <a href="#information-to-include-when-you-need-help">Erforderliche Informationen beim Anfordern von Hilfe</a>. Dort wird beschrieben, welche Informationen Sie für den Supportmitarbeiter bereithalten sollen, um eine schnelle Problemlösung zu ermöglichen.</p>
            </td>
          </tr>
        </tbody></table>

## Problembehandlung: Kennwortrückschreibung
Wenn beim Aktivieren, Deaktivieren oder Verwenden der Kennwortrückschreibung ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fehlerbeschreibung</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welchen Fehler beobachtet der Benutzer?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Allgemeine Fehler bei Integration und Start.</p>
            </td>
            <td>
              <p>Der Dienst zum Zurücksetzen von Kennwörtern wird lokal nicht gestartet, im Anwendungsereignisprotokoll für den Azure AD Connect-Computer wird Fehler&#160;6800 protokolliert.</p>
              <p>
                
              </p>
              <p>Nach der Integration können Verbundbenutzer oder Benutzer mit Kennworthashsynchronisierung ihre Kennwörter nicht zurücksetzen.</p>
            </td>
            <td>
              <p>Wenn die Kennwortrückschreibung aktiviert ist, ruft das Synchronisierungsmodul die Bibliothek zur Kennwortrückschreibung auf, um eine Konfiguration (Integration) durchzuführen. Die Kommunikation erfolgt hierbei über den Cloudintegrationsdienst. Fehler, die bei der Integration oder beim Start des WCF-Endpunkts für die Kennwortrückschreibung auftreten, führen zu Fehlern im Ereignisprotokoll auf Ihrem Azure AD Connect-Computer.</p>
              <p>Beim Neustart des ADSync-Diensts wird, sofern die Kennwortrückschreibung konfiguriert wurde, der WCF-Endpunkt gestartet. Wenn jedoch beim Start des Endpunkts ein Fehler auftritt, wird lediglich Fehler&#160;6800 protokolliert, und der Synchronisierungsdienst wird gestartet. Das Vorhandensein dieses Ereignisses bedeutet, dass der Endpunkt für die Kennwortrückschreibung nicht gestartet wurde. Die Ereignisprotokolldetails für dieses Ereignis (6800) geben – gemeinsam mit den von der PasswordResetService-Komponente generierten Ereignisprotokolleinträgen – an, warum der Endpunkt nicht gestartet werden konnte. Überprüfen Sie diese Ereignisprotokollfehler, und versuchen Sie Azure AD Connect neu zu starten, wenn die Kennwortrückschreibung weiterhin nicht funktioniert. Wenn das Problem weiterhin auftritt, versuchen Sie die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Fehler beim Konfigurieren der Kennwortrückschreibung während der Azure AD Connect-Installation.</p>
            </td>
            <td>
              <p>Im letzten Schritt des Azure AD Connect-Installationsvorgangs wird ein Fehler mit dem Hinweis angezeigt, dass die Kennwortrückschreibung nicht konfiguriert werden konnte.</p>
              <p>
                
              </p>
              <p>Das Azure AD Connect-Anwendungsereignisprotokoll enthält Fehler&#160;32009 mit dem Text "Fehler beim Abrufen des Authentifizierungstokens".</p>
            </td>
            <td>
              <p>Dieser Fehler tritt in den folgenden beiden Fällen auf:</p>
              <ul>
                <li class="unordered">
										Sie haben zu Beginn der Azure AD Connect-Installation ein falsches Kennwort für das globale Administratorkonto angegeben.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Sie haben versucht, zu Beginn der Azure AD Connect-Installation einen Verbundbenutzer für das globale Administratorkonto zu verwenden.&lt;br>&lt;br> </li>
              </ul>
              <p>Stellen Sie zur Beseitigung dieses Fehlers sicher, dass Sie zu Beginn der Azure AD Connect-Installation kein Verbundkonto für den globalen Administrator angegeben haben, und dass das angegebene Kennwort richtig ist.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Fehler beim Konfigurieren der Kennwortrückschreibung während der Azure AD Connect-Installation.</p>
            </td>
            <td>
              <p>Das Ereignisprotokoll für den Azure AD Connect-Computer enthält Fehler&#160;32002, ausgelöst durch die PasswordResetService-Komponente.</p>
              <p>
                
              </p>
              <p>Der angezeigte Fehler lautet: "Fehler bei der Verbindungsherstellung mit ServiceBus. Der Tokenanbieter konnte kein Sicherheitstoken bereitstellen...".</p>
              <p>
                
              </p>
            </td>
            <td>
              <p>Die Ursache für diesen Fehler besteht darin, dass der Dienst für die Kennwortzurücksetzung, der in Ihrer lokalen Umgebung ausgeführt wird, keine Verbindung mit dem Service Bus-Endpunkt in der Cloud herstellen kann. Dieser Fehler wird normalerweise durch eine Firewallregel verursacht, die eine ausgehende Verbindung mit einem bestimmten Port oder einer Webadresse blockiert.</p>
              <p>
                
              </p>
              <p>Stellen Sie sicher, dass Ihre Firewall die folgenden ausgehenden Verbindungen zulässt:</p>
              <ul>
                <li class="unordered">
										Sämtlicher Datenverkehr über TCP&#160;443 (HTTPS)&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Ausgehende Verbindungen mit &lt;br>&lt;br></li>
              </ul>
              <p>
                
              </p>
              <p>Nachdem Sie diese Regeln aktualisiert haben, starten Sie den Azure AD Connect-Computer neu. Anschließend sollte die Kennwortrückschreibung wieder funktionieren.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Der lokale Endpunkt für die Kennwortrückschreibung ist nicht erreichbar.</p>
            </td>
            <td>
              <p>Nachdem einige Zeit alles funktioniert hat, können Verbundbenutzer oder Benutzer mit Kennworthashsynchronisierung ihre Kennwörter nicht zurücksetzen.</p>
            </td>
            <td>
              <p>In einigen seltenen Fällen kann der Dienst für die Kennwortrückschreibung möglicherweise nicht neu gestartet werden, wenn Azure AD Connect neu gestartet wurde. Prüfen Sie in diesen Fällen zunächst, ob die lokale Kennwortrückschreibung als aktiviert angezeigt wird. Diese Überprüfung können Sie mit dem Azure AD Connect-Assistenten oder mit PowerShell durchführen (siehe Anleitungen oben). Wenn die Funktion als aktiviert angezeigt wird, deaktivieren und aktivieren Sie sie erneut über die Benutzeroberfläche oder PowerShell. Weitere Informationen zur Vorgehensweise finden Sie unter "Schritt&#160;2: Aktivieren der Kennwortrückschreibung auf dem Computer zur Verzeichnissynchronisierung &amp; Konfigurieren von Firewallregeln" in <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Aktivieren/Deaktivieren der Kennwortrückschreibung</a>.</p>
              <p>
                
              </p>
              <p>Wenn das Problem auf diese Weise nicht gelöst wird, deinstallieren Sie Azure AD Connect vollständig, und installieren Sie es neu.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Berechtigungsfehler</p>
            </td>
            <td>
              <p>Verbundbenutzer oder Benutzer mit Kennworthashsynchronisierung, die versuchen, ihre Kennwörter zurückzusetzen, erhalten nach dem Übertragen des Kennworts eine Fehlermeldung mit dem Hinweis, dass ein Dienstproblem aufgetreten ist.</p>
              <p>
                
              </p>
              <p>Zusätzlich hierzu wird während der Vorgänge zur Kennwortzurücksetzung möglicherweise ein Fehler in den lokalen Ereignisprotokollen angezeigt, dass dem Verwaltungs-Agent der Zugriff verweigert wurde.</p>
            </td>
            <td>
              <p>Wenn Sie diesen Fehler in Ihrem Ereignisprotokoll sehen, stellen Sie sicher, dass das AD MA-Konto (dieses wurde während der Konfiguration im Assistenten angegeben) über die erforderlichen Berechtigungen für die Kennwortrückschreibung verfügt.</p>
              <p>
                
              </p>
              <p>HINWEIS: Nachdem diese Berechtigung erteilt wurde, kann es bis zu 1&#160;Stunde dauern, bis die Berechtigungen über den sdprop-Hintergrundtask auf dem DC angewendet wurden. </p>
              <p>Damit die Kennwortzurücksetzung funktioniert, müssen die Berechtigungen in die Sicherheitsbeschreibung des Benutzerobjekts geschrieben werden, dessen Kennwort zurückgesetzt wird. Bis diese Berechtigungen im Benutzerobjekt angezeigt werden, treten bei der Kennwortzurücksetzung weiterhin Fehler mit einer Zugriffsverweigerung auf.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Fehler beim Konfigurieren der Kennwortrückschreibung über den Azure AD Connect-Konfigurations-Assistenten. </p>
            </td>
            <td>
              <p>Im Assistenten wird beim Aktivieren/Deaktivieren der Kennwortrückschreibung der Fehler "Ermitteln von MA nicht möglich" angezeigt.</p>
            </td>
            <td>
              <p>Dies ist ein bekannter Fehler in der veröffentlichten Version von Azure AD Connect, der in der folgenden Situation auftritt:</p>
              <ol class="ordered">
                <li>
										Sie konfigurieren Azure AD Connect für den Mandanten "abc.com" (überprüfte Domäne) mithilfe von Anmeldeinformationen. Dies führt dazu, dass ein AAD-Connector namens "abc.com – AAD" erstellt wird.&lt;br>&lt;br></li>
                <li>
										Anschließend ändern Sie die AAD-Anmeldeinformationen (mithilfe der alten Benutzeroberfläche) für den Connector (beachten Sie, dass der Mandant derselbe ist, aber der Domänenname abweicht). &lt;br>&lt;br></li>
                <li>
										Jetzt versuchen Sie, die Kennwortrückschreibung zu aktivieren/zu deaktivieren. Der Assistent setzt den Namen des Connectors mithilfe der Anmeldeinformationen "abc.onmicrosoft.com – AAD" zusammen und übergibt diesen an das Cmdlet für die Kennwortrückschreibung. Dies führt zu einem Fehler, da kein Connector mit diesem Namen erstellt wurde.&lt;br>&lt;br></li>
              </ol>
              <p>Dieser Fehler wurde in den aktuellen Builds behoben. Wenn Sie einen älteren Build verwenden, können Sie diesen Fehler umgehen, indem Sie das PowerShell-Cmdlet zum Aktivieren/Deaktivieren des Features verwenden. Weitere Informationen zur Vorgehensweise finden Sie unter "Schritt&#160;2: Aktivieren der Kennwortrückschreibung auf dem Computer zur Verzeichnissynchronisierung &amp; Konfigurieren von Firewallregeln" in <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Aktivieren/Deaktivieren der Kennwortrückschreibung</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Es kann keine Kennwortzurücksetzung für Benutzer in besonderen Gruppen wie z.&#160;B. "Domänen-Admins" / "Unternehmensadministratoren" usw. durchgeführt werden.</p>
            </td>
            <td>
              <p>Verbundbenutzer oder Benutzer mit Kennworthashsynchronisierung, die einer geschützten Gruppe angehören und versuchen, ihre Kennwörter zurückzusetzen, erhalten nach dem Übertragen des Kennworts eine Fehlermeldung mit dem Hinweis, dass ein Dienstproblem aufgetreten ist.</p>
            </td>
            <td>
              <p>Berechtigte Benutzer in Active Directory werden mithilfe von "AdminSDHolder" geschützt. Weitere Informationen finden Sie unter <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a>. </p>
              <p>
                
              </p>
              <p>Dies bedeutet, dass die Sicherheitsbeschreibungen dieser Objekte regelmäßig mit der in "AdminSDHolder" angegebenen Sicherheitsbeschreibung abgeglichen werden. Stimmen die Sicherheitsbeschreibungen nicht überein, werden sie zurückgesetzt. Aus diesem Grund werden die zusätzlichen Berechtigungen, die für die Kennwortrückschreibung benötigt werden, nicht auf solche Benutzer angewendet. Dies kann dazu führen, dass die Kennwortrückschreibung für die Benutzer nicht funktioniert. Die Kennwortverwaltung für Benutzer wird daher in diesen Gruppen nicht unterstützt, da dies dem AD-Sicherheitsmodell widerspricht.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Bei Vorgängen zur Kennwortzurücksetzung wird der Fehler "Objekt nicht gefunden" angezeigt.</p>
            </td>
            <td>
              <p>Verbundbenutzer oder Benutzer mit Kennworthashsynchronisierung, die versuchen, ihre Kennwörter zurückzusetzen, erhalten nach dem Übertragen des Kennworts eine Fehlermeldung mit dem Hinweis, dass ein Dienstproblem aufgetreten ist.</p>
              <p>
                
              </p>
              <p>Darüber hinaus wird im Ereignisprotokoll für den Azure AD Connect-Dienst bei Vorgängen zur Kennwortzurücksetzung möglicherweise der Fehler "Objekt nicht gefunden" angezeigt. </p>
            </td>
            <td>
              <p>Dieser Fehler deutet üblicherweise darauf hin, dass das Synchronisierungsmodul entweder nicht in der Lage ist, das Benutzerobjekt im AAD-Connectorbereich oder dem zugeordneten MV- oder AD-Connectorbereichsobjekt zu ermitteln. </p>
              <p>
                
              </p>
              <p>Stellen Sie zur Fehlerbeseitigung sicher, dass für den Benutzer tatsächlich eine Synchronisierung von der lokalen Umgebung zu AAD über die aktuelle Instanz von Azure AD Connect durchgeführt wird, und untersuchen Sie den Status des Objekts in Connectorbereichen und MV. Bestätigen Sie, dass das AD CS-Objekt über die Regel "Microsoft.InfromADUserAccountEnabled.xxx" mit dem MV-Objekt verbunden ist.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Bei Vorgängen im Rahmen der  Kennwortzurücksetzung tritt der Fehler "Mehrere Übereinstimmungen gefunden" auf.</p>
            </td>
            <td>
              <p>Verbundbenutzer oder Benutzer mit Kennworthashsynchronisierung, die versuchen, ihre Kennwörter zurückzusetzen, erhalten nach dem Übertragen des Kennworts eine Fehlermeldung mit dem Hinweis, dass ein Dienstproblem aufgetreten ist.</p>
              <p>
                
              </p>
              <p>Darüber hinaus wird im Ereignisprotokoll für den Azure AD Connect-Dienst bei Vorgängen zur Kennwortzurücksetzung möglicherweise der Fehler "Mehrere Übereinstimmungen gefunden" angezeigt. </p>
            </td>
            <td>
              <p>Dies deutet darauf hin, dass das Synchronisierungsmodul ermittelt hat, dass das MV-Objekt über die Regel "Microsoft.InfromADUserAccountEnabled.xxx" mit mehr als einem AD CS-Objekt verbunden ist. Dies bedeutet, dass das Benutzerkonto in mehr als einer Gesamtstruktur über ein aktives Konto verfügt. </p>
              <p>
                
              </p>
              <p>Aktuell wird dieses Szenario für die Kennwortrückschreibung nicht unterstützt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Bei Kennwortvorgängen treten Konfigurationsfehler auf.</p>
            </td>
            <td>
              <p>Bei Kennwortvorgängen treten Konfigurationsfehler auf. Das Anwendungsereignisprotokoll enthält Azure AD Connect-Fehler&#160;6329 mit dem folgenden Text: 0x8023061f (Der Vorgang konnte nicht ausgeführt werden, weil auf diesem Verwaltungs-Agent die Kennwortsynchronisierung nicht aktiviert ist.)</p>
            </td>
            <td>
              <p>Dieser Fehler tritt auf, wenn die Azure AD Connect-Konfiguration geändert wird, um eine neue AD-Gesamtstruktur hinzuzufügen (oder um eine vorhandene Gesamtstruktur zu entfernen und erneut hinzuzufügen), <strong>nachdem</strong> die Funktion zur Kennwortrückschreibung bereits aktiviert wurde. Kennwortvorgänge für Benutzer in solchen neu hinzugefügten Gesamtstrukturen werden mit einem Fehler abgebrochen. Um diesen Fehler zu beseitigen, müssen Sie die Funktion zur Kennwortrückschreibung nach Abschluss der Konfigurationsänderungen an der Gesamtstruktur deaktivieren und dann erneut aktivieren.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Das Zurückschreiben von Kennwörtern, die von Benutzern zurückgesetzt wurden, funktioniert einwandfrei, aber beim Zurückschreiben von Kennwörtern, die von einem Benutzer geändert wurden oder durch einen Administrator für einen Benutzer zurückgesetzt wurden, treten Fehler auf.</p>
            </td>
            <td>
              <p>Wenn Sie versuchen, über das Azure-Verwaltungsportal ein Kennwort im Namen eines Benutzers zurückzusetzen, wird eine Meldung mit diesem Text angezeigt: "Der in Ihrer lokalen Umgebung ausgeführte Dienst für die Kennwortzurücksetzung bietet keine Unterstützung für das Zurücksetzen von Benutzerkennwörtern durch Administratoren. Führen Sie ein Upgrade auf die aktuelle Version von Azure AD Connect aus, um dieses Problem zu lösen."</p>
            </td>
            <td>
              <p>Dies geschieht, wenn die Version des Synchronisierungsmoduls keine Unterstützung für den verwendeten Vorgang für die Kennwortrückschreibung bietet. Alle Azure AD Connect-Versionen ab 1.0.0419.0911 bieten Unterstützung für alle Vorgänge im Rahmen der Kennwortverwaltung. Dazu gehören das Zurückschreiben zurückgesetzter Kennwörter, das Zurückschreiben geänderter Kennwörter sowie das durch einen Administrator über das Azure-Verwaltungsportal initiierte Zurückschreiben zurückgesetzter Kennwörter.&#160; DirSync-Versionen ab 1.0.6862 bieten nur Unterstützung für das Zurückschreiben zurückgesetzter Kennwörter. Um diesen Fehler zu beseitigen und die Funktion zur Kennwortrückschreibung in Ihrer Organisation optimal zu nutzen, wird dringend empfohlen, die aktuelle Version von Azure AD Connect oder Azure Active Directory Connect zu installieren (weitere Informationen finden Sie unter <a href="../active-directory-aadconnect#download-azure-ad-connect">Tools für die Verzeichnisintegration</a>).</p>
            </td>
          </tr>
        </tbody></table>


## Ereignisprotokoll-Fehlercodes für die Kennwortrückschreibung
Eine bewährte Methode bei der Problembehandlung für die Kennwortrückschreibung besteht darin, das Anwendungsereignisprotokoll auf Ihrem Azure AD Connect-Computer zu überprüfen. Dieses Ereignisprotokoll enthält Ereignisse aus zwei Quellen, die für die Kennwortrückschreibung von Interesse sind. Die Quelle "PasswordResetService" beschreibt Vorgänge und Probleme im Zusammenhang mit der Kennwortrückschreibung. Die Quelle "ADSync" beschreibt Vorgänge und Probleme im Zusammenhang mit dem Zurücksetzen von Kennwörtern in Ihrer AD-Umgebung.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Code</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Name / Meldung</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quelle</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Beschreibung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>BAIL: MMS(4924) 0x80230619 – "Eine Einschränkung verhindert, dass das Kennwort in das aktuell angegebene geändert wird."</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Dieses Ereignis tritt auf, wenn der Dienst für die Kennwortrückschreibung versucht, ein Kennwort für Ihr lokales Verzeichnis festzulegen, das die in der Domäne geltenden Anforderungen im Hinblick auf Alter, Verlauf, Komplexität oder Filterung für Kennwörter nicht erfüllt.</p>
              <ul>
                <li class="unordered">
										Wenn Sie ein Mindestkennwortalter festgelegt habe und das Kennwort kürzlich geändert wurde, können Sie das Kennwort erst wieder ändern, wenn es das für Ihre Domäne festgelegte Kennwortalter erreicht hat. Zu Testzwecken sollte das Mindestalter auf&#160;0 festgelegt werden.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Wenn Sie Anforderungen für den Kennwortverlauf festgelegt haben, müssen Sie ein Kennwort auswählen, das die letzten n Male nicht verwendet wurde, wobei "n" für die Einstellung des Kennwortverlaufs steht. Wenn Sie ein Kennwort auswählen, das die letzten n Male verwendet wurde, wird dieser Fehler angezeigt. Zu Testzwecken sollte der Verlauf auf&#160;0 festgelegt werden.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Wenn Anforderungen an die Kennwortkomplexität gelten, werden diese erzwungen, wenn der Benutzer versucht, ein Kennwort zu ändern oder zurückzusetzen.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Wenn Sie Kennwortfilter aktiviert haben und ein Benutzer versucht, ein Kennwort auszuwählen, das nicht den Filterkriterien entspricht, tritt bei der Kennwortänderung oder -zurücksetzung ein Fehler auf.&lt;br>&lt;br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der lokale Dienst eine Anforderung zur Kennwortzurücksetzung für einen Verbundbenutzer oder einen Benutzer mit Kennworthashsynchronisierung ermittelt hat, die aus der Cloud stammt. Dieses Ereignis ist das erste Ereignis bei jedem Rückschreibevorgang für zurückgesetzte Kennwörter.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein Benutzer während einer Kennwortzurücksetzung ein neues Kennwort angefordert hat. Es wurde ermittelt, dass dieses Kennwort die Unternehmensanforderungen für Kennwörter erfüllt, und das Kennwort wurde erfolgreich in die lokale AD-Umgebung zurückgeschrieben.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein Benutzer ein Kennwort ausgewählt hat, und dass dieses Kennwort erfolgreich in der lokalen Umgebung empfangen wurde. Bei dem Versuch, das Kennwort in der lokalen AD-Umgebung festzulegen, ist jedoch ein Fehler aufgetreten. Dies kann aus verschiedenen Gründen passieren:</p>
              <ul>
                <li class="unordered">
										Das Benutzerkennwort entspricht nicht den für die Domäne geltenden Anforderungen in Bezug auf Alter, Verlauf, Komplexität oder Kennwortfilter. Versuchen Sie, den Fehler durch Auswahl eines komplett neuen Kennworts zu beheben.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Das MA-Dienstkonto weist nicht die geeigneten Berechtigungen zum Festlegen des neuen Kennworts für das betreffende Benutzerkonto auf.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Das Benutzerkonto befindet sich in einer geschützten Gruppe, z.&#160;B. "Domänen-Admins" oder "Unternehmensadministratoren". Für diese Gruppen ist eine Kennwortverwaltung nicht möglich.&lt;br>&lt;br></li>
              </ul>
              <p>Informationen zu weiteren Situationen, die diesen Fehler auslösen können, finden Sie unter <a href="#troubleshoot-password-writeback">Problembehandlung: Kennwortrückschreibung</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis tritt auf, wenn Sie die Kennwortrückschreibung mit Azure AD Connect aktivieren. Es zeigt, dass die Integration Ihrer Organisation in den Webdienst für die Kennwortrückschreibung gestartet wurde.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Integrationsvorgang erfolgreich war und die Funktion zur Kennwortrückschreibung einsatzbereit ist.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der lokale Dienst eine Anforderung zur Kennwortänderung für einen Verbundbenutzer oder einen Benutzer mit Kennworthashsynchronisierung ermittelt hat, die aus der Cloud stammt. Dieses Ereignis ist das erste Ereignis bei jedem Rückschreibevorgang für geänderte Kennwörter.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein Benutzer während einer Kennwortänderung ein neues Kennwort angefordert hat. Es wurde ermittelt, dass dieses Kennwort die Unternehmensanforderungen für Kennwörter erfüllt, und das Kennwort wurde erfolgreich in die lokale AD-Umgebung zurückgeschrieben.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein Benutzer ein Kennwort ausgewählt hat, und dass dieses Kennwort erfolgreich in der lokalen Umgebung empfangen wurde. Bei dem Versuch, das Kennwort in der lokalen AD-Umgebung festzulegen, ist jedoch ein Fehler aufgetreten. Dies kann aus verschiedenen Gründen passieren:</p>
              <ul>
                <li class="unordered">
										Das Benutzerkennwort entspricht nicht den für die Domäne geltenden Anforderungen in Bezug auf Alter, Verlauf, Komplexität oder Kennwortfilter. Versuchen Sie, den Fehler durch Auswahl eines komplett neuen Kennworts zu beheben.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Das MA-Dienstkonto weist nicht die geeigneten Berechtigungen zum Festlegen des neuen Kennworts für das betreffende Benutzerkonto auf.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Das Benutzerkonto befindet sich in einer geschützten Gruppe, z.&#160;B. "Domänen-Admins" oder "Unternehmensadministratoren". Für diese Gruppen ist eine Kennwortverwaltung nicht möglich.&lt;br>&lt;br></li>
              </ul>
              <p>Informationen zu weiteren Situationen, die diesen Fehler auslösen können, finden Sie unter <a href="#troubleshoot-password-writeback">Problembehandlung: Kennwortrückschreibung</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Der lokale Dienst hat eine Anforderung zur Kennwortzurücksetzung für einen Verbundbenutzer oder einen Benutzer mit Kennworthashsynchronisierung ermittelt, die im Namen eines Benutzers vom Administrator initiiert wurde. Dieses Ereignis ist das erste Ereignis bei jedem Rückschreibevorgang für zurückgesetzte Kennwörter, der von einem Administrator initiiert wird.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Der Administrator hat während einer vom Administrator initiierten Kennwortzurücksetzung ein neues Kennwort angefordert. Es wurde ermittelt, dass dieses Kennwort die Unternehmensanforderungen für Kennwörter erfüllt, und das Kennwort wurde erfolgreich in die lokale AD-Umgebung zurückgeschrieben.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Der Administrator hat im Namen eines Benutzers ein Kennwort ausgewählt, und dieses Kennwort wurde erfolgreich in der lokalen Umgebung empfangen. Bei dem Versuch, das Kennwort in der lokalen AD-Umgebung festzulegen, ist jedoch ein Fehler aufgetreten. Dies kann aus verschiedenen Gründen passieren:</p>
              <ul>
                <li class="unordered">
										Das Benutzerkennwort entspricht nicht den für die Domäne geltenden Anforderungen in Bezug auf Alter, Verlauf, Komplexität oder Kennwortfilter. Versuchen Sie, den Fehler durch Auswahl eines komplett neuen Kennworts zu beheben.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Das MA-Dienstkonto weist nicht die geeigneten Berechtigungen zum Festlegen des neuen Kennworts für das betreffende Benutzerkonto auf.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Das Benutzerkonto befindet sich in einer geschützten Gruppe, z.&#160;B. "Domänen-Admins" oder "Unternehmensadministratoren". Für diese Gruppen ist eine Kennwortverwaltung nicht möglich.&lt;br>&lt;br></li>
              </ul>
              <p>Informationen zu weiteren Situationen, die diesen Fehler auslösen können, finden Sie unter <a href="#troubleshoot-password-writeback">Problembehandlung: Kennwortrückschreibung</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis tritt auf, wenn Sie die Kennwortrückschreibung mit Azure AD Connect deaktivieren. Es zeigt, dass der Vorgang zum Entfernen Ihrer Organisation aus dem Webdienst für die Kennwortrückschreibung gestartet wurde.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Vorgang zum Entfernen erfolgreich war und die Funktion zur Kennwortrückschreibung erfolgreich deaktiviert wurde.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Vorgang zum Entfernen nicht erfolgreich war. Dies kann auf Berechtigungsfehler in Bezug auf das Cloudadministratorkonto oder das lokale Administratorkonto zurückzuführen sein, das bei der Konfiguration angegeben wurde. Oder der Fehler wurde ausgelöst, weil Sie versucht haben, ein Cloudkonto für einen globalen Verbundadministrator anzugeben, als Sie die Kennwortrückschreibung deaktiviert haben. Prüfen Sie zur Beseitigung des Fehlers Ihre Administratorberechtigungen, und stellen Sie sicher, dass Sie kein Verbundkonto zum Konfigurieren der Kennwortrückschreibung verwenden.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Dienst für die Kennwortrückschreibung erfolgreich gestartet wurde und bereit ist, Anforderungen zur Kennwortverwaltung aus der Cloud zu empfangen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Dienst für die Kennwortrückschreibung beendet wurde, und dass Anforderungen zur Kennwortverwaltung aus der Cloud nicht verarbeitet werden können.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass für den globalen Administrator, der während der Azure AD Connect-Einrichtung angegeben wurde, erfolgreich ein Autorisierungstoken abgerufen wurde, um den Vorgang zur Integration oder zum Entfernen zu starten.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Schlüssel zur Kennwortverschlüsselung, der zum Verschlüsseln von Kennwörtern aus der Cloud verwendet wird, die an Ihre lokale Umgebung gesendet werden, erfolgreich erstellt wurde.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist auf einen unbekannten Fehler während eines Vorgangs zur Kennwortverwaltung hin. Lesen Sie den Ausnahmetext im Ereignis, um weitere Informationen zu erhalten. Wenn Probleme vorliegen, deaktivieren Sie die Kennwortrückschreibung, und aktivieren Sie sie anschließend erneut. Wenn das Problem so nicht gelöst werden kann, senden Sie eine Kopie des Ereignisprotokolls zusammen mit der angegebenen Nachverfolgungs-ID an Ihren Supportmitarbeiter.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass bei der Verbindungsherstellung mit dem Clouddienst für die Kennwortzurücksetzung ein Fehler aufgetreten ist. Dies geschieht im Allgemeinen, wenn der lokale Dienst keine Verbindung mit dem Webdienst für die Kennwortzurücksetzung herstellen kann. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass bei der Verbindungsherstellung mit der Service Bus-Instanz Ihres Mandanten ein Fehler aufgetreten ist. Dies kann passieren, wenn Sie in Ihrer lokalen Umgebung ausgehende Verbindungen blockieren. Prüfen Sie Ihre Firewall, und stellen Sie sicher, dass Verbindungen über TCP&#160;443 und mit <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> möglich sind. Versuchen Sie es anschließend erneut. Wenn weiterhin Probleme auftreten, deaktivieren Sie die Kennwortrückschreibung, und aktivieren Sie sie anschließend erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass die an die Webdienst-API übergebene Eingabe ungültig war. Wiederholen Sie den Vorgang.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass beim Entschlüsseln des aus der Cloud empfangenen Kennworts ein Fehler aufgetreten ist. Dieser Fehler kann verursacht werden, wenn der Entschlüsselungsschlüssel des Clouddiensts und der lokalen Umgebung nicht übereinstimmen. Um das Problem zu lösen, versuchen Sie die Kennwortrückschreibung in der lokalen Umgebung zu deaktivieren und erneut zu aktivieren.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Während der Integration wurden mandantenspezifische Informationen in einer Konfigurationsdatei in Ihrer lokalen Umgebung gespeichert. Dieses Ereignis zeigt an, dass beim Speichern dieser Datei ein Fehler aufgetreten ist, oder dass beim Starten des Diensts ein Fehler beim Lesen der Datei aufgetreten ist. Um das Problem zu lösen, versuchen Sie die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren, um eine Neuerstellung dieser Konfigurationsdatei zu erzwingen. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Während der Integration werden Daten vom Clouddienst für die Kennwortzurücksetzung an den lokalen Dienst für die Kennwortzurücksetzung gesendet. Diese Daten werden dann in eine Datei im Arbeitsspeicher geschrieben, bevor sie an den Synchronisierungsdienst gesendet und sicher auf der Festplatte gespeichert werden. Dieses Ereignis weist auf ein Problem hin, das beim Schreiben oder Aktualisieren dieser Daten im Arbeitsspeicher aufgetreten ist. Um das Problem zu lösen, versuchen Sie die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren, um eine Neuerstellung dieser Konfigurationsdaten zu erzwingen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass eine ungültige Antwort vom Webdienst für die Kennwortzurücksetzung empfangen wurde. Um dieses Problem zu lösen, versuchen Sie die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass kein Autorisierungstoken für das globale Administratorkonto abgerufen werden konnte, das beim Azure AD Connect-Setup angegeben wurde. Dieser Fehler kann durch einen ungültigen Benutzernamen oder ein ungültiges Kennwort verursacht werden, der bzw. das für das globale Administratorkonto angegeben wurde. Eine weitere Fehlerursache kann sein, dass es sich um ein Konto für einen globalen Verbundadministrator handelt. Führen Sie zur Beseitigung dieses Fehlers die Konfiguration mit dem richtigen Benutzernamen und Kennwort erneut aus, und stellen Sie sicher, dass es sich bei dem Administratorkonto um ein verwaltetes Konto (nur Cloud oder Kennwortsynchronisierung) handelt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass beim Generieren des Kennwortverschlüsselungsschlüssels oder beim Entschlüsseln eines vom Clouddienst empfangenen Kennworts ein Fehler aufgetreten ist. Bei Auftreten dieses Fehlers liegt häufig ein Problem mit der lokalen Umgebung vor. Prüfen Sie die Details im Ereignisprotokoll, um weitere Informationen zum aufgetretenen Problem und zu dessen Lösung zu erhalten. Sie können zur Lösung des Problems auch versuchen, die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der lokale Dienst nicht ordnungsgemäß mit dem Webdienst für die Kennwortzurücksetzung kommunizieren konnte, um den Integrationsvorgang zu starten. Die Fehlerursache kann eine Firewallregel oder ein Problem beim Abrufen eines Authentifizierungstokens für Ihren Mandanten sein. Stellen Sie zur Beseitigung dieses Fehlers sicher, dass ausgehende Verbindungen über TCP&#160;443 und TCP&#160;9350-9354 oder mit <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> nicht blockiert werden, und dass es sich bei dem für die Integration verwendeten AAD-Administratorkonto nicht um ein Verbundkonto handelt. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der lokale Dienst nicht ordnungsgemäß mit dem Webdienst für die Kennwortzurücksetzung kommunizieren konnte, um den Vorgang zum Entfernen zu starten. Die Fehlerursache kann eine Firewallregel oder ein Problem beim Abrufen eines Autorisierungstokens für Ihren Mandanten sein. Stellen Sie zur Beseitigung dieses Fehlers sicher, dass ausgehende Verbindungen über Port&#160;443 oder mit <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> nicht blockiert werden, und dass es sich bei dem für die Integration verwendeten AAD-Administratorkonto nicht um ein Verbundkonto handelt. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass erneut versucht werden musste, eine Verbindung mit der Service Bus-Instanz Ihres Mandanten herzustellen. Unter normalen Umständen ist dies kein Problem, aber wenn dieses Ereignis häufiger angezeigt wird, prüfen Sie ggf. Ihre Netzwerkverbindung mit dem Service&#160;Bus. Dies gilt insbesondere, wenn Sie eine Verbindung mit hoher Latenz oder niedriger Bandbreite verwenden.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Um die Integrität des Diensts für die Kennwortrückschreibung zu überwachen, werden alle 5&#160;Minuten Taktdaten an den Webdienst für die Kennwortzurücksetzung gesendet. Dieses Ereignis weist darauf hin, dass beim Senden dieser Integritätsinformationen zurück an den Cloudwebdienst ein Fehler aufgetreten ist. Diese Integritätsinformationen enthalten keine OII- oder PII-Daten. Es handelt sich lediglich um ein Taktsignal und grundlegende Dienststatistiken, um Dienststatusinformationen in der Cloud bereitzustellen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein unbekannter Fehler von AD zurückgegeben wurde. Prüfen Sie das Ereignisprotokoll auf dem Azure AD Connect-Server auf Ereignisse der Quelle "ADSync", um weitere Informationen zu diesem Fehler zu erhalten.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Benutzer, der ein Kennwort zurücksetzen oder ändern möchte, nicht im lokalen Verzeichnis gefunden wurde. Dieser Fehler kann auftreten, wenn der Benutzer lokal, aber nicht in der Cloud gelöscht wurde, oder wenn ein Problem mit der Synchronisierung vorliegt. Prüfen Sie Ihre Synchronisierungsprotokolle sowie die Details zu den letzten Synchronisierungsvorgängen, um weitere Informationen zu erhalten.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Wenn eine Anforderung zur Kennwortzurücksetzung oder -änderung aus der Cloud stammt, wird mithilfe des bei der Einrichtung von Azure AD Connect angegeben Cloudankers ermittelt, wie diese Anforderung mit einem Benutzer in Ihrer lokalen Umgebung verknüpft wird. Dieses Ereignis weist darauf hin, dass zwei Benutzer mit demselben Cloudankerattribut im lokalen Verzeichnis gefunden wurden. Prüfen Sie Ihre Synchronisierungsprotokolle sowie die Details zu den letzten Synchronisierungsvorgängen, um weitere Informationen zu erhalten.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass das Dienstkonto für den Verwaltungs-Agent über keine ausreichenden Berechtigungen verfügt, um für das betreffende Konto neues Kennwort festzulegen. Stellen Sie sicher, dass das MA-Konto in der Gesamtstruktur des Benutzers über die Berechtigungen zum Zurücksetzen und Ändern von Kennwörtern für alle Objekte in der Gesamtstruktur verfügt. Weitere Informationen zur Vorgehensweise finden Sie unter <a href="../active-directory-passwords-getting-started#step-4-set-up-the-appropriate-active-directory-permissions">Schritt&#160;4: Einrichten der geeigneten Active Directory-Berechtigungen</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass versucht wurde, ein Kennwort für ein Konto zurückzusetzen oder zu ändern, das lokal deaktiviert ist. Aktivieren Sie das Konto, und versuchen Sie es erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass versucht wurde, ein Kennwort für ein Konto zurückzusetzen oder zu ändern, das lokal gesperrt ist. Sperrungen können auftreten, wenn ein Benutzer innerhalb eines kurzen Zeitraums zu häufig versucht hat, ein Kennwort zu ändern oder zurückzusetzen. Entsperren Sie das Konto, und versuchen Sie es erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Benutzer beim Ausführen einer Kennwortänderung ein falsches aktuelles Kennwort angegeben hat. Geben Sie das richtige aktuelle Kennwort an, und versuchen Sie es erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis tritt auf, wenn der Dienst für die Kennwortrückschreibung versucht, ein Kennwort für Ihr lokales Verzeichnis festzulegen, das die in der Domäne geltenden Anforderungen im Hinblick auf Alter, Verlauf, Komplexität oder Filterung für Kennwörter nicht erfüllt.</p>
              <ul>
                <li class="unordered">
										Wenn Sie ein Mindestkennwortalter festgelegt habe und das Kennwort kürzlich geändert wurde, können Sie das Kennwort erst wieder ändern, wenn es das für Ihre Domäne festgelegte Kennwortalter erreicht hat. Zu Testzwecken sollte das Mindestalter auf&#160;0 festgelegt werden.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Wenn Sie Anforderungen für den Kennwortverlauf festgelegt haben, müssen Sie ein Kennwort auswählen, das die letzten n Male nicht verwendet wurde, wobei "n" für die Einstellung des Kennwortverlaufs steht. Wenn Sie ein Kennwort auswählen, das die letzten n Male verwendet wurde, wird dieser Fehler angezeigt. Zu Testzwecken sollte der Verlauf auf&#160;0 festgelegt werden.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Wenn Anforderungen an die Kennwortkomplexität gelten, werden diese erzwungen, wenn der Benutzer versucht, ein Kennwort zu ändern oder zurückzusetzen.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Wenn Sie Kennwortfilter aktiviert haben und ein Benutzer versucht, ein Kennwort auszuwählen, das nicht den Filterkriterien entspricht, tritt bei der Kennwortänderung oder -zurücksetzung ein Fehler auf.&lt;br>&lt;br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass beim Zurückschreiben eines Kennworts in das lokale Verzeichnis aufgrund eines Konfigurationsproblems in Active Directory ein Fehler aufgetreten ist. Überprüfen Sie das Anwendungsereignisprotokoll für den Azure AD Connect-Computer auf Meldungen vom ADSync-Dienst, um weitere Informationen zur Fehlerursache zu erhalten. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
        </tbody></table>
## Problembehandlung: Konnektivität bei der Kennwortrückschreibung

Wenn es bei Verwendung der Azure AD Connect-Komponente für die Kennwortrückschreibung zu Dienstunterbrechungen kommt, können Sie die folgenden Schritte für eine schnelle Problemlösung einsetzen:

 - [Neustarten des Azure AD Connect-Synchronisierungsdiensts](#restart-the-azure-AD-sync-service)
 - [Deaktivieren und erneutes Aktivieren der Funktion für die Kennwortrückschreibung](#disable-and-re-enable-the-password-writeback-feature)
 - [Installieren der aktuellen Azure AD Connect-Version](#install-the-latest-azure-ad-connect-release)
 - [Problembehandlung: Kennwortrückschreibung](#troubleshoot-password-writeback)

Im Allgemeinen wird empfohlen, diese Schritte in der oben genannten Reihenfolge auszuführen, um Ihren Dienst schnellstmöglich wiederherzustellen.

### Neustarten des Azure AD Connect-Synchronisierungsdiensts
Durch einen Neustart des Azure AD Connect-Synchronisierungsdiensts können Konnektivitätsprobleme oder andere vorübergehende Probleme mit dem Dienst beseitigt werden.

 1.	Klicken Sie als Administrator auf dem Server, auf dem **Azure AD Connect** ausgeführt wird, auf **Start**.
 2.	Geben Sie im Suchfeld **"services.msc"** ein, und drücken Sie die **Eingabetaste**.
 3.	Suchen Sie nach dem Eintrag **Microsoft Azure AD Connect**.
 4.	Klicken Sie mit der rechten Maustaste auf den Diensteintrag, klicken Sie auf **Neu starten**, und warten Sie, bis der Vorgang abgeschlossen wurde.

    ![][002]

Durch Ausführen dieser Schritte wird die Verbindung mit dem Clouddienst erneut hergestellt, und mögliche Unterbrechungen werden behoben. Wenn das Problem durch einen Neustart des Synchronisierungsdiensts nicht gelöst werden kann, empfehlen wir, im nächsten Schritt die Funktion zur Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.

### Deaktivieren und erneutes Aktivieren der Funktion für die Kennwortrückschreibung
Durch das Deaktivieren und anschließende erneute Aktivieren der Funktion für die Kennwortrückschreibung können Konnektivitätsprobleme behoben werden.

 1.	Öffnen Sie als Administrator den **Konfigurations-Assistenten für Azure AD Connect**.
 2.	Geben Sie im Dialogfeld **Mit Azure AD verbinden** Ihre **Anmeldeinformationen für globale Azure AD-Administratoren** ein.
 3.	Geben Sie im Dialogfeld **Mit Azure AD verbinden** die **Anmeldeinformationen für Administratoren der AD-Domänendienste** ein.
 4.	Klicken Sie im Dialogfeld **Eindeutige Identifizierung der Benutzer** auf die Schaltfläche **Weiter**.
 5.	Deaktivieren Sie im Dialogfeld **Optionale Features** das Kontrollkästchen **Kennwort zurückschreiben**.

    ![][003]

 6.	Klicken Sie auf den verbleibenden Assistentenseiten auf **Weiter**, ohne Änderungen vorzunehmen, bis Sie zur Seite **Bereit zur Konfiguration** gelangen.
 7.	Stellen Sie sicher, dass auf der Konfigurationsseite die **Option zur Kennwortrückschreibung als deaktiviert angezeigt wird**, und klicken Sie dann auf die Schaltfläche **Konfigurieren**, um Ihre Änderungen zu übernehmen.
 8.	Deaktivieren Sie auf der Seite **Fertig** die Option **Jetzt synchronisieren**, und klicken Sie auf **Fertig stellen**, um den Assistenten zu schließen.
 9.	Öffnen Sie erneut den **Konfigurations-Assistenten für Azure AD Connect**.
 10.	**Wiederholen Sie die Schritte 2 bis 8**, **aktivieren Sie jedoch die Option für die Kennwortrückschreibung** auf dem Bildschirm **Optionale Features**, um den Dienst erneut zu aktivieren.

    ![][004]

Durch Ausführen dieser Schritte wird die Verbindung mit dem Clouddienst erneut hergestellt, und mögliche Unterbrechungen werden behoben.

Wenn das Problem durch das Deaktivieren und erneute Aktivieren der Funktion für die Kennwortrückschreibung nicht gelöst werden kann, empfehlen wir, im nächsten Schritt die Funktion zur Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.

### Installieren der aktuellen Azure AD Connect-Version
Durch das erneute Installieren des Azure AD Connect-Pakets werden Konfigurationsprobleme behoben, die sich möglicherweise auf die Fähigkeit zur Verbindungsherstellung mit den Clouddiensten oder auf die Verwaltung von Kennwörtern in Ihrer lokalen AD-Umgebung auswirken. Es wird empfohlen, diese Maßnahme erst zu ergreifen, wenn Sie ersten zwei Schritte bereits ausgeführt haben.

 1.	Sie können die aktuelle Version von Azure AD Connect [hier](active-directory-aadconnect.md#download-azure-ad-connect) herunterladen.
 2.	Da Sie Azure AD Connect bereits installiert haben, müssen Sie lediglich ein direktes Upgrade Ihrer Azure AD Connect-Installation auf die aktuelle Version durchführen.
 3.	Führen Sie das heruntergeladene Paket aus, und folgen Sie den Bildschirmanweisungen zum Aktualisieren Ihres Azure AD Connect-Computers. Sofern Sie keine Anpassung der integrierten Synchronisierungsregeln durchgeführt haben, sind keine weiteren manuellen Schritte erforderlich. Falls Sie die Synchronisierungsregeln angepasst haben sollten, **sichern Sie diese, bevor Sie mit dem Upgrade fortfahren, und stellen Sie sie nach dem Upgrade erneut manuell bereit**.

Durch Ausführen dieser Schritte wird die Verbindung mit dem Clouddienst erneut hergestellt, und mögliche Unterbrechungen werden behoben.

Wenn das Installieren der aktuellen Version von Azure AD Connect nicht zur Lösung des Problems führt, empfehlen wir, als letzten Schritt nach dem Installieren des aktuellen QFE für die Synchronisierung die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.

Wenn das Problem anschließend weiterhin besteht, wird empfohlen, unter [Problembehandlung: Kennwortrückschreibung](#troubleshoot-password-writeback) und [Häufig gestellte Fragen zur Azure AD-Kennwortverwaltung](active-directory-passwords-faq.md) nachzusehen, ob Ihr Problem möglicherweise dort beschrieben wird.


<br/> <br/> <br/>

**Zusätzliche Ressourcen**


* [Was ist die Kennwortverwaltung?](active-directory-passwords.md)
* [Funktionsweise der Kennwortverwaltung](active-directory-passwords-how-it-works.md)
* [Erste Schritte mit der Kennwortverwaltung](active-directory-passwords-getting-started.md)
* [Anpassen der Kennwortverwaltung](active-directory-passwords-customize.md)
* [Best Practices für die Kennwortverwaltung](active-directory-passwords-best-practices.md)
* [Operative Einblicke durch Berichte zur Kennwortverwaltung](active-directory-passwords-get-insights.md)
* [Häufig gestellte Fragen zur Kennwortverwaltung](active-directory-passwords-faq.md)
* [Weitere Informationen](active-directory-passwords-learn-more.md)
* [Kennwortverwaltung auf MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)




[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"

 

<!---HONumber=August15_HO6-->