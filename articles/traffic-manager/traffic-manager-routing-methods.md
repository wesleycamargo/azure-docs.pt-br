<properties 
   pageTitle="Gerenciador de Tráfego - métodos de roteamento de tráfego - | Microsoft Azure"
   description="Este artigo o ajudará a entender os diferentes métodos de roteamento de tráfego usados pelo Gerenciador de Tráfego"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />

# Métodos de roteamento do Gerenciador de Tráfego

Há três métodos de roteamento de tráfego disponíveis no Gerenciador de Tráfego. Cada perfil do Gerenciador de Tráfego pode usar apenas um método de roteamento por vez, embora você possa selecionar um método de roteamento diferente para seu perfil a qualquer momento.

É importante observar que todos os métodos de roteamento incluem monitoramento de ponto de extremidade. Depois de configurar seu perfil do Gerenciador de Tráfego para especificar o método de roteamento de tráfego que atenda melhor às suas necessidades, defina as configurações de monitoramento. Quando o monitoramento estiver configurado corretamente, o Gerenciador de Tráfego monitorará o estado de seus pontos de extremidade, consistindo em serviços de nuvem e sites, e não enviará tráfego a pontos de extremidade que ele achar que não estão disponíveis. Para obter mais informações sobre o monitoramento do Gerenciador de Tráfego, consulte [Sobre o monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md).

Os três métodos de roteamento de tráfego do Gerenciador de Tráfego são:

