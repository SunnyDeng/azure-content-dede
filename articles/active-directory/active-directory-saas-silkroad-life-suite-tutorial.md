<properties
	pageTitle="Tutorial: Azure Active Directory-Integration in SilkRoad Life Suite | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und der SilkRoad Life Suite konfigurieren."
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
	ms.date="10/23/2015"
	ms.author="markusvi"/>


# Tutorial: Azure Active Directory-Integration in die SilkRoad Life Suite

In diesem Tutorial erfahren Sie, wie Sie die SilkRoad Life Suite in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration der SilkRoad Life Suite in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer auf die SilkRoad Life Suite zugreifen kann. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei der SilkRoad Life Suite anzumelden (einmaliges Anmelden).

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration in die SilkRoad Life Suite konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Ein SilkRoad Life Suite-Abonnement mit aktiviertem einmaligen Anmelden.


> [AZURE.NOTE]Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen der SilkRoad Life Suite aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen der SilkRoad Life Suite aus dem Katalog
Zum Konfigurieren der Integration der SilkRoad Life Suite in Azure AD müssen Sie die SilkRoad Life Suite aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um die SilkRoad Life Suite aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie in das Suchfeld **SilkRoad Life Suite** ein.<br><br> ![Anwendungen][5]<br>
7. Wählen Sie im Ergebnisbereich **SilkRoad Life Suite** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen. <br><br>![Anwendungen][50]<br>


##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens „Britta Simon“ das einmalige Anmelden von Azure AD in der SilkRoad Life Suite konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in der SilkRoad Life Suite einem Benutzer in Azure AD entspricht. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in der SilkRoad Life Suite muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den Wert des **Benutzernamens** in Azure AD dem **Benutzernamen** in der SilkRoad Life Suite als Wert zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei der SilkRoad Life Suite müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines SilkRoad Life Suite-Testbenutzers](#creating-a-silkroad-life-suite-test-user)**, um eine Entsprechung von Britta Simon in der SilkRoad Life Suite zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Das Ziel dieses Abschnitts ist es, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in Azure in Ihrer Anwendung der SilkRoad Life Suite zu konfigurieren.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei der SilkRoad Life Suite die folgenden Schritte aus:**

5. Melden Sie sich bei Ihrer SilkRoad Life Suite-Unternehmenswebsite als Administrator an. 


    > [AZURE.NOTE]Um Zugriff auf die Authentifizierungsanwendung der Anwendung SilkRoad Life Suite zu erhalten und den Verbund mit Microsoft Azure AD zu konfigurieren, wenden Sie sich an den SilkRoad-Support oder Ihren Vertriebsmitarbeiter für SilkRoad-Dienste.


6. Wechseln Sie zu **Service Provider**, und klicken Sie dann auf **Federation Details**. <br><br>![Azure AD – einmaliges Anmelden][10] <br>


1. Klicken Sie auf **Download Federation Metadata**, und speichern Sie die Metadatendatei dann auf Ihrem Computer. <br><br>![Azure AD – einmaliges Anmelden][11] <br>

3. Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite **SilkRoad Life Suite** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei SilkRoad Life Suite anmelden** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br> ![Azure AD – einmaliges Anmelden][7] <br>

3. Führen Sie auf der Dialogfeldseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Azure AD – einmaliges Anmelden][8] <br>
 
    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei der SilkRoad Life Suite-Website verwenden (z. B. **https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).

    b. Öffnen Sie die heruntergeladene **Silkroad**-Metadatendatei.

    c. Suchen Sie das Tag **AssertionConsumerService**, und kopieren Sie dann das Attribut **Location**. <br><br>![Azure AD – einmaliges Anmelden][21] <br>
   
    d. Fügen Sie den Wert im Textfeld **Antwort-URL** ein.
 
    e. Klicken Sie auf **Weiter**.
 
4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren um SilkRoad Life Suite** die folgenden Schritte aus: <br><br>![Azure AD – einmaliges Anmelden][9] <br>

    a. Klicken Sie auf "Zertifikat herunterladen", und speichern Sie das Zertifikat auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.




1. Klicken Sie in der Anwendung **SilkRoad** auf **Authentication Sources**. <br><br>![Azure AD – einmaliges Anmelden][12] <br>



1. Klicken Sie auf **Add Authentication Source**. <br><br>![Azure AD – einmaliges Anmelden][13] <br>



1. Führen Sie im Abschnitt **Add Authentication Source** die folgenden Schritte aus: <br><br>![Azure AD – einmaliges Anmelden][14] <br>

    a. Klicken Sie unter **Option 2 - Metadata File** auf **Browse**, um die heruntergeladene Metadatendatei hochzuladen.

    b. Klicken Sie auf **Create Identity Provider using File Data**.



1. Klicken Sie im Abschnitt **Authentication Sources** auf **Edit**. <br><br>![Azure AD – einmaliges Anmelden][15] <br>


1. Führen Sie im Dialogfeld **Edit Authentication Source** die folgenden Schritte aus: <br><br>![Azure AD – einmaliges Anmelden][16] <br>

    a. Wählen Sie für **Enabled** die Option **Yes**.

    b. Geben Sie im Textfeld **IdP Description** eine Beschreibung für die Konfiguration ein (z. B. *Azure AD SSO*).

    c. Geben Sie im Textfeld **IdP Name** einen Namen ein, der für Ihre Konfiguration spezifisch ist (z. B. *Azure SP*).

    d. Klicken Sie auf **Speichern**.


6. Deaktivieren Sie alle anderen Authentifizierungsquellen. <br><br>![Azure AD – einmaliges Anmelden][17]<br>

7. Klicken Sie im Azure AD-Portal auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Weiter**. <br><br>![Azure AD – einmaliges Anmelden][18]

1. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**. <br><br>![Azure AD – einmaliges Anmelden][19]


### Erstellen einen Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen einer Testbenutzerin namens Britta Simon im Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png) <br> 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) <br>
 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) <br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Geben Sie im Textfeld **Benutzername** den Namen **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png) <br>
 
    a. Geben Sie im Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie im Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie im Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Rolle **Benutzer** aus. e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**. <br><br> ![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) <br>
 
8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie den Wert aus dem Feld **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.

  
 
### Erstellen einen SilkRoad Life Suite-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in der SilkRoad Life Suite. Britta benötigt eine SSO-ID (auch bezeichnet als *AuthParam*), die Brittas **E-Mail-Adresse** in Azure AD entspricht.

**Führen Sie die folgenden Schritte aus, um eine Benutzerin namens Britta Simon in der SilkRoad Life Suite zu erstellen:**

1. Bitten Sie das SilkRoad Life Suite-Supportteam, einen Benutzer zu erstellen, dessen Attribut für die **SSO-ID** der **E-Mail-Adresse** von Britta Simon in Azure AD entspricht.



### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts besteht darin, Britta Simon für das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf die SilkRoad Life Suite erhält. <br><br>![Benutzer zuweisen][200] <br>

**Führen Sie die folgenden Schritte aus, um Britta Simon der SilkRoad Life Suite zuzuweisen:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im Azure-Portal auf der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br> <br>![Benutzer zuweisen][201]<br>
2. Wählen Sie in der Anwendungsliste **SilkRoad Life Suite** aus. <br><br>![Benutzer zuweisen][202] <br>
1. Klicken Sie im oberen Menü auf **Benutzer**. <br><br>![Benutzer zuweisen][203] <br>
1. Wählen Sie in der Benutzerliste den Eintrag **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel für die SilkRoad Life Suite klicken, sollten Sie automatisch an Ihrer SilkRoad Life Suite-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO1-->