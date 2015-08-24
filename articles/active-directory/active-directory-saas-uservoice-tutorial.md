<properties pageTitle="Tutorial: Azure Active Directory-Integration mit UserVoice | Microsoft Azure" description="Erfahren Sie, wie Sie UserVoice mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit UserVoice
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=524477), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und UserVoice erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein UserVoice-Mandant
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie UserVoice zugewiesen haben, mittels einmaligen Anmeldens auf der UserVoice-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Sie können aber auch den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für UserVoice
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Szenario")

##Aktivieren der Anwendungsintegration für UserVoice
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für UserVoice aktivieren.

###So aktivieren Sie die Anwendungsintegration für UserVoice:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **UserVoice** ein.

    ![Anwendungskatalog](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **UserVoice** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei UserVoice zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für UserVoice müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI) \(in englischer Sprache\) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **UserVoice** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei UserVoice anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **UserVoice-Anmelde-URL** die URL im Format "*https://\\<Mandantenname\>.UserVoice.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-uservoice-tutorial/IC777517.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um UserVoice** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **C:\\UserVoice.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der UserVoice-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste oben die Option „Einstellungen“, und wählen Sie dann aus dem Menü die Option „Webportal“ aus.

    ![Einstellungen](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Einstellungen")

7.  Klicken Sie auf der Registerkarte **Webportal** des Abschnitts **Benutzerauthentifizierung** auf **Bearbeiten**, um die Dialogfeldseite **Benutzerauthentifizierung bearbeiten** zu öffnen.

    ![Webportal](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Webportal")

8.  Führen Sie auf der Dialogseite **Benutzerauthentifizierung bearbeiten** die folgenden Schritte aus:

    ![Benutzerauthentifizierung bearbeiten](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Benutzerauthentifizierung bearbeiten")

    1.  Klicken Sie auf **Einmaliges Anmelden \(SSO\)**.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren bei UserVoice** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Einmalige Remoteanmeldung** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren bei UserVoice** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Einmalige Remoteabmeldung** ein.
    4.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **SHA1-Fingerabdruck des aktuellen Zertifikats** ein.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI).

    5.  Klicken Sie auf **Authentifizierungseinstellungen speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei UserVoice anmelden können, müssen sie in UserVoice bereitgestellt werden. Im Fall von UserVoice ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit:

1.  Melden Sie sich bei Ihrem **UserVoice**-Mandanten an.

2.  Wechseln Sie zu **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Einstellungen")

3.  Klicken Sie auf **Allgemein**.

4.  Klicken Sie auf **Agents und Berechtigungen**.

    ![Agents und Berechtigungen](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agents und Berechtigungen")

5.  Klicken Sie auf** Administratoren hinzufügen**.

    ![Administratoren hinzufügen](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Administratoren hinzufügen")

6.  Führen Sie im Dialogfeld **Benutzer einladen** die folgenden Schritte aus:

    ![Administratoren einladen](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Administratoren einladen")

    1.  Geben Sie in das Textfeld „E-Mails“ die E-Mail-Adresse eines vorhandenen Azure AD-Kontos ein, das Sie bereitstellen möchten, und klicken Sie auf **Hinzufügen**.
    2.  Klicken Sie auf **Einladen**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von UserVoice-Benutzerkonten oder mithilfe der von UserVoice bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie UserVoice Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **UserVoice** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->