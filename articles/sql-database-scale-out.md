<properties linkid="manage-services-how-to-scale-a-sqldb" urlDisplayName="How to scale" pageTitle="How to scale a SQL Database - Azure" metaKeywords="" description="Learn about options for scaling your SQL Database in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Scale a SQL Database Solution" authors="" solutions="" manager="" editor="" />

Skalieren einer SQL-Datenbanklösung
===================================

Auf Azure ist Datenbankskalierbarkeit gleichbedeutend mit horizontaler Skalierung; hierbei wird eine Arbeitsauslastung neu auf mehrere Commodity-Server in einem Datencenter verteilt. Die horizontale Skalierung ist eine Strategie zur Behebung von Problemen mit Datenkapazität oder Leistung. Für eine sehr große und stark wachsende Datenbank wird irgendwann eine horizontale Skalierung erforderlich, unabhängig davon, ob nur einige wenige oder sehr viele Benutzer darauf zugreifen.

Auf Azure erfolgt die horizontale Skalierung am besten durch Erstellung eines Verbunds. Der SQL-Datenbankverbund basiert auf horizontaler Partitionierung (Sharding), wobei eine oder mehrere Tabellen nach Zeile geteilt und auf mehrere Verbundmitglieder aufgeteilt werden.

Ein Verbund ist jedoch nicht die einzige Lösung für Skalierbarkeitsprobleme. Manchmal legen die Eigenschaften von Daten oder Anwendungsanforderungen auch einfachere Methoden nahe. In der folgenden Liste werden mögliche Lösungen in der Reihenfolge ihrer Komplexität vorgestellt.

Vergrößern der Datenbank
------------------------

Datenbanken werden mit einer festen Größe erstellt, die von einem editionsspezifischen Maximalwert abhängt. Für die Web-Edition kann eine Datenbank auf maximal 5 GB vergrößert werden. Für die Business-Edition beträgt die maximale Datenbankgröße 150 GB. Am einfachsten kann die Datenkapazität durch Ändern der Edition und der maximalen Größe erhöht werden:

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

Verwenden mehrerer Datenbanken und Zuordnen von Benutzern
---------------------------------------------------------

In eingeschränkten Szenarien können Kopien einer Datenbank erstellt und dann Anmeldungen und Benutzer für die einzelnen Datenbanken zugewiesen werden. Vor der Einführung der Verbundfunktion war dies eine gängige Vorgehensweise zur Neuverteilung einer Arbeitsbelastung. Dieses Verfahren ist für Datenbanken geeignet, die Sie kurzzeitig verwenden und später in einer primären Datenbank zusammenführen, die Sie lokal verwalten, und für Lösungen, die schreibgeschützte Daten bereitstellen.

Verwenden von Verbunden
-----------------------

Mit SQL-Datenbankverbunden können Sie eine bessere Skalierbarkeit und eine höhere Leistung erzielen. Eine oder mehrere Tabellen in einer Datenbank werden nach Zeile geteilt und auf mehrere Datenbanken (Verbundmitglieder) aufgeteilt. Diese Art der horizontalen Partitionierung wird häufig als "Sharding" bezeichnet. Dieses Verfahren ist primär in Szenarien hilfreich, in denen Skalierbarkeit und Leistung erzielt oder Kapazität verwaltet werden muss.

Verbunde werden nur in der Business-Edition unterstützt. Weitere Informationen finden Sie unter [Verbunde in der Azure SQL-Datenbank](http://msdn.microsoft.com/en-us/library/windowsazure/hh597452.aspx) und [Lernprogramm für SQL-Datenbankverbunde – DBA](http://msdn.microsoft.com/en-us/library/windowsazure/hh778416.aspx).

Erwägen anderer Speicherformen
------------------------------

Denken Sie daran, dass Azure mehrere Datenspeicherformen unterstützt, darunter Tabellenspeicher und Blob-Speicher. Sofern Sie keine relationalen Funktionen benötigen, ist Tabellen- oder Blob-Speicher eventuell die wirtschaftlichere Lösung.

[Federations in SQL Database]: http://msdn.microsoft.com/en-us/library/windowsazure/hh597452.aspx
[SQL Database Federations Tutorial - DBA]: http://msdn.microsoft.com/en-us/library/windowsazure/hh778416.aspx
