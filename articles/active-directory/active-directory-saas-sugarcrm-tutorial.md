<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Sugar CRM | Microsoft Azure" description="Erfahren Sie, wie Sie Sugar CRM mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Sugar CRM
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=534768), um Feedback abzugeben.
  
In diesem Lernprogramm wird die Integration von Azure und Sugar CRM erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Sugar CRM-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Sugar CRM zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Sugar CRM-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Alternativ können Sie die [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586) nutzen.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Sugar CRM
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Szenario")

##Aktivieren der Anwendungsintegration für Sugar CRM
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Sugar CRM aktivieren.

###So aktivieren Sie die Anwendungsintegration für Sugar CRM:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** **Sugar CRM** ein.

    ![Anwendungskatalog](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Sugar CRM** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Sugar CRM zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei in Ihren Sugar CRM-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Sugar CRM** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Sugar CRM anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Sugar CRM-Anmelde-URL** die URL ein, welche die Benutzer zur Anmeldung bei Sugar CRM verwenden \(z. B. **http://company.sugarondemand.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Sugar CRM** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster auf der Sugar CRM-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Administrator**.

    ![Admin](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

7.  Klicken Sie unter **Administration** auf **Kennwortverwaltung**.

    ![Verwaltung](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Verwaltung")

8.  Aktivieren Sie **SAML-Authentifizierung aktivieren**.

    ![Verwaltung](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Verwaltung")

9.  Führen Sie im Abschnitt für die **SAML-Authentifizierung** die folgenden Schritte aus:

    ![SAML-Authentifizierung](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML-Authentifizierung")

    1.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Sugar CRM** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn ins Textfeld **Anmelde-URL** ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Sugar CRM** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn ins Textfeld **SLO-URL** ein.
    3.  Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat.

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    4.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie anschließend das gesamte Zertifikat in das Textfeld **X.509-Zertifikat** ein.
    5.  Klicken Sie auf **Speichern**.

10. Wählen Sie im Azure-Portal auf der Dialogseite **Einmaliges Anmelden konfigurieren für Sugar CRM** die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Sugar CRM anmelden können, müssen sie in Sugar CRM bereitgestellt werden. Im Fall von Sugar CRM ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit:

1.  Melden Sie sich bei der **Sugar CRM**-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Administrator**.

    ![Admin](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

3.  Klicken Sie im Abschnitt **Verwaltung** auf **Benutzerverwaltung**.

    ![Verwaltung](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Verwaltung")

4.  Klicken Sie auf **Benutzer \> Neuen Benutzer erstellen**.

    ![Neuen Benutzer erstellen](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Neuen Benutzer erstellen")

5.  Führen Sie auf der Registerkarte **Benutzerprofil** die folgenden Schritte aus:

    ![Neuer Benutzer](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "Neuer Benutzer")

    1.  Geben Sie die entsprechenden Informationen in die Textfelder „Benutzername“, „Nachname“ und „E-Mail-Adresse“ eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.

6.  Wählen Sie als **Status** **Aktiv**.

7.  Führen Sie auf der Registerkarte „Kennwort“ die folgenden Schritte aus:

    ![Neuer Benutzer](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "Neuer Benutzer")

    1.  Geben Sie das Kennwort in das entsprechende Textfeld ein.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von Sugar CRM-Benutzerkonten oder mithilfe der von Sugar CRM bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Sugar CRM Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Sugar CRM** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->