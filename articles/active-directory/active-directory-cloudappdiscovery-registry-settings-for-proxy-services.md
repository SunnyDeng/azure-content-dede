<properties 
	pageTitle="Cloud App Discovery-Registrierungseinstellungen für Proxydienste | Microsoft Azure" 
	description="In diesem Thema werden die Schritte beschrieben, die Sie ausführen müssen, um den erforderlichen Port auf den Computern mit dem Cloud App Discovery-Agent festzulegen." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="markusvi"/>

# Cloud App Discovery-Registrierungseinstellungen für Proxydienste

Standardmäßig verwendet der Cloud App Discovery-Agent nur die Ports 80 oder 443. Wenn Sie planen, Cloud App Discovery in einer Umgebung mit einem Proxyserver zu installieren, der einen benutzerdefinierten Port (weder 80 noch 443) verwendet, müssen Sie die Agents zur Verwendung dieses Ports konfigurieren. Die Konfiguration basiert auf einem Registrierungsschlüssel.


In diesem Thema werden die Schritte beschrieben, die Sie ausführen müssen, um den erforderlichen Port auf den Computern mit dem Cloud App Discovery-Agent festzulegen.



**Um den Port zu ändern, der vom Computer mit dem Cloud App Discovery-Agent verwendet wird, führen Sie die folgenden Schritte aus:**


1. Starten Sie den Registrierungs-Editor. <br> ![Ausführen](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Navigieren Sie zum folgenden Registrierungsschlüssel, oder erstellen Sie ihn neu: <br> **HKLM\_LOCAL\_MACHINE\\Software\\Microsoft\\Cloud App Discovery\\Endpoint**

3. Erstellen Sie einen neuen Wert vom Typ **mehrteilige Zeichenfolge** namens **Ports**. ![Neu](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Doppelklicken Sie auf den Wert "Ports", um das Dialogfeld **Mehrteilige Zeichenfolge bearbeiten** zu öffnen.


5. Geben Sie im Textfeld "Wertdaten" die folgenden Werte ein, und fügen Sie alle benutzerdefinierten Ports hinzu, die von Ihrer Organisation verwendet werden: <br><br> **80** <br> **8080** <br> **8118** <br> **8888** <br> **81** <br> **12080** <br> **6999** <br> **30606** <br> **31595** <br> **4080** <br> **443** <br> **1110** <br><br> ![Mehrteilige Zeichenfolge bearbeiten](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Klicken Sie auf **OK**, um das Dialogfeld **Mehrteilige Zeichenfolge bearbeiten** zu schließen.



**Weitere Ressourcen**


* [Wie ermittle ich nicht genehmigte Cloud-Apps, die in meiner Organisation verwendet werden?](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=AcomDC_0107_2016-->