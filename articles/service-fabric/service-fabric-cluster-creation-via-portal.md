<properties
   pageTitle="Einrichten eines Service Fabric-Clusters im Azure-Portal | Microsoft Azure"
   description="Einrichten eines Service Fabric-Clusters im Azure-Portal."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/16/2015"
   ms.author="chackdan"/>

# Einrichten eines Service Fabric-Clusters im Azure-Portal

Auf dieser Seite erhalten Sie Informationen zum Einrichten eines Service Fabric-Clusters. Hier wird davon ausgegangen, dass Ihr Abonnement ausreichend Kerne zum Bereitstellen der virtuellen IaaS-Computer umfasst, aus denen dieser Cluster besteht.


## Erstellen des Clusters

1. Melden Sie sich beim Azure-Portal [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal) an.

2. Klicken Sie auf **+ Neu**, um eine neue Ressourcenvorlage hinzufügen. Wählen Sie unsere Vorlage **Service Fabric Cluster** im Marketplace unter „Alles“ aus (Sie navigieren zu **Alles**, indem Sie auf die oberste Kategorie (Marketplace >) klicken, unter „Alles“ nach „Fabric“ suchen und die Eingabetaste drücken. Manchmal funktioniert der automatische Filter nicht, daher müssen Sie die **Eingabetaste drücken**) ![SearchforServiceFabricClusterTemplate][SearchforServiceFabricClusterTemplate]

3. Wählen Sie „Service Fabric Cluster“ aus der Liste aus
4. Navigieren Sie zum Blatt „Service Fabric Cluster“, klicken Sie auf **Erstellen** und geben Sie Details zu Ihrem Cluster ein
5. Erstellen Sie eine **neue Ressourcengruppe (RG)** mit demselben Namen wie der Cluster, damit Sie sie später einfach finden können. Das ist besonders hilfreich, wenn Sie Änderungen an Ihrer Bereitstellung vornehmen und/oder Ihren Cluster löschen.

  	Hinweis: Auch wenn Sie eine vorhandene Ressourcengruppe verwenden können, empfiehlt es sich, eine neue Ressourcengruppe zu erstellen. Dadurch können Sie nicht benötigte Cluster einfach löschen.

 	 ![CreateRG][CreateRG]


6. Stellen Sie sicher, dass Sie das gewünschte **Abonnement** auswählen, mit dem Ihr Cluster bereitgestellt werden soll – insbesondere, wenn Sie über mehrere Abonnements verfügen.

7. Wählen Sie einen **Speicherort** aus der Dropdownliste aus (wenn Sie einen anderen Speicherort wünschen, andernfalls wird standardmäßig „USA, Westen“ ausgewählt).

8. Konfigurieren Sie Ihren **Knotentyp**. Der Knotentyp kann als Äquivalent zu „Rollen“ in der Cloud-Diensten erachtet werden. Sie definieren die Größe, die Anzahl und Eigenschaften der virtuellen Computer. Ihr Cluster kann mehrere Knotentypen aufweisen. Die einzige Einschränkung besteht darin, dass Sie mindestens einen Knotentyp (Primär oder den ersten, den Sie im Portal definieren) mit mindestens 5 virtuellen Computern benötigen.
	1. Wählen Sie aus, welche Größe bzw. welcher Tarif benötigt wird (standardmäßig ist D4 Standard ausgewählt – wenn Sie diesen Cluster nur zum Testen Ihrer Anwendung verwenden, können Sie auch D2 oder kleinere virtuelle Computer auswählen).	
	2. Wählen Sie die Anzahl der virtuellen Computer aus. Sie können Sie die Anzahl der virtuellen Computer im Knotentyp später nach oben oder unten skalieren, jedoch muss der primäre oder erste Knotentyp über mindestens 5 virtuelle Computer verfügen.
	3. Wählen Sie einen Namen für Ihren Knotentyp aus (1 bis 12 Zeichen, nur Buchstaben und Zahlen).	
	4. Benutzernamen und Kennwort für den virtuellen Computer wählen
	5. **Überlegungen zum Knotentyp, wenn mehrere Knotentypen vorliegen**. Wenn Sie einen Cluster mit einem einzelnen Knotentyp bereitstellen möchten, fahren Sie mit dem nächsten Schritt fort.

		- Wir werden dieses Konzept im folgenden Beispiel genauer ansehen. Nehmen wir an, Sie möchten eine Anwendung bereitstellen, die einen „Front End“- und einen „Back End“-Dienst umfasst. Sie möchten den „Front End“-Dienst auf kleinere virtuelle Computer (z. B. A2, D2 usw.), deren Ports über das Internet zugänglich sind, und den rechenintensiven „Back End“-Dienst auf größere virtuelle Computer (z. B. D4, D6, D12 usw.) verlagern, die nicht mit dem Internet verbunden sind.
		- Obwohl beide Dienste auf einem Knotentyp verwendet werden können, empfiehlt es sich, diese in einen Cluster mit zwei Knotentypen zu platzieren. Jeder Knotentyp kann unterschiedliche Eigenschaften aufweisen, z. B. Internetverbindung und Größe des virtuellen Computers und Anzahl der virtuellen Computer, die unabhängig voneinander skaliert werden können.
		- Definieren Sie, welcher Knotentyp mindestens 5 virtuelle Computer umfasst. Die anderen Knotentypen können über mindestens einen virtuellen Computer verfügen.
					

  	![CreateNodeType][CreateNodeType]

