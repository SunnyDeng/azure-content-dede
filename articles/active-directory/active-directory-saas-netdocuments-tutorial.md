<properties pageTitle="Tutorial: Azure Active Directory-Integration mit NetDocuments | Microsoft Azure" description="Erfahren Sie, wie Sie NetDocuments mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit NetDocuments
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=529696), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und NetDocuments erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen NetDocuments-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie NetDocuments zugewiesen haben, mittels einmaliger Anmeldung auf der NetDocuments-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Alternativ können sie den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für NetDocuments
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Szenario")
##Aktivieren der Anwendungsintegration für NetDocuments
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für NetDocuments aktivieren.

###So aktivieren Sie die Anwendungsintegration für NetDocuments

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **NetDocuments** ein.

    ![Anwendungskatalog](./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **NetDocuments** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![NetDocuments](./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei NetDocuments zu authentifizieren.
Zum Konfigurieren des einmaligen Anmeldens für NetDocuments müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.
Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **NetDocuments** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei NetDocuments anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795045.png "App-URL konfigurieren")

    1.  Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei der NetDocuments-Anwendung verwenden (z. B. "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*").
    2.  Geben Sie im Textfeld **NetDocuments-Antwort-URL** den gleichen Wert wie in das Textfeld **Anmelde-URL** ein.  

        >[AZURE.NOTE]Den richtigen Wert finden Sie am Ende des Dialogfelds **Identitätsverbund** \(siehe Screenshot für Schritt 9\).

    3.  Klicken Sie auf **Weiter**.

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für NetDocuments** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der NetDocuments-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Administrator**.

7.  Klicken Sie auf **Benutzer und Gruppen hinzufügen und entfernen**.

    ![Repository](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repository")

8.  Klicken Sie auf **Erweiterte Authentifizierungsoptionen konfigurieren**.

    ![Erweiterte Authentifizierungsoptionen konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Erweiterte Authentifizierungsoptionen konfigurieren")

9.  Führen Sie im Dialogfeld **Identitätsverbund** die folgenden Schritte aus:

    ![Identitätsverbund](./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Identitätsverbund")

    1.  Wählen Sie als **Servertyp für Identitätsverbund** die Option **Active Directory-Verbunddienste** aus.
    2.  Klicken Sie auf **Datei auswählen**, um die heruntergeladene Metadatendatei hochzuladen.
    3.  Klicken Sie auf **OK**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei NetDocuments anmelden können, müssen sie in NetDocuments bereitgestellt werden. Im Fall von NetDocuments ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrer **NetDocuments**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie oben im Menü auf **Administrator**.

    ![Admin](./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Admin")

3.  Klicken Sie auf **Benutzer und Gruppen hinzufügen und entfernen**.

    ![Repository](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repository")

4.  Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten, und klicken Sie auf **Benutzer hinzufügen**.

    ![E-Mail-Adresse](./media/active-directory-saas-netdocuments-tutorial/IC795053.png "E-Mail-Adresse")

    >[AZURE.NOTE]Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von NetDocuments-Benutzerkonten oder mithilfe der von NetDocuments bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie NetDocuments Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **NetDocuments** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Ja")
  
Wenn Sie die Einstellungen für einmaliges Anmelden testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!-------HONumber=August15_HO7-->