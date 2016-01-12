<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit Halogen Software"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Halogen Software konfigurieren."
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
	ms.date="12/18/2015"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit Halogen Software

In diesem Tutorial erfahren Sie, wie Sie Halogen Software in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von Halogen Software in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer auf Halogen Software Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch für Halogen Software anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Halogen Software konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Ein Halogen Software-Abonnement, für das die einmalige Anmeldung aktiviert ist.


> [AZURE.NOTE]Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von Halogen Software aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Halogen Software aus dem Katalog
Zum Konfigurieren der Integration von Halogen Software in Azure AD müssen Sie Halogen Software aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Halogen Software aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br> ![Active Directory][1]

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]
5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]
6. Geben Sie in das Suchfeld den Begriff **Halogen Software** ein.<br> ![Anwendungen][5]
7. Wählen Sie im Ergebnisbereich **Halogen Software**, und klicken Sie dann auf **Fertigstellen**, um die Anwendung hinzuzufügen.<br>



##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens "Britta Simon" das einmalige Anmelden von Azure AD in Halogen Software konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in Halogen Software zu einem Benutzer in Azure AD ist. Anders ausgedrückt muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Halogen Software eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den Wert des **Benutzernames** in Azure AD als Wert zum **Benutzernamen** in Halogen Software zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Halogen Software müssen Sie die folgenden Bausteinen ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)** – um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** – um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines Halogen Software-Testbenutzers](#creating-a-halogen-software-test-user)** – um eine Entsprechung von Britta Simon in Halogen Software zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** – um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)** – um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Das Ziel dieses Abschnitts ist es, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in Azure in Ihrer Halogen Software-Anwendung zu konfigurieren.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Halogen Software die folgenden Schritte aus:**

1. Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite **Halogen Software** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.<br><br> ![Einmaliges Anmelden konfigurieren][8]

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Halogen Software anmelden** **Azure AD einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.<br><br> ![Azure AD – einmaliges Anmelden][9]

3. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Konfigurieren von App-Einstellungen][10]
 
     3\.1 Geben Sie im Textfeld **Anmelde-URL** die URL ein, die von Ihren Benutzern nach folgendem Muster zur Anmeldung bei der Halogen Software verwendet wird: **https://global.hgncloud.com/fabrikam/welcome.jsp*

     3\.2. Klicken Sie auf **Weiter**.
 
4. Klicken Sie auf der Seite **Einmaliges Anmelden für Halogen Software konfigurieren** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.<br><br>![Was ist Azure AD Connect?][11]

5. Melden Sie sich in einem anderen Browserfenster in Ihrer **Halogen Software**-Anwendung als Administrator an.
6. Klicken Sie auf die Registerkarte **Options**. <br><br>![Was ist Azure AD Connect?][12]
7. Klicken Sie im linken Navigationsbereich auf **SAML-Konfiguration**. <br><br>![Was ist Azure AD Connect?][13]
8. Führen Sie auf der Seite **SAML-Konfiguration** die folgenden Schritte aus. <br><br>![Was ist Azure AD Connect?][14]

     8\.1. Wählen Sie als **Eindeutigen Bezeichner** **NameID** aus.

     8\.2. Wählen Sie für **Eindeutiger Bezeichner ist zugeordnet** **Benutzername** aus.

     8\.3. Klicken Sie zum Hochladen der heruntergeladenen Metadatendatei auf **Durchsuchen**, um die Datei auszuwählen, und dann auf **Datei hochladen**.

     8\.4. Klicken Sie zum Testen der Konfiguration auf **Durchführen des Tests**. >[AZURE.NOTE]Sie müssen warten, bis die Meldung "*Der SAML-Test ist abgeschlossen. Schließen Sie dieses Fenster*" erscheint. Schließen Sie dann das geöffnete Browserfenster. <br> Das Kontrollkästchen **SAML aktivieren** ist nur aktiviert, wenn der Test abgeschlossen wurde.

     8\.5. Wählen Sie **SAML aktivieren**.
    
     8\.6. Klicken Sie auf **Änderungen speichern**.


9. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen. <br><br>![Was ist Azure AD Connect?][15]
10. Klicken Sie auf der Seite **Bestätigung für einmaliges Anmelden** auf **Abschließen**. <br><br>![Was ist Azure AD Connect?][16]




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
  
 
### Erstellen eines Testbenutzers für Halogen Software

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Halogen Software.

**Um einen Benutzer namens Britta Simon in Halogen Software zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich in Ihrer **Halogen Software**-Anwendung als Administrator an.
2. Klicken Sie auf die Registerkarte **Benutzercenter** und dann auf **Benutzer erstellen**. <br><br>![Was ist Azure AD Connect?][300]  
3. Führen Sie auf der Dialogfeldseite **Neuer Benutzer** die folgenden Schritte aus: <br><br>![Was ist Azure AD Connect?][301]
  1. Geben Sie in das Textfeld **Vorname** **Britta** ein. 
  2. Geben Sie in das Textfeld **Nachname** **Simon** ein.
  3. Geben Sie im Textfeld **Benutzername** **Britta Simons Benutzernamen im Azure AD-Portal** ein.
  4. Geben Sie im Textfeld **Kennwort** ein Kennwort für Britta ein.
  5. Klicken Sie auf **Speichern**.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist es, Britta Simon für das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf Halogen Software erhält. <br><br>![Was ist Azure AD Connect?][200]

**Um Britta Simon Halogen Software zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br> <br><br>![Was ist Azure AD Connect?][201]
2. Wählen Sie in der Anwendungsliste **Halogen Software** aus. <br><br>![Was ist Azure AD Connect?][202]
1. Klicken Sie im oberen Menü auf **Benutzer**.<br> <br><br>![Was ist Azure AD Connect?][203]
1. Wählen Sie in der Benutzerliste **Britta Simon** aus. <br><br>![Was ist Azure AD Connect?][204]
2. Klicken Sie in der Symbolleiste unten auf **Zuweisen**. <br><br>![Was ist Azure AD Connect?][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie auf die Kachel Halogen Software im Zugriffsbereich klicken, sollten Sie automatisch in Ihrer Halogen Software-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

<!---HONumber=AcomDC_1223_2015-->