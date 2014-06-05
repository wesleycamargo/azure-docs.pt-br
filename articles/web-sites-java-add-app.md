<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Adicionar um aplicativo a seu site Java" pageTitle="Adicionar um aplicativo a seu site Java" metaKeywords="" description="Este tutorial mostra como adicionar uma página ou aplicativo a seu site Java no Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Adicionar um aplicativo a seu site Java" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

# Adicionar um aplicativo a seu site Java no Azure

Este tutorial mostra como adicionar uma página da web ou um aplicativo a um site Java criado anteriormente usando a galeria do Azure ou a interface do usuário da configuração do Azure.

Observe que você pode usar o controle de origem para carregar o aplicativo ou páginas da web, incluindo cenários de integração contínua. As instruções de uso do controle do código-fonte com seu site estão disponíveis em [Publicando do controle do código-fonte para sites do Azure](../web-sites-publish-source-control). Como uma alternativa ao controle do código-fonte, este artigo mostra como usar o FTP para carregar o aplicativo.

Este tutorial pressupõe que você já tenha concluído as etapas em [Introdução ao Microsoft Azure e ao Java](../web-sites-java-get-started).

# Personalizar um site usando FTP
Você precisará determinar suas credenciais FTP e usá-las para acessar o conteúdo do site. Em seguida, você pode modificar o conteúdo para executar o aplicativo. O exemplo mostrado aqui usará o Explorador de Arquivos para facilitar o FTP, mas você também pode usar outras técnicas FTP. 

## Usar credenciais FTP para acessar o conteúdo do site

1. No Portal de Gerenciamento do Microsoft Azure, navegue até a exibição **Sites**.
2. Na exibição **Sites**, clique no nome do seu site.
3. Clique em **Painel**.
4. Na exibição **Painel** exiba, em **Visão Rápida**, clique em **Baixar o perfil de publicação**. Salve o arquivo localmente. Mantenha esse arquivo protegido, pois ele contém o nome de usuário e a senha que permitem publicar em seu site (bem como copiar conteúdo de seu site).
5. Abra o arquivo de Configurações de Publicação baixado usando um editor de texto. Dentro desse arquivo, observe os valores de **userName** e **userPwd**. Eles representam o nome de usuário e a senha, respectivamente, que você usará para acessar os arquivos no site.
6. Acesse os arquivos do seu site, fornecendo o nome de usuário e a senha quando solicitado. Este exemplo usará FTP no Internet Explorer, mas você também pode usar outras técnicas. Para continuar a usar FTP, na exibição **Painel**, clique na URL listada em **Nome do Host FTP**. (Você também pode determinar o nome do host FTP de dentro do arquivo Configurações de Publicação, é o valor atribuído a **publishUrl**.) 
7. Quando for solicitado o nome de usuário e a senha, use os valores especificados no arquivo Configurações de Publicação para **userName** e **userPwd**. 
8. Ainda no Internet Explorer, para alternar para o modo de exibição do Explorador de Arquivos, clique em **Exibir** e, em seguida, clique em **Abrir site FTP no Explorador de Arquivos**.

## Acessar a pasta webapps de seu site

Na exibição Explorador de Arquivos do seu site no Azure, agora você pode personalizar seu site. Você precisará copiar o aplicativo para a pasta **webapps** de seu site. O caminho de navegação para essa pasta varia dependendo de como seu site está configurado.

- Se você configurar seu site usando a galeria de aplicativos do Azure, no Explorador de Arquivos, clique duas vezes em **site**, clique duas vezes em **wwwroot**, clique duas vezes em **bin**, clique duas vezes na versão do servidor de aplicativos aplicação que seu site está usando e, em seguida, clique duas vezes em **webapps**. 
- Se você configurar seu site usando a interface do usuário de configuração do Azure, no Explorador de Arquivos, clique duas vezes em **site**, clique duas vezes em **wwwroot** e, em seguida, clique duas vezes em **webapps**. 
- Se você configurar seu site usando um upload personalizado, navegue conforme necessário para a pasta **webapps**. 

## Para adicionar um arquivo WAR ao seu site usando FTP

1. Navegue até a pasta **webapps** usando a técnica adequada para seu site, conforme descrito acima.
2. Copie o arquivo WAR na pasta **webapps**.

O servidor da aplicativos irá detectar que você adicionou o arquivo WAR e o carregará automaticamente. Em seguida, você pode executar seu aplicativo no navegador, por meio da URL do site com o nome do arquivo WAR anexado a ela. 

Por exemplo, navegue até http://*mysitename*.azurewebsites.net/*mywar*, onde *mysitename* é o nome especificado para a sua URL, e *mywar* é o nome que diferencia maiúsculas de minúsculas do WAR copiado (sem **.war** à direita).

## Para adicionar uma página da web ao seu site usando FTP
1. Navegue até a pasta **webapps**. 
2. Crie uma nova pasta dentro da pasta **webapps**.
3. Abra a nova pasta.
4. Adicione a página da web na nova pasta. 
 
O servidor da aplicativos irá detectar que você adicionou a nova pasta e o arquivo web e os carregará automaticamente. 
Em seguida, execute o arquivo JSP usando a URL no formato http://*mysitename*.azurewebsites.net/*myfolder*/*myfile.jsp*, onde *mysitename* é o nome especificado para a URL *myfolfer* é a pasta que você criou na **webapps**, e *myfile.jsp* é o nome do arquivo JSP que você criou.

Observe que se você copiar arquivos (diferentes de arquivos WAR) no diretório raiz, o servidor de aplicativos precisará ser reiniciado para que esses arquivos sejam usados. A funcionalidade de carregamento automático dos sites Java em execução no Azure baseia-se na adição de um novo arquivo WAR ou da adição de novos arquivos ou diretórios à pasta **webapps**.  


  

