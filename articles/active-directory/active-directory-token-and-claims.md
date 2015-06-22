<properties 
   pageTitle="Unterstützte Token- und Anspruchstypen"
   description="Anleitung zum Verstehen und Auswerten der Ansprüche in den SAML 2.0- und JWT-Token (JSON Web Tokens), die Azure Active Directory (Azure AD) ausstellt."
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory" 
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/29/2015"
   ms.author="mbaldwin"/>

# Unterstützte Token- und Anspruchstypen

Zweck dieses Themas ist es, Ihnen das Verstehen und Auswerten der Ansprüche in den SAML 2.0- und JWT-Token (JSON Web Tokens) zu erleichtern, die Azure Active Directory (Azure AD) ausstellt.

Das Thema beginnt mit einer Beschreibung der einzelnen Tokenansprüche und zeigt ein Beispiel des Anspruchs in einem SAML-Token und einem JWT-Token. Ansprüche mit Vorschaustatus sind separat aufgeführt. Am Ende finden Sie Beispieltoken, damit Sie die Ansprüche im Kontext sehen können.

Mit der Zeit werden von Azure weitere Ansprüche zu Token hinzugefügt, um neue Szenarien zu ermöglichen. In der Regel führen wir diese Ansprüche im Vorschaustatus ein. Nach einer Testphase wird für die Ansprüche der volle Support geboten. Zur Vorbereitung auf Anspruchsänderungen sollten Anwendungen, die Token von Azure AD akzeptieren, nicht vertraute Tokenansprüche ignorieren, damit neue Ansprüche die Anwendung nicht beeinträchtigen. Anwendungen, die Ansprüche im Vorschaustatus verwenden, sollten nicht von diesen Ansprüchen abhängig sein und keine Ausnahmen auslösen, wenn der Anspruch nicht im Token enthalten ist. Wenn Ihre Anwendung Ansprüche benötigt, die in von Azure AD-ausgestellten SAML- oder JWT-Token nicht verfügbar sind, können Sie unten auf dieser Seite im Abschnitt "Ergänzungen aus der Community" neue Anspruchstypen vorschlagen und diskutieren.

## Referenz zu Tokenansprüchen

In diesem Abschnitt werden die Ansprüche in Token aufgelistet und beschrieben, die Azure AD zurückgibt. Enthalten sind die SAML-Version und JWT-Version des Anspruchs und eine Beschreibung des Anspruchs und seiner Verwendung. Die Ansprüche sind in alphabetischer Reihenfolge aufgeführt.

### Anwendungs-ID

Der Anspruch "Anwendungs-ID" identifiziert die Anwendung, die das Token verwendet, um auf eine Ressource zuzugreifen. Die Anwendung kann als sie selbst oder im Auftrag eines Benutzers agieren. Die Anwendungs-ID stellt in der Regel ein Anwendungsobjekt dar, kann aber auch ein Dienstprinzipalobjekt in Azure AD darstellen.

Azure AD unterstützt in einem SAML-Token keinen Anwendungs-ID-Anspruch.

In einem JWT-Token ist die Anwendungs-ID in einem "appid"-Anspruch enthalten.

    "appid":"15CB020F-3984-482A-864D-1D92265E8268"

### Audience (Zielgruppe)
Die Zielgruppe eines Tokens ist der beabsichtigte Empfänger des Tokens. Die Anwendung, die das Token empfängt, muss prüfen, ob der "Audience"-Wert ordnungsgemäß ist, und alle Token ablehnen, die für eine andere Zielgruppe vorgesehen sind.

Der "Audience"-Wert ist eine Zeichenfolge (in der Regel die Basisadresse der Ressource, auf die zugegriffen wird), z. B. "https://contoso.com". In Azure AD-Token ist die Zielgruppe der App-ID-URI der Anwendung, die das Token angefordert hat. Wenn die Anwendung (welche die Zielgruppe ist) über mehr als einen App-ID-URI verfügt, entspricht der App-ID-URI im "Audience"-Anspruch des Tokens dem App-ID-URI in der Tokenanforderung. Bei einem SAML-Token wird der "Audience"-Anspruch im "Audience"-Element des "AudienceRestriction"-Elements definiert.

    <AudienceRestriction>
    <Audience>https://contoso.com</Audience>
    </AudienceRestriction>

