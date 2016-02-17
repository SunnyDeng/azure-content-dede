<properties
	pageTitle="Tutorial: Azure Active Directory-Integration von ImageRelay | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ImageRelay konfigurieren."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration von ImageRelay

In diesem Tutorial erfahren Sie, wie Sie ImageRelay in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von ImageRelay in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer auf ImageRelay Zugriff hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ImageRelay anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen

Um die Azure AD-Integration von ImageRelay konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein ImageRelay-Abonnement, das für das einmalige Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.


## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von ImageRelay aus dem Katalog

2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von ImageRelay aus dem Katalog
Zum Konfigurieren der Integration von ImageRelay in Azure AD müssen Sie ImageRelay aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um ImageRelay aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des Azure-Portals auf **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie im Suchfeld **ImageRelay** ein.<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)<br>
7. Wählen Sie im Ergebnisbereich **ImageRelay** aus, und klicken Sie dann auf **Fertig stellen**, um die Anwendung hinzuzufügen. <br><br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand einer Testbenutzerin namens „Britta Simon“ veranschaulicht werden, wie das einmalige Anmelden von Azure AD bei ImageRelay konfiguriert und getestet werden kann.

Damit das einmalige Anmelden funktioniert, benötigt Azure AD ein Benutzerkonto, das für einen entsprechenden Benutzer in ImageRelay steht. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ImageRelay muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den Wert **Benutzername** aus Azure AD dem Wert **Benutzername** in ImageRelay zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei ImageRelay müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
4. **[Erstellen eines ImageRelay-Testbenutzers](#creating-a-userecho-test-user)**, um eine Entsprechung von Britta Simon in ImageRelay zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts ist, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in der ImageRelay-Anwendung zu konfigurieren.


**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei ImageRelay die folgenden Schritte aus:**

1. Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **ImageRelay** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

     ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei ImageRelay anmelden?** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) <br>

3. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus:

     ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) <br>

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei der ImageRelay-Anwendung verwenden (z. B. **https://fabrikam.ImageRelay.com/*).

    b. Klicken Sie auf **Weiter**.

4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für ImageRelay** die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen** und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.

5. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer ImageRelay-Unternehmenswebsite als Administrator an.

    a. Klicken Sie in der Symbolleiste oben auf **Users & Permissions**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png)<br>

    b. Klicken Sie auf **Create New Permission**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) <br>

    c. Aktivieren Sie im Dialogfeld **Single Sign On Settings** das Kontrollkästchen **This Group can only sign-in via Single Sign On**, und klicken Sie dann auf **Save**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) <br>

    d. Wechseln Sie zu **Account Settings**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) <br>

    e. Klicken Sie auf **Single Sign On Settings**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)<br>

    f. Füllen Sie das Formular aus wie angegeben, und klicken Sie auf **Save**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)<br>

    - **Login URL (SSO)**: Dies ist die Dienst-URL für die einmalige Anmeldung aus Azure Active Directory.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_13.png)<br>

    - **Logout Service URL**: Dies ist die Dienst-URL für die einmalige Abmeldung aus Azure Active Directory.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_14.png)<br>

    - Wählen Sie unter **Name Id Format** die Option **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** aus.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_15.png)<br>

    - Wählen Sie unter **Binding Options for Requests from the Service Provider (Image Relay)** die Option **POST Binding** aus.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_16.png)<br>

  	- Klicken Sie unter **x.509 Certificate** auf **Update Certificate**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)<br>

    - Öffnen Sie das in Schritt 4 aus Azure Active Directory heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn hier ein.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)<br>

    - Aktivieren Sie für **Just-In-Time User Provisioning** das Kontrollkästchen **Enable Just-In-Time User Provisioning**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)<br>

    - Wählen Sie die Berechtigungsgruppe aus (z. B. **SSO Basic**), bei der die Anmeldung nur per einmaligem Anmelden erfolgen soll.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)<br>

6. Wählen Sie im Azure AD-Portal die Bestätigung aus, dass Sie das einmalige Anmelden konfiguriert haben, und klicken Sie dann auf **Weiter**.

    ![Azure AD – einmaliges Anmelden][10]<br>

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**.

    ![Azure AD – einmaliges Anmelden][11]


### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf **Active Directory**.<br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png)<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**.<br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) <br>

4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**.<br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png)<br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus:<br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) <br>

    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Option **Benutzer** aus.

    e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**.<br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) <br>

8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus:<br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) <br>

    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.



### Erstellen eines ImageRelay-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in ImageRelay.

**Um eine Benutzerin namens Britta Simon in ImageRelay zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer ImageRelay-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Users & Permissions**, und klicken Sie auf **Create SSO User**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) <br>

1. Geben Sie unter **Email** die E-Mail-Adresse, unter **First Name** den Vornamen, unter **Last Name** den Nachnamen und unter **Company** das Unternehmen des bereitzustellenden Benutzers an. Wählen Sie außerdem die Berechtigungsgruppe aus (z. B. SSO Basic). Dies ist die Gruppe, die sich nur per einmaligem Anmelden anmelden kann.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) <br>

1. Klicken Sie auf **Create**.

### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist, für Britta Simon das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf ImageRelay erhält. <br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon zu ImageRelay zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br> <br>![Benutzer zuweisen][201]<br>

2. Wählen Sie in der Anwendungsliste **ImageRelay** aus.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) <br>

1. Klicken Sie im oberen Menü auf **Benutzer**.<br><br>![Benutzer zuweisen][203] <br>

1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**.<br><br>![Benutzer zuweisen][205]


### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel „ImageRelay“ klicken, sollten Sie automatisch bei Ihrer ImageRelay-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0204_2016-->