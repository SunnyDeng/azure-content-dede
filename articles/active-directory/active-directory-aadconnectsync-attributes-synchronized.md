<properties
	pageTitle="Azure AD Connect-Synchronisierung: Mit Azure Active Directory synchronisierte Attribute | Microsoft Azure"
	description="Listet die mit Azure Active Directory synchronisierten Attribute auf."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect-Synchronisierung: Mit Azure Active Directory synchronisierte Attribute

In diesem Thema werden die Attribute aufgelistet, die von der Azure AD Connect-Synchronisierung synchronisiert werden.<br> Die Attribute sind nach den zugehörigen Azure AD-Apps gruppiert.


## Office 365 ProPlus

| Attributname| Benutzer| Kommentar |
| --- | :-: | --- |
| accountEnabled| X| Definiert, ob ein Konto aktiviert ist.|
| cn| X| |
| displayName| X| |
| objectSID| X| Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten.|
| pwdLastSet| X| Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Von Kennwortsynchronisierung und Verbund verwendet.|
| sourceAnchor| X| Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird.|
| usageLocation| X| Mechanische Eigenschaft. Das Land des Benutzers. Wird für die Lizenzzuweisung verwendet.|
| userPrincipalName| X| Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail].|


## Exchange Online

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definiert, ob ein Konto aktiviert ist.|
| assistant| X| X| | |
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| info| X| X| X| Dieses Attribut wird derzeit nicht für Gruppen verwendet.|
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickname| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchExtensionCustomAttribute2| X| X| X| Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchExtensionCustomAttribute3| X| X| X| Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchExtensionCustomAttribute4| X| X| X| Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchExtensionCustomAttribute5| X| X| X| Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSendersHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg-IsOrganizational| | | X| |
| objectSID| X| | X| Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Von Kennwortsynchronisierung und Verbund verwendet.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sn| X| X| | |
| sourceAnchor| X| X| X| Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | Mechanische Eigenschaft. Das Land des Benutzers. Wird für die Lizenzzuweisung verwendet.|
| userCertificate| X| X| | |
| userPrincipalName| X| | | Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail].|
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |



## SharePoint Online

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definiert, ob ein Konto aktiviert ist.|
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| info| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| mobile| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Von Kennwortsynchronisierung und Verbund verwendet.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sn| X| X| | |
| sourceAnchor| X| X| X| Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | Mechanische Eigenschaft. Das Land des Benutzers. Wird für die Lizenzzuweisung verwendet.|
| userPrincipalName| X| | | Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail].|
| wWWHomePage| X| X| | |

## Lync Online

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definiert, ob ein Konto aktiviert ist.|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten.|
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Von Kennwortsynchronisierung und Verbund verwendet.|
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sn| X| X| | |
| sourceAnchor| X| X| X| Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | Mechanische Eigenschaft. Das Land des Benutzers. Wird für die Lizenzzuweisung verwendet.|
| userPrincipalName| X| | | Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail].|
| wWWHomePage| X| X| | |


## Azure RMS

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definiert, ob ein Konto aktiviert ist.|
| cn| X| | X| Allgemeiner Name oder Alias. Meistens das Präfix des Werts [mail].|
| displayName| X| X| X| Eine Zeichenfolge, die den häufig als Anzeigename (Vorname Nachname) angezeigten Namen darstellt.|
| mail| X| X| X| Vollständige E-Mail-Adresse|
| member| | | X| |
| objectSID| X| | X| Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten.|
| proxyAddresses| X| X| X| Mechanische Eigenschaft. Von Azure AD verwendet. Enthält alle sekundären E-Mail-Adressen für den Benutzer.|
| pwdLastSet| X| | | Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen.|
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sourceAnchor| X| X| X| Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird.|
| usageLocation| X| | | Mechanische Eigenschaft. Das Land des Benutzers. Wird für die Lizenzzuweisung verwendet.|
| userPrincipalName| X| | | Dieser Benutzerprinzipalname ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail].|


## Intune

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definiert, ob ein Konto aktiviert ist.|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Von Kennwortsynchronisierung und Verbund verwendet.|
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sourceAnchor| X| X| X| Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird.|
| usageLocation| X| | | Mechanische Eigenschaft. Das Land des Benutzers. Wird für die Lizenzzuweisung verwendet.|
| userPrincipalName| X| | | Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail].|



## Dynamics CRM

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definiert, ob ein Konto aktiviert ist.|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| objectSID| X| | X| Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten.|
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Von Kennwortsynchronisierung und Verbund verwendet.|
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sn| X| X| | |
| sourceAnchor| X| X| X| Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | Mechanische Eigenschaft. Das Land des Benutzers. Wird für die Lizenzzuweisung verwendet.|
| userPrincipalName| X| | | Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail].|

