<properties pageTitle="Tutorial: Azure Active Directory-Integration mit New Relic | Microsoft Azure" description="Erfahren Sie, wie Sie New Relic mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit New Relic
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=536553), um Feedback abzugeben.
  
In diesem Tutorial wird erläutert, wie einmaliges Anmelden für Azure Active Directory und New Relic eingerichtet wird.
  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein New Relic-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Azure Active Directory-Benutzer, die Sie New Relic zugewiesen haben, mittels einmaliger Anmeldung über den AAD-Zugriffsbereich anmelden.

1.  Aktivieren der Anwendungsintegration für New Relic
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-new-relic-tutorial/IC797030.png "Szenario")
##Aktivieren der Anwendungsintegration für New Relic
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für New Relic aktivieren.

###So aktivieren Sie die Anwendungsintegration für New Relic

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-new-relic-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-new-relic-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-new-relic-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Begriff **New Relic** ein.

    ![Anwendungskatalog](./media/active-directory-saas-new-relic-tutorial/IC797031.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **New Relic** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei New Relic zu authentifizieren.

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **New Relic** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei New Relic anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **New Relic-Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei New Relic verwenden, und klicken Sie dann auf **Weiter**.

    Bei der App-URL handelt es sich um die URL Ihres New Relic-Mandanten \(z. B. **https://rpm.newrelic.com*):

    ![App-URL konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC797034.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für New Relic** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der **New Relic**-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im oberen Menü auf **Kontoeinstellungen**.

    ![Kontoeinstellungen](./media/active-directory-saas-new-relic-tutorial/IC797036.png "Kontoeinstellungen")

7.  Klicken Sie auf die Registerkarte **Sicherheit und Authentifizierung** und anschließend auf die Registerkarte **Einmaliges Anmelden**.

    ![Einmaliges Anmelden](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Einmaliges Anmelden")

8.  Führen Sie auf der Dialogfeldseite für SAML die folgenden Schritte aus:

    ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  Klicken Sie auf **Datei auswählen**, um Ihr heruntergeladenes Azure Active Directory-Zertifikat hochzuladen.
    2.  Kopieren Sie im Microsoft Azure-Portal auf der Seite **Einmaliges Anmelden konfigurieren für New Relic** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn ins Textfeld **Remoteanmelde-URL** ein.
    3.  Kopieren Sie im Microsoft Azure-Portal auf der Seite **Einmaliges Anmelden konfigurieren für New Relic** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn ins Textfeld **Zielseiten-URL für die Abmeldung** ein.
    4.  Klicken Sie auf **Änderungen speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure Active Directory-Benutzer bei New Relic anmelden können, müssen sie in New Relic bereitgestellt werden. Im Fall von New Relic ist die Bereitstellung eine manuelle Aufgabe.

###Führen Sie zum Bereitstellen von Benutzerkonten in New Relic die folgenden Schritte aus:

1.  Melden Sie sich bei der **New Relic**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie im oberen Menü auf **Kontoeinstellungen**.

    ![Kontoeinstellungen](./media/active-directory-saas-new-relic-tutorial/IC797040.png "Kontoeinstellungen")

3.  Klicken Sie links im Bereich **Konto** auf **Zusammenfassung** und anschließend auf **Benutzer hinzufügen**.

    ![Kontoeinstellungen](./media/active-directory-saas-new-relic-tutorial/IC797041.png "Kontoeinstellungen")

4.  Führen Sie im Dialogfeld **Aktive Benutzer** die folgenden Schritte aus:

    ![Aktive Benutzer](./media/active-directory-saas-new-relic-tutorial/IC797042.png "Aktive Benutzer")

    1.  Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse eines gültigen Azure Active Directory-Benutzers ein, den Sie bereitstellen möchten.
    2.  Wählen Sie für **Rolle** die Option **Benutzer** aus.
    3.  Klicken Sie auf **Diesen Benutzer hinzufügen**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von New Relic-Benutzerkonten oder mithilfe der von New Relic bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie New Relic Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **New Relic** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-new-relic-tutorial/IC797043.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-new-relic-tutorial/IC767830.png "Ja")
  
Wenn Sie die Einstellungen für einmaliges Anmelden testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->