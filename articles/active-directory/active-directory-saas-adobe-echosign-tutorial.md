<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Adobe EchoSign | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Adobe EchoSign mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" authors="markusvi"  
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

#Lernprogramm: Azure Active Directory-Integration mit Adobe EchoSign

In diesem Lernprogramm wird die Integration von Azure und Adobe EchoSign erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Adobe EchoSign-Abonnement, für das einmaliges Anmelden aktiviert ist.

Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Adobe EchoSign zugewiesen haben, mittels einmaligen Anmeldens auf Ihrer Adobe EchoSign-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Adobe EchoSign
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Szenario")
##Aktivieren der Anwendungsintegration für Adobe EchoSign

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Adobe EchoSign aktivieren.

###So aktivieren Sie die Anwendungsintegration für Adobe EchoSign

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Adobe EchoSign** ein.

    ![Anwendungskatalog](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Adobe EchoSign**, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Adobe EchoSign zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei erstellen. Falls Sie mit diesem Verfahren nicht vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite **Adobe EchoSign** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Adobe EchoSign anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **Adobe EchoSign-Anmelde-URL** die URL im Format „**https://company.echosign.com/*"“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Adobe EchoSign** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Adobe EchoSign-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü oben auf **Konto**, und klicken Sie dann im Navigationsbereich links unter**Kontoeinstellungen** auf **SAML-Einstellungen**.

    ![Konto](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Konto")

7.  Führen Sie im Abschnitt für die SAML-Einstellungen die folgenden Schritte aus:

    ![SAML Settings](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML-Einstellungen")

    1.  Wählen Sie als **SAML-Modus** die Option **SAML erforderlich**.
    2.  Aktivieren Sie **EchoSign-Kontoadministratoren die Anmeldung mit ihren EchoSign-Anmeldeinformationen erlauben**.
    3.  Wählen Sie für **Benutzererstellung** die Option **Über SAML authentifizierte Benutzer automatisch hinzufügen** aus.

8.  Fahren Sie mit den folgenden Schritten fort:

    ![SAML-Einstellungen](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML-Einstellungen")

    1.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Adobe EchoSign** den Wert der **Entitäts-ID**, und fügen Sie ihn in das Textfeld **IdP Entity ID** ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Adobe EchoSign** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **IdP Login URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Adobe EchoSign** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **IdP Logout URL** ein.
    4.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

		>[AZURE.TIP]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache).
    5.  Öffnen Sie das Base-64-codierte Zertifikat in Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **IdP Certificate** ein.
    6.  Klicken Sie auf **Änderungen speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Adobe EchoSign anmelden können, müssen sie in Adobe EchoSign bereitgestellt werden. Im Fall von Adobe EchoSign ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrer **Adobe EchoSign**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie im Menü oben auf **Konto**, und klicken Sie dann im Navigationsbereich links unter **Benutzer & Gruppen** auf **Neuen Benutzer erstellen**.

    ![Konto](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Konto")

3.  Führen Sie im Abschnitt **Neuen Benutzer erstellen** die folgenden Schritte aus:

    ![Create User](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Benutzer erstellen")

    1.  Geben Sie in die Textfelder **E-Mail-Adresse**, **Vorname** und **Nachname** die entsprechenden Informationen eines gültigen AAD-Benutzerkontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Benutzer erstellen**.

		>[AZURE.NOTE]Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Adobe EchoSign-Benutzerkonten oder mithilfe von APIs erstellen, die von Adobe EchoSign zum Bereitstellen von AAD-Benutzerkonten bereitgestellt werden.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Adobe EchoSign Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Adobe EchoSign** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->