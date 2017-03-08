---
title: "Implantar seu primeiro aplicativo Web HTML no Azure em cinco minutos (CLI 2.0 Visualização) | Microsoft Docs"
description: "Saiba como é fácil executar aplicativos Web no Serviço de Aplicativo implantando um aplicativo de exemplo. Inicie o desenvolvimento real rapidamente e veja os resultados imediatamente."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7bc52251f2d0a6aca271bd3d013690bdd0d6b752
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-your-first-html-web-app-to-azure-in-five-minutes-cli-20-preview"></a>Implantar seu primeiro aplicativo Web HTML no Azure em cinco minutos (CLI 2.0 Visualização)
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Este tutorial o ajuda a implantar um aplicativo Web HTML+CSS simples para o [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).
Você pode usar o Serviço de Aplicativo para criar aplicativos Web, [back-ends de aplicativos móveis](/documentation/learning-paths/appservice-mobileapps/) e [aplicativos de API](../app-service-api/app-service-api-apps-why-best-platform.md).

Você irá: 

* Criar um aplicativo Web no Serviço de Aplicativo do Azure.
* Implantar o HTML e CSS a ele.
* Ver suas páginas em execução na produção.
* Atualize seu conteúdo da mesma maneira como faria com [confirmações do Git por push](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa

Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](app-service-web-get-started-html-cli-nodejs.md) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- [CLI do Azure 2.0](app-service-web-get-started-html.md) – nossa próxima geração de CLI para o modelo de implantação de gerenciamento de recursos

## <a name="prerequisites"></a>Pré-requisitos
* [Git](http://www.git-scm.com/downloads).
* [Visualização da CLI do Azure 2.0](/cli/azure/install-az-cli2).
* Uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Você pode [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie um aplicativo inicial e brinque com ele por até uma hora: não é necessário cartão de crédito ou compromissos.
> 
> 

## <a name="deploy-a-simple-html-site"></a>Implantar um site simples em HTML
1. Abra um novo prompt de comando do Windows, janela do PowerShell, shell do Linux ou terminal do OS X. Execute `git --version` e `azure --version` para verificar se o Git e a CLI do Azure estão instalados em seu computador.
   
    ![Testar a instalação das ferramentas da CLI para seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started-languages/1-test-tools-2.0.png)
   
    Se ainda não tiver instalado as ferramentas, confira [Pré-requisitos](#Prerequisites) para obter links de download.
2. Faça logon no Azure da seguinte forma:
   
        az login
   
    Siga a mensagem de ajuda para continuar o processo de logon.
   
    ![Fazer logon no Azure para criar seu primeiro aplicativo Web](./media/app-service-web-get-started-languages/3-azure-login-2.0.png)

3. Defina o usuário de implantação para o Serviço de Aplicativo. Mais tarde, você implantará o código usando essas credenciais.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Para este primeiro tutorial sobre o Serviço de Aplicativo, você realmente não precisa saber o que ele é.

        az group create --location "<location>" --name my-first-app-group

    Para ver quais possíveis valores você pode usar para `<location>`, use o comando `az appservice list-locations` da CLI.

3. Criar um novo [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) ”GRATUITO”. Para este primeiro tutorial do Serviço de Aplicativo, saiba que você não será cobrado pelos aplicativos Web deste plano.

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. Crie um novo aplicativo Web com um nome exclusivo no `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. Em seguida, você obterá o site HTML de exemplo que deseja implantar. Altere para um diretório de trabalho (`CD`) e clone o aplicativo de exemplo desta forma:
   
        cd <working_directory>
        git clone https://github.com/Azure-Samples/app-service-web-html-get-started.git

5. Altere para o repositório do aplicativo de exemplo. 
   
        cd app-service-web-html-get-started
5. Configure a implantação Git local para seu aplicativo Web do Serviço de Aplicativo com o seguinte comando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    Você receberá uma saída JSON como esta, o que significa que o repositório Git remoto está configurado:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Adicione a URL no JSON como um Git remoto para seu repositório local (chamado de `azure` para manter a simplicidade).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Implante o código de exemplo no aplicativo do Azure como você faria com qualquer código do Git. Quando solicitado, use a senha configurada anteriormente.
   
        git push azure master
   
    ![Enviar código por push para seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started/5-push-code.png)
   
    Se usar uma das estruturas de linguagem, você verá uma saída diferente. Isso ocorre porque o `git push` não só coloca o código no Azure, mas também dispara tarefas de implantação no mecanismo de implantação. Se houver um arquivo package.json (Node.js) ou requirements.txt (Python) na raiz do projeto (repositório) ou um arquivo packages.config no projeto ASP.NET, o script de implantação restaurará os pacotes necessários para você. Você também pode [habilitar a extensão do Composer](web-sites-php-mysql-deploy-use-git.md#composer) para processar automaticamente arquivos composer.json no aplicativo PHP.

Parabéns, você implantou seu aplicativo no Serviço de Aplicativo do Azure.

## <a name="see-your-app-running-live"></a>Ver o aplicativo em execução
Para ver seu aplicativo em execução no Azure, execute este comando em qualquer pasta no repositório:

    azure site browse

## <a name="make-updates-to-your-app"></a>Fazer atualizações no aplicativo
Agora você pode usar o Git para enviar da raiz do projeto (repositório) a qualquer momento e fazer uma atualização no site ativo. Você faz isso da mesma forma que foi feito ao implantar o aplicativo no Azure pela primeira vez. Por exemplo, sempre que você desejar enviar novas alterações que testou localmente, bastará executar os seguintes comandos da raiz do projeto (repositório):

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Próximas etapas
Encontre as etapas preferidas de desenvolvimento e implantação para sua estrutura de linguagem:

* [.NET](web-sites-dotnet-get-started.md)
* [PHP](app-service-web-php-get-started.md)
* [Node.js](app-service-web-nodejs-get-started.md)
* [Python](web-sites-python-ptvs-django-mysql.md)
* [Java](web-sites-java-get-started.md)

Ou faça mais com seu primeiro aplicativo Web. Por exemplo:

* Experimente [outras maneiras de implantar seu código no Azure](web-sites-deploy.md). Por exemplo, para implantar de um dos repositórios GitHub, basta selecionar **GitHub** em vez de **Repositório Git Local** nas **Opções de implantação**.
* Leve o aplicativo do Azure para o próximo patamar. Autenticar os usuários. Dimensione-o com base na demanda. Configure alguns alertas de desempenho. Tudo isso com apenas alguns cliques. Confira [Adicionar funcionalidade a seu primeiro aplicativo Web](app-service-web-get-started-2.md).


