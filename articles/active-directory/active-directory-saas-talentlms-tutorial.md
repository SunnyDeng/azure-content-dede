<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit TalentLMS | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie TalentLMS mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit TalentLMS
  
In diesem Tutorial wird die Integration von Azure und TalentLMS erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein TalentLMS-Mandant
  
Nach Abschluss dieses Tutorials können sich die TalentLMS zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer TalentLMS-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für TalentLMS
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Szenario")

##Aktivieren der Anwendungsintegration für TalentLMS
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für TalentLMS aktivieren.

###So aktivieren Sie die Anwendungsintegration für TalentLMS:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** **TalentLMS** ein.

    ![Anwendungskatalog](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **TalentLMS** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei TalentLMS zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für TalentLMS müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für ** TalentLMS** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei TalentLMS anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die ** TalentLMS-Anmelde-URL** die URL im Format „*https://\<Mandantenname>. TalentLMSapp.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![Anmelde-URL](./media/active-directory-saas-talentlms-tutorial/IC777294.png "Anmelde-URL")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für TalentLMS** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\ TalentLMS.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der TalentLMS-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Abschnitt **Konto und Einstellungen** auf die Registerkarte **Benutzer**.

    ![Konto und Einstellungen](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Konto und Einstellungen")

7.  Klicken Sie auf **Einmaliges Anmelden (SSO)**.

8.  Führen Sie im Abschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Einmaliges Anmelden")

    1.  Wählen Sie aus der Liste **SSO-Integrationstyp** die Option **SAML 2.0** aus.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für TalentLMS** den Wert der **Identitätsanbieter-ID**, und fügen Sie ihn in das Textfeld **Identitätsanbieter (IdP)** ein.
    3.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Fingerabdruck des Zertifikats** ein.

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI).

    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um TalentLMS** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn ins Textfeld **Remoteanmelde-URL** ein.
    5.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um TalentLMS** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn ins Textfeld **Remoteabmelde-URL** ein.
    6.  Geben Sie in das Textfeld **TargetedID** ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** ein.
7.  Geben Sie in das Textfeld **Vorname** ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** ein.
8.  Geben Sie in das Textfeld **Nachname** ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** ein.
9.  Geben Sie in das Textfeld **E-Mail** ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** ein.
10. Klicken Sie auf **Speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei TalentLMS anmelden können, müssen sie in TalentLMS bereitgestellt werden. Im Fall von TalentLMS ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit:

1.  Melden Sie sich bei Ihrem **TalentLMS**-Mandanten an.

2.  Klicken Sie auf **Benutzer** und dann auf ** Benutzer hinzufügen**.

3.  Führen Sie auf der Dialogfeldseite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Benutzer hinzufügen")

    1.  Geben Sie die zugehörigen Attributwerte des Azure AD-Benutzerkontos in die folgenden Textfelder ein: **Vorname**, **Nachname**, **E-Mail-Adresse**.
    2.  Klicken Sie auf **Benutzer hinzufügen**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von TalentLMS-Benutzerkonten oder mithilfe der von TalentLMS bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie TalentLMS Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **TalentLMS** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->