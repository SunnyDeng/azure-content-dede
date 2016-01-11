<properties
	pageTitle="Tutorial: Azure Active Directory-Integration in SD Elements | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SD Elements konfigurieren."
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
	ms.date="12/21/2015"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration in SD Elements

In diesem Tutorial erfahren Sie, wie Sie SD Elements in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von SD Elements in Azure AD bietet folgende Vorteile:

- Sie können in Azure AD den Zugriff auf SD Elements steuern.
- Benutzer können sich mit ihren Azure AD-Konten automatisch bei SD Elements anmelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – in Azure Active Directory. 


Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen

Um die Azure AD-Integration in SD Elements konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SD Elements-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE]Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.


## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von SD Elements aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von SD Elements aus dem Katalog
Zum Konfigurieren der Integration von SD Elements in Azure AD müssen Sie SD Elements aus dem Katalog zu Ihrer Liste verwalteter SaaS-Apps hinzufügen.

**Führen Sie folgende Schritte aus, um SD Elements aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des klassischen Azure-Portals auf **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **What do you want to do** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie **SD Elements** in das Suchfeld ein.<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_01.png)<br>
7. Wählen Sie im Ergebnisbereich **SD Elements** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_02.png)<br>


##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt wird anhand einer Testbenutzerin namens Britta Simon veranschaulicht, wie das einmalige Anmelden von Azure AD in SD Elements konfiguriert und getestet wird.

Zur Verwendung des einmaligen Anmeldens muss Azure AD den entsprechenden Gegenbenutzer in SD Elements kennen. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SD Elements muss eine Verknüpfung eingerichtet werden.<br> Zu diesem Zweck wird der Benutzername aus Azure AD als Benutzername in SD Elements zugewiesen.

Um das einmalige Anmelden von Azure AD mit SD Elements konfigurieren und testen zu können, ist Folgendes erforderlich:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines SD Elements-Testbenutzers](#creating-a-sd-elements-test-user)**, um eine Entsprechung von Britta Simon in SD Elements zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt wird das einmalige Anmelden von Azure AD im klassischen Azure-Portal aktiviert und das einmalige Anmelden in Ihrer SD Elements-Anwendung konfiguriert.

Die SD Elements-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihre Konfiguration der **SAML-Tokenattribute** mit benutzerdefinierten Attributzuordnungen versehen. Der folgende Screenshot zeigt ein Beispiel für diesen Vorgang:

![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_14.png) <br>



**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in SD Elements folgende Schritte aus:**

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **SD Elements** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei SD Elements anmelden** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_03.png) <br>

3. Führen Sie auf der Dialogfeldseite **App-Einstellungen konfigurieren** folgende Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_04.png) <br>


    a. Geben Sie im Textfeld **Aussteller** die Aussteller-URL Ihres Mandanten im folgenden Format ein: *https://\<Mandantenname>.sdelements.com/sso/saml2/metadata*.
   
    b. Geben Sie im Textfeld **Antwort-URL** die Antwort-URL Ihres Mandanten im folgenden Format ein: *https://\<Mandantenname>.sdelements.com/sso/saml2/acs*.

    > [AZURE.NOTE]Die tatsächliche Aussteller-URL und Antwort-URL für Ihren Mandanten erhalten Sie vom [SD Elements-Supportteam](mailto:support@sdelements.com).
      
    c. Klicken Sie auf **Weiter**.


4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für SD Elements** folgende Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


1. Wenden Sie sich zur Aktivierung des einmaligen Anmeldens mit der heruntergeladenen Zertifikatdatei an das [SD Elements-Supportteam](mailto:support@sdelements.com).


5. Melden Sie sich in einem anderen Browserfenster bei Ihrem SD Elements-Mandanten als Administrator an.

6. Klicken Sie im oberen Menü auf „System“ und anschließend auf „Einmaliges Anmelden“. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) <br>


7. Führen Sie im Dialogfeld **Einstellungen für einmaliges Anmelden** folgende Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) <br>

    a. Wählen Sie für **SSO-Typ** die Option **SAML** aus.

    b. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für SD Elements** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld für die Entitäts-ID des Identitätsanbieters ein.

    c. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für SD Elements** den Wert für **Dienst-URL für einmalige Anmeldung**, und fügen Sie ihn in das Textfeld für den SSO-Dienst des Identitätsanbieters ein.

    d. Klicken Sie auf **Speichern**.

