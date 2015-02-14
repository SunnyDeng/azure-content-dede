1. Suchen Sie in der Datei "insert.js" für die Tabelle **Kanäle** nach den folgenden Codezeilen, kommentieren Sie sie aus, oder entfernen Sie sie aus der Datei. Speichern Sie anschließend die Änderungen.

		sendNotifications(item.channelUri);

		function sendNotifications(uri) {
		    console.log("Uri: ", uri);
		    push.wns.sendToastText01(uri, {
		        text1: "Sample toast from sample insert"
		    }, {
		        success: function (pushResponse) {
		            console.log("Sent push:", pushResponse);
		        }
		    });
		}
		
	Wenn Sie Änderungen in der Datei "insert.js" speichern, wird eine neue Version des Skripts zu Ihrem mobilen Dienst hochgeladen.

2. Erweitern Sie im Server-Explorer die Tabelle "TodoItem", öffnen Sie die Datei "insert.js", und ersetzen Sie die aktuelle Einfügefunktion durch folgenden Code. Speichern Sie anschließend Ihre Änderungen: 

		function insert(item, user, request) {
			request.execute({
				success: function() {
					request.respond();
					sendNotifications();
				}
			});
		
			function sendNotifications() {
				var channelsTable = tables.getTable('channels');
				channelsTable.read({
					success: function(devices) {
						devices.forEach(function(device) {
							push.wns.sendToastText04(device.channelUri, {
								text1: item.text
							}, {
								success: function(pushResponse) {
									console.log("Sent push:", pushResponse);
								}
							});
						});
					}
				});
			}
		}
		
	Wenn Sie nun ein neues Aktivitätselement hinzufügen, wird eine Pushbenachrichtigung an alle registrierten Geräte gesendet.

<!--HONumber=42-->
