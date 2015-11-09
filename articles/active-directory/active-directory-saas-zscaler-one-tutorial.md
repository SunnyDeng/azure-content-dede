<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit ZScaler One | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie ZScaler One mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Zscaler One

In diesem Tutorial wird die Integration von Azure und Zscaler One erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Zscaler One-Abonnement, für das einmaliges Anmelden aktiviert ist  

Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie Zscaler One zugewiesen haben, mittels einmaliger Anmeldung auf Ihrer Zscaler One-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können Sie die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Zscaler One
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren von Proxyeinstellungen
4.  Konfigurieren der Benutzerbereitstellung
5.  Zuweisen von Benutzern  

![Szenario](./media/active-directory-saas-zscaler-one-tutorial/IC800214.png "Szenario")

##Aktivieren der Anwendungsintegration für Zscaler One

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zscaler One aktivieren.

###So aktivieren Sie die Anwendungsintegration für Zscaler One:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.  

	![Active Directory](./media/active-directory-saas-zscaler-one-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

	![Anwendungen](./media/active-directory-saas-zscaler-one-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

	![Anwendung hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

	![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **Zscaler One** ein.

	![Anwendungskatalog](./media/active-directory-saas-zscaler-one-tutorial/IC800215.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Zscaler One** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

	![ZScaler One](./media/active-directory-saas-zscaler-one-tutorial/IC800216.png "ZScaler One")

##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zscaler One zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren Zscaler One-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Zscaler One** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800217.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Zscaler One anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800218.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Zscaler One-Anmelde-URL** die URL ein, welche die Benutzer zur Anmeldung bei Zscaler One verwenden, und klicken Sie dann auf **Weiter**.

	![App-URL konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800219.png "App-URL konfigurieren")

	>[AZURE.NOTE]Sie erhalten bei Bedarf den tatsächlichen Wert für Ihre Umgebung von Ihrem Zscaler One-Supportteam.

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Zscaler One** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800220.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Zscaler One-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben im Menü auf **Verwaltung**.

	![Verwaltung](./media/active-directory-saas-zscaler-one-tutorial/IC800206.png "Verwaltung")

7.  Klicken Sie unter **Administratoren & Rollen verwalten ** auf **Benutzer & Authentifizierung verwalten**.

	![Benutzer & Authentifizierung verwalten](./media/active-directory-saas-zscaler-one-tutorial/IC800207.png "Benutzer & Authentifizierung verwalten")

8.  Führen Sie im Abschnitt **Auswählen von Authentifizierungsoptionen für Ihre Organisation** die folgenden Schritte aus:

	![Authentifizierung](./media/active-directory-saas-zscaler-one-tutorial/IC800208.png "Authentifizierung")

	1.  Wählen Sie **Authentifizeren mit der einmaligen Anmeldung für SAML**.  
	2.  Klicken Sie auf **Einzelne Parameter der einmaligen Anmeldung für SAML konfigurieren**.  

9.  Führen Sie auf der Dialogfeldseite **Einzelne Parameter der einmaligen Anmeldung für SAML konfigurieren** die folgenden Schritte aus, und klicken Sie dann auf **Fertig stellen**.

	![Einmaliges Anmelden](./media/active-directory-saas-zscaler-one-tutorial/IC800209.png "Einmaliges Anmelden")

	1.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Zscaler One** den Wert der **Authentifizierungsanforderungs-URL**, und fügen Sie ihn in das Textfeld **URL des SAML-Portals, an das Benutzer zur Authentifizierung weitergeleitet werden** ein.  
	2.  Geben Sie im Textfeld **Attribut mit Anmeldenamen** die **NameID** ein.  
	3.  Klicken Sie auf **Zscaler pem**, um das heruntergeladene Zertifikat hochzuladen.  
	4.  Wählen Sie **Automatische SAML-Bereitstellung aktivieren**.  

10. Führen Sie auf der Dialogseite **Benutzerauthentifizierung konfigurieren** die folgenden Schritte aus:

	![Verwaltung](./media/active-directory-saas-zscaler-one-tutorial/IC800210.png "Verwaltung")

	1.  Klicken Sie auf **Speichern**.  
	2.  Klicken Sie auf **Jetzt aktivieren**.  

11. Wählen Sie im Azure-Portal auf der Dialogseite **Einmaliges Anmelden konfigurieren für Zscaler One** die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**.

	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800221.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren von Proxyeinstellungen

###So konfigurieren Sie die Proxyeinstellungen in Internet Explorer:

1.  Starten Sie **Internet Explorer**.  

2.  Wählen Sie im Menü **Tools** **Internetoptionen**, um das Dialogfeld **Internetoptionen** zu öffnen.

	!["Internetoptionen",](./media/active-directory-saas-zscaler-one-tutorial/IC769492.png ""Internetoptionen",")

3.  Klicken Sie auf die Registerkarte **Verbindungen**.

	![Verbindungen](./media/active-directory-saas-zscaler-one-tutorial/IC769493.png "Verbindungen")

4.  Klicken Sie zum Öffnen des Dialogfelds **LAN-Einstellungen** auf **LAN-Einstellungen**.

5.  Führen Sie im Abschnitt "Proxyserver" die folgenden Schritte aus:

	![Proxyserver](./media/active-directory-saas-zscaler-one-tutorial/IC769494.png "Proxyserver")

	1.  Wählen Sie "Proxyserver für LAN verwenden" aus.  
	2.  Geben Sie in das Textfeld „Adresse“ **gateway.zscalerone.net** ein.  
	3.  Geben Sie im Textfeld „Port“ **80** ein.  
	4.  Wählen Sie **Proxyserver für lokale Adressen umgehen**.  
	5.  Klicken Sie zum Schließen des Dialogfelds **Local Area Network (LAN) Settings** auf **OK**.  

6.  Klicken Sie zum Schließen des Dialogfelds **Internetoptionen** auf **OK**.

##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei ZScaler One anmelden können, müssen sie in ZScaler One bereitgestellt werden. Im Fall von ZScaler One ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Zscaler One**-Mandanten an.  

2.  Klicken Sie auf **Verwaltung**.

	![Verwaltung](./media/active-directory-saas-zscaler-one-tutorial/IC781035.png "Verwaltung")

3.  Klicken Sie auf **Benutzerverwaltung**.

	![Hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Hinzufügen")

4.  Klicken Sie in der Registerkarte **Benutzer** auf **Hinzufügen**.

	![Hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Hinzufügen")

5.  Führen Sie im Abschnitt "Benutzer hinzufügen" die folgenden Schritte aus:

	![Benutzer hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC781038.png "Benutzer hinzufügen")

	1.  Geben Sie die **Benutzer-ID**, den **Benutzeranzeigenamen**, das **Kennwort** und **Kennwort bestätigen** ein, und wählen Sie dann **Gruppen** und die **Abteilung** eines gültigen AAD-Kontos, das Sie bereitstellen möchten.  
	2.  Klicken Sie auf **Speichern**.  

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von ZScaler One-Benutzerkonten oder mithilfe der von ZScaler One bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ZScaler One Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.  

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Zscaler One** auf **Benutzer zuweisen**.

	![Benutzer zuweisen](./media/active-directory-saas-zscaler-one-tutorial/IC800222.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

	![Ja](./media/active-directory-saas-zscaler-one-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->