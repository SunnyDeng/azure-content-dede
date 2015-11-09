<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit TimeOffManager | Microsoft Azure" 
    description="Erfahren Sie, wie Sie TimeOffManager mit Azure Active Directory verwenden können, um das einmalige Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." 
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

#Tutorial: Azure Active Directory-Integration mit TimeOffManager
  
In diesem Tutorial wird die Integration von Azure und TimeOffManager erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein TimeOffManager-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie TimeOffManager zugewiesen haben, mittels einmaliger Anmeldung auf der TimeOffManager-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für TimeOffManager
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Szenario")

##Aktivieren der Anwendungsintegration für TimeOffManager
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für TimeOffManager aktivieren.

###So aktivieren Sie die Anwendungsintegration für TimeOffManager:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **TimeOffManager** ein.

    ![Anwendungskatalog](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **TimeOffManager** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei TimeOffManager zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren TimeOffManager-Mandanten hochladen.  
Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **TimeOffManager** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei TimeOffManager anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** in das Textfeld **TimeOffManager-Antwort-URL** die TimeOffManager AssertionConsumerService-URL ein (z. B.: „* Beispiel: https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "App-URL konfigurieren")

    Sie können die Antwort-URL von der Seite zur Einstellung der einfachen Anmeldung beim TimeOffManager abrufen.

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Einstellungen für einmaliges Anmelden")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für TimeOffManager** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der TimeOffManager-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Konto > Kontooptionen > Einstellungen für einmaliges Anmelden**.

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Einstellungen für einmaliges Anmelden")

7.  Führen Sie im Abschnitt **Einstellungen für einmaliges Anmelden** die folgenden Schritte aus:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Einstellungen für einmaliges Anmelden")

    1.  Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache).

    2.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie anschließend das gesamte Zertifikat in das Textfeld **X.509-Zertifikat** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für TimeOffManager** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld **IdP-Aussteller** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für den TimeOffManager** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **IdP-Endpunkt-URL** ein.
    5.  Wählen Sie unter **SAML erzwingen** die Option **Nein** aus.
    6.  Wählen Sie unter **Benutzer automatisch erstellen**, die Option **Ja** aus.
    7.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für TimeOffManager** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn ins Textfeld **Abmelde-URL** ein.
    8.  Klicken Sie auf **Änderungen speichern**.

8.  Wählen Sie im Azure-Portal auf der Dialogseite **Einmaliges Anmelden konfigurieren für TimeOffManager** die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Einmaliges Anmelden konfigurieren")

9.  Klicken Sie oben im Menü auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen.

    ![Attribute](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Attribute")

10. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![SAML-Tokenattribute](./media/active-directory-saas-timeoffmanager-tutorial/IC795921.png "SAML-Tokenattribute")

    |Attributname|Attributwert|
	|---|---|
    |Firstname|User.givenname|
	|Lastname|User.surname|

    1.  Klicken Sie für jede Datenzeile in der obigen Tabelle auf **Benutzerattribut hinzufügen**.
    2.  Geben Sie im Textfeld **Attributname** den für die Zeile angezeigten Attributnamen ein.
    3.  Geben Sie im Textfeld **Attributwert** den für die Zeile angezeigten Attributwert ein.
    4.  Klicken Sie auf **Fertig stellen**.

11. Klicken Sie auf **Änderungen übernehmen**.

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei TimeOffManager anmelden können, müssen sie in TimeOffManager bereitgestellt werden.  
TimeOffManager unterstützt die bedarfsabhängige Benutzerbereitstellung. Für Sie steht kein Aktionselement zur Verfügung.  
Die Benutzer werden automatisch während der erstmaligen und einmaligen Anmeldung hinzugefügt.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von TimeOffManager-Benutzerkonten oder mithilfe der von TimeOffManager bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie TimeOffManager Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **TimeOffManager** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->