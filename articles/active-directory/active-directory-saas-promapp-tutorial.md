<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit Promapp | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Promapp konfigurieren."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="markusvi"/>


# Tutorial: Azure Active Directory-Integration mit Promapp

In diesem Tutorial erfahren Sie, wie Sie Promapp in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von Promapp in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Promapp hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Promapp anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Promapp konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Ein Promapp-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE]Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus drei großen Bausteinen:

1. Hinzufügen von Promapp aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Promapp aus dem Katalog
Zum Konfigurieren der Integration von Promapp in Azure AD müssen Sie Promapp aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Promapp aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht im Hauptmenü der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie im Suchfeld als Suchbegriff **Promapp** ein.<br><br> ![Anwendungen][5]<br>
7. Wählen Sie im Ergebnisbereich **Promapp** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen. <br><br>![Anwendungen][500]<br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden von Azure AD in Promapp konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Promapp als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Promapp muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als Wert für den **Benutzernamen** in Promapp zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Promapp müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines Promapp-Testbenutzers](#creating-a-halogen-software-test-user)**, um eine Entsprechung von Britta Simon in Promapp zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Das Ziel dieses Abschnitts besteht darin, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in Ihrer Promapp-Anwendung zu konfigurieren.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Promapp die folgenden Schritte aus:**

1. Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Promapp** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Promapp anmelden** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br> ![Azure AD – einmaliges Anmelden][7] <br>

3. Führen Sie auf der Dialogfeldseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Azure AD – einmaliges Anmelden][8] <br>
 
     a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei der Promapp-Website verwenden (z. B. **https://companyname.promapp.com/instancename*).


     b. Klicken Sie auf **Weiter**.
 
4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für Promapp** die folgenden Schritte aus: <br><br>![Azure AD – einmaliges Anmelden][9] <br>

    a. Klicken Sie auf "Zertifikat herunterladen", und speichern Sie das Zertifikat auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


6. Melden Sie sich bei Ihrer Promapp-Unternehmenswebsite als Administrator an.

6. Klicken Sie im Menü oben auf **Admin**. <br><br>![Azure AD – einmaliges Anmelden][12]<br>

6. Klicken Sie auf **Konfigurieren**. <br><br>![Azure AD – einmaliges Anmelden][13]<br>



4. Führen Sie im Dialogfeld **Sicherheit** die folgenden Schritte aus: <br><br>![Azure AD – einmaliges Anmelden][14] <br>

    a. Kopieren Sie im Azure-Portal im Dialogfeld **Einmaliges Anmelden konfigurieren für Promapp** die **Remoteanmelde-URL**, fügen Sie sie ins Textfeld **SSO-Anmelde-URL** ein, und klicken Sie dann auf **Speichern**.

    b. Wählen Sie für **SSO-Modus** die Option **Optional**, und klicken Sie dann auf **Speichern**.

    c. Öffnen Sie das heruntergeladene Zertifikat im Editor, kopieren Sie seinen Inhalt mit Ausnahme der ersten (*-----BEGIN CERTIFICATE-----*) und der letzten Zeile (*-----END CERTIFICATE-----*), und fügen Sie ihn in das Textfeld **SSO-x.509-Zertifikat** ein. Klicken Sie anschließend auf **Speichern**.




6. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus, und klicken Sie dann auf **Weiter**. <br><br>![Azure AD – einmaliges Anmelden][10]<br>

7. Klicken Sie auf der Seite **Bestätigung für einmaliges Anmelden** auf **Abschließen**.

  <br><br>![Azure AD – einmaliges Anmelden][11]




### Erstellen einen Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-promapp-tutorial/create_aaduser_09.png) <br> 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) <br>
 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) <br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-promapp-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie in das Textfeld **Benutzername** den Namen **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-promapp-tutorial/create_aaduser_06.png) <br>
 
    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Rolle **Benutzer** aus. e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Temporäres Kennwort abrufen** auf **Erstellen**. <br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-promapp-tutorial/create_aaduser_07.png) <br>
 
8. Führen Sie auf der Dialogfeldseite **Temporäres Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-promapp-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie den Wert aus dem Feld **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.

  
 
### Erstellen eines Promapp-Testbenutzers

Die Promapp-Anwendung unterstützt die Just-in-Time-Bereitstellung. Das bedeutet, dass ggf. automatisch ein Benutzerkonto erstellt wird, wenn über den Zugriffsbereich auf die Anwendung zugegriffen wird.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts besteht darin, Britta Simon die Verwendung des einmaligen Anmeldens bei Azure zu ermöglichen, indem sie Zugriff auf Promapp erhält. <br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon Promapp zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im Azure-Portal im Hauptmenü der Verzeichnisansicht auf **Anwendung**. <br><br>![Benutzer zuweisen][201] <br>
2. Wählen Sie in der Anwendungsliste **Promapp** aus. <br><br>![Benutzer zuweisen][202] <br>
1. Klicken Sie im Menü oben auf **Benutzer**.<br><br>![Benutzer zuweisen][203] <br>
1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel „Promapp“ klicken, sollten Sie automatisch bei Ihrer Promapp-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_01.png

[500]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_500.png

[6]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_02.png
[8]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_03.png
[9]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_04.png
[10]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[20]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_10.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_400.png
[401]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_401.png
[402]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_402.png

<!---HONumber=Nov15_HO2-->