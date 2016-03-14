<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit CS Stars | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und CS Stars konfigurieren."
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
	ms.date="02/29/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit CS Stars

In diesem Tutorial erfahren Sie, wie Sie CS Stars in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von CS Stars in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer auf CS Stars Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei CS Stars anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im klassischen Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit CS Stars konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein CS Stars-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus drei großen Bausteinen:

1. Hinzufügen von CS Stars aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von CS Stars aus dem Katalog
Zum Konfigurieren der Integration von CS Stars in Azure AD müssen Sie CS Stars aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um CS Stars aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br>![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**. <br><br>![Anwendungen][2]<br>

4. Klicken Sie unten auf der Seite auf **Hinzufügen**. <br><br>![Anwendungen][3]<br>

5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**. <br><br>![Anwendungen][4]<br>

6. Geben Sie im Suchfeld den Text **CS Stars** ein. <br><br>![Anwendungen][5]<br>

7. Wählen Sie im Ergebnisbereich **CS Stars** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen. <br><br>![Anwendungen][400]<br>



##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens „Britta Simon“ das einmalige Anmelden von Azure AD in CS Stars konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in CS Stars zu einem Benutzer in Azure AD ist. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in CS Stars muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den Wert des **Benutzernamens** in Azure AD als Wert dem **Benutzernamen** in CS Stars zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei CS Stars müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines CS Stars-Testbenutzers](#creating-a-cs-stars-test-user)**, um eine Entsprechung von Britta Simon in CS Stars zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts besteht darin, das einmalige Anmelden von Azure AD im klassischen Azure-Portal zu aktivieren und das einmalige Anmelden in Ihrer CS Stars-Anwendung zu konfigurieren.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in CS Stars die folgenden Schritte aus:**

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **CS Stars** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br>![Einmaliges Anmelden konfigurieren][6]<br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei CS Stars anmelden** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br>![Azure AD – einmaliges Anmelden][7]<br>

3. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Konfigurieren von App-Einstellungen][8]<br>
 
     3\.1 Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei der CS Stars-Anwendung verwenden (z. B. **https://uat.csstars.com/enterprise/default.cmdx?ssoclient=C234UAT2*)).

     > [AZURE.NOTE] Wenden Sie sich an Ihren Ansprechpartner von Marsh ClearSight, falls Sie den richtigen Wert nicht kennen.

     3\.2. Klicken Sie auf **Weiter**.
 
4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren unter CS Stars** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer. <br><br>![Was ist Azure AD Connect?][9]<br>

5. Wenden Sie sich an Ihren Ansprechpartner von Marsh ClearSight, und übergeben Sie die Metadatendatei, um das einmalige Anmelden für CS Stars aktivieren zu lassen.


6. Wählen Sie im klassischen Azure-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus, und klicken Sie dann auf **Weiter**. <br><br>![Was ist Azure AD Connect?][10]<br>

7. Klicken Sie auf der Seite **Bestätigung für einmaliges Anmelden** auf **Abschließen**. <br><br>![Was ist Azure AD Connect?][11]<br>




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im klassischen Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus. <br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)<br> 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)<br>
 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)<br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)<br>
  1. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.
  2. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.
  3. Klicken Sie auf Weiter.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)<br>
  1. Geben Sie in das Textfeld **Vorname** den Text **Britta** ein.  
  2. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.
  3. Geben Sie in das Textfeld **Anzeigename** den Text **Britta Simon** ein.
  4. Wählen Sie in der Liste **Rolle** **Benutzer** aus.
  5. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Temporäres Kennwort abrufen** auf **Erstellen**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)<br>
 
8. Führen Sie auf der Dialogfeldseite **Temporäres Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)<br>
  1. Notieren Sie den Wert von **Neues Kennwort**.
  2. Klicken Sie auf **Fertig stellen**.   

  
 
### Erstellen eines CS Stars-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in CS Stars.

**Um einen Benutzer namens Britta Simon in CS Stars zu erstellen, führen Sie die folgenden Schritte aus:**

1. Wenden Sie sich an Ihren Ansprechpartner bei Marsh ClearSight.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist, Britta Simon für das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf CS Stars erhält. <br><br>![Benutzer zuweisen][200]<br>

**Um Britta Simon CS Stars zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im klassischen Azure-Portal auf der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**. <br><br>![Benutzer zuweisen][201] <br>
2. Wählen Sie in der Anwendungsliste **CS Stars** aus. <br><br>![Benutzer zuweisen][202]<br>
1. Klicken Sie im oberen Menü auf **Benutzer**. <br><br><br>![Benutzer zuweisen][203]<br>
1. Wählen Sie in der Benutzerliste den Eintrag **Britta Simon** aus.

2. Klicken Sie in der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]<br>



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel „CS Stars“ klicken, sollten Sie automatisch in Ihrer CS Stars-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_01.png
[6]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_02.png
[7]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_03.png
[8]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_04.png
[9]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_05.png
[10]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_06.png
[11]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_07.png
[20]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_202.png
[203]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_403.png

<!---HONumber=AcomDC_0302_2016-->