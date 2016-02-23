<properties
   pageTitle="ACS-Containerverwaltung über die Webbenutzeroberfläche"
   description="Stellen Sie mithilfe der Marathon-Webbenutzeroberfläche Container für einen Azure Container Service-Clusterdienst bereit."
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
   
# Containerverwaltung über die Webbenutzeroberfläche
   
Mesos stellt eine Umgebung für die Bereitstellung und Skalierung geclusterter Workloads bereit und abstrahiert die zugrunde liegende Hardware. Neben Mesos verwaltet auch ein Framework die Planung und Ausführung von Computeworkload. Frameworks sind zwar für viele gängige Workloads verfügbar, in diesem Dokument wird jedoch das Erstellen und Skalieren von Containerbereitstellungen mit Marathon erläutert. Bevor Sie diese Beispiele durchgehen, benötigen Sie einen in ACS konfigurierten Mesos-Cluster und eine Remoteverbindung mit diesem Cluster. Weitere Informationen zu diesen Elementen finden Sie in den folgenden Artikeln:

- [Bereitstellen eines Azure Container Service-Clusters](./container-service-deployment.md) 
- [Herstellen der Verbindung mit einem ACS-Cluster](./container-service-connect.md)

## Erkunden der Mesos-Benutzeroberfläche

Navigieren Sie mit eingerichtetem SSH-Tunnel zu http://localhost/Mesos. Dadurch wird die Mesos-Webbenutzeroberfläche geladen. Auf der Seite können Sie Informationen zum Mesos-Cluster erfassen, z. B. aktivierte Agents, Aufgabenstatus und die Ressourcenverfügbarkeit.

![Bereitstellung erstellen](media/ui1.png)

## Erkunden der Marathon-Benutzeroberfläche

Navigieren Sie zum Anzeigen der Marathon-Benutzeroberfläche zu http://localhost/Marathon. Auf diesem Bildschirm können Sie einen neuen Container oder eine andere Anwendung auf dem ACS Mesos-Cluster starten sowie Informationen zu ausgeführten Containern und Anwendungen anzeigen.

![Bereitstellung erstellen](media/ui2.png)

## Bereitstellen eines Docker-Containers

Um mit Marathon einen neuen Container auf dem Mesos-Cluster zu starten, klicken Sie auf die Schaltfläche `Create Application`. Das Formular „Neue Anwendung“ wird zum Definieren der Anwendungs- oder Containerparameter verwendet. In diesem Beispiel wird ein einfacher nginx-Container bereitgestellt. Geben Sie Folgendes ein: Klicken Sie nach dem Abschluss auf „Erstellen“.
 
Feld | Wert
----------------|-----------
ID | nginx
Image | nginx
Netzwerk | Überbrückt
Container-Port | 80
Hostport | 80
Protokoll | TCP

![Bereitstellung erstellen](media/ui3.png)

Auf der Marathon-Hauptseite wird der Bereitstellungsstatus für den Container angezeigt.

![Bereitstellung erstellen](media/ui4.png)

Wenn Sie zurück zur Mesos-App wechseln (http://localhost/Mesos)), sehen Sie, dass jetzt eine Aufgabe – in diesem Fall ein Docker-Container – auf dem Mesos-Cluster ausgeführt wird. Sie sehen auch, auf welchem Clusterknoten die Aufgabe ausgeführt wird.

![Bereitstellung erstellen](media/ui5.png)

## Skalieren eines Docker-Containers

Auf der Marathon-Webbenutzeroberfläche kann auch die Anzahl der Instanzen eines Containers skaliert werden. Navigieren Sie dazu zur Marathon-Seite, wählen Sie den zu skalierenden Container aus, und klicken Sie auf die Schaltfläche `scale`. Geben Sie im Fenster zum Skalieren der Anwendung die gewünschte Anzahl der Containerinstanzen ein, und wählen Sie `Scale Application`.

![Bereitstellung erstellen](media/ui6.png)

Sobald der Skalierungsvorgang abgeschlossen ist, sehen Sie mehrere Instanzen derselben Aufgabe auf Mesos-Agents verteilt.

![Bereitstellung erstellen](media/ui8.png)

<!---HONumber=AcomDC_0218_2016-->