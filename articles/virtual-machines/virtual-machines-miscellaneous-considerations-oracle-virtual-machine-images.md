<properties 
pageTitle="Considerações diversas sobre imagens de máquinas virtuais do Oracle"
	description="Conheça considerações adicionais antes de implantar uma máquina virtual do Oracle no Azure."
	services="virtual-machines"
	documentationCenter=""
	manager=""
	authors="bbenz"
	tags=""/>

<tags
ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz"/>

#Considerações diversas sobre imagens de máquinas virtuais do Oracle
Este artigo aborda considerações sobre máquinas virtuais do Oracle no Azure, que se baseiam em imagens de software Oracle fornecidas pela Microsoft, com o Windows Server como sistema operacional.

-  Imagens de máquina virtual no Oracle Database
-  Imagens de máquina virtual do Oracle WebLogic Server
-  Imagens de máquina virtual no JDK do Oracle

##Imagens de máquina virtual no Oracle Database
### Não há suporte para clustering (RAC)

Atualmente, o Azure não dá suporte ao Oracle Real Application Clusters (RAC) do Banco de Dados Oracle. Somente instâncias autônomas do Oracle Database são permitidas. Isso ocorre porque atualmente o Azure não dá suporte ao compartilhamento de disco virtual de uma maneira de leitura/gravação entre várias instâncias de máquinas virtuais. O UDP multicast também não tem suporte.

### Nenhum IP interno estático

