# Visão geral    
## [Sobre os Aplicativos Web](app-service-web-overview.md)
## [Comparar opções de hospedagem](choose-web-site-cloud-service-vm.md)

# Início rápido    
## [Criar site HTML estático](app-service-web-get-started-html.md)
## [Criar aplicativo do ASP.NET](app-service-web-get-started-dotnet.md)        
## [Criar aplicativo PHP](app-service-web-get-started-php.md) 
## [Criar aplicativo Node.js](app-service-web-get-started-nodejs.md) 
## [Criar aplicativo Java](app-service-web-get-started-java.md)        
## [Criar aplicativo Python](app-service-web-get-started-python.md)    

# Exemplos
## [CLI do Azure](app-service-cli-samples.md) 
## [PowerShell](app-service-powershell-samples.md) 

# Tutoriais

# Conceitos
## [Como funciona o Serviço de Aplicativo](../app-service/app-service-how-works-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [Planos do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [Ambientes do Serviço de Aplicativo](app-service-app-service-environment-intro.md)
## [Autenticação e autorização](../app-service/app-service-authentication-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
## [Autenticação com o AD local](web-sites-authentication-authorization.md)


# Guias de Instruções
## Desenvolver seu aplicativo    
### ASP.NET
#### [Aplicativo ASP.NET com Banco de dados SQL](web-sites-dotnet-get-started.md)
#### [Desenvolver um aplicativo ASP.NET Core com código VS](web-sites-create-web-app-using-vscode.md)
### PHP
#### [Aplicativo Laravel com MySQL](app-service-web-php-get-started.md)
#### [Configurar o seu projeto em PHP](web-sites-php-configure.md)
#### [Configurar multissite em WordPress](web-sites-php-convert-wordpress-multisite.md) 
### Node.js
#### [Aplicativo Sails.js com Banco de dados NOSQL](app-service-web-nodejs-sails.md)
#### [Usar io.js](web-sites-nodejs-iojs.md)
#### [Depurar aplicativo Node.js](web-sites-nodejs-debug.md)
### Java
#### [Aplicativo Java com Eclipse](app-service-web-eclipse-create-hello-world-web-app.md)
#### [Aplicativo Java com IntelliJ](app-service-web-intellij-create-hello-world-web-app.md)
#### [Usar SDK do Azure para Java](java-create-azure-website-using-java-sdk.md)
#### [Carregar aplicativo existente](web-sites-java-add-app.md)
#### [Depuração remota do Eclipse](app-service-web-debug-java-web-app-in-eclipse.md)
#### [Depuração remota do IntelliJ](app-service-web-debug-java-web-app-in-intellij.md)
### Python
#### [Aplicativo do Django com o MySQL](web-sites-python-ptvs-django-mysql.md)

### [Enviar emails com SendGrid](sendgrid-dotnet-how-to-send-email.md)

### Configurar tempo de execução    
#### [PHP no Windows](web-sites-php-configure.md)

#### [Java](web-sites-java-add-app.md)
#### [Node.js no Linux](app-service-linux-using-nodejs-pm2.md)
#### [Python](web-sites-python-configure.md)
            
### Configurar o aplicativo
#### [Usar configurações do aplicativo](web-sites-configure.md)
            
## [Implantar no Azure](web-sites-deploy.md)
### [Implantar por FTP](app-service-deploy-content-sync.md)
### [Implantar por sincronização de nuvem](web-sites-deploy.md)
### [Implantar continuamente](app-service-continuous-deployment.md)
### [Implantar no preparo](web-sites-staged-publishing.md)
### [Implantar a partir do Git local](app-service-deploy-local-git.md)
### [Implantar com modelo](app-service-deploy-complex-application-predictably.md)
### [Implantação agile](app-service-agile-software-development.md)
### [Teste beta](app-service-web-test-in-production-controlled-test-flight.md)

### [Definir credenciais de implantação](app-service-deployment-credentials.md)

### Mapear domínio personalizado
#### [Comprar domínio](custom-dns-web-site-buydomains-web-app.md)
#### [Mapear domínio de terceiros](web-sites-custom-domain-name.md)
#### [Mapear domínios com o Gerenciador de Tráfego](web-sites-traffic-manager-custom-domain-name.md)
#### [Mapear domínios GoDaddy](web-sites-godaddy-custom-domain-name.md)
#### [Migrar um domínio ativo](app-service-custom-domain-name-migrate.md)