Bei einem JWT-Token ist die Zielgruppe in einem "aud"-Anspruch enthalten.

    "aud":"https://contoso.com"

### Kontextklassenreferenz für die Anwendungsauthentifizierung

Der Anspruch "Kontextklassenreferenz für die Anwendungsauthentifizierung" gibt an, wie der Client authentifiziert wurde. Bei einem öffentlichen Client ist der Wert 0. Wenn die Client-ID und der geheime Clientschlüssel verwendet werden, ist der Wert 1.

Bei einem JWT-Token ist der Wert der Kontextklassenreferenz für die Anwendungsauthentifizierung in einem "appidacr"-Anspruch (anwendungsspezifischer ACR-Wert) enthalten.

    "appidacr": "0"

### Klassenreferenz des Anwendungskontexts
Der Anspruch "Klassenreferenz des Anwendungskontexts" gibt an, wie der Antragsteller im Gegensatz zum Client im Anspruch "Kontextklassenreferenz für die Anwendungsauthentifizierung" authentifiziert wurde. Der Wert "0" gibt an, dass die Endbenutzerauthentifizierung nicht die ISO/IEC 29115-Anforderungen erfüllt.

- Bei einem JWT-Token ist der Anspruch der Klassenreferenz des Anwendungskontexts in einem "acr"-Anspruch (anwendungsspezifischer ACR-Wert) enthalten.

    "acr": "0"

### Authentication Instant (Authentifizierungszeitpunkt)

Der "Authentication Instant"-Anspruch zeichnet Datum und Uhrzeit des Erfolgens der Authentifizierung auf.

Bei einem SAML-Token ist der Authentifizierungszeitpunkt im "AuthnInstant"-Attribut des "AuthnStatement"-Elements enthalten. Es stellt einen "datetime"-Wert in UTC-Zeit (Z) dar.

    <AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">

Azure AD unterstützt in JWT-Token keinen vergleichbaren Anspruch.

### Authentication Method (Authentifizierungsmethode)

Der "Authentication Method"-Anspruch gibt an, wie der Antragsteller des Tokens authentifiziert wurde. Bei diesem Beispiel hat der Identitätsanbieter ein Kennwort zur Authentifizierung des Benutzers verwendet. http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password

Bei einem SAML-Token ist der "Authentication Method"-Wert im "AuthnContextClassRef"-Element enthalten.

    <AuthnContextClassRef>http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password</AuthnContextClassRef>

Bei einem JWT-Token ist der "Authentication Method"-Wert im "amr"-Anspruch enthalten.

    “amr”: ["pwd"]

###Vorname

Der Anspruch "First Name" oder "Given Name" gibt den Vornamen des Benutzers entsprechend der Festlegung im Azure AD-Benutzerobjekt an.

Bei einem SAML-Token ist der Vorname in einen Anspruch im SAML-Attributelement "givenName" enthalten.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>
    <AttributeValue>Frank<AttributeValue>

Bei einem JWT-Token ist der Vorname in einem "given_name"-Anspruch enthalten.

    "given_name": "Frank"

### Groups (Gruppen)

Der Anspruch "Groups" enthält die Objekt-IDs, die die Gruppenmitgliedschaften des Antragstellers darstellen. Diese Werte sind eindeutig (siehe "Object ID") und eignen sich zum sicheren Verwalten des Zugriffs, z. B. für das Erzwingen der Autorisierung für den Zugriff auf eine Ressource. Die im Anspruch "Groups" enthaltenen Gruppen werden über die "GroupMembershipClaims"-Eigenschaft des Anwendungsmanifests anwendungsbezogen konfiguriert. Mit dem Wert "null" werden alle Gruppen ausgeschlossen. Beim Wert "SecurityGroup" sind nur Active Directory-Sicherheitsgruppenmitglieder enthalten. Beim Wert "All" sind sowohl Sicherheitsgruppen als auch Office 365-Verteilerlisten enthalten. Bei jeder Konfiguration stellt der Gruppenanspruch transitive Gruppenmitgliedschaften des Antragstellers dar.

