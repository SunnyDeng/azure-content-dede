<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Lucidchart | Microsoft Azure" description="Erfahren Sie, wie Sie Lucidchart mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Lucidchart
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=532346), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Lucidchart erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Lucidchart-Software-Abonnement, für das das einmalige Anmelden aktiviert ist.
  
Nach Abschluss dieses Tutorials können sich die Lucidchart zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Lucidchart-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können Sie die [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586) nutzen.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Lucidchart
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Szenario")
##Aktivieren der Anwendungsintegration für Lucidchart
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Lucidchart aktivieren.

###So aktivieren Sie die Anwendungsintegration für Lucidchart

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Lucidchart** ein.

    ![Anwendungskatalog](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Lucidchart** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Lucidchart zu authentifizieren.

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Lucidchart** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Lucidchart anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Lucidchart-Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei Lucidchart verwenden (z. B. **https://chart2.office.lucidchart.com/saml/sso/azure*")), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Lucidchart** auf **Metadaten herunterladen**, und speichern Sie die Datendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Lucidchart-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im oberen Menü auf **Team**.

    ![Team](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")

7.  Klicken Sie auf **Anwendung > SAML verwalten**.

    ![SAML verwalten](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "SAML verwalten")

8.  Führen Sie auf der Dialogseite **SAML-Authentifizierungseinstellungen** die folgenden Schritte aus:

    1.  Wählen Sie **SAML-Authentifizierung aktivieren**, und klicken Sie dann auf **Optional**. ![SAML-Authentifizierungseinstellungen](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML-Authentifizierungseinstellungen")
    2.  Geben Sie im Textfeld **Domäne** Ihre Domäne ein, und klicken Sie auf **Zertifikat ändern**. ![Zertifikat ändern](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Zertifikat ändern")
    3.  Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **Metadaten hochladen** ein. ![Metadaten hochladen](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Metadaten hochladen")
    4.  Wählen Sie **Automatisch neue Benutzer zum Team hinzufügen**, und klicken Sie auf **Änderungen speichern**. ![Änderungen speichern](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Änderungen speichern")

9.  Wählen Sie die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in Lucidchart steht kein Aktionselement zur Verfügung. Wenn sich ein zugewiesener Benutzer über den Zugriffsbereich bei Lucidchart anmelden möchte, überprüft Lucidchart, ob der Benutzer vorhanden ist. Ist noch kein Benutzerkonto verfügbar, wird es von Lucidchart automatisch erstellt.
##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Lucidchart Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Lucidchart** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->