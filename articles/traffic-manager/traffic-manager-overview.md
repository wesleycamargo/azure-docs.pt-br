<properties 
   pageTitle="O que é o Gerenciador de Tráfego| Microsoft Azure"
   description="Este artigo o ajudará a entender o que é o Gerenciador de Tráfego e como ele funciona"
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
   ms.date="12/07/2015"
   ms.author="joaoma" />

# O que é o Gerenciador de Tráfego?

O Gerenciador de Tráfego do Microsoft Azure permite controlar a distribuição do tráfego do usuário para seus pontos de extremidade especificados, o que pode incluir serviços de nuvem do Azure, sites e outros pontos de extremidade. O Gerenciador de Tráfego funciona aplicando um mecanismo de políticas inteligente às consultas DNS (Domain Name System) para os nomes de domúnio de seus recursos da Internet. Seus serviços de nuvem ou sites do Azure podem ser executados em datacenters diferentes em todo o mundo.

O Gerenciador de Tráfego pode ajudá-lo a:

- **Melhorar a disponibilidade de aplicativos críticos** – o Gerenciador de Tráfego permite aumentar a disponibilidade de seus aplicativos críticos, monitorando seus pontos de extremidade no Azure e fornecendo recursos de failover automático quando um serviço de nuvem do Azure, site do Azure ou outro local é desativado.
- **Melhorar a capacidade de resposta de aplicativos de alto desempenho** – o Azure permite executar serviços de nuvem ou sites em datacenters espalhados pelo mundo inteiro. O Gerenciador de Tráfego pode melhorar a capacidade de resposta de seus aplicativos e os tempos de entrega de conteúdo direcionando os usuários finais para o ponto de extremidade com a menor latência de rede do cliente.
- **Atualizar e realizar a manutenção do serviço sem tempo de inatividade** – o Gerenciador de Tráfego dá suporte a cenários estendidos para a nuvem híbrida e implantações locais, incluindo os cenários de "expandir para nuvem", "migrar para nuvem" e "failover para nuvem". Para manutenção planejada, você desabilita o ponto de extremidade no Gerenciador de Tráfego e aguarda até que o ponto de extremidade conclua a manutenção de conexões existentes. Quando não houver mais tráfego para o ponto de extremidade, atualize o serviço nesse ponto de extremidade, teste-o e reative-o no Gerenciador de Tráfego. Isso o ajuda a manter e atualizar seus serviços sem tempo de inatividade para clientes.
- **Distribuição de tráfego para implantações grandes e complexas** – Com perfis aninhados do Gerenciador de Tráfego, nos quais um perfil do Gerenciador de Tráfego pode ter outro perfil do Gerenciador de Tráfego como um ponto de extremidade, você pode criar configurações para otimizar o desempenho e a distribuição para implantações maiores e mais complexas. Para obter mais informações, consulte [Perfis aninhados](#nested-profiles).

## Como funciona o Gerenciador de Tráfego

Quando você configura um perfil do Gerenciador de Tráfego, as configurações que você especifica fornecem ao Gerenciador de Tráfego as informações necessárias para determinar qual ponto de extremidade deve atender à solicitação com base em uma consulta DNS. Nenhum tráfego real de ponto de extremidade é roteado por meio do Gerenciador de Tráfego.

A *Figura 1* mostra como o Gerenciador de Tráfego direciona os usuários para um de um conjunto de pontos de extremidade. Os números na Figura 1 correspondem às descrições numeradas abaixo:

![Como funciona o Gerenciador de Tráfego](./media/traffic-manager-overview/IC740854.jpg)

**Figura 1**

1. **Tráfego do usuário para nome de domínio da empresa**: o cliente solicita informações usando o nome de domínio da empresa. A meta é resolver um nome DNS para um endereço IP. Os domínios da empresa devem ser reservados por meio de registros normais de nomes de domínio da Internet, que são mantidos fora do Gerenciador de Tráfego. Na Figura 1, o domínio da empresa de exemplo é *www.contoso.com*.
2. **Nome de domínio da empresa para nome de domínio do Gerenciador de Tráfego**: o registro de recurso DNS para o domínio da empresa aponta para um nome de domínio do Gerenciador de Tráfego mantido no Gerenciador de Tráfego do Azure. Isso é obtido por meio de um registro de recurso CNAME, que mapeia o nome de domínio da empresa para o nome de domínio do Gerenciador de Tráfego. No exemplo, o nome de domínio do Gerenciador de Tráfego é *contoso.trafficmanager.net*.
3. **Nome de domínio e perfil do Gerenciador de Tráfego**: o nome de domínio do Gerenciador de Tráfego faz parte do perfil do Gerenciador de Tráfego. O servidor DNS do usuário envia uma nova consulta DNS para o nome de domínio do Gerenciador de Tráfego (no nosso exemplo, *contoso.trafficmanager.net*), que é recebida pelos servidores de nome DNS do Gerenciador de Tráfego.
4. **Regras de perfil do Gerenciador de Tráfego processadas**: o Gerenciador de Tráfego usa o método de roteamento de tráfego e o monitoramento de status especificados para determinar qual ponto de extremidade do Azure ou outro ponto de extremidade deve atender à solicitação.
5. **Nome de domínio do ponto de extremidade enviado ao usuário**: o Gerenciador de Tráfego retorna um registro CNAME que mapeia o nome de domínio do Gerenciador de Tráfego para o nome de domínio do ponto de extremidade. O servidor DNS do usuário resolve o nome de domínio do ponto de extremidade para o endereço IP correspondente e envia-o ao usuário.
6. **Usuário chama o ponto de extremidade**: o usuário chama o ponto de extremidade retornado diretamente usando o seu endereço IP.

Como o domínio da empresa e o endereço IP resolvido são armazenados em cache no computador cliente, o usuário continua a interagir com o ponto de extremidade escolhido até que sua entrada de cache DNS local expire. É importante observar que o cliente DNS armazena em cache as entradas do host DNS pela duração de sua TTL (vida útil). A recuperação de entradas de host do cache do cliente DNS ignora o perfil do Gerenciador de Tráfego e poderá sofrer atrasos na conexão se o ponto de extremidade se tornar indisponível antes que a TTL expire. Se a TTL de uma entrada de host DNS no cache expirar e o computador cliente precisar resolver o nome de domínio da empresa novamente, ele enviará uma nova consulta DNS. O computador cliente pode receber o endereço IP de um ponto de extremidade diferente dependendo do método de roteamento de tráfego aplicado e da integridade dos pontos de extremidade no momento da solicitação.

## Como implementar o Gerenciador de Tráfego

A *Figura 2* mostra as etapas, em ordem, que são necessárias para implementar o Gerenciador de Tráfego. Essas etapas poderão ser executadas em uma ordem ligeiramente diferente depois que você tiver uma compreensão sólida das práticas recomendadas e da configuração do Gerenciador de Tráfego. Os números na Figura 2 correspondem às descrições numeradas abaixo:

![Como configurar o Gerenciador de Tráfego](./media/traffic-manager-overview/IC740855.jpg)

**Figura 2**

1. **Implante os serviços de nuvem do Azure, sites do Azure ou outros pontos de extremidade em seu ambiente de produção**. Quando você cria um perfil do Gerenciador de Tráfego, ele deve ser associado a uma assinatura. Em seguida, você adiciona pontos de extremidade para serviços de nuvem e sites de camada Padrão em produção, que fazem parte da mesma assinatura. Se um ponto de extremidade está em preparação e não está em um ambiente de produção do Azure ou não está na mesma assinatura, ele pode ser adicionado como um ponto de extremidade externo. Para obter mais informações sobre serviços de nuvem, consulte [Serviços de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obter mais informações sobre sites, consulte [Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Escolha um nome para o domínio do Gerenciador de Tráfego.** Pense em um nome para seu domínio com um prefixo exclusivo. A última parte do domínio, trafficmanager.net, é fixa. Para obter mais informações, consulte [Práticas recomendadas](#best-practices).
3. **Decida a configuração de monitoramento que você deseja usar.** O Gerenciador de Tráfego monitora pontos de extremidade para garantir que eles fiquem online, independentemente do método de roteamento de tráfego. Depois de definir as configurações de monitoramento, o Gerenciador de Tráfego não direcionará o tráfego para pontos de extremidade que estejam offline de acordo com o sistema de monitoramento, a menos que detecte que todos os pontos de extremidade estão offline ou que não possa detectar o status de nenhum dos pontos de extremidade contidos no perfil. Para obter mais informações sobre o monitoramento, consulte [Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md).
4. **Decida o método de roteamento de tráfego que você deseja usar**. Três métodos de roteamento de tráfego diferentes estão disponíveis. Reserve tempo para entender qual método melhor atende às suas necessidades. Se você precisar alterar o método posteriormente, você poderá fazer isso em qualquer momento. Observe também que cada método requer etapas de configuração ligeiramente diferentes. Para obter informações sobre os métodos de roteamento de tráfego, consulte [Sobre os métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md).
5. **Crie seu perfil e defina as configurações**. Você pode usar as APIs REST, o Windows PowerShell ou o Portal Clássico do Azure para criar seu perfil do Gerenciador de Tráfego e definir as configurações. Para obter mais informações, consulte [Como definir as configurações do Gerenciador de Tráfego](#how-to-configure-traffic-manager-settings). As etapas a seguir supõem que você usará a **Criação Rápida** no Portal Clássico do Azure. 
   - **Crie seu perfil do Gerenciador de Tráfego**: para criar um perfil usando a Criação Rápida no Portal Clássico do Azure, confira [Gerenciar Perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md).
   - **Configurar as definições do método de roteamento de tráfego** – Na Criação Rápida, você deve selecionar o método de roteamento de tráfego para o seu perfil. Essa configuração pode ser alterada a qualquer momento após a conclusão das etapas de Criação Rápida. Para etapas de configuração, consulte o tópico que corresponde ao seu método de roteamento de tráfego: [Configurar o método de roteamento de tráfego de Desempenho](traffic-manager-configure-performance-load-balancing.md), [Configurar o método de roteamento de tráfego de Failover](traffic-manager-configure-failover-load-balancing.md) e [Configurar o método de roteamento de tráfego de Round Robin](traffic-manager-configure-round-robin-load-balancing.md).
   
   >[AZURE.NOTE] O método Round Robin de roteamento de tráfego agora dá suporte à distribuição ponderada de tráfego de rede. No entanto, neste momento você deve usar APIs REST ou o Windows PowerShell para configurar o peso. Para obter mais informações e um exemplo de configuração, consulte [Pontos de extremidade externos do Gerenciador de Tráfego do Azure e Round Robin ponderado por meio do PowerShell](https://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) no blog do Azure.

   - **Configure os pontos de extremidade** – os pontos de extremidade não são configurados durante a Criação Rápida. Depois de criar seu perfil e especificar seu método de roteamento de tráfego, você deve informar os pontos de extremidade ao Gerenciador de Tráfego. Para obter as etapas de como configurar pontos de extremidade, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md)

   - **Defina as configurações de monitoramento** – as configurações de monitoramento não são definidas durante a Criação Rápida. Depois de criar seu perfil e especificar seu método de roteamento de tráfego, você deve informar ao Gerenciador de Tráfego o que ele deve monitorar. Para obter as etapas de como configurar o monitoramento, consulte [Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md).
6. **Teste o seu perfil do Gerenciador de Tráfego**. Teste se o seu perfil e o domínio estão funcionando conforme o esperado. Para obter informações sobre como fazer isso, consulte [Testando as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md).
7. **Aponte o registro de recurso DNS do nome de domínio da empresa para o perfil, para torná-lo dinâmico**. Para obter mais informações, consulte [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md).

Usando o exemplo na Figura 1, você alteraria o registro de recurso DNS em seus servidores para o seguinte a fim de apontar o nome de domínio da empresa para o nome de domínio do Gerenciador de Tráfego: www.contoso.com IN CNAME contoso.trafficmanager.net

## Como definir as configurações do Gerenciador de Tráfego

Você pode definir as configurações do Gerenciador de Tráfego usando o Portal Clássico do Azure, com APIs REST e cmdlets do Windows PowerShell.

Embora cada elemento da API REST não esteja visível no Portal Clássico do Azure, muitas configurações estão disponíveis usando qualquer um dos métodos. Para obter mais informações sobre o uso de APIs REST, consulte [Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Para obter mais informações sobre os cmdlets do Windows PowerShell para o Gerenciador de Tráfego, consulte [Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

>[AZURE.NOTE] Atualmente, não há suporte para a configuração de pontos de extremidade externos (tipo = 'Qualquer'), ponderações para o método de roteamento de tráfego Round Robin e perfis aninhados com o Portal Clássico do Azure. Você deve usar o REST (consulte [Criar definição](http://go.microsoft.com/fwlink/p/?LinkId=400772)) ou o Windows PowerShell (consulte [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)).

### Definição das configurações no Portal Clássico do Azure

No Portal Clássico do Azure, você pode criar o perfil do Gerenciador de Tráfego usando a Criação Rápida. A Criação Rápida permite criar um perfil básico. Depois de criar seu perfil, você pode definir configurações adicionais ou editar as configurações que definiu anteriormente. Para obter mais informações sobre a criação do seu perfil do Gerenciador de Tráfego usando Criação Rápida, consulte [Gerenciar perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md).

Você pode definir as seguintes configurações no Portal Clássico do Azure:

- **Prefixo DNS** – um prefixo exclusivo criado por você. Os perfis são exibidos no Portal Clássico do Azure por prefixo.
- **TTL do DNS** – o valor do DNS TTL (vida útil) controla com que frequência o servidor de nomes de caching local do cliente consultará o sistema DNS do Gerenciador de Tráfego do Azure para entradas DNS atualizadas.
- **Assinatura** – selecione a assinatura à qual seu perfil corresponderá. Observe que essa opção só aparecerá se você tiver várias assinaturas.
- **Método de roteamento de tráfego** – A maneira que você deseja que o Gerenciador de Tráfego lide com o roteamento de tráfego.
- **Ordem de Failover** – A ondem dos pontos de extremidade ao usar o método de roteamento de tráfego de failover.
- **Monitoramento** – as configurações de monitoramento contêm o protocolo (HTTP ou HTTPS), a porta e o caminho relativo e nome de arquivo.

### Definindo configurações usando APIs REST

Você pode criar e configurar seu perfil do Gerenciador de Tráfego usando APIs REST. Para obter mais informações, consulte [Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Perfil** – um perfil contém um prefixo de nome de domínio que você cria. Cada perfil corresponde à sua assinatura. Você pode criar vários perfis por assinatura. O nome do perfil está visível no Portal Clássico do Azure. O nome que você cria, que está contido no perfil, é conhecido como o Domínio do Gerenciador de Tráfego.
- **Definição** – uma definição contém configurações de política e de monitor. Uma definição corresponde a um perfil. Você pode ter apenas uma definição por perfil. A definição em si não fica visível no Portal Clássico do Azure, embora muitas das configurações contidas na definição estejam visíveis e possam ser configuradas no Portal Clássico do Azure.
- **Opções de DNS** – em cada definição, há opções de DNS. É onde a TTL do DNS é configurada.
- **Monitores** – em cada definição, há configurações de monitor. É onde o protocolo, a porta e o caminho relativo e nome de arquivo são configurados. As configurações de monitor são visíveis e podem ser definidas no Portal Clássico do Azure. Para obter mais informações, consulte [Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md).
- **Política** – em cada definição, há configurações de política. A política é onde os métodos de roteamento de tráfego e os pontos de extremidade são especificados. A política em si não fica visível no Portal Clássico do Azure, embora algumas das configurações de política estejam visíveis e possam ser definidas no Portal Clássico do Azure. Para obter mais informações, consulte [Sobre os métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md).

## Definindo configurações usando o Windows PowerShell

Você pode criar e configurar seu perfil do Gerenciador de Tráfego usando o Windows PowerShell. Para obter mais informações, consulte [Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## Práticas recomendadas

- **Torne os prefixos exclusivos e fáceis de entender** – o nome DNS de seu perfil do Gerenciador de Tráfego deve ser exclusivo. Você pode controlar somente a primeira parte do nome DNS. O nome de domínio do Gerenciador de Tráfego é usado apenas a fins de para identificação e direcionamento de solicitações de cliente. Os computadores cliente nunca exibirão esses nomes para o usuário final. No entanto, os perfis são identificados por esse nome de domínio. Portanto, é importante que você possa identificá-lo rapidamente diferenciando-o de outros nomes de domínio listados no Portal Clássico do Azure.
- **Use pontos para adicionar exclusividade ou tornar os nomes de domínio legíveis** – você também pode usar pontos para separar partes de seu prefixo de nome de domínio. Se estiver planejando criar várias políticas no Gerenciador de Tráfego, use uma hierarquia consistente para diferenciar um serviço do outro. Por exemplo, a Contoso tem serviços globais para Web, cobrança e gerenciamento de serviços públicos. As três políticas seriam *web.contoso.trafficmanager.net*, *bill.contoso.trafficmanager.net* e *util.contoso.trafficmanager.net*. Ao configurar serviços de nuvem ou sites, use nomes que incluam o local. Por exemplo, *web-us-contoso.cloudapp.net* e *web-asia-contoso.cloudapp.net*. As limitações são aquelas impostas pelo DNS. Pressuponha que um nome de domínio é uma sequência de rótulos separados por pontos (label.label.label.label.etc.). No momento desta documentação, os limites para nomes de domínio no Gerenciador de Tráfego são:
   - Cada rótulo pode ter no máximo 63 caracteres.
   - Você não pode ter mais de 40 rótulos no total. Como dois rótulos são usados por trafficmanager.net, isso deixa 38 para seu prefixo.
   - O nome de domínio inteiro pode ter no máximo 253 caracteres. Lembre-se de que trafficmanager.net usa 19 desses caracteres.
- **TTL do DNS** – o valor TTL do DNS controla com que frequência o servidor de nomes de caching local do cliente consultará o sistema DNS do Gerenciador de Tráfego do Azure para entradas DNS atualizadas. Qualquer alteração que ocorrer no Gerenciador de Tráfego, como alterações de perfil ou de disponibilidade do ponto de extremidade, levará esse período de tempo para ser atualizada em todo o sistema global de servidores DNS. Recomendamos que você mantenha a configuração com o valor padrão de 300 segundos (5 minutos). Um número maior aumenta o tempo pelo qual as respostas DNS do Gerenciador de Tráfego são armazenadas em cache por resolvedores de DNS e clientes, o que reduz a latência geral da consulta DNS. No entanto, se for necessário um failover muito rápido, convém configurar um valor menor.
- **Os pontos de extremidade devem estar em uma única assinatura** – todos os pontos de extremidade devem estar na mesma assinatura em que você está criando o perfil. Você pode adicionar pontos de extremidade de assinaturas diferentes a um perfil como pontos de extremidade externos, mas o Azure não os removerá automaticamente se você desabilitar ou excluir o serviço associado. Como resultado, pontos de extremidade externos permanecem no perfil do Gerenciador de Tráfego, e você continuará a ser cobrado por eles, a menos que os remova manualmente.
- **Apenas serviços de produção** – apenas pontos de extremidade em um ambiente de produção estão disponíveis. Não é possível direcionar para pontos de extremidade em execução em um ambiente de preparo. Observe que, se você executar uma troca de endereço VIP (IP virtual) enquanto um perfil estiver direcionando tráfego, o tráfego usará o ponto de extremidade permutado apenas no ambiente de produção.
- **Nomeie seus pontos de extremidade para que eles possam ser facilmente identificados** – considere o prefixo DNS que você deseja usar. O nome DNS é usado porque é garantido que ele seja exclusivo em uma assinatura, enquanto o nome do serviço de nuvem ou site pode não ser. Para evitar confusão, dê a um serviço de nuvem ou site um nome e prefixo DNS que sejam iguais ou semelhantes. Se você tiver mais de 20 sites e serviços de nuvem, a nomeação inadequada poderá dificultar a localização do ponto de extremidade correto. Além disso, pontos de extremidade nomeados inadequadamente tornarão os perfis difíceis de manter.
- **Todos os pontos de extremidade em um perfil devem atender às mesmas operações e portas** – se você misturar os pontos de extremidade, será mais provável que um cliente chame um ponto de extremidade que não possa atender à solicitação.
- **Todos os serviços de nuvem em um perfil devem usar as mesmas configurações de monitoramento** – você pode escolher somente um único caminho e arquivo para monitorar todos os pontos de extremidade em determinada definição. Você pode inserir "/" na caixa de texto de **Caminho relativo e nome de arquivo** para que o monitoramento tente acessar o caminho e o nome de arquivo padrão.
- **Desabilite pontos de extremidade para alterações temporárias, em vez de alterar sua configuração** – em muitos casos, talvez seja conveniente colocar um ponto de extremidade offline. Em vez de remover o ponto de extremidade de seu perfil, basta desabilitar o ponto de extremidade individual em seu perfil em vez disso. Isso mantém o ponto de extremidade como parte do perfil, mas o perfil age como se o ponto de extremidade não estivesse incluído nele. Essa ação é muito útil para remover temporariamente um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver funcionando novamente, você poderá habilitá-lo. Para obter mais informações, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md).
- **Desabilite um perfil para alterações temporárias, em vez de excluí-lo** – talvez você queira fazer com que um perfil inteiro fique offline, não apenas pontos de extremidade individuais especificados dentro dele. Para fazer isso, desabilite o perfil. Quando você desabilita um perfil, todas as configurações permanecem disponíveis para edição no Portal Clássico do Azure e, assim, você pode colocar o perfil novamente online de modo rápido e fácil quando quiser usá-lo novamente. Para obter mais informações, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md).
- **Armazenamento** – como você projeta o local e a distribuição do seu armazenamento é uma consideração importante ao usar o Gerenciador de Tráfego. Considere a transação de ponta a ponta e como seus dados fluirão quando você criar e implantar seus aplicativos para o Gerenciador de Tráfego.
- **SQL Azure** – semelhante ao que ocorre no design de armazenamento, analise o estado de seu aplicativo e seus requisitos de dados ao estender seus pontos de extremidade a várias regiões geográficas.

## Perfis aninhados

Você pode especificar o nome de outro perfil do Gerenciador de Tráfego como um ponto de extremidade, uma prática conhecida como perfis aninhados. O nome de perfil do Gerenciador de Tráfego é seu nome DNS, como contoso-europe.trafficmanager.net.

Isso permite que você configure o Gerenciador de Tráfego para que as consultas de nome DNS de entrada sejam analisadas em um conjunto de camadas a fim de garantir que o cliente solicitante seja direcionado para o conjunto correto de pontos de extremidade. A *Figura 3* mostra um exemplo.

![Exemplo de perfis aninhados do Gerenciador de Tráfego](./media/traffic-manager-overview/IC751072.png)

**Figura 3**

Você pode aninhar até 10 níveis e cada perfil pode ser configurado com um método de roteamento de tráfego diferente.

Por exemplo, você poderia criar uma configuração para o seguinte:

- Na camada superior (o perfil do Gerenciador de Tráfego que é mapeado para o seu nome DNS externo), você poderá configurar o perfil com o método de roteamento de tráfego de desempenho.
- Na camada intermediária, um conjunto de perfis do Gerenciador de Tráfego representam datacenters diferentes e usam o método de roteamento de tráfego de round robin.
- Na camada inferior, um conjunto de pontos de extremidade de serviço de nuvem em cada datacenter atende às solicitações de tráfego do usuário.

O resultado é que os usuários são direcionados para um data center regional apropriado com base no desempenho e para um serviço de nuvem nesse data center com base na distribuição de carga igual ou ponderada. Por exemplo, o peso pode ser usado para distribuir um pequeno percentual do tráfego para uma implantação nova ou de teste para testes ou comentários de clientes.

A *Figura 4* mostra a configuração.

![Exemplo de perfis multicamadas do Gerenciador de Tráfego](./media/traffic-manager-overview/IC751073.png)

**Figura 4**

Na *Figura 4*, o perfil do Gerenciador de Tráfego na camada de nível superior é um perfil pai, e os perfis do Gerenciador de Tráfego na camada intermediária são perfis filhos.

Se o Gerenciador de Tráfego direcionar os usuários para um perfil filho que tenha um pequeno número de pontos de extremidade íntegros, será possível sobrecarregar os pontos de extremidade e causar problemas de desempenho. Para evitar essa situação, você pode configurar o perfil pai do Gerenciador de Tráfego com um limite de pontos de extremidade íntegros que determina se qualquer um dos pontos de extremidade nos perfis filhos desse pai pode receber tráfego. Por exemplo, para verificar se há pelo menos três pontos de extremidade íntegros nos perfis filhos, você definiria o valor de limite como 3. No exemplo na Figura 4, você configuraria o perfil do Gerenciador de Tráfego de camada de nível superior para esse limite.

Para adicionar um perfil do Gerenciador de Tráfego como um ponto de extremidade e configurar o número mínimo de pontos de extremidade íntegros, você deve usar REST (consulte [Criar definição](http://go.microsoft.com/fwlink/p/?LinkId=400772)) ou o Windows PowerShell (consulte [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). Não é possível usar o Portal Clássico do Azure.

## Figuras do Gerenciador de Tráfego

Se você quiser obter as figuras deste tópico como slides do PowerPoint para sua própria apresentação sobre o Gerenciador de Tráfego ou para modificá-las para suas próprias finalidades, consulte [Figuras do Gerenciador de Tráfego na documentação do MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Próximas etapas

[Métodos de roteamento do Gerenciador de Tráfego](traffic-manager-routing-methods.md)

[Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md)

[Criar um perfil](traffic-manager-manage-profiles.md)

[Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!---HONumber=AcomDC_0128_2016-->