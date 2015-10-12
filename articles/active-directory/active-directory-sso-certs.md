<properties
	pageTitle="Verwalten von Verbundzertifikaten in Azure AD | Microsoft Azure"
	description="Erfahren Sie, wie Sie das Ablaufdatum für Verbundzertifikate anpassen und Zertifikate erneuern, die in Kürze ablaufen."
	services="active-directory"
	documentationCenter=""
	authors="liviodlc"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/29/2015"
	ms.author="liviodlc"/>

#Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory

Dieser Artikel enthält häufige gestellte Fragen im Zusammenhang mit den Zertifikaten, die Azure Active Directory erstellt, um die einmalige Verbundanmeldung für Ihre SaaS-Anwendungen einzurichten.

Dieser Artikel ist nur relevant für Apps, die für **Azure AD – einmaliges Anmelden** konfiguriert sind, wie im folgenden Beispiel gezeigt:

![Azure AD – einmaliges Anmelden](./media/active-directory-sso-certs/fed-sso.PNG)

##Anpassen des Ablaufdatums für das Verbundzertifikat

Standardmäßig wird für Zertifikate eine Gültigkeitsdauer von zwei Jahren festgelegt. Mit folgenden Schritten können Sie ein anderes Ablaufdatum für Ihr Zertifikat auswählen. In den Screenshots wurde Salesforce als Beispiel verwendet, diese Schritte können jedoch für jede SaaS-Verbund-App angewendet werden.

1. Klicken Sie in Azure Active Directory auf der Seite "Schnellstart" für Ihre Anwendung auf **Einmaliges Anmelden konfigurieren**.

	![Öffnen des SSO-Konfigurations-Assistenten](./media/active-directory-sso-certs/config-sso.png)

2. Wählen Sie **Azure AD – einmaliges Anmelden**, und klicken Sie dann auf **Weiter**.

3. Geben Sie die **URL für Anmeldung** für Ihre Anwendung ein, und aktivieren Sie das Kontrollkästchen **Konfigurieren Sie das für die einmalige Verbundanmeldung verwendete Zertifikat**. Klicken Sie auf **Weiter**.

	![Azure AD – einmaliges Anmelden](./media/active-directory-sso-certs/new-app-config-sso.PNG)

4. Wählen Sie auf der nächsten Seite **Neues Zertifikat generieren** aus, und geben Sie an, wie lange das Zertifikat gültig sein soll. Klicken Sie auf **Weiter**.

	![Generieren eines neues Zertifikats](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Klicken Sie anschließend auf **Zertifikat herunterladen**. Um Informationen zum Hochladen des Zertifikats für Ihre spezielle SaaS-App zu erhalten, klicken Sie auf **Konfigurationsanweisungen anzeigen**.

	![Herunterladen und Hochladen des Zertifikats](./media/active-directory-sso-certs/new-app-config-app.PNG)

6. Das Zertifikat wird erst aktiviert, wenn Sie das Kontrollkästchen "Bestätigung" am unteren Rand des Dialogfelds aktivieren und auf "Senden" klicken.

##Vorgehensweise zum Erneuern eines in Kürze ablaufenden Zertifikats

Im Idealfall sollte die unten gezeigten Schritte zur Erneuerung keine wesentliche Ausfallzeiten für Ihre Benutzer verursachen. In den Screenshots in diesem Abschnitt wurde Salesforce als Beispiel verwendet, diese Schritte können jedoch für jede SaaS-Verbund-App angewendet werden.

1. Klicken Sie in Azure Active Directory auf der Seite "Schnellstart" für Ihre Anwendung auf **Einmaliges Anmelden konfigurieren**.

	![Öffnen des SSO-Konfigurations-Assistenten](./media/active-directory-sso-certs/renew-sso-button.PNG)

2. Auf der ersten Seite des Dialogfelds sollte **Azure AD – einmaliges Anmelden** bereits ausgewählt sein. Klicken Sie auf **Weiter**.

3. Aktivieren Sie auf der zweiten Seite das Kontrollkästchen **Konfigurieren Sie das für die einmalige Verbundanmeldung verwendete Zertifikat**. Klicken Sie auf **Weiter**.

	![Azure AD – einmaliges Anmelden](./media/active-directory-sso-certs/renew-config-sso.PNG)

4. Wählen Sie auf der nächsten Seite **Neues Zertifikat generieren** aus, und geben Sie an, wie lange das neue Zertifikat gültig sein soll. Klicken Sie auf **Weiter**.

	![Generieren eines neues Zertifikats](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Klicken Sie auf **Zertifikat herunterladen**. Um Ihr Zertifikat zu verlängern, führen Sie die folgenden beiden Schritte aus:

	- Laden Sie das neue Zertifikat im Bestätigungsbildschirm der einmaligen Anmeldung der SaaS-App hoch. Um Informationen zu den erforderlichen Schritten für Ihre spezielle SaaS-App zu erhalten, klicken Sie auf **Konfigurationsanweisungen anzeigen**.

	- Aktivieren Sie in Azure AD das Kontrollkästchen "Bestätigung" unten im Dialogfeld, um das neue Zertifikat zu aktivieren, und klicken Sie zum Senden auf **Weiter**.

	> [AZURE.IMPORTANT]Die einmalige Anmeldung bei der App wird in dem Moment deaktiviert, in dem einer der beiden Schritte abgeschlossen ist, sie wird jedoch wieder aktiviert, sobald der zweite Schritt abgeschlossen ist. Um daher Ausfallzeiten so gering wie möglich zu halten, bereiten Sie sich darauf vor, beide Schritte möglichst schnell nacheinander auszuführen.

	![Herunterladen und Hochladen des Zertifikats](./media/active-directory-sso-certs/renew-config-app.PNG)

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!---HONumber=Oct15_HO1-->