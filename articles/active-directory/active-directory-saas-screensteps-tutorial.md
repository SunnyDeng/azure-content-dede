<properties pageTitle="Tutorial: Azure Active Directory-Integration mit ScreenSteps | Microsoft Azure" description="Erfahren Sie, wie Sie ScreenSteps mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und mehr zu aktivieren!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit ScreenSteps
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=525874), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und ScreenSteps erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein ScreenSteps-Mandant
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie ScreenSteps zugewiesen haben, mittels einmaliger Anmeldung auf der ScreenSteps-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Alternativ können sie den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ScreenSteps
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Szenario")
##Aktivieren der Anwendungsintegration für ScreenSteps
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ScreenSteps aktivieren.

###So aktivieren Sie die Anwendungsintegration für ScreenSteps:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** **ScreenSteps** ein.

    ![Anwendungskatalog](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **ScreenSteps** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ScreenSteps zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **ScreenSteps** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei ScreenSteps anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **ScreenSteps-Anmelde-URL** die URL im Format „*https://\\<Mandantenname\>.ScreenSteps.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778521.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für ScreenSteps** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der ScreenSteps-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Kontoverwaltung**.

    ![Kontenverwaltung](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Kontenverwaltung")

7.  Klicken Sie auf **Remoteauthentifizierung**.

    ![Remoteauthentifizierung](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Remoteauthentifizierung")

8.  Klicken Sie auf **Authentication Endpoint erstellen**.

    ![Remoteauthentifizierung](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Remoteauthentifizierung")

9.  Führen Sie im Abschnitt **Authentication Endpoint erstellen** die folgenden Schritte aus:

    ![Authentication Endpoint erstellen](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Authentication Endpoint erstellen")

    1.  Geben Sie in das Textfeld **Titel** einen Titel ein.
    2.  Wählen Sie aus der Liste **Mode** die Option **SAML** aus.
    3.  Klicken Sie auf **Erstellen**.

10. Führen Sie im Abschnitt **Remote Authentication Endpoint** die folgenden Schritte aus:

    ![Remote Authentication Endpoint](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote Authentication Endpoint")

    1.  Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um ScreenSteps** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Remoteanmelde-URL** ein.
    2.  Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um ScreenSteps** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Remoteabmelde-URL** ein.
    3.  Klicken Sie auf **Datei auswählen**, und laden Sie dann das heruntergeladene Zertifikat hoch.
    4.  Klicken Sie auf **Aktualisieren**.

11. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei **ScreenSteps**, anmelden können, müssen sie in **ScreenSteps** bereitgestellt werden. Im Fall von **ScreenSteps** ist die Bereitstellung eine manuelle Aufgabe.

###Führen Sie zum Bereitstellen von Benutzerkonten in ScreenSteps die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrem **ScreenSteps**-Mandanten an.

2.  Klicken Sie auf **Kontoverwaltung**.

    ![Kontenverwaltung](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Kontenverwaltung")

3.  Klicken Sie auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Benutzer")

4.  Klicken Sie auf **Benutzer erstellen**.

    ![Alle Benutzer](./media/active-directory-saas-screensteps-tutorial/IC778545.png "Alle Benutzer")

5.  Wählen Sie aus der Liste **Benutzerrolle** eine Rolle für den Benutzer.

6.  Geben Sie im Abschnitt „Benutzerrolle“ die Daten für **Vorname**, **Nachname**, **E-Mail**, **Anmelden**, **Kennwort**, **Kennwortbestätigung** in die entsprechenden Textfelder eines gültigen AAD-Kontos ein, das Sie bereitstellen möchten.

    ![Neuer Benutzer](./media/active-directory-saas-screensteps-tutorial/IC778546.png "Neuer Benutzer")

7.  Wählen Sie im Abschnitt Gruppen die Option **Authentifizierungsgruppe \(SAML\)** aus, und klicken Sie dann auf **Benutzer erstellen**.

    ![Groups \(Gruppen\)](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Groups (Gruppen)")

>[AZURE.NOTE]Sie können ScreenSteps-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von ScreenSteps-Benutzerkonten oder mithilfe der von FM:Systems bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ScreenSteps Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **ScreenSteps** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-screensteps-tutorial/IC773094.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Benutzer zuweisen](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Benutzer zuweisen")
  
Wenn Sie die Einstellungen für einmaliges Anmelden testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->