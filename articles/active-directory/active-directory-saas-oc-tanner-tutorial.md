<properties
	pageTitle="Lernprogramm: Azure Active Directory-Integration mit O.C. Tanner – AppreciateHub | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und O.C. Tanner – AppreciateHub konfigurieren."
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
	ms.date="10/09/2015"
	ms.author="markusvi"/>


# Lernprogramm: Azure Active Directory-Integration mit O.C. Tanner – AppreciateHub

In diesem Lernprogramm wird die Integration von O.C. Tanner – AppreciateHub mit Azure Active Directory (Azure AD) erläutert.<br>Die Integration von O.C. Tanner – AppreciateHub mit Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer auf O.C. Tanner – AppreciateHub Zugriff hat. 
- Sie können für die Benutzer die automatische Anmeldung bei O.C. Tanner – AppreciateHub (einmaliges Anmelden) mit ihren Azure AD-Konten einrichten.
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Für das Konfigurieren der Azure AD-Integration mit O.C. Tanner – AppreciateHub benötigen Sie die folgenden Elemente:

- Ein Azure AD-Abonnement.
- Ein O.C. Tanner – AppreciateHub-Software-Abonnement, für das die einmalige Anmeldung aktiviert ist.


> [AZURE.NOTE]Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus drei großen Bausteinen:

1. Hinzufügen von O.C. Tanner – AppreciateHub aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von O.C. Tanner – AppreciateHub aus dem Katalog
Für das Konfigurieren der Integration von O.C. Tanner – AppreciateHub in Azure AD müssen Sie O.C. Tanner – AppreciateHub aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Befolgen Sie folgende Schritte, um O.C. Tanner – AppreciateHub aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br> ![Active Directory][1] <br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**. <br><br> ![Anwendungen][2] <br>

4. Klicken Sie unten auf der Seite auf **Hinzufügen**. <br><br> ![Anwendungen][3] <br>

5. Klicken Sie im Dialogfeld **What do you want to do** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>

6. Geben Sie im Suchfeld den Suchbegriff **O.C. Tanner – AppreciateHub** ein. <br><br> ![Anwendungen][5] <br>

7. Wählen Sie im Ergebnisbereich **O.C. Tanner – AppreciateHub** aus, und klicken Sie dann auf **Fertig stellen** um die Anwendung hinzuzufügen. <br><br> ![Anwendungen][25] <br>




##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens "Britta Simon" das einmalige Anmelden mit Azure AD in O.C. Tanner – AppreciateHub konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in O.C. Tanner – AppreciateHub zu einem Benutzer in Azure AD ist. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in O.C. Tanner – AppreciateHub muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als Wert dem **Benutzernamen** in O.C. Tanner – AppreciateHub zuweisen.
 
Für das Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit O.C. Tanner – AppreciateHub, sind folgende Bausteine erforderlich:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines O.C. Tanner – AppreciateHub-Testbenutzers](#creating-a-halogen-software-test-user)** – um eine Entsprechung von Britta Simon in O.C. Tanner – AppreciateHub zu haben, die mit ihrer Azure AD-Darstellung verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Das Ziel dieses Abschnitts ist, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in der O.C. Tanner – AppreciateHub-Anwendung zu konfigurieren.<br>

**Für das Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit O.C. Tanner – AppreciateHub sind folgende Schritte erforderlich:**

1. Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite von **O.C. Tanner – AppreciateHub** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.<br><br> ![Einmaliges Anmelden konfigurieren][6]

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei O.C. Tanner – AppreciateHub anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden**, und klicken Sie dann auf **Weiter**.<br><br> ![Azure AD – einmaliges Anmelden][7]

3. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Konfigurieren von App-Einstellungen][8]
 
     a. Öffnen Sie die Metadatendatei mithilfe des folgenden Links: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).

     b. Suchen Sie den Knoten **Md:AssertionConsumerService**.

     c. Kopieren Sie den Wert des **Location**-Attributs. <br><br>![Konfigurieren von App-Einstellungen][12]
     
     d. Fügen Sie im Textfeld **Anmelde-URL** den im vorherigen Schritt abgerufenen Wert ein.

     >[AZURE.NOTE]Wenden Sie sich bei Problemen beim Auffinden der Antwort-URL in der Metadatendatei an das O.C. Tanner – AppreciateHub-Supportteam unter [sso@octanner.com](mailto:sso@octanner.com).

     e. Klicken Sie auf **Weiter**.
 
4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um O.C. Tanner – AppreciateHub** auf **Download metadata**, und speichern Sie die Metadatendatei dann lokal auf Ihrem Computer.<br><br>![Was ist Azure AD Connect?][9]

5. Kontaktieren Sie das O.C. Tanner – AppreciateHub-Supportteam, stellen Sie ihm die Metadatendatei bereit, und bitten Sie es, SSO für Sie zu aktivieren.


6. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus, und klicken Sie dann auf **Weiter**. <br><br>![Was ist Azure AD Connect?][10]

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**.<br><br>![Was ist Azure AD Connect?][11]




### Erstellen einen Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**.<br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**.<br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png)
 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png)

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png)

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    a. Geben Sie in das Textfeld **Vorname** **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Rolle **Benutzer** aus. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**.<br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png)
 
8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus:<br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png)
  
    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.

  
 
### Erstellen eines O.C. C. Tanner – AppreciateHub-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in O.C. Tanner – AppreciateHub.

**Für das Erstellen eines Benutzers namens Britta Simon in O.C. Tanner – AppreciateHub sind folgende Schritte erforderlich:**

1. Bitten Sie das O.C. Tanner-Supportteam, einen Benutzer zu erstellen, dessen nameID-Attribut dem Benutzernamen von Britta Simon in Azure AD entspricht.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist, Britta Simon für das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf O.C. Tanner – AppreciateHub erhält. <br><br>![Benutzer zuweisen][200]

**Für das Zuweisen von Britta Simon an O.C. Tanner – AppreciateHub sind folgende Schritte erforderlich:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br> <br><br>![Benutzer zuweisen][201]
2. Wählen Sie in der Anwendungsliste **O.C. Tanner – AppreciateHub** aus. <br><br>![Benutzer zuweisen][202]
1. Klicken Sie im oberen Menü auf **Benutzer**.<br> <br><br>![Benutzer zuweisen][203]
1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel "O.C. Tanner – AppreciateHub" klicken, sollten Sie automatisch bei Ihrer O.C. Tanner – AppreciateHub-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png

<!---HONumber=Oct15_HO3-->