<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit PerformanceCentre | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und PerformanceCentre konfigurieren."
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
	ms.date="01/26/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit PerformanceCentre

In diesem Tutorial erfahren Sie, wie Sie PerformanceCentre in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von PerformanceCentre in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf PerformanceCentre hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei PerformanceCentre anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten: im klassischen Azure Active Directory-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit PerformanceCentre konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein PerformanceCentre-Abonnement, für das die einmalige Anmeldung aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus drei großen Bausteinen:

1. Hinzufügen von PerformanceCentre aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von PerformanceCentre aus dem Katalog
Zum Konfigurieren der Integration von PerformanceCentre in Azure AD müssen Sie PerformanceCentre aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um PerformanceCentre aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **What do you want to do** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie in das Suchfeld als Suchbegriff **PerformanceCentre** ein.<br><br> ![Anwendungen][5]<br>
7. Wählen Sie im Ergebnisbereich **PerformanceCentre** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen. <br><br>![Anwendungen][500]<br>


##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden in Azure AD in PerformanceCentre konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in PerformanceCentre als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in PerformanceCentre muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als Wert für den **Benutzernamen** in PerformanceCentre zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei PerformanceCentre müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines PerformanceCentre-Testbenutzers](#creating-a-halogen-software-test-user)**, um eine Entsprechung von Britta Simon in PerformanceCentre zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts besteht darin, das einmalige Anmelden in Azure AD im klassischen Azure AD-Portal zu aktivieren und das einmalige Anmelden in Ihrer PerformanceCentre-Anwendung zu konfigurieren.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD mit PerformanceCentre die folgenden Schritte aus:**

1. Klicken Sie im klassischen Azure AD-Portal auf der Anwendungsintegrationsseite für **PerformanceCentre** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei PerformanceCentre anmelden?** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br> ![Azure AD – einmaliges Anmelden][7] <br>

3. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Azure AD – einmaliges Anmelden][8] <br>
 
     a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei der PerformanceCentre-Website verwenden (z. B. **http://companyname.performancecentre.com/saml/SSO*).
 
     b. Klicken Sie auf **Weiter**.
 
4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für PerformanceCentre** die folgenden Schritte aus: <br><br>![Azure AD – einmaliges Anmelden][9] <br>

    a. Klicken Sie auf **Metadaten herunterladen** und speichern Sie die Datei auf Ihrem Computer.



1. Melden Sie sich bei Ihrer **PerformanceCentre**-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der Registerkarte links auf **Konfigurieren**. <br><br>![Azure AD – einmaliges Anmelden][10]

2. Klicken Sie auf der Registerkarte links auf **Verschiedenes** und anschließend auf **Einmaliges Anmelden**. <br><br>![Azure AD – einmaliges Anmelden][11]

2. Wählen Sie als **Protokoll** die Option **SAML** aus. <br><br>![Azure AD – einmaliges Anmelden][12]

2. Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **Identitätsanbietermetadaten** ein. Klicken Sie anschließend auf **Speichern**. <br><br>![Azure AD – einmaliges Anmelden][13]

2. Überprüfen Sie, ob die Werte für **Entitätsbasis-URL** und **Entitäts-ID-URL** korrekt sind. <br><br>![Azure AD – einmaliges Anmelden][14]


6. Wählen Sie im klassischen Azure AD-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus, und klicken Sie dann auf **Weiter**. <br><br>![Azure AD – einmaliges Anmelden][15]<br>

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**. <br><br>![Azure AD – einmaliges Anmelden][16]




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im klassischen Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)<br> 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) <br>
 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) <br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) <br>
 
    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Rolle **Benutzer** aus. e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) <br>
 
8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.

  
 
### Erstellen eines PerformanceCentre-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in PerformanceCentre.

**Um einen Benutzer namens Britta Simon in PerformanceCentre zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer PerformanceCentre-Unternehmenswebsite als Administrator an.

2. Klicken Sie im Menü auf der linken Seite auf **Verknüpfen**, und klicken Sie dann auf **Teilnehmer erstellen**. <br><br>![Benutzer erstellen][400]<br>

4. Führen Sie auf der Dialogfeldseite **Verknüpfen – Teilnehmer erstellen** die folgenden Schritte aus: <br><br>![Benutzer erstellen][401]<br>

    a. Geben Sie die erforderlichen Attribute für Britta Simon in die entsprechenden Textfelder ein.
    
    > [AZURE.IMPORTANT] Das Attribut für den Benutzernamen von Britta in PerformanceCentre muss mit dem Benutzernamen in Azure AD identisch sein.


    b. Wählen Sie unter **Rolle auswählen** die Option **Clientadministrator** aus.

    c. Klicken Sie auf **Speichern**.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts besteht darin, Britta Simon die Verwendung des einmaligen Anmeldens bei Azure zu ermöglichen, indem sie Zugriff auf PerformanceCentre erhält. <br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon PerformanceCentre zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im klassischen Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**. <br><br>![Benutzer zuweisen][201] <br>
2. Wählen Sie in der Anwendungsliste **PerformanceCentre** aus. <br><br>![Benutzer zuweisen][202] <br>
1. Klicken Sie im oberen Menü auf **Benutzer**. <br><br>![Benutzer zuweisen][203] <br>
1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel „PerformanceCentre“ klicken, sollten Sie automatisch bei Ihrer PerformanceCentre-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png

<!---HONumber=AcomDC_0128_2016-->