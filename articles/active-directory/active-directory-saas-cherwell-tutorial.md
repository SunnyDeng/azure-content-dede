<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Cherwell | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Cherwell mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu aktivieren." 
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
    ms.date="01/05/2016" 
    ms.author="jeedes" />

#Lernprogramm: Azure Active Directory-Integration mit Cherwell

In diesem Lernprogramm wird die Integration von Azure und Cherwell erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Cherwell-Software-Abonnement, für das das einmalige Anmelden aktiviert ist.

Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Cherwell zugewiesen haben, mittels einmaligen Anmeldens auf der Cherwell-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Cherwell
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Szenario")
##Aktivieren der Anwendungsintegration für Cherwell

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Cherwell aktivieren.

###So aktivieren Sie die Anwendungsintegration für Cherwell

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Cherwell** ein.

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  Wählen Sie im Ergebnisbereich **Cherwell** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Cherwell zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Cherwell** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Cherwell anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798992.png "App-URL konfigurieren")

    1.  Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei **Cherwell** verwenden (z. B. **https://pictdev.cherwellondemand.com/cherwellclient*).
2.  Klicken Sie auf **Weiter**.

4.  Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren um Cherwell** die folgenden Schritte aus.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Einmaliges Anmelden konfigurieren")

    1.  Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.
    2.  Kopieren Sie die **Identitätsanbieter-URL**.
    3.  Kopieren Sie die **Dienst-URL für einmalige Anmeldung**.
    4.  Klicken Sie auf **Weiter**.

5.  Senden Sie das heruntergeladene Zertifikat, die **Identitätsanbieter-URL** und die **Dienst-URL für einmalige Anmeldung** an Ihr Cherwell-Supportteam.

    >[AZURE.NOTE]Die eigentliche SSO-Konfiguration muss vom Supportteam von Cherwell durchgeführt werden. Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Cherwell anmelden können, müssen sie in Cherwell bereitgestellt werden. Im Fall von Cherwell müssen die Benutzerkonten vom Supportteam von Cherwell erstellt werden.

>[AZURE.NOTE]Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Cherwell-Benutzerkonten oder mithilfe der von Cherwell bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Cherwell Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Cherwell** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0107_2016-->