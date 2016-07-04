<properties
	pageTitle="Provisionar a Máquina Virtual de Ciência de Dados Linux | Microsoft Azure"
	description="Configure e crie uma Máquina Virtual de Ciência de Dados Linux no Azure para realizar a análise e o aprendizado de máquina."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="gokuma;bradsev" />

# Provisionar a Máquina Virtual de Ciência de Dados Linux 


A Máquina Virtual de Ciência de Dados Linux é uma imagem de máquina virtual (VM) do Azure pré-instalada e configurada com uma coleção de ferramentas que são normalmente usadas para análise de dados e aprendizado de máquina. Os componentes de software principais incluídos são:

- Microsoft R Open
- Distribuição do Anaconda Python (v 2.7 e v 3.5), incluindo bibliotecas de análise de dados populares
- Bloco de anotações do Jupyter (R, Python)
- Gerenciador de Armazenamento do Azure
- Linha de comando do Azure para gerenciar recursos do Azure
- Banco de dados PostgresSQL
- Ferramentas de aprendizado de máquina
    - [CNTK (Kit de Ferramentas de Rede Computacional)](https://github.com/Microsoft/CNTK): um software de aprendizado aprofundado da Microsoft Research
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): um sistema de aprendizado de máquina rápido com suporte a técnicas como online, hash, allreduce, reduções, learning2search, ativo e aprendizado interativo.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): uma ferramenta que fornece implementação de árvore aumentada rápida e precisa
    - [Rattle](http://rattle.togaware.com/) (a Ferramenta Analítica do R para Aprender com Facilidade): ferramenta que torna muito fácil a introdução à análise de dados e aprendizado de máquina em R, com uma exploração de dados baseada em GUI e modelagem com geração de código R automática. 
- SDK do Azure em Java, Python, node.js, Ruby, PHP
- Bibliotecas em R e Python para uso em Aprendizado de Máquina do Azure e outros serviços do Azure
- Ferramentas de desenvolvimento e editores (Eclipse, Emacs, gedit, vi)

Fazer a ciência de dados envolve a iteração em uma sequência de tarefas:

- localizar, carregar e pré-processar dados 
- compilar e testar modelos 
- implantar os modelos para consumo em aplicativos inteligentes 

Não é incomum que os cientistas de dados usem uma variedade de ferramentas para executar essas tarefas. Pode ser muito demorado encontrar as versões apropriadas do software e depois baixar, compilar e instalar estas versões.

A Máquina Virtual de Ciência de Dados Linux pode aliviar essa carga substancialmente. Use-a para iniciar rapidamente seu projeto de análise. Ela habilita você a trabalhar em tarefas em uma variedade de linguagens, incluindo R, Python, SQL, Java e C++. O Eclipse fornece um IDE para desenvolver e testar seu código que é fácil de usar. O SDK do Azure incluído na VM permite que você compile seus aplicativos usando vários serviços em Linux para a plataforma de nuvem da Microsoft. Além disso, você tem acesso a outras linguagens como Ruby, Perl, PHP e node.js, também pré-instaladas.

Não há encargos de software para esta imagem da VM de ciência de dados. Você só paga pelas taxas de uso do hardware do Azure, que são avaliadas com base no tamanho da máquina virtual que você provisiona com a imagem de VM. Mais detalhes sobre as taxas de computação podem ser encontrados [aqui](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linuxdsvm/).


## Pré-requisitos

Antes de criar uma Máquina Virtual de Ciência de Dados Linux, você deve ter o seguinte:

- **Uma assinatura do Azure**: para obter uma, confira [Obter avaliação gratuita do Azure](https://azure.microsoft.com/free/).
- **Uma conta de armazenamento do Azure**: para criar uma, confira [Criar uma conta de armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account) Como alternativa, a conta de armazenamento pode ser criada como parte do processo de criação da VM se você não desejar usar uma conta existente.


## Criar sua Máquina Virtual de Ciência de Dados Linux

Veja as etapas para criar uma instância da Máquina Virtual de Ciência de Dados Linux:

1.	Navegue até a listagem de máquinas virtuais no [Portal do Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2.	 Clique no botão **Criar** na parte inferior para ser levado para um assistente.![configure-data-science-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.	 As seções a seguir fornecem as **entradas** para cada uma das **5 etapas** (enumeradas à direita da figura acima) do assistente usado para criar a Máquina Virtual de ciência de Dados da Microsoft. Aqui estão as entradas necessárias para configurar cada uma das seguintes etapas:

  **a. Noções básicas**:

   - **Nome**: o nome do servidor de ciência de dados que você está criando.
   - **Nome de Usuário**: primeira ID de logon da conta
   - **Senha**: primeira senha da conta (você pode usar a chave pública SSH, em vez de senha)
   - **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado OBSERVAÇÃO: Você deve ter privilégios de criação de recurso nesta assinatura. 
   - **Grupo de Recursos**: é possível criar um novo grupo ou usar um existente
   - **Local**: selecione o datacenter mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para o acesso mais rápido à rede

  **b. Tamanho**:

   - Selecione um dos tipos de servidor que atenda aos seus requisitos funcionais e restrições de custo. Você pode obter mais opções de tamanhos de VM selecionando “Exibir Tudo”

  **c. Configurações**

   - **Tipo de disco**: escolha Premium se você preferir uma SSD (unidade de estado sólido); caso contrário, escolha "Padrão".
   - **Conta de Armazenamento**: é possível criar uma nova conta de armazenamento do Azure em sua assinatura ou usar uma já existente no mesmo *Local* que foi escolhido na etapa Noções básicas do assistente.
   - **Outros parâmetros**: na maioria dos casos, você simplesmente usará os valores padrão. É possível focalizar o link informativo para obter ajuda sobre um campo específico, caso você queira considerar o uso de valores não padrão.

  **d. Resumo**:

   - Verifique se todas as informações inseridas estão corretas.

  **e. Comprar**:

   - Clique em **Comprar** para iniciar o provisionamento. Um link para os termos da transação é fornecido. A VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa **Tamanho**. 


O provisionamento deve levar cerca de 10 a 20 minutos. O status do provisionamento é exibido no Portal do Azure.

## Como acessar uma Máquina Virtual de Ciência de Dados Linux

Depois de criar a VM, é possível fazer logon nela usando SSH com as credenciais da conta criada na seção Noções básicas da etapa 3 para a interface shell de texto. No Windows, você pode baixar uma ferramenta de cliente SSH como o [Putty](http://www.putty.org). Se você preferir uma área de trabalho gráfica (Sistema do Windows X), poderá usar o encaminhamento X11 no Putty ou instalar o cliente X2Go.

>[AZURE.NOTE] O cliente X2go apresentou desempenho significativamente melhor do que o encaminhamento X11 nos testes. Portanto, é recomendável usar o cliente X2Go para interface gráfica de área de trabalho.


## Instalando e configurando o cliente X2Go

A VM Linux já está provisionada com um servidor X2Go e pronta para aceitar conexões de cliente. Para se conectar à área de trabalho gráfica da VM Linux, você precisa fazer o descrito a seguir no seu cliente.

1. Baixe e instale o cliente X2Go para sua plataforma de cliente [daqui](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Execute o cliente X2Go e selecione "*Nova Sessão*". Ela abrirá uma janela de configuração com várias guias. Insira os seguintes parâmetros de configuração: 
    * **Guia Sessão**:
        - **Host**: o nome do host ou endereço IP da sua VM de Ciência de Dados Linux.
        - **Logon**: nome de usuário de logon na VM Linux.
        - **Porta SSH**: deixe-a em 22, o valor padrão.
        - **Tipo de Sessão**: altere o valor para XFCE. OBSERVAÇÃO: no momento, a VM Linux dá suporte apenas à área de trabalho XFCE.
    * **Guia Mídia**: você poderá desligar o suporte a som e impressão de cliente se não precisar usá-los. 
    * **Pastas compartilhadas**: caso você queira que os diretórios de seus computadores cliente sejam montados na VM Linux, adicione os diretórios de computador cliente que você deseja compartilhar com a VM nesta guia. 

Uma vez que você fizer logon na VM usando o cliente SSH ou área de trabalho gráfica XFCE por meio do cliente X2Go, você estará pronto para começar a usar as ferramentas instaladas e configuradas na VM. No XFCE, você pode ver atalhos do menu de aplicativos e ícones da área de trabalho para muitas das ferramentas.

## Como criar uma senha forte no servidor do bloco de anotações do Jupyter 

Execute o comando a seguir em um prompt de comando na Máquina Virtual de Ciência de Dados para criar sua própria senha forte para o servidor de bloco de anotações do Jupyter instalado na máquina.

	python -c "import IPython;print(IPython.lib.passwd())"

Insira uma senha forte quando solicitado.

Você verá o hash de senha no formato "sha1:xxxxxx" na saída. Copie esse hash de senha e substitua o hash existente em seu arquivo de configuração do bloco de anotações localizado em: **/usr/local/etc/jupyter/jupyter\_notebook\_config.py** por um nome de parâmetro ***c.NotebookApp.password***. Você precisará editar esse arquivo como o **usuário raiz**.

Você só deve substituir o valor de hash existente que está entre aspas. As aspas e o prefixo ***sha1:*** para o valor do parâmetro precisam ser mantidos.

Por fim, você precisa parar e reiniciar o serviço Jupyter que é instalado na pasta /etc/init.d/jupyter.

>[AZURE.NOTE] Se a nova senha não for aceita após a reinicialização do jupyter ou você tiver problemas ao parar o jupyter, tente reiniciar a máquina virtual.

## Ferramentas Instaladas na Máquina Virtual de Ciência de Dados Linux

### Microsoft R Open 
R é uma das linguagens mais populares para análise de dados e aprendizado de máquina. Se você quiser usar o R para sua análise, a VM terá o MRO (Microsoft R Open) com a MKL (Math Kernel Library). A MKL otimiza as operações matemáticas frequentes em algoritmos analíticos. O MRO é 100% compatível com CRAN-R e qualquer uma das bibliotecas R publicadas em CRAN pode ser instalada no MRO. Você pode editar seus programas R em um dos editores padrão como vi, Emacs ou gedit. Você também pode baixar e usar outros IDEs como o [RStudio](http://www.rstudio.com). Para sua conveniência, um script simples (installRStudio.sh) é fornecido no diretório **/dsvm/tools** que instala o RStudio. Se você estiver usando o editor Emacs, observe que o ESS (Emacs Speaks Statistics) do pacote Emacs, que simplifica o trabalho com arquivos em R no editor Emacs, foi pré-instalado.

Para iniciar o R, basta digitar ***R*** no shell. Isso leva você para um ambiente interativo. Para desenvolver seu programa R você normalmente usa um editor como gedit, vi ou Emacs e, em seguida, executa os scripts no R. Se você instalar o RStudio, você terá um ambiente IDE gráfico completo para desenvolver o seu programa em R.

Também há um script de R para você instalar os [20 melhores pacotes do R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html), caso deseje. Esse script pode ser executado quando você estiver na interface interativa de R, na qual você pode entrar (conforme mencionado) digitando R no shell.

### Python
Para o desenvolvimento com Python, as distribuições 2.7 e 3.5 do Anaconda Python foram instaladas. Essa distribuição contém o Python base com aproximadamente 300 dos mais populares pacotes de matemática, engenharia e análise de dados. Você pode usar os editores de texto padrão. Além disso, você pode usar o Spyder, um IDE do Python que é fornecido com distribuições do Anaconda Python. O Spyder precisa de uma área de trabalho gráfica ou de encaminhamento X11. Um atalho para o Spyder é fornecido na área de trabalho gráfica.

Como temos ambos o Python 2.7 e o 3.5, você precisa ativar especificamente a versão desejada do Python com a qual você deseja trabalhar na sessão atual. O processo de ativação define a variável PATH para a versão desejada do Python.

Para ativar o Python 2.7, execute do shell o descrito a seguir:

	source /anaconda/bin/activate root

O Python 2.7 está instalado em */anaconda/bin*.

Para ativar o Python 3.5, execute do shell o descrito a seguir:

	source /anaconda/bin/activate py35


O Python 3.5 está instalado em */anaconda/envs/py35/bin*

Agora, para invocar a sessão interativa do python, basta digitar ***python*** no shell. Se você estiver em uma interface gráfica ou tiver a configuração do encaminhamento X11, você poderá digitar o comando ***spyder*** para iniciar o IDE do Python.

### Bloco de anotações do Jupyter
A distribuição do Anaconda também acompanha um bloco de anotações do Jupyter, um ambiente de compartilhamento de código e de análise. Um servidor de bloco de anotações do Jupyter foi previamente configurado com os kernels do Python 2, do Python 3 e do R. Há um ícone de área de trabalho chamado "Bloco de anotações do Jupyter" para iniciar o navegador a fim de acessar o servidor do Notebook. Se você estiver usando a VM por meio de cliente SSH ou X2go, você também poderá visitar [https://localhost:9999/](https://localhost:9999/) para acessar o servidor de bloco de anotações do Jupyter.

>[AZURE.NOTE] Continue se você obtiver quaisquer avisos de certificado.

Você pode acessar o servidor de bloco de anotações do Jupyter por meio de qualquer host. Basta digitar "https://<endereço IP ou nome DNS da VM>:9999/". Empacotamos exemplos de alguns blocos de anotações, um em Python em outro em R. Você pode ver o link para os exemplos na home page do bloco de anotações após a autenticação no bloco de anotações do Jupyter usando a senha criada anteriormente. Você pode criar um novo bloco de anotações, selecionando "Novo" e, em seguida, o kernel de idioma. Se você não vir o botão "Novo", clique no ícone do Jupyter na parte superior esquerda para ir para a home page do servidor de notebook.


### IDEs e Editores 

Você tem a opção de vários editores de código. Isso inclui vi/VIM, Emacs, gEdit e Eclipse. Eclipse e gEdit são editores gráficos e você precisa estar conectado a uma área de trabalho gráfica para usá-los. Há atalhos do menu do aplicativo e da área de trabalho para iniciar esses editores.

**VIM** e **Emacs** são editores baseados em texto. No Emacs, instalamos um pacote complementar chamado ESS (Emacs Speaks Statistics) que facilita o trabalho com R no editor Emacs. Mais informações podem ser encontradas em: [ESS](http://ess.r-project.org/).

**Eclipse** é um IDE de software livre, extensível e que dá suporte a vários idiomas. A edição para desenvolvedores de Java é a instância instalada na VM. Há plug-ins disponíveis para várias linguagens populares, os quais podem ser instalados para estender o ambiente do Eclipse. Também temos um plug-in instalado no Eclipse chamado **Kit de ferramentas do Azure para Eclipse**, que permite a você criar, desenvolver, testar e implantar com facilidade aplicativos do Azure usando o ambiente de desenvolvimento do Eclipse, o qual dá suporte a linguagens como Java. Também há um **SDK do Azure para Java** que permite o acesso a diferentes serviços do Azure de dentro de um ambiente Java. É possível encontrar mais informações sobre o kit de ferramentas do Azure para Eclipse em [Kit de ferramentas do Azure para Eclipse](../azure-toolkit-for-eclipse.md).

**LaTex** é instalado por meio do pacote texlive, juntamente com um pacote complementar do Emacs chamado [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html), que simplifica a criação de seus documentos do LaTex no Emacs.

### Bancos de dados

#### Postgres
O banco de dados de software livre **Postgres** está disponível na VM com os serviços em execução e initdb já concluído. Você ainda precisa criar bancos de dados e usuários. Consulte a documentação do Postgres.

####  Cliente Gráfico do SQL
O **SQuirrel SQL**, cliente gráfico do SQL, foi fornecido para conectar-se a bancos de dados diferentes (Microsoft SQL Server, Postgres, MySQL, etc) e executar consultas SQL. Você pode executá-lo de uma sessão de área de trabalho gráfica (usando o cliente X2Go, por exemplo). Para invocar o SQuirrel SQL, você pode iniciá-lo clicando no ícone na área de trabalho ou então executar o comando a seguir no shell.

	/usr/local/squirrel-sql-3.7/squirrel-sql.sh 

Antes do primeiro uso, você precisa configurar os drivers e aliases de banco de dados. Os JDBC drivers estão localizados em:

*/usr/share/java/jdbcdrivers*

É possível encontrar mais informações sobre o SQuirrel SQL no site: [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### Ferramentas de linha de comando para acessar o Microsoft SQL Server

O pacote de driver ODBC do Microsoft SQL Server também vem com duas ferramentas de linha de comando:

**bcp** - o utilitário em massa bcp copia dados entre uma instância do Microsoft SQL Server e um arquivo de dados em um formato especificado pelo usuário. O utilitário bcp pode ser usado para importar grandes números de novas linhas para tabelas do SQL Server ou para exportar dados de tabelas para arquivos de dados. Para importar dados para uma tabela, você deve usar um arquivo de formato criado para essa tabela ou então entender a estrutura da tabela e os tipos de dados que são válidos para suas colunas.

Mais informações podem ser encontradas em [Conectando-se com o bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd** - o utilitário sqlcmd permite inserir instruções Transact-SQL, procedimentos do sistema e arquivos de script no prompt de comando. Esse utilitário usa o ODBC para executar lotes do Transact-SQL.

Mais informações podem ser encontradas em [Conectando-se com o sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx)

>[AZURE.NOTE] Há algumas diferenças nesse utilitário entre as plataformas Linux e Windows. Consulte a página de documentação acima para obter detalhes.


#### Bibliotecas de Acesso do Banco de Dados

Há bibliotecas disponíveis em Python e R para acessar bancos de dados.

- No R, o pacote **RODBC** ou pacote **dplyr** permite que você consulte ou execute instruções SQL no servidor de banco de dados. 
- No Python, a biblioteca **pyodbc** fornece acesso ao banco de dados com o ODBC como a camada subjacente.  

Para acessar o **Postgres**:

- do Python: use a biblioteca **psycopg2**. 
- do R: use o pacote **RPostgreSQL**.


### Ferramentas do Azure 
As ferramentas do Azure a seguir são instaladas na VM:

- **Interface de Linha de Comando do Azure**: a CLI (Interface de Linha de Comando) do Azure permite a você criar e gerenciar recursos do Azure por meio de comandos do shell. Para invocar as ferramentas do Azure, digite apenas ***azure help***. Para saber mais, confira a [página de documentação da CLI do Azure](../virtual-machines-command-line-tools.md).
- **Gerenciador do Armazenamento do Microsoft Azure**: o Gerenciador do Armazenamento do Microsoft Azure é uma ferramenta gráfica usada para navegar pelos objetos armazenados na sua Conta de Armazenamento do Azure e carregar/baixar os dados de e para os blobs do Azure. Você pode acessar o Gerenciador de Armazenamento do ícone de atalho da área de trabalho. Você pode invocá-lo de um prompt do shell digitando ***StorageExplorer***. Você precisa estar conectado de um cliente X2go ou ter a configuração do encaminhamento X11. 
- **Bibliotecas do Azure**: a seguir estão algumas das bibliotecas que foram instaladas e estão, portanto, disponíveis para você:

- **Python**: as bibliotecas relacionadas ao Azure no Python que estão instaladas são ***azure***, ***azureml***, ***pydocumentdb***, ***pyodbc***. As três primeiras bibliotecas permitem que você acesse os serviços de armazenamento do Azure, o Aprendizado de Máquina do Azure e o Banco de Dados de Documentos do Azure (um banco de dados NoSQL no Azure). A quarta biblioteca, pyodbc (juntamente com o Microsoft ODBC Driver for SQL Server), habilita, do Python, o acesso ao Microsoft SQL Server, ao Banco de Dados SQL do Azure e ao SQL Data Warehouse do Azure pelo uso de uma interface do ODBC. Digite ***pip list*** para ver toda a biblioteca listada. Certifique-se de executar este comando nos ambientes do Python 2.7 e o 3.5.

- **R**: as bibliotecas relacionadas ao Azure em R que estão instaladas são ***AzureML*** e ***RODBC***.

- **Java**: a lista de bibliotecas Java do Azure pode ser encontrada no diretório ***/dsvm/sdk/AzureSDKJava*** na VM. As bibliotecas principais são APIs de armazenamento e gerenciamento do Azure, Banco de Dados de Documentos e JDBC drivers para SQL Server.

Você pode acessar o [portal do Azure](https://portal.azure.com) do navegador Firefox previamente instalado. No portal do Azure, você pode criar, gerenciar e monitorar recursos do Azure.

### Aprendizado de Máquina do Azure

O AM (Aprendizado de Máquina) do Azure é um serviço de nuvem totalmente gerenciado que habilita você a compilar, implantar e compartilhar soluções de análise preditiva. Você compila seus modelos e experimentos do Estúdio de Aprendizado de Máquina do Azure. Ele pode ser acessado de um navegador da Web na máquina virtual de ciência de dados visitando [Aprendizado de Máquina do Microsoft Azure](https://studio.azureml.net).

Depois que fizer logon no Estúdio de Aprendizado de Máquina do Azure, você terá acesso a uma tela de experimentação em que você pode compilar um fluxo lógico para os algoritmos de aprendizado de máquina. Você também tem acesso a um Bloco de Anotações do Jupyter hospedado no AM do Azure e pode trabalhar perfeitamente com os testes no Estúdio. O AM do Azure permite a você colocar em operação os modelos de AM compilados por você, encapsulando-as em uma interface de serviço Web. Isso habilita clientes escritos em qualquer linguagem a invocar as previsões dos modelos de AM. Você pode encontrar mais informações sobre o AM do Azure na [Documentação do Aprendizado de Máquina](https://azure.microsoft.com/documentation/services/machine-learning/).

Você pode também criar seus modelos em R ou Python na VM e, em seguida, implantá-los em produção no AM do Azure. Instalamos bibliotecas em R e Python para habilitar essa funcionalidade.

- A biblioteca em R chama-se ***AzureML***. 
- Em Python, chama-se ***azureml***. 

Para obter informações sobre como implantar modelos em R e Python no AM do Azure, consulte a seção *Compilar modelos usando R ou Python e Operacionalizá-los usando o Aprendizado de Máquina do Azure* de [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](machine-learning-data-science-vm-do-ten-things.md).
 
>[AZURE.NOTE] Essas instruções foram escritas para a versão do Windows da VM de Ciência de Dados, mas as informações lá fornecidas sobre a implantação de modelos para o AM do Azure também são aplicáveis à VM Linux.

### Ferramentas de Aprendizado de Máquina

A VM vem com algumas ferramentas/algoritmos de AM que foram pré-compilados e pré-instalados localmente. Estão incluídos:

* **CNTK** (Kit de Ferramentas de Rede Computacional da Microsoft Research) - um software de aprendizado aprofundado
* **Vowpal Wabbit** -um algoritmo de aprendizado rápido online
* **xgboost** -uma ferramenta que fornece algoritmos de árvore aumentados e otimizados
* **Python** - o Anaconda Python é fornecido com os algoritmos de AM com bibliotecas como Scikit-learn. Você pode instalar outras bibliotecas executando a instalação do pip
* **R** - uma vasta biblioteca de funções de AM estão disponíveis para R. Algumas das bibliotecas pré-instaladas são lm, glm, randomForest e rpart. Outras bibliotecas podem ser instaladas, executando 

		install.packages(<lib name>)

Eis aqui algumas informações adicionais sobre as três primeiras ferramentas de AM na lista.

#### CNTK
Este é um kit de ferramentas de aprendizado aprofundado de software livre. Ele é uma ferramenta de linha de comando (cntk) e já está em PATH.

Para executar um exemplo básico, faça o seguinte no shell:

	# Copy samples to your home directory and execute cntk
	cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo 
	cd cntkdemo/Data
	cntk configFile=../Config/Simple.cntk

Você encontrará a saída do modelo em *~/cntkdemo/Output/Models*

Mais informações sobre CNTK podem ser encontradas em [github.com/Microsoft/CNTK](https://github.com/Microsoft/CNTK) e na [Wiki CNTK](https://github.com/Microsoft/CNTK/wiki).


#### VW (Vowpal Wabbit):

Vowpal Wabbit é um sistema de aprendizado de máquina que resulta em avanços na vanguarda do aprendizado de máquina, usando técnicas como online, hash, allreduce, reduções, learning2search, ativo e aprendizado interativo.

Para executar a ferramenta em um exemplo bastante básico, faça o seguinte:

	cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
	cd vwdemo
	vw house_dataset

Há outras demonstrações maiores nesse diretório. Mais informações sobre o VW podem ser encontradas em [github.com/JohnLangford/vowpal\_wabbit](https://github.com/JohnLangford/vowpal_wabbit) e na [Wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### xgboost
Essa é uma biblioteca que é projetada e otimizada para algoritmos aumentados (de árvore). O objetivo desta biblioteca é empurrar os limites de computação de máquinas para os extremos necessários para fornecer aumento de árvore de grande escala escalonável, portátil e preciso.

Ele é fornecido como uma linha de comando, bem como uma biblioteca do R.

Para usar esta biblioteca em R, você pode iniciar a sessão interativa do R (basta digitar *R* no shell) e carregar a biblioteca.

Aqui está um exemplo simples, que você pode executar no prompt do R:

	library(xgboost)

	data(agaricus.train, package='xgboost')
	data(agaricus.test, package='xgboost')
	train <- agaricus.train
	test <- agaricus.test
	bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
	pred <- predict(bst, test$data)

Para executar a linha de comando do xgboost, aqui estão os comandos a executar no shell:

	cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
	cd xgboostdemo
	xgboost mushroom.conf


Um arquivo .model é gravado no diretório especificado. Mais informações sobre este exemplo de demonstração podem ser encontradas [aqui](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Mais informações sobre o xgboost são encontradas na [página de documentação do xgboost](https://xgboost.readthedocs.org/en/latest/) e no seu [repositório Github](https://github.com/dmlc/xgboost).

#### Rattle
O Rattle (a Ferramenta Analítica do R para Aprender com Facilidade) torna muito fácil a introdução à mineração de dados em R, com uma exploração e modelagem de dados baseada em GUI. Ele apresenta resumos estatísticos e visuais dos dados, transforma os dados que podem ser modelados prontamente, compila modelos de dados supervisionados e sem supervisão, apresenta o desempenho dos modelos graficamente e calcula as pontuações de novos conjuntos de dados. Ele também gera código R replicando as operações na interface do usuário que pode ser executado diretamente em R OU usado como ponto de partida para análise posterior.

Para executar o Rattle, você precisa estar em uma sessão de logon da área de trabalho gráfica. No terminal, digite ```R``` para entrar no ambiente R. No prompt do R, digite os seguintes comandos:

	library(rattle)
	rattle()
	
Agora, uma interface gráfica se abrirá com um conjunto de guias. Aqui estão etapas de início rápido no Rattle para usar um conjunto de dados meteorológicos de exemplo e compilar um modelo. Em algumas das etapas abaixo, ele solicitará que quaisquer pacotes R necessários que ainda não estejam no sistema sejam instalados e carregados automaticamente. **OBSERVAÇÃO**: você pode ver um prompt na sua janela do console do R solicitando que você escolha se deseja ou não instalar pacotes para sua biblioteca pessoal caso você não tenha acesso ao pacote no diretório do sistema (o padrão). Se você vir esses prompts, responda "s".

1. Clique em Executar
2. Uma caixa de diálogo será exibida perguntando se você deseja usar o conjunto de dados meteorológicos. Clique em Sim para carregar o exemplo
3. Clique na guia Modelo
4. Clique em Executar para compilar uma árvore de decisão
5. Clique em Desenhar para exibir a árvore de decisão 
6. Clique no botão de opção Floresta e clique em Executar para compilar uma floresta aleatória 
7. Clique no botão Avaliar
8. Clique no botão de opção Risco e clique em Executar para exibir dois gráficos de desempenho de risco (cumulativos)
9. Clique na guia Log para mostrar o código R de geração para as operações acima (observação: há um bug na versão atual do Rattle - Insira '#' antes de 'Exportar este log...' no texto do log).
10. Clique no botão Exportar para salvar o script de R para o arquivo weather\_script.R na pasta base

Você pode sair do Rattle e do R. Agora você pode modificar o script do R gerado ou usá-la como ele é para executá-lo em qualquer momento, para repetir tudo o que foi feito na interface do usuário do Rattle. Essa é uma maneira fácil, especialmente para iniciantes em R, de fazer análise e aprendizado de máquina rapidamente em uma interface gráfica e, ao mesmo tempo, gerar código em R automaticamente para modificar e/ou aprender.


## Próximas etapas
Veja algumas das próximas etapas para continuar sua aprendizagem e exploração.

* Explore as várias ferramentas de ciência de dados na VM de ciência de dados ao experimentar as ferramentas descritas neste artigo. Você também pode executar *dsvm-more-info* no shell contido na máquina virtual para uma introdução básica e ponteiros para obter mais informações sobre as ferramentas instaladas na VM.  
* Saiba como criar soluções completas de análise sistematicamente usando o [Processo de Ciência de Dados de Equipe](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)
* Visite a [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) para obter exemplos de análise de dados e de aprendizado de máquina que usam o Cortana Analytics Suite. 

<!---HONumber=AcomDC_0622_2016-->