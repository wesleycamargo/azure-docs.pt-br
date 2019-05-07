---
title: O Azure Monitor - Zero de monitoramento para o Kubernetes aplicativos hospedados do aplicativo de instrumentação | Microsoft Docs
description: Zero monitoramento para aplicativos do Kubernetes hospedado de aplicativos de instrumentação é uma solução de monitoramento que permite que você colete a telemetria do Application Insights que pertencem às solicitações de entrada e saídas para e de pods em execução em seu cluster do Kubernetes por utilizando a tecnologia de malha de serviço chamado Istio.
services: application-insights
author: rishabjolly
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: rijolly
ms.openlocfilehash: 73f95ab75b49fb8ec5b61f6e30080f8f6d474c16
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149889"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Aplicativos hospedados pelo zero monitoramento de aplicativos de instrumentação para Kubernetes

> [!IMPORTANT]
> Essa funcionalidade está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Monitor agora aproveita a tecnologia de malha do serviço em seu cluster Kubernetes para fornecer o monitoramento de aplicativo de caixa para qualquer aplicativo do Kubernetes hospedado fora do. Padrão, como recursos do Application Insight [mapa de aplicativo](../../azure-monitor/app/app-map.md) modelar suas dependências, [Stream de métricas ao vivo](../../azure-monitor/app/live-stream.md) para o monitoramento em tempo real, visualizações avançadas com o [padrão Dashboard](../../azure-monitor/app/overview-dashboard.md), [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md), e [pastas de trabalho](../../azure-monitor/app/usage-workbooks.md). Esse recurso ajudará os usuários identificar gargalos de desempenho e pontos de acesso de falha em todas as suas cargas de trabalho do Kubernetes em um namespace de Kubernetes selecionado. Aproveitando os investimentos de malha de serviço com tecnologias como Istio, o Azure Monitor permite o monitoramento de aplicativo instrumentado pelo automaticamente sem qualquer modificação no código do seu aplicativo.

> [!NOTE]
> Essa é uma das muitas maneiras de realizar o monitoramento de aplicativo no Kubernetes. Você também pode instrumentar qualquer aplicativo hospedado no Kubernetes usando o [SDK do Application Insights](../../azure-monitor/azure-monitor-app-hub.md) sem a necessidade de uma malha de serviço. Monitorar o Kubernetes sem instrumentar o aplicativo com um SDK que você pode usar o abaixo do método.

## <a name="prerequisites"></a>Pré-requisitos

