<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Learningpool | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Learningpool mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Learningpool
  
In diesem Tutorial wird die Integration von Azure und Learningpool erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Learningpool-Abonnement, für das das einmalige Anmelden aktiviert ist.
  
Nach Abschluss dieses Lernprogramms können sich die Learningpool zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer Learningpool-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie auch die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Learningpool
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Szenario")
##Aktivieren der Anwendungsintegration für Learningpool
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Learningpool aktivieren.

###Führen Sie zum Aktivieren der Anwendungsintegration für Learningpool die folgenden Schritte aus:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Learningpool** ein.

    ![Anwendungskatalog](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Learningpool** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Learningpool zu authentifizieren.
  
Die Learningpool-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Ihrer Konfiguration der **SAML-Tokenattribute** benutzerdefinierte Attributzuordnungen hinzugefügt werden. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![SAML-Tokenattribute](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML-Tokenattribute")

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Learningpool** im Menü oben auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen.

    ![Attribute](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attribute")

2.  Führen Sie zum Hinzufügen der erforderlichen Attributzuordnungen die folgenden Schritte aus:

    ###

    |Attributname |Attributwert |
	|------------------------------|---------------------------|

     urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname
	|-------------------------------|--------------------------|  
	 urn:oid:2.5.4.42|User.givenname   
    |urn:oid:0.9.2342.19200300.100.1.3|User.mail
    |urn:oid:2.5.4.4|User.surname

    1.  Klicken Sie für jede Datenzeile in der obigen Tabelle auf **Benutzerattribut hinzufügen**.
    2.  Geben Sie im Textfeld **Attributname** den für die Zeile angezeigten Attributnamen ein.
    3.  Wählen Sie in der Liste **Attributwert** den für die Zeile angezeigten Attributwert aus.
    4.  Klicken Sie auf **Fertig stellen**.

3.  Klicken Sie auf **Änderungen übernehmen**.

4.  Klicken Sie in Ihrem Browser auf **Zurück**, um erneut das Dialogfeld **Schnellstart** zu öffnen.

5.  Klicken Sie auf **Einmaliges Anmelden konfigurieren** um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Einmaliges Anmelden konfigurieren")

6.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Learningpool anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Einmaliges Anmelden konfigurieren")

7.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Learningpool-Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei Learningpool verwenden (z. B. *https://parliament.preview.learningpool.com/auth/shibboleth/index.php)), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795078.png "App-URL konfigurieren")

8.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Learningpool** auf **Metadaten herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Einmaliges Anmelden konfigurieren")

9.  Leiten Sie die Metadatendatei an das Supportteam von Learningpool weiter.

    >[AZURE.NOTE]Das einmalige Anmelden muss vom Supportteam von Learningpool aktiviert werden.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Learningpool anmelden können, müssen sie in Learningpool bereitgestellt werden.
  
Für das Konfigurieren der Benutzerbereitstellung in Learningpool steht kein Aktionselement zur Verfügung. Benutzer müssen vom Supportteam von Learningpool erstellt werden.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Learningpool-Benutzerkonten oder mithilfe der von Learningpool bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Learningpool Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Learningpool** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->