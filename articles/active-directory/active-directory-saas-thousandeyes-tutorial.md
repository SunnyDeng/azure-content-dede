<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit ThousandEyes | Microsoft Azure" 
    description="Erfahren Sie, wie Sie ThousandEyes mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit ThousandEyes
  
In diesem Tutorial wird erläutert, wie einmaliges Anmelden für Azure Active Directory (Azure AD) und ThousandEyes eingerichtet wird.
  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein ThousandEyes-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die AAD-Benutzer, denen Sie den Zugriff auf ThousandEyes zugewiesen haben, mittels einmaliger Anmeldung auf der ThousandEyes-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe „Einführung in den AAD-Zugriffsbereich“).

1.  Aktivieren der Anwendungsintegration für ThousandEyes
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Szenario")

##Aktivieren der Anwendungsintegration für ThousandEyes
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ThousandEyes aktivieren.

###So aktivieren Sie die Anwendungsintegration für ThousandEyes:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **ThousandEyes** ein.

    ![Anwendungskatalog](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **ThousandEyes** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei ThousandEyes zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **ThousandEyes** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei ThousandEyes anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **ThousandEyes-Anmelde-URL** die URL-Benutzer ein, welche die Benutzer zur Anmeldung bei ThousandEyes verwenden (z. B. **https://app.thousandeyes.com/login/sso*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für ThousandEyes** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der **ThousandEyes**-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im oberen Menü auf das Symbol **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Einstellungen")

7.  Klicken Sie auf **Konto**.

    ![Konto](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Konto")

8.  Klicken Sie auf die Registerkarte **Sicherheit und Authentifizierung**.

    ![Sicherheit und Authentifizierung](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Sicherheit und Authentifizierung")

9.  Führen Sie im Abschnitt **Einmaliges Anmelden einrichten** die folgenden Schritte aus:

    ![Einmaliges Anmelden einrichten](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Einmaliges Anmelden einrichten")

    1.  Wählen Sie **Einmaliges Anmelden aktivieren** aus.
    2.  Kopieren Sie im Microsoft Azure-Portal auf der Seite **Einmaliges Anmelden konfigurieren für ThousandEyes** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn ins Textfeld **Anmeldeseiten-URL** ein.
    3.  Kopieren Sie im Microsoft Azure-Portal auf der Seite **Einmaliges Anmelden konfigurieren für ThousandEyes** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn ins Textfeld **Abmeldeseiten-URL** ein.
    4.  Kopieren Sie im Microsoft Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für ThousandEyes** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Identitätsanbieter-Aussteller** ein.
    5.  Klicken Sie auf dem **Zertifikat des Identitätsanbieters** auf **Datei auswählen**, und laden Sie dann das Zertifikat hoch, welches Sie aus dem Microsoft Azure-Portal heruntergeladen haben.
    6.  Klicken Sie auf **Speichern**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei ThousandEyes anmelden können, müssen sie in ThousandEyes bereitgestellt werden. Im Fall von ThousandEyes ist die Bereitstellung eine manuelle Aufgabe.

###Führen Sie zum Bereitstellen von Benutzerkonten in ThousandEyes die folgenden Schritte aus:

1.  Melden Sie sich bei der ThousandEyes-Unternehmenswebsite als Administrator an.

2.  Klicken Sie auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Einstellungen")

3.  Klicken Sie auf **Account**.

    ![Konto](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Konto")

4.  Klicken Sie auf Registerkarte **Konten und Benutzer**.

    ![Konten und Benutzer](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Konten und Benutzer")

5.  Führen Sie im Abschnitt **Benutzer und Konten hinzufügen** die folgenden Schritte aus:

    ![Benutzerkonten hinzufügen](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Benutzerkonten hinzufügen")

    1.  Geben Sie **Name**, **E-Mail** und weitere Details eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Neuen Benutzer zum Konto hinzufügen**.

        >[AZURE.NOTE]Der AAD-Kontoinhaber erhält eine E-Mail mit einem Link zur Bestätigung und Aktivierung des Kontos.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von ThousandEyes-Benutzerkonten oder mithilfe der von ThousandEyes bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ThousandEyes Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **ThousandEyes** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->