<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit Aha! | Microsoft Azure" description="Erfahren Sie, wie Sie Aha! mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit Aha!
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=550992), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und Aha! erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Aha!-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie Aha! zugewiesen haben, mittels einmaligen Anmeldens auf Ihrer Aha!-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Aha!
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-aha-tutorial/IC798944.png "Szenario")
##Aktivieren der Anwendungsintegration für Aha!

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Aha! aktivieren.

###So aktivieren Sie die Anwendungsintegration für Aha!

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-aha-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-aha-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-aha-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Aha!** ein.

    ![Anwendungskatalog](./media/active-directory-saas-aha-tutorial/IC798945.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Aha!** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Aha! zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Aha!** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aha-tutorial/IC798946.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Aha! anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aha-tutorial/IC798947.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** in das Textfeld **Aha!- Anmelde-URL** den URL ein, den Ihre Benutzer zur Anmeldung bei der Aha!- Anwendung verwenden (z. B. "**https://company.aha.io/session/new*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-aha-tutorial/IC798948.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um Aha!** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aha-tutorial/IC798949.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Aha!-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im oberen Menü auf **Settings**.

    ![Einstellungen](./media/active-directory-saas-aha-tutorial/IC798950.png "Einstellungen")

7.  Klicken Sie auf **Account**.

    ![Profil](./media/active-directory-saas-aha-tutorial/IC798951.png "Profil")

8.  Klicken Sie auf **Security and single sign-on**.

    ![Security and single sign-on](./media/active-directory-saas-aha-tutorial/IC798952.png "Security and single sign-on")

9.  Wählen Sie im Abschnitt **Single Sign-On** als **Identity Provider** die Option **SAML2.0** aus.

    ![Security and single sign-on](./media/active-directory-saas-aha-tutorial/IC798953.png "Security and single sign-on")

10. Führen Sie auf der Konfigurationsseite **Single Sign-On** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-aha-tutorial/IC798954.png "Einmaliges Anmelden")

    1.  Geben Sie im Textfeld **Name** einen Namen für die Konfiguration ein.
    2.  Wählen Sie für **Configure using** die Option **Metadata File** aus.
    3.  Klicken Sie auf **Browse**, um die heruntergeladene Metadatendatei hochzuladen.
    4.  Klicken Sie auf **Update**.

11. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aha-tutorial/IC798955.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Aha! anmelden können, müssen sie in Aha! bereitgestellt werden. Im Fall von Aha! ist die Bereitstellung eine automatisierte Aufgabe. Für Sie steht kein Aktionselement zur Verfügung.
  
Benutzer werden beim Versuch des einmaligen Anmeldens bei Bedarf automatisch erstellt.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Aha!-Benutzerkonten oder mithilfe der von Aha! bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Aha! Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Aha!** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-aha-tutorial/IC798956.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-aha-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->