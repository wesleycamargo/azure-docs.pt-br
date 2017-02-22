---
title: "O que é uma Máquina Virtual de Ciência de Dados? | Microsoft Docs"
description: "Aprenda sobre os principais cenários, recursos e como começar a usar as Máquinas Virtuais de Ciência de Dados, um ambiente e kit de ferramentas pronto para análise."
keywords: "ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 6c664a055e577881d4fcccd5b0ba4047d88aa9ef
ms.openlocfilehash: 64b413451c6ce47cc3aa14322b2aa0342e1e3ffe


---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introdução à Máquina Virtual de Ciência de Dados baseada em nuvem para Linux e Windows
A Máquina Virtual de Ciência de Dados é uma imagem de VM personalizada na nuvem do Microsoft Azure, especificamente criada para ciência de dados. Ela tem muitas ferramentas conhecidas de ciência de dados, entre outras, pré-instaladas e pré-configuradas que ajudam a começar a criar rapidamente aplicativos inteligentes para análise avançada. Ela está disponível no Windows Server 2012 ou nas versões Linux baseadas no OpenLogic 7.2 CentOS. 

Este tópico explica o que você pode fazer com a VM de Ciência de Dados, descreve alguns dos principais cenários de uso da VM, lista os principais recursos nas versões Windows e Linux, além de fornecer instruções sobre como começar a usá-los.

## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>O que é possível fazer com a Máquina Virtual de Ciência de Dados?
O objetivo da Máquina Virtual de Ciência de Dados é fornecer aos profissionais de dados em todos os níveis de habilidade e funções um ambiente descomplicado de ciência de dados. Com essa VM, você economiza tempo considerável que gastaria se tivesse distribuído um ambiente comparável por conta própria. Em vez disso, inicie seu projeto de ciência de dados imediatamente em uma instância de VM recentemente criada. 

A VM de Ciência de Dados foi desenvolvida e configurada para trabalhar com amplos cenários de uso. Você pode reduzir ou aumentar o ambiente de acordo com as necessidades do seu projeto. É possível usar seu idioma preferido para programar as tarefas de ciência de dados. Você pode instalar outras ferramentas e personalizar o sistema para suas necessidades exatas.

## <a name="key-scenarios"></a>Principais cenários
Esta seção sugere alguns cenários importantes para os quais a VM de Ciência de Dados pode ser implantada.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Área de trabalho de análise pré-configurada na nuvem
A VM de Ciência de Dados fornece uma configuração de linha de base para equipes de ciência de dados que buscam substituir as respectivas áreas de trabalho locais por uma área de trabalho de nuvem gerenciada. Essa linha de base garante que todos os cientistas de dados em uma equipe tenham uma configuração consistente com a qual podem verificar experiências e promover colaboração. Ela também diminui os custos reduzindo a carga de administração de sistema e poupando o tempo necessário para avaliar, instalar e manter os vários pacotes de software necessários à análise avançada.  

### <a name="data-science-training-and-education"></a>Educação e treinamento de ciência de dados
Os treinadores e educadores corporativos que ensinam sobre ciência de dados geralmente fornecem uma imagem de máquina virtual para garantir que seus alunos tenham uma configuração consistente e que as amostras funcionem de maneira previsível. A VM de Ciência de Dados cria um ambiente sob demanda com uma configuração consistente que facilita o suporte e os desafios de incompatibilidade. Nos casos em que esses ambientes precisam ser criados com frequência, especialmente para aulas rápidas de treinamento, os alunos são consideravelmente beneficiados.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade elástica sob demanda para projetos em larga escala
As maratonas/competições de ciência de dados ou modelagem e exploração de dados em larga escala exigem que a capacidade de hardware seja escalada horizontalmente, geralmente por um curto período. A VM de Ciência de Dados pode ajudar a replicar o ambiente de ciência de dados rapidamente em servidores escalados horizontalmente sob demanda, o que permite a realização de testes que exigem a execução de recursos de computação altamente potentes.

### <a name="short-term-experimentation-and-evaluation"></a>Avaliação e experimento de curto prazo
A VM de Ciência de Dados pode ser usada para avaliar ferramentas, ou aprender sobre elas, como o Microsoft R Server, SQL Server, Visual Studio, Jupyter, kits de ferramentas de aprendizado de máquina/aprendizado profundo, além de novas ferramentas conhecidas na comunidade com mínimo esforço de configuração. Uma vez que a VM de Ciência de Dados pode ser configurada rapidamente, ela pode ser aplicada em outros cenários de uso de curto prazo, como na replicação de testes publicados, na execução de demonstrações, depois de passo a passos em sessões online ou em tutoriais de conferência.

## <a name="whats-included-in-the-data-science-vm"></a>O que está incluído na VM de Ciência de Dados?
A Máquina Virtual de Ciência de Dados apresenta muitas ferramentas conhecidas de ciência de dados já instaladas e configuradas. Ela também inclui ferramentas que facilitam o trabalho com vários produtos de análise e dados do Azure. Você pode explorar e criar modelos preditivos em conjuntos de dados de larga escala usando o Microsoft R Server ou SQL Server 2016. Também está incluído um conjunto de outras ferramentas da comunidade de software livre e da Microsoft, bem como código de exemplo e notebooks. A tabela a seguir relaciona e compara os principais componentes incluídos nas edições do Windows e Linux da Máquina Virtual de Ciência de Dados.

