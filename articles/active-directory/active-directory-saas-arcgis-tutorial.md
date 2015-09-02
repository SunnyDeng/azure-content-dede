<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit ArcGIS | Microsoft Azure" description="Erfahren Sie, wie Sie ArcGIS mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit ArcGIS
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=526915), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und ArcGIS erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein ArcGIS-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie ArcGIS zugewiesen haben, mittels einmaligen Anmeldens auf Ihrer ArcGIS-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Sie können aber auch den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ArcGIS
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Szenario")
##Aktivieren der Anwendungsintegration für ArcGIS

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ArcGIS aktivieren.

###So aktivieren Sie die Anwendungsintegration für ArcGIS

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **ArcGIS** ein.

    ![Anwendungskatalog](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **ArcGIS** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ArcGIS zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **ArcGIS** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei ArcGIS anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **ArcGIS-Anmelde-URL** die von Ihren Benutzern verwendete URL im Format "*https://company.maps.arcgis.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784740.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um ArcGIS** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der ArcGIS-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Edit Settings**.

    ![Einstellungen bearbeiten](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Einstellungen bearbeiten")

7.  Klicken Sie auf **Security**.

    ![Sicherheit](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Sicherheit")

8.  Klicken Sie unter **Enterprise Logins** auf **Set Identity Provider**.

    ![Enterprise Logins](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise Logins")

9.  Führen Sie auf der Konfigurationsseite **Set Identity Provider** die folgenden Schritte aus.

    ![Set Identity Provider](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Set Identity Provider")

    1.  Geben Sie in das Textfeld "Name" den Namen Ihrer Organisation ein.
    2.  Wählen Sie für **Metadata for the Enterprise Identity Provider will be supplied using** die Option **A File** aus.
    3.  Klicken Sie auf **Datei auswählen**, um die heruntergeladene Metadatendatei hochzuladen.
    4.  Klicken Sie auf **Set Identity Provider**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei ArcGIS anmelden können, müssen sie in ArcGIS bereitgestellt werden.  
Im Fall von ArcGIS ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **ArcGIS**-Mandanten an.

2.  Klicken Sie auf **Mitglieder einladen**.

    ![Invite Members](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invite Members")

3.  Wählen Sie **Mitglieder automatisch hinzufügen, ohne eine E-Mail zu senden** aus, und klicken Sie dann auf **Weiter**.

    ![Add Members Automatically](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Add Members Automatically")

4.  Führen Sie auf der Dialogfeldseite **Mitglieder** die folgenden Schritte aus:

    ![Add and review](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Add and review")

    1.  Geben Sie den **Vornamen**, den **Nachnamen** und die **E-Mail-Adresse** eines gültigen AAD-Benutzerkontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf**Add And Review**.

5.  Überprüfen Sie die eingegebenen Daten, und klicken Sie dann auf **Mitglieder hinzufügen**.

    ![Add member](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Add member")

>[AZURE.NOTE] Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von ArcGIS-Benutzerkonten oder mithilfe der von ArcGIS bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ArcGIS Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **ArcGIS** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!------HONumber=August15_HO7-->