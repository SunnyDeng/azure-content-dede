Die folgende Tabelle informiert über die einzelnen zentralen Kontingente, Einschränkungen, Standardwerte und Drosselungen für Azure Scheduler:

|Ressource|Beschreibung der Einschränkung|
|---|---|
|**Auftragsgröße**|Die maximale Auftragsgröße beträgt 16 KB. Bei Überschreitung dieses Grenzwerts durch eine PUT- oder PATCH-Anforderung wird der Statuscode 400 (unzulässige Anforderung) zurückgegeben.|
|**Größe der Anforderungs-URL**|Die Anforderungs-URL darf maximal 2048 Zeichen umfassen.|
|**Aggregierte Headergröße**|Die aggregierte Headergröße darf maximal 4096 Zeichen umfassen.|
|**Headeranzahl**|Die maximale Headeranzahl beträgt 50 Header.|
|**Textlänge**|Die maximale Textlänge beträgt 8192 Zeichen.|
|**Wiederholungsspanne**|Die maximal zulässige Wiederholungsspanne beträgt 18 Monate.|
|**Zeit bis zur Startzeit**|Der maximal zulässige Wert für die Zeit bis zur Startzeit beträgt 18 Monate.|
|**Auftragsverlauf**|Die maximale Größe des im Auftragsverlauf gespeicherten Antworttexts beträgt 2048 Bytes.|
|**Frequency**|Das Standardkontingent für die maximale Häufigkeit beträgt eine Stunde (kostenlose Auftragssammlung) bzw. eine Minute (Standard-Auftragssammlung). Die maximale Häufigkeit kann für eine Auftragssammlung auf einen Wert unterhalb des Maximalwerts festgelegt werden. Der für die Auftragssammlung festgelegte Wert gilt für alle Aufträge in der Auftragssammlung. Beim Versuch, einen Auftrag mit einer Häufigkeit zu erstellen, die die maximal zulässige Häufigkeit der Auftragssammlung übersteigt, ist die Anforderung nicht erfolgreich, und der Statuscode 409 (Konflikt) wird zurückgegeben.|
|**Aufträge**|Das Standardkontingent für die maximale Auftragsanzahl liegt bei fünf Aufträgen (kostenlose Auftragssammlung) bzw. bei 50 Aufträgen (Standard-Auftragssammlung). Die maximale Auftragsanzahl kann für eine Auftragssammlung konfiguriert werden. Der für die Auftragssammlung festgelegte Wert gilt für alle Aufträge in der Auftragssammlung. Bei Überschreitung des maximalen Auftragskontingents wird der Statuscode 409 (Konflikt) zurückgegeben.|
|**Speicherung des Auftragsverlaufs**|Der Auftragsverlauf wird bis zu 2 Monate lang oder maximal für die letzten 1000 Ausführungen beibehalten.|
|**Speicherung abgeschlossener und fehlerhafter Aufträge**|Abgeschlossene und fehlerhafte Aufträge werden 60 Tage lang gespeichert.|
|**Timeout**|Bei HTTP-Aktionen gilt ein statisches (nicht konfigurierbares) Anforderungstimeout von 30 Sekunden. Greifen Sie bei länger dauernden Vorgängen auf asynchrone HTTP-Protokolle zurück, bei denen beispielsweise umgehend der Statuscode 202 zurückgegeben und der Vorgang im Hintergrund fortgesetzt wird.|

<!---HONumber=Oct15_HO3-->