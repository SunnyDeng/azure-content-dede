<properties 
	pageTitle="Anpassen: Azure AD-Kennwortverwaltung | Microsoft Azure" 
	description="Vorgehensweise zum Anpassen von Aussehen und Verhalten der Kennwortverwaltung und von Benachrichtigungen in Azure AD an Ihre Anforderungen." 
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
	ms.date="10/08/2015" 
	ms.author="asteen"/>

# Anpassen der Kennwortverwaltung an die Anforderungen Ihrer Organisation
Es wird empfohlen, alle verfügbaren Konfigurationsoptionen für die Kennwortverwaltung zu untersuchen und auszuprobieren, um Ihren Benutzern das bestmögliche Ergebnis zu bieten. Tatsächlich können Sie sofort damit anfangen, indem Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) zur Registerkarte "Konfiguration" der **Active Directory-Erweiterung** wechseln. Dieses Thema führt Sie durch alle Anpassungen zur Kennwortverwaltung, die Sie als Administrator über die Registerkarte **Konfigurieren** für Ihr Verzeichnis innerhalb des [Azure-Verwaltungsportals](https://manage.windowsazure.com) vornehmen können. Dazu gehören:

- [**Anpassen von Aussehen und Verhalten der Kennwortverwaltung**](#password-managment-look-and-feel)
- [**Anpassen des Verhaltens der Benutzerkennwortverwaltung**](#password-management-behavior)
- [**Anpassen der Benachrichtigungen der Kennwortverwaltung**](#password-management-notifications)

## Aussehen und Verhalten der Kennwortverwaltung
In der folgenden Tabelle werden die Auswirkungen der einzelnen Steuerelemente auf die bereitgestellte Funktionalität für Benutzer beschrieben, die sich für die Kennwortzurücksetzung registrieren und ihre Kennwörter zurücksetzen. Sie können diese Optionen im Abschnitt **Verzeichniseigenschaften** auf der Registerkarte **Konfigurieren** Ihres Verzeichnisses innerhalb des [Azure-Verwaltungsportals](https://manage.windowsazure.com) konfigurieren.

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Richtliniensteuerung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Beschreibung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Auswirkungen?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Verzeichnisname</p>
              </td>
              <td>
                <p>Legt fest, welcher Organisationsname den Benutzern oder Administratoren bei der E-Mail-Kommunikation zur Kennwortzurücksetzung angezeigt wird</p>
              </td>
              <td>
                <p>
                  <strong>"Wenden Sie sich an Ihren Administrator"-E-Mails:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt den Anzeigenamen der Von-Adresse fest, z. B. "Microsoft im Auftrag von <strong>Wingtip Toys</strong>"<br><br></li>
                  <li class="unordered">
												Legt den Betreff der E-Mail fest, z. B. "E-Mail-Prüfcode für das <strong>Wingtip Toys</strong>-Konto"<br><br></li>
                </ul>
                <p>
                  <strong>E-Mails zur Kennwortzurücksetzung:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt den Anzeigenamen der Von-Adresse fest, z. B. "Microsoft im Auftrag von <strong>Wingtip Toys</strong>"<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Darstellung der Seiten " Anmelden" und " Zugriffsbereich"</p>
              </td>
              <td>
                <p>Legt fest, ob Benutzern, die die Seite zur Kennwortzurücksetzung besuchen, das Microsoft-Logo oder Ihr eigenes benutzerdefiniertes Logo angezeigt wird. Dieses Konfigurationselement fügt den Seiten "Zugriffsbereich" und "Anmelden" außerdem Ihr Branding hinzu.</p>
                <p>
                  
                </p>
                <p>Unter <a href="https://technet.microsoft.com/library/dn532270.aspx">Hinzufügen von Unternehmensbranding zu Anmelde- und Zugriffsbereichsseiten</a> erhalten Sie weitere Informationen zum Branding- und Anpassungsfeature für Mandanten.</p>
              </td>
              <td>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt fest, ob Ihr Logo anstelle des Microsoft-Standardlogos am oberen Rand des Kennwortzurücksetzungsportals angezeigt wird.<br><br></li>
                  <li class="unordered">
                    <strong>Hinweis:</strong> Ihr Logo wird möglicherweise nicht auf der ersten Seite des Kennwortzurücksetzungsportals angezeigt, wenn Sie direkt zur Seite "Kennwort zurücksetzen" wechseln. Sobald ein Benutzer seine Benutzer-ID eingibt und auf "Weiter" klickt, wird Ihr Logo angezeigt. Sie können erzwingen, dass Ihr Logo beim Laden der Seite angezeigt wird, indem Sie den whr-Parameter folgendermaßen an die Seite zur Kennwortzurücksetzung übergeben: <a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a><br><br></li>
                </ul>
                <p>
                  <strong>"Wenden Sie sich an Ihren Administrator"-E-Mails:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt fest, ob Ihr Logo am Ende der E-Mails an Administratoren angezeigt wird, wenn Benutzer Sie über den Link "Wenden Sie sich an Ihren Administrator" auf der Benutzeroberfläche zum Zurücksetzen des Kennworts kontaktieren möchten.<br><br></li>
                </ul>
                <p>
                  <strong>E-Mails zur Kennwortzurücksetzung:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt fest, ob Ihr Logo am Ende der E-Mail-Nachrichten angezeigt wird, die an Benutzer gesendet werden, wenn diese ihre Kennwörter zurücksetzen.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Verhalten der Kennwortverwaltung
In der folgenden Tabelle werden die Auswirkungen der einzelnen Steuerelemente auf die bereitgestellte Funktionalität für Benutzer beschrieben, die sich für die Kennwortzurücksetzung registrieren und ihre Kennwörter zurücksetzen. Sie können diese Optionen im Abschnitt **Richtlinie zum Zurücksetzen des Benutzerkennworts** auf der Registerkarte **Konfigurieren** Ihres Verzeichnisses im [Azure-Verwaltungsportal](https://manage.windowsazure.com) konfigurieren.

> [AZURE.NOTE]Dem von Ihnen verwendeten Administratorkonto muss eine AAD Premium-Lizenz zugewiesen sein, damit diese Richtliniensteuerelemente angezeigt werden.<br><br>Diese Richtliniensteuerelemente gelten nur für Endbenutzer, die ihre Kennwörter zurücksetzen, nicht für Administratoren. **Administratoren besitzen eine Standardrichtlinie einer alternativen E-Mail-Adresse und/oder eines Mobiltelefons. Diese wird von Microsoft angegeben und kann nicht geändert werden.**

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Richtliniensteuerung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Beschreibung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Auswirkungen?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</p>
              </td>
              <td>
                <p>Legt fest, ob das Zurücksetzen von Kennwörtern für Benutzer in diesem Verzeichnis aktiviert ist. </p>
              </td>
              <td>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn diese Einstellung auf "Nein" gesetzt ist, können Benutzer keine eigenen Daten für die Überprüfung registrieren.<br><br></li>
                  <li class="unordered">
												Wenn diese Einstellung auf "Ja" gesetzt ist, kann jeder im Verzeichnis enthaltene Endbenutzer Daten für die Authentifizierung registrieren, indem er das Registrierungsportal unter <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> aufruft.<br><br></li>
                  <li class="unordered">
                    <strong>Hinweis:</strong> Benutzern muss eine Azure AD Premium- oder Basic-Lizenz zugewiesen werden, bevor sie sich für die Kennwortzurücksetzung registrieren können.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn diese Einstellung auf "Nein" festgelegt ist, wird Benutzern eine Meldung angezeigt, dass sie sich zum Zurücksetzen des Kennworts an ihren Administrator wenden müssen.<br><br></li>
                  <li class="unordered">
												Wenn sie auf "Ja" gesetzt ist, können Benutzer ihre Kennwörter automatisch zurücksetzen, indem sie <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a> aufrufen oder auf einer beliebigen Anmeldeseite mit Organisations-ID auf den Link <strong>Sie können nicht auf Ihr Konto zugreifen?</strong> klicken.<br><br></li>
                  <li class="unordered">
                    <strong>Hinweis:</strong> Benutzern muss eine Azure AD Premium- oder Basic-Lizenz zugewiesen werden, bevor sie ihre Kennwörter zurücksetzen können.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Zugriff auf die Kennwortrücksetzung beschränken</p>
              </td>
              <td>
                <p>Legt fest, ob nur eine bestimmte Gruppe von Benutzern das Zurücksetzen von Kennwörtern verwenden darf. (Nur sichtbar, wenn <strong>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</strong> auf <strong>Ja</strong> festgelegt ist).</p>
              </td>
              <td>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Ist diese Einstellung auf "Nein" festgelegt, können sich alle Endbenutzer im Verzeichnis für die Kennwortzurücksetzung unter <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> registrieren.<br><br></li>
                  <li class="unordered">
												Ist die Einstellung auf "Ja" festgelegt, können nur die Endbenutzer, die im Steuerelement <strong>Gruppe, die eine Kennwortrücksetzung durchführen kann</strong> angegeben sind, sich für die Kennwortzurücksetzung unter <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> registrieren.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Ist diese Einstellung auf "Nein" festgelegt, können alle Endbenutzer im Verzeichnis ihre Kennwörter zurücksetzen.<br><br></li>
                  <li class="unordered">
												Ist die Einstellung auf "Ja" festgelegt, können nur die Endbenutzer, die im Steuerelement <strong>Gruppe, die eine Kennwortrücksetzung durchführen kann</strong> angegeben sind, ihre Kennwörter zurücksetzen.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Gruppe, die eine Kennwortrücksetzung durchführen kann</p>
              </td>
              <td>
                <p>Legt fest, welche Gruppe von Endbenutzern die Kennwortzurücksetzung durchführen kann. </p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn <strong>Zugriff auf die Kennwortrücksetzung beschränken</strong> auf <strong>Ja</strong> festgelegt ist.)</p>
              </td>
              <td>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn keine Gruppe angegeben ist und Sie auf <strong>Speichern</strong> klicken, wird eine leere Gruppe namens <strong>SSPRSecurityGroupUsers</strong> für Sie erstellt.<br><br></li>
                  <li class="unordered">
												Wenn Sie Ihre eigene Gruppe angeben möchten, können Sie Ihren eigenen Anzeigenamen angeben.<br><br></li>
                </ul>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn <strong>Zugriff auf die Kennwortrücksetzung beschränken</strong> auf <strong>Ja</strong> festgelegt ist, können nur Endbenutzer in dieser Gruppe sich für die Kennwortzurücksetzung registrieren. <br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn <strong>Zugriff auf die Kennwortrücksetzung beschränken</strong> auf <strong>Ja</strong> festgelegt ist, können nur Endbenutzer in dieser Gruppe ihre Kennwörter zurücksetzen.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Für Benutzer verfügbare Authentifizierungsmethoden</p>
              </td>
              <td>
                <p>Legt fest, welche Überprüfungsmethoden ein Benutzer beim Zurücksetzen des Kennworts verwenden darf.</p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn <strong>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</strong> auf <strong>Ja</strong> festgelegt ist).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Mindestens eine Option muss ausgewählt werden.<br><br></li>
                  <li class="unordered">
												Es wird dringend empfohlen, mindestens zwei Optionen zu aktivieren, um Ihren Benutzern die größtmögliche Flexibilität beim Zurücksetzen ihrer Kennwörter zu bieten.<br><br></li>
                  <li class="unordered">
												Wenn Sie Sicherheitsfragen verwenden, empfehlen wir ausdrücklich, dass Sie sie in Verbindung mit einer weiteren Authentifizierungsmethode verwenden, da Sicherheitsfragen weniger sicher sein können als Methoden zur Kennwortzurücksetzung per Telefon oder E-Mail.<br><br></li>
                </ul>
                <p>
                  <strong>Welche Verzeichnisfelder werden verwendet?</strong>
                </p>
                <ul>
                  <li class="unordered">
												"Bürotelefon" entspricht dem Attribut <strong>Bürotelefon</strong> eines Benutzerobjekts im Verzeichnis.<br><br></li>
                  <li class="unordered">
												"Mobiltelefon" entspricht entweder dem Attribut für <strong>mobile Authentifizierung</strong> (das nicht öffentlich angezeigt wird) oder dem Attribut <strong>Mobiltelefon</strong> (das öffentlich sichtbar ist) für ein Benutzerobjekt im Verzeichnis. Der Dienst sucht zunächst unter <strong>Telefon für Authentifizierung</strong> nach Daten. Wenn keine vorhanden sind, wird das Attribut <strong>Mobiltelefon</strong> abgefragt.<br><br></li>
                  <li class="unordered">
												"Alternative E-Mail-Adresse" entspricht entweder dem Attribut <strong>E-Mail-Adresse zur Authentifizierung</strong> (das nicht öffentlich angezeigt wird) oder dem Attribut <strong>Alternative E-Mail-Adresse</strong> (das öffentlich sichtbar ist) für ein Benutzerobjekt im Verzeichnis. Der Dienst sucht zunächst unter <strong>E-Mail-Adresse zur Authentifizierung</strong> nach Daten. Wenn keine vorhanden sind, wird das Attribut <strong>Alternative E-Mail-Adresse</strong> abgefragt.<br><br></li>
                  <li class="unordered">
												Sicherheitsfragen werden privat und sicher in einem Benutzerobjekt im Verzeichnis gespeichert und können nur während der Registrierung von Benutzern beantwortet werden. Aus Sicherheitsgründen gibt es derzeit für einen Administrator keine Möglichkeit, diese Antworten zu bearbeiten oder abzurufen.<br><br></li>
                  <li class="unordered">
                    <strong>Hinweis: </strong>Standardmäßig werden nur die Cloudattribute "Bürotelefon" und "Mobiltelefon" mit Ihrem Cloudverzeichnis aus dem lokalen Verzeichnis synchronisiert. Weitere Informationen über die lokalen Attribute, die mit der Cloud synchronisiert werden, finden Sie unter <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Mit Azure Active Directory synchronisierte Attribute</a>.<br><br></li>
                </ul>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Beeinflusst, welche Authentifizierungsmethoden beim Registrieren von Benutzern angezeigt werden. Wenn Sie eine bestimmte Authentifizierungsmethode nicht aktivieren, können Benutzer sich nicht selbst für diese Authentifizierungsmethode registrieren.<br><br></li>
                  <li class="unordered">
                    <strong>Hinweis: </strong>Benutzer können derzeit nicht ihre eigenen geschäftlichen Telefonnummern registrieren. Diese Authentifizierungsmethode muss vom Administrator definiert werden.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt fest, welche Authentifizierungsmethoden ein Benutzer für einen bestimmten Überprüfungsschritt verwenden kann. Wenn für einen Benutzer beispielsweise sowohl im Feld <strong>Bürotelefon</strong> als auch im Feld <strong>Telefon für Authentifizierung</strong> in Azure Active Directory Daten vorliegen, kann er eine dieser Authentifizierungsmethoden verwenden, um das Kennwort wiederherzustellen.<br><br></li>
                  <li class="unordered">
                    <strong>Hinweis: </strong>Benutzer können ihr Kennwort nur dann zurücksetzen, wenn für sie Daten in den Authentifizierungsmethoden vorliegen, die Sie als Administrator aktiviert haben.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Anzahl erforderlicher Authentifizierungsmethoden</p>
              </td>
              <td>
                <p>Bestimmt die Mindestanzahl der verfügbaren Authentifizierungsmethoden, die ein Benutzer zum Zurücksetzen des Kennworts durchlaufen muss.</p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn <strong>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</strong> auf <strong>Ja</strong> festgelegt ist).</p>
              </td>
              <td>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Kann auf 1 oder 2 erforderliche Authentifizierungsmethoden festgelegt werden.<br><br></li>
                </ul>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Bestimmt die Mindestanzahl der Authentifizierungsmethoden, die ein Benutzer registrieren muss, um die Kennwortzurücksetzung abzuschließen.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Betrifft die Anzahl der Überprüfungsschritte, die ein Benutzer durchlaufen muss, bevor er ein Kennwort zurücksetzen kann. Ein Überprüfungsschritt wird definiert als ein Benutzer, der einen Bestandteil der Authentifizierungsinformationen (z. B. einen Anruf an die Bürotelefonnummer oder eine E-Mail an die alternative E-Mail-Adresse) verwendet, um das Konto zu überprüfen.<br><br></li>
                  <li class="unordered">
                    <strong>Hinweis:</strong> Wenn für das Konto eines Benutzers nicht die erforderliche Menge an Authentifizierungsinformationen definiert wurde, um ein erfolgreiches Zurücksetzen des Kennworts entsprechend der von Ihnen festgelegten Richtlinie zu gewährleisten, wird dem Benutzer eine Fehlerseite angezeigt, über die er einen Administrator zum Zurücksetzen des Kennworts auffordern kann.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Anzahl der für die Registrierung erforderlichen Fragen</p>
              </td>
              <td>
                <p>Bestimmt die Mindestanzahl von Fragen, die ein Benutzer beim Registrieren für die Sicherheitsfragenoption beantworten muss.</p>
                <p>(Nur sichtbar, wenn das Kontrollkästchen <strong>Sicherheitsfragen</strong> aktiviert ist).</p>
              </td>
              <td>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Kann auf drei bis fünf für die Registrierung erforderliche Fragen festgelegt werden.<br><br></li>
                  <li class="unordered">
												Die Anzahl der für die Registrierung erforderlichen Fragen muss größer als oder gleich der Anzahl der für die Zurücksetzung erforderlichen Fragen sein.<br><br></li>
                  <li class="unordered">
												Es empfiehlt sich, die Anzahl der für die Registrierung erforderlichen Fragen auf einen höheren Wert zu setzen als die Anzahl, die für die Zurücksetzung erforderlich ist, um den Benutzern beim Zurücksetzen der Kennwörter mehr Flexibilität einzuräumen. Dies ist außerdem eine sicherere Konfiguration, da wir aus dem Pool aller vom Benutzer registrierten Fragen nach dem Zufallsprinzip Fragen auswählen, die der Benutzer beantworten muss.<br><br></li>
                </ul>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Bestimmt die Mindestanzahl von Fragen, die ein Benutzer beantworten muss, damit er vollständig für die Kennwortzurücksetzung registriert ist.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Anzahl der für die Zurücksetzung erforderlichen Fragen </p>
              </td>
              <td>
                <p>Bestimmt die Mindestanzahl von Fragen, die ein Benutzer beim Zurücksetzen eines Kennworts beantworten muss.</p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn das Kontrollkästchen <strong>Sicherheitsfragen</strong> aktiviert ist).</p>
              </td>
              <td>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Kann auf drei bis fünf für die Zurücksetzung erforderliche Fragen festgelegt werden.<br><br></li>
                  <li class="unordered">
												Die Anzahl der für die Zurücksetzung erforderlichen Fragen muss kleiner als oder gleich der Anzahl der für die Registrierung erforderlichen Fragen sein.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Bestimmt die Mindestanzahl von Fragen, die ein Benutzer beantworten muss, bevor sein Kennwort zurückgesetzt werden kann.<br><br></li>
                  <li class="unordered">
												Zum Zeitpunkt der Kennwortzurücksetzung wird diese Anzahl von Fragen nach dem Zufallsprinzip aus der Gesamtliste der für den Benutzer registrierten Fragen ausgewählt. Wenn für den Benutzer beispielsweise fünf Fragen registriert wurden, werden drei der fünf Fragen nach dem Zufallsprinzip ausgewählt, wenn der Benutzer ein Kennwort zurücksetzen möchte. Der Benutzer muss dann alle dieser Fragen richtig beantworten, damit das Kennwort zurückgesetzt werden kann.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Informationsbasierte Sicherheitsfragen</p>
              </td>
              <td>
                <p>Definiert die vordefinierten Sicherheitsfragen, aus denen Ihre Benutzer auswählen können, wenn sie sich für die Kennwortzurücksetzung registrieren und wenn sie ihre Kennwörter zurücksetzen.</p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn das Kontrollkästchen <strong>Sicherheitsfragen</strong> aktiviert ist).</p>
              </td>
              <td>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Alle Fragen der Wissensdatenbank werden anhand des Gebietsschemas im Browser in den vollständigen Satz von Office&#160;365-Sprachen lokalisiert. <br><br></li>
                  <li class="unordered">
												Insgesamt können bis zu 20&#160;Fragen definiert werden (Summe aus benutzerdefinierten und Wissensdatenbank-Fragen).<br><br></li>
                 <li class="unordered">
												Die Mindestanzahl von Zeichen für Antworten ist 3 Zeichen.<br><br></li>
                  <li class="unordered">
												Die maximale Zeichenanzahl für Antworten ist 40 Zeichen.<br><br></li>
                  <li class="unordered">
												Benutzer können nicht zweimal dieselbe Frage beantworten.<br><br></li>
                  <li class="unordered">
												Benutzer können für zwei verschiedene Fragen nicht dieselbe Antwort angeben.<br><br></li>
                  <li class="unordered">
												Beim Definieren der Antworten kann ein beliebiger Zeichensatz verwendet werden (einschließlich Unicode).<br><br></li>
                  <li class="unordered">
												Die Anzahl der definierten Fragen muss größer als oder gleich der Anzahl der für die Registrierung erforderlichen Fragen sein.<br><br></li>
                </ul>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt fest, für welche Fragen ein Benutzer Antworten bereitstellen kann, wenn er sich für die Kennwortzurücksetzung registriert.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt fest, welche Fragen ein Benutzer zum Zurücksetzen eines Kennworts verwenden kann.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzerdefinierte Sicherheitsfragen</p>
              </td>
              <td>
                <p>Definiert die Sicherheitsfragen, aus denen Ihre Benutzer auswählen können, wenn sie sich für die Kennwortzurücksetzung registrieren und wenn sie ihre Kennwörter zurücksetzen.</p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn das Kontrollkästchen <strong>Sicherheitsfragen</strong> aktiviert ist).</p>
              </td>
              <td>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Insgesamt können bis zu 20&#160;Fragen definiert werden (Summe aus benutzerdefinierten und Wissensdatenbank-Fragen).<br><br></li>
                  <li class="unordered">
												Die maximale Zeichenanzahl für Fragen ist 200 Zeichen.<br><br></li>
                  <li class="unordered">
												Die Mindestanzahl von Zeichen für Antworten ist 3 Zeichen.<br><br></li>
                  <li class="unordered">
												Die maximale Zeichenanzahl für Antworten ist 40 Zeichen.<br><br></li>
                  <li class="unordered">
												Benutzer können nicht zweimal dieselbe Frage beantworten.<br><br></li>
                  <li class="unordered">
												Benutzer können für zwei verschiedene Fragen nicht dieselbe Antwort angeben.<br><br></li>
                  <li class="unordered">
												Bei der Definition von Fragen und Antworten kann ein beliebiger Zeichensatz verwendet werden (einschließlich Unicode).<br><br></li>
                  <li class="unordered">
												Die Anzahl der definierten Fragen muss größer als oder gleich der Anzahl der für die Registrierung erforderlichen Fragen sein.<br><br></li>
                  <li class="unordered">
												Die Definition verschiedener Fragen für verschiedene Gebietsschemas wird für benutzerdefinierte Fragen nicht unterstützt. Alle benutzerdefinierten Fragen werden auch bei abweichendem Gebietsschema im Browser in der Sprache angezeigt, in der sie in der administrativen Benutzeroberfläche eingegeben wurden. Falls diese Fragen lokalisiert werden sollen, verwenden Sie bitte stattdessen wissensbasierte Fragen.<br><br></li>
                </ul>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt fest, für welche Fragen ein Benutzer Antworten bereitstellen kann, wenn er sich für die Kennwortzurücksetzung registriert.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Legt fest, welche Fragen ein Benutzer zum Zurücksetzen eines Kennworts verwenden kann.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Sollen sich Benutzer bei der Anmeldung im Zugriffsbereich registrieren müssen?</p>
                <p>
                  
                </p>
              </td>
              <td>
                <p>Legt fest, ob ein Benutzer bei der nächsten Anmeldung an den Zugriffsbereich Kontaktdaten für die Kennwortzurücksetzung registrieren muss.</p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn <strong>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</strong> auf <strong>Ja</strong> festgelegt ist).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn Sie dieses Feature deaktivieren, können Sie Benutzer auch manuell an <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> verweisen, um ihre Kontaktdaten zu registrieren.  <br><br></li>
                  <li class="unordered">
												Benutzer können das Registrierungsportal auch aufrufen, indem sie im Zugriffsbereich auf der Registerkarte "Profil" auf den Link <strong>Für die Kennwortzurücksetzung registrieren</strong> klicken.<br><br></li>
                  <li class="unordered">
												Die Registrierung über diese Methode kann durch Klicken auf die Schaltfläche "Abbrechen" oder durch Schließen des Fensters verworfen werden. Benutzer werden jedoch bei jeder Anmeldung daran erinnert, wenn sie sich nicht registrieren.<br><br></li>
                </ul>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Diese Einstellung wirkt sich nicht auf das Verhalten des Registrierungsportals selbst aus, sondern legt fest, ob das Registrierungsportal den Benutzern bei der Anmeldung am Zugriffsbereich angezeigt wird.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Anzahl der Tage, bis Benutzer ihre Kontaktdaten bestätigen müssen</p>
              </td>
              <td>
                <p>Wenn <strong>Sollen Benutzer sich registrieren müssen?</strong> aktiviert ist, bestimmt diese Einstellung den Zeitraum, der verstreichen kann, bevor ein Benutzer seine Daten erneut bestätigen muss. </p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn <strong>Sollen sich Benutzer bei der Anmeldung im Zugriffsbereich registrieren müssen?</strong> auf <strong>Ja</strong> gesetzt ist.)</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Hinweis: </strong>
                </p>
                <ul>
                  <li class="unordered">
												Werte zwischen 0 und 730 Tagen werden akzeptiert, wobei 0 Tage bedeutet, dass Benutzer nie zur erneuten Bestätigung ihrer Kontaktdaten aufgefordert werden.<br><br></li>
                </ul>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Diese Einstellung wirkt sich nicht auf das Verhalten des Registrierungsportals selbst aus, sondern legt fest, ob den Benutzern das Registrierungsportal angezeigt wird, wenn ihre Kontaktdaten bestätigt werden müssen. <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Link "Wenden Sie sich an Ihren Administrator." anpassen?</p>
              </td>
              <td>
                <p>Steuert, ob der Link "Wenden Sie sich an Ihren Administrator" (auf der linken Seite gezeigt), der bei Auftreten eines Fehlers oder bei zu langen Wartezeiten für Benutzer im Kennwortzurücksetzungsportal angezeigt wird, auf eine benutzerdefinierte URL oder E-Mail-Adresse verweist.</p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn <strong>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</strong> auf <strong>Ja</strong> festgelegt ist).</p>
              </td>
              <td>
                <p>
                  <strong>Hinweis: </strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn Sie diese Einstellung aktivieren, müssen Sie eine benutzerdefinierte URL oder E-Mail-Adresse auswählen, indem Sie das Feld <strong>Benutzerdefinierte E-Mail-Adresse oder URL</strong> direkt unterhalb dieser Einstellung ausfüllen.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn die Einstellung auf "Nein" gesetzt ist, senden Benutzer beim Klicken auf den hervorgehobenen Link eine E-Mail mit der Anforderung, das Kennwort zurückzusetzen, an die primäre E-Mail-Adresse aller Mandantenadministratoren. Diese E-Mail wird durch die folgende Logik versendet:<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
														Falls Kennwortadministratoren vorhanden sind, wird die E-Mail an alle Kennwortadministratoren, maximal an 100 Empfänger, gesendet.<br><br></li>
                      <li class="unordered">
														Falls keine Kennwortadministratoren vorhanden sind, wird die E-Mail an alle Benutzeradministratoren, maximal an 100 Empfänger, gesendet.<br><br></li>
                      <li class="unordered">
														Falls keine Benutzeradministratoren vorhanden sind, wird die E-Mail an alle globalen Administratoren, maximal an 100 Empfänger, gesendet.<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
												Wenn diese Einstellung auf "Ja" festgelegt ist, passt sie das Verhalten des oben hervorgehobenen Links an, sodass er auf eine benutzerdefinierte URL oder E-Mail-Adresse verweist, unter der Ihre Benutzer Hilfe beim Zurücksetzen des Kennworts erhalten.<br><br></li>
                  <li class="unordered">
												Wenn Sie eine URL angeben, wird diese auf einer neuen Registerkarte geöffnet.<br><br></li>
                  <li class="unordered">
												Wenn Sie eine E-Mail-Adresse angeben, erstellen wir einen mailto-Link zu dieser E-Mail-Adresse.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzerdefinierte E-Mail-Adresse oder URL</p>
              </td>
              <td>
                <p>Steuert die E-Mail-Adresse oder URL, auf die der Link <strong>Wenden Sie sich an Ihren Administrator</strong> verweist. </p>
                <p>
                  
                </p>
                <p>(Nur sichtbar, wenn <strong>Link "Wenden Sie sich an Ihren Administrator." anpassen?</strong> auf <strong>Ja</strong> festgelegt ist.)</p>
              </td>
              <td>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Hierbei muss es sich um eine gültige Intranet- oder Extranet-URL oder E-Mail-Adresse handeln.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Ändert das Ziel des Links <strong>Wenden Sie sich an Ihren Administrator</strong>.<br><br></li>
                  <li class="unordered">
												Wenn Sie eine E-Mail-Adresse angeben, wird der Link zu einem mailto-Link zu dieser E-Mail-Adresse.<br><br></li>
                  <li class="unordered">
												Wenn Sie eine URL angeben, wird der Link zu einem standardmäßigen "href", der auf diese URL verweist. Diese wird auf einer neuen Registerkarte geöffnet.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Kennwörter in lokales Active Directory zurückschreiben</p>
              </td>
              <td>
                <p>Steuert, ob das Rückschreiben von Kennwörtern für dieses Verzeichnis aktiviert ist. In diesem Fall wird der Status des lokalen Rückschreibungsdiensts angezeigt.</p>
                <p>
                  
                </p>
                <p>Dies ist hilfreich, wenn der Dienst aufgrund eines Ausfalls vorübergehend deaktiviert werden soll.</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  <strong>Hinweis:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Dieses Steuerelement wird nur angezeigt, wenn Sie das Rückschreiben von Kennwörtern durch Herunterladen der neuesten Version von Azure AD Connect und durch Aktivieren der Option <strong>Kennwortrückschreiben</strong> im Auswahlbildschirm <strong>Optionale Features</strong> installiert haben.<br><br></li>
                  <li class="unordered">
												Wenn Sie das Rückschreiben von Kennwörtern aktiviert haben und ein Konfigurationsproblem mit dem Dienst vorliegt, können Sie sich auf dieser Registerkarte die Option <strong>Status des Kennwortrückschreibungs-Diensts</strong> ansehen und auf Fehler prüfen.<br><br></li>
                  <li class="unordered">
												Mögliche Statuswerte sind:<br><br><ul><li class="unordered"><strong>Konfiguriert</strong> – Alles funktioniert erwartungsgemäß.<br><br></li><li class="unordered"><strong>Nicht konfiguriert</strong> – Sie haben das Rückschreiben installiert, der Dienst ist jedoch nicht erreichbar. Stellen Sie sicher, dass Sie ausgehende Verbindungen an 443 nicht blockiert haben, und versuchen Sie, den Dienst neu zu installieren, wenn weiterhin Probleme auftreten.<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn das Rückschreiben bereitgestellt und konfiguriert und diese Option auf <strong>Nein</strong> gesetzt ist, wird das Rückschreiben deaktiviert, und Verbundbenutzer und Benutzer mit Kennworthashsynchronisierung können sich nicht für das Zurücksetzen ihrer Kennwörter registrieren.<br><br></li>
                  <li class="unordered">
												Wenn die Option auf <strong>Ja</strong> gesetzt ist, wird das Rückschreiben aktiviert, und Verbundbenutzer und Benutzer mit Kennworthashsynchronisierung können ihre Kennwörter zurücksetzen.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn das Rückschreiben bereitgestellt und konfiguriert und diese Option auf <strong>Nein</strong> gesetzt ist, wird das Rückschreiben deaktiviert, und Verbundbenutzer und Benutzer mit Kennworthashsynchronisierung können ihre Kennwörter nicht zurücksetzen.<br><br></li>
                  <li class="unordered">
												Wenn die Option auf <strong>Ja</strong> gesetzt ist, wird das Rückschreiben aktiviert, und Verbundbenutzer und Benutzer mit Kennworthashsynchronisierung können ihre Kennwörter zurücksetzen.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Benachrichtigungen zur Kennwortverwaltung
