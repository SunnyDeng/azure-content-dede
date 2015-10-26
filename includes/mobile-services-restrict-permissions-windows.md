
1. Erweitern Sie im Server-Explorer in Visual Studio den Knoten **Azure**, **Mobile Services** und den mobilen Dienst.

2. Klicken Sie mit der rechten Maustaste auf die Tabelle **TodoItem**, klicken Sie auf **Bearbeiten**, legen Sie für alle Berechtigungen **Nur authentifizierte Benutzer** fest, und klicken Sie dann auf **Übernehmen**. Dadurch wird sichergestellt, dass alle Vorgänge für die Tabelle _TodoItem_ einen authentifizierten Benutzer erfordern.

3. Klicken Sie mit der rechten Maustaste auf das Client-App-Projekt, klicken Sie auf **Debuggen** und dann auf **Neue Instanz starten**. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

	Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

<!---HONumber=Oct15_HO3-->