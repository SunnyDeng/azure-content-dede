<properties
   pageTitle="Verbinden mit einem Azure Container Service-Cluster | Microsoft Azure"
   description="Verbinden mit einem Azure Container Service-Cluster über einen SSH-Tunnel"
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

Die Mesos und Swarm-Cluster, die von Azure Container Service bereitgestellt werden, stellen REST-Endpunkte bereit. Diese Endpunkte sind jedoch nicht extern verfügbar. Zum Verwalten dieser Endpunkte muss ein SSH-Tunnel erstellt werden. Sobald ein SSH-Tunnel eingerichtet wurde, können Sie Befehle auf die Cluster-Endpunkte anwenden und die Cluster-Benutzeroberfläche über einen Browser auf Ihrem System anzeigen. In diesem Dokument wird die Erstellung eines SSH-Tunnels in Linux, OSX und Windows schrittweise erläutert.

> Sie können eine SSH-Sitzung mit einem Clusterverwaltungssystem erstellen, dies wird jedoch nicht empfohlen. Wenn Sie direkt auf einem Verwaltungssystem arbeiten, besteht das Risiko unbeabsichtigter Konfigurationsänderungen.

## SSH Tunnel unter Linux/OSX

Suchen Sie zunächst nach dem öffentlichen DNS-Namen der Master mit Lastenausgleich. Erweitern Sie hierzu die Ressourcengruppe, sodass jede Ressource angezeigt wird. Suchen Sie die öffentliche IP-Adresse des Masters, und wählen Sie sie aus. Dadurch wird ein Blatt mit Informationen zur öffentlichen IP-Adresse geöffnet, das den DNS-Namen enthält. Speichern Sie diesen Namen für die spätere Verwendung. <br />

![PuTTY-Verbindung](media/pubdns.png)

Öffnen Sie nun eine Shell, und führen Sie den folgenden Befehl aus, wobei Folgendes gilt:

**PORT** ist der Port des Endpunkts, den Sie verfügbar machen möchten. Verwenden Sie 2375 für Swarm und Port 80 für Mesos. **BENUTZERNAME** ist der Benutzername, der bei der Bereitstellung des Clusters angegeben wurde. **DNSPREFIX** ist das DNS-Präfix, der bei der Bereitstellung des Clusters angegeben wurde. **REGION** ist die Region, in der sich die Ressourcengruppe befindet.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Mesos-Tunnel

Öffnen Sie einen Tunnel zu den zu Mesos gehörigen Endpunkten, indem Sie einen Befehl wie den folgenden ausführen.

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Auf die zu Mesos gehörigen Endpunkte kann nun folgendermaßen zugegriffen werden:

- Mesos – `http://localhost/mesos`
- Marathon – `http://localhost/marathon`
- Chronos – `http://localhost/chronos` 

So ähnlich finden Sie die Rest-APIs für jede Anwendung über diesen Tunnel: Marathon – `http://localhost/marathon/v2`. Weitere Informationen zu den verschiedenen verfügbaren APIs finden Sie in der Mesosphere-Dokumentation für die [Marathon-API](https://mesosphere.github.io/marathon/docs/rest-api.html) und die [Chronos-API](https://mesos.github.io/chronos/docs/api.html) sowie in der Apache-Dokumentation für die [Mesos Scheduler-API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

### Swarm-Tunnel

Führen Sie zum Öffnen eines Tunnels zum Swarm-Endpunkt einen Befehl wie diesen aus.

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Nun können Sie die DOCKER\_HOST-Umgebungsvariable folgendermaßen festlegen und Ihre Docker-Befehlszeilenvariante normal verwenden.

```
export DOCKER_HOST=:2375
```

## SSH-Tunnel unter Windows

Zum Erstellen von SSH-Tunneln unter Windows stehen mehrere Optionen zur Verfügung. Dieses Dokument enthält ausführliche Informationen zur Verwendung von PuTTY.

Laden Sie PuTTY auf Ihr Windows-System herunter, und führen Sie die Anwendung aus.

Geben Sie einen Hostnamen ein, der aus dem Administratorbenutzernamen für den Cluster und dem öffentlichen DNS-Namen für den ersten Master im Cluster besteht. Der Hostname sieht wie folgt aus: `adminuser@PublicDNS`. Geben Sie 2200 für den Port ein.

![PuTTY-Verbindung](media/putty1.png)

Wählen Sie `SSH` und `Authentication`, und fügen Sie Ihre private Schlüsseldatei für die Authentifizierung hinzu.

![PuTTY-Verbindung](media/putty2.png)

Wählen Sie `Tunnels` und `configure` für die folgenden weitergeleiteten Ports aus:
- **Quellport:** Ihr Wert (80 für Mesos und 2375 für Swarm)
- **Ziel:** localhost:80 (für Mesos) oder localhost:2375 (für Swarm)

Das folgende Beispiel wird für Mesos konfiguriert, für Docker Swarm ist es jedoch ähnlich.

> Port 80 darf beim Erstellen dieser Tunnel nicht verwendet werden.

![PuTTY-Verbindung](media/putty3.png)

Speichern Sie nach Abschluss des Vorgangs die Verbindungskonfiguration, und verbinden Sie die PuTTY-Sitzung. Wenn eine Verbindung besteht, wird die Portkonfiguration im Ereignisprotokoll für PuTTY angezeigt.

![PuTTY-Verbindung](media/putty4.png)

Wenn der Tunnel für Mesos konfiguriert wurde, kann hier auf den entsprechenden Endpunkt zugegriffen werden:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos` 

Wenn der Tunnel für Docker Swarm konfiguriert wurde, kann über die Docker-Befehlszeilenschnittstelle auf den Swarm-Cluster zugegriffen werden. Sie müssen zunächst eine Windows-Umgebungsvariable mit dem Namen `DOCKER_HOST` und dem Wert ` :2375` konfigurieren.

## Nächste Schritte
 
Bereitstellen und Verwalten von Containern mit Mesos oder Swarm
 
- [Arbeiten mit ACS und Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0309_2016-->