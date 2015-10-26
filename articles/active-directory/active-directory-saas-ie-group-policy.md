<properties
   pageTitle="How to Deploy the Access Panel Extension for Internet Explorer using Group Policy (Bereitstellen der Zugriffsbereichserweiterung für Internet Explorer mit der Gruppenrichtlinie; in englischer Sprache) | Microsoft Azure"
   description="So stellen Sie das Internet Explorer-Add-On für das Portal „Meine Apps“ mithilfe von Gruppenrichtlinien bereit"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="stevenpo"
   editor=""/>
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/28/2015"
   ms.author="liviodlc"/>

#How to Deploy the Access Panel Extension for Internet Explorer using Group Policy (Bereitstellen der Zugriffsbereichserweiterung für Internet Explorer mit der Gruppenrichtlinie; in englischer Sprache)

In diesem Tutorial wird erläutert, wie Sie mithilfe von Gruppenrichtlinien die Zugriffsbereichserweiterung für Internet Explorer per Remotezugriff auf den Computern Ihrer Benutzer installieren. Diese Erweiterung ist für Benutzer von Internet Explorer erforderlich, die sich bei Apps anmelden müssen, die mit der [kennwortbasierten einmaligen Anmeldung](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) konfiguriert wurden.

Administratoren wird empfohlen, die Bereitstellung dieser Erweiterung zu automatisieren. Andernfalls müssen Benutzer die Erweiterung selbst herunterladen und installieren. Diese Vorgehensweise ist jedoch fehleranfällig; zudem sind Administratorberechtigungen erforderlich. In diesem Tutorial wird eine Methode zum Automatisieren von Softwarebereitstellungen mithilfe von Gruppenrichtlinien erläutert. [Weitere Informationen zu Gruppenrichtlinien](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Die Zugriffsbereichserweiterung ist auch für [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) und [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998) verfügbar. Für diese Browser sind keine Administratorberechtigungen erforderlich.

##Voraussetzungen

- Sie haben [Active Directory-Domänendienste](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) eingerichtet und die Computer Ihrer Benutzer Ihrer Domäne hinzugefügt.
- Zum Bearbeiten von Gruppenrichtlinienobjekten benötigen Sie die Berechtigung „Einstellungen bearbeiten“. Standardmäßig verfügen Mitglieder der folgenden Sicherheitsgruppen über diese Berechtigung: Domänenadministratoren, Organisationsadministratoren und Richtlinien-Ersteller-Besitzer. [Weitere Informationen.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##Schritt 1: Erstellen des Verteilungspunkts

Zunächst müssen Sie das Installationspaket an einem Speicherort im Netzwerk ablegen, auf den von allen Computern aus zugegriffen werden kann, auf denen Sie die Erweiterung per Remotezugriff installieren möchten. Gehen Sie dazu folgendermaßen vor:

1. Melden Sie sich beim Server als Administrator an.

2. Wechseln Sie im Fenster **Server-Manager** zu **Dateien und Speicherdienste**.

	![Dateien und Speicherdienste öffnen](./media/active-directory-saas-ie-group-policy/files-services.png)

3. Klicken Sie auf die Registerkarte **Freigaben**. Klicken Sie auf **Aufgaben** > **Neue Freigabe...**.

	![Dateien und Speicherdienste öffnen](./media/active-directory-saas-ie-group-policy/shares.png)

4. Schließen Sie den **Assistenten für neue Freigaben**, und legen Sie Berechtigungen fest, um sicherzustellen, dass von den Computern Ihrer Benutzer aus darauf zugegriffen werden kann. [Weitere Informationen zu Freigaben](https://technet.microsoft.com/library/cc753175.aspx)

5. Laden Sie das folgende Microsoft Windows Installer-Paket (MSI-Datei) herunter: [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi).

6. Kopieren Sie das Installationspaket an den gewünschten Speicherort auf der Freigabe.

	![Kopieren Sie die MSI-Datei auf Ihre Freigabe.](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. Stellen Sie sicher, dass von den Clientcomputern aus auf das Installationspaket auf der Freigabe zugegriffen werden kann.

##Schritt 2: Erstellen des Gruppenrichtlinienobjekts

1. Melden Sie sich bei dem Server mit den Active Directory-Domänendiensten (Active Directory Domain Services, AD DS) an.

2. Navigieren Sie im Server-Manager zu **Extras** > **Gruppenrichtlinienverwaltung**.

	![Klicken Sie auf „Extras“ > „Gruppenrichtlinienverwaltung“.](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. Zeigen Sie im linken Bereich des Fensters **Gruppenrichtlinienverwaltung** die Hierarchie Ihrer Organisationseinheiten (OE) an, und legen Sie fest, in welchem Umfang die Gruppenrichtlinie angewendet werden soll. Sie können beispielsweise eine kleine Organisationseinheit auswählen, die einigen wenigen Benutzern zu Testzwecken bereitgestellt wird, oder eine Organisationseinheit der obersten Ebene festlegen, die im gesamten Unternehmen bereitgestellt wird.

	> [AZURE.NOTE]Wenn Sie Organisationseinheiten erstellen oder bearbeiten möchten, wechseln Sie zurück zum Server-Manager, und rufen Sie **Extras** > **Active Directory-Benutzer und -Computer** auf.

4. Nachdem Sie eine Organisationseinheit ausgewählt haben, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Gruppenrichtlinienobjekt hier erstellen und verknüpfen...**.

	![Neues Gruppenrichtlinienobjekt erstellen](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. Geben Sie in der Eingabeaufforderung **Neues Gruppenrichtlinienobjekt** einen Namen für das neue Gruppenrichtlinienobjekt ein.

	![Neues Gruppenrichtlinienobjekt benennen](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. Klicken Sie mit der rechten Maustaste auf das Gruppenrichtlinienobjekt, das Sie gerade erstellt haben, und wählen Sie **Bearbeiten**.

	![Neues Gruppenrichtlinienobjekt bearbeiten](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##Schritt 3: Zuweisen des Installationspakets

1. Legen Sie fest, ob Sie die Erweiterung basierend auf der **Computerkonfiguration** oder basierend auf der **Benutzerkonfiguration** bereitstellen möchten. Bei Verwendung der [Computerkonfiguration](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx) wird die Erweiterung auf dem Computer installiert, und zwar unabhängig davon, welche Benutzer sich bei ihm anmelden. Im Gegensatz dazu wird bei Verwendung von [Benutzerkonfiguration](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx) die Erweiterung für die Benutzer installiert, und zwar unabhängig davon, bei welchem Computer sie sich anmelden.

2. Folgen Sie abhängig vom gewählten Konfigurationstyp im linken Bereich des Fensters **Gruppenrichtlinienverwaltungs-Editor** einem der folgenden Ordnerpfade:
	- `Computer Configuration/Policies/Software Settings/`
	- `User Configuration/Policies/Software Settings/`

3. Klicken Sie mit der rechten Maustaste auf **Softwareinstallation**, und wählen Sie dann **Neu** > **Paket...**.

	![Neues Paket für die Softwareinstallation erstellen](./media/active-directory-saas-ie-group-policy/new-package.png)

4. Klicken Sie auf den freigegebenen Ordner mit dem Installationspaket aus [Schritt 1: Erstellen des Verteilungspunkts](#step-1-create-the-distribution-point), markieren Sie die MSI-Datei, und klicken Sie auf **Öffnen**.

	> [AZURE.IMPORTANT]Wenn sich die Freigabe auf demselben Server befindet, vergewissern Sie sich, dass Sie nicht über den lokalen Dateipfad, sondern über den Netzwerkdateipfad auf die MSI-Datei zugreifen.

	![Wählen Sie das Installationspaket im freigegebenen Ordner.](./media/active-directory-saas-ie-group-policy/select-package.png)

5. Wählen Sie in der Eingabeaufforderung **Software bereitstellen** als Bereitstellungsmethode die Option **Zugewiesen**. Klicken Sie dann auf **OK**.

	![Wählen Sie „Zugewiesen“, und klicken Sie dann auf „OK“.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

Die Erweiterung wird nun für die ausgewählte Organisationseinheit bereitgestellt. [Weitere Informationen zur Gruppenrichtlinien-Softwareinstallation](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##Schritt 4: Automatisches Aktivieren der Erweiterung für Internet Explorer 

Zusätzlich zur Ausführung des Installationsprogramms muss jede Erweiterung für Internet Explorer vor ihrer Verwendung explizit aktiviert werden. Gehen Sie folgendermaßen vor, um die Zugriffsbereichserweiterung mithilfe von Gruppenrichtlinien zu aktivieren:

1. Navigieren Sie abhängig vom Konfigurationstyp, den Sie in [Schritt 3: Zuweisen des Installationspakets](#step-3-assign-the-installation-package) gewählt haben, im linken Bereich des Fensters **Gruppenrichtlinienverwaltungs-Editor** zu einem der folgenden Speicherorte:
	- `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
	- `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. Klicken Sie mit der rechten Maustaste auf **Add-On-Liste**, und wählen Sie **Bearbeiten**. ![Bearbeiten Sie die Add-On-Liste.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. Wählen Sie im Fenster **Add-On-Liste** die Option **Aktiviert**. Klicken Sie unter dem Abschnitt **Optionen** auf **Anzeigen...**.

	![Klicken Sie auf „Aktivieren“ und anschließend auf „Anzeigen...“.](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. Führen Sie im Fenster **Inhalt anzeigen** die folgenden Schritte aus:

	1. Kopieren Sie für die erste Spalte (Feld **Wertname**) die folgende Klassen-ID: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

	2. Geben Sie für die zweite Spalte (Feld **Wert**) den folgenden Wert ein: `1`

	3. Klicken Sie auf **OK**, um das Fenster **Inhalt anzeigen** zu schließen.

	![Füllen Sie die Werte wie oben angegeben aus.](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. Klicken Sie auf **OK**, um die Änderungen zu übernehmen, und schließen Sie das Fenster **Add-On-Liste**.

Die Erweiterung sollte jetzt für die Computer in der ausgewählten Organisationseinheit aktiviert sein. [Erfahren Sie mehr über die Verwendung von Gruppenrichtlinien zum Aktivieren oder Deaktivieren von Add-Ons für Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

##Schritt 5: Testen der Bereitstellung

Gehen Sie folgendermaßen vor, um zu überprüfen, ob die Erweiterungsbereitstellung erfolgreich war:

1. Wenn Sie für die Bereitstellung die Option **Computerkonfiguration** verwendet haben, melden Sie sich bei einem Clientcomputer an, der zu der in [Schritt 2: Erstellen des Gruppenrichtlinienobjekts](#step-2-create-the-group-policy-object) ausgewählten Organisationseinheit gehört. Falls die Bereitstellung mit **Benutzerkonfiguration** erfolgt ist, melden Sie sich als Benutzer an, der zu dieser Organisationseinheit gehört.

2. Möglicherweise werden die Gruppenrichtlinienänderungen auf dem Computer erst durch mehrmaliges Anmelden vollständig aktualisiert. Öffnen Sie zum Erzwingen der Aktualisierung ein Fenster für die **Eingabeaufforderung**, und führen Sie den folgenden Befehl aus: `gpupdate /force`

3. Der Computer muss neu gestartet werden, damit die Installation ausgeführt werden kann. Das Starten kann während der Installation der Erweiterung erheblich länger dauern als gewöhnlich.

4. Öffnen Sie nach dem Neustart **Internet Explorer**. Klicken Sie in der oberen rechten Ecke des Fensters auf **Extras** (das Zahnradsymbol), und wählen Sie dann **Add-Ons verwalten**.

	![Klicken Sie auf „Extras“ > „Add-Ons verwalten“.](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. Überprüfen Sie im Fenster **Add-Ons verwalten**, ob die **Zugriffsbereichserweiterung** installiert und ihr **Status** auf **Aktiviert** festgelegt wurde.

	![Stellen Sie sicher, dass die Zugriffsbereichserweiterung installiert und aktiviert wurde.](./media/active-directory-saas-ie-group-policy/verify-install.png)

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!---HONumber=Oct15_HO3-->