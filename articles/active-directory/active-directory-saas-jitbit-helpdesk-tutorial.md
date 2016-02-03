<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Jitbit Helpdesk | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Jitbit Helpdesk mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Tutorial: Azure Active Directory-Integration mit Jitbit Helpdesk
  
In diesem Tutorial wird die Integration von Azure und Jitbit Helpdesk erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Jitbit Helpdesk-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Jitbit Helpdesk zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf der Jitbit Helpdesk-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie auch die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Jitbit Helpdesk
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Szenario")
##Aktivieren der Anwendungsintegration für Jitbit Helpdesk
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Jitbit Helpdesk aktivieren.

###So aktivieren Sie die Anwendungsintegration für Jitbit Helpdesk

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** **Jitbit Helpdesk** ein.

    ![Anwendungskatalog](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Jitbit Helpdesk** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Jitbit Helpdesk zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

>[AZURE.IMPORTANT]Um einmaliges Anmelden für Ihren Jitbit Helpdesk-Mandanten konfigurieren zu können, müssen Sie sich zunächst an den technischen Support von Jitbit Helpdesk wenden und dieses Feature aktivieren lassen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Jitbit Helpdesk** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Jitbit Helpdesk anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Jitbit Helpdesk-Anmelde-URL** die URL im Format „*https://\<Mandantenname>.Jitbit.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Jitbit Helpdesk** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Jitbit Helpdesk.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Jitbit Helpdesk-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben auf der Symbolleiste auf **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Verwaltung")

7.  Klicken Sie auf **Allgemeine Einstellungen**.

    ![Benutzer, Unternehmen und Berechtigungen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Benutzer, Unternehmen und Berechtigungen")

8.  Führen Sie im Konfigurationsabschnitt **Authentifizierungseinstellungen** die folgenden Schritte aus:

    ![Authentifizierungseinstellungen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Authentifizierungseinstellungen")

    1.  Wählen Sie **Einmaliges Anmelden mit SAML 2.0 aktivieren** mit SSO mit **OneLogin** aus.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Jitbit Helpdesk** den Wert von **Vom Dienstanbieter initiierter Endpunkt**, und fügen Sie ihn in das Textfeld **Endpunkt-URL** ein.
    3.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.
        
		>[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    4.  Öffnen Sie das Base-64-codierte Zertifikat, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.
    5.  Klicken Sie auf **Änderungen speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Jitbit Helpdesk anmelden können, müssen sie in Jitbit Helpdesk bereitgestellt werden. Im Fall von Jitbit Helpdesk ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Jitbit Helpdesk**-Mandanten an.

2.  Klicken Sie oben im Menü auf **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Verwaltung")

3.  Klicken Sie auf **Benutzer, Unternehmen und Berechtigungen**.

    ![Benutzer, Unternehmen und Berechtigungen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Benutzer, Unternehmen und Berechtigungen")

4.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Benutzer hinzufügen")

5.  Geben Sie im Abschnitt „Erstellen“ die Daten des Azure AD-Kontos, das Sie bereitstellen möchten, in die Textfelder für **Benutzername**, **E-Mail**, **Vorname** und **Nachname** ein.

    ![Erstellen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Erstellen")

6.  Klicken Sie auf **Erstellen**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Jitbit Helpdesk-Benutzerkonten oder mithilfe der von Jitbit Helpdesk bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Jitbit Helpdesk Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Jitbit Helpdesk** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->