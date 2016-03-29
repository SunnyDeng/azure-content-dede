<properties
	pageTitle="Azure Active Directory B2C – Vorschau: Token, Sitzung und einmaliges Anmelden – Konfiguration | Microsoft Azure"
	description="Token, Sitzung und einmaliges Anmelden – Konfiguration in Azure Active Directory B2C "
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C – Vorschau: Token, Sitzung und einmaliges Anmelden – Konfiguration

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Diese Funktion ermöglicht Ihnen eine präzisere Steuerung auf [Basis einer Richtlinie](active-directory-b2c-reference-policies.md) von:
 
1. Lebensdauer von Sicherheitstoken, die von Azure Active Directory (Azure AD) B2C ausgegeben werden.
2. Lebensdauer von Webanwendungssitzungen, die von Azure AD B2C verwaltet werden.
3. Mehrere Apps und Richtlinien übergreifendes Verhalten von einmaligem Anmelden (Single sign-on, SSO) in Ihrem B2C-Mandanten.

Sie können diese Funktion in Ihrem B2C-Mandanten wie folgt verwenden:

1. Führen Sie diese Schritte aus, um [im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Anmelderichtlinien**. *Hinweis: Sie können diese Funktion auf jeden Richtlinientyp anwenden, nicht nur auf **Anmelderichtlinien***.
3. Öffnen Sie eine Richtlinie, indem Sie darauf klicken. Klicken Sie z. B. auf **B2C\_1\_SiIn**.
4. Klicken Sie oben auf dem Blatt auf **Bearbeiten**.
5. Klicken Sie auf **Token, Sitzung und einmaliges Anmelden**.
6. Nehmen Sie die gewünschten Änderungen vor. Informieren Sie sich in den folgenden Abschnitten zu verfügbaren Eigenschaften.
7. Klicken Sie auf **OK**.
8. Klicken Sie oben auf dem Blatt auf **Speichern**.

![Screenshot von Token, Sitzung und einmaliges Anmelden](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## Konfiguration der Tokengültigkeitsdauer

Azure AD B2C unterstützt das [Authorization-Protokoll OAuth 2.0](active-directory-b2c-reference-protocols.md) zum Aktivieren des sicheren Zugriffs auf geschützte Ressourcen. Um diese Unterstützung zu implementieren, gibt Azure AD B2C verschiedene [Sicherheitstoken](active-directory-b2c-reference-tokens.md) heraus. Dies sind die Eigenschaften, die Sie zum Verwalten der Gültigkeitsdauer von Sicherheitstoken, die von Azure AD B2C ausgegeben werden, verwenden können:

- **Gültigkeitsdauer von Zugriffs-ID-Token(Minuten)**: Die Gültigkeitsdauer des OAuth 2.0-Bearertokens, das verwendet wird, um auf eine geschützte Ressource zuzugreifen. Azure AD B2C gibt zurzeit nur ID-Token heraus. Dieser Wert würde auch für Zugriffstoken gelten, wenn wir Unterstützung für sie hinzufügen.
   - Standardwert: 60 Minuten.
   - Minimum (inklusive): 15 Minuten.
   - Maximum (inklusive): 1.440 Minuten.
- **Aktualisierungstoken-Gültigkeitsdauer (Tage)**: Der maximale Zeitraum, vor dessen Verstreichen ein Aktualisierungstoken verwendet werden kann, um einen neuen Zugriff oder ein neues ID-Token zu erwerben (und optional ein neues Aktualisierungstoken, wenn Ihrer Anwendung der `offline_access`-Bereich gewährt wurde).
   - Standardwert: 14 Tage.
   - Minimum (inklusive): 1 Tag.
   - Minimum (inklusive): 90 Tage.
- **Aktualisierungstoken-Gültigkeitsdauer – gleitendes Fenster (Tage)**: Nach Ablauf dieses Zeitraums muss der Benutzer sich erneut authentifizieren, unabhängig von der Gültigkeitsdauer des zuletzt von der Anwendung abgerufenen Aktualisierungstokens. Es kann nur bereitgestellt werden, wenn die Option auf **Begrenzt** gesetzt ist. Sein Wert muss mindestens dem Wert **Aktualisierungstoken-Gültigkeitsdauer (Tage)** entsprechen. Wenn die Option auf **Unbegrenzt** gesetzt ist, können Sie keinen bestimmten Wert angeben.
   - Standardwert: 90 Tage.
   - Minimum (inklusive): 1 Tag.
   - Maximum (inklusive): 365 Tage.

Dies sind einige Anwendungsfälle, die Sie mit diesen Eigenschaften aktivieren können:

- Ermöglichen Sie einem Benutzer, unbegrenzt in einer mobilen Anwendung angemeldet zu bleiben, solange er ständig in der Anwendung aktiv ist. Hierzu können Sie die Option **Aktualisierungstoken-Gültigkeitsdauer – gleitendes Fenster (Tage)** in Ihrer Richtlinie auf **Unbegrenzt** setzen.
- Erfüllen Sie die Sicherheits- und Complianceanforderungen Ihrer Branche durch Festlegung der entsprechenden Zugriffstoken-Gültigkeitsdauer.

## Sitzungskonfiguration

Azure AD B2C unterstützt das [OpenID Connect-Authentifizierungsprotokoll](active-directory-b2c-reference-oidc.md) zum Aktivieren der sicheren Anmeldung bei Webanwendungen. Dies sind die Eigenschaften, die Sie verwenden können, um Webanwendungssitzungen zu verwalten:

- **Gültigkeitsdauer von Web-App-Sitzungen (Minuten)**: Die Gültigkeitsdauer von Azure AD B2C-Sitzungscookies, die nach erfolgreicher Authentifizierung des Benutzers im Browser gespeichert werden.
   - Standardwert: 1.440 Minuten.
   - Minimum (inklusive): 15 Minuten.
   - Maximum (inklusive): 1.440 Minuten.
- **Web-App-Sitzungs-Zeitüberschreitung**: Wenn diese Option auf **Absolut** gesetzt ist, muss der Benutzer sich nach dem in **Gültigkeitsdauer von Web-App-Sitzungen (Minuten)** angegebenen Zeitraum erneut authentifizieren. Wenn diese Option auf **Rollen** gesetzt ist (Standardeinstellung), bleibt der Benutzer angemeldet, solange er ständig in Ihrer Webanwendung aktiv ist.

Dies sind einige Anwendungsfälle, die Sie mit diesen Eigenschaften aktivieren können:

- Erfüllen Sie die Sicherheits- und Complianceanforderungen Ihrer Branche durch Festlegung der entsprechenden Webanwendungssitzungs-Gültigkeitsdauer.
- Erzwingen Sie erneute Authentifizierung nach einem festgelegten Zeitraum während der Interaktion eines Benutzers mit einem Hochsicherheitsteil Ihrer Webanwendung. 

## Einmaliges Anmelden (Single Sign-on, SSO) – Konfiguration

Wenn Sie in Ihrem B2C-Mandanten über mehrere Anwendungen und Richtlinien verfügen, können Sie die Interaktionen der Benutzer mithilfe der Eigenschaft **Konfiguration des einmaligen Anmeldens** übergreifend verwalten. Sie können die Eigenschaft auf eine der folgenden Optionen festlegen:

- **Mandant**: Dies ist die Standardeinstellung. Mit dieser Einstellung können mehrere Anwendungen und Richtlinien in Ihrem B2C-Mandanten die gleiche Benutzersitzung gemeinsam nutzen. Sobald ein Benutzer sich z. B. bei einer Anwendung, Contoso Shopping, anmeldet, kann er sich beim Zugriff darauf auch nahtlos bei einer anderen anmelden, Contoso Pharmacy.
- **Richtlinie**: Damit können Sie eine Benutzersitzung ausschließlich für eine Richtlinie beibehalten, unabhängig von den Anwendungen, die sie verwenden. Wenn der Benutzer sich z. B. bereits angemeldet und einen Multi-Factor Authentication-Schritt (MFA) abgeschlossen hat, kann er Zugriff auf Hochsicherheitsteile mehrerer Anwendungen erhalten, solange die an die Richtlinie gebundene Sitzung nicht abläuft.
- **Deaktiviert**: Bei jeder Ausführung der Richtlinie muss der Benutzer die gesamte Benutzerreise ausführen. Dies erlaubt beispielsweise mehreren Benutzern, sich bei der Anwendung anzumelden (in einem Szenario mit freigegebenem Desktop), auch während ein einzelner Benutzer während der gesamten Zeit angemeldet bleibt.

<!---HONumber=AcomDC_0316_2016-->