<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Clarizen | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Clarizen mit Azure Active Directory verwenden können, um das einmalige Anmelden, automatisierte Bereitstellung und vieles mehr zu aktivieren." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Lernprogramm: Azure Active Directory-Integration mit Clarizen

In diesem Lernprogramm wird die Integration von Azure und Clarizen erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Clarizen-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie Clarizen zugewiesen haben, mittels einmaligen Anmeldens auf Ihrer Clarizen-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Clarizen
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Szenario")
##Aktivieren der Anwendungsintegration für Clarizen

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Clarizen aktivieren.

###So aktivieren Sie die Anwendungsintegration für Clarizen

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **Clarizen** ein.

    ![Anwendungskatalog](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Clarizen** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Clarizen zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Clarizen** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Clarizen anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Einmaliges Anmelden konfigurieren")

3.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Clarizen** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Einmaliges Anmelden konfigurieren")

4.  Melden Sie sich in einem anderen Webbrowserfenster bei der **Clarizen**-Unternehmenswebsite als Administrator an (z. B. **https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Klicken Sie auf Ihren Benutzernamen, und klicken Sie dann auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Einstellungen")

6.  Klicken Sie auf die Registerkarte **Global Settings**, und klicken Sie dann neben **Federated Authentication** auf **edit**.

    ![Global Settings](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")

7.  Führen Sie im Dialogfeld **Federated Authentication** die folgenden Schritte aus:

    ![Federated Authentication](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")

    1.  Klicken Sie auf **Upload**, um das heruntergeladene Zertifikat hochzuladen.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Clarizen** den Wert für **Dienst-URL für einmalige Anmeldung**, und fügen Sie ihn in das Textfeld **Anmelde-URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Clarizen** den Wert für **Dienst-URL für einmaliges Abmelden**, und fügen Sie ihn ins Textfeld **Abmelde-URL** ein.
    4.  Aktivieren Sie **Use POST**.
    5.  Klicken Sie auf **Speichern**.

8.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Clarizen anmelden können, müssen sie in Clarizen bereitgestellt werden. Im Fall von Clarizen ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei der **Clarizen**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie auf **People**.

    ![People](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")

3.  Klicken Sie auf **Invite Users**.

    ![Invite Users](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")

4.  Führen Sie auf der Dialogfeldseite "Invite People" die folgenden Schritte aus:

    ![Invite People](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Personen einladen")

    1.  Geben Sie im Textfeld **Email** die E-Mail-Adresse eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Einladen**.

    >[AZURE.NOTE]Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Clarizen Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Clarizen** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->