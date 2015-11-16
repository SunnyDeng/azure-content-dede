Die folgende Tabelle listet die Grenzwerte der verschiedenen Dienstebenen (S1, S2, F1) auf. Informationen zu den Kosten jeder *Einheit* finden Sie unter [IoT Hub – Preise](http://azure.microsoft.com/pricing/details/iot-hub/).

| Ressource | S1 Standard | S2 Standard | F1 Free |
| -------- | ----------- | ----------- | ------- |
| Geräte/Einheit | 500 | 500 | 10 |
| Nachrichten/Tag | 50\.000 | 1\.500.000 | 3\.000 |
| Maximale Anzahl der Einheiten | 200 | 200 | 1 |
| Geräteaktualisierungen (Erstellen, Aktualisieren,<br/> Löschen) pro Einheit pro Tag | 1100 | 1100 | 1100 |

> [AZURE.NOTE]Wenden Sie sich an den Microsoft-Support, wenn Sie voraussichtlich mehr als 200 Einheiten mit einem Hub im Tarif S1 oder S2 verwenden.

Die folgende Tabelle enthält die für IoT Hub-Ressourcen geltenden Grenzwerte:

| Ressource | Begrenzung |
| -------- | ----- |
| Maximale Anzahl von IoT Hubs pro Azure-Abonnement | 10 |
| Maximale Anzahl von Geräte-Identitäten,<br/> die bei einem einzelnen Aufruf zurückgegeben wird | 1000 |
| Maximale Beibehaltungsdauer von IoT Hub-Nachrichten | 7 Tage |
| Maximale Größe einer Nachricht von einem Gerät an die Cloud | 256 KB |
| Maximale Größe eines Batches, das vom Gerät an die Cloud gesendet wird | 256 KB |
| Maximale Anzahl von Nachrichten im Batch, das vom Gerät an die Cloud gesendet wird | 500 |
| Maximale Größe einer Nachricht von der Cloud an das Gerät | 64 KB |
| Maximale Gültigkeitsdauer von Nachrichten von der Cloud an das Gerät | 2 Tage |
| Maximale Übermittlungsanzahl von Nachrichten von der<br/> Cloud an das Gerät | 100 |
| Maximale Übermittlungsanzahl von Feedbacknachrichten<br/> als Reaktion auf eine Nachricht von der Cloud an das Gerät | 100 |
| Maximale Gültigkeitsdauer von Feedbacknachrichten<br/> als Reaktion auf eine Nachricht von der Cloud an das Gerät | 2 Tage |

Der IoT Hub-Dienst drosselt Anforderungen, wenn die folgenden Kontingente überschritten werden:

| Drosselung | Wert pro Hub |
| -------- | ------------- |
| Vorgänge in der Identitätsregistrierung <br/> (Erstellen, Abrufen, Aktualisieren, Löschen), <br/> einzelne Import-/Exportvorgänge oder Massenimport/-export | 100/Minute/Einheit, bis zu 5.000/Minute |
| Geräteverbindungen | 100/Sekunde/Einheit |
| Senden von Nachrichten von Geräten an die Cloud | 2000/Minute/Einheit (für S2), 60/Minute/Einheit (für S1) <br/> Mindestens 100/Sek. |
| Vorgänge von Cloud zu Geräten <br/> (Senden, Empfangen, Feedback) | 100/Minute/Einheit |

<!---HONumber=Nov15_HO2-->