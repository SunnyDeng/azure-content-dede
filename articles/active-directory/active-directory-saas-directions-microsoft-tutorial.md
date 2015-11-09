<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Directions on Microsoft | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Directions on Microsoft mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Directions on Microsoft

In diesem Tutorial wird die Integration von Azure Active Directory und Directions on Microsoft erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Directions on Microsoft-Abonnement

Falls Sie noch kein Directions on Microsoft-Verbundabonnement haben, senden Sie per E-Mail eine Anforderung an „*service@DirectionsOnMicrosoft.com*”.

Nach Abschluss dieses Tutorials können sich die Azure Active Directory-Benutzer, die Sie Directions on Microsoft zugewiesen haben, mittels einmaliger Anmeldung bei der Anwendung anmelden.

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Directions on Microsoft
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Szenario")
##Aktivieren der Anwendungsintegration für Directions on Microsoft

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Directions on Microsoft aktivieren.

###So aktivieren Sie die Anwendungsintegration für Directions on Microsoft

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **Directions on Microsoft** ein.

    ![Anwendungskatalog](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Directions on Microsoft** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Szenario](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Szenario")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Directions on Microsoft zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Directions on Microsoft** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Aktivieren der einmaligen Anmeldung](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Aktivieren der einmaligen Anmeldung")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Directions on Microsoft anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD – einmaliges Anmelden")

3.  Geben Sie auf der Seite **App-URL konfigurieren** ****https://www.directionsonmicrosoft.com/user/login** in das Textfeld „Anmelde-URL“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Directions on Microsoft** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Einmaliges Anmelden konfigurieren")

5.  Senden Sie die Metadatendatei an das Supportteam von Directions on Microsoft (**service@DirectionsOnMicrosoft.com*). Geben Sie in der E-Mail Ihre Unternehmensinformationen an, damit das Supportteam von Directions on Microsoft Ihre Verbundwebsitemitgliedschaft ausfindig machen kann.

    >[AZURE.NOTE]Einmaliges Anmelden für Directions on Microsoft muss vom Supportteam von Directions on Microsoft aktiviert werden. Sie erhalten eine Benachrichtigung, wenn einmaliges Anmelden aktiviert wurde.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Für das Konfigurieren der Benutzerbereitstellung in Directions on Microsoft steht kein Aktionselement zur Verfügung. Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Directions on Microsoft anzumelden, überprüft Directions on Microsoft, ob der Benutzer vorhanden ist. Ist noch kein Benutzerkonto verfügbar, wird es von Directions on Microsoft automatisch erstellt.
##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Directions on Microsoft Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Directions on Microsoft** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Ja")

<!---HONumber=Nov15_HO1-->