<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Chromeriver | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Chromeriver mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu aktivieren." 
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


#Lernprogramm: Azure Active Directory-Integration mit Chromeriver

In diesem Lernprogramm wird die Integration von Azure und Chromeriver erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Chromeriver-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie Chromeriver zugewiesen haben, mittels einmaligen Anmeldens auf Ihrer Chromeriver-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Chromeriver
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Szenario")
##Aktivieren der Anwendungsintegration für Chromeriver

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Chromeriver aktivieren.

###So aktivieren Sie die Anwendungsintegration für Chromeriver

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **Chromeriver** ein.

    ![Anwendungskatalog](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Chromeriver** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Chromeriver zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Chromeriver** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Chromeriver anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-Einstellungen konfigurieren** die folgenden Schritte aus:

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Konfigurieren von App-Einstellungen")

    1.  Geben Sie im Textfeld **Antwort-URL** Ihre **AssertionConsumerService-URL** für Chromeriver ein (z. B. **https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*).

        >[AZURE.NOTE]Sie erhalten diesen Wert vom Supportteam für Chromeriver.

    2.  Klicken Sie auf **Weiter**.

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Chromeriver** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Einmaliges Anmelden konfigurieren")

5.  Senden Sie die heruntergeladene Metadatendatei an das Supportteam für Chromeriver.

    >[AZURE.NOTE]Die eigentliche SSO-Konfiguration muss vom Supportteam von Chromeriver durchgeführt werden. Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Chromeriver anmelden können, müssen sie in Chromeriver bereitgestellt werden. Im Fall von Chromeriver müssen die Benutzerkonten vom Supportteam von Chromeriver erstellt werden.

>[AZURE.NOTE]Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Chromeriver-Benutzerkonten oder mithilfe der von Chromeriver bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Chromeriver Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Chromeriver** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->