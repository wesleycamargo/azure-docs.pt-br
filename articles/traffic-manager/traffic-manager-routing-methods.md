---
title: "Gerenciador de Tráfego – métodos de roteamento de tráfego | Microsoft Docs"
description: "Este artigo o ajudará a entender os diferentes métodos de roteamento de tráfego usados pelo Gerenciador de Tráfego"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 993408c9d008a94dfd4004ed6826a2fe6180b20c
ms.openlocfilehash: aa3c77c0bf9db1f875dd992c4eb7a494af58c400

---

# <a name="traffic-manager-traffic-routing-methods"></a>Métodos de roteamento de tráfego do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure dá suporte a três métodos de roteamento de tráfego para determinar como o tráfego de rede será encaminhado para vários pontos de extremidade de serviço. O Gerenciador de Tráfego aplica o método de roteamento de tráfego para cada consulta DNS recebida. O método de roteamento de tráfego determina qual ponto de extremidade foi retornado na resposta DNS.

O suporte do Azure Resource Manager para o Gerenciador de Tráfego do Azure usa uma terminologia diferente do modelo de implantação clássico. A tabela a seguir mostra as diferenças entre os termos Clássicos e do Resource Manager:

| Termo do Resource Manager | Termo clássico |
| --- | --- |
| Método de roteamento de tráfego |Método de balanceamento de carga |
| Método de prioridade |Método de failover |
| Método ponderado |Método round robin |
| Método de desempenho |Método de desempenho |

Com base nos comentários dos clientes, alteramos a terminologia para melhorar a clareza e reduzir equívocos comuns. Não diferenças na funcionalidade.

Três métodos de roteamento de tráfego estão disponíveis no Gerenciador de Tráfego:

* **Prioridade:** selecione “Prioridade” quando desejar usar um ponto de extremidade de serviço primário para todo o tráfego e fornecer backups, caso os pontos de extremidade primário ou de backup não estejam disponíveis.
* **Ponderado:** selecione "Ponderado" quando quiser distribuir o tráfego entre um conjunto de pontos de extremidade, seja uniformemente ou de acordo com os pesos que você definir.
* **Desempenho**: selecione "Desempenho" quando tiver pontos de extremidade em diferentes regiões e quiser que os usuários finais usem o ponto de extremidade “mais próximo” em termos de menor latência de rede.

