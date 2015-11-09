<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit FreshService | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie FreshService mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit FreshService
  
In diesem Tutorial wird die Integration von Azure und FreshService erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein FreshService-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie FreshService zugewiesen haben, wie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) beschrieben mittels einmaliger Anmeldung bei der Anwendung anmelden.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für FreshService
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Szenario")
##Aktivieren der Anwendungsintegration für FreshService
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für FreshService aktivieren.

###So aktivieren Sie die Anwendungsintegration für FreshService

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Suchbegriff **FreshService** ein.

    ![Anwendungskatalog](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **FreshService** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![FreshService](./media/active-directory-saas-freshservice-tutorial/IC790809.png "FreshService")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei FreshService zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für FreshService müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **FreshService** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei FreshService anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **FreshService-Anmelde-URL** die URL ein, welche die Benutzer zur Anmeldung bei FreshService verwenden (z. B. "**http://democompany.freshservice.com/*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790812.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für FreshService** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der FreshService-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben im Menü auf **Admin**.

    ![Admin](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

7.  Klicken Sie im **Kundenportal** auf **Sicherheit**.

    ![Sicherheit](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Sicherheit")

8.  Führen Sie im Abschnitt **Sicherheit** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Einmaliges Anmelden")

    1.  Aktivieren Sie **Einmaliges Anmelden (SSO)**.
    2.  Wählen Sie **SAML-SSO** aus.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für FreshService** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **SAML-Anmelde-URL** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für FreshService** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Abmelde-URL** ein.
    5.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Fingerabdruck des Sicherheitszertifikats** ein.
    
        >[AZURE.TIP]Weitere Informationen finden Sie unter [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI) (in englischer Sprache).

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei FreshService anmelden können, müssen sie in FreshService bereitgestellt werden. Im Fall von FreshService ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei der **FreshService**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie oben im Menü auf **Admin**.

    ![Admin](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

3.  Klicken Sie im Abschnitt **Benutzerverwaltung** auf **Anfordernde Personen**.

    ![Anfordernde Personen](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Anfordernde Personen")

4.  Klicken Sie auf **Neue anfordernde Person**.

    ![Neue anfordernde Personen](./media/active-directory-saas-freshservice-tutorial/IC790819.png "Neue anfordernde Personen")

5.  Führen Sie im Abschnitt **Neue anfordernde Person** die folgenden Schritte aus:

    ![Neue anfordernde Person](./media/active-directory-saas-freshservice-tutorial/IC790820.png "Neue anfordernde Person")

    1.  Geben Sie die Attribute **Vorname** und **E-Mail** eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

    >[AZURE.NOTE]Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von FreshService-Benutzerkonten oder mithilfe der von FreshService bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie FreshService Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **FreshService** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->