<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit Citrix GoToMeeting | Microsoft Azure" description="Erfahren Sie, wie Sie Citrix GoToMeeting mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit Citrix GoToMeeting  
Gilt für: Azure

>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=522412), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und Citrix GoToMeeting erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mandant für Citrix GoToMeeting

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Citrix GoToMeeting
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Konfiguration](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Konfiguration")
##Aktivieren der Anwendungsintegration für Citrix GoToMeeting

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Citrix GoToMeeting aktivieren.

###So aktivieren Sie die Anwendungsintegration für Citrix GoToMeeting

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Citrix GoToMeeting** ein.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  Wählen Sie im Ergebnisbereich **Citrix GoToMeeting** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Citrix GoToMeeting zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei in Ihren Citrix GoToMeeting-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie auf der Anwendungsintegrationsseite für **Citrix GoToMeeting** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Einmaliges Anmelden aktivieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Citrix GoToMeeting anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Einmaliges Anmelden konfigurieren")

3.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um Citrix GoToMeeting** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Einmaliges Anmelden konfigurieren")

4.  Melden Sie sich in einem anderen Browser-Fenster bei Ihrem Citrix GoToMeeting-Mandanten als Administrator an.

5.  Öffnen Sie die Seite [Set up SAML 2.0 single sign-on configuration \(SSO\)](https://login.citrixonline.com/saml/settings.html), und führen Sie die folgenden Schritte aus:

    ![SAML-Setup](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC689232.png "SAML-Setup")

    1.  Aktivieren Sie **Configure manually**.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Citrix GoToMeeting** den Wert für **URL der Abmeldeseite**, und fügen Sie ihn in das Textfeld **Sign-out page URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Citrix GoToMeeting** den Wert für **URL der Anmeldeseite**, und fügen Sie ihn in das Textfeld **Sign-in page URL** ein.
    4.  Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\).

    5.  Klicken Sie auf **Replace certificate**, und laden Sie dann Ihre **base-64 encoded certificate file** hoch.
    6.  Klicken Sie auf **Speichern**.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Citrix GoToMeeting aktivieren.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Klicken Sie im Azure-Verwaltungsportal auf die Anwendungsintegrationsseite **Citrix GoToMeeting**, und klicken Sie auf **Benutzerbereitstellung konfigurieren**, um das Dialogfeld **Benutzerbereitstellung konfigurieren** zu öffnen.

    ![Konfigurieren der Benutzerbereitstellung](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Konfigurieren der Benutzerbereitstellung")

2.  Geben Sie auf der Seite **Citrix GotoMeeting-Administratorbenutzername** einen Citrix GoToMeeting-Kontonamen ein, der über Administratorrechte für Ihren Citrix GoToMeeting-Mandanten verfügt, und klicken Sie dann auf **Weiter**.

    ![Konfigurieren der Benutzerbereitstellung](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Konfigurieren der Benutzerbereitstellung")

3.  Klicken Sie anschließend auf der Seite **Bestätigung** auf das Häkchen, um die Konfiguration zu speichern.

4.  Klicken Sie auf die Schaltfläche **Überprüfen**, um die Konfiguration zu überprüfen.
##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Citrix GoToMeeting Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Citrix GoToMeeting** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Ja")

Nach 10 Minuten können Sie überprüfen, ob das Konto mit Citrix GoToMeeting synchronisiert wurde.

Als ersten Überprüfungsschritt können Sie den Bereitstellungsstatus überprüfen, indem Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite **Citrix GoToMeeting** auf "Dashboard" klicken.

![Dashboard](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

Ein erfolgreich abgeschlossener Benutzerbereitstellungszyklus wird durch einen entsprechenden Status angezeigt:

![Integrationsstatus](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integrationsstatus")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich.

Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->