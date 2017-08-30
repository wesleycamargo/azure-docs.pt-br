---
title: Criar um aplicativo Ruby com aplicativos Web no Linux | Microsoft Docs
description: Saiba como criar aplicativos Ruby com o aplicativo Web do Azure no Linux.
keywords: "serviço de aplicativo do azure, linux, oss, ruby"
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
ms.date: 08/15/2017
ms.author: wesmc;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 17f3f1a2122c508501134a0c43ab6abce412fb44
ms.contentlocale: pt-br
ms.lasthandoff: 08/24/2017

---
# <a name="create-a-ruby-app-with-web-apps-on-linux"></a>Criar um aplicativo Ruby com aplicativos Web no Linux 

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Os [aplicativos Web do Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) fornecem um serviço de hospedagem na Web altamente escalonável,com aplicação automática de patches. Este guia de início rápido mostra como criar um aplicativo Ruby on Rails básico e implantá-lo no Azure como um aplicativo Web no Linux.

![Olá, Mundo](./media/app-service-linux-ruby-get-started/hello-world-updated.png)

## <a name="prerequisites"></a>Pré-requisitos

* [Ruby 2.4.1 ou superior](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller).
* [Git](https://git-scm.com/downloads).
* Uma [assinatura ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de terminal, execute o comando a seguir para clonar o repositório do aplicativo de exemplo para o computador local:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente

Execute o servidor de trilhos para que o aplicativo funcione. Altere para o diretório *hello-world* e o comando `rails server` iniciará o servidor.

```bash
cd hello-world\bin
rails server
```
    
Usando o navegador da Web, navegue até `http://localhost:3000` para testar o aplicativo localmente.    

![Olá, Mundo](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="modify-app-to-display-welcome-message"></a>Modificar o aplicativo para exibir a mensagem de boas-vindas

Modifique o aplicativo para que ele exiba uma mensagem de boas-vindas. Altere o controlador do aplicativo para que ele retorne a mensagem como HTML para o navegador. 

Abra *~/workspace/hello-world/app/controllers/application_controller.rb* para edição. Modifique a classe `ApplicationController` para que ela se assemelhe ao seguinte exemplo de código:

  ```ruby
  class ApplicationController > ActionController :: base
    protect_from_forgery with: :exception 
    def hello
      render html: "Hello, world from Azure Web App on Linux!"
    end
  end
  ```

Seu aplicativo está configurado. Usando o navegador da Web, navegue até `http://localhost:3000` para confirmar a página inicial de raiz.

![Olá, Mundo configurado](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ruby-web-app-on-azure"></a>Criar um aplicativo Web Ruby no Azure

Use o comando [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) para criar um plano de serviço de aplicativo para seu aplicativo Web. 
 
```azurecli-interactive
  az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --is-linux
```

Em seguida, execute o comando [az webapp create](https://docs.microsoft.com/cli/azure/webapp) para criar o aplicativo Web que usa o plano de serviço recém-criado. Observe que o tempo de execução é definido como `ruby|2.3`. Não se esqueça de substituir `<app name>` por um nome exclusivo do aplicativo.

```azurecli-interactive
  az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "ruby|2.3" --deployment-local-git
```

Após um aplicativo Web ser criado, uma página de **Visão geral** ficará disponível para exibição. Navegue até ela. A seguinte página inicial é exibida:

![Página inicial](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>Implantar seu aplicativo

Use o Git para implantar o aplicativo Ruby no Azure. O aplicativo Web já tem uma implantação do Git configurada. Você pode recuperar a URL de implantação emitindo um comando [az webapp deployment](https://docs.microsoft.com/cli/azure/webapp/deployment).  

```bash
az webapp deployment source show --name <app name> --resource-group myResourceGroup
```

Observe que a URL do Git tem o seguinte formato, com base no nome do seu aplicativo Web:

```bash
https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
```

[!INCLUDE [Clean-up section](../../includes/configure-deployment-user-no-h.md)]

Execute os seguintes comandos para implantar o aplicativo local em seu site do Azure:

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Confirme que as operações de implantação remota relatam êxito. Os comandos produzem uma saída semelhante ao texto a seguir:

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

Depois que a implantação for concluída, reinicie o aplicativo Web para a implantação entrar em vigor usando o comando [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#restart), conforme mostrado aqui:

```azurecli-interactive 
az webapp restart --name <app name> --resource-group myResourceGroup
```

Navegue até o site e verifique os resultados.

```bash
http://<your web app name>.azurewebsites.net
```
![aplicativo web atualizado](./media/app-service-linux-ruby-get-started/hello-world-updated.png)

> [!NOTE]
> Enquanto o aplicativo estiver reiniciando, tentar navegar pelo site leva a um código de status HTTP `Error 503 Server unavailable`. Pode levar alguns minutos para reiniciar completamente.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Próximas etapas

[Perguntas frequentes sobre o Aplicativo Web do Serviço de Aplicativo do Azure no Linux](https://docs.microsoft.com/azure/app-service-web/app-service-linux-faq.md)
