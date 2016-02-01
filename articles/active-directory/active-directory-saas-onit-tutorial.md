<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Onit | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Onit mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Onit
  
In diesem Tutorial wird die Integration von Azure und Onit erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Onit-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können socj die Azure AD-Benutzer, die Sie Onit zugewiesen haben, wie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) beschrieben mittels einmaliger Anmeldung bei Ihrer Onit-Unternehmenswebsite anmelden (durch den Dienstanbieter initiierte Anmeldung).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Onit
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-onit-tutorial/IC791166.png "Szenario")
##Aktivieren der Anwendungsintegration für Onit
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Onit aktivieren.

###So aktivieren Sie die Anwendungsintegration für Onit

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-onit-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-onit-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-onit-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Onit** ein.

    ![Anwendungskatalog](./media/active-directory-saas-onit-tutorial/IC791167.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Onit** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Onit zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für Onit müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.
  
Die Onit-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der **SAML-Tokenattribute** benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![Einmaliges Anmelden](./media/active-directory-saas-onit-tutorial/IC791168.png "Einmaliges Anmelden")

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Onit** im Menü oben auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen.

    ![Attribute](./media/active-directory-saas-onit-tutorial/IC791169.png "Attribute")

2.  Führen Sie zum Hinzufügen der erforderlichen Attributzuordnungen die folgenden Schritte aus:

    
    |Attributname|Attributwert|
	|---|---|
	|Name|User.userprincipalname|
    |E-Mail|User.mail|

    1.  Klicken Sie für jede Datenzeile in der obigen Tabelle auf **Benutzerattribut hinzufügen**.
    2.  Geben Sie im Textfeld **Attributname** den für die Zeile angezeigten Attributnamen ein.
    3.  Wählen Sie in der Liste **Attributwert** den für die Zeile angezeigten Attributwert aus.
    4.  Klicken Sie auf **Fertig stellen**.

3.  Klicken Sie auf **Änderungen übernehmen**.

4.  Klicken Sie in Ihrem Browser auf **Zurück**, um erneut das Dialogfeld **Schnellstart** zu öffnen.

5.  Klicken Sie auf **Einmaliges Anmelden konfigurieren** um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-onit-tutorial/IC791170.png "Einmaliges Anmelden konfigurieren")

6.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Onit anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-onit-tutorial/IC791171.png "Einmaliges Anmelden konfigurieren")

7.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Onit-Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei Onit verwenden (z. B. **https://ms-sso-test.onit.com*”)), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-onit-tutorial/IC791172.png "App-URL konfigurieren")

8.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Onit** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-onit-tutorial/IC791173.png "Einmaliges Anmelden konfigurieren")

9.  Melden Sie sich in einem anderen Webbrowserfenster bei der Onit-Unternehmenswebsite als Administrator an.

10. Klicken Sie oben im Menü auf **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-onit-tutorial/IC791174.png "Verwaltung")

11. Klicken Sie auf **Unternehmen bearbeiten**.

    ![Unternehmen bearbeiten](./media/active-directory-saas-onit-tutorial/IC791175.png "Unternehmen bearbeiten")

12. Klicken Sie auf die Registerkarte **Sicherheit**.

    ![Unternehmensinformationen bearbeiten](./media/active-directory-saas-onit-tutorial/IC791176.png "Unternehmensinformationen bearbeiten")

13. Führen Sie im Abschnitt **Sicherheit** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-onit-tutorial/IC791177.png "Einmaliges Anmelden")

    1.  Wählen Sie als **Authentifizierungsstrategie** die Option **Einmaliges Anmelden und Kennwort**.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Onit** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **IDP-Ziel-URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Onit** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Idp-Abmelde-URL** ein.
    4.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Fingerabdruck des IDP-Zertifikats (SHA1)** ein.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI).

    5.  Wählen Sie für **SSO-Typ** die Option **SAML** aus.
    6.  Geben Sie im Textfeld **Text der SSO-Anmeldeschaltfläche** den gewünschten Text ein.
    7.  Wählen Sie **Anmeldung mit SSO: Erforderlich für die folgenden Domänen/Benutzer**, geben Sie die E-Mail-Adresse eines Testbenutzers in das entsprechende Textfeld ein, und klicken Sie dann auf **Aktualisieren**. ![Unternehmen bearbeiten](./media/active-directory-saas-onit-tutorial/IC791178.png "Unternehmen bearbeiten")

14. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-onit-tutorial/IC791179.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Onit anmelden können, müssen sie in Onit bereitgestellt werden. Im Fall von Onit ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der **Onit**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Verwaltung](./media/active-directory-saas-onit-tutorial/IC791180.png "Verwaltung")

3.  Führen Sie auf der Dialogfeldseite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-onit-tutorial/IC791181.png "Benutzer hinzufügen")

    1.  Geben Sie **Name** und **E-Mail-Adresse** eines gültigen AAD-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Erstellen**.  

        >[AZURE.NOTE]Der Besitzer des Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Onit-Benutzerkonten oder mithilfe der von Onit bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Onit Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Onit** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-onit-tutorial/IC791182.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-onit-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->