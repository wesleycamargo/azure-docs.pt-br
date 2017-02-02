---
title: Criar, configurar e implantar um aplicativo Web PHP no Azure | Microsoft Docs
description: "Um tutorial que mostra como executar um aplicativo Web PHP (Laravel) no Serviço de Aplicativo do Azure. Saiba como configurar o Serviço de Aplicativo do Azure para atender aos requisitos da estrutura PHP que você escolher."
services: app-service\web
documentationcenter: php
author: cephalin
manager: wpickett
editor: 
tags: mysql
ms.assetid: cb73859d-48aa-470a-b486-d984746d6d26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: f595be46983bf07783b529de885d889c18fdb61a
ms.openlocfilehash: 6bbbf253b9735695e7ace0c27e4bc96df7a0c779


---
# <a name="create-configure-and-deploy-a-php-web-app-to-azure"></a>Criar, configurar e implantar um aplicativo Web PHP no Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar, configurar e implantar um aplicativo Web PHP para o Azure e como configurar o Serviço de Aplicativo do Azure para atender aos requisitos do aplicativo Web PHP. Ao fim do tutorial, você terá um aplicativo Web [Laravel](https://www.laravel.com/) funcional em execução no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

Como desenvolvedor do PHP, você pode incluir sua estrutura favorita do PHP no Azure. Este tutorial usa Laravel simplesmente como um exemplo concreto de aplicativo. Você aprenderá: 

* Implantar usando Git
* Definir a versão do PHP
* Usar um arquivo de inicialização que não está no diretório raiz do aplicativo
* Acessar variáveis de ambiente específicas
* Atualizar o aplicativo no Azure

Você pode aplicar o que aprendeu aqui para outros aplicativos Web PHP que implantar no Azure.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa

Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](app-service-web-php-get-started-cli-nodejs.md) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- [CLI do Azure 2.0 (Visualização)](app-service-web-php-get-started.md) - nossa próxima geração de CLI para o modelo de implantação de gerenciamento de recursos

## <a name="prerequisites"></a>Pré-requisitos
* [PHP 7.0](http://php.net/downloads.php)
* [Compositor](https://getcomposer.org/download/)
* [Visualização da CLI do Azure 2.0](/cli/azure/install-az-cli2)
* [Git](http://www.git-scm.com/downloads)
* Uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Veja um aplicativo Web em ação. [Experimente o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751) imediatamente e crie um aplicativo inicializador de curta duração, sem necessidade de cartão de crédito e sem compromisso.
> 
> 

## <a name="create-a-php-laravel-app-on-your-dev-machine"></a>Criar um aplicativo PHP (Laravel) em seu computador de desenvolvimento
1. Abra um novo prompt de comando do Windows, janela do PowerShell, shell do Linux ou terminal do OS X. Execute os comandos a seguir para verificar se as ferramentas necessárias estão instaladas corretamente no computador. 
   
        php --version
        composer --version
        az --version
        git --version
   
    Se ainda não tiver instalado as ferramentas, confira [Pré-requisitos](#Prerequisites) para obter links de download.

2. Instale o Laravel da seguinte forma:
   
        composer global require "laravel/installer"

3. `CD` em um diretório de trabalho e crie um novo aplicativo Laravel desta forma:
   
        cd <working_directory>
        laravel new <app_name>
4. `CD` no diretório `<app_name>` recém-criado e teste o aplicativo da seguinte forma:
   
        cd <app_name>
        php artisan serve
   
    Agora você poderá navegar até http://localhost:8000 em um navegador e ver a tela inicial do Laravel.
   
    ![Teste o aplicativo PHP (Laravel) localmente antes de implantá-lo no Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)

1. Inicializar um repositório Git e confirmar todo o código:

        git init
        git add .
        git commit -m "Hurray! My first commit for my Azure web app!"

Até o momento, há apenas o fluxo de trabalho regular do Laravel e do Git e você não está aqui para <a href="https://laravel.com/docs/5.3" rel="nofollow">aprender sobre o Laravel</a>. Vamos continuar.

## <a name="create-an-azure-web-app-and-set-up-git-deployment"></a>Criar um aplicativo Web do Azure e configurar a implantação do Git
> [!NOTE]
> "Espere! E se eu quiser implantar com o FTP?" Há um [tutorial sobre o FTP](web-sites-php-mysql-deploy-use-ftp.md) para suas necessidades. 
> 
> 

Com a CLI do Azure, você pode criar um aplicativo Web no Serviço de Aplicativo do Azure e configurá-lo para implantação no Git com uma única linha de comando. Vamos fazer isso.

1. Faça logon no Azure da seguinte forma:
   
        az login
   
    Siga a mensagem de ajuda para continuar o processo de logon.
   
3. Defina o usuário de implantação para o Serviço de Aplicativo. Mais tarde, você implantará o código usando essas credenciais.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Para este tutorial do PHP, você não precisa realmente saber o que é.

        az group create --location "<location>" --name my-php-app-group

    Para ver quais possíveis valores você pode usar para `<location>`, use o comando `az appservice list-locations` da CLI.

3. Criar um novo [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO". Para este tutorial do PHP, saiba que você não será cobrado para aplicativos Web neste plano.

        az appservice plan create --name my-php-appservice-plan --resource-group my-php-app-group --sku FREE

4. Crie um novo aplicativo Web com um nome exclusivo no `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-php-app-group --plan my-php-appservice-plan

5. Configure a implantação Git local para o novo aplicativo Web com o seguinte comando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-php-app-group

    Você receberá uma saída JSON como esta, o que significa que o repositório Git remoto está configurado:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Adicione a URL no JSON como um Git remoto para seu repositório local (chamado de `azure` para manter a simplicidade).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
<a name="configure"/>

## <a name="configure-the-azure-web-app"></a>Configurar o aplicativo Web do Azure
Para que o aplicativo Laravel funcione no Azure, você precisa prestar atenção a várias coisas. Você fará este exercício semelhante para a estrutura PHP de sua escolha.

* Configure o PHP 5.6.4 ou superior. Confira os [Últimos requisitos do servidor Laravel 5.3](https://laravel.com/docs/5.3#server-requirements) para obter toda a lista de requisitos do servidor. O restante da lista consiste em extensões que já estão habilitadas por instalações de PHP do Azure. 
* Defina as variáveis de ambiente de que o aplicativo precisa. O Laravel usa o arquivo `.env` para facilitar a configuração de variáveis de ambiente. No entanto, já que não deve ser confirmado no controle de origem (confira [Configuração do ambiente Laravel](https://laravel.com/docs/5.3/configuration#environment-configuration), você definirá as configurações do aplicativo Web do Azure em vez disso.
* Verifique se o ponto de entrada do aplicativo Laravel, `public/index.php`, é carregado primeiro. Confira [Visão geral do ciclo de vida Laravel](https://laravel.com/docs/5.3/lifecycle#lifecycle-overview). Em outras palavras, você precisa definir a URL raiz do aplicativo Web para apontar para o diretório `public` .
* Habilite a extensão do Composer no Azure, já que você tem um composer.json. Assim, você poderá deixar que o Composer se preocupe sobre como obter os pacotes necessários quando você implantar com o `git push`. É uma questão de conveniência. 
  Se você não habilitar a automação do Composer, bastará remover `/vendor` do arquivo `.gitignore` para que o Git inclua (deixe de ignorar) tudo no diretório `vendor` ao confirmar e implantar o código.

Vamos configurar essas tarefas sequencialmente.

1. Defina a versão do PHP que o aplicativo Laravel requer.
   
        az appservice web config update --php-version 7.0 --name <app_name> --resource-group my-php-app-group
   
    Você terminou de definir a versão do PHP. 

2. Gere uma nova `APP_KEY` para o aplicativo Web do Azure e defina-a como uma configuração de aplicativo para o aplicativo Web do Azure.
   
        php artisan key:generate --show
        az appservice web config appsettings update --settings APP_KEY="<output_of_php_artisan_key:generate_--show>" --name <app_name> --resource-group my-php-app-group

3. Além disso, ative a depuração do Laravel para antecipar-se a qualquer página `Whoops, looks like something went wrong.` complicada.
   
        az appservice web config appsettings update --settings APP_DEBUG=true --name <app_name> --resource-group my-php-app-group
   
    Você terminou de definir as variáveis de ambiente.
   
   > [!NOTE]
   > Aguarde: vamos desacelerar um pouco e explicar o que o Laravel faz e o que o Azure faz aqui. O Laravel usa o arquivo `.env` no diretório raiz para fornecer variáveis de ambiente ao aplicativo, em que você encontrará a linha `APP_DEBUG=true` (e também `APP_KEY=...`). Essa variável é acessada em `config/app.php` pelo código `'debug' => env('APP_DEBUG', false),`. [env()](https://laravel.com/docs/5.3/helpers#method-env) é um método auxiliar Laravel que usa o PHP [getenv()](http://php.net/manual/en/function.getenv.php) nos bastidores.
   > 
   > No entanto, `.env` é ignorado pelo Git porque é chamado pelo arquivo `.gitignore` no diretório raiz. Em outras palavras, `.env` no repositório Git local não é enviado ao Azure com o restante dos arquivos. Obviamente, você pode remover essa linha de `.gitignore`, mas já estabelecemos que não é recomendado confirmar esse arquivo no controle de origem. No entanto, você ainda precisa de uma maneira para especificar essas variáveis de ambiente no Azure. 
   > 
   > A boa notícia é que as configurações de aplicativo no Serviço de Aplicativo do Azure dão suporte a [getenv()](http://php.net/manual/en/function.getenv.php) no PHP. Portanto, embora possa usar o FTP ou outros meios para carregar manualmente um arquivo `.env` no Azure, você pode especificar apenas as variáveis que deseja como configurações de aplicativo do Azure, sem um `.env` no Azure, como acabou de fazer. Além disso, se uma variável estiver em um arquivo `.env` e em configurações de aplicativo do Azure, a configuração de aplicativo do Azure prevalecerá.     
   > 
   > 
4. As duas últimas tarefas (definir o diretório virtual e habilitar o Composer) exigem o [portal do Azure](https://portal.azure.com). Portanto, faça logon no [portal](https://portal.azure.com) com sua conta do Azure.
5. No menu à esquerda, clique em **Serviços de Aplicativos** > **&lt;app_name>** > **Extensões**.
   
    ![Habilite o Composer para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
   
6. Clique em **Adicionar** para adicionar uma extensão.
7. Selecione **Composer** in the **folha** [Escolher extensão](../azure-resource-manager/resource-group-portal.md#manage-resources) (*Escolher extensão*: uma página do portal que é aberta horizontalmente).
8. Clique em **OK** na [folha](../azure-resource-manager/resource-group-portal.md#manage-resources) **Aceitar os termos legais**. 
9. Clique em **OK** na [folha](../azure-resource-manager/resource-group-portal.md#manage-resources) **Adicionar extensão**.
   
    Quando o Azure terminar de adicionar a extensão, você deverá ver uma mensagem pop-up amigável no canto, bem como o  **Composer** listado na [folha](../azure-resource-manager/resource-group-portal.md#manage-resources) **Extensões**.
   
    ![Folha de extensões depois de habilitar o Composer para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-composer-end.png)
   
    Você terminou de habilitar o Composer.
10. De volta à [folha de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) do aplicativo Web, clique em **Configurações do Aplicativo**.
    
     ![Acesse a folha Configurações para definir o diretório virtual para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)
    
     Na [folha](../azure-resource-manager/resource-group-portal.md#manage-resources) **Configurações do Aplicativo**, observe a versão do PHP definida anteriormente:
    
     ![Versão do PHP na folha Configurações para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a-cli2.png)
    
     e as configurações de aplicativo que você adicionou:
    
     ![Configurações de aplicativo na folha Configurações para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)
11. Role até a parte inferior da [folha](../azure-resource-manager/resource-group-portal.md#manage-resources) e altere o diretório virtual raiz para apontar para **site\wwwroot\public** em vez de **site\wwwroot**.
    
     ![Defina o diretório virtual para o aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)
12. Clique em **Salvar** na parte superior da [folha](../azure-resource-manager/resource-group-portal.md#manage-resources).
    
     Você terminou de definir o diretório virtual. 

## <a name="deploy-your-web-app-with-git-and-setting-environment-variables"></a>Implantar o aplicativo Web com o Git (e definir variáveis de ambiente)
Você está pronto para implantar o código agora. Você fará isso no prompt de comando ou terminal.

1. Envie seu código por push para o aplicativo Web do Azure, como você faria para qualquer repositório Git:
   
        git push azure master 
   
    Quando solicitado, use as credenciais que você criou anteriormente.

2. Vamos ver isso em execução no navegador executando este comando:
   
        az appservice web browse --name <app_name> --resource-group my-php-app-group
   
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

### <a name="azure-cli-shows-site-is-not-an-azure-command"></a>A CLI do Azure mostra "'site' não é um comando do azure"
Ao executar `azure site *` no terminal de linha de comando, você verá o erro `error:   'site' is not an azure command. See 'azure help'.` 

Isso geralmente ocorre devido à alternância para o modo "ARM" (Azure Resource Manager). Para resolver o problema, alterne novamente para o modo "ASM" (Gerenciamento de Serviços do Azure) executando `azure config mode asm`.

<a name="http403"></a>

### <a name="web-app-shows-http-403-error"></a>O aplicativo Web mostra o erro HTTP 403
Você implantou o aplicativo Web no Azure com êxito, mas, ao navegar para o aplicativo Web, obtém um `HTTP 403` ou `You do not have permission to view this directory or page.`

Isso provavelmente ocorre porque o aplicativo Web não consegue encontrar o ponto de entrada para o aplicativo Laravel. Verifique se você alterou o diretório raiz virtual para apontar para `site\wwwroot\public`, em que o `index.php` do Laravel está (confira [Configurar o aplicativo Web do Azure](#configure)).

<a name="whoops"></a>

### <a name="web-app-shows-whoops-looks-like-something-went-wrong"></a>O aplicativo Web mostra "Opa, parece que algo deu errado".
Você implantou o aplicativo Web no Azure com êxito, mas, ao navegar para o aplicativo Web, recebe a mensagem indecifrável `Whoops, looks like something went wrong.`

Para obter um erro mais descritivo, habilite a depuração do Laravel definindo a variável de ambiente `APP_DEBUG` como `true` (confira [Configurar o aplicativo Web do Azure](#configure)).

<a name="encryptor"></a>

### <a name="web-app-shows-no-supported-encryptor-found"></a>O aplicativo Web mostra "Nenhum criptografador com suporte encontrado".
Você implantou o aplicativo Web no Azure com êxito, mas, ao navegar para o aplicativo Web, recebe a mensagem de erro abaixo:

![APP_KEY ausente no aplicativo PHP (Laravel) no Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)

Esse é um erro desagradável, mas, pelo menos, não é indecifrável, pois você ativou a depuração do Laravel. Uma pesquisa superficial da cadeia de caracteres de erro nos fóruns do Laravel mostrará que isso ocorreu porque a APP_KEY não foi definida em `.env` ou, no seu caso, `.env` não está presente no Azure. Você pode corrigir isso adicionando a configuração `APP_KEY` como uma configuração de aplicativo do Azure (confira [Configurar o aplicativo Web do Azure](#configure)).

## <a name="next-steps"></a>Próximas etapas
Saiba como adicionar dados ao aplicativo [criando um banco de dados MySQL no Azure](../store-php-create-mysql-database.md). Além disso, confira mais links úteis para o PHP no Azure abaixo:

* [Central de Desenvolvedores do PHP](/develop/php/)
* [Criar um aplicativo Web do Azure Marketplace](app-service-web-create-web-app-from-marketplace.md)
* [Configurar o PHP em aplicativos Web do Serviço de Aplicativo do Azure](web-sites-php-configure.md)
* [Converter WordPress em Multissite no Serviço de Aplicativo do Azure](web-sites-php-convert-wordpress-multisite.md)
* [WordPress de classe empresarial no Serviço de Aplicativo do Azure](web-sites-php-enterprise-wordpress.md)




<!--HONumber=Dec16_HO3-->


