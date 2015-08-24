<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Mimecast Personal Portal | Microsoft Azure" description="Erfahren Sie, wie Sie Mimecast Personal Portal mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Mimecast Personal Portal
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=529832), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Mimecast Personal Portal erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Mimecast Personal Portal-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Mimecast Personal Portal zugewiesen haben, mittels einmaliger Anmeldung auf der Mimecast Personal Portal-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Alternativ können Sie den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Mimecast Personal Portal
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Szenario")
##Aktivieren der Anwendungsintegration für Mimecast Personal Portal
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Mimecast Personal Portal aktivieren.

###Führen Sie zum Aktivieren der Anwendungsintegration für Mimecast Personal Portal die folgenden Schritte aus:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Begriff **Mimecast Personal Portal** ein.

    ![Anwendungskatalog](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Mimecast Personal Portal** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Mimecast Personal Portal")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Mimecast Personal Portal zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Mimecast Personal Portal** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Mimecast Personal Portal anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **Mimecast Personal Portal-Anmelde-URL** die von den Benutzern für die Anmeldung bei Mimecast Personal Portal verwendete URL ein \(z. B. „https://webmail-uk.mimecast.com“ oder „https://webmail-us.mimecast.com“\), und klicken Sie dann auf **Weiter**.

    >[AZURE.NOTE]Die Anmelde-URL ist regionsspezifisch.

    ![App-URL konfigurieren](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Mimecast Personal Portal** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei Mimecast Personal Portal als Administrator an.

6.  Wechseln Sie zu **Dienste \> Anwendung**.

    ![Anwendungen](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Anwendungen")

7.  Klicken Sie auf **Authentifizierungsprofile**.

    ![Authentifizierungsprofile](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Authentifizierungsprofile")

8.  Klicken Sie auf **Neues Authentifizierungsprofil**.

    ![Neues Authentifizierungsprofil](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "Neues Authentifizierungsprofil")

9.  Führen Sie im Abschnitt **Authentifizierungsprofil** die folgenden Schritte aus:

    ![Authentifizierungsprofil](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Authentifizierungsprofil")

    1.  Geben Sie im Textfeld **Beschreibung** einen Namen für die Konfiguration ein.
    2.  Wählen Sie **SAML-Authentifizierung für Mimecast Personal Portal aktivieren** aus.
    3.  Wählen Sie für **Anbieter** die Option **Azure Active Directory** aus.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Mimecast Personal Portal** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Aussteller-URL** ein.
    5.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Mimecast Personal Portal** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Anmelde-URL** ein.
    6.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Mimecast Personal Portal** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Abmelde-URL** ein.  

        >[AZURE.NOTE]Der Wert für Anmelde-URL und Abmelde-URL sind bei Mimecast Personal Portal gleich.

    7.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    8.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, entfernen Sie die erste Zeile \(*--*\) und die letzte Zeile \(*--*\), und kopieren Sie den übrigen Inhalt in die Zwischenablage. Fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters \(Metadaten\)** ein.
    9.  Wählen Sie **Einmaliges Anmelden zulassen**.
    10. Klicken Sie auf **Speichern**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Mimecast Personal Portal anmelden können, müssen Sie in Mimecast Personal Portal bereitgestellt werden. Im Fall von Mimecast Personal Portal ist die Bereitstellung eine manuelle Aufgabe.
  
Sie müssen eine Domäne registrieren, bevor Sie Benutzer erstellen können.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei **Mimecast Personal Portal** als Administrator an.

2.  Wechseln Sie zu **Verzeichnisse \> Intern**.

    ![Verzeichnisse](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Verzeichnisse")

3.  Klicken Sie auf **Neue Domäne registrieren**.

    ![Neue Domäne registrieren](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Neue Domäne registrieren")

4.  Nachdem die neue Domäne erstellt wurde, klicken Sie auf **Neue Adresse**.

    ![Neue Adresse](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "Neue Adresse")

5.  Führen Sie im Dialogfeld für die neue Adresse die folgenden Schritte aus:

    ![Speichern](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Speichern")

    1.  Geben Sie die Attribute **E-Mail-Adresse**, **Globaler Name**, **Kennwort** und **Kennwort bestätigen** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Mimecast Personal Portal-Benutzerkonten oder mithilfe der von Mimecast Personal Portal bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Mimecast Personal Portal Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Mimecast Personal Portal** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->