<properties
   pageTitle="Diretrizes de Caching | Microsoft Azure"
   description="Diretrizes sobre caching para melhorar o desempenho e a escalabilidade."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Diretrizes de Caching

![](media/best-practices-caching/pnp-logo.png)

O caching é uma técnica comum, que tem como objetivo melhorar o desempenho e a escalabilidade de um sistema, copiando temporariamente dados frequentemente acessados para armazenamento rápido localizado perto do aplicativo. Se esse armazenamento rápido de dados está localizado mais próximo do aplicativo que a fonte original, o caching pode melhorar significativamente os tempos de resposta para aplicativos cliente fornecendo dados mais rapidamente. O cache é mais eficaz quando uma instância do cliente lê repetidamente os mesmos dados, especialmente se os dados permanecem relativamente estáticos e o repositório de dados original está lento em relação à velocidade do cache, está sujeito a um nível alto de contenção ou está longe quando a latência de rede pode causar lentidão no acesso.

## Caching em aplicativos distribuídos

Aplicativos distribuídos normalmente implementam uma ou ambas as estratégias a seguir ao colocar dados em cache:

- Usando um cache particular, onde os dados são mantidos localmente no computador que executa uma instância de um aplicativo ou serviço.
- Usando um cache compartilhado, servindo como uma fonte comum que pode ser acessada por vários processos e/ou computadores.

Em ambos os casos, a colocação em cache pôde ser executada no lado do cliente (pelo processo fornecendo da interface de usuário a um sistema, como um navegador da Web ou aplicativo de área de trabalho) e/ou do servidor (pelo processo fornecendo os serviços comerciais sendo executados remotamente).

### Caching particular

O tipo mais básico de cache é um repositório na memória, mantido no espaço de endereço de um único processo e acessado diretamente pelo código que é executado nesse processo. Esse tipo de cache pode ser acessado muito rápido e pode fornecer uma estratégia extremamente eficiente para armazenar quantidades modestas de dados estáticos, já que o tamanho de um cache normalmente é restrito pelo volume de memória disponível no computador que hospeda o processo. Se você precisar colocar em cache mais informações do que é possível armazenar fisicamente na memória, você pode gravar dados colocados em cache no sistema de arquivos local. Esses dados serão necessariamente mais lentos para acessar do que dados mantidos em memória, mas ainda devem ser mais rápidos e confiáveis do que recuperar dados via uma rede.

Se você tem várias instâncias de um aplicativo que usa esse modelo em execução simultânea, cada instância do aplicativo terá seu próprio cache independente contendo sua própria cópia dos dados.

Você deve pensar em um cache como um instantâneo dos dados originais, em algum ponto no passado. Se esses dados não são estáticos, é provável que instâncias diferentes do aplicativo mantenham versões diferentes dos dados em seus caches. Portanto, a mesma consulta executada por essas instâncias poderia retornar resultados diferentes, como mostrado na Figura 1.

![Usando um cache em memória em diferentes instâncias de um aplicativo](media/best-practices-caching/Figure1.png)

_Figura 1: Usando um cache em memória em instâncias diferentes de um aplicativo_

### Cache Compartilhado

Usar um cache compartilhado pode ajudar a aliviar a preocupação de que dados podem diferir em cada cache, como pode ocorrer com o caching em memória. O cache compartilhado garante que diferentes instâncias de aplicativo vejam a mesma exibição dos dados colocados em cache localizando o cache em um local separado, normalmente hospedado como parte de um serviço separado, conforme mostrado na Figura 2.

![Usando um cache compartilhado\_](media/best-practices-caching/Figure2.png)

_Figura 2: Usando um cache compartilhado_

Uma importante vantagem de usar a abordagem de colocação em cache compartilhado é a escalabilidade que ela pode ajudar a fornecer. Muitos serviços de cache compartilhado são implementados usando um cluster de servidores e utilizam o software que distribui os dados pelo cluster de modo transparente. Uma instância de aplicativo simplesmente envia uma solicitação para o serviço de cache e a infra-estrutura subjacente é responsável por determinar o local dos dados armazenados em cache no cluster. Você pode facilmente dimensionar o cache adicionando mais servidores.

As desvantagens da abordagem de cache compartilhado são que o acesso ao cache é mais lento porque ele não é mais mantido localmente para cada instância do aplicativo, e a necessidade de implementar um serviço de cache separado pode acrescentar complexidade à solução.

## Considerações para o uso de caching

As seções a seguir descrevem detalhadamente as considerações para a criação e uso de um cache.

### Quando devem dados ser colocados em cache?

Caching pode melhorar drasticamente o desempenho, a escalabilidade e a disponibilidade. Quanto mais dados você tem e maior o número de usuários que precisam acessar esses dados, maiores se tornam as vantagens do caching graças à redução da latência e da contenção, associada ao processamento de grandes volumes de solicitações simultâneas no repositório de dados original. Por exemplo, um banco de dados pode oferecer suporte a um número limitado de conexões simultâneas, todavia, recuperar dados de um cache compartilhado em vez do banco de dados subjacente permite que um aplicativo cliente acesse esses dados mesmo que o número de conexões disponíveis esteja atualmente esgotado. Além disso, se o banco de dados ficar indisponível, os aplicativos cliente poderão continuar usando os dados mantidos no cache.

Você deve considerar a possibilidade de colocar em cache dados lidos com frequência, mas que não são modificados frequentemente (os dados têm uma grande proporção de operações de leitura em relação às operações de gravação). No entanto, você não deve usar o cache como o repositório autoritativo de informações essenciais; Você deve garantir que todas as alterações que seu aplicativo não pode perder sejam salvas sempre em um repositório de dados persistente. Desse modo, se o cache estiver indisponível, o aplicativo pode continuar a operar usando o repositório de dados e você não perderá informações importantes.

### Tipos de dados e estratégias de população de cache

A chave para uso eficiente de um cache reside em determinar os dados mais apropriados para ele e colocar esses dados em cache no momento apropriado. Os dados podem ser adicionados ao cache sob demanda na primeira vez em que são recuperados por um aplicativo, de modo que o aplicativo precise buscar os dados apenas uma vez no repositório de dados e os acessos subsequentes possam ser atendidos pelo uso do cache.

Como alternativa, um cache pode ser parcialmente ou totalmente preenchido com dados com antecedência, normalmente quando o aplicativo é iniciado (um método conhecido como propagação). No entanto, não é aconselhável implementar a propagação em um cache grande, já que essa abordagem pode impor uma carga elevada e repentina sobre o repositório de dados original quando o aplicativo começa a ser executado.

Geralmente uma análise de padrões de uso pode ajudar a decidir entre pré-popular um cache total ou parcialmente, e também a escolher os dados que devem ser armazenados em cache. Por exemplo, provavelmente seria útil propagar o cache com os dados estáticos de perfil de usuário para os clientes que usam o aplicativo regularmente (talvez todo dia), mas não para os clientes que usam o aplicativo apenas uma vez por semana.

O caching geralmente funciona bem com dados imutáveis ou que mudam com pouca frequência. Exemplos incluem informações de referência como informações sobre preço e produto em um aplicativo de comércio eletrônico, ou recursos compartilhados estáticos que são caros para construir. Alguns ou todos esses dados podem ser colocados em cache na inicialização do aplicativo, para minimizar a demanda por recursos e melhorar o desempenho. Também pode ser apropriado ter um processo em segundo plano que atualize periodicamente os dados de referência em cache para assegurar que eles estejam atualizados, ou que atualize o cache quando os dados de referência mudarem.

O caching pode ser menos útil para dados dinâmicos, embora haja algumas exceções a essa consideração (consulte a seção Caching de Dados Altamente Dinâmicos posteriormente neste guia para obter mais informações). Quando os dados originais são alterados regularmente, as informações em cache tornam-se obsoletas muito rapidamente ou então a sobrecarga de manter o cache sincronizado com o repositório de dados original reduz a eficácia do cache. Observe que um cache não precisa incluir os dados completos para uma entidade. Por exemplo, se um item de dados representa um objeto com vários valores, como um cliente de banco com nome, endereço e saldo da conta bancária, alguns desses elementos permanecerão estáticos (o nome e o endereço), enquanto outros (como o saldo da conta) podem ser mais dinâmicos. Nessas situações, pode ser útil armazenar em cache as partes estáticas dos dados e apenas recuperar (ou calcular) as informações restantes como e quando necessário.

A análise de uso e testes de desempenho devem ser executados para determinar se alguma das opções a seguir é adequada: pré-popular o cache, carregar os dados em cache sob demanda, ou ainda uma combinação dessas duas abordagens. A decisão deve ser baseada em uma combinação do padrão de utilização e da volatilidade dos dados. A análise de desempenho e utilização do cache é especialmente importante em aplicativos que encontram cargas pesadas e devem ser altamente dimensionáveis. Por exemplo, em cenários altamente escalonáveis, talvez faça sentido propagar o cache para reduzir a carga no repositório de dados em horários de pico.

O caching também pode ser usado para evitar a repetição de computações conforme o aplicativo é executado. Se uma operação transforma dados ou executa um cálculo complicado, ele pode salvar os resultados da operação no cache. Se o mesmo cálculo for necessário posteriormente, o aplicativo pode simplesmente recuperar os resultados por meio do cache.

Um aplicativo pode modificar os dados armazenados em um cache, mas você deve considerar o cache como um repositório de dados transitório, que pode desaparecer a qualquer momento. Não armazene dados valiosos apenas no cache, mas certifique-se de manter as informações também no repositório de dados original. Desse modo, se o cache ficar indisponível, a possibilidade de perda de dados será minimizada.

### Caching de dados altamente dinâmicos

Armazenar informações que mudam rapidamente em um repositório de dados persistente pode impor uma sobrecarga ao sistema. Por exemplo, considere um dispositivo que reporta continuamente o status ou alguma outra medida. Se um aplicativo decide não armazenar esses dados em cache tendo como motivo o fato de que as informações em cache quase sempre estariam desatualizadas, então a mesma consideração pode ser verdadeira ao armazenar e recuperar essas informações do repositório de dados; no tempo utilizado para salvar e coletar esses dados, eles podem ter mudado. Em uma situação como essa, considere as vantagens de armazenar as informações dinâmicas diretamente no cache em vez do repositório de dados persistente. Se os dados não são de caráter crítico e não precisam ser auditados, não importa se determinada alteração ocasional é perdida.

### Gerenciando a expiração de dados em um cache

Na maioria dos casos, os dados armazenados em um cache são uma cópia dos dados mantidos no repositório de dados original. Os dados no repositório de dados original podem mudar depois de terem sido armazenado em cache, fazendo com que os dados em cache tornem-se obsoletos. Muitos sistemas de armazenamento em cache permitem configurar o cache para expirar dados e reduzir o período pelo qual os dados podem permanecer desatualizados.

Quando dados armazenados em cache expiram, eles são removidos do cache e o aplicativo deve recuperar os dados do repositório de dados original (ele pode colocar as informações recentemente coletadas novamente no cache). Você pode definir uma política de expiração padrão quando configura o cache. Em muitos serviços de cache você também pode determinar o período de validade para objetos individuais ao armazená-los programaticamente no cache (alguns caches permitem especificar o período de validade como um valor absoluto, ou então como um valor deslizante que faz com que o item seja removido do cache se não for acessado durante o tempo especificado. Essa configuração substitui qualquer política de expiração em todo o cache, mas somente para os objetos especificados.

> [AZURE.NOTE]Considere cuidadosamente o período de validade para o cache e os objetos que ele contém. Se o período for definido com um valor excessivamente curto, os objetos expirarão rápido demais e isso reduzirá os benefícios de usar o cache. Se o período for definido com um valor excessivamente longo, você correrá o risco de os dados se tornarem obsoletos.

Também é possível que o cache fique cheio, caso os dados possam permanecer armazenados ali por um longo tempo. Nesse caso, todas as solicitações para adicionar novos itens no cache podem causar a retirada forçada de alguns itens, em um processo conhecido como remoção. Os serviços de cache normalmente dão preferência à remoção de dados LRU (usados menos recentemente), mas geralmente você pode substituir essa política e impedir que determinados itens sejam removidos. No entanto, se você adotar essa abordagem, haverá risco de seu cache exceder a memória disponível e qualquer aplicativo que tentar adicionar um item ao cache falhará, gerando uma exceção.

Algumas implementações de caching podem fornecer políticas de remoção adicionais. Normalmente, elas incluem a política “usados mais recentemente” (na expectativa de que os dados não sejam necessários novamente), a política primeiro a entrar, primeiro a sair (dados mais antigos são removidos primeiro), ou ainda a remoção explícita com base em um evento disparado (como a modificação dos dados).

### Invalidando dados em um cache no lado do cliente

Dados armazenados em um cache no lado do cliente geralmente são considerados fora da responsabilidade do serviço que fornece os dados ao cliente; um serviço não pode forçar diretamente um cliente a adicionar ou remover informações de um cache no lado do cliente. Isso significa que é possível que um cliente que usa um cache mal configurado (por exemplo, com políticas de expiração implementadas incorretamente) para continuar usando informações desatualizadas armazenadas em cache localmente quando as informações na fonte de dados original tiverem sido alteradas.

Se você estiver criando um aplicativo Web que serve de dados em uma conexão HTTP, você pode forçar implicitamente um cliente Web (como um navegador ou proxy da Web) a coletar as informações mais recentes, se um recurso for atualizado, alterando o URI desse recurso. Clientes Web normalmente usam o URI de um recurso como a chave no cache do lado do cliente, portanto, alterar o URI faz com que o cliente Web ignore quaisquer versões de um recurso previamente armazenadas em cache e, em vez disso, busque a nova versão.

## Gerenciando simultaneidade em um cache

Caches são frequentemente projetados para serem compartilhados por várias instâncias de um aplicativo. Cada instância do aplicativo pode ler e modificar dados no cache. Consequentemente, os mesmos problemas de simultaneidade que surgem com qualquer repositório de dados compartilhado também são aplicáveis a um cache. Em uma situação em que um aplicativo precisa modificar dados mantidos no cache, talvez você precise garantir que as atualizações feitas por uma instância do aplicativo não substitua cegamente as alterações feitas por outra instância.

Dependendo da natureza dos dados e da probabilidade de colisões, você pode adotar uma dessas duas abordagens para simultaneidade:

- __Otimista.__ O aplicativo verifica, imediatamente antes de atualizar os dados no cache, se eles sofreram alterações desde que foram recuperados. Se os dados ainda são os mesmos, a alteração pode ser feita. Caso contrário, o aplicativo precisa decidir se deseja atualizá-los (a lógica de negócios que orienta essa decisão será específica para cada aplicativo). Essa abordagem é adequada para situações em que as atualizações são frequentes ou em que a ocorrência de colisões é improvável.
- __Pessimista.__ O aplicativo bloqueia os dados no cache quando recupera-os para impedir que outra instância altere esses dados. Esse processo garante que colisões não ocorrerão, mas pode bloquear outras instâncias que precisem processar os mesmos dados. Simultaneidade pessimista pode afetar a escalabilidade da solução e deve ser usada apenas para operações de curta duração. Essa abordagem pode ser apropriada para situações em que há maior probabilidade de colisões, especialmente se um aplicativo atualiza vários itens no cache e precisa garantir que essas alterações sejam aplicadas de modo consistente.

### Implementando alta disponibilidade e escalabilidade e melhorando o desempenho

Um cache não deve ser o repositório principal dos dados; essa é a função do repositório de dados original por meio do qual o cache é populado. O repositório de dados original é responsável por garantir a persistência dos dados.

Tenha cuidado para não introduzir, em suas soluções, dependências críticas sobre a disponibilidade de um serviço de cache compartilhado. Um aplicativo deve ser capaz de continuar funcionando se o serviço que fornece o cache compartilhado não está disponível; o aplicativo não deve parar ou falhar enquanto aguarda o andamento do serviço de cache ser retomado. Portanto, o aplicativo deve estar preparado para detectar a disponibilidade do serviço de cache e voltar para o repositório de dados original se o cache estiver inacessível. O [Padrão de Disjuntor](http://msdn.microsoft.com/library/dn589784.aspx) é útil para lidar com esse cenário. O serviço que fornece o cache pode ser recuperado e, depois que ele se torna disponível, o cache pode ser populado novamente, já que os dados são lidos no repositório de dados original, seguindo uma estratégia como o [Padrão Cache-Aside](http://msdn.microsoft.com/library/dn589799.aspx).

No entanto, voltar para o repositório de dados original se o cache estiver temporariamente indisponível pode ter um impacto de escalabilidade sobre o sistema; enquanto o armazenamento de dados está sendo recuperado, o repositório de dados original pode ser inundado por solicitações de dados, resultando em tempos limite esgotados e conexões com falha. Uma estratégia que você deve considerar é implementar um cache local e privado em cada instância de um aplicativo junto com o cache compartilhado que todas as instâncias do aplicativo acessam. Quando o aplicativo recupera um item, ele pode verificar primeiro em seu cache local, em seguida no cache compartilhado e, finalmente, no repositório de dados original. O cache local pode ser populado usando os dados no cache compartilhado ou aqueles no banco de dados, se o cache compartilhado não estiver disponível. Essa abordagem requer configuração cuidadosa para impedir que o cache local se torne muito desatualizado em relação ao cache compartilhado, mas o cache local atua como um buffer se o cache compartilhado está inacessível. A Figura 3 mostra essa estrutura.

![Usando um cache local e privado com um cache compartilhado\_](media/best-practices-caching/Caching3.png) _Figura 3: Usando um cache local e privado com um cache compartilhado_

Para dar suporte a caches grandes que mantêm dados de vida útil relativamente longa, alguns serviços de cache fornecem uma opção de alta disponibilidade, que implementa o failover automático se o cache fica indisponível. Essa abordagem geralmente envolve a replicação dos dados em cache armazenados em um servidor de cache primário para um servidor de cache secundário, alternando para o servidor secundário se o servidor primário falha ou se a conectividade é perdida. Quando dados são gravados em cache no servidor primário, a replicação para o servidor secundário pode ocorrer de modo assíncrono para reduzir a latência associada a gravar em múltiplos destinos. Essa abordagem leva à possibilidade de algumas informações em cache serem perdidas em caso de falha, mas a proporção desses dados deve ser pequena em comparação com o tamanho geral do cache.

Se um cache compartilhado for grande, pode ser benéfico particionar os dados em cache em nós para reduzir as chances de contenção e melhorar a escalabilidade. Em vários caches compartilhados, há suporte para a capacidade de adicionar dinamicamente (e remover) nós e rebalancear os dados em partições. Essa abordagem pode envolver clustering, por meio do qual a coleção de nós é apresentada a aplicativos cliente como um cache transparente, único, mas internamente os dados estão dispersos entre os nós após uma estratégia de distribuição predefinida, que equilibra a carga uniformemente. O [documento Diretrizes de Particionamento de Dados](http://msdn.microsoft.com/library/dn589795.aspx) no site da Microsoft fornece mais informações sobre possíveis estratégias de particionamento.

O clustering também pode adicionar mais disponibilidade de cache; se um nó falhar, o restante do cache ainda estará acessível. O clustering é usado frequentemente em conjunto com a replicação e o failover; cada nó pode ser replicado e, se o nó falhar, a réplica pode ser colocada online rapidamente.

Muitas operações de leitura e gravação provavelmente envolvem valores ou objetos únicos de dados. No entanto, pode haver ocasiões em que é necessário armazenar ou recuperar grandes volumes de dados rapidamente. Por exemplo, a propagação de um cache pode envolver a gravação de centenas ou milhares de itens no cache, ou então um aplicativo pode precisar recuperar um grande número de itens relacionados do cache como parte da mesma solicitação. Vários caches em grande escala oferecem operações em lote para esses fins, permitindo que um aplicativo cliente empacote um grande volume de itens em uma única solicitação e reduzindo a sobrecarga associada à realização de um grande número de solicitações pequenas.

## Caching e consistência eventual.

O padrão Cache-Aside depende de instância do aplicativo que está a popular o cache ter acesso à versão mais recente e consistente dos dados. Em um sistema que implementa a consistência eventual (como um repositório de dados replicados), isso não pode ser o caso. Uma instância de um aplicativo pode modificar um item de dados e invalidar a versão em cache desse item. Outra instância do aplicativo pode tentar ler este item no cache causando um erro de cache; por isso, ele lê os dados no repositório de dados e adiciona-os ao cache. No entanto, se o repositório de dados não foi totalmente sincronizado com as outras réplicas, a instância do aplicativo pode ler e popular o cache com o valor antigo.

Para obter mais informações sobre como lidar com consistência de dados, consulte a página Diretrizes de Consistência de Dados no site da Microsoft.

### Protegendo dados em cache

Independentemente do serviço de cache que você usar, você deve pensar em como proteger os dados mantidos no cache contra acesso não autorizado. Há dois problemas principais:

- A privacidade dos dados no cache.
- A privacidade dos dados que fluem entre o cache e o aplicativo usando o cache.

Para proteger os dados no cache, o serviço de cache pode implementar um mecanismo de autenticação exigindo que aplicativos se identifiquem, além de um esquema de autorização que especifique quais identidades podem acessar dados em cache e as operações (leitura e gravação) que essas identidades têm permissão para executar. Para reduzir os custos associados a leitura e gravação de dados, uma identidade pode usar todos os dados no cache após ter recebido acesso de leitura e/ou gravação nesse cache. Se for necessário restringir o acesso a subconjuntos dos dados armazenados em cache, você pode:

- Dividir o cache em partições (usando servidores de cache diferentes) e conceder às identidades o acesso somente às partições que elas devem ter permissão para usar, ou
- Criptografar os dados em cada subconjunto usando chaves diferentes e fornecer as chaves de criptografia apenas às identidades que devem ter acesso a cada subconjunto. Um aplicativo cliente pode ainda ser capaz de recuperar todos os dados no cache, mas ele só poderá descriptografar os dados para os quais tem as chaves.

Para proteger os dados conforme eles fluem para dentro e fora do cache, você depende dos recursos de segurança fornecidos pela infraestrutura de rede que aplicativos cliente usam para se conectar ao cache. Se o cache é implementado usando um servidor local dentro da mesma organização que hospeda os aplicativos cliente, pode ser que o isolamento da rede em si não exija etapas adicionais. Se o cache está localizado remotamente e requer uma conexão TCP ou HTTP por uma rede pública (como a Internet), você deve considerar possibilidade de implementar SSL.

## Considerações sobre como implementar caching com o Microsoft Azure

O Azure fornece o Cache Redis do Azure. Isso é uma implementação do software livre Cache Redis, que é executado como um serviço em um datacenter do Azure. Ele fornece um serviço de cache que pode ser acessado de qualquer aplicativo do Azure, seja o aplicativo implementado como um serviço de nuvem, um site, ou dentro de uma máquina virtual do Azure. Os caches podem ser compartilhados por aplicativos cliente que têm a chave de acesso apropriada.

O Redis é uma solução de caching de alto desempenho que oferece disponibilidade, escalabilidade e segurança. Normalmente, ele é executado como um serviço espalhado por um ou mais computadores dedicados e tenta armazenar tanta informação quanto possível na memória, para garantir acesso rápido. Essa arquitetura destina-se a oferecer baixa latência e alta taxa de transferência, reduzindo a necessidade de executar operações de E/S lentas.

O Cache Redis do Azure é compatível com muitas das diversas APIs usadas por aplicativos cliente. Se você tem aplicativos existentes que já usam o Redis em execução localmente, o Cache Redis do Azure oferece um caminho de migração rápido para caching na nuvem.

> [AZURE.NOTE]O Azure também fornece o Serviço de Cache Gerenciado. Esse serviço é baseado no mecanismo de Cache do AppFabric da Microsoft. Ele permite que você crie um cache distribuído que pode ser compartilhado por aplicativos vagamente vinculados. O cache é hospedado em servidores de alto desempenho em execução em um datacenter do Azure. No entanto, essa opção não é mais recomendada e é fornecida apenas para oferecer suporte a aplicativos existentes que foram criados para usá-la. Para todos os novos desenvolvimentos, use o Cache Redis do Azure em seu lugar.
>
> Além disso, o Azure oferece suporte a cache na função. Esse recurso permite que você crie um cache específico para um serviço de nuvem. O cache é hospedado por instâncias de uma função de trabalho ou função Web e só pode ser acessado por funções que operam como parte da mesma unidade de implantação de serviço de nuvem (uma unidade de implantação é o conjunto de instâncias de função implantadas como um serviço de nuvem para uma região específica). O cache é clusterizado, e todas as instâncias da função dentro da mesma unidade de implantação que hospeda o cache tornam-se parte do mesmo cluster de cache. Os aplicativos existentes que usam cache na função podem continuar a fazer isso, mas migrar para o Cache Redis do Azure pode trazer mais benefícios. Para obter mais informações sobre quando usar o Cache Redis do Azure ou um cache na função, visite a página [Qual oferta de Cache do Azure é a correta para mim?](http://msdn.microsoft.com/library/azure/dn766201.aspx), no site da Microsoft.


### Recursos do Redis

O Redis é mais do que um servidor de cache simples; ele fornece um banco de dados na memória distribuído com um conjunto abrangente de comandos, que oferece suporte a vários cenários comuns conforme descrito na seção Casos de uso para caching do Redis, posteriormente neste documento. Essa seção resume alguns dos principais recursos oferecidos pelo Redis.

### Redis como um banco de dados na memória

O Redis oferece suporte tanto a operações de leitura quanto de gravação. Ao contrário de muitos caches (que devem ser considerados como repositórios de dados transitórios), gravações podem ser protegidas contra falhas do sistema, seja armazenando-as periodicamente em um arquivo instantâneo local ou em um arquivo de log tipo “apenas acrescentar”. Todas as gravações são assíncronas e não bloqueiam a leitura e gravação de dados por clientes. Quando o Redis começa a ser executado, ele lê os dados do arquivo de instantâneo ou de log e usa-os para construir o cache na memória. Para obter mais informações, consulte [Persistência do Redis](http://redis.io/topics/persistence) no site do Redis.

> [AZURE.NOTE]O Redis não garante que todas as gravações serão salvas no caso de uma falha catastrófica, mas no pior caso possível, você deverá perder apenas os dados equivalentes a alguns segundos. Lembre-se de que um cache não se destina a agir como uma fonte de dados autoritativa, é responsabilidade dos aplicativos usando o cache garantir que os dados críticos sejam salvos com êxito em um repositório de dados apropriado. Para obter mais informações, consulte Padrão Cache-Aside.

#### Tipos de dados do Redis

Redis é um repositório de chave-valor, onde os valores podem conter tipos simples ou estruturas de dados complexas como hashes, listas e conjuntos. Ele oferece suporte a um conjunto de operações atômicas nesses tipos de dados. As chaves podem ser permanentes ou marcadas com um tempo limite de expiração, ponto em que a chave e seu valor correspondente são automaticamente removidos do cache. Para obter mais informações sobre chaves e valores do Redis, visite a página [Uma Introdução a abstrações e tipos de dados do Redis](http://redis.io/topics/data-types-intro) no site do Redis.

#### Replicação e clustering do Redis

O Redis permite replicação de mestres/subordinados, para ajudar a garantir a disponibilidade e manter a taxa de transferência; operações de gravação para um nó mestre Redis são replicadas para um ou mais nós subordinados, enquanto operações de leitura podem ser atendidas pelo mestre ou qualquer um dos subordinados. No caso de uma partição de rede, os subordinados podem continuar a fornecer dados e ressincronizar novamente com o mestre, de modo transparente, quando a conexão for restabelecida. Para obter mais detalhes, visite a página [Replicação](http://redis.io/topics/replication) no site do Redis.

O Redis também oferece clustering, permitindo que você distribua a carga e divida dados em fragmentos entre servidores de modo transparente. Esse recurso melhora a escalabilidade, já que novos servidores do Redis podem ser adicionados e os dados reparticionados conforme o tamanho do cache aumenta. Além disso, cada servidor no cluster pode ser replicado por meio da replicação de mestre/subordinados para garantir a disponibilidade por cada nó no cluster. Para obter mais informações sobre clustering e fragmentação, visite a [página Tutorial de Cluster Redis](http://redis.io/topics/cluster-tutorial) no site do Redis.

> [AZURE.NOTE]Atualmente, o Cache Redis do Azure não oferece suporte a clustering. Se você quiser criar um cluster do Redis, você pode criar seu próprio servidor Redis personalizado. Para obter mais informações, consulte a seção Criando um Cache Redis Personalizado, posteriormente neste documento.

### Uso da memória no Redis

Um cache Redis tem um tamanho limitado, dependendo dos recursos disponíveis no computador host. Quando configura um servidor Redis, você pode especificar a quantidade máxima de memória que ele pode usar. Uma chave em um cache Redis pode ser configurada com um tempo de expiração, após o qual ele é removido do cache automaticamente. Esse recurso pode ajudar a impedir que o cache de memória seja preenchido com dados antigos ou obsoletos.

Conforme a memória é preenchida, o Redis pode remover automaticamente as chaves e seus valores, seguindo uma série de políticas. O padrão é LRU (menos utilizado recentemente), mas você também pode selecionar outras políticas como remover chaves aleatoriamente ou desativar completamente a remoção (nesse caso, as tentativas de adicionar itens ao cache falharão se o cache estiver cheio). A página [Usando o Redis como um Cache LRU](http://redis.io/topics/lru-cache) fornece mais informações a respeito.

### Lotes e transações do Redis

O Redis habilita um aplicativo cliente a enviar uma série de operações que leem e gravam dados em cache como uma transação atômica. Todos os comandos na transação têm garantia de serem executados em sequência, e nenhum comando emitido por outros clientes simultâneos será colocado entre eles. No entanto, esses não são transações verdadeiras, já que seriam executadas por um banco de dados relacional. O processamento de transações consiste em dois estágios: enfileiramento de comandos e execução de comandos. Durante o estágio de enfileiramento de comandos, os comandos que compõem a transação são enviados pelo cliente. Se ocorrer algum tipo de erro neste momento (por exemplo, um erro de sintaxe ou número errado de parâmetros), o Redis se recusará a processar a transação inteira e a descartará. Durante a fase de execução, o Redis executa em sequência cada comando enfileirado. Se um comando falha durante essa fase, o Redis continua com o próximo comando na fila; ele não reverte os efeitos de todos os comandos que já foram executados. Esse modo simplificado de transação ajuda a manter o desempenho e evitar problemas de desempenho causados por contenção. O Redis implementa um modo de bloqueio otimista para ajudar a manter a consistência. Para obter informações detalhadas sobre as transações e bloqueio com o Redis, visite a [página Transações](http://redis.io/topics/transactions) no site do Redis.

O Redis também oferece suporte ao envio em lote não transacional de solicitações. O protocolo Redis, que os clientes usam para enviar comandos para um servidor Redis, habilita um cliente a enviar uma série de operações como parte da mesma solicitação. Isso pode ajudar a reduzir a fragmentação de pacotes na rede. Quando o lote é processado, cada comando é executado. Ao contrário do que ocorre em uma transação, se qualquer um desses comandos for malformado ele será rejeitado, mas os comandos restantes serão executados. Além disso, não há nenhuma garantia quanto à ordem em que os comandos em lote serão processados.

### Segurança no Redis

O Redis concentra-se puramente no fornecimento de acesso rápido aos dados, sendo projetado para execução em um ambiente confiável e para ser acessado somente por clientes confiáveis. O Redis oferece suporte somente a um modelo de segurança limitada com base em autenticação por senha (é possível remover a autenticação completamente, embora isso não seja recomendado). Todos os clientes autenticados compartilham a mesma senha global e têm acesso aos mesmos recursos. Se precisar de segurança de logon mais abrangente, você deverá implementar sua própria camada de segurança na frente do servidor do Redis e todas as solicitações de cliente devem passar por essa camada adicional; o Redis não deve ser exposto diretamente a clientes não confiáveis ou não autenticados.

Você pode restringir o acesso aos comandos desabilitando-os ou renomeando-os (e fornecendo os novos nomes apenas a clientes privilegiados).

O Redis não oferece suporte direto a nenhuma forma de criptografia de dados, portanto, toda codificação deve ser realizada por aplicativos cliente. Além disso, o Redis não fornece nenhuma forma de segurança no transporte, portanto, se precisar proteger os dados conforme eles fluem pela rede, você deverá implementar um proxy SSL.

Para obter mais informações, visite a página [Segurança do Redis](http://redis.io/topics/security) no site do Redis.

> [AZURE.NOTE]O Cache Redis do Azure fornece sua própria camada de segurança, por meio da qual os clientes se conectam; os servidores de Redis subjacentes não são expostos à rede pública.

### Usando o Cache Redis do Azure

O Cache Redis do Azure fornece acesso a servidores Redis executados em servidores hospedados em um datacenter do Azure; ele atua como uma fachada que fornece controle de acesso e segurança. Você pode provisionar um cache usando o Portal de Gerenciamento do Azure. O portal fornece várias configurações predefinidas, variando de um cache de 53 GB em execução como um serviço dedicado, que oferece suporte a comunicações SSL (para privacidade) e replicação de mestre/subordinados com uma disponibilidade de SLA de 99,9%, até um cache de 250 MB sem replicação (nenhuma garantia de disponibilidade) em execução em hardware compartilhado.

Usando o Portal de Gerenciamento do Azure, você também pode configurar a política de remoção do cache, além de controlar o acesso ao cache adicionando usuários às funções fornecidas; Proprietário, Colaborador, Leitor. Essas funções definem as operações que os membros podem executar. Por exemplo, os membros da função Proprietário têm controle completo sobre o cache (incluindo segurança) e seu conteúdo; os membros da função Colaborador podem ler e gravar informações no cache, enquanto os membros da função Leitor só podem recuperar dados por meio do cache.

A maioria das tarefas administrativas são executadas por meio do Portal de Gerenciamento do Azure e, por esse motivo, muitos dos comandos administrativos disponíveis na versão padrão do Redis não estão disponíveis, incluindo a capacidade de modificar a configuração programaticamente, desligar o servidor Redis, configurar servidores subordinados adicionais ou salvar dados em disco de modo forçado.

O Portal de Gerenciamento do Azure inclui uma exibição gráfica prática, que permite a você monitorar o desempenho do cache. Por exemplo, você pode exibir o número de conexões que estão sendo feitas, o número de solicitações realizadas, o volume de leituras e gravações e o número de ocorrências no cache versus perdas no cache. Usando essas informações você pode determinar a eficiência do cache e, se necessário, alternar para uma configuração diferente ou alterar a política de remoção. Além disso, você pode criar alertas que enviam mensagens de email para um administrador se uma ou mais métricas de tipo crítico estiverem fora de um intervalo esperado. Por exemplo, se o número de erros de cache na última hora exceder um valor especificado, um administrador poderia ser alertado, já que o cache pode ser pequeno demais ou os dados podem estar sendo removidos demasiadamente rápido.

Você também pode monitorar a CPU, memória e uso de rede para o cache.

> [AZURE.NOTE]O Cache Redis do Azure destina-se a atuar somente como um cache e não como um banco de dados. Como resultado, atualmente ele não implementa a persistência do Redis.

Para obter mais informações e exemplos que mostram como criar e configurar um Cache Redis do Azure, visite a página [Visão geral do Cache Redis do Azure](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/), no blog do Azure.

## Caching de estado de sessão e saída HTML

Se você criar aplicativos Web ASP.NET que são executados usando funções Web do Azure, você pode salvar informações de estado de sessão e de saída HTML em um Cache Redis do Azure. O provedor de estado de sessão de Cache Redis do Azure permite que você compartilhe informações de sessão entre diferentes instâncias de um aplicativo Web ASP .NET, e é muito útil em situações de Web farm em que a afinidade cliente-servidor não está disponível e o armazenamento de dados de sessão em cache na memória não seria apropriado.

Usar o Provedor de Estado de Sessão com o Cache Redis do Azure oferece vários benefícios, incluindo:

- ele pode compartilhar o estado de sessão entre um grande número de instâncias de um aplicativo Web ASP .NET, oferecendo maior escalabilidade;
- ele dá suporte acesso controlado e simultâneo aos mesmos dados de estado de sessão para múltiplos leitores e um único gravador;
- ele pode usar compactação para economizar memória e melhorar o desempenho da rede.

Para obter mais informações, visite a página [Provedor de Estado de Sessão ASP.NET para Cache Redis do Azure](http://msdn.microsoft.com/library/azure/dn690522.aspx), no site da Microsoft.

> [AZURE.NOTE]Não use o Provedor de Estado de Sessão para Cache Redis do Azure para aplicativos ASP.NET executados fora do ambiente do Azure. A latência de acessar o cache de fora do Azure pode eliminar os benefícios de desempenho obtidos pelo caching de dados.

De modo similar, o Provedor de Cache de Saída para Cache Redis do Azure permite que você salve as respostas HTTP geradas por um aplicativo Web ASP .NET. Usar o Provedor de Cache de Saída com o Cache Redis do Azure pode melhorar os tempos de resposta dos aplicativos que processam saída HTML complexa; instâncias do aplicativo gerando respostas semelhantes podem fazer uso dos fragmentos de saída compartilhados no cache em vez de gerar essa saída HTML novamente. Para obter mais informações, visite a página [Provedor de Cache de Saída ASP.NET para Cache Redis do Azure](http://msdn.microsoft.com/library/azure/dn798898.aspx), no site da Microsoft.

## Criando um cache Redis personalizado

O Cache Redis do Azure atua como uma fachada para os servidores Redis subjacentes. Atualmente ele oferece suporte a um conjunto fixo de configurações, mas não oferece suporte ao clustering do Redis. Se você precisa de uma configuração avançada não contemplada pelo Cache Redis do Azure (como um cache maior que 53 GB), você pode criar e hospedar seus próprios servidores Redis usando máquinas virtuais do Azure. Esse é um processo potencialmente complexo, já que, se você deseja implementar replicação, talvez seja necessário criar várias VMs para atuarem como nós mestres e subordinados. Além disso, se você quiser criar um cluster, precisará de vários servidores mestres e subordinados; uma topologia de replicação mínima clusterizada, que oferece um alto grau de disponibilidade e escalabilidade, consiste de pelo menos 6 VMs organizadas como 3 pares de servidores mestre/subordinado (um cluster deve conter pelo menos 3 nós mestres). Os servidores em cada par de mestre/subordinado devem estar localizados juntos para minimizar a latência; no entanto, cada conjunto de pares pode ser executado em diferentes datacenters do Azure localizados em diferentes regiões, caso você deseje posicionar os dados armazenados em cache perto dos aplicativos que têm mais probabilidade de usá-los. A página [Executando o Redis em uma VM com CentOS Linux no Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) no site da Microsoft explica um exemplo que mostra como criar e configurar um nó do Redis executado como uma VM do Azure.

Observe que, se implementar seu próprio cache Redis desse modo, você será responsável por monitorar, gerenciar e proteger o serviço.

## Particionando um cache Redis

O particionamento de cache envolve dividir o cache por diversos computadores. Essa estrutura oferece várias vantagens em relação ao uso de um único servidor de cache, incluindo:

- Criação de um cache muito maior do que o que é possível armazenar em um único servidor.
- Distribuição de dados entre servidores, melhorando a disponibilidade. Se um servidor falha ou fica inacessível, somente os dados que ele contém ficam indisponíveis; os dados nos servidores restantes ainda podem ser acessados. Para um cache isso não é crucial, já que os dados armazenados em cache são apenas uma cópia temporária dos dados mantidos em um banco de dados; além disso, dados colocados em cache em um servidor que se torna inacessível podem ser, em vez disso, armazenados em cache em um servidor diferente.
- Distribuir a carga entre servidores, o que melhora o desempenho e escalabilidade.
- Manter os dados geograficamente próximos dos usuários que os acessam, reduzindo a latência.

Para um cache, o modo mais comum de particionamento é a fragmentação. Nessa estratégia, cada partição (ou fragmento) é um cache Redis por si só. Os dados são direcionados para uma partição específica usando a lógica de fragmentação, que pode usar diversas abordagens para distribuir os dados. O [Padrão de Fragmentação](http://msdn.microsoft.com/library/dn589797.aspx) fornece mais informações sobre como implementar a fragmentação.

Para implementar o particionamento em um cache Redis, você pode adotar uma das abordagens a seguir:

- _Roteamento de consulta no lado do servidor._ Nessa técnica, um aplicativo cliente envia uma solicitação para qualquer um dos servidores Redis que compõem o cache (provavelmente o servidor mais próximo). Cada servidor Redis armazena metadados que descrevem a partição contida nele, além de conter informações sobre quais partições estão localizadas em outros servidores. O servidor Redis examina a solicitação do cliente e, se ela puder ser resolvida localmente, ele executará a operação solicitada; caso contrário, ele encaminhará a solicitação para o servidor adequado. Esse modelo é implementado usando o clustering do Redis e é descrito mais detalhadamente na página [Tutorial de cluster do Redis](http://redis.io/topics/cluster-tutorial), no site do Redis. O clustering do Redis é transparente para os aplicativos cliente e outros servidores do Redis podem ser adicionados ao cluster (e os dados particionados novamente) sem a necessidade de reconfigurar os clientes.

  >[AZURE.IMPORTANT]Atualmente, o Cache Redis do Azure não dá suporte ao clustering do Redis. Se você quiser implementar essa abordagem, deve criar um cache Redis personalizado conforme descrito anteriormente.

- _Particionamento no lado do cliente._ Nesse modelo, o aplicativo cliente contém uma lógica (possivelmente na forma de uma biblioteca) que encaminha solicitações para o servidor Redis apropriado. Essa abordagem pode ser usada com Cache Redis do Azure; crie vários Caches Redis do Azure (um para cada partição de dados) e implemente a lógica no lado do cliente que encaminha as solicitações para o cache correto. Se o esquema de particionamento for alterado (por exemplo, se outros Caches Redis do Azure forem criados), os aplicativos cliente precisarão ser reconfigurados.

- _Particionamento assistido por proxy._ Nesse esquema, aplicativos cliente enviam solicitações para um serviço de proxy intermediário que compreende como os dados são particionados e então encaminha a solicitação para o servidor Redis apropriado. Essa abordagem também pode ser usada com o Cache Redis do Azure; o serviço de proxy pode ser implementado como um serviço de nuvem do Azure. Essa abordagem exige um nível adicional de complexidade para implementar o serviço e as solicitações podem levar mais tempo do que o particionamento no lado do cliente.

A página [Particionamento: como dividir dados entre várias instâncias do Redis](http://redis.io/topics/partitioning) no site do Redis fornece mais informações sobre a implementação do particionamento com o Redis.

### Implementando aplicativos de cliente de cache Redis

O Redis oferece suporte a aplicativos cliente escritos em numerosas linguagens de programação. Se você está criando novos aplicativos usando o .NET Framework, a abordagem recomendada é usar a biblioteca de cliente Stackexchange.Redis. Esta biblioteca fornece um modelo de objeto do .NET Framework que abstrai os detalhes para conectar-se a um servidor Redis, enviar comandos e receber respostas. Ela está disponível no Visual Studio como um pacote NuGet. Você pode usar essa mesma biblioteca para se conectar a um cache Redis do Azure, ou a um cache Redis personalizado hospedado em uma máquina virtual.

Para se conectar a um servidor do Redis, você utiliza o método `Connect` estático da classe `ConnectionMultiplexer`. A conexão que cria esse método é projetada para ser usada durante a vida útil do aplicativo cliente, e a mesma conexão pode ser usada por vários threads simultâneos; não reconecte-se e desconecte-se cada vez que você executar uma operação do Redis, já que isso pode prejudicar o desempenho. Você pode especificar os parâmetros de conexão, como o endereço do host Redis e a senha. Se você estiver usando o Cache Redis do Azure, a senha será a chave primária ou então a chave secundária gerada para o Cache Redis do Azure usando o Portal de Gerenciamento do Azure.

Depois de conectar-se ao servidor Redis, você pode obter um identificador no banco de dados Redis que atua como o cache. A conexão Redis oferece o método `GetDatabase` para fazer isso. Você pode recuperar itens do cache e armazenar dados nele usando os métodos `StringGet` e `StringSet`. Esses métodos esperam uma chave como um parâmetro, e retornam o item em cache com um valor correspondente (`StringGet`) ou então adicionam o item com essa chave (`StringSet`) ao cache.

Dependendo do local do servidor Redis, muitas operações podem causar alguma latência enquanto uma solicitação é transmitida para o servidor e uma resposta é retornada ao cliente. A biblioteca do StackExchange fornece versões assíncronas de muitos dos métodos que ela expõe para ajudar aplicativos cliente a permanecerem responsivos. Esses métodos oferecem suporte ao [Padrão Assíncrono Baseado em Tarefa](http://msdn.microsoft.com/library/hh873175.aspx) no .NET Framework.

O trecho de código a seguir mostra um método denominado `RetrieveItem`, que ilustra um exemplo de uma implementação do padrão cache-aside com base em Redis e na biblioteca do StackExchange. O método utiliza um valor de chave de cadeia de caracteres e tenta recuperar o item correspondente do cache do Redis, chamando o método `StringGetAsync` (a versão assíncrona do `StringGet`). Se o item não for encontrado, ele é extraído da fonte de dados subjacente usando o método `GetItemFromDataSourceAsync` (que é um método local e não faz parte da biblioteca do StackExchange) e, em seguida, adicionado ao cache usando o método `StringSetAsync` para que, da próxima vez, esse item possa ser recuperado mais rapidamente.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

Os métodos `StringGet` e `StringSet` não são restringem-se a recuperar ou armazenar valores de cadeia de caracteres; eles podem executar qualquer item serializado como uma matriz de bytes. Se você precisa salvar um objeto .NET, pode serializá-lo como um fluxo de bytes e usar o método StringSet para gravá-lo em cache. De modo similar, você pode ler um objeto no cache usando o método StringGet e desserializá-lo como um objeto .NET. O código a seguir mostra um conjunto de métodos de extensão para a interface IDatabase (o método GetDatabase de uma conexão Redis retorna um objeto `IDatabase`) e algum código de exemplo que usa esses métodos para ler e gravar um objeto BlogPost no cache:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

O código a seguir ilustra um método chamado `RetrieveBlogPost` que usa esses métodos de extensão para ler e gravar um objeto serializável `BlogPost` no cache, seguindo o padrão cache-aside:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

O Redis oferece suporte ao pipelining de comandos, caso um aplicativo cliente envie múltiplas solicitações assíncronas. O Redis pode multiplexar as solicitações usando a mesma conexão, em vez de receber os comandos e respondê-los em uma sequência estrita. Essa abordagem ajuda a reduzir a latência, fazendo uso mais eficiente da rede. O trecho de código a seguir mostra um exemplo que obtém os detalhes de dois clientes simultaneamente. O código envia duas solicitações e, em seguida, executa algum outro processamento (não mostrado) antes de esperar para receber os resultados. O método Wait do objeto de cache é semelhante ao método Task.Wait do .NET Framework:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

A página [Desenvolver para o Cache Redis do Azure](http://msdn.microsoft.com/library/azure/dn690520.aspx) no site da Microsoft fornece mais informações sobre como escrever aplicativos cliente capazes de usar o Cache Redis do Azure. Informações adicionais estão disponíveis na [página Uso Básico](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) no site da Stackexchange e na página [Pipelines e Multiplexadores](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md), no mesmo site fornece mais informações sobre operações assíncronas e pipelining com Redis e a biblioteca do StackExchange. A seção Casos de Uso para Caching do Redis, posteriormente nestas diretrizes, fornece exemplos de algumas das técnicas mais avançadas que você pode aplicar aos dados armazenados em um cache do Redis.

## Casos de Uso para Caching do Redis

O uso mais simples de Redis para armazenar em cache relaciona-se a armazenar pares chave/valor nos quais o valor é uma cadeia de caracteres não interpretada de comprimento arbitrário, que pode conter quaisquer dados binários (é essencialmente uma matriz de bytes que pode ser tratada como uma cadeia de caracteres). Esse cenário foi ilustrado na seção Implementando Aplicativos de Cliente de Cache Redis, anteriormente nestas diretrizes. Observe que as chaves também contêm dados não interpretados, portanto, você pode usar qualquer informação binária como chave, embora quanto mais longa a chave, mais espaço será necessário para armazená-la e mais tempo levará para executar operações de pesquisa. Para facilidade de uso e de manutenção, projetar seu keyspace com cuidado e use as chaves significativas (mas não detalhadas). Por exemplo, use chaves estruturadas, como "cliente:100" para representar a chave para o cliente com ID 100, em vez de utilizar simplesmente "100". Esse esquema permite distinguir facilmente entre valores que armazenam tipos de dados diferentes. Por exemplo, você também pode usar a chave "pedidos:100" para representar a chave para o pedido com ID 100.

Além de cadeias de caracteres binárias unidimensionais, um valor em um par chave/valor do Redis também pode conter informações mais estruturadas, incluindo listas, conjuntos (classificados e não classificados) e hashes. O Redis oferece um conjunto abrangente de comandos capazes de manipular esses tipos e muitos desses comandos estão disponíveis para aplicativos do .NET Framework, por meio de uma biblioteca de cliente como StackExchange. A página [Uma introdução aos tipos de dados do Redis e abstrações](http://redis.io/topics/data-types-intro) no site do Redis fornece uma visão geral mais detalhada desses tipos e os comandos que você pode usar para manipulá-los.

Esta seção resume alguns casos de uso comuns para esses comandos e tipos de dados.

### Executando operações atômicas e em lote

O Redis oferece suporte a uma série de operações atômicas de obtenção e definição de valores de cadeia de caracteres. Essas operações removem os riscos de corrida possíveis que podem ocorrer ao usar comandos `GET` e `SET` separados. As operações disponíveis incluem:

- `INCR`, `INCRBY`, `DECR` e `DECRBY`, que executam operações atômicas de acréscimo e decréscimo em valores de dados numéricos inteiros. A biblioteca do StackExchange fornece versões sobrecarregadas dos métodos `IDatabase.StringIncrementAsync` e `IDatabase.StringDecrementAsync` para executar essas operações e retornam o valor resultante armazenado no cache. O trecho de código a seguir ilustra como usar estes métodos:

  ```csharp ConnectionMultiplexer redisHostConnection = ...; IDatabase cache = redisHostConnection.GetDatabase(); ... await cache.StringSetAsync("data:counter", 99); ... long oldValue = await cache.StringIncrementAsync("data:counter"); // Increment by 1 (the default) // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50); // Decrement by 50 // newValue should be 50 ```

- `GETSET` que recupera o valor associado a uma chave e altera-o para um novo valor. A biblioteca do StackExchange disponibiliza essa operação por meio do método `IDatabase.StringGetSetAsync`. O trecho de código a seguir mostra um exemplo desse método. Esse código retorna o valor atual associado à chave "data:counter" do exemplo anterior e redefine o valor desta chave de volta a zero, tudo como parte da mesma operação:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` e `MSET`, que podem retornar ou alterar um conjunto de valores de cadeia de caracteres como uma única operação. Os métodos `IDatabase.StringGetAsync` e `IDatabase.StringSetAsync` são sobrecarregados para oferecerem suporte a essa funcionalidade, conforme mostrado no exemplo a seguir:

  ```csharp ConnectionMultiplexer redisHostConnection = ...; IDatabase cache = redisHostConnection.GetDatabase(); ... // Create a list of key/value pairs var keysAndValues = new List<KeyValuePair<RedisKey  RedisValue>>() { new KeyValuePair<RedisKey  RedisValue>("data:key1", "value1"), new KeyValuePair<RedisKey  RedisValue>("data:key99", "value2"), new KeyValuePair<RedisKey  RedisValue>("data:key322", "value3") };

  // Store the list of key/value pairs in the cache cache.StringSet(keysAndValues.ToArray()); ... // Find all values that match a list of keys RedisKey keys = { "data:key1", "data:key99", "data:key322"}; RedisValue values = null; values = cache.StringGet(keys); // values should contain { "value1", "value2", "value3" } ```

Você também pode combinar várias operações em uma única transação Redis conforme descrito na seção Lotes e Transações do Redis, nestas diretrizes. A biblioteca do StackExchange fornece suporte para transações por meio da interface `ITransaction`. Você pode criar um objeto ITransaction usando o método IDatabase.CreateTransaction e invocar comandos na transação usando os métodos fornecidos no objeto `ITransaction`. A interface `ITransaction` fornece acesso a um conjunto de métodos similar àquele da interface `IDatabase`, exceto pelo fato de que todos os métodos são assíncronos; eles só são executados quando o método `ITransaction.Execute` é chamado. O valor retornado pelo método Execute indica se a transação foi criada com êxito (true) ou falhou (false).

O trecho de código a seguir mostra um exemplo em que ocorre incremento e decremento a dois contadores como parte da mesma transação:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Lembre-se de que as transações de Redis são diferentes de transações em bancos de dados relacionais. O método Execute simplesmente coloca em fila todos os comandos que compõem a transação para execução e, se algum deles está malformado, a transação é abortada. Se todos os comandos foram enfileirados com êxito, cada comando será executado de modo assíncrono. Se algum comando falhar, o processamento dos outros ainda continuará. Se você precisar verificar se um comando foi concluído com êxito, você deve buscar os resultados do comando usando a propriedade Result da tarefa correspondente, conforme mostrado no exemplo acima. A leitura da propriedade Result estará bloqueada até que a tarefa tenha sido concluída.

Para obter mais informações, consulte a página [Transações em Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) no site Stackexchange.Redis.

Para executar operações em lote, você pode usar a interface IBatch da biblioteca do StackExchange. Essa interface fornece acesso a um conjunto de métodos similar àquele da interface IDatabase, exceto pelo fato de que todos os métodos são assíncronos. Você cria um objeto IBatch usando o método IDatabase.CreateBatch e, em seguida, executa o lote usando o método IBatch.Execute, conforme mostrado no exemplo a seguir. Esse código simplesmente define um valor de cadeia de caracteres, incrementa e decrementa os mesmos contadores usados no exemplo anterior e exibe os resultados:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

É importante entender que ao contrário de uma transação, se um comando em um lote falhar por estar malformado, outros comandos ainda podem ser executados. o método IBatch.Execute não retorna nenhuma indicação de sucesso ou falha.

### Executando operações de cache do tipo disparar e esquecer

O Redis oferece suporte a operações do tipo disparar e esquecer usando sinalizadores de comando. Nessa situação, o cliente simplesmente inicia uma operação, mas não tem interesse no resultado e não espera até o comando ser concluído. O exemplo a seguir mostra como executar o comando INCR como uma operação de disparar e esquecer:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### Expirando chaves automaticamente

Quando você armazena um item em um cache do Redis, você pode especificar um tempo limite após o qual o item será removido automaticamente do cache. Você também pode consultar quanto tempo uma chave ainda tem antes de expirar, usando o comando `TTL`; esse comando está disponível para aplicativos StackExchange pelo uso do método IDatabase.KeyTimeToLive.

O trecho de código a seguir mostra um exemplo de configuração de um tempo de expiração de 20 segundos em uma chave, e consulta do ciclo de vida restante da chave:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

Você também pode definir o horário de expiração para uma data e hora específicos usando o comando EXPIRE, disponível na biblioteca do StackExchange como o método KeyExpireAsync:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Dica:_ você pode remover manualmente um item do cache usando o comando DEL, que está disponível por meio da biblioteca do StackExchange como o método IDatabase.KeyDeleteAsync.

### Usando marcas para correlação cruzada entre itens em cache

Um conjunto do Redis é uma coleção de vários itens que compartilham uma única chave. Você pode criar um conjunto usando o comando SADD. Você pode recuperar os itens em um conjunto usando o comando SMEMBERS. A biblioteca do StackExchange implementa o comando SADD por meio do método IDatabase.SetAddAsync, e o comando SMEMBERS com o método IDatabase.SetMembersAsync. Você também pode combinar conjuntos existentes para criar novos conjuntos usando os comandos SUNION (união de conjunto), SINTER (interseção de conjunto) e SDIFF (diferença de conjunto). A biblioteca do StackExchange unifica essas operações no método IDatabase.SetCombineAsync; o primeiro parâmetro para esse método especifica a operação de conjunto a realizar.

Os trechos de código a seguir mostram como conjuntos podem ser úteis para armazenar e recuperar coleções de itens relacionados rapidamente. Esse código usa o tipo BlogPost, descrito na seção Implementando Aplicativos de Cliente de Cache do Redis. Um objeto BlogPost contém quatro campos — uma ID, um título, uma pontuação de classificação e uma coleção de marcas. O primeiro trecho de código abaixo mostra os dados de exemplo usados para popular uma lista em C# com objetos BlogPost:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

Você pode armazenar as marcas para cada objeto BlogPost como um conjunto em um cache do Redis e associar cada conjunto à ID do BlogPost. Isso habilita um aplicativo a localizar rapidamente todas as marcas que pertencem a uma postagem de blog específica. Para habilitar a pesquisa na direção oposta e localizar todas as postagens de blog que compartilham uma marca específica, você pode criar outro conjunto contendo postagens de blog que fazem referência à ID de marca na chave:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Essas estruturas permitem executar muitas consultas comuns de maneira muito eficiente. Por exemplo, você pode encontrar e exibir todas as marcas para a postagem de blog 1 desse modo:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Você pode encontrar todas as marcas que são comuns às postagens de blog 1 e 2 executando uma operação de interseção de conjunto, da seguinte maneira:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

E você pode encontrar todas as postagens de blog que contêm uma marca específica:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### Localizando itens acessados recentemente

Um problema comum necessário para muitos aplicativos é localizar os itens acessados mais recentemente. Por exemplo, um site de blog talvez queira exibir informações sobre as postagens de blog lidas mais recentemente. Você pode implementar essa funcionalidade usando uma lista do Redis. Uma lista do Redis contém vários itens que compartilham a mesma chave, mas a lista atua como uma fila duas extremidades. Usando os comandos LPUSH (deslocar para a esquerda) e RPUSH (deslocar para a direita), você pode enviar itens para ambas as extremidades da lista. Você pode recuperar itens de qualquer das duas extremidades da lista usando os comandos LPOP e RPOP. Você também pode retornar um conjunto de elementos usando os comandos LRANGE e RRANGE. Os trechos de código a seguir mostram como você pode executar essas operações usando a biblioteca do StackExchange. Esse código usa o tipo BlogPost dos exemplos anteriores. Conforme uma postagem de blog é lida por um usuário, o título da postagem do blog é deslocado pela lista associada à chave "blog:recent\_posts" no cache do Redis, usando o método IDatabase.ListLeftPushAsync:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

Conforme mais postagens do blog são lidas, seus títulos são deslocados pela mesma lista. A lista é ordenada pela sequência na qual eles foram adicionados; as postagens de blog lidas mais recentemente ficam na direção da extremidade esquerda da lista (se a mesma postagem do blog é lida mais de uma vez, ela terá múltiplas entradas na lista). Você pode exibir os títulos das postagens lidas mais recentemente usando o método IDatabase.ListRange. Este método usa a chave que contém a lista, um ponto inicial e um ponto final. O código a seguir recupera os títulos das 10 postagens de blog (itens de 0 a 9) posicionadas mais à esquerda na lista:

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Observe que ListRangeAsync não remove itens da lista; para fazer isso, você pode usar os métodos IDatabase.ListLeftPopAsync e IDatabase.ListRightPopAsync.

Para impedir que a lista aumente indefinidamente, você pode retirar itens periodicamente, encurtando a lista. O trecho de código abaixo remove todos os itens da lista exceto os 5 posicionados mais à esquerda:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### Implementando uma placa líder

Por padrão, os itens em um conjunto não são mantidos em nenhuma ordem específica. Você pode criar um conjunto ordenado, usando o comando ZADD (o método IDatabase.SortedSetAdd na biblioteca do StackExchange). Os itens são ordenados por meio de um valor numérico chamado de pontuação, fornecido como um parâmetro para o comando. O trecho de código a seguir adiciona o título de uma postagem de blog a uma lista ordenada. No exemplo, cada publicação de blog também tem um campo de pontuação que contém a classificação da postagem do blog.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Você pode recuperar os títulos de postagens de blog em ordem crescente segundo a pontuação usando o método IDatabase.SortedSetRangeByRankWithScores:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE]A biblioteca do StackExchange também fornece o método IDatabase.SortedSetRangeByRankAsync que retorna os dados em ordem de pontuação, mas não retorna as pontuações.

Você também pode recuperar itens em ordem decrescente segundo suas pontuações e limitar o número de itens retornados, fornecendo parâmetros adicionais ao método IDatabase.SortedSetRangeByRankWithScoresAsync. O exemplo a seguir exibe os títulos e pontuações das 10 postagens de blog com melhor classificação:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

O exemplo a seguir usa o método IDatabase.SortedSetRangeByScoreWithScoresAsync, que você pode usar para limitar os itens retornados àqueles cuja pontuação se encontra em um determinado intervalo:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### Envio de mensagens por meio de canais

Além de atuar como um cache de dados, um servidor Redis oferece envio de mensagens por meio de um mecanismo de editor/assinante de alto desempenho. Aplicativos cliente podem se inscrever em um canal, enquanto outros aplicativos ou serviços podem publicar mensagens no canal. Aplicativos inscritos receberão então essas mensagens e poderão processá-las.

Para inscrever-se em canais, o Redis oferece o comando SUBSCRIBE. Esse comando espera que o nome de um ou mais canais no qual o aplicativo aceitará mensagens. A biblioteca do StackExchange inclui a interface ISubscription, que habilita um aplicativo do .NET Framework a inscrever-se e publicar em canais. Você cria um objeto ISubscription pelo uso do método GetSubscriber da conexão com o servidor do Redis, então escuta mensagens em um canal usando o método SubscribeAsync desse objeto. O exemplo de código a seguir mostra como assinar um canal denominado "messages:blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

O primeiro parâmetro para o método Subscribe é o nome do canal. Esse nome segue as mesmas convenções usadas por chaves no cache e pode conter quaisquer dados binários, embora seja recomendável usar cadeias de caracteres relativamente curtas e significativas para ajudar a garantir o bom desempenho e a capacidade de manutenção. Observe também que o namespace usado pelos canais é separado daquele usada por chaves, portanto, você pode ter canais e chaves que tenham o mesmo nome, embora isso possa tornar o código de seu aplicativo mais difícil de manter.

O segundo parâmetro é um delegado Action. Esse delegado é executado assincronamente sempre que uma nova mensagem aparece no canal. Este exemplo simplesmente exibe a mensagem no console (a mensagem conterá o título de uma postagem de blog).

Para publicar em um canal, um aplicativo pode usar o comando PUBLISH do Redis. A biblioteca do StackExchange fornece o método IServer.PublishAsync para executar esta operação. O próximo trecho de código mostra como publicar uma mensagem no canal "messages:blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Há vários pontos que você deve compreender sobre o mecanismo de publicação/assinatura:

- Vários assinantes podem se inscrever no mesmo canal e todos eles receberão as mensagens publicadas nesse canal.
- Os assinantes só recebem mensagens que foram publicadas depois de sua inscrição. Canais não são armazenados em buffer, e assim que uma mensagem é publicada, a infraestrutura de Redis envia a mensagem por push para cada assinante e em seguida remove-a.
- Por padrão, as mensagens são recebidas pelos assinantes na ordem em que são enviadas. Em um sistema muito ativo com um grande número de mensagens e muitos editores e assinantes, entrega sequencial garantida de mensagens pode diminuir o desempenho do sistema. Se cada mensagem é independente e a ordem é irrelevante, você pode habilitar processamento simultâneo pelo sistema Redis, que pode ajudar a melhorar a capacidade de resposta. Você pode obter isso em um cliente StackExchange definindo a PreserveAsyncOrder da conexão usada pelo assinante como false: ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## Diretrizes e Padrões Relacionados

O padrão a seguir também pode ser relevante para seu cenário ao implementar caching em seus aplicativos:

- [Padrão Cache-Aside](http://msdn.microsoft.com/library/dn589799.aspx): Este padrão descreve como carregar dados em um cache sob demanda por meio de um repositório de dados. Esse padrão também ajuda a manter a consistência entre os dados mantidos no cache e os dados no repositório de dados original.
- O [padrão de fragmentação](http://msdn.microsoft.com/library/dn589797.aspx) fornece informações sobre como implementar o particionamento horizontal, para ajudar a melhorar a escalabilidade ao armazenar e acessar grandes volumes de dados.

## Mais informações

- A página [Classe MemoryCache](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) no site da Microsoft.
- A página [Cache do Microsoft Azure](http://msdn.microsoft.com/library/windowsazure/gg278356.aspx) no site da Microsoft.
- A página [Qual oferta de Cache do Azure é a correta para mim?](http://msdn.microsoft.com/library/azure/dn766201.aspx) no site da Microsoft.
- A página [Modelo de Configuração](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) no site da Microsoft.
- A página [Padrão Assíncrono Baseado em Tarefa](http://msdn.microsoft.com/library/hh873175.aspx) no site da Microsoft.
- A página [Pipelines e Multiplexadores](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) no repositório GitHub StackExchange.Redis.
- A página [Persistência do Redis](http://redis.io/topics/persistence) no site do Redis.
- A [página Replicação](http://redis.io/topics/replication) no site do Redis.
- A página [Tutorial de Cluster Redis](http://redis.io/topics/cluster-tutorial) no site do Redis.
- A página [Particionamento: como dividir dados entre várias instâncias do Redis](http://redis.io/topics/partitioning) página no site do Redis.
- A página [Usando o Redis como um Cache LRU](http://redis.io/topics/lru-cache) no site do Redis.
- A [página Transações](http://redis.io/topics/transactions) no site do Redis.
- A página [Segurança do Redis](http://redis.io/topics/security) no site do Redis.
- A página [Visão geral do Cache Redis do Azure](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) no blog do Azure.
- A página [Executando o Redis em uma VM Linux do CentOS](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) no Azure, no site da Microsoft.
- A página [Provedor de Estado de Sessão ASP.NET para Cache Redis do Azure](http://msdn.microsoft.com/library/azure/dn690522.aspx), no site da Microsoft.
- A página [Provedor de Cache de Saída ASP.NET para Cache Redis do Azure](http://msdn.microsoft.com/library/azure/dn798898.aspx), no site da Microsoft.
- A página [Desenvolver para Cache Redis do Azure](http://msdn.microsoft.com/library/azure/dn690520.aspx) no site do Azure.
- A página [Uma Introdução a Abstrações e Tipos de Dados do Redis](http://redis.io/topics/data-types-intro), no site do Redis.
- A página [Uso Básico](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md), no site StackExchange.Redis.
- Para obter mais informações, consulte a página [Transações em Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) no repositório Stackexchange.Redis.
- O [Guia de Particionamento de Dados](http://msdn.microsoft.com/library/dn589795.aspx), no site da Microsoft.

<!---HONumber=Oct15_HO3-->