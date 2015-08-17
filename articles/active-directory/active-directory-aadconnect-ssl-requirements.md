<properties 
	pageTitle="Azure AD Connect – SSL-Zertifikatanforderungen" 
	description="Azure AD Connect – SSL-Zertifikatanforderungen für die Verwendung mit AD FS." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect – SSL-Zertifikatanforderungen

**Wichtig:** Es wird dringend empfohlen, dasselbe SSL-Zertifikat für alle Knoten der AD FS-Farm sowie alle Webanwendungsproxy-Server zu verwenden.

- Dieses Zertifikat muss ein X.509-Zertifikat sein. 
- In einer Testumgebung können Sie ein selbstsigniertes Zertifikat für Verbundserver verwenden. Für eine Produktionsumgebung empfehlen wir jedoch, dass Sie das Zertifikat von einer öffentlichen Zertifizierungsstelle beziehen. 
	- Wenn Sie ein Zertifikat verwenden, das nicht öffentlich vertrauenswürdig ist, stellen Sie sicher, dass dem auf jedem Webanwendungsproxy-Server installierten Zertifikat sowohl auf dem lokalen Server als auch auf allen Verbundservern vertraut wird. 
- Auf CNG-Schlüsseln (CryptoAPI Next Generation) und Schlüsselspeicheranbietern basierende Zertifikate werden nicht unterstützt. Dies bedeutet, dass Sie ein CSP-basiertes Zertifikat (Kryptografiedienstanbieter) verwenden müssen, kein Zertifikat von einem Schlüsselspeicheranbieter. 
- Die Identität des Zertifikats muss mit dem Namen des Verbunddiensts (z. B. "fs.contoso.com") übereinstimmen. 
	- Die Identität ist die Erweiterung eines alternativen Antragstellernamens (SAN) des Typs "dNSName". Wenn keine SAN-Einträge vorhanden sind, wird der Name des Antragstellers als allgemeiner Name angegeben.  
	- Im Zertifikat können mehrere SAN-Einträge hinterlegt werden, sofern einer von ihnen mit dem Namen des Verbunddiensts übereinstimmt. 
	- Wenn Sie die Arbeitsbereichverknüpfung verwenden möchten, ist ein zusätzlicher SAN mit dem Wert "enterpriseregistration" gefolgt vom UPN-Suffix des Unternehmens erforderlich, z. B. "enterpriseregistration.contoso.com". 

Platzhalterzertifikate werden unterstützt.
 

<!---HONumber=August15_HO6-->