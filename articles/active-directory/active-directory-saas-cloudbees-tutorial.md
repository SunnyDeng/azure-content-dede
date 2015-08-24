<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit CloudBees | Microsoft Azure" description="Erfahren Sie, wie Sie CloudBees mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit CloudBees
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=528737), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und CloudBees erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen CloudBees-Mandanten

Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie CloudBees zugewiesen haben, mittels einmaligen Anmeldens auf der CloudBees-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Sie können aber auch den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/azure/dn308586.aspx)\).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für CloudBees
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-cloudbees-tutorial/IC790415.png "Szenario")

##Aktivieren der Anwendungsintegration für CloudBees

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für CloudBees aktivieren.

###So aktivieren Sie die Anwendungsintegration für CloudBees

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cloudbees-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-cloudbees-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-cloudbees-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-cloudbees-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **CloudBees** ein.

    ![Anwendungskatalog](./media/active-directory-saas-cloudbees-tutorial/IC790416.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **CloudBees** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![CloudBees](./media/active-directory-saas-cloudbees-tutorial/IC790417.png "CloudBees")

##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei CloudBees zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für CloudBees müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI) \(in englischer Sprache\) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **CloudBees** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cloudbees-tutorial/IC790418.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei CloudBees anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cloudbees-tutorial/IC790419.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **CloudBees-Anmelde-URL** die von den Benutzern für die Anmeldung bei CloudBees verwendete URL ein \(z. B. **https://grandcentral.cloudbees.com/login*"\), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-cloudbees-tutorial/IC790420.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um CloudBees** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cloudbees-tutorial/IC790421.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der CloudBees-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im oberen Menü auf das Symbol **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-cloudbees-tutorial/IC790422.png "Einstellungen")

7.  Klicken Sie auf die Registerkarte **SSO integration**.

8.  Führen Sie im Abschnitt **Set Up SAML 2.0 Single Sign On** die folgenden Schritte aus:

    ![Einmaliges Anmelden einrichten](./media/active-directory-saas-cloudbees-tutorial/IC790423.png "Einmaliges Anmelden einrichten")

    1.  Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um CloudBees** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Remote login URL** ein.
    2.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Existing certificate fingerprint** ein.
    
        >[AZURE.TIP]Weitere Informationen finden Sie unter [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI) \(in englischer Sprache\).

    3.  Geben Sie im Textfeld **Authentication domain** die Domäne Ihres Unternehmens ein.
    4.  Klicken Sie auf **Speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cloudbees-tutorial/IC790424.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei **CloudBees** anmelden können, müssen sie in **CloudBees** bereitgestellt werden. Im Fall von **CloudBees** ist die Bereitstellung eine manuelle Aufgabe.

###Führen Sie zum Bereitstellen von Benutzerkonten in CloudBees die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrer **CloudBees**-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Edit Users**.

    ![Benutzer](./media/active-directory-saas-cloudbees-tutorial/IC790429.png "Benutzer")

3.  Klicken Sie auf **Hinzufügen**.

    ![Hinzufügen](./media/active-directory-saas-cloudbees-tutorial/IC790430.png "Hinzufügen")

4.  Führen Sie im Abschnitt **Add User** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-cloudbees-tutorial/IC790431.png "Benutzer hinzufügen")

    1.  Geben Sie **Email**, **First Name**, **Last Name** und weitere Details eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Add User**.

        >[AZURE.NOTE]Eine E-Mail mit einer Anmeldeanleitung wird an den Besitzer des Kontos gesendet.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von CloudBees-Benutzerkonten oder mithilfe der von CloudBees bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie CloudBees Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **CloudBees** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-cloudbees-tutorial/IC790432.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-cloudbees-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/azure/dn308586.aspx).

<!---HONumber=August15_HO7-->