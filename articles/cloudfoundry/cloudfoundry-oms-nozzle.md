---
title: Implantar o bocal do Azure Log Analytics para monitoramento do Cloud Foundry | Microsoft Docs
description: Orientações passo a passo sobre a implantação do bocal do agregador de logs do Cloud Foundry para o Azure Log Analytics. Use o bocal para monitorar as métricas de desempenho e de integridade de sistema do Cloud Foundry.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: c58c2b255d269aef7e8b3fea62d003ad0c16ef0a
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971241"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implantar o Bocal do Azure Log Analytics para Monitoramento do Sistema do Cloud Foundry

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) é um serviço no Azure. Ele ajuda a coletar e analisar dados gerados em ambientes locais e em nuvem.

O Bocal do Log Analytics (o Bocal) é um componente do CF (Cloud Foundry), que encaminha métricas do firehose do [agregador de logs do Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) para o Log Analytics. Com o Bocal, é possível coletar, exibir e analisar a integridade do sistema e as métricas de desempenho do CF em várias implantações.

Neste documento, você aprenderá como implantar o Bocal no ambiente do CF e acessar os dados do console do Azure Log Analytics.

## <a name="prerequisites"></a>pré-requisitos

As etapas a seguir são pré-requisitos para implantar o Bocal.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Implantar um ambiente CF ou Pivotal Cloud Foundry no Azure

É possível usar o Bocal com uma implantação de software livre do CF ou com uma implantação do PCF (Pivotal Cloud Foundry).

