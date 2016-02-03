<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit AppDynamics | Microsoft Azure" 
    description="Erfahren Sie, wie Sie AppDynamics mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit AppDynamics

In diesem Lernprogramm wird die Integration von Azure und AppDynamics erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein AppDynamics-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms können sich die AppDynamics zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer AppDynamics-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie auch die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für AppDynamics
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Szenario")
##Aktivieren der Anwendungsintegration für AppDynamics

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für AppDynamics aktivieren.

###So aktivieren Sie die Anwendungsintegration für AppDynamics

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **AppDynamics** ein.

    ![Anwendungskatalog](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **AppDynamics** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei AppDynamics zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **AppDynamics** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei AppDynamics anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **AppDynamics-Anmelde-URL** die von den Benutzern für die Anmeldung bei AppDynamics verwendete URL ein (z. B. **https://companyname.saas.appdynamics.com*")), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um AppDynamics** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der AppDynamics-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben auf der Symbolleiste auf **Settings**, und klicken Sie dann auf **Administration**.

    ![Verwaltung](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Verwaltung")

7.  Klicken Sie auf die Registerkarte **Authentication Provider**.

    ![Authentication Provider](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")

8.  Führen Sie im Abschnitt **Authentication Provider** die folgenden Schritte aus:

    ![SAML-Konfiguration](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML-Konfiguration")

    1.  Wählen Sie als **Authentication Provider** die Option **SAML** aus.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um AppDynamics** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn ins Textfeld **Login URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um AppDynamics** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn ins Textfeld **Logout URL** ein.
    4.  Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    5.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Certificate** ein.
    6.  Klicken Sie auf **Save**. ![Speichern](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Speichern")

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei AppDynamics anmelden können, müssen sie in AppDynamics bereitgestellt werden. Im Fall von AppDynamics ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der AppDynamics-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Users**, und klicken Sie dann auf das Symbol **+**, um das Dialogfeld **Create User** zu öffnen.

    ![Benutzer](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Benutzer")

3.  Führen Sie im Abschnitt **Benutzer erstellen** die folgenden Schritte aus:

    ![Create User](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Benutzer erstellen")

    1.  Geben Sie die Textfelder **Username**, **Name**, **Email**, **New Password** und **Repeat New Password** die entsprechenden Informationen eines gültigen AAD-Kontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von AppDynamics-Benutzerkonten oder mithilfe der von AppDynamics bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie AppDynamics Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **AppDynamics** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->