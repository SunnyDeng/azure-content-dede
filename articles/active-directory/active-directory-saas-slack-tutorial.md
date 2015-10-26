<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Slack | Microsoft Azure" description="Hier erfahren Sie, wie Sie Slack mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Slack
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=536424), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Slack erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Slack-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Slack zugewiesen haben, mittels einmaliger Anmeldung auf der Slack-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Slack
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-slack-tutorial/IC794980.png "Szenario")

##Aktivieren der Anwendungsintegration für Slack
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Slack aktivieren.

###So aktivieren Sie die Anwendungsintegration für Slack:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-slack-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-slack-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-slack-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** **Slack** ein.

    ![Anwendungskatalog](./media/active-directory-saas-slack-tutorial/IC794981.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Slack** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Szenario](./media/active-directory-saas-slack-tutorial/IC796925.png "Szenario")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Slack zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Slack** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-slack-tutorial/IC794982.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Slack anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-slack-tutorial/IC794983.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Slack-Anmelde-URL** die URL Ihres Slack-Mandanten ein (z. B. **https://azuread.slack.com*")), und klicken Sie auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-slack-tutorial/IC794984.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Slack** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-slack-tutorial/IC794985.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Slack-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **An Microsoft Azure AD > Teameinstellungen**.

    ![Teameinstellungen](./media/active-directory-saas-slack-tutorial/IC794986.png "Teameinstellungen")

7.  Klicken Sie im Abschnitt **Teameinstellungen** auf die Registerkarte **Authentifizierung**, und klicken Sie dann auf **Einstellungen ändern**.

    ![Teameinstellungen](./media/active-directory-saas-slack-tutorial/IC794987.png "Teameinstellungen")

8.  Führen Sie im Dialogfeld **SAML-Authentifizierungseinstellungen** die folgenden Schritte aus:

    ![SAML-Einstellungen](./media/active-directory-saas-slack-tutorial/IC794988.png "SAML-Einstellungen")

    1.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Slack** den Wert der **SAML-SSO-URL**, und fügen Sie ihn in das Textfeld **SAML 2.0 Endpoint (HTTP)** ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Slack** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Identitätsanbieter-Aussteller** ein.
    3.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.
    
        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    4.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Öffentliches Zertifikat** ein.
    5.  Deaktivieren Sie **Benutzern gestatten, ihre E-Mail-Adresse zu ändern**.
    6.  Wählen Sie **Benutzern gestatten, ihren eigenen Benutzernamen zu wählen** aus.
    7.  Wählen Sie als **Authentifizierung für Ihr Team muss verwendet werden von** die Option **Ist optional** aus.
    8.  Klicken Sie auf **Konfiguration speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-slack-tutorial/IC794989.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Slack anmelden können, müssen sie in Slack bereitgestellt werden.
  
Für das Konfigurieren der Benutzerbereitstellung in Slack steht kein Aktionselement zur Verfügung. Wenn ein zugewiesener Benutzer versucht, sich bei Slack anzumelden, wird ggf. automatisch ein Slack-Konto erstellt.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Slack Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Slack** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-slack-tutorial/IC794990.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-slack-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->