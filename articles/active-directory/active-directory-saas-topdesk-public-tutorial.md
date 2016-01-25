<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit TOPdesk - Public | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie TOPdesk - Public mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit TOPdesk - Public

In diesem Tutorial wird die Integration von Azure und TOPdesk - Public erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein TOPdesk - Public-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die TOPdesk - Public zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer TOPdesk - Public-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für TOPdesk - Public
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Szenario")

##Aktivieren der Anwendungsintegration für TOPdesk - Public
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für TOPdesk - Public aktivieren.

###So aktivieren Sie die Anwendungsintegration für TOPdesk - Public:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** **TOPdesk - Public** ein.

    ![Anwendungskatalog](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **TOPdesk - Public** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![TOPdesk Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei TOPdesk - Public zu authentifizieren. Für das Konfigurieren des einmaligen Anmeldens für TOPdesk - Public müssen Sie eine Logosymboldatei hochladen. Um die Symboldatei zu erhalten, wenden Sie sich an das TOPdesk-Supportteam.

###So konfigurieren Sie einmaliges Anmelden:

1.  Melden Sie sich bei der **TOPdesk - Public**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie im Menü **TOPdesk - Public** auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Einstellungen")

3.  Klicken Sie auf **Anmeldeeinstellungen**.

    ![Anmeldeeinstellungen](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Anmeldeeinstellungen")

4.  Erweitern Sie das Menü **Anmeldeeinstellungen**, und klicken Sie dann auf **Allgemein**.

    ![Allgemein](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Allgemein")

5.  Führen Sie im Abschnitt **Öffentlich** des Konfigurationsabschnitts **SAML-Anmeldung** die folgenden Schritte aus:

    ![Technische Einstellungen](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technische Einstellungen")

    1.  Klicken Sie auf **Herunterladen**, um die öffentliche Metadatendatei herunterzuladen und sie dann lokal auf Ihrem Computer zu speichern.
    2.  Öffnen Sie die Metadatendatei, und suchen Sie den Knoten **AssertionConsumerService**. ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
    3.  Kopieren Sie den Wert **AssertionConsumerService**.  

        >[AZURE.NOTE]Sie benötigen den Wert später im Tutorial im Abschnitt **App-URL konfigurieren**.

6.  Melden Sie sich in einem anderen Webbrowserfenster beim **Azure Active Directory** Portal als Administrator an.

7.  Klicken Sie auf der Anwendungsintegrationsseite für **TOPdesk - Public** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Einmaliges Anmelden konfigurieren")

8.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei TOPdesk - Public anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Einmaliges Anmelden konfigurieren")

9.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "App-URL konfigurieren")

    1.  Geben Sie in das Textfeld **TOPdesk - Public-Anmelde-URL** die URL ein, welche die Benutzer zur Anmeldung bei der „TOPdesk - Public“-Anwendung verwenden (z. B. „**https://qssolutions.topdesk.net*").
2.  Fügen Sie in das Textfeld **TOPdesk – öffentliche Antwort-URL** die **TOPdesk - Public-AssertionConsumerService-URL** ein (z. B.: "**https://qssolutions.topdesk.net/tas/public/login/saml*")
3.  Klicken Sie auf **Weiter**.

10. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für TOPdesk - Public** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Einmaliges Anmelden konfigurieren")

11. Zum Erstellen einer Zertifikatsdatei führen Sie die folgenden Schritte durch:

    ![Zertifikat](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Zertifikat")

    1.  Öffnen Sie die heruntergeladene Metadatendatei.
    2.  Erweitern Sie den Knoten **RoleDescriptor**, der über einen **xsi:type** von **fed:ApplicationServiceType** verfügt.
    3.  Kopieren Sie den Wert des Knotens **X509Certificate**.
    4.  Speichern Sie den kopierten **X509Certificate**-Wert lokal in einer Datei auf Ihrem Computer.

12. Klicken Sie auf Ihrer TOPdesk - Public-Unternehmenswebsite im Menü **TOPdesk** auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Einstellungen")

13. Klicken Sie auf **Anmeldeeinstellungen**.

    ![Anmeldeeinstellungen](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Anmeldeeinstellungen")

14. Erweitern Sie das Menü **Anmeldeeinstellungen**, und klicken Sie dann auf **Allgemein**.

    ![Allgemein](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Allgemein")

15. Klicken Sie im Abschnitt **Öffentlich** auf **Hinzufügen**.

    ![SAML-Anmeldung](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML-Anmeldung")

16. Führen Sie auf der Seite **SAML Configuration Assistant** die folgenden Schritte aus:

    ![SAML Configuration Assistant](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML Configuration Assistant")

    1.  Klicken Sie zum Hochladen der heruntergeladenen Metadatendatei unter **Verbundmetadaten** auf **Durchsuchen**.
    2.  Klicken Sie zum Hochladen Ihrer Zertifikatdatei unter **Zertifkat (RSA)** auf **Durchsuchen**.
    3.  Klicken Sie z um Hochladen der Logodatei, die Sie vom TOPdesk-Supportteam erhalten haben, unter **Logosymbol** auf **Durchsuchen**.
    4.  Geben Sie in das Textfeld **Benutzernamen-Attribut** ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** ein.
5.  Geben Sie in das Textfeld **Anzeigenname** einen Namen für die Konfiguration ein.
    6.  Klicken Sie auf **Speichern**.

17. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei TOPdesk - Public anmelden können, müssen sie in TOPdesk - Public bereitgestellt werden. Im Fall von TOPdesk - Public ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **TOPdesk - Public**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie im Menü oben auf **TOPdesk > Neu > Support-Dateien > Person**.

    ![Person](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Person")

3.  Führen Sie im Dialogfeld „Neue Person“ die folgenden Schritte aus:

    ![Neue Person](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "Neue Person")

    1.  Klicken Sie auf die Registerkarte „Allgemein“.
    2.  Geben Sie in das Textfeld „Nachname“ den Nachnamen eines gültigen Azure Active Directory-Kontos ein, welches Sie bereitstellen möchten.
    3.  Wählen Sie einen **Speicherort** für das Konto aus.
    4.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von TOPdesk - Public-Benutzerkonten oder mithilfe der von TOPdesk - Public bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie TOPdesk - Public Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für TOPdesk - Public auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->