<properties 
    pageTitle="Hinzufügen eines Benutzers in RemoteApp" 
    description="Erfahren Sie, wie Sie in RemoteApp Benutzerhinzufügen" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/06/2015" 
    ms.author="elizapo" />

# Gewusst wie: Hinzufügen eines Benutzers in RemoteApp

Bevor Ihre Benutzer die Apps in RemoteApp anzeigen und verwenden können, müssen Sie ihnen Zugriff gewähren. Dies ist der einfache Teil: Geben Sie auf der Registerkarte **Benutzerzugriff** die Kontoinformationen für den Benutzer ein, dem Zugriff auf diesen Dienst gewährt werden soll.

Welche Kontoinformationen benötigen Sie? Das hängt vom dem Typ der erstellten Sammlung (Cloud oder Hybrid) und der möglichen Verwendung von Office 365 ProPlus in dieser Sammlung ab.

## Unterstützte Benutzeridentitäten

Azure RemoteApp verfügt über zwei Bereitstellungsmethoden: Cloud und Hybrid. Beide unterstützen jeweils unterschiedliche Benutzeridentitäten für den Benutzerzugriff auf Anwendungen.

Bei einer Hybrid-Sammlung von RemoteApp müssen Sie eine lokale Active Directory-Domäneninfrastruktur und einen Azure Active Directory-Mandanten mit Verzeichnisintegration (und optional einmaliges Anmelden) einrichten. Darüber hinaus müssen Sie einige Active Directory-Objekte im lokalen Verzeichnis erstellen.

Für eine RemoteApp-Cloud-Sammlung kann beliebigen Benutzern mit Azure Active Directory-Supportidentitäten der Benutzerzugriff auf RemoteApp einschließlich der Microsoft-Konten erteilt werden. Beachten Sie die folgende Tabelle.

Office 365-Benutzer sind Azure Active Directory-Benutzer. Wenn diese über mit dem Verzeichnis synchronisierte Azure Active Directory-Hybrid-Konten verfügen, kann ihnen der Benutzerzugriff in einer Hybrid-Bereitstellung von RemoteApp erteilt werden.

Diese Tabelle dient als Kurzübersicht zu den für Ihre Sammlung unterstützten Identitäten und den Active Directory-Anforderungen.

|Benutzerkonten |Cloud |Hybrid|
|--------------|--------|------|
|Microsoft Account| 	Ja|	Nein|
|Azure Active Directory (Azure AD)| | |	
|Nur Azure AD-Cloud |Ja |Nein |
|ADsync mit Kennwortsynchronisierung |Ja |Ja |
|ADsync ohne Kennwortsynchronisierung|	Ja |Nein |
|ADsync mit AD FS |Ja |Ja |
|Von Azure unterstützte Drittidentitätsanbieter (z. B. Ping) |Ja |Nein|	
|Multi-Factor Authentication |Ja |Ja |

Beachten Sie die [weiteren Informationen](remoteapp-ad.md) zum Konfigurieren von Active Directory für RemoteApp.


**Hinweis:** Azure Active Directory-Benutzer müssen dem Ihrem Abonnement zugeordneten Mandanten angehören. (Sie können Ihr Abonnement im Portal auf der Registerkarte **Einstellungen** anzeigen und bearbeiten. Weitere Informationen finden Sie unter [Ändern des von RemoteApp verwendeten Azure Active Directory-Mandanten](remoteapp-changetenant.md).)

## Benutzerkontoinformationen für Office 365 ProPlus
Bei Verwendung des Vorlagenimages von Office 365 ProPlus in Ihrer Sammlung *oder* bei Erstellung eines benutzerdefinierten Images, das Office 365 verwendet, dürfen Sie nur Azure Active Directory-Benutzer hinzufügen, die Office 365-Abonnements für die Standarddomäne Ihres Abonnements haben. Weitere Informationen finden Sie unter [Verwenden von Office 365 mit Azure RemoteApp](remoteapp-o365.md).

<!--HONumber=54-->