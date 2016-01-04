**1. Wie werden Kunden über die Deaktivierung eines SDK benachrichtigt?**

Bei Deaktivierung eines SDK benachrichtigt Microsoft seine Kunden 12 Monate vor Beendigung der Unterstützung, um einen reibungslosen Übergang zu einem unterstützten SDK sicherzustellen. Kunden werden über verschiedene Kommunikationskanäle benachrichtigt: Azure-Verwaltungsportal, Developer Center, Blogbeitrag und direkter Austausch mit den Administratoren der entsprechenden Dienste.

**2. Können Kunden mit einem DocumentDB SDK, das deaktiviert werden soll, während des 12-monatigen Zeitraums Anwendungen entwickeln?**

Ja, mit einem solchen DocumentDB SDK können Kunden während der 12-monatigen Frist Anwendungen bei vollem Zugriff erstellen, bereitstellen und ändern. Während der 12-monatigen Frist wird Kunden empfohlen, ggf. zu einer neueren unterstützten Version des DocumentDB SDK zu wechseln.

**3. Können Kunden, mit einem deaktivierten DocumentDB SDK nach Ablauf des 12-monatigen Benachrichtigungszeitraums Anwendungen erstellen und ändern?**

Das SDK wird nach Ablauf des 12-monatigen Benachrichtigungszeitraums deaktiviert. Der Zugriff auf DocumentDB durch eine Anwendung mithilfe eines deaktivierten SDK wird von der DocumentDB-Plattform nicht zugelassen. Darüber hinaus bietet Microsoft keinen Kundensupport für das deaktivierte SDK.

**4. Was geschieht mit Kunden, die Anwendungen ausführen, die eine nicht unterstützte Version des DocumentDB SDK verwenden?**

Alle Versuche zum Herstellen einer Verbindung mit dem DocumentDB-Diensts mit einer deaktivierten SDK-Version werden abgelehnt.

**5. Werden neue Features und Funktionen allen noch aktivierten SDKs hinzugefügt?**

Neue Features und Funktionen werden nur neuen Versionen hinzugefügt. Wenn Sie eine alte, noch aktivierte Version des SDK verwenden, funktionieren Ihre Anforderungen an DocumentDB weiter wie bisher, doch Sie haben keinen Zugriff auf neue Funktionen.

**6. Was muss ich tun, wenn ich meine Anwendung nicht vor einem Stichtag aktualisieren kann?**

Es wird empfohlen, so früh wie möglich auf das neueste SDK zu aktualisieren. Sobald ein SDK für die Deaktivierung markiert wurde, bleiben Ihnen 12 Monate zur Aktualisierung Ihrer Anwendung. Wenn Sie aus einem beliebigen Grund die Aktualisierung der Anwendung in diesem Zeitraum nicht schaffen, kontaktieren Sie das [DocumentDB-Team](mailto:askdocdb@microsoft.com), und bitten Sie vor dem Stichtag um Hilfe.

<!---HONumber=AcomDC_1203_2015-->