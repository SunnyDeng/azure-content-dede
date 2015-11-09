<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit LogicMonitor | Microsoft Azure" 
    description="Erfahren Sie, wie Sie LogicMonitor mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit LogicMonitor
  
In diesem Lernprogramm wird die Integration von Azure und LogicMonitor erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen LogicMonitor-Mandanten
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für LogicMonitor
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Szenario")
##Aktivieren der Anwendungsintegration für LogicMonitor
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für LogicMonitor aktivieren.

###So aktivieren Sie die Anwendungsintegration für LogicMonitor

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **logicmonitor** ein.

    ![Anwendungskatalog](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **LogicMonitor** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei LogicMonitor zu authentifizieren.

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **LogicMonitor** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei LogicMonitor anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **Anmelde-URL** die von den Benutzern für die Anmeldung bei LogicMonitor verwendete URL ein (z. B. "**http://company.logicmonitor.com*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für LogicMonitor** auf **Metadaten herunterladen**, und speichern Sie die Daten auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich bei der **LogicMonitor**-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im oberen Menü auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Einstellungen")

7.  Klicken Sie auf der Navigationsleiste links auf **Einmaliges Anmelden**

    ![Einmaliges Anmelden](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Einmaliges Anmelden")

8.  Führen Sie im Abschnitt **Einstellungen für einmaliges Anmelden (SSO)** die folgenden Schritte aus:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Einstellungen für einmaliges Anmelden")

    1.  Wählen Sie **Einmaliges Anmelden aktivieren** aus.
    2.  Wählen Sie für **Standardmäßige Rollenzuweisung** die Option **Schreibgeschützt**.
    3.  Öffnen Sie die heruntergeladene Metadatendatei im Editor, und fügen Sie den Inhalt der Datei ins Textfeld **Identitätsanbietermetadaten** ein.
    4.  Klicken Sie auf **Änderungen speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich AAD-Benutzer anmelden können, müssen sie in der LogicMonitor-Anwendung unter Verwendung ihrer Azure Active Directory-Benutzernamen bereitgestellt werden.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der LogicMonitor-Unternehmenswebsite als Administrator an.

2.  Klicken Sie im oberen Menü auf **Einstellungen** und dann auf **Rollen und Benutzer**.

    ![Rollen und Benutzer](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Rollen und Benutzer")

3.  Klicken Sie auf **Hinzufügen**.

4.  Führen Sie im Abschnitt **Konto hinzufügen** die folgenden Schritte aus:

    ![Konto hinzufügen](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Konto hinzufügen")

    1.  Geben Sie für den Azure Active Directory-Benutzer, den Sie bereitstellen möchten, Werte für **Benutzername**, **E-Mail**, **Kennwort** und **Kennwort erneut eingeben** in die entsprechenden Textfelder ein.
    2.  Wählen Sie **Rollen**, **Berechtigungen anzeigen** und **Status**.
    3.  Klicken Sie auf **Senden**.

>[AZURE.NOTE]Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von LogicMonitor-Benutzerkonten oder mithilfe der von LogicMonitor bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie LogicMonitor Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **LogicMonitor** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->