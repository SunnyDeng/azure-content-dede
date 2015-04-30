
<properties 
    pageTitle="Ändern des Azure Active Directory-Mandanten in RemoteApp"
    description="Erfahren Sie, wie Sie den Azure Active Directory-Mandanten ändern, der RemoteApp zugeordnet ist." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2015" 
    ms.author="elizapo" />



# Ändern des Azure Active Directory-Mandanten in RemoteApp

RemoteApp  ermöglicht den Benutzerzugriff mithilfe von Azure Active Directory (Azure AD). Der einzige Azure AD-Mandant, den Sie verwenden können, ist derjenige, der mit dem Azure-Abonnement verknüpft ist. Sie können das entsprechende Abonnement im Portal auf der Seite "Einstellungen" anzeigen. Prüfen Sie dafür die Spalte Verzeichnis auf der Registerkarte Abonnements. 

Wenn Sie einen anderen Mandanten verwenden möchten, führen Sie die folgenden Schritte aus, um die Verknüpfung mit Ihrem Abonnement zu ändern:

1. Entfernen Sie im Portal alle Azure AD-Benutzer, denen Sie Zugriff auf die RemoteApp-Dienste gewährt haben.


2. Legen Sie ein Microsoft-Konto (früher als Live-ID bezeichnet) als Dienstadministrator fest. (Prüfen Sie **Einstellungen -> Administratoren**.)
	1. Klicken Sie in der oberen rechten Ecke auf den aktuell angemeldeten Benutzer, und klicken sie anschließend auf **Meine Rechnung anzeigen**.
	2. Wählen Sie Ihr Abonnement, und klicken Sie anschließend auf **Abonnementdetails bearbeiten**.
	3. Nehmen Sie die erforderlichen Änderungen vor.



3. Melden Sie sich vom Portal ab, und melden Sie sich dann erneut am Microsoft-Konto an, das Sie im vorherigen Schritt angegeben haben.


4. Klicken Sie auf **Neu -> App-Dienste -> Active Directory -> Benutzerdefiniert erstellen -> Vorhandenes Verzeichnis verwenden**. Fügen Sie den Azure AD-Mandanten hinzu, den Sie mit diesem Abonnement verknüpfen möchten.


5. Wählen Sie unter **Einstellungen -> Abonnements** Ihr Abonnement, und klicken Sie anschließend auf **Verzeichnis bearbeiten**. Wählen Sie den Azure-AD-Mandanten aus, den Sie verwenden möchten.



Nun können Sie den neuen Azure-AD-Mandanten für die Zugriffskontrolle auf das Azure-Abonnement und zum Konfigurieren des Benutzerzugriffs in RemoteApp verwenden.

<!--HONumber=52-->