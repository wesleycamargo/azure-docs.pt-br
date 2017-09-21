---
title: Implantar o Bocal do Azure Log Analytics para Monitoramento do Cloud Foundry | Microsoft Docs
description: "Orientação passo a passo sobre como implantar o bocal agregador de logs no Azure Log Analytics, configurar o Azure Log Analytics e o console OMS e usar essas ferramentas para monitorar a integridade do sistema e as métricas de desempenho do Cloud Foundry."
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f704a2638a4b6b57c014d502dd87303a55334672
ms.contentlocale: pt-br
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implantar o Bocal do Azure Log Analytics para Monitoramento do Sistema do Cloud Foundry

## <a name="background"></a>Segundo plano

O [Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) é um serviço do OMS (Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/)). Ele ajuda a coletar e analisar dados gerados em ambientes locais e em nuvem.

O Bocal do Microsoft Azure Log Analytics (o Bocal) é um componente do Cloud Foundry, que encaminha métricas do [Agregador de Logs Firehose do Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) para o Azure Log Analytics. Com o Bocal, é possível coletar, exibir e analisar a integridade do sistema e as métricas de desempenho do Cloud Foundry em várias implantações.

Neste documento, será mostrado como implantar o Bocal no ambiente do Cloud Foundry e acessar os dados do console OMS do Azure Log Analytics.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="1-deploy-a-cf-or-pcf-environment-in-azure"></a>1. Implantar um ambiente CF ou PCF no Azure

É possível usar o Bocal com uma implantação de software livre do CF (Cloud Foundry) ou com uma implantação do PCF (Pivotal Cloud Foundry).