9. **Anwendungsports**– Wenn Sie vorhaben, Ihre Anwendungen sofort im Cluster bereitzustellen, fügen Sie die Ports hinzu, die Sie für Ihre Anwendungen auf diesem Knotentyp öffnen möchten (oder auf den von Ihnen erstellten Knotentypen). Sie können dem Knotentyp später Ports hinzufügen, indem Sie den mit diesem Knotentyp verknüpften Load Balancer ändern (Sie müssen einen Test hinzufügen und diesen dann den Load Balancer-Regeln hinzufügen). Das gestaltet sich nun es etwas einfacher, da die Portal-Automatisierung die erforderlichen Tests und Regel zum LB hinzufügt.
	1. Sie finden die Anwendungsports in Ihren Dienstmanifesten, die Teil des Anwendungspakets sind. Rufen Sie Ihre Anwendungen auf, öffnen Sie die Dienstmanifeste und notieren Sie alle Eingabeendpunkte, die Ihre Anwendung zum Kommunizieren mit der Außenwelt benötigt.
	2. Fügen Sie dem Feld für die Anwendungseingangsendpunkte alle Ports (durch Kommas getrennt) hinzu. Der Endpunkt der TCP-Client-Verbindung ist standardmäßig 19000, sodass Sie keinen angegeben müssen. Für meine Anwendung muss z. B. Port „83“ geöffnet werden. Diesen finden Sie in der servicemanifest.xml in Ihrem Anwendungspaket (möglicherweise sind mehrere servicemanifest.xml vorhanden).

  Die meisten der Beispielanwendungen verwenden Port 80 und 8081. Fügen Sie diese also später hinzu, wenn Sie Beispiele in diesem Cluster bereitstellen möchten.

  ![Ports][Ports]



1. **Optional: Placement-Eigenschaften**– Sie müssen hier keine Konfigurationen hinzufügen, da vom System eine Standard-Platzierungseigenschaft von „NodeTypeName“ hinzugefügt wird. Sie können bei Bedarf weitere hinzufügen, wenn die Anwendung das erfordert. 

  
## Sicherheitskonfigurationen

Zu diesem Zeitpunkt unterstützt Service Fabric die Cluster-Sicherung nur über ein X509-Zertifikat. Davor müssen Sie Ihr Zertifikat in KeyVault hochladen. Weitere Informationen zur Vorgehensweise finden Sie unter [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md).

Die Cluster-Sicherung ist optional, wird jedoch dringend empfohlen. Wenn Sie Ihren Cluster nicht sichern, müssen Sie für den Sicherheitsmodus die Option „Keine“ auswählen.

Informationen zu Sicherheitsaspekten und Vorgehensweisen finden Sie unter [Fabric-Service Fabric-Clustersicherheit](service-fabric-cluster-security.md)

![SecurityConfigs][SecurityConfigs]



## Optional: Konfigurieren der Diagnose

Die Diagnose ist standardmäßig in Ihrem Cluster aktiviert, um die Behebung von Problemen in Ihrem Cluster zu vereinfachen. Wenn Sie die Diagnose deaktivieren möchten:

1. Navigieren Sie zum Blatt „Diagnose-Konfigurationen“.

2. Ändern Sie den Status auf „Aus“.

## Optional: Fabric-Einstellungen

Mit dieser erweiterten Option können Sie die Standardeinstellungen für den Service Fabric-Cluster ändern. Sie sollten die Standardeinstellungen **nicht ändern**, wenn Sie nicht sicher sind, dass Ihre Anwendung und/oder Ihr Cluster diese benötigen.

