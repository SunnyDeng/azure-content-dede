<properties pageTitle="Tutorial: Azure Active Directory-Integration mit InsideView | Microsoft Azure" description="Hier erfahren Sie, wie Sie InsideView mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit InsideView
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=529077), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und InsideView erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen InsideView-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie InsideView zugewiesen haben, mittels einmaliger Anmeldung auf der InsideView-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für InsideView
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-insideview-tutorial/IC794128.png "Szenario")
##Aktivieren der Anwendungsintegration für InsideView
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für InsideView aktivieren.

###So aktivieren Sie die Anwendungsintegration für InsideView

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-insideview-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-insideview-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-insideview-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **InsideView** ein.

    ![Anwendungskatalog](./media/active-directory-saas-insideview-tutorial/IC794129.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **InsideView** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei InsideView zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **InsideView** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794131.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei InsideView anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794132.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **InsideView-Antwort-URL** Ihre InsideView-SSO-URL ein (z. B. *`https://my.insideview.com/iv/<STS Name>/login.iv`) ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794133.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für InsideView** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794134.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der InsideView-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben auf der Symbolleiste auf **Administrator**, **Einstellungen für einmaliges Anmelden** und dann auf **SAML hinzufügen**.

    ![SAML-Einstellungen für einmaliges Anmelden](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML-Einstellungen für einmaliges Anmelden")

7.  Führen Sie im Abschnitt **Neue SAML hinzufügen** die folgenden Schritte aus:

    ![Neue SAML hinzufügen](./media/active-directory-saas-insideview-tutorial/IC794136.png "Neue SAML hinzufügen")

    1.  Geben Sie im Textfeld **STS-Name** einen Namen für die Konfiguration ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für InsideView ** den Wert von **Vom Dienstanbieter initiierter Endpunkt**, und fügen Sie ihn in das Textfeld **Nicht angeforderter SamlP/WS-Fed-Endpunkt** ein.
    3.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.
        
		>[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    4.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **STS-Zertifikat** ein.
    5.  Geben Sie ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** in das Textfeld **CRM-Benutzer-ID-Zuordnung** ein.
6.  Geben Sie ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** in das Textfeld **CRM-E-Mail-Zuordnung** ein.
7.  Geben Sie ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** in das Textfeld **CRM-Vornamenzuordnung** ein.
8.  Geben Sie ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** in das Textfeld **CRM-Nachnamenzuordnung** ein.
9.  Klicken Sie auf **Speichern**.

8.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794137.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei InsideView anmelden können, müssen sie in InsideView bereitgestellt werden. Im Fall von InsideView ist die Bereitstellung eine manuelle Aufgabe.
  
Wenden Sie sich an den Kundenservicemanager, oder senden Sie eine E-Mail an ****support@insideview.com**, um Benutzer oder Kontakte in InsideView erstellen zu lassen.

>[AZURE.NOTE]Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von InsideView-Benutzerkonten oder mithilfe der von InsideView bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie InsideView Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **InsideView** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-insideview-tutorial/IC794138.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-insideview-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->