<properties pageTitle="Vorschau von Azure Active Directory-Domänendiensten: Erste Schritte | Microsoft Azure" description="Erste Schritte mit Azure Active Directory-Domänendiensten" services="active-directory-ds" documentationCenter="" authors="mahesh-unnikrishnan" manager="stevenpo editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Azure AD-Domänendienste *(Vorschau)* – Erste Schritte

## Schritt 4: Aktualisieren der DNS-Einstellungen für das virtuelle Azure-Netzwerk
Sie haben nun Azure Active Directory-Domänendienste für Ihr Verzeichnis erfolgreich aktiviert und müssen im nächsten Schritt sicherstellen, dass Computer innerhalb des virtuellen Netzwerks eine Verbindung zu diesen Diensten herstellen und sie nutzen können. Zu diesem Zweck müssen Sie die DNS-Servereinstellungen für Ihr virtuelles Netzwerk so aktualisieren, dass sie auf die IP-Adressen verweisen, unter denen Azure Active Directory-Domänendienste auf dem virtuellen Netzwerk verfügbar sind.

> [AZURE.NOTE] Notieren Sie sich die IP-Adressen für Azure AD-Domänendienste, die auf der Registerkarte **Konfigurieren** für Ihr Verzeichnis angezeigt werden, nachdem Sie Azure AD-Domänendienste für das Verzeichnis aktiviert haben.

Führen Sie die folgenden Konfigurationsschritte aus, um die DNS-Servereinstellung für das virtuelle Netzwerk zu aktualisieren, in dem Sie Azure AD-Domänendienste aktiviert haben.

1. Navigieren Sie zum **Azure-Verwaltungsportal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Wählen Sie den Knoten **Netzwerke** auf der linken Seite aus.

    ![Knoten “Virtuelle Netzwerke“](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. Wählen Sie in der Registerkarte **Virtuelle Netzwerke** das virtuelle Netzwerk aus, in dem Sie Azure AD-Domänendienste aktiviert haben, um dessen Eigenschaften anzuzeigen.
4. Klicken Sie auf die Registerkarte **Konfigurieren**.

    ![Knoten „Virtuelle Netzwerke“](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. Geben Sie im Abschnitt **DNS-Server** die IP-Adressen von Azure AD-Domänendiensten ein.
6. Stellen Sie sicher, dass Sie beide IP-Adressen eingeben, die im Abschnitt **Domänendienste** auf der Registerkarte **Konfigurieren** für Ihr Verzeichnis angezeigt werden.
7. Klicken Sie im Aufgabenbereich am unteren Rand der Seite auf **Speichern**, um die DNS-Servereinstellungen für dieses virtuelle Netzwerk zu speichern.

   ![Aktualisieren Sie die DNS-Servereinstellungen für das virtuelle Netzwerk.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Nach der Aktualisierung der DNS-Servereinstellungen für das virtuelle Netzwerk kann es eine Weile dauern, bis virtuelle Computer im Netzwerk die aktualisierte DNS-Konfiguration erhalten. Wenn ein virtueller Computer keine Verbindung zur Domäne herstellen kann, können Sie den DNS-Cache auf dem virtuellen Computer leeren (z. B. mithilfe von „ipconfig /flushdns“), um eine Aktualisierung der DNS-Einstellungen auf dem virtuellen Computer zu erzwingen.

---
[**Nächster Schritt: Aktivieren der Synchronisierung von Kennwörtern für Azure AD-Domänendienste.**](active-directory-ds-getting-started-password-sync.md)

<!---HONumber=AcomDC_0211_2016-->