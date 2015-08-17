
Um die Endgeräte zu schützen, müssen Sie den Zugriff auf authentifizierte Clients einschränken.

1. Navigieren Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) zu Ihren Mobile Services, und klicken Sie dann auf **Daten** > den Tabellennamen (**TodoItem**) > **Berechtigungen**. 

2. Legen Sie alle Berechtigungen für Tabellenvorgänge auf **Nur authentifizierte Benutzer** fest.

	 Dadurch wird sichergestellt, dass alle Vorgänge für die Tabelle einen authentifizierten Benutzer erfordern. Die Einstellung ist auch für dieses Lernprogramm erforderlich. Um Ihr spezifisches Szenario zu unterstützen, können Sie für jeden Vorgang verschiedene Berechtigungen einstellen.

<!---HONumber=August15_HO6-->