## Noções básicas sobre rede virtual

### O que é uma rede virtual (VNet) do Azure?

Você pode usar redes virtuais para provisionar e gerenciar VPNs (redes virtuais privadas) no Azure e, se desejar, vincular as redes virtuais no Azure a outras redes virtuais no Azure ou à sua infraestrutura de TI local para criar soluções híbridas e entre locais. Cada rede virtual que você cria tem seu próprio bloco CIDR e pode ser vinculada a outras redes virtuais e redes locais, desde que os blocos CIDR não entrem em conflito. Você também tem controles das configurações do servidor DNS para redes virtuais e segmentação da rede virtual em sub-redes.

Use redes virtuais para:

- Criar uma rede virtual dedicada somente de nuvem privada
									
	Às vezes, você não precisa de uma configuração entre locais para sua solução. Quando você cria uma redes virtuais, seus serviços e VMs na rede virtual podem se comunicar de forma direta e segura entre si na nuvem. Isso mantém o tráfego seguramente na rede virtual, mas ainda permite que você configure conexões de ponto de extremidade para as VMs e os serviços que exigem comunicação com a Internet como parte de sua solução.

- Estender seu data center com segurança
									
	Com as redes virtuais, você pode criar VPNs S2S (site a site) tradicionais para escalar com segurança a capacidade do data center. As VPNs S2S usam IPSEC para fornecer uma conexão segura entre o gateway de VPN corporativo e o Azure.

- Habilitar cenários de nuvem híbrida
									
	As redes virtuais proporcionam a flexibilidade para oferecer suporte a uma variedade de cenários de nuvem híbrida. É possível conectar com segurança aplicativos baseados em nuvem a qualquer tipo de sistema local, como mainframes e sistemas Unix.

### Como saber se preciso de uma rede virtual?

Consulte o tópico [Visão geral da rede virtual](../articles/virtual-network/virtual-networks-overview.md) para ver uma tabela de decisão que ajudará você a decidir sobre a melhor opção de design de rede para seu ambiente.

### Como começar?

Leia [a documentação da Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para saber como começar a usá-la. Essa página contém links para as etapas de configuração comum, bem como informações que ajudarão você a entender tudo o que será necessário levar em consideração ao projetar a rede virtual.

### Quais serviços posso usar com redes virtuais?

As redes virtuais podem ser usadas com diversos serviços diferentes do Azure, como Serviços de Nuvem (PaaS), Máquinas Virtuais e Aplicativos Web. No entanto, há alguns serviços que não têm suporte em uma rede virtual. Verifique o serviço específico que você deseja usar e se ele é compatível.

### Posso usar redes virtuais sem conectividade entre locais?

Sim. É possível usar uma rede virtual sem usar uma conectividade site a site. Isso será especialmente útil se você desejar executar controladores de domínio e farms do SharePoint no Azure.

## Configuração da rede virtual

### Quais ferramentas eu uso para criar uma redes virtuais?

Você pode usar as seguintes ferramentas para criar ou configurar uma rede virtual:

- Portal do Azure (para Redes Virtuais clássicas e do Gerenciador de Recursos).

- Um arquivo de configuração de rede (netcfg - somente para Redes Virtuais clássicas). Consulte [Configurar uma rede virtual usando um arquivo de configuração de rede](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).

- PowerShell (para Redes Virtuais clássicas e do Gerenciador de Recursos).

- CLI do Azure (para Redes Virtuais clássicas e do Gerenciador de Recursos).

### Quais intervalos de endereço posso usar em minhas redes virtuais?

Você pode usar intervalos de endereços IP públicos e qualquer intervalo de endereços IP definido na [RFC 1918](http://tools.ietf.org/html/rfc1918).

### Posso ter endereços IP públicos em minhas redes virtuais?

Sim. Para saber mais sobre intervalos de endereços IP públicos, consulte [Espaço de endereço IP público em uma rede virtual (rede virtual)](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). Lembre-se de que os IPs públicos não estarão diretamente acessíveis na Internet.

### Há um limite para o número de sub-redes na minha rede virtual?

Não há nenhum limite para o número de sub-redes que você usa em uma rede virtual. Todas as sub-redes devem estar totalmente contidas no espaço de endereço de rede virtual e não devem se sobrepor.

### Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?

O Azure reserva alguns endereços IP em cada sub-rede. O primeiro e o último endereço IP das sub-redes são reservados para conformidade de protocolo, juntamente com três ou mais endereços usados para os serviços do Azure.

### Que tamanho, máximo e mínimo, as redes virtuais e sub-redes podem ter?

A menor sub-rede à qual oferecemos suporte é de /29 e a maior é de /8 (usando definições da sub-rede CIDR).

### Posso levar minhas VLANs para o Azure usando redes virtuais?

Não. As v são sobreposições da Camada 3. O Azure não oferece suporte a nenhuma semântica da Camada 2.

### Posso especificar políticas de roteamento personalizadas nas minhas redes virtuais e sub-redes?

Sim. Você pode usar o UDR (Roteamento Definido pelo Usuário). Para saber mais sobre UDR, visite [Rotas definidas pelo usuário e Encaminhamento IP](../articles/virtual-network/virtual-networks-udr-overview.md).

### As redes virtuais oferecem suporte ao multicast ou à difusão?

Não. Não há suporte para nenhum dos dois.

### Quais protocolos posso usar nas redes virtuais?

Você pode usar protocolos padrão baseados em IP nas redes virtuais. No entanto, os pacotes encapsulados de IP em IP, multicast, difusão e pacotes GRE (Encapsulamento de Roteamento Genérico) são bloqueados nas redes virtuais. Os protocolos padrão que funcionam incluem:

- TCP
- UDP
- ICMP

### Posso executar ping em meus roteadores padrão em uma rede virtual?

Não.

### Posso usar tracert para diagnosticar a conectividade?

Não.

### Posso adicionar sub-redes depois que a rede virtual é criada?

Sim. As sub-redes podem ser adicionadas às redes virtuais a qualquer momento, desde que o endereço da sub-rede não faça parte de outra sub-rede na rede virtual.

### Posso modificar o tamanho da minha sub-rede depois de criá-la?

Você poderá adicionar, remover, expandir ou reduzir uma sub-rede se não houver VMs ou serviços implantados nela, usando cmdlets do PowerShell ou o arquivo NETCFG. Também é possível adicionar, remover, expandir ou reduzir todos os prefixos, desde que as sub-redes que contêm VMs ou serviços não sejam afetadas pela alteração.

### Posso modificar sub-redes depois de criá-las?

Sim. Você pode adicionar, remover e modificar os blocos CIDR usados por uma rede virtual.

### Posso me conectar à Internet se eu estiver executando meus serviços em uma rede virtual?

Sim. Todos os serviços implantados em uma rede virtual podem se conectar à internet. Cada serviço de nuvem implantado no Azure tem um VIP publicamente endereçável atribuído a ele. Você precisará definir pontos de extremidade de entrada para funções de PaaS e pontos de extremidade de máquinas virtuais a fim de habilitar esses serviços para aceitar conexões da internet.

### As redes virtuais oferecem suporte ao IPv6?

Não. Não é possível usar o IPv6 com redes virtuais neste momento.

### Uma rede virtual pode abranger regiões?

Não. A rede virtual é limitada a uma única região.

### Posso conectar uma rede virtual a outra rede virtual no Azure?

Sim. Você pode criar comunicação rede virtual para rede virtual usando APIs REST ou o Windows PowerShell.

## Resolução de nomes (DNS)

### Quais são minhas opções DNS para redes virtuais?

Use a tabela de decisão na página [Resolução de nome para VMs e instâncias de função](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md), que orientará você por todas as opções DNS disponíveis.

### Posso especificar servidores DNS para uma rede virtual?

Sim. Você pode especificar endereços IP do servidor DNS nas configurações da rede virtual. Isso será aplicado como o servidor DNS padrão a todas as VMs na rede virtual.

### Quantos servidores DNS posso especificar?

Você pode especificar até 12 servidores DNS.

### Posso modificar meus servidores DNS depois de ter criado a rede?

Sim. Você pode alterar a lista de servidores DNS para sua rede virtual a qualquer momento. Se você alterar sua lista de servidores DNS, será preciso reiniciar cada uma das VMs em sua rede virtual para que elas selecionem o novo servidor DNS.


### O que é o DNS fornecido pelo Azure? Ele funciona com redes virtuais?

O DNS fornecido pelo Azure é um serviço DNS multilocatário oferecido pela Microsoft. O Azure registra todas as VMs e instâncias de função nesse serviço. Esse serviço fornece resolução de nome por nome de host para VMs e instâncias de função contidas no mesmo serviço de nuvem e por FQDN para VMs e instâncias de função na mesma rede virtual.

> [AZURE.NOTE] No momento, existe uma limitação para os primeiros 100 serviços de nuvem na rede virtual para resolução de nome entre locatários usando DNS fornecido pelo Azure. Se você estiver usando seu próprio servidor DNS, essa limitação não se aplicará.

### Posso substituir minhas configurações DNS por VM/serviço?

Sim. Você pode definir servidores DNS por serviço de nuvem para substituir as configurações de rede padrão. No entanto, é recomendável usar DNS para toda a rede, tanto quanto possível.

### Posso colocar meu próprio sufixo DNS?

Não. Você não pode especificar um sufixo DNS personalizado para suas redes virtuais.

## redes virtuais e VMs

### Posso implantar VMs em uma rede virtual?

Sim.

### Posso implantar VMs do Linux em uma rede virtual?

Sim. Você pode implantar qualquer distribuição Linux com suporte do Azure.

### Qual é a diferença entre um VIP público e um endereço IP interno?

- Um endereço IP interno é um endereço IP que é atribuído a cada VM em uma rede virtual pelo DHCP. Ele não é voltado para o público. Se tiver criado uma rede virtual, o endereço IP interno será atribuído do intervalo que você especificou nas configurações de sub-rede da rede virtual. Se você não tiver uma rede virtual, um endereço IP interno ainda será atribuído. O endereço IP interno permanecerá com a VM durante seu tempo de vida, a menos que a VM seja desalocada.

- Um VIP público é o endereço IP público atribuído ao balanceador de carga ou serviço de nuvem. Ele não é atribuído diretamente à NIC da VM. O VIP permanece com o serviço de nuvem ao qual é atribuído até que todas as VMs nesse serviço de nuvem sejam desalocadas ou excluídas. Quando isso ocorrer, ele será liberado.

### Qual endereço IP minha VM receberá?

- **Endereço IP interno:** se você implantar uma VM em uma rede virtual, a VM receberá um endereço IP interno de um pool de endereços IP internos que você especifica. As VMs se comunicam dentro das redes virtuais usando endereços IP internos. Embora o Azure atribua um endereço IP interno dinâmico, você pode solicitar um endereço estático para a VM. Para saber mais sobre endereços IP internos estáticos, visite [Como definir um IP interno estático](../articles/virtual-network/virtual-networks-reserved-private-ip.md).

- **VIP:** sua VM também está associada a um VIP, embora um VIP nunca seja atribuído à VM diretamente. Um VIP é um endereço IP público que pode ser atribuído ao serviço de nuvem. Você pode, se desejar, reservar um VIP para seu serviço de nuvem.

- **ILPIP:** você também pode configurar um endereço ILPIP (IP público em nível de instância) Os ILPIPs são diretamente associados à VM, e não ao serviço de nuvem. Para saber mais sobre ILPIPs, visite [Visão geral de IP público em nível de instância](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### Posso reservar um endereço IP interno para uma VM que criarei posteriormente?

Não. Não é possível reservar um endereço IP interno. Se um endereço IP interno estiver disponível, ele será atribuído a uma VM ou à instância de função pelo servidor DHCP. Essa VM pode ou não ser aquela à qual você deseja que o endereço IP interno seja atribuído. No entanto,você pode alterar o endereço IP interno de uma VM já criada para qualquer endereço IP interno disponível.

### Os endereços IP internos mudam para VMs em uma rede virtual?

Sim. Os endereços IP internos permanecem com a VM durante seu tempo de vida, a menos que a VM seja desalocada. Quando uma VM é desalocada, o endereço IP interno é liberado, a menos que você tenha definido um endereço IP interno estático para a VM Se a VM for simplesmente parada (e não colocada no status **Parada (Desalocada)**), o endereço IP permanecerá atribuído à VM.

### Posso atribuir manualmente os endereços IP a NICs em VMs?

Não. Você não deve alterar as propriedades da interface de VMs. As alterações podem levar à potencial perda de conectividade da VM.

### O que acontece com meus endereços IP se eu desligar uma VM?

Nada. Os endereços IP (VIP público e endereço IP interno) permanecerão com seu serviço de nuvem ou máquina virtual.

> [AZURE.NOTE] Se você deseja simplesmente desligar a VM, não use o Portal de Gerenciamento para fazer isso. Atualmente, o botão de desligamento desaloca a máquina virtual.

### Posso mover VMs de uma sub-rede para outra em uma rede virtual sem reimplantação?

Sim. Você pode encontrar mais informações [aqui](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md).

### Posso configurar um endereço MAC estático para minha VM?

Não. Um endereço MAC não pode ser configurado estaticamente.

### O endereço MAC permanecerá o mesmo para minha VM depois de criado?

Não, mas só será alterado se a VM for colocada no status Parado (Desalocado). Se você alterar o tamanho da VM, reinicializar ou, no caso da recuperação de serviço ou manutenção planejada do servidor host, o endereço MAC será retido.

### Posso me conectar à internet de uma VM em uma rede virtual?

Sim. Todos os serviços implantados em uma rede virtual podem se conectar à Internet. Além disso, cada serviço de nuvem implantado no Azure tem um VIP publicamente endereçável atribuído a ele. Você precisa definir pontos de extremidade de entrada para funções de PaaS e pontos de extremidade para VMs a fim de habilitar esses serviços para aceitar conexões da Internet.

## redes virtuais e serviços

### Quais serviços posso usar com redes virtuais?

Você só pode usar serviços de computação em redes virtuais. Os serviços de computação são limitados aos Serviços de Nuvem (funções web e de trabalho) e VMs.

### Posso usar Aplicativos Web com Rede Virtual?

Sim. Você pode implantar Aplicativos Web em uma Rede Virtual usando o ASE (Ambiente do Serviço de Aplicativo). Além disso, os Aplicativos Web poderão se conectar e acessar os recursos na Rede Virtual do Azure se você tiver configurado o ponto a site para a sua Rede Virtual. Para saber mais, confira o seguinte:


- [Criando Aplicativos Web em um Ambiente do Serviço de Aplicativo](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Integração da rede virtual de Aplicativos Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Usando conexões híbridas e integração de rede virtual com Aplicativos Web](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Integrar um aplicativo Web a uma Rede Virtual do Azure](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### Posso implantar serviços de nuvem com funções web e de trabalho (PaaS) em uma rede virtual?

Sim. Você pode implantar serviços PaaS em redes virtuais.

### Como implanto funções PaaS em uma rede virtual?

Você pode fazer isso especificando o nome e os mapeamentos de função/sub-rede da rede virtual na seção de configuração de rede da sua configuração de serviço. Não é preciso atualizar nenhum dos binários.

### Posso mover meus serviços para dentro e fora das redes virtuais?

Não. Não é possível mover serviços para dentro e fora das redes virtuais. Será preciso excluir e reimplantar o serviço para movê-lo para outra rede virtual.

## redes virtuais e segurança

### Qual é o modelo de segurança para redes virtuais?

As redes virtuais são completamente isoladas umas das outras e de outros serviços hospedados na infraestrutura do Azure. Uma rede virtual é um limite de confiança.

### Posso definir ACLs ou NSGs em minhas redes virtuais?

Não. Não é possível associar ACLs ou NSGs a redes virtuais. No entanto, as ACLs podem ser definidas em pontos de extremidade de entrada para VMs que foram implantadas em redes virtuais e NSGs podem ser associados a sub-redes ou NICs.

### Existe um white paper sobre segurança da rede virtual?

Sim. Você pode baixá-lo [aqui](http://go.microsoft.com/fwlink/?LinkId=386611).

## APIs, esquemas e ferramentas

### Posso gerenciar redes virtuais usando código?

Sim. Você pode usar APIs REST para gerenciar redes virtuais e conectividade entre locais. Mais informações podem ser encontradas [aqui](http://go.microsoft.com/fwlink/?LinkId=296833).

### Há suporte a ferramentas para redes virtuais?

Sim. Você pode usar ferramentas de linha de comando e o PowerShell para uma variedade de plataformas. Mais informações podem ser encontradas [aqui](http://go.microsoft.com/fwlink/?LinkId=317721).

<!---HONumber=AcomDC_0720_2016-->