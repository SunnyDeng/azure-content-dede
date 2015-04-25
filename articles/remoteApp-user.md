<properties title="Add a user in RemoteApp" pageTitle="Hinzufügen eines Benutzers in RemoteApp" description="Informationen zum Hinzufügen von Benutzern in RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Gewusst wie: Hinzufügen eines Benutzers in RemoteApp
 
Bevor Ihre Benutzer die Apps in RemoteApp anzeigen und verwenden können, müssen Sie ihnen Zugriff gewähren. Dies ist der einfache Teil: Geben Sie auf der Registerkarte **Benutzerzugriff** die Kontoinformationen für den Benutzer ein, dem Zugriff auf diesen Dienst gewährt werden soll.

Welche Kontoinformationen benötigen Sie? Das hängt vom dem Typ der erstellten Sammlung (Cloud oder Hybrid) und der möglichen Verwendung von Office 365 ProPlus in dieser Sammlung ab.

##Benutzerkontoinformationen bei Hybrid- oder Cloud-Sammlung
Die Cloud-Sammlung unterstützt Microsoft-Konten und Azure Active Directory-Geschäftskonten mit Verzeichnissynchronisierung (die auch Office 365-Konten sind). 

Die Hybrid-Sammlung unterstützt nur Azure Active Directory-Konten, die von einer Windows Server Active Directory-Bereitstellung synchronisiert wurden (mithilfe eines Tools wie "DirSync"); hier besonders Konten, die entweder mit der Option "Kennwortsynchronisierung" oder mit Active Directory Federation Services (AD FS) in einer Verbundkonfiguration synchronisiert wurden. Weitere Informationen zu den Voraussetzungen für Azure Active Directory finden Sie unter [Konfigurieren von Active Directory für Azure RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-ad/).

**Hinweis:** Azure Active Directory-Benutzer müssen zu dem Mandanten gehören, der Ihrem Abonnement zugeordnet ist. (Ihr Abonnement können Sie im Portal auf der Registerkarte **Einstellungen** anzeigen und ändern. Weitere Informationen hierzu finden Sie unter [Ändern des von RemoteApp verwendeten Azure Active Directory-Mandanten](http://msdn.microsoft.com/de-de/3d6c4fd1-c981-4c57-9402-59fe31b11883).)

##Benutzerkontoinformationen für Office 365 ProPlus
Bei Verwendung des Vorlagenimages von Office 365 ProPlus in Ihrer Sammlung *oder* bei Erstellung eines benutzerdefinierten Images, das Office 365 verwendet, dürfen Sie nur Azure Active Directory-Benutzer hinzufügen, die Office 365-Abonnements für die Standarddomäne Ihres Abonnements haben. Weitere Informationen finden Sie unter [Verwenden von Office 365 mit Azure RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-o365/).

<!--HONumber=35.2-->
