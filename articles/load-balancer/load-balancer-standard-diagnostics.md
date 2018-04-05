---
title: Azure Load Balancer Standard - Diagnósticos | Microsoft Docs
description: Usar as métricas e informações de integridade disponíveis para diagnóstico para o Azure Load Balancer Standard
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 1d39cdc13e69740dc99e67f935b60db218536044
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Diagnóstico de métricas e integridade para o Load Balancer Standard

O Azure Load Balancer Standard fornece os seguintes recursos de diagnósticos para os seus recursos:
* Métricas multidimensionais: o Load Balancer Standard fornece novos recursos de diagnóstico multidimensionais para configurações públicas e internas do Load Balancer. Isso permite monitorar, gerenciar e solucionar problemas de seus recursos do Load Balancer.

* Integridade de recursos: a página do Load Balancer no Portal do Azure e a página de Integridade de Recursos (em Monitor) expõem a Integridade de Recursos para a configuração do Balanceador de Carga público do Load Balancer Standard.

Este artigo fornece um tour rápido por esses recursos e como usá-los para o Load Balancer Standard.

## <a name = "MultiDimensionalMetrics"></a>Métricas multidimensionais

O Azure Load Balancer fornece as novas métricas multidimensionais por meio de novas métricas do Azure (visualização) no portal e ajuda você a obter informações de diagnósticas em tempo real sobre os recursos do balanceador de carga. 

As métricas a seguir estão atualmente disponíveis para diferentes configurações de Load Balancer (LB) Standard:

| Métrica | Tipo de recurso | DESCRIÇÃO | Agregação recomendada |
| --- | --- | --- | --- |
| Disponibilidade de VIP (disponibilidade de caminho de dados) | LB público | O Load Balancer Standard usa continuamente o caminho de dados de dentro de uma região para o front-end do Load Balancer e até a pilha do SDN compatível com a sua VM. Contanto que instâncias íntegras permaneçam, a medição seguirá o mesmo caminho que o tráfego com balanceamento de carga do seu aplicativo. O caminho de dados usado por seus clientes também é validado. A medição é invisível para seu aplicativo e não interfere com outras operações.| Média |
| Disponibilidade de DIP (status de investigação de integridade) |  LB público e interno | O Load Balancer Standard usa um serviço de investigação de integridade distribuído que monitora a integridade do ponto de extremidade do aplicativo de acordo com as definições de configuração. Essa métrica fornece uma exibição agregada ou por ponto de extremidade filtrado de cada ponto de extremidade de instância individual no pool do Load Balancer.  Você pode verificar como o Load Balancer vê a integridade de seu aplicativo conforme indicado pela configuração de sua investigação de integridade. |  Média |
| Pacotes SYN |  LB público | O Load Balancer Standard não encerra conexões TCP nem interage com os fluxos de pacotes TCP ou UDP. Fluxos e seus handshakes estão sempre entre a origem e a instância VM. Para solucionar melhor os problemas dos cenários de protocolo TCP, é possível usar contadores de pacotes SYN para entender quantas tentativas de conexão TCP são feitas. A métrica informa o número de pacotes SYN do TCP que foram recebidos.| Média |
| Conexões SNAT |  LB público |O Load Balancer Standard relata o número de fluxos de saída mascarados para o front-end do endereço IP Público. As portas SNAT são um recurso esgotável. Essa métrica pode dar uma indicação do grau de dependência que seu aplicativo tem do SNAT para fluxos com origem externa.  Contadores para fluxos SNAT de saída bem-sucedidos e com falha são relatados e podem ser usados para solucionar problemas e entender a integridade dos fluxos de saída.| Média |
| Contadores de bytes |  LB público e interno | O Load Balancer Standard informa os dados processados por front-end.| Média |
| Contadores de pacotes |  LB público e interno | O Load Balancer Standard relata os dados processados por front-end.| Média |

