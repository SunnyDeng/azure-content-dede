<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Egnyte | Microsoft Azure" description="Hier erfahren Sie, wie Sie Egnyte mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Egnyte
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=528188), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Egnyte erläutert.
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Egnyte-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Egnyte zugewiesen haben, mittels einmaliger Anmeldung auf der Egnyte-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Egnyte
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Szenario")
##Aktivieren der Anwendungsintegration für Egnyte
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Egnyte aktivieren.

###So aktivieren Sie die Anwendungsintegration für Egnyte

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **egnyte** ein.

    ![Anwendungskatalog](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Egnyte** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Egnyte zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Egnyte** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Egnyte anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Egnyte-Anmelde-URL** die URL im Format „**https://company.egnyte.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787817.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Egnyte** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Egnyte-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Einstellungen")

7.  Klicken Sie im Menü auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Einstellungen")

8.  Klicken Sie auf die Registerkarte **Konfiguration** und anschließend auf **Sicherheit**.

    ![Sicherheit](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Sicherheit")

9.  Führen Sie im Abschnitt **SSO-Authentifizierung** die folgenden Schritte aus:

    ![SSO-Authentifizierung](./media/active-directory-saas-egnyte-tutorial/IC787822.png "SSO-Authentifizierung")

    1.  Wählen Sie für **SSO-Authentifizierung** die Option **SAML 2.0** aus.
    2.  Wählen Sie für **Identitätsanbieter** die Option **AzureAD** aus.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Egnyte** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Anmelde-URL des ID-Anbieters** ein.
4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Egnyte** den Wert der **Entitäts-ID**, und fügen Sie ihn in das Textfeld **Entitäts-ID des Identitätsanbieters** ein.
    5.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    6.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.
    7.  Wählen Sie für **Standardbenutzerzuordnung** die Option **E-Mail-Adresse** aus.
    8.  Wählen Sie für **Domänenspezifischen Ausstellerwert verwenden** die Einstellung **Deaktiviert** aus.
    9.  Klicken Sie auf **Speichern**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Egnyte anmelden können, müssen sie in Egnyte bereitgestellt werden. Im Fall von Egnyte ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei der **Egnyte**-Unternehmenswebsite als Administrator an.

2.  Navigieren Sie zu **Einstellungen > Benutzer/Gruppen**.

3.  Klicken Sie auf **Neuen Benutzer hinzufügen**, und wählen Sie dann den Benutzertyp aus, den Sie hinzufügen möchten.

    ![Benutzer](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Benutzer")

4.  Führen Sie im Abschnitt **Neuer Standardbenutzer** die folgenden Schritte aus:

    ![Neuer Standardbenutzer](./media/active-directory-saas-egnyte-tutorial/IC787825.png "Neuer Standardbenutzer")

    1.  Geben Sie die **E-Mail-Adresse**, den **Benutzernamen** und weitere Details eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

    >[AZURE.NOTE]Der Besitzer des Azure Active Directory-Kontos erhält eine Benachrichtigungs-E-Mail.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Egnyte-Benutzerkonten oder mithilfe der von Egnyte bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Egnyte Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Egnyte** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->