* [Implantar o Cloud Foundry no Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implantar o Pivotal Cloud Foundry no Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Instale as ferramentas da linha de comando do CF para implantar o Bocal

O Bocal será executado como um aplicativo no ambiente do CF e será necessária uma CLI do CF para implantar o aplicativo. Para criar e configurar o usuário, também é necessária ter uma permissão de acesso ao agregador de logs firehose e ao Cloud Controller, bem como o cliente da linha de comando do UAA.

* [Instalar a CLI do Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalar o cliente da linha de comando do UAA do Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Verifique se o Rubygems está instalado antes de configurar o cliente da linha de comando do UAA.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Criar um espaço de trabalho do OMS no Azure

#### <a name="create-the-oms-workspace-manually"></a>Criar o espaço de trabalho do OMS manualmente

É possível criar o espaço de trabalho do OMS manualmente e carregar exibições e alertas pré-configurados do OMS após a implantação do Bocal.

1. No portal do Azure, pesquise o Log Analytics na lista de serviços do Marketplace e selecione-o.
2. Clique em Criar e, em seguida, selecione opções para os seguintes itens:

* Espaço de trabalho do OMS: digite um nome para o espaço de trabalho.
* Assinatura: se você tiver várias assinaturas, escolha a mesma com a implantação do CF.
* Grupo de recursos: é possível criar um novo grupo de recursos ou usar o mesmo com a implantação do CF.
* Local
* Tipo de preço: clique em OK para concluir.
> Para obter mais informações, consulte [Introdução ao Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)

#### <a name="create-the-oms-workspace-through-the-oms-template"></a>Criar o espaço de trabalho do OMS usando o modelo do OMS

É possível criar o espaço de trabalho do OMS e carregar exibições e alertas pré-configurados do OMS por meio da [Solução Log Analytics do Azure OMS para o Cloud Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution)

## <a name="deploy-the-nozzle"></a>Implantar o Bocal

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implantar o Bocal como um bloco Ops Manager do PCF

Se o PCF foi implantado por meio do Ops Manager, siga estas etapas para [Instalar e Configurar o Bocal no PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). O Bocal é instalado como um bloco com o Ops Manager.

### <a name="deploy-the-nozzle-as-an-application-to-cloud-foundry"></a>Implantar o Bocal como um aplicativo no Cloud Foundry

Se você não estiver usando o Ops Manager do PCF, será necessário efetuar push do Bocal como um aplicativo.

#### <a name="log-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Faça logon na implantação do CF como administrador por meio da CLI do CF

Na caixa de desenvolvimento, execute o seguinte comando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

> "SYSTEM_DOMAIN" é o nome de domínio do CF. É possível recuperá-lo procurando por "SYSTEM_DOMAIN" no arquivo de manifesto de implantação no CF. 
> "CF_User" é o nome do administrador do CF. É possível recuperar o nome e a senha procurando pela seção "scim", pelo nome e pelo "cf_admin_password" no arquivo de manifesto de implantação no CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Criar um usuário do CF e conceder as permissões necessárias

Na caixa de desenvolvimento, execute os seguintes comandos:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

> "SYSTEM_DOMAIN" é o nome de domínio do CF. É possível recuperá-lo procurando por "SYSTEM_DOMAIN" no arquivo de manifesto de implantação no CF.

#### <a name="download-the-latest-azure-log-analytics-nozzle-release"></a>Baixe a última versão do Bocal do Azure Log Analytics

Na caixa de desenvolvimento, execute o seguinte comando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables-in-manifestyml-in-your-current-directory"></a>Defina variáveis de ambiente no "manifest.yml" do diretório atual

Esse é o manifesto do aplicativo para o Bocal. É necessário substituir o valor com as informações específicas do espaço de trabalho do OMS.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics, default is 10s.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics, default is 10s.
OMS_MAX_MSG_NUM_PER_BATCH : The max number of messages in a batch to OMS Log Analytics, default is 1000.
API_ADDR                  : The api URL of the CF environment, refer to "Push the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
DOPPLER_ADDR              : Loggregator's traffic controller URL, refer to "Deploy the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
FIREHOSE_USER             : CF user you created in "Push the Nozzle as an App to Cloud Foundry" section, who has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma separated list, valid event types are METRIC,LOG,HTTP
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the Trafficcontroller
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments
IDLE_TIMEOUT              : Keep Alive duration for the firehose consumer, default is 60s.
LOG_LEVEL                 : Logging level of the nozzle, valid levels: DEBUG, INFO, ERROR
LOG_EVENT_COUNT           : If true, the total count of events that the nozzle has received and sent will be logged to OMS Log Analytics as CounterEvents
LOG_EVENT_COUNT_INTERVAL  : The time interval of logging event count to OMS Log Analytics, default is 60s.
```

### <a name="push-the-application-from-your-dev-box"></a>Efetuar push do aplicativo na caixa de desenvolvimento

Verifique se que você está na pasta "oms-log-analytics-firehose-nozzle" e execute:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar a instalação do Bocal

### <a name="from-apps-manager-for-pcf"></a>No Gerenciador de Aplicativos (para o PCF)

1. Faça logon no Ops Manager e verifique se o bloco é exibido no painel de instalação.
2. Faça logon no Gerenciador de Aplicativos, verifique se o espaço criado para o bocal aparece no relatório de uso e se o status é normal.

### <a name="from-dev-box"></a>Na caixa de desenvolvimento

Na janela da CLI do CF da caixa de desenvolvimento, digite:
```
cf apps
```
Verifique se o aplicativo do Bocal do OMS está em execução.

## <a name="view-the-data-in-oms-portal"></a>Exiba os dados no portal do OMS

### <a name="1-import-oms-view"></a>1. Importar a exibição do OMS

No portal do OMS, procure **Exibir Designer** -> **Importar** -> **Procurar**, selecione um dos arquivos omsview, por exemplo, *Cloud Foundry.omsview* e salve a exibição. Um **Bloco** será exibido na página principal da Visão Geral do OMS. Clique no **Bloco** e ele mostrará as métricas visualizadas.

Os operadores podem personalizar ou criar novas exibições por meio do **Designer de exibição**.

O *"Cloud Foundry.omsview"* é uma versão prévia do modelo de exibição do OMS no Cloud Foundry. Um modelo padrão totalmente configurado está sendo desenvolvido, envie sugestões e comentários para a [Seção de Problemas](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Criar regras de alerta

Os operadores podem [criar os alertas](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts) e personalizar as consultas e valores limite conforme o necessário. Um conjunto de alertas recomendados é mostrado a seguir.

| Consulta de pesquisa                                                                  | Gerar alerta com base em | Descrição                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Número de resultados < 1   | **bbs.Domain.cf-apps** indica se o domínio cf-apps está atualizado, o que significa que as solicitações de aplicativo do CF enviadas pelo Cloud Controller estão sincronizadas com o bbs.LRPsDesired (AIs recomendadas para Diego) para execução. Nenhum dado recebido significa que o domínio cf-apps não está atualizado na janela de tempo exibida. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Número de resultados > 0   | Para células Diego, 0 significa íntegro e 1 significa não íntegro. Defina o alerta se várias **células Diego não íntegras** forem detectadas na janela de tempo exibida. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Número de resultados > 0 | 1 significa que o sistema é íntegro e 0 significa que o sistema é não íntegro. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Número de resultados > 0   | Consul emite o status de integridade periodicamente. 0 significa que o sistema é íntegro e 1 significa que emissor de rota detectou que o **Consul está inoperante**. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Número de resultados > 0 | O número delta de mensagens intencionalmente **descartadas** pelo Doppler em razão da pressão de retorno. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Número de resultados > 0   | O Agregador de Logs emite **LGR** para indicar problemas com o processo de registro em log, por exemplo, quando a saída de mensagens de log é muito alta. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Número de resultados > 0   | Quando o bocal recebe o alerta de consumidor lento do Agregador de Logs, ele envia o ValueMetric **slowConsumerAlert** ao OMS. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Número de resultados > 0   | Se o número delta de **eventos perdidos** atingir um limite, isso significa que o bocal terá problemas de execução. |

## <a name="scale"></a>Escala

### <a name="scale-the-nozzle"></a>Dimensionar o bocal

Recomenda-se que os operadores comecem com pelo menos duas instâncias do bocal. O firehose distribui a carga de trabalho em todas as instâncias do bocal.
Para assegurar-se de que o bocal consegue acompanhar o tráfego de dados do firehose, o operador deve configurar o alerta **slowConsumerAlert**, listado na seção “Criar Regras de Alerta”. Depois do alerta, siga a [orientação para bocal lento](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) para determinar se o dimensionamento é necessário.
Para aumentar o bocal, use o Gerenciador de Aplicativos ou a CLI do CF para aumentar a quantidade de instâncias ou recursos de disco/memória do bocal.

### <a name="scale-the-loggregator"></a>Dimensionar o Agregador de Logs

O Agregador de Logs envia a mensagem de log **LGR** para indicar problemas com o processo de registro. O operador pode monitorar o alerta para determinar se o agregador de logs precisa ser aumentado.
Para aumentar o agregador de logs, o operador pode aumentar o tamanho do buffer Doppler ou adicionar mais instâncias de servidor Doppler ao manifesto do CF. Para saber mais detalhes, verifique [as orientações para dimensionar o Agregador de Logs](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Atualização

Para atualizar o Bocal com uma versão mais recente, baixe a versão nova do bocal, siga as etapas na seção "Implantar" e efetue push do aplicativo novamente.

Para remover o Bocal, siga estas etapas:

### <a name="from-the-ops-manager"></a>No Ops Manager

1. Faça logon no Ops Manager
2. Localize o bloco "Bocal do Microsoft Azure Log Analytics para o PCF"
3. Clique no ícone de lixeira e confirme a ação de exclusão.

### <a name="from-the-dev-box"></a>Na caixa de desenvolvimento

Na janela da CLI do CF, digite:
```
cf delete <App Name> -r
```

Se você remover o Bocal, os dados no portal do OMS não serão removidos automaticamente, eles expirarão com base na configuração de retenção de análise do Log Analytics no OMS.

## <a name="support-and-feedback"></a>Suporte e comentários

O Bocal do Azure Log Analytics é um software livre. Envie suas perguntas e comentários para a [seção do github](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Para abrir uma solicitação de suporte do Azure, use “Máquina Virtual que executa o Cloud Foundry” como categoria de serviço. 

## <a name="next-step"></a>Próxima etapa

Além das métricas do Cloud Foundry abrangidas no Bocal, é possível utilizar o agente do OMS para obter insights sobre os dados operacionais no nível da VM (Syslog, Desempenho, Alertas, Inventário), ele é instalado como um complemento Bosh nas VMs do CF.
> Para saber mais detalhes, consulte [Implantar o agente do OMS na implantação do Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
