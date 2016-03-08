<properties
   pageTitle="Melden und Überprüfen der Integrität mit Azure Service Fabric | Microsoft Azure"
   description="Erfahren Sie, wie Sie Integritätsberichte aus Ihrem Dienstcode senden und die Integrität Ihres Diensts mithilfe von Integritätsüberwachungstools prüfen können, die von Azure Service Fabric bereitgestellt werden."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="toddabel"/>


# Melden und Überprüfen der Dienstintegrität
Wenn in Ihren Diensten Probleme auftreten, hängt Ihre Fähigkeit zum Reagieren auf und Beheben von Vorfällen und Ausfällen davon ab, wie schnell Sie die Probleme erkennen. Indem Ihr Dienstcode Probleme und Ausfälle dem Service Fabric Health Manager meldet, können Sie standardmäßige Systemüberwachungstools nutzen, die Service Fabric zum Überprüfen des Integritätsstatus bereitstellt.

In diesem Artikel wird anhand eines Beispiels das Hinzufügen eines Integritätsberichts zu einem Dienst und das Überprüfen des Integritätsstatus mithilfe von Service Fabric-Tools erläutert. Dieser Artikel soll als kurze Einführung in die Funktionen zur Integritätsüberwachung in Service Fabric dienen. Ausführlichere Informationen finden Sie in der Artikelserie zur Integrität. Beginnen Sie mit dem Link am Ende dieses Dokuments.

## Voraussetzungen
Folgendes muss installiert sein: Visual Studio 2015 Service Fabric SDK

## So stellen Sie eine Anwendung bereit und überprüfen ihre Integrität
Führen Sie die folgenden Schritte aus, um eine Anwendung bereitzustellen und ihre Integrität zu überprüfen:

1. Starten Sie Visual Studio als Administrator.

