<properties
	pageTitle="Azure AD Connect-Synchronisierung: Mit Azure Active Directory synchronisierte Attribute"
	description="Listet die mit Azure Active Directory synchronisierten Attribute auf."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect-Synchronisierung: Mit Azure Active Directory synchronisierte Attribute

In diesem Thema werden die Attribute aufgelistet, die von der Azure AD Connect-Synchronisierung synchronisiert werden.<br> Die Attribute sind nach den zugehörigen Azure AD-Apps gruppiert.
 




## Office 365 ProPlus

| Attributname| Benutzer| Kommentar |
| --- | :-: | --- |
| accountEnabled| X| Abgeleitet von "userAccountControl"|
| cn| X| |
| displayName| X| |
| objectSID| X| |
| pwdLastSet| X| |
| sourceAnchor| X| Das für Benutzer verwendete Attribut wird im Installationshandbuch konfiguriert.|
| usageLocation| X| "msExchUsageLocation" in AD DS|
| userPrincipalName| X| |


## Exchange Online

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Abgeleitet von "userAccountControl"|
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
| info| X| X| X| |
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
| msExchExtensionCustomAttribute1| X| X| X| |
| msExchExtensionCustomAttribute2| X| X| X| |
| msExchExtensionCustomAttribute3| X| X| X| |
| msExchExtensionCustomAttribute4| X| X| X| |
| msExchExtensionCustomAttribute5| X| X| X| |
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
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sn| X| X| | |
| sourceAnchor| X| X| X| Das für Benutzer verwendete Attribut wird im Installationshandbuch konfiguriert.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | "msExchUsageLocation" in AD DS|
| userCertificate| X| X| | |
| userPrincipalName| X| | | |
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |


## SharePoint Online

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Abgeleitet von "userAccountControl"|
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
| objectSID| X| | X| |
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
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sn| X| X| | |
| sourceAnchor| X| X| X| Das für Benutzer verwendete Attribut wird im Installationshandbuch konfiguriert.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | "msExchUsageLocation" in AD DS|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |

## Lync Online

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Abgeleitet von "userAccountControl"|
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
| objectSID| X| | X| |
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sn| X| X| | |
| sourceAnchor| X| X| X| Das für Benutzer verwendete Attribut wird im Installationshandbuch konfiguriert.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | "msExchUsageLocation" in AD DS|
| userPrincipalName| X| | | |
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
| accountEnabled| X| | | Abgeleitet von "userAccountControl"|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sourceAnchor| X| X| X| Das für Benutzer verwendete Attribut wird im Installationshandbuch konfiguriert.|
| usageLocation| X| | | "msExchUsageLocation" in AD DS|
| userPrincipalName| X| | | |


## Dynamics CRM

| Attributname| Benutzer| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Abgeleitet von "userAccountControl"|
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
| objectSID| X| | X| |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Abgeleitet von "groupType"|
| sn| X| X| | |
| sourceAnchor| X| X| X| Das für Benutzer verwendete Attribut wird im Installationshandbuch konfiguriert.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | "msExchUsageLocation" in AD DS|
| userPrincipalName| X| | | |


## Zusätzliche Ressourcen

* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO6-->