6. Bestätigen Sie im klassischen Azure-Portal die Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Weiter**. <br><br>![Azure AD – einmaliges Anmelden][10]<br>

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**. <br><br>![Azure AD – einmaliges Anmelden][11]

1. Klicken Sie oben im Menü auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen.<br><br>![Einmaliges Anmelden konfigurieren][21]<br>


2. Führen Sie für jede Zeile der folgenden Tabelle die folgenden Schritte aus:

    | Attributname | Attributwert |
    | ---            | ---             |
    | E-Mail | user.mail |
    | firstname | user.givenname |
    | lastname | user.surname |


    a. Klicken Sie auf **Benutzerattribut hinzufügen**. <br><br>![Einmaliges Anmelden konfigurieren][23]<br>

    b. Geben Sie im Textfeld **Attributname** den Attributnamen ein, und wählen Sie als Attributwert den für diese Zeile angezeigten Attributwert aus. <br><br>![Einmaliges Anmelden konfigurieren][22]<br>

    c. Klicken Sie auf **Benutzerattribut hinzufügen**. <br><br>![Einmaliges Anmelden konfigurieren][23]<br>

1. Klicken Sie auf **Änderungen übernehmen**. <br><br>![Einmaliges Anmelden konfigurieren][24]<br>

### Erstellen eines Azure AD-Testbenutzers
In diesem Abschnitt wird im klassischen Azure-Portal eine Testbenutzerin namens Britta Simon erstellt.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des klassischen Azure-Portals auf **Active Directory**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_09.png) <br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) <br>

4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) <br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_06.png) <br>

    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Option **Benutzer** aus.

    e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_07.png) <br>

8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_08.png) <br>

    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.



### Erstellen eines SD Elements-Testbenutzers

In diesem Abschnitt wird in SD Elements eine Benutzerin namens Britta Simon erstellt. Im Falle von in SD Elements müssen Benutzer manuell erstellt werden.

**Führen Sie die folgenden Schritte aus, um in SD Elements eine Benutzerin namens Britta Simon zu erstellen:**

1.	Melden Sie sich in einem Webbrowserfenster bei der SD Elements-Unternehmenswebsite als Administrator an.

2.	Klicken Sie im oberen Menü auf „Benutzerverwaltung“ und dann auf „Benutzer“.
 
    ![Erstellen eines SD Elements-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) <br>

3.	Klicken Sie auf „Neuen Benutzer hinzufügen“.
 
    ![Erstellen eines SD Elements-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png) <br>

4.	Führen Sie im Dialogfeld „Neuen Benutzer hinzufügen“ folgende Schritte aus:

    ![Erstellen eines SD Elements-Testbenutzers](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) <br>

    a. Geben Sie in das Textfeld **E-Mail** die E-Mail-Adresse von Britta Simon aus Azure AD ein.

    b. Geben Sie in das Textfeld **Vorname** **Britta** ein.

    c. Geben Sie in das Textfeld **Nachname** **Simon** ein.

    d. Wählen Sie für **Rolle** die Option **Benutzer** aus.

    e. Klicken Sie auf **Benutzer erstellen**.




### Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt wird Britta Simon die Verwendung des einmaligen Anmeldens von Azure ermöglicht, indem ihr Zugriff auf SD Elements gewährt wird. <br><br>![Benutzer zuweisen][200] <br>

**Führen Sie folgende Schritte aus, um Britta Simon SD Elements zuzuweisen:**

1. Klicken Sie im oberen Menü des klassischen Azure-Portals in der Verzeichnisansicht auf **Anwendungen**, um die Anwendungsansicht zu öffnen. <br><br>![Benutzer zuweisen][201] <br>

2. Wählen Sie in der Anwendungsliste **SD Elements** aus. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_50.png) <br>

1. Klicken Sie im oberen Menü auf **Benutzer**. <br><br>![Benutzer zuweisen][203] <br>

1. Wählen Sie in der Benutzerliste die Option **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel „SD Elements“ klicken, werden Sie automatisch in Ihrer SD Elements-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[21]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_82.png
[23]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_83.png


[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1223_2015-->