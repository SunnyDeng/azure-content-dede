<properties umbracoNaviHide="0" pageTitle="Azure AD Integration with Google Apps" metaKeywords="Azure Google Apps Integration" description="Learn about integrating Azure AD with Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Azure AD Integration with Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Azure AD Integration with Google Apps" authors="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Azure AD-Integration in Google Apps

Die diesem Lernprogramm wird die Integration von Azure und Google Apps erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Testkonto in Google Apps

Wenn Sie noch kein gültiges Konto in Google Apps haben, können Sie sich z. B. für ein Testkonto auf der Google Apps for Business-Website anmelden.

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

Aktivieren der Anwendungsintegration für Google Apps
Konfigurieren der einmaligen Anmeldung
Aktivieren des Google Apps-API-Zugriffs
Hinzufügen von benutzerdefinierten Domänen
Konfigurieren der Benutzerbereitstellung

# Aktivieren der Anwendungsintegration für Google Apps

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Google Apps aktivieren.

## So aktivieren Sie die Anwendungsintegration für Google Apps

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.
2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.
3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.
4.  Klicken Sie unten auf der Seite auf **Hinzufügen**, um das Dialogfeld **Anwendung hinzufügen** zu öffnen.
5.  Klicken Sie im Dialogfeld **Integrate an app with Azure AD** auf **Manage access to an application**.
6.  Wählen Sie auf der Seite **Select an application to manage** den Eintrag **Google Apps** in der Liste der Anwendungen aus.
7.  Klicken Sie auf die Schaltfläche **Fertig stellen**, um die Anwendung hinzuzufügen und das Dialogfeld zu schließen.

# Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Google Apps zu authentifizieren.

## So konfigurieren Sie die einmalige Verbundanmeldung

1.  Wählen Sie im linken Navigationsbereich des Azure-Verwaltungsportals **Active Directory** aus, um die Dialogfeldseite "Active Directory" zu öffnen.
2.  Wählen Sie in der Verzeichnisliste das gewünschte Verzeichnis aus, um die zugehörige Konfigurationsseite zu öffnen.
3.  Wählen Sie im Menü der obersten Ebene **ANWENDUNGEN** aus.
4.  Wählen Sie in der Liste der Anwendungen **Google Apps** aus, um das Konfigurationsdialogfeld "Google Apps" zu öffnen.
5.  Klicken Sie auf **Einmaliges Anmelden konfigurieren** um das Dialogfeld **EINMALIGES ANMELDEN KONFIGURIEREN** zu öffnen.

    ![Google\_Tutorial\_01][Google\_Tutorial\_01]

6.  Legen Sie auf der Dialogfeldseite "Select the single sign-on mode for this app" die Option "Users authenticate with their account in Azure AD" als MODUS fest, und klicken Sie dann auf die Schaltfläche "Weiter".

    ![Google\_Tutorial\_02][Google\_Tutorial\_02]

7.  Geben Sie auf der Dialogfeldseite **App-URL konfigurieren** im Textfeld **GOOGLE APPS-MANDANTEN-URL** die Google Apps-Konto-URL ein, und klicken Sie dann auf die Schaltfläche **Weiter**.

    ![Google\_Tutorial\_03][Google\_Tutorial\_03]

8.  Führen Sie auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Google Apps** die folgenden Schritte aus, und klicken Sie dann auf die Schaltfläche **Fertig stellen**.

    -   Klicken Sie auf **Download certificate**, und speichern Sie das Zertifikat unter **c:\\googleapps.cer**.
    -   Öffnen Sie die Google Apps-Anmeldeseite, und melden Sie sich an.

    ![Google\_Tutorial\_04][Google\_Tutorial\_04]

    -   Klicken Sie in der **Admin-Konsole** auf "Security".

    ![Google\_Tutorial\_05][Google\_Tutorial\_05]

    Wenn das Sicherheitssymbol nicht angezeigt wird, klicken Sie unten auf der Seite auf "More controls".

9.  Klicken Sie auf der Seite **Security** auf **Advanced settings**.

    ![Google\_Tutorial\_06][Google\_Tutorial\_06]

10. Wählen Sie im Abschnitt **Advanced settings** der Seite die Option **Set up single sign-on** aus.

    ![Google\_Tutorial\_07][Google\_Tutorial\_07]

11. Führen Sie auf der Seite "Set up single sign-on" die folgenden Schritte aus:

    ![Google\_Tutorial\_08][Google\_Tutorial\_08]

        + Select <strong>Enable Single Sign-on</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>SINGLE SIGN-ON URL</strong>, and then paste it into the related textbox on the <strong>Security page</strong> in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Single sign-out service URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Change password URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + Click the <strong>Browse</strong> button to locate the <strong>Verification certificate</strong>, and then click Upload.
        + Click <strong>Save</strong> changes.

12. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um Google Apps** des Azure AD-Portals auf die Schaltfläche "Fertig stellen".

Nun können Sie den [Zugriffsbereich][Zugriffsbereich] aufrufen und das einmalige Anmelden bei Google Apps testen.

# Aktivieren des Google Apps-API-Zugriffs

Wenn Sie Azure Active Directory zur Benutzerbereitstellung in Google Apps integrieren, müssen Sie den API-Zugriff für Ihr Konto in Google Apps aktivieren.

## So aktivieren Sie den Google Apps-API-Zugriff

1.  Melden Sie sich bei Ihrem Google Apps-Konto an.
2.  Klicken Sie in der **Admin-Konsole** auf **Security**.

    ![Google\_Tutorial\_05][Google\_Tutorial\_05]

    </p>
    Wenn das Sicherheitssymbol nicht angezeigt wird, klicken Sie unten in der Admin-Konsole auf "More controls".

3.  Klicken Sie auf der Seite "Security " auf **API reference**, um die entsprechende Konfigurationsseite des Dialogfelds zu öffnen.
4.  Wählen Sie **Enable API access** aus.

    ![Google\_Tutorial\_09][Google\_Tutorial\_09]

    </p>

# Hinzufügen von benutzerdefinierten Domänen

Um die Benutzerbereitstellung mit Google Apps konfigurieren zu können, müssen die Azure AD-Domäne und die Google Apps-Domäne den gleichen vollqualifizierten Domänennamen (FQDN) verwenden. Wenn Sie jedoch beispielsweise das Szenario in diesem Lernprogramm mit Testkonten testen, sind die FQDNs der Konten in der Regel nicht gleich. Um dieses Problem zu beheben, können Sie benutzerdefinierte Domänen in Azure AD und in Google Apps konfigurieren.
Die Konfiguration einer benutzerdefinierten Domäne erfordert Zugriff auf die DNS-Zonendatei Ihrer öffentlichen Domäne.

![Google\_Tutorial\_10][Google\_Tutorial\_10]

## So fügen Sie eine benutzerdefinierte Domäne in Azure AD hinzu

1.  Wählen Sie im linken Navigationsbereich des Azure-Verwaltungsportals **Active Directory** aus, um die Dialogfeldseite **Active Directory** zu öffnen.
2.  Wählen Sie in der Verzeichnisliste das gewünschte Verzeichnis aus, um die zugehörige Konfigurationsseite zu öffnen.
3.  Wählen Sie im Menü der obersten Ebene **DOMÄNEN** aus.
4.  Um das Textfeld **DOMÄNENNAME HINZUFÜGEN** zu öffnen, geben Sie Ihren Domänennamen ein, und klicken Sie auf **Hinzufügen**.
5.  Klicken Sie auf **Weiter**, um die Dialogfeldseite **Verify your domain name** zu öffnen.

    ![Google\_Tutorial\_11][Google\_Tutorial\_11]

6.  Wählen Sie einen **EINTRAGSTYP** aus, und registrieren Sie dann den ausgewählten Eintrag in Ihrer DNS-Zonendatei.

    ![Google\_Tutorial\_12][Google\_Tutorial\_12]

7.  Überprüfen Sie mit dem **nslookup-Befehl**, ob der DNS-Eintrag erfolgreich registriert wurde.

    ![Google\_Tutorial\_13][Google\_Tutorial\_13]

## So fügen Sie eine benutzerdefinierte Domäne in Google Apps hinzu

1.  Melden Sie sich bei Ihrem Google Apps-Konto an.
2.  Klicken Sie in der **Admin-Konsole** auf **Domains**.

    ![Google\_Tutorial\_14][Google\_Tutorial\_14]

3.  Klicken Sie auf **Add a custom domain**.

    ![Google\_Tutorial\_15][Google\_Tutorial\_15]

4.  Klicken Sie auf **Use a domain you already own** und dann auf **Continue**.

    ![Google\_Tutorial\_16][Google\_Tutorial\_16]

5.  Geben Sie den Namen Ihrer benutzerdefinierten Domäne ein, und klicken Sie auf **Continue**.

    ![Google\_Tutorial\_17][Google\_Tutorial\_17]

6.  Führen Sie die erforderlichen Schritte aus, um zu bestätigen, dass Sie der Eigentümer der Domäne sind.

    Wenn die einmalige Verbundanmeldung für Sie bereits konfiguriert ist, müssen Sie die Google Apps-Konto-URL in der Konfiguration der einmaligen Verbundanmeldung aktualisieren.