Bei einem SAML-Token ist der Gruppenanspruch im "Groups"-Attribut enthalten.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
    <AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>

Bei einem JWT-Token ist der Gruppenanspruch im "Groups"-Anspruch enthalten.

    “groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]

### Identity Provider (Identitätsanbieter)

Der "Identity Provider"-Anspruch zeichnet den Identitätsanbieter auf, der den Antragsteller des Tokens authentifiziert hat. Dieser Wert ist identisch mit dem Wert des "Issuer "-Anspruchs, es sei denn, das Benutzerkonto gehört zu einem anderen Mandanten als der Aussteller.

Bei einem SAML-Token ist der Identitätsanbieter in einem Anspruch im SAML-Attributelement "identityprovider" enthalten.

    <Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>
    <AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>

Bei einem JWT-Token ist der Identitätsanbieter in einem "ipd"-Anspruch enthalten.

    "idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### IssuedAt (Ausgestellt um)

Der "IssuedAt"-Anspruch speichert die Uhrzeit, zu der das Token ausgestellt wurde. Er wird häufig verwendet, um die Aktualität von Token zu messen. Bei einem SAML-Token ist der "IssuedAt"-Wert in der "IssueInstant"-Assertion enthalten.

    <Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">

Bei einem JWT-Token ist der "IssuedAt"-Wert in einem "iat"-Anspruch enthalten. Der Wert wird ausgedrückt als Anzahl der Sekunden seit 1970-01-010:0:0Z in koordinierter Weltzeit (UTC).

    "iat": 1390234181

### Issuer (Aussteller)

Der "Issuer"-Anspruch identifiziert den Sicherheitstokendienst (STS), der das Token und den Azure AD-Mandanten erstellt und zurückgibt. Bei den Token, die Azure AD zurückgibt, ist der Aussteller "sts.windows.net". Die GUID im "Issuer"-Anspruch ist die Mandanten-ID des Azure AD-Verzeichnisses. Die Mandanten-ID ist ein unveränderlicher und zuverlässiger Bezeichner des Verzeichnisses.

Bei einem SAML-Token ist der "Issuer"-Anspruch im "Issuer"-Element enthalten.

    <Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>

Bei einem JWT-Token ist der Antragsteller in einem "iss"-Anspruch enthalten.

    "iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### Nachname

Der Anspruch "Nachname" gibt den Nachnamen des Benutzers entsprechend der Definition im Azure AD-Benutzerobjekt an. Bei einem SAML-Token ist der Nachname in einen Anspruch im SAML-Attributelement "surname" enthalten.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>
    <AttributeValue>Miller<AttributeValue>

Bei einem JWT-Token ist der Nachname in einem "family_name"-Anspruch enthalten.

    "family_name": "Miller"

### Name

Der Anspruch "Name" gibt einen von Menschen lesbaren Wert an, der den Antragsteller des Tokens identifiziert. Es wird nicht garantiert, dass der Wert innerhalb eines Mandanten eindeutig ist, weshalb er nur zu Anzeigezwecken dient. Bei einem SAML-Token ist der Name im "Name"-Attribut enthalten.

    <Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>
    <AttributeValue>frankm@contoso.com<AttributeValue>

Bei einem JWT-Token ist der Name im "unique_name"-Anspruch enthalten.

    "unique_name": "frankm@contoso.com"

### Object ID (Objekt-ID)

