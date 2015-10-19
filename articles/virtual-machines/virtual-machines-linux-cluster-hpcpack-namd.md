<properties
 pageTitle="NAMD com Microsoft HPC Pack em VMs do Linux | Microsoft Azure"
 description="Implante um cluster do Microsoft HPC Pack no Azure e execute uma simulação do NAMD com charmrun em vários nós de computação do Linux."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/02/2015"
 ms.author="danlep"/>

# Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure

Este artigo mostra como implantar um cluster do Microsoft HPC Pack no Azure e executar um trabalho do [NAMD](http://www.ks.uiuc.edu/Research/namd/) com **charmrun** em vários nós de computação do Linux em uma rede de cluster virtual para calcular e visualizar a estrutura de um sistema biomolecular grande.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação de recursos com o modelo clássico de implantação.


O NAMD (para o programa Nanoscale Molecular Dynamics) é um pacote de dinâmica molecular paralela criado para a simulação de alto desempenho de sistemas biomoleculares grandes que contêm milhões de átomos, como vírus, estruturas celulares e proteínas grandes. O NAMD é dimensionado para centenas de núcleos de simulações típicas e para mais de 500.000 núcleos para as simulações maiores.

O Microsoft HPC Pack fornece recursos para executar uma variedade de aplicativos de HPC e paralelos em larga escala, incluindo aplicativos MPI, em clusters de máquinas virtuais do Microsoft Azure. A partir do Microsoft HPC Pack 2012 R2, o HPC Pack também dá suporte a aplicativos de HPC no Linux em VMs com nós de computação do Linux implantadas em um cluster do HPC Pack. Veja [Introdução aos nós de computação do Linux em um cluster do HPC Pack no Azure](virtual-machines-linux-cluster-hpcpack.md) para saber como começar a usar os nós de computação do Linux com o HPC Pack.


## Pré-requisitos

* **Cluster do HPC Pack com nós de computação do Linux** – Veja [Introdução aos nós de computação do Linux em um cluster do HPC Pack no Azure](virtual-machines-linux-cluster-hpcpack.md) para obter os pré-requisitos e as etapas para implantar um cluster do HPC Pack com nós de computação do Linux no Azure usando um script do Azure PowerShell e as imagens do HPC Pack no Azure Marketplace.

    Veja abaixo um exemplo de arquivo de configuração XML que você pode usar com o script para implantar um cluster do HPC Pack baseado no Azure consistindo em um nó principal do Windows Server 2012 R2 e quatro nós de computação do CentOS 6.6 de tamanho grande (A3). Substitua os valores apropriados por sua assinatura e nomes de serviço.

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>West US</Location>  
      <VNet>
        <VNetName>MyVNet</VNetName>
        <SubnetName>Subnet-1</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>CentOS66HN</VMName>
        <ServiceName>MyHPCService</ServiceName>
        <VMSize>Large</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
        <ServiceName>MyLnxCNService</ServiceName>
        <VMSize>Large</VMSize>
        <NodeCount>4</NodeCount>
        <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>    
```


* **Arquivos do tutorial e software do NAMD** – Baixe o software do NAMD para o Linux no site do [NAMD](http://www.ks.uiuc.edu/Research/namd/). Este artigo se baseia na versão 2.10 do NAMD e usa o arquivo [Linux-x86\_64 (Intel/AMD de 64 bits com Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310), que você usará para executar o NAMD em vários nós de computação do Linux em uma rede de cluster. Baixe também os [Arquivos do tutorial do NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Siga as instruções neste artigo para extrair o arquivo morto e os exemplos do tutorial no nó principal do cluster.

* **VMD** (opcional) – Para ver os resultados do trabalho do NAMD, baixe e instale o programa de visualização molecular [VMD](http://www.ks.uiuc.edu/Research/vmd/) em um computador de sua escolha. A versão atual é 1.9.2. Visite o site de download do VMD para começar.


## Configurar a relação de confiança mútua entre os nós de computação
Executar um trabalho de nós cruzados em vários nós do Linux requer que os nós tenham uma relação de confiança entre si (por **rsh** ou **ssh**). Quando você cria o cluster do HPC Pack com o script de implantação de IaaS do Microsoft HPC Pack, o script configura automaticamente uma relação de confiança mútua permanente para a conta de administrador que você especificar. Para usuários que não sejam administradores criados no domínio do cluster, é necessário configurar uma relação de confiança mútua temporária entre os nós quando um trabalho é alocado para eles e destruir a relação depois que o trabalho for concluído. Para fazer isso para cada usuário, forneça um par de chaves RSA para o cluster usado pelo HPC Pack para estabelecer a relação de confiança.

### Gerar um par de chaves RSA
É fácil gerar um par de chaves RSA, que contém uma chave pública e uma chave privada, executando o comando **ssh-keygen** do Linux.

1.	Faça logon em um computador com Linux.

2.	Execute o comando a seguir.

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE]Pressione **Enter** para usar as configurações padrão até que o comando seja concluído. Não insira uma senha aqui. Quando for solicitada uma senha, basta pressionar **Enter**.

    ![Gerar um par de chaves RSA][keygen]

3.	Altere o diretório para o diretório ~/.ssh. A chave privada é armazenada em id\_rsa e a chave pública em id\_rsa.pub.

    ![Chaves públicas e privadas][keys]

### Adicionar o par de chaves ao cluster do HPC Pack
1.	Faça uma conexão de Área de trabalho remota ao nó principal com a sua conta de administrador do HPC Pack (a conta de administrador criada quando você executou o script de implantação).

2. Use os procedimentos padrão do Windows Server para criar uma conta de usuário de domínio no domínio do Active Directory do cluster. Por exemplo, use o Usuário do Active Directory e a ferramenta Computers no nó principal. Com os exemplos neste artigo, supomos que você criará um usuário de domínio chamado hpclab\\hpcuser.

2.	Crie um arquivo chamado C:\\cred.xml e copie os dados da chave RSA nele. Você pode encontrar um exemplo desse arquivo no Apêndice ao final deste artigo.

    ```
    <ExtendedData>
      <PrivateKey>Copy the contents of private key here</PrivateKey>
      <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.	Abra uma janela Comando e digite o seguinte comando para definir os dados de credenciais para a conta de hpclab\\hpcuser. Use o parâmetro **extendeddata** para transmitir o nome do arquivo C:\\cred.xml que você criou para os dados da chave.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Esse comando é concluído com êxito sem resultados. Depois de definir as credenciais para as contas de usuário, você precisa executar os trabalhos, armazenar o arquivo cred.xml em um local seguro ou excluí-lo.

5.	Se você gerou o par de chaves RSA em um dos nós do Linux, lembre-se de excluir as chaves após terminar de usá-las. O HPC Pack não define a relação de confiança mútua se ele encontrar um arquivo id\_rsa ou id\_rsa.pub existente.

>[AZURE.IMPORTANT]Não recomendamos executar um trabalho do Linux como um administrador de cluster em um cluster compartilhado, já que um trabalho enviado por um administrador é executado na conta raiz nos nós do Linux. Um trabalho enviado por um usuário que não seja um administrador é executado em uma conta de usuário local do Linux com o mesmo nome que o usuário do trabalho, e o HPC Pack define a relação de confiança mútua para esse usuário do Linux em todos os nós alocados para o trabalho. Você pode configurar o usuário do Linux manualmente nos nós do Linux antes de executar o trabalho, ou o HPC Pack criará o usuário automaticamente quando o trabalho for enviado. Se o HPC Pack criar o usuário, ele o excluirá depois que o trabalho for concluído. As chaves são removidas após a conclusão do trabalho nos nós para reduzir ameaças de segurança.

## Configurar um compartilhamento de arquivos para nós do Linux

Agora configure um padrão de compartilhamento SMB em uma pasta no nó principal e monte a pasta compartilhada em todos os nós do Linux para permitir que os nós do Linux acessem os arquivos do NAMD com um caminho comum. Confira as opções de compartilhamento de arquivos e as etapas em [Introdução aos nós de computação do Linux em um cluster do HPC Pack no Azure](virtual-machines-linux-cluster-hpcpack.md). (Nesse artigo, recomendamos montar uma pasta compartilhada no nó principal, pois, atualmente, não há suporte para o serviço de Arquivos do Azure nos nós do CentOS 6.6 Linux, que fornece recursos semelhantes. Para saber mais sobre como montar um compartilhamento de Arquivos do Azure, confira [Persistindo conexões para arquivos do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).)

1.	Crie uma pasta no nó principal e compartilhe-a com todos, configurando privilégios de Leitura/Gravação. Neste exemplo, \\\CentOS66HN\\Namd é o nome da pasta, em que CentOS66HN é o nome de host do nó principal.

2. Extraia os arquivos do NAMD na pasta usando uma versão do Windows de **tar** ou outro utilitário do Windows que funciona em arquivos .tar. Extraia o arquivo tar do NAMD para \\\CentOS66HN\\Namd\\namd2 e os arquivos do tutorial em \\\CentOS66HN\\Namd\\namd2\\namdsample.

2.	Abra uma janela do Windows PowerShell e execute os seguintes comandos para montar a pasta compartilhada.

    ```
    PS > clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

O primeiro comando cria uma pasta chamada /namd2 em todos os nós do grupo LinuxNodes. O segundo comando monta a pasta compartilhada //CentOS66HN/Namd/namd2 para a pasta com os bits de dir\_mode e file\_mode definidos como 777. O *nome de usuário* e a *senha* no comando devem ser as credenciais de um usuário no nó principal.

>[AZURE.NOTE]O símbolo “`” no segundo comando é um símbolo de escape para o PowerShell. “`,” significa que “,” (o caractere de vírgula) é uma parte do comando.


## Preparar para executar um trabalho do NAMD

 Seu trabalho do NAMD requer um arquivo *nodelist* para **charmrun** para saber o número de nós que serão usados ao iniciar processos do NAMD. Você escreverá um script Bash que gera o arquivo nodelist e executa **charmrun** com esse arquivo nodelist. Depois, você pode enviar um trabalho do NAMD no Gerenciador de Cluster do HPC que chama esse script.

### Variáveis de ambiente e arquivo nodelist
Informações sobre nós e núcleos podem ser encontradas na variável de ambiente $CCP\_NODES\_CORES, que é definida automaticamente pelo nó principal do HPC Pack quando o trabalho for ativado. O formato para a variável $CCP\_NODES\_CORES é o seguinte:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
```

Ele lista o número total de nós, os nomes dos nós e o número de núcleos em cada nó que estão alocados para o trabalho. Por exemplo, se o trabalho precisar de 10 núcleos para ser executado, o valor de $CCP\_NODES\_CORES será semelhante a:

```
3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
```

Veja abaixo as informações no arquivo nodelist, que serão geradas pelo script:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Por exemplo:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```
### Script Bash para criar um arquivo nodelist

Usando um editor de texto de sua escolha, crie o seguinte script Bash na pasta que contém os arquivos do programa NAMD e nomeie-o hpccharmrun.sh. Um exemplo completo desse arquivo pode ser encontrado no Apêndice deste artigo. Este script Bash tem as funções descritas a seguir.

>[AZURE.TIP]Salve o script como um arquivo de texto com as terminações de linha do Linux (somente LF, não CR LF). Isso garante que ele seja executado corretamente nos nós do Linux.

1.	Defina algumas variáveis.

    ```
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.	Obtenha informações sobre o nó das variáveis de ambiente. $NODESCORES armazena uma lista de palavras divididas de $CCP\_NODES\_CORES. $COUNT é o tamanho de $NODESCORES.

    ```
    # Get node information from the environment variables
    # CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```

3.	Se a variável $CCP\_NODES\_CORES não estiver definida, basta iniciar **charmrun** diretamente. (Isso só deve ocorrer quando você executa esse script diretamente nos nós do Linux.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
    	# CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
    	#echo ${CHARMRUN} $*
    	${CHARMRUN} $*
    ```

4.	Outra opção é criar um arquivo nodelist para **charmrun**.

    ```
    else
    	# Create the nodelist file
    	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    	# Write the head line
    	echo "group main" > ${NODELIST_PATH}

    	# Get every node name and number of cores and write into the nodelist file
    	I=1
    	while [ ${I} -lt ${COUNT} ]
    	do
    		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
    		let "I=${I}+2"
    	done
```
5.	Execute **charmrun** com o arquivo nodelist, obtenha seu status de retorno e remova o arquivo nodelist ao final.

    ${CCP\_NUMCPUS} é outra variável de ambiente definida pelo nó principal do HPC Pack. Ela armazena o número total de núcleos alocados para esse trabalho. Usamos essa variável para especificar o número de processos para charmrun.

    ```
	# Run charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
    fi

    ```
6.	Saia com o status de retorno de **charmrun**.

    ```
    exit ${RTNSTS}
    ```

## Enviar um trabalho do NAMD

Agora você está pronto para enviar um trabalho do NAMD no Gerenciador de Cluster do HPC.

1.	Conecte-se ao nó principal do cluster e inicie o Gerenciador de Cluster do HPC.

2.  Em **Gerenciamento de Nós**, verifique se os nós de computação do Linux estão no estado **Online**. Se não estiverem, selecione-os e clique em **Colocar Online**.

2.  Em **Gerenciamento de Trabalhos**, clique em **Novo Trabalho**.

3.	Insira um nome para o trabalho como *hpccharmrun*.

    ![Novo trabalho do HPC][namd_job]

4.	Na página **Detalhes do Trabalho** em **Recursos de Trabalho**, selecione o tipo de recurso como **Nó** e defina o **Mínimo** para 3. Neste exemplo, executaremos o trabalho em três nós do Linux, sendo que cada nó tem quatro núcleos.

    ![Recursos de trabalho][job_resources]

5.	Na página **Detalhes da Tarefa e Redirecionamento de E/S**, adicione uma nova tarefa ao trabalho e defina os valores a seguir.

    * **Linha de comando** - `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

    * **Diretório de trabalho** - /namd2

    * **Mínimo** - 3

    ![Detalhes de tarefa][task_details]

    >[AZURE.NOTE]Defina o diretório de trabalho aqui, pois **charmrun** tentará navegar até o mesmo diretório de trabalho em cada nó. Se o diretório de trabalho não for definido, o HPC Pack iniciará o comando em uma pasta nomeada aleatoriamente, criada em um dos nós do Linux. Isso causa o seguinte erro nos outros nós: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Para evitar isso, especifique um caminho de pasta que possa ser acessado por todos os nós como o diretório de trabalho.

5.	Clique em **Enviar** para executar este trabalho.

    Por padrão, o HPC Pack envia o trabalho como a sua atual conta de usuário conectado. Uma caixa de diálogo pode solicitar que você insira o nome de usuário e a senha depois de clicar em **Enviar**.

    ![Credenciais de trabalho][creds]

    Em algumas condições, o HPC Pack memoriza as informações do usuário inseridas anteriormente e não mostrará esta caixa de diálogo. Para fazer com que o HPC Pack mostre-a novamente, digite o comando a seguir em uma janela Comando e envie o trabalho.

    ```
    hpccred delcreds
    ```

6.	O trabalho leva vários minutos para ser concluído.

7.	Encontre o log do trabalho em \<headnodeName>\\Namd\\namd2\\namd2\_hpccharmrun.log e os arquivos de saída em \<headnode>\\Namd\\namd2\\namdsample\\1-2-sphere.

8.	Opcionalmente, inicie o VMD para exibir os resultados do trabalho. As etapas para visualizar os arquivos de saída do NAMD (neste caso, uma molécula da proteína ubiquitina em uma esfera de água) estão além do escopo deste artigo. Veja [Tutorial do NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) para obter mais detalhes.

    ![Resultados de trabalho][vmd_view]

## Apêndice

### Exemplo de script hpccharmrun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
	#echo ${CHARMRUN} $*
	${CHARMRUN} $*
else
	# Create the nodelist file
	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

	# Write the head line
	echo "group main" > ${NODELIST_PATH}

	# Get every node name & cores and write into the nodelist file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 
### Exemplo de arquivo cred.xml

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```




<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-cluster-hpcpack-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-namd/creds.png
[task_details]: ./media/virtual-machines-linux-cluster-hpcpack-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-cluster-hpcpack-namd/vmd_view.png

<!---HONumber=Oct15_HO2-->