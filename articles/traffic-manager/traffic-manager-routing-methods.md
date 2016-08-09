<properties 
   pageTitle="Gerenciador de Tráfego - métodos de roteamento de tráfego - | Microsoft Azure"
   description="Este artigo o ajudará a entender os diferentes métodos de roteamento de tráfego usados pelo Gerenciador de Tráfego"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="jtuliani" />

# Métodos de roteamento de tráfego do Gerenciador de Tráfego

Esta página descreve os métodos de roteamento de tráfego com suporte do Gerenciador de Tráfego do Azure. Eles são usados para direcionar os usuários finais para o ponto de extremidade de serviço correto.

> [AZURE.NOTE] A API do ARM (Azure Resource Manager) para o Gerenciador de Tráfego usa uma terminologia diferente da API do ASM (Azure Service Management). Essa alteração foi introduzida após comentários de clientes para aumentar a clareza e reduzir equívocos comuns. Nesta página, usaremos a terminologia do ARM. As diferenças são:

>- No ARM, usamos "método de roteamento de tráfego" para descrever o uso de algoritmo para determinar a qual ponto de extremidade um determinado usuário final deve ser direcionado em um momento específico. No ASM, chamamos isso de "método de balanceamento de carga".

>- No ARM, usamos "ponderado" para fazer referência ao método de roteamento de tráfego que distribui o tráfego entre todos os pontos de extremidade disponíveis, com base no peso definido para cada ponto de extremidade. No ASM, chamamos isso de "round robin".
>- ARM, usamos "prioridade" para fazer referência ao método de roteamento de tráfego que direciona todo o tráfego para o primeiro ponto de extremidade disponível em uma lista ordenada. No ASM, chamamos isso de "failover".

> Em todos os casos, a única diferença está no nome. Não diferenças na funcionalidade.


O Gerenciador de Tráfego do Azure dá suporte a vários algoritmos para determinar como os usuários finais são encaminhados para os diferentes pontos de extremidade de serviço. Eles são chamados de métodos de roteamento de tráfego. O método de roteamento de tráfego é aplicado a cada consulta DNS recebida para determinar qual ponto de extremidade deve ser retornado na resposta DNS.

Três métodos de roteamento de tráfego estão disponíveis no Gerenciador de Tráfego:

