<properties linkid="services-linux-cassandra-with-linux" urlDisplayName="Cassandra with Linux" pageTitle="Run Cassandra with Linux on Azure" metaKeywords="" description="Explains how to run a Cassandra cluster on Linux in Azure Virtual Machines." metaCanonical="" services="virtual-machines" documentationCenter="nodejs" title="Running Cassandra with Linux on Azure and Accessing it from Node.js" authors="hanuk" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanuk"></tags>

# <span></span></a>Executando Cassandra com Linux no Azure e acessando-a do Node.js

**Autor:** Hanu Kommalapati

## Sumário

-   [Visão geral][]
-   [Esquema de implantação Cassandra][]
-   [Implantação composta][]
-   [Implantação de máquinas virtuais Azure][]
-   [Tarefa 1: Implantar o Cluster Linux][]
-   [Tarefa 2: Configurar Cassandra em cada Máquina Virtual][]
-   [Tarefa 3: Acessar o Cluster de Cassandra do Node.js][]
-   [Conclusão][]

## <span id="overview"></span> </a>Visão geral

O Azure fornece um serviço de banco de dados NoSQL com o armazenamento de tabela do Azure que possibilita o armazenamento sem esquema de objetos de negócios. Este serviço pode ser usado do Node.JS, .NET, Java e outras linguagens que podem falar HTTP e REST. No entanto, existem outros bancos de dados NoSQL populares como Cassandra e Couchbase que não podem ser executados no Azure PaaS devido ao seu modelo de serviço de nuvem sem estado. As máquinas virtuais Azure permitem agora a execução desses bancos de dados NoSQL no Azure sem alterações para a base de código. A intenção da redação deste artigo é mostrar como executar um cluster Cassandra em máquinas virtuais e acessá-las do Node.js. Isso não cobre a implantação Cassandra para operações de produção do mundo real onde é necessário examinar o cluster Cassandra de vários centros de dados com as estratégias de backup e recuperação associadas. Neste exercício, usaremos a versão Ubuntu 12.04 do Linux e Cassandra 1.0.10; No entanto, o processo pode ser ajustado para qualquer distribuição do Linux.

## <span id="schematic"></span> </a>Esquema de implantação Cassandra

A capacidade de máquinas virtuais do Azure permite a execução de bancos de dados NoSQL como [Cassandra][] em nuvem pública da Microsoft tão fácil quanto executá-los em um ambiente de nuvem privada, exceto uma diferença de configuração da rede virtual específica à infraestrutura de máquinas virtuais do Azure. Quando este artigo foi escrito, Cassandra não estava disponível como um serviço gerenciado no Azure e, portanto, neste artigo, examinaremos a configuração de um cluster Cassandra em máquinas virtuais e acessá-lo de outra instância do Linux hospedada dentro de máquinas virtuais também. Os trechos de código do node.js mostrados também podem ser usados do aplicativo da web de PaaS hospedado ou do serviço da web. Uma das principais vantagens do Azure é permitir que o modelo de aplicativo composto aproveite o melhor dos mundos PaaS e IaaS.

Há dois modelos de implantação viáveis para o ambiente de aplicativo de Cassandra: uma implantação composta e implantação de máquinas virtuais independente. Em uma implantação composta, um cluster de máquinas virtuais hospedadas Cassandra será consumido de um aplicativo da web PaaS hospedado do Azure (ou serviço da web) usando a interface Thrift através do balanceador de carga. Embora cada proxies de nó Cassandra ajuda a solicitação para outros nós de mesmo nível em caso de falha de espaço da chave, o balanceador de carga ajuda com o balanceamento de carga de nível de entrada das solicitações. O balanceador de carga também cria uma área protegida de firewall para um melhor controle dos dados.

## <span id="composite"></span> </a> Implantação composta

A meta de uma implantação composta é maximizar o uso de PaaS, mantendo a superfície da máquina virtual a um mínimo absoluto, em um esforço para economizar a sobrecarga imposta pelo gerenciamento de infraestrutura de máquinas virtuais. Devido à sobrecarga de gerenciamento do servidor, implante apenas os componentes que requerem um comportamento com monitoração de estado que não pode ser facilmente modificado devido a vários motivos, incluindo o tempo de entrega, falta de visibilidade em código-fonte e acesso de baixo nível para o sistema operacional.

