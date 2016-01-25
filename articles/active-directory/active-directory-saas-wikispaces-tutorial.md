<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Wikispaces | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Wikispaces mit Azure Active Directory verwenden können, um einmalige Anmeldung, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="MarkusVi"  
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

#Tutorial: Azure Active Directory-Integration mit Wikispaces
  
In diesem Tutorial wird die Integration von Azure und Wikispaces erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Wikispaces-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Wikispaces zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Wikispaces-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Wikispaces
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Szenario")

##Aktivieren der Anwendungsintegration für Wikispaces
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Wikispaces aktivieren.

###So aktivieren Sie die Anwendungsintegration für Wikispaces:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** **Wikispaces** ein.

    ![Anwendungskatalog](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Wikispaces** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Wikispaces zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Wikispaces** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Wikispaces anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **Wikispaces-Anmelde-URL** die URL im Format „**http://company.wikispaces.net*"“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Wikispaces** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Einmaliges Anmelden konfigurieren")

5.  Senden Sie die Metadatendatei an das Supportteam von Wikispaces.

    >[AZURE.NOTE]Das einmalige Anmelden muss vom Supportteam von Wikispaces konfiguriert werden. Sie erhalten eine Benachrichtigung, sobald die Konfiguration abgeschlossen ist.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Wikispaces anmelden können, müssen sie in Wikispaces bereitgestellt werden. Im Fall von Wikispaces ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit:

1.  Melden Sie sich bei Ihrer **Wikispaces**-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Mitglieder**.

    ![Mitglieder](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Mitglieder")

3.  Klicken Sie auf **Personen einladen**.

    ![Personen einladen](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Personen einladen")

4.  Führen Sie im Abschnitt **Personen einladen** die folgenden Schritte aus:

    ![Personen einladen](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Personen einladen")

    1.  Geben Sie **Benutzernamen oder E-Mail-Adresse** eines gültigen AAD-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Send**.  

        >[AZURE.NOTE]Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Wikispaces-Benutzerkonten oder mithilfe der von Wikispaces bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Wikispaces Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Wikispaces** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->