<properties
   pageTitle="Problembehandlung beim Status &quot;Heruntergestuft&quot; in Azure Traffic Manager"
   description="Problembehandlung bei Traffic Manager-Profilen, bei denen der Status &quot;Heruntergestuft&quot; angezeigt wird."
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="adinah"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="joaoma" />
# Problembehandlung beim Status "Heruntergestuft" in Azure Traffic Manager
Auf dieser Seite wird beschrieben, wie Probleme bei einem Azure Traffic Manager-Profil mit dem Status "Heruntergestuft" behoben werden können. Weiterhin werden einige wichtige Punkte zu Traffic Manager-Tests erörtert.


Sie haben ein Traffic Manager-Profil konfiguriert, das auf einige Ihrer in cloudapp.net gehosteten Dienste verweist. Nach einigen Sekunden wird der Status als "Heruntergestuft" angezeigt.

![Status "Heruntergestuft"](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Auf der Registerkarte "Endpunkte" dieses Profils können Sie sehen, dass für einen oder mehrere Endpunkte der Status "Offline" angezeigt wird:

![Offline](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## Wichtige Hinweise zu Traffic Manager-Tests

- In Traffic Manager wird ein Endpunkt nur als ONLINE eingestuft, wenn beim Test eine Antwort "200" vom Testpfad zurückgegeben wird.
- Eine 30x-Umleitung-Antwort (oder jede andere von "200" abweichende Antwort) schlägt fehl, selbst wenn die umgeleitete URL eine Antwort "200" zurückgibt.

- Bei HTTPs-Tests werden Zertifikatfehler ignoriert.
 
- Der eigentliche Inhalt des Testpfads ist unerheblich, solange eine Antwort "200" zurückgegeben wird. Wenn der eigentliche Websiteinhalt keine Antwort "200" zurückgibt (d. h., wenn über die ASP-Seiten eine Umleitung an eine ACS-Anmeldeseite oder eine andere CNAME-URL erfolgt), besteht eine gängige Methode darin, den Pfad beispielsweise auf "/favicon.ico" festzulegen.
 
- Es wird empfohlen, den Testpfad mit ausreichender Logik festzulegen, sodass ermittelt werden kann, ob die Website aktiv oder inaktiv ist. Durch Festlegen des Pfads im Beispiel oben auf "/favicon.ico" wird lediglich getestet, ob "w3wp.exe" antwortet, jedoch nicht, ob die Website fehlerfrei ist. Besser wäre es, den Pfad beispielsweise auf "/Probe.aspx" festzulegen und in "Probe.aspx" ausreichend Logik einzufügen, dass ermittelt wird, ob die Website fehlerfrei ist (d. h. Überprüfen der Leistungsindikatoren, um sicherzustellen, dass keine CPU-Auslastung von 100 % vorliegt oder keine große Anzahl an fehlerhaften Anforderungen empfangen wird; oder Zugreifen auf Ressourcen wie z. B. den Datenbank- oder Sitzungszustand, um sicherzustellen, dass die Anwendungslogik funktioniert usw.).
 
- Wenn alle Endpunkte in einem Profil heruntergestuft sind, behandelt Traffic Manager alle Endpunkte als fehlerfrei und leitet den Datenverkehr an alle Endpunkte weiter. Dadurch wird sichergestellt, dass ein potenzielles Problem im Testmechanismus, das zu fälschlicherweise fehlerhaften Testergebnissen führt, nicht auch zu einem vollständigen Ausfall des Diensts führt.

  

## Problembehandlung

Ein Tool zum Beheben von Fehlern bei Traffic Manager-Tests ist Wget. Sie können das Paket für Binärdateien und Abhängigkeiten unter [Wget](http://gnuwin32.sourceforge.net/packages/wget.htm) herunterladen. Beachten Sie, dass Sie statt Wget auch andere Programme wie Fiddler oder curl verwenden können. Im Grunde benötigen Sie nur eine Anwendung, mit der die unverarbeitete HTTP-Antwort angezeigt wird.

Wenn Sie Wget installiert haben, öffnen Sie eine Eingabeaufforderung, und führen Sie Wget für die URL sowie den Testport und Testpfad aus, die in Traffic Manager konfiguriert sind. In diesem Beispiel ist dies http://watestsdp2008r2.cloudapp.net:80/Probe.

![Problembehandlung](./media/traffic-manager-troubleshooting-degraded/traffic-manager-troubleshooting.png)

Verwenden von Wget:

![Wget](./media/traffic-manager-troubleshooting-degraded/traffic-manager-wget.png)

 

Beachten Sie, dass Wget angibt, dass die URL eine 301-Umleitung an http://watestsdp2008r2.cloudapp.net/Default.aspx zurückgegeben hat. Wie wir aus dem Abschnitt "Wichtige Hinweise zu Traffic Manager-Tests" oben wissen, gilt eine 30x-Umleitung-Antwort in den Traffic Manager-Tests als Fehler, sodass im Test der Status "Offline" angezeigt wird. An dieser Stelle kann einfach die Websitekonfiguration überprüft werden, um sicherzustellen, dass aus dem Pfad "/Probe" eine Antwort "200" zurückgegeben wird (oder konfigurieren Sie den Traffic Manager-Test so, dass er auf einen Pfad verweist, der eine Antwort "200" zurückgibt).

 

Wenn bei dem Test das HTTPs-Protokoll verwendet wird, sollten Sie in Wget den Parameter "--no-check-certificate" hinzufügen, damit Zertifikatkonflikte in der cloudapp.net-URL ignoriert werden.


## Nächste Schritte


[Informationen zu Lastenausgleichsmethoden von Traffic Manager](traffic-manager-load-balancing-methods.md)

[Traffic Manager – Übersicht](../traffic-manmager-overview.md)

[Cloud-Dienste](http://go.microsoft.com/fwlink/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=July15_HO2-->