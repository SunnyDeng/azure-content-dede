<properties
   pageTitle="Verbinden mit einem Azure Container Service-Cluster | Microsoft Azure"
   description="Erfahren Sie, wie Sie die Verbindung mit einem Azure Container Service-Cluster über einen SSH-Tunnel herstellen."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Container, Microservices, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>


# Verbinden mit einem Azure Container Service-Cluster

Für die Mesos- und Swarm-Cluster, die vom Azure Container Service bereitgestellt werden, werden REST-Endpunkte verfügbar gemacht. Diese Endpunkte sind aber nicht öffentlich zugänglich. Zum Verwalten dieser Endpunkte müssen Sie einen SSH-Tunnel (Secure Shell) erstellen. Sobald ein SSH-Tunnel eingerichtet wurde, können Sie Befehle auf die Cluster-Endpunkte anwenden und die Cluster-Benutzeroberfläche über einen Browser auf Ihrem System anzeigen. In diesem Dokument wird die Erstellung eines SSH-Tunnels in Linux, OS X und Windows schrittweise erläutert.

>[AZURE.NOTE] Sie können eine SSH-Sitzung mit einem Clusterverwaltungssystem erstellen. Dies ist aber nicht zu empfehlen. Wenn Sie direkt auf einem Verwaltungssystem arbeiten, besteht das Risiko unbeabsichtigter Konfigurationsänderungen.

## Erstellen eines SSH-Tunnels unter Linux oder OS X

Als Erstes ermitteln Sie beim Erstellen eines SSH-Tunnels unter Linux oder OS X den öffentlichen DNS-Namen von Masterelementen mit Lastenausgleich. Erweitern Sie hierzu die Ressourcengruppe, damit jede Ressource angezeigt wird. Suchen Sie die öffentliche IP-Adresse des Masters, und wählen Sie sie aus. Es wird ein Blatt mit Informationen zur öffentlichen IP-Adresse geöffnet, das den DNS-Namen enthält. Speichern Sie diesen Namen für die spätere Verwendung. <br />

![Öffentlicher DNS-Name](media/pubdns.png)

Öffnen Sie nun eine Shell, und führen Sie den folgenden Befehl aus, wobei Folgendes gilt:

**PORT** ist der Port des Endpunkts, den Sie verfügbar machen möchten. Für Swarm ist dies 2375. Verwenden Sie für Mesos Port 80. **BENUTZERNAME** ist der Benutzername, der bei der Bereitstellung des Clusters angegeben wurde. **DNSPREFIX** ist das DNS-Präfix, das bei der Bereitstellung des Clusters angegeben wurde. **REGION** ist die Region, in der sich die Ressourcengruppe befindet.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Mesos-Tunnel

Öffnen Sie einen Tunnel zu den zu Mesos gehörigen Endpunkten, indem Sie einen Befehl wie den folgenden ausführen:

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Nun können Sie wie folgt auf die zu Mesos gehörigen Endpunkte zugreifen:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

Sie erreichen die REST-APIs für jede Anwendung über diesen Tunnel auf ähnliche Weise: Marathon – `http://localhost/marathon/v2`. Weitere Informationen zu den verschiedenen APIs, die verfügbar sind, finden Sie in der Mesosphere-Dokumentation zur [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html). Lesen Sie sich die Informationen zur [Chronos API](https://mesos.github.io/chronos/docs/api.html) und in der Apache-Dokumentation zur [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) durch.

### Swarm-Tunnel

Führen Sie zum Öffnen eines Tunnels zum Swarm-Endpunkt einen Befehl wie diesen aus:

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Nun können Sie die DOCKER\_HOST-Umgebungsvariable wie folgt festlegen und Ihre Docker-Befehlszeilenschnittstelle normal verwenden.

```
export DOCKER_HOST=:2375
```

## Erstellen eines SSH-Tunnels unter Windows

Es gibt mehrere Möglichkeiten, wie Sie SSH-Tunnel unter Windows erstellen können. In diesem Dokument wird beschrieben, wie Sie PuTTY verwenden, um dies zu erreichen.

Laden Sie PuTTY auf Ihr Windows-System herunter, und führen Sie die Anwendung aus.

Geben Sie einen Hostnamen ein, der aus dem Administratorbenutzernamen für den Cluster und dem öffentlichen DNS-Namen für den ersten Master im Cluster besteht. Der **Hostname** sieht wie folgt aus: `adminuser@PublicDNS`. Geben Sie 2200 als **Port** ein.

![PuTTY-Konfiguration 1](media/putty1.png)

Wählen Sie `SSH` und `Authentication`. Fügen Sie Ihre private Schlüsseldatei für die Authentifizierung hinzu.

![PuTTY-Konfiguration 2](media/putty2.png)

Wählen Sie `Tunnels`, und konfigurieren Sie die folgenden weitergeleiteten Ports:
- **Quellport:** Ihr Wert – 80 für Mesos oder 2375 für Swarm verwenden.
- **Ziel:** localhost:80 für Mesos oder localhost:2375 für Swarm verwenden.

Das folgende Beispiel ist für Mesos konfiguriert, sieht für Docker Swarm aber ähnlich aus.

>[AZURE.NOTE] Port 80 darf beim Erstellen dieses Tunnels nicht verwendet werden.

![PuTTY-Konfiguration 3](media/putty3.png)

Speichern Sie nach Abschluss des Vorgangs die Verbindungskonfiguration, und stellen Sie die Verbindung mit der PuTTY-Sitzung her. Beim Herstellen der Verbindung können Sie die Portkonfiguration im PuTTY-Ereignisprotokoll anzeigen.

![PuTTY-Ereignisprotokoll](media/putty4.png)

Wenn Sie den Tunnel für Mesos konfiguriert haben, können Sie wie folgt auf den dazugehörigen Endpunkt zugreifen:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

Wenn Sie den Tunnel für Docker Swarm konfiguriert haben, können Sie über die Docker-Befehlszeilenschnittstelle auf den Swarm-Cluster zugreifen. Sie müssen zunächst eine Windows-Umgebungsvariable mit dem Namen `DOCKER_HOST` und dem Wert ` :2375` konfigurieren.

## Nächste Schritte

Bereitstellen und Verwalten von Containern mit Mesos oder Swarm

- [Verwenden von Azure Container Service und Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0323_2016-->