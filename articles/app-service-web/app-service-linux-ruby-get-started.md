---
title: "Criar um aplicativo Ruby no Aplicativo Web do Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Aprenda a criar aplicativos Ruby com o Aplicativo Web de Serviço de Aplicativo do Azure no Linux."
keywords: "serviço de aplicativo do azure, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63bf790a50b894ea1732ae3142e6c1ff9ccbd4c
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-ruby-app-with-azure-web-app-on-linux---preview"></a>Criar um aplicativo Ruby com o Aplicativo Web do Azure no Linux – versão prévia

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como criar um aplicativo Ruby on Rails básico localmente e implantá-lo no Aplicativo Web do Azure no Linux.

## <a name="prerequisites"></a>Pré-requisitos

* [Ruby 2.3.3 ou superior](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller) instalado no computador de desenvolvimento.
* [Git](https://git-scm.com/downloads) instalado no computador de desenvolvimento
* Uma [assinatura ativa do Azure](https://azure.microsoft.com/pricing/free-trial/)
* Este tutorial foi escrito no contexto de um ambiente Ubuntu. Todos os comandos de sistema são específico ao bash.


## <a name="create-a-new-local-rails-application"></a>Criar um novo aplicativo Rails local

1. Crie um diretório para o novo aplicativo e passe para esse diretório.

        mkdir ~/workspace
        cd ~/workspace

2. Inicialize o Ruby e verifique a versão usando o comando `ruby -v`.

    ![Inicialização do Ruby](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. Instalar o Rails usando o comando `gem install rails`.

    ![Instalar Rails](./media/app-service-linux-ruby-get-started/install-rails.png)

4. Crie um aplicativo Rails chamado **Olá, Mundo** usando o seguinte comando:

    Se estiver usando o Rails 5.1.0 ou posterior, inclua a opção `--skip-yarn` conforme mostrado no seguinte comando:

        rails new hello-world --skip-yarn

    Para versões do Rails anteriores à 5.1.0, use o seguinte comando:

        rails new hello-world 

    ![Novo Olá, Mundo](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![Novo Olá, Mundo](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

    Se você estiver usando o Rails 5.1 +, um package.json será criado se a opção `--skip-yarn` não for usada. Não queremos que ele seja incluído em nossa implantação. Como alternativa, você pode excluir o arquivo package.json ou adicioná-lo ao arquivo *.git-ignore* no diretório da seguinte maneira: 

        # Ignore package.json
        /package.json

5. Altere para o diretório *hello-world* e inicie o servidor.

        cd hello-world
        rails server

    ![Iniciar Olá, Mundo](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
    
6. Usando o navegador da Web, navegue até `http://localhost:3000` para testar o aplicativo localmente.    

    ![Olá, Mundo](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="prepare-the-app-for-azure"></a>Preparar o aplicativo para o Azure

Por padrão, a imagem do Ruby executa o servidor com o sinalizador `-e production`. Este ambiente requer algumas configurações para o Aplicativo Web do Azure no Linux. O contêiner se encarrega de parte dessa configuração (como a configuração de um `SECRET_KEY_BASE`). Uma rota padrão deve ser configurada. Caso contrário, você receberá um erro 404 quando navegar no site.

Para configurar uma rota padrão:

1. Abra *~/workspace/hello-world/config/routes.rb* para edição. Adicione a linha a seguir, como mostrado na captura de tela. 

        root 'application#hello'

    ![routes.rb](./media/app-service-linux-ruby-get-started/routes-rb.png)


2. Abra *~/workspace/hello-world/app/controllers/application_controller.rb* para edição. Adicione as linhas a seguir, como mostrado na captura de tela.

        def hello
            render html: "Hello, world from Azure Web App on Linux!"
        end

    ![routes.rb](./media/app-service-linux-ruby-get-started/application-controller-rb.png)


3. Seu aplicativo está configurado. Usando o navegador da Web, navegue até `http://localhost:3000` para confirmar a página inicial de raiz.

    ![Olá, Mundo configurado](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## <a name="create-a-ruby-website-on-azure"></a>Criar um site Ruby no Azure

1. Navegue até o [portal do Azure](http://portal.azure.com) e entre usando sua assinatura. Adicione um **Aplicativo Web no Linux**, como mostrado nesta captura de tela:

    ![Criar Aplicativo Web no Linux](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. A folha **Criar** é aberta, conforme mostrado na seguinte captura de tela:

    ![A folha Criar](./media/app-service-linux-ruby-get-started/create-blade.png)


    1. Dê um nome ao aplicativo Web.
    2. Escolha um grupo de recursos existente ou crie um novo. (Veja as regiões disponíveis na [seção de limitações](app-service-linux-intro.md).)
    3. Escolha um plano do Serviço de Aplicativo do Azure existente ou crie um. (Veja as observações do plano do Serviço de Aplicativo na [seção de limitações](app-service-linux-intro.md).)
    4. Escolha a pilha de tempo de execução interna **Ruby 2.3** para a configuração de seu contêiner.
    5. Clique em **Fixar no painel** para o aplicativo Web.
    6. Clique em **Criar**.

3. Após o aplicativo Web ser criado, a folha **Visão Geral** é exibida. Copie a **URL** do novo aplicativo Web e abra-a em seu navegador. A seguinte página inicial é exibida.

    ![página inicial](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>Implantar seu aplicativo

Neste tutorial, usamos o Git para implantar o aplicativo Ruby local no Azure.

1. O novo site do Azure já tem uma implantação do Git configurada. Você encontrará a URL de implantação do Git navegando até a URL a seguir depois de inserir o nome do aplicativo Web:

        https://{your web app name}.scm.azurewebsites.net/api/scm/info

    A URL do Git tem o seguinte formato, com base no nome do seu aplicativo Web:

        https://{your web app name}.scm.azurewebsites.net/{your web app name}.git

2. Execute os seguintes comandos para implantar o aplicativo local em seu site do Azure.

        cd ~/workspace/hello-world
        git init
        git remote add azure <Git deployment URL from above>
        git add -A
        git commit -m "Initial deployment commit"
        git push master

    Confirme que as operações de implantação remota relatam êxito.

    ![Implantando o aplicativo Web](./media/app-service-linux-ruby-get-started/deployment-success.png)

    Se você vir um erro informando que o computador remoto foi desligado, é provável que a implantação ainda esteja andamento. Nesse caso, navegue até a seguinte URL no seu navegador:

        https://{your web app name}.scm.azurewebsites.net/api/deployments

3. Depois que a implantação for concluída, reinicie o aplicativo Web para a implantação entrar em vigor. No [portal do Azure](http://portal.azure.com), navegue até a folha **Visão Geral** do seu aplicativo Web.

    Clique em **Reiniciar** na barra de ferramentas.

    ![Reiniciar o aplicativo Web](./media/app-service-linux-ruby-get-started/restart-web-app.png)

4. Navegue até o site e confirme que as atualizações estão em vigor. 

    Enquanto o aplicativo estiver reiniciando, tentar navegar pelo site leva a um erro de código de status HTTP 503 (servidor indisponível). Pode levar alguns minutos para reiniciar completamente.

        http://{your web app name}.azurewebsites.net

    ![aplicativo web atualizado](./media/app-service-linux-ruby-get-started/hello-world-updated.png)
    

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações relacionadas ao Aplicativo Web do Serviço de Aplicativo do Azure no Linux, consulte os links a seguir. Você também pode postar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Como criar Aplicativos Web no Serviço de Aplicativo no Linux](app-service-linux-how-to-create-web-app.md)
* [Como usar uma imagem personalizada do Docker para o Serviço de Aplicativo no Linux](app-service-linux-using-custom-docker-image.md)
* [Usando a configuração PM2 para Node.js em Aplicativos Web no Linux](app-service-linux-using-nodejs-pm2.md)
* [Usando o .NET Core em Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-using-dotnetcore.md)
* [Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-faq.md)


