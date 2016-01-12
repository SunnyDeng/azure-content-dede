<properties
	pageTitle="Lernprogramm: Azure Active Directory-Integration in die Questetra BPM Suite | Microsoft Aure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und der Questetra BPM Suite konfigurieren."
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


# Lernprogramm: Azure Active Directory-Integration in Questetra BPM Suite

In diesem Lernprogramm erfahren Sie, wie Sie die Questetra BPM Suite in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration der Questetra BPM Suite in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer auf die Questetra BPM Suite Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch für die Questetra BPM Suite anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration in die Questetra BPM Suite konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Ein [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/)-Abonnement mit aktiviertem einmaligen Anmelden


> [AZURE.NOTE]Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Lernprogramm beschriebene Szenario besteht aus zwei Hauptteilen:

1. Hinzufügen der Questetra BPM Suite aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen der Questetra BPM Suite aus dem Katalog
Zum Konfigurieren der Integration der Questetra BPM Suite in Azure AD müssen Sie die Questetra BPM Suite der Liste mit den verwalteten SaaS-Apps aus dem Katalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um die Questetra BPM Suite aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br> ![Active Directory][1]

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]
5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]
6. Geben Sie in das Suchfeld **Questetra BPM Suite** ein.<br> ![Anwendungen][5]
7. Wählen Sie im Ergebnisbereich **Questetra BPM Suite** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.<br>



##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens „Britta Simon“ das einmalige Anmelden von Azure AD in der Questetra BPM Suite konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in der Questetra BPM Suite zu einem Benutzer in Azure AD ist. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in der Questetra BPM Suite muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den Wert des **Benutzernamens** in Azure AD dem **Benutzernamen** in der Questetra BPM Suite als Wert zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei der Questetra BPM Suite müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** – um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines Questetra BPM Suite-Testbenutzers](#creating-a-questetra-bpm-suite-test-user)**, um eine Entsprechung von Britta Simon in der Questetra BPM Suite zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** – um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Das Ziel dieses Abschnitts ist es, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in Azure in Ihrer Anwendung der Questetra BPM Suite zu konfigurieren.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in der Questetra BPM Suite die folgenden Schritte aus:**

1. Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite **Questetra BPM Suite** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.<br><br> ![Einmaliges Anmelden konfigurieren][8]

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Questetra BPM Suite anmelden** die Option **Azure AD einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.<br><br> ![Azure AD – einmaliges Anmelden][9]


3. Melden Sie sich in einem anderen Webbrowserfenster bei der **Questetra BPM Suite**-Unternehmenswebsite als Administrator an.

4. Klicken Sie im oberen Menü auf **Systemeinstellungen**. <br><br> ![Azure AD – einmaliges Anmelden][10]

5. Klicken Sie zum Öffnen der Seite **SingleSignOnSAML** auf **SSO (SAML)**. <br><br> ![Azure AD – einmaliges Anmelden][11]


6. Führen Sie im Azure-Portal auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Konfigurieren von App-Einstellungen][13]
 
    a. Kopieren Sie auf der **Questetra BPM Suite**-Unternehmenswebsite im Abschnitt mit den SP-Informationen die **ACS-URL**, und fügen Sie sie in das Textfeld **Anmelde-URL** ein.

    b. Kopieren Sie auf der Unternehmenswebsite von **Questetra BPM Suite** im Abschnitt mit den SP-Informationen die **Entitäts-ID**, und fügen Sie sie dann in das Textfeld **Aussteller-URL** ein.

    c. Kopieren Sie auf der Unternehmenswebsite von **Questetra BPM Suite** im Abschnitt mit den SP-Informationen die **ACS-URL**, und fügen Sie sie in das Textfeld **Anmelde-URL** ein.

    d. Klicken Sie auf **Weiter**.

 
7. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um Questetra BPM Suite** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.<br><br>![Einmaliges Anmelden konfigurieren][14]


