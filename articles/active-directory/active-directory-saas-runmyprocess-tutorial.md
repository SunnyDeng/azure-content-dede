<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit RunMyProcess | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie RunMyProcess mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit RunMyProcess
  
In diesem Tutorial wird die Integration von Azure und RunMyProcess erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein RunMyProcess-Mandant
  
Nach Abschluss dieses Tutorials können sich die RunMyProcess zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf der RunMyProcess-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für RunMyProcess
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "Szenario")
##Aktivieren der Anwendungsintegration für RunMyProcess
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für RunMyProcess aktivieren.

###So aktivieren Sie die Anwendungsintegration für RunMyProcess:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** das Wort **RunMyProcess** ein.

    ![Anwendungskatalog](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **RunMyProcess** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei RunMyProcess zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **RunMyProcess** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei RunMyProcess anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **RunMyProcess-Anmelde-URL** die URL im Format „**http://company.runmyprocess.com*"“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für RunMyProcess** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der RunMyProcess-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zur **Konto > Konfiguration**.

    ![Konto](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "Konto")

7.  Klicken Sie auf die Registerkarte **Authentifizierungsmethode**.

8.  Führen Sie im Abschnitt **Authentifizierungsmethode** die folgenden Schritte aus:

    ![SSO](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")

    1.  Wählen Sie **SSO mit Samlv2** als **Methode**.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für RunMyProcess** den Wert der **SAML-SSO-URL**, und fügen Sie ihn in das Textfeld **SAML-Umleitung** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für RunMyProcess** den Wert für **Dienst-URL für einmaliges Abmelden**, und fügen Sie ihn in das Textfeld **Abmeldungsumleitung** ein.
    4.  Geben Sie in das Textfeld **Namens-ID-Format** als Format **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** ein.
    5.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    6.  Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat** ein.
    7.  Klicken Sie auf **Speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei RunMyProcess anmelden können, müssen sie in RunMyProcess bereitgestellt werden. Im Fall von RunMyProcess ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit:

1.  Melden Sie sich bei der **RunMyProcess**-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Konto > Benutzer**, und klicken Sie dann auf **Neuer Benutzer**.

    ![Neuer Benutzer](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "Neuer Benutzer")

3.  Führen Sie im Abschnitt **Benutzereinstellungen** die folgenden Schritte aus:

    ![Profil](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "Profil")

    1.  Geben Sie **Name** und **E-Mail-Adresse** eines gültigen AAD-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Wählen Sie eine **IDE-Sprache**, eine **Sprache** und ein **Profil** aus.
    3.  Wählen Sie **Kontoerstellungs-E-Mail an mich senden** aus.
    4.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von RunMyProcess-Benutzerkonten oder mithilfe der von RunMyProcess bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie RunMyProcess Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **RunMyProcess** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->