# Konfigurieren der Benutzerbereitstellung

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Google Apps aktivieren.

## So konfigurieren Sie die Benutzerbereitstellung

1.  Wählen Sie im linken Navigationsbereich des Azure-Verwaltungsportals **Active Directory** aus, um die Dialogfeldseite **Active Directory** zu öffnen.
2.  Wählen Sie in der Verzeichnisliste das gewünschte Verzeichnis aus, um die zugehörige Konfigurationsseite zu öffnen.
3.  Wählen Sie im Menü der obersten Ebene **ANWENDUNGEN** aus.
    Wählen Sie in der Liste der Anwendungen **Google Apps** aus, um das Konfigurationsdialogfeld **Google Apps** zu öffnen.
4.  Klicken Sie auf **Kontosynchronisierung konfigurieren**, um das Dialogfeld **KONTOSYNCHRONISIERUNG KONFIGURIEREN** zu öffnen.
5.  Geben Sie auf der Dialogfeldseite **KONTOSYNCHRONISIERUNG KONFIGURIEREN** den Google Apps-Domänennamen, den Google Apps-Benutzernamen und das Google Apps-Kennwort an, und klicken Sie dann auf **Weiter**.

    ![Google\_Tutorial\_18][Google\_Tutorial\_18]

6.  Klicken Sie auf der Seite **Bestätigung** auf **Fertig stellen**, um das Dialogfeld **KONTOSYNCHRONISIERUNG KONFIGURIEREN** zu schließen.

Sie können nun ein Testkonto erstellen und nach 10 Minuten prüfen, ob nach Konto nach Google Apps synchronisiert wurde.

Weitere Informationen
[Best Practices for Managing the Application access enhancements for Azure Active Directory
Application Access (Bewährte Methoden zur Verwaltung der Verbesserungen des Anwendungszugriffs für den Azure Active Directory-Anwendungszugriff, in englischer Sprache)][Best Practices for Managing the Application access enhancements for Azure Active Directory
Application Access (Bewährte Methoden zur Verwaltung der Verbesserungen des Anwendungszugriffs für den Azure Active Directory-Anwendungszugriff, in englischer Sprache)]

  [Google\_Tutorial\_01]: ./media/integration-azure-google-apps/Google_Tutorial_01.png
  [Google\_Tutorial\_02]: ./media/integration-azure-google-apps/Google_Tutorial_02.png
  [Google\_Tutorial\_03]: ./media/integration-azure-google-apps/Google_Tutorial_03.png
  [Google\_Tutorial\_04]: ./media/integration-azure-google-apps/Google_Tutorial_04.png
  [Google\_Tutorial\_05]: ./media/integration-azure-google-apps/Google_Tutorial_05.png
  [Google\_Tutorial\_06]: ./media/integration-azure-google-apps/Google_Tutorial_06.png
  [Google\_Tutorial\_07]: ./media/integration-azure-google-apps/Google_Tutorial_07.png
  [Google\_Tutorial\_08]: ./media/integration-azure-google-apps/Google_Tutorial_08.png
  [Zugriffsbereich]: https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633
  [Google\_Tutorial\_09]: ./media/integration-azure-google-apps/Google_Tutorial_09.png
  [Google\_Tutorial\_10]: ./media/integration-azure-google-apps/Google_Tutorial_10.png
  [Google\_Tutorial\_11]: ./media/integration-azure-google-apps/Google_Tutorial_11.png
  [Google\_Tutorial\_12]: ./media/integration-azure-google-apps/Google_Tutorial_12.png
  [Google\_Tutorial\_13]: ./media/integration-azure-google-apps/Google_Tutorial_13.png
  [Google\_Tutorial\_14]: ./media/integration-azure-google-apps/Google_Tutorial_14.png
  [Google\_Tutorial\_15]: ./media/integration-azure-google-apps/Google_Tutorial_15.png
  [Google\_Tutorial\_16]: ./media/integration-azure-google-apps/Google_Tutorial_16.png
  [Google\_Tutorial\_17]: ./media/integration-azure-google-apps/Google_Tutorial_17.png
  [Google\_Tutorial\_18]: ./media/integration-azure-google-apps/Google_Tutorial_18.png
  [Best Practices for Managing the Application access enhancements for Azure Active Directory
  Application Access (Bewährte Methoden zur Verwaltung der Verbesserungen des Anwendungszugriffs für den Azure Active Directory-Anwendungszugriff, in englischer Sprache)]: http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx
