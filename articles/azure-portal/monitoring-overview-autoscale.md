<properties
	pageTitle="Visão geral do dimensionamento automático em Aplicativos Web, Serviços de Nuvem e Máquinas Virtuais do Microsoft Azure | Microsoft Azure"
	description="Visão Geral do dimensionamento automático no Microsoft Azure. Aplica-se a Máquinas Virtuais, Serviços de Nuvem e Aplicativos Web."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="robb"/>

# Visão geral do dimensionamento automático em Aplicativos Web, Serviços de Nuvem e Máquinas Virtuais do Microsoft Azure

Este artigo descreve o que é o dimensionamento automático do Microsoft Azure, seus benefícios e instrui você sobre como começar a usá-lo.

O dimensionamento automático do Azure Insights se aplica apenas a

* [Conjuntos de Escala de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/)
* [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/)
* [Serviço de Aplicativo - Aplicativos Web](https://azure.microsoft.com/services/app-service/web/)
  
   
>[AZURE.NOTE] O Azure tem dois métodos de dimensionamento automático. Uma versão mais antiga do dimensionamento automático aplica-se a Máquinas Virtuais (conjuntos de disponibilidade). Esse recurso tem suporte limitado e recomendamos a migração para os Conjuntos de Escala de VM para obter um suporte mais rápido e confiável ao dimensionamento automático. Um link sobre como usar a tecnologia mais antiga será incluído neste artigo.


## O que é o dimensionamento automático 

O dimensionamento automático permite que você tenha a quantidade certa de recursos em execução a fim de manipular a carga em seu aplicativo sem perder dinheiro devido a recursos ociosos. Ele permite que você adicione ou remova recursos de computação automaticamente com base em um conjunto de regras. A Figura 1 mostra esse conceito.

![Conceito de dimensionamento automático explicado](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

**Figura 1: conceito de dimensionamento automático explicado**

O dimensionamento automático ocorre apenas horizontalmente, que é um aumento ("out") ou diminuição ("in") do número de instâncias de VM. O dimensionamento horizontal é mais flexível em uma situação de nuvem, pois permite que você execute milhares de VMs para atender à carga. O outro tipo de dimensionamento é o vertical. O dimensionamento vertical mantém o mesmo número de VMs, mas torna a VM mais ("up") ou menos ("down") poderosa. O poder é medido em termos de memória, velocidade da CPU, espaço em disco etc. O dimensionamento vertical tem mais limitações. Ele depende da disponibilidade de um hardware maior, o que pode variar por região, e atinge rapidamente o limite superior. O dimensionamento vertical também exige normalmente um início e parada da VM. Para saber mais, confira [Dimensionar verticalmente uma máquina virtual do Azure com a Automação do Azure ](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


O dimensionamento automático exige que você crie regras sobre quando expandir ou reduzir os recursos. Os critérios que você pode definir para controlar as ações de dimensionamento incluem

* Número **mínimo** e **máximo** de instâncias a serem executadas. Um mínimo para ter certeza de que seu aplicativo esteja sempre em execução, e um máximo para controlar os custos.
* **Regra ou condição** para o dimensionamento automático. Pode ser um dimensionamento com base em métrica ou agendamento.
* **Tempo de resfriamento**, que é a quantidade de tempo de espera após um evento de dimensionamento automático, antes de permitir que outro evento de dimensionamento automático ocorra. Esse período serve para proteger contra um estado chamado de "oscilação", que ocorre quando as VMs são adicionadas e removidas rapidamente em questão de minutos. Há um custo para iniciar ou parar uma VM. A oscilação não economiza o custo, e a VM que está sendo iniciada e parada não pode fazer qualquer processamento útil, portanto, isso é pior do que deixar a VM em execução.

   
A lista completa de valores configuráveis está disponível na [API REST de Dimensionamento automático](https://msdn.microsoft.com/library/dn931928.aspx).


## Diagrama conceitual  
A Figura 2 mostra uma visão geral conceitual do dimensionamento automático, seguida por uma explicação das partes do diagrama.

![Adicionar alerta](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)

**Figura 2: Visão geral do processo de dimensionamento automático**

## Métricas de recurso 
Os recursos emitem métricas, que são processadas posteriormente por regras. As métricas são fornecidas por métodos diferentes. Conjuntos de Escala de VM usam dados de telemetria de agentes de diagnóstico do Azure, enquanto a telemetria para Aplicativos Web e Serviços de Nuvem vêm diretamente da Infraestrutura do Azure. Algumas estatísticas usadas normalmente incluem o Uso da CPU, utilização de memória, contagens de thread, comprimento da fila e uso do disco. Para obter uma lista de quais dados de telemetria você pode usar, confira [Métricas comuns de dimensionamento automático](insights-autoscale-common-metrics.md).

 
## Hora
Regras com base em agendamento têm base em UTC. Ao configurar as regras, você deve definir o fuso horário corretamente.

## Regras de dimensionamento automático
O diagrama mostra apenas uma regra de dimensionamento automático, mas você pode ter muitas deles. Você pode criar regras complexas de sobreposição, conforme o necessário para sua situação. Os tipos de regra incluem
 
 - **Com base em métrica** - Por exemplo, executar esta ação quando o uso da CPU estiver acima de 50%.
 - **Com base no tempo** - Por exemplo, disparar um webhook todas os sábados às 8h em um determinado fuso horário.

 
## Automação e ações de dimensionamento automático

As regras podem disparar um ou mais tipos de ações.

- **Escala** - Aumenta ou diminui as VMs
- **Email** - Envia um email para administradores e coadministradores de uma assinatura, e para o endereço de email adicional que você especificar
- **Automatizar via webhooks** - Chama webhooks, que podem disparar várias ações complexas dentro ou fora do Azure. Dentro do Azure, você pode iniciar um runbook de Automação do Azure, Função do Azure ou Aplicativo Lógico do Azure. Entre os exemplos de URL de terceiros fora do Azure estão serviços como o Slack e o Twilio.


## Detalhes das regras do Resource Manager

As regras de dimensionamento automático têm a seguinte estrutura em um modelo do Azure Resource Manager.

![Estrutura da regra de dimensionamento automático do Azure Resource Manager](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure2.png)

Você pode listar vários perfis. Cada perfil pode ter várias regras. Os métodos de notificação e locais são incluídos após seus perfis. Os exemplos de notificação incluem um webhook com o URI, ou emails com os endereços de email.

Para obter exemplos de código, consulte

* [Configuração avançada de Dimensionamento Automático usando modelos do Resource Manager para conjuntos de escala de VM](insights-advanced-autoscale-virtual-machine-scale-sets.md)
* [API de REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx)

## Métodos de acesso 
Você pode configurar as regras de dimensionamento automático usando

- Portal do Azure
- PowerShell
- Interface de Biblioteca Comum (CLI)
- API REST do Insights

## Explicações passo a passo sobre o dimensionamento automático

- [Dimensionamento de Serviços de Nuvem](../cloud-services/cloud-services-how-to-scale-portal.md)
- [Dimensionamento de Aplicativos Web](insights-how-to-scale.md)
- [Dimensionamento de conjuntos de disponibilidade da máquina virtual clássica](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/)
- [Dimensionamento de Conjuntos de Escala de VM no Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)
- [Dimensionamento de Conjuntos de Escala de VM no Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md)
- [Configuração avançada de Dimensionamento Automático usando modelos do Resource Manager para conjuntos de escala de VM](insights-advanced-autoscale-virtual-machine-scale-sets.md)

## Próximas etapas

Para saber mais sobre o dimensionamento automático, use as Explicações passo a passo sobre o dimensionamento automático listadas anteriormente ou consulte os recursos a seguir:

- [Métricas comuns de dimensionamento automático do Azure Insights](insights-autoscale-common-metrics.md)
- [Práticas recomendadas para dimensionamento automático do Azure Insights](insights-autoscale-best-practices.md)
- [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook](insights-autoscale-to-webhook-email.md)
- [API REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx) - Consulte toda a API e os significados de cada um dos campos e valores
- [Solução de problemas do dimensionamento automático de Conjuntos de Escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

<!---HONumber=AcomDC_0907_2016-->