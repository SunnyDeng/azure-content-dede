<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Replicon | Microsoft Azure" description="Hier erfahren Sie, wie Sie Replicon mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Replicon
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=525008), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Replicon erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Replicon-Mandant
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Replicon zugewiesen haben, mittels einmaliger Anmeldung auf der Replicon-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Replicon
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-replicon-tutorial/IC777798.png "Szenario")
##Aktivieren der Anwendungsintegration für Replicon
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Replicon aktivieren.

###So aktivieren Sie die Anwendungsintegration für Replicon:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-replicon-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-replicon-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-replicon-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Replicon** ein.

    ![Anwendungskatalog](./media/active-directory-saas-replicon-tutorial/IC777799.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Replicon** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Replicon zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Replicon** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-replicon-tutorial/IC777801.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Replicon anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-replicon-tutorial/IC777802.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-replicon-tutorial/IC777803.png "App-URL konfigurieren")

    1.  Geben Sie in das Textfeld **Replicon-Anmelde- URL** Ihre Replicon-Mandanten-URL ein (z. B.: **https://na2.replicon.com/company/saml2/sp-sso/post*).
2.  Geben Sie im Textfeld **Replicon-Antwort-URL** Ihre **AssertionConsumerService-URL** für Replicon ein (z. B. **https://global.replicon.com/!/saml2/company/sso/post*).

        >[AZURE.NOTE]Sie können die URL aus den Replicon-Metadaten unter: **https://global.replicon.com/!/saml2/\< YourCompanyKey >** abrufen.

    3.  Klicken Sie auf **Weiter**.

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Replicon** auf **Metadaten herunterladen**, und speichern Sie die Metadaten auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-replicon-tutorial/IC777804.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Replicon-Unternehmenswebsite als Administrator an.

6.  Um SAML 2.0 zu konfigurieren, führen Sie die folgenden Schritte aus:

    ![Klicken Sie auf „SAML-Authentifizierung aktivieren“.](./media/active-directory-saas-replicon-tutorial/IC777805.png "Klicken Sie auf „SAML-Authentifizierung aktivieren“.")

    1.  Zum Anzeigen des Dialogfeldes **EnableSAML Authentication2** hängen Sie Folgendes hinter Ihrem Firmenschlüssel. **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2** an. Nachfolgend wird das Schema das Schema der vollständigen URL angezeigt: **https://na2.replicon.com/\< YourCompanyKey > /services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
    2.  Klicken Sie auf **+**, um den Abschnitt **v20Configuration** auszuklappen.
    3.  Klicken Sie auf **+**, um den Abschnitt **metaDataConfiguration** auszuklappen.
    4.  Klicken Sie auf **Datei auswählen**, um die Metadaten-XML-Datei Ihres Identitätsanbieters auszuwählen. Klicken Sie dann auf **Senden**.

7.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-replicon-tutorial/IC778418.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Replicon anmelden können, müssen sie in Replicon bereitgestellt werden. Im Fall von Replicon ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich in einem Webbrowserfenster bei der Replicon-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Verwaltung > Benutzer**.

    ![Benutzer](./media/active-directory-saas-replicon-tutorial/IC777806.png "Benutzer")

3.  Klicken Sie auf **+Benutzer**.

    ![Benutzer hinzufügen](./media/active-directory-saas-replicon-tutorial/IC777807.png "Benutzer hinzufügen")

4.  Führen Sie im Abschnitt **Benutzerprofil** die folgenden Schritte aus:

    ![Benutzerprofil](./media/active-directory-saas-replicon-tutorial/IC777808.png "Benutzerprofil")

    1.  Geben Sie im Textfeld **Anmeldungsname** die E-Mail-Adresse des Azure AD-Benutzers ein, den Sie bereitstellen möchten.
    2.  Als **Authentifizierungstyp** wählen Sie **SSO** aus.
    3.  Geben Sie in das Textfeld **Abteilung** die Abteilung des Benutzers ein.
    4.  Wählen Sie als **Mitarbeitertyp** die Option **Administrator** aus.
    5.  Klicken Sie auf **Benutzerprofil speichern**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Replicon-Benutzerkonten oder mithilfe der von Replicon bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Replicon Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Replicon** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-replicon-tutorial/IC777809.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-replicon-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->