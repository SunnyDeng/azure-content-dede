

<properties 
   pageTitle="Übersicht über die Systemüberwachung für Azure Application Gateway | Microsoft Azure"
   description="Weitere Informationen zu den Überwachungsfunktionen in Azure Application Gateway"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Systemüberwachung des Anwendungsgateways – Übersicht 


Azure Application Gateway überwacht standardmäßig die Integrität aller Ressourcen in seinem Back-End-Pool und entfernt automatisch alle als fehlerhaft geltenden Ressourcen aus dem Pool. Application Gateway überwacht die fehlerhaften Instanzen weiterhin und fügt sie dem fehlerfreien Back-End-Pool hinzu, sobald sie verfügbar sind und auf Zustandsüberprüfungen reagieren.

Zusätzlich zur standardmäßigen Überwachung der Integritätsüberprüfung können Sie die Integritätsüberprüfung auch an die Anforderungen Ihrer Anwendung anpassen. In diesem Artikel werden sowohl standardmäßige als auch benutzerdefinierte Integritätsüberprüfungen behandelt.

## Standardmäßige Integritätsüberprüfung

Ein Anwendungsgateway konfiguriert automatisch eine standardmäßige Integritätsüberprüfung, wenn Sie keine benutzerdefinierte Überprüfungskonfiguration einrichten. Das Verhalten der Überwachung funktioniert durch das Erstellen einer HTTP-Anforderung an die für den Back-End-Pool konfigurierten IP-Adressen.

Beispiel: Sie konfigurieren Ihr Anwendungsgateway für die Verwendung von der Back-End-Server A, B und C zum Empfang von HTTP-Netzwerkdatenverkehr an Port 80. Die standardmäßige Integritätsüberwachung testet die drei Server alle 30 Sekunden auf eine fehlerfreie HTTP-Antwort. Eine fehlerfreie HTTP-Antwort weist einen [Statuscode](https://msdn.microsoft.com/library/aa287675.aspx) zwischen 200 und 399 auf.

Wenn die Standardüberprüfung für Server A fehlschlägt, entfernt Application Gateway sie aus dem Back-End-Pool, und der Netzwerkdatenverkehr an diesen Server wird angehalten. Die Standardüberprüfung führt weiterhin alle 30 Sekunden eine Überprüfung für Server A aus. Wenn Server A erfolgreich auf eine Anforderung einer standardmäßigen Integritätsüberprüfung antwortet, wird er dem Back-End-Pool wieder als fehlerfrei hinzugefügt, und der Datenverkehr an den Server startet erneut.

Die Standardüberprüfung untersucht nur http://127.0.0.1:<port>, um den Integritätsstatus zu bestimmen. Wenn Sie die Integritätsüberprüfung für eine benutzerdefinierte URL konfigurieren oder andere Einstellungen ändern möchten, müssen Sie benutzerdefinierte Überprüfungen wie unten beschrieben verwenden.

### Einstellungen für die standardmäßige Integritätsüberprüfung

|Überprüfungseigenschaften | Wert | Beschreibung|
|---|---|---|
| Überprüfungs-URL| http://127.0.0.1/ | URL-Pfad |
| Intervall | 30 | Überprüfungsintervall in Sekunden |
| Timeout | 30 | Zeitüberschreitung der Überprüfung in Sekunden |
| Fehlerhafter Schwellenwert | 3 | Anzahl der Wiederholungsversuche der Überprüfung Ein Back-End-Server wird als außer Betrieb markiert, nachdem die Anzahl der aufeinanderfolgenden fehlgeschlagenen Überprüfungen einen fehlerhaften Schwellenwert erreicht. |


## Benutzerdefinierte Integritätsüberprüfung 

Benutzerdefinierte Überprüfungen ermöglichen Ihnen eine präzisere Kontrolle über die Überwachung des Systemzustands. Bei Verwendung von benutzerdefinierten Überprüfungen können Sie das Überprüfungsintervall, die URL und den zu überprüfenden Pfad konfigurieren und festlegen, wie viele fehlerhafte Antworten akzeptiert werden, bevor die Back-End-Pool-Instanz als fehlerhaft gekennzeichnet wird.


### Einstellungen für die benutzerdefinierte Integritätsüberprüfung

|Überprüfungseigenschaften| Beschreibung|
|---|---|
| Name | Name der Überprüfung Dieser Name wird verwendet, um in den Back-End-HTTP-Einstellungen auf die Überprüfung zu verweisen. |
| Protokoll | Das zum Senden der Überprüfung verwendete Protokoll HTTP ist das einzige gültige Protokoll. |
| Host | Hostname zum Senden der Überprüfung |
| Path | Relativer Pfad der Überprüfung Gültiger Pfad beginnt mit „/“. Überprüfung wird am <protocol>://<host>:<port><path> gesendet, |
| Intervall | Überprüfungsintervall in Sekunden Dies ist das Zeitintervall zwischen zwei aufeinanderfolgenden Überprüfung.| 
| Timeout | Zeitüberschreitung der Überprüfung in Sekunden Überprüfung wird als fehlgeschlagen markiert, wenn innerhalb dieses Zeitraums keine gültige Antwort empfangen wird. |
| Fehlerhafter Schwellenwert | Anzahl der Wiederholungsversuche der Überprüfung Ein Back-End-Server wird als außer Betrieb markiert, nachdem die Anzahl der aufeinanderfolgenden fehlgeschlagenen Überprüfungen einen fehlerhaften Schwellenwert erreicht. |

## Nächste Schritte

Nachdem Sie sich mit der Systemüberwachung des Anwendungsgateways vertraut gemacht haben, können Sie eine [benutzerdefinierte Integritätsüberprüfung](application-gateway-create-probe-ps.md) für den Azure-Ressourcen-Manager oder eine [benutzerdefinierte Integritätsüberprüfung](application-gateway-create-probe-classic-ps.md) für das klassische Azure-Bereitstellungsmodell konfigurieren.

<!---HONumber=AcomDC_0107_2016-->