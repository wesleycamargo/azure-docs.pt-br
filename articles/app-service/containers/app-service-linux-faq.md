---
title: "Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure."
keywords: "serviço de aplicativo do azure, aplicativo web, perguntas frequentes, linux, oss"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 146c598f5cb62612327ce679a6bfaadc8312b149
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="azure-app-service-web-app-for-containers-faq"></a>Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure

Com a liberação do Aplicativo Web para Contêineres, estamos trabalhando para adicionar recursos e fazer melhorias em nossa plataforma. Estas são algumas perguntas frequentes que nossos clientes têm feito nos últimos meses.
Caso tenha uma pergunta, comente o artigo e responderemos assim que possível.

## <a name="built-in-images"></a>Imagens internas

**P:** desejo divida os contêineres de Docker internos que a plataforma oferece. Onde posso encontrar esses arquivos?

**R:** É possível encontrar todos os arquivos do Docker no [GitHub](https://github.com/azure-app-service). É possível encontrar todos os contêineres do Docker no [Hub do Docker](https://hub.docker.com/u/appsvc/).

**P:** Quais são os valores esperados para a seção Arquivo de Inicialização quando configuro a pilha de tempo de execução?

**R:** Para o Node.js, é possível especificar o arquivo de configuração PM2 ou o arquivo de script. Para o .NET Core, especifique o nome da DLL compilada. Para o Ruby, é possível especificar o script Ruby com o qual você deseja inicializar o aplicativo.

## <a name="management"></a>Gerenciamento

**P:** O que acontece quando eu pressiono o botão de reinicialização no Portal do Azure?

**R:** Isso equivale à reinicialização do Docker.

**P:** Posso usar o SSH (Secure Shell) para me conectar à VM (máquina virtual) do contêiner de aplicativo?

**R:** Sim, você pode fazer isso através do site do SCM.

**P:** Quero criar um plano do Serviço de Aplicativo do Linux por meio do SDK ou de um modelo do ARM, como posso fazer isso?

**R:** Você precisa definir o campo `reserved` do serviço de aplicativo como `true`.

## <a name="continuous-integrationdeployment"></a>Integração/implantação contínua

**P:** Meu aplicativo Web ainda usa uma imagem de contêiner antiga do Docker depois que atualizei a imagem no Hub do Docker. Há suporte para implantação/integração contínua de contêineres personalizados?

**R:** Para configurar a integração/implantação contínua para o Registro de Contêiner do Azure ou imagens do DockerHub, verifique o artigo [Implantação contínua com os Aplicativos Web para Contêineres do Azure](./app-service-linux-ci-cd.md). Para registros privados, é possível atualizar o contêiner parando e, em seguida, iniciando o Aplicativo Web. Se preferir, é possível alterar ou adicionar uma configuração de aplicativo fictício para forçar uma atualização do contêiner.

**P:** Há suporte para ambientes de preparo?

**R:** Sim.

**P:** Posso usar **implantação da web** implantar meu aplicativo web?

**R:** Sim, você precisa definir um aplicativo de configuração chamado `WEBSITE_WEBDEPLOY_USE_SCM` para `false`.

## <a name="language-support"></a>Suporte ao idioma

**P:** Há suporte para aplicativos .NET Core não compilados?

**R:** Sim.

**P:** Há suporte para o Criador como um gerenciador de dependências para aplicativos PHP?

**R:** Sim. Durante a implantação de Git, o Kudu deve detectar que você está implantando um aplicativo PHP (graças à presença de um arquivo de composer.lock) e disparará uma instalação de criador para você.

## <a name="custom-containers"></a>Contêineres personalizados

**P:** Estou usando meu próprio contêiner personalizado. Meu aplicativo reside no diretório `/home/`, mas não consigo encontrar meus arquivos ao navegar pelo conteúdo usando o [site do SCM](https://github.com/projectkudu/kudu) ou um cliente FTP. Onde estão meus arquivos?

**R:** Montamos um compartilhamento SMB no diretório `/home/`. Isso substituirá todo o conteúdo existente nele.

**P:** Estou usando meu próprio contêiner personalizado. Quero que a plataforma monte um compartilhamento SMB para o diretório `/home/`.

**R:** Defina a configuração de aplicativo `WEBSITES_ENABLE_APP_SERVICE_STORAGE` para `true` ou remova totalmente a configuração do aplicativo. Lembre-se de que isso causará reinicializações de contêiner quando o armazenamento de plataforma passar por uma alteração. 

Observe que se WEBSITES_ENABLE_APP_SERVICE_STORAGE for 'false', o diretório /home/ não poderá mais ser compartilhado entre instâncias de escala e os arquivos gravados ali não serão persistidos entre as reinicializações.

**P:** Meu contêiner personalizado demora para iniciar e a plataforma o reinicia antes que ele termine a inicialização.

**R:** Você pode configurar o tempo que a plataforma aguardará antes de reiniciar o contêiner. Isso pode ser feito definindo a configuração de aplicativo `WEBSITES_CONTAINER_START_TIME_LIMIT` para o valor desejado em segundos. O padrão é 230 segundos e o máximo é de 600 segundos.

**P:** Qual é o formato da URL do servidor do Registro privado?

**R:** Você precisa fornecer a URL completa do registro, incluindo `http://` ou `https://`.

**P:** Qual é o formato do nome da imagem na opção de Registro privado?

**R:** Você precisa adicionar o nome de imagem completo, incluindo a URL do Registro privado (por exemplo, myacr.azurecr.io/dotnet:latest). Nomes de imagem que usam a porta personalizada [não podem ser inseridos por meio do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Use a ferramenta [`az` de linha de comando](https://docs.microsoft.com/en-us/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) para definir `docker-custom-image-name`.

**P:** que quero expor mais de uma porta em minha imagem de contêiner personalizados. Isso é possível?

**R:** No momento, não há suporte para esse recurso.

**P:** posso colocar meu próprio armazenamento?

**R:** No momento, não há suporte para esse recurso.

**P:** não é possível navegar os processos do meu contêiner personalizado em execução ou de sistema de arquivos do site do SCM. Por que isso acontece?

**R:** O site do SCM é executado em um contêiner separado. Não é possível verificar o sistema de arquivos ou os processos em execução do contêiner de aplicativo.

**P:** Meu contêiner personalizado escuta uma porta diferente da porta 80. Como configurar meu aplicativo para rotear as solicitações para essa porta?

**R:** Nós temos detecção automática da porta; além disso, é possível especificar uma configuração de aplicativo chamada **WEBSITES_PORT** e fornecer a ela o valor do número da porta esperada. A plataforma estava usando a configuração de aplicativo `PORT` anteriormente, estamos planejando substituir o uso dessa configuração de aplicativo e passar a usar exclusivamente `WEBSITES_PORT`.

**P:** É necessário implementar o HTTPS no meu contêiner personalizado?

**R:** Não, a plataforma manipula a terminação HTTPS nos front-ends compartilhados.

## <a name="pricing-and-sla"></a>Preço e SLA

**P:** Qual é o preço agora que o serviço está disponível?

**R:** Você pagará pela quantidade de horas em que o aplicativo é executado, com o preço normal do Serviço de Aplicativo do Azure.

## <a name="other"></a>Outros

**P:** quais são os caracteres com suporte em nomes de configurações do aplicativo?

**R:** Só é possível usar A-Z, a-z, 0-9 e sublinhado para as configurações do aplicativo.

**P:** onde solicitar novos recursos?

**R:** É possível enviar sua ideia para o [fórum de comentários dos Aplicativos Web](https://aka.ms/webapps-uservoice). Adicione “[Linux]” ao título de sua ideia.

## <a name="next-steps"></a>Próximas etapas

* [O que é o Aplicativo Web do Azure para Contêineres?](app-service-linux-intro.md)
* [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](../../app-service-web/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implantação contínua com o Aplicativo Web do Azure para Contêineres](./app-service-linux-ci-cd.md)

