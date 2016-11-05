---
title: Criar, configurar e implantar um aplicativo Web PHP no Azure
description: Um tutorial que mostra como executar um aplicativo Web PHP (Laravel) no Serviço de Aplicativo do Azure. Saiba como configurar o Serviço de Aplicativo do Azure para atender aos requisitos da estrutura PHP que você escolher.
services: app-service\web
documentationcenter: php
author: cephalin
manager: wpickett
editor: ''
tags: mysql

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/03/2016
ms.author: cephalin

---
# <a name="create,-configure,-and-deploy-a-php-web-app-to-azure"></a>Criar, configurar e implantar um aplicativo Web PHP no Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar, configurar e implantar um aplicativo Web PHP para o Azure e como configurar o Serviço de Aplicativo do Azure para atender aos requisitos do aplicativo Web PHP. Ao fim do tutorial, você terá um aplicativo Web [Laravel](https://www.laravel.com/) funcional em execução no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

Como desenvolvedor do PHP, você pode incluir sua estrutura favorita do PHP no Azure. Este tutorial usa Laravel simplesmente como um exemplo concreto de aplicativo. Você aprenderá: 

* Implantar usando Git
* Definir a versão do PHP
* Usar um arquivo de inicialização que não está no diretório raiz do aplicativo
* Acessar variáveis de ambiente específicas
* Atualizar o aplicativo no Azure

Você pode aplicar o que aprendeu aqui para outros aplicativos Web PHP que implantar no Azure.

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Instalar o [PHP 5.6.x](http://php.net/downloads.php) (o suporte ao PHP 7 está na versão beta)
* Instalar o [Composer](https://getcomposer.org/download/)
* Instalar a [CLI do Azure](../xplat-cli-install.md)
* Instalar o [Git](http://www.git-scm.com/downloads)
* Obtenha uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Veja um aplicativo Web em ação. [Experimente o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751) imediatamente e crie um aplicativo inicializador de curta duração, sem necessidade de cartão de crédito e sem compromisso.
> 
> 

## <a name="create-a-php-(laravel)-app-on-your-dev-machine"></a>Criar um aplicativo PHP (Laravel) em seu computador de desenvolvimento
1. Abra um novo prompt de comando do Windows, janela do PowerShell, shell do Linux ou terminal do OS X. Execute os comandos a seguir para verificar se as ferramentas necessárias estão instaladas corretamente no computador. 
   
        php --version
        composer --version
        azure --version
        git --version
   
    ![Teste a instalação das ferramentas antes de criar o aplicativo PHP (Laravel) para o Azure](./media/app-service-web-php-get-started/test-tools.png)
   
    Se ainda não tiver instalado as ferramentas, confira [Pré-requisitos](#Prerequisites) para obter links de download.
2. Instale o Laravel da seguinte forma:
   
        composer global require "laravel/installer
3. `CD` em um diretório de trabalho e crie um novo aplicativo Laravel desta forma:
   
        cd <working_directory>
        laravel new <app_name>
4. `CD` no diretório `<app_name>` recém-criado e teste o aplicativo da seguinte forma:
   
        cd <app_name>
        php artisan serve
   
    Agora você poderá navegar até http://localhost:8000 em um navegador e ver a tela inicial do Laravel.
   
    ![Teste o aplicativo PHP (Laravel) localmente antes de implantá-lo no Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)

Até o momento, há apenas o fluxo de trabalho regular do Laravel, e você não está aqui para <a href="https://laravel.com/docs/5.2" rel="nofollow">aprender sobre o Laravel</a>. Vamos continuar.

## <a name="create-an-azure-web-app-and-set-up-git-deployment"></a>Criar um aplicativo Web do Azure e configurar a implantação do Git
> [!NOTE]
> "Espere! E se eu quiser implantar com o FTP?" Há um [tutorial sobre o FTP](web-sites-php-mysql-deploy-use-ftp.md) para suas necessidades. 
> 
> 

Com a CLI do Azure, você pode criar um aplicativo Web no Serviço de Aplicativo do Azure e configurá-lo para implantação no Git com uma única linha de comando. Vamos fazer isso.

1. Altere para o modo ASM e faça logon no Azure:
   
        azure config mode asm
        azure login
   
    Siga a mensagem de ajuda para continuar o processo de logon.
   
    ![Faça logon no Azure para implantar o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/log-in-to-azure-cli.png)
2. Execute o comando para criar o aplicativo Web com a implantação no Git. Quando solicitado, especifique o número da região desejada.
   
        azure site create --git <app_name>
   
    ![Crie o recurso do Azure para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/create-site-cli.png)
   
   > [!NOTE]
   > Se nunca tiver configurado credenciais de implantação para sua assinatura do Azure, você será solicitado a criá-las. Essas credenciais, não as credenciais de sua conta do Azure, são usadas pelo Serviço de Aplicativo somente para implantações do Git e logons de FTP. 
   > 
   > 
   
    O comando cria um novo repositório Git no diretório atual (com `git init`) e conecta-o ao repositório no Azure como um Git remoto (com `git remote add`).

<a name="configure"/>

## <a name="configure-the-azure-web-app"></a>Configurar o aplicativo Web do Azure
Para que o aplicativo Laravel funcione no Azure, você precisa prestar atenção a várias coisas. Você fará este exercício semelhante para a estrutura PHP de sua escolha.

* Configure o PHP 5.5.9 ou superior. Confira [Últimos requisitos do servidor Laravel 5.2](https://laravel.com/docs/5.2#server-requirements) para obter toda a lista de requisitos do servidor. O restante da lista consiste em extensões que já estão habilitadas por instalações de PHP do Azure. 
* Defina as variáveis de ambiente de que o aplicativo precisa. O Laravel usa o arquivo `.env` para facilitar a configuração de variáveis de ambiente. No entanto, já que não deve ser confirmado no controle de origem (confira [Configuração do ambiente Laravel](https://laravel.com/docs/5.2/configuration#environment-configuration), você definirá as configurações do aplicativo Web do Azure em vez disso.
* Verifique se o ponto de entrada do aplicativo Laravel, `public/index.php`, é carregado primeiro. Confira [Visão geral do ciclo de vida Laravel](https://laravel.com/docs/5.2/lifecycle#lifecycle-overview). Em outras palavras, você precisa definir a URL raiz do aplicativo Web para apontar para o diretório `public` .
* Habilite a extensão do Composer no Azure, já que você tem um composer.json. Assim, você poderá deixar que o Composer se preocupe sobre como obter os pacotes necessários quando você implantar com o `git push`. É uma questão de conveniência. Se você não habilitar a automação do Composer, bastará remover `/vendor` do arquivo `.gitignore` para que o Git inclua (deixe de ignorar) tudo no diretório `vendor` ao confirmar e implantar o código.

Vamos configurar essas tarefas sequencialmente.

1. Defina a versão do PHP que o aplicativo Laravel requer.
   
        azure site set --php-version 5.6
   
    Você terminou de definir a versão do PHP. 
2. Gere uma nova `APP_KEY` para o aplicativo Web do Azure e defina-a como uma configuração de aplicativo para o aplicativo Web do Azure.
   
        php artisan key:generate --show
        azure site appsetting add APP_KEY="<output_of_php_artisan_key:generate_--show>"
3. Além disso, ative a depuração do Laravel para antecipar-se a qualquer página `Whoops, looks like something went wrong.` complicada.
   
        azure site appsetting add APP_DEBUG=true
   
    Você terminou de definir as variáveis de ambiente.
   
   > [!NOTE]
   > Aguarde: vamos desacelerar um pouco e explicar o que o Laravel faz e o que o Azure faz aqui. O Laravel usa o arquivo `.env` no diretório raiz para fornecer variáveis de ambiente ao aplicativo, em que você encontrará a linha `APP_DEBUG=true` (e também `APP_KEY=...`). Essa variável é acessada em `config/app.php` pelo código     `'debug' => env('APP_DEBUG', false),`. [env()](https://laravel.com/docs/5.2/helpers#method-env) é um método auxiliar Laravel que usa o PHP [getenv()](http://php.net/manual/en/function.getenv.php) nos bastidores.
   > 
   > No entanto, `.env` é ignorado pelo Git porque é chamado pelo arquivo `.gitignore` no diretório raiz. Em outras palavras, `.env` 
   > no repositório Git local não é enviado ao Azure com o restante dos arquivos. Obviamente, você pode remover essa linha de `.gitignore`, mas já estabelecemos que não é recomendado confirmar esse arquivo no controle de origem. No entanto, você ainda precisa de uma maneira para especificar essas variáveis de ambiente no Azure. 
   > 
   > A boa notícia é que as configurações de aplicativo no Serviço de Aplicativo do Azure dão suporte a [getenv()](http://php.net/manual/en/function.getenv.php) 
   > no PHP. Portanto, embora possa usar o FTP ou outros meios para carregar manualmente um arquivo `.env` no Azure, você pode especificar apenas as variáveis que deseja como configurações de aplicativo do Azure, sem um `.env` no Azure, como acabou de fazer. Além disso, se uma variável estiver em um arquivo `.env` e em configurações de aplicativo do Azure, a configuração de aplicativo do Azure prevalecerá.     
   > 
   > 
4. As duas últimas tarefas (definir o diretório virtual e habilitar o Composer) exigem o [portal do Azure](https://portal.azure.com). Portanto, faça logon no [portal](https://portal.azure.com) com sua conta do Azure.
5. No menu à esquerda, clique em **Serviços de Aplicativos** > **&lt;nome_do_aplicativo>** > **Ferramentas**.
   
    ![Habilite o Composer para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
   
   > [!TIP]
   > Se clicar em **Configurações** em vez de **Ferramentas**, você poderá acessar a folha **Configurações de Aplicativo** 
   > , que permite definir versões do PHP, configurações de aplicativo e diretórios virtuais, como acabou de fazer. 
   > 
   > 
6. Clique em **Extensões** > **Adicionar** para adicionar uma extensão.
7. Selecione **Composer** in the **folha** [Escolher extensão](../azure-portal-overview.md) (*Escolher extensão*: uma página do portal que é aberta horizontalmente).
8. Clique em **OK** na folha **Aceitar os termos legais**. 
9. Clique em **OK** na folha **Adicionar extensão**.
   
    Quando o Azure terminar de adicionar a extensão, você deverá ver uma mensagem pop-up amigável no canto, bem como o  **Composer** listado na folha **Extensões**.
   
    ![Folha de extensões depois de habilitar o Composer para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-composer-end.png)
   
    Você terminou de habilitar o Composer.
10. De volta à folha do aplicativo Web, clique em **Configurações** > **Configurações do Aplicativo**.
    
     ![Acesse a folha Configurações para definir o diretório virtual para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)
    
     Na folha **Configurações do Aplicativo** , observe a versão do PHP definida anteriormente:
    
     ![Versão do PHP na folha Configurações para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a.png)
    
     e as configurações de aplicativo que você adicionou:
    
     ![Configurações de aplicativo na folha Configurações para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)
11. Role até a parte inferior da folha e altere o diretório virtual raiz para apontar para **site\wwwroot\public** em vez de **site\wwwroot**.
    
     ![Defina o diretório virtual para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)
12. Clique em **Salvar** na parte superior da folha.
    
     Você terminou de definir o diretório virtual. 

## <a name="deploy-your-web-app-with-git-(and-setting-environment-variables)"></a>Implantar o aplicativo Web com o Git (e definir variáveis de ambiente)
Você está pronto para implantar o código agora. Você fará isso no prompt de comando ou terminal.

1. Confirme todas as alterações e implante o código no aplicativo Web da maneira como faria em qualquer repositório Git:
   
        git add .
        git commit -m "Hurray! My first commit for my Azure app!"
        git push azure master 
   
    Ao executar `git push`, você será solicitado a fornecer sua senha de implantação do Git. Se você precisou criar credenciais de implantação em `azure site create` anteriormente, digite a senha que usou.
2. Vamos ver isso em execução no navegador executando este comando:
   
        azure site browse
   
    O navegador deve mostrar a tela inicial do Laravel.
   
    ![Tela inicial do Laravel após implantar o aplicativo Web no Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
   
    Parabéns, agora você está executando um aplicativo Web Laravel no Azure.

## <a name="troubleshoot-common-errors"></a>Solução de problemas comuns
Aqui estão alguns os erros que você pode encontrar ao seguir este tutorial:

* [A CLI do Azure mostra "'site' não é um comando do azure"](#clierror)
* [O aplicativo Web mostra o erro HTTP 403](#http403)
* [O aplicativo Web mostra "Opa, parece que algo deu errado".](#whoops)
* [O aplicativo Web mostra "Nenhum criptografador com suporte encontrado".](#encryptor)

<a name="clierror"></a>

### <a name="azure-cli-shows-"'site'-is-not-an-azure-command""></a>A CLI do Azure mostra "'site' não é um comando do azure"
Ao executar `azure site *` no terminal de linha de comando, você verá o erro `error:   'site' is not an azure command. See 'azure help'.` 

Isso geralmente ocorre devido à alternância para o modo "ARM" (Azure Resource Manager). Para resolver o problema, alterne novamente para o modo "ASM" (Gerenciamento de Serviços do Azure) executando `azure config mode asm`.

<a name="http403"></a>

### <a name="web-app-shows-http-403-error"></a>O aplicativo Web mostra o erro HTTP 403
Você implantou o aplicativo Web no Azure com êxito, mas, ao navegar para o aplicativo Web, obtém um `HTTP 403` ou `You do not have permission to view this directory or page.`

Isso provavelmente ocorre porque o aplicativo Web não consegue encontrar o ponto de entrada para o aplicativo Laravel. Verifique se você alterou o diretório raiz virtual para apontar para `site\wwwroot\public`, em que o `index.php` do Laravel está (confira [Configurar o aplicativo Web do Azure](#configure)).

<a name="whoops"></a>

### <a name="web-app-shows-"whoops,-looks-like-something-went-wrong.""></a>O aplicativo Web mostra "Opa, parece que algo deu errado".
Você implantou o aplicativo Web no Azure com êxito, mas, ao navegar para o aplicativo Web, recebe a mensagem indecifrável `Whoops, looks like something went wrong.`

Para obter um erro mais descritivo, habilite a depuração do Laravel definindo a variável de ambiente `APP_DEBUG` como `true` (confira [Configurar o aplicativo Web do Azure](#configure)).

<a name="encryptor"></a>

### <a name="web-app-shows-"no-supported-encryptor-found.""></a>O aplicativo Web mostra "Nenhum criptografador com suporte encontrado".
Você implantou o aplicativo Web no Azure com êxito, mas, ao navegar para o aplicativo Web, recebe a mensagem de erro abaixo:

![APP_KEY ausente no aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)

Esse é um erro desagradável, mas, pelo menos, não é indecifrável, pois você ativou a depuração do Laravel. Uma pesquisa superficial da cadeia de caracteres de erro nos fóruns do Laravel mostrará que isso ocorreu porque a APP_KEY não foi definida em `.env` ou, no seu caso, `.env` não está presente no Azure. Você pode corrigir isso adicionando a configuração `APP_KEY` como uma configuração de aplicativo do Azure (confira [Configurar o aplicativo Web do Azure](#configure)).

## <a name="next-steps"></a>Próximas etapas
Saiba como adicionar dados ao aplicativo [criando um banco de dados MySQL no Azure](../store-php-create-mysql-database.md). Além disso, confira mais links úteis para o PHP no Azure abaixo:

* [Central de Desenvolvimento do PHP](/develop/php/).
* [Criar um aplicativo Web do Azure Marketplace](app-service-web-create-web-app-from-marketplace.md)
* [Configurar o PHP em aplicativos Web do Serviço de Aplicativo do Azure](web-sites-php-configure.md)
* [Converter WordPress em Multissite no Serviço de Aplicativo do Azure](web-sites-php-convert-wordpress-multisite.md)
* [WordPress de classe empresarial no Serviço de Aplicativo do Azure](web-sites-php-enterprise-wordpress.md)

<!--HONumber=Oct16_HO2-->


