<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Mindflash | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Mindflash mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="01/14/2015" 
    ms.author="jeedes" />

#Tutorial: Azure Active Directory-Integration mit Mindflash
  
In diesem Tutorial wird die Integration von Azure und Mindflash erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Mindflash-Software-Abonnement, für das das einmalige Anmelden aktiviert ist.
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie Mindflash zugewiesen haben, mittels einmaliger Anmeldung auf Ihrer Mindflash-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie auch die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Mindflash
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-mindflash-tutorial/IC787132.png "Szenario")
##Aktivieren der Anwendungsintegration für Mindflash
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Mindflash aktivieren.

###So aktivieren Sie die Anwendungsintegration für Mindflash

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-mindflash-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-mindflash-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-mindflash-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Mindflash** ein.

    ![Anwendungskatalog](./media/active-directory-saas-mindflash-tutorial/IC787133.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Mindflash** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Mindflash zu authentifizieren.

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Mindflash** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Mindflash anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **Anmelde-URL** die URL im Format „**http://company.mindflash.com*"“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-mindflash-tutorial/IC787137.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Mindflash** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Einmaliges Anmelden konfigurieren")

5.  Senden Sie die Metadatendatei an das Supportteam von Mindflash.

    >[AZURE.NOTE]Das einmalige Anmelden muss vom Supportteam von Mindflash konfiguriert werden. Sie erhalten eine Benachrichtigung, sobald die Konfiguration abgeschlossen ist.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Mindflash anmelden können, müssen sie in Mindflash bereitgestellt werden. Im Fall von Mindflash ist die Bereitstellung eine manuelle Aufgabe.

###Führen Sie zum Bereitstellen von Benutzerkonten die folgenden Schritte aus:

1.  Melden Sie sich bei der **Mindflash**-Unternehmenswebsite als Administrator an.

2.  Navigieren Sie zu **Benutzer verwalten**.

    ![Benutzer verwalten](./media/active-directory-saas-mindflash-tutorial/IC787140.png "Benutzer verwalten")

3.  Klicken Sie mit der rechten Maustaste auf **Benutzer hinzufügen**, und klicken Sie dann auf **Neu**.

4.  Führen Sie im Abschnitt **Neue Benutzer hinzufügen** die folgenden Schritte aus:

    ![Neue Benutzer hinzufügen](./media/active-directory-saas-mindflash-tutorial/IC787141.png "Neue Benutzer hinzufügen")

    1.  Geben Sie den **Vornamen**, den **Nachnamen** und die **Email-Adresse** eines gültigen AAD-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Hinzufügen**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von Mindflash-Benutzerkonten oder mithilfe der von Mindflash bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Mindflash Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Mindflash** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-mindflash-tutorial/IC787142.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-mindflash-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->