- Um [cluster do Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Acesso ao cluster para executar do console *kubectl*.
- Um [recurso do Application Insights](create-new-resource.md)
- Ter uma malha de serviço. Se o cluster não tiver Istio implantado, você pode aprender como [instalar e usar Istio no serviço Kubernetes do Azure](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Funcionalidades

Usando o zero de monitoramento para o Kubernetes aplicativos hospedados do aplicativo de instrumentação, você será capaz de usar:

- [Mapa do aplicativo](../../azure-monitor/app/app-map.md)
- [Métricas de Stream em tempo real](../../azure-monitor/app/live-stream.md)
- [Painéis](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Distributed-tracing](../../azure-monitor/app/distributed-tracing.md)
- [Monitoramento de transação de ponta a ponta](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Etapas de instalação

Para habilitar a solução, podemos executará as seguintes etapas:
- Implante o aplicativo (se ainda não estiver implantada).
- Certifique-se de que o aplicativo é parte da malha de serviço.
- Observe a telemetria coletada.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurar seu aplicativo para trabalhar com uma malha de serviço

Istio suporta dois modos de [instrumentar um pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Na maioria dos casos, é mais fácil marcar o namespace do Kubernetes que contém o aplicativo com o *istio injeção* rótulo:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Uma vez que os dados de levantamentos da malha de serviço desativado durante a transmissão, nós não podem interceptar o tráfego criptografado. Para o tráfego que não saem do cluster, use um protocolo não criptografado (por exemplo, HTTP). Para o tráfego externo deve ser criptografado, considere [Configurando a terminação SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) no controlador de entrada.

Aplicativos em execução fora da malha de serviço não são afetados.

### <a name="deploy-your-application"></a>Implantar seu aplicativo

- Implantar seu aplicativo *namespace my app* namespace. Se o aplicativo já estiver implantado e você tiver seguido o método de injeção automática sidecar descrito acima, você precisará recriar os pods para garantir que istio injeta o sidecar; a iniciar uma atualização sem interrupção ou exclua pods individuais e aguarde até que eles sejam recriados.
- Certifique-se de que seu aplicativo está em conformidade com [Istio requisitos](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Implantar zero de monitoramento para o Kubernetes aplicativos hospedados do aplicativo de instrumentação

1. Baixe e extraia um [ *adaptador do Application Insights* release](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navegue até */src/kubernetes/* dentro da pasta release.
3. Editar *application-insights-istio-mixer-adapter-deployment.yaml*
    - Editar o valor de *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variável de ambiente para conter a chave de instrumentação do recurso do Application Insights no portal do Azure para conter a telemetria.
    - Se necessário, edite o valor de *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variável de ambiente para conter uma lista separada por vírgulas de namespaces para o qual você gostaria de habilitar o monitoramento. Deixe em branco para monitorar todos os namespaces.
4. Aplique *cada* YAML arquivo encontrado em *src/kubernetes/* executando o seguinte (você ainda deve estar dentro de */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificar implantação

- Verifique se o adaptador do Application Insights tiver sido implantado:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Em alguns casos, o ajuste fino de ajuste é necessário. Para incluir ou excluir a telemetria para um pod individual seja coletado, use *appinsights/monitoring.enabled* rótulo desse pod. Isso terá prioridade sobre todas as configurações de namespace. Definir *appinsights/monitoring.enabled* ao *verdadeiro* para incluir o pod e, ao *false* para excluí-lo.

### <a name="view-application-insights-telemetry"></a>Exibir telemetria do Application Insights

- Gere uma solicitação de exemplo no seu aplicativo para confirmar se monitoramento está funcionando corretamente.
- Dentro de 3 a 5 minutos, você deve começar a ver a telemetria aparecem no portal do Azure. Certifique-se de fazer check-out a *mapa de aplicativo* seção do recurso do Application Insights no Portal.

## <a name="troubleshooting"></a>solução de problemas

Seguir o fluxo de solução de problemas para usar quando telemetria não aparece no portal do Azure, como é esperado.

1. Verifique se o aplicativo está sob carga e está enviando/recebendo solicitações HTTP simples. Uma vez que a telemetria é retirada durante a transmissão, o tráfego criptografado não é suportado. Se não houver nenhuma solicitação de entrada ou saída, haverá sem telemetria tanto.
2. Verifique se a chave de instrumentação correta é fornecida na *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variável de ambiente no *application-insights-istio-mixer-adapter-deployment.yaml*. A chave de instrumentação é encontrada na *visão geral* guia do recurso do Application Insights no portal do Azure.
3. Verifique se o namespace correto do Kubernetes é fornecido na *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variável de ambiente no *application-insights-istio-mixer-adapter-deployment.yaml*. Deixe em branco para monitorar todos os namespaces.
4. Certifique-se de que os pods do seu aplicativo tem sido injetados sidecar por Istio. Verificar a existência de sidecar do Istio em cada pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verifique se há um contêiner denominado *istio proxy* em execução no pod.

5. Exiba rastreamentos do adaptador do Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A contagem de itens de telemetria recebida é atualizada uma vez por minuto. Se ele não fique minuto a minuto - nenhuma telemetria está sendo enviada para o adaptador por Istio.
   Procure erros no log.
6. Se ele tiver sido estabelecido que *Application Insight para Kubernetes* adaptador não está sendo alimentado telemetria, verifique os logs de Mixer do Istio para descobrir por que ele não está enviando dados para o adaptador:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Procure erros, especialmente referentes à comunicação com o *applicationinsightsadapter* adaptador.

## <a name="faq"></a>Perguntas frequentes

Para obter as informações mais recentes para o progresso neste projeto, visite o [adaptador do Application Insights para o GitHub do projeto do Mixer Istio](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Desinstalar

Para desinstalar o produto, para *cada* YAML arquivo encontrado em *kubernetes/src/* executar:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como Azure Monitor e contêineres funcionam juntos visita [Azure Monitor para a visão geral dos contêineres](../../azure-monitor/insights/container-insights-overview.md)