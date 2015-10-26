<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Freshdesk | Microsoft Azure" description="Hier erfahren Sie, wie Sie Freshdesk mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Freshdesk
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=524323), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Freshdesk erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Freshdesk-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Freshdesk zugewiesen haben, mittels einmaliger Anmeldung auf der Freshdesk-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Freshdesk
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Szenario")
##Aktivieren der Anwendungsintegration für Freshdesk
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Freshdesk aktivieren.

###So aktivieren Sie die Anwendungsintegration für Freshdesk

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Freshdesk** ein.

    ![Anwendungskatalog](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Freshdesk** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Freshdesk zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für Freshdesk müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Freshdesk** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Freshdesk anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Freshdesk-Anmelde-URL** die URL im Format „*https://\<Mandantenname>.Freshdesk.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Freshdesk** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Freshdesk.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Freshdesk-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben im Menü auf **Administrator**.

    ![Admin](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")

7.  Klicken Sie auf der Registerkarte **Allgemeine Einstellungen** auf **Sicherheit**.

    ![Sicherheit](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Sicherheit")

8.  Führen Sie im Abschnitt **Sicherheit** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Einmaliges Anmelden")

    1.  Wählen Sie für **Einmaliges Anmelden (SSO)** die Einstellung **Ein** aus.
    2.  Wählen Sie **SAML-SSO** aus.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Freshdesk** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **SAML-Anmelde-URL** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Freshdesk** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Abmelde-URL** ein.
    5.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Fingerabdruck des Sicherheitszertifikats** ein.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI).

    6.  Klicken Sie auf **Speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Freshdesk anmelden können, müssen sie in Freshdesk bereitgestellt werden. Im Fall von Freshdesk ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem**Freshdesk**-Mandanten an.

2.  Klicken Sie oben im Menü auf **Administrator**.

    ![Admin](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")

3.  Klicken Sie auf der Registerkarte **Allgemeine Einstellungen** auf **Agents**.

    ![Agents](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4.  Klicken Sie auf **Neuer Agent**.

    ![Neuer Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Neuer Agent")

5.  Führen Sie im Dialogfeld „Agent-Informationen“ die folgenden Schritte aus:

    ![Agent-Informationen](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent-Informationen")

    1.  Geben Sie im Textfeld **Vollständiger Name** den Namen des Azure AD-Kontos ein, das Sie bereitstellen möchten.
    2.  Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse des Azure AD-Kontos ein, das Sie bereitstellen möchten.
    3.  Geben Sie im Textfeld **Titel** den Titel des Azure AD-Kontos ein, das Sie bereitstellen möchten.
    4.  Wählen Sie **Agent-Funktion** aus, und klicken Sie dann auf **Zuweisen**.
    5.  Klicken Sie auf **Speichern**.
    
        >[AZURE.NOTE]Der Besitzer des Azure AD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Freshdesk-Benutzerkonten oder mithilfe der von Freshdesk bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Freshdesk Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Freshdesk** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->