Der "Object ID"-Anspruch enthält einen eindeutigen Bezeichner eines Objekts in Azure AD. Dieser Wert ist unveränderlich und kann nicht neu zugewiesen oder wiederverwendet werden. Daher können Sie ihn für die sichere Durchführung von Autorisierungsüberprüfungen verwenden, z. B. wenn das Token verwendet wird, um auf eine Ressource zuzugreifen. Verwenden Sie die Objekt-ID zum Identifizieren eines Objekts in Abfragen von Azure AD. Bei einem SAML-Token ist die Objekt-ID im "objectidentifier"-Attribut enthalten.

    <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
    <AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>

Bei einem JWT-Token ist die Objekt-ID in einem "oid"-Anspruch enthalten.

    "oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"

### Roles (Rollen)

Der "Roles"-Anspruch bietet benutzerfreundliche Zeichenfolgen, die die Rollenzuweisungen für Anwendungen für den Antragsteller in Azure AD darstellen und zum Erzwingen der rollenbasierten Zugriffssteuerung verwendet werden können. Anwendungsrollen werden über die "appRoles"-Eigenschaft des Anwendungsmanifests anwendungsbezogen definiert. Die "value"-Eigenschaft der einzelnen Anwendungsrollen ist der Wert, der im "roles"-Anspruch enthalten ist. Die Rollen im "roles"-Anspruch stellen alle Anwendungsrollen dar, die dem Antragsteller direkt und indirekt über eine Gruppenmitgliedschaft zugewiesen wurden. Bei einem SAML-Token ist der Rollenanspruch im "roles"-Attribut enthalten.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">
    <AttributeValue>Admin</AttributeValue>

Bei einem JWT-Token ist der Rollenanspruch im "roles"-Anspruch enthalten.

    “roles”: ["Admin", … ]

### Umfang

Der Umfang des Tokens gibt die Identitätswechselberechtigungen an, die der Clientanwendung erteilt wurden. Die Standardberechtigung ist "user_impersonation". Der Besitzer der gesicherten Ressource kann zusätzliche Werte in Azure AD registrieren.

Bei einem JWT-Token wird der Umfang des Tokens in einem "scp"-Anspruch angegeben.

    "scp": "user_impersonation"

### Subject (Antragsteller)

Der Antragsteller des Tokens ist der Prinzipal, für den das Token Informationen zusichert, z. B. der Benutzer einer Anwendung. Dieser Wert ist unveränderlich und kann nicht neu zugewiesen oder wiederverwendet werden. Daher kann er für die sichere Durchführung von Autorisierungsüberprüfungen verwendet werden, z. B. wenn das Token verwendet wird, um auf eine Ressource zuzugreifen. Da der Antragsteller immer in den Token vorhanden ist, die Azure AD ausstellt, wird die Nutzung dieses Werts in einem allgemeinen Autorisierungssystem empfohlen.

Bei einem SAML-Token wird der Antragsteller des Tokens im "NameID"-Element des "Subject"-Elements angegeben. "NameID" ist ein eindeutiger, nicht wiederverwendeter Bezeichner des Antragstellers, der ein Benutzer, eine Anwendung oder ein Dienst sein kann.

"SubjectConfirmation" ist kein Anspruch. Mit dem Element wird beschrieben, wie der Antragsteller des Tokens verifiziert wird. "Bearer" gibt an, dass der Antragsteller durch den Besitz des Tokens bestätigt wird.

    <Subject>
    <NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
    </Subject>

Bei einem JWT-Token ist der Antragsteller in einem "sub"-Anspruch enthalten.

    "sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"

### Tenant ID (Mandanten-ID)
Die Mandanten-ID ist ein unveränderlicher, nicht wieder verwendbarer Bezeichner, der den Verzeichnismandanten identifiziert, der das Token ausgestellt hat. Sie können diesen Wert verwenden, um auf mandantenspezifische Verzeichnisressourcen in einer mehrinstanzenfähigen Anwendung zugreifen. Sie können z. B. diesen Wert verwenden, um den Mandanten in einem Aufruf der Graph-API zu identifizieren.

Bei einem SAML-Token ist die Mandanten-ID in einem Anspruch im SAML-Attributelement "tenantid" enthalten.

    <Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>
    <AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>

