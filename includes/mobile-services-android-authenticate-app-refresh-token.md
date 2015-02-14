Unser Tokencache sollte normalerweise funktionieren, doch was tun, wenn das Token abläuft oder widerrufen wird? Das Token kann beispielsweise abgelaufen sein, wenn die App nicht ausgeführt wurde. Dann ist der Tokencache ungültig. Das Token kann auch abgelaufen sein, wenn die App ausgeführt wird und einen direkten Aufruf ausführt oder ein Aufruf von der Mobile Services-Bibliothek ausgeführt wird. Ergebnis ist der HTTP-Statuscode 401 "Nicht autorisiert". 

Wir müssen in der Lage sein, einen abgelaufenen Token zu erkennen und zu aktualisieren. Dazu verwenden wir einen [ServiceFilter](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html) aus der [Android-Clientbibliothek](http://dl.windowsazure.com/androiddocs/).

In diesem Abschnitt definieren Sie einen ServiceFilter, der einen HTTP-Statuscode 401 erkennt und die Aktualisierung des Tokens und des Tokencaches auslöst. Zusätzlich blockiert dieser ServiceFilter während der Authentifizierung andere ausgehende Anforderungen, sodass diese Anforderungen das aktualisierte Token verwenden können.

1. Öffnen Sie in Eclipse die Datei ToDoActivity.java und fügen Sie die folgenden Importanweisungen hinzu:
 
        import java.util.concurrent.atomic.AtomicBoolean;
		import java.util.concurrent.ExecutionException;

		import com.microsoft.windowsazure.mobileservices.MobileServiceException;
 
2. Fügen Sie die folgenden Elemente zur Klasse `ToDoActivity` hinzu. 

    	public boolean bAuthenticating = false;
	    public final Object mAuthenticationLock = new Object();

    Sie werden verwendet, um die Authentifizierung des Benutzers zu synchronisieren. Die Authentifizierung soll nur einmal vorgenommen werden. Aufrufe während der Authentifizierung sollten warten und das Token der gerade laufenden Authentifizierung verwenden.

3. Fügen Sie in der Datei ToDoActivity.java die folgende Methode zur ToDoActivity-Klasse hinzu, um während der Authentifizierung ausgehende Aufrufe auf anderen Threads zu blockieren.

	    /**
    	 * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
    	 */
    	public boolean detectAndWaitForAuthentication()
    	{
    		boolean detected = false;
    		synchronized(mAuthenticationLock)
    		{
    			do
    			{
    				if (bAuthenticating == true)
    					detected = true;
    				try
    				{
    					mAuthenticationLock.wait(1000);
    				}
    				catch(InterruptedException e)
    				{}
    			}
    			while(bAuthenticating == true);
    		}
    		if (bAuthenticating == true)
    			return true;
    		
    		return detected;
    	}
    	

4. Fügen Sie in der Datei ToDoActivity.java die folgende Methode zur ToDoActivity-Klasse hinzu. Diese Methode löst die Wartezeit aus und aktualisiert das Token der ausgehenden Anforderungen, wenn die Authentifizierung abgeschlossen ist. 

    	
    	/**
    	 * Waits for authentication to complete then adds or updates the token 
    	 * in the X-ZUMO-AUTH request header.
    	 * 
    	 * @param request
    	 *            The request that receives the updated token.
    	 */
    	private void waitAndUpdateRequestToken(ServiceFilterRequest request)
    	{
    		MobileServiceUser user = null;
    		if (detectAndWaitForAuthentication())
    		{
    			user = mClient.getCurrentUser();
    			if (user != null)
    			{
    				request.removeHeader("X-ZUMO-AUTH");
    				request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
    			}
    		}
    	}


5. Aktualisieren Sie in der Datei "ToDoActivity.java" die `authenticate`-Methode der "ToDoActivity"-Klasse, sodass diese einen booleschen Parameter akzeptiert, um die Erzwingung der Aktualisierung des Tokens und des Tokencaches zu ermöglichen. Außerdem müssen die blockierten Threads benachrichtigt werden, wenn die Authentifizierung abgeschlossen ist, damit sie das neue Token übernehmen.

	    /**
    	 * Authenticates with the desired login provider. Also caches the token. 
    	 * 
    	 * If a local token cache is detected, the token cache is used instead of an actual 
	     * login unless bRefresh is set to true forcing a refresh.
    	 * 
	     * @param bRefreshCache
    	 *            Indicates whether to force a token refresh. 
	     */
    	private void authenticate(boolean bRefreshCache) {
	        
		    bAuthenticating = true;
		    
		    if (bRefreshCache || !loadUserTokenCache(mClient))
	        {
	            // New login using the provider and update the token cache.
	            mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
	                    new UserAuthenticationCallback() {
	                        @Override
	                        public void onCompleted(MobileServiceUser user,
	                                Exception exception, ServiceFilterResponse response) {
    
	                    		synchronized(mAuthenticationLock)
	                    		{
		                        	if (exception == null) {
		                                cacheUserToken(mClient.getCurrentUser());
                                        createTable();
		                            } else {
		                                createAndShowDialog(exception.getMessage(), "Login Error");
		                            }
		            				bAuthenticating = false;
		            				mAuthenticationLock.notifyAll();
	                    		}
	                        }
	                    });
	        }
		    else
		    {
		    	// Other threads may be blocked waiting to be notified when 
		    	// authentication is complete.
		    	synchronized(mAuthenticationLock)
		    	{
		    		bAuthenticating = false;
		    		mAuthenticationLock.notifyAll();
		    	}
                createTable();
		    }
	    }   



6. Fügen Sie in der Datei "ToDoActivity.java" diesen Code für eine neue `RefreshTokenCacheFilter`-Klasse in der "ToDoActivity"-Klasse hinzu:

		/**
		* The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
		 * When 401 is encountered, the filter calls the authenticate method on the 
		 * UI thread. Out going requests and retries are blocked during authentication. 
		 * Once authentication is complete, the token cache is updated and 
		 * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
		 * that request.   
		 */
		private class RefreshTokenCacheFilter implements ServiceFilter {
		 
	        AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();                     
	        
	        @Override
	        public ListenableFuture<ServiceFilterResponse> handleRequest(
	        		final ServiceFilterRequest request, 
	        		final NextServiceFilterCallback nextServiceFilterCallback
	        		)
	        {
	            // In this example, if authentication is already in progress we block the request
	            // until authentication is complete to avoid unnecessary authentications as 
	            // a result of HTTP status code 401. 
	            // If authentication was detected, add the token to the request.
	            waitAndUpdateRequestToken(request);
	 
	            // Send the request down the filter chain
	            // retrying up to 5 times on 401 response codes.
	            ListenableFuture<ServiceFilterResponse> future = null;
	            ServiceFilterResponse response = null;
	            int responseCode = 401;
	            for (int i = 0; (i < 5 ) && (responseCode == 401); i++)
	            {
	                future = nextServiceFilterCallback.onNext(request);
	                try {
	                    response = future.get();
	                    responseCode = response.getStatus().getStatusCode();
	                } catch (InterruptedException e) {
	                   e.printStackTrace();
	                } catch (ExecutionException e) {
	                    if (e.getCause().getClass() == MobileServiceException.class)
	                    {
	                        MobileServiceException mEx = (MobileServiceException) e.getCause();
	                        responseCode = mEx.getResponse().getStatus().getStatusCode();
	                        if (responseCode == 401)
	                        {
	                            // Two simultaneous requests from independent threads could get HTTP status 401. 
	                            // Protecting against that right here so multiple authentication requests are
	                            // not setup to run on the UI thread.
	                            // We only want to authenticate once. Requests should just wait and retry 
	                            // with the new token.
	                            if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                                                                                                      
	                            {
	                                // Authenticate on UI thread
	                                runOnUiThread(new Runnable() {
	                                    @Override
	                                    public void run() {
	                                        // Force a token refresh during authentication.
	                                        authenticate(true);
				// ToDoActivity.mMainActivity.authenticate(true);
	
	                                    }
	                                });
	                            }
	
				                // Wait for authentication to complete then update the token in the request. 
				                waitAndUpdateRequestToken(request);
				                mAtomicAuthenticatingFlag.set(false);                                                  
	                        }
	                    }
	                }
	            }
	            return future;
	        }
		}


    Dieser Service-Filter überprüft jede Antwort auf den HTTP-Statuscode 401 "Nicht autorisiert". Wird er gefunden, dann wird eine neue Loginanforderung auf dem UI-Thread festgelegt, um ein neues Token abzurufen. Andere Aufrufe werden blockiert, bis die Anmeldung abgeschlossen ist, oder bis 5 Versuche fehlgeschlagen sind. Sobald das neue Token abgerufen wurde, werden die Anforderung, die 401 ausgelöst hatte, sowie die blockierten Aufrufe mit dem neuen Token wiederholt. 

7. Aktualisieren Sie in der Datei "ToDoActivity.java" die `onCreate`-Methode wie folgt:

		@Override
	    public void onCreate(Bundle savedInstanceState) {
		    super.onCreate(savedInstanceState);
		    
		    setContentView(R.layout.activity_to_do);
		    mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);
        
		    // Initialize the progress bar
		    mProgressBar.setVisibility(ProgressBar.GONE);
        
		    try {
		    	// Create the Mobile Service Client instance, using the provided
		    	// Mobile Service URL and key
		    	mClient = new MobileServiceClient(
		    			"https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
		    			"<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());
            
		    	// Authenticate passing false to load the current token cache if available.
		    	authenticate(false);
		        	
		    } catch (MalformedURLException e) {
			    createAndShowDialog(new Exception("Error creating the Mobile Service. " +
			        "Verify the URL"), "Error");
		    }
	    }


       In diesem Code wird `RefreshTokenCacheFilter` zusätzlich zu `ProgressFilter` verwendet. Der Tokencache soll auch während `onCreate` geladen werden. Daher wird `false` an die `authenticate`-Methode übergeben.



<!--HONumber=42-->
