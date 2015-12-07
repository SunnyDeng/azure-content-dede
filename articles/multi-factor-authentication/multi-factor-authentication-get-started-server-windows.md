<properties 
	pageTitle="Windows-Authentifizierung und Azure Multi-Factor Authentication-Server" 
	description="Dies ist die Azure Multi-Factor Authentication-Seite, die bei der Bereitstellung der Windows-Authentifizierung und des Azure Multi-Factor Authentication-Servers Unterstützung bietet." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="billmath"/>

# Windows-Authentifizierung und Azure Multi-Factor Authentication-Server

Mit dem Abschnitt für die Windows-Authentifizierung kann der Administrator die Windows-Authentifizierung für eine oder mehrere Anwendungen aktivieren und konfigurieren. Folgendes ist eine Liste der Dinge, die vor dem Einrichten der Windows-Authentifizierung beachtet werden sollten.

-  Ein Neustart ist erforderlich, bevor Azure Multi-Factor Authentication für Terminaldienste wirksam wird.
-  Wenn "Multi-Factor Authentication-Benutzerabgleich erforderlich" aktiviert ist und Sie nicht in der Benutzerliste aufgeführt sind, können Sie sich nach dem Neustart des Computers nicht anmelden.
-  Vertrauenswürdige IPs hängen davon ab, ob die Anwendung der Client-IP die Authentifizierung bereitstellen kann. Derzeit werden nur Terminaldienste unterstützt.  







>[AZURE.NOTE]Dieses Feature wird nicht unterstützt, um Terminaldienste unter Windows Server 2012 R2 zu sichern.
 



## Um eine Anwendung mit der Windows-Authentifizierung zu sichern, verwenden Sie das folgende Verfahren.

1. Klicken Sie im Azure Multi-Factor Authentication-Server auf das Symbol "Windows-Authentifizierung". ![Windows-Authentifizierung](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Aktivieren Sie das Kontrollkästchen für die Windows-Authentifizierung. Standardmäßig ist dieses Kontrollkästchen deaktiviert.
3. Die Registerkarte "Anwendungen" gibt dem Administrator die Möglichkeit, eine oder mehrere Anwendungen für die Windows-Authentifizierung zu konfigurieren.
4. Wählen Sie einen Server oder eine Anwendung, und geben Sie an, ob der Server/die Anwendung aktiviert ist. Klicken Sie auf OK.
5. Klicken Sie auf die Schaltfläche "Hinzufügen".
6. Auf der Registerkarte "Vertrauenswürdige IP-Adressen" können Sie Azure Multi-Factor Authentication für Windows-Sitzungen überspringen, die aus bestimmten IPs stammen. Wenn Mitarbeiter die Anwendung im Büro und zu Hause verwenden, können Sie z. B. entscheiden, dass deren Telefone für Azure Multi-Factor Authentication im Büro nicht klingeln sollen. Dazu geben Sie das Bürosubnetz als Eintrag "Vertrauenswürdige IPs" an.
7. Klicken Sie auf die Schaltfläche "Hinzufügen".
8. Wählen Sie "Einzelne IP-Adresse", wenn Sie eine einzelne IP-Adresse überspringen möchten.
9. Wählen Sie die "IP-Bereich", wenn Sie einen gesamten IP-Bereich überspringen möchten. Beispiel 10.63.193.1-10.63.193.100.
10. Wählen Sie "Subnetz", wenn Sie einen IP-Bereich mithilfe der Subnetznotation angeben möchten. Geben Sie die Start-IP des Subnetzes an, und wählen Sie die entsprechende Netzmaske aus der Dropdown-Liste. 
11. Klicken Sie auf die Schaltfläche "OK".

<!---HONumber=AcomDC_1125_2015-->