- **Prioridade:** selecione "Prioridade" quando quiser usar um ponto de extremidade de serviço primário para todo o tráfego e fornecer backups caso o ponto de extremidade primário ou os pontos de extremidade de backup não estejam disponíveis. Para obter mais informações, consulte [Método de roteamento de tráfego por prioridade](#priority-traffic-routing-method).

- **Ponderado:** selecione "Ponderado" quando quiser distribuir o tráfego entre um conjunto de pontos de extremidade, seja uniformemente ou de acordo com pesos que você definir. Para obter mais informações, consulte [Método de roteamento de tráfego ponderado](#weighted-traffic-routing-method).

- **Desempenho**: selecione "Desempenho" quando você tiver houver pontos de extremidade em regiões diferentes e quiser que os usuários finais usem o ponto de extremidade “mais próximo” em termos de menor latência de rede. Para obter mais informações, consulte [Método de roteamento de tráfego por desempenho](#performance-traffic-routing-method).

> [AZURE.NOTE] Todos os perfis do Gerenciador de Tráfego incluem monitoramento contínuo de integridade do ponto de extremidade e failover automático do ponto de extremidade. Há suporte para todos os métodos de roteamento de tráfego. Para obter mais informações, consulte [Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md).

Um único perfil do Gerenciador de Tráfego pode usar apenas um único método de roteamento de tráfego. Você pode selecionar um método de roteamento de tráfego diferente para o seu perfil a qualquer momento. As alterações são aplicadas em até um minuto e não há tempo de inatividade. Métodos de roteamento de tráfego podem ser combinados usando perfis aninhados do Gerenciador de Tráfego. Isso permite que configurações sofisticadas e flexíveis de roteamento de tráfego sejam criadas para atender às necessidades de aplicativos maiores e mais complexos. Para obter mais informações, consulte [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

## Método de roteamento de tráfego por prioridade

Frequentemente, as organizações desejam fornecer confiabilidade para seus serviços e fazem isso fornecendo um ou mais serviços de backup para o caso de seu serviço principal falhar. O método de roteamento de tráfego por "Prioridade" permite que os clientes do Azure implementem facilmente esse padrão de failover.

![Método de roteamento de tráfego por "Prioridade" do Gerenciador de Tráfego do Azure][1]

O perfil do Gerenciador de Tráfego é configurado com uma lista priorizada de pontos de extremidade de serviço. Por padrão, todo o tráfego de usuários finais é enviado ao ponto de extremidade primário (com a prioridade mais alta). Se o ponto de extremidade primário não estiver disponível (com base no status de habilitado/desabilitado do ponto de extremidade configurado e no monitoramento contínuo do ponto de extremidade), os usuários serão encaminhados ao segundo ponto de extremidade. Se os pontos de extremidade primário e secundário não estiverem disponíveis, o tráfego passará para o terceiro e assim por diante.

A configuração das prioridades dos pontos de extremidade é feita de forma diferente nas APIs do ARM (e no novo portal do Azure) com relação às APIs do ASM (e no portal clássico):

- Nas APIs do ARM, a prioridade do ponto de extremidade é configurada explicitamente, usando a propriedade "priority" definida para cada ponto de extremidade. Essa propriedade deve ter um valor entre 1 e 1000, em que os valores mais baixos representam uma prioridade mais alta. Dois pontos de extremidade não podem compartilhar o mesmo valor de prioridade. A propriedade é opcional e, quando for omitida, uma prioridade padrão com base na ordem do ponto de extremidade será usada.

- Nas APIs do ASM, a prioridade dos pontos de extremidade é configurada implicitamente, com base na ordem em que os pontos de extremidade são listados na definição do perfil. Você também pode configurar a ordem de failover no portal "clássico" do Azure, na página de Configuração do perfil.

## Método de roteamento de tráfego ponderado

Uma abordagem comum para fornecer alta disponibilidade e maximizar a utilização do serviço é fornecer um conjunto de pontos de extremidade e distribuir tráfego entre todos eles, de forma uniforme ou com um peso predefinido. Esse recurso tem suporte com o método de roteamento de tráfego "Ponderado".

![Método de roteamento de tráfego "Ponderado" do Gerenciador de Tráfego do Azure][2]

No método de roteamento de tráfego Ponderado, é atribuído um peso a cada ponto de extremidade como parte da configuração do perfil do Gerenciador de Tráfego. Cada peso é um inteiro de 1 a 1000. Esse parâmetro é opcional e, se for omitido, um peso padrão de "1" será usado.
  
O tráfego de usuários finais é distribuído entre todos os pontos de extremidade de serviço disponíveis (com base no status de habilitado/desabilitado do ponto de extremidade configurado e no monitoramento contínuo do ponto de extremidade). Para cada consulta DNS recebida, um ponto de extremidade disponível é escolhido aleatoriamente, com a probabilidade baseada no peso atribuído a esse ponto de extremidade e aos outros pontos de extremidade disponíveis.

Usar o mesmo peso para todos os pontos de extremidade resulta em uma distribuição uniforme do tráfego, ideal para levar a uma utilização consistente de um conjunto de pontos de extremidade idênticos. Usar pesos maiores (ou menores) em determinados pontos de extremidade faz com que esses pontos de extremidade sejam retornados com maior (ou menor) frequência nas respostas DNS e, portanto, recebam mais tráfego. Isso possibilita diversos cenários úteis:

- Atualização gradual de aplicativo: aloque um percentual do tráfego para roteá-lo para um novo ponto de extremidade e aumentar gradualmente o tráfego até que ele chegue a 100%.

- Perfil de migração para o Azure: crie um perfil com pontos de extremidade do Azure e externos e especifique o peso do tráfego que é roteado para cada ponto de extremidade.

- Estouro de nuvem para capacidade adicional: expanda rapidamente uma implantação local na nuvem colocando-a atrás de um perfil do Gerenciador de Tráfego. Quando precisar de capacidade extra na nuvem, você poderá adicionar ou habilitar mais pontos de extremidade e especificar qual parte do tráfego vai para cada ponto de extremidade.

O roteamento de tráfego ponderado pode ser configurado por meio do novo portal do Azure, mas os pesos não podem ser configurados por meio do portal "clássico". Ele também pode ser configurado por meio do ARM e do ASM usando o Azure PowerShell, a CLI do Azure e as APIs REST do Azure.

Observação: as respostas DNS são armazenadas em cache tanto pelos clientes quanto pelos servidores DNS recursivos que esses clientes usam para fazer suas consultas DNS. É importante entender o impacto potencial desse caching nas distribuições de tráfego ponderadas. Se o número de clientes e de servidores DNS recursivos for grande, como é o caso dos aplicativos da Internet típicos, a distribuição de tráfego funcionará conforme o esperado. No entanto, se o número de clientes ou servidores DNS recursivos for pequeno, o cache poderá afetar significativamente a distribuição de tráfego. Casos de uso comuns em que isso pode ocorrer incluem:

- Ambientes de desenvolvimento e teste
- Comunicações de aplicativo para aplicativo
- Aplicativos voltados para uma base restrita de usuários que compartilha uma infraestrutura de DNS recursivo comum, como funcionários de uma organização.

Esses efeitos do caching de DNS são comuns a todos os sistemas de roteamento de tráfego baseados em DNS, e não específicos ao Gerenciador de Tráfego do Azure. Em alguns casos, limpar explicitamente o cache do DNS pode fornecer uma solução alternativa. Em outros casos, um método alternativo de roteamento de tráfego pode ser mais apropriado.

## Método de roteamento de tráfego por desempenho

É possível melhorar a capacidade de resposta de muitos aplicativos implantando pontos de extremidade em dois ou mais locais do mundo e encaminhando os usuários finais para a localização "mais próxima" a eles. Essa é a finalidade do método de roteamento de tráfego por "Desempenho".

![Método de roteamento de tráfego por "Desempenho" do Gerenciador de Tráfego do Azure][3]

Para maximizar a capacidade de resposta, o ponto de extremidade "mais próximo" não é necessariamente o mais próximo em termos de distância geográfica. Em vez disso, o método de roteamento de tráfego por "Desempenho" determina qual ponto de extremidade é o mais próximo do usuário final em termos de latência de rede. Isso é determinado por uma Tabela de Latência da Internet que mostra o tempo de ida e volta entre intervalos de endereços IP e cada data center do Azure.

O Gerenciador de Tráfego examina cada solicitação DNS recebida e procura o endereço IP de origem da solicitação na Tabela de Latência da Internet. Isso determina a latência de endereço IP para cada data center do Azure. O Gerenciador de Tráfego, então, escolhe qual dos pontos de extremidade disponíveis (com base no status de habilitado/desabilitado do ponto de extremidade configurado e no monitoramento contínuo do ponto de extremidade) tem a menor latência e retorna esse ponto de extremidade na resposta DNS. O usuário final, portanto, é direcionado para o ponto de extremidade que lhe proporcione a menor latência e, portanto, o melhor desempenho.

Conforme explicado em [How Traffic Manager Works (Como funciona o Gerenciador de Tráfego)](traffic-manager-how-traffic-manager-works.md), o Gerenciador de Tráfego não recebe consultas DNS diretamente dos usuários finais, ele as recebe do serviço de DNS recursivo que eles estão configurados para usar. Sendo assim, o endereço IP usado para determinar o ponto de extremidade "mais próximo" não é o endereço IP do usuário final, e sim o endereço IP do seu serviço de DNS recursivo. Na prática, esse endereço IP é um bom proxy para o usuário final para essa finalidade.

Para compensar alterações na Internet global e o acréscimo de novas regiões do Azure, o Gerenciador de Tráfego atualiza regularmente a Tabela de Latência da Internet que consome. No entanto, ela não pode levar em consideração variações em tempo real no desempenho ou na carga na Internet.

O roteamento de tráfego por desempenho não leva em consideração a carga em determinado ponto de extremidade de serviço, embora o Gerenciador de Tráfego monitore seus pontos de extremidade e não os inclua em respostas a consultas DNS caso estejam indisponíveis.

Pontos a serem observados:

- Se seu perfil contiver vários pontos de extremidade na mesma região do Azure, o tráfego direcionado a essa região será distribuído uniformemente entre pontos de extremidade disponíveis (com base no status de habilitado/desabilitado do ponto de extremidade configurado e no monitoramento contínuo do ponto de extremidade). Se você preferir uma distribuição de tráfego diferente dentro de uma região, isso poderá ser feito usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

- Se todos os pontos de extremidade habilitados em uma determinada região do Azure estiverem degradados (de base no monitoramento contínuo do ponto de extremidade), o tráfego desses pontos de extremidade será distribuído entre todos os outros pontos de extremidade disponíveis especificados no perfil, não para os mais próximos. Isso é para ajudar a evitar uma potencial falha em cascata que pode ocorrer se o ponto de extremidade mais próximo ficar sobrecarregado. Se você preferir definir a sequência de failover do ponto de extremidade, isso poderá ser feito usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

- Ao usar o método de roteamento de tráfego de por Desempenho com pontos de extremidade externos ou aninhados, você precisará especificar a localização desses pontos de extremidade. Escolha a região do Azure mais próxima da sua implantação; as opções disponíveis são as regiões do Azure, pois esses são os locais com suporte na Tabela de Latência de Internet.

- O algoritmo que escolhe o ponto de extremidade a ser retornado para um determinado usuário final é determinístico, não há aleatoriedade envolvida. Consultas DNS repetidas do mesmo cliente serão direcionadas ao mesmo ponto de extremidade. No entanto, não se deve depender do método de roteamento de tráfego por Desempenho para sempre encaminhar um determinado usuário para uma determinada implantação (que poderá ser necessário, por exemplo, se dados desse usuário estiverem armazenados em somente uma localização). Isso ocorre porque, quando viajam, os usuários finais normalmente usam servidores DNS recursivos diferentes e podem ser encaminhados para um ponto de extremidade diferente. Eles também podem ser afetados por atualizações da Tabela de Latência da Internet.

- Quando a Tabela de Latência da Internet é atualizada, você pode observar que alguns clientes foram direcionados para pontos de extremidade diferentes. O número de usuários afetados deve ser mínimo e reflete um roteamento mais preciso com base em dados de latência atuais. Essas atualizações são essenciais para manter a precisão do roteamento de tráfego por Desempenho à medida que a Internet evolui continuamente.


## Próximas etapas

Aprenda a desenvolver aplicativos de alta disponibilidade usando o [monitoramento de ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md)

Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-manage-profiles.md)


<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png

<!---HONumber=AcomDC_0727_2016-->