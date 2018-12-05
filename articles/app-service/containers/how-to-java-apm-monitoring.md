---
title: Configurar Java APM e ferramentas de monitoramento com Serviço de Aplicativo do Azure no Linux
description: Saiba como enviar logs e métricas para seus aplicativos Java em execução no Serviço de Aplicativo do Azure Linux para provedores de NewRelic e o aplicativo Dynamics APM
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 11/27/2018
ms.author: astay;routlaw
ms.openlocfilehash: 06ae71fea1b85a74d87588d2635038c64c8540cc
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52576916"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Como: ferramentas do monitoramento de desempenho do aplicativo com aplicativos Java no Serviço de Aplicativo do Azure no Linux

Este artigo mostra como conectar aplicativos Java implantados no Serviço de Aplicativo do Azure no Linux com o desempenho do aplicativo NewRelic e da AppDynamics monitoram as plataformas (APM).

## <a name="configure-new-relic"></a>Configurar o New Relic
1. Criar uma conta de NewRelic em [NewRelic.com](https://newrelic.com/signup)
1. Fazer o download do agente de Java do NewRelic, ele terá um nome de arquivo semelhante ao `newrelic-java-x.x.x.zip`.
1. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
1. [SSH em sua instância do Serviço de Aplicativo](/azure/app-service/containers/app-service-linux-ssh-support) e crie um novo diretório `/home/site/wwwroot/apm`. 
1. Carregue os arquivos do agente NewRelic Java descompactados em um diretório em `/home/site/wwwroot/apm`. Os arquivos para seu agente devem estar em `/home/site/wwwroot/apm/newrelic`.
1. Modifique o arquivo YAML em `/home/site/wwwroot/apm/newrelic/newrelic.yml` e substitua o valor de licença de espaço reservado com sua própria chave de licença.
1. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando **Tomcat**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente o `javaagent` opção ao final do valor atual.

## <a name="configure-appdynamics"></a>Configurar o AppDynamics
1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Fazer o download do agente Java do site do AppDynamics, o nome do arquivo será semelhante a `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH em sua instância do Serviço de Aplicativo](/azure/app-service/containers/app-service-linux-ssh-support) e crie um novo diretório `/home/site/wwwroot/apm`. 
1. Carregue os arquivos do agente Java descompactados em um diretório em `/home/site/wwwroot/apm`. Os arquivos para seu agente devem estar em `/home/site/wwwroot/apm/appdynamics`.
1. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` quando `<YOUR_SITE_NAME>` for o nome do seu Serviço de Aplicativo.
    - Se você estiver usando **Java SE**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` quando `<YOUR_SITE_NAME>` for o nome do seu Serviço de Aplicativo.
