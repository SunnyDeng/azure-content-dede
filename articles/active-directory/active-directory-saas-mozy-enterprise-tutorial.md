<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Mozy Enterprise | Microsoft Azure" description="Hier erfahren Sie, wie Sie Mozy Enterprise mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Mozy Enterprise
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=524186), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Mozy Enterprise erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mozy Enterprise-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Mozy Enterprise zugewiesen haben, mittels einmaliger Anmeldung auf der Mozy Enterprise-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Mozy Enterprise
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Szenario")
##Aktivieren der Anwendungsintegration für Mozy Enterprise
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Mozy Enterprise aktivieren.

###So aktivieren Sie die Anwendungsintegration für Mozy Enterprise

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Begriff **Mozy Enterprise** ein.

    ![Anwendungskatalog](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Mozy Enterprise** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Mozy Enterprise zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren Mozy Enterprise-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Mozy Enterprise** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Mozy Enterprise anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Mozy Enterprise-Anmelde-URL** die URL im Format „*https://\<Mandantenname>.Mozyenterprise.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Mozy Enterprise** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Mozy Enterprise-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Abschnitt **Konfiguration** auf **Authentifizierungsrichtlinie**.

    ![Authentifizierungsrichtlinie](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Authentifizierungsrichtlinie")

7.  Führen Sie im Abschnitt **Authentifizierungsrichtlinie** die folgenden Schritte aus:

    ![Authentifizierungsrichtlinie](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Authentifizierungsrichtlinie")

    1.  Wählen Sie für **Verzeichnisdienst** die Option **Anbieter** aus.
    2.  Wählen Sie **LDAP-Push verwenden** aus.
    3.  Klicken Sie auf die Registerkarte **SAML-Authentifizierung**.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Mozy Enterprise** den Wert der **Authentifizierungsanforderungs-URL**, und fügen Sie ihn in das Textfeld **Authentifizierungs-URL** ein.
    5.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Mozy Enterprise** den Wert der **Identitätsanbieter-ID**, und fügen Sie ihn in das Textfeld **SAML-Endpunkt** ein.
    6.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    7.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie anschließend das gesamte Zertifikat in das Textfeld **SAML-Zertifikat** ein.
    8.  Wählen Sie **SSO für Administratoren zur Anmeldung mit den Netzwerkanmeldeinformationen aktivieren** aus.
    9.  Klicken Sie auf **Änderungen speichern**.

8.  Wählen Sie im Azure-Portal auf der Dialogseite **Einmaliges Anmelden konfigurieren für Mozy Enterprise** die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Mozy Enterprise anmelden können, müssen sie in Mozy Enterprise bereitgestellt werden. Im Fall von Mozy Enterprise ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Mozy Enterprise**-Mandanten an.

2.  Klicken Sie auf **Benutzer** und dann auf **Neuen Benutzer hinzufügen**.

    ![Benutzer](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Benutzer")

    >[AZURE.NOTE]Die Option **Neuen Benutzer hinzufügen** wird nur angezeigt, wenn **Mozy** unter **Authentifizierungsrichtlinie** als Anbieter ausgewählt ist. Wenn die SAML-Authentifizierung konfiguriert ist, werden die Benutzer automatisch bei ihrer ersten Anmeldung mittels SSO hinzugefügt.

3.  Führen Sie im Dialogfeld „Neuer Benutzer“ die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Benutzer hinzufügen")

    1.  Wählen Sie in der Liste **Gruppe auswählen** eine Gruppe aus.
    2.  Wählen Sie in der Liste **Benutzertyp** einen Typ aus.
    3.  Geben Sie im Textfeld **Benutzername** den Namen des Azure AD-Benutzers ein.
    4.  Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse des Azure AD-Benutzers ein.
    5.  Wählen Sie **E-Mail mit Anweisungen an Benutzer senden** aus.
    6.  Klicken Sie auf **Benutzer hinzufügen**.

    >[AZURE.NOTE]Nach dem Erstellen des Benutzers erhält der Azure AD-Benutzer eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Mozy Enterprise-Benutzerkonten oder mithilfe der von Mozy Enterprise bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
 
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Mozy Enterprise Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Mozy Enterprise** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->