<properties 
	pageTitle="Executar Cassandra com Linux no Azure" 
	description="Explica como executar um cluster Cassandra no Linux em máquinas virtuais do Azure." 
	services="virtual-machines" 
	documentationCenter="nodejs" 
	authors="hanuk" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/01/2014" 
	ms.author="hanuk"/>





<h1><a id = ""></a>Executando Cassandra com Linux no Azure e acessando-a do Node.js </h1>
**Autor:** Hanu Kommalapati

## Sumário##

- [Visão geral][]
- [Implantação de região única][]
- [Testar o cluster de Cassandra de única região][]
- [Implantação em várias regiões][]
- [Testar o cluster de Cassandra de várias regiões][]
- [Testar o cluster de Cassandra do Node.js][]
- [Conclusão][]

##<a id="overview"> </a>Visão geral ##
O Microsoft Azure é uma plataforma de nuvem aberta que executa tanto os softwares da Microsoft como os não pertencentes à Microsoft e que inclui sistemas operacionais, servidores de aplicativos, middlewares de mensagens, bem como bancos de dados SQL e NoSQL de ambos os modelos de software livre e comercial. A criação de serviços resilientes em nuvens públicas, incluindo o Azure, requer um planejamento cuidadoso e uma arquitetura deliberada para ambos os servidores de aplicativos, bem como camadas de armazenamento. A arquitetura de armazenamento distribuída de Cassandra naturalmente ajuda na criação de sistemas altamente disponíveis que são tolerantes a falhas para as falhas de cluster. Cassandra é um banco de dados NoSQL de escala na nuvem mantido pela Apache Software Foundation em cassandra.apache.org; Cassandra é escrito em Java e, portanto, pode ser executado em ambas as plataformas Windows e Linux. 

O foco deste artigo é mostrar a implantação de Cassandra no Ubuntu como um cluster de centro de único ou de vários dados utilizando Máquinas Virtuais e Redes Virtuais do Microsoft Azure. A implantação do cluster para cargas de trabalho de produção otimizada está fora do escopo deste artigo porque requer configuração de nó em vários discos, design apropriado de topologia em anel e modelagem de dados para dar suporte à replicação necessária, consistência, taxa de transferência e requisitos de alta disponibilidade de dados. 

Este artigo adota uma abordagem fundamental para mostrar o que está envolvido na criação do cluster de Cassandra em comparação com Docker, Chef ou Puppet, os quais podem tornar a implantação da infraestrutura muito mais fácil.  

##<a id="depmodels"> </a>Os modelos de implantação ##
A rede do Microsoft Azure permite a implantação de clusters particulares isolados, o acesso pode ser restrito para atingir uma segurança de rede refinada.  Como este artigo é sobre a implantação de Cassandra num nível fundamental, não nos concentraremos no nível de consistência e no design de armazenamento ideal para a taxa de transferência. Aqui está a lista de requisitos de rede para o nosso cluster hipotético:

- Os sistemas externos não podem acessar o banco de dados de Cassandra desde dentro ou fora do Azure
- O cluster de Cassandra precisa estar atrás de um balanceador de carga de tráfego de poupança
- Implantar nós de Cassandra em dois grupos em cada data center para uma disponibilidade de cluster aprimorada 
- Bloquear o cluster até que apenas o farm de servidores do aplicativo tenha acesso ao banco de dados diretamente
- Nenhum ponto de extremidade de rede pública diferente de SSH
- Cada nó de Cassandra precisa de um endereço IP interno fixo

Cassandra pode ser implantado em uma única região do Azure ou em várias regiões, de acordo com a natureza distribuída da carga de trabalho. O modelo de implantação de várias regiões pode ser utilizado para atender os usuários finais mais próximos de uma determinada região geográfica por meio da mesma infraestrutura de Cassandra. A replicação do nó interno de Cassandra cuida da sincronização de gravações de vários mestres provenientes de vários data centers e apresenta uma exibição consistente dos dados para os aplicativos. A implantação de várias regiões também pode ajudar na redução do risco de falhas mais amplas de serviço do Azure. A consistência ajustável e a replicação de topologia de Cassandra ajudará a atender às diversas necessidades de RPO dos aplicativos. 


###<a id="oneregion"></a>Implantação de região única ###
Começaremos com uma implantação de região única e utilizaremos os aprendizados obtidos na criação de um modelo de várias regiões. A rede virtual do Azure será usada para criar sub-redes isoladas para que os requisitos de segurança da rede mencionados acima possam ser atendidos.  O processo descrito na criação da implantação de região única utiliza o Ubuntu 14.04 LTS e Cassandra 2.08; no entanto, o processo pode facilmente ser adotado para as outras variantes do Linux. A seguir estão algumas das características sistemáticas da implantação de região única.  

