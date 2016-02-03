<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit TeamSeer | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie TeamSeer mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit TeamSeer
  
In diesem Tutorial wird die Integration von Azure und TeamSeer erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein TeamSeer-Mandant
  
Nach Abschluss dieses Tutorials können sich die TeamSeer zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer TeamSeer-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für TeamSeer
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Szenario")

##Aktivieren der Anwendungsintegration für TeamSeer
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für TeamSeer aktivieren.

###So aktivieren Sie die Anwendungsintegration für TeamSeer:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** **TeamSeer** ein.

    ![Anwendungskatalog](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **TeamSeer** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei TeamSeer zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **TeamSeer** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei TeamSeer anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **TeamSeer-Anmelde-URL** die URL im Format „**http://www.teamseer.com/companyid*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789629.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für TeamSeer** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster auf der TeamSeer-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Personalverwaltung**.

    ![Personalverwaltung](./media/active-directory-saas-teamseer-tutorial/IC789634.png "Personalverwaltung")

7.  Klicken Sie auf **Setup**.

    ![Einrichtung](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Einrichtung")

8.  Klicken Sie auf **SAM-Anbieterdetails einrichten**.

    ![SAML-Einstellungen](./media/active-directory-saas-teamseer-tutorial/IC789636.png "SAML-Einstellungen")

9.  Führen Sie im Abschnitt „SAML-Anbieterdetails“ die folgenden Schritte aus:

    ![SAML-Einstellungen](./media/active-directory-saas-teamseer-tutorial/IC789637.png "SAML-Einstellungen")

    1.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für TeamSeer** den Wert für **Dienst-URL für einmaliges Anmelden**, und fügen Sie ihn ins Textfeld **URL** ein.
    2.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache).

    3.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Öffentliches IdP-Zertifikat** ein.

10. Führen Sie zum Abschluss der SAML-Anbieterkonfiguration die folgenden Schritte aus:

    ![SAML-Einstellungen](./media/active-directory-saas-teamseer-tutorial/IC789638.png "SAML-Einstellungen")

    1.  Geben Sie unter **Test-e-Mail-Adressen** die E-Mail-Adresse des Testbenutzers ein.
    2.  Geben Sie in das Textfeld **Aussteller** die Aussteller-URL des Dienstanbieters ein.
    3.  Klicken Sie auf **Speichern**.

11. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei TeamSeer anmelden können, müssen sie in TeamSeer bereitgestellt werden. Im Fall von TeamSeer ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit:

1.  Melden Sie sich bei Ihrer **TeamSeer**-Unternehmenswebsite als Administrator an.

2.  Führen Sie die folgenden Schritte aus:

    ![Personalverwaltung](./media/active-directory-saas-teamseer-tutorial/IC789640.png "Personalverwaltung")

    1.  Wechseln Sie zu **Personalverwaltung > Benutzer**.
    2.  Klicken Sie auf **Assistent Neuen Benutzer ausführen**.

3.  Führen Sie im Abschnitt mit den **Benutzerdaten** die folgenden Schritte aus:

    ![Benutzerdetails](./media/active-directory-saas-teamseer-tutorial/IC789641.png "Benutzerdetails")

    1.  Geben Sie **Vorname**, **Nachname**, **Benutzername (E-Mail-Adresse)** eines gültigen AAD-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Weiter**.

4.  Befolgen Sie den Anweisungen am Bildschirm zum Hinzufügen eines neuen Benutzers. Klicken Sie dann auf **Fertig stellen**.

>[AZURE.NOTE]Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von TeamSeer-Benutzerkonten oder mithilfe der von TeamSeer bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie TeamSeer Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **TeamSeer** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->