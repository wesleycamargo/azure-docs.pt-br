---
title: Provisionar a Máquina Virtual de Ciência de Dados do Windows no Azure | Microsoft Docs
description: Configure e crie uma Máquina Virtual de Ciência de Dados no Azure para realizar a análise e o aprendizado de máquina.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: gokuma
ms.openlocfilehash: 1b293ee8f0f83d727cd647cdcdcc424b4db7e5d3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240878"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Provisionar a Máquina Virtual de Ciência de Dados do Windows no Azure
A DSVM (Máquina Virtual de Ciência de Dados) da Microsoft é uma imagem de VM (máquina virtual) do Azure do Windows. Ela é pré-instalada e configurada com diversas ferramentas que são usadas para a análise de dados e o aprendizado de máquina. AS ferramentas a seguir estão incluídas:

* [Azure Machine Learning](../service/index.yml) Workbench.
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer edition.
* Distribuição do Anaconda Python.
* Jupyter Notebook com kernels do R, Python, PySpark.
* Microsoft Visual Studio Community.
* Microsoft Power BI Desktop.
* Microsoft SQL Server 2017 Developer edition.
* Uma instância autônoma do Apache Spark para desenvolvimento e teste local.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Ferramentas de aprendizado de máquina e análise de dados:
  * Estruturas de aprendizado profundo. Um conjunto avançado de estruturas de IA está incluso na VM: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet e Keras.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit). Um sistema de aprendizado de máquina rápido que dá suporte a técnicas como online hashing, allreduce, reduções, learning2search e aprendizado interativo e ativo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/). Uma ferramenta que fornece implementação de árvore aumentada rápida e precisa.
  * [Rattle](https://togaware.com/rattle/), a ferramenta analítica de R para aprender com facilidade. Uma ferramenta que facilita a introdução à análise de dados e ao aprendizado de máquina em R. Ela inclui uma exploração de dados baseada em GUI e modelagem com geração de código R automática.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/). Software de mineração de dados visual e aprendizado de máquina em Java.
  * [Apache Drill](https://drill.apache.org/). Um mecanismo de consulta SQL livre de esquema para Apache Hadoop, NoSQL e armazenamento em nuvem.  Oferece suporte a interfaces ODBC e JDBC para consultas NoSQL e arquivos de ferramentas de BI padrão, como Power BI, Microsoft Excel e Tableau.
* Bibliotecas em R e Python para uso no Azure Machine Learning e em outros serviços do Azure.
* Git, incluindo Git Bash, para trabalhar com repositórios de código-fonte que incluem GitHub e Azure DevOps. Git fornece vários utilitários populares de linha de comando do Linux que são acessíveis no Git Bash e um prompt de comando. Exemplos são awk, sed, perl, grep, find, wget e curl.

Ciência de dados envolve a iteração em uma sequência de tarefas:

1. Localizar, carregar e pré-processar dados.
1. Compilar e testar modelos.
1. Implantar os modelos para consumo em aplicativos inteligentes.

Os cientistas de dados usam várias ferramentas para essas tarefas. Pode ser muito demorado encontrar as versões apropriadas do software e depois baixá-las e instalá-las. A Máquina Virtual de Ciência de Dados da Microsoft economiza tempo fornecendo uma imagem pronta para uso que pode ser provisionada no Azure com diversas ferramentas populares pré-instaladas e configuradas. 

A Máquina Virtual de Ciência de Dados da Microsoft impulsiona seu projeto de análise. É possível trabalhar em tarefas em várias linguagens, incluindo R, Python, SQL e C#. O Visual Studio fornece um IDE (ambiente de desenvolvimento integrado) fácil de usar para desenvolver e testar o código. O SDK do Azure está incluído na VM. Portanto, você pode compilar seus aplicativos usando vários serviços na plataforma de nuvem da Microsoft. 

Não há encargos de software para esta imagem da VM de ciência de dados. Você paga apenas as taxas de uso do Azure. Elas dependem do tamanho da máquina virtual que você provisiona. Mais detalhes sobre as taxas de computação estão na seção **Detalhes de preço** na página [Máquina Virtual de Ciência de Dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice). 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Outras versões da Máquina Virtual de Ciência de Dados
* Uma imagem do [Ubuntu](dsvm-ubuntu-intro.md). Conta com muitas ferramentas semelhantes para a DSVM, além de algumas estruturas de aprendizado profundo. 
* Uma imagem do [Linux CentOS](linux-dsvm-intro.md).
* A [edição do Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) da Máquina Virtual de Ciência de Dados. Algumas ferramentas estão disponíveis apenas na edição do Windows Server 2016. Caso contrário, este artigo também se aplicará à edição do Windows Server 2012.

## <a name="prerequisite"></a>Pré-requisito
Para criar uma Máquina Virtual de Ciência de Dados da Microsoft, é necessário ter uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Criar sua Máquina Virtual de Ciência de Dados da Microsoft
Para criar uma instância da Máquina Virtual de Ciência de Dados da Microsoft, siga estas etapas:

1. Navegue até a listagem de máquinas virtuais no [Portal do Azure](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Você pode ser solicitado a fazer logon na sua conta do Azure, caso ainda não esteja conectado.
1. Selecione o botão **Criar** na parte inferior para ser levado para um assistente.

  ![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png) 

1. O assistente que cria a Máquina Virtual de Ciência de Dados requer **entrada**. A seguinte entrada é necessária para configurar cada uma das etapas mostradas à direita da figura:

  a. **Noções básicas**:

    i. **Nome**. O nome do servidor de ciência de dados que está sendo criado.  

    II. **Tipo de Disco da VM**. Escolha **SSD** ou **HDD**. Para uma instância GPU NC_v1 como NVidia Tesla K80, escolha **HDD** como o tipo de disco.   

    III. **Nome de Usuário**. A ID da conta do administrador para entrar.   

    IV. **Senha**. A senha da conta do administrador.  

    V. **Assinatura**. Se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado.   

    VI. **Grupo de Recursos**. Você pode criar um grupo novo ou usar um grupo existente.   

    VII. **Local**. Selecione o data center mais apropriado. Para um acesso mais rápido à rede, é o data center que contém a maioria dos seus dados ou que está mais próximo de sua localização física.   

  b. **Tamanho**. Selecione um dos tipos de servidor que atenda aos seus requisitos funcionais e restrições de custo. Para obter mais opções de tamanhos de VM, selecione **Exibir Tudo**.  

  c. **Configurações**:  

    i. **Usar Managed Disks**. Escolha **Managed** se você quiser que o Azure gerencie os discos da VM. Caso contrário, será necessário especificar uma conta de armazenamento nova ou existente.  

    II. **Outros parâmetros**. É possível usar os valores padrão. Se você quiser usar valores não padrão, passe o ponteiro do mouse sobre o link informativo para obter ajuda sobre os campos específicos.  

  d. **Resumo**. Verifique se todas as informações inseridas estão corretas. Selecione **Criar**. 

> [!NOTE]
> * A VM não tem encargos adicionais além dos custos de computação para o tamanho do servidor que você escolheu na etapa **Tamanho**. 
> * O provisionamento leva cerca de 10 a 20 minutos. Seu status é exibido no portal do Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Como acessar uma Máquina Virtual de Ciência de Dados da Microsoft
Depois que a VM for criada e provisionada, será possível entrar na área de trabalho remotamente usando as credenciais da conta de administrador que você configurou anteriormente na seção **Noções básicas**. Você está pronto para começar a usar as ferramentas que estão instaladas e pré-configuradas na VM. Muitas das ferramentas têm blocos do menu iniciar e ícones da área de trabalho. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Ferramentas Instaladas na Máquina Virtual de Ciência de Dados da Microsoft

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition
É possível usar o Microsoft Enterprise Library para R ou Python escalonável para a análise porque o Machine Learning Server Developer edition está instalado na VM. Anteriormente conhecida como Microsoft R Server, Machine Learning Server é uma plataforma de análise de classe empresarial amplamente implementável. Ela está disponível tanto para R como Python e é escalonável, segura e com suporte comercial. 

A Machine Learning Server dá suporte variadas estatísticas de Big Data, modelagem preditiva e tarefas de aprendizado de máquina. Ela oferece suporte a uma gama completa de análises: exploração, análise, visualização e modelagem. Ao usar e estender o R e Python de software livre, a Machine Learning Server é compatível com funções e scripts de R e Python. Também é compatível com pacotes CRAN, pip e Conda para analisar dados em escala empresarial. 

A Machine Learning Server também aborda as limitações de memória interna do R de software livre, adicionando processamento paralelo e em partes dos dados. Assim é possível executar análises em dados muito maiores do que cabe na memória principal. O Visual Studio Community está incluído na VM. Ele contém as Ferramentas de R para Visual Studio e a extensão PTVS (Ferramentas Python para Visual Studio), que fornece um IDE completo para trabalhar com o R ou o Python. Nós também fornecemos outros IDEs, como [RStudio](http://www.rstudio.com) e [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) na VM. 

### <a name="python"></a>Python
Para o desenvolvimento usando o Python, as distribuições do Anaconda Python 2.7 e 3.6 foram instaladas. Essas distribuições têm o Python base com aproximadamente 300 dos mais populares pacotes de matemática, engenharia e análise de dados. É possível usar a PTVS, que é instalada dentro do Visual Studio Community 2017. Ou você pode usar um dos IDEs em pacote com Anaconda como IDLE ou Spyder. Pesquise e inicie um desses pacotes (Win+S).

> [!NOTE]
> Para apontar as Ferramentas Python para Visual Studio para o Anaconda Python 2.7, você precisa criar ambientes personalizados para cada versão. Para definir esses caminhos de ambiente no Visual Studio 2017 Community, navegue até **Ferramentas** > **Ferramentas Python** > **Ambientes Python**. Em seguida, selecione **+ Custom**. 
> 
> 

Anaconda Python 3.6 é instalado em **C:\Anaconda**. Anaconda Python 2.7 é instalado em **c:\Anaconda\envs\python2**. Para ver etapas detalhadas, consulte a [Documentação do PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters). 

### <a name="the-jupyter-notebook"></a>O Jupyter Notebook
A distribuição do Anaconda também acompanha o Jupyter Notebook, um ambiente para compartilhamento de código e análise. Um servidor do Jupyter Notebook foi pré-configurado com os kernels do Python 2.7, Python 3.x, PySpark, Julia e R. Para inicializar o servidor do Jupyter e o navegador a fim de acessar o servidor do Notebook, há um ícone da área de trabalho chamado **Jupyter Notebook**. 

Nós empacotamos vários notebooks de exemplo em Python e R. Após acessar o Jupyter, os notebooks mostram como trabalhar com as seguintes tecnologias:

* Machine Learning Server.
* Serviços de Machine Learning do SQL Server, análise no banco de dados. 
* Python.
* Microsoft Cognitive ToolKit.
* Tensorflow.
* Outras tecnologias do Azure. 

É possível ver o link para os exemplos na home page do notebook após a autenticação no Jupyter Notebook usando a senha criada em uma etapa anterior. 

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017
O Visual Studio Community edition é instalado na VM. É uma versão gratuita do popular IDE da Microsoft que pode ser usada para fins de avaliação e para pequenas equipes. Consulte os [Termos de licenciamento](https://www.visualstudio.com/support/legal/mt171547). 

Abra o Visual Studio clicando duas vezes no ícone da área de trabalho ou no menu **Iniciar** . Pesquise programas (Win+S), seguido por **Visual Studio**. Nesse local, você pode criar projetos em linguagens como C#, Python, R e node.js. Plug-ins instalados tornam conveniente trabalhar com os seguintes serviços do Azure:
* Catálogo de Dados do Azure
* Azure HDInsight Hadoop e Spark
* Azure Data Lake 

Agora também há um plug-in chamado ```Visual Studio Tools for AI``` que é integrado diretamente ao Azure Machine Learning e ajuda a compilar aplicativos de IA rapidamente. 

> [!NOTE]
> Você poderá receber uma mensagem informando que o período de avaliação expirou. Insira suas credenciais de conta da Microsoft. Ou crie uma nova conta gratuita para obter acesso ao Visual Studio Community. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition
Uma versão de desenvolvedor do SQL Server 2017 com os Serviços de Machine Learning para execução da análise no banco de dados é fornecida na VM com R ou Python. Os Serviços de Machine Learning fornecem uma plataforma para desenvolver e implantar aplicativos inteligentes. É possível usar essas linguagens e os vários pacotes da comunidade para criar modelos e gerar previsões para seus dados do SQL Server. Você pode manter a análise próxima aos dados porque os Serviços de Machine Learning, no banco de dados, integram as linguagens R e Python dentro do SQL Server. Isso elimina os custos e os riscos de segurança associados à movimentação de dados.

> [!NOTE]
> O SQL Server Developer Edition só pode ser usado para fins de desenvolvimento e teste. Você precisa de uma licença para executá-la em produção. 
> 
> 

É possível acessar o SQL Server iniciando o SQL Server Management Studio. O nome da VM é preenchido como o **Nome do Servidor**. Use a autenticação do Windows quando estiver conectado como o administrador no Windows. Quando estiver no SQL Server Management Studio, você pode criar outros usuários, criar bancos de dados, importar dados e executar consultas SQL. 

Para habilitar a análise no banco de dados usando os Serviços de Machine Learning do SQL, execute o comando a seguir como uma única ação no SQL Server Management Studio depois de entrar como administrador do servidor: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Várias ferramentas do Azure são instaladas na VM:

* Um atalho da área de trabalho vai para a documentação do SDK do Azure. 
* **AzCopy**: é usado para mover dados para dentro e fora da sua conta de Armazenamento do Microsoft Azure. Para ver o uso, insira **Azcopy** em um prompt de comando. 
* Use o **Gerenciador de Armazenamento do Azure** para procurar os objetos armazenados na sua conta de Armazenamento do Azure. Ele também transfere dados de e para o Armazenamento do Microsoft Azure. Para acessar essa ferramenta, você pode inserir **Gerenciador de Armazenamento** no campo **Pesquisar**. Ou encontre a ferramenta no menu **Iniciar** do Windows. 
* **Adlcopy** move dados para o Azure Data Lake. Para ver o uso, insira **adlcopy** em um prompt de comando. 
* **dtui** move dados para dentro e fora do Azure Cosmos DB, um banco de dados NoSQL na nuvem. Insira **dtui** em um prompt de comando. 
* **Integration Runtime do Azure Data Factory** move dados entre as fontes de dados locais e a nuvem. É usado em ferramentas como o Azure Data Factory. 
* **Microsoft Azure PowerShell** é uma ferramenta usada para administrar os recursos do Azure na linguagem de scripts do PowerShell. Ela também é instalada na VM. 

### <a name="power-bi"></a>Power BI
O **Power BI Desktop** é instalado para ajudá-lo a compilar painéis e visualizações. Use essa ferramenta para extrair dados de fontes diferentes, criar painéis e relatórios e publicá-los na nuvem. Para obter mais informações, consulte o site do [Power BI](https://powerbi.microsoft.com). Você pode localizar o Power BI Desktop no menu **Iniciar**. 

> [!NOTE]
> É necessária uma conta do Microsoft Office 365 para acessar o Power BI. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

O Azure Machine Learning Workbench é um aplicativo de área de trabalho e uma interface de linha de comando. O Workbench tem preparação de dados interna que aprende as etapas de preparação dos seus dados conforme você as executa. Ele também fornece gerenciamento de projetos, histórico de execuções e integração ao notebook para aumentar sua produtividade. 

Você pode aproveitar as melhores estruturas de software livre, incluindo TensorFlow, Cognitive Toolkit, Spark ML e scikit-learn para desenvolver seus modelos. Na DSVM, fornecemos um ícone da área de trabalho para instalar o Azure Machine Learning Workbench no diretório **%LOCALAPPDATA%** do usuário individual. 

Cada usuário do workbench deve tomar uma ação única. Clique duas vezes no ícone da área de trabalho ```AzureML Workbench Setup``` para instalar a instância do workbench. O Azure Machine Learning também cria e usa um ambiente de Python por usuário, o qual é extraído no diretório **%LOCALAPPDATA%\amlworkbench\python**.

## <a name="more-microsoft-development-tools"></a>Mais ferramentas de desenvolvimento da Microsoft
O [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) é usado para descobrir e baixar outras ferramentas de desenvolvimento da Microsoft. Também há um atalho para a ferramenta fornecida na área de trabalho de Máquina Virtual de Ciência de Dados da Microsoft.  

## <a name="important-directories-on-the-vm"></a>Diretórios importantes na VM
| item | Diretório |
| --- | --- |
| Configurações de servidor do Jupyter Notebook | C:\ProgramData\jupyter |
| Diretório base de amostras do Bloco de Anotações do Jupyter | c:\dsvm\notebooks e c:\users\<username>\notebooks |
| Outras amostras | c:\dsvm\samples |
| Anaconda, padrão: Python 3.6 | c:\Anaconda |
| Ambiente do Anaconda Python 2.7 | c:\Anaconda\envs\python2 |
| Python (Autônomo) da Microsoft Machine Learning Server | C:\Arquivos de Programas\Microsoft\ML Server\PYTHON_SERVER |
| Instância R Padrão, Machine Learning Server (Autônoma) | C:\Arquivos de Programas\Microsoft\ML Server\R_SERVER |
| Diretório da instância no banco de dados do Serviços de Machine Learning do SQL | C:\Arquivos de Programas\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench, por usuário | %localappdata%\amlworkbench | 
| Ferramentas diversas | c:\dsvm\tools |

> [!NOTE]
> Na edição do Windows Server 2012 da DSVM e na edição do Windows Server 2016 antes de março de 2018, o ambiente padrão do Anaconda é Python 2.7. O ambiente secundário é Python 3.5 localizado em **c:\Anaconda\envs\py35**. 
> 
> 

## <a name="next-steps"></a>Próximas etapas

* Explore as ferramentas na VM de ciência de dados, selecionando o menu **Iniciar**.
* Saiba mais sobre os Serviços de Azure Machine Learning e o Workbench visitando a [página de início rápido e os tutoriais](../service/index.yml) do produto. 
* Navegue até **C:\Arquivos de Programas\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** para obter exemplos de como usar a biblioteca RevoScaleR no R, que dá suporte à análise de dados em escala empresarial.  
* Leia o artigo [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](https://aka.ms/dsvmtenthings).
* Saiba como criar soluções completas de análise sistematicamente usando o [Processo de Ciência de Dados de Equipe](../team-data-science-process/index.yml).
* Visite a [Galeria de IA do Azure](http://gallery.cortanaintelligence.com) para obter exemplos de Machine Learning e de análise de dados que usam o Azure Machine Learning e os serviços de dados relacionados no Azure. Também fornecemos um ícone para essa galeria no menu **Iniciar** e na área de trabalho da máquina virtual.

