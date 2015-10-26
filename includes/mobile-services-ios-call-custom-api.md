
### Aufrufen der benutzerdefinierten API aus einer iOS-App

Verwenden Sie zum Aufrufen dieser benutzerdefinierten API von einem iOS-Client aus die `MSClient invokeAPI`-Methode. Es gibt zwei Versionen dieser Methode, eine für JSON-formatierte Anforderungen und eine für beliebige Datentypen:

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content will be treated as JSON.
	-(void)invokeAPI:(NSString *)APIName
	            body:(id)body
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIBlock)completion;

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content can be of any media type.
	-(void)invokeAPI:(NSString *)APIName
	            data:(NSData *)data
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIDataBlock)completion;


Übergeben Sie z. B. zum Senden einer JSON-Anforderung an die benutzerdefinierte API "SendEmail" ein `NSDictionary` für die Anforderungsparameter:

	NSDictionary *emailHeader = @{ @"to": @"email.com", @"subject" : @"value" };

	[self.client invokeAPI:@"sendEmail"
	     body:emailBody
	     HTTPMethod:@"POST"
	     parameters:emailHeader
	     headers:nil
	     completion:completion ];
		

<!---HONumber=Oct15_HO3-->