In der folgenden Tabelle werden die Auswirkungen der einzelnen Steuerelemente auf die bereitgestellte Funktionalität für Benutzer und Administratoren beschrieben, die Benachrichtigungen zur Kennwortzurücksetzung erhalten. Sie können diese Optionen im Abschnitt **Benachrichtigungen** auf der Registerkarte **Konfigurieren** Ihres Verzeichnisses im [Azure-Verwaltungsportal](https://manage.windowsazure.com) konfigurieren.

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Richtliniensteuerung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Beschreibung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Auswirkungen?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Administratoren benachrichtigen, wenn andere Administratoren ihre eigenen Kennwörter zurücksetzen</p>
              </td>
              <td>
                <p>Legt fest, ob alle globale Administratoren über eine E-Mail an ihre primäre E-Mail-Adresse benachrichtigt werden, sobald ein anderer Administrator eines beliebigen Typs sein eigenes Kennwort zurücksetzt.</p>
              </td>
              <td>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn diese Option auf "Nein" festgelegt ist, werden keine Benachrichtigungen gesendet.<br><br></li>
                  <li class="unordered">
												Wenn diese Option auf "Ja" gesetzt ist, werden alle übrigen globalen Administratoren benachrichtigt, sobald ein einziger Administrator sein eigenes Kennwort zurücksetzt.<br><br></li>
                  <li class="unordered">
												Diese Benachrichtigung wird per E-Mail an die primären E-Mail-Adressen aller übrigen globalen Administratoren in der Organisation gesendet.<br><br></li>
                </ul>
                <p>
                  <strong>Beispiel:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn diese Option aktiviert ist, geschieht Folgendes: Wenn Administrator A sein Kennwort zurücksetzt und auf dem Mandanten drei weitere Administratoren vorliegen (B, C und D), erhalten die Administratoren B, C und D eine E-Mail-Nachricht, die angibt, dass Administrator A sein Kennwort zurückgesetzt hat.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Benutzer und Administratoren benachrichtigen, wenn ihr eigenes Kennwort zurückgesetzt wurde</p>
              </td>
              <td>
                <p>Legt fest, ob Benutzer oder Administratoren, die ihre eigenen Kennwörter zurücksetzen, eine E-Mail-Benachrichtigung erhalten, dass ihr Kennwort zurückgesetzt wurde.</p>
              </td>
              <td>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Wenn diese Option auf "Nein" festgelegt ist, werden keine Benachrichtigungen gesendet.<br><br></li>
                  <li class="unordered">
												Wenn diese Einstellung auf "Ja" festgelegt ist, erhält ein Benutzer oder Administrator nach dem Zurücksetzen des eigenen Kennworts eine Benachrichtigung, dass das Kennwort zurückgesetzt wurde.<br><br></li>
                  <li class="unordered">
												Diese Benachrichtigung wird per E-Mail an die primäre und die alternative (Authentifizierungs-) E-Mail-Adresse des Benutzers gesendet, der das Kennwort zurückgesetzt hat.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/> <br/> <br/>

**Zusätzliche Ressourcen**


* [Was ist die Kennwortverwaltung?](active-directory-passwords.md)
* [Funktionsweise der Kennwortverwaltung](active-directory-passwords-how-it-works.md)
* [Erste Schritte mit der Kennwortverwaltung](active-directory-passwords-getting-started.md)
* [Best Practices für die Kennwortverwaltung](active-directory-passwords-best-practices.md)
* [Operative Einblicke durch Berichte zur Kennwortverwaltung](active-directory-passwords-get-insights.md)
* [Häufig gestellte Fragen zur Kennwortverwaltung](active-directory-passwords-faq.md)
* [Problembehandlung für die Kennwortverwaltung](active-directory-passwords-troubleshoot.md)
* [Weitere Informationen](active-directory-passwords-learn-more.md)
* [Kennwortverwaltung auf MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)

<!---HONumber=Oct15_HO4-->