Todos os perfis do Gerenciador de Tráfego incluem o monitoramento da integridade do ponto de extremidade e o failover automático do ponto de extremidade. Para obter mais informações, consulte [Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md). Um único perfil do Gerenciador de Tráfego pode usar apenas um único método de roteamento de tráfego. Você pode selecionar um método de roteamento de tráfego diferente para o seu perfil a qualquer momento. As alterações são aplicadas em até um minuto e não há tempo de inatividade. Métodos de roteamento de tráfego podem ser combinados usando perfis aninhados do Gerenciador de Tráfego. O aninhamento permite configurações sofisticadas e flexíveis de roteamento de tráfego para atender às necessidades de aplicativos maiores e mais complexos. Para obter mais informações, consulte [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Método de roteamento de tráfego por prioridade

Frequentemente, as organizações desejam fornecer confiabilidade para seus serviços implantando um ou mais serviços de backup, caso seu serviço primário fique inativo. O método de roteamento de tráfego por “Prioridade” permite que os clientes do Azure implementem esse padrão de failover com facilidade.

![Método de roteamento de tráfego por “Prioridade” do Gerenciador de Tráfego do Azure][1]

O perfil do Gerenciador de Tráfego contém uma lista priorizada de pontos de extremidade de serviço. Por padrão, o Gerenciador de Tráfego envia todo o tráfego para o ponto de extremidade primário (prioridade mais alta). Se o ponto de extremidade primário não estiver disponível, o Gerenciador de Tráfego encaminhará o tráfego para o segundo ponto de extremidade. Se os pontos de extremidade primário e secundário não estiverem disponíveis, o tráfego passará para o terceiro e assim por diante. A disponibilidade do ponto de extremidade é baseada no status configurado (habilitado ou desabilitado) e no monitoramento contínuo do ponto de extremidade.

### <a name="configuring-endpoints"></a>Configurando pontos de extremidade

Com o Azure Resource Manager, você configura a prioridade do ponto de extremidade usando explicitamente a propriedade “priority” para cada ponto de extremidade. Essa propriedade é um valor entre 1 e 1000. Valores mais baixos representam uma prioridade mais alta. Pontos de extremidade não podem compartilhar os valores de prioridade. A configuração da propriedade é opcional. Quando é omitida, uma prioridade padrão baseada na ordem do ponto de extremidade é usada.

Com a interface Clássica, a prioridade do ponto de extremidade é configurada implicitamente. A prioridade baseia-se na ordem em que os pontos de extremidade são listados na definição do perfil.

## <a name="weighted-traffic-routing-method"></a>Método de roteamento de tráfego ponderado

O método de roteamento de tráfego “Ponderado” permite que você distribua o tráfego de maneira uniforme ou use uma ponderação predefinida.

![Método de roteamento de tráfego “Ponderado” do Gerenciador de Tráfego do Azure][2]

No método de roteamento de tráfego Ponderado, você atribui um peso a cada ponto de extremidade na configuração do perfil do Gerenciador de Tráfego. Cada peso é um inteiro de 1 a 1000. Esse parâmetro é opcional. Se ele for omitido, os Gerenciadores de Tráfego usarão um peso padrão de “1”.

Para cada consulta DNS recebida, o Gerenciador de Tráfego escolhe aleatoriamente um ponto de extremidade disponível. A probabilidade de escolher um ponto de extremidade baseia-se nos pesos atribuídos a todos os pontos de extremidade disponíveis. Usar o mesmo peso em todos os pontos de extremidade resulta em uma distribuição uniforme do tráfego. Usar pesos maiores ou menores em pontos de extremidade específicos faz com que esses pontos de extremidade sejam retornados com maior ou menor frequência nas respostas DNS.

O método ponderado permite alguns cenários úteis:

* Atualização gradual de aplicativo: aloque um percentual do tráfego para roteá-lo para um novo ponto de extremidade e aumentar gradualmente o tráfego até que ele chegue a 100%.
* Migração de aplicativos para o Azure: crie um perfil com pontos de extremidade externos e do Azure. Ajuste o peso dos pontos de extremidade para dar preferência aos novos pontos de extremidade.
* Estouro de nuvem para capacidade adicional: expanda rapidamente uma implantação local na nuvem colocando-a atrás de um perfil do Gerenciador de Tráfego. Quando precisar de capacidade extra na nuvem, você poderá adicionar ou habilitar mais pontos de extremidade e especificar qual parte do tráfego vai para cada ponto de extremidade.

O novo portal do Azure dá suporte à configuração de roteamento de tráfego ponderado. Os pesos não podem ser configurados no portal Clássico. Você também pode configurar pesos usando o Resource Manager e as versões clássicas do Azure PowerShell, a CLI e as APIs REST.

É importante entender que as respostas DNS são armazenadas em cache por clientes e pelos servidores DNS recursivo que os clientes usam para resolver nomes DNS. Esse cache pode ter um impacto nas distribuições de tráfego ponderado. Quando o número de clientes e de servidores DNS recursivo é grande, a distribuição de tráfego funciona conforme esperado. No entanto, quando o número de clientes ou de servidores DNS recursivo é pequeno, o cache pode distorcer consideravelmente a distribuição de tráfego.

Os casos de uso comuns incluem:

* Ambientes de desenvolvimento e teste
* Comunicações de aplicativo para aplicativo
* Aplicativos voltados para uma base de usuários estreita que compartilha uma infraestrutura comum de DNS recursivo (por exemplo, funcionários de uma empresa que se conectam por meio de um proxy)

Esses efeitos do caching de DNS são comuns a todos os sistemas de roteamento de tráfego baseados em DNS, e não específicos ao Gerenciador de Tráfego do Azure. Em alguns casos, limpar explicitamente o cache DNS pode fornecer uma solução alternativa. Em outros casos, um método alternativo de roteamento de tráfego pode ser mais apropriado.

## <a name="performance-traffic-routing-method"></a>Método de roteamento de tráfego por desempenho

A implantação de pontos de extremidade em duas ou mais localizações do mundo pode melhorar a capacidade de resposta de vários aplicativos, encaminhando o tráfego para a localização “mais próxima” a você. O método de roteamento de tráfego por “Desempenho” fornece essa funcionalidade.

![Método de roteamento de tráfego por “Desempenho” do Gerenciador de Tráfego do Azure][3]

O ponto de extremidade “mais próximo” não é necessariamente o mais próximo em termos de distância geográfica. Em vez disso, o método de roteamento de tráfego por “Desempenho” determina o ponto de extremidade mais próximo medindo a latência de rede. O Gerenciador de Tráfego mantém uma Tabela de Latência da Internet para controlar o tempo da viagem de ida e volta entre intervalos de endereços IP e em cada data center do Azure.

O Gerenciador de Tráfego pesquisa o endereço IP de origem da solicitação DNS de entrada na Tabela de Latência da Internet. O Gerenciador de Tráfego escolhe um ponto de extremidade disponível no data center do Azure que tem a latência mais baixa para esse intervalo de endereços IP e retorna o ponto de extremidade na resposta DNS.

Como explicamos em [Como funciona o Gerenciador de Tráfego](traffic-manager-how-traffic-manager-works.md), o Gerenciador de Tráfego não recebe consultas DNS diretamente de clientes. Em vez disso, as consultas DNS são recebidas do serviço DNS recursivo que os clientes são configurados para usar. Portanto, o endereço IP usado para determinar o ponto de extremidade “mais próximo” não é o endereço IP do cliente, mas o endereço IP do serviço DNS recursivo. Na prática, esse endereço IP é um bom proxy para o cliente.

Regularmente, o Gerenciador de Tráfego atualiza a Tabela de Latência da Internet para verificar se há alterações na Internet global e em novas regiões do Azure. No entanto, o desempenho do aplicativo varia de acordo com as variações em tempo real na carga pela Internet. O roteamento de tráfego por desempenho não monitora a carga em um ponto de extremidade de serviço específico. No entanto, se um ponto de extremidade ficar indisponível, o Gerenciador de Tráfego não o retornará nas respostas às consultas DNS.

Pontos a serem observados:

* Se seu perfil contiver vários pontos de extremidade na mesma região do Azure, o Gerenciador de Tráfego distribuirá o tráfego de maneira uniforme pelos pontos de extremidade disponíveis nessa região. Se preferir uma distribuição de tráfego diferente em uma região, use os [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).
* Se todos os pontos de extremidade habilitados em determinada região do Azure estiverem degradados, o Gerenciador de Tráfego distribuirá o tráfego entre todos os outros pontos de extremidade disponíveis, em vez do ponto de extremidade mais próximo. Essa lógica impede que ocorra uma falha em cascata, não sobrecarregando o ponto de extremidade mais próximo. Se desejar definir uma sequência de failover preferencial, use os [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).
* Ao usar o método de roteamento de tráfego por Desempenho com pontos de extremidade externos ou aninhados, você precisará especificar a localização desses pontos de extremidade. Escolha a região do Azure mais próxima de sua implantação. Essas localizações são os valores com suporte na Tabela de Latência da Internet.
* O algoritmo que escolhe o ponto de extremidade é determinístico. Consultas DNS repetidas do mesmo cliente são direcionadas ao mesmo ponto de extremidade. Normalmente, os clientes usam servidores DNS recursivo diferentes quando estão viajando. O cliente pode ser encaminhado para outro ponto de extremidade. O roteamento também pode ser afetado pelas atualizações à Tabela de Latência da Internet. Portanto, o método de roteamento de tráfego por Desempenho não garante que um cliente sempre seja encaminhado para o mesmo ponto de extremidade.
* Quando a Tabela de Latência da Internet é alterada, você pode observar que alguns clientes foram direcionados para um ponto de extremidade diferente. Essa alteração de roteamento é mais precisa com base nos dados de latência atuais. Essas atualizações são essenciais para manter a precisão do roteamento de tráfego por Desempenho à medida que a Internet evolui continuamente.

## <a name="next-steps"></a>Próximas etapas

Aprenda a desenvolver aplicativos de alta disponibilidade usando o [monitoramento de ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md)

Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



<!--HONumber=Jan17_HO1-->


