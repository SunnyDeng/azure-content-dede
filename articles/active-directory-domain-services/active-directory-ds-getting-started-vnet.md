<properties
	pageTitle="Vorschau der Azure Active Directory-Domänendienste: Erste Schritte | Microsoft Azure"
	description="Erste Schritte mit Azure Active Directory-Domänendiensten"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/09/2015"
	ms.author="maheshu"/>

# Azure AD-Domänendienste *(Vorschau)* – Erste Schritte

## Richtlinien zur Auswahl eines virtuellen Azure-Netzwerks
Beachten Sie bei der Auswahl eines virtuellen Netzwerks zur Verwendung mit Azure Active Directory-Domänendiensten die folgenden Richtlinien:

- Stellen Sie sicher, dass Sie ein virtuelles Netzwerk in einer Region auswählen, die von den Azure Active Directory-Domänendiensten unterstützt wird. Die aktuelle Liste der unterstützten Azure-Regionen finden Sie auf der Seite [Regionen](https://azure.microsoft.com/regions/#services).
- Wenn Sie ein vorhandenes virtuelles Netzwerk verwenden möchten, stellen Sie sicher, dass es sich um ein regionales virtuelles Netzwerk handelt. Virtuelle Netzwerke, die den Vorgängermechanismus der Affinitätsgruppen verwenden, können nicht mit Azure Active Directory-Domänendiensten eingesetzt werden. Sie müssen [ältere virtuelle Netzwerke zu regionalen virtuellen Netzwerken migrieren](../virtual-networks-migrate-to-regional-vnet.md).
- Wählen Sie das virtuelle Netzwerk aus, das virtuelle Computer hostet oder hosten wird, die Zugriff auf die Azure Active Directory-Domänendienste benötigen. Die Domänendienste können später nicht in ein anderes virtuelles Netzwerk verschoben werden.
- Azure AD-Domänendiensten werden nicht von virtuellen Netzwerken unterstützt, die mit dem Azure-Ressourcen-Manager erstellt wurden.


## Schritt 2: Erstellen eines virtuellen Azure-Netzwerks
Im nächsten Konfigurationsschritt wird ein virtuelles Azure-Netzwerk erstellt, in dem Sie Azure Active Directory-Domänendienste aktivieren möchten. Wenn Sie bereits über ein vorhandenes virtuelles Netzwerk verfügen, das Sie verwenden möchten, können Sie diesen Schritt überspringen.

> [AZURE.NOTE]Stellen Sie sicher, dass das virtuelle Azure-Netzwerk, das Sie für Azure Active Directory-Domänendienste erstellen oder verwenden möchten, einer Azure-Region angehört, die von Azure Active Directory-Domänendiensten unterstützt wird. Auf unserer Seite [Regionen](active-directory-ds-regions.md) finden Sie eine Liste der Azure-Regionen, in denen Azure AD-Domänendienste verfügbar sind.

Sie müssen sich den Namen des virtuellen Netzwerks notieren, damit Sie das richtige virtuelle Netzwerk auswählen, wenn Sie Azure Active Directory-Domänendienste in einem nachfolgenden Konfigurationsschritt aktivieren.

Führen Sie die folgenden Konfigurationsschritte durch, um ein virtuelles Azure-Netzwerk zu erstellen, in dem Sie Azure Active Directory-Domänendienste aktivieren möchten.

1. Navigieren Sie zum **Azure-Verwaltungsportal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Wählen Sie den Knoten **Netzwerke** auf der linken Seite aus.
3. Klicken Sie im Aufgabenbereich unten auf der Seite auf **NEU**.

    ![Knoten “Virtuelle Netzwerke“](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Wählen Sie im Knoten **Network Services** den Eintrag **Virtual Network** aus.
5. Klicken Sie auf **Schnellerfassung** um ein virtuelles Netzwerk zu erstellen.

    ![Virtuelles Netzwerk – Schnellerfassung](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Geben Sie einen **Namen** für Ihr virtuelles Netzwerk an. Optional können Sie den **Adressraum** oder die **maximale Anzahl virtueller Computer** für dieses Netzwerk konfigurieren. Sie können die DNS-Servereinstellung vorerst auf "Kein" festgelegt lassen. Diese Einstellung wird nach der Aktivierung der Azure Active Directory-Domänendienste aktualisiert.
7. Stellen Sie sicher, dass Sie in der Dropdownliste **Standort** eine unterstützte Azure-Region auswählen. Auf unserer Seite [Regionen](active-directory-ds-regions.md) finden Sie eine Liste der Azure-Regionen, in denen Azure AD-Domänendienste verfügbar sind. Dies ist ein wichtiger Schritt. Wenn Sie ein virtuelles Netzwerk in einer Azure-Region auswählen, die von den Azure Active Directory-Domänendiensten nicht unterstützt wird, können Sie den Dienst in diesem virtuellen Netzwerk nicht aktivieren.
8. Klicken Sie auf die Schaltfläche **Virtuelles Netzwerk erstellen**, um das virtuelle Netzwerk zu erstellen.

    ![Erstellen Sie ein virtuelles Netzwerk für Azure Active Directory-Domänendienste.](./media/active-directory-domain-services-getting-started/create-vnet.png)

---
[**Nächster Schritt: Aktivieren der Azure AD-Domänendienste**](active-directory-ds-getting-started-enableaadds.md)

<!---HONumber=Nov15_HO3-->