<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit Moxtra | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Moxtra konfigurieren."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="prasannas"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit Moxtra

In diesem Tutorial erfahren Sie, wie Sie Moxtra in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von Moxtra in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Moxtra hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Moxtra anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im klassischen Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Moxtra konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Moxtra-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE]Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von Moxtra aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Moxtra aus dem Katalog
Zum Konfigurieren der Integration von Moxtra in Azure AD müssen Sie Moxtra aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Moxtra aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br>
![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br>
![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br>
![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **What do you want to do** auf **Anwendung aus dem Katalog hinzufügen**.<br><br>
![Anwendungen][4]<br>
6. Geben Sie im Suchfeld den Suchbegriff **Moxtra** ein.<br><br>
![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)<br>
7. Wählen Sie im Ergebnisbereich **Moxtra** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.
<br><br>
![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_02.png)<br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden von Azure AD in Moxtra konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Moxtra als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Moxtra muss eine Linkbeziehung eingerichtet werden.<br>
Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als Wert für den **Benutzernamen** in Moxtra zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Moxtra müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines Moxtra-Testbenutzers](#creating-a-moxtra-test-user)**, um eine Entsprechung von Britta Simon in Moxtra zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts besteht darin, das einmalige Anmelden von Azure AD im klassischen Azure-Portal zu aktivieren und das einmalige Anmelden in Ihrer Moxtra-Anwendung zu konfigurieren.

Die Moxtra-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diesen Vorgang:
<br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png) <br>



**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Moxtra die folgenden Schritte aus:**

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Moxtra** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.
<br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Moxtra anmelden** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.
<br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png) <br>

3. Führen Sie auf der Dialogfeldseite **App-Einstellungen konfigurieren** die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png) <br>

    a. Geben Sie im Textfeld **Anmelde-URL** die folgende URL ein: ****https://www.moxtra.com/service/#login**.

    b. Klicken Sie auf **Weiter**.
 
 
4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für Moxtra** die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen** und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


1. Melden Sie sich in einem anderen Webbrowserfenster bei der Moxtra-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der Symbolleiste auf der linken Seite auf **Admin-Konsole > SAML Single Sign-On** und dann auf **Neu**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) <br>


1. Führen Sie auf der Seite **SAML** die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png) <br>

    a. Geben Sie im Textfeld **Name** einen Namen für Ihre Konfiguration ein (z. B. *SAML*).

    b. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Moxtra** den Wert der **Entitäts-ID**, und fügen Sie ihn in das Textfeld **IdP-Entitäts-ID** ein.

    c. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Moxtra** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Anmelde-URL** ein.

    d. Geben Sie in das Textfeld **AuthnContextClassRef** die Zeichenkette **urn:oasis:names:tc:SAML:2.0:ac:classes:Password** ein.

    e. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Moxtra** den Wert für **Namensbezeichnerformat**, und fügen Sie ihn in das Textfeld **Namensbezeichnerformat** ein.

    f. Öffnen Sie das heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Textfeld **Zertifikat** ein.

    g. Geben Sie im Textfeld für die SAML-E-Mail-Domäne Ihre SAML-E-Mail-Domäne ein.
    > [AZURE.NOTE]Klicken Sie unten auf „**i**“, um die Schritte zum Überprüfen der Domäne anzuzeigen.


    h. Klicken Sie auf **Aktualisieren**.


6. Wählen Sie im klassischen Azure-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus und klicken Sie dann auf **Weiter**.
<br><br>![Azure AD – einmaliges Anmelden][10]<br>

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**.
<br><br>![Azure AD – einmaliges Anmelden][11]

1. Klicken Sie zum Hinzufügen von benutzerdefinierten Attributzuordnungen zur Konfiguration der SAML-Tokenattribute im Menü oben auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png) <br>



1. Führen Sie für jede Datenzeile in der Tabelle unten die folgenden Schritte aus:

    | Attributname | Attributwert |
    | ---            | ---             |
    | firstname | givenname |
    | lastname | surname |
    | idpid | *<Wert für die **Entitäts-ID** aus dem Dialogfeld **Einmaliges Anmelden konfigurieren für Moxtra** im klassischen Azure-Portal>* |

 
    a. Klicken Sie auf „Benutzerattribut hinzufügen“.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png) <br>

    b. Geben Sie im Dialogfeld **Benutzerattribut hinzufügen** den Attributnamen und den Attributwert ein, der für die jeweilige Zeile in der Tabelle angezeigt wird. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png) <br>

    c. Klicken Sie auf **Fertig stellen**.



1. Klicken Sie auf **Änderungen übernehmen**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png) <br>








### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im klassischen Azure-Portal.<br>
Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**.
<br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png)<br> 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**.
<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) <br>
 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**.
<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) <br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus:
<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus:
<br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png) <br>
 
    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Rolle **Benutzer** aus.
    e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**.
<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png) <br>
 
8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus:
<br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.

  
 
### Erstellen eines Moxtra-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Moxtra.

**Um einen Benutzer namens Britta Simon in Moxtra zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der Moxtra-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf der Symbolleiste auf der linken Seite auf **Admin-Konsole > Benutzerverwaltung** und dann auf **Benutzer hinzufügen**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) <br>



1. Führen Sie im Dialogfeld **Add User** die folgenden Schritte aus:

    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** **Simon** ein.

    c. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse von Britta Simon im klassischen Azure-Portal ein.

    d. Geben Sie im Textfeld **Division** den Text **Dev** ein.

    e. Geben Sie im Textfeld **Abteilung** den Text **IT** ein.

    f. Wählen Sie **Administrator** aus.

    g. Klicken Sie auf **Hinzufügen**.





### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts besteht darin, Britta Simon die Verwendung des einmaligen Anmeldens bei Azure zu ermöglichen, indem sie Zugriff auf Moxtra erhält.
<br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon Moxtra zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im klassischen Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.
<br><br>![Benutzer zuweisen][201] <br>

2. Wählen Sie in der Anwendungsliste **Moxtra** aus.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png) <br>

1. Klicken Sie im oberen Menü auf **Benutzer**.
<br><br>![Benutzer zuweisen][203] <br>

1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**.
<br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br>
Wenn Sie im Zugriffsbereich auf die Kachel „Moxtra“ klicken, sollten Sie automatisch bei Ihrer Moxtra-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0211_2016-->