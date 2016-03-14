<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit UserEcho | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und UserEcho konfigurieren."
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
	ms.date="02/17/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit UserEcho

In diesem Tutorial erfahren Sie, wie Sie UserEcho in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von UserEcho in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer auf UserEcho Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei UserEcho anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im klassischen Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit UserEcho konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein UserEcho-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von UserEcho aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von UserEcho aus dem Katalog
Zum Konfigurieren der Integration von UserEcho in Azure AD müssen Sie UserEcho aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um UserEcho aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **What do you want to do** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie im Suchfeld **UserEcho** ein.<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)<br>
7. Wählen Sie im Ergebnisbereich **UserEcho** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)<br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens „Britta Simon“ veranschaulicht werden, wie das einmalige Anmelden von Azure AD in UserEcho konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in UserEcho als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in UserEcho muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als Wert dem **Benutzernamen** in UserEcho zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei UserEcho müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines UserEcho-Testbenutzers](#creating-a-userecho-test-user)**, um eine Entsprechung von Britta Simon in UserEcho zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Das Ziel dieses Abschnitts ist, das einmalige Anmelden von Azure AD im klassischen Azure-Portal zu aktivieren und das einmalige Anmelden in der UserEcho-Anwendung zu konfigurieren.




**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in UserEcho die folgenden Schritte aus:**

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **UserEcho** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei UserEcho anmelden?** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png) <br>

3. Führen Sie auf der Dialogfeldseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png) <br>

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei der UserEcho-Anwendung verwenden (z. B. **https://fabrikam.UserEcho.com/*).

    b. Klicken Sie auf **Weiter**.
 
 
4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für UserEcho** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen** und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


1. Melden Sie sich in einem anderen Webbrowserfenster bei der UserEcho-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf der Symbolleiste oben auf Ihren Benutzernamen, um das Menü zu erweitern, und klicken Sie dann auf **Einrichtung**. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) <br>

1. Klicken Sie auf **Integrationen**. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) <br>


1. Klicken Sie auf **Website** und dann auf **Einmaliges Anmelden (SAML2)**. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) <br>


1. Führen Sie auf der Seite **Einmaliges Anmelden (SAML)** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png) <br>

    a. Wählen Sie für **SAML-aktiviert** die Option **Ja**.

    b. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite „Einmaliges Anmelden konfigurieren für UserEcho“ den Wert für **Dienst-URL für einmaliges Anmelden**, und fügen Sie ihn in das Textfeld **SAML-SSO-URL** ein.

    c. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite „Einmaliges Anmelden konfigurieren für UserEcho“ den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Remoteabmelde-URL** ein.

    d. Öffnen Sie das heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.

    e. Klicken Sie auf **Speichern**.


6. Wählen Sie im klassischen Azure-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus und klicken Sie dann auf **Weiter**. <br><br>![Azure AD – einmaliges Anmelden][10]<br>

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**. <br><br>![Azure AD – einmaliges Anmelden][11]




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im klassischen Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png)<br> 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) <br>
 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) <br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png) <br>
 
    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Rolle **Benutzer** aus. e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png) <br>
 
8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.

  
 
### Erstellen eines UserEcho-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in UserEcho.

**Um einen Benutzer namens Britta Simon in UserEcho zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der UserEcho-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf der Symbolleiste oben auf Ihren Benutzernamen, um das Menü zu erweitern, und klicken Sie dann auf **Einrichtung**. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) <br>

1. Klicken Sie auf **Benutzer**, um den Abschnitt **Benutzer** zu erweitern. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png) <br>

1. Klicken Sie auf **Benutzer**. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png) <br>

1. Klicken Sie auf **Neuen Benutzer einladen**. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png) <br>


1. Führen Sie im Dialogfeld **Neuen Benutzer einladen** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png) <br>

    a. Geben Sie in das Textfeld **Name** den Namen **Britta Simon** ein.

    b. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse von Britta Simon im klassischen Azure-Portal ein.

    c. Klicken Sie auf **Einladen**.

Eine Einladung wird an Britta gesendet, die ihr Zugriff auf UserEcho gewährt.



### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist, für Britta Simon das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf UserEcho erhält. <br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon UserEcho zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im klassischen Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**. <br><br>![Benutzer zuweisen][201] <br>

2. Wählen Sie in der Anwendungsliste **UserEcho**. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png) <br>

1. Klicken Sie im oberen Menü auf **Benutzer**. <br><br>![Benutzer zuweisen][203] <br>

1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel „UserEcho“ klicken, sollten Sie automatisch in Ihrer UserEcho-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0302_2016-->