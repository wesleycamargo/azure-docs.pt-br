---
title: Perguntas frequentes sobre o Serviço de Aplicativo do Azure no Linux | Microsoft Docs
description: Perguntas frequentes sobre o Serviço de Aplicativo do Azure no Linux.
keywords: serviço de aplicativo do azure, aplicativo web, perguntas frequentes, linux, oss
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.author: msangapu
ms.openlocfilehash: 5b3b3d3946b56ff53ad74c2ab93a646baa787d05
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222970"
---
# <a name="azure-app-service-on-linux-faq"></a>Perguntas frequentes sobre o Serviço de Aplicativo do Azure no Linux

Com o lançamento do Serviço de Aplicativo no Linux, estamos trabalhando para adicionar recursos e fazer melhorias em nossa plataforma. Este artigo fornece as respostas para as perguntas que nossos clientes têm feito recentemente.

Se você tiver qualquer dúvida, comente este artigo.

## <a name="built-in-images"></a>Imagens internas

**Quero dividir os contêineres de Docker internos que a plataforma oferece. Onde posso encontrar esses arquivos?**

É possível encontrar todos os arquivos do Docker no [GitHub](https://github.com/azure-app-service). É possível encontrar todos os contêineres do Docker no [Hub do Docker](https://hub.docker.com/u/appsvc/).

**Quais são os valores esperados para a seção Arquivo de Inicialização quando configuro a pilha de tempo de execução?**

Para o Node.js, é possível especificar o arquivo de configuração PM2 ou o arquivo de script. Para o .NET Core, especifique o nome da DLL compilada como `dotnet <myapp>.dll`. Para o Ruby, é possível especificar o script Ruby com o qual você deseja inicializar o aplicativo.

## <a name="management"></a>Gerenciamento

**O que acontece quando eu pressiono o botão de reinicialização no portal do Azure?**

Esta ação é igual a um reinício do Docker.

**Posso usar o SSH (Secure Shell) para me conectar à VM (máquina virtual) do contêiner de aplicativo?**

Sim, você pode fazer isso por meio do site de gerenciamento do controle de origem (SCM) .

> [!NOTE]
> Você também pode se conectar ao contêiner de aplicativo diretamente do seu computador de desenvolvimento local usando SSH, SFTP ou Visual Studio Code (para aplicativos do Node.js de depuração ao vivo). Para obter mais informações, consulte [Depuração remota e SSH no Serviço de Aplicativo no Linux](https://aka.ms/linux-debug).
>

**Como criar um plano de Serviço de Aplicativo Linux por meio de um SDK ou um modelo do Azure Resource Manager?**

Você deve definir o campo **reservado** do serviço de aplicativo para *true*.

## <a name="continuous-integration-and-deployment"></a>Integração e implantação contínuas

**Meu aplicativo Web ainda usa uma imagem de contêiner antiga do Docker depois que atualizei a imagem no Hub do Docker. Há suporte para implantação/integração contínua de contêineres personalizados?**

Sim, para configurar integração/implantação contínua para o Registro de Contêiner do Azure ou DockerHub, seguindo a [Implantação contínua com o Aplicativo Web para Contêineres](./app-service-linux-ci-cd.md). Para registros privados, é possível atualizar o contêiner parando e, em seguida, iniciando o Aplicativo Web. Se preferir, é possível alterar ou adicionar uma configuração de aplicativo fictício para forçar uma atualização do contêiner.

**Há suporte para ambientes de preparo?**

Sim.

**Posso usar a *implantação da Web* para implantar meu aplicativo Web?**

Sim, você precisa definir uma configuração de aplicativo chamada `WEBSITE_WEBDEPLOY_USE_SCM` como *false*.

**A implantação do Git do meu aplicativo falha ao usar um aplicativo Web do Linux. Como fazer para resolver o problema??**

Se a implantação do Git falhar no aplicativo Web do Linux, escolha uma das opções a seguir para implantar o código do aplicativo:

- Usar o recurso Entrega Contínua (versão prévia): você pode armazenar o código-fonte do seu aplicativo em um repositório Git do Team Services ou em um repositório do GitHub para usar a Entrega Contínua do Azure. Para obter mais informações, consulte [Como configurar a Entrega Contínua para aplicativos Web do Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Usar a [API de implantação via arquivo ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): para usar essa API, [adicione o SSH ao seu aplicativo Web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) e vá para a pasta onde você deseja implantar seu código. Execute o código a seguir:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se você receber uma mensagem de erro informando que o comando `curl` não foi encontrado, instale a ondulação usando `apt-get install curl` antes de executar o comando `curl` anterior.

## <a name="language-support"></a>Suporte ao idioma

**Eu quero usar websockets no aplicativo Node.js, há definições ou configurações especiais a serem definidas?**

Sim, desabilite `perMessageDeflate` no código Node.js do servidor. Por exemplo, se estiver usando o socket.io, use o código a seguir:

```nodejs
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Há suporte para aplicativos .NET Core não compilados?**

Sim.

**Há suporte para o Criador como um gerenciador de dependências para aplicativos PHP?**

Sim, durante uma implantação do Git, o Kudu deve detectar que você está implantando um aplicativo PHP (graças à presença de um arquivo composer.lock) e o Kudu, então, disparará uma instalação de criador.

## <a name="custom-containers"></a>Contêineres personalizados

**Estou usando meu próprio contêiner personalizado. Quero que a plataforma monte um compartilhamento SMB para o diretório `/home/`.**

É possível fazer isso, definindo a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` configuração do aplicativo como *true*. Lembre-se de que isso fará com que o contêiner seja reiniciado quando o armazenamento da plataforma passar por uma alteração.

>[!NOTE]
>Se a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` configuração não for especificada ou definida como para *false*, o `/home/`diretório não será compartilhado entre as instâncias de escala e os arquivos gravados lá não serão mantidos nos reinícios.

**Meu contêiner personalizado demora para iniciar e a plataforma o reinicia antes que ele termine a inicialização.**

Você pode configurar o tempo que a plataforma aguardará antes de reiniciar o contêiner. Para fazer isso, defina a configuração de aplicativo `WEBSITES_CONTAINER_START_TIME_LIMIT` como o valor desejado. O valor padrão é 230 segundos e o valor máximo permitido é 1800 segundos.

**Qual é o formato da URL do servidor do Registro privado?**

Forneça a URL completa do registro, incluindo `http://` ou `https://`.

**Qual é o formato do nome da imagem na opção de Registro privado?**

Adicione o nome de imagem completa, incluindo a URL de registro particular (por exemplo, myacr.azurecr.io/dotnet:latest). Os nomes de imagem que usam a porta personalizada [não podem ser inseridos por meio do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para definir `docker-custom-image-name`, use a [ferramenta de linha de comando `az`](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**Posso expor mais de uma porta em minha imagem de contêiner personalizados?**

No momento, não damos suporte a mais de uma porta.

**Posso colocar meu próprio armazenamento?**

No momento, não damos suporte a trazer seu próprio armazenamento.

**Por que não é possível navegar os processos do meu contêiner personalizado em execução ou de sistema de arquivos do site do SCM?**

O site do SCM é executado em um contêiner separado. Não é possível verificar o sistema de arquivos ou os processos em execução do contêiner de aplicativo.

**Meu contêiner personalizado escuta uma porta diferente da porta 80. Como configurar meu aplicativo para rotear as solicitações para essa porta?**

Temos a detecção automática de porta. Também é possível especificar uma configuração de aplicativo chamada *WEBSITES_PORT* e fornecer a ela o valor do número da porta esperada. Anteriormente, a plataforma usava a configuração de aplicativo *PORTA*. Nós estamos planejando substituir essa configuração de aplicativo e usar *WEBSITES_PORT* exclusivamente.

**É necessário implementar o HTTPS no meu contêiner personalizado?**

Não, a plataforma manipula a terminação HTTPS nos front-ends compartilhados.

## <a name="pricing-and-sla"></a>Preço e SLA

**Qual é o preço agora que o serviço está disponível?**

Você será cobrado pela quantidade de horas em que o aplicativo é executado, com o preço normal do Serviço de Aplicativo do Azure.

## <a name="other-questions"></a>Outras perguntas

**Quais são os caracteres com suporte em nomes de configurações do aplicativo?**

Você pode usar apenas letras (A-Z, a-z), números (0-9) e o caractere de sublinhado (_) para as configurações do aplicativo.

**Onde posso solicitar os novos recursos?**

É possível enviar sua ideia para o [fórum de comentários dos Aplicativos Web](https://aka.ms/webapps-uservoice). Adicione “[Linux]” ao título de sua ideia.

## <a name="next-steps"></a>Próximas etapas

- [O que é o Serviço de Aplicativo do Azure no Linux?](app-service-linux-intro.md)
- [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Implantação Contínua com o Aplicativo Web para Contêineres](./app-service-linux-ci-cd.md)