## Abschließen der Clustererstellung
Klicken Sie auf die **Zusammenfassung**, um die von Ihnen bereitgestellten Konfigurationsdateien anzuzeigen oder die ARM-Vorlage für die Bereitstellung des Clusters herunterzuladen. Nach dem Angeben der Pflichteinstellungen wird die Schaltfläche „Erstellen“ aktiviert, und Sie können mit der Clustererstellung beginnen.
 

Sie sehen den Status in den Benachrichtigungen verfolgen (klicken Sie auf das Glockensymbol neben der Statusleiste rechts im Bildschirm). Wenn Sie beim Erstellen des Cluster auf „An Startmenü anheften“ geklickt haben, wird „Service Fabric-Cluster wird bereitgestellt“ im Startmenü angezeigt.

![Benachrichtigungen][Notifications]

## Anzeigen des Cluster-Status

Wenn die Bereitstellung abgeschlossen ist, können Sie Ihren Cluster im Portal prüfen.

1. Klicken Sie unter **Durchsuchen** auf die Ressource – **Service Fabric-Cluster**.

2. Suchen Sie den Cluster, und klicken Sie darauf.

  ![BrowseCluster][BrowseCluster]

3. Sie können jetzt die Details Ihres Clusters, einschließlich der öffentlichen IP-Adresse, im Dashboard anzeigen. Wenn Sie die Maus über **Öffentliche IP-Adresse des Clusters** bewegen, wird eine Zwischenablage angezeigt, in die Sie durch Klicken kopieren können.

  ![ClusterDashboard][ClusterDashboard]

  Der Knotenmonitor auf dem Blatt „Cluster-Dashboard“ zeigt die Anzahl der virtuellen Computer, die fehlerfrei bzw. nicht fehlerfrei sind. Weitere Informationen zum Integritätsstatus finden Sie in der Dokumentation im Abschnitt [Einführung in das Service Fabric-Integritätsmodell](service-fabric-health-introduction.md).


## Herstellen einer Verbindung mit dem Cluster und Bereitstellen einer Anwendung

Mit der Clustereinrichtung können Sie jetzt eine Verbindung herstellen und mit dem Bereitstellen von Anwendungen beginnen.

1. Starten Sie Windows PowerShell auf einem Computer, auf dem Sie das Service Fabric-SDK installiert haben.

2. Führen Sie einen der folgenden PS-Befehle aus, je nachdem, ob ein sicherer oder unsicherer Cluster erstellt werden soll.
 

- Option 1 **Herstellen einer Verbindung zu einem unsicheren Cluster**. 

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10 
```

Führen Sie die folgenden Befehle zum Bereitstellen der Anwendung aus, und ersetzen Sie die angezeigten Pfade durch die entsprechenden Pfade auf Ihrem Computer.

 - Option 2: **Verbindung zu einem sicheren Cluster herstellen**

Führen Sie die folgenden Schritte aus, um das Zertifikat auf dem Computer einzurichten, den Sie verwenden, um „Connect-ServiceFabricCluster“-PS-Cmd auszuführen.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Führen Sie den folgenden PS aus, um die Verbindung zu einem sicheren Cluster auszuführen. Die Details des Zertifikats entsprechen Ihren Angaben im Portal.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
            -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
            -StoreLocation CurrentUser -StoreName My
```
Beispielsweise sollte der PS-Befehls oben wie der Folgende aussehen.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
            -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
            -StoreLocation CurrentUser -StoreName My
```


Führen Sie die folgenden Befehle zum Bereitstellen der Anwendung aus, und ersetzen Sie die angezeigten Pfade durch die entsprechenden Pfade auf Ihrem Computer. Im folgenden Beispiel wird die Beispielanwendung zur Wortzählung bereitgestellt.

Kopieren Sie das Paket in den Cluster, den Sie im vorherigen Schritt verbunden haben.


```powershell
$applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
````
Register your application type with service fabric.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
````

Erstellen Sie eine neue Instanz auf dem soeben registrierten Anwendungstyp.

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
````

Öffnen Sie jetzt den gewünschten Browser, und stellen Sie eine Verbindung zu dem Endpunkt her, den die Anwendung überwacht. Bei meiner Beispielanwendung zur Wortzählung sieht die URL wie folgt aus.

http://sfcluster4doc.westus.cloudapp.azure.com:31000



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
- [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md)
- [Einführung in das Service Fabric-Integritätsmodell](service-fabric-health-introduction.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!----HONumber=Nov15_HO4-->