- **Failover**: selecione Failover quando houver pontos de extremidade nos mesmos datacenters do Azure ou em data centers diferentes (conhecidos como regiões no Portal de Gerenciamento) e você quiser usar um ponto de extremidade primário para todo o tráfego, mas fornecer backups caso os pontos de extremidade primários ou de backup estejam indisponíveis. Para obter mais informações, consulte [Método de roteamento de tráfego de failover](#failover-traffic-routing-method).

- **Round Robin**: selecione Round Robin quando desejar distribuir a carga entre um conjunto de pontos de extremidade no mesmo datacenter ou em datacenters diferentes. Para obter mais informações, consulte [Método de roteamento de tráfego de Round Robin](#round-robin-traffic-routing-method).

- **Desempenho**: selecione Desempenho quando houver pontos de extremidade em locais geográficos diferentes e você quiser solicitar aos clientes que usem o ponto de extremidade “mais próximo” em termos de menor latência. Para obter mais informações, consulte [Método de roteamento de tráfego de desempenho](#performance-traffic-routing-method).

Observe que os sites do Azure já fornecem método de roteamento de tráfego de round robin e failover para sites em um datacenter, independentemente do modo do site. O Gerenciador de Tráfego permite que você especifique o roteamento de tráfego de round robin e failover para sites em datacenters diferentes.

>[AZURE.NOTE]O TTL (vida útil) informa aos clientes DNS e resolvedores nos servidores DNS por quanto tempo é preciso armazenar em cache os nomes resolvidos. Os clientes continuarão a usar determinado ponto de extremidade ao resolver seu nome de domínio até que a entrada do cache DNS local para o nome expire.

## Método de roteamento de tráfego de failover

Geralmente, uma organização deseja fornecer confiabilidade para seus serviços. Ela faz isso fornecendo serviços de backup no caso de seu serviço principal falhar. Um padrão comum para failover de serviço é fornecer um conjunto de pontos de extremidade idênticos hospedados idênticos e enviar tráfego para um serviço primário, com uma lista de um ou mais backups. Se o serviço primário não estiver disponível, os clientes solicitantes serão referenciados para o próximo serviço, em ordem. Se o primeiro e o segundo serviço na lista não estiverem disponíveis, o tráfego passará para o terceiro e assim por diante.

Ao se configurar o método de roteamento de tráfego de failover, a ordem dos pontos de extremidade selecionados é importante. Usando o Portal de Gerenciamento, você pode configurar a ordem de failover na página Configuração para o perfil.

A Figura 1 mostra um exemplo do método de roteamento de tráfego de failover para um conjunto de pontos de extremidade.

![Método de roteamento de Failover do Gerenciador de Tráfego](./media/traffic-manager-routing-methods/IC750592.jpg)

**Figura 1**

As etapas numeradas a seguir correspondem aos números na Figura 1.

1. O Gerenciador de Tráfego recebe uma solicitação de entrada de um cliente por meio do DNS e localiza o perfil.
2. O perfil contém uma lista ordenada de pontos de extremidade. O Gerenciador de Tráfego verifica qual ponto de extremidade é o primeiro na lista. Se o ponto de extremidade estiver online (com base no monitoramento de ponto de extremidade contínuo), ele especificará o nome DNS do ponto de extremidade na resposta DNS para o cliente. Se o ponto de extremidade estiver offline, o Gerenciador de Tráfego determinará o próximo ponto de extremidade online na lista. Neste exemplo, CS-A está offline (não disponível), mas CS-B está online (disponível).
3. O Gerenciador de Tráfego retorna o nome de domínio de CS-B para o servidor DNS do cliente, que resolve o nome de domínio para um endereço IP e o envia ao cliente.
4. O cliente inicia o tráfego para CS-B.

## Método de roteamento de tráfego de round robin

Um padrão comum de roteamento de tráfego é fornecer um conjunto de pontos de extremidade idênticos e enviar tráfego para cada um deles em um estilo round robin. O método Round Robin divide o tráfego entre vários pontos de extremidade. Ele seleciona um ponto de extremidade íntegro aleatoriamente e não enviará tráfego a serviços que são detectados como estando desativados. Para obter mais informações, consulte [Monitoramento do Gerenciador de Tráfego](../traffic-manager-monitoring.md).

A Figura 2 mostra um exemplo do método de roteamento de tráfego de round robin para um conjunto de pontos de extremidade.

![Método de roteamento de Round Robin do Gerenciador de Tráfego](./media/traffic-manager-routing-methods/IC750593.jpg)

**Figura 2**

As etapas numeradas a seguir correspondem aos números na Figura 2.

1. O Gerenciador de Tráfego recebe uma solicitação de entrada de um cliente e localiza o perfil.
2. O perfil contém uma lista de pontos de extremidade. O Gerenciador de Tráfego seleciona um ponto de extremidade nessa lista aleatoriamente, excluindo qualquer ponto de extremidade offline (não disponível) determinado pelo monitoramento de ponto de extremidade do Gerenciador de Tráfego. Neste exemplo, esse é o ponto de extremidade CS-B.
3. O Gerenciador de Tráfego retorna o nome de domínio de CS-B ao servidor DNS do cliente. O servidor DNS do cliente resolve esse nome de domínio para um endereço IP e o envia ao cliente.
4. O cliente inicia o tráfego para CS-B.

O roteamento de tráfego de round robin também dá suporte à distribuição ponderada de tráfego de rede. A Figura 3 mostra um exemplo do método de roteamento de tráfego de round robin ponderado para um conjunto de pontos de extremidade.

![Método de roteamento Ponderado de Round Robin](./media/traffic-manager-routing-methods/IC750594.png)

**Figura 3**

O roteamento de tráfego de round robin ponderado permite distribuir a carga para vários pontos de extremidade com base no valor de peso atribuído de cada ponto de extremidade. Quanto maior o peso, com mais frequência um ponto de extremidade será retornado. Os cenários em que esse método pode ser útil incluem:

- Atualização gradual de aplicativo: aloque um percentual do tráfego para roteá-lo para um novo ponto de extremidade e aumentar gradualmente o tráfego até que ele chegue a 100%.
- Perfil de migração para o Azure: crie um perfil com pontos de extremidade do Azure e externos e especifique o peso do tráfego que é roteado para cada ponto de extremidade.
- Estouro de nuvem para capacidade adicional: expanda rapidamente uma implantação local na nuvem colocando-a atrás de um perfil do Gerenciador de Tráfego. Quando precisar de capacidade extra na nuvem, você poderá adicionar ou habilitar mais pontos de extremidade e especificar qual parte do tráfego vai para cada ponto de extremidade.

No momento, não é possível usar o Portal de Gerenciamento para configurar o roteamento de tráfego ponderado. O Azure fornece acesso programático a esse método usando a API REST do Gerenciamento de Serviço e os cmdlets do PowerShell do Azure associados.

Para obter informações sobre como usar as APIs REST, consulte [Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Para obter informações sobre como usar os cmdlets do PowerShell do Azure, consulte [Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769). Para um exemplo de configuração, consulte [Pontos de extremidade externos do Gerenciador de Tráfego do Azure e Round Robin ponderado via PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) no blog do Azure.

Para testar o perfil de um único cliente e observar o comportamento de round robin igual ou ponderado, verifique se o nome DNS é resolvido para os endereços IP diferentes dos pontos de extremidade de acordo com os valores iguais ou ponderados no perfil. Durante o teste, você deve desabilitar o cache de cliente DNS no lado do cliente ou limpar o cache DNS entre cada tentativa para garantir que uma nova consulta de nome DNS seja enviada.

## Método de roteamento de tráfego de desempenho

Para rotear o tráfego de pontos de extremidade localizados em datacenters diferentes em todo o mundo, você pode direcionar o tráfego de entrada para o ponto de extremidade mais próximo em termos de menor latência entre o cliente solicitante e o ponto de extremidade. Normalmente, o ponto de extremidade "mais próximo" corresponde diretamente à distância geográfica mais curta. O método de roteamento de tráfego de desempenho permite que você faça a distribuição com base no local e na latência, mas não consegue levar em consideração as alterações em tempo real feitas na carga ou na configuração da rede.

O método de roteamento de tráfego de desempenho localiza o cliente solicitante e direciona-o ao ponto de extremidade mais próximo. A "proximidade" é determinada por uma Tabela de Latência da Internet que mostra o tempo de ida e volta entre vários endereços IP e cada data center do Azure. Essa tabela é atualizada periodicamente e não se destina a ser um reflexo em tempo real do desempenho na Internet. Ela não leva em consideração a carga em determinado serviço, embora o Gerenciador de Tráfego monitore seus pontos de extremidade com base no método escolhido e não os inclua em respostas a consultas DNS caso estejam indisponíveis. Em outras palavras, o roteamento de tráfego de desempenho também incorpora o método de roteamento de tráfego de failover.

A Figura 4 mostra um exemplo do método de roteamento de tráfego de desempenho para um conjunto de pontos de extremidade.

![Método de roteamento de Desempenho do Gerenciador de Tráfego](./media/traffic-manager-routing-methods/IC753237.jpg)

**Figura 4**

As etapas numeradas a seguir correspondem aos números na Figura 4.

1. O Gerenciador de Tráfego cria a Tabela de Latência da Internet periodicamente. A infraestrutura do Gerenciador de Tráfego executa testes para determinar os tempos de viagem de ida e volta entre diferentes pontos no mundo e os data centers do Azure que hospedam pontos de extremidade.
2. O Gerenciador de Tráfego recebe uma solicitação de entrada de um cliente por meio de seu servidor DNS local e localiza o perfil.
3. O Gerenciador de Tráfego localiza a linha na Tabela de Latência da Internet para o endereço IP da solicitação DNS de entrada. Como o servidor DNS local do usuário está executando uma consulta DNS iterativa para localizar o servidor DNS autoritativo para o nome do perfil do Gerenciador de Tráfego, a consulta DNS é enviada do endereço IP do servidor DNS local do cliente.
4. O Gerenciador de Tráfego localiza o data center com o menor tempo para os data centers que hospedam os pontos de extremidade definidos no perfil. Neste exemplo, é o CS-B.
5. O Gerenciador de Tráfego retorna o nome de domínio de CS-B para o servidor DNS local do cliente, que resolve o nome de domínio para um endereço IP e o envia ao cliente.
6. O cliente inicia o tráfego para CS-B.

**Pontos a serem observados:**

- Se o seu perfil contiver vários pontos de extremidade no mesmo datacenter, o tráfego direcionado para esse datacenter será distribuído uniformemente pelos pontos de extremidade disponíveis e íntegros, de acordo com o monitoramento do ponto de extremidade.
- Se todos os pontos de extremidade de um datacenter estiverem indisponíveis (de acordo com o monitoramento de ponto de extremidade), o tráfego desses pontos de extremidade será distribuído entre todos os outros pontos de extremidade disponíveis especificados no perfil, não para os mais próximos. Isso é para ajudar a evitar uma potencial falha em cascata que pode ocorrer se o ponto de extremidade mais próximo ficar sobrecarregado.
- Quando a Tabela de Latência da Internet é atualizada, você pode notar uma diferença nos padrões de tráfego e de carga em seus pontos de extremidade. Essas alterações devem ser mínimas.
- Ao usar o método de roteamento de tráfego de desempenho com pontos de extremidade externos, você precisará especificar o local desses pontos de extremidade. Escolha a região do Azure mais próxima de sua implantação. Para obter mais informações, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md).

## Figuras do Gerenciador de Tráfego

Se você quiser obter as figuras deste tópico como slides do PowerPoint para sua própria apresentação sobre o Gerenciador de Tráfego ou para modificá-las para suas próprias finalidades, consulte [Figuras do Gerenciador de Tráfego na documentação do MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Próximas etapas

[Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md)

[Criar um perfil](traffic-manager-manage-profiles.md)

[Adicionar um ponto de extremidade](traffic-manager-endpoints.md)
 

<!---HONumber=Nov15_HO4-->