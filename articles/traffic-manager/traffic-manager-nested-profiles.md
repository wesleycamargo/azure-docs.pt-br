---
title: Perfil Aninhados do Gerenciador de Tráfego | Microsoft Docs
description: Este artigo explica o recurso “Perfis Aninhados” do Gerenciador de Tráfego do Azure
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/25/2016
ms.author: sewhee

---
# Perfis aninhados do Gerenciador de Tráfego
O Gerenciador de Tráfego inclui uma variedade de métodos de roteamento de tráfego, permitindo que você controle como ele escolhe qual ponto de extremidade deve receber tráfego de cada usuário final. Eles estão descritos nos [Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md) e permitem que este atenda aos requisitos mais comuns de roteamento de tráfego.

Cada perfil do Gerenciador de Tráfego especifica um único método de roteamento de tráfego. No entanto, há ocasiões em que os aplicativos mais complexos requerem roteamento de tráfego mais sofisticado do que pode ser fornecido por um único perfil do Gerenciador de Tráfego.

Para dar suporte a esses aplicativos complexos, o Gerenciador de Tráfego permite que perfis do Gerenciador de Tráfego sejam combinados, ou *aninhados*, permitindo que os benefícios de mais de um método de roteamento de tráfego seja usado por um único aplicativo. Perfis aninhados permitem criar esquemas de roteamento de tráfego mais flexíveis e poderosos para dar suporte às necessidades de implantações maiores e mais complexas.

Além disso, perfis aninhados permitem substituir o comportamento padrão do Gerenciador de Tráfego em determinados casos, como no roteamento de tráfego dentro de uma região ou durante o failover ao usar o roteamento de tráfego de Desempenho.

O restante desta página explica, por meio de uma sequência de exemplos, como perfis aninhados do Gerenciador de Tráfego podem ser usados em uma variedade de cenários. Concluímos com algumas perguntas frequentes sobre perfis aninhados

## Exemplo 1: combinando roteamento de tráfego de “Desempenho” e “Ponderado”
Suponha que seu aplicativo está implantado em várias regiões do Azure: Oeste dos EUA, Europa Ocidental e Ásia Oriental. Você usa o método de roteamento de tráfego de “Desempenho” do Gerenciador de Tráfego para distribuir o tráfego para a região mais próxima do usuário.

![Perfil único do Gerenciador de Tráfego][1]

Agora, suponha que você deseja avaliar uma atualização do serviço com alguns poucos usuários antes de distribuí-lo de forma mais abrangente. Para tal, é recomendável usar o método roteamento de tráfego "ponderado", que pode direcionar um pequeno percentual do tráfego para a implantação de avaliação. Com um único perfil, não é possível combinar roteamentos de tráfego “Ponderado” e de “Desempenho”. Com os perfis aninhados, você pode fazer ambos.

Desta forma: suponha que você queira avaliar a nova implantação na Europa Ocidental. Você configura a implantação de avaliação junto com a implantação de produção existente e cria um perfil do Gerenciador de Tráfego usando apenas esses dois pontos de extremidade e o método de roteamento de tráfego "Ponderado". Em seguida, você adiciona esse perfil “filho” como um ponto de extremidade para o perfil “pai”, que ainda usa o método de roteamento de tráfego de Desempenho e também contém as outras implantações globais como pontos de extremidade.

O diagrama a seguir ilustra esse exemplo:

![Perfis aninhados do Gerenciador de Tráfego][2]

Com essa organização, o tráfego direcionado por meio do perfil pai será distribuído entre regiões como normal. Na Europa Ocidental, o tráfego será direcionado entre as implantações de produção e avaliação de acordo com os pesos atribuídos.

