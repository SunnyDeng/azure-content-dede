Der Speicheremulator unterstützt nur ein einziges festgelegtes Konto und einen bekannten Authentifizierungsschlüssel für die Authentifizierung mit einem gemeinsam verwendeten Schlüssel. Dieses Konto und dieser Schlüssel sind die einzigen gemeinsam verwendeten Anmeldeinformationen, die für den Speicheremulator verwendet werden dürfen. Sie lauten wie folgt:

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
    
> [AZURE.NOTE]Der vom Speicheremulator unterstützte Authentifizierungsschlüssel ist lediglich für das Testen der Funktionalität des Clientauthentifizierungscodes vorgesehen. Er erfüllt keinerlei Sicherheitszwecke. Sie können das in der Produktion verwendete Speicherkonto und den zugehörigen Schlüssel nicht mit dem Speicheremulator verwenden. Beachten Sie außerdem, dass Sie das Entwicklungskonto nicht mit Produktionsdaten verwenden sollten.

Um mit Ihrer Anwendung möglichst einfach eine Verbindung zum Speicheremulator herzustellen, konfigurieren Sie aus der Konfigurationsdatei in Ihrer Anwendung eine Verbindungszeichenfolge, die auf das Kontextmenü verweist `UseDevelopmentStorage=true`. Eine Verbindungszeichenfolge, die auf den Speicheremulator in einer App.config-Datei verweist, kann beispielsweise wie folgt aussehen:

    <appSettings>
      <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
    </appSettings>

Um eine Verbindungszeichenfolge zu erstellen, die auf den Emulator-Kontonamen und -schlüssel verweist, beachten Sie, dass Sie die Endpunkte für jeden der Dienste angeben müssen, die Sie vom Emulator in der Verbindungszeichenfolge verwenden möchten. Dies ist erforderlich, damit die Verbindungszeichenfolge auf die Emulator-Endpunkte verweist. Diese unterscheiden sich von den Endpunkten eines Produktions-Speicherkontos. Beispielsweise wird der Wert der Verbindungszeichenfolge wie folgt aussehen:

	DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
	AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
    BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
    TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
    QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1; 

Dieser Wert stimmt mit dem oben angezeigten Kontextmenü überein, `UseDevelopmentStorage=true`.

Sie können auch einen HTTP-Proxy angeben, der beim Testen des Dienstes anhand des Speicheremulators verwendet werden soll. Dies kann zum Beobachten von HTTP-Anforderungen und -Antworten nützlich sein, während Sie Vorgänge anhand der Speicherdienste debuggen. Fügen Sie der Verbindungszeichenfolge die Option `DevelopmentStorageProxyUri` hinzu, um einen Proxy anzugeben, und legen Sie ihren Wert auf den Proxy-URI fest. Hier sehen Sie beispielsweise eine Verbindungszeichenfolge, die auf den Speicheremulator verweist und einen HTTP-Proxy konfiguriert:

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

<!---HONumber=July15_HO2-->