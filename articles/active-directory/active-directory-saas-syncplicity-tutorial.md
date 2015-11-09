<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Syncplicity | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Syncplicity mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Syncplicity
  
In diesem Tutorial wird erläutert, wie einmaliges Anmelden zwischen Azure Active Directory (Azure AD) und Syncplicity eingerichtet wird.
  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Syncplicity-Mandant
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, denen Sie den Zugriff auf Syncplicity zugewiesen haben, mittels einmaliger Anmeldung auf der Syncplicity-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen.

1.  Aktivieren der Anwendungsintegration für Syncplicity
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Szenario")

##Aktivieren der Anwendungsintegration für Syncplicity
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Syncplicity aktivieren.

###So aktivieren Sie die Anwendungsintegration für Syncplicity:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** **Syncplicity** ein.

    ![Syncplicity-Anwendungskatalog](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Syncplicity-Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Syncplicity** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei Syncplicity zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Syncplicity** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Syncplicity anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Microsoft Azure AD – einmaliges Anmelden")

3.  Geben Sie auf der Seite **App-URL konfigurieren** in das Textfeld **Syncplicity-Anmelde-URL** die URL ein, mit der sich die Benutzer bei der Syncplicity-Anwendung anmelden. Klicken Sie dann auf **Weiter**.

    Bei der App-URL handelt es sich um die URL Ihres Syncplicity-Mandanten (z. B. **http://company.Syncplicity.com*):

    ![App-URL konfigurieren](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Syncplicity** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich bei Ihrem **Syncplicity**-Mandanten an.

6.  Klicken Sie im Menü oben auf **Admin**, wählen Sie **Einstellungen**, und klicken Sie dann auf **Benutzerdefinierte Domäne und einmaliges Anmelden**.

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7.  Führen Sie auf der Dialogfeldseite **Einmaliges Anmelden (SSO)** die folgenden Schritte aus:

    ![Einmaliges Anmelden (Single Sign-On, SSO)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Single Sign-On (SSO)")

    1.  Geben Sie in das Textfeld **Benutzerdefinierte Domäne** den Namen Ihrer Domäne ein.
    2.  Wählen Sie **Aktiviert** als **Status der einmaligen Anmeldung**.
    3.  Kopieren Sie im Microsoft Azure-Portal auf der Seite **Einmaliges Anmelden konfigurieren für Syncplicity** den Wert der **Entitäts-ID**, und fügen Sie ihn in das Textfeld **Entitäts-ID** ein.
    4.  Kopieren Sie im Microsoft Azure-Portal auf der Seite **Einmaliges Anmelden konfigurieren für Syncplicity** den Wert für die **Dienst-URL für einmaliges Anmelden**, und fügen Sie ihn ins Textfeld **URL der Anmeldeseite** ein.
    5.  Kopieren Sie im Microsoft Azure-Portal auf der Seite **Einmaliges Anmelden konfigurieren für Syncplicity** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Abmeldeseiten-URL** ein.
    6.  Klicken Sie auf dem **Zertifikat des Identitätsanbieters** auf **Datei auswählen**, und laden Sie dann das Zertifikat hoch, welches Sie aus dem Microsoft Azure-Portal heruntergeladen haben.
    7.  Klicken Sie auf **Änderungen speichern**.

8.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Bestätigung](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Bestätigung")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich AAD-Benutzer anmelden können, müssen sie in der Syncplicity-Anwendung bereitgestellt werden. In diesem Abschnitt wird beschrieben, wie in Syncplicity AAD-Benutzerkonten erstellt werden.

###Führen Sie zum Bereitstellen von Benutzerkonten in Syncplicity die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrem **Syncplicity**-Mandanten an (z. B.: **https://company.Syncplicity.com*).

2.  Klicken Sie auf **Admin**, und wählen Sie **Benutzerkonten**.

3.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Benutzer verwalten](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Benutzer verwalten")

4.  Geben Sie die **E-Mail-Adresse** eines AAD-Kontos ein, das Sie bereitstellen möchten, und wählen Sie dann **Benutzer** als **Rolle**. Klicken Sie abschließend auf **Weiter**.

    ![Azure-Kontoinformationen](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Azure-Kontoinformationen")

    >[AZURE.NOTE]Der AAD-Kontoinhaber erhält eine E-Mail mit einem Link zur Bestätigung und Aktivierung des Kontos.

5.  Wählen Sie eine Gruppe in Ihrem Unternehmen aus, bei der Ihr neuer Benutzer Mitglied werden soll. Klicken Sie dann auf **Weiter**.

    ![Gruppenmitgliedschaft](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Gruppenmitgliedschaft")

    >[AZURE.NOTE]Wenn keine Gruppen aufgelistet sind, klicken Sie einfach auf **Weiter**.

6.  Wählen Sie die Ordner aus, die Sie auf dem Computer des Benutzers unter die Kontrolle von Syncplicity stellen möchten. Klicken Sie dann auf **Weiter**.

    ![Syncplicity-Ordner](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Syncplicity-Ordner")

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Syncplicity-Benutzerkonten oder mithilfe der von Syncplicity bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Syncplicity Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Syncplicity** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->