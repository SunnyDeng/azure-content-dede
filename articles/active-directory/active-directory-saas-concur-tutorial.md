<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Concur | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Concur mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Concur  


In diesem Tutorial wird die Integration von Azure und Concur erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mandanten in Concur

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Concur
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-concur-tutorial/IC769766.png "Szenario")

>[AZURE.NOTE]Die Konfiguration Ihres Concur-Abonnements für Verbund-SSO über SAML ist eine separate Aufgabe, für die Sie sich an Concur wenden müssen.

##Aktivieren der Anwendungsintegration für Concur

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Concur aktivieren.

###So aktivieren Sie die Anwendungsintegration für Concur

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-concur-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie zum Öffnen des **Anwendungskatalogs** auf **App hinzufügen** und anschließend auf **Eine Anwendung hinzufügen, die mein Unternehmen verwendet**.

    ![Was möchten Sie tun?](./media/active-directory-saas-concur-tutorial/IC700995.png "Was möchten Sie tun?")

5.  Geben Sie im **Suchfeld** das Wort **Concur** ein.

    ![Anwendungskatalog](./media/active-directory-saas-concur-tutorial/IC721727.png "Anwendungskatalog")

6.  Wählen Sie im Ergebnisbereich **Concur** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Concur](./media/active-directory-saas-concur-tutorial/IC721728.png "Concur")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Concur zu authentifizieren.

>[AZURE.NOTE]Die Konfiguration Ihres Concur-Abonnements für Verbund-SSO über SAML ist eine separate Aufgabe, für die Sie sich an Concur wenden müssen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Concur** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-concur-tutorial/IC769767.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Concur anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-concur-tutorial/IC769768.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Concur-Anmelde-URL** die Anmelde-URL Ihres Concur-Mandanten ein, und klicken Sie auf **Weiter**:

    ![Anmelde-ULR konfigurieren](./media/active-directory-saas-concur-tutorial/IC769769.png "Anmelde-ULR konfigurieren")

4.  Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für Concur** die folgenden Schritte aus.

    ![Anmelde-ULR konfigurieren](./media/active-directory-saas-concur-tutorial/IC769770.png "Anmelde-ULR konfigurieren")

    1.  Klicken Sie auf „Metadaten herunterladen“, und speichern Sie die Datendatei auf Ihrem Computer.
    2.  Wenden Sie sich an das Supportteam von Concur, um SSO für Ihren Mandanten konfigurieren.
    3.  Wählen Sie die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.  

	>[AZURE.NOTE]Die Konfiguration Ihres Concur-Abonnements für Verbund-SSO über SAML ist eine separate Aufgabe, für die Sie sich an Concur wenden müssen.

##Konfigurieren der Benutzerbereitstellung

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Concur aktivieren.

Die Aktivierung von Apps im Dienst für Ausgaben erfordert die korrekte Einrichtung und Verwendung eines Webdienstadministrator-Profils. Fügen Sie die Webdienstadministrator-Rolle (WSAdmin) nicht einfach dem vorhandenen Administratorprofil hinzu, das Sie zur Verwaltung von Reisekosten und Spesen verwenden.

Concur-Berater oder der Kundenadministrator müssen ein separates Webdienstadministrator-Profil erstellen, und der Kundenadministrator muss dieses Profil für die Webdienstadministrator-Funktionen verwenden (z. B. zum Aktivieren von Apps). Diese Profile müssen vom normalen Administratorprofil für Reisekosten und Spesen des Kundenadministrators getrennt sein (dem Administratorprofil für Reisekosten und Spesen sollte die WSAdmin-Rolle nicht zugewiesen werden).

Geben Sie beim Erstellen des Profils, das zum Aktivieren der App verwendet wird, den Namen des Kundenadministrators in die Felder des Benutzerprofils ein. Dadurch wird das Profil als Besitzer zugewiesen. Nachdem Sie die Profile erstellt haben, muss sich der Kunde mit diesem Profil anmelden, um im Menü „Webdienste“ auf die Schaltfläche *Aktivieren* für eine Partneranwendung zu klicken.

Aus den folgenden Gründen sollte diese Aktion nicht mit dem Profil ausgeführt werden, das für die normale Verwaltung von Reisekosten und Spesen verwendet wird.

1.  Der Kunde muss im Dialogfeld, das nach dem Aktivieren einer App angezeigt wird, auf *Ja* klicken. Dadurch wird bestätigt, dass der Kunde dem Zugriff der Partneranwendung auf seine Daten zustimmt. Es ist daher nicht möglich, dass Sie oder der Partner auf diese Schaltfläche „Ja“ klicken.
2.  Falls ein Kundenadministrator, der eine App mit dem Reisekosten- und Spesenprofil aktiviert hat, das Unternehmen verlässt (und das Profil deaktiviert wird), funktionieren alle Apps, die mit diesem Profil aktiviert wurden, erst wieder, wenn sie mit einem anderen aktiven Webdienstadministrator-Profil aktiviert werden. Aus diesem Grund sollten separate Webdienstadministrator-Profile erstellt werden.
3.  Wenn ein Administrator das Unternehmen verlässt, kann der mit dem Webdienstadministrator-Profil verknüpfte Name ggf. in den Namen des neuen Administrators geändert werden. Da dieses Profil nicht deaktiviert werden muss, hat dies keinerlei Auswirkungen auf die aktivierte App.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Concur**-Mandanten an.

2.  Klicken Sie im Menü **Verwaltung** auf **Webdienste**.

    ![Concur-Mandant](./media/active-directory-saas-concur-tutorial/IC721729.png "Concur-Mandant")

3.  Wählen Sie auf der linken Seite des Bereichs **Webdienste** die Option **Partneranwendung aktivieren** aus.

    ![Partneranwendung aktivieren](./media/active-directory-saas-concur-tutorial/IC721730.png "Partneranwendung aktivieren")

4.  Wählen Sie in der Liste **Anwendung aktivieren** die Option **Azure Active Directory** aus, und klicken Sie dann auf **Aktivieren**.

    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  Klicken Sie auf **Ja**, um das Dialogfeld **Aktion bestätigen** zu schließen.

    ![Aktion bestätigen](./media/active-directory-saas-concur-tutorial/IC721732.png "Aktion bestätigen")

6.  Wählen Sie im Azure-Verwaltungsportal in der Anwendungsliste die Anwendung **Concur** aus, um die Dialogfeldseite **Concur** zu öffnen.

7.  Klicken Sie zum Öffnen des Dialogfelds **Benutzerbereitstellung konfigurieren** auf **Benutzerbereitstellung konfigurieren**.

8.  Geben Sie den Benutzernamen und das Kennwort Ihres Concur-Administrators ein, und klicken Sie dann auf **Weiter**.

9.  Klicken Sie zum Abschließen der Konfiguration auf der Bestätigungsseite auf **Abschließen**.

Sie können nun ein Testkonto erstellen und nach 10 Minuten überprüfen, ob das Konto mit Concur synchronisiert wurde.
##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Concur Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Concur** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-concur-tutorial/IC769771.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-concur-tutorial/IC767830.png "Ja")

Nach 10 Minuten können Sie überprüfen, ob das Konto mit Concur synchronisiert wurde.

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->