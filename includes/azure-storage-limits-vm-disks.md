Ein virtueller Azure-Computer unterstützt das Anfügen mehrerer Datenträger. Zur Optimierung der Leistung sollten Sie die Anzahl stark ausgelasteter Datenträger begrenzen, die an den virtuellen Computer angefügt sind, um eine mögliche Drosselung zu vermeiden. Wenn nicht alle Datenträger gleichzeitig hoch ausgelastet sind, kann das Speicherkonto eine höhere Anzahl von Datenträgern unterstützen.

- **Für Standardspeicherkonten:** Ein Standardspeicherkonto hat eine maximale Gesamtanforderungsrate von 20.000 IOPS. Die gesamten IOPS auf allen Datenträgern eines virtuellen Computers in einem Standardspeicherkonto dürfen dieses Limit nicht überschreiten.

	Basierend auf dem Limit für Anforderungsraten können Sie die Anzahl der Datenträger mit hoher Auslastung ungefähr berechnen, die von einem Speicherkonto unterstützt werden. Im Basic-Tarif liegt die maximal zulässige Anzahl der Datenträger mit hoher Auslastung für einen virtuellen Computer beispielsweise bei 66 (20.000 : 300 IOPS pro Datenträger) und im Standard-Tarif bei ungefähr 40 (20.000 : 500 IOPS pro Datenträger) – siehe die folgende Tabelle.
 
- **Für Storage Premium-Konten:** Ein Storage Premium-Konto hat eine maximale Gesamtdurchsatzrate von 50 Gbit/s. Der Gesamtdurchsatz aller Ihrer VM-Datenträger darf dieses Limit nicht überschreiten.

<!---HONumber=AcomDC_1125_2015-->