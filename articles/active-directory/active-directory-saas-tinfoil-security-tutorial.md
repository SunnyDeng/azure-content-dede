<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Tinfoil Security | Microsoft Azure"
    description="Hier erfahren Sie, wie Sie Tinfoil Security mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Tinfoil Security
  
In diesem Tutorial wird die Integration von Azure und Tinfoil Security erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Tinfoil Security-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie Tinfoil Security zugewiesen haben, mittels einmaliger Anmeldung auf Ihrer Tinfoil Security-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Tinfoil Security
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Einmaliges Anmelden konfigurieren")

##Aktivieren der Anwendungsintegration für Tinfoil Security
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Tinfoil Security aktivieren.

###So aktivieren Sie die Anwendungsintegration für Tinfoil Security:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **Tinfoil Security** ein.

    ![Anwendungskatalog](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Tinfoil Security** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Tinfoil Security zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für Tinfoil Security müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Tinfoil Security** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Tinfoil Security anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** des Textfeldes **Tinfoil Security-Antwort-URL** Ihre Tinfoil Security Assertion Consumer Service (ACS)-URL ein (z. B.: "**https://www.tinfoilsecurity.com/saml/consume*", und klicken Sie dann auf **Weiter**.

    >[AZURE.NOTE]Sie sollten in der Lage sein, die ACS-URL aus den Tinfoil Security-Metadaten abzurufen (https://www.tinfoilsecurity.com/saml/metadata).

    ![App-URL konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Tinfoil Security** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **C:\\Tinfoil Security.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Tinfoil Security-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben auf der Symbolleiste auf **Mein Konto**.

    ![Dashboard](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")

7.  Klicken Sie auf **Sicherheit**.

    ![Sicherheit](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Sicherheit")

8.  Führen Sie auf der Konfigurationsseite **Einmaliges Anmelden** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Einmaliges Anmelden")

    1.  Wählen Sie **SAML aktivieren**.
    2.  Klicken Sie auf **Manuelle Konfiguration**.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Tinfoil Security** den Wert der **SAML-SSO-URL**, und fügen Sie ihn in das Textfeld **SAML POST-URL** ein.
    4.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **SAML-Zertifikatsfingerabdruck** ein.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI).

    5.  Kopieren Sie **Ihre Konto-ID**.
    6.  Klicken Sie auf **Speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Einmaliges Anmelden konfigurieren")

10. Klicken Sie oben im Menü auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen.

    ![Attribute](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attribute")

11. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![Attribute](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attribute")

    1.  Klicken Sie auf **Benutzerattribut hinzufügen**.
    2.  Geben Sie im Textfeld **Attributname** die Zeichenfolge **accountid** ein.
    3.  Fügen Sie in das Textfeld **Attributwert** den Konto-ID-Wert ein, den Sie im vorherigen Abschnitt kopiert haben.
    4.  Klicken Sie auf **Fertig stellen**.

12. Klicken Sie auf **Änderungen übernehmen**.

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Tinfoil Security anmelden können, müssen sie in Tinfoil Security bereitgestellt werden. Im Fall von Tinfoil Security ist die Bereitstellung eine manuelle Aufgabe.

###So rufen Sie einen bereitgestellten Benutzer ab:

1.  Wenn der Benutzer einen Teil eines Enterprise-Kontos ist, müssen Sie sich an das Tinfoil Security-Supportteam wenden, um sich das Benutzerkonto anlegen zu lassen.

2.  Wenn der Benutzer ein normaler Tinfoil Security-Benutzer ist, kann der Benutzer zu allen Websites des Benutzers einen Mitarbeiter hinzufügen. Dies löst einen Prozess aus, bei dem eine Einladung an die angegebene E-Mail-Adresse gesendet wird, um ein neues Tinfoil Security-Benutzerkonto anzulegen.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Tinfoil Security-Benutzerkonten oder mithilfe der von Tinfoil Security bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Tinfoil Security Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Tinfoil Security** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->