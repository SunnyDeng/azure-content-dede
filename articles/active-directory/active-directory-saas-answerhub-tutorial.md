<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit AnswerHub | Microsoft Azure" description="Erfahren Sie, wie Sie AnswerHub mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit AnswerHub
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=528077), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und AnswerHub erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein AnswerHub-Abonnement, für das das einmalige Anmelden aktiviert ist.

Nach Abschluss dieses Lernprogramms können sich die AnswerHub zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer AnswerHub-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für AnswerHub
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Szenario")
##Aktivieren der Anwendungsintegration für AnswerHub

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für AnswerHub aktivieren.

###So aktivieren Sie die Anwendungsintegration für AnswerHub

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **AnswerHub** ein.

    ![Anwendungskatalog](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **AnswerHub** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei AnswerHub zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **AnswerHub** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei AnswerHub anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **AnswerHub-Anmelde-URL** die URL im Format "**https://company.answerhub.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785170.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um AnswerHub** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der AnswerHub-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Administration**.

7.  Klicken Sie auf die Registerkarte **User and Group**.

8.  Klicken Sie im Navigationsbereich auf der linken Seite im Abschnitt **Social Settings** auf **SAML Setup**.

9.  Klicken Sie auf die Registerkarte **IDP Config**.

10. Führen Sie auf der Registerkarte **IDP Config** die folgenden Schritte aus:

    ![SAML Setup](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML Setup")

    1.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um AnswerHub** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **IDP Login URL** ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um AnswerHub** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **IDP Logout URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um AnswerHub** den Wert für **Namensbezeichnerformat**, und fügen Sie ihn in das Textfeld **IDP Name Identifier Format** ein.
    4.  Klicken Sie auf **Keys and Certificates**.

11. Führen Sie auf der Registerkarte "Keys and Certificates" die folgenden Schritte aus:

    ![Keys and Certificates](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Keys and Certificates")

    1.  Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

		>[AZURE.TIP]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache).

    2.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **IDP Public Key (x509 Format)** ein.
    3.  Klicken Sie auf **Speichern**.

12. Klicken Sie auf der Registerkarte **IDP Config** auf **Save**.

13. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei AnswerHub anmelden können, müssen sie in AnswerHub bereitgestellt werden. Im Fall von AnswerHub ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrer **AnswerHub**-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Administration**.

3.  Klicken Sie auf die Registerkarte **Users & Groups**.

4.  Klicken Sie im Navigationsbereich auf der linken Seite im Abschnitt **Manage Users** auf **Create or import users**.

    ![Users & Groups](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Users & Groups")

5.  Geben Sie in die Textfelde **Email address**, **Username** und **Password** die entsprechenden Informationen eines gültigen Azure Active Directory-Benutzerkontos ein, das Sie bereitstellen möchten, und klicken Sie dann auf **Save**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von AnswerHub-Benutzerkonten oder mithilfe der von AnswerHub bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie AnswerHub Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **AnswerHub** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->