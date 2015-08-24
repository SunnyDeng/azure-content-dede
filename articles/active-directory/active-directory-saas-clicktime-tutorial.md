<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit ClickTime | Microsoft Azure" description="Erfahren Sie, wie Sie ClickTime mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit ClickTime
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=%20524782), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und ClickTime erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen ClickTime-Mandanten

Nach Abschluss dieses Lernprogramms können sich die ClickTime zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer ClickTime-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Sie können aber auch den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ClickTime
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-clicktime-tutorial/IC777274.png "Szenario")
##Aktivieren der Anwendungsintegration für ClickTime

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ClickTime aktivieren.

###So aktivieren Sie die Anwendungsintegration für ClickTime

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-clicktime-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-clicktime-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-clicktime-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **ClickTime** ein.

    ![Anwendungskatalog](./media/active-directory-saas-clicktime-tutorial/IC777275.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **ClickTime** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ClickTime zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei in Ihren ClickTime-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\) weitere Informationen.

>[AZURE.IMPORTANT]Damit Sie einmaliges Anmelden für Ihren ClickTime-Mandanten konfigurieren können, müssen Sie sich zunächst an den technischen Support von ClickTime wenden, um dieses Feature zu aktivieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **ClickTime** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-clicktime-tutorial/IC777277.png "Einmaliges Anmelden aktivieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei ClickTime anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clicktime-tutorial/IC777278.png "Einmaliges Anmelden konfigurieren")

3.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um ClickTime** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clicktime-tutorial/IC777279.png "Einmaliges Anmelden konfigurieren")

4.  Melden Sie sich in einem anderen Webbrowserfenster bei der ClickTime-Unternehmenswebsite als Administrator an.

5.  Klicken Sie oben auf der Symbolleiste auf **Preferences**, und klicken Sie dann auf **Security Settings**.

6.  Führen Sie im Konfigurationsabschnitt **Single Sign-On Preferences** die folgenden Schritte aus:

    ![Security Settings](./media/active-directory-saas-clicktime-tutorial/IC777280.png "Security Settings")

    1.  Aktivieren Sie **Allow** sign-in using Single Sign-On \(SSO\) with **OneLogin**.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um ClickTime** den Wert für **Dienst-URL für einmaliges Anmelden**, und fügen Sie ihn in das Textfeld **Identity Provider Endpoint** ein.
    3.  Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\).

    4.  Öffnen Sie das Base64-codierte Zertifikat in **Editor**, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Textfeld **X.509 Certificate** ein.
    5.  Klicken Sie auf **Speichern**.

7.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clicktime-tutorial/IC777281.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei ClickTime anmelden können, müssen sie in ClickTime bereitgestellt werden. Im Fall von ClickTime ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **ClickTime**-Mandanten an.

2.  Klicken Sie auf der Symbolleiste oben auf **Company** und dann auf **People**.

    ![People](./media/active-directory-saas-clicktime-tutorial/IC777282.png "People")

3.  Klicken Sie auf **Add Person**.

    ![Add Person](./media/active-directory-saas-clicktime-tutorial/IC777283.png "Add Person")

4.  Führen Sie im Abschnitt "New Person" die folgenden Schritte aus:

    ![People](./media/active-directory-saas-clicktime-tutorial/IC777284.png "People")

    1.  Geben Sie im Textfeld **email address** die E-Mail-Adresse Ihres Azure AD-Kontos ein.
    2.  Geben Sie im Textfeld **full name** den Namen des Azure AD-Kontos ein.  

        >[AZURE.NOTE]Wenn Sie möchten, können Sie zusätzliche Eigenschaften des neuen Personenobjekts festlegen.

    3.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von ClickTime-Benutzerkonten oder mithilfe der von ClickTime bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ClickTime Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **ClickTime** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-clicktime-tutorial/IC777285.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-clicktime-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->