### Speichern Ihrer Speicherkonto-Anmeldeinformationen in der Datei „app.config“

Als Nächstes speichern Sie Ihre Anmeldeinformationen in der Datei „app.config“ des Projekts. Bearbeiten Sie die Datei „app.config“ ähnlich wie im folgenden Beispiel, indem Sie `myaccount` durch den Namen Ihres Speicherkontos und `mykey` durch den Schlüssel Ihres Speicherkontos ersetzen.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

<!---HONumber=AcomDC_0309_2016-->