<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Standardlimit</th>
   <th align="left" valign="middle">Maximales Limit</th>
</tr>
<tr>
   <td valign="middle"><p>Azure Media Services (AMS)-Konten in einem einzelnen Abonnement</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>Objekte pro AMS-Konto</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>1.000.000</p></td>
</tr>
<tr>
   <td valign="middle"><p>Verkettete Aufgaben pro Auftrag</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>30</p></td>
</tr>
<tr>
   <td valign="middle"><p>Objekte pro Aufgabe</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Objekte pro Auftrag</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Aufträge pro AMS-Konto </p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50.000<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Eindeutige Locators, die einem Objekt gleichzeitig zugeordnet sind</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>5<sup>4</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Livekanäle pro AMS-Konto </p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>n.z.<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Programme im angehaltenen Zustand pro Kanal </p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>n.z.<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Programme im ausgeführten Zustand pro Kanal </p></td>
   <td valign="middle"><p>3</p></td>
   <td valign="middle"><p>n.z.<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Streamingendpunkte im ausgeführten Zustand pro AMS-Konto</p></td>
   <td valign="middle"><p>2</p></td>
   <td valign="middle"><p>n.z.<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Streamingeinheiten pro Streamingendpunkt </p></td>
   <td valign="middle"><p>10 </p></td>
   <td valign="middle"><p>n.z.<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Codierungseinheiten pro AMS-Konto </p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>n.z.<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup> Sie können die Aktualisierung der Grenzwerte für dieses Kontingent anfordern, indem Sie ein Supportticket öffnen. Erstellen Sie keine weiteren AMS-Konten, um die Grenzwerte zu erhöhen, sondern senden Sie stattdessen ein Supportticket.

<sup>2</sup> Diese Zahl umfasst fertig gestellte, aktive und abgebrochene Aufträge sowie Aufträge in der Warteschlange. Gelöschte Aufträge sind nicht enthalten. Sie können die alten Aufträge mithilfe von **IJob.Delete** oder der HTTP **DELETE**-Anforderung löschen.

<sup>3</sup> Wenn Sie eine Anforderung zum Auflisten von Auftragsentitäten senden, werden maximal 1.000 Entitäten pro Anforderung zurückgegeben. Falls Sie alle gesendeten Aufträge nachverfolgen müssen, können Sie "Nach oben"/"Überspringen" wie in [OData-Systemabfrageoptionen](http://msdn.microsoft.com/library/gg309461.aspx) beschrieben verwenden.

<sup>4</sup> Locators sind nicht für die Verwaltung der Zugriffssteuerung pro Benutzer konzipiert. Um einzelnen Benutzern verschiedene Zugriffsrechte zu erteilen, verwenden Sie Lösungen zur Verwaltung digitaler Rechte (Digital Rights Management, DRM).

<!---HONumber=July15_HO4-->