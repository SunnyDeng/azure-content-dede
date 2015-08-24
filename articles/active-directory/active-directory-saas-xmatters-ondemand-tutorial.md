<properties pageTitle="Tutorial: Azure Active Directory-Integration mit xMatters OnDemand | Microsoft Azure" description="Hier erfahren Sie, wie Sie xMatters OnDemand mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit xMatters OnDemand
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=524330), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und xMatters OnDemand erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen xMatters OnDemand-Mandanten
  
Nach Abschluss dieses Tutorials können sich die xMatters OnDemand zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer xMatters OnDemand-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können Sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für xMatters OnDemand
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Szenario")

##Aktivieren der Anwendungsintegration für xMatters OnDemand
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für xMatters OnDemand aktivieren.

###So aktivieren Sie die Anwendungsintegration für xMatters OnDemand

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** **xMatters OnDemand** ein.

    ![Anwendungskatalog](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **xMatters OnDemand** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei xMatters OnDemand zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **xMatters OnDemand** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei xMatters OnDemand anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **xMatters OnDemand-Anmelde-URL** die URL im Format „*https://<Mandantenname>.xMatters OnDemandapp.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für xMatters OnDemand** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal als: **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT]Sie müssen das Zertifikat an das xMatters-Supportteam weiterleiten. Das Zertifikat muss vom xMatters-Supportteam hochgeladen werden, bevor Sie die Konfiguration der einmaligen Anmeldung abschließen können.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der xMatters OnDemand-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste oben auf **Administrator**, und klicken Sie dann in der Navigationsleiste auf der linken Seite auf **Unternehmensdetails**.

    ![Admin](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7.  Führen Sie auf der Seite **SAML-Konfiguration** die folgenden Schritte aus:

    ![SAML-Konfiguration](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML-Konfiguration")

    1.  Wählen Sie **SAML aktivieren**.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für xMatters OnDemand** den Wert der **Identitätsanbieter-ID**, und fügen Sie ihn in das Textfeld **Identitätsanbieter-ID** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für xMatters OnDemand** den Wert für **Dienst-URL für einmaliges Anmelden**, und fügen Sie ihn in das Textfeld **URL für einmaliges Anmelden** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für xMatters OnDemand** den Wert für **Dienst-URL für einmaliges Abmelden**, und fügen Sie ihn in das Textfeld **URL für einmaliges Abmelden** ein.
    5.  Klicken Sie auf der Seite „Unternehmensdetails“ oben auf **Speichern**. ![Unternehmensdetails](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Unternehmensdetails")

8.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Um Azure AD-Benutzern die Anmeldung bei xMatters OnDemand zu ermöglichen, müssen sie in xMatters OnDemand bereitgestellt werden. Im Fall von xMatters OnDemand ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **XMatters OnDemand**-Mandanten an.

2.  Klicken Sie auf die Registerkarte **Benutzer**.

3.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Benutzer](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Benutzer")

4.  Wählen Sie **Aktiv**.

5.  Führen Sie im Abschnitt **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Benutzer hinzufügen")

    1.  Geben Sie die **Benutzer-ID**, den **Vornamen**, den **Nachnamen** und die **Website** eines gültigen AAD-Kontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von xMatters OnDemand-Benutzerkonten oder mithilfe der von xMatters OnDemand bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie xMatters OnDemand Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **xMatters OnDemand** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->