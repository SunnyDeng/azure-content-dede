<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit ADP GlobalView | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ADP GlobalView konfigurieren."
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
	ms.date="03/21/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit ADP GlobalView

In diesem Tutorial erfahren Sie, wie Sie ADP GlobalView in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von ADP GlobalView in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf ADP GlobalView hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ADP GlobalView anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im klassischen Azure-Portal.


Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen

Um die Azure AD-Integration mit ADP GlobalView konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein ADP GlobalView-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.


## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von ADP GlobalView aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von ADP GlobalView aus dem Katalog
Zum Konfigurieren der Integration von ADP GlobalView in Azure AD müssen Sie ADP GlobalView aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um ADP GlobalView aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie im Suchfeld als Suchbegriff **ADP GlobalView** ein.<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)<br>
7. Wählen Sie im Ergebnisbereich **ADP GlobalView** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen. <br><br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens „Britta Simon“ veranschaulicht werden, wie das einmalige Anmelden von Azure AD in ADP GlobalView konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in ADP GlobalView als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ADP GlobalView muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als Wert dem **Benutzernamen** in ADP GlobalView zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei ADP GlobalView müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines ADP GlobalView-Testbenutzers](#creating-a-adp-globalview-test-user)**, um eine Entsprechung von Britta Simon in ADP GlobalView zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts besteht darin, das einmalige Anmelden von Azure AD im klassischen Azure-Portal zu aktivieren und das einmalige Anmelden in der ADP GlobalView-Anwendung zu konfigurieren.

Die ADP GlobalView-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen: Der Anspruchname ist immer eine Kombination aus **PersonImmutableID** und dem Wert, den wir ExtensionAttribute2 zugewiesen haben. Dieses Attribut enthält die Mitarbeiter-ID (EmployeeID) des Benutzers. Hier erfolgt die Benutzerzuordnung von Azure AD zu ADP GlobalView auf Grundlage der EmployeeID. Je nach Anwendungseinstellungen ist jedoch auch eine Zuordnung zu einem anderen Wert möglich. Wenden Sie sich also zunächst an das ADP GlobalView-Team, um den korrekten Bezeichner eines Benutzers zu erfragen, und ordnen Sie diesen Wert dann dem Anspruch **PersonImmutableID** zu. Sie können auch den Email- und den UserID-Anspruch zuordnen, wie im Screenshot gezeigt.
 
<br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png) <br>

Bevor Sie die SAML-Assertion konfigurieren können, müssen Sie sich an Ihr ADP GlobalView-Supportteam wenden und für Ihren Mandanten den Wert des Attributs für den eindeutigen Bezeichner anfordern. Sie benötigen diesen Wert, um den benutzerdefinierten Anspruch für Ihre Anwendung zu konfigurieren.


**Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD bei ADP GlobalView die folgenden Schritte aus:**

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **ADP GlobalView** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei ADP GlobalView anmelden?** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png) <br>

3. Führen Sie auf der Dialogfeldseite **App-Einstellungen konfigurieren** folgende Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png) <br>


    a. Geben Sie im Textfeld **Bezeichner** die URL zum Identifizieren der ADP GlobalView-Anwendung ein. Verwenden Sie hierbei eines der folgenden Muster: **https://<Servername>.globalview.adp.com/federate2**, **https://<Servername>.globalview.adp.com/federate**


    b. Geben Sie im Textfeld **Antwort-URL** die URL ein, die von Azure AD zum Posten der Antwort an die ADP GlobalView-Anwendung verwendet. Verwenden Sie hierbei eines der folgenden Muster: **https://<Servername>.globalview.adp.com/federate2/sp/ACS.saml2**, **https://<Servername>.globalview.adp.com/federate/sp/ACS.saml2**

    c. Klicken Sie auf **Weiter**.


4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für ADP GlobalView** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen** und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


5. Wenden Sie sich an das Supportteam von ADP GlobalView, um SSO für Ihre Anwendung konfigurieren zu lassen, und stellen Sie Folgendes bereit:

    - Die heruntergeladene Zertifikatdatei
    - Entitäts-ID
    - SAML-SSO-URL
    - Dienst-URL für einmaliges Anmelden


    > [AZURE.NOTE] Nachdem das **ADP GlobalView**-Team die Instanz konfiguriert hat, erfragen Sie den Wert **RelayState** und senden ihn an Azure AD, um die Anwendungsinstanz zu konfigurieren. Sie können diesen Wert, die Azure AD-Mandanten-ID und die Anwendungs-ID unter [waadpartners@microsoft.com](mailTo:waadpartners@microsoft.com) an das Azure AD-Team senden. Nach der Konfiguration können Sie die Integration testen – sie funktioniert. Bedenken Sie also, dass dieser Konfigurationsschritt wichtig ist, damit die Anwendungsintegration funktioniert.

6. Wählen Sie im klassischen Azure-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus und klicken Sie dann auf **Weiter**. <br><br>![Azure AD – einmaliges Anmelden][10]<br>

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**. <br><br>![Azure AD – einmaliges Anmelden][11]



### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im klassischen Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png)<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png) <br>

4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png) <br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als „Benutzertyp“ die Option „Neuer Benutzer in Ihrer Organisation“ aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png) <br>

    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Option **Benutzer** aus.

    e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png) <br>

8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png) <br>

    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.



### Erstellen eines ADP GlobalView-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in ADP GlobalView. Arbeiten Sie mit dem ADP GlobalView-Supportteam zusammen, um die Benutzer im ADP GlobalView-Konto hinzuzufügen.


> [AZURE.NOTE] Setzen Sie sich mit dem Supportteam von ADP GlobalView in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts besteht darin, Britta Simon die Verwendung des einmaligen Anmeldens bei Azure zu ermöglichen, indem sie Zugriff auf ADP GlobalView erhält. <br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon ADP GlobalView zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im klassischen Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**. <br><br>![Benutzer zuweisen][201] <br>

2. Wählen Sie in der Anwendungsliste **ADP GlobalView** aus. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png) <br>

1. Klicken Sie im oberen Menü auf **Benutzer**. <br><br>![Benutzer zuweisen][203] <br>

1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel „ADP GlobalView“ klicken, sollten Sie automatisch bei Ihrer ADP GlobalView-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0323_2016-->