* [Implantar o Cloud Foundry no Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implantar o Pivotal Cloud Foundry no Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Instale as ferramentas da linha de comando do CF para implantar o Bocal

O Bocal é executado como um aplicativo no ambiente CF. Você precisa da CLI do CF para implantar o aplicativo.

O Bocal também precisa de permissão de acesso para o firehose do agregador de logs e para o controlador da nuvem. Para criar e configurar o usuário, você precisa do serviço UAA (Conta e Autenticação do Usuário).

* [Instalar a CLI do Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalar o cliente da linha de comando do UAA do Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Antes de configurar o cliente da linha de comando do UAA, certifique-se de que o Rubygems está instalado.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Criar um workspace do Log Analytics

É possível criar o workspace do Log Analytics manualmente ou usando um modelo. O modelo será implantado uma configuração de exibições de KPI do OMS e alertas para o console do OMS. 

#### <a name="to-create-the-workspace-manually"></a>Para criar o workspace manualmente:

1. No Portal do Azure, pesquise a lista de serviços no Azure Marketplace e, em seguida, selecione Log Analytics.
2. Selecione **Criar** e, em seguida, selecione opções para os seguintes itens:

   * **Espaço de trabalho do OMS**: digite um nome para o espaço de trabalho.
   * **Assinatura**: se você tiver várias assinaturas, escolha a mesma que a da sua implantação do CF.
   * **Grupo de recursos**: é possível criar um novo grupo de recursos ou usar o mesmo com a implantação do CF.
   * **Local**: insira o local.
   * **Tipo de preço**: selecione **OK** para concluir.

Para saber mais, confira [Introdução ao Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-oms-workspace-through-the-oms-monitoring-template-from-azure-market-place"></a>Para criar o workspace do OMS por meio do modelo de monitoramento do OMS do Azure Marketplace:

1. Abra o portal do Azure.
2. Clique no sinal "+" ou em "Criar um recurso" no canto superior esquerdo.
3. Digite "Cloud Foundry" na janela de pesquisa, selecione "Solução de Monitoramento Cloud Foundry do OMS".
4. A página inicial do modelo da solução de monitoramento Cloud Foundry do OMS é carregada, clique em “Criar” para iniciar a folha do modelo.
5. Insira os parâmetros necessários:
    * **Assinatura**: selecione uma assinatura do Azure para o espaço de trabalho do OMS, normalmente a mesmo com a implantação do Cloud Foundry.
    * **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo para o espaço de trabalho do OMS.
    * **Localização do Grupo de Recursos**: selecione a localização do grupo de recursos.
    * **OMS_Workspace_Name**: insira um nome de espaço de trabalho. Se o espaço de trabalho não existir, o modelo criará um novo.
    * **OMS_Workspace_Region**: selecione a localização para o espaço de trabalho.
    * **OMS_Workspace_Pricing_Tier**: selecione o SKU do espaço de trabalho do OMS. Confira as [diretrizes de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para referência.
    * **Termos legais**: clique em Termos legais e clique em “Criar” para aceitar o termo legal.
- Depois que todos os parâmetros forem especificados, clique em "Criar" para implantar o modelo. Quando a implantação for concluída, o status será exibido na guia de notificação.


## <a name="deploy-the-nozzle"></a>Implantar o Bocal

Há algumas maneiras diferentes para implantar o Bocal: como um bloco PCF ou como um aplicativo CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implantar o Bocal como um bloco Ops Manager do PCF

Siga as etapas para [instalar e configurar o Bocal do Azure Log Analytics para PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Essa é a abordagem simplificada, o bloco PCF Ops manager configurará e efetuará o push do bocal automaticamente. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Implantar o Bocal manualmente como um aplicativo CF

Se você não estiver usando o Ops Manager do PCF, implante o Bocal como um aplicativo. As seções a seguir descrevem esse processo.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Entre na implantação do CF como administrador por meio da CLI do CF

Execute o comando a seguir:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" é o nome de domínio do CF. É possível recuperá-lo procurando por "SYSTEM_DOMAIN" no arquivo de manifesto de implantação no CF. 

"CF_User" é o nome do administrador do CF. É possível recuperar o nome e a senha procurando a seção "scim", o nome e o "cf_admin_password" no arquivo de manifesto de implantação do CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Criar um usuário do CF e conceder as permissões necessárias

Execute os seguintes comandos:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" é o nome de domínio do CF. É possível recuperá-lo procurando por "SYSTEM_DOMAIN" no arquivo de manifesto de implantação no CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Baixar a última versão do Bocal do Log Analytics

Execute o comando a seguir:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Configurar variáveis de ambiente

Agora é possível definir variáveis de ambiente no arquivo manifest.yml em seu diretório atual. O exemplo a seguir mostra o manifesto do aplicativo do Bocal. Substitua os valores por suas informações específicas de workspace do Log Analytics.

```
OMS_WORKSPACE             : Log Analytics workspace ID: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Efetuar push do aplicativo no seu computador de desenvolvimento

Certifique-se de que você está na pasta oms-log-analytics-firehose-nozzle. Execute o comando a seguir:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar a instalação do Bocal

### <a name="from-apps-manager-for-pcf"></a>No Gerenciador de Aplicativos (para o PCF)

1. Entre no Ops Manager e certifique-se de que o bloco é exibido no painel de instalação.
2. Entre no Gerenciador de Aplicativos, certifique-se de que o espaço criado para o bocal aparece no relatório de uso e confirme que o status é normal.

### <a name="from-your-development-computer"></a>No seu computador de desenvolvimento

Na janela da CLI do CF, digite:
```
cf apps
```
Verifique se o aplicativo do Bocal do OMS está em execução.

## <a name="view-the-data-in-the-oms-portal"></a>Exiba os dados no portal do OMS

Se você tiver implantado a solução de monitoramento do OMS por meio do modelo do Marketplace, acesse o portal do Azure e encontre a solução do OMS. Você pode encontrar a solução no grupo de recursos especificado no modelo. Clique na solução, navegue até o "Console do OMS", as exibições pré-configuradas são listadas, com os principais KPIs de sistema do Cloud Foundry, dados de aplicativos, alertas e métricas de integridade da VM. 

Se você tiver criado o workspace do OMS manualmente, siga as etapas abaixo para criar exibições e alertas:

### <a name="1-import-the-oms-view"></a>1. Importar o modo de exibição do OMS

No portal do OMS, navegue até **Designer de exibição** > **Importar** > **Procurar** e selecione um dos arquivos omsview. Por exemplo, selecione *Cloud Foundry.omsview* e salve a exibição. Agora um bloco é exibido na página **Visão Geral** do OMS. Selecione-a para ver as métricas visualizadas.

É possível personalizar essas exibições ou criar novas por meio do **Designer de exibição**.

O *"Cloud Foundry.omsview"* é uma versão prévia do modelo de exibição do OMS do Cloud Foundry. Este é um modelo padrão totalmente configurado. Se você tiver sugestões ou comentários sobre o modelo, envie-os para a [seção de problemas](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Criar regras de alerta

É possível [criar os alertas](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts) e personalizar as consultas e os valores limite conforme necessário. A seguir estão os alertas recomendados:

| Consulta de pesquisa                                                                  | Gerar alerta com base em | DESCRIÇÃO                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Número de resultados < 1   | **bbs.Domain.cf-apps** indica se o domínio cf-aps está atualizado. Isso significa que as solicitações de aplicativo do CF enviadas pelo Cloud Controller estão sincronizadas com o bbs.LRPsDesired (AIs recomendadas para Diego) para execução. Nenhum dado recebido significa que o domínio cf-apps não está atualizado na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Número de resultados > 0   | Para células Diego, 0 significa íntegro e 1 significa não íntegro. Defina o alerta se várias células Diego não íntegras forem detectadas na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Número de resultados > 0 | 1 significa que o sistema é íntegro e 0 significa que o sistema é não íntegro. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Número de resultados > 0   | Consul emite o status de integridade periodicamente. 0 significa que o sistema é íntegro e 1 significa que emissor de rota detectou que o Consul está inoperante. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Número de resultados > 0 | O número delta de mensagens intencionalmente removidas pelo Doppler em razão da pressão de retorno. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Número de resultados > 0   | O Agregador de Logs emite **LGR** para indicar problemas com o processo de registro. Um exemplo desse problema ocorre quando a saída de mensagem de log é muito alta. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Número de resultados > 0   | Quando o Bocal recebe o alerta de consumidor lento do agregador de logs, ele envia o ValueMetric **slowConsumerAlert** ao Log Analytics. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Número de resultados > 0   | Se o número delta de eventos perdidos atingir um limite, isso significará que o bocal terá problemas de execução. |

## <a name="scale"></a>Escala

É possível dimensionar o Bocal e o agregador de logs.

### <a name="scale-the-nozzle"></a>Dimensionar o bocal

Você deve iniciar com pelo menos duas instâncias do Bocal. O firehose distribui a carga de trabalho em todas as instâncias do Bocal.
Para certificar-se de que o Bocal pode acompanhar o tráfego de dados do firehouse, configure o alerta **slowConsumerAlert** (listado na seção anterior, "Criar regras de alerta"). Após ter sido alertado, execute as [diretrizes para Bocal lento](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) para determinar se o dimensionamento é necessário.
Para escalar verticalmente o Bocal, use o Gerenciador de Aplicativos ou a CLI do CF para aumentar a quantidade de instâncias ou os recursos de disco ou de memória do Bocal.

### <a name="scale-the-loggregator"></a>Dimensionar o agregador de logs

O Agregador de Logs envia uma mensagem de log **LGR** para indicar problemas com o processo de registro. É possível monitorar o alerta para determinar se o agregador de logs precisa ser escalado verticalmente.
Para escalar verticalmente o agregador de logs, aumente o tamanho do buffer do Doppler ou adicione mais instâncias de servidor do Doppler no manifesto do CF. Para obter mais informações, consulte [as diretrizes para dimensionar o agregador de logs](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Atualizar

Para atualizar o Bocal com uma versão mais recente, baixe a versão do novo Bocal, siga as etapas na seção “Implantar o Bocal” anterior e efetue push do aplicativo novamente.

### <a name="remove-the-nozzle-from-ops-manager"></a>Remover o Bocal do Ops Manager

1. Entre no Ops Manager.
2. Localize o bloco **Bocal do Microsoft Azure Log Analytics para PCF**.
3. Selecione o ícone de lixeira e confirme a exclusão.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Remover o Bocal do seu computador de desenvolvimento

Na janela da CLI do CF, digite:
```
cf delete <App Name> -r
```

Se você remover o Bocal, os dados no Portal do OMS não serão removidos automaticamente. Eles expiram com base na sua configuração de retenção do Log Analytics.

## <a name="support-and-feedback"></a>Suporte e comentários

O Bocal do Azure Log Analytics é de software livre. Envie suas dúvidas e comentários para a [seção GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Para abrir uma solicitação de suporte do Azure, escolha “Máquina virtual que executa o Cloud Foundry” como a categoria de serviço. 

## <a name="next-step"></a>Próxima etapa

Do PCF2.0, as métricas de desempenho da VM são transferidas para bocal do Azure Log Analytics pelo Encaminhador de Métricas do Sistema e integradas ao workspace do OMS. Você não precisa mais do agente do OMS para as métricas de desempenho da VM. No entanto você ainda pode usar o agente do OMS para coletar informações de Syslog. O agente do OMS é instalado como um complemento Bosh nas VMs do seu CF. 

Para obter detalhes, consulte [Deploy OMS agent to your Cloud Foundry deployment](https://github.com/Azure/oms-agent-for-linux-boshrelease) (Implantar o agente do OMS na implantação do Cloud Foundry).
