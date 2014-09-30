Nosso cache de token deve funcionar em casos simples, mas o que acontece quando o token expira ou é revogado? Precisamos ser capazes de detectar tokens expirados. O token pode expirar quando o aplicativo não está em execução. Isso significa que o cache de token é inválido. O token também pode expirar enquanto o aplicativo está em execução durante uma chamada feita diretamente pelo aplicativo ou pela biblioteca dos Serviços Móveis. O resultado será um código de status HTTP 401, “Não autorizado". Sendo assim, precisamos ter um modo de detectar e atualizar o token. Para fazer isso, usamos um [ServiceFilter][] da [biblioteca cliente do Android][].

Nesta seção, você definirá um ServiceFilter que detectará uma resposta de código de status HTTP 401 e acionará uma atualização do token e do cache de token. Além disso, este ServiceFilter bloqueará outras solicitações de saída durante a autenticação, de modo que essas solicitações possam usar o token atualizado.

1.  No Eclipse, abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação:

        import java.util.concurrent.atomic.AtomicBoolean;

2.  No arquivo ToDoActivity.java, adicione os seguintes membros à classe ToDoActivity.

        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();

    Eles serão usados para sincronizar a autenticação do usuário. É necessário autenticar somente uma vez. Quaisquer chamadas realizadas durante uma autenticação devem aguardar e usar o novo token da autenticação em andamento.

3.  No arquivo ToDoActivity.java, adicione o seguinte método à classe ToDoActivity, que será usado para bloquear chamadas de saída em outros threads enquanto a autenticação estiver em andamento.

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

4.  No arquivo ToDoActivity.java, adicione o seguinte método à classe ToDoActivity. Este método desencadeará a espera e a atualização do token em solicitações de saída quando a autenticação estiver concluída.

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

5.  No arquivo ToDoActivity.java, atualize o método `authenticate` da classe ToDoActivity para que ela aceite um parâmetro booliano, permitindo que a atualização do token e do cache de token seja forçada. Nós também precisamos notificar os threads bloqueados quando a autenticação for concluída para que eles possam obter o novo token.

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

6.  No arquivo ToDoActivity.java, adicione a seguinte definição à classe `RefreshTokenCacheFilter` na classe ToDoActivity:

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

    Este filtro de serviço verificará cada resposta ao código de status HTTP 401, “Não autorizado”. Se um 401 for encontrado, um novo login solicita a obtenção de um novo token, que será configurado no thread de IU. Outras chamadas serão bloqueadas até que o logon seja concluído. Quando o novo token é obtido, a solicitação que desencadeou o 401 será realizada novamente com o novo token e quaisquer chamadas bloqueadas serão realizadas novamente com o novo token.

7.  No arquivo ToDoActivity.java, atualize o método `onCreate` como a seguir:

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

    Neste código, o RefreshTokenCacheFilter é usado além do ProgressFilter. Também durante `onCreate`, queremos carregar o cache de token. Sendo assim, false é transmitido ao método `authenticate`.

  [ServiceFilter]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html
  [biblioteca cliente do Android]: http://dl.windowsazure.com/androiddocs/
