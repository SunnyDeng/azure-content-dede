<properties 
	pageTitle="Erste Schritte: Azure AD-Kennwortverwaltung | Microsoft Azure" 
	description="Ermöglichen Sie es Benutzern, ihre eigenen Kennwörter zurückzusetzen, ermitteln Sie Voraussetzungen für die Kennwortzurücksetzung, und aktivieren Sie das Zurückschreiben von Kennwörtern zum Verwalten lokaler Kennwörter in Active Directory." 
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

# Erste Schritte mit der Kennwortverwaltung
Es sind lediglich einige einfache Schritte erforderlich, um Ihren Benutzern das Verwalten eigener Cloudkennwörter für Azure Active Directory oder lokaler Kennwörter für Active Directory zu ermöglichen. Nachdem Sie sichergestellt haben, dass einige wenige Voraussetzungen erfüllt sind, haben Sie im Handumdrehen eine Kennwortänderung und -zurücksetzung für Ihre gesamte Organisation eingerichtet. In diesem Artikel lernen Sie die folgenden Konzepte kennen:

* [**Aktivieren von Benutzern für das Zurücksetzen ihrer Cloudkennwörter für Azure Active Directory**](#enable-users-to-reset-their-azure-ad-passwords)
 - [Voraussetzungen für die Self-Service-Kennwortzurücksetzung](#prerequisites)
 - [Schritt 1: Konfigurieren der Richtlinie für die Kennwortzurücksetzung](#step-1-configure-password-reset-policy)
 - [Schritt 2: Hinzufügen von Kontaktdaten für Ihren Testbenutzer](#step-2-add-contact-data-for-your-test-user)
 - [Schritt 3: Zurücksetzen Ihres Kennworts als Benutzer](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Aktivieren von Benutzern für das Zurücksetzen oder Ändern ihrer lokalen Active Directory-Kennwörter**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [Voraussetzungen für das Zurückschreiben von Kennwörtern](#writeback-prerequisites)
 - [Schritt 1: Herunterladen der aktuellen Version von Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [Schritt 2: Aktivieren der Kennwortrückschreibung in Azure AD Connect über die Benutzeroberfläche oder PowerShell mit anschließender Überprüfung](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [Schritt 3: Konfigurieren der Firewall](#step-3-configure-your-firewall)
 - [Schritt 4: Einrichten der geeigneten Berechtigungen](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [Schritt 5: Zurücksetzen Ihres AD-Kennworts als Benutzer mit anschließender Überprüfung](#step-5-reset-your-ad-password-as-a-user)

## Aktivieren von Benutzern für das Zurücksetzen ihrer Azure AD-Kennwörter
In diesem Abschnitt werden Sie durch den Vorgang zum Aktivieren der Self-Service-Kennwortzurücksetzung für Ihr AAD-Cloudverzeichnis, das Registrieren von Benutzern für die Self-Service-Kennwortzurücksetzung und das Testen einer Self-Service-Kennwortzurücksetzung als ein Benutzer geleitet.

- [Voraussetzungen für die Self-Service-Kennwortzurücksetzung](#prerequisites)
- [Schritt 1: Konfigurieren der Richtlinie für die Kennwortzurücksetzung](#step-1-configure-password-reset-policy)
- [Schritt 2: Hinzufügen von Kontaktdaten für Ihren Testbenutzer](#step-2-add-contact-data-for-your-test-user)
- [Schritt 3: Zurücksetzen Ihres Kennworts als Benutzer](#step-3-reset-your-azure-ad-password-as-a-user)


###  Voraussetzungen
Bevor Sie die Self-Service-Kennwortzurücksetzung aktivieren und verwenden können, müssen Sie die folgenden Voraussetzungen erfüllen:

- Erstellen Sie einen AAD-Mandanten. Weitere Informationen finden Sie unter [Erste Schritte mit Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).
- Schließen Sie ein Azure-Abonnement ab. Weitere Informationen finden Sie unter [Was ist ein Azure AD-Mandant?](active-directory-administer.md#what-is-an-azure-ad-tenant).
- Ordnen Sie Ihren AAD-Mandanten Ihrem Azure-Abonnement zu. Weitere Informationen zu Azure-Abonnements finden Sie unter [Wie Azure-Abonnements mit Azure AD verknüpft sind](https://msdn.microsoft.com/library/azure/dn629581.aspx).
- Führen Sie ein Upgrade auf Azure AD Premium oder Basic durch. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](http://azure.microsoft.com/pricing/details/active-directory/).

  >[AZURE.NOTE]Um die Self-Service-Kennwortzurücksetzung zu aktivieren, müssen Sie ein Upgrade auf Azure AD Premium oder Basic durchführen. Weitere Informationen finden Sie im Artikel zu den verschiedenen Azure Active Directory-Editionen. Hier finden Sie u. a. ausführliche Informationen dazu, wie Sie sich für Azure AD Premium oder Basic registrieren, wie Sie Ihren Lizenzplan und den Azure AD-Zugriff aktivieren, und wie Sie Administratoren und Benutzerkonten Zugriff gewähren.
  
- Erstellen Sie mindestens ein Administratorkonto und ein Benutzerkonto in Ihrem AAD-Verzeichnis.
- Weisen Sie dem erstellten Administrator- und Benutzerkonto eine AAD Premium- oder Basic-Lizenz zu.

### Schritt 1: Konfigurieren der Richtlinie für die Kennwortzurücksetzung
Führen Sie die folgenden Schritte aus, um die Richtlinie für das Zurücksetzen von Benutzerkennwörtern zu konfigurieren:
 
1.	Öffnen Sie einen Browser Ihrer Wahl, und wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com).
2.	Suchen Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) auf der Navigationsleiste links nach der **Active Directory-Erweiterung**.

    ![][001]

3. Klicken Sie unterhalb der Registerkarte **Verzeichnis** auf das Verzeichnis, in dem Sie die Richtlinie für das Zurücksetzen von Benutzerkennwörtern konfigurieren möchten, beispielsweise Wingtip Toys.

    ![][002]

4.	Klicken Sie auf die Registerkarte **Konfigurieren**.

    ![][003]

5.	Scrollen Sie unterhalb der Registerkarte **Konfigurieren** bis zum Abschnitt **Richtlinie zum Zurücksetzen des Benutzerkennworts**. Hier können Sie alle Aspekte der Richtlinie zum Zurücksetzen von Benutzerkennwörtern für ein vorgegebenes Verzeichnis konfigurieren.

    >[AZURE.NOTE]Diese **Richtlinie gilt nur für Endbenutzer in Ihrer Organisation, nicht für Administratoren**. Aus Sicherheitsgründen steuert Microsoft die Richtlinie zum Zurücksetzen von Administratorkennwörtern. Wenn dieser Abschnitt nicht angezeigt wird, stellen Sie sicher, dass Sie sich für Azure Active Directory Premium oder Basic registriert und dem für das Konfigurieren dieser Funktion verwendete Administratorkonto **eine Lizenz zugewiesen** haben.

    ![][004]

6.	Legen Sie zum Konfigurieren der Richtlinie für das Zurücksetzen von Benutzerkennwörtern die Umschalttaste **Benutzer sind für das Zurücksetzen des Kennworts aktiviert** auf **Ja** fest. Es werden verschiedene weitere Steuerelemente angezeigt, mit deren Hilfe Sie die Funktionsweise dieses Features in Ihrem Verzeichnis konfigurieren können. Passen Sie die Kennwortzurücksetzung nach Ihren Anforderungen an. Wenn Sie mehr über die verschiedenen Steuerelemente zum Anpassen der Richtlinie für die Kennwortzurücksetzung wissen möchten, lesen Sie den Artikel [Anpassen: Azure AD-Kennwortverwaltung](active-directory-passwords-customize).

    ![][005]

7.	Nachdem Sie die Richtlinie für die Kennwortzurücksetzung wie gewünscht für Ihren Mandanten konfiguriert haben, klicken Sie im unteren Bereich des Bildschirms auf **Speichern**.

  >[AZURE.NOTE]Es wird empfohlen, eine Richtlinie für die Kennwortzurücksetzung mit einer Überprüfung in zwei Schritten zu verwenden, damit Sie sehen können, wie die Richtlinie in sehr komplexen Szenarien funktioniert.

  ![][006]

### Schritt 2: Hinzufügen von Kontaktdaten für Ihren Testbenutzer
Sie haben mehrere Möglichkeiten, für Organisationsbenutzer Daten einzugeben, die für das Zurücksetzen von Kennwörtern verwendet werden.

-	Bearbeiten Sie Benutzer im [Azure-Verwaltungsportal](https://manage.windowsazure.com) oder im [Office 365-Verwaltungsportal](https://portal.microsoftonline.com).
-	Verwenden Sie AAD Connect, um Benutzereigenschaften aus einer lokalen Active Directory-Domäne mit Azure AD zu synchronisieren.
-	Verwenden Sie Windows PowerShell zum Bearbeiten der Benutzereigenschaften.
-	Ermöglichen Sie es Benutzern, ihre eigenen Daten zu registrieren, indem Sie sie auf das Registrierungsportal unter [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) weiterleiten.
-	Legen Sie die Benutzerregistrierung für die Kennwortzurücksetzung bei Anmeldung beim Zugriffsbereich unter [http://myapps.microsoft.com](http://myapps.microsoft.com) als erforderlich fest, indem Sie die Option für die SSPR-Konfiguration **Sollen sich Benutzer bei der Anmeldung beim Zugriffsbereich registrieren müssen?** auf **Ja** festlegen.

Weitere Informationen dazu, welche Daten beim Zurücksetzen von Kennwörtern verwendet werden und welche Formatierungsanforderungen für diese Daten gelten, finden Sie unter [Welche Daten werden bei der Kennwortzurücksetzung verwendet?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### So fügen Sie über das Benutzerregistrierungsportal Benutzerkontaktdaten hinzu
1.	Um das Registrierungsportal für die Kennwortzurücksetzung zu verwenden, müssen Sie für die Benutzer in Ihrer Organisation einen Link auf diese Seite bereitstellen ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) oder die Option zur Anforderung einer automatischen Benutzerregistrierung aktivieren. Wenn die Benutzer auf diesen Link klicken, werden sie aufgefordert, sich mit ihrem Organisationskonto anzumelden. Anschließend wird die folgende Seite angezeigt:

    ![][007]

2.	Hier können Benutzer ihre Mobiltelefonnummer, eine alternative E-Mail-Adresse oder Sicherheitsfragen angeben. Der folgende Screenshot zeigt Überprüfung mit einem Mobiltelefon.

    ![][008]

3.	Nachdem ein Benutzer diese Informationen angegeben hat, wird die Seite aktualisiert um anzuzeigen, dass die Informationen gültig sind (nachstehend geschwärzt). Durch Klicken auf die Schaltflächen **Fertig stellen** oder **Abbrechen** wird der Benutzer um Zugriffsbereich weitergeleitet.

    ![][009]

4.	Nachdem ein Benutzer die Informationen bestätigt hat, wird das Profil mit den bereitgestellten Daten aktualisiert. In diesem Beispiel wurde die Nummer für **Bürotelefon** manuell eingegeben, deshalb kann der Benutzer diese Nummer auch als Kontaktmethode für das Zurücksetzen des Kennworts verwenden.

    ![][010]

### Schritt 3: Zurücksetzen Ihres AD-Kennworts als Benutzer mit anschließender Überprüfung
Nachdem Sie eine Richtlinie für die Kennwortzurücksetzung konfiguriert und Kontaktdaten für Ihren Benutzer angegeben haben, kann dieser Benutzer eine Self-Service-Kennwortzurücksetzung durchführen.

#### So führen Sie eine Self-Service-Kennwortzurücksetzung durch
1.	Wenn Sie zu einer Website wie [**portal.microsoftonline.com**](http://portal.microsoftonline.com) wechseln, wird ein Anmeldebildschirm wie der folgende angezeigt. Klicken Sie auf den Link **Können Sie nicht auf Ihr Konto zugreifen?**, um die Benutzeroberfläche für die Kennwortzurücksetzung zu testen.

    ![][011]

2.	Nach dem Klicken auf **Können Sie nicht auf Ihr Konto zugreifen?** werden Sie auf eine neue Seite umgeleitet, auf der Sie gefragt werden, für welche **Benutzer-ID** Sie ein Kennwort zurücksetzen möchten. Geben Sie hier Ihre Test-**Benutzer-ID** ein, führen Sie die Captchaprüfung aus, und klicken Sie auf **Weiter**.

    ![][012]

3.	Da der Benutzer in diesem Fall ein **Bürotelefon**, ein **Mobiltelefon** und eine **alternative E-Mail-Adresse** angegeben hat, werden dem Benutzer für die erste Überprüfung alle diese Optionen angeboten.

    ![][013]

4.	Wählen Sie in diesem Fall zunächst die Option für einen **Anruf** beim **Bürotelefon** aus. Beachten Sie, dass der Benutzer bei Auswahl einer telefonbasierten Methode zunächst gebeten wird, die **Telefonnummer zu überprüfen**, bevor das Kennwort zurückgesetzt werden kann. Auf diese Weise wird verhindert, dass böswillige Benutzer die Telefonnummern von Benutzern in Ihrer Organisation nicht für ein Spamming missbrauchen.

    ![][014]

5.	Nachdem der Benutzer die Telefonnummer bestätigt hat, erscheint nach Auswahl der Anrufoption ein Drehfeld, und das Telefon des Benutzers klingelt. Sobald der Benutzer den Hörer abhebt, wird eine Nachricht abgespielt. In dieser wird der Benutzer aufgefordert, zum Bestätigen des Kontos **die #-Taste zu drücken**. Durch das Drücken der #-Taste wird automatisch bestätigt, dass der Benutzer den ersten Überprüfungsschritt erfolgreich ausgeführt hat, und die Benutzeroberfläche wechselt zum zweiten Überprüfungsschritt.

    ![][015]

6.	Nachdem Sie den ersten Überprüfungsschritt erfolgreich ausgeführt haben, wird die Benutzeroberfläche automatisch aktualisiert, um die erste Überprüfungsmethode aus der Auswahlliste zu entfernen. Da Sie in diesem Fall Ihr **Bürotelefon** für den ersten Überprüfungsschritt verwendet haben, verbleiben nur **Mobiltelefon** und **Alternative E-Mail-Adresse** als Optionen für den zweiten Überprüfungsschritt. Klicken Sie auf die E-Mail-Option **E-Mail an meine alternative E-Mail-Adresse senden**. Nachdem Sie dies getan haben, wird beim Klicken auf "E-Mail" eine E-Mail an die alternative E-Mail-Adresse gesendet.

    ![][016]

7.	Nachstehend sehen Sie ein Beispiel für eine E-Mail, die der Benutzer erhält. Beachten Sie hierbei das Branding für den Mandanten:

    ![][017]

8.	Nach dem Empfang der E-Mail wird die Seite aktualisiert, und Sie können den in der E-Mail enthaltenen Code in das nachstehend gezeigte Textfeld eingeben. Nach der erfolgreichen Eingabe des Codes wird die Schaltfläche "Weiter" angezeigt, und Sie können den zweiten Überprüfungsschritt abschließen.

    ![][018]

9.	Nachdem Sie die Anforderungen der Organisationsrichtlinie erfüllt haben, sind Sie jetzt berechtigt, ein neues Kennwort auszuwählen. Das Kennwort muss die AAD-Anforderungen für sichere Kennwörter erfüllen (siehe [Kennwortrichtlinie in Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)). Ein Indikator für die Kennwortsicherheit zeigt dem Benutzer an, ob das eingegebene Kennwort dieser Richtlinie entspricht.

    ![][019]

10.	Nachdem Sie übereinstimmende Kennwörter eingegeben haben, die der Organisationsrichtlinie entsprechen, wird das Kennwort zurückgesetzt, und Sie können sich sofort mit dem neuen Kennwort anmelden.

    ![][020]


## Aktivieren von Benutzern für das Zurücksetzen ihrer AD-Kennwörter

In diesem Abschnitt werden Sie durch den Vorgang zum Konfigurieren der Kennwortzurücksetzung für das Zurückschreiben von Kennwörtern in ein lokales Active Directory-Verzeichnis geleitet.

- [Voraussetzungen für das Zurückschreiben von Kennwörtern](#writeback-prerequisites)
- [Schritt 1: Herunterladen der aktuellen Version von Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
- [Schritt 2: Aktivieren der Kennwortrückschreibung in Azure AD Connect über die Benutzeroberfläche oder PowerShell mit anschließender Überprüfung](#step-2-enable-password-writeback-in-azure-ad-connect)
- [Schritt 3: Konfigurieren der Firewall](#step-3-configure-your-firewall)
- [Schritt 4: Einrichten der geeigneten Berechtigungen](#step-4-set-up-the-appropriate-active-directory-permissions)
- [Schritt 5: Zurücksetzen Ihres AD-Kennworts als Benutzer mit anschließender Überprüfung](#step-5-reset-your-ad-password-as-a-user)


### Voraussetzungen für das Zurückschreiben von Kennwörtern
Bevor Sie das Zurückschreiben von Kennwörtern aktivieren und verwenden können, müssen Sie die folgenden Voraussetzungen erfüllen:

- Sie verfügen über einen Azure AD-Mandanten, für den Azure AD Premium aktiviert ist. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).
- Für Ihren Mandanten wurde die Kennwortzurücksetzung konfiguriert und aktiviert. Weitere Informationen finden Sie unter [Aktivieren von Benutzern für das Zurücksetzen ihrer Azure AD-Kennwörter](#enable-users-to-reset-their-azure-ad-passwords).
- Sie verfügen über mindestens ein Administratorkonto und ein Testbenutzerkonto mit einer Azure AD Premium-Lizenz zum Testen dieser Funktion. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

  >[AZURE.NOTE]Stellen Sie sicher, dass es sich bei dem zum Aktivieren der Kennwortrückschreibung verwendeten Administratorkonto um ein Cloudadministratorkonto handelt (erstellt in Azure AD), und nicht um ein Verbundkonto (erstellt im lokalen AD-Verzeichnis und synchronisiert mit Azure AD).
  
- Sie verfügen über eine lokale AD-Bereitstellung mit einer oder mehreren Gesamtstrukturen, in der Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 oder Windows Server 2012 R2 mit den aktuellen Service Packs ausgeführt wird.

  >[AZURE.NOTE]Wenn Sie eine ältere Version von Windows Server 2008 oder 2008 R2 ausführen, können Sie diese Funktion verwenden, müssen aber [KB 2386717 herunterladen und installieren](https://support.microsoft.com/kb/2386717), bevor Sie Ihre lokale AD-Kennwortrichtlinie in der Cloud erzwingen können.
  
- Sie haben Azure AD Connect installiert und Ihre AD-Umgebung für eine Synchronisierung mit der Cloud vorbereitet. Weitere Informationen finden Sie unter [Verwenden Ihrer lokalen Identitätsinfrastruktur in der Cloud](active-directory-aadconnect.md).
- Wenn Sie DirSync verwenden, müssen Sie sicherstellen, dass die Unternehmensfirewall so konfiguriert ist, dass ausgehende Verbindungen blockiert werden und **TCP-Port 828 oder 818** geöffnet ist, um das Zurückschreiben von Kennwörtern zu aktivieren und zu nutzen. Wenn Sie Azure AD Sync oder Azure AD Connect verwenden, ist dieser Schritt nicht erforderlich, da nur **TCP 443** ausgehend (und in einigen Fällen **TCP 9350-9354**) geöffnet werden müssen.

  >[AZURE.NOTE]Es wird dringend empfohlen, bei Verwendung von Azure AD Sync oder DirSync ein Upgrade auf die neueste Version von Azure AD Connect durchzuführen, um eine bestmögliche Funktionalität sicherzustellen und von neuen Features zu profitieren.
  

### Schritt 1: Herunterladen der aktuellen Version von Azure AD Connect
Das Zurückschreiben von Kennwörtern steht in Versionen von Azure AD Connect oder im Azure AD Sync-Tool mit Versionsnummer **1.0.0419.0911** oder höher zur Verfügung. Das Zurückschreiben von Kennwörtern mit automatischer Entsperrung von Konten steht in Versionen von Azure AD Connect oder im Azure AD Sync-Tool mit Versionsnummer **1.0.0485.0222** oder höher zur Verfügung. Wenn Sie eine ältere Version verwenden, führen Sie ein Upgrade auf diese oder eine höhere Version durch, bevor Sie fortfahren. [Klicken Sie hier, um die aktuelle Version von Azure AD Connect herunterzuladen](active-directory-aadconnect.md#download-azure-ad-connect).

#### So überprüfen Sie die Version von Azure AD Sync
1.	Navigieren Sie zu **%ProgramFiles%\\Azure Active Directory Sync**. 2.	Suchen Sie nach der ausführbaren Datei **ConfigWizard.exe**.
3.	Klicken Sie mit der rechten Maustaste auf die ausführbare Datei, und wählen Sie im Kontextmenü die Option **Eigenschaften** aus.
4.	Klicken Sie auf die Registerkarte **Details**.
5.	Suchen Sie nach dem Feld **Dateiversion**.

    ![][021]

Wenn die angezeigte Versionsnummer größer oder gleich **1.0.0419.0911** ist, oder wenn Sie Azure AD Connect installieren, können Sie zu [Schritt 2: Aktivieren der Kennwortrückschreibung in Azure AD Connect über die Benutzeroberfläche oder PowerShell mit anschließender Überprüfung](#step-2-enable-password-writeback-in-azure-ad-connect) wechseln.

 >[AZURE.NOTE]Wenn Sie Azure AD Connect zum ersten Mal installieren, wird empfohlen, die Best Practices zum Vorbereiten Ihrer Umgebung auf eine Verzeichnissynchronisierung zu befolgen. Bevor Sie Azure AD Connect installieren, müssen Sie entweder im [Office 365-Verwaltungsportal](https://portal.microsoftonline.com) oder im [Azure-Verwaltungsportal](https://manage.windowsazure.com) die Verzeichnissynchronisierung aktivieren. Weitere Informationen finden Sie unter [Verwalten von Azure AD Connect](active-directory-aadconnect-whats-next.md).


### Schritt 2: Aktivieren der Kennwortrückschreibung in Azure AD Connect
Nun, da Sie das Azure AD Connect-Tool heruntergeladen haben, können Sie das Zurückschreiben von Kennwörtern aktivieren. Hierzu stehen zwei Möglichkeiten zur Verfügung. Sie können das Zurückschreiben von Kennwörtern entweder im Bildschirm für die optionalen Features im Setup-Assistenten für Azure AD Connect aktivieren, oder Sie verwenden zur Aktivierung Windows PowerShell.

#### So aktivieren Sie das Zurückschreiben von Kennwörtern im Konfigurations-Assistenten
1.	Öffnen Sie auf Ihrem **Computer für die Verzeichnissynchronisierung** den **Azure AD Connect**-Konfigurations-Assistenten.
2.	Klicken Sie durch die Schritte, bis Sie zum Konfigurationsbildschirm **Optionale Features** gelangen.
3.	Aktivieren Sie die Option **Kennwort zurückschreiben**.

    ![][022]

4.	Schließen Sie den Assistenten ab. Auf der letzten Seite werden die Änderungen zusammengefasst. Dort wird auch die Änderung zur Aktivierung der Kennwortrückschreibung angezeigt.

> [AZURE.NOTE]Sie können die Funktion zum Zurückschreiben von Kennwörtern jederzeit deaktivieren, indem Sie diesen Assistenten erneut ausführen, oder indem Sie die Einstellung **Kennwörter in das lokale Verzeichnis zurückschreiben** auf [Nein](https://manage.windowsazure.com) festlegen. Sie finden diese Einstellung im **Azure-Verwaltungsportal** auf der Registerkarte **Konfigurieren** im Abschnitt **Richtlinie zum Zurücksetzen des Benutzerkennworts**. Weitere Informationen zum Anpassen Ihrer Konfiguration für die Kennwortzurücksetzung finden Sie unter [Anpassen: Azure AD-Kennwortverwaltung](active-directory-passwords-customize.md).

#### So aktivieren Sie das Zurückschreiben von Kennwörtern mithilfe von Windows PowerShell
1.	Öffnen Sie auf Ihrem **Computer für die Verzeichnissynchronisierungscomputer** ein neues **Windows PowerShell-Fenster mit erhöhten Rechten**.
2.	Wenn das Modul nicht bereits geladen ist, geben Sie den `Import-Module ADSync`-Befehl ein, um die Azure AD Connect-Cmdlets in Ihre aktuelle Sitzung zu laden.
3.	Rufen Sie eine Liste der AAD-Connectors in Ihrem System ab, indem Sie das `Get-ADSyncConnector`-Cmdlet ausführen und die Ergebnisse in `$aadConnectorName` speichern.
4.	Rufen Sie den aktuellen Status der Rückschreibung für den aktuellen Connector ab, indem Sie das folgende Cmdlet ausführen: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.	Aktivieren Sie das Zurückschreiben von Kennwörtern, indem Sie dieses Cmdlet ausführen: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE]Stellen Sie bei der Aufforderung zur Eingabe von Anmeldeinformationen sicher, dass es sich bei dem für "AzureADCredential" angegebenen Administratorkonto um ein **Cloudadministratorkonto (erstellt in Azure AD)** handelt, und nicht um ein Verbundkonto (erstellt im lokalen AD-Verzeichnis und synchronisiert mit Azure AD).[AZURE.NOTE]Sie können die Funktion zum Zurückschreiben von Kennwörtern über PowerShell deaktivieren, indem Sie die obigen Anweisungen erneut ausführen, aber hierbei `$false` übergeben, oder indem Sie die Einstellung [Kennwörter in das lokale Verzeichnis zurückschreiben](https://manage.windowsazure.com) auf **Nein** festlegen. Sie finden diese Einstellung im **Azure-Verwaltungsportal** auf der Registerkarte **Konfigurieren** im Abschnitt **Richtlinie zum Zurücksetzen des Benutzerkennworts**.

#### Überprüfen, ob die Konfiguration erfolgreich war
Nachdem Sie die Konfiguration abgeschlossen haben, wird im Windows PowerShell-Fenster die Meldung "Das Zurückschreiben der Kennwortzurücksetzung ist aktiviert" angezeigt, in der Konfigurationsoberfläche wird eine Erfolgsmeldung eingeblendet.

Sie können die erfolgreiche Installation des Diensts auch überprüfen, indem Sie die Ereignisanzeige öffnen, zum Anwendungsereignisprotokoll navigieren und in der Quelle **PasswordResetService** nach dem Ereignis **31005 - OnboardingEventSuccess** suchen.

  ![][023]

### Schritt 3: Konfigurieren der Firewall
Nachdem Sie das Zurückschreiben von Kennwörtern in Azure AD Connect aktiviert haben, müssen Sie sicherstellen, dass der Dienst eine Verbindung mit der Cloud herstellen kann.

1.	Wenn Sie nach dem Abschluss der Installation in Ihrer Umgebung unbekannte ausgehende Verbindungen blockieren, müssen Sie außerdem Änderungen an den folgenden Regeln Ihrer Firewall vornehmen. Stellen Sie sicher, dass Sie Ihren AAD Connect-Computer nach dem Durchführen dieser Änderungen neu starten:
   - Lassen Sie ausgehende Verbindungen über TCP-Port 443 zu.
   - Lassen Sie ausgehende Verbindungen mit https://ssprsbprodncu-sb.accesscontrol.windows.net/ zu. 
   - Wenn Sie einen Proxy verwenden oder allgemeine Verbindungsprobleme vorliegen, lassen Sie ausgehende Verbindungen über TCP-Port 9350-9534 zu.

### Schritt 4: Einrichten der geeigneten Active Directory-Berechtigungen
Bei der anfänglichen Konfiguration mit dem Konfigurations-Assistenten wird ein Konto X eingerichtet. Für jede Gesamtstruktur, die Benutzer enthält, deren Kennwörter zurückgesetzt werden, müssen X die Berechtigungen **Kennwort zurücksetzen**, **Kennwort ändern**, **Kennwort schreiben** für `lockoutTime` sowie die erweiterten Rechte **Berechtigungen schreiben** für `pwdLastSet` für das Stammobjekt jeder Domäne in dieser Gesamtstruktur erteilt werden. Die Rechte müssen hierbei so festgelegt werden, dass sie an alle Benutzerobjekte vererbt werden.

Durch das Festlegen dieser Berechtigungen kann das MA-Dienstkonto für jede Gesamtstruktur Kennwörter im Namen der Benutzerkonten innerhalb dieser Gesamtstruktur verwalten. Wenn Sie diese Berechtigungen nicht gewähren, erhalten die Benutzer – obwohl das Zurückschreiben scheinbar ordnungsgemäß konfiguriert ist – Fehler bei dem Versuch, ihre lokalen Kennwörter über die Cloud verwalten. Nachfolgend finden Sie eine detaillierte Schrittanleitung zum Gewähren dieser Berechtigungen im MMC-Snap-In **Active Directory-Benutzer und -Gruppen**.

>[AZURE.NOTE]Es kann bis zu einer Stunde dauern, bis diese Berechtigungen auf alle Objekte in Ihrem Verzeichnis repliziert wurden.

#### So richten Sie die geeigneten Berechtigungen für das Zurückschreiben von Kennwörtern ein

1.	Öffnen Sie **Active Directory-Benutzer und -Computer** mit einem Konto, das über geeignete Berechtigungen für die Domänenverwaltung verfügt.
2.	Vergewissern Sie sich, dass im **Menü "Ansicht"** die Option **Erweiterte Features** aktiviert ist.
3.	Klicken Sie im linken Bereich mit der rechten Maustaste auf das Objekt, das den Stamm der Domäne repräsentiert.
4.	Klicken Sie auf die Registerkarte **Sicherheit**.
5.	Klicken Sie dann auf **Erweitert**.

    ![][024]

6.	Klicken Sie auf der Registerkarte **Berechtigungen** auf **Hinzufügen**.

    ![][025]

7.	Wählen Sie das Konto aus, dem Sie Berechtigungen hinzufügen möchten (es sollte sich um dasselbe Konto handeln, das beim Einrichten der Synchronisierung für diese Gesamtstruktur angegeben wurde).
8.	Wählen Sie in der Dropdownliste oben den Eintrag **Nachfolgerbenutzerobjekt**.
9.	Aktivieren Sie im nun angezeigten Dialogfeld **Berechtigungseintrag** die Kontrollkästchen für **Kennwort zurücksetzen**, **Kennwort ändern**, **Berechtigungen schreiben** für `lockoutTime` sowie **Berechtigungen schreiben** für `pwdLastSet`.

    ![][026] ![][027] ![][028]

10.	Klicken Sie anschließend in allen geöffneten Dialogfeldern auf **Anwenden/OK**.

### Schritt 5: Zurücksetzen Ihres AD-Kennworts als Benutzer
Jetzt, da das Zurückschreiben von Kennwörtern aktiviert ist, können Sie die ordnungsgemäße Funktion testen, indem Sie das Kennwort eines Benutzers zurücksetzen, dessen Konto mit Ihrem Cloudmandanten synchronisiert wurde.
 
#### So prüfen Sie, ob das Zurückschreiben von Kennwörtern ordnungsgemäß funktioniert
1.	Navigieren Sie zu [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com), oder wechseln Sie zu einem beliebigen Organisations-ID-Anmeldebildschirm, und klicken Sie auf den Link **Können Sie nicht auf Ihr Konto zugreifen?**.

    ![][029]

2.	Sie sollten jetzt eine neue Seite sehen, in der Sie nach der Benutzer-ID gefragt werden, für die Sie ein Kennwort zurücksetzen möchten. Geben Sie die ID des Testbenutzers ein, und führen Sie die Schritte zur Kennwortzurücksetzung aus.
3.	Nachdem Sie Ihr Kennwort zurückgesetzt haben, wird ein Bildschirm angezeigt, der dem folgenden ähnelt. Diese Anzeige weist darauf hin, dass Sie Ihr Kennwort im lokalen Verzeichnis und/oder in den Cloudverzeichnissen erfolgreich zurückgesetzt haben.

    ![][030]

4.	Um zu überprüfen, ob der Vorgang erfolgreich war, oder ob Fehler ermittelt wurden, öffnen Sie auf Ihrem **Computer für die Verzeichnissynchronisierung** die **Ereignisanzeige**, navigieren Sie zum **Anwendungsereignisprotokoll**, und suchen Sie nach dem Ereignis **31002 - PasswordResetSuccess** mit der Quelle **PasswordResetService** für Ihren Testbenutzer.

    ![][031]


<br/> <br/> <br/>

**Zusätzliche Ressourcen**


* [Was ist die Kennwortverwaltung?](active-directory-passwords.md)
* [Funktionsweise der Kennwortverwaltung](active-directory-passwords-how-it-works.md)
* [Anpassen der Kennwortverwaltung](active-directory-passwords-customize.md)
* [Best Practices für die Kennwortverwaltung](active-directory-passwords-best-practices.md)
* [Operative Einblicke durch Berichte zur Kennwortverwaltung](active-directory-passwords-get-insights.md)
* [Häufig gestellte Fragen zur Kennwortverwaltung](active-directory-passwords-faq.md)
* [Problembehandlung für die Kennwortverwaltung](active-directory-passwords-troubleshoot.md)
* [Weitere Informationen](active-directory-passwords-learn-more.md)
* [Kennwortverwaltung auf MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"

 

<!---HONumber=July15_HO3-->