2. Erstellen Sie ein Projekt für einen zustandsbehafteten Dienst.

  ![Erstellen einer Service Fabric-Anwendung mit einem zustandsbehafteten Dienst](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Drücken Sie **F5**, um die Anwendung im Debugmodus auszuführen. Die Anwendung wird im lokalen Cluster bereitgestellt.

4. Sobald die Anwendung ausgeführt wird, starten Sie den Service Fabric-Explorer, indem Sie mit der rechten Maustaste auf die Taskleisten-App „Lokaler Cluster-Manager“ klicken und im Kontextmenü **Lokalen Cluster verwalten** wählen.

![Starten des Service Fabric-Explorers über die Taskleiste](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. Die Anwendungsintegrität sollte wie in der folgenden Abbildung angezeigt werden. Zu diesem Zeitpunkt sollte die Anwendung stabil und fehlerfrei sein.

  ![Fehlerfreie Anwendung in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Sie können die Integrität auch mithilfe der PowerShell überprüfen. Die Integrität einer Anwendung kann mithilfe von ```Get-ServiceFabricApplicationHealth``` und die Integrität eines Diensts mithilfe von ```Get-ServiceFabricServiceHealth``` überprüft werden. Der Integritätsbericht für die gleiche Anwendung in PowerShell sieht wie folgt aus: ![Fehlerfreie Anwendung in PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## So fügen Sie dem Dienstcode benutzerdefinierte Integritätsereignisse hinzu
Die Service Fabric-Visual Studio-Projektvorlagen enthalten Beispielcode. Die folgenden Schritte zeigen, wie Sie Integritätsereignisse aus dem Dienstcode melden können. Diese Berichte werden automatisch in den Standardtools für die Überwachung der Integrität angezeigt, die Service Fabric bietet, z. B. Service Fabric-Explorer, Integritätsanzeige im Azure-Portal und PowerShell.

1. Öffnen Sie erneut die Anwendung, die Sie zuvor in Visual Studio erstellt haben, oder erstellen Sie eine neue Anwendung, indem Sie in den Visual Studio-Vorlagen einen zustandsbehafteten Dienst verwenden.
2. Öffnen Sie die Datei **Stateful1.cs**. Suchen Sie die Deklaration für `var myDictionary`, und fügen Sie den Code rechts unten hinter der Deklaration von `var myDictionary` hinzu. Das hier erstellte `fabricClient`-Objekt wird später zum Melden der Integrität verwendet.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    Fügen Sie der Datei **Stateful1.cs** auch diesen Namespace hinzu.

    ```csharp
    using System.Fabric;
    ```

4. Suchen Sie als Nächstes den Aufruf von `myDictionary.TryGetValueAsync` in der *RunAsync*-Methode. Wie Sie sehen, ist die Rückgabe ein `result` mit dem aktuellen Wert des Zählers, da die Schlüssellogik dieser Anwendung einen laufenden Zähler vorsieht. Wenn dies eine reale Anwendung wäre und das Fehlen des Ergebnisses einen Fehler darstellen würde, sollte dies dem Health Manager gemeldet werden.
5. Zum Melden eines Integritätsereignisses aufgrund des Fehlens eines Ergebnisses als Fehler fügen Sie den folgenden Code hinter dem Aufruf von `myDictionary.TryGetValueAsync` hinzu. Wir melden das Ereignis als `StatefulServiceReplicaHealthReport`, da die Meldung aus einem zustandsbehafteten Dienst erfolgt. Die „PartitionId“ und „ReplicaId“, die an das Berichtsereignis übergeben werden, dienen zum Bestimmen der Quelle dieses Berichts, sobald er in einem der Integritätsüberwachungstools angezeigt wird. Denn ein bereitgestellter zustandsbehafteter Dienst kann mehrere Partitionen und jede Partition mehrere Replikate aufweisen. Der Parameter `HealthInformation` speichert Informationen zum gemeldeten Integritätsproblem. Fügen Sie der Datei **Stateful1.cs** diesen Namespace hinzu.

    ```csharp
    using System.Fabric.Health;
    ```

    Fügen Sie diesen Code nach dem Aufruf von `myDictionary.TryGetValueAsync` hinzu.

    ```csharp
    if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Wir simulieren diesen Fehler nun und prüfen, ob er in den Integritätsüberwachungstools angezeigt wird. Zum Simulieren des Fehlers kommentieren Sie zunächst die erste Zeile im Integritätsberichtscode aus, den Sie zuvor hinzugefügt haben. Nach dem Auskommentieren der ersten Zeile sieht der Code wie folgt aus: Dadurch wird nun bei jeder Ausführung von „RunAsync“ dieser Integritätsbericht ausgelöst. Nach der Änderung führen Sie die Anwendung durch Drücken von **F5** aus.

    ```csharp
    //if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

6. Sobald die Anwendung ausgeführt wird, öffnen Sie den Service Fabric-Explorer zum Überprüfen der Integrität der Anwendung. Dieses Mal zeigt der Service Fabric-Explorer die Anwendung als fehlerhaft an. Dies liegt an dem Fehler, der vom zuvor hinzugefügten Code gemeldet wurde. ![Fehlerhafte Anwendung in Service Fabric-Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Wenn Sie im Service Fabric-Explorer in der Strukturansicht das primäre Replikat auswählen, erkennen Sie, das auch hier die Integrität nicht gegeben ist. Außerdem werden Integritätsberichtsdetails angezeigt, die dem Parameter `HealthInformation` im Code hinzugefügt wurden. Dieselben Integritätsberichte werden auch in PowerShell und im Azure-Portal angezeigt. ![Replikatsintegrität in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Dieser Bericht verbleibt im Integritäts-Manager, bis er von einem anderen Bericht ersetzt oder dieses Replikat gelöscht wird. Da wir im HealthInformation-Objekt keinen Wert für „TimeToLive“ festgelegt haben, läuft dieser Integritätsbericht nie ab. Damit die Integrität abgefragt und gemeldet werden kann, muss der FabricClient mit Administratorrechten ausgeführt werden.

## Nächste Schritte
[Ausführlichere Informationen zur Service Fabric-Integrität](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_0224_2016-->