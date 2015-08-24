<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit Brightspace von Desire2Learn | Microsoft Azure" description="Erfahren Sie, wie Sie Brightspace von Desire2Learn mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit Brightspace von Desire2Learn
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=550977), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und Brightspace von Desire2Learn erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Abonnement von Brightspace von Desire2Learn mit aktiviertem einmaligen Anmelden

Nach Abschluss dieses Lernprogramms können sich die Brightspace von Desire2Learn zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer Unternehmenswebsite von Brightspace von Desire2Learn bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Sie können aber auch den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Brightspace von Desire2Learn
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Szenario")
##Aktivieren der Anwendungsintegration für Brightspace von Desire2Learn

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Brightspace von Desire2Learn aktivieren.

###So aktivieren Sie die Anwendungsintegration für Brightspace von Desire2Learn

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Brightspace von Desire2Learn** ein.

    ![Anwendungskatalog](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Brightspace von Desire2Learn** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Brightspace von Desire2Learn zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite **Brightspace von Desire2Learn** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Brightspace von Desire2Learn anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "App-URL konfigurieren")

    1.  Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei **Brightspace von Desire2Learn** verwenden \(z. B. **https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
2.  Klicken Sie auf **Weiter**.

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um Brightspace von Desire2Learn** auf **Metadaten herunterladen**, und speichern Sie die Metadaten auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Einmaliges Anmelden konfigurieren")

5.  Senden Sie die heruntergeladene Metadatendatei an Ihr Support-Team für Brightspace von Desire2Learn.

    >[AZURE.NOTE]Das Support-Team für Brightspace von Desire2Learn muss die tatsächliche Konfiguration von SSO übernehmen. Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Um Azure AD-Benutzern die Anmeldung bei Brightspace von Desire2Learn zu ermöglichen, müssen sie in Brightspace von Desire2Learn bereitgestellt werden. Im Fall von Brightspace von Desire2Learn müssen die Benutzerkonten durch Ihr Support-Team für Brightspace Desire2Learn erstellt werden.

>[AZURE.NOTE]Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Benutzerkonten für Brightspace von Desire2Learn oder mithilfe der von Brightspace von Desire2Learn bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Brightspace von Desire2Learn Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Brightspace von Desire2Learn** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->