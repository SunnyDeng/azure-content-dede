<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit AirWatch | Microsoft Azure" 
    description="Erfahren Sie, wie Sie AirWatch mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit AirWatch

In diesem Lernprogramm wird die Integration von Azure und AirWatch erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein AirWatch-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Tutorials können sich die AirWatch zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer AirWatch-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für AirWatch
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##Aktivieren der Anwendungsintegration für AirWatch

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für AirWatch aktivieren.

###So aktivieren Sie die Anwendungsintegration für AirWatch

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-airwatch-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-airwatch-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-airwatch-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **AirWatch** ein.

    ![Anwendungskatalog](./media/active-directory-saas-airwatch-tutorial/IC791914.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **AirWatch** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei AirWatch zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei erstellen. Falls Sie mit diesem Verfahren nicht vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **AirWatch** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei AirWatch anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **AirWatch-Anmelde-URL** die von Ihren Benutzern zur Anmeldung bei der AirWatch-Anwendung verwendete URL ein (z. B. „*https://companycode.awmdm.com/AirWatch/Login?gid=companycode*“), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791918.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für AirWatch** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der AirWatch-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im linken Navigationsbereich auf **Konten**, und klicken Sie dann auf **Administratoren**.

    ![Administratoren](./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administratoren")

7.  Erweitern Sie das Menü **Einstellungen**und klicken Sie dann auf **Verzeichnisdienste**.

    ![Einstellungen](./media/active-directory-saas-airwatch-tutorial/IC791921.png "Einstellungen")

8.  Klicken Sie auf die Registerkarte **Benutzer**, geben Sie im Textfeld **Basis-DN** Ihren Domänennamen ein, und klicken Sie dann auf **Speichern**.

    ![Benutzer](./media/active-directory-saas-airwatch-tutorial/IC791922.png "Benutzer")

9.  Klicken Sie auf die Registerkarte **Server**.

    ![Server](./media/active-directory-saas-airwatch-tutorial/IC791923.png "Server")

10. Führen Sie die folgenden Schritte aus:

    ![Hochladen](./media/active-directory-saas-airwatch-tutorial/IC791924.png "Hochladen")

    1.  Wählen Sie unter **Verzeichnistyp** die Option **Keiner** aus.
    2.  Aktivieren Sie **SAML für Authentifizierung verwenden**.
    3.  Klicken Sie auf **Hochladen**, um das heruntergeladene Zertifikat hochzuladen.

11. Führen Sie im Abschnitt **Anforderung** die folgenden Schritte aus:

    ![Anforderung](./media/active-directory-saas-airwatch-tutorial/IC791925.png "Anforderung")

    1.  Wählen Sie als **Bindungstyp anfordern** die Option **POST** aus.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Airwatch** den Wert für **Dienst-URL für einmalige Anmeldung**, und fügen Sie ihn in das Textfeld **Identitätsanbieter-URL für einmaliges Anmelden** ein.
    3.  Wählen Sie als **NameID-Format** die Option **E-Mail-Adresse** aus.
    4.  Klicken Sie auf **Speichern**.

12. Klicken Sie erneut auf die Registerkarte **Benutzer**.

    ![Benutzer](./media/active-directory-saas-airwatch-tutorial/IC791926.png "Benutzer")

13. Führen Sie im Abschnitt **Attribut** die folgenden Schritte aus:

    ![Attribut](./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attribut")

    1.  Geben Sie im Textfeld **Objektbezeichner** den Wert ****http://schemas.microsoft.com/identity/claims/objectidentifier** ein.
2.  Geben Sie im Textfeld **Benutzername** den Wert ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** ein.
3.  Geben Sie im Textfeld **Anzeigename** den Wert ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** ein.
4.  Geben Sie im Textfeld **Vorname** den Wert ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** ein.
5.  Geben Sie im Textfeld **Nachname** den Wert ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** ein.
6.  Geben Sie in das Textfeld **E-Mail** den Wert ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** ein.
7.  Klicken Sie auf **Speichern**.

14. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei AirWatch anmelden können, müssen sie in AirWatch bereitgestellt werden. Im Fall von AirWatch ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei der **AirWatch**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie im Navigationsbereich links auf **Konten**, und klicken Sie dann auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-airwatch-tutorial/IC791929.png "Benutzer")

3.  Klicken Sie im Menü **Benutzer** auf **Listenansicht**, und klicken Sie dann auf **Hinzufügen > Benutzer hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-airwatch-tutorial/IC791930.png "Benutzer hinzufügen")

4.  Führen Sie im Dialogfeld **Hinzufügen / Benutzer bearbeiten** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-airwatch-tutorial/IC791931.png "Benutzer hinzufügen")

    1.  Geben Sie in die Textfelder **Benutzername**, **Kennwort**, **Kennwort bestätigen**, **Vorname**, **Nachname** und **E-Mail-Adresse** die Informationen eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von AirWatch-Benutzerkonten oder mithilfe der von AirWatch bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie AirWatch Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **AirWatch** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-airwatch-tutorial/IC791932.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-airwatch-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->