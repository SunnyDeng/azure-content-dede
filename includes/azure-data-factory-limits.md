Data Factory ist ein mehrinstanzenfähiger Dienst mit den folgenden Standardlimits, um sicherzustellen, dass Kundenabonnements vor anderen Arbeitslasten geschützt sind. Viele der Limits können problemlos für Ihr Abonnement bis zum maximalen Limit angehoben werden, indem Sie sich an den Support wenden.

**Ressource** | **Standardlimit** | **Maximales Limit**
-------- | ------------- | -------------
Pipelines innerhalb einer Data Factory | 100 | 2500
Datasets innerhalb einer Data Factory | 500 | 5\.000
Gleichzeitige Slices pro Dataset | 10 | 10
Bytes pro Objekt für Pipelineobjekte <sup>1</sup> | 200 KB | 2000 KB
Bytes pro Objekt für Dataset- und LinkedService-Objekte<sup>1</sup> | 30 KB | 2000 KB
Felder pro Objekt | 100 | [Support kontaktieren](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Bytes pro Feldname oder Bezeichner | 2 KB | [Support kontaktieren](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Bytes pro Feld | 30 KB | [Support kontaktieren](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Bedarfsgesteuerte HDInsight-Clusterkerne innerhalb eines Abonnements <sup>2</sup> | 48 | [Support kontaktieren](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Anzahl der Wiederholungsversuche für Ausführungen von Pipelineaktivitäten | 1\.000 | MaxInt (32 Bit)

<sup>1</sup> Pipeline-, Dataset- und verknüpfte Dienstobjekte stellen eine logische Gruppierung Ihrer Workload dar. Limits für diese Objekte beziehen sich nicht auf die Datenmenge, die Sie mit dem Azure Data Factory-Dienst verschieben und verarbeiten können. Data Factory kann so skaliert werden, dass Petabytes an Daten verarbeitet werden können.

<sup>2</sup> Bedarfsgesteuerte HDInsight-Kerne werden über das Abonnement zugewiesen, das die Data Factory enthält. Daher ist das obige Limit das von der Data Factory erzwungene Kernlimit für bedarfsgesteuerte HDInsight-Kerne und unterscheidet sich vom Kernlimit, das für Ihr Azure-Abonnement gilt.


**Ressource** | **Unteres Standardlimit** | **Minimales Limit**
-------- | ------------------- | -------------
Planungsintervall | 15 Minuten | 15 Minuten
Intervall zwischen Wiederholungsversuchen | 1 Sekunde | 1 Sekunde
Timeoutwert für Wiederholungsversuche | 1 Sekunde | 1 Sekunde


### Limits für Webdienstaufrufe

Der Azure-Ressourcen-Manager weist Limits für API-Aufrufe auf. Sie können API-Aufrufe mit einer Rate innerhalb der [API-Limits des Azure-Ressourcen-Managers](azure-subscription-service-limits/#resource-group-limits) ausführen.

<!---HONumber=AcomDC_1125_2015-->