O Azure atribui um endereço IP interno a cada máquina virtual. A menos que a máquina virtual faça parte de uma rede virtual, o endereço IP da máquina virtual será dinâmico e poderá ser alterado depois de reiniciar a máquina virtual. Isso pode causar problemas porque o Oracle Database espera que o endereço IP seja estático. Para evitar esse problema, considere adicionar a máquina virtual a uma Rede Virtual do Azure. Consulte [Rede Virtual](http://azure.microsoft.com/documentation/services/virtual-network/) e [Criar uma rede virtual no Azure](create-virtual-network.md) para saber mais.

### Opções de configuração de disco anexado

Você pode colocar arquivos de dados no disco do sistema operacional da máquina virtual ou em discos anexados, também conhecidos como discos de dados. Os discos anexados podem oferecer melhor desempenho e flexibilidade de tamanho do que o disco do sistema operacional. O disco do sistema operacional pode ser preferível somente para bancos de dados de até 10 gigabytes (GB).

Os discos anexados se baseiam no serviço de armazenamento de Blobs do Azure. Cada disco é capaz de fornecer uma velocidade máxima teórica de aproximadamente 500 operações de entrada/saída por segundo (IOPS). O desempenho dos discos anexados pode não ser o ideal inicialmente, e o desempenho de IOPS pode melhorar consideravelmente após um período de "burn-in" de cerca de 60 a 90 minutos de operação contínua. Se um disco permanecer ocioso depois disso, o desempenho do IOPS poderá ser reduzido até outro período de burn-in na operação contínua. Em resumo, quanto mais ativo for um disco, mais provável é que ele se aproxime do desempenho ideal de IOPS.

Embora a abordagem mais simples seja anexar um disco único à máquina virtual e colocar os arquivos de banco de dados nesse disco, essa abordagem também é mais restritiva em termos de desempenho. Em vez disso, frequentemente é possível melhorar o desempenho do IOPS se você usar vários discos anexados, espalhar os dados do banco de dados entre eles e usar o ASM (Automatic Storage Management) do Oracle. Consulte [Visão geral do Armazenamento Automático do Oracle](http://www.oracle.com/technetwork/database/index-100339.html) para saber mais. Embora seja possível usar particionamento em vários discos no nível do sistemas operacionais, essa abordagem não é recomendada porque não é comprovado que ela melhore o desempenho de IOPS.

Considere duas abordagens diferentes para anexar vários discos com base em se você deseja priorizar o desempenho de operações de leitura ou de gravação do banco de dados:

- O **Oracle ASM sozinho** costuma resultar em um melhor desempenho de operação de gravação, mas em um IOPS pior para operações de leitura em comparação com a abordagem que usa pools de armazenamento do Windows Server 2012. A ilustração a seguir mostra essa organização logicamente. ![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image2.png)

- O **Oracle ASM com pools de armazenamento do Windows Server 2012** provavelmente resultará em um melhor desempenho de operações de leitura do IOPS se o seu banco de dados executa primariamente operações de leitura, ou se você valorizar o desempenho de operações de leitura mais que o de operações de gravação. Uma imagem com base no sistema operacional Windows Server 2012 é necessária. Consulte [Implantar espaços de armazenamento em um servidor autônomo](http://technet.microsoft.com/library/jj822938.aspx) para saber mais sobre pools de armazenamento. Dessa forma, dois subconjuntos iguais de discos anexados primeiro são "distribuídos" juntos como discos físicos em dois volumes de pool de armazenamento e, em seguida, os volumes são adicionados a um grupo de discos ASM. A ilustração a seguir mostra essa organização logicamente.

	![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image3.png)

>[AZURE.IMPORTANT]Avalie o equilíbrio entre o desempenho de gravação e leitura caso a caso. Os resultados reais podem variar ao usar essas abordagens.

### Considerações sobre alta disponibilidade e recuperação de desastres

Ao usar o Banco de Dados Oracle em máquinas virtuais do Azure, você será responsável por implementar uma solução de recuperação de desastres e alta disponibilidade para evitar que haja tempo de inatividade. Você também é responsável pelo backup de seus dados e aplicativos.

É possível obter alta disponibilidade e recuperação de desastres para o Oracle Database Enterprise Edition (sem RAC) no Azure usando o [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) ou [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), com dois bancos de dados em duas máquinas virtuais separadas. As duas máquinas virtuais devem estar no mesmo [serviço de nuvem](cloud-services-connect-virtual-machine.md) e na mesma [rede virtual](http://azure.microsoft.com/documentation/services/virtual-network/) para garantir que possam acessar umas às outras por um endereço IP privado persistente. Além disso, é recomendável colocar as máquinas virtuais no mesmo [conjunto de disponibilidade](manage-availability-virtual-machines.md) para permitir que o Azure as coloque em domínios de falha e domínios de atualização separados. Observe que apenas máquinas virtuais no mesmo serviço de nuvem podem participar do mesmo conjunto de disponibilidade. Cada máquina virtual deve ter pelo menos 2 GB de memória e 5 GB de espaço em disco.

Com o Oracle Data Guard, a alta disponibilidade pode ser obtida com um banco de dados primário em uma máquina virtual, um banco de dados secundário (em espera) em outra máquina virtual e replicação unidirecional entre eles. O resultado é o acesso de leitura à cópia do banco de dados. Com o Oracle GoldenGate, você pode configurar a replicação bidirecional entre dois bancos de dados. Para saber como configurar uma solução de alta disponibilidade para seus bancos de dados usando essas ferramentas, consulte a documentação do [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e do [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) no site do Oracle. Se precisar de acesso de leitura-gravação à cópia do banco de dados, você poderá usar o [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##Imagens de máquina virtual do Oracle WebLogic Server

-  **Há suporte para clustering apenas na Enterprise Edition.** Se estiver usando imagens do WebLogic Server licenciadas pela Microsoft (especificamente, aqueles com o Windows Server como sistema operacional), você está licenciado para usar o clustering do WebLogic somente quando usar a Enterprise Edition do WebLogic Server. Não use clustering com a Standard Edition do WebLogic Server.

-  **Tempos limite de conexão:** se o seu aplicativo se basear em conexões com pontos de extremidade públicos de outro serviço de nuvem do Azure (como um serviço da camada de banco de dados), o Azure poderá fechar essas conexões abertas após 4 minutos de inatividade. Isso poderá afetar os recursos e aplicativos que dependam de pools de conexões, uma vez que conexões inativas por mais do que o limite podem não permanecer válidas. Se isso afetar o aplicativo, considere habilitar a lógica "keep-alive" em seus pools de conexão.

	Observe que, se o ponto de extremidade for *interno* à implantação do serviço de nuvem do Azure (como uma máquina virtual de banco de dados autônomo dentro do *mesmo* serviço de nuvem que suas máquinas virtuais do WebLogic), a conexão é direta e não se baseia no balanceador de carga do Azure e, portanto, não está sujeita a um tempo limite da conexão.

-  **O multicast de protocolo UDP não tem suporte.** O Azure dá suporte ao unicast de UDP, mas não ao multicast ou à difusão. O WebLogic Server pode se basear nos recursos de unicast UDP do Azure. Para obter melhores resultados com o unicast UDP, é recomendável que o tamanho do cluster WebLogic seja mantido estático ou que no máximo 10 servidores gerenciados sejam incluídos no cluster.

-  **O WebLogic Server espera que as portas públicas e privadas sejam as mesmas para o acesso T3 (por exemplo, ao usar o Enterprise JavaBeans).** Considere um cenário de várias camadas em que um aplicativo de serviço de camada (EJB) está em execução em um cluster do WebLogic Server que consiste em dois ou mais servidores gerenciados, em um serviço de nuvem chamado **SLWLS**. A camada do cliente está em um serviço de nuvem diferente, executando um programa Java simples tentando chamar o EJB na camada de serviço. Como é necessário balancear a carga da camada de serviço, um ponto de extremidade público com balanceamento de carga precisa ser criado para Máquinas Virtuais no cluster do WebLogic Server. Se a porta privada que você especificar para o ponto de extremidade for diferente da porta pública (por exemplo, 7006:7008), ocorrerá um erro como o seguinte:

		[java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

		Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

	Isso ocorre porque para qualquer acesso remoto do T3, o WebLogic Server espera que a porta do balanceador de carga e a porta do servidor gerenciado WebLogic sejam iguais. No caso acima, o cliente está acessando a porta 7006 (a porta do balanceador de carga) e o servidor gerenciado está escutando na 7008 (a porta privada). Observe que essa restrição se aplica somente ao acesso T3, não HTTP.

	Para evitar esse problema, use uma das seguintes alternativas:

	-  Use os mesmos números de porta pública e privada para pontos de extremidade com balanceamento de carga dedicados ao acesso T3.

	-  Inclua o seguinte parâmetro JVM ao iniciar o WebLogic Server:

			-Dweblogic.rjvm.enableprotocolswitch=true

Para obter informações relacionadas, consulte o artigo **860340.1** da Base de Conhecimentos em <http://support.oracle.com>.

-  **Limitações de balanceamento de carga e clustering dinâmico.** Suponha que você queira usar um cluster dinâmico no WebLogic Server e expô-lo por meio de um ponto de extremidade único, com balanceamento de carga, no Azure. Isso poderá ser feito, desde que você use um número da porta fixa para cada um dos servidores gerenciados (atribuídos dinamicamente dentro de um intervalo) e não inicie mais servidores gerenciados do que o número de máquinas monitoradas pelo administrador (ou seja, não mais que um servidor gerenciado por máquina virtual). Se sua configuração fizer com que sejam iniciados mais servidores WebLogic do que há máquinas virtuais (isto é, várias instâncias do WebLogic Server compartilharão a mesma máquina virtual), não será possível que mais de uma dessas instâncias do WebLogic Server se associe a um determinado número da porta e as outras na máquina virtual falharão.

	Por outro lado, se você configurar o servidor de administração para atribuir automaticamente números de porta exclusivos para os servidores gerenciados, o balanceamento de carga não será possível porque o Azure não dá suporte ao mapeamento de uma única porta pública para várias portas privadas, como seria necessário para esta configuração.

-  **Várias instâncias do Weblogic Server em uma máquina virtual.** Dependendo dos requisitos da sua implantação, você pode considerar a opção de executar várias instâncias do WebLogic Server na mesma máquina virtual, se ela for grande o suficiente. Por exemplo, em uma máquina virtual de tamanho médio, que contém dois núcleos, você poderia optar por executar duas instâncias do WebLogic Server. No entanto, observe que ainda é recomendado que você evite introduzir pontos únicos de falha em sua arquitetura, como seria o caso se você usasse apenas uma máquina virtual que esteja executando várias instâncias do WebLogic Server. Usar pelo menos duas máquinas virtuais poderia ser uma abordagem melhor, e cada uma dessas máquinas virtuais poderia então executar várias instâncias do WebLogic Server. Cada uma dessas instâncias de WebLogic Server ainda pode fazer parte do mesmo cluster. Observe, no entanto, que atualmente não é possível usar o Azure para balancear a carga dos pontos de extremidade expostos por essas implantações do WebLogic Server na mesma máquina virtual, pois o balanceador de carga do Azure exige que os servidores com balanceamento de carga sejam distribuídos entre máquinas virtuais exclusivas.

##Imagens de máquina virtual no JDK do Oracle

-  **Atualizações mais recentes do JDK 6 e 7.** Embora seja recomendável usar a versão mais recente com suporte público do Java (atualmente, o Java 8), o Azure também disponibiliza imagens do JDK 6 e 7. Eles são voltados a aplicativos herdados que ainda não estão prontos para serem atualizado para o JDK 8. Enquanto atualizações para imagens do JDK anteriores podem não estar disponíveis para público em geral, devido à parceria da Microsoft com a Oracle, as imagens do JDK 6 e 7 fornecidas pelo Azure devem conter uma atualização mais recente, não pública, que normalmente é oferecida pela Oracle somente para um grupo selecionado de clientes com suporte da Oracle. As novas versões das imagens do JDK ficarão disponíveis com o passar do tempo, com versões atualizadas do JDK 6 e 7.

	Observe que o JDK disponível nesse nessas imagens do JDK 6 e 7, as máquinas virtuais e imagens derivadas deles só podem ser usados dentro do Azure.

-  **JDK de 64 bits.** As imagens de máquina virtual do Oracle WebLogic Server e as imagens de máquina virtual do Oracle JDK fornecidas pelo Azure contêm as versões de 64 bits do Windows Server e o JDK.

##Recursos adicionais
[Imagens de máquina virtual Oracle para Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=August15_HO9-->