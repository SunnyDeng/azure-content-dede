<properties
	pageTitle="Lernprogramm: Azure Active Directory-Integration mit QuickHelp | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und QuickHelp konfigurieren."
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
	ms.date="11/06/2015"
	ms.author="markusvi"/>


# Lernprogramm: Azure Active Directory-Integration mit QuickHelp

In diesem Lernprogramm erfahren Sie, wie Sie QuickHelp in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von QuickHelp in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer auf QuickHelp Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei QuickHelp anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit QuickHelp konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Ein QuickHelp-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE]Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von QuickHelp aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von QuickHelp aus dem Katalog
Zum Konfigurieren der Integration von QuickHelp in Azure AD müssen Sie QuickHelp aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um QuickHelp aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br>![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **What do you want to do** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie im Suchfeld das Wort **QuickHelp** ein.<br><br> ![Anwendungen][5]<br>
7. Wählen Sie im Ergebnisbereich **QuickHelp** aus, und klicken Sie dann auf **Fertig stellen**, um die Anwendung hinzuzufügen. <br><br>![Anwendungen][500]<br>


##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens "Britta Simon" veranschaulicht werden, wie das einmalige Anmelden von Azure AD in QuickHelp konfiguriert und getestet werden kann.


Zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei QuickHelp müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines QuickHelp-Testbenutzers](#creating-a-quickhelp-test-user)**, um eine Entsprechung von Britta Simon in QuickHelp zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Das Ziel dieses Abschnitts ist, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in der QuickHelp-Anwendung zu konfigurieren.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in QuickHelp die folgenden Schritte aus:**

1. Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **QuickHelp** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei QuickHelp anmelden?** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br> ![Azure AD – einmaliges Anmelden][7] <br>

3. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Konfigurieren von App-Einstellungen][8] <br>
 
     a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei der QuickHelp-Website verwenden (z. B. *https://quickhelp.com/bsiazure/*).

     >[AZURE.NOTE]Wenden Sie sich an das QuickHelp-Supportteam, falls Ihnen die Anmelde-URL nicht bekannt ist.

     b. Klicken Sie auf **Weiter**.

 
4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für QuickHelp** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer. <br><br>![Was ist Azure AD Connect?][9] <br>



1. Melden Sie sich bei Ihrer QuickHelp-Unternehmenswebsite als Administrator an.

2. Klicken Sie oben im Menü auf **Admin**. <br><br>![Einmaliges Anmelden konfigurieren][21]<br>


1. Klicken Sie im Menü **QuickHelp Admin** auf **Einstellungen**. <br><br>![Einmaliges Anmelden konfigurieren][22]<br>

1. Klicken Sie auf **Authentifizierungseinstellungen**.

1. Führen Sie auf der Seite **Authentifizierungseinstellungen** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren][23]<br>

    a. Wählen Sie für **SSO-Typ** die Option **WSFederation** aus.

    b. Klicken Sie zum Hochladen der heruntergeladenen Azure-Metadatendatei auf **Durchsuchen**, navigieren Sie zur Datei, und klicken Sie dann auf **Metadaten hochladen**.

    d. Geben Sie in das Textfeld **E-Mail** den Wert ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** ein.

    e. Geben Sie im Textfeld **Vorname** den **Wert http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** ein.

    f. Geben Sie im Textfeld **Nachname** den **Wert http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** ein.

    g. Klicken Sie in der **Aktionsleiste** auf **Speichern**.







6. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus, und klicken Sie dann auf **Weiter**. <br><br>![Was ist Azure AD Connect?][10]<br>

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**. <br><br>![Was ist Azure AD Connect?][11]




### Erstellen einen Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**.<br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png)<br> 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**.<br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) <br>
 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png)<br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) <br>
 
    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Rolle **Benutzer** aus. e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**. <br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) <br>
 
8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.

  
 
### Erstellen einen QuickHelp-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in QuickHelp. Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in QuickHelp zu einem Benutzer in Azure AD ist. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in QuickHelp muss eine Linkbeziehung eingerichtet werden.

QuickHelp unterstützt die Just-in-Time-Bereitstellung. Auf diese Weise kann ein Benutzerkonto bei Bedarf automatisch in QuickHelp erstellt und mit dem Azure AD-Konto verknüpft werden.

Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist, Britta Simon für das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf QuickHelp erhält. <br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon QuickHelp zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**. <br><br>![Benutzer zuweisen][201] <br>

2. Wählen Sie in der Anwendungsliste **QuickHelp** aus. <br><br>![Benutzer zuweisen][202] <br>

1. Klicken Sie im oberen Menü auf **Benutzer**. <br><br>![Benutzer zuweisen][203] <br>

1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel "QuickHelp" klicken, sollten Sie automatisch bei Ihrer QuickHelp-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png

<!---HONumber=Nov15_HO3-->