| **Edição do Windows** | **Edição do Linux** |
| -- | --|
| Microsoft R Open com pacotes populares pré-instalados | Microsoft R Open com pacotes populares pré-instalados |
| Microsoft R Server Developer Edition com algoritmos do MicrosoftML e do Microsoft R Operationalization  |Microsoft R Server Developer Edition |
| Anaconda Python 2.7, 3.5 |Anaconda Python 2.7, 3.5 com pacotes populares pré-instalados|
| JuliaPro com pacotes populares pré-instalados | JuliaPro com pacotes populares pré-instalados |
| Servidor do Jupyter Notebook (R, Python, Julia) |JupyterHub: notebooks para multiusuários do Jupyter (R, Python, Julia, PySpark) |
| SQL Server 2016 SP1 Developer Edition: análise no banco de dados escalonável com os serviços de R |PostgreSQL, SQuirreL SQL (ferramenta de banco de dados), drivers do SQL Server e linha de comando (bcp, sqlcmd) |
|- Visual Studio Community Edition 2015 (IDE) </br> - Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools </br> - Node.js, Python e Ferramentas do R para Visual Studio (RTVS 0.5) </br>- R Studio Desktop|IDEs e editores </br> - Eclipse com plug-in do kit de ferramentas do Azure </br> - Emacs (com ESS, auctex) gedit </br> - IntelliJ IDEA</br> - PyCharm</br> - Atom</br> - Visual Studio Code|
| Power BI Desktop |-- |
| Ferramentas de Aprendizado de Máquina </br> - Integração ao Machine Learning do Azure </br> - Microsoft Cognitive Toolkit (CNTK 2.0) aprendizado profundo/AI </br> - Xgboost (ferramenta conhecida de aprendizado de máquina em competições de ciência de dados) </br> - Vowpal Wabbit (aprendiz online rápido) </br> - Rattle (ferramenta visual de dados e análise de início rápido) </br> - Mxnet (aprendizado profundo/AI) </br> - Tensorflow  |Ferramentas de Aprendizado de Máquina </br> - Integrações ao Machine Learning do Azure </br> - CNTK (aprendizado profundo/AI) </br> - Xgboost (ferramenta conhecida de aprendizado de máquina em competições de ciência de dados) </br> - Vowpal Wabbit (aprendiz online rápido) </br> - Rattle (ferramenta visual de dados e análise de início rápido) </br> - Mxnet (aprendizado profundo/AI)|
| SDKs para acessar o Azure e o pacote de serviços Cortana Intelligence |SDKs para acessar o Azure e o pacote de serviços Cortana Intelligence |
| Ferramentas de movimentação e gerenciamento de dados dos recursos de Big Data e do Azure: Gerenciador de Armazenamento do Azure, CLI, PowerShell, AdlCopy (Azure Data Lake), AzCopy, dtui (para DocumentDB), Gateway de Gerenciamento de Dados da Microsoft |Ferramentas de movimentação e gerenciamento de dados dos recursos de Big Data e do Azure: Gerenciador de Armazenamento do Azure, CLI |
| Git, plug-in do Visual Studio Team Services |Git |
| Porta do Windows dos utilitários de linha de comando mais conhecidos do Linux/Unix acessível por meio de GitBash/prompt de comando |-- |
| Weka | Weka |
| Drill | Drill |
| --| Local do Spark |

## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Como começar a usar a VM de Ciência de Dados do Windows
* Crie uma instância da VM no Windows acessando [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) e selecionando o botão verde **Criar Máquina Virtual**.
* Entre na VM da sua área de trabalho remota usando as credenciais que você especificou quando criou a VM.
* Para descobrir e iniciar as ferramentas disponíveis, clique no menu **Iniciar**.

## <a name="get-started-with-the-linux-data-science-vm"></a>Começar a usar a VM de Ciência de Dados do Linux
* Crie uma instância da VM no Linux (com base no OpenLogic CentOS) acessando [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) e selecionando o botão **Criar Máquina Virtual**.
* Entre na VM de um cliente SSH, como Putty ou Comando SSH, usando as credenciais que você especificou quando criou a VM.
* No prompt do shell, insira dsvm-more-info.
* Para obter uma área de trabalho gráfica, baixe [aqui](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) o cliente X2Go para sua plataforma e siga as instruções no documento da VM de Ciência de Dados para Linux [Provisionar a Máquina Virtual de Ciência de Dados Linux](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Próximas etapas
### <a name="for-the-windows-data-science-vm"></a>Para a VM de Ciência de Dados do Windows
* Para saber mais sobre como executar ferramentas específicas disponíveis na versão do Windows, confira [Provisionar uma Máquina Virtual de Ciência de Dados da Microsoft](machine-learning-data-science-provision-vm.md) e
* Para saber mais sobre como executar várias tarefas necessárias para o seu projeto de ciência de dados na VM do Windows, confira [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Para a VM de Ciência de Dados do Linux
* Para saber mais sobre como executar ferramentas específicas disponíveis na versão do Linux, confira [Provisionar a Máquina Virtual de Ciência de Dados Linux](machine-learning-data-science-linux-dsvm-intro.md).
* Para obter um passo a passo que mostre como executar várias tarefas comuns de ciência de dados com o VM Linux, confira [Ciência de dados na Máquina Virtual da Ciência de Dados do Linux](machine-learning-data-science-linux-dsvm-walkthrough.md).




<!--HONumber=Feb17_HO1-->