### [Migrar do IIS](web-sites-migration-from-iis-server.md)
### [Teste em produção](app-service-web-test-in-production-get-start.md)
### [Adicionar funcionalidade a um aplicativo Web](app-service-web-get-started-2.md)

## Conectar ao banco de dados/recursos        

### [Conectar a dados locais](web-sites-hybrid-connection-get-started.md) 
### [Conectar a rede virtual do Azure](web-sites-integrate-with-vnet.md)
### [Conectar-se à rede virtual do Azure com o PowerShell](app-service-vnet-integration-powershell.md)
### [Conectar-se ao MongoDB em VM do Azure](web-sites-dotnet-store-data-mongodb-vm.md)
            
##Proteger o aplicativo
### Autenticar usuários        
#### [Autenticar com o Azure AD](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com o Facebook](../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com o Google](../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com a conta da Microsoft](../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com o Twitter](../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com o AD local](web-sites-authentication-authorization.md)
#### [Aplicativo com um banco de dados multilocatário](web-sites-dotnet-entity-framework-row-level-security.md)
### Atribuir SSL personalizado
#### [Comprar certificado SSL](web-sites-purchase-ssl-web-site.md)
#### [Configurar certificado SSL de terceiros](web-sites-configure-ssl-certificate.md)
### [Impor HTTPS](web-sites-configure-ssl-certificate.md#enforce-https-on-your-app)
### [Configurar a autenticação mútua TLS](app-service-web-configure-tls-mutual-auth.md)
            
##Escalar aplicativo        
### [Escalar verticalmente](web-sites-scale.md)
### [Escalar horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Balanceamento de carga com o Gerenciador de Tráfego](web-sites-traffic-manager.md)
### [Escala elevada com ambientes do Serviço de Aplicativo](../app-service/app-service-app-service-environments-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Usar o CDN do Azure para alcance global](cdn-websites-with-cdn.md)
### [Conectar-se à Cache Redis por meio do protocolo Memcache](web-sites-connect-to-redis-using-memcache-protocol.md)
### [Criar uma Cache Redis](../redis-cache/cache-redis-cache-arm-provision.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Gerenciar estado de sessão com o cache Redis do Azure](web-sites-dotnet-session-state-caching.md)

## Monitoramento 
### [Monitorar aplicativos](web-sites-monitor.md)
### [Habilitar logs](web-sites-enable-diagnostic-log.md)
### [Logs de transmissão](web-sites-streaming-logs-and-console.md)

## Fazer backup do conteúdo        
### [Fazer backup de seu aplicativo](web-sites-backup.md)
### [Restaurar seu aplicativo a partir do backup](web-sites-restore.md)
### [Fazer backup com a API REST](websites-csm-backup.md)

## Gerenciar recursos de aplicativos
### [Clonar aplicativo com o PowerShell](app-service-web-app-cloning.md)
### [Clonar aplicativo com o portal](app-service-web-app-cloning-portal.md)
### [Mover recursos](app-service-move-resources.md)
### [Usar o Azure Resource Manager com PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
### [Gerenciar aplicativos usando a Automação do Azure](automation-manage-web-app.md)



# Referência    
## [CLI 2.0](/cli/azure/appservice)
## [PowerShell](/powershell)
## [API REST](/rest/api/appservice/) 
        
# Recursos    
## Solucionar problemas        
### [Solucionar problemas com o Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
### [Solucionar problemas de aplicativo do Node.js](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
### [Solucionar problemas HTTP 502 e 503](app-service-web-troubleshoot-http-502-http-503.md)
### [Solucionar problemas de desempenho](app-service-web-troubleshoot-performance-degradation.md)
## [Preços](https://azure.microsoft.com/pricing/details/app-service/)     
## [Informações de quota](../azure-subscription-service-limits.md#app-service-limits)    
## [Atualizações de serviço e Notas de versão](https://azure.microsoft.com/updates/?product=app-service)    
## [práticas recomendadas](app-service-best-practices.md)
## [Exemplos](https://azure.microsoft.com/resources/samples/?service=app-service)    
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=app-service)
## Guias    
### [Arquiteturas de referência](../guidance/guidance-ra-app-service.md)    
### [Scripts de implantação](https://azure.microsoft.com/documentation/scripts/)    
