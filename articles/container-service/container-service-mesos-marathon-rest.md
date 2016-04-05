<properties
   pageTitle="ACS-Containerverwaltung mit der REST-API | Microsoft Azure"
   description="Stellen Sie mithilfe der Marathon-REST-API Container für einen Azure Container Service-Mesos-Cluster bereit."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
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
   ms.author="nepeters"/>
   
# Containerverwaltung mit der REST-API

Mesos stellt eine Umgebung für die Bereitstellung und Skalierung geclusterter Workloads bereit und abstrahiert die zugrunde liegende Hardware. Neben Mesos verwalten auch Frameworks die Planung und Ausführung von Computeworkload. Frameworks sind zwar für viele gängige Workloads verfügbar, in diesem Dokument wird jedoch das Erstellen und Skalieren von Containerbereitstellungen mit Marathon erläutert.

Bevor Sie diese Beispiele durchgehen, benötigen Sie einen in ACS konfigurierten Mesos-Cluster und eine Remoteverbindung mit diesem Cluster. Weitere Informationen zu diesen Elementen finden Sie in den folgenden Artikeln:

- [Bereitstellen eines Azure Container Service-Clusters](./container-service-deployment.md) 
- [Herstellen der Verbindung mit einem ACS-Cluster](./container-service-connect.md)


Sobald eine Verbindung mit dem ACS-Cluster hergestellt wurde, kann über http://localhost:local-port auf Mesos und die zugehörigen REST-APIs zugegriffen werden. Bei den Beispielen in diesem Dokument wird davon ausgegangen, dass das Tunneling über Port 80 erfolgt. Der Marathon-Endpunkt kann beispielsweise unter `http://localhost/marathon/v2/` erreicht werden. Weitere Informationen zu den verschiedenen APIs finden Sie in der Mesosphere-Dokumentation für die [Marathon-API](https://mesosphere.github.io/marathon/docs/rest-api.html) und die [Chronos-API](https://mesos.github.io/chronos/docs/api.html) sowie in der Apache-Dokumentation für die [Mesos Scheduler-API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## Erfassen von Informationen von Mesos und Marathon

Erfassen Sie vor dem Bereitstellen von Containern im Mesos-Cluster einige Informationen zum Mesos-Cluster, z. B. die Namen und den aktuellen Status der Mesos-Agents. Fragen Sie dazu den `master/slaves`-Endpunkt der Mesos-REST-API ab. Wenn alles gut geht, wird eine Liste der Mesos-Agents mit jeweils einigen Eigenschaften angezeigt.

```bash
curl http://localhost/mesos/master/slaves
```

Verwenden Sie nun den Marathon-`/apps`-Endpunkt, um aktuelle Anwendungsbereitstellungen im Mesos-Cluster zu überprüfen. Wenn es sich um einen neuen Cluster handelt, wird ein leeres Array für Apps angezeigt.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Bereitstellen eines Containers im Docker-Format

Container im Docker-Format werden über Marathon mithilfe einer JSON-Datei bereitgestellt, die die vorgesehene Bereitstellung beschreibt. Im folgenden Beispiel wird der nginx-Container bereitgestellt; dabei wird Port 80 des Mesos-Agents an Port 80 des Containers gebunden.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Zum Bereitstellen eines Containers im Docker-Format erstellen Sie eine eigene JSON-Datei, oder verwenden Sie das hier bereitgestellte Beispiel ([Azure ACS Demo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)), und speichern Sie es an einem zugänglichen Speicherort. Führen Sie anschließend den folgenden Befehl aus, und geben Sie den Namen der JSON-Datei an, um den Container bereitzustellen.

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
```

Die Ausgabe sieht etwa wie folgt aus:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Wenn Sie jetzt Marathon auf Anwendungen abfragen, wird diese neue Anwendung in der Ausgabe angezeigt.

```
curl localhost/marathon/v2/apps
```

## Skalieren der Container

Die Marathon-API kann auch zum horizontalen Hoch- oder Herunterskalieren von Anwendungsbereitstellungen verwendet werden. Im vorherigen Beispiel wurde eine Instanz einer Anwendung bereitgestellt; diese skalieren wir jetzt horizontal auf drei Instanzen hoch. Dazu erstellen Sie eine JSON-Datei mit dem folgenden JSON-Text und speichern sie an einem zugänglichen Speicherort.

```json
{ "instances": 3 }
```

Führen Sie den folgenden Befehl aus, um die Anwendung zu horizontal hochzuskalieren.

> Hinweis: Der URI ist http://localhost/marathon/v2/apps/ und anschließend die ID der zu skalierenden Anwendung. Wenn Sie das hier bereitgestellte nginx-Beispiel verwenden, lautet der URI http://localhost/v2/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Fragen Sie schließlich den Marathon-Endpunkt für Anwendungen ab. Sie werden feststellen, dass nun drei nginx-Container vorhanden sind.

```
curl localhost/marathon/v2/apps
```

## Marathon-REST-API-Interaktion mit PowerShell

Dieselbe Aktion kann mithilfe von PowerShell auf einem Windows-System ausgeführt werden. Mit dieser schnellen Übung werden ähnliche Aufgaben wie bei der letzten Übung ausgeführt, dieses Mal werden jedoch PowerShell-Befehle verwendet.

Führen Sie den folgenden Befehl aus, um Informationen zum Mesos-Cluster zu erfassen, z. B. Agent-Namen und -Status.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Container im Docker-Format werden über Marathon mithilfe einer JSON-Datei bereitgestellt, die die vorgesehene Bereitstellung beschreibt. Im folgenden Beispiel wird der nginx-Container bereitgestellt; dabei wird Port 80 des Mesos-Agents an Port 80 des Containers gebunden.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Erstellen Sie eine eigene JSON-Datei, oder verwenden Sie das hier bereitgestellte Beispiel – [Azure ACS Demo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json) –, und speichern Sie es an einem zugänglichen Speicherort. Führen Sie anschließend den folgenden Befehl aus, und geben Sie den Namen der JSON-Datei an, um den Container bereitzustellen.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Die Marathon-API kann auch zum horizontalen Hoch- oder Herunterskalieren von Anwendungsbereitstellungen verwendet werden. Im vorherigen Beispiel wurde eine Instanz einer Anwendung bereitgestellt; diese skalieren wir jetzt horizontal auf drei Instanzen hoch. Dazu erstellen Sie eine JSON-Datei mit dem folgenden JSON-Text und speichern sie an einem zugänglichen Speicherort.

```json
{ "instances": 3 }
```

Führen Sie den folgenden Befehl aus, um die Anwendung zu horizontal hochzuskalieren.

> Hinweis: Der URI ist http://loclahost/marathon/v2/apps/ und anschließend die ID der zu skalierenden Anwendung. Wenn Sie das hier bereitgestellte nginx-Beispiel verwenden, lautet der URI http://localhost/v2/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0330_2016-->