![Diagrama de implantação composta][]

## <span id="deployment"></span> </a>Implantação de máquinas virtuais Azure

![Implantação de máquinas virtuais][]

Nos diagramas acima, um cluster de quatro nós Cassandra é implantado dentro das máquinas virtuais atrás de um balanceador de carga que está configurado para permitir o tráfego Thrift. O aplicativo PaaS hospedado do Azure acessa o cluster usando bibliotecas Thrift específicas de linguagem. Há bibliotecas para linguagens incluindo Java, C#, Node.js, Python e C++. A implantação de máquinas virtuais independente mostrada no segundo diagrama consome dados por aplicativos executados dentro de outro serviço de nuvem hospedado em máquinas virtuais.

## <span id="task1"></span> </a>Tarefa 1: Implantar o Cluster Linux

A sequência típica para a criação de um cluster é:

![Diagrama de sequência para a criação de um cluster][]

**Etapa 1: Gerar o par de chave SSH**

O Azure precisa de uma chave pública X509 que é PEM ou DER codificada no momento de provisionamento. Gerar um par de chaves pública/privada usando as instruções localizadas em [Como usar SSH com Linux no Azure][]. Se você planeja usar putty.exe como um cliente SSH no Windows ou Linux, você terá que converter o PEM codificado da chave privada RSA para o formato PPK usando puttygen.exe. Instruções para isso podem ser encontradas em [Gerando par de chaves SSH para a implantação de MV do Linux no Windows Azure][].

**Etapa 2: Criar uma MV Ubuntu**

Para criar a primeira MV Ubuntu, faça logon no portal de visualização do Azure, clique em **Novo**, clique em **Máquina Virtual**, clique em **Da galeria**, clique em **Servidor Unbuntu 12.xx** e em seguida, clique na seta à direita. Para um tutorial que descreve como criar uma MV Linux, consulte [Criar uma máquina Virtual executando Linux][].

Em seguida, insira as seguintes informações na tela de configuração da MV:

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Nome do campo</th>
<th align="left">Valor do campo</th>
<th align="left">Comentários</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Nome da Máquina Virtual</td>
<td align="left">hk-cas1</td>
<td align="left">Este é o nome do organizador da MV</td>
</tr>
<tr class="even">
<td align="left">Novo nome de usuário</td>
<td align="left">localadmin</td>
<td align="left">&quot;admin&quot; é um nome de usuário reservado no Ubuntu 12.xx</td>
</tr>
<tr class="odd">
<td align="left">Nova senha</td>
<td align="left"><em>senha de alta segurança</em></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Confirmar Senha</td>
<td align="left"><em>senha de alta segurança</em></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Tamanho</td>
<td align="left">Pequena</td>
<td align="left">Selecione a MV com base nas necessidades de IO.</td>
</tr>
<tr class="even">
<td align="left">Proteja usando a chave SSH para autenticação</td>
<td align="left">Clique na caixa de seleção</td>
<td align="left">Verifique se você deseja proteger com uma chave SSH</td>
</tr>
<tr class="odd">
<td align="left">Certificado</td>
<td align="left"><em>nome do arquivo do certificado de chave pública</em></td>
<td align="left">Chave pública SSH codificada em DER ou PEM gerada com OpenSSL ou outras ferramentas</td>
</tr>
</tbody>
</table>

Insira as seguintes informações na tela do Modo da MV:

| Nome do campo                          | Valor do campo                  | Comentários                                                                               |
|----------------------------------------|---------------------------------|-------------------------------------------------------------------------------------------|
| MV Virtual autônoma                    | caixa de rádio "verificação"    | Isso é para a primeira MV de MVs subsequentes, usaremos a opção "Conectar à MV existente" |
| Nome DNS                               | *nome exclusivo*.cloudapp.net   | Dê um nome do balanceador de carga independente da máquina                                |
| Conta de armazenamento                 | *conta de armazenamento padrão* | Use a conta de armazenamento padrão que você criou                                        |
| Região/grupo de afinidade/rede virtual | Oeste dos EUA                   | Selecione uma região na qual seus aplicativos da web acessam o cluster Cassandra          |

Repita o processo acima para todas as máquinas virtuais que farão parte do cluster Cassandra. Neste ponto, todas as máquinas farão parte da mesma rede e podem efetuar ping entre si. Se o ping não funcionar, verifique a configuração do firewall (por exemplo, iptables) da MV para certificar-se de que o ICMP é permitido. Certifique-se de desativar o ICMP quando a conectividade de rede for testada com sucesso para reduzir o vetor de ataque.