### <a name="view-load-balancer-metrics-in-the-portal"></a>Exibir métricas do Load Balancer no portal

O Portal do Azure expõe as métricas do Load Balancer por meio da página Métricas (Visualização), que está disponível na página Recursos do Load Balancer para um recurso específico do Load Balancer e também na página do Azure Monitor. 

Para exibir as métricas para os recursos do Load Balancer Standard, procure pelas Métricas (visualização) em um desses locais, selecione o tipo de Métrica (recurso do balanceador de métrica, se na página Monitor) na lista suspensa, defina o tipo de Agregação apropriado e, opcionalmente, configure a filtragem e o agrupamento necessários e você poderá ver a exibição histórica para as métricas sob as condições fornecidas.

![Exibição de métricas para o Load Balancer Standard](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Figura - Disponibilidade DIP/Métrica de status de investigação de integridade do Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperar as métricas multidimensionais programaticamente por meio de APIs

Orientações de API para recuperar as definições de métrica multidimensional e os valores estão disponíveis em [Monitorar acompanhamento de API REST > API multidimensional](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)

### <a name = "DiagnosticScenarios"></a>Cenários comuns de diagnósticos e modos de exibição recomendados

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>O caminho de dados está disponível e funcionando para o VIP do Load Balancer?

A métrica de disponibilidade de VIP descreve a integridade do caminho de dados dentro da região para o host de computação onde se encontram suas VMs. É uma integridade de reflexão da infraestrutura do Azure. Você pode usar essa métrica para:
- Monitorar a disponibilidade externa do seu serviço
- Aprofundar-se e entender se a plataforma na qual o serviço é implantado está íntegra ou se o sistema operacional convidado ou a instância do aplicativo estão íntegros
- Isolar se um evento está relacionado ao seu serviço ou o plano de dados subjacente. Não confunda isso com o status de investigação de integridade ("disponibilidade DIP").

Para obter a disponibilidade de VIP para os recursos do Load Balancer Standard:
- Verifique se o recurso do Load Balancer correto está selecionado. 
- Selecione **Disponibilidade de VIP** da lista suspensa **Métrica**. 
- Selecione **Méd** para **Agregação**. 
- Além disso, adicione filtro sobre o endereço VIP ou a porta VIP como a dimensão com o endereço IP de front-end ou a porta de front-end necessários, e o grupo pela dimensão selecionada.

![Investigação do VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura - Detalhes de investigação do VIP do Load Balancer*

A métrica é gerada por uma medida de ativa e dentro da faixa. Um serviço de investigação dentro da região origina o tráfego para essa medida e é ativado assim que você cria uma implantação com um front-end público e continua até que você remova o front-end. 

>[!NOTE]
>Não há suporte para front-ends internos no momento. 

Um pacote que corresponde ao front-end de sua implantação e uma regra são gerados periodicamente. Eles atravessam a região da origem até o host onde se encontra uma VM no pool de back-end. A infraestrutura do Load Balancer executará as mesmas operações de balanceamento de carga e translação, assim como faz para todos os outros tráfegos. Essa investigação é dentro da banda no seu ponto de extremidade com carga balanceada. Depois que a investigação chega no host de computação onde se encontra uma VM íntegra no pool de back-end, o host de computação irá gerar uma resposta para o serviço de investigação. A VM não vê esse tráfego.
A disponibilidade de VIP falha pelos seguintes motivos:
- Sua implantação não tem nenhuma VM íntegra restante no pool de back-end. 
- Ocorreu uma interrupção de infraestrutura que faz com que a disponibilidade de VIP falhe.

Você pode usar a [métrica de disponibilidade de VIP junto com o status de investigação de integridade para fins de diagnóstico](https://aka.ms/lbdiagnostics#vipavailabilityandhealthprobes).

Use **Média** como a agregação para a maioria dos cenários.

#### <a name="are-the-backend-instances-for-my-vip-responding-to-probes"></a>As instâncias de back-end para o VIP estão respondendo às investigações?

A métrica de status de investigação de integridade descreve a integridade da implantação do aplicativo, conforme configurado por você ao configurar a investigação de integridade do Load Balancer. O Load Balancer usa o status da investigação de integridade para determinar para onde enviar novos fluxos. As investigações de integridade se originam de um endereço de infraestrutura do Azure e são visíveis no sistema operacional convidado da VM.

Para obter a disponibilidade de DIP para os recursos do Load Balancer Standard,
- Selecione a Métrica **Disponibilidade de DIP** com tipo de agregação **Méd**. 
- Aplique um filtro no endereço IP ou porta (ou ambos) de VIP necessário.

![Disponibilidade do DIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Figura - Disponibilidade de VIP para Load Balancer*

As investigações de integridade falham pelos seguintes motivos:
- Se você configurar uma investigação de integridade para uma porta que não escutando ou não está respondendo ou com o protocolo incorreto. Se o serviço estiver usando regras DSR (IP flutuante), você precisa certificar-se de que o serviço está escutando no endereço IP da configuração de IP da NIC e não apenas no loopback configurado com o endereço IP de front-end.
- Se o teste não é permitido pelo Grupo de Segurança de Rede, o firewall do sistema operacional convidado da VM ou os filtros da camada do aplicativo.

Use **Média** como a agregação para a maioria dos cenários.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Como verificar as minhas estatísticas de conexão de saída? 

A métrica de conexões SNAT descreve o volume de êxito e falha (para [fluxos de saída](https://aka.ms/lboutbound)).

Um volume de conexões com falha maior que zero indica o esgotamento de porta SNAT. Você deve investigar mais e determinar o que pode estar causando essas falhas. Manifestos de esgotamento de porta SNAT como falhas para estabelecer um [fluxos de saída](https://aka.ms/lboutbound). Revise o artigo sobre conexões de saída para entender os cenários, mecanismos no trabalho e como reduzir/projetar para evitar o esgotamento da porta SNAT. 

Para obter estatísticas de conexão SNAT,
- Selecione o tipo de métrica **Conexões SNAT** e **Soma** como agregação. 
- Agrupe por **Estado de Conexão** para contagens de Conexão SNAT bem-sucedidas e com falha conta representadas por linhas diferentes. 

![Conexão SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura - Contagem de conexão SNAT para o Load Balancer*


#### <a name="how-do-i-check-inbound--outbound-connection-attempts-for-my-service"></a>Como verificar as tentativas de conexão de entrada/saída para meu serviço?

A métrica de pacotes SYN descreve o volume de pacotes TCP SYN, que foram recebidos ou enviados (para [fluxos de saída](https://aka.ms/lboutbound)), associado a um determinado front-end. Essa métrica pode ser usada para entender as tentativas de conexão TCP com seu serviço.
Use Total como a agregação para a maioria dos cenários.

![Conexão SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura - Contagem de SYN para o Load Balancer*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Como verificar o consumo de largura de banda da rede? 

A métrica Contadores de Bytes/Pacotes descreve o volume de bytes e pacotes enviados ou recebidos pelo seu serviço em uma base por front-end.
Use **Total** como a agregação para a maioria dos cenários.

Para obter estatísticas de contagem de Bytes ou Pacotes
- Selecione o tipo de métrica **Contagem de Bytes** e/ou **Contagem de Pacotes** com **Méd** como a agregação. 
- Aplique um filtro em um IP de front-end específico, porta de front-end ou IP de back-end ou à porta em questão. 
- ou obtenha estatísticas gerais de recursos do Load Balancer sem nenhuma filtragem.


Alguns modos de exibição de exemplo para métricas com configurações diferentes -

![Contagem de Bytes](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura - Contagem de Bytes para o Load Balancer*

#### <a name = "vipavailabilityandhealthprobes"></a>Como posso diagnosticar minha implantação de Load Balancer?

A combinação das métricas de disponibilidade de VIP & investigações de integridade em um único gráfico pode permitir que você identifique onde procurar o problema e resolvê-lo. Você pode obter a garantia de que o Azure está funcionando corretamente e usar isso para determinar conclusivamente a configuração ou o aplicativo que é a causa raiz.

Você pode usar métricas de investigação de integridade para entender como o Azure exibe a integridade de sua implantação de acordo com a configuração que você forneceu. Olhar para investigações de integridade é sempre uma excelente primeira etapa no monitoramento ou determinação de uma causa.

Você pode executar uma etapa adicional e usar as métricas de disponibilidade de VIP para obter insights sobre como o Azure exibe a integridade do plano de dados subjacente responsável por sua implantação específica. Ao combinar as duas métricas, você pode isolar onde a falha pode estar conforme ilustrado neste exemplo:

![Diagnóstico de VIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Figura - Combinando métricas de disponibilidade de DIP e VIP*

O gráfico mostra as seguintes informações:
- A própria infraestrutura estava íntegra, a infraestrutura que hospeda suas VMs foi alcançada e mais de uma VM foi colocada no back-end. Isso é indicado pelo traço azul para disponibilidade de VIP, que é mostrado a 100%. 
- No entanto, o status de investigação de integridade (disponibilidade de DIP) é de 0% no início do gráfico, conforme indicado pelo traço laranja. A área circulada em vermelho destaca onde as investigações de integridade (disponibilidade de DIP) se tornaram íntegras, e em que ponto a implantação do cliente foi capaz de aceitar novos fluxos.

O gráfico permitiu que cliente solucionasse problemas da implantação por conta própria, sem a necessidade de adivinhar ou perguntar ao suporte se havia outros problemas ocorrendo. O serviço do cliente não estava disponível porque as investigações de integridade estavam falhando devido a um erro de configuração ou a um aplicativo com falha.

### <a name = "Limitations"></a>Limitações

- A disponibilidade de VIP está atualmente disponível somente para front-ends públicos.

## <a name = "ResourceHealth"></a>Status de integridade de recurso

O status de integridade para os recursos do Load Balancer Standard é exposto por meio do **Recursos de integridade** existente em **Monitor > Integridade do Serviço**.

>[!NOTE]
>A integridade de recursos para o Load Balancer está disponível atualmente somente para a configuração pública do Load Balancer Standard. Recursos internos do Load Balancer ou recursos de SKU básico do Load Balancer não expõem a integridade de recursos.

Para exibir a integridade de seus recursos de Load Balancer Standard público,
 - Navegue até **Monitor > Integridade do Serviço**.

  ![Página do Monitor](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura - Integridade do serviço no Azure Monitor*

 - Selecione **Integridade de recursos** e verifique se a **ID da assinatura** e **Tipo de recurso = Load Balancer** corretos estão selecionados.

  ![Status de integridade de recurso](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura - Selecione o recurso para o modo de exibição de integridade*

 - Clique no recurso de Load Balancer da lista para exibir seu status de integridade histórica.

    ![Status de integridade do Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura - Modo de exibição de integridade de recursos do Load Balancer*
 
A tabela a seguir lista o status de integridade vários de vários recursos e suas descrições. 

| Status de Integridade de Recurso | DESCRIÇÃO |
| --- | --- |
| Disponível | O recurso de Load Balancer Standard público está íntegro e disponível |
| Indisponível | O recurso de Load Balancer Standard público não está íntegro. Diagnostique a integridade por meio do Azure Monitor > Métricas. (*Isso também pode significar que o Recurso não é Load Balancer Standard público*) |
| Desconhecido | A integridade de recursos para o Load Balancer Standard público ainda não foi atualizada. (*Isso também pode significar que o Recurso não é Load Balancer Standard público*)  |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md)
- Saiba mais sobre a [conectividade de saída do Load Balancer](https://aka.ms/lboutbound)