## Drittanbieteranwendungen
Dies ist ein Satz von Attributen, die verwendet werden können, wenn das Azure AD-Verzeichnis nicht zur Unterstützung von Office 365, Dynamics oder Intune verwendet wird. Er umfasst eine kleine Gruppe von Kernattributen.

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definiert, ob ein Konto aktiviert ist.|
| cn| X| | X| |
| displayName| X| X| X| |
| givenName| X| X| | |
| mail| X| | X| |
| managedBy| | | X| |
| mailNickName| X| X| X| |
| member| | | X| |
| objectSID| X| | | Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Von Kennwortsynchronisierung und Verbund verwendet.|
| sn| X| X| | |
| sourceAnchor| X| X| X| Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird.|
| usageLocation| X| | | Mechanische Eigenschaft. Das Land des Benutzers. Wird für die Lizenzzuweisung verwendet.|
| userPrincipalName| X| | | Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail].|

## Windows 10
Bei unter Windows 10 per Domänenbeitritt in die Domäne eingebundenen Computern (Geräten) werden einige Attribute mit Azure AD synchronisiert. Weitere Informationen zu den Szenarien finden Sie unter [Verbinden von in die Domäne eingebundenen Geräten mit Azure AD für Windows 10-Funktionen](active-directory-azureadjoin-devices-group-policy.md). Diese Attribute werden immer synchronisiert, und Windows 10 wird nicht als App angezeigt, für die Sie die Auswahl aufheben können. Ein unter Windows 10 in die Domäne eingebundener Computer wird dadurch identifiziert, dass das Attribut „userCertificate“ aufgefüllt ist.

| Attributname| Gerät| Kommentar |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Hardcodierter Wert für in die Domäne eingebundene Computer |
| displayName | X| |
| ms-DS-CreatorSID | X| Auch als „registeredOwnerReference“ bezeichnet.|
| objectGUID | X| Auch als „deviceID“ bezeichnet.|
| objectSID | X| Auch als „onPremisesSecurityIdentifier“ bezeichnet.|
| operatingSystem | X| Auch als „deviceOSType“ bezeichnet.|
| operatingSystemVersion | X| Auch als „deviceOSVersion“ bezeichnet.|
| userCertificate | X| |

Diese Attribute für Benutzer ergänzen die anderen Apps, die Sie ausgewählt haben.

| Attributname| Benutzer| Kommentar |
| --- | :-: | --- |
| domainFQDN| X| Auch als „dnsDomainName“ bezeichnet. Beispiel: contoso.com|
| domainNetBios| X| Auch als „netBiosName“ bezeichnet. Beispiel: CONTOSO|

## Exchange-Hybridrückschreiben
Diese Attribute werden vom Azure AD auf das lokale Active Directory zurückgeschrieben, wenn Sie die Aktivierung des Exchange-Hybrids auswählen. Abhängig von Ihrer Exchange-Version werden möglicherweise weniger Attribute synchronisiert.

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObjectID| X| | | Abgeleitet von cloudAnchor in Azure AD. Dies ist neu in Exchange 2016.|
| msExchArchiveStatus| X| | | Online-Archiv: Ermöglicht Kunden, E-Mail-Nachrichten zu archivieren.|
| msExchBlockedSendersHash| X| | | Filtern: Schreibt lokale Filterung, Onlinesicherung und blockierte Absenderdaten von Clients zurück.|
| msExchSafeRecipientsHash| X| | | Filtern: Schreibt lokale Filterung, Onlinesicherung und blockierte Absenderdaten von Clients zurück.|
| msExchSafeSendersHash| X| | | Filtern: Schreibt lokale Filterung, Onlinesicherung und blockierte Absenderdaten von Clients zurück.|
| msExchUCVoiceMailSettings| X| | | Aktivieren von Unified Messaging (UM) – Online-Voicemail: Wird von Microsoft Lync Server-Integration verwendet, um Lync Server lokal zu melden, dass der Benutzer Voicemail in Onlinediensten verwendet.|
| msExchUserHoldPolicies| X| | | Beweissicherungsverfahren: Ermöglicht Clouddiensten, zu bestimmen, welche Benutzer einem Beweissicherungsverfahren unterliegen.|
| proxyAddresses| X| X| X| Nur die x500-Adresse von Exchange Online wird eingefügt.|

## Hinweise zu Attributen
- Bei Verwendung einer alternativen ID wird das lokale Attribut userPrincipalName mit dem Azure AD-Attribut onPremisesUserPrincipalName synchronisiert. Das alternative ID-Attribut, z. B. Mail, wird mit dem Azure AD-Attribut userPrincipalName synchronisiert.


## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0128_2016-->