**Etapa 3: Adicionar uma carga balanceada de ponto de extremidade Thrift**

Após a etapa 1 e a etapa 2, cada MV deve ter o ponto de extremidade do SSH já definido. Agora vamos adicionar a carga equilibrada do ponto de extremidade Thrift com uma porta pública de 9160. Esta é a sequência:

a) A partir da tela de detalhes da primeira MV, clique em "Adicionar ponto de extremidade"

b) Na tela "Adicionar o ponto de extremidade à máquina virtual", selecione o botão "Adicionar o ponto de extremidade"

c) Clique na seta à direita

d) Na tela "Especificar detalhes do ponto de extremidade", digite o seguinte

<table>

<tr>

<th>
Nome do campo

</th>

<th>
Valor do campo

</th>

<th>
Comentários

</th>

</tr>

<tr>

<td>
Nome

</td>

<td>
cassandra

</td>

<td>
Qualquer nome do ponto de extremidade exclusivo estará bem

</td>

</tr>

<tr>

<td>
Protocolo

</td>

<td>
TCP

</td>

<td>
</td>

</tr>

<tr>

<td>
Porta pública

</td>

<td>
9160

</td>

<td>
Porta Thrift padrão.

</td>

</tr>

<tr>

<td>
Porta privada

</td>

<td>
9160

</td>

<td>
A menos que você alterou isso em cassandra.yaml

</td>

</tr>

</table>
Após o trabalho acima, a primeira MV exibirá o ponto de extremidade cassandra no campo BALANCEAMENTO DE CARGA como "Não". Ignore isso por enquanto, pois será alterado para "Sim" depois que adicionarmos esse ponto de extremidade para as MVs subsequentes

</p>
e) Agora, selecione a segunda MV e adicione o ponto de extremidade repetindo o processo acima com apenas pequenas diferenças que você irá selecionar "tráfego de balanceamento de carga em um ponto de extremidade existente" e use "cassandra-960" da caixa suspensa. Neste estágio, o mapeamento do ponto de extremidade para ambas as MVs alterará o estado do status de BALANCEAMENTO DE CARGA "Não" para "Sim".

Repita "e" para os nós subsequentes no cluster.

Agora que temos as MVs prontas, é hora de configurar Cassandra em cada uma das MVs. Como Cassandra não é uma parte padrão de muitas distribuições do Linux, vamos recorrer a um processo de implantação manual.

Observe que estamos usando uma abordagem manual para a instalação do software em cada MV. No entanto, o processo pode ser acelerado, configurando uma MV Cassandra totalmente funcional, capturá-la como a imagem base e criar instâncias adicionais usando essa imagem base. As instruções para capturar a imagem do Linux estão localizadas em [Como capturar uma imagem de uma máquina virtual executando Linux][].]

## <span id="task2"></span> </a>Tarefa 2: Configurar Cassandra em cada Máquina Virtual

**Etapa 1: Instalar os pré-requisitos**

Cassandra requer máquina virtual Java, portanto, instale o JRE mais recente usando o seguinte comando para derivados Debian, incluindo o Ubuntu:

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

**Etapa 2: Instalação do Cassandra**

1.  Faça logon usando SSH para a instância da MV Linux (Ubuntu).

