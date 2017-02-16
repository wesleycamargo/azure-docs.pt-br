Nosso cache de token deve funcionar em casos simples, mas o que acontece quando o token expira ou é revogado? O token pode expirar quando o aplicativo não está em execução. Isso significa que o cache de token é inválido. O token também pode expirar enquanto o aplicativo estiver realmente em execução. O resultado é um código de status HTTP 401, "Não autorizado". 

Precisamos ser capazes de detectar um token expirado e atualizá-lo. Para fazer isso, usamos um [ServiceFilter](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html) da [biblioteca cliente do Android](http://dl.windowsazure.com/androiddocs/).

Nesta seção, você definirá um ServiceFilter que detectará uma resposta de código de status HTTP 401 e acionará uma atualização do token e do cache de token. Além disso, este ServiceFilter bloqueará outras solicitações de saída durante a autenticação, de modo que essas solicitações possam usar o token atualizado.

1. Abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação:
   
        import java.util.concurrent.atomic.AtomicBoolean;
        import java.util.concurrent.ExecutionException;
   
        import com.microsoft.windowsazure.mobileservices.MobileServiceException;
2. Adicione os seguintes membros à classe `ToDoActivity` : 
   
        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();
   
    Eles serão usados para sincronizar a autenticação do usuário. É necessário autenticar somente uma vez. Quaisquer chamadas realizadas durante uma autenticação devem aguardar e usar o novo token da autenticação em andamento.
3. No arquivo ToDoActivity.java, adicione o seguinte método à classe ToDoActivity, que será usado para bloquear chamadas de saída em outros threads enquanto a autenticação estiver em andamento.
   
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
4. No arquivo ToDoActivity.java, adicione o seguinte método à classe ToDoActivity. Este método desencadeará a espera e atualizará o token em solicitações de saída quando a autenticação estiver concluída. 

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


1. No arquivo ToDoActivity.java, atualize o método `authenticate` da classe ToDoActivity para que ela aceite um parâmetro booliano, permitindo que a atualização do token e do cache de token seja forçada. Nós também precisamos notificar os threads bloqueados quando a autenticação for concluída para que eles possam obter o novo token.
   
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
2. No arquivo ToDoActivity.java, adicione este código para uma nova classe `RefreshTokenCacheFilter` na classe ToDoActivity:
   
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

    Este filtro de serviço verificará cada resposta ao código de status HTTP 401, “Não autorizado”. Se um 401 for encontrado, um novo login solicita a obtenção de um novo token, que será configurado no thread de IU. Outras chamadas serão bloqueadas até que o logon seja concluído ou até 5 tentativas falharem. Quando o novo token é obtido, a solicitação que desencadeou o 401 será realizada novamente com o novo token e quaisquer chamadas bloqueadas serão realizadas novamente com o novo token. 

1. No arquivo ToDoActivity.java, adicione este código para uma nova classe `ProgressFilter` na classe ToDoActivity:
   
        /**
        * The ProgressFilter class renders a progress bar on the screen during the time the App is waiting for the response of a previous request.
        * the filter shows the progress bar on the beginning of the request, and hides it when the response arrived.
        */
        private class ProgressFilter implements ServiceFilter {
            @Override
            public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback nextServiceFilterCallback) {
   
                final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
   
                runOnUiThread(new Runnable() {
   
                    @Override
                    public void run() {
                        if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
                    }
                });
   
                ListenableFuture<ServiceFilterResponse> future = nextServiceFilterCallback.onNext(request);
   
                Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
                    @Override
                    public void onFailure(Throwable e) {
                        resultFuture.setException(e);
                    }
   
                    @Override
                    public void onSuccess(ServiceFilterResponse response) {
                        runOnUiThread(new Runnable() {
   
                            @Override
                            public void run() {
                                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.GONE);
                            }
                        });
   
                        resultFuture.set(response);
                    }
                });
   
                return resultFuture;
            }
        }
   
    Esse filtro mostrará a barra de progresso no início da solicitação e a ocultará quando chegar a resposta.
2. No arquivo ToDoActivity.java, atualize o método `onCreate` da seguinte maneira:
   
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

       In this code, `RefreshTokenCacheFilter` is used in addition to `ProgressFilter`. Also during `onCreate` we want to load the token cache. So `false` is passed in to the `authenticate` method.




<!--HONumber=Jan17_HO3-->