8. Führen Sie auf der Unternehmenswebsite von **Questetra BPM Suite** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren][15]

    a. Wählen Sie **Einmaliges Anmelden aktivieren** aus.
     
    b. Kopieren Sie im Azure-Portal den Wert von **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Entity ID** ein.

    c. Kopieren Sie im Azure-Portal den Wert **Dienst-URL für einmalige Anmeldung**, und fügen Sie ihn in das Textfeld **Sign-in page URL** ein.

    d. Kopieren Sie im Azure-Portal den Wert **Dienst-URL für einmalige Abmeldung**, und fügen Sie ihn in das Textfeld **Sign-out page URL** ein.

    e. Geben Sie in das Textfeld **NameID format** als Format **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** ein.


    f. Erstellen Sie eine Base64-codierte Datei aus dem heruntergeladenen Zertifikat.

    >[AZURE.TIP]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache).

    g. Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Überprüfungszertifikat** ein.

    h. Klicken Sie auf **Speichern**.


9. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus, und klicken Sie dann auf **Weiter**. <br><br>![Was ist Azure AD Connect?][17]


10. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**.<br><br>![Was ist Azure AD Connect?][18]




### Erstellen einen Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br>![Azure AD-Testbenutzer erstellen][100] 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br>![Azure AD-Testbenutzer erstellen][101]

4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br>![Azure AD-Testbenutzer erstellen][102]

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br>![Azure AD-Testbenutzer erstellen][103]
 
    a. Wählen Sie als **Benutzertyp** die Option **Neuer Benutzer in Ihrer Organisation** aus.
  
    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf Weiter.
6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Azure AD-Testbenutzer erstellen][104] 
  
    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.
 
    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Option **Benutzer** aus.

    e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Temporäres Kennwort abrufen** auf **Erstellen**. <br><br>![Azure AD-Testbenutzer erstellen][105]

8. Führen Sie auf der Dialogfeldseite **Temporäres Kennwort abrufen** die folgenden Schritte aus: <br><br>![Azure AD-Testbenutzer erstellen][106]
  1. Notieren Sie den Wert von **Neues Kennwort**.
  2. Klicken Sie auf **Fertig stellen**.   
  
 
### Erstellen eines Questetra BPM Suite-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in der Questetra BPM Suite.

**Führen Sie die folgenden Schritte aus, um eine Benutzerin namens Britta Simon in der Questetra BPM Suite zu erstellen:**

1.	Melden Sie sich auf der Questetra BPM Suite-Unternehmenswebsite als Administrator an.
2.	Wechseln Sie zu **Systemeinstellungen > Benutzerliste > Neuer Benutzer**. 
3.	Führen Sie im Dialogfeld "Neuer Benutzer" die folgenden Schritte aus: <br><br>![Testbenutzer erstellen][300] 

    a. Geben Sie in das Textfeld **Name** den Benutzernamen von Britta in Azure AD ein.

    b. Geben Sie in das Textfeld **E-Mail** den Benutzernamen von Britta in Azure AD ein.

    c. Geben Sie im Textfeld **Kennwort** ein Kennwort ein.

4.	Klicken Sie auf **Neuen Benutzer hinzufügen**.



### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist es, Britta Simon für das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf die Questetra BPM Suite erhält. <br><br>![Was ist Azure AD Connect?][200]

**Führen Sie die folgenden Schritte aus, um Britta Simon der Questetra BPM Suite zuzuweisen:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im Azure-Portal in der Verzeichnisansicht im Menü oben auf **Anwendungen**.<br> <br>![Was ist Azure AD Connect?][201]
2. Wählen Sie in der Anwendungsliste **Questetra BPM Suite** aus. <br><br>![Was ist Azure AD Connect?][205]
1. Klicken Sie im Menü oben auf **Benutzer**.<br> <br>![Was ist Azure AD Connect?][202]
1. Wählen Sie in der Benutzerliste **Britta Simon** aus. <br><br>![Was ist Azure AD Connect?][203]
2. Klicken Sie in der Symbolleiste unten auf **Zuweisen**. <br><br>![Was ist Azure AD Connect?][204]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel für die Questetra BPM Suite klicken, sollten Sie automatisch an Ihrer Questetra BPM Suite-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png

<!---HONumber=AcomDC_1223_2015-->