**Alta disponibilidade:** os nós de Cassandra mostrados na Figura 1 são implantados em dois conjuntos de disponibilidade, de modo que os nós sejam distribuídos entre vários domínios de falha de alta disponibilidade. As VMs anotadas com cada conjunto de disponibilidade são mapeadas para 2 domínios de falha. O Microsoft Azure utiliza o conceito de domínio de falha para gerenciar o tempo de inatividade não planejado (por exemplo, falhas de hardware ou software) enquanto o conceito de domínio de atualização (por exemplo, aplicação de patch/atualizações de SO host ou convidado, atualizações de aplicativo) é usado para gerenciar o tempo de inatividade agendado. Consulte [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](http://msdn.microsoft.com/pt-br/library/dn251004.aspx) para a função de domínios de falha e de atualização na obtenção de alta disponibilidade. 

![Single region deployment](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

Figura 1: Implantação de região única

Observe que, no momento da redação deste artigo, o Azure não permite o mapeamento explícito de um grupo de VMs em um domínio de falha específico; consequentemente, mesmo com o modelo de implantação mostrado na Figura 1, é estatisticamente provável que todas as máquinas virtuais possam ser mapeadas para dois domínios de falha em vez de quatro. 

**Balanceamento de carga de tráfego de poupança:** as bibliotecas de cliente de poupança dentro do servidor da web se conectam ao cluster por meio de um balanceador de carga interno. Isto exige o processo de adição do balanceador de carga interno à sub-rede "dados" (consulte a Figura 1) no contexto do serviço de nuvem que hospeda o cluster de Cassandra. Depois que o balanceador de carga interno for definido, cada nó exigirá que o ponto de extremidade com balanceamento de carga seja adicionado com as anotações de um conjunto com balanceamento de carga com o nome do balanceador de carga definido anteriormente. Consulte [Balanceamento de carga interno do Azure ](http://msdn.microsoft.com/pt-br/library/azure/dn690121.aspx)para obter mais detalhes.

**Sementes de cluster:** é importante selecionar os nós que estejam altamente disponíveis para sementes, pois os nós mais novos comunicar-se-ão com os nós de propagação para descobrir a topologia do cluster. Um nó de cada conjunto de disponibilidade será designado como nó de propagação para evitar pontos únicos de falha. 

**Fator de Replicação e Nível de Consistência:** a alta disponibilidade interna e a durabilidade de dados de Cassandra se caracterizada pelo Fator de Replicação (RF - número de cópias de cada linha armazenada no cluster) e o Nível de Consistência (número de réplicas a serem lidas/gravadas antes de retornar o resultado ao chamador). O Fator de Replicação é especificado durante a criação do KEYSPACE (semelhante a um banco de dados relacional), enquanto o Nível de Consistência é especificado durante a consulta CRUD. O Fator de Replicação é especificado durante a criação do KEYSPACE, enquanto o Nível de Consistência é especificado durante a consulta. Consulte a documentação de Cassandra em [Configuração para Consistência](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obter detalhes de consistência e a fórmula para o cálculo de quorum.

Cassandra dá suporte a dois tipos de modelos de integridade de dados - Consistência e Consistência Eventual; o Fator de Replicação e o Nível de Consistência juntos determinarão se os dados serão consistentes logo que uma operação de gravação for concluída ou se estarão eventualmente consistentes. Por exemplo, a especificação de QUORUM como o Nível de Consistência garantirá sempre a Consistência de dados durante qualquer nível de consistência, abaixo do número de réplicas a ser gravado conforme necessário para alcançar o QUORUM (por exemplo, UM) resulta em dados sendo eventualmente consistentes. 

O cluster de 8 nós mostrado acima, com um fator de replicação de 3 e nível de consistência de leitura/gravação de QUORUM (2 nós são lidos ou gravados para consistência), poderá sobreviver à perda teórica de no máximo 1 nó por grupo de replicação antes de que o aplicativo perceba a falha. Isso pressupõe que todos os key spaces também têm solicitações de balanceamento de leitura/gravação.  A seguir estão os parâmetros que usaremos para o cluster implantado: 

Configuração de cluster de Cassandra de região única:

<table>
<tr>

<th>Parâmetros de cluster</th><th>Valor</th><th>Comentários</th></tr>
<tr><td>Número de nós (N) </td><td>8</td><td>Número total de nós do cluster</td></tr>
<tr><td>Fator de replicação (RF)</td><td>	3 </td><td>	Número de réplicas de uma determinada linha </td></tr>
<tr><td>Nível de consistência (gravação)</td><td>	QUORUM[(RF/2) + 1) = 2] [O resultado da fórmula é arredondado para baixo] </td><td> Grava no máximo 2 réplicas antes que a resposta seja enviada ao chamador; a 3ª réplica é gravada como eventualmente consistente. </td></tr>
<tr><td>Nível de consistência (leitura)	</td><td>QUORUM [(RF/2) + 1 = 2] [O resultado da fórmula é arredondado para baixo]</td><td>	Grava 2 réplicas antes de enviar a resposta ao chamador.</td></tr>
<tr><td>Estratégia de replicação </td><td>	NetworkTopologyStrategy [consulte [Replicação de dados](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) na documentação de Cassandra para obter mais informações]</td><td>	Entende a topologia de implantação e coloca as réplicas em nós para que todas as réplicas não terminem no mesmo rack</td></tr>
<tr><td>Informante	</td><td>GossipingPropertyFileSnitch [consulte [Informantes](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) na documentação de Cassandra para obter mais informações]</td><td>	NetworkTopologyStrategy usa um conceito de informante para compreender a topologia. GossipingPropertyFileSnitch oferece maior controle ao mapear cada nó para rack e data center. O cluster faz uso de fofocas para propagar essas informações. Isso é muito mais simples na configuração de IP dinâmico em relação a PropertyFileSnitch </td></tr>
</TABLE>

**Considerações do Azure para o cluster de Cassandra:** a capacidade das Máquinas Virtuais do Microsoft Azure usa o armazenamento de Blobs do Azure para a persistência de disco; o armazenamento do Azure salva 3 réplicas de cada disco para alta durabilidade. Isto significa que cada linha de dados inseridos em uma tabela de Cassandra já está armazenada em 3 réplicas e, portanto, a consistência dos dados já está resolvida mesmo que o Fator de Replicação (RF) seja 1. O principal problema com o Fator de Replicação sendo 1 é que o aplicativo terá um tempo de inatividade, mesmo que um único nó de Cassandra falhe. No entanto, se um nó estiver inativo para os problemas (por exemplo, falhas de software do sistema e hardware) reconhecidos pelo Controlador de Malha do Azure, ele provisionará um novo nó em seu lugar usando as mesmas unidades de armazenamento. O Provisionamento de um novo nó para substituir o antigo pode levar alguns minutos. Da mesma forma que para atividades de manutenção planejadas, como alterações do SO convidado, atualizações de Cassandra e alterações dos aplicativos, o Controlador de Malha do Azure executa, sem interrupção, as atualizações dos nós do cluster. As atualizações sem interrupção também podem desativar alguns nós por vez e, portanto, o cluster pode experimentar um breve tempo de inatividade para algumas partições. No entanto, os dados não serão perdidos devido à redundância interna de armazenamento do Azure.  

Para sistemas implantados no Azure que não exijam alta disponibilidade (por exemplo, cerca 99,9 que equivale a 8,76 h/ano; consulte [Alta disponibilidade](http://en.wikipedia.org/wiki/High_availability) para obter mais detalhes), você poderá executar com RF = 1 e Nível de Consistência = UM.  Para aplicativos com requisitos de alta disponibilidade, RF = 3 e Nível de Consistência = QUORUM tolerarão o tempo de inatividade de um dos nós de uma das réplicas. RF = 1 em implantações tradicionais (por exemplo, no local) não pode ser usado devido à possível perda de dados resultante de problemas, como falhas no disco.   

## Implantação em várias regiões ##
A replicação de reconhecimento e o modelo de consistência de data center de Cassandra, descritos acima, ajudam com a implantação de várias regiões fora da caixa sem a necessidade de qualquer ferramenta externa. Isto é bem diferente dos bancos de dados relacionais tradicionais em que a configuração de espelhamento de banco de dados para gravações de vários mestres pode ser bastante complexa. Cassandra em uma configuração de várias regiões pode ajudar com os cenários de uso, incluindo os seguintes: 

**Implantação baseada na proximidade:** os aplicativos multilocatários, com mapeamento claro de usuários locatários -para-região, podem se beneficiar pelas latências baixas do cluster de várias regiões. Por exemplo, um sistema de gerenciamento de aprendizado para instituições de ensino pode implantar um cluster distribuído em regiões do leste e oeste dos EUA para atender às respectivas áreas para transações e análises. Os dados podem ser localmente consistentes no momento das leituras e gravações e podem ser eventualmente consistentes entre as duas as regiões. Existem outros exemplos, como distribuição de mídia, comércio eletrônico e qualquer coisa que forneça uma base de usuários concentrados geograficamente é um bom caso de uso para este modelo de implantação. 

**Alta disponibilidade:** a redundância é um fator importante na obtenção de alta disponibilidade de softwares e hardwares; consulte Criação de sistemas de nuvem confiáveis no Microsoft Azure para obter mais detalhes. No Microsoft Azure, o único modo confiável de conseguir uma redundância real é implantando um cluster de várias regiões. Os aplicativos podem ser implantados em um modo ativo-ativo ou ativo-passivo e, se uma das regiões estiver inativa, o Gerenciador de Tráfego do Azure pode redirecionar o tráfego para a região ativa. Com a implantação de região única, se a disponibilidade for 99,9, uma implantação de duas região pode atingir uma disponibilidade de 99,9999, calculada pela fórmula: (1-(1-0,999) * (1-0,999)) * 100); consulte o artigo anterior para obter detalhes. 

**Recuperação de desastres:** o cluster de Cassandra de várias regiões, se criado adequadamente, pode resistir a falhas catastróficas de data center. Se uma região estiver inativa, o aplicativo implantado para outras regiões pode começar a atender os usuários finais. Como as outras implementações de continuidade de negócios, o aplicativo precisa ser tolerante a algumas perda de dados resultantes dos dados no pipeline assíncrono. No entanto, Cassandra torna a recuperação muito mais rápida do que o tempo gasto pelos processos de recuperação de banco de dados tradicional. A Figura 2 mostra o modelo típico de implantação de várias regiões com oito nós em cada região. Ambas as regiões são imagens espelhadas umas das outras devido à mesma simetria; os projetos do mundo real dependem do tipo de carga de trabalho (por exemplo, transacional ou analítico), RPO, RTO, consistência dos dados e requisitos de disponibilidade. 

![Multi region deployment](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

Figura 2: Implantação de Cassandra de várias regiões

### Integração de rede ###
Os conjuntos de máquinas virtuais implantados em redes privadas localizadas em duas regiões se comunicam entre si usando um túnel VPN. O túnel VPN conecta dois gateways de software provisionados durante o processo de implantação de rede. Ambas as regiões possuem uma arquitetura de rede semelhante em termos de sub-redes "web" e "dados"; a rede do Azure permite a criação de quantas sub-redes forem necessárias e a aplicação de ACLs conforme necessário pela segurança de rede. Ao projetar a topologia de cluster entre a latência de comunicação do data center e o impacto econômico da rede, o tráfego precisam ser considerado. 

### Consistência de dados para implantação em vários data centers ###
As implantações distribuídas precisam estar cientes do impacto da topologia de cluster na produtividade e alta disponibilidade. O RF e o Nível de Consistência precisam ser selecionados de forma que o quorum não dependa da disponibilidade de todos os data centers. 
Para um sistema que precisa de consistência alta, um LOCAL_QUORUM para o nível de consistência (para leituras e gravações) garantirá que as leituras e gravações locais sejam atendidas a partir dos nós locais enquanto os dados são replicados assincronamente para os data centers remotos.  A tabela 2 resume os detalhes de configuração para o cluster de várias regiões descritos posteriormente na gravação. 

**Configuração de cluster de Cassandra de duas regiões**

<table>
<tr><th>Parâmetros de cluster </th><th>Valor	</th><th>Comentários </th></tr>
<tr><td>Número de nós (N) </td><td>	8 + 8	</td><td> Número total de nós do cluster </td></tr>
<tr><td>Fator de replicação (RF)</td><td>	3 	</td><td>número de réplicas de uma determinada linha </td></tr>
<tr><td>Nível de consistência (gravação)	</td><td>LOCAL_QUORUM [(sum(RF)/2) + 1) = 4] [o resultado da fórmula é arredondado para baixo]	</td><td>2 nós serão gravados no primeiro data center de forma síncrona; os 2 nós adicionais necessários para o quorum serão gravados assincronamente no 2º data center. </td></tr>
<tr><td>Nível de consistência (leitura)</td><td>	LOCAL_QUORUM [((RF/2) + 1) = 2] [O resultado da fórmula é arredondado para baixo]	</td><td>As solicitações de leitura são satisfeitas a partir de apenas uma região; 2 nós são lidos antes de que a resposta seja enviada de volta ao cliente.  </td></tr>
<tr><td>Estratégia de replicação </td><td>	NetworkTopologyStrategy[consulte [Replicação de dados](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) na documentação de Cassandra para obter mais informações] </td><td>	Entende a topologia de implantação e coloca as réplicas em nós para que todas as réplicas não terminem no mesmo rack  </td></tr>
<tr><td>Informante</td><td> GossipingPropertyFileSnitch [consulte [Informantes](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) na documentação de Cassandra para obter mais informações] </td><td>NetworkTopologyStrategy usa um conceito de informante para compreender a topologia. GossipingPropertyFileSnitch oferece maior controle ao mapear cada nó para rack e data center. O cluster faz uso de fofocas para propagar essas informações. Isso é muito mais simples na configuração de IP dinâmico em relação a PropertyFileSnitch </td></tr> 
</table> 

##A CONFIGURAÇÃO DO SOFTWARE##
As seguintes versões de software são usadas durante a implantação:

<table>
<tr><th>Software</th><th>Fonte</th><th>Versão</th></tr>
<tr><td>JRE	</td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA	</td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu	</td><td>[Portal do Azure Mcrosoft](http://azure.microsoft.com) </td><td>14.04 LTS</td></tr>
</table>

Como o download do JRE requer a aceitação manual da licença do Oracle, para simplificar a implantação, baixe todos os softwares necessários para a área de trabalho para carregar mais tarde na imagem de modelo do Ubuntu que estaremos criando como precursora da implantação de cluster. 

Baixe o software acima em um diretório de downloads conhecido (por exemplo, %TEMP%/downloads no Windows ou ~/downloads no Linux ou no Mac) na área de trabalho local. 

### CRIAR UMA MV UBUNTU ###
Nesta etapa do processo, criaremos a imagem do Ubuntu com o software de pré-requisito para que a imagem possa ser reutilizada para o provisionamento de vários nós de Cassandra.  
####ETAPA 1: Gerar o par de chave SSH####
O Azure precisa de uma chave pública X509 que é PEM ou DER codificada no momento de provisionamento. Gerar um par de chaves pública/privada usando as instruções localizadas em Como usar SSH com Linux no Azure. Se você planeja usar putty.exe como um cliente SSH no Windows ou Linux, você precisará converter a chave privada RSA codificada PEM para o formato PPK usando puttygen.exe; as instruções para isso podem ser encontradas na página da web acima. 

####ETAPA 2: Criar uma VM do modelo Ubuntu####
Para criar a VM do modelo, faça logon no portal azure.microsoft.com e use a seguinte sequência: Clique em NOVO,COMPUTAÇÃO, MÁQUINA VIRTUAL, DA GALERIA, UBUNTU, Servidor Ubuntu 14.04 LTS e, em seguida, clique na seta para a direita. Para um tutorial que descreva como criar uma VM do Linux, consulte Criar uma Máquina Virtual executando o Linux.

Insira as seguintes informações na tela #1 "Configuração da máquina virtual": 

<table>
<tr><th>NOME DO CAMPO              </td><td>       VALOR DO CAMPO               </td><td>         COMENTÁRIOS                </td><tr>
<tr><td>DATA DE LANÇAMENTO DA VERSÃO    </td><td> Selecione uma data a partir do menu suspenso</td><td></td><tr>
<tr><td>NOME DA MÁQUINA VIRTUAL    </td><td> cass-template	               </td><td> Este é o nome do organizador da MV </td><tr>
<tr><td>CAMADA	                 </td><td> PADRÃO	                       </td><td> Deixar o padrão              </td><tr>
<tr><td>TAMANHO adequado	                 </td><td> A1                              </td><td>Selecione a VM com base nas necessidades de E/S; para esta finalidade, deixe o valor padrão </td><tr>
<tr><td> NOME DE NOVO USUÁRIO	         </td><td> localadmin	                   </td><td> "admin" é um nome de usuário reservado no Ubuntu 12.xx e depois</td><tr>
<tr><td> AUTENTICAÇÃO	     </td><td> Clique na caixa de seleção                 </td><td>Verifique se você deseja proteger com uma chave SSH </td><tr>
<tr><td> CERTIFICADO	         </td><td> nome do arquivo do certificado de chave pública </td><td> Use a chave pública gerada anteriormente</td><tr>
<tr><td> Nova senha	</td><td> senha de alta segurança </td><td> </td><tr>
<tr><td> Confirmar Senha	</td><td> senha de alta segurança </td><td></td><tr>
</table>

Insira as seguintes informações na tela #2 "Configuração da máquina virtual": 

<table>
<tr><th>NOME DO CAMPO             </th><th> VALOR DO CAMPO	                   </th><th> COMENTÁRIOS                                 </th></tr>
<tr><td> SERVIÇO DE NUVEM	</td><td> Crie um novo serviço de nuvem	</td><td>O serviço de nuvem é um recurso de contêiner de computação, como as máquinas virtuais</td></tr>
<tr><td> NOME DNS DO SERVIÇO DE NUVEM	</td><td>ubuntu-template.cloudapp.net	</td><td>Dê um nome do balanceador de carga independente da máquina</td></tr>
<tr><td> REGIÃO/GRUPO DE AFINIDADE/REDE VIRTUAL </td><td>	Oeste dos EUA	</td><td> Selecione uma região na qual seus aplicativos da web acessam o cluster Cassandra</td></tr>
<tr><td>CONTA DE ARMAZENAMENTO </td><td>	Usar padrão	</td><td>Use a conta de armazenamento padrão ou uma conta de armazenamento criada previamente em uma determinada região</td></tr>
<tr><td>CONJUNTO DE DISPONIBILIDADE </td><td>	Nenhum </td><td>	Deixe em branco</td></tr>
<tr><td>PONTOS DE EXTREMIDADE	</td><td>Usar padrão </td><td>	Use a configuração padrão de SSH </td></tr>
</table>

Clique na seta para a direita, mantenha os padrões na tela #3 e clique no botão de "verificação" para concluir o processo de provisionamento da VM. Depois de alguns minutos, a VM com o nome "ubuntu-template" deve estar no status "em execução". 

###INSTALAR O SOFTWARE NECESSÁRIO###
####ETAPA 1: Carregar tarballs ####
Usando scp ou pscp, copie o software baixado anteriormente para o diretório ~/downloads usando o seguinte formato de comando: 

#####pscp server-jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz #####

Repita o comando acima para JRE, bem como para os bits de Cassandra. 

####ETAPA 2: Preparar a estrutura de diretório e extraia os arquivos####
Faça o logon na VM, crie a estrutura de diretórios e extraia o software como um superusuário usando o script bash abaixo:

	#!/bin/bash
	CASS_INSTALL_DIR="/opt/cassandra"
	JRE_INSTALL_DIR="/opt/java"
	CASS_DATA_DIR="/var/lib/cassandra"
	CASS_LOG_DIR="/var/log/cassandra"
	DOWNLOADS_DIR="~/downloads"
	JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
	CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
	SVC_USER="localadmin"
	
	RESET_ERROR=1
	MKDIR_ERROR=2
	
	reset_installation ()
	{
	   rm -rf $CASS_INSTALL_DIR 2> /dev/null
	   rm -rf $JRE_INSTALL_DIR 2> /dev/null
	   rm -rf $CASS_DATA_DIR 2> /dev/null
	   rm -rf $CASS_LOG_DIR 2> /dev/null
	}
	make_dir ()
	{
	   if [ -z "$1" ]
	   then
	      echo "make_dir: invalid directory name"
	      exit $MKDIR_ERROR
	   fi
	   
	   if [ -d "$1" ]
	   then
	      echo "make_dir: directory already exists"
	      exit $MKDIR_ERROR
	   fi
	
	   mkdir $1 2>/dev/null
	   if [ $? != 0 ]
	   then
	      echo "directory creation failed"
	      exit $MKDIR_ERROR
	   fi
	}
	
	unzip()
	{
	   if [ $# == 2 ]
	   then
	      tar xzf $1 -C $2
	   else
	      echo "archive error"
	   fi
	   
	}
	
	if [ -n "$1" ]
	then
	   SVC_USER=$1
	fi
	
	reset_installation 
	make_dir $CASS_INSTALL_DIR
	make_dir $JRE_INSTALL_DIR
	make_dir $CASS_DATA_DIR
	make_dir $CASS_LOG_DIR
	
	#unzip JRE and Cassandra 
	unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
	unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR
	
	#Change the ownership to the service credentials
	
	chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
	chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
	echo "edit /etc/profile to add JRE to the PATH"
	echo "installation is complete"


Se você colar este script na janela vim, certifique-se de remover o retorno de carro ('\r") usando o seguinte comando:

	tr -d '\r' <infile.sh >outfile.sh

####Etapa 3: Editar etc/perfil####
Acrescente o seguinte ao final: 

	JAVA_HOME=/opt/java/jdk1.8.0_05 
	CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
	PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
	export JAVA_HOME
	export CASS_HOME
	export PATH

####Etapa 4: Instalar JNA para sistemas de produção####
Use a seguinte sequência de comando: 
O comando a seguir instalará jna-3.2.7.jar e jna-platform-3.2.7.jar no diretório /usr/share.java
sudo apt-get install libjna-java 

Crie links simbólicos no diretório $CASS_HOME/lib para que o script de inicialização de Cassandra possa encontrar essas jars:

	ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

	ln -s /usr/share/java/jna-platrom-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####Etapa 5: Configurar cassandra.yaml####
Edite cassandra.yaml em cada VM para refletir a configuração necessária para todas as máquinas virtuais [ajustaremos isso durante o provisionamento real]: 

<table>
<tr><th>Nome do campo   </th><th> Valor  </th><th>	Comentários </th></tr>
<tr><td>cluster_name </td><td>	"CustomerService"	</td><td> Use o nome que reflete a sua implantação</td></tr> 
<tr><td>listen_address	</td><td>[deixe em branco]	</td><td> Excluir "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[deixe em branco]	</td><td> Excluir "localhost" </td></tr>
<tr><td>sementes	</td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"	</td><td>Lista de todos os endereços IP que são designados como sementes.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> É usado pelo NetworkTopologyStrateg para deduzir o data center e o rack da VM</td></tr>
</table>

####Etapa 6: Capturar a imagem VM####
Faça logon na máquina virtual usando o nome de host (hk-cas-template.cloudapp.net) e a chave privada SSH criada anteriormente. Consulte como usar a SSH com o Linux no Azure para obter detalhes sobre como fazer logon usando o comando ssh ou putty.exe. 

Execute a seguinte sequência de ações para capturar a imagem:
#####1. Desprovisionar#####
Use o comando "sudo waagent-deprovision+user" para remover informações específicas da instância de Máquina Virtual. Veja [Como capturar uma Máquina Virtual Linux](http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-linux-capture-image/) para usar como um modelo para mais detalhes sobre o processo de captura de imagem. 

#####2: Desligar a VM#####
Certifique-se de que a máquina virtual esteja realçada e clique no link DESLIGAMENTO na barra de comandos inferior.

#####3: Capturar a imagem#####
Certifique-se de que a máquina virtual esteja realçada e clique no link CAPTURAR na barra de comandos inferior. Na próxima tela, atribua um NOME DE IMAGEM (por exemplo, hk-cas-2-08-ub-14-04-2014071), uma DESCRIÇÃO DA IMAGEM apropriada, e clique na marca de "verificação" para concluir o processo de captura.

Isto levará alguns segundos e a imagem deverá estar disponível na seção MINHAS IMAGENS da galeria de imagens. A VM de origem será automaticamente delatada depois que a imagem for capturada com êxito. 

##Processo de implantação de região única##
**Etapa 1: Criar a Rede Virtual**
Faça logon no portal de gerenciamento e crie uma Rede Virtual com os atributos mostrados na tabela. Consulte [Configurar uma Rede Virtual somente na nuvem no portal de gerenciamento](http://msdn.microsoft.com/pt-br/library/azure/dn631643.aspx) para obter as etapas detalhadas do processo.      

<table>
<tr><th>Nome do atributo da VM</th><th>Valor</th><th>Comentários</th></tr>
<tr><td>Nome</td><td>vnet-cass-west-us</td><td></td></tr>	
<tr><td>Região</td><td>Oeste dos EUA</td><td></td></tr>	
<tr><td>Servidores DNS	</td><td>Nenhum</td><td>Ignore isso, pois não estamos usando um servidor DNS</td></tr>
<tr><td>Configurar uma VPN ponto a site</td><td>Nenhum</td><td> Ignore isso</td></tr>
<tr><td>Configurar uma VPN site a site</td><td>Nenhum</td><td> Ignore isso</td></tr>
<tr><td>Espaço de endereço</td><td>10.1.0.0/16</td><td></td></tr>	
<tr><td>IP Inicial</td><td>10.1.0.0</td><td></td></tr>	
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Adicione as seguintes sub-redes: 

<table>
<tr><th>Nome</th><th>IP Inicial</th><th>CIDR</th><th>Comentários</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Sub-rede para a web farm</td></tr>
<tr><td>dados</td><td>10.1.2.0</td><td>/24 (251)</td><td>Sub-rede para os nós do banco de dados</td></tr>
</table>

As sub-redes de Web e Dados podem ser protegidas por meio dos grupos de segurança de rede, a cobertura das mesmas está fora do escopo deste artigo.  

**Etapa 2: Provisionar Máquinas Virtuais** 
Usando a imagem criada anteriormente, criaremos as seguintes máquinas virtuais no servidor de nuvem "hk-c-svc-west" e as associaremos às respectivas sub-redes, conforme mostrado abaixo: 

<table>
<tr><th>Nome da máquina    </th><th>Sub-rede	</th><th>Endereço IP	</th><th>Conjunto de disponibilidade</th><th>DC/Rack</th><th>Propagar?</th></tr>
<tr><td>hk-c1-west-us	</td><td>dados	</td><td>10.1.2.4	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1 </td><td>Sim</td></tr>
<tr><td>hk-c2-west-us	</td><td>dados	</td><td>10.1.2.5	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1	</td><td>Não </td></tr>
<tr><td>hk-c3-west-us	</td><td>dados	</td><td>10.1.2.6	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>Sim</td></tr>
<tr><td>hk-c4-west-us	</td><td>dados	</td><td>10.1.2.7	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>Não </td></tr>
<tr><td>hk-c5-west-us	</td><td>dados	</td><td>10.1.2.8	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>Sim</td></tr>
<tr><td>hk-c6-west-us	</td><td>dados	</td><td>10.1.2.9	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>Não </td></tr>
<tr><td>hk-c7-west-us	</td><td>dados	</td><td>10.1.2.10	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>Sim</td></tr>
<tr><td>hk-c8-west-us	</td><td>dados	</td><td>10.1.2.11	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>Não </td></tr>
<tr><td>hk-w1-west-us	</td><td>web	</td><td>10.1.1.4	</td><td>hk-w-aset-1	</td><td>                       </td><td>N/D</td></tr>
<tr><td>hk-w2-west-us	</td><td>web	</td><td>10.1.1.5	</td><td>hk-w-aset-1	</td><td>                       </td><td>N/D</td></tr>
</table>

A criação da lista de VMs mencionada acima exige o seguinte processo:

1.  Crie um serviço de nuvem vazio em uma determinada região
2.	Crie uma VM a partir da imagem capturada anteriormente e anexe-a à rede virtual criada anteriormente; repita esta etapa para todas as VMs
3.	Adicione um balanceador de carga interno ao serviço de nuvem e anexe-o à sub-rede "dados"
4.	Para cada VM criada anteriormente, adicione um ponto de extremidade com balanceamento de carga para o tráfego de poupança através de um conjunto de balanceamento de carga conectado ao balanceador de carga interno criado anteriormente

O processo acima pode ser executado usando o Portal de gerenciamento do Azure; use uma máquina Windows (use uma VM no Azure se você não tiver acesso a uma máquina Windows), use o seguinte script do PowerShell para provisionar todas as 8 VMs automaticamente.

**Lista1: Script do PowerShell para provisionamento de máquinas virtuais**
		
		#Tested with Azure Powershell - November 2014	
		#This powershell script deployes a number of VMs from an existing image inside an Azure region
		#Import your Azure subscription into the current Powershell session before proceeding
		#The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template
		
		#fundamental variables - change these to reflect your subscription
		$country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
		$numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
		$subscriptionName = "Azure_subscription_name"; 
		$vmSize="ExtraSmall"; $imageName="your_linux_image_name"
		$ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"
		
		#generated variables
		$serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"
		
		$vmNames = @()
		for ($i=0; $i -lt $numVMs; $i++)
		{
		   $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
		}
		
		#select an Azure subscription already imported into Powershell session
		Select-AzureSubscription -SubscriptionName $subscriptionName -Current
		Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount
		
		#create an empty cloud service
		New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
		Write-Host "Created $serviceName"
		
		$VMList= @()   # stores the list of azure vm configuration objects
		#create the list of VMs
		foreach($vmName in $vmNames)
		{
		   $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
		   Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
		   Set-AzureSubnet "data"
		}
		
		New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList
		
		#Create internal load balancer
		Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
		Write-Host "Created $ilbName"
		#Add add the thrift endpoint to the internal load balancer for all the VMs
		foreach($vmName in $vmNames)
		{
		    Get-AzureVM -ServiceName $serviceName -Name $vmName |
		        Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName | 
		        Update-AzureVM 
		
		    Write-Host "created $vmName"     
		}

**Etapa 3: Configurar Cassandra em cada VM**

Faça logon na VM e execute o seguinte procedimento: 

* Edite $CASS_HOME/conf/cassandra-rackdc.properties para especificar as propriedades de data center e rack:
      
       dc =EASTUS, rack =rack1

* Edite cassandra.yaml para configurar os nós de propagação, conforme abaixo:
     
       Sementes: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Etapa 4: Iniciar as VMs e testar o cluster**

Faça logon em um dos nós (por exemplo, hk-c1-west-us) e execute o seguinte comando para ver o status do cluster: 
       
       nodetool -h 10.1.2.4 -p 7199 status 

Você deverá ver uma tela semelhante à seguinte para um cluster de 8 nós: 

<table>
<tr><th>Status</th></th>Endereço	</th><th>Carga	</th><th>Tokens	</th><th>Possui </th><th>ID do host	</th><th>Rack</th></tr>
<tr><th>UN	</td><td>10.1.2.4 	</td><td>87.81 KB	</td><td>256	</td><td>38,0%	</td><td>Guid (removido)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.5 	</td><td>41.08 KB	</td><td>256	</td><td>68,9%	</td><td>Guid (removido)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.6 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>Guid (removido)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.7 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>Guid (removido)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.8 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>Guid (removido)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.9 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>Guid (removido)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.10 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid (removido)</td><td>rack4</td></tr>
<tr><th>UN	</td><td>10.1.2.11 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid (removido)</td><td>rack4</td></tr>
</table>

##<a id="testone"> </a>Testar o cluster de região única##
Use as etapas a seguir para testar o cluster:

1.    Usando o comando do Powershell Get-AzureInternalLoadbalancer commandlet, obtenha o endereço IP do balanceador de carga interno (por exemplo, 10.1.2.101). A sintaxe do comando é mostrada abaixo: Get-AzureLoadbalancer - ServiceName "hk-c-svc-west-us" [exibe os detalhes do balanceador de carga interno juntamente com seu endereço IP]
2.	Faça logon na web farm VM (por exemplo, hk-w1-west-us) usando Putty ou ssh
3.	Execute $CASS_HOME/bin/cqlsh 10.1.2.101 9160 
4.	Use os seguintes comandos CQL para verificar se o cluster está funcionando:

		CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };	
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		
		SELECT * FROM Customers;

Você deve ver uma tela como a mostrada abaixo:

<table>
  <tr><th> customer_id </th><th> firstname </th><th> lastname </th></tr>
  <tr><td> 1 </td><td> John </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Doe </td></tr>
</table>

Observe que o keyspace criado na etapa 4 usa SimpleStrategy com um replication_factor de 3. SimpleStrategy é recomendado para implantações únicas de data center, enquanto NetworkTopologyStrategy é recomendado para implantações de vários data centers. Um replication_factor de 3 fornecerá tolerância às falhas do nó. 

##<a id="tworegion"> </a>Processo de implantação de várias regiões##
Utilizará a implantação de região única concluída e repetirá o mesmo processo para instalar a segunda região. A principal diferença entre a implantação de região única e e a de várias regiões é a configuração de túnel VPN para comunicação entre regiões; começaremos com a instalação de rede, provisionamento de VMs e configuração de Cassandra. 

###Etapa 1: Criar uma rede virtual na 2ª região###
Faça logon no portal de gerenciamento e crie uma Rede Virtual com os atributos mostrados na tabela. Consulte [Configurar uma Rede Virtual somente na nuvem no portal de gerenciamento](http://msdn.microsoft.com/pt-br/library/azure/dn631643.aspx) para obter as etapas detalhadas do processo.      

<table>
<tr><th>Nome do atributo    </th><th>Valor	</th><th>Comentários</th></tr>
<tr><td>Nome	</td><td>vnet-cass-east-us</td><td></td></tr>	
<tr><td>Região	</td><td>Leste dos EUA</td><td></td></tr>	
<tr><td>Servidores DNS		</td><td></td><td>Ignore isso, pois não estamos usando um servidor DNS</td></tr>
<tr><td>Configurar uma VPN ponto a site</td><td></td><td>		Ignore isso</td></tr>
<tr><td>Configurar uma VPN site a site</td><td></td><td>		Ignore isso</td></tr>
<tr><td>Espaço de endereço	</td><td>10.2.0.0/16</td><td></td></tr>	
<tr><td>IP Inicial	</td><td>10.2.0.0	</td><td></td></tr>
<tr><td>CIDR	</td><td>/16 (65531)</td><td></td></tr>
</table>	

Adicione as seguintes sub-redes: 
<table>
<tr><th>Nome    </th><th>IP Inicial	</th><th>CIDR	</th><th>Comentários</th></tr>
<tr><td>web	</td><td>10.2.1.0	</td><td>/24 (251)	</td><td>Sub-rede para a web farm</td></tr>
<tr><td>dados	</td><td>10.2.2.0	</td><td>/24 (251)	</td><td>Sub-rede para os nós do banco de dados</td></tr>
</table>


###Etapa 2: Criar Redes Locais###
Uma Rede Local na rede virtual do Azure é um espaço de endereço proxy que é mapeado para um site remoto, incluindo uma nuvem privada ou outra região do Azure. Este espaço de endereço proxy está associado a um gateway remoto para roteamento de rede para os destinos de rede corretos. Consulte [Configurar uma conexão VNet a VNet](http://msdn.microsoft.com/pt-br/library/azure/dn690122.aspx) para obter instruções sobre como estabelecer conexões VNET a VNET. 

Crie duas redes locais para os seguintes detalhes:

<table>
<tr><th>Nome da rede          </th><th>Endereço do Gateway da VPN	</th><th>Espaço de endereço	</th><th>Comentários</th></tr>
<tr><td>hk-lnet-map-to-east-us</td><td>	23.1.1.1	</td><td>10.2.0.0/16	</td><td>Ao criar a Rede Local, um espaço reservado para o endereço de gateway nos é dado. O endereço do gateway real é preenchido quando o gateway é criado. Certifique-se de que o espaço do endereço corresponda exatamente ao respectivo VNET remoto; neste caso, o VNET criado na região leste dos EUA.</td></tr>
<tr><td>hk-lnet-map-to-west-us	</td><td>23.2.2.2	</td><td>10.1.0.0/16	</td><td>Ao criar a Rede Local, um espaço reservado para o endereço de gateway nos é dado. O endereço do gateway real é preenchido quando o gateway é criado. Certifique-se de que o espaço do endereço corresponda exatamente ao respectivo VNET remoto; neste caso, o VNET criado na região oeste dos EUA.</td></tr>
</table>


###Etapa 3: Mapear rede "Local" para os respectivos VNETs###
No portal de gerenciamento de serviço, selecione cada vnet, clique em "Configurar", marque "Conectar à rede local" e selecione as Redes Locais para os seguintes detalhes: 

<table>
<tr><th>Rede Virtual </th><th>Rede Local</th></tr>
<tr><td>hk-vnet-west-us	</td><td>hk-lnet-map-to-east-us</td></tr>
<tr><td>hk-vnet-east-us	</td><td>hk-lnet-map-to-west-us</td></tr>
</table>

###Etapa 4: Criar Gateways em VNET1 e VNET2###
No painel de ambas as redes virtuais, clique em CRIAR GATEWAY, o que acionará o processo de provisionamento do gateway de VPN. Após alguns minutos, o painel de cada rede virtual deverá exibir o endereço de gateway real. 
###Etapa 5: Atualizar redes "Local" com os respectivos endereços "Gateway"###
Edite ambas as redes locais para substituir o endereço IP do gateway de espaço reservado com o endereço IP real dos gateways provisionados. Use o seguinte mapeamento: 

<table>
<tr><th>Rede Local    </th><th>Gateway de Rede Virtual</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Gateway de hk-vnet-west-us</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Gateway de hk-vnet-east-us</td></tr>
</table>

###Etapa 6: Atualizar a chave compartilhada###
Use o seguinte script do Powershell para atualizar a chave IPSec de cada gateway de VPN [use a chave sake para ambos os gateways]: 
Set-AzureVNetGatewayKey -VNetName hk-vnet-east-us -LocalNetworkSiteName hk-lnet-map-to-west-us -SharedKey D9E76BKK
Set-AzureVNetGatewayKey -VNetName hk-vnet-west-us -LocalNetworkSiteName hk-lnet-map-to-east-us -SharedKey D9E76BKK 

###Etapa 6: Estabelecer a conexão VNET para VNET###
No portal de gerenciamento do serviço do Azure, use o menu "PAINEL" de ambas as redes virtuais para estabelecer a conexão de gateway a gateway. Use os itens do menu "CONECTAR" na barra de ferramentas inferior. Após alguns minutos, o painel deverá exibir graficamente os detalhes da conexão.

###Etapa 7: Criar as máquinas virtuais na região #2 ###
Criar a imagem do Ubuntu, conforme descrito na implantação da região #1, seguindo as mesmas etapas, ou copie o arquivo VHD de imagem para a conta de armazenamento do Azure localizada na região #2 e crie a imagem. Use esta imagem e crie a seguinte lista de máquinas virtuais em um novo serviço de nuvem hk-c-svc-east-us: 

<table>
<tr></th>Nome da máquina </th><th>Sub-rede</th><th>Endereço IP</th><th>Conjunto de disponibilidade</th><th>DC/Rack</th><th>Propagar?</th></tr>
<tr><td>hk-c1-east-us	</td><td>dados	</td><td>10.2.2.4	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack1	</td><td>Sim</td></tr>
<tr><td>hk-c2-east-us	</td><td>dados	</td><td>10.2.2.5	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack1	</td><td>Não </td></tr>
<tr><td>hk-c3-east-us	</td><td>dados	</td><td>10.2.2.6	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack2	</td><td>Sim</td></tr>
<tr><td>hk-c5-east-us	</td><td>dados	</td><td>10.2.2.8	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack3	</td><td>Sim</td></tr>
<tr><td>hk-c6-east-us	</td><td>dados	</td><td>10.2.2.9	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack3	</td><td>Não </td></tr>
<tr><td>hk-c7-east-us  </td><td>dados	</td><td>10.2.2.10	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack4	</td><td>Sim</td></tr>
<tr><td>hk-c8-east-us	</td><td>dados	</td><td>10.2.2.11	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack4	</td><td>Não </td></tr>
<tr><td>hk-w1-east-us	</td><td>web	</td><td>10.2.1.4	</td><td>hk-w-aset-1	</td><td>	N/D</td><td>N/D</td></tr>
<tr><td>hk-w2-east-us	</td><td>web	</td><td>10.2.1.5	</td><td>hk-w-aset-1	</td><td>	N/D</td><td>N/D</td></tr>
</table>

Siga as mesmas instruções que para a região #1, mas use o espaço de endereço de 10.2.xxx.xxx. 
###Etapa 8: Configurar Cassandra em cada VM###
Faça logon na VM e execute o seguinte procedimento: 

1. Edite $CASS_HOME/conf/cassandra-rackdc.properties para especificar as propriedades de data center e rack no formato:
    dc =EASTUS
    rack = rack1
2. Edite cassandra.yaml para configurar os nós de propagação: 
    Sementes: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"
###Etapa 9: Iniciar Cassandra######
Faça logon em cada VM e inicie Cassandra em segundo plano, executando o seguinte comando:
$CASS_HOME/bin/cassandra

##<a id="testtwo"> </a>Testar o cluster de várias regiões##
Até agora, Cassandra foi implantado em 16 nós com 8 nós em cada região do Azure. Estes nós estão no mesmo cluster por meio do nome comum do cluster e da configuração do nó de propagação. Use o seguinte processo para testar o cluster:
###Etapa 1: Obenha o IP do balanceador de carga interno para ambas as regiões usando o PowerShell### 
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  

    Observe os endereços IP (por exemplo, oeste - 10.1.2.101, leste - 10.2.2.101) exibidos.

###Etapa 2: Execute o seguinte na região oeste depois de efetuar logon em hk-w1-west-us###
1.    Execute $CASS_HOME/bin/cqlsh 10.1.2.101 9160 
2.	Execute os seguintes comandos CQL:

		CREATE KEYSPACE customers_ks
		WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

Você deve ver uma tela como a mostrada abaixo:

<table>
<tr><th>customer_id </th><th>firstname</th><th>Lastname</th></tr>
<tr><td>1</td><td>John</td><td>Doe</td></tr>
<tr><td>2</td><td>Jane</td><td>Doe</td></tr>
</table>

###Etapa 3: Execute o seguinte na região leste depois de efetuar logon em hk-w1-east-us:###
1.    Execute $CASS_HOME/bin/cqlsh 10.2.2.101 9160 
2.	Execute os seguintes comandos CQL:

		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

Você deverá ver a mesma tela exibida para a região oeste:

<table>
<tr><th>customer_id </th><th>firstname</th><th>Lastname</th></tr>
<tr><td>1</td><td>John</td><td>Doe</td></tr>
<tr><td>2</td><td>Jane</td><td>Doe</td></tr>
</table>

Execute mais algumas inserções e veja como são replicadas para a parte west-us do cluster. 

##<a id="testnode"> </a>Testar o cluster de Cassandra do Node.js##
Usando uma das VMs do Linux encaixotadas anteriormente na camada "web", executaremos um script simples do Node.js para ler os dados inseridos anteriormente 

**Etapa 1: Instale o Node.js e o cliente de Cassandra**

1. Instale o Node.js e npm
2. Instale o pacote de nó "cassandra-client" usando npm
3. Execute o seguinte script no prompt de shell que exibe a cadeia de caracteres json dos dados recuperados: 

		var pooledCon = require('cassandra-client').PooledConnection;
		var ksName = "custsupport_ks";
		var cfName = "customers_cf";
		var hostList = ['internal_loadbalancer_ip:9160'];
		var ksConOptions = { hosts: hostList,
		                     keyspace: ksName, use_bigints: false };
		
		function createKeyspace(callback){
		   var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
		   var sysConOptions = { hosts: hostList,  
		                         keyspace: 'system', use_bigints: false };
		   var con = new pooledCon(sysConOptions);
		   con.execute(cql,[],function(err) {
		   if (err) {
		     console.log("Failed to create Keyspace: " + ksName);
		     console.log(err);
		   }
		   else {
		     console.log("Created Keyspace: " + ksName);
		     callback(ksConOptions, populateCustomerData);
		   }
		   });
		   con.shutdown();
		} 
		
		function createColumnFamily(ksConOptions, callback){
		  var params = ['customers_cf','custid','varint','custname',
		                'text','custaddress','text'];
		  var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
		var con =  new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) {
		         console.log("Failed to create column family: " + params[0]);
		         console.log(err);
		      }
		      else {
		         console.log("Created column family: " + params[0]);
		         callback();
		      }
		  });
		  con.shutdown();
		} 
		
		//populate Data
		function populateCustomerData() {
		   var params = ['John','Infinity Dr, TX', 1];
		   updateCustomer(ksConOptions,params);
		
		   params = ['Tom','Fermat Ln, WA', 2];
		   updateCustomer(ksConOptions,params);
		}
		
		//update will also insert the record if none exists
		function updateCustomer(ksConOptions,params)
		{
		  var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) console.log(err);
		      else console.log("Inserted customer : " + params[0]);
		  });
		  con.shutdown();
		}
		
		//read the two rows inserted above
		function readCustomer(ksConOptions)
		{
		  var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,[],function(err,rows) {
		      if (err) 
		         console.log(err);
		      else 
		         for (var i=0; i<rows.length; i++)
		            console.log(JSON.stringify(rows[i]));
		    });
		   con.shutdown();
		}
		
		//exectue the code
		createKeyspace(createColumnFamily);
		readCustomer(ksConOptions)


##<a id="conclusion"></a>Conclusão##
O Microsoft Azure é uma plataforma flexível que permite a execução tanto de softwares da Microsoft, bem como de softwares livres, conforme demonstrado neste exercício. Os clusters de Cassandra altamente disponíveis podem ser implantados em um único data center por meio de propagação de nós de cluster em vários domínios de falha. Os clusters de Cassandra também podem ser implantados em regiões geograficamente distantes do Azure para sistemas à prova de desastres. Juntos, o Azure e Cassandra possibilitam a construção serviços de nuvem altamente escalonáveis, altamente disponíveis e recuperáveis contra desastres, necessários pelos serviços de escala de internet de hoje em dia.  

[Visão geral]: #overview
[Implantação de região única]: #oneregion
[Testar o cluster de Cassandra de única região]: #testone
[Implantação em várias regiões]: #tworegion
[Testar o cluster de Cassandra de várias regiões]: #testtwo
[Testar o cluster de Cassandra do Node.js]: #testnode
[Conclusão]: #conclusion

##Referências##
- [http://cassandra.apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com) 
- [http://www.nodejs.org](http://www.nodejs.org) 

<!--HONumber=42-->
