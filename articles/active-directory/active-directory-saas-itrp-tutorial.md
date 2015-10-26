<properties pageTitle="Tutorial: Azure Active Directory-Integration mit ITRP | Microsoft Azure" description="Hier erfahren Sie, wie Sie ITRP mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit ITRP
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=524329), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und ITRP erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen ITRP-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie ITRP zugewiesen haben, mittels einmaliger Anmeldung auf der ITRP-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ITRP
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-itrp-tutorial/IC775551.png "Szenario")
##Aktivieren der Anwendungsintegration für ITRP
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ITRP aktivieren.

###So aktivieren Sie die Anwendungsintegration für ITRP

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-itrp-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-itrp-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-itrp-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** **ITRP** ein.

    ![Anwendungskatalog](./media/active-directory-saas-itrp-tutorial/IC775565.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **ITRP** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ITRP zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für ITRP müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **ITRP** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-itrp-tutorial/IC771709.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei ITRP anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-itrp-tutorial/IC775567.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **ITRP-Anmelde-URL** die URL im Format „*https://\<Mandantenname>.ITRP.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-itrp-tutorial/IC775568.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für ITRP** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\ITRP.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-itrp-tutorial/IC775569.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der ITRP-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben auf der Symbolleiste auf **Einstellungen**.

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  Klicken Sie im linken Navigationsbereich auf **Einmaliges Anmelden**.

    ![Einmaliges Anmelden](./media/active-directory-saas-itrp-tutorial/IC775571.png "Einmaliges Anmelden")

8.  Führen Sie im Konfigurationsabschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-itrp-tutorial/IC775572.png "Einmaliges Anmelden")

    ![Einmaliges Anmelden](./media/active-directory-saas-itrp-tutorial/IC775573.png "Einmaliges Anmelden")

    1.  Klicken Sie auf **Aktivieren**.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für ITRP** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Remoteabmelde-URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für ITRP** den Wert der **SAML-SSO-URL**, und fügen Sie ihn in das Textfeld **SAML-SSO-URL** ein.
    4.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Fingerabdruck des Zertifikats** ein.
        
		>[AZURE.TIP]Weitere Informationen finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI).

    5.  Klicken Sie auf **Speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-itrp-tutorial/IC775574.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei ITRP anmelden können, müssen sie in ITRP bereitgestellt werden. Im Fall von ITRP ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **ITRP**-Mandanten an.

2.  Klicken Sie oben auf der Symbolleiste auf **Einträge**.

    ![Admin](./media/active-directory-saas-itrp-tutorial/IC775575.png "Admin")

3.  Wählen Sie im Popupmenü die Option **Personen** aus.

    ![Personen](./media/active-directory-saas-itrp-tutorial/IC775587.png "Personen")

4.  Klicken Sie auf **Neue Person hinzufügen** (+).

    ![Admin](./media/active-directory-saas-itrp-tutorial/IC775576.png "Admin")

5.  Führen Sie im Dialogfeld „Neue Person hinzufügen“ die folgenden Schritte aus:

    ![Benutzer](./media/active-directory-saas-itrp-tutorial/IC775577.png "Benutzer")

    1.  Geben Sie den **Namen** und die **E-Mail-Adresse** eines gültigen AAD-Kontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von ITRP-Benutzerkonten oder mithilfe der von ITRP bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ITRP Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **ITRP** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-itrp-tutorial/IC775588.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-itrp-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->