2.  Use wget para baixar bits Cassandra do espelho sugerido em (<http://cassandra.apache.org/download/>)[<http://cassandra.apache.org/download/>] para o diretório "~/downloads" como apache-cassandra-bin.tar.gz. Observe que o número da versão não está incluído no arquivo baixado para certificar-se de que a publicação permaneça independente da versão.

3.  Descompacte a bola tar no diretório de logon padrão, executando o seguinte comando:

        tar -zxvf downloads/apache-cassandra-bin.tar.gz

    O comando acima expandirá o arquivo no diretório da versão apache-cassandra.

4.  Crie os dois seguintes diretórios padrão para a realização de registros e dados:

        $ sudo chown -R /var/lib/cassandra
        $ sudo chown -R /var/log/cassandra

5.  Conceda permissões de gravação para a identidade do usuário sob a qual Cassandra será executada

        a.  sudo chown -R <user>:<group> /var/lib/cassandra
        b.  sudo chown -R <user>:<group> /var/log/cassandra
        To use current user context, replace the <user> and <group> with $USER and $GROUP

6.  Inicie o Cassandra do diretório de bin da versão apache-cassandra usando o seguinte comando:

        $ ./cassandra

O acima iniciará o nó Cassandra como um processo em segundo plano. Use -cassandra "f" para iniciar o processo no modo de primeiro plano.

O registro deve poder mostrar o erro mx4j. Cassandra funcionará bem sem o mx4j, mas é necessário para gerenciar a instalação de Cassandra. Finalize o processo de Cassandra antes da próxima etapa.

**Etapa 3: Instale mx4j**

    a)  Download mx4j: wget [http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download](http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download) -O mx4j.tar.gz
    b)  tar -zxvf mx4j.tar.gz
    c)  cp mx4j-23.0.2/lib/*.jar ~/apache-cassandra-<version>/lib
    d)  rm -rf mx4j-23.0.2
    e)  rm mx4j.tar.gz

Reinicie o processo Cassandra nesta fase

**Etapa 4: Instalação do teste do Cassandra**

Execute o seguinte comando a partir do diretório bin do Cassandra para conectar usando o cliente thrift:

    cassandra-cli -h localhost -p 9160

**Etapa 5: Habilite o Cassandra para conexões externas**

Por padrão, Cassandra só é definido para ouvir no endereço loopback e, portanto, essa alteração é obrigatória para conexões externas.

Edite "conf/cassandra.yaml" para alterar o **listen\_address** e **rpc\_address** para o endereço de IP ou nome do organizador do servidor, assim o nó atual ficará visível para outros nós bem como para os balanceadores de carga externos.

Repita a Etapa 1 a 5 para todos os nós no cluster.

Agora que todas as MVs individuais estão prontas com o software necessário, é hora de estabelecer a comunicação entre os nós por meio da configuração de propagação. Analise as informações localizadas em [][]<http://wiki.apache.org/cassandra/MultinodeCluster></a> para obter detalhes sobre a configuração de cluster de vários nós.

**Etapa 6: Configure o Cluster de vários nós**

Edite cassandra.yaml para alterar as seguintes propriedades em todas as MVs:

**a) cluster\_name**

O nome do cluster padrão está definido como "Cluster de teste"; altere-o para algo que reflete o seu aplicativo. Exemplo: "AppStore" . Se você já tinha iniciado o cluster com o "Cluster de teste" para testes durante a instalação, você receberá um erro de incompatibilidade do nome do cluster. Para corrigir este erro, exclua todos os arquivos no diretório /var/lib/cassandra/data/system.

**b) sementes**

Os endereços de IP especificados aqui serão usados por novos nós para saber mais sobre a topologia de anel. Defina os nós mais confiáveis como suas sementes em um formato separado por vírgula: "*host1*,*host2*" . Exemplo de configuração: "hk-ub1,hk-ub2".

Iremos aceitar os tokens padrão fornecidos pelos servidores semente, pois esse não é nosso foco neste exercício. Para geração de token ideal, consulte o script de python, localizado em:
[][1]<http://wiki.apache.org/cassandra/GettingStarted></a>.

Reinicie Cassandra em todos os nós para aplicar as alterações acima.

**Etapa 7: Teste o Cluster de vários nós**

O Nodetool instalado no diretório bin do Cassandra ajudará com as operações do cluster. Nós usaremos o nodetool para verificar a configuração do cluster por meio do formato de comando a seguir:

    $ bin/nodetool -h <hostname> -p 7199 ring

Se a configuração estiver correta, ela deve exibir as informações como mostrado a seguir para um cluster de 3 nós:

<table>
<colgroup>
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Endereço</td>
<td align="left">CONTROLADOR DE DOMÍNIO</td>
<td align="left">Rack</td>
<td align="left">Status</td>
<td align="left">Estado</td>
<td align="left">Carga</td>
<td align="left">Possui</td>
<td align="left">Token</td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">149463697837832744402916220269706844972</td>
</tr>
<tr class="odd">
<td align="left">10.26.196.68</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Acima</td>
<td align="left">Normal</td>
<td align="left">15,69 KB</td>
<td align="left">25.98%</td>
<td align="left">114445918355431753244435008039926455424</td>
</tr>
<tr class="even">
<td align="left">10.26.198.81</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Acima</td>
<td align="left">Normal</td>
<td align="left">15,69 KB</td>
<td align="left">53.44%</td>
<td align="left">70239176883275351288292106998553981501</td>
</tr>
<tr class="odd">
<td align="left">10.26.198.84</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Acima</td>
<td align="left">Normal</td>
<td align="left">18,35 KB</td>
<td align="left">25.98%</td>
<td align="left">149463697837832744402916220269706844972</td>
</tr>
</tbody>
</table>

Neste estágio, o cluster está pronto para clientes Thrift através da URL de serviço de nuvem (nome DNS fornecido durante a criação da primeira MV) criada durante a tarefa "Implantar o Cluster do Linux".

## <span id="task3"></span> </a>Tarefa 3: Acesse o Cluster de Cassandra do Node.js

Crie uma MV Linux no Azure usando o processo descrito nas tarefas anteriores. Certifique-se de que essa MV seja uma MV autônoma, pois usaremos isso como um cliente para acessar o cluster do Cassandra. Instalaremos o Node.js, NPM e [cliente cassandra][] do github antes de conectar ao cluster do Cassandra dessa MV:

**Etapa 1: Instale o Node.js e NPM**

a) Instale os pré-requisitos

    sudo apt-get install g++ libssl-dev apache2-utils make

b) Usaremos a fonte do GitHub para compilar e instalar; antes de podermos clonar o repo, é necessário instalar o tempo de execução do núcleo git:

    sudo apt-get install git-core

c) Repo do nó do clone

    git clone git://github.com/joyent/node.git

d) O acima criará o diretório com o nome "nó". Execute a seguinte sequência de comando para compilar a instalação node.js:

    cd node
    ./configure
    make
    sudo make install

e) Instale o NPM dos binários estáveis, executando o seguinte comando

    curl http://npmjs.org/install.sh | sh

**Etapa 2: Instale o pacote do cliente cassandra**

    npm cassandra-client 

**Etapa 3: Prepare o armazenamento do Cassandra**

O armazenamento do Cassandra usa os conceitos de KEYSPACE e COLUMNFAMILY que podem ser comparados aproximadamente às estruturas do BANCO DE DADOS e TABELA na linguagem do RDBMS. O KEYSAPCE conterá um conjunto de definições de COLUMNFAMILY. Cada COLUMNFAMILY conterá um conjunto de linhas e sucessivamente cada linha contém várias colunas, conforme mostrado na visualização composta abaixo:

![Linhas e colunas][]

Nós usaremos o cluster Cassandra implantado anteriormente para demonstrar o acesso do node.js, criando e consultando as estruturas de dados acima. Criaremos um script simples node.js, que realiza a preparação básica do cluster para armazenar dados de cliente. As técnicas mostradas no script podem ser usadas facilmente em um aplicativo de web do node.js ou serviços da web. Tenha em mente que os trechos destinam-se apenas para mostrar como as coisas funcionam e para as soluções do mundo real, o código mostrado tem muito espaço (por exemplo, segurança, registro, escalabilidade, etc.) para melhoria.

Vamos definir as variáveis necessárias no escopo do script para incluir o PooledConnection do módulo de cliente do cassandra, o nome do keyspace usado com frequência e os parâmetros de conexão do keyspace:

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

Em preparação para armazenar os dados do cliente, é necessário primeiro criar um KEYSPACE usando o seguinte script de exemplo:

    casdemo.js: 
    function createKeyspace(callback){
       var cql = 'CREATE KEYSPACE ' + ksName + ' WITH 
       strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
       var sysConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'],  
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

a função createKeysapce assume uma função de retorno de chamada como argumento que se destina a executar a função de criação COLUMNFAMILY uma vez que o KEYSPACE é um pré-requisito para a criação da família coluna. Observe que é necessário conectar ao "sistema" KEYSPACE para definição de KEYSPACE do aplicativo. [Linguagem de consulta do Cassandra (CQL)][] é usada de forma consistente na interação com o cluster em trechos de código. Como o CQL composto no script acima não tem qualquer marcador de parâmetro, estamos usando uma coleção de parâmetros em branco ("[]") ao método PooledConnection.execute().

Durante a criação do espaço da chave com êxito, a função createColumnFamily(), mostrada no trecho a seguir, será executada para criar as definições de COLUMNFAMILY necessárias:

    casdemo.js: 
    //Creates COLUMNFAMILY
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

Modelo CQL com parâmetros será combinado com objeto de parâmetros para gerar CQL válido para a criação de COLUMNFAMILY. Após a criação bem sucedida do COLUMNFAMILY, o retorno de chamada fornecido, neste caso populateCustomerData(), será chamado como parte da cadeia de chamada assíncrona.

    casdemo.js: 
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
      var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where 
                 custid=?';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) console.log(err);
          else console.log("Inserted customer : " + params[0]);
      });
      con.shutdown();
    }

populateCustomerData() insere duas linhas na COLUMNFAMILY, ou seja, os customers\_cf. Na linguagem de consulta do Cassandra, a atualização irá inserir o registro se o registro já não estiver presente no processo tornando a instrução INSERIR CQL redundante.

Até o momento, conectamos com fio a cadeia de retorno de chamada: createKeyspace() para createColumnFamily() para populateCustomerData(). Agora é hora de começarmos a executar o código pelo seguinte trecho de código:

    casdemo.js:
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    createKeyspace(createColumnFamily);
    //rest of the not shown

Execute o seguinte comando a partir da janela de comandos para executar o script:

    //the following command will create the KEYSPACE, COLUMNFAMILY and //inserts two customer records
    $ node casdemo.js

O método readCustomer() acessará o Azure hospedado do cluster e exibirá o trecho recuperado JSON após a execução da consulta CQL:

    casdemo.js: 
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

Modifique casdemo.js para adicionar o função acima e a chame após comentar no método anterior chamado createKeyspace(), como mostrado abaixo:

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    //createKeyspace(createColumnFamily);
    readCustomer(ksConOptions)
    //rest of the code below not shown
        

## <span id="conclusion"></span> </a>Conclusão

A capacidade de máquinas virtuais Azure permite a criação do Linux (imagens fornecidas por parceiros da Microsoft) e máquinas virtuais do Windows que permitem a migração de produtos de servidor e aplicativos existentes com zero alterações. O servidor do banco de dados NoSQL do Cassandra discutido neste artigo é um desses exemplos. O cluster do Cassandra configurado nesta gravação pode ser acessado pelos serviços de nuvem hospedados do Azure, nuvens públicas e privadas de terceiros de ambientes Windows e Linux OS. Neste artigo, abordamos o node.js como o cliente; No entanto, o Cassandra pode ser acessado do .NET, Java e outros ambientes de linguagem.

  [Visão geral]: #overview
  [Esquema de implantação Cassandra]: #schematic
  [Implantação composta]: #composite
  [Implantação de máquinas virtuais Azure]: #deployment
  [Tarefa 1: Implantar o Cluster Linux]: #task1
  [Tarefa 2: Configurar Cassandra em cada Máquina Virtual]: #task2
  [Tarefa 3: Acessar o Cluster de Cassandra do Node.js]: #task3
  [Conclusão]: #conclusion
  [Cassandra]: http://wiki.apache.org/cassandra/
  [Diagrama de implantação composta]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png
  [Implantação de máquinas virtuais]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png
  [Diagrama de sequência para a criação de um cluster]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux4.png
  [Como usar SSH com Linux no Azure]: http://www.windowsazure.com/pt-br/manage/linux/how-to-guides/ssh-into-linux/
  [Gerando par de chaves SSH para a implantação de MV do Linux no Windows Azure]: http://blogs.msdn.com/b/hanuk/archive/2012/06/07/generating-ssh-key-pair-for-linux-vm-deployment-on-windows-azure.aspx
  [Criar uma máquina Virtual executando Linux]: http://www.windowsazure.com/pt-br/manage/linux/tutorials/virtual-machine-from-gallery/
  [Como capturar uma imagem de uma máquina virtual executando Linux]: https://www.windowsazure.com/pt-br/manage/linux/how-to-guides/capture-an-image/
  []: http://wiki.apache.org/cassandra/MultinodeCluster
  [1]: http://wiki.apache.org/cassandra/GettingStarted
  [cliente cassandra]: https://github.com/racker/node-cassandra-client
  [Linhas e colunas]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux3.png
  [Linguagem de consulta do Cassandra (CQL)]: http://cassandra.apache.org/doc/cql/CQL.html