Observe que, quando o perfil pai usa o método de roteamento de tráfego de "Desempenho", é necessário conhecer a localização de cada ponto de extremidade. Para pontos de extremidade aninhados, como pontos de extremidade externos, essa localização deve ser especificada como parte da configuração do ponto de extremidade. Escolha a região do Azure mais próxima da sua implantação; as opções disponíveis são as regiões do Azure, pois esses são os locais com suporte na Tabela de Latência de Internet. Para obter mais detalhes, consulte [Método de roteamento de tráfego de “Desempenho” do Gerenciador de Tráfego](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## Exemplo 2: monitoramento de ponto de extremidade em Perfis Aninhados
O Gerenciador de Tráfego monitora ativamente a integridade de cada ponto de extremidade de serviço. Se um ponto de extremidade for considerado não íntegro, o Gerenciador de Tráfego direcionará os usuários para pontos de extremidade alternativos, preservando a disponibilidade geral do seu serviço. Esse comportamento de failover e monitoramento do ponto de extremidade se aplica a todos os métodos de roteamento de tráfego. Consulte [Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md) para obter mais detalhes.

Para perfis aninhados, aplicam-se algumas regras especiais de monitoramento de ponto de extremidade. Quando um perfil pai estiver configurado com um perfil filho como um ponto de extremidade aninhado, o pai não realiza verificações de integridade no filho diretamente. Em vez disso, a integridade de pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho, e essas informações são propagadas para a hierarquia do perfil aninhado para determinar a integridade do ponto de extremidade aninhado dentro do perfil pai. Isso determina se o perfil pai direcionará o tráfego para o filho. Os detalhes completos da maneira exata de como a integridade do ponto de extremidade aninhado no perfil pai é calculada por meio da integridade do perfil filho são mostrados [abaixo](#faq).

Retornando ao Exemplo 1, suponha que a implantação de produção na Europa Ocidental falhe. Por padrão, o perfil “filho” vai direcionar todo o tráfego para a implantação de avaliação. Se isso também falhar, o perfil pai determinará que, como nenhum ponto de extremidade filho está íntegro, o perfil filho não deverá receber tráfego e ocorrerá o failover todo o tráfego da Europa Ocidental para outras regiões.

![Failover de perfil aninhado (comportamento padrão)][3]

Você pode ficar satisfeito com essa medida ou preocupado, pois a implantação de Avaliação não deveria ser usada como um failover para todo o tráfego da Europa Ocidental, preferindo o failover para as outras regiões caso a implantação de produção na Europa Ocidental falhe, *independentemente* da integridade da implantação de avaliação. Ao configurar o perfil filho como um ponto de extremidade no perfil pai, também é possível especificar o parâmetro “MinChildEndpoints”, que determina o número mínimo de pontos de extremidade devem estar disponíveis no perfil filho. Abaixo desse limite (cujo padrão é 1), o perfil pai considerará todo o perfil filho como indisponível e direcionará o tráfego para os outros pontos de extremidade do perfil pai.

O exemplo a seguir ilustra: com MinChildEndpoints definido como 2, se alguma das implantações da Europa Ocidental falhar, o perfil pai determinará que o perfil filho não deverá receber tráfego e os usuários serão direcionados para as outras regiões.

![Failover de perfil com “MinChildEndpoints” = 2][4]

Observe que, quando o perfil filho usa o método de roteamento de tráfego de “Prioridade”, todo o tráfego enviado para esse filho é recebido por um único ponto de extremidade. Portanto, não há muita vantagem em definir MinChildEndpoints como algo diferente de “1” neste caso.

## Exemplo 3: regiões de failover priorizadas no roteamento de tráfego de "Desempenho"
Com um único perfil usando o roteamento de tráfego de "Desempenho", se um ponto de extremidade (por exemplo, da Europa Ocidental) falhar, todo o tráfego que seria direcionado para este ponto de extremidade será distribuído entre os outros pontos de extremidade em todas as regiões. Esse é o comportamento padrão para o método de roteamento de tráfego de “Desempenho”, projetado para evitar o carregamento excessivo do ponto de extremidade mais próximo, causando a propagação de uma série de falhas.

![Tráfego de roteamento de “Desempenho” com failover padrão][5]

No entanto, suponha que você prefira que o failover do tráfego da Europa Ocidental vá para o Oeste dos EUA preferencialmente, sendo direcionado para outro local apenas se ambos os pontos de extremidade estiverem indisponíveis. É possível fazer isso criando um perfil filho que usa o método de roteamento de tráfego de “Prioridade”, conforme mostrado:

![Tráfego de roteamento de “Desempenho” com failover preferencial][6]

Como o ponto de extremidade da Europa Ocidental tem prioridade maior que o ponto de extremidade do Oeste dos EUA, todo o tráfego será enviado para o ponto de extremidade da Europa Ocidental quando ambos estiverem online. Se a Europa Ocidental falhar, seu tráfego será direcionado para o Oeste dos EUA. O tráfego da Europa Ocidental seria direcionado para a Ásia Oriental somente se o Oeste dos EUA também falhasse.

Você pode repetir esse padrão para todas as regiões, substituindo todos os três pontos de extremidade no perfil pai por três perfis filho, cada um deles oferecendo uma sequência de failover priorizada.

## Exemplo 4: controlando o roteamento de tráfego de “Desempenho” entre vários pontos de extremidade na mesma região
Suponha que o método de roteamento de tráfego de “Desempenho” seja usado em um perfil com mais de um ponto de extremidade em uma determinada região, como o Oeste dos EUA. Por padrão, o tráfego direcionado para essa região será distribuído uniformemente entre todos os pontos de extremidade disponíveis nessa região.

![Tráfego de roteamento de “Desempenho” com distribuição de tráfego na região (comportamento padrão)][7]

Esse padrão pode ser alterado usando perfis aninhados do Gerenciador de Tráfego. Em vez de adicionar vários pontos de extremidade ao Oeste dos EUA, esses pontos de extremidade podem ser colocados em um perfil filho separado, e o perfil filho é adicionado ao pai como o único ponto de extremidade no Oeste dos EUA. As configurações no perfil filho podem então ser usadas para controlar a distribuição de tráfego com o Oeste dos EUA, habilitando, por exemplo, o roteamento de tráfego ponderado ou baseado em prioridade dentro dessa região.

![Tráfego de roteamento de “Desempenho” com distribuição de tráfego na região personalizado][8]

## Exemplo 5: configurações de monitoramento por ponto de extremidade
Suponha que você está usando o Gerenciador de Tráfego para migrar o tráfego entre um site da Web local herdado e uma nova versão baseada em nuvem hospedada no Azure. Para o site herdado, é recomendável usar a página inicial (caminho “/”) para monitorar a integridade do site, porém, para a nova versão baseada em nuvem, você está implementando uma página de monitoramento personalizada que inclui verificações adicionais (caminho “/monitor.aspx”).

![Monitoramento do ponto de extremidade do Gerenciador de Tráfego (comportamento padrão)][9]

As configurações de monitoramento em um perfil do Gerenciador de Tráfego se aplicam a todos os pontos de extremidade no perfil, o que significa que você precisaria ter usado anteriormente o mesmo caminho em ambos os sites. Com os perfis aninhados do Gerenciador de Tráfego, agora você pode usar um perfil filho por site para definir as diferentes configurações de monitoramento por site:

![Monitoramento do ponto de extremidade do Gerenciador de Tráfego com definições por ponto de extremidade][10]

## Perguntas frequentes
### Como posso configurar perfis aninhados?
Perfis aninhados do Gerenciador de Tráfego podem ser configurados usando ARM (Azure Resource Manager) e APIs REST do ASM (Azure Service Management), cmdlets do PowerShell e comandos da CLI do Azure de plataforma cruzada. Também há suporte por meio do Portal do Azure, no entanto, não há suporte para eles no Portal “Clássico”.

### A quantas camadas de aninhamento o Gerenciador de Tráfego dá suporte?
Você pode aninhar perfis em até 10 níveis de profundidade. “Loops” não são permitidos.

### Posso combinar outros tipos de ponto de extremidade com perfis filho aninhados no mesmo perfil do Gerenciador de Tráfego?
Sim. Não há nenhuma restrição sobre como combinar os pontos de extremidade de diferentes tipos em um perfil.

### Como o modelo de cobrança se aplica a perfis aninhados?
Há não impacto negativo sobre os preços ao usar perfis aninhados.

A cobrança do Gerenciador de Tráfego tem dois componentes: verificações de integridade do ponto de extremidade e milhões de consultas DNS (para obter detalhes completos, consulte nossa [página de preço](https://azure.microsoft.com/pricing/details/traffic-manager/)). Veja aqui como isso se aplica a perfis aninhados:

* Verificações de integridade de ponto de extremidade: não há nenhum encargo para um perfil filho quando configurado como um ponto de extremidade em um perfil pai. Pontos de extremidade no perfil filho que estão monitorando os serviços subjacentes são cobrados normalmente.
* Consultas DNS: cada consulta é contada apenas uma vez. Uma consulta em um perfil pai que retorna um ponto de extremidade de um perfil filho é cobrada apenas no perfil pai.

### Há impacto no desempenho para perfis aninhados?
Não há nenhum impacto no desempenho ao usar perfis aninhados.

Os servidores de nome do Gerenciador de Tráfego atravessa a hierarquia de perfil internamente durante o processamento de cada consulta DNS, para que uma consulta DNS para um perfil pai possa receber uma resposta DNS com um ponto de extremidade de um perfil filho.

Portanto, um único registro CNAME é usado, o mesmo usado em um único perfil do Gerenciador de Tráfego. **Não** é necessário ter uma cadeia de registros CNAME para cada perfil na hierarquia, portanto, não ocorre nenhum penalidade no desempenho.

### Como o Gerenciador de Tráfego computa a integridade de um ponto de extremidade aninhado em um perfil pai com base na integridade do perfil filho?
Quando um perfil pai estiver configurado com um perfil filho como um ponto de extremidade aninhado, o pai não realiza verificações de integridade no filho diretamente. Em vez disso, a integridade de pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho, e essas informações são propagadas para a hierarquia do perfil aninhado para determinar a integridade do ponto de extremidade aninhado dentro do perfil pai. Isso determina se o perfil pai direcionará o tráfego para o filho.

A tabela a seguir descreve o comportamento de verificações de integridade do Gerenciador de Tráfego para um ponto de extremidade aninhado em um perfil pai que aponta para um perfil filho.

| Status do Monitor de perfil filho | Status do monitor de ponto de extremidade pai | Observações |
| --- | --- | --- |
| Desabilitado. O perfil filho foi desabilitado pelo usuário. |Parada |O estado do ponto de extremidade pai é Parado, não Desabilitado. O estado Desabilitado é reservado para indicar que você desabilitou o ponto de extremidade no perfil pai. |
| Degradado. Pelo menos um ponto de extremidade do perfil filho está no estado Degradado. |Online: o número de pontos de extremidade Online no perfil filho é pelo menos o valor de MinChildEndpoints. CheckingEndpoint: o número de pontos de extremidade Online mais CheckingEndpoint no perfil filho é pelo menos o valor de MinChildEndpoints. Degradado: caso contrário. |O tráfego é roteado para um ponto de extremidade do status CheckingEndpoint. Se MinChildEndpoints estiver definido como muito alto, o ponto de extremidade sempre será degradado. |
| Online. Pelo menos um ponto de extremidade de perfil filho está no estado Online e nenhum está no estado Degradado. |Veja acima. | |
| CheckingEndpoints. Pelo menos um ponto de extremidade de perfil filho é “CheckingEndpoint”; nenhum é “Online” nem “Degradado” |Mesmo que acima. | |
| Inativo. Todos os pontos de extremidade de perfil filho estão com status Desabilitado ou Parado, ou então esse é um perfil sem nenhum ponto de extremidade |Parada | |

## Próximas etapas
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

<!---HONumber=AcomDC_0824_2016-->