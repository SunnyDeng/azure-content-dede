<properties
	pageTitle="Lernprogramm: Azure Active Directory-Integration mit SciQuest Spend Director | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und HSciQuest Spend Director konfigurieren."
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
	ms.date="01/05/2016"
	ms.author="jeedes"/>


# Lernprogramm: Azure Active Directory-Integration mit SciQuest Spend Director

In diesem Tutorial erfahren Sie, wie Sie SciQuest Spend Director in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von SciQuest Spend Director in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer auf SciQuest Spend Director Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch für SciQuest Spend Director anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit SciQuest Spend Director konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Ein SciQuest Spend Director-Abonnement, für das die einmalige Anmeldung aktiviert ist.


> [AZURE.NOTE]Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von SciQuest Spend Director aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von SciQuest Spend Director aus dem Katalog
Zum Konfigurieren der Integration von SciQuest Spend Director in Azure AD müssen Sie SciQuest Spend Director aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um SciQuest Spend Director aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br> ![Active Directory][1]

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]
5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]
6. Geben Sie in das Suchfeld **SciQuest Spend Director** ein.<br> ![Anwendungen][5]
7. Wählen Sie im Ergebnisbereich **HSciQuest Spend Director**, und klicken Sie dann auf **Fertigstellen**, um die Anwendung hinzuzufügen.<br>![Anwendungen][6]


##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens "Britta Simon" das einmalige Anmelden von Azure AD in SciQuest Spend Director konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in SciQuest Spend Director zu einem Benutzer in Azure AD ist. Anders ausgedrückt muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SciQuest Spend Director eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den Wert des **Benutzernames** in Azure AD als Wert zum **Benutzernamen** in SciQuest Spend Director zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei SciQuest Spend Director müssen Sie die folgenden Bausteinen ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)** – um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** – um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines SciQuest Spend Director-Testbenutzers](#creating-a-halogen-software-test-user)** – um eine Entsprechung von Britta Simon in SciQuest Spend Director zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** – um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)** – um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Das Ziel dieses Abschnitts ist es, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in Azure in Ihrer SciQuest Spend Director-Anwendung zu konfigurieren.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in SciQuest Spend Director die folgenden Schritte aus:**

1. Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite **SciQuest Spend Director** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.<br><br> ![Einmaliges Anmelden konfigurieren][8]

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei SciQuest Spend Director anmelden** **Azure AD einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.<br><br> ![Azure AD – einmaliges Anmelden][9]

3. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Konfigurieren von App-Einstellungen][10]
 
     3\.1. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die von Ihren Benutzern nach folgendem Muster zur Anmeldung bei der SciQuest Spend Director verwendet wird: **https://.*sciquest.com/.**

     3\.2. Geben Sie im Textfeld **Antwort-URL** den gleichen Wert ein, den Sie in das Textfeld **Anmelde-URL** eingetippt haben.

     3\.3. Klicken Sie auf **Weiter**.
 
4. Klicken Sie auf der Seite **Einmaliges Anmelden für SciQuest Spend Director konfigurieren** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.<br><br>![Was ist Azure AD Connect?][11]

5. Wenden Sie sich an den SciQuest-Support, um diese Authentifizierungsmethode mithilfe der oben genannten heruntergeladenen Metadaten zu aktivieren.

6. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.<br><br>![Was ist Azure AD Connect?][15]
10. Klicken Sie auf der Seite **Bestätigung für einmaliges Anmelden** auf **Abschließen**. <br><br>![What is Azure AD Connect][16]




### Erstellen einen Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br>![Was ist Azure AD Connect?][100] 
2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.
3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br>![Was ist Azure AD Connect?][101] 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br>![Was ist Azure AD Connect?][102] 
5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br>![Was ist Azure AD Connect?][103] 
  1. Wählen Sie als **Benutzertyp** **Neuer Benutzer in Ihrer Organisation** aus.
  2. Geben Sie in das **Textfeld** „Benutzername“ **BrittaSimon** ein.
  3. Klicken Sie auf Weiter.
6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Was ist Azure AD Connect?][104] 
  1. Geben Sie in das Textfeld **Vorname** **Britta** ein.  
  2. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.
  3. Geben Sie in das Textfeld **Anzeigename** den Text **Britta Simon** ein.
  4. Wählen Sie in der Liste **Rolle** **Benutzer** aus.
  5. Klicken Sie auf **Weiter**.
7. Klicken Sie auf der Dialogfeldseite **Temporäres Kennwort abrufen** auf **Erstellen**. <br><br>![Was ist Azure AD Connect?][105]  
8. Führen Sie auf der Dialogfeldseite **Temporäres Kennwort abrufen** die folgenden Schritte aus: <br><br>![Was ist Azure AD Connect?][106]   
  1. Notieren Sie den Wert von **Neues Kennwort**.
  2. Klicken Sie auf **Fertig stellen**.   
  
 
### Erstellen eines SciQuest Spend Director-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in SciQuest Spend Director.

Wenden Sie sich an das Supportteam von SciQuest Spend Director, und geben sie dort die Details zu Ihrem Testkonto an, damit es erstellt wird.

Alternativ können Sie auch die Just-in-Time-Bereitstellung nutzen, ein Feature für einmaliges Anmelden, das von SciQuest Spend Director unterstützt wird. <br> Wenn die Just-in-Time-Bereitstellung aktiviert ist, werden Benutzer automatisch von SciQuest Spend Director beim Versuch des einmaligen Anmeldens erstellt, wenn sie nicht vorhanden sind. Mit dieser Funktion ist es nicht mehr erforderlich, Gegenbenutzer für einmaliges Anmelden manuell zu erstellen.

Damit die Just-in-Time-Bereitstellung aktiviert wird, wenden Sie sich an das Supportteam von SciQuest Spend Director.
  

### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist es, Britta Simon für das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf SciQuest Spend Director erhält. <br><br>![Was ist Azure AD Connect?][200]

**Um Britta Simon SciQuest Spend Director zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br> <br><br>![Was ist Azure AD Connect?][201]
2. Wählen Sie in der Anwendungsliste **SciQuest Spend Director** aus. <br><br>![Was ist Azure AD Connect?][202]
1. Klicken Sie im oberen Menü auf **Benutzer**.<br> <br><br>![Was ist Azure AD Connect?][203]
1. Wählen Sie in der Benutzerliste **Britta Simon** aus. <br><br>![Was ist Azure AD Connect?][204]
2. Klicken Sie in der Symbolleiste unten auf **Zuweisen**. <br><br>![Was ist Azure AD Connect?][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie auf die Kachel SciQuest Spend Director im Zugriffsbereich klicken, sollten Sie automatisch in Ihrer SciQuest Spend Director-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png

<!---HONumber=AcomDC_0107_2016-->