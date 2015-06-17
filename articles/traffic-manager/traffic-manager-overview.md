<properties 
   pageTitle="Visão geral do Gerenciador de Tráfego"
   description="Este artigo o ajudará a entender o que é o Gerenciador de Tráfego e como ele funciona"
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Visão geral do Gerenciador de Tráfego

O Gerenciador de Tráfego do Microsoft Azure permite controlar a distribuição do tráfego do usuário para seus pontos de extremidade especificados, o que pode incluir serviços de nuvem do Azure, sites e outros pontos de extremidade. O Gerenciador de Tráfego funciona aplicando um mecanismo de políticas inteligente às consultas DNS (Domain Name System) para os nomes de domínio de seus recursos da Internet. Seus serviços de nuvem ou sites do Azure podem ser executados em datacenters diferentes por todo o mundo.

O Gerenciador de Tráfego pode ajudá-lo a:

- **Melhorar a disponibilidade de aplicativos críticos** - o Gerenciador de Tráfego permite aumentar a disponibilidade de seus aplicativos críticos, monitorando seus pontos de extremidade no Azure e fornecendo recursos de failover automático quando um serviço de nuvem do Azure, site do Azure ou outro local é desativado.
- **Melhorar a capacidade de resposta de aplicativos de alto desempenho** - o Azure permite executar serviços de nuvem ou sites em datacenters espalhados pelo mundo inteiro. O Gerenciador de Tráfego pode melhorar a capacidade de resposta dos aplicativos e o tempo de entrega de conteúdo, direcionando os usuários finais para o ponto de extremidade com a menor latência de rede em relação ao cliente.
- **Atualizar e realizar a manutenção do serviço sem tempo de inatividade** - o Gerenciador de Tráfego dá suporte a cenários estendidos para a nuvem híbrida e implantações locais, incluindo os cenários de "expandir para nuvem", "migrar para nuvem" e "failover para nuvem". Para manutenção planejada, você desabilita o ponto de extremidade no Gerenciador de Tráfego e aguarda até que o ponto de extremidade conclua a manutenção das conexões existentes. Quando não houver mais nenhum tráfego ao ponto de extremidade, você atualiza o serviço nesse ponto de extremidade, testa-o e então habilita-o novamente no Gerenciador de Tráfego. Isso ajuda você a manter e atualizar seus serviços sem tempo de inatividade para clientes.
- **Distribuição de tráfego para implantações grandes e complexas** - Com perfis aninhados do Gerenciador de Tráfego, nos quais um perfil do Gerenciador de Tráfego pode ter outro perfil do Gerenciador de Tráfego como um ponto de extremidade, você pode criar configurações para otimizar o desempenho e a distribuição para implantações maiores e mais complexas. Para obter mais informações, consulte [Perfis aninhados](#nested-profiles).

## Como funciona o Gerenciador de Tráfego

Quando você configura um perfil do Gerenciador de Tráfego, as configurações que você especifica fornecem ao Gerenciador de Tráfego as informações necessárias para determinar qual ponto de extremidade deve atender à solicitação, com base em uma consulta DNS. Nenhum tráfego de ponto de extremidade é efetivamente roteado por meio do Gerenciador de Tráfego.

A *Figura 1* mostra como o Gerenciador de Tráfego direciona os usuários para um de um conjunto de pontos de extremidade. Os números na Figura 1 correspondem às descrições numeradas abaixo:

![How Traffic Manager works](./media/traffic-manager-overview/IC740854.jpg)

**Figura 1**

1. **Tráfego de usuário para nome de domínio de empresa**: O cliente solicita informações usando o nome de domínio da empresa. A meta é resolver um nome DNS para um endereço IP. Os domínios da empresa devem ser reservados por meio de registros normais de nomes de domínio da Internet, que são mantidos fora do Gerenciador de Tráfego. Na Figura 1, o domínio da empresa de exemplo é *www.contoso.com*.
2. **Nome de domínio de empresa para nome de domínio do Gerenciador de Tráfego**: O registro de recurso DNS para os pontos de domínio da empresa aponta para um nome de domínio do Gerenciador de Tráfego mantido no Gerenciador de Tráfego do Azure. Isso é obtido por meio de um registro de recurso CNAME, que mapeia o nome de domínio da empresa para o nome de domínio do Gerenciador de Tráfego. No exemplo, o nome de domínio do Gerenciador de Tráfego é *contoso.trafficmanager.net*.
3. **Nome de domínio do e perfil do Gerenciador de Tráfego**: O nome de domínio do Gerenciador de Tráfego faz parte do perfil do Gerenciador de Tráfego. O servidor DNS do usuário envia uma nova consulta DNS para o nome de domínio do Gerenciador de Tráfego (no nosso exemplo, *contoso.trafficmanager.net*), que é recebida pelos servidores de nome DNS do Gerenciador de Tráfego.
4. **Regras de perfil do Gerenciador de Tráfego processadas**: O Gerenciador de Tráfego usa o método de balanceamento de carga e o monitoramento de status especificados para determinar qual ponto de extremidade do Azure ou de outro tipo deve atender à solicitação.
5. **Nome de domínio de ponto de extremidade enviado ao usuário**: O Gerenciador de Tráfego retorna um registro CNAME que mapeia o nome de domínio do Gerenciador de Tráfego para o nome de domínio do ponto de extremidade. O servidor DNS do usuário resolve o nome de domínio do ponto de extremidade para o endereço IP correspondente e envia-o ao usuário.
6. **O usuário chama o ponto de extremidade**: O usuário chama o ponto de extremidade retornado diretamente, usando seu endereço IP.

Uma vez que o domínio da empresa e o endereço IP resolvido forem armazenados em cache no computador cliente, o usuário continuará a interagir com o ponto de extremidade escolhido até que sua entrada de cache DNS local expire. É importante observar que o cliente DNS armazena em cache as entradas do host DNS pela duração do seu TTL (período de vida útil). Recuperar entradas do host por meio do cache do cliente DNS é uma ação que ignora o perfil do Gerenciador de Tráfego, e podem ocorrer atrasos na conexão se o ponto de extremidade ficar indisponível antes que o TTL expire. Se o TTL de uma entrada de host DNS no cache expira e o computador cliente precisa resolver o nome de domínio da empresa novamente, ele envia uma nova consulta DNS. O computador cliente pode receber o endereço IP de um ponto de extremidade diferente, dependendo do método de balanceamento de carga aplicado e da integridade dos pontos de extremidade no momento da solicitação.

## Como implementar o Gerenciador de Tráfego

A *Figura 2* mostra as etapas, na devida ordem, que são necessárias para implementar o Gerenciador de Tráfego. Essas etapas podem ser executadas em uma ordem ligeiramente diferente depois de ter uma compreensão sólida da configuração do Gerenciador de Tráfego e das práticas recomendadas relacionadas. Os números na Figura 2 correspondem às descrições numeradas abaixo:

![How to configure Traffic Manager](./media/traffic-manager-overview/IC740855.jpg)

**Figura 2**

1. **Implante os serviços de nuvem do Azure, sites do Azure ou outros pontos de extremidade em seu ambiente de produção**. Quando você cria um perfil do Gerenciador de Tráfego, ele deve ser associado a uma assinatura. Em seguida, você adiciona pontos de extremidade para serviços de nuvem e sites de camada Padrão em produção, que fazem parte da mesma assinatura. Se um ponto de extremidade está em preparação e não está em um ambiente de produção do Azure ou não está na mesma assinatura, ele pode ser adicionado como um ponto de extremidade externo. Para obter mais informações sobre serviços de nuvem, consulte [Serviços de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obter mais informações sobre sites, consulte [Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Escolha um nome para o domínio do Gerenciador de Tráfego**. Pense em um nome para seu domínio com um prefixo exclusivo. A última parte do domínio, trafficmanager.net, é fixa. Para obter mais informações, consulte [Práticas recomendadas](#best-practices).
3. **Decida a configuração de monitoramento que você deseja usar**. O Gerenciador de Tráfego monitora pontos de extremidade para garantir que eles fiquem online, independentemente do método de balanceamento de carga. Depois de definir as configurações de monitoramento, o Gerenciador de Tráfego não direcionará o tráfego para pontos de extremidade que estejam offline de acordo com o sistema de monitoramento, a menos que detecte que todos os pontos de extremidade estão offline ou que não possa detectar o status de nenhum dos pontos de extremidade contidos no perfil. Para obter mais informações sobre como monitorar, consulte [Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md).
4. **Escolha o método de balanceamento de carga que você deseja usar**. Há três métodos de balanceamento de carga diferentes disponíveis. Reserve tempo para entender qual método melhor atende às suas necessidades. Se você precisar alterar o método posteriormente, você poderá fazer isso em qualquer momento. Observe também que cada método requer etapas de configuração ligeiramente diferentes. Para obter informações sobre os métodos de balanceamento de carga, consulte [Sobre os métodos de balanceamento de carga do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md).
5. **Crie seu perfil e defina as configurações**. Você pode usar APIs REST, Windows PowerShell ou o Portal de Gerenciamento para criar seu perfil do Gerenciador de Tráfego e definir as configurações. Para obter mais informações, consulte [Como definir as configurações do Gerenciador de Tráfego](#how-to-configure-traffic-manager-settings). As etapas a seguir supõem que você usará a **Criação Rápida** no Portal de Gerenciamento. 
   - **Crie seu perfil do Gerenciador de Tráfego** - para criar um perfil usando a Criação Rápida no Portal de Gerenciamento, consulte [Gerenciar perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md).
   - **Defina as configurações do método de balanceamento de carga** - usando a Criação Rápida, você deve selecionar a método de balanceamento de carga para o seu perfil. Essa configuração pode ser alterada em qualquer momento depois de concluir as etapas de Criação Rápida. Para etapas de configuração, consulte o tópico que corresponde ao seu método de balanceamento de carga: [Configurar balanceamento de carga de desempenho](traffic-manager-configure-performance-load-balancing.md),[Configurar balanceamento de carga de failover](traffic-manager-configure-failover-load-balancing.md), [Configurar balanceamento de Round Robin](traffic-manager-configure-round-robin-load-balancing.md).
   >[AZURE.NOTE] O método Round Robin do balanceamento de carga agora dá suporte a distribuição ponderada de tráfego de rede. No entanto, neste momento você deve usar APIs REST ou o Windows PowerShell para configurar o peso. Para obter mais informações e um exemplo de configuração, consulte [Pontos de extremidade externos do Gerenciador de Tráfego do Azure e Round Robin ponderado por meio do PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) no blog do Azure.

   - **Configurar pontos de extremidade** - pontos de extremidade não são configurados durante a Criação Rápida. Depois de criar seu perfil e especificar o método de balanceamento de carga, você deve, em seguida, informar os pontos de extremidade ao Gerenciador de Tráfego. Para obter as etapas de como configurar pontos de extremidade, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md)

   - **Definir configurações de monitoramento** - as configurações de monitoramento não são configuradas durante a Criação Rápida. Depois de criar seu perfil e especificar o método de balanceamento de carga, você deve, em seguida, informar ao Gerenciador de Tráfego o que monitorar. Para obter as etapas de como configurar o monitoramento, consulte [Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md).
6. **Teste o seu perfil do Gerenciador de Tráfego**. Teste se o seu perfil e o domínio estão funcionando conforme o esperado. Para obter informações sobre como fazer isso, consulte [Testando as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md).
7. **Aponte o registro de recurso DNS do nome de domínio da empresa para o perfil, para torná-lo dinâmico**. Para obter mais informações, consulte [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md).

Usando o exemplo na Figura 1, você alteraria o registro de recurso DNS em seus servidores para o seguinte, para assim apontar o nome de domínio da empresa para o nome de domínio do Gerenciador de Tráfego:
    www.contoso.com IN CNAME contoso.trafficmanager.net

## Como definir as configurações do Gerenciador de Tráfego

Você pode configurar o Gerenciador de Tráfego usando o Portal de Gerenciamento, com APIs REST ou então com cmdlets do Windows PowerShell.

Embora cada elemento API REST não esteja visível no Portal de Gerenciamento, muitas configurações estão disponíveis usando qualquer dos dois métodos. Para obter mais informações sobre o uso de APIs REST, consulte [Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Para obter mais informações sobre os cmdlets do Windows PowerShell para o Gerenciador de Tráfego, consulte [Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

>[AZURE.NOTE] Atualmente, não há nenhum suporte para configurar pontos de extremidade externos (tipo = 'Qualquer'), pesos para o método de balanceamento de carga Round Robin e perfis aninhados com o Portal de Gerenciamento. Você deve usar o REST (consulte [Criar definição](http://go.microsoft.com/fwlink/p/?LinkId=400772)) ou então o Windows PowerShell (consulte [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)).

### Definindo configurações no Portal de Gerenciamento

No Portal de Gerenciamento, você pode criar o perfil do Gerenciador de Tráfego usando a Criação Rápida. A Criação Rápida permite criar um perfil básico. Depois de criar seu perfil, você pode definir configurações adicionais ou editar as configurações realizadas anteriormente. Para obter mais informações sobre a criação do seu perfil do Gerenciador de Tráfego usando Criação Rápida, consulte [Gerenciar perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md).

No Portal de Gerenciamento, você pode definir as configurações a seguir:

- **Prefixo DNS** - um prefixo exclusivo criado por você. Os perfis são exibidos no Portal de Gerenciamento segundo o prefixo.
- **TTL do DNS** - o valor de TTL (período de vida útil) do DNS controla com que frequência o servidor de nomes de armazenamento em cache local do cliente consultará o sistema DNS do Gerenciador de Tráfego do Azure para entradas DNS atualizadas.
- **Assinatura** - selecione a assinatura à qual seu perfil corresponderá. Observe que essa opção só aparecerá se você tiver múltiplas assinaturas.
- **Método de balanceamento de carga** - a maneira como deseja que o Gerenciador de Tráfego lide com o balanceamento de carga.
- **Ordem de failover** - ao usar o método de balanceamento de carga de failover, trata-se da ordem dos pontos de extremidade.
- **Monitoramento** - as configurações de monitoramento contêm o protocolo (HTTP ou HTTPS), porta, nome de arquivo e o caminho relativo.

### Definindo configurações usando APIs REST

Você pode criar e configurar seu perfil do Gerenciador de Tráfego usando APIs REST. Para obter mais informações, consulte [Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Perfil** - um perfil contém um prefixo de nome de domínio que você cria. Cada perfil corresponde à sua assinatura. Você pode criar vários perfis por assinatura. O nome do perfil fica visível no Portal de Gerenciamento. O nome que você cria, o qual está contido no perfil, é conhecido como o domínio do Gerenciador de Tráfego.
- **Definição** - uma definição contém as configurações de política e as configurações do monitor. Uma definição corresponde a um perfil. Você pode ter apenas uma definição por perfil. A definição em si não fica visível no Portal de Gerenciamento, embora muitas das configurações contidas na definição fiquem visíveis e possam ser configuradas nesse portal.
- **Opções de DNS** - há opções de DNS contidas em cada definição. É aqui que o TTL do DNS é configurado.
- **Monitores** - há configurações de monitor em cada definição. É aqui que o protocolo, porta, nome de arquivo e caminho relativo são configurados. Configurações de monitor ficam visíveis e podem ser definidas no Portal de Gerenciamento. Para obter mais informações, consulte [Monitoramento de Gerenciador de Tráfego](traffic-manager-monitoring.md).
- **Política** - há configurações de política em cada definição. A política é onde os pontos de extremidade e métodos de balanceamento de carga são especificados. A política em si não fica visível no Portal de Gerenciamento, embora muitas das configurações para a política fiquem visíveis e possam ser configuradas nesse portal. Para obter mais informações, consulte [Sobre métodos de balanceamento de carga do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md).

## Definindo configurações usando o Windows PowerShell

Você pode criar e configurar seu perfil do Gerenciador de Tráfego usando o Windows PowerShell. Para obter mais informações, consulte [Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## Práticas recomendadas

- **Torne os prefixos exclusivos e fáceis de entender** - o nome DNS do seu perfil do Gerenciador de Tráfego deve ser exclusivo. Você pode controlar somente a primeira parte do nome DNS. O nome de domínio do Gerenciador de Tráfego é usado apenas para fins de identificação e direcionamento de solicitações do cliente. Os computadores cliente nunca exibirão esses nomes para o usuário final. No entanto, perfis são identificados por esse nome de domínio; portanto, é importante que você possa identificá-lo rapidamente entre outros nomes de domínio listados no Portal de Gerenciamento.
- **Use pontos para adicionar exclusividade aos nomes de domínio ou torná-los legíveis** - você também pode usar pontos para separar partes do seu prefixo de nome de domínio.  Se você estiver planejando criar várias políticas no Gerenciador de Tráfego, use uma hierarquia consistente para diferenciar um serviço do outro. Por exemplo, a Contoso tem serviços globais para Web, cobrança e gerenciamento de utilitários. As três políticas seriam *web.contoso.trafficmanager.net*, *bill.contoso.trafficmanager.net* e *util.contoso.trafficmanager.net*. Ao configurar serviços de nuvem ou sites, use nomes que incluam o local. Por exemplo, *web-us-contoso.cloudapp.net* e *web-asia-contoso.cloudapp.net*. As limitações são aquelas impostas pelo DNS. Suponha que um nome de domínio é uma sequência de rótulos separados por pontos (label.label.label.label.etc.). No momento desta documentação, os limites para nomes de domínio no Gerenciador de Tráfego são os seguintes:
   - Cada rótulo pode ter um máximo de 63 caracteres.
   - Você não pode ter mais de 40 rótulos no total. Já que dois rótulos são ocupados pelo trafficmanager.net, isso significa que restam 38 para seu prefixo.
   - O nome de domínio inteiro pode ser de no máximo 253 caracteres. Tenha em mente que o trafficmanager.net ocupa 19 desses caracteres.
- **TTL do DNS** - o valor de TTL do DNS controla com que frequência o servidor de nomes de armazenamento em cache local do cliente consultará o sistema DNS do Gerenciador de Tráfego do Azure para entradas DNS atualizadas. Qualquer alteração que ocorra no Gerenciador de Tráfego, como por exemplo alterações de perfil ou alterações na disponibilidade do ponto de extremidade, fará com que esse período de tempo seja atualizado em todo o sistema global dos servidores DNS. Recomendamos que você mantenha a configuração com o valor padrão de 300 segundos (5 minutos). Um número maior aumenta o tempo pelo qual respostas de DNS do Gerenciador de Tráfego são armazenadas em cache por resolvedores DNS e clientes, o que reduz a latência geral da consulta DNS. No entanto, se for necessário um failover bem rápido, convém configurar um valor menor.
- **Pontos de extremidade devem estar em uma única assinatura** - todos os pontos de extremidade devem estar na mesma assinatura em que você está criando o perfil. Você pode adicionar pontos de extremidade de assinaturas diferentes a um perfil como pontos de extremidade externos, mas o Azure não os removerá automaticamente se você desabilitar ou excluir o serviço associado. Como resultado, os pontos de extremidade externos permanecem no perfil do Gerenciador de Tráfego e você continuará a ser cobrado por eles, a menos que os remova manualmente.
- **Apenas serviços de produção** - somente os pontos de extremidade em um ambiente de produção estão disponíveis. Não é possível direcionar para pontos de extremidade em execução em um ambiente de preparo. Observe que, se você executar uma permutação de endereço IP virtual (VIP) enquanto um perfil estiver direcionando tráfego, o tráfego usará o ponto de extremidade recém-permutado para o ambiente de produção.
- **Nomeie seus pontos de extremidade para que eles possam ser facilmente identificados** - considere o prefixo DNS que você deseja usar. O nome de DNS é usado porque é garantido que ele será exclusivo em uma assinatura, enquanto o nome do serviço de nuvem ou site pode não ser. Para evitar confusão, dê a um site ou serviço de nuvem um nome e um prefixo DNS que sejam iguais ou semelhantes. Se você tiver mais de 20 sites e serviços de nuvem, a nomeação inadequada pode dificultar a localização do ponto de extremidade correto. Além disso, pontos de extremidade nomeados inadequadamente tornarão os perfis difíceis de manter.
- **Todos os pontos de extremidade em um perfil devem atender às mesmas operações e portas** - se você mesclar os pontos de extremidade, aumenta a chance de um cliente chamar um ponto de extremidade que não atenda à sua solicitação.
- **Todos os serviços de nuvem em um perfil devem usar as mesmas configurações de monitoramento** - você pode escolher somente um único caminho e arquivo para monitorar todos os pontos de extremidade em determinada definição. Você pode inserir "/" na caixa de texto **Caminho relativo e nome de arquivo**, para que o monitoramento tente acessar o caminho padrão e o nome do arquivo.
- **Desabilitar pontos de extremidade para alterações temporárias, em vez de alterar a configuração** - em muitos casos, talvez seja conveniente fazer com que um ponto de extremidade fique offline. Em vez de remover o ponto de extremidade do seu perfil, apenas desabilite o ponto de extremidade individual em seu perfil. Isso deixa o ponto de extremidade como parte do perfil, mas o perfil age como se o ponto de extremidade não estivesse incluído nele. Isso é muito útil para remover temporariamente um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver funcionando novamente, você poderá habilitá-lo. Para obter mais informações, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md).
- **Desabilite um perfil para alterações temporárias, em vez de excluí-lo** - talvez você queira fazer com que um perfil inteiro fique offline, não apenas pontos de extremidade individuais especificados dentro dele. Para fazer isso, desabilite o perfil. Quando você desabilita um perfil, todas as configurações permanecem disponíveis para edição no portal de gerenciamento e, em seguida, você pode colocar o perfil novamente online de modo rápido e fácil quando quiser usá-lo novamente. Para obter mais informações, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md).
- **Armazenamento** - como você projeta o local e a distribuição do seu armazenamento é uma consideração importante ao usar o Gerenciador de Tráfego. Pense sobre a transação de ponta a ponta e sobre como seus dados fluirão quando você criar e implantar seus aplicativos no Gerenciador de Tráfego.
- **SQL do Azure** - semelhante ao que ocorre no design de armazenamento, analise o estado de seu aplicativo e seus requisitos de dados ao estender seus pontos de extremidade a várias regiões geográficas.

## Perfis aninhados

Você pode especificar o nome de outro perfil do Gerenciador de Tráfego como um ponto de extremidade, uma prática conhecida como perfis aninhados. O nome do perfil do Gerenciador de Tráfego é seu nome DNS, como contoso-europe.trafficmanager.net.

Isso permite que você configure o Gerenciador de Tráfego para que as consultas de nome de DNS em entrada sejam analisadas em um conjunto de camadas, para garantir que o cliente solicitante seja direcionado para o conjunto correto de pontos de extremidade. A *Figure 3* mostra um exemplo.

![Example of nested Traffic Manager profiles](./media/traffic-manager-overview/IC751072.png)

**Figura 3**

Você pode aninhar até 10 níveis, e cada perfil pode ser configurado com um método de balanceamento de carga diferente.

Por exemplo, você poderia criar uma configuração para o seguinte:

- Na camada superior (perfil do Gerenciador de Tráfego que é mapeado para o seu nome de DNS externo), você poderá configurar o perfil com o método de balanceamento de carga de desempenho.
- Na camada intermediária, um conjunto de perfis do Gerenciador de Tráfego representam datacenters diferentes e usam o método de balanceamento de carga de Rodízio.
- Na camada inferior, um conjunto de pontos de extremidade de serviço de nuvem em cada datacenter atende às solicitações de tráfego do usuário.

O resultado é que os usuários são direcionados para um datacenter regional apropriado baseado no desempenho, e para um serviço de nuvem contido nesse datacenter e baseado em uma distribuição de carga igual ou ponderada. Por exemplo, a ponderação pode ser usada para distribuir um pequeno percentual do tráfego para uma implantação nova ou experimental, para testes ou feedback de clientes.

A *Figura 4* mostra a configuração.

![Example of multi-tiered Traffic Manager profiles](./media/traffic-manager-overview/IC751073.png)

**Figura 4**

Na *Figure 4*, o perfil do Gerenciador de Tráfego na camada de nível superior é um perfil pai, enquanto aqueles na camada intermediária são perfis filho.

Se o Gerenciador de Tráfego direciona os usuários para um perfil filho que tem um pequeno número de pontos de extremidade íntegros, é possível sobrecarregar esses pontos de extremidade e causar problemas de desempenho. Para evitar essa situação, você pode configurar o perfil pai do Gerenciador de Tráfego com um limite de pontos de extremidade íntegros, que determina se qualquer um dos pontos de extremidade contidos nos perfis filho desse perfil pai pode receber tráfego. Por exemplo, se você quiser assegurar que existam pelo menos três pontos de extremidade íntegros dentro dos perfis filho, você definiria o valor desse limite como 3. No exemplo da Figura 4, você configuraria o perfil do Gerenciador de Tráfego com camada de nível superior para esse limite.

Para adicionar um perfil do Gerenciador de Tráfego como um ponto de extremidade e configurar o número mínimo de pontos de extremidade íntegros, você deve usar o REST (consulte [Criar definição](http://go.microsoft.com/fwlink/p/?LinkId=400772)) ou o Windows PowerShell (consulte [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). Você não pode usar o Portal de Gerenciamento.

## Figuras do Gerenciador de Tráfego

Se você deseja as figuras neste tópico como slides do PowerPoint para sua própria apresentação sobre o Gerenciador de Tráfego ou deseja modificá-las para seus próprios fins, consulte a [Documentação de figuras do Gerenciador de Tráfego no MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Consulte também

[Serviços de Nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584)

[Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!--HONumber=49--> 