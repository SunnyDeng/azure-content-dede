<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit 15Five | Microsoft Azure" description="Erfahren Sie, wie Sie 15Five mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit 15Five
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=528017), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und 15Five erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein 15Five-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie 15Five zugewiesen haben, mittels einmaligen Anmeldens auf Ihrer 15Five-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für 15Five
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-15five-tutorial/IC784667.png "Szenario")
##Aktivieren der Anwendungsintegration für 15Five

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für 15Five aktivieren.

###So aktivieren Sie die Anwendungsintegration für 15Five

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-15five-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-15five-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-15five-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **15Five** ein.

    ![Anwendungskatalog](./media/active-directory-saas-15five-tutorial/IC784668.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **15Five** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei 15Five zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **15Five** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-15five-tutorial/IC784670.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei 15Five anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-15five-tutorial/IC784671.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **15Five-Anmelde-URL** die URL im Format "**https://company.15Five.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-15five-tutorial/IC784672.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um 15Five** auf **Metadaten herunterladen**, und senden Sie die Metadatendatei an das 15Five-Supportteam.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-15five-tutorial/IC784673.png "Einmaliges Anmelden konfigurieren")

    >[AZURE.NOTE]Das einmalige Anmelden muss vom Supportteam von 15Five aktiviert werden.

5.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-15five-tutorial/IC784674.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei 15Five anmelden können, müssen sie in 15Five bereitgestellt werden. Im Fall von 15Five ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der **15Five**-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Manage Company**.

    ![Manage Company](./media/active-directory-saas-15five-tutorial/IC784675.png "Manage Company")

3.  Wechseln Sie zu **People > Add People**.

    ![People](./media/active-directory-saas-15five-tutorial/IC784676.png "People")

4.  Führen Sie im Abschnitt "Add New Person" die folgenden Schritte aus:

    ![Add New Person](./media/active-directory-saas-15five-tutorial/IC784677.png "Add New Person")

    1.  Geben Sie in die Textfelder **First Name**, **Last Name**, **Title** und **Email address** die entsprechenden Informationen eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Done**.

    >[AZURE.NOTE]Der Besitzer des Azure AD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von 15Five-Benutzerkonten oder mithilfe der von 15Five bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie 15Five Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **15Five** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-15five-tutorial/IC784678.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-15five-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->