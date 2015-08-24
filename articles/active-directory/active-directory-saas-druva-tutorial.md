<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Druva | Microsoft Azure" description="Hier erfahren Sie, wie Sie Druva mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Druva
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=534089), um Feedback abzugeben.

In diesem Tutorial wird die Integration von Azure und Druva erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Druva-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Druva zugewiesen haben, mittels einmaliger Anmeldung auf der Druva-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Druva
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-druva-tutorial/IC795084.png "Szenario")
##Aktivieren der Anwendungsintegration für Druva

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Druva aktivieren.

###So aktivieren Sie die Anwendungsintegration für Druva

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-druva-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-druva-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-druva-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Druva** ein.

    ![Anwendungskatalog](./media/active-directory-saas-druva-tutorial/IC795085.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Druva** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Druva zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

Die Druva-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der **SAML-Tokenattribute** benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen.

![SAML-Tokenattribute](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML-Tokenattribute")

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure Active Directory-Portal auf der Anwendungsintegrationsseite für **Druva** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-druva-tutorial/IC795027.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Druva anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-druva-tutorial/IC795088.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Druva-Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei Druva verwenden (z. B. „**https://cloud.druva.com/home/*”), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-druva-tutorial/IC795089.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Druva** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-druva-tutorial/IC795090.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Druva-Unternehmenswebsite als Administrator an.

6.  Navigieren Sie zu **Verwalten > Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-druva-tutorial/IC795091.png "Einstellungen")

7.  Führen Sie im Dialogfeld „Einstellungen für einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-druva-tutorial/IC795092.png "Einstellungen für einmaliges Anmelden")

    1.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Druva** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Anmelde-URL des ID-Anbieters** ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Druva** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Abmelde-URL des ID-Anbieters** ein.
    3.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    4.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des ID-Anbieters** ein.
    5.  Klicken Sie zum Öffnen der Seite **Einstellungen** auf **Speichern**.

8.  Klicken Sie auf der Seite **Einstellungen** auf**SSO-Token generieren**.

    ![Einstellungen](./media/active-directory-saas-druva-tutorial/IC795093.png "Einstellungen")

9.  Führen Sie im Dialogfeld **Token für einmaliges Anmelden** die folgenden Schritte aus:

    ![SSO-Token](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO-Token")

    1.  Klicken Sie auf **Kopieren**.
    2.  Klicken Sie auf **Schließen**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-druva-tutorial/IC795095.png "Einmaliges Anmelden konfigurieren")

11. Klicken Sie oben im Menü auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen.

    ![Attribute](./media/active-directory-saas-druva-tutorial/IC795096.png "Attribute")

12. Führen Sie zum Hinzufügen der erforderlichen Attributzuordnungen die folgenden Schritte aus:

	|Attributname|Attributwert|
    |---|---|
    |insync\_auth\_token|<*Wert in der Zwischenablage*>|

    1.  Klicken Sie für jede Datenzeile in der obigen Tabelle auf **Benutzerattribut hinzufügen**.
    2.  Geben Sie im Textfeld **Attributname** den für die Zeile angezeigten Attributnamen ein.
    3.  Geben Sie im Textfeld **Attributwert** den für die Zeile angezeigten Attributwert ein.
    4.  Klicken Sie auf **Fertig stellen**.

13. Klicken Sie auf **Änderungen übernehmen**.
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Druva anmelden können, müssen sie in Druva bereitgestellt werden. Im Fall von Druva ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der **Druva**-Unternehmenswebsite als Administrator an.

2.  Navigieren Sie zu **Verwalten > Benutzer**.

    ![Benutzer verwalten](./media/active-directory-saas-druva-tutorial/IC795097.png "Benutzer verwalten")

3.  Klicken Sie auf **Neu erstellen**.

    ![Benutzer verwalten](./media/active-directory-saas-druva-tutorial/IC795098.png "Benutzer verwalten")

4.  Führen Sie im Dialogfeld „Neuen Benutzer erstellen“ die folgenden Schritte aus:

    ![Neuen Benutzer erstellen](./media/active-directory-saas-druva-tutorial/IC795099.png "Neuen Benutzer erstellen")

    1.  Geben Sie die E-Mail-Adresse und den Namen eines gültigen Azure Active Directory-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Benutzer erstellen**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Druva-Benutzerkonten oder mithilfe der von Druva bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Druva Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Druva** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-druva-tutorial/IC795100.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-druva-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->