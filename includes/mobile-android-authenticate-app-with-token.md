
Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, bei der der Client bei jedem Starten der App sowohl den Identitätsanbieter als auch den Azure-Back-End-Dienst kontaktieren muss. Diese Methode ist jedoch ineffizient und zudem können Probleme auftreten, wenn viele Kunden die App gleichzeitig starten möchten. Ein besserer Ansatz ist es daher, das vom Azure-Service zurückgegebene Authentifizierungstoken zwischenzuspeichern und vor einer anbieterbasierten Anmeldung zu verwenden.

>[AZURE.NOTE]Das vom Azure-Back-End-Dienst zurückgegebene Authentifizierungstoken können Sie unabhängig davon, ob Sie die clientverwaltete oder die dienstverwaltete Authentifizierung verwenden, zwischenspeichern. In diesem Lernprogramm wird die dienstverwaltete Authentifizierung verwendet.


1. Öffnen Sie die Datei "ToDoActivity.java", und fügen Sie die folgenden import-Anweisungen ein:

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Fügen Sie die folgenden Elemente der `ToDoActivity`-Klasse hinzu.

    	public static final String SHAREDPREFFILE = "temp";	
	    public static final String USERIDPREF = "uid";	
    	public static final String TOKENPREF = "tkn";	


3. Fügen Sie der Datei "ToDoActivity.java" die folgende Definition für die `cacheUserToken`-Methode hinzu.
 
    	private void cacheUserToken(MobileServiceUser user)
	    {
    		SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
    	    Editor editor = prefs.edit();
	        editor.putString(USERIDPREF, user.getUserId());
    	    editor.putString(TOKENPREF, user.getAuthenticationToken());
	        editor.commit();
    	}	
  
    Diese Methode speichert die Benutzer-ID und das Token in einer Einstellungsdatei, die als privat gekennzeichnet wird. Dadurch sollte der Zugriff auf den Cache gesichert sein, sodass andere Apps auf dem Gerät nicht auf das Token zugreifen können, da die Einstellungen für die App in Sandboxes abgeschirmt sind. Trotzdem ist es möglich, dass jemand, der Zugriff auf das Gerät erlangt hat, auf andere Weise auf den Tokencache zugreift.

    >[AZURE.NOTE]Sollte es sich um äußerst sensible Daten handeln und anderen Benutzern der Zugriff auf das Gerät möglich sein, können Sie das Token durch Verschlüsselung zusätzlich schützen. Eine absolut sichere Lösung kann jedoch im Rahmen dieses Lernprogramms nicht gegeben werden und hängt zudem von Ihren Sicherheitsanforderungen ab.


4. Fügen Sie der Datei "ToDoActivity.java" die folgende Definition für die `loadUserTokenCache`-Methode hinzu.

    	private boolean loadUserTokenCache(MobileServiceClient client)
	    {
	        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
    	    String userId = prefs.getString(USERIDPREF, "undefined"); 
	        if (userId == "undefined")
	            return false;
    	    String token = prefs.getString(TOKENPREF, "undefined"); 
    	    if (token == "undefined")
    	        return false;
        	    
    	    MobileServiceUser user = new MobileServiceUser(userId);
    	    user.setAuthenticationToken(token);
    	    client.setCurrentUser(user);
        	    
    	    return true;
	    }



5. Ersetzen Sie in der Datei *ToDoActivity.java* die `authenticate`-Methode durch die folgende Methode, die einen Tokencache verwendet. Wechseln Sie den Login-Anbieter, wenn Sie ein anderes Konto als Google verwenden möchten.

		private void authenticate() {
			// We first try to load a token cache if one exists.
		    if (loadUserTokenCache(mClient))
		    {
		        createTable();
		    }
		    // If we failed to load a token cache, login and create a token cache
		    else
		    {
			    // Login using the Google provider.    
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
		
		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}   		
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();	
		    		}
		    	});
		    }
		}

6. Erstellen Sie die App und testen Sie die Authentifizierung mit einem gültigen Konto. Führen Sie sie mindestens zweimal aus. Während der ersten Ausführung sollten Sie eine Aufforderung zur Anmeldung und Erstellung des Tokencache erhalten. Danach wird bei jeder Anmeldung versucht, zur Authentifizierung den Tokencache zu laden, und es sollte keine Anmeldung mehr erforderlich sein.

<!---HONumber=AcomDC_1210_2015-->