<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit RightAnswers | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie RightAnswers mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit RightAnswers
  
In diesem Tutorial wird die Integration von Azure und RightAnswers erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein RightAnswers-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie RightAnswers zugewiesen haben, wie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) beschrieben mittels einmaliger Anmeldung bei der Anwendung anmelden.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für RightAnswers
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Szenario")
##Aktivieren der Anwendungsintegration für RightAnswers
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für RightAnswers aktivieren.

###So aktivieren Sie die Anwendungsintegration für RightAnswers:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **RightAnswers** ein.

    ![Anwendungskatalog](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **RightAnswers** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei RightAnswers zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **RightAnswers** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei RightAnswers anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Anmelde-URL** die URL ein, welche die Benutzer zur Anmeldung bei RightAnswers verwenden (z. B. **https://fortify.rightanswers.com/portal/ss/*), und klicken Sie dann auf **Weiter**.

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Konfigurieren von App-Einstellungen")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für RightAnswers** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Einmaliges Anmelden konfigurieren")

5.  Senden Sie die heruntergeladene Metadatendatei an das Supportteam von RightAnswers.

    >[AZURE.NOTE]Die eigentliche SSO-Konfiguration muss vom Supportteam von RightAnswers durchgeführt werden. Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei RightAnswers anmelden können, müssen sie in RightAnswers bereitgestellt werden. Im Fall von RightAnswers ist die Bereitstellung eine automatisierte Aufgabe. Für Sie steht kein Aktionselement zur Verfügung.
  
Benutzer werden beim Versuch des einmaligen Anmeldens bei Bedarf automatisch erstellt.

>[AZURE.NOTE]Sie können RightAnswers-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von AAD-Benutzerkonten oder mithilfe der von RightAnswers bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie RightAnswers Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **RightAnswers** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->