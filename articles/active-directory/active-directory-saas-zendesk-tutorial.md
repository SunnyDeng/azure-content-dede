<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Zendesk | Microsoft Azure" description="Hier erfahren Sie, wie Sie Zendesk mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Zendesk
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=522569), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Zendesk erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Zendesk-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Zendesk zugewiesen haben, mittels einmaliger Anmeldung auf der Zendesk-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Zendesk
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Szenario")

##Aktivieren der Anwendungsintegration für Zendesk
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zendesk aktivieren.

###So aktivieren Sie die Anwendungsintegration für Zendesk

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** **Zendesk** ein.

    ![Anwendungskatalog](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Zendesk** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zendesk zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für Zendesk müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Zendesk** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Einmaliges Anmelden")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Zendesk anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Zendesk-Anmelde-URL** die URL im Format „*https://\<Mandantenname>.Zendesk.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-zendesk-tutorial/IC773088.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Zendesk** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Zendesk.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Zendesk-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Administrator**.

7.  Klicken Sie im linken Navigationsbereich auf **Einstellungen**, und klicken Sie dann auf **Sicherheit**.

    ![Sicherheit](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Sicherheit")

8.  Wählen Sie auf der Seite **Sicherheit** die Registerkarte **Administratoren und Agents**.

9.  Wählen Sie **Einmaliges Anmelden (SSO) und SAML**, und wählen Sie dann **SAML**.

10. Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Zendesk** den Wert der **SAML-SSO-URL**, und fügen Sie ihn in das Textfeld **SAML-SSO-URL** ein.

11. Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Zendesk** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Remoteabmelde-URL** ein.

    ![Einmaliges Anmelden](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Einmaliges Anmelden")

12. Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Fingerabdruck des Zertifikats** ein.

	>[AZURE.TIP]Weitere Informationen finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI).

13. Klicken Sie auf **Speichern**.

14. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei **Zendesk** anmelden können, müssen sie in **Zendesk** bereitgestellt werden. Im Fall von **Zendesk** ist die Bereitstellung eine manuelle Aufgabe.

###Führen Sie zum Bereitstellen von Benutzerkonten in Zendesk die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrem**Zendesk**-Mandanten an.

2.  Wählen Sie die Registerkarte **Kundenliste**.

3.  Wählen Sie die Registerkarte **Benutzer**, und klicken Sie auf **Hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Benutzer hinzufügen")

4.  Geben Sie die E-Mail-Adresse eines vorhandenen Azure AD-Kontos ein, das Sie bereitstellen möchten, und klicken Sie auf **Speichern**.

    ![Neuer Benutzer](./media/active-directory-saas-zendesk-tutorial/IC773633.png "Neuer Benutzer")

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Zendesk-Benutzerkonten oder mithilfe der von Zendesk bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Zendesk Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Zendesk** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->