

1. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **Registrierungen**. 

	![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)

2. In **Registrierungen**, klicken Sie auf die **Skript**-Registerkarte, und wählen Sie **Einfügen**.
   
	![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)

	Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **** eine Einfügung auftritt.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code, und klicken Sie anschließend auf **Speichern**:

		function insert(item, user, request) {
			var registrationTable = tables.getTable('Registrations');
			registrationTable
				.where({ handle: item.handle })
				.read({ success: insertChannelIfNotFound });
	        function insertChannelIfNotFound(existingRegistrations) {
        	    if (existingRegistrations.length > 0) {
            	    request.respond(200, existingRegistrations[0]);
        	    } else {
            	    request.execute();
        	    }
    	    }
	    }

   Auf diese Weise wird ein neues Einfügeskript registriert, das die Registrierungsinformationen in der neuen Tabelle speichert.


<!--HONumber=42-->