Bei einem JWT-Token ist die Mandanten-ID in einem "tid"-Anspruch enthalten.

    "tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"

### Token Lifetime (Tokengültigkeitsdauer)
Der Anspruch "Tokengültigkeitsdauer" definiert das Zeitintervall, in dem ein Token gültig ist. Der Dienst, der das Token überprüft, muss bestätigen, dass das aktuelle Datum innerhalb der Gültigkeitsdauer des Tokens liegt. Andernfalls muss er das Token ablehnen. Der Dienst kann einen Puffer von bis zu fünf Minuten über die Tokengültigkeitsdauer hinaus zulassen, um Differenzen bei der Istzeit (d. h. den zeitlichen Versatz) zwischen Azure AD und dem Dienst zu berücksichtigen.

Bei einem SAML-Token wird der Anspruch "Tokengültigkeitsdauer" im "Conditions"-Element mit den Attributen "NotBefore" und "NotOnOrAfter" definiert.

    <Conditions
    NotBefore="2013-03-18T21:32:51.261Z"
    NotOnOrAfter="2013-03-18T22:32:51.261Z"
    >

Bei einem JWT-Token wird die Tokengültigkeitsdauer mithilfe der Ansprüche "nbf" (nicht vor) und "exp" (Ablaufzeit) definiert. Der Wert dieser Ansprüche wird ausgedrückt als Anzahl der Sekunden seit 1970-01-010:0:0Z in koordinierter Weltzeit (UTC). Weitere Informationen finden Sie unter "RFC 3339".

    "nbf":1363289634,
    "exp":1363293234

### User Principal Name (Benutzerprinzipalname)
Der "User Principal Name"-Anspruch speichert den Benutzernamen des Benutzerprinzipals.

Bei einem JWT-Token ist der Benutzerprinzipalname in einem "upn"-Anspruch enthalten.

    "upn": frankm@contoso.com

### Version
Der Anspruch "Version" speichert die Versionsnummer des Tokens. Bei einem JWT-Token ist die Version in einem "ver"-Anspruch enthalten.

    "ver": "1.0"

## Beispieltoken

Dieser Abschnitt enthält Beispiele von SAML- und JWT-Token, die Azure AD zurückgibt. Bei diesen Beispielen können Sie die Ansprüche im Kontext sehen. SAML-Token

Dies ist ein Beispiel eines typischen SAML-Tokens.

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
			  <X509Data>
				<X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
			  </X509Data>
			</KeyInfo>
		  </ds:Signature>
		  <Subject>
			<NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
			<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
		  </Subject>
		  <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
			<AudienceRestriction>
			  <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
			</AudienceRestriction>
		  </Conditions>
		  <AttributeStatement>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
			  <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
			  <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
			  <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
			  <AttributeValue>Admin</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
			  <AttributeValue>Sample</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
			  <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
			  <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
			  <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
			  <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
			  <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
			  <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
			  <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
			  <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
			  <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
			  <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
			  <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
			  <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
			  <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
			  <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
			</Attribute>
		  </AttributeStatement>
		  <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
			<AuthnContext>
			  <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
			</AuthnContext>
		  </AuthnStatement>
		</Assertion>
	  </t:RequestedSecurityToken>
	  <t:RequestedAttachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### JWT-Token – Benutzeridentitätswechsel

Dies ist ein Beispiel eines typischen JWT-Tokens (JSON webtoken), das bei einem Benutzeridentitätswechsel verwendet wird. Zusätzlich zu den Ansprüchen enthält das Token eine Versionsnummer in **ver** und **appidacr** und die Klassenreferenz des Anwendungskontexts, die angibt, wie der Client authentifiziert wurde. Bei einem öffentlichen Client ist der Wert 0. Wenn eine Client-ID oder ein geheimer Clientschlüssel verwendet wird, ist der Wert 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

##Siehe auch
### Konzepte

[Azure Active Directory-Authentifizierungsprotokolle](active-directory-authentication-protocols.md)

<!---HONumber=58--> 