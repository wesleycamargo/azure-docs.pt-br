---
title: "Perfis aninhados do Gerenciador de Tráfego | Microsoft Docs"
description: "Este artigo explica o recurso “Perfis Aninhados” do Gerenciador de Tráfego do Azure"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 3e48a28aa1ecda6792e79646a33875c8f01a878f
ms.openlocfilehash: fdf22a3f8d0ba6f1838af4f5e6924c8c0a18ef64

---

# <a name="nested-traffic-manager-profiles"></a>Perfis aninhados do Gerenciador de Tráfego

O Gerenciador de Tráfego inclui uma variedade de métodos de roteamento de tráfego que permitem controlar como o Gerenciador de Tráfego escolhe qual ponto de extremidade deve receber o tráfego de cada usuário final. Para obter mais informações, consulte [Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).

Cada perfil do Gerenciador de Tráfego especifica um único método de roteamento de tráfego. No entanto, há cenários que exigem um roteamento de tráfego mais sofisticado do que o roteamento fornecido por um único perfil do Gerenciador de Tráfego. Aninhe perfis do Gerenciador de Tráfego para combinar os benefícios de mais de um método de roteamento de tráfego. Os perfis aninhados permitem que você substitua o comportamento padrão do Gerenciador de Tráfego para dar suporte a implantações de aplicativo maiores e mais complexas.

Os exemplos a seguir ilustram como usar os perfis aninhados do Gerenciador de Tráfego em vários cenários.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemplo 1: combinando roteamento de tráfego de “Desempenho” e “Ponderado”

Suponha que você implantou um aplicativo nas seguintes regiões do Azure: Oeste dos EUA, Europa Ocidental e Ásia Oriental. Você usa o método de roteamento de tráfego por “Desempenho” do Gerenciador de Tráfego para distribuir o tráfego para a região mais próxima do usuário.

![Perfil único do Gerenciador de Tráfego][1]

Agora, suponha que você deseja testar uma atualização ao serviço antes de distribuí-la mais amplamente. Você deseja usar o método de roteamento de tráfego “ponderado” para direcionar um pequeno percentual do tráfego para a implantação de teste. Você configura a implantação de teste junto com a implantação de produção existente na Europa Ocidental.

Não é possível combinar roteamentos de tráfego “Ponderado” e por “Desempenho” em um único perfil. Para dar suporte a esse cenário, você cria um perfil do Gerenciador de Tráfego usando os dois pontos de extremidade da Europa Ocidental e o método de roteamento de tráfego “Ponderado”. Em seguida, você adiciona esse perfil “filho” como um ponto de extremidade ao perfil “pai”. O perfil pai ainda usa o método de roteamento de tráfego por Desempenho e contém as outras implantações globais como pontos de extremidade.

O diagrama a seguir ilustra esse exemplo:

![Perfis aninhados do Gerenciador de Tráfego][2]

Nessa configuração, o tráfego direcionado por meio do perfil pai distribui o tráfego entre regiões normalmente. Na Europa Ocidental, o perfil aninhado distribui o tráfego para os pontos de extremidade de produção e de teste de acordo com os pesos atribuídos.

Quando o perfil pai usa o método de roteamento de tráfego por “Desempenho”, uma localização deve ser atribuída a cada ponto de extremidade. A localização é atribuída quando você configura o ponto de extremidade. Escolha a região do Azure mais próxima de sua implantação. As regiões do Azure são os valores de localização com suporte na Tabela de Latência da Internet. Para obter mais informações, consulte [Método de roteamento de tráfego por “Desempenho” do Gerenciador de Tráfego](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemplo 2: monitoramento de ponto de extremidade em Perfis Aninhados

O Gerenciador de Tráfego monitora ativamente a integridade de cada ponto de extremidade de serviço. Se um ponto de extremidade não estiver íntegro, o Gerenciador de Tráfego direcionará os usuários para pontos de extremidade alternativos a fim de preservar a disponibilidade do serviço. Esse comportamento de failover e monitoramento do ponto de extremidade se aplica a todos os métodos de roteamento de tráfego. Para obter mais informações, consulte [Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md). O monitoramento de ponto de extremidade funciona de forma diferente para perfis aninhados. Com perfis aninhados, o perfil pai não executa verificações de integridade no filho diretamente. Em vez disso, a integridade dos pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho. Essas informações de integridade são propagadas até a hierarquia do perfil aninhado. O perfil pai usa essa integridade agregada para determinar se o tráfego será direcionado ao perfil filho. Consulte a seção [Perguntas frequentes](#faq) deste artigo para obter detalhes completos sobre o monitoramento de integridade dos perfis aninhados.

Voltando ao exemplo anterior, suponha que a implantação de produção na Europa Ocidental falhe. Por padrão, o perfil “filho” direciona todo o tráfego para a implantação de teste. Se a implantação de teste também falhar, o perfil pai determinará que o perfil filho não deve receber o tráfego, pois nenhum ponto de extremidade filho está íntegro. Em seguida, o perfil pai distribui o tráfego para as outras regiões.

![Failover de perfil aninhado (comportamento padrão)][3]

Talvez você esteja satisfeito com essa organização. Ou você pode estar preocupado porque todo o tráfego para Europa Ocidental agora vai para a implantação de teste, em vez de um tráfego de subconjunto limitado. Independentemente da integridade da implantação de teste, você desejará executar failover para as outras regiões quando a implantação de produção na Europa Ocidental falhar. Para habilitar esse failover, é possível especificar o parâmetro “MinChildEndpoints” ao configurar o perfil filho como um ponto de extremidade no perfil pai. O parâmetro determina o número mínimo de pontos de extremidade disponíveis no perfil filho. O valor padrão é '1'. Para este cenário, você pode definir o valor de MinChildEndpoints como 2. Abaixo desse limite, o perfil pai considera o perfil filho inteiro como indisponível e direciona o tráfego para os outros pontos de extremidade.

A seguinte figura ilustra essa configuração:

![Failover de Perfil Aninhado com “MinChildEndpoints” = 2][4]

> [!NOTE]
> O método de roteamento de tráfego por “Prioridade” distribui todo o tráfego para um único ponto de extremidade. Portanto, não há muita utilidade em uma configuração de MinChildEndpoints diferente de “1” para um perfil filho.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemplo 3: regiões de failover priorizadas no roteamento de tráfego de "Desempenho"

O comportamento padrão do método de roteamento de tráfego por “Desempenho” é projetado para evitar o carregamento excessivo do ponto de extremidade mais próximo, causando a propagação de uma série de falhas. Quando um ponto de extremidade falha, todo o tráfego que seria direcionado ao ponto de extremidade é distribuído de maneira uniforme para os outros pontos de extremidade em todas as regiões.

![Roteamento de tráfego por “desempenho” com failover padrão][5]

No entanto, suponha que você prefere o failover de tráfego da Europa Ocidental para o Oeste dos EUA e direciona o tráfego para outras regiões somente quando os pontos de extremidade não estão disponíveis. Você pode criar essa solução usando um perfil filho com o método de roteamento de tráfego por “Prioridade”.

![Tráfego de roteamento de “Desempenho” com failover preferencial][6]

Como o ponto de extremidade da Europa Ocidental tem prioridade maior que o ponto de extremidade do Oeste dos EUA, todo o tráfego é enviado para o ponto de extremidade da Europa Ocidental quando os dois pontos de extremidade estão online. Se a Europa Ocidental falhar, seu tráfego será direcionado para o Oeste dos EUA. Com o perfil aninhado, o tráfego é direcionado para a Ásia Oriental somente quando há uma falha na Europa Ocidental e no Oeste dos EUA.

É possível repetir esse padrão para todas as regiões. Substitua os três pontos de extremidade no perfil pai por três perfis filho, cada um deles fornecendo uma sequência de failover priorizada.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemplo 4: controlando o roteamento de tráfego de “Desempenho” entre vários pontos de extremidade na mesma região

Suponha que o método de roteamento de tráfego por “Desempenho” é usado em um perfil que tem mais de um ponto de extremidade em determinada região. Por padrão, o tráfego direcionado para essa região é distribuído uniformemente entre todos os pontos de extremidade disponíveis nessa região.

![Roteamento de tráfego por “desempenho” com distribuição de tráfego na região (comportamento padrão)][7]

Em vez de adicionar vários pontos de extremidade na Europa Ocidental, esses pontos de extremidade são fechados em um perfil filho separado. O perfil filho é adicionado ao pai como o único ponto de extremidade da Europa Ocidental. As configurações no perfil filho podem controlar a distribuição de tráfego com a Europa Ocidental, habilitando o roteamento de tráfego ponderado ou baseado em prioridade nessa região.

![Roteamento de tráfego por “desempenho” com distribuição de tráfego na região personalizado][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemplo 5: configurações de monitoramento por ponto de extremidade

Suponha que você está usando o Gerenciador de Tráfego para migrar diretamente o tráfego de um site local herdado para uma nova versão baseada em Nuvem hospedada no Azure. Para o site herdado, você deseja usar o URI da home page para monitorar a integridade do site. Mas para a nova versão baseada em Nuvem, você está implementando uma página personalizada de monitoramento (caminho “/monitor.aspx”) que inclui verificações adicionais.

![Monitoramento do ponto de extremidade do Gerenciador de Tráfego (comportamento padrão)][9]

As configurações de monitoramento em um perfil do Gerenciador de Tráfego se aplicam a todos os pontos de extremidade em um único perfil. Com perfis aninhados, você usa um perfil filho diferente por site para definir diferentes configurações de monitoramento.

![Monitoramento do ponto de extremidade do Gerenciador de Tráfego com definições por ponto de extremidade][10]

## <a name="faq"></a>Perguntas frequentes

### <a name="how-do-i-configure-nested-profiles"></a>Como posso configurar perfis aninhados?

Os perfis aninhados do Gerenciador de Tráfego podem ser configurados usando o Azure Resource Manager e as APIs REST clássicas do Azure, cmdlets do Azure PowerShell e comandos da CLI do Azure de plataforma cruzada. Também há suporte para eles no novo portal do Azure. Não há suporte para eles no portal clássico.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>A quantas camadas de aninhamento o Gerenciador de Tráfego dá suporte?

Você pode aninhar perfis em até 10 níveis de profundidade. “Loops” não são permitidos.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Posso combinar outros tipos de ponto de extremidade com perfis filho aninhados no mesmo perfil do Gerenciador de Tráfego?

Sim. Não há nenhuma restrição sobre como combinar os pontos de extremidade de diferentes tipos em um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como o modelo de cobrança se aplica a perfis aninhados?

Há não impacto negativo sobre os preços ao usar perfis aninhados.

A cobrança do Gerenciador de Tráfego tem dois componentes: verificações de integridade do ponto de extremidade e milhões de consultas DNS

* Verificações de integridade de ponto de extremidade: não há nenhum encargo para um perfil filho quando configurado como um ponto de extremidade em um perfil pai. O monitoramento dos pontos de extremidade no perfil filho é cobrado como de costume.
* Consultas DNS: cada consulta é contada apenas uma vez. Uma consulta em um perfil pai que retorna um ponto de extremidade de um perfil filho é contada apenas no perfil pai.

Para obter detalhes completos, consulte a [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Há impacto no desempenho para perfis aninhados?

Nº Não há nenhum impacto no desempenho ao usar perfis aninhados.

Os servidores de nomes do Gerenciador de Tráfego atravessam a hierarquia de perfil internamente durante o processamento de cada consulta DNS. Uma consulta DNS a um perfil pai pode receber uma resposta DNS com um ponto de extremidade de um perfil filho. Um único registro CNAME é usado se você está usando um único perfil ou perfis aninhados. Não é necessário criar um registro CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como o Gerenciador de Tráfego calcula a integridade de um ponto de extremidade aninhado em um perfil pai?

O perfil pai não executa verificações de integridade no filho diretamente. Em vez disso, a integridade dos pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho. Essas informações são propagadas até a hierarquia do perfil aninhado para determinar a integridade do ponto de extremidade aninhado. O perfil pai usa essa integridade agregada para determinar se o tráfego pode ser direcionado para o filho.

A tabela a seguir descreve o comportamento das verificações de integridade do Gerenciador de Tráfego de um ponto de extremidade aninhado.

| Status do Monitor de perfil filho | Status do monitor de ponto de extremidade pai | Observações |
| --- | --- | --- |
| Desabilitado. O perfil filho foi desabilitado. |Parada |O estado do ponto de extremidade pai é Parado, não Desabilitado. O estado Desabilitado é reservado para indicar que você desabilitou o ponto de extremidade no perfil pai. |
| Degradado. Pelo menos um ponto de extremidade do perfil filho está no estado Degradado. |Online: o número de pontos de extremidade Online no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos de extremidade Online mais CheckingEndpoint no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>Degradado: caso contrário. |O tráfego é roteado para um ponto de extremidade do status CheckingEndpoint. Se MinChildEndpoints estiver definido com um valor muito alto, o ponto de extremidade estará sempre degradado. |
| Online. Pelo menos, um ponto de extremidade do perfil filho está em um estado Online. Nenhum ponto de extremidade está no estado Degradado. |Veja acima. | |
| CheckingEndpoints. Pelo menos, um ponto de extremidade do perfil filho é um 'CheckingEndpoint'. Nenhum ponto de extremidade está ‘Online’ ou ‘Degradado’ |Mesmo que acima. | |
| Inativo. Todos os pontos de extremidade de perfil filho estão com status Desabilitado ou Parado ou esse é um perfil que não tem nenhum ponto de extremidade. |Parada | |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como o Gerenciador de Tráfego funciona](traffic-manager-how-traffic-manager-works.md)

Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png



<!--HONumber=Jan17_HO1-->


