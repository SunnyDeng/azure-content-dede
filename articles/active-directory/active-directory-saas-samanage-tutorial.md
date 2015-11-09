<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Samanage | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Samanage mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Samanage
  
In diesem Tutorial wird die Integration von Azure und Samanage erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Samanage-Mandant
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie Samanage zugewiesen haben, mittels einmaligen Anmeldens auf Ihrer Samanage-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Samanage
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-samanage-tutorial/IC771705.png "Szenario")
##Aktivieren der Anwendungsintegration für Samanage
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Samanage aktivieren.

###So aktivieren Sie die Anwendungsintegration für Samanage:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-samanage-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-samanage-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-samanage-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **Salesforce** ein.

    ![Anwendungskatalog](./media/active-directory-saas-samanage-tutorial/IC771707.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Samanage** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Samanage](./media/active-directory-saas-samanage-tutorial/IC771708.png "Samanage")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Samanage zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei erstellen. Falls Sie mit diesem Verfahren nicht vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Samanage** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-samanage-tutorial/IC771709.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Samanage anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-samanage-tutorial/IC771710.png "Microsoft Azure AD – einmaliges Anmelden")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Samanage-Anmelde-URL** die URL im Format „*https://\<Mandantenname>.Samanage.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-samanage-tutorial/IC771711.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Samanage** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-samanage-tutorial/IC777613.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Samanage-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Dashboard**, und wählen Sie **Setup** im linken Navigationsbereich aus.

    ![Dashboard](./media/active-directory-saas-samanage-tutorial/IC771712.png "Dashboard")

7.  Klicken Sie auf **Einmaliges Anmelden**.

    ![Einmaliges Anmelden](./media/active-directory-saas-samanage-tutorial/IC771713.png "Einmaliges Anmelden")

8.  Führen Sie auf der Dialogfeldseite **Mit SAML anmelden** die folgenden Schritte aus, und klicken Sie dann auf **Änderungen speichern**:

    1.  Klicken Sie auf **Einmaliges Anmelden mit SAML aktivieren**. ![Mit SAML anmelden](./media/active-directory-saas-samanage-tutorial/IC771719.png "Mit SAML anmelden")
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren bei Samanage** den Wert der **Identitätsanbieter-ID**, und fügen Sie ihn in das Textfeld **Identitätsanbieter-URL** ein.![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-samanage-tutorial/IC771720.png "Einmaliges Anmelden konfigurieren")
3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Samanage** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn ins Textfeld **Anmelde-URL** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Samanage** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn ins Textfeld **Abmelde-URL** ein.
    5.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    6.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.
    7.  Klicken Sie auf **Benutzer erstellen, wenn sie nicht in Samanage vorhanden sind**. ![Aktualisieren](./media/active-directory-saas-samanage-tutorial/IC771722.png "Aktualisieren")
    8.  Klicken Sie auf **Aktualisieren**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-samanage-tutorial/IC771723.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Samanage anmelden können, müssen sie in Samanage bereitgestellt werden. Im Fall von Samanage ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit:

1.  Melden Sie sich bei Ihrem **Samanage**-Mandanten an.

2.  Wechseln Sie zu **Dashboard > Setup**.

    ![Einrichtung](./media/active-directory-saas-samanage-tutorial/IC771724.png "Einrichtung")

3.  Klicken Sie auf die Registerkarte **Benutzer**.

    ![Benutzer](./media/active-directory-saas-samanage-tutorial/IC771725.png "Benutzer")

4.  Klicken Sie auf **Neuer Benutzer**.

    ![Neuer Benutzer](./media/active-directory-saas-samanage-tutorial/IC771726.png "Neuer Benutzer")

5.  Geben Sie die **E-Mail-Adresse** und den **Namen** eines vorhandenen Azure AD-Kontos ein, das Sie bereitstellen möchten, und klicken Sie auf **Benutzer erstellen**.

    >[AZURE.NOTE]Der Besitzer des AAD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

    ![Benutzer erstellen](./media/active-directory-saas-samanage-tutorial/IC771727.png "Benutzer erstellen")

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Samanage-Benutzerkonten oder mithilfe der von Samanage bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Samanage Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Samanage** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-samanage-tutorial/IC771728.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-samanage-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->