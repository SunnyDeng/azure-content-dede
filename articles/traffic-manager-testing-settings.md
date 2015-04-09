<properties 
   pageTitle="Testen von Traffic Manager-Einstellungen"
   description="In diesem Artikel erfahren Sie, wie Sie die Traffic Manager-Einstellungen testen."
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Testen von Traffic Manager-Einstellungen

Die beste Möglichkeit, um Ihre Traffic Manager-Einstellungen zu testen, besteht darin, einige Clients einzurichten und die im Profil enthaltenen Endpunkte, die aus Cloud-Diensten und Websites bestehen, nacheinander zu beenden. Die folgenden Tipps unterstützen Sie dabei, das Traffic Manager-Profil zu testen.

## Grundlegende Testschritte

- **Legen Sie die DNS-Gültigkeitsdauer (TTL) auf einen sehr niedrigen Wert fest**, damit Änderungen schnell weitergegeben werden, beispielsweise auf 30 Sekunden.
- **Notieren Sie sich die IP-Adresse der Azure-Cloud-Services und -Websites** in dem von Ihnen getesteten Profil.
- **Verwenden Sie Tools, mit denen Sie einen DNS-Namen in eine IP-Adresse auflösen** und anzeigen können. Überprüfen Sie, ob der Name Ihrer Unternehmensdomäne in IP-Adressen der im Profil enthaltenen Endpunkte aufgelöst wird. Die Auflösung sollte in Übereinstimmung mit der Lastenausgleichsmethode Ihres Traffic Manager-Profils erfolgen. Wenn Sie einen Computer unter Windows verwenden, können Sie das Tool "Nslookup.exe" an einer Eingabeaufforderung oder Windows PowerShell-Eingabeaufforderung ausführen. Weitere öffentlich verfügbare Tools, mit denen Sie eine IP-Adresse herausfinden können, sind ebenfalls im Internet verfügbar.

### So überprüfen Sie ein Traffic Manager-Profil mit "nslookup"

1. Öffnen Sie eine Eingabeaufforderung oder eine Windows PowerShell-Eingabeaufforderung als Administrator.
2. Geben Sie `ipconfig /flushdns` ein, um den DNS-Resolvercache zu löschen.
3. Geben Sie `nslookup <your Traffic Manager domain name>` ein. Beispielsweise überprüft der folgende Befehl den Domänennamen mit dem Präfix *myapp.contoso*.
    nslookup myapp.contoso.trafficmanager.net
   Das Ergebnis sieht in der Regel wie folgt aus:
   - Der DNS-Name und die IP-Adresse des DNS-Servers, auf den zugegriffen wird, um diesen Traffic Manager-Domänennamen aufzulösen.
   - Der Traffic Manager-Domänenname, der in die Befehlszeile nach "nslookup" eingegeben wurde, sowie die IP-Adresse, in die die Traffic Manager-Domäne aufgelöst wird. Die zweite IP-Adresse ist für die Überprüfung wichtig. Es ist vor allem wichtig, die zweite IP-Adresse zu überprüfen. Diese sollte einer öffentlichen virtuelle IP-Adresse (VIP-Adresse) eines der Cloud-Dienste oder einer Website im Traffic Manager-Profil entsprechen, das Sie testen.

## Testen von Lastenausgleichsmethoden

### So testen Sie eine Failover-Lastenausgleichsmethode

1. Alle Endpunkte müssen ausgeführt werden.
2. Verwenden Sie einen einzelnen Client.
3. Fordern Sie die DNS-Auflösung für den Namen Ihrer Unternehmensdomäne mithilfe des Tools "Nslookup.exe" oder eines ähnlichen Hilfsprogramms an.
4. Stellen Sie sicher, dass die aufgelöste IP-Adresse, die Sie abrufen, zum primären Endpunkt gehört.
5. Fahren Sie den primären Endpunkt herunter bzw. entfernen Sie die Überwachungsdatei, sodass Traffic Manager davon ausgeht, dass der Dienst nicht in Betrieb ist.
6. Warten Sie den Zeitraum der im Traffic Manager-Profile angegebenen DNS-Gültigkeitsdauer zuzüglich weiterer zwei Minuten ab. Wenn Ihre DNS-Gültigkeitsdauer z. B. 300 Sekunden (fünf Minuten) beträgt, müssen Sie sieben Minuten warten.
7. Leeren Sie den DNS-Clientcache, und fordern Sie dann DNS-Auflösung an. In Windows können Sie den DNS-Cache mit dem Befehl "ipconfig /flushdns" leeren, den Sie an einer Eingabeaufforderung oder einer Windows PowerShell-Eingabeaufforderung ausgeben.
8. Stellen Sie sicher, dass die abgerufene IP-Adresse zum sekundären Endpunkt gehört.
9. Wiederholen Sie diesen Vorgang, indem Sie den sekundären Endpunkt beenden, dann den tertiären Endpunkt usw. In jedem dieser Fälle muss sichergestellt werden, dass von der DNS-Auflösung die IP-Adresse des nächsten Endpunkts in der Liste zurückgegeben wird. Wenn keiner der Endpunkte mehr betriebsbereit ist, sollte erneut die IP-Adresse des primären Endpunkts abgerufen werden.

### So testen Sie eine Roundrobin-Lastenausgleichsmethode

1. Alle Endpunkte müssen ausgeführt werden.
2. Verwenden Sie einen einzelnen Client.
3. Fordern Sie die DNS-Auflösung für Ihre Unternehmensdomäne mithilfe des Tools "Nslookup.exe" oder eines ähnlichen Hilfsprogramms an.
4. Stellen Sie sicher, dass die erhaltene IP-Adresse eine Adresse aus Ihrer Liste ist.
5. Leeren Sie den DNS-Clientcache, und wiederholen Sie dann immer wieder die Schritte 3 und 4. Für jeden Ihrer Endpunkte sollte eine andere IP-Adresse zurückgegeben werden. Anschließend wird der Prozess wiederholt.

### So testen Sie die Lastenausgleichsmethode "Leistung"

Um die Lastenausgleichsmethode "Leistung" effizient zu testen, müssen Sie über Clients an verschiedenen Orten weltweit verfügen. Sie können Clients in Azure erstellen, die versuchen, Ihre Dienste mithilfe des Domänennamens Ihres Unternehmens aufzurufen. Bei einem globalen Unternehmen können Sie sich auch remote bei Clients in anderen Teilen der Welt anmelden und den Test über diese Clients durchführen.

Sie können kostenlose webbasierte DNS-Lookup- und Analysedienste verwenden. Mit einigen davon können Sie die DNS-Namensauflösung von verschiedenen Standorten aus überprüfen. Führen Sie beispielsweise eine Suche nach "DNS-Lookup" aus, um Beispiele zu erhalten. Sie können auch eine Drittanbieterlösung wie Gomez oder Keynote verwenden, um sicherzustellen, dass der Datenverkehr von den Profilen wie erwartet verteilt wird.

## Siehe auch

[Informationen zu Lastenausgleichsmethoden von Traffic Manager](traffic-manager-load-balancing-methods.md)

[Traffic Manager-Konfigurationsaufgaben](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Traffic Manager - Übersicht](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/?LinkId=313584)


<!--HONumber=49-->