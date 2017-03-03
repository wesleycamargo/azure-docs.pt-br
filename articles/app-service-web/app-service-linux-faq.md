---
title: "Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux."
keywords: "serviço de aplicativo do azure, aplicativo web, perguntas frequentes, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
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
ms.sourcegitcommit: 831ef097027721146531e8d699fe3f67417a57ea
ms.openlocfilehash: b88aa3d0ae89aec81c2b9144fb5de3210a0b8d1e
ms.lasthandoff: 02/18/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux #

Com a versão do serviço de aplicativo do Azure no Linux (atualmente em visualização), estamos trabalhando na adição de recursos e fazer melhorias para nossa plataforma. Aqui estão algumas perguntas frequentes que nossos clientes têm feito nos últimos meses.
Se você tiver uma pergunta, comente o artigo e, o responderemos assim que possível.

## <a name="built-in-images"></a>Imagens internas ##

**P:** desejo divida os contêineres de Docker internos que a plataforma oferece. Onde posso encontrar esses arquivos?

**R:** Docker todos os arquivos podem ser encontrados aqui: https://github.com/azure-app-service. Todos os contêineres de Docker podem ser encontrados aqui: https://hub.docker.com/u/appsvc/.

## <a name="management"></a>Gerenciamento ##

**P:** pressiono o botão de reinicialização no portal, mas minha webapp não foi reiniciado, por quê?

**R:** estamos trabalhando sobre como habilitar o botão Redefinir em breve, agora, você tem duas opções.
1. Adicionar ou alterar uma configuração de aplicativo fictício, isso forçará o webapp para reiniciar. 
2. Parar e iniciar o webapp.

**P:** possível SSH na VM?

**R:** Não, forneceremos uma maneira de usar SSH em seu contêiner de aplicativo em uma versão futura.

## <a name="continuous-integration--deployment"></a>Implantação/integração contínuas ##

**P:** meu webapp ainda usa uma imagem de contêiner de encaixe antiga depois de atualizar a imagem no DockerHub? Há suporte para implantação/integração contínua de contêineres personalizados?

**R:** você pode atualizar o contêiner, parar e iniciar seu aplicativo Web ou alterar/adicionar uma configuração de aplicativo fictício configurando para forçar uma atualização do seu contêiner, será ter um recurso de CI/CD para contêineres personalizados em uma versão futura.

## <a name="language-support"></a>Suporte ao idioma ##

**P:** dão suporte a aplicativos de núcleo .net não compilado?

**R:** não, você precisa implantar o aplicativo de núcleo .net compilado com todas as dependências, uma implantação completa e experiência de build estarão disponíveis em uma versão futura.

## <a name="built-in-images"></a>Imagens internas ##

**P:** Quais são os valores esperados para a seção Arquivo de Inicialização quando configuro a pilha de tempo de execução?

**R:** Para o Node.js, você pode especificar o arquivo de configuração de PM2 ou o arquivo de script. Para o .Net Core, você deve especificar o nome da dll compilada. Para o Ruby, você pode especificar um script Ruby com o qual você deseja inicializar o aplicativo.

## <a name="custom-containers"></a>Contêineres personalizados ##

**P:** usando o meu próprio contêiner personalizado. Meu aplicativo reside no diretório \home\, mas não encontro meus arquivos quando navegar o conteúdo usando o site do SCM ou um cliente de ftp. Onde estão meus arquivos?

**R:** podemos montar um compartilhamento SMB para \home\ diretório; portanto, substituindo qualquer conteúdo.

**P:** que quero expor mais de uma porta em minha imagem de contêiner personalizados. Isso é possível?

**R:** atualmente não é aceita.

**P:** posso colocar meu próprio armazenamento?

**R:** atualmente não é aceita.

**P:** não é possível navegar os processos do meu contêiner personalizado em execução ou de sistema de arquivos do site do SCM. Por que isso acontece?

**R:** site o SCM é executado em um contêiner separado, você não pode verificar os processos de execução ou sistema de arquivo do contêiner do aplicativo.

**P:** Meu contêiner personalizado escuta uma porta diferente da porta 80. Como configurar meu aplicativo para rotear as solicitações para essa porta?

**R:** Você pode especificar uma configuração de aplicativo chamada **PORT** e dar a ela o valor do número da porta esperada.

## <a name="pricing-and-sla"></a>Preço e SLA ##

**P:** qual é o preço enquanto estiver na visualização pública?

**R:** você será cobrado metade do número de horas que seu aplicativo é executado, o serviço de aplicativo do Azure normal preços; efetivamente que significa um desconto de 50% sobre o preço normal de serviço de aplicativo do Azure.

## <a name="other"></a>Outros ##

**P:** quais são os caracteres com suporte em nomes de configurações do aplicativo?

**R:** você só pode usar A-Z, a-z, 0-9 e sublinhado para configurações do aplicativo.

**P:** onde solicitar novos recursos?

**R:** pode enviar sua ideia aqui: https://aka.ms/webapps-uservoice. Adicione [Linux] ao título da sua ideia.

## <a name="next-steps"></a>Próximas etapas
* [O que é Serviço de Aplicativo no Linux?](app-service-linux-intro.md)
* [Como criar Aplicativos Web no Serviço de Aplicativo no Linux](app-service-linux-how-to-create-a-web-app.md)

