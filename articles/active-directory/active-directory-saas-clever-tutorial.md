<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Clever | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Clever mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Lernprogramm: Azure Active Directory-Integration mit Clever

In diesem Lernprogramm wird die Integration von Azure und Clever erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Clever-Mandanten

Nach Abschluss dieses Lernprogramms können sich die Clever zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer Clever-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie auch die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Clever
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-clever-tutorial/IC798977.png "Szenario")
##Aktivieren der Anwendungsintegration für Clever

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Clever aktivieren.

###So aktivieren Sie die Anwendungsintegration für Clever

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-clever-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-clever-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-clever-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Clever** ein.

    ![Anwendungskatalog](./media/active-directory-saas-clever-tutorial/IC798978.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Clever** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Clever zu authentifizieren. Die Clever-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der **SAML-Tokenattribute** benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![Attribute](./media/active-directory-saas-clever-tutorial/IC798980.png "Attribute")

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Clever** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clever-tutorial/IC784682.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Clever anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clever-tutorial/IC798981.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Clever-Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei Clever verwenden (z. B. **https://clever.com/in/azsandbox*), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-clever-tutorial/IC798982.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um Clever** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clever-tutorial/IC798983.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Clever-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf der Symbolleiste auf **Instant Login**.

    ![Instant Login](./media/active-directory-saas-clever-tutorial/IC798984.png "Instant Login")

7.  Führen Sie auf der Seite **Instant Login** die folgenden Schritte aus:

    ![Instant Login](./media/active-directory-saas-clever-tutorial/IC798985.png "Instant Login")

    1.  Geben Sie die **Login URL** ein.  

        >[AZURE.NOTE]Die **Login URL** ist ein benutzerdefinierter Wert. Sie erhalten diesen Wert vom Clever-Supportteam.

    2.  Wählen Sie als **Identity System** die Option **ADFS** aus.
    3.  Klicken Sie auf **Speichern**.

8.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clever-tutorial/IC798986.png "Einmaliges Anmelden konfigurieren")

9.  Klicken Sie oben im Menü auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen.

    ![Attribute](./media/active-directory-saas-clever-tutorial/IC795920.png "Attribute")

10. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![SAML-Tokenattribute](./media/active-directory-saas-clever-tutorial/IC795921.png "SAML-Tokenattribute")

	|Attributname|Attributwert|
    |---|---|
    |clever.Student.Credentials.district\_username|User.userprincipalname|

    1.  Klicken Sie für jede Datenzeile in der obigen Tabelle auf **add user attribute**.
    2.  Geben Sie im Textfeld **Attribute Name** den für die Zeile angezeigten Attributnamen ein.
    3.  Geben Sie im Textfeld **Attribute Value** den für die Zeile angezeigten Attributwert ein.
    4.  Klicken Sie auf **Fertig stellen**.

11. Klicken Sie auf **Apply Changes**.

##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Clever anmelden können, müssen sie in Clever bereitgestellt werden. Im Fall von Clever ist die Bereitstellung eine manuelle Aufgabe, die von Ihrem Clever-Support-Team ausgeführt werden muss.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Clever-Benutzerkonten oder mithilfe der von Clever bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Clever Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Clever** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-clever-tutorial/IC798987.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-clever-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->