<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Sprinklr | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Sprinklr mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Sprinklr
  
In diesem Tutorial wird die Integration von Azure und Sprinklr erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Sprinklr-Mandant
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Sprinklr zugewiesen haben, mittels einmaliger Anmeldung auf der Sprinklr-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Sprinklr
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Szenario")

##Aktivieren der Anwendungsintegration für Sprinklr
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Sprinklr aktivieren.

###So aktivieren Sie die Anwendungsintegration für Sprinklr:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** **Sprinklr** ein.

    ![Anwendungskatalog](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Sprinklr** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Sprinklr zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie mit diesem Verfahren nicht vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Sprinklr** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer Sprinklr anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Sprinklr-Anmelde-URL** die URL im Format „*https://\<Mandanten-Name>.sprinklr.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Sprinklr** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Sprinklr-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Verwaltung > Einstellungen**.

    ![Verwaltung](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Verwaltung")

7.  Wechseln Sie zur **Partner verwalten > Einmaliges Anmelden** im linken Fensterbereich.

    ![Partner verwalten](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Partner verwalten")

8.  Klicken Sie auf **+Einmalige Anmeldungen hinzufügen**.

    ![Einmaliges Anmelden](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Einmaliges Anmelden")

9.  Führen Sie im Dialogfeld **Einmaliges Anmelden** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Einmaliges Anmelden")

    1.  Geben Sie im Textfeld **Name** einen Namen für die Konfiguration ein (z. B.: *WAADSSOTest*).
    2.  Wählen Sie **Aktiviert**.
    3.  Wählen Sie **Neues SSO-Zertifikat verwenden**.
    4.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    5.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.
    6.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Sprinklr** den Wert der **Identitätsanbieter-ID**, und fügen Sie ihn in das Textfeld **Entitäts-ID** ein.
    7.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Sprinklr** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Anmelde-URL des Identitätsanbieters** ein.
    8.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Sprinkrl** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Abmelde-URL des Identitätsanbieters** ein.
    9.  Wählen Sie für **SAML-Benutzer-ID-Typ** **Erklärung enthält sprinklr.com-Benutzernamen des Benutzers**.
    10. Wählen Sie für **SAML-Benutzer-ID-Speicherort** die Option **Benutzer-ID ist das Namensbezeichnerelement der Ausstellererklärung**.
    11. Schließen Sie das Textfeld mit **Speichern**.

        ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich AAD-Benutzer anmelden können, müssen sie für den Zugriff innerhalb der Sprinklr-Anwendung bereitgestellt werden. In diesem Abschnitt wird beschrieben, wie in Sprinklr AAD-Benutzerkonten erstellt werden.

###Führen Sie zum Bereitstellen von Benutzerkonten in Sprinklr die folgenden Schritte aus:

1.  Melden Sie sich bei der Sprinklr-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Verwaltung > Einstellungen**.

    ![Verwaltung](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Verwaltung")

3.  Wechseln Sie zu **Client verwalten > Benutzer** im linken Fensterbereich.

    ![Einstellungen](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Einstellungen")

4.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Einstellungen](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Einstellungen")

5.  Führen Sie im Dialogfeld **Benutzer bearbeiten** die folgenden Schritte aus:

    ![Benutzer bearbeiten](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Benutzer bearbeiten")

    1.  Geben Sie in die Textfelder **E-Mail**, **Vorname** und **Nachname** die Informationen des Azure AD-Benutzerkontos ein, welches Sie bereitstellen möchten.
    2.  Wählen Sie **Kennwort deaktiviert**.
    3.  Wählen Sie eine **Sprache** aus.
    4.  Wählen Sie einen **Benutzertyp** aus.
    5.  Klicken Sie auf **Aktualisieren**.

    >[AZURE.IMPORTANT]**Kennwort deaktiviert** muss ausgewählt sein, um einem Benutzer die Anmeldung über einen Identitätsanbieter zu ermöglichen.

6.  Wechseln Sie zu **Rolle**, und führen Sie dann die folgenden Schritte aus:

    ![Partnerrollen](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Partnerrollen")

    1.  Wählen Sie aus der Liste **Global** die Option **ALL\_Permissions** aus.
    2.  Klicken Sie auf **Aktualisieren**.

>[AZURE.NOTE]Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Sprinklr-Benutzerkonten oder mithilfe der von Sprinklr bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Sprinklr Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Sprinklr **auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->