Die Datenbank-Transaktionseinheit (Database Transaction Unit, DTU) ist die Maßeinheit in SQL-Datenbank, die die relative Leistung von Datenbanken basierend auf einer echten Maßeinheit darstellt: die Datenbanktransaktion. Dazu haben wir eine Reihe von Vorgängen ausgewählt, die typisch für eine Online-Transaktionsverarbeitung (Online Transaction Processing, OLTP) sind, und dann gemessen, wie viele Transaktionen pro Sekunde unter maximaler Belastung vollständig abgeschlossen werden konnten (dies ist die Kurzversion, die Details können Sie in der [Benchmark-Übersicht](../articles/sql-database/sql-database-benchmark-overview.md) nachlesen).

Eine Basic-Datenbank verfügt über 5 DTUs, was bedeutet, dass 5 Transaktionen pro Sekunde abgeschlossen werden können, wohingegen eine Premium-P11-Datenbank über 1.750 DTUs verfügt.

![Einzeldatenbank-DTUs nach Ebene und Stufe](./media/sql-database-understanding-dtus/single_db_dtus.png)

Die DTU für Einzeldatenbanken kann direkt in die eDTU für elastische Datenbanken übertragen werden. Beispielsweise bietet eine Datenbank in einem Pool mit elastischen Basic-Datenbanken bis zu 5 eDTUs. Das ist eine ähnliche Leistung wie bei einer Basic-Einzeldatenbank. Der Unterschied besteht darin, dass elastische Datenbanken keine eDTUs aus dem Pool verwenden, bis dies erforderlich ist.

![Elastische Pools nach Ebene](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Ein einfaches Beispiel veranschaulicht dies. Nehmen Sie einen Basic-Pool für elastische Datenbanken mit 1.000 DTUs, in dem Sie 800 Datenbanken ablegen. Solange nur 200 der 800 Datenbanken zu einem beliebigen Zeitpunkt genutzt werden (5 DTU x 200 = 1000), wird die maximale Kapazität des Pools nicht erreicht und die Leistung der Datenbank nicht beeinträchtigt. In diesem Beispiel wird aus Gründen der Übersichtlichkeit vereinfacht. In Wirklichkeit sind die Dinge ein wenig komplizierter. Das Portal erledigt die komplizierten Aufgaben für Sie und gibt eine Empfehlung basierend auf der bisherigen Verwendung der Datenbank ab. Weitere Informationen zu den Empfehlungen oder zum Selbermachen finden Sie unter [Überlegungen zum Preis und zur Leistung für einen elastischen Datenbankpool](../articles/sql-database/sql-database-elastic-pool-guidance.md).

<!---HONumber=Nov15_HO1-->