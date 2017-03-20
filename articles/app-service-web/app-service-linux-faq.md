---
title: "Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux."
keywords: "serviço de aplicativo do azure, aplicativo web, perguntas frequentes, linux, oss"
services: app-service
documentationCenter: 
authors: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 148bc76b7f3e09745cbecfa41710a5e949704948
ms.lasthandoff: 03/08/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux

Com a liberação do Serviço de Aplicativo do Azure no Linux (atualmente em visualização), estamos trabalhando para adicionar recursos e fazer melhorias em nossa plataforma. Estas são algumas perguntas frequentes que nossos clientes têm feito nos últimos meses.
Caso tenha uma pergunta, comente o artigo e responderemos assim que possível.

## <a name="built-in-images"></a>Imagens internas

**P:** desejo divida os contêineres de Docker internos que a plataforma oferece. Onde posso encontrar esses arquivos?

**R:** É possível encontrar todos os arquivos do Docker no [GitHub](https://github.com/azure-app-service). É possível encontrar todos os contêineres do Docker no [Hub do Docker](https://hub.docker.com/u/appsvc/).

**P:** Quais são os valores esperados para a seção Arquivo de Inicialização quando configuro a pilha de tempo de execução?

**R:** Para o Node.js, é possível especificar o arquivo de configuração PM2 ou o arquivo de script. Para o .NET Core, especifique o nome da DLL compilada. Para o Ruby, é possível especificar o script Ruby com o qual você deseja inicializar o aplicativo.

## <a name="management"></a>Gerenciamento

**P:** Pressionei o botão de reinicialização no portal do Azure, mas meu aplicativo Web não foi reiniciado. Por quê?

**R:** Estamos trabalhando para habilitar o botão de reinicialização em um futuro próximo. No momento, você tem duas opções:
- Adicionar ou alterar uma configuração de aplicativo fictício. Isso forçará o aplicativo Web a ser reiniciado.
- Pare e inicie o aplicativo Web.

**P:** Posso usar o SSH (Secure Shell) para me conectar à VM (máquina virtual) do contêiner de aplicativo?

**R:** Não. Forneceremos uma maneira de usar o SSH para se conectar ao contêiner de aplicativo em uma versão futura.

## <a name="continuous-integrationdeployment"></a>Integração/implantação contínua

**P:** Meu aplicativo Web ainda usa uma imagem de contêiner antiga do Docker depois que atualizei a imagem no Hub do Docker. Há suporte para implantação/integração contínua de contêineres personalizados?

**R:** É possível atualizar o contêiner parando e, em seguida, iniciando o aplicativo Web. Se preferir, é possível alterar ou adicionar uma configuração de aplicativo fictício para forçar uma atualização do contêiner. Estamos planejando ter um recurso de integração/implantação contínua para contêineres personalizados em uma versão futura.

## <a name="language-support"></a>Suporte ao idioma

**P:** Há suporte para aplicativos .NET Core não compilados?

**R:** Não. Você precisa implantar aplicativos .NET Core compilados com todas as dependências. Estamos planejando uma experiência completa de implantação e build em uma versão futura.

**P:** Há suporte para o Criador como um gerenciador de dependências para aplicativos PHP?

**R:** Não. É necessário implantar os aplicativos PHP com todas as dependências. Estamos planejando uma experiência completa de implantação em uma versão futura.

## <a name="custom-containers"></a>Contêineres personalizados

**P:** Estou usando meu próprio contêiner personalizado. Meu aplicativo reside no diretório \home\, mas não consigo encontrar meus arquivos ao navegar pelo conteúdo usando o [site do SCM](https://github.com/projectkudu/kudu) ou um cliente FTP. Onde estão meus arquivos?

**R:** Montamos um compartilhamento SMB no diretório \home\. Isso substitui todo o conteúdo existente nele.

**P:** que quero expor mais de uma porta em minha imagem de contêiner personalizados. Isso é possível?

**R:** No momento, não há suporte para esse recurso.

**P:** posso colocar meu próprio armazenamento?

**R:** No momento, não há suporte para esse recurso.

**P:** não é possível navegar os processos do meu contêiner personalizado em execução ou de sistema de arquivos do site do SCM. Por que isso acontece?

**R:** O site do SCM é executado em um contêiner separado. Não é possível verificar o sistema de arquivos ou os processos em execução do contêiner de aplicativo.

**P:** Meu contêiner personalizado escuta uma porta diferente da porta 80. Como configurar meu aplicativo para rotear as solicitações para essa porta?

**R:** É possível especificar uma configuração de aplicativo chamada **PORT** e fornecer a ela o valor do número da porta esperada.

## <a name="pricing-and-sla"></a>Preço e SLA

**P:** Qual é o preço durante o uso da visualização pública?

**R:** Você pagará metade do número de horas em que o aplicativo é executado, com o preço normal do Serviço de Aplicativo do Azure. Isso significa que você obtém um desconto de 50% sobre o preço normal do Serviço de Aplicativo do Azure.

## <a name="other"></a>Outros

**P:** quais são os caracteres com suporte em nomes de configurações do aplicativo?

**R:** Só é possível usar A-Z, a-z, 0-9 e sublinhado para as configurações do aplicativo.

**P:** onde solicitar novos recursos?

**R:** É possível enviar sua ideia para o [fórum de comentários dos Aplicativos Web](https://aka.ms/webapps-uservoice). Adicione “[Linux]” ao título de sua ideia.

## <a name="next-steps"></a>Próximas etapas
* [O que é Serviço de Aplicativo no Linux?](app-service-linux-intro.md)
* [Criando aplicativos Web no Serviço de Aplicativo no Linux](app-service-linux-how-to-create-a-web-app.md)

