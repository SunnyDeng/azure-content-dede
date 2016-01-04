
<properties
    pageTitle="Ändern des Azure Active Directory-Mandanten in Azure RemoteApp | Microsoft Azure"
    description="Erfahren Sie, wie Sie den Azure Active Directory-Mandanten ändern, der Azure RemoteApp zugeordnet ist."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="elizapo" />



# Ändern des Azure Active Directory-Mandanten in Azure RemoteApp

Azure RemoteApp ermöglicht Benutzerzugriff mithilfe von Azure Active Directory (Azure AD). Der einzige Azure AD-Mandant, den Sie in Azure RemoteApp verwenden können, ist derjenige, der mit dem Azure-Abonnement verknüpft ist. Sie können das entsprechende Abonnement im Portal auf der Seite **Einstellungen** anzeigen. Prüfen Sie dafür die Spalte **Verzeichnis** auf der Registerkarte **Abonnements**.

> [AZURE.NOTE]Um diese Änderung erfolgreich auszuführen, müssen Sie zunächst alle Benutzer für den vorhandenen Azure Active Directory-Mandanten aus allen Azure RemoteApp-Sammlungen entfernen. Wechseln Sie zu diesem Zweck im Azure-Portal zur Registerkarte **Azure RemoteApp**, und öffnen Sie die einzelnen Azure RemoteApp-Sammlungen. Klicken Sie auf die Registerkarte **Benutzer**, und entfernen Sie Benutzer, die zu Ihrem aktuellen Azure Active Directory-Mandanten gehören. Wiederholen Sie dies für alle vorhandenen Azure RemoteApp-Sammlungen. Ohne diese Schritte ist es nicht möglich, Sammlungen zu erstellen oder zu patchen.

Wenn Sie einen anderen Mandanten verwenden möchten, führen Sie die folgenden Schritte aus, um die Verknüpfung mit Ihrem Abonnement zu ändern:

1. Entfernen Sie im Portal alle Azure AD-Benutzer, denen Sie Zugriff auf die Azure RemoteApp-Sammlungen gewährt haben. (Siehe den vorherigen Hinweis zur Vorgehensweise.)


2. Legen Sie ein Microsoft-Konto (früher Live ID genannt) als Dienstadministrator fest. (Wissen Sie noch nicht, ob Sie bereits der Dienstadministrator sind? Sie können dies herausfinden, indem Sie auf **Einstellungen -> Administratoren** klicken.) Hier sehen, wie Sie dies ändern können:
	1. Klicken Sie rechts oben auf den Benutzer und anschließend auf **Meine Rechnung anzeigen**.
	2. Klicken Sie auf das Abonnement. Führen Sie dann auf der neuen Seite einen Bildlauf nach unten durch, und klicken Sie rechts auf **Abonnementdetails bearbeiten**. (Rechts unten in der Mitte, wenn Ihnen das bei der Suche hilft).
	3. Geben Sie das Microsoft-Konto des Benutzers ein, der Dienstadministrator sein soll.

3. Melden Sie sich nun vom Portal ab, und melden Sie sich erneut mit dem Microsoft-Konto an, das Sie im vorherigen Schritt angegeben haben.


4. Klicken Sie auf **Neu -> App Services -> Active Directory -> Verzeichnis -> Benutzerdefiniert erstellen**.
5. Wählen Sie unter **Verzeichnis** den Eintrag **Vorhandenes Verzeichnis verwenden** aus. Wir müssen Sie nun vom Portal abmelden, weshalb Sie **Ich bin jetzt für die Abmeldung bereit** wählen.
6. Melden Sie sich erneut am Portal als globaler Administrator des Verzeichnisses an, das Sie hinzufügen möchten. (Wenn Sie nicht bereits ein globaler Administrator waren, müssen Sie sich erneut abmelden und dann wieder anmelden.)
7. Bei der Anmeldung werden Sie gefragt, ob Sie Ihren vorhandenen AD-Mandanten mit Ihrem Abonnement verwenden möchten. Klicken Sie auf **Weiter** und dann auf **Jetzt abmelden**.
5. Melden Sie sich wieder an, und kehren Sie zu **Einstellungen -> Abonnements** zurück. Wählen Sie Ihr Abonnement aus, und klicken Sie anschließend auf **Verzeichnis bearbeiten**. Wählen Sie den Azure-AD-Mandanten aus, den Sie verwenden möchten.



Nun können Sie den neuen Azure AD-Mandanten für die Zugriffskontrolle auf das Azure-Abonnement und zum Konfigurieren des Benutzerzugriffs in Azure RemoteApp verwenden.

<!---HONumber=AcomDC_1210_2015-->