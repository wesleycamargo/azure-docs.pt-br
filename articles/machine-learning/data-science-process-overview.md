---
title: "Ciclo de vida do Processo de Ciência de Dados de Equipe | Microsoft Docs"
description: "Componentes e etapas de ciclo de vida para estruturar seus projetos de ciência de dados."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: bradsev;hangzh;gokuma
translationtype: Human Translation
ms.sourcegitcommit: 1796f7a7cd174d7ed6582878d72c59995aac41cb
ms.openlocfilehash: 995ba0dc3ffd2bc78625db7d1176ca0d5e1611a0
ms.lasthandoff: 02/08/2017


---
# <a name="team-data-science-process-lifecycle"></a>Ciclo de vida do Processo de Ciência de Dados de Equipe
O TDSP (Processo de Ciência de Dados de Equipe) fornece um ciclo de vida recomendado que você pode usar para estruturar o desenvolvimento de seus projetos de ciência de dados. O ciclo de vida descreve as etapas, do início ao fim, que os projetos normalmente seguem quando são executados. Se você estiver usando outro ciclo de vida de ciência de dados, como [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) ou o próprio processo personalizado de sua organização, você ainda poderá usar o TDSP baseado em tarefas no contexto desses ciclos de vida de desenvolvimento. 

Esse ciclo de vida foi projetado para projetos de ciência de dados que devem ser fornecidos como parte de aplicativos inteligentes. Esses aplicativos implantam modelos de aprendizado de máquina ou de inteligência artificial para análise preditiva. Projetos de ciência de dados exploratórios e projetos de análise ad hoc ou avulsos também podem se beneficiar do uso desse processo, mas nesses casos, algumas etapas descritas podem não ser necessárias.    

Esta é uma representação visual do **ciclo de vida do Processo de Ciência de Dados de Equipe**. 

![Ciclo de vida do TDSP](./media/data-science-process-overview/tdsp-lifecycle.png) 

O ciclo de vida do TDSP é composto por cinco estágios principais executados de forma iterativa. Estão incluídos:

* **Noções básicas sobre negócios**
* **Aquisição de dados e reconhecimento**
* **Modelagem**
* **Implantação**
* **Aceitação do cliente**

Para cada estágio, fornecemos as seguintes informações:

* **Metas**: os objetivos específicos discriminados.
* **Como fazer isso**: as tarefas específicas descritas e as diretrizes fornecidas para concluí-las.
* **Artefatos**: as entregas e o suporte para produzi-las.

## <a name="1-business-understanding"></a>1. Noções básicas sobre negócios
### <a name="goals"></a>Metas
* As **principais variáveis** são especificadas, que devem servir como **metas modelo**, e cujas métricas relacionadas são usadas para determinar o sucesso do projeto.
* As **fontes de dados** relevantes são identificadas, às quais a empresa tem acesso, e/ou de outras fontes, quando necessário.

### <a name="how-to-do-it"></a>Como fazer isso
Há duas tarefas principais abordadas neste estágio: 

* **Definir os objetivos**: trabalhe com o cliente e outros stakeholders para compreender e identificar problemas de negócios. Formule perguntas que definem as metas de negócios e que as técnicas de ciência de dados podem ter como meta.
* **Identificar as fontes de dados**: encontre os dados relevantes que ajudam a responder as perguntas que definem os objetivos do projeto.

#### <a name="11-define-objectives"></a>1.1 Definir os objetivos
1. Um objetivo central desta etapa é identificar as principais **variáveis de negócios** que a análise precisa prever. Essas variáveis são denominadas as **metas modelo** e as métricas associadas a elas são usadas para determinar o sucesso do projeto. Previsão de vendas ou a probabilidade de que um pedido seja fraudulento são exemplos dessas metas.
2. Defina as **metas do projeto**, fazendo perguntas e refinando perguntas “inteligentes” que são relevantes, específicas e inequívocas. A ciência de dados é o processo de usar nomes e números para responder a essas perguntas. “*Ao escolher sua pergunta, imagine que você está se aproximando de um oráculo que pode lhe dizer qualquer coisa que existe no universo, desde que a resposta seja um número ou um nome*”. Para obter outras diretrizes, consulte a seção **Ask a Sharp Question** (Faça uma pergunta inteligente) do blog [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) (Como realizar a ciência de dados).   A ciência de dados e o aprendizado de máquina são normalmente usados para responder a cinco tipos de perguntas:
   * Quanto custa ou quantos? (regressão)
   * Qual categoria? (classificação)
   * Qual grupo? (clustering)
   * Isso é estranho? (detecção de anomalias)
   * Qual opção deve ser escolhida? (recomendação)
3. Defina a **equipe do projeto** especificando as funções e responsabilidades dos membros. Desenvolva um plano de marcos de alto nível que pode ser usado para iteração, conforme mais informações são descobertas.  
4. **Defina as métricas de sucesso**. Por exemplo: obter a precisão da previsão de rotatividade de clientes de X% até o final deste projeto de 3 meses, para que possamos oferecer promoções para reduzir a rotatividade. As métricas devem ser **SMART** (INTELIGENTES): 
   * E**S**pecíficas 
   * **M**ensuráveis
   * **A**lcançáveis 
   * **R**elevantes 
   * Com limite de **T**empo 

#### <a name="12-identify-data-sources"></a>1.2 Identificar as fontes de dados
Identifique as fontes de dados que contêm exemplos conhecidos de respostas para as perguntas inteligentes. Procure os seguintes dados:

* Dados que são **Relevantes** à pergunta. Temos medidas da meta e recursos relacionados a ela?
* Dados que são uma **Medida precisa** de nossa meta modelo e os recursos de interesse.

Por exemplo, não é incomum descobrir que os sistemas existentes precisam coletar e registrar tipos de dados adicionais para solucionarem o problema e atingirem os objetivos do projeto. Nesse caso, talvez você queira procurar fontes de dados externas ou atualizar seus sistemas para coletar dados mais novos.

### <a name="artifacts"></a>Artefatos
Estas são as entregas deste estágio:

* [**Documento de estatuto**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): um modelo padrão é fornecido na definição de estrutura do projeto TDSP. Este é um documento dinâmico atualizado ao longo do projeto, conforme novas descobertas são feitas e os requisitos de negócios mudam. O segredo é a iteração neste documento, com a adição de mais detalhes, à medida que você avança no processo de descoberta. Mantenha o cliente e outros stakeholders envolvidos no processo de mudança e comunique os motivos das mudanças de forma clara.  
* [**Fontes de dados**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): essa é parte do Relatório de Dados encontrada na estrutura do projeto TDSP. Descreve as fontes dos dados brutos. Em estágios posteriores, você preenche outros detalhes como scripts para mover os dados para seu ambiente de análise.  
* [**Dados**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): este documento fornece as descrições e o esquema (tipos de dados, informações sobre regras de validação, se houver) para os dados usados para responder à pergunta. Os diagramas ou as descrições de relação de entidades são incluídas, se disponíveis.

## <a name="2-data-acquisition-and-understanding"></a>2. Aquisição de dados e reconhecimento
### <a name="goals"></a>Metas
* Um conjunto de dados limpo e de alta qualidade, cujas relações com as variáveis da meta são de fácil compreensão, está localizado no ambiente de análise, pronto para o modelo.
* Foi desenvolvida uma arquitetura da solução do pipeline de dados para atualizar e pontuar os dados regularmente.

### <a name="how-to-do-it"></a>Como fazer isso
Há três tarefas principais abordadas neste estágio:

* **Ingestão dos dados** no ambiente de análise da meta.
* **Exploração dos dados** para determinar se a qualidade dos dados é adequada para responder à pergunta. 
* **Configuração de um pipeline de dados** para pontuar dados novos ou atualizados regularmente.

#### <a name="21-ingest-the-data"></a>2.1 Ingestão dos dados
Configure o processo para mover os dados dos locais de origem para os locais de destino em que as operações de análise como treinamento e previsões deverão ser executadas. Para obter detalhes técnicos e opções sobre como fazer isso com vários serviços de dados do Azure, consulte [Carregar dados em ambientes de armazenamento para análise](machine-learning-data-science-ingest-data.md). 

#### <a name="22-explore-the-data"></a>2.2 Exploração dos dados
Antes de treinar seus modelos, você precisa desenvolver uma compreensão total dos dados. Em geral, conjuntos de dados do mundo real apresentam ruído, são valores ausentes ou têm uma série de outras discrepâncias. O resumo e a visualização de dados podem ser usados para auditar a qualidade dos dados e fornecer as informações necessárias para processá-los antes que estejam prontos para modelagem. Para obter diretrizes sobre como limpar os dados, consulte [Tarefas para preparar dados para o aprendizado de máquina avançado](machine-learning-data-science-prepare-data.md). Esse processo costuma ser iterativo. 

O TDSP fornece um utilitário automatizado chamado [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) para ajudar a visualizar os dados e preparar relatórios de resumo de dados. Recomendamos começar com o IDEAR primeiro para explorar os dados e ajudar a desenvolver uma compreensão inicial dos dados de forma interativa e sem nenhuma codificação e, em seguida, escrever código personalizado para exploração de dados e visualização. 

Quando estiver satisfeito com a qualidade dos dados limpos, a próxima etapa é entender melhor os padrões inerentes aos dados, que vão ajudá-lo a escolher e desenvolver um modelo preditivo apropriado para sua meta. Procure provas para descobrir se os dados estão bem conectados à meta e se há dados suficientes para continuar com as próximas etapas de modelagem. Novamente, esse processo costuma ser iterativo. Talvez você precise encontrar novas fontes de dados com dados mais precisos ou mais relevantes para aumentar o conjunto de dados inicialmente identificado no estágio anterior.  

#### <a name="23-set-up-a-data-pipeline"></a>2.3 Configuração de um pipeline de dados
Além da ingestão inicial e limpeza dos dados, normalmente, você precisa configurar um processo para pontuar novos dados ou atualizar os dados regularmente como parte de um processo de aprendizado contínuo. Faça isso configurando um pipeline de dados ou um fluxo de trabalho. Este é um [exemplo](machine-learning-data-science-move-sql-azure-adf.md) de como configurar um pipeline com o [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). Uma arquitetura da solução do pipeline de dados é desenvolvida neste estágio. O pipeline também é desenvolvido em paralelo com os próximos estágios do projeto de ciência de dados. O pipeline pode ser baseado em lote, de transmissão/tempo real ou híbrido, dependendo de suas necessidades de negócios e das restrições dos sistemas existentes nos quais essa solução está sendo integrada. 

### <a name="artifacts"></a>Artefatos
Veja a seguir as entregas deste estágio.

* [**Relatório de qualidade dos dados**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): este relatório contém resumos de dados, relações entre cada atributo e meta, classificação de variáveis, etc. A ferramenta [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), fornecida como parte do TDSP, pode gerar esse relatório rapidamente em qualquer conjunto de dados de tabela, como um arquivo CSV ou uma tabela relacional. 
* **Arquitetura da solução**: isso pode ser um diagrama ou uma descrição do pipeline de dados usado para executar a pontuação ou as previsões em relação aos novos dados, após a criação de um modelo. Também contém o pipeline para treinar novamente seu modelo com base nos novos dados. O documento é armazenado no [diretório](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando o modelo da estrutura de diretório do TDSP é usado.
* **Decisão de ponto de verificação**: antes de começar a engenharia de recursos completos e a criação do modelo, reavalie o projeto para determinar se há valor suficiente esperado para continuar investindo nele. Você pode, por exemplo, estar pronto para continuar, precisar coletar mais dados, ou abandonar o projeto, pois não existem dados para responder à pergunta.

## <a name="3-modeling"></a>3. Modelagem
### <a name="goals"></a>Metas
* Recursos de dados ideais para o modelo de aprendizado de máquina.
* Um modelo de ML informativo que prevê a meta com mais precisão.
* Um modelo de ML adequado para a produção.

### <a name="how-to-do-it"></a>Como fazer isso
Há três tarefas principais abordadas neste estágio:

* **Engenharia de recursos**: crie recursos de dados dos dados brutos para facilitar o treinamento do modelo.
* **Treinamento do modelo**: encontre o modelo que responde à pergunta com mais precisão, comparando suas métricas de sucesso.
* Determine se o modelo é **adequado para produção**.

#### <a name="31-feature-engineering"></a>3.1 Engenharia de recursos
A engenharia de recursos envolve a inclusão, agregação e transformação de variáveis brutas para criar os recursos usados na análise. Se você desejar obter análises sobre o que está guiando um modelo, você precisa entender como os recursos estão relacionados entre si e como os algoritmos de aprendizado de máquina devem ser usar esses recursos. Esta etapa exige uma combinação criativa das competências de domínio e análises obtidas na etapa de exploração de dados. Esse é um equilíbrio entre encontrar e incluir variáveis informativas, evitando o excesso de variáveis não relacionadas. Variáveis informativas melhoram nossos resultados; variáveis não relacionadas introduzem ruído desnecessário no modelo. Você também precisa gerar esses recursos para os novos dados obtidos durante a pontuação. Portanto, a geração desses recursos pode depender apenas dos dados que estão disponíveis no momento da pontuação. Para obter diretrizes técnicas sobre a engenharia de recursos ao usar várias tecnologias de dados do Azure, consulte [Engenharia de recursos no Processo de Ciência de Dados](machine-learning-data-science-create-features.md). 

#### <a name="32-model-training"></a>3.2 Treinamento do modelo
Dependendo do tipo da pergunta que você está tentando responder, há vários algoritmos de modelagem disponíveis. Para obter diretrizes sobre como escolher algoritmos, consulte [Como escolher algoritmos para o Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md). Embora este artigo tenha sido escrito para o Azure Machine Learning, as diretrizes fornecidas aqui são úteis para outras estruturas de ML. 

O processo de treinamento do modelo inclui as seguintes etapas: 

* Divisão dos dados de entrada aleatoriamente para modelagem em um conjunto de dados de treinamento e um conjunto de dados de teste.
* Crie os modelos usando o conjunto de dados de treinamento.
* Avaliação (conjunto de dados de treinamento e de teste) de uma série de algoritmos de aprendizado de máquina concorrentes, juntamente com os vários parâmetros de ajustes associados (conhecidos como limpeza de parâmetros), voltados para responder à pergunta de interesse com os dados atuais.
* Determine a “melhor” solução para responder à pergunta, comparando a métrica de sucesso entre os métodos alternativos.

> [!NOTE]
> **Prevenção contra perda**: a perda de dados pode ser causada pela inclusão de dados que estão fora do conjunto de dados de treinamento, que permite a um modelo ou ao algoritmo de aprendizado de máquina fazer previsões boas de forma irrealista. A perda é um motivo comum pelo qual os cientistas de dados ficam preocupados quando obtêm resultados preditivos que parecem bons demais para serem verdadeiros. Essas dependências podem ser difíceis de serem detectadas. Para evitar isso, geralmente é necessária a iteração entre a criação de um conjunto de dados de análise, criação de um modelo e avaliação da precisão. 
> 
> 

Fornecemos uma [Ferramenta automatizada de Modelagem e Relatório](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) com o TDSP, que pode ser executada por meio de vários algoritmos e limpezas de parâmetros para produzir um modelo de linha de base. Ela também produz um relatório de modelagem de linha de base, que fornece um resumo do desempenho de cada combinação de modelo e parâmetro, incluindo a importância da variável. Esse processo também é iterativo, pois pode gerar mais engenharia de recursos. 

### <a name="artifacts"></a>Artefatos
Os artefatos produzidos neste estágio incluem:

* [**Conjuntos de recursos**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): os recursos desenvolvidos para a modelagem são descritos na seção Conjunto de recursos do relatório Definição de dados. Contém ponteiros para o código para gerar os recursos e a descrição de como o recurso foi gerado.
* [**Relatório de modelagem**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): para cada modelo testado, é produzido um relatório padrão que segue um modelo do TDSP especificado.
* **Decisão de ponto de verificação**: avalie se o modelo tem um bom desempenho, suficiente para implantá-lo em um sistema de produção. Algumas perguntas importantes a serem feitas são:
  * O modelo responde à pergunta com confiança suficiente, considerando os dados de teste? 
  * Experimente abordagens alternativas: coletar dados adicionais, realizar mais engenharia de recursos ou fazer experimentos com outros algoritmos?

## <a name="4-deployment"></a>4. Implantação
### <a name="goal"></a>Objetivo
* Os modelos e o pipeline são implantados em um ambiente de produção ou semelhante à produção para aceitação do usuário final. 

### <a name="how-to-do-it"></a>Como fazer isso
A principal tarefa abordada neste estágio:

* **Operacionalizar o modelo**: implante o modelo e o pipeline em um ambiente de produção ou semelhante à produção para consumo do aplicativo.

#### <a name="41-operationalize-a-model"></a>4.1 Operacionalizar um modelo
Assim que você tiver um conjunto de modelos com um bom desempenho, eles poderão ser operacionalizados para o consumo de outros aplicativos. Dependendo dos requisitos de negócios, as previsões são feitas em tempo real ou em lotes. Para serem operacionalizados, os modelos precisam ser expostos com uma interface de API aberta, que é facilmente consumida de vários aplicativos, como sites, planilhas, painéis online, ou aplicativos de linha de negócios ou de back-end. Para obter exemplos de operacionalização de modelos com um serviço Web do Azure Machine Learning, consulte [Implantar um serviço Web do Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md). Também é uma boa ideia criar a telemetria e o monitoramento no modelo de produção e no pipeline de dados implantado, para ajudar com o relatório de status do sistema e a solução de problemas.  

### <a name="artifacts"></a>Artefatos
* Painel de status de principais métricas e integridade do sistema.
* Relatório de modelagem final com detalhes da implantação.
* Documento da arquitetura da solução final.

## <a name="5-customer-acceptance"></a>5. Aceitação do cliente
### <a name="goal"></a>Objetivo
* **Finalizar as entregas do projeto**: confirme se o pipeline, o modelo e a implantação em um ambiente de produção atendem aos objetivos do cliente.

### <a name="how-to-do-it"></a>Como fazer isso
Há três tarefas principais abordadas neste estágio:

* **Validação do sistema**: confirme se o modelo implantado e o pipeline atendem às necessidades do cliente.
* **Entrega do projeto**: para a entidade que executará o sistema na produção.

O cliente validará se o sistema atende às suas necessidades de negócios e responde às perguntas com uma precisão aceitável para implantar o sistema na produção para uso do aplicativo cliente. Toda a documentação é finalizada e revisada. Uma entrega do projeto para a entidade responsável pelas operações é concluída. Isso poderá ser, por exemplo, uma equipe de TI ou de ciência de dados do cliente ou um agente do cliente responsável por executar o sistema na produção. 

### <a name="artifacts"></a>Artefatos
O principal artefato produzido nesse estágio final é o [**Relatório final do projeto**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md). Este é o relatório técnico do projeto que contém todos os detalhes do projeto que são úteis para conhecer e operar o sistema. Um [modelo](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) é fornecido pelo TDSP, que pode ser usado no estado em que se encontra ou personalizado, para as necessidades específicas do cliente. 

## <a name="summary"></a>Resumo
O [ciclo de vida do Processo de Ciência de Dados de Equipe](http://aka.ms/datascienceprocess) é modelado como uma sequência de etapas iteradas que fornecem diretrizes sobre as tarefas necessárias para usar os modelos preditivos. Esses modelos podem ser implantados em um ambiente de produção para serem utilizados para criar aplicativos inteligentes. O objetivo deste ciclo de vida do processo é dar continuidade a um projeto de ciência de dados em direção a um ponto final claro de engajamento. Embora seja verdade que a ciência de dados é um exercício em pesquisas e descobertas, a capacidade de comunicar isso com clareza para sua equipe e seus clientes usando um conjunto bem definido de artefatos que utiliza modelos padronizados pode ajudar a evitar divergências e aumentar a probabilidade de uma conclusão bem-sucedida de um projeto complexo de ciência de dados.

## <a name="next-steps"></a>Próximas etapas
Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos** . Eles serão listados e vinculados com descrições em miniatura no tópico [Passo a passo do Processo de Ciência de Dados de Equipe](data-science-process-walkthroughs.md).


