<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit Adaptive Suite | Microsoft Azure" description="Erfahren Sie, wie Sie Adaptive Suite mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit Adaptive Suite
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=615749), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und Adaptive Suite erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Adaptive Suite-Mandanten

Nach Abschluss dieses Lernprogramms können sich die Adaptive Suite zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer Adaptive Suite-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Sie können aber auch den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Adaptive Suite
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Szenario")
##Aktivieren der Anwendungsintegration für Adaptive Suite

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Adaptive Suite aktivieren.

###So aktivieren Sie die Anwendungsintegration für Adaptive Suite

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Adaptive Suite** ein.

    ![Anwendungskatalog](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Adaptive Suite** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Adaptive Suite zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für Adaptive Suite müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI) \(in englischer Sprache\) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite **Adaptive Suite** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Adaptive Suite anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-Einstellungen konfigurieren** im Textfeld **Antwort-URL** die URL im Format "**https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*" ein, und klicken Sie dann auf **Weiter**.

    >[AZURE.NOTE]Sie finden diesen Wert auf der Seite **SAML SSO Settings** der Adaptive Suite.

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Konfigurieren von App-Einstellungen")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um Adaptive Suite** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Adaptive Suite-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Admin**.

    ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

7.  Klicken Sie im Abschnitt **Users and Roles** auf **Manage SAML SSO Settings**.

    ![Manage SAML SSO Settings](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Manage SAML SSO Settings")

8.  Führen Sie auf der Seite **SAML SSO Settings** die folgenden Schritte aus:

    ![SAML SSO Settings](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO Settings")

    1.  Geben Sie im Textfeld **Identity provider name** einen Namen für die Konfiguration ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Adaptive Suite** den Wert der **Entitäts-ID**, und fügen Sie ihn in das Textfeld **Identity provider Entity ID** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Adaptive Suite** den Wert **SAML-SSO-URL**, und fügen Sie ihn in das Textfeld **Identity provider SSO URL** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Adaptive Suite** den Wert **SAML-SSO-URL**, und fügen Sie ihn in das Textfeld **Custom logout URL** ein.
    5.  Klicken Sie auf **Choose file**, um das heruntergeladene Zertifikat hochzuladen.
    6.  Wählen Sie als **SAML user id** den **User’s Adaptive Insights user name** aus.
    7.  Wählen Sie als **SAML user id location** die **User id in NameID of Subject** aus.
    8.  Wählen Sie als **SAML NameID format** die Option **Email address** aus.
    9.  Wählen Sie für **Enable SAML** die Option **Allow SAML SSO and direct Adaptive Insights login** aus.
    10. Klicken Sie auf **Speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Adaptive Suite anmelden können, müssen sie in Adaptive Suite bereitgestellt werden. Im Fall von Adaptive Suite ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der **Adaptive Suite**-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Admin**.

    ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

3.  Klicken Sie im Abschnitt **Users and Roles** auf **Add User**.

    ![Benutzer hinzufügen](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Benutzer hinzufügen")

4.  Führen Sie im Abschnitt **New User** die folgenden Schritte aus:

    ![Senden](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Senden")

    1.  Geben Sie in die Textfelder **Name**, **Login**, **Email** und **Password** die entsprechenden Informationen eines gültigen Azure Active Directory-Benutzerkontos ein, das Sie bereitstellen möchten.
    2.  Wählen Sie eine **Role** aus.
    3.  Klicken Sie auf **Senden**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von Adaptive Suite-Benutzerkonten oder mithilfe der von Adaptive Suite bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Adaptive Suite Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Adaptive Suite** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->