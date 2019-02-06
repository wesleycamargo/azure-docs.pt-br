---
title: Perguntas frequentes sobre o Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: 'Azure Machine Learning Studio: perguntas frequentes sobre faturamento, funcionalidades e limitações de um serviço de nuvem para modelagem preditiva simplificada.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462272"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Perguntas frequentes do Azure Machine Learning Studio: funcionalidades e limitações
Aqui estão algumas perguntas frequentes e as respostas correspondentes sobre o Azure Machine Learning, um serviço de nuvem para o desenvolvimento de modelos de previsão e soluções de operacionalização por meio de serviços Web. 

## <a name="general-questions"></a>Perguntas gerais
**O que é o Machine Learning Studio?**

O Machine Learning Studio é um ambiente de tela do tipo "arrastar e soltar" que você acessa usando um navegador da Web. O Machine Learning Studio hospeda uma paleta de módulos em uma interface de composição visual que o habilita a criar um fluxo de trabalho de ciência de dados de ponta a ponta na forma de um teste.

Para saber mais sobre o Estúdio do Machine Learning, confira [O que é o Machine Learning Studio](what-is-ml-studio.md)

**O que é a o serviço de API de Machine Learning do Azure?**

O serviço de API do Machine Learning permite implantar modelos preditivos, como os internos do Machine Learning Studio, como serviços Web escalonáveis e tolerantes a falhas. Os serviços Web criados pelo serviço de API do Machine Learning são APIs REST, que fornecem uma interface para comunicação entre aplicativos externos e seu modelo de análise preditiva.

Para saber mais, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

**Onde estão listados os meus serviços Web clássicos? Onde estão listados os meus novos serviços da Web do Azure Resource Manager?**

Serviços Web criados usando o modelo de implantação Clássico e serviços Web criados usando o novo modelo de implantação do Azure Resource Manager são listados no portal de [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/).

Os serviços Web clássicos estão listados no [Machine Learning Studio](http://studio.azureml.net), na guia **Serviços Web**.

## <a name="azure-machine-learning-questions"></a>Perguntas sobre o Azure Machine Learning
**O que são Serviços Web do Azure Machine Learning?**

Os Serviços Web do Machine Learning fornecem uma interface entre um aplicativo e um modelo de pontuação do fluxo de trabalho do Machine Learning. Um aplicativo externo pode usar o Azure Machine Learning para se comunicar com um modelo de pontuação do fluxo de trabalho do Machine Learning em tempo real. Uma chamada a um serviço Web do Machine Learning retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada a um serviço Web, você passa uma chave de API que foi criada quando você implantou o serviço Web. Um serviço Web do Machine Learning baseia-se em REST, uma opção popular de arquitetura para projetos de programação da Web.

O Azure Machine Learning tem dois tipos de serviços Web:

* RSS (Serviço de Solicitação-Resposta): um serviço de baixa latência e altamente escalonável que fornece uma interface para os modelos sem estado criados e implantados usando o Machine Learning Studio.
* BES (Serviço de Execução em Lote): um serviço assíncrono que pontua um lote de registros de dados.

Há várias maneiras para consumir a API REST e acessar o serviço Web. Por exemplo, você pode escrever um aplicativo em C#, R ou Python usando o código de exemplo gerado quando implantou o serviço Web.

O código de exemplo está disponível em:
- A página Consumo do serviço Web no portal de Serviços Web do Azure Machine Learning
- Página de Ajuda da API no painel do serviço Web no Machine Learning Studio

Você também pode usar a pasta de trabalho de exemplo do Microsoft Excel que é criada e está disponível no painel de serviço Web no Machine Learning Studio.

**Quais são as principais atualizações no Azure Machine Learning?**

Para ver as atualizações mais recentes, confira [O que há de novo no Azure Machine Learning](../../active-directory/fundamentals/whats-new.md).

## <a name="import-and-export-data-for-machine-learning"></a>Importar e exportar dados para o Machine Learning
**Para quais fontes de dados o Machine Learning dá suporte?**

Você pode baixar os dados para um experimento do Machine Learning Studio de três maneiras:

- Carregar um arquivo local como um conjunto de dados
- Usar um módulo para importar dados de serviços de dados de nuvem
- Importar um conjunto de dados salvo de outro experimento

Para saber mais sobre formatos de arquivo com suporte, confira [Importar dados de treinamento para o Machine Learning Studio](import-data.md).

### <a id="ModuleLimit"></a>Que tamanho o conjunto de dados para os meus módulos pode ter?
Os módulos do Machine Learning Studio dão suporte a conjuntos de dados com até 10 GB de dados numéricos densos para casos de uso comuns. Se um módulo receber mais de uma entrada, o valor de 10 GB será o total dos tamanhos das entradas. Você também pode conjuntos de dados de exemplo maiores usando consultas do Hive ou de banco de dados Azure SQL ou pode usar o Learning por meio contagens de pré-processamento antes da ingestão.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados maiores durante a normalização de recursos e são limitados a menos de 10 GB:

* Esparsos
* Categóricos
* Cadeias de caracteres
* Dados binários

Os seguintes módulos são limitados a conjuntos de dados com menos de 10 GB:

* Módulos de recomendação
* Módulo SMOTE (Técnica de Sobreamostragem Minoritária Sintética)
* Módulos de script: R, Python, SQL
* Módulos em que o tamanho dos dados de saída pode ser maior que o tamanho dos dados de entrada, como Join ou Feature Hashing
* Validação cruzada, Hiperparâmetros de Modelo de Ajuste, Regressão Ordinal e Classes Múltiplas, um versos todos, quando o número de iterações é muito grande

### <a id="UploadLimit"></a>Quais são os limites para o upload de dados?
Para conjuntos de dados maiores do que alguns GB, carregue dados no Armazenamento do Azure ou no banco de dados Azure SQL ou use o Azure HDInsight em vez de carregar diretamente de um arquivo local.

**Eu posso ler dados da Amazon S3?**

Se tiver uma pequena quantidade de dados e quiser expô-los por meio de uma URL HTTP, você pode usar o módulo [Importar Dados][import-data]. Para uma quantidade maior de dados, transfira-os para o Armazenamento do Azure primeiro e use o módulo [Importar Dados][import-data] para colocá-los em seu teste.
<!--

<SEE CLOUD DS PROCESS>
-->

**Há uma funcionalidade interna de entrada de imagem?**

Você pode aprender sobre a funcionalidade de entrada de imagem na referência [Importar imagens][image-reader].

## <a name="modules"></a>Módulos
**O algoritmo, a fonte de dados, o formato de dados ou a operação de transformação de dados que estou procurando não está no Estúdio do Azure Machine Learning. Quais são minhas opções?**

Você pode acessar o [fórum de comentários dos usuários](https://go.microsoft.com/fwlink/?LinkId=404231) para ver solicitações de recurso que estamos acompanhando. Inclua seu voto em uma solicitação se um recurso que está procurando já foi solicitado. Se o recurso que está procurando não existe, crie uma nova solicitação. É possível exibir o status de sua solicitação neste fórum também. Acompanhamos esta lista de perto e atualizamos o status da disponibilidade do recurso com frequência. Além disso, você pode usar o suporte interno para R e Python para criar transformações personalizadas quando necessário.

**Posso trazer meu código existente para o Machine Learning Studio?**

Sim, é possível levar seus códigos existentes de R ou Python para o Machine Learning Studio, executá-los no mesmo teste com os aprendizes fornecidos pelo Azure Machine Learning e implantar a solução como um serviço Web por meio do Azure Machine Learning. Para obter mais informações, consulte [Estender seu experimento com R](extend-your-experiment-with-r.md) e [Executar scripts Python de aprendizado de máquina no Azure Machine Learning Studio](execute-python-scripts.md).

**É possível usar algo como [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir um modelo?**

Não, não há suporte para PMML (Predictive Model Markup Language). Você pode usar código R e Python personalizado para definir um módulo.

**Quantos módulos posso executar em paralelo em meu experimento?**  

Você pode executar até quatro módulos em paralelo em um experimento.

## <a name="data-processing"></a>Processamento de dados
**Há uma capacidade de visualizar dados (além de visualizações de R) interativamente dentro do experimento?**

Clique na saída de um módulo para visualizar os dados e obter estatísticas.

**Ao visualizar resultados ou dados em um navegador, o número de linhas e colunas é limitado. Por quê?**

Como grandes quantidades de dados podem ser enviadas a um navegador, o tamanho dos dados é limitado para evitar a lentidão no Machine Learning Studio. Para visualizar todos os dados/resultados, é melhor baixar os dados e usar o Excel ou outra ferramenta.

## <a name="algorithms"></a>Algoritmos
**Quais algoritmos existentes têm suporte no Machine Learning Studio?**

O Machine Learning Studio fornece algoritmos de última geração, como Árvores de Decisão Aumentadas Escalonáveis, sistemas de Recomendação Bayesiana, Redes Neurais Profundas e Selvas de Decisão desenvolvidos na Microsoft Research. Pacotes de aprendizado de máquina escalonáveis de software livre, como Vowpal Wabbit, também estão incluídos. O Machine Learning Studio dá suporte a algoritmos de aprendizado de máquina para classificação binária e de múltiplas classes, de regressão e de clustering. Consulte a lista completa de [Módulos do Machine Learning][machine-learning-modules].

**Vocês sugerem automaticamente o algoritmo correto do Machine Learning para usar com meus dados?**

Não, mas o Machine Learning Studio tem várias maneiras de comparar os resultados de cada algoritmo para determinar o que é adequado para o seu problema.

**Vocês têm alguma orientação sobre a seleção de um algoritmo em vez de outro para os algoritmos fornecidos?**

Consulte [Como escolher um algoritmo](algorithm-choice.md).

**Os algoritmos fornecidos são escritos em R ou Python?**

Não, esses algoritmos são escritos, em sua maioria, em linguagens compiladas para fornecer um melhor desempenho.

**São fornecidos detalhes dos algoritmos?**

A documentação fornece algumas informações sobre os algoritmos, e os parâmetros para ajuste são descritos para otimizar o algoritmo para seu uso.  

**Há algum suporte para aprendizado online?**

Não, atualmente há suporte apenas para treinamento programático.

**Posso visualizar as camadas de um Modelo de Rede Neural usando o módulo interno?**

 Não.

**Posso criar meus próprio módulos em C# ou em outra linguagem?**

No momento, você só pode usar R para criar novos módulos personalizados.

## <a name="r-module"></a>Módulo R
**Quais pacotes de R estão disponíveis no Machine Learning Studio?**

O Machine Learning Studio dá suporte a mais de 400 pacotes CRAN R atualmente. Aqui está a [lista atual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos os pacotes incluídos. Consulte também [Estender seu experimento com R](extend-your-experiment-with-r.md) para saber como recuperar essa lista por conta própria. Se o pacote desejado não estiver nessa lista, forneça o nome do pacote no [fórum de comentários do usuário](https://go.microsoft.com/fwlink/?LinkId=404231).

**É possível criar um módulo personalizado em R?**

Sim, consulte [Criar módulos personalizados em R no Azure Machine Learning](custom-r-modules.md) para saber mais.

**Há um ambiente REPL para R?**

Não, não há um ambiente REPL (Read-Eval-Print-Loop) para R no Studio.

## <a name="python-module"></a>Módulo de Python
**É possível criar um módulo personalizado em Python?**

Atualmente, não, mas você pode usar um ou mais módulos [Executar script Python][python] para obter o mesmo resultado.

**Há um ambiente REPL para Python?**

Você pode usar os blocos de notas Jupyter no Machine Learning Studio. Para saber mais, confira [Introdução aos blocos de notas Jupyter no Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Serviço Web

**Como posso readaptar os modelos de Machine Learning de forma programática?**

Use as APIs de novos treinamentos. Para obter mais informações, consulte [Treinar novamente os modelos de Machine Learning de forma programática](retrain-models-programmatically.md). O exemplo de código também está disponível na [Demonstração de readaptação com o Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Posso implantar o modelo localmente ou em um aplicativo que não tem uma conexão com a Internet?**

 Não.

**Há uma latência de linha de base que é esperada para todos os serviços Web?**

Confira [Limites da assinatura do Azure](../../azure-subscription-service-limits.md).

**Quando seria melhor executar meu modelo de previsão como um serviço de Execução em Lotes em vez de um serviço de Resposta da Solicitação?**

O Serviço de Solicitação-Resposta (RRS) é um serviço Web de baixa latência e alta escala usado para fornecer uma interface para modelos sem monitoração de estado que são criados e implantados por meio do ambiente de testes. O BES (Serviço de Execução em Lote) é um serviço que classifica de forma assíncrona um lote de registros de dados. A entrada para BES é como os dados de entrada que RRS usa. A principal diferença é que o BES lê um bloco de registros de uma variedade de fontes, como o armazenamento de Blobs do Azure, o armazenamento de Tabelas do Azure, um banco de dados do SQL Azure, o HDInsight (consulta hive) e fontes HTTP. Para saber mais, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

**Como posso atualizar o modelo para o serviço Web implantado?**

Para atualizar um modelo de previsão para um serviço já implantado, modifique e execute novamente o experimento que você usou para criar e salvar o modelo treinado. Depois que você tiver uma nova versão do modelo adaptado disponível, o Machine Learning Studio perguntará se você deseja atualizar o serviço Web. Para obter detalhes sobre como atualizar um serviço Web implantado, confira [Implantar um serviço Web do Machine Learning](publish-a-machine-learning-web-service.md).

Você também pode usar as APIs de novos treinamentos.
Para obter mais informações, consulte [Treinar novamente os modelos de Machine Learning de forma programática](retrain-models-programmatically.md). O exemplo de código também está disponível na [Demonstração de readaptação com o Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Como posso monitorar meu serviço Web implantado na produção?**

Após implantar um modelo de previsão, você pode monitorá-lo no portal dos Serviços Web do Azure Machine Learning. Cada serviço implantado tem seu próprio painel, onde você pode ver informações de monitoramento do serviço. Para obter mais informações sobre como gerenciar os serviços Web implantados, confira [Gerenciar um serviço Web usando o portal dos Serviços Web do Azure Machine Learning](manage-new-webservice.md) e [Gerenciar um workspace do Azure Machine Learning](manage-workspace.md).

**Existe um lugar onde posso ver a saída do meu RRS/BES?**

Para RRS, a resposta do serviço Web é normalmente onde você vê o resultado. Você também pode escrevê-lo no armazenamento de Blobs do Azure. Para BES, a saída é gravada em um blob, por padrão. Você também pode gravar a saída em um banco de dados ou em uma tabela usando o módulo [Exportar Dados][export-data].

**Posso criar serviços Web apenas de modelos criados no Machine Learning Studio?**

Não, você também pode criar serviços Web diretamente usando blocos de anotações do Jupyter e RStudio.

**Onde posso encontrar informações sobre códigos de erro?**

Confira [Códigos de erro do módulo de Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) para obter uma lista dos códigos de erro e descrições.

**Qual é a escalabilidade do serviço Web?**

Atualmente, o ponto de extremidade padrão é provisionado com 20 solicitações RRS simultâneas por ponto de extremidade. Você pode dimensioná-lo com até 200 solicitações simultâneas por ponto de extremidade e os serviços Web com até 10.000 pontos de extremidade por serviço Web, como descrito em [Dimensionando um serviço Web](scaling-webservice.md). Para BES, cada ponto de extremidade pode processar 40 solicitações por vez. Acima de 40 solicitações, as restantes são enfileiradas. Essas solicitações enfileiradas são executadas automaticamente conforme a fila anda.

**Trabalhos em R são distribuídos entre nós?**

 Não.  

**Quantos dados posso usar para treinamento?**

Os módulos do Machine Learning Studio dão suporte a conjuntos de dados com até 10 GB de dados numéricos densos para casos de uso comuns. Se um módulo tiver mais de uma entrada, o tamanho total de todas as entradas será de 10 GB. Você também pode criar amostras de conjuntos de dados maiores por meio de consultas Hive, por meio de consultas ao Banco de Dados SQL do Azure ou fazer o pré-processamento com módulos [Aprendizado por contagens][counts] antes do uso.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados maiores durante a normalização de recursos e são limitados a menos de 10 GB:

* Esparsos
* Categóricos
* Cadeias de caracteres
* Dados binários

Os seguintes módulos são limitados a conjuntos de dados com menos de 10 GB:

* Módulos de recomendação
* Módulo SMOTE (Técnica de Sobreamostragem Minoritária Sintética)
* Módulos de script: R, Python, SQL
* Módulos em que o tamanho dos dados de saída pode ser maior que o tamanho dos dados de entrada, como Join ou Feature Hashing
* Validação Cruzada, Hiperparâmetros de Modelo de Ajuste, Regressão Ordinal e Classes Múltiplas, um versos todos, quando o número de iterações é muito grande

Para conjuntos com mais de alguns GB, faça upload de dados para o armazenamento do Azure ou Banco de Dados Azure SQL ou use o HDInsight, em vez de fazer upload diretamente de um arquivo local.

**Há qualquer limitação de tamanho de vetores?**

Linhas e colunas têm a limitação do .NET de Int Máximo: 2,147,483,647.

**Posso ajustar o tamanho da máquina virtual que executa o serviço Web?**

 Não.  

## <a name="security-and-availability"></a>Segurança e disponibilidade
**Quem pode acessar o ponto de extremidade http para o serviço Web por padrão? Como faço para restringir o acesso ao ponto de extremidade?**

Depois que um serviço Web for implantado, criaremos um ponto de extremidade padrão para esse serviço. O ponto de extremidade padrão pode ser chamado usando sua chave de API. Você pode adicionar mais pontos de extremidade com suas próprias chaves no portal de Serviços Web ou de forma programática, usando as APIs de Gerenciamento de Serviço Web. São necessárias chaves de acesso para fazer chamadas ao serviço Web. Para saber mais, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

**O que acontece se minha conta de armazenamento do Azure não puder ser encontrada?**

O Machine Learning Studio depende de uma conta de armazenamento do Azure fornecida pelo usuário para salvar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio quando um workspace é criado. Após o workspace ser criado, se a conta de armazenamento for excluída e não puder mais ser encontrada, o workspace deixará de funcionar e todos os testes nele falharão.

Se você excluir acidentalmente a conta de armazenamento, recrie essa conta de armazenamento exatamente com o mesmo nome e exatamente na mesma região que a conta de armazenamento excluída. Depois disso, sincronize novamente a chave de acesso.

**O que acontecerá se a chave de acesso da minha conta de armazenamento não estiver sincronizada?**

O Machine Learning Studio depende de uma conta de armazenamento do Azure fornecida pelo usuário para armazenar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio quando um workspace é criado e as chaves de acesso são associadas a ele. Se as chaves de acesso forem alteradas, depois que o workspace for criado, ele não poderá mais acessar a conta de armazenamento. Ele deixará de funcionar e todos os testes no workspace falharão.

Se tiver alterado as chaves de acesso da conta de armazenamento, sincronize novamente as chaves de acesso na configuração do workspace usando o portal do Azure.  


## <a name="billing-questions"></a>Perguntas sobre cobrança

Para saber mais sobre preços e cobrança, confira [Preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
