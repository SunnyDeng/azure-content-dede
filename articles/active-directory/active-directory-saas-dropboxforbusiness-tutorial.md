<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Dropbox für Unternehmen | Microsoft Azure" description="Hier erfahren Sie, wie Sie Dropbox für Unternehmen mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Dropbox für Unternehmen
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=522415), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Dropbox für Unternehmen erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Testmandanten in Dropbox für Unternehmen
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Dropbox für Unternehmen zugewiesen haben, mittels einmaliger Anmeldung auf der Unternehmenswebsite für Dropbox für Unternehmen bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Dropbox für Unternehmen
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Szenario")



##Aktivieren der Anwendungsintegration für Dropbox für Unternehmen
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Dropbox für Unternehmen aktivieren.

###So aktivieren Sie die Anwendungsintegration für Dropbox für Unternehmen

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** die Zeichenfolge **Dropbox für Unternehmen** ein.

    ![Anwendungskatalog](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Dropbox für Unternehmen** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Dropbox für Unternehmen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox für Unternehmen")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Dropbox für Unternehmen zu authentifizieren.

Im Rahmen dieses Verfahrens müssen Sie ein Base-64-codiertes Zertifikat in Ihren Dropbox für Unternehmen-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Dropbox für Unternehmen** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Dropbox für Unternehmen anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus:

     3.1. Melden Sie sich bei Ihrem Dropbox für Unternehmen-Mandanten an. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Einmaliges Anmelden konfigurieren")

     3.2. Klicken Sie im Navigationsbereich auf der linken Seite auf **Verwaltungskonsole**. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Einmaliges Anmelden konfigurieren")

     3.3. Klicken Sie in der **Verwaltungskonsole** im linken Navigationsbereich auf **Authentifizierung**. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Einmaliges Anmelden konfigurieren")

     3.4. Wählen Sie im Abschnitt **Einmaliges Anmelden** die Option **Einmaliges Anmelden aktivieren**, und klicken Sie dann auf **Mehr**, um diesen Abschnitt zu erweitern. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Einmaliges Anmelden konfigurieren")

     3.5. Kopieren Sie die URL neben **Benutzer können sich durch die Eingabe ihrer E-Mail-Adresse anmelden oder direkt gelangen zu**. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Einmaliges Anmelden konfigurieren")

     3.6. Fügen Sie die URL im Azure-Portal im Textfeld **Anmelde-URL für DropBox für Unternehmen** ein. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Einmaliges Anmelden konfigurieren")



4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Dropbox für Unternehmen** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Einmaliges Anmelden konfigurieren")


5. Gehen Sie auf Ihrem Dropbox für Unternehmen-Mandanten im Abschnitt **Einmaliges Anmelden** der Seite **Authentifizierung** folgendermaßen vor: <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Einmaliges Anmelden konfigurieren")

     5.1. Klicken Sie auf **Erforderlich**.

     5.2. Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Dropbox für Unternehmen** den Wert für **URL der Anmeldeseite**, und fügen Sie ihn ins Textfeld **Anmelde-URL** ein.


     5.3. Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat. > [AZURE.TIP] Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache).


     5.4. Klicken Sie auf **Zertifikat wählen**, und wechseln Sie dann zu Ihrer **Base-64-codierten Zertifikatdatei**.


     5.5. Klicken Sie auf **Änderungen speichern**, um die Konfiguration für Ihren DropBox für Unternehmen-Mandanten abzuschließen.


6. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen. <br><br> 

![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Einmaliges Anmelden konfigurieren")





##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Dropbox für Unternehmen aktivieren.


### So konfigurieren Sie die Benutzerbereitstellung

1. Klicken Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite für **Dropbox für Unternehmen** auf **Benutzerbereitstellung konfigurieren**, um das Dialogfeld **Benutzerbereitstellung konfigurieren** zu öffnen.

2. Klicken Sie auf der Seite "Benutzerbereitstellung aktivieren für DropBox für Unternehmen" auf "Benutzerbereitstellung aktivieren", um das Dialogfeld "Bei Dropbox anmelden zum Verbinden mit Azure AD" zu öffnen. <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Benutzerbereitstellung")

3. Melden Sie sich im Dialogfeld **Bei Dropbox anmelden zum Verbinden mit Azure AD** bei Ihrem Dropbox für Unternehmen-Mandanten an. <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Benutzerbereitstellung")



4. Klicken Sie auf **Zulassen**, um Azure AD den Zugriff auf Dropbox zu gewähren. <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Benutzerbereitstellung")



5. Klicken Sie zum Abschließen der Konfiguration auf die Schaltfläche **Abschließen**. <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Benutzerbereitstellung")




##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Dropbox für Unternehmen Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Dropbox für Unternehmen** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Ja")
  


Nach 10 Minuten können Sie überprüfen, ob das Konto mit Citrix GoToMeeting synchronisiert wurde.

Als ersten Überprüfungsschritt können Sie den Bereitstellungsstatus überprüfen, indem Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite für **Dropbox für Unternehmen** auf **Dashboard** klicken.

<br><br> ![Benutzer zuweisen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Benutzer zuweisen")


Ein erfolgreich abgeschlossener Benutzerbereitstellungszyklus wird durch einen entsprechenden Status angezeigt.

<br><br> ![Benutzer zuweisen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Benutzer zuweisen")


Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).




## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!-----HONumber=August15_HO8-->