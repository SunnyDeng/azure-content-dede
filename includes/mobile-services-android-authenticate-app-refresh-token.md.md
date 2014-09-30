Unser Tokencache sollte normalerweise funktionieren, doch was tun, wenn das Token abläuft oder widerrufen wird? Wir müssen daher in der Lage sein, abgelaufene Token zu erkennen. Das Token kann beispielsweise abgelaufen sein, wenn die App nicht ausgeführt wurde. Dann ist der Tokencache ungültig. Das Token kann auch abgelaufen sein, wenn die App ausgeführt wird und einen direkten Aufruf ausführt oder ein Aufruf von der Mobile Services-Bibliothek ausgeführt wird. Ergebnis ist der HTTP-Statuscode 401 "Nicht autorisiert". Solche Fälle müssen wir erkennen und das Token aktualisieren. Dazu verwenden wir einen [ServiceFilter][] aus der [Android-Clientbibliothek][].

In diesem Abschnitt definieren Sie einen ServiceFilter, der einen HTTP-Statuscode 401 erkennt und die Aktualisierung des Tokens und des Tokencaches auslöst. Zusätzlich blockiert dieser ServiceFilter während der Authentifizierung andere ausgehende Anforderungen, sodass diese Anforderungen das aktualisierte Token verwenden können.

1.  Öffnen Sie in Eclipse die Datei ToDoActivity.java und fügen Sie die folgenden Importanweisungen hinzu:

        import java.util.concurrent.atomic.AtomicBoolean;

2.  Fügen Sie in der Datei ToDoActivity.java die folgenden Member zur ToDoActivity-Klasse hinzu.

        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();

    Sie werden verwendet, um die Authentifizierung des Benutzers zu synchronisieren. Die Authentifizierung soll nur einmal vorgenommen werden. Aufrufe während der Authentifizierung sollten warten und das Token der gerade laufenden Authentifizierung verwenden.

3.  Fügen Sie in der Datei ToDoActivity.java die folgende Methode zur ToDoActivity-Klasse hinzu, um während der Authentifizierung ausgehende Aufrufe auf anderen Threads zu blockieren.

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

4.  Fügen Sie in der Datei ToDoActivity.java die folgende Methode zur ToDoActivity-Klasse hinzu. Diese Methode löst die Wartezeit aus und aktualisiert das Token der ausgehenden Anforderungen, wenn die Authentifizierung abgeschlossen ist.

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

5.  Aktualisieren Sie in der Datei ToDoActivity.java die `authenticate`-Methode der ToDoActivity-Klasse, sodass diese einen booleschen Parameter akzeptiert, um die Erzwingung der Aktualisierung des Tokens und des Tokencaches zu ermöglichen. Außerdem müssen die blockierten Threads benachrichtigt werden, wenn die Authentifizierung abgeschlossen ist, damit sie das neue Token übernehmen.

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

6.  Fügen Sie in der Datei ToDoActivity.java die folgende Definition für die `RefreshTokenCacheFilter`-Klasse zur ToDoActivity-Klasse hinzu:

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

            /**
             * The AuthenticationRetryFilterCallback class is a wrapper around the response 
             * callback that encapsulates the request and other information needed to enable 
             * a retry of the request when HTTP status code 401 is encountered. 
             */
            private class AuthenticationRetryFilterCallback implements ServiceFilterResponseCallback
            {
                // Data members used to retry the request during the response.
                ServiceFilterRequest mRequest;
                NextServiceFilterCallback mNextServiceFilterCallback;
                ServiceFilterResponseCallback mResponseCallback;

                public AuthenticationRetryFilterCallback(ServiceFilterRequest request, 
                        NextServiceFilterCallback nextServiceFilterCallback, 
                        ServiceFilterResponseCallback responseCallback)
                {
                    mRequest = request;
                    mNextServiceFilterCallback = nextServiceFilterCallback;
                    mResponseCallback = responseCallback;
                }

                @Override
                public void onResponse(ServiceFilterResponse response, Exception exception) {

                    // Filter out the 401 responses to update the token cache and 
                    // retry the request
                    if ((response != null) && (response.getStatus().getStatusCode() == 401))
                    { 
                        // Two simultaneous requests from independent threads could get HTTP 
                        // status 401. Protecting against that right here so multiple 
                        // authentication requests are not setup to run on the UI thread.
                        // We only want to authenticate once. Other requests should just wait 
                        // and retry with the new token.
                        if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                           
                        {
                            // Authenticate on UI thread
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    // Force a token refresh during authentication.
                                    authenticate(true);
                                }
                            });
                        }

                        // Wait for authentication to complete then 
                        // update the token in the request.
                        waitAndUpdateRequestToken(this.mRequest);
                        mAtomicAuthenticatingFlag.set(false);                       

                        // Retry recursively with a new token as long as we get a 401.
                        mNextServiceFilterCallback.onNext(this.mRequest, this);
                    }

                    // Responses that do not have 401 status codes just pass through.
                    else if (this.mResponseCallback != null)  
                      mResponseCallback.onResponse(response, exception);
                }
            }


            @Override
            public void handleRequest(final ServiceFilterRequest request, 
                final NextServiceFilterCallback nextServiceFilterCallback,
                ServiceFilterResponseCallback responseCallback) {

                // In this example, if authentication is already in progress we block the request
                // until authentication is complete to avoid unnecessary authentications as 
                // a result of HTTP status code 401. 
                // If authentication was detected, add the token to the request.
                waitAndUpdateRequestToken(request);

                // Wrap the request in a callback object that will facilitate retries.
                AuthenticationRetryFilterCallback retryCallbackObject = 
                    new AuthenticationRetryFilterCallback(request, nextServiceFilterCallback,
                          responseCallback); 

                // Send the request down the filter chain.
                nextServiceFilterCallback.onNext(request, retryCallbackObject);         
            }
        }

    Dieser Service-Filter überprüft jede Antwort auf den HTTP-Statuscode 401 "Nicht autorisiert". Wird er gefunden, dann wird eine neue Loginanforderung auf dem UI-Thread festgelegt, um ein neues Token abzurufen. Andere Aufrufe werden blockiert, bis die Anmeldung abgeschlossen ist. Sobald das neue Token abgerufen wurde, werden die Anforderung, die 401 ausgelöst hatte, sowie die blockierten Aufrufe mit dem neuen Token wiederholt.

7.  Aktualisieren Sie in der Datei ToDoActivity.java die `onCreate`-Methode wie folgt:

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

    In diesem Code wird der RefreshTokenCacheFilter zusätzlich zum ProgressFilter verwendet. Der Tokencache soll auch während `onCreate` geladen werden. Daher wird false an die `authenticate`-Methode übergeben.

  [ServiceFilter]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html
  [Android-Clientbibliothek]: http://dl.windowsazure.com/androiddocs/
