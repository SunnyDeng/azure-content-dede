<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit HR2day by Merces | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und HR2day by Merces konfigurieren."
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
	ms.date="03/07/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit HR2day by Merces

In diesem Tutorial erfahren Sie, wie Sie HR2day by Merces in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von HR2day by Merces in Azure AD bietet folgende Vorteile:

- Sie können in Azure AD steuern, wer auf HR2day by Merces Zugriff hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei HR2day by Merces anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im klassischen Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen

Um die Azure AD-Integration mit HR2day by Merces konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein HR2day by Merces-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.


## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von HR2day by Merces aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von HR2day by Merces aus dem Katalog
Zum Konfigurieren der Integration von HR2day by Merces in Azure AD müssen Sie HR2day by Merces aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um HR2day by Merces aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **What do you want to do** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie im Suchfeld **HR2day by Merces** ein.<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)<br>
7. Wählen Sie im Ergebnisbereich **HR2day by Merces** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen. <br><br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden von Azure AD in HR2day by Merces konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in HR2day by Merces als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in HR2day by Merces muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den Wert des **Benutzernamens** in Azure AD als Wert dem **Benutzernamen** in HR2day by Merces zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit HR2day by Merces müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines HR2day by Merces-Testbenutzers](#creating-a-hr2day-by-merces-test-user)**, um eine Entsprechung von Britta Simon in HR2day by Merces zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei HR2day by Merces zu authentifizieren.

Die HR2day by Merces-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihre Konfiguration der SAML-Tokenattribute mit benutzerdefinierten Attributzuordnungen versehen. Der folgende Screenshot zeigt ein Beispiel für diesen Vorgang: <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) <br>

Bevor Sie die SAML-Assertion konfigurieren können, müssen Sie sich über [servicedesk@merces.nl](mailto:servicedesk@merces.nl) an Ihr HR2day-Supportteam wenden und für Ihren Mandanten den Wert des Attributs für den eindeutigen Bezeichner anfordern. Sie benötigen diesen Wert, um die Schritte im nächsten Abschnitt abzuschließen.


**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in HR2day by Merces folgende Schritte aus:**

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite von **HR2day by Merces** im Menü oben auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) <br>

2. Führen Sie die folgenden Schritte durch, um die erforderlichen Attributzuordnungen hinzuzufügen: <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) <br>


	 a. Klicken Sie auf **Benutzerattribut hinzufügen**.

	 b. Geben Sie im Textfeld **Attributname** die Zeichenfolge **ATTR\_LOGINCLAIM** ein.

	 c. Wählen Sie in der Liste **Attributwert** den Wert **Join()** aus.

	 d. Wählen Sie in der Liste **String1** die Option **User.mail** aus.

	 e. Geben Sie im Textfeld **String2** den **eindeutigen Bezeichner** ein, der von Ihrem HR2day-Team bereitgestellt wurde.

	 f. Geben Sie im Textfeld **Trennzeichen** das Zeichen **@** ein.

	 g. Klicken Sie auf **Fertig stellen**.

  
3. Klicken Sie auf **Änderungen übernehmen**.


1. Klicken Sie im Menü am oberen Rand auf **Schnellstart**, um das Dialogfeld **Schnellstart** zu öffnen. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) <br>



1. Klicken Sie auf **Einmaliges Anmelden konfigurieren** um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei HR2day by Merces anmelden** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) <br>

3. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) <br>


    a. Geben Sie im Textfeld „Anmelde-URL“ die URL ein, die von Ihren Benutzern nach folgendem Muster zur Anmeldung bei der HR2day by Merces-Anwendung verwendet wird: **https://<mandantenname>.force.com/<instanzname>**.

    b. Klicken Sie auf **Weiter**.

4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für HR2day by Merces** folgende Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen** und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


5. Für die Konfiguration von SSO für Ihre Anwendung wenden Sie sich über [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) an das Supportteam von HR2day by Merces. Fügen Sie die heruntergeladene Zertifikatsdatei an Ihre E-Mail an. Geben Sie außerdem die SAML-SSO-URL, die Abmelde-URL und die Aussteller-URL an, damit diese für die SSO-Integration (Single Sign-On, einmaliges Anmelden) konfiguriert werden können.


> [AZURE.NOTE] Informieren Sie das Merces-Team darüber, dass für diese Integration das Festlegen der Entitäts-ID mit diesem Muster erforderlich ist: ****https://hr2day.force.com/INSTANCENAME**



6. Wählen Sie im klassischen Azure-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus und klicken Sie dann auf **Weiter**. <br><br>![Azure AD – einmaliges Anmelden][10]<br>

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**. <br><br>![Azure AD – einmaliges Anmelden][11]



### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im klassischen Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png)<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) <br>

4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) <br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) <br>

    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Option **Benutzer** aus.

    e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) <br>

8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) <br>

    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.



### Erstellen eines HR2day by Merces-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in HR2day by Merces. Wenden Sie sich an das Supportteam von HR2day by Merces, um die Benutzer unter dem HR2day by Merces-Konto hinzuzufügen.


> [AZURE.NOTE] Wenn Sie einen Benutzer manuell erstellen müssen, setzen Sie sich mit dem Supportteam von HR2day by Merces in Verbindung.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts besteht darin, Britta Simon die Verwendung des einmaligen Anmeldens bei Azure zu ermöglichen, indem sie Zugriff auf HR2day by Merces erhält. <br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon HR2day by Merces zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im klassischen Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**. <br><br>![Benutzer zuweisen][201] <br>

2. Wählen Sie in der Anwendungsliste **HR2day by Merces** aus. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) <br>

1. Klicken Sie im oberen Menü auf **Benutzer**. <br><br>![Benutzer zuweisen][203] <br>

1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel „HR2day by Merces“ klicken, sollten Sie automatisch bei Ihrer HR2day by Merces-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0309_2016-->