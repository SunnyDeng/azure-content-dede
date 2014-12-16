

1. Klicken Sie im Azure-Verwaltungsportal auf die Registerkarte **Daten** und anschließend auf die Tabelle **Registrierungen**. 

	![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)

2. Klicken Sie in **Registrierungen** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.
   
	![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)

	Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **Registrierungen** eingefügt wird.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

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

