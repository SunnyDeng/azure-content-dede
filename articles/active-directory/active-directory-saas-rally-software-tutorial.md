<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Rally Software | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Rally Software mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Rally Software
  
In diesem Tutorial wird die Integration von Azure und Rally Software erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Rally Software-Mandanten
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Rally Software
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Szenario")
##Aktivieren der Anwendungsintegration für Rally Software
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Rally Software aktivieren.

###So aktivieren Sie die Anwendungsintegration für Rally Software

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **rally** ein.

    ![Anwendungskatalog](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Rally Software** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Rally Software zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Rally Software** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Rally anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD – einmaliges Anmelden")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Rally Software-Mandanten-URL** die URL im Format „*https://\<Mandantenname>.rally.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-rally-software-tutorial/IC769529.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Rally** auf „Metadaten herunterladen“, und speichern Sie die Daten auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich bei Ihrem **Rally Software**-Mandanten an.

6.  Klicken Sie in der Symbolleiste oben auf **Einrichtung** und dann auf **Abonnement**.

    ![Abonnement](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Abonnement")

7.  Klicken Sie oben rechts auf der Symbolleiste auf die Schaltfläche **Aktion**, und wählen Sie dann **Abonnement bearbeiten**.

8.  Führen Sie auf der Dialogfeldseite **Abonnement** die folgenden Schritte aus, und klicken Sie dann auf **Speichern und schließen**:

    ![Authentifizierung](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentifizierung")

    1.  Wählen Sie im Dropdownmenü für die Authentifizierung die Option **Rally- oder SSO-Authentifizierung** aus.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Rally Software** den Wert der **Identitätsanbieter-ID**, und fügen Sie ihn in das Textfeld **Identitätsanbieter-URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Rally Software** den Wert für **Remoteabmelde-URL**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich AAD-Benutzer anmelden können, müssen sie in der Rally Software-Anwendung unter Verwendung ihrer Azure Active Directory-Benutzernamen bereitgestellt werden.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem Rally Software-Mandanten an.

2.  Navigieren Sie zu **Einrichtung > BENUTZER**, und klicken Sie auf **+ Neuen Benutzer hinzufügen**.

    ![Benutzer](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Benutzer")

3.  Geben Sie den Namen in das Textfeld für den neuen Benutzer ein, und klicken Sie dann auf **Mit Details hinzufügen**.

4.  Führen Sie im Abschnitt **Benutzer erstellen** die folgenden Schritte aus:

    ![Create User](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Benutzer erstellen")

    1.  Geben Sie im Textfeld **Benutzername** den Namen des Azure AD-Benutzers ein, den Sie bereitstellen möchten.
    2.  Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Azure AD-Benutzers ein, den Sie bereitstellen möchten.
    3.  Klicken Sie auf **Speichern und schließen**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Rally Software-Benutzerkonten oder mithilfe der von Rally Software bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Rally Software Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Rally Software** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->