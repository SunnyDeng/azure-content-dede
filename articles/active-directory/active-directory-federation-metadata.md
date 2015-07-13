<properties
   pageTitle="Verbundmetadaten"
   description="Eine Beschreibung der Metadatenendpunkte und des Inhalts des Metadatendokuments, den Dienste verwenden müssen, welche Azure AD-Tokens akzeptieren."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/30/2015"
   ms.author="mbaldwin"/>

# Verbundmetadaten
Azure Active Directory (Azure AD) veröffentlicht ein Verbundmetadatendokument für Dienste, die für das Akzeptieren des von Azure Active Directory ausgestellten Sicherheitstokens konfiguriert sind. Das Format des Verbundmetadatendokuments wird in der [Webdiensteverbund-Sprache (WS-Verbund), Version 1.2,](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) beschrieben, einer Erweiterung der [Metadaten für die OASIS Security Assertion Markup Language (SAML), Version 2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

Dieses Thema enthält eine Liste der Metadatenendpunkte und erläutert den Inhalt des Metadatendokuments, den Dienste verwenden müssen, welche Azure AD-Tokens akzeptieren.

##Mandantenspezifische und mandantenunabhängige Metadatenendpunkte

Azure AD veröffentlicht mandantenspezifische und mandantenunabhängige Endpunkte. Mandantenspezifische Endpunkte sind auf einen bestimmten Mandanten ausgelegt. Die mandantenspezifischen Verbundmetadaten enthalten Informationen über den Mandanten, einschließlich mandantenspezifischer Aussteller- und Endpunktinformationen. Anwendungen, die den Zugriff auf einen einzelnen Mandanten einschränken, verwenden mandantenspezifische Endpunkte.

Mandantenunabhängige Endpunkte stellen Informationen bereit, die für alle Azure AD-Mandanten gemeinsam verwendet werden. Diese Informationen gelten für die unter *login.windows.net* gehosteten Mandanten und werden über Mandanten hinweg gemeinsam genutzt. Mandantenunabhängige Endpunkte werden für mehrinstanzenfähige Anwendungen empfohlen, da sie nicht einem bestimmten Mandanten zugeordnet sind.

## Verbundmetadaten-Endpunkte

Azure AD veröffentlicht Verbundmetadaten in *https://login.windows.net/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml*, wobei der Wert von <TenantDomainName> entweder "common" oder ein mandantenspezifischer Wert sein kann. Die Endpunkte sind adressierbar. Daher können Sie die Adresswebsite aufrufen, um die Verbundmetadaten für einen Mandanten anzuzeigen.

Für **mandantenspezifische Endpunkte** kann <TenantDomainName> einem der folgenden Typen entsprechen:

- Ein registrierter Domänenname für einen Azure AD-Mandanten, z. B.: "contoso.onmicrosoft.com".

- Die unveränderliche Mandanten-ID der Domäne, z. B. "72f988bf-86f1-41af-91ab-2d7cd011db45".

Für **mandantenunabhängige Endpunkte** lautet der Wert von <TenantDomainName> **common**. Dieser Name gibt nur die Verbundmetadatenelemente an, die für alle Azure AD-Mandanten gelten, die unter "login.windows.net" gehostet werden.

Ein mandantenspezifischer Endpunkt kann z. B. folgendermaßen lauten: *https://login.windows.net/contoso.onmicrosoft.comFederationMetadata/2007-06/FederationMetadata.xml*. Der mandantenunabhängige Endpunkt lautet *https://login.windows.net/common/FederationMetadata/2007-06/FederationMetadata.xml*.

## Inhalt der Verbundmetadaten

Der folgende Abschnitt enthält die Informationen, die von Diensten benötigt werden, welche die von Azure AD ausgegebenen Tokens nutzen.

### EntityID

Das Element **EntityDescriptor** enthält ein **EntityID**-Attribut. Der Wert des **EntityID**-Attributs stellt den Aussteller dar, d. h. den Sicherheitstokendienst (STS), der das Token ausgestellt hat. Der Aussteller ist unbedingt zu überprüfen, damit Sie bestätigen können, welcher Mandant ein Token ausgestellt hat.

Die folgenden Metadaten zeigen eine mandantenspezifisches **EntityDescriptor**-Beispielelement mit einem **EntityID**-Element.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b" 
    entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">

Die **EntityID** im mandantenunabhängigen Endpunkt enthält eine Vorlage, die zum Generieren eines mandantenspezifischen **EntityID**-Werts verwendet werden kann. Ersetzen Sie das Literal "{tenant}" im mandantenunabhängigen Endpunkt durch den Wert des **TenantID**-Anspruchs aus einem Token. Der resultierende Wert stimmt mit dem Herausgeber des Tokens überein. Diese Strategie ermöglicht es einer mehrinstanzenfähigen Anwendung, den Aussteller für einen bestimmten Mandanten zu überprüfen.

Die folgende Metadaten zeigen ein Beispiel für ein mandantenunabhängiges **EntityID**-Element. In diesem Element ist {tenant} ein Literal, kein Platzhalter.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd" 
    entityID="https://sts.windows.net/{tenant}/">

### Tokensignaturzertifikat
Wenn ein Dienst ein Token erhält, das von einem Azure AD-Mandanten ausgestellt wird, muss die Signatur des Tokens mit einen Signaturschlüssel überprüft werden, der im Verbundmetadatendokument veröffentlicht wird.

Die Verbundmetadaten umfassen den öffentlichen Teil der Zertifikate, die von den Mandanten für die Tokensignatur verwendet werden. Die unformatierten Bytes des Zertifikats werden im **KeyDescriptor**-Element angezeigt. Das Tokensignaturzertifikat ist nur dann für die Signierung gültig, wenn der Wert des **use**-Attributs **signing** lautet.

Ein von Azure AD veröffentlichtes Verbundmetadatendokument kann mehrere Signaturschlüssel aufweisen, z. B. wenn bei Azure AD die Aktualisierung des Signaturzertifikats vorbereitet wird. Wenn ein Verbundmetadatendokument mehrere Zertifikate enthält, sollte ein Dienst, der die Token überprüft, alle Zertifikate im Dokument unterstützen.

Die folgenden Metadaten zeigen ein Beispiel für ein **KeyDescriptor**-Element mit einem Signaturschlüssel.

    <KeyDescriptor use="signing">
    <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
    <X509Data>
    <X509Certificate>
    MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
    </X509Certificate>
    </X509Data>
    </KeyInfo>
    </KeyDescriptor>

Das **KeyDescriptor**-Element wird an zwei Stellen im Verbundmetadatendokument aufgeführt: im WS-Verbund-spezifischen Abschnitt und im SAML-spezifischen Abschnitt. Die in beiden Abschnitten veröffentlichten Zertifikate sind identisch.

Im WS-Verbund-spezifischen Abschnitt liest ein WS-Verbundmetadatenleser die Zertifikate aus einem **RoleDescriptor**-Element des Typs **SecurityTokenServiceType**.

Die folgenden Metadaten zeigen ein Beispiel für ein **RoleDescriptor**-Element.

    <RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706"
    xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">`

Im SAML-spezifischen Abschnitt liest ein WS-Verbundmetadatenleser die Zertifikate aus einem **IDPSSODescriptor**-Element.

Die folgenden Metadaten zeigen ein Beispiel für ein **IDPSSODescriptor**-Element.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">

Es gibt keine Unterschiede im Format von mandantenspezifischen und mandantenunabhängigen Zertifikaten.

### WS-Verbundendpunkt-URL
Die Verbundmetadaten enthalten die URL, die von Azure AD für das einmalige Anmelden und das einmalige Abmelden im WS-Verbundprotokoll verwendet wird. Dieser Endpunkt wird im **PassiveRequestorEndpoint**-Element aufgeführt.

Die folgenden Metadaten zeigen ein Beispiel für ein **PassiveRequestorEndpoint**-Element für einen mandantenspezifischen Endpunkt.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

Für den mandantenunabhängigen Endpunkt wird die WS-Verbund-URL im WS-Verbundendpunkt aufgeführt, wie im folgenden Beispiel gezeigt.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/common/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

### SAML-Protokollendpunkt-URL

Die Verbundmetadaten enthalten die URL, die von Azure AD für das einmalige Anmelden und das einmalige Abmelden im SAML 2.0-Verbundprotokoll verwendet wird. Diese Endpunkte werden im **IDPSSODescriptor**-Element angezeigt.

Die URLs für Anmeldung und Abmeldung werden in den Elementen **SingleSignOnService** und **SingleLogoutService** aufgeführt. Die folgenden Metadaten zeigen ein Beispiel für ein **PassiveResistorEndpoint**-Element für einen mandantenspezifischen Endpunkt.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https:// login.windows.net/contoso.onmicrosoft.com /saml2" />
    </IDPSSODescriptor>

Auf ähnliche Weise werden die Endpunkte für die gemeinsamen SAML 2.0-Protokollendpunkte in den mandantenunabhängigen Verbundmetadaten veröffentlicht, wie im folgenden Beispiel gezeigt wird.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    </IDPSSODescriptor>

## Weitere Informationen
[Azure Active Directory-Authentifizierungsprotokolle](active-directory-authentication-protocols.md)

[Entwicklerhandbuch zu Azure Active Directory](active-directory-developers-guide.md)

<!---HONumber=62-->