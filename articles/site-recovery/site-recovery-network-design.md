<properties
	pageTitle="Criando sua infraestrutura de rede para a recuperação de desastres | Microsoft Azure"
	description="Este artigo analisa as considerações de design de rede para o Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="prateek9us"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/22/2016"
	ms.author="pratshar"/>

#  Criando sua infraestrutura de rede para a recuperação de desastres

Este artigo é direcionado a profissionais de TI que são responsáveis pela arquitetura, implementação e suporte para a infraestrutura da continuidade de negócios e recuperação de desastres (BCDR), e que desejam aproveitar o Microsoft Azure Site Recovery (ASR) para dar suporte e fortalecer seus serviços BCDR. Este documento analisa as considerações práticas para a implantação do servidor do System Center Virtual Machine Manager, os prós e contras das sub-redes ampliadas vs. o failover da sub-rede e como estruturar a recuperação de desastres para os sites virtuais no Microsoft Azure.

## Visão geral

[O Azure Site Recovery (ASR)](https://azure.microsoft.com/services/site-recovery/) é um serviço do Microsoft Azure que gerencia a proteção e a recuperação de seus aplicativos virtualizados para fins da continuidade de negócios e recuperação de desastres (BCDR). Este documento destina-se para guiar o leitor no processo de criação de redes, focando em arquitetar intervalos IP e sub-redes no site de recuperação de desastres ao replicar as máquinas virtuais (VMs) usando o Site Recovery.

Além disso, este artigo demonstra como o Site Recovery permite arquitetar e implementar um datacenter virtual multissite para dar suporte aos serviços BCDR no momento do teste ou de um desastre.

Em um mundo onde todos esperam uma conectividade 24/7, é mais importante do que nunca manter sua infraestrutura e aplicativos funcionando. A finalidade da Continuidade de Negócios e Recuperação de Desastres (BCDR) é restaurar os componentes com falha para que a organização possa retomar rapidamente as operações normais. Desenvolver estratégias de recuperação de desastres para lidar com eventos devastadores e improváveis é um grande desafio. Isso é devido à dificuldade inerente de prever o futuro, especialmente quando se trata de eventos improváveis e de alto custo para fornecer as medidas adequadas de proteção contra catástrofes abrangentes.

É crucial que o planejamento BCDR, Objetivo do Tempo de Recuperação (RTO) e Objetivo do Ponto de Recuperação (RPO) sejam definidos como parte de um plano de recuperação de desastres. Quando um desastre atinge o datacenter do cliente, usando o Azure Site Recovery, os clientes podem colocar online rapidamente (baixo RTO) suas máquinas virtuais replicadas localizadas no datacenter secundário ou no Microsoft Azure com uma perda mínima de dados (baixo RPO).

O failover é possibilitado pelo ASR que inicialmente copia as máquinas virtuais designadas do datacenter primário para o datacenter secundário ou para o Azure (dependendo do cenário), em seguida, atualiza periodicamente as réplicas. Durante o planejamento da infraestrutura, o design da rede deve ser considerado como um gargalo em potencial que pode impedir que você atenda os objetivos RTO e RPO da empresa.

Quando os administradores estiverem planejando implantar uma solução de recuperação de desastres, uma das principais questões em suas mentes será como a máquina virtual deve ser acessível após a conclusão do failover. Usando o Mapeamento de Rede no ASR, o administrador pode escolher a qual rede a máquina virtual deve ser anexada após o failover. Para obter mais informações sobre o Mapeamento de Rede, consulte [Preparar-se para o mapeamento de rede](site-recovery-network-mapping.md).

Ao criar a rede para o site de recuperação, o administrador tem duas opções:

- Usar um intervalo de endereços IP diferente para a rede no site de recuperação. Nesse cenário, a máquina virtual, após o failover, terá um novo endereço IP e o administrador terá de fazer uma atualização de DNS. Leia mais sobre como fazer a atualização de DNS aqui
- Usar o mesmo intervalo de endereços IP para a rede no site de recuperação. Em determinados cenários, os administradores preferem manter os endereços IP que eles têm no site primário, mesmo após o failover. Em um cenário normal, um administrador teria que atualizar as rotas para indicar o novo local dos endereços IP. Mas no cenário no qual uma VLAN ampliada é implantada entre os sites primário e de recuperação, reter os endereços IP para as máquinas virtuais torna-se uma opção atraente. Manter os mesmos endereços IP simplifica o processo de recuperação eliminando qualquer etapa pós-failover relacionada à rede.

Este documento analisa duas principais considerações técnicas para a criação de seu espaço de endereço da rede para a recuperação de desastres:

- Criar a topologia de implantação do fabric manager (System Center Virtual Machine Manager) para proteger as cargas de trabalho por meio do Azure Site Recovery (ASR).
- Gerenciamento do Endereço IP em um ambiente de recuperação de desastres.


## Criar a implantação do System Center VMM para o Azure Site Recovery

Os requisitos de negócios ditam as decisões de topologia. Com base nesses requisitos de negócios, uma organização pode optar por ter vários datacenters gerenciados por um único líder de gerenciamento ou vários líderes de gerenciamento gerenciando datacenters diferentes. Esta seção descreve como uma organização cria uma topologia de implantação do System Center Virtual Machine Manager (SCVMM) para proteger as cargas de trabalho com o ASR.

O Azure Site Recovery colabora para a estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Com base no princípio de simplicidade da criação, o ASR é modelado de forma a permitir que os administradores criem uma solução de recuperação de desastres em suas topologias de datacenter existentes. Assim o ASR permite que as empresas escolham uma topologia que melhor atenda às suas necessidades de negócios.

Para obter uma lista completa dos cenários de implantação, consulte [O que é Recuperação de Site?](site-recovery-overview.md)

Os clientes podem proteger as cargas de trabalho em um servidor VMM no site primário com um Servidor VMM gerenciando o site secundário. Em caso de desastre, todas as cargas de trabalho farão diretamente o failover do site primário para o site secundário que está sendo gerenciado pelo servidor VMM secundário. No entanto, algumas organizações talvez queiram usar um único servidor VMM para gerenciar todos os seus datacenters para evitar a sobrecarga de gerenciamento. Para um cliente com essa topologia, é fundamental ser capaz de recuperar o servidor VMM antes que as cargas de trabalho possam ser recuperadas. Como resultado, um cliente deve criar com cuidado suas topologias VMM para tornar suas cargas de trabalho à "prova de desastres".

Se você tiver apenas um servidor VMM em sua infraestrutura, será possível implantar o Azure Site Recovery para replicar as máquinas virtuais nas nuvens VMM para o Azure ou replicar entre as nuvens em um único servidor VMM. Para a recuperação, você precisará realizar manualmente o failover do servidor VMM de fora do console do Azure Site Recovery usando a Réplica do Hyper-V no console do Gerenciador do Hyper-V. É recomendável implantar o Servidor VMM em uma das seguintes topologias para conseguir recuperar as cargas de trabalho com os melhores RTO (Objetivo do Tempo de Recuperação) e RPO (Objetivo do Ponto de Recuperação) possível.

### Implantação autônoma

Nessa topologia, você implantará um servidor VMM autônomo em uma máquina virtual no site primário, em seguida, replicará essa máquina virtual para um site secundário com o Azure Site Recovery e a Réplica do Hyper-V. Em alguns casos, instalar o servidor VMM e seu SQL Server de suporte na mesma máquina virtual pode reduzir o tempo de inatividade porque apenas uma VM deve ser instanciada. Quando o serviço VMM está usando um SQL Server remoto, você precisa recuperar a instância do SQL Server antes de recuperar o servidor VMM.

As etapas para implantar um único VMM em uma VM com a Réplica do Hyper-V são:

1. Configure o VMM em uma VM com o SQL Server instalado.
2. Adicione hosts a serem gerenciados a nuvens no servidor VMM.
3. Faça logon no portal do Azure e, em seguida, configure nuvens para proteção.
4. Habilite a replicação para todas as VMs que precisam ser protegidas pelo servidor VMM.
5. Vá para o console do Gerenciador do Hyper-V, escolha o Hyper-V Replica e, em seguida, habilite a replicação na VM do VMM. Certifique-se de que a VM do VMM não é adicionada para as nuvens que estão protegidas pelo serviço ASR para que as configurações de replicação do Hyper-V não sejam substituídas pelo ASR.

No caso de um desastre, as cargas de trabalho podem ser recuperadas usando as seguintes etapas:

1. Faça o failover da VM do VMM de réplica para o site de recuperação usando o Gerenciador do Hyper-V.
2. Depois que a VM do VMM for recuperada, o usuário poderá fazer logon no Hyper-V Recovery Manager do site secundário.
3. Após a conclusão do failover não planejado os usuários poderão acessar todos os recursos no site principal.


Essa topologia exige que o usuário faça o failover manualmente de sua VM do VMM para o site secundário antes de poder fazer o failover de suas cargas de trabalho.

![autônomo](./media/site-recovery-network-design/standalone.png)

### Implantação de cluster


Implantar um servidor VMM altamente disponível é um método de fazer com que o próprio serviço VMM reconheça o cluster. Isso será útil se as cargas de trabalho críticas estiverem sendo gerenciadas pelo VMM, pois isso garante a disponibilidade da carga de trabalho e protege um servidor VMM de falhas. É extremamente importante ter o servidor VMM disponível sempre. Fazer com que o serviço reconheça o cluster ajuda a proteger o servidor VMM do failover de hardware do host no qual o Servidor VMM está em execução, bem como proteger dos problemas que podem ocorrer na VM na qual o VMM está em execução. Implantar um servidor VMM altamente disponível requer que o servidor VMM esteja presente em um Cluster de Failover do Windows Server. Para obter mais informações sobre como implantar um servidor VMM altamente disponível, consulte a postagem System Center do blog disponível aqui.

Ao proteger as cargas de trabalho usando o ASR, o servidor VMM deve ser implantado em um cluster ampliado em sites geograficamente separados, conforme mostrado na Figura 2. O banco de dados SQL Server usados pelo VMM deve ser protegido com os grupos de disponibilidade AlwaysOn do SQL Server com uma réplica no site secundário. Se ocorrer um desastre, o servidor VMM e seu banco de dados SQL Server correspondente automaticamente farão o failover para o site de recuperação, depois disso todas as cargas de trabalho poderão sofrer failover usando o ASR.

![Cluster do VMM ampliado](./media/site-recovery-network-design/network-design1.png)

Figura 2

## Criando o espaço do endereço de rede em um ambiente de recuperação de desastres

Quando os administradores estiverem planejando implantar uma solução de recuperação de desastres, uma das principais questões em suas mentes será como os aplicativos ficarão acessíveis após a conclusão do failover. Até certo ponto, os aplicativos modernos são quase sempre dependentes da rede, portanto, mover fisicamente um serviço de um site para outro representa um desafio de rede. Há duas maneiras principais desse problema ser abordado nas soluções de recuperação de desastres. A primeira abordagem é manter endereços IP fixos. Apesar da movimentação dos serviços e dos servidores de hospedagem estarem em locais físicos diferentes, os aplicativos levam a configuração de endereço IP com eles para o novo local. A segunda abordagem envolve a alteração completa do endereço IP durante a transição para o site recuperado. Cada abordagem possui diversas variações de implementação que são resumidas abaixo.

Ao criar a rede para o site de recuperação, o administrador tem duas opções:

### Opção 1: Manter os endereços IP 

De uma perspectiva do processo de recuperação de desastres, usar endereços IP fixos parece ser o método mais fácil de implementar, mas tem uma série de desafios em potencial que tornam a abordagem menos popular na prática. O Azure Site Recovery fornece a capacidade de manter os endereços IP em todos os cenários. Antes de se decidir manter o IP, a devida consideração deve ser dada às restrições impostas nos recursos de failover. Vejamos os fatores que podem ajudá-lo a tomar uma decisão para manter os endereços IP ou não. Nessa categoria, há dois subtipos principais: sub-rede ampliada e failover da sub-rede. Iremos analisar separadamente os cenários quando há um failover da sub-rede ou uma sub-rede ampliada em dois locais.

#### Sub-rede Ampliada

Aqui, a sub-rede é disponibilizada simultaneamente nos locais primário e DR (recuperação de desastres). Simplificando, isso significa que você pode mover um servidor e sua configuração IP (Camada 3) para o segundo site, e a rede roteará o tráfego para o novo local automaticamente. Isso é trivial para se lidar do ponto de vista do servidor, mas há uma série de desafios:

- Do ponto de vista da Camada 2 (camada do link de dados), irá requerer um equipamento de rede que possa gerenciar uma VLAN ampliada, mas isto se tornou um problema menor pois, agora, está amplamente disponível. O segundo problema e mais difícil é que ampliando a VLAN, o domínio de falha em potencial estende-se para os dois sites, basicamente se tornando um ponto único de falha. Embora seja uma ocorrência improvável, pode acontecer de uma tempestade de transmissão iniciar, mas não conseguir ser isolada. Encontramos opiniões mistas sobre esse último problema e vimos muitas implementações bem-sucedidas, bem como "nunca implementaremos essa tecnologia aqui".
- A sub-rede ampliada não será possível se você estiver usando o Microsoft Azure como o site DR (recuperação de desastres).


#### Failover de sub-rede

É possível implementar o failover da sub-rede para obter os benefícios da solução de sub-rede ampliada descrita acima sem ampliar a sub-rede em vários sites. Aqui, qualquer sub-rede dada estaria presente no Site 1 ou Site 2, mas nunca em ambos os sites simultaneamente. Para manter o espaço do endereço IP no caso de um failover, é possível organizar por meio da programação a infraestrutura do roteador para mover as sub-redes de um site para outro. Em um cenário de failover, as sub-redes seriam movidas com suas VMs associadas protegidas. A principal desvantagem dessa abordagem é que, no caso de falha, você precisa mover toda a sub-rede, o que pode ser bom, mas pode afetar as considerações de granularidade do failover.

Vamos examinar como uma empresa fictícia, denominada Contoso, é capaz de replicar suas VMs para um local de recuperação durante o failover na sub-rede inteira. Primeiro, vejamos como a Contoso é capaz de gerenciar suas sub-redes enquanto replica as VMs entre dois locais, então, analisemos como o failover da sub-rede funciona quando o Azure é usado como o site de recuperação de desastres.

##### Failover da sub-rede – DR no Nível Empresarial

Vejamos um cenário no qual desejamos manter o IP de cada uma das VMs e fazer o failover na sub-rede inteira. O site principal tem aplicativos em execução na sub-rede 192.168.1.0/24. Quando o failover ocorre, todas as máquinas virtuais que fazem parte dessa sub-rede farão o failover para o site de recuperação e manterão seus endereços IP. As rotas terão de ser adequadamente modificadas para refletir o fato de que todas as máquinas virtuais que pertencem à sub-rede 192.168.1.0/24 agora foram movidas para o site de recuperação.

Na ilustração a seguir, as rotas entre o site primário e o site de recuperação, o terceiro site e o site primário, e o terceiro site e o site de recuperação precisarão ser modificadas devidamente. As seguintes suposições foram feitas para a versão inicial deste documento:

- Cada datacenter é atendido por sua própria instância do VMM do System Center. Não haverá nenhuma replicação de bancos de dados do System Center VMM entre data centers.
- Cada datacenter usará endereços IP estáticos para as máquinas virtuais.
- A conectividade entre os datacenters é por meio de um circuito dedicado, e não via conectividade VPN pela Internet.

	![Manter o endereço IP](./media/site-recovery-network-design/network-design3.png)

	A figura 3

	![Manter o endereço IP](./media/site-recovery-network-design/network-design2.png)
	
	Figura 4

Ao habilitar a proteção para uma máquina virtual específica, o ASR alocará os recursos de rede de acordo com o seguinte fluxo de trabalho:

- O ASR aloca um endereço IP para cada interface de rede na máquina virtual a partir do pool de endereços IP estáticos definido na rede relevante para cada instância do VMM do System Center.
- Se o administrador definir o mesmo pool de endereços IP, para a rede no site de recuperação, do pool de endereços IP da rede no site primário, enquanto aloca o endereço IP para a máquina virtual de réplica, o ASR alocará o mesmo endereço IP da máquina virtual primária. O IP é reservado no VMM, mas não definido como o IP de failover. O IP de failover é definido apenas antes do failover.

	![Manter o endereço IP](./media/site-recovery-network-design/network-design4.png)
	
	Figura 5

A Figura 5 mostra as configurações do TCP/IP de Failover para a máquina virtual de réplica (no console do Hyper-V). Essas configurações seriam preenchidas antes da máquina virtual ser iniciada após um failover

Se o mesmo IP não estiver disponível, o ASR alocará outro endereço IP disponível a partir do pool de endereços IP definido.

Depois da máquina virtual ser habilitada para a proteção, você poderá usar o seguinte script de exemplo para verificar o IP que foi alocado para a máquina virtual. O mesmo IP seria definido como o IP de Failover e atribuído à VM no momento do failover:

![Manter o endereço IP](./media/site-recovery-network-design/script.png)

>[AZURE.NOTE] No cenário no qual as máquinas virtuais usam o DHCP, o gerenciamento dos endereços IP fica completamente fora do controle do ASR. Um administrador precisa garantir que o servidor DHCP, que atende os endereços IP no site de recuperação, possa atender a partir do mesmo intervalo do site primário.

##### Failover da Sub-rede Local – DR (recuperação de desastres) para o Azure

O Azure Site Recovery (ASR) permite que o Microsoft Azure seja usado como um site de recuperação de desastres para suas máquinas virtuais. Nesse caso, você precisará lidar com mais de restrição.

Examinemos um cenário no qual uma empresa fictícia, denominada Banco Woodgrove, tem uma infraestrutura local que hospeda sua linha de aplicativos de negócios e eles estão hospedando seus aplicativos móveis no Azure. A conectividade entre as VMs do Banco Woodgrove no Azure e os servidores locais é fornecida por uma VPN (rede virtual privada) site a site (S2S). A VPN S2S permite que a rede virtual do Banco Woodgrove no Azure seja vista como uma extensão da rede do local do Banco. Essa comunicação é habilitada pela VPN S2S entre a borda do Banco Woodgrove e a rede virtual do Azure. Agora, o Woodgrove deseja usar o ASR para replicar suas cargas de trabalho em execução em seu datacenter para o Azure. Essa opção atenda às necessidades do Woodgrove, que deseja uma opção de DR (recuperação de desastres) econômica e capaz de armazenar os dados em ambientes de nuvem públicos. O Woodgrove tem de lidar com aplicativos e configurações que dependem de endereços IP codificados especificamente, então, precisa manter os endereços IP para seus aplicativos após o failover no Azure.

A Infraestrutura de local do Woodgrove é gerenciada por um servidor VMM 2012 R2. Uma rede lógica baseada em VLAN, denominada Rede de Aplicativos, foi criada no servidor VMM do System Center. Uma rede VM, denominada Rede de VM do Aplicativo, é criada usando a rede lógica. Todas as máquinas virtuais no aplicativo usam endereços IP estáticos, portanto, um pool de IPs estáticos também é definido para a rede lógica.

**Rede lógica**

![Rede lógica](./media/site-recovery-network-design/network-design5.png)

Figura 6

**Rede VM**

![Rede VM](./media/site-recovery-network-design/network-design6.png)

Figura 7

O Woodgrove decidiu atribuir endereços IP do intervalo de endereços IP (172.16.1.0/24, 172.16.2.0/24) para seus recursos em execução no Azure.

Para o Woodgrove conseguir replicar suas máquinas virtuais no Azure, mantendo os endereços IP, uma Rede Virtual do Azure precisa ser criada. Deve ser uma extensão da rede local para que os aplicativos possam fazer o failover do site local para o Azure diretamente. O Azure permite que você adicione a conectividade VPN ponto a site, assim como a site a site, às redes virtuais criadas no Azure. Ao configurar sua conexão site a site, a rede Azure permitirá rotear o tráfego para o local (o Azure chama isso de rede local) somente se o intervalo de endereços IP for diferente do intervalo de endereços IP local porque o Azure não oferece suporte à ampliação das sub-redes. Isso significa que, se você tiver uma sub-rede 192.168.1.0/24 local, não é possível adicionar 192.168.1.0/24 na rede do Azure. Isso é esperado porque o Azure não sabe que não há nenhuma VMs ativa na sub-rede e que a sub-rede está sendo criada apenas para fins de recuperação de desastres. Para poder rotear corretamente o tráfego para fora de uma rede do Azure, as sub-redes na rede e a rede local não devem entrar em conflito.

![Failover da sub-rede](./media/site-recovery-network-design/network-design7.png)

Figura 8

Para ajudar o Woodgrove a atender seus requisitos de negócios, precisamos implementar os fluxos de trabalho os seguir:

- Crie uma rede adicional, chame-a de Rede de Recuperação, onde as máquinas virtuais de failover seriam criadas.
- Para garantir que o IP de uma VM seja mantido após um failover, vá para a guia Configurar em Propriedades da VM, especifique o mesmo IP que d máquina virtual tem no local, em seguida, clique em Salvar. Quando a VM tiver um failover, o Azure Site Recovery atribuirá o IP fornecido para a máquina virtual. 

	![Propriedades da rede](./media/site-recovery-network-design/network-design8.png)

	Figura 9

Assim que o failover for inicializado e as máquinas virtuais forem criadas na Rede de Recuperação com o IP desejado, a conectividade com essa rede poderá ser estabelecida usando uma Conexão Vnet a Vnet. Se requerido, essa ação poderá ser por script. Como analisado na seção anterior sobre o failover da sub-rede, mesmo no caso de failover para o Azure, as rotas teriam que ser adequadamente modificadas para refletir que o 192.168.1.0/24 agora foi movido para o Azure.

![Propriedades da rede](./media/site-recovery-network-design/network-design9.png)

Figura 10

### Opção 2: Alterar os IP endereços

Essa abordagem parece ser mais predominante com base no que os autores têm visto. Ela assume a forma de alterar o endereço IP de cada VM envolvida no failover. Uma desvantagem dessa abordagem requer que a rede de entrada 'saiba' que o aplicativo que estava em IPx, agora está em IPy. Mesmo se IPx e IPy forem nomes lógicos, as entradas DNS normalmente precisam ser alteradas ou liberadas em toda a rede e as entradas em cache nas tabelas de rede precisam ser atualizadas ou liberadas, portanto, um tempo de inatividade pode ser visto dependendo de como a infraestrutura do DNS foi configurada. Esses problemas podem ser reduzidos usando valores de TTL baixos no caso dos aplicativos de intranet e usando o [Gerenciador de Tráfego do Azure com ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)para os aplicativos baseados na Internet.

#### Alterando os endereços IP - DR no Nível Empresarial

Vejamos o cenário no qual você planeja usar diferentes IPs nos sites primário e de recuperação. No exemplo a seguir, também temos um terceiro site de onde os aplicativos hospedados no site primário ou de recuperação podem ser acessados.

![IP diferente](./media/site-recovery-network-design/network-design10.png)

Figura 11

Na Figura 11, há alguns aplicativos hospedados na sub-rede 192.168.1.0/24 no site primário e eles foram configurados para aparecerem no site de recuperação na sub-rede 172.16.1.0/24 após um failover. Rotas de rede/conexões de VPN foram configuradas corretamente para que todos os três sites possam acessar um ao outro.
 
Como a Figura 12 mostra, depois do failover de um ou mais aplicativos, eles serão restaurados na sub-rede de recuperação. Nesse caso, não estamos restritos ao failover na sub-rede inteira ao mesmo tempo. Nenhuma alteração é necessária para reconfigurar a VPN nem as rotas da rede. Um failover e algumas atualizações DNS manterão os aplicativos acessíveis. Se o DNS for configurado para permitir atualizações dinâmicas, então, as máquinas virtuais se registrarão usando o novo IP assim que iniciarem após um failover.

![IP diferente](./media/site-recovery-network-design/network-design11.png)

Figura 12

Após o failover, a máquina virtual de réplica pode ter um endereço IP que não é igual ao endereço IP da máquina virtual primária. As máquinas virtuais atualizarão o servidor DNS que estiverem usando depois que forem iniciadas. As entradas DNS normalmente precisam ser alteradas ou liberadas em toda a rede e as entradas em cache nas tabelas de rede precisam ser atualizadas ou liberadas, portanto, não é incomum enfrentar tempo de inatividade enquanto ocorrem essas alterações de estado. Esse problema pode ser reduzido:

- Usando valores TTL baixos para aplicativos de intranet.
- Usando o Gerenciador de Tráfego do Azure com o ASR para os aplicativos baseados na Internet.
- Usando o script a seguir em seu plano de recuperação para atualizar o servidor DNS para garantir uma atualização em tempo hábil (o script não é necessário se o registro de DNS dinâmico estiver configurado)

![IP diferente](./media/site-recovery-network-design/script2.png)

#### Alterar os endereços IP – DR (recuperação de desastres) para o Azure

A postagem [ Configuração da Infraestrutura de Rede como um Site de Recuperação de Desastres](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) do blog explica como configurar a infraestrutura de rede do Microsoft Azure necessária quando manter os endereços IP não é um requisito. Começa descrevendo o aplicativo, depois, examina como configurar a rede no local e no Azure, em seguida, termina com como fazer um failover de teste e um failover planejado.




## Próximas etapas

[Saiba](site-recovery-network-mapping.md) como a Recuperação de Site mapeia redes de origem e destino.

<!---HONumber=AcomDC_0224_2016-->