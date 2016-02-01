<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Citrix ShareFile | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Citrix ShareFile mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Lernprogramm: Azure Active Directory-Integration mit Citrix-ShareFile

In diesem Lernprogramm wird die Integration von Azure und Citrix ShareFile erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Citrix ShareFile-Mandanten

Nach Abschluss dieses Lernprogramms können sich die Citrix ShareFile zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf der Citrix ShareFile-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie auch die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Citrix ShareFile
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Szenario")
##Aktivieren der Anwendungsintegration für Citrix ShareFile

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Citrix ShareFile aktivieren.

###So aktivieren Sie die Anwendungsintegration für Citrix ShareFile

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Citrix ShareFile** ein.

    ![Anwendungskatalog](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Citrix ShareFile** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Citrix ShareFile zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Citrix ShareFile** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Einmaliges Anmelden aktivieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Citrix ShareFile anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **Anmelde-URL für Citrix ShareFile** die URL im Format `https://<tenant-name>.shareFile.com` ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um Citrix ShareFile** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der **Citrix ShareFile**-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben auf der Symbolleiste auf **Admin**.

7.  Klicken Sie im linken Navigationsbereich auf **Configure Single Sign-On**.

    ![Kontoverwaltung](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Kontoverwaltung")

8.  Gehen Sie auf der Dialogseite **Single Sign-On/ SAML 2.0 Configuration** unter **Basic Settings** folgendermaßen vor:

    ![Einmaliges Anmelden](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Einmaliges Anmelden")

    1.  Aktivieren Sie **Enable SAML**.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Citrix ShareFile ** den Wert der **Entitäts-ID**, und fügen Sie ihn in das Textfeld **Your IDP Issuer/ Entity ID** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Citrix ShareFile** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Login URL** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Citrix ShareFile** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Logout URL** ein.
    5.  Klicken Sie auf **Change** neben dem Feld **X.509 Certificate**, und laden Sie dann das vom Azure AD-Portal heruntergeladene Zertifikat hoch.![Basic Settings](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Basic Settings")

9.  Klicken Sie im Citrix ShareFile-Verwaltungsportal auf **Save**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Configure single sign-on](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Citrix ShareFile anmelden können, müssen sie in Citrix ShareFile bereitgestellt werden. Im Fall von Citrix ShareFile ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Citrix ShareFile**-Mandanten an.

2.  Klicken Sie auf **Manage Users > Manage Users Home > + Create Employee**.

    ![Create Employee](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Create Employee")

3.  Geben Sie **EMail Address**, **First Name** und **Last Name** eines gültigen Azure AD-Kontos ein, das Sie bereitstellen möchten.

    ![Basic Information](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Basic Information")

4.  Klicken Sie auf **Add User**.

    >[AZURE.NOTE]Der Besitzer des AAD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Citrix ShareFile-Benutzerkonten oder mithilfe der von Citrix ShareFile bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Citrix ShareFile Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Citrix ShareFile** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->