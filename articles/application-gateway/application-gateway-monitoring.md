<properties 
   pageTitle="Konfigurieren eines Application Gateways für benutzerdefinierte Überprüfungen mit Azure-Ressourcen-Manager | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Konfigurieren von benutzerdefinierten Überprüfungen für Azure Application Gateway mit Azure-Ressourcen-Manager."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Systemüberwachung und benutzerdefinierte Überprüfungen 


Azure Application Gateway überwacht die Integrität aller Ressourcen in seinem Back-End-Pool und entfernt automatisch alle als fehlerhaft geltenden Ressourcen aus dem Pool.

Sie konfigurieren die Systemüberwachung mit zwei Arten von Überprüfungen: standardmäßige Integritätsüberprüfung und benutzerdefinierte Überprüfung.

## Standardmäßige Integritätsüberprüfung

Ein Anwendungsgateway konfiguriert automatisch eine standardmäßige Integritätsüberprüfung, wenn Sie keine benutzerdefinierte Überprüfungskonfiguration einrichten. Das Überwachungsverhalten basiert auf dem Senden einer HTTP-Anforderung an die IP-Adressen, die für den Back-End-Pool konfiguriert sind, sowie auf dem Port, der unter der HTTP-Einstellung des Back-Ends für das Application Gateway konfiguriert ist.

Beispiel: Sie konfigurieren Ihr Anwendungsgateway für die Verwendung von der Back-End-Server A, B und C zum Empfang von HTTP-Netzwerkdatenverkehr an Port 80. Die standardmäßige Integritätsüberwachung testet die drei Server alle 30 Sekunden auf eine fehlerfreie HTTP-Antwort. Eine fehlerfreie HTTP-Antwort weist einen [Statuscode](https://msdn.microsoft.com/library/aa287675.aspx) zwischen 200 und 399 auf.

Wenn die Standardüberprüfung für Server A fehlschlägt, entfernt das Application Gateway sie aus dem Back-End-Pool, und der Netzwerkdatenverkehr an diesen Server wird angehalten. Die Standardüberprüfung führt weiterhin alle 30 Sekunden eine Überprüfung für Server A aus. Wenn Server A erfolgreich auf eine Anforderung einer standardmäßigen Integritätsüberprüfung antwortet, wird er dem Back-End-Pool wieder als fehlerfrei hinzugefügt, und der Datenverkehr an den Server startet erneut.

Für die Standardüberprüfung werden nur die IP-Adressen zum Überprüfen des Status verwendet. Wenn Sie die Integrität überprüfen möchten, indem Sie die Verbindung mit einer URL testen, müssen Sie eine benutzerdefinierte Überprüfung verwenden.


## Benutzerdefinierte Überprüfung 

Benutzerdefinierte Überprüfungen ermöglichen Ihnen eine präzisere Kontrolle über die Überwachung des Systemzustands. Bei Verwendung von benutzerdefinierten Überprüfungen können Sie das Überprüfungsintervall, die URL und den zu überprüfenden Pfad konfigurieren und festlegen, wie viele fehlerhafte Antworten akzeptiert werden, bevor die Back-End-Pool-Instanz als fehlerhaft gekennzeichnet wird.


Einstellungen der benutzerdefinierten Überprüfung:

- **Testintervall**: Konfiguriert die Testintervallüberprüfungen.
- **Test-Timeout**: Dient zum Definieren des Test-Timeouts für eine Überprüfung von HTTP-Anforderungen.
- **Fehlerhafter Schwellenwert**: Gibt an, nach wie vielen fehlgeschlagenen Anforderungen die Instanz als fehlerhaft gekennzeichnet wird.  
- **Hostname und Pfad**: Falls Ihre Website oder Webfarm nicht über eine HTTP-Antwort ausschließlich für die IP-Adresse verfügt, müssen Sie für eine gültige, fehlerfreie HTTP-Antwort den Hostnamen und Pfad der Überprüfung konfigurieren. Beispiel: Sie verfügen über die Website http://contoso.com/, aber dies führt nicht zu einer gültigen HTTP-Antwort. Es müssen ein Hostname und ein Pfad konfiguriert werden, um die gültige, fehlerfreie HTTP-Antwort anzugeben, damit überprüft werden kann, ob die Webserverinstanz fehlerfrei ist. In diesem Fall können Sie die benutzerdefinierte Überprüfung für „http://contoso.com/path/custompath.htm“ konfigurieren, damit die HTTP-Antwort bei Überprüfungen erfolgreich ist. 



>[AZURE.NOTE] Benutzerdefinierte Überprüfungen können nur mit PowerShell konfiguriert werden

<!---HONumber=AcomDC_0128_2016-->