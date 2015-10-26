<properties pageTitle="Tutorial: Azure Active Directory-Integration mit ServiceNow | Microsoft Azure" description="Hier erfahren Sie, wie Sie ServiceNow mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit ServiceNow
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=521880), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und ServiceNow erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mandanten in ServiceNow
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie ServiceNow zugewiesen haben, mittels einmaliger Anmeldung auf der ServiceNow-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ServiceNow
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Szenario")
##Aktivieren der Anwendungsintegration für ServiceNow
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ServiceNow aktivieren.

###So aktivieren Sie die Anwendungsintegration für ServiceNow

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Text **ServiceNow** ein.

    ![Anwendungskatalog](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **ServiceNow** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ServiceNow zu authentifizieren.

Im Rahmen dieses Verfahrens müssen Sie ein Base-64-codiertes Zertifikat in Ihren Dropbox für Unternehmen-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **ServiceNow** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei ServiceNow anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **ServiceNow-Anmelde-URL** die URL im Format "*https://<InstanceName>.servicenow.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC769497.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für ServiceNow** auf **Zertifikat herunterladen**, speichern Sie das Zertifikat lokal auf Ihrem Computer, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Einmaliges Anmelden konfigurieren")

5.  Klicken Sie in Ihrem ServiceNow-Mandanten auf der Navigationsleiste auf der linken Seite auf **Eigenschaften**, um die Seite mit den **SAML 2.0 SSO-Eigenschaften** zu öffnen.


6. Führen Sie im Dialogfeld mit den **SAML 2.0 SSO-Eigenschaften** die folgenden Schritte aus:

     6\.1. Wählen Sie unter **Enable external authentication** die Option **Yes**.


     6\.2. Geben Sie im Textfeld **The Identity Provider URL which will issue the SAML2 security token with user info** die Information **https://sts.windows.net/<GUID Ihres Mandanten>/** ein.


     6\.3. Geben Sie im Textfeld **The base URL to the Identity Provider’s AuthnRequest service** die Information **https://login.windows.net/<GUID Ihres Mandanten>/saml2** ein.


     6\.4. Geben Sie im Textfeld **The base URL to the Identity Provider’s SingleLogoutRequest service** die Information **https://login.windows.net/<GUID Ihres Mandanten>/saml2** ein.


     6\.5. Geben Sie im Textfeld **The protocol binding for the Identity Provider’s SingleLogoutRequest service** die Information **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect** ein.

     6\.6. Wählen Sie unter **Sign LogoutRequest** die Option **Yes** aus.

     6\.7. Geben Sie **https://login.windows.net/<GUID Ihres Mandanten>/saml2** in das Textfeld **Wenn beim einmaligen Anmelden in SAML 2.0 ein Fehler auftritt, weil die Sitzung nicht authentifiziert ist oder dies die Erstanmeldung ist, zu folgender URL umleiten ** ein.

  

7. Gehen Sie im Abschnitt mit den **Dienstanbietereigenschaften (ServiceNow)** folgendermaßen vor:

     7\.1. Geben Sie im Textfeld **The URL to Service-now instance homepage** die URL zur Homepage Ihrer ServiceNow-Instanz ein. Die URL zur Homepage der ServiceNow-Instanz ist eine Verkettung Ihrer **ServiceNow-Mandanten-URL** und **/navpage.do**: **https://<InstanceName>.service-now.com/navpage.do** <br><br> ![Homepage der ServiceNow-Instanz](./media/active-directory-saas-servicenow-tutorial/IC700342.png "Homepage der ServiceNow-Instanz")


     7\.2. Geben Sie im Textfeld **The entity identification, or the issuer** die URL Ihres Mandanten ein.

     7\.3. Geben Sie im Textfeld **The audience uri that accepts SAML2 token** die URL Ihres Mandanten ein.

     7\.4. Geben Sie im Textfeld **The User table field to match with the Subject’s NameID element in the SAMLResponse** die Zeichenfolge **email** ein.

     7\.5. Geben Sie im Textfeld **The NameID policy to use for returning the Subject’s NameID in the SAMLResponse** die Zeichenfolge **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified** ein.

     7\.6. Lassen Sie **Create an AuthnContextClass request in the AuthnRequest statement** deaktiviert.

     7\.7. Geben Sie im Textfeld **The AuthnContextClassRef method that will be included in our SAML 2.0 AuthnRequest to the Identity Provider** Folgendes ein: ****http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.



8. Führen Sie im Abschnitt **Advanced settings** die folgenden Schritte aus:

     8\.1. Geben Sie im Textfeld **The number in seconds before “notBefore” constraint, or after “notOnOrAfter” constraint to consider still valid** die Anzahl **60** ein.


9. Klicken Sie zum Speichern der Konfiguration auf **Save**.

10. Klicken Sie auf der Navigationsleiste auf der linken Seite auf **Certificate**, um die Seite **Certificate** zu öffnen.



11. Führen Sie auf der Zertifikatseite die folgenden Schritte aus, um Ihr Zertifikat hochzuladen:

     11\.1. Klicken Sie auf **Neu**.

     11\.2. Geben Sie im Textfeld **Name** den Namen **SAML 2.0** ein.

     11\.3. Wählen Sie **Active**.

     11\.4. Wählen Sie unter **Format** das Format **PEM** aus.

     11\.5. Erstellen Sie eine Base64-codierte Datei aus dem heruntergeladenen Zertifikat.[AZURE.NOTE]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache).

     11\.6. Öffnen Sie die Base64-codierte Datei in **Editor**, und kopieren Sie dann den Inhalt dieser Datei in die Zwischenablage.

     11\.7. Fügen Sie den Inhalt der Zwischenablage in das Textfeld **PEM Certificate** ein.

     11\.8. Klicken Sie auf **Senden**.



12. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus, und klicken Sie dann auf "Abschließen", um das Dialogfeld "Einmaliges Anmelden konfigurieren" zu schließen. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC749326.png "Einmaliges Anmelden konfigurieren")




##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für ServiceNow aktivieren.


### So konfigurieren Sie die Benutzerbereitstellung

1. Klicken Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite für **ServiceNow** auf **Benutzerbereitstellung konfigurieren**. <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Benutzerbereitstellung")


2. Stellen Sie auf der Seite **Geben Sie Ihre ServiceNow-Anmeldeinformationen ein, um die automatische Benutzerbereitstellung zu aktivieren** die folgenden Konfigurationseinstellungen bereit: Benutzerbereitstellung konfigurieren

     2\.1. Geben Sie im Textfeld **ServiceNow-Instanzname** den Namen des ServiceNow-Instanz ein.

     2\.2. Geben Sie im Textfeld **Administratorbenutzername für ServiceNow** den Namen des ServiceNow-Administratorkontos ein.

     2\.3. Geben Sie im Textfeld **ServiceNow-Administratorkennwort** das Kennwort für dieses Konto ein.

     2\.4. Klicken Sie auf **Überprüfen**, um die Konfiguration zu überprüfen.

     2\.5. Klicken Sie auf **Weiter**, um die Seite **Nächste Schritte** zu öffnen.

     2\.6. Wenn Sie alle Benutzer für diese Anwendung bereitstellen möchten, wählen Sie **Dieser Anwendung automatisch alle Benutzerkonten im Verzeichnis bereitstellen**. <br><br> ![Nächste Schritte](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Nächste Schritte")

     2\.7. Klicken Sie auf der Seite **Nächste Schritte** auf **Abgeschlossen**, um die Konfiguration zu speichern.











##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ServiceNow Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **ServiceNow** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=Oct15_HO3-->