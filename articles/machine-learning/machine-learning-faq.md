<properties
	pageTitle="Perguntas Frequentes sobre o Aprendizado de Máquina do Azure | Microsoft Azure"
	description="Introdução ao Aprendizado de Máquina do Azure: perguntas frequentes sobre cobrança, recursos e limitações de um serviço de nuvem para modelagem preditiva simplificada."
	keywords="introdução ao aprendizado de máquina, modelagem preditiva, o que é aprendizado de máquina"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/14/2016"
	ms.author="garye"/>

# Perguntas frequentes (FAQ) sobre o Aprendizado de Máquina do Azure: cobrança, suporte, recursos e limitações

Essas perguntas frequentes são perguntas sobre o Aprendizado de Máquina do Azure, um serviço de nuvem para desenvolver modelos preditivos e soluções de operacionalização através de serviços Web. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte.

## Perguntas gerais

**O que é Aprendizado de Máquina do Azure?**

O Aprendizado de Máquina do Azure é um serviço totalmente gerenciado que você pode usar para criar, testar, operar e gerenciar soluções analíticas preditivas na nuvem. Com apenas um navegador, agora você pode entrar, fazer upload de dados e iniciar imediatamente experimentos de aprendizado de máquina. Modelagem de previsão do tipo "arrastar e soltar", um grande palete de módulos e uma biblioteca de modelos de início tornam as tarefas comuns de Aprendizado de Máquina algo rápido e simples. Para saber mais, consulte [Visão geral do serviço de Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/). Para obter uma introdução sobre o Aprendizado de Máquina que aborda conceitos e terminologia principal, consulte [Introdução ao Aprendizado de Máquina do Azure](machine-learning-what-is-machine-learning.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**O que é o Estúdio de Aprendizado de Máquina?**

O Estúdio de Aprendizado de Máquina é um ambiente de bancada de trabalho que você acessa por meio de um navegador da Web. O Estúdio de Aprendizado de Máquina hospeda uma paleta de módulos com uma interface de composição visual que permite criar um fluxo de trabalho de ciência de dados de ponta a ponta na forma de um teste.

Para saber mais sobre o Estúdio do Aprendizado de Máquina, confira [O que é o Estúdio de Aprendizado de Máquina](machine-learning-what-is-ml-studio.md)

**O que é a o serviço de API de Aprendizado de Máquina do Azure?**

O serviço de API do Aprendizado de Máquina permite implantar modelos preditivos, por exemplo, os criados no Estúdio de Aprendizado de Máquina, como serviços Web escalonáveis e tolerantes a falhas. Os serviços Web criados pelo serviço de API do Aprendizado de Máquina são APIs REST, que fornecem uma interface para comunicação entre aplicativos externos e seu modelo analítico preditivo.

Para saber mais, consulte [Conectar a um serviço Web do Aprendizado de Máquina](machine-learning-connect-to-azure-machine-learning-web-service.md).

**Onde estão listados os meus serviços Web clássicos? Onde estão listados os meus novos serviços da Web do Azure Resource Manager?**

Os serviços Web clássicos estão listados no [Estúdio de Aprendizado de Máquina](http://studio.azureml.net) na guia Serviços Web. Novos serviços Web baseados em Azure Resource Manager são listados no portal [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/). Não há listagem cruzada disponível.

## Perguntas sobre o serviço Web de Aprendizado de Máquina do Microsoft Azure

**O que são os Serviços Web de AM do Azure?**

Com o serviço Web de Aprendizado de Máquina do Azure, um aplicativo externo se comunica com um modelo de pontuação do fluxo de trabalho de Aprendizado de Máquina em tempo real. Uma chamada do serviço Web de Aprendizado de Máquina retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada de serviço Web do Machine Learning, transmita uma chave de API que foi criada quando você implantou o serviço Web. O serviço Web de Aprendizado de Máquina baseia-se em REST, uma opção popular de arquitetura para projetos de programação da Web.

O Aprendizado de Máquina do Azure tem dois tipos de serviços:

* RRS (Serviço de Solicitação-Resposta) – um serviço de baixa latência e altamente escalonável que fornece uma interface para os modelos sem estado criados e implantados no Estúdio de Aprendizado de Máquina.
* BES (Serviço de Execução de Lote) – um serviço assíncrono que pontua um lote de registros de dados.

Há várias maneiras para consumir a API REST e acessar o serviço Web. Por exemplo, você pode escrever um aplicativo em C#, R ou Python usando o código de exemplo gerado quando implantou o serviço Web.

O código de exemplo está disponível: na página Consumir do serviço Web no portal dos Serviços Web de Machine Learning, na página Ajuda de API no painel de serviço Web no Machine Learning Studio.

Ou você pode usar a pasta de trabalho de exemplo do Microsoft Excel criada para você (também disponível no painel do serviço Web no Studio).

**Quais são as principais atualizações com os novos Serviços Web de AM do Azure?**

Para saber mais sobre os novos Serviços Web do Machine Learning, confira a [documentação relacionada](machine-learning-whats-new.md).

## Perguntas sobre o Estúdio de Aprendizado de Máquina

### Criando um experimento

**Há controle de versão ou integração Git para gráficos de experimento?**

Não. No entanto, o Estúdio de Aprendizado de Máquina retém cada iteração de um teste que não pode ser modificada por outros usuários. Para saber mais, confira [Gerenciar iterações de teste no Estúdio de Aprendizado de Máquina](machine-learning-manage-experiment-iterations.md).


### Implantando um teste

**Posso implantar um teste de previsão como um novo serviço Web (baseado no Azure Resource Manager) se eu já o tiver como um serviço Web clássico?**

Não, não é possível implantar um teste que foi implantado anteriormente como um serviço Web clássico. Você deve criar um novo teste de previsão e implantá-lo.


### Importando e exportando dados para o Aprendizado de Máquina

**Para quais fontes de dados o Aprendizado de Máquina dá suporte?**

Dados podem ser carregados em um teste de Estúdio de Aprendizado de Máquina em uma destas três maneiras: carregando um arquivo local como um conjunto de dados, usando um módulo para importar dados de serviços de dados de nuvem ou importando um conjunto de dados salvo de outro teste. Para saber mais sobre formatos de arquivo com suporte, confira [Importar dados de treinamento para o Machine Learning Studio](machine-learning-data-science-import-data.md).


#### <a id="ModuleLimit"></a>Que tamanho o conjunto de dados para os meus módulos pode ter?

Os módulos do Estúdio de Aprendizado de Máquina dão suporte a conjuntos de dados com até 10 GB de dados numéricos densos para casos de uso comuns. Se um módulo receber mais de uma entrada, os 10 GB são o total dos tamanhos das entradas. Você também pode criar amostras de conjuntos de dados maiores por meio de consultas ao Banco de Dados SQL do Hive ou Azure ou do pré-processamento do Aprendizado por contagens, antes da ingestão.

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados maiores durante a normalização de recursos e são limitados a menos de 10 GB:

- Esparsos
- Categóricos
- Cadeias de caracteres
- Dados binários

Os seguintes módulos são limitados a conjuntos de dados com menos de 10 GB:

- Módulos de recomendação
- Módulo SMOTE
- Módulos de script: R, Python, SQL
- Módulos em que o tamanho dos dados de saída pode ser maior que o tamanho dos dados de entrada, como Join ou Feature Hashing.
- Validação cruzada, Hiperparâmetros de Modelo de Ajuste, Regressão Ordinal e Classes Múltiplas, um versos todos, quando o número de iterações é muito grande.

Para conjuntos com mais de alguns GB, você deve fazer upload de dados para o armazenamento do Azure ou Banco de Dados SQL do Azure ou usar o HDInsight, em vez de fazer upload diretamente do arquivo local.


####<a id="UploadLimit"></a>Quais são os limites para o upload de dados?
Para conjuntos com mais de 2 GB, faça upload dos dados para o armazenamento do Azure ou Banco de Dados SQL do Azure ou use o HDInsight em vez de fazer upload diretamente do arquivo local.

**Eu posso ler dados da Amazon S3?**

Para saber mais sobre como gerenciar os serviços Web implantados, veja [Gerenciar um espaço de trabalho do Aprendizado de Máquina do Azure][import-data]. Para uma quantidade maior de dados, transfira-os para o Armazenamento do Azure primeiro e use o módulo [Importar Dados][import-data] para colocá-los em seu teste.
<!--
<SEE CLOUD DS PROCESS>
-->

**Há uma funcionalidade interna de entrada de imagem?**

Você pode aprender sobre a funcionalidade de entrada de imagem na referência [Importar imagens][image-reader].

### Módulos

**O algoritmo, a fonte de dados, o formato de dados ou a operação de transformação de dados que estou procurando não está no Estúdio de Aprendizado de Máquina do Azure. Quais são minhas opções?**

Você pode visitar o [fórum de comentários dos usuários](http://go.microsoft.com/fwlink/?LinkId=404231) para ver solicitações de recurso que estamos acompanhando. Inclua seu voto em uma solicitação se um recurso que está procurando já foi solicitado. Se o recurso que está procurando não existe, crie uma nova solicitação. É possível exibir o status de sua solicitação neste fórum também. Acompanhamos esta lista de perto e atualizamos o status da disponibilidade do recurso com frequência. Além disso, com o suporte interno para R e Python, transformações personalizadas podem ser criadas conforme necessário.


**Posso trazer meu código existente para o Estúdio de Aprendizado de Máquina?**

Sim, é possível levar seus códigos existentes de R ou Python para o Estúdio de Aprendizado de Máquina, executá-los no mesmo teste com os aprendizes fornecidos pelo Aprendizado de Máquina do Azure e implantar a solução como um serviço Web por meio do Aprendizado de Máquina do Azure. Para saber mais, confira [Estender seu teste com R](machine-learning-extend-your-experiment-with-r.md) e [Executar scripts Python de aprendizado de máquina no Estúdio de Aprendizado de Máquina do Azure](machine-learning-execute-python-scripts.md).

**É possível usar algo como um [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir um modelo?**

Não, não há suporte para isso. No entanto, códigos personalizados em R e Python podem ser usados para definir um módulo.

**Quantos módulos posso executar em paralelo em meu experimento?**

Você pode executar até quatro módulos em paralelo em um experimento.


### Processamento de dados

**Há uma capacidade de visualizar dados (além de visualizações de R) interativamente dentro do experimento?**

Clicando na saída de um módulo, você pode visualizar os dados e obter estatísticas.

**Ao visualizar resultados ou dados no navegador, o número de linhas e colunas é limitado, por que?**

Como os dados são transmitidos ao navegador e podem ser grandes, o tamanho dos dados é limitado para evitar lentidão do Estúdio de Aprendizado de Máquina. Para visualizar todos os dados/resultados, é melhor baixar os dados e usar o Excel ou outra ferramenta.

### Algoritmos

**Quais algoritmos existentes têm suporte no Estúdio de Aprendizado de Máquina?**

O Machine Learning Studio fornece algoritmos de última geração, como Árvores de Decisão Aumentadas Escalonáveis, sistemas de Recomendação Bayesiana, Redes Neurais Profundas e Selvas de Decisão desenvolvidos na Microsoft Research. Pacotes de aprendizado de máquina escalonáveis de software livre, como Vowpal Wabbit, também estão incluídos. O Estúdio de Aprendizado de Máquina dá suporte a algoritmos de aprendizado de máquina para classificação binária e de múltiplas classes, de regressão e de clustering. Consulte a lista completa de [Módulos do Aprendizado de Máquina][machine-learning-modules].

**Vocês sugerem automaticamente o algoritmo correto do Aprendizado de Máquina para usar com meus dados?**

Não, mas há várias maneiras, no Machine Learning Studio, para comparar os resultados de cada algoritmo e determinar uma adequado para seu problema.

**Vocês têm alguma orientação sobre a seleção de um algoritmo em vez de outro para os algoritmos fornecidos?** Consulte [Como escolher um algoritmo ](machine-learning-algorithm-choice.md).

**Os algoritmos fornecidos são escritos em R ou Python?**

Não, esses algoritmos são escritos, em sua maioria, em linguagens compiladas para fornecer um melhor desempenho.

**São fornecidos detalhes dos algoritmos?**

A documentação fornece algumas informações sobre os algoritmos, e os parâmetros fornecidos para ajuste são descritos para otimizar o algoritmo para seu uso.

**Há algum suporte para aprendizado online?**

Não, atualmente há suporte apenas para treinamento programático.

**Posso visualizar as camadas de um Modelo de Rede Neural usando o Módulo interno?**

Nº

**Posso criar meus próprio módulos em C# ou em outra linguagem?**

Atualmente, novos módulos personalizados podem ser criados apenas em R.

### Módulo R

**Quais pacotes de R estão disponíveis no Estúdio de Aprendizado de Máquina?**

O Estúdio de Aprendizado de Máquina dá suporte a mais de 400 pacotes CRAN R atualmente. Aqui está a [lista atual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos os pacotes incluídos. Confira também [Estender seu teste com R](machine-learning-extend-your-experiment-with-r.md) para saber como recuperar essa lista por conta própria. Se o pacote que desejar não estiver nesta lista, forneça o nome do pacote no [fórum de feedback do usuário](http://go.microsoft.com/fwlink/?LinkId=404231).

**É possível criar um módulo personalizado em R?**

Sim, consulte [Criar módulos personalizados em R no Aprendizado de Máquina do Azure](machine-learning-custom-r-modules.md) para saber mais.

**Há um ambiente REPL para R?**

Não, não há um ambiente REPL para R no estúdio.

### Módulo de Python

**É possível criar um módulo personalizado em Python?**

Atualmente, não, mas você pode usar um ou mais módulos [Executar script Python][python] para obter o mesmo resultado.

**Há um ambiente REPL para Python?**

Você pode usar os blocos de notas Jupyter no Estúdio de Aprendizado de Máquina. Para saber mais, confira [Introdução aos blocos de notas Jupyter no Estúdio de Aprendizado de Máquina do Azure](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## Serviço Web

###Readaptando modelos programaticamente

**Como posso readaptar os modelos de Aprendizado de Máquina de forma programática?**

Para saber mais, confira [Readaptar os modelos de Machine Learning de forma programática](machine-learning-retrain-models-programmatically.md). O exemplo de código também está disponível na [Demonstração de readaptação com o Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

### Criação

**Posso implantar o modelo localmente ou em um aplicativo sem conexão à Internet?**

Nº


**Há uma latência de linha de base que é esperada para todos os serviços Web?**

Consulte [Limites da assinatura do Azure](../azure-subscription-service-limits.md)

### Uso

**Quando seria melhor executar meu modelo de previsão como um serviço de Execução em Lotes em vez de um serviço de Resposta da Solicitação?**

O Serviço de Solicitação-Resposta (RRS) é um serviço Web de baixa latência e alta escala usado para fornecer uma interface para modelos sem monitoração de estado que são criados e implantados por meio do ambiente de testes. O BES (Serviço de Execução em Lote) é um serviço para classificar de forma assíncrona um lote de registros de dados. A entrada para o BES é semelhante à entrada de dados usada no RRS. A principal diferença é que o BES lê um bloco de registros de uma variedade de fontes, como os serviços de Blob e Tabela do Azure, o Banco de Dados SQL do Azure, o HDInsight (consulta de Hive) e fontes de HTTP. Para saber mais, consulte [Como consumir serviços Web do Aprendizado de Máquina](machine-learning-consume-web-services.md).

**Como posso atualizar o modelo para o serviço Web implantado?**

Atualizar um modelo preditivo para um serviço já implantado é tão simples quanto modificar e executar novamente o teste que você usou para criar e salvar o modelo adaptado. Uma vez que tiver uma nova versão do modelo adaptado disponível, o Machine Learning Studio perguntará se você deseja atualizar seu serviço Web. Para obter detalhes sobre como atualizar um serviço Web implantado, confira [Implantar um serviço Web do Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

Você também pode usar as APIs de novos treinamentos. Para obter mais informações, consulte [Treinar novamente os modelos de Aprendizado de Máquina de forma programática](machine-learning-retrain-models-programmatically.md). O exemplo de código também está disponível na [Demonstração de readaptação com o Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Como posso monitorar meu serviço Web implantado na produção?**

Depois que um modelo de previsão for implantado, você poderá monitorá-lo no portal clássico do Azure. Cada serviço implantado tem seu próprio painel, onde você pode ver informações de monitoramento do serviço. Para saber mais sobre como gerenciar os serviços Web implantadas, veja [Gerenciar um espaço de trabalho do Aprendizado de Máquina do Azure](machine-learning-manage-workspace.md).

**Existe um lugar onde posso ver a saída do meu RRS/BES?**

Para RRS, a resposta do serviço Web é normalmente onde você vê o resultado. Você também pode escrevê-lo no Armazenamento de Blobs do Azure. Para BES, a saída é gravada em um blob, por padrão. Você também pode gravar a saída em um banco de dados ou em uma tabela usando o módulo [Exportar Dados][export-data].

**Posso criar serviços Web apenas de modelos criados no Estúdio de Aprendizado de Máquina?**

Não, você também pode criar serviços Web diretamente no bloco de anotações do Jupyter e no RStudio.

**Onde posso encontrar informações sobre códigos de erro?**

Confira [Códigos de erro do módulo de Aprendizado de Máquina](https://msdn.microsoft.com/library/azure/dn905910.aspx) para obter uma lista dos códigos de erro e descrições.

## Escalabilidade

**Qual é a escalabilidade do serviço Web?**

Atualmente, o ponto de extremidade padrão é provisionado com 20 solicitações RRS simultâneas por ponto de extremidade. Você pode dimensioná-lo com até 200 solicitações simultâneas por ponto de extremidade, e os serviços Web com até 10.000 pontos de extremidade por serviço Web, conforme descrito no artigo [Dimensionamento de pontos de extremidade de API](machine-learning-scaling-endpoints.md). Para BES, cada ponto de extremidade permite processar 40 solicitações por vez. Acima de 40 solicitações, as restantes são enfileiradas. Essas solicitações enfileiradas são executadas automaticamente conforme a fila anda.


**Trabalhos em R são distribuídos entre nós?**

Nº


**Quantos dados posso usar para treinamento?**

Os módulos do Estúdio de Aprendizado de Máquina dão suporte a conjuntos de dados com até 10 GB de dados numéricos densos para casos de uso comuns. Se um módulo tiver mais de uma entrada, o tamanho total de todas as entradas juntas será de 10 GB. Você também pode criar amostras de conjuntos de dados maiores por meio de consultas ao Banco de Dados SQL do Azure ou do Hive, ou fazer o pré-processamento com módulos [Aprendizado por contagens][counts] antes do uso.

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados maiores durante a normalização de recursos e são limitados a menos de 10 GB:

- esparso
- categóricos
- cadeias de caracteres
- dados binários

Os seguintes módulos são limitados a conjuntos de dados com menos de 10 GB:

- Módulos de recomendação
- Módulo SMOTE
- Módulos de script: R, Python, SQL
- Módulos em que o tamanho dos dados de saída pode ser maior que o tamanho dos dados de entrada, como Join ou Feature Hashing.
- Validação Cruzada, Hiperparâmetros de Modelo de Ajuste, Regressão Ordinal e Classes Múltiplas, um versos todos, quando o número de iterações é muito grande.

Para conjuntos com mais de alguns GB, você deve fazer upload de dados para o armazenamento do Azure ou Banco de Dados Azure SQL ou usar o HDInsight, em vez de fazer upload diretamente de um arquivo local.


**Há qualquer limitação de tamanho de vetores?**

Linhas e colunas são têm a limitação do .NET de Int Máximo: 2.147.483.647.

**O tamanho da máquina virtual que está sendo usada para executar o serviço Web pode ser ajustado?**

Nº

## Segurança e disponibilidade

**Quem tem acesso, por padrão, ao ponto de extremidade HTTP para o serviço Web? Como faço para restringir o acesso ao ponto de extremidade?**

Depois que um serviço Web for implantado, criaremos um ponto de extremidade padrão para esse serviço. O ponto de extremidade padrão pode ser chamado usando sua chave de API. Outros pontos de extremidade podem ser adicionados com suas próprias chaves no portal clássico do Azure ou de forma programática usando as APIs de Gerenciamento de Serviços Web. São necessárias chaves de acesso para fazer chamadas ao serviço Web. Para saber mais, consulte [Conectar a um serviço Web do Aprendizado de Máquina](machine-learning-connect-to-azure-machine-learning-web-service.md).


**O que acontece se minha conta de armazenamento do Azure não puder ser encontrada?**

O Estúdio de Aprendizado de Máquina depende de uma conta de armazenamento do Azure fornecida pelo usuário para salvar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Estúdio de Aprendizado de Máquina no momento em um espaço de trabalho é criado. Após o espaço de trabalho ser criado, se a conta de armazenamento for excluída e não puder mais ser encontrada, o espaço de trabalho deixará de funcionar e todos os testes nele falharão.

Se você excluir acidentalmente a conta de armazenamento, recrie essa conta de armazenamento exatamente com o mesmo nome e exatamente na mesma região que a conta de armazenamento excluída. Depois disso, sincronize novamente a Chave de Acesso.


**O que acontecerá se a chave de acesso da minha conta de armazenamento não estiver sincronizada?**

O Machine Learning Studio depende de uma conta de armazenamento do Azure fornecida pelo usuário para armazenar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio no momento em que um espaço de trabalho é criado e as Chaves de Acesso são associadas a ele. Se as Chaves de Acesso forem alteradas, depois que o espaço de trabalho for criado, ele não poderá mais acessar a conta de armazenamento. Ele deixará de funcionar e todos os testes no espaço de trabalho falharão.

Se você tiver alterado as Chaves de Acesso da conta de armazenamento, sincronize novamente as Chaves de Acesso na configuração do espaço de trabalho no portal clássico do Azure.


## Azure Marketplace

Confira as [Perguntas frequentes sobre a publicação e utilização de aplicativos no Marketplace do Aprendizado de Máquina](machine-learning-marketplace-faq.md).

## Suporte e treinamento

**Onde posso obter treinamento para o Aprendizado de Máquina do Azure?**

O [Centro de Documentação do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) hospeda tutoriais em vídeo e guias de instruções. Esses guias passo a passo fornecem uma introdução aos serviços e explicam o ciclo de vida de ciência dos dados da importação de dados, limpeza de dados, criação de modelos preditivos e implantação deles na produção com o Aprendizado de Máquina do Azure.

Incluiremos novo material continuamente no Machine Learning Center. Você pode enviar solicitações de material de aprendizado adicional no Centro de Aprendizado de Máquina no [fórum de comentários dos usuários](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Você também pode encontrar treinamento na [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Como fazer para obter suporte ao Aprendizado de Máquina do Azure?**

Para obter suporte técnico para o Aprendizado de Máquina do Azure, vá para [Suporte do Azure](/support/options/) e selecione **Aprendizado de Máquina**.

O Aprendizado de Máquina do Azure também possui um fórum de comunidade no MSDN, em que é possível fazer questões relacionadas ao Aprendizado de Máquina do Azure. O fórum é monitorado pela equipe do Aprendizado de Máquina do Azure. Visite o [Fórum do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## Perguntas sobre cobrança

**Como a cobrança do Aprendizado de Máquina funciona?**

Há dois componentes do serviço Aprendizado de Máquina do Azure. O Estúdio de Aprendizado de Máquina e os Serviços Web de Aprendizado de Máquina.

Enquanto você estiver avaliando o Estúdio de Aprendizado de Máquina, poderá usar a camada de cobrança gratuita. A Camada gratuita também permite que você implante um serviço Web clássico com capacidade limitada.

Depois de decidir que o Aprendizado de Máquina do Azure atende às suas necessidades, você poderá se inscrever na camada Standard. Para se inscrever, você precisa ter uma Assinatura do Microsoft Azure.

Na camada Standard, você será cobrado mensalmente por cada espaço de trabalho que definir no Machine Learning Studio. Quando executa um teste no estúdio, você é cobrado por recursos de computação durante a execução do teste. Quando você implanta um serviço Web clássico, as transações e horas de computação são PAYG (pré-pagas).

Os novos serviços Web de Aprendizado de Máquina apresentam planos de cobrança que permitem maior previsibilidade dos custos. Preços em camadas oferecem taxas com desconto para clientes que precisam de uma maior capacidade.

Quando você cria um plano, assume um custo fixo com uma quantidade incluída de horas de computação de API e de transações de API. Se você precisar de mais quantidades incluídas, poderá adicionar mais instâncias ao seu plano. Se precisar de muito mais quantidades incluídas, você poderá escolher um plano de camada superior que tenha quantidades incluídas ainda maiores e uma melhor taxa de desconto.

Depois que as quantidades incluídas em instâncias existentes forem consumidas, o uso adicional será cobrado na taxa excedente associada à camada de plano de cobrança.

Observação: as quantidades incluídas são realocadas a cada 30 dias e as quantidades não usadas não passam para o período seguinte.

Para saber mais sobre preços e cobrança, confira [Preços do Aprendizado de Máquina](https://azure.microsoft.com/pricing/details/machine-learning/).

**O Aprendizado de Máquina tem uma avaliação gratuita?**

 O Aprendizado de Máquina do Azure tem uma opção de assinatura gratuita (consulte [Preços do Aprendizado de Máquina](https://azure.microsoft.com/pricing/details/machine-learning/) para obter detalhes) e o Estúdio de Aprendizado de Máquina tem uma avaliação rápida de 8 horas disponível (faça logon no [Estúdio de Aprendizado de Máquina](https://studio.azureml.net/?selectAccess=true&o=2) para obter essa avaliação).
 
 Além disso, quando você se inscrever em uma avaliação gratuita do Azure, poderá experimentar qualquer serviço do Azure por um mês. Para saber mais sobre a avaliação gratuita do Azure, visite [Perguntas frequentes sobre a avaliação gratuita do Azure](/pricing/free-trial-faq/).

**O que é uma transação?**

Uma transação representa uma chamada à API a qual responde o Aprendizado de Máquina do Azure. AS chamadas RRS (Serviço de solicitação-resposta) e BES (Serviço de execução em lote) são agregadas e cobradas em seu plano de cobrança.

**Posso usar as quantidades de transação incluídas em um plano para transações RRS e BES?**

Sim, as transações dos RRS e BES são agregadas e cobradas em seu plano de cobrança.

**O que é uma hora de computação de API?**

Uma hora de computação de API é a unidade de cobrança do tempo que leva para as chamadas à API serem executadas usando os recursos de computação de AM. Todas as chamadas são agregadas para fins de cobrança.

**Quanto tempo demora uma chamada típica à API de produção?**

As horas de produção de chamada à API podem variar significativamente, oscilando geralmente de centenas de milissegundos a alguns segundos, mas podem exigir minutos, dependendo da complexidade do processamento dos dados e do modelo de aprendizado de máquina. A melhor forma de estimar os tempos de chamada à API de produção é submeter um modelo a benchmark no serviço Aprendizado de Máquina.

**O que é uma hora de computação do Estúdio?**

Uma hora de computação do Estúdio é a unidade de cobrança para o tempo agregado em que seus testes usam recursos de computação no Estúdio.

**Nos novos serviços Web, para que serve a camada de desenvolvimento e teste?**

Os novos serviços Web fornecem várias camadas que você pode usar para provisionar seu plano de cobrança. A camada de desenvolvimento e teste é uma camada que fornece quantidades incluídas limitadas e permite que você avalie seu teste como novo serviço Web sem incorrer em custos. Você tem a oportunidade de "mergulhar nesta experiência" para ver como ele funciona.

**Existem encargos de armazenamento separados?**

A camada Gratuita do Aprendizado de Máquina não exige nem permite armazenamento separado. A camada Standard do Aprendizado de Máquina exige que os usuários tenham uma conta de armazenamento do Azure. O Armazenamento do Azure [é cobrado separadamente](https://azure.microsoft.com/pricing/details/storage/).

**Como funciona a alta disponibilidade do suporte do Aprendizado de Máquina?**

As horas de produção de chamada à API podem variar significativamente, oscilando geralmente de centenas de milissegundos a alguns segundos, mas podem exigir minutos, dependendo da complexidade do processamento dos dados e do modelo de aprendizado de máquina. A melhor forma de estimar os tempos de chamada à API de produção é submeter um modelo a benchmark no serviço Aprendizado de Máquina.

**Em quais tipos específicos de recursos de computação minhas chamadas à API de produção serão executadas?**

O serviço Machine Learning é um serviço multilocatário, cujos recursos reais de computação usados no back-end variam e são otimizados para oferecer desempenho e previsibilidade.

### Gerenciamento de novos serviços Web 

**O que acontece se eu excluir meu plano?**

O plano é removido de sua assinatura e você é cobrado pelo uso rateado.

Observação: você não pode excluir um plano que está sendo usado por um serviço Web. Para excluir o plano, atribua um novo plano ao serviço Web ou exclua o serviço Web.

**O que é uma instância de plano?**

Uma instância de plano é uma unidade de quantidades incluídas que você pode adicionar ao seu plano de cobrança. Quando você seleciona uma camada de cobrança para o plano de cobrança, ela vem com uma instância. Se você precisar de mais quantidades incluídas, poderá adicionar instâncias da camada de cobrança selecionada ao seu plano.

**Quantas instâncias do plano posso adicionar?**

Você pode ter uma instância da camada de desenvolvimento e teste em uma assinatura.

Para as camadas S1, S2 e S3, você pode adicionar tantas quantas forem necessárias.

Observação: dependendo de seu uso antecipado, pode ser mais econômico atualizar para um nível mais alto de quantidades incluídas em vez de adicionar instâncias à camada atual.

**O que acontece quando eu mudo de camadas de plano (atualização/downgrade)?**

O plano antigo será excluído e o uso atual será cobrado em uma base rateada. Um novo plano com as quantidades incluídas completas da camada atualizada/com downgrade é criado para o restante do período.

Observação: as quantidades incluídas são alocadas por período e as quantidades não usadas não são transferidas para o período seguinte.

**O que acontece quando eu aumento as instâncias em um plano?**

As quantidades estão incluídas em uma base rateada e podem levar até 24 horas para entrar em vigor.

**O que acontece quando eu excluo uma instância de um plano?**

A instância é removida de sua assinatura e você é cobrado pelo uso rateado.


### Inscrevendo-se em planos de novos serviços Web

**Como faço para assinar um plano?**

Existem duas maneiras de criar planos de cobrança.

Quando você implanta um novo serviço Web, pode escolher um plano existente ou criar um novo.

Planos criados dessa maneira ficam em sua região padrão e o serviço Web será implantado nessa região.

Se quiser implantar serviços em regiões diferentes da sua região padrão, convém definir seus planos de cobrança antes de implantá-lo,

Nesse caso, você pode entrar no portal Serviços Web de Aprendizado de Máquina do Azure e navegar até a página de planos. Dali, você pode adicionar, excluir e modificar planos existentes.

**Com que plano devo começar?**

Recomendamos que você comece com a camada S1 standard e monitore o uso de seu serviço. Se você estiver consumindo suas quantidades incluídas rapidamente, poderá adicionar instâncias ou mudar para uma camada mais elevada e obter melhores taxas de desconto. Você pode ajustar seu plano de cobrança conforme o necessário em todo seu ciclo de cobrança.

**Em quais regiões os novos planos estão disponíveis?**

Os novos planos de cobrança estão disponíveis nas três regiões de produção em que há suporte para os novos serviços Web:

* Centro-Sul dos Estados Unidos
* Europa Ocidental
* Sudeste da Ásia

**Tenho serviços Web em várias regiões. Preciso de um plano para cada região?**

Sim. Os preços de planos variam para cada região. Ao implantar um serviço Web em outra região, você precisará atribuir a ele um plano específico dessa região.

### Novos serviços Web - Excedentes

**Como verifico se o uso do serviço Web está excedente?**

Você pode exibir o uso em todos os seus planos na página Planos no portal de serviços Web de Machine Learning. Entre no portal e clique na opção de menu Planos.

Nas colunas Transações e Computação da tabela, você pode ver as quantidades incluídas do plano e a porcentagem de uso.

**O que acontece quando uso toda a quantidade incluída na camada de desenvolvimento e teste?**

Serviços que têm camadas de desenvolvimento e teste atribuídas a eles são interrompidos até o período seguinte ou você pode movê-los para uma das camadas pagas.

**Para os serviços Web clássicos e excedentes de novos serviços Web, como são os preços calculados para cargas de trabalho de RRS (solicitação de resposta) e BES (lote)?**

Para uma carga de trabalho RRS, você será cobrado por todas as chamadas de transação de API que fizer, além do tempo de cálculo associado a essas solicitações. Seus custos da transação de API de produção RRS são, portanto, calculados como o número total de chamadas de API que você faz multiplicado pelo preço por mil transações (rateado por transação individual). Seus custos de horas de computação da API de produção de API RRS são calculados como a quantia de tempo necessária para cada chamada à API ser executada, multiplicada pelo número total de transações de API multiplicado pelo preço por hora de computação da API de produção.

Por exemplo, para excedente em Standard S1, um milhão de transações de API que demoram 0,72 segundo cada para execução resultariam em (1.000.000 * *US$ 0,50/1K de transações API) em US$ 500 em custos de transação de API de produção e (1,000,000 * 0,72 s * US$ 2/h) US$ 400 nas horas de computação da API de produção, para um total de US$ 900.

Para uma carga de trabalho BES, você é cobrado do mesmo modo. No entanto, os custos de transação de API representam o número de trabalhos de lote que você enviou e os custos calculados representam o tempo calculado associado a esses trabalhos de lote. Seus custos da transação de API de produção BES são, portanto, calculados como o número total de trabalhos enviados multiplicado pelo preço por mil transações (rateado por transação individual). Seus custos de horas de computação de API de produção de API BES são calculados como a quantia de tempo necessária para cada linha em seu trabalho para execução multiplicada pelo número total de linhas em seu trabalho, multiplicado pelo número total de trabalhos, multiplicado pelo preço por hora de computação da API de produção. Ao usar a calculadora Aprendizado de Máquina, a métrica de transação representa o número de trabalhos que você planeja enviar e o tempo por campo de transação representa o tempo combinado necessário para execução de todas as linhas em cada trabalho.

Por exemplo, para excedente na camada Standard S1, se você envia 100 trabalhos por dia que consistem de 500 linhas cada e que demoram 0,72 segundo cada, seus custos mensais são (100 trabalhos por dia = 3.100 trabalhos/mês * US$ 0,50/1K de transações de API) US$ 1,55 nos custos de transação de API de produção e (500 linhas * 0,72 s * 3.100 trabalhos * US$ 2/h) US$ 620 nas horas de computação da API de produção, para um total de US$ 621,55.

### Serviços Web clássicos de AM do Azure

**O pré-pago ainda está disponível?** Sim, os serviços Web clássicos ainda estão disponíveis no Aprendizado de Máquina do Azure.

### Camadas Gratuita e Standard do Aprendizado de Máquina

**O que está incluído na Camada gratuita do Aprendizado de Máquina do Azure?**

A camada Gratuita do Aprendizado de Máquina do Azure tem o objetivo de fornecer uma introdução abrangente ao Estúdio de Aprendizado de Máquina do Azure. Tudo o que você precisa é de uma conta da Microsoft para se conectar. A Camada gratuita inclui acesso gratuito a um espaço de trabalho do Estúdio de Aprendizado de Máquina do Azure por [conta da Microsoft](https://www.microsoft.com/account/default.aspx). Ela inclui a capacidade de usar até 10 GB de armazenamento e de operacionalizar modelos como APIs de preparo. Cargas de trabalho de camada gratuita não são cobertas por um SLA e destinam-se ao desenvolvimento e uso pessoal. As cargas de trabalho de Camada gratuita não podem acessar dados ao conectar-se a um servidor SQL local.

**O que está incluso na camada e nos planos Standard do Aprendizado de Máquina do Azure?**

A camada Standard do Aprendizado de Máquina do Azure é uma versão paga do Estúdio de Aprendizado de Máquina do Azure. A taxa mensal do serviço Azure ML Studio é cobrada por estação, por mês e rateada em meses parciais. As horas de teste no Estúdio AM do Azure são cobradas por hora de computação para testes ativos. A cobrança é rateada em horas parciais.

O serviço API do AM do Azure é cobrado dependendo da sua natureza como serviço Web clássico ou novo serviço Web.

As cobranças a seguir são agregadas por espaço de trabalho para sua assinatura.

* Assinatura do Espaço de Trabalho do Machine Learning: a Assinatura do Espaço de Trabalho do Machine Learning é uma taxa mensal que dá acesso a um espaço de trabalho do Estúdio AM e é necessária para executar testes no estúdio e utilizar as APIs de produção.
* Horas de Teste no Estúdio – esse medidor agrega todas as cobranças de computação vencidas ao executar testes no Estúdio AM e executar chamadas de API de produção no ambiente de preparo.
* Dados de acesso por conexão com um servidor SQL local em seus modelos para treinamento e pontuação.
* Para serviços Web clássicos:
	* Horas de Computação da API de Produção – esse medidor inclui cobranças de computação vencidas por serviços Web em execução na produção.
	* Transações de API de Produção (em milhares) – esse medidor inclui cobranças vencidas por chamada ao serviço Web de produção.

Além dos encargos mencionados, no caso de novos serviços Web, os encargos são agregados para o plano selecionado:

* Plano Standard S1/S2/S3 API (unidades) – essa métrica representa o tipo da instância selecionada para novos serviços Web
* Horas de computação de API excedente Standard S1/S2/S3 – essa métrica inclui encargos de computação resultantes dos novos serviços Web em execução na produção depois que as quantidades incluídas nas instâncias existentes são consumidas. O uso adicional será cobrado com a sobretaxa associada à camada de plano S1/S2/S3.
* Transações de API excedentes Standard S1/S2/S3 (em milhares) – essa métrica inclui encargos resultantes totalizadas por chamada para a novo serviço Web de produção depois que as quantidades incluídas nas instâncias existentes são consumidas. O uso adicional será cobrado com a sobretaxa associada à camada de plano S1/S2/S3.
* Quantidade Incluída de Horas de Computação de API – com os novos serviços Web, essa métrica representa a quantidade incluída de horas de computação de API
* Quantidade Incluída de Transações de API (em milhares) – com os novos serviços Web, essa métrica representa a quantidade incluída de transações de API


**Como faço para me inscrever na Camada gratuita de Aprendizado de Máquina do Azure?**

Tudo o que você precisa é de uma conta da Microsoft. Vá para o [início do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) e clique no botão **Iniciar agora**. Conecte-se com sua conta da Microsoft e um espaço de trabalho na camada Gratuita será criado para você. É possível começar a explorar e criar testes do Aprendizado de Máquina imediatamente.

**Como faço para me inscrever na camada Standard de Aprendizado de Máquina do Azure?**

Primeiro, é necessário ter acesso a uma assinatura do Azure para criar um espaço de trabalho do ML Standard. Você pode inscrever-se em uma assinatura do Azure com avaliação gratuita de 30 dias e atualizar posteriormente para uma assinatura paga ou comprar uma assinatura do Azure paga imediatamente. Em seguida, é possível criar um espaço de trabalho do Machine Learning no portal clássico do Microsoft Azure depois de ter acesso à assinatura. Veja as [instruções passo a passo](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Se preferir, é possível ser convidado por um proprietário de espaço de trabalho de Aprendizado de Máquina Standard para acessar o espaço de trabalho do proprietário.

**Posso especificar minha própria conta de armazenamento de blobs do Azure para usar com a camada Gratuita?**

Não. A camada Standard é equivalente à versão do serviço de Aprendizado de Máquina que estava disponível antes de as camadas terem sido introduzidas.

**Posso implantar meus modelos de aprendizado de máquina como APIs na camada Gratuita?**

Sim. É possível operacionalizar os modelos de aprendizado de máquina para serviços de API de preparo como parte da camada gratuita. Para colocar o serviço de API de preparo em produção e obter um ponto de extremidade de produção para o serviço operacionalizado, é necessário usar a camada Standard.

**Qual é a diferença entre a avaliação Gratuita do Azure e a camada Gratuita do Aprendizado de Máquina do Azure?**

A [avaliação gratuita do Microsoft Azure](https://azure.microsoft.com/free/) oferece créditos que podem ser aplicados a qualquer serviço do Azure em um mês. A Camada gratuita do Azure Machine Learning oferece acesso contínuo especificamente ao serviço Azure Machine Learning para cargas de trabalho que não são de produção.

**Como devo mover um teste da camada Gratuita para a camada Standard?**

Para copiar seus testes da Camada gratuita para a camada Standard:

1.	Conecte-se ao Estúdio de Aprendizado de Máquina do Azure e certifique-se de que esteja visualizando o espaço de trabalho Gratuito e a Standard no seletor na parte superior da varra de navegação.
2.	Alterne para o espaço de trabalho Gratuito se estiver na Standard.
3.	Na exibição de lista de testes, selecione um teste que você deseja copiar e clique no botão de comando Copiar.
4.	Selecione o espaço de trabalho Standard na caixa de diálogo pop-up e clique no botão Copiar. Todos os conjuntos de dados associados, modelos treinados e afins são copiados com o teste no espaço de trabalho Standard.
6.	Você precisa executar novamente o teste e republicá-lo no serviço Web no espaço de trabalho Standard.

### Espaço de trabalho do Estúdio

**Existem faturas diferentes para espaços de trabalho diferentes?**

As cobranças referentes a espaços de trabalho são divididas em separado para cada medidor aplicável em uma única fatura.

**Em quais tipos de recursos de computação específicos meus testes serão executados?**

O serviço Machine Learning é um serviço multilocatário, cujos recursos reais de computação usados no back-end variam e são otimizados para oferecer desempenho e previsibilidade.

### Acesso de convidado

**O que é o acesso de convidado para o Estúdio de Aprendizado de Máquina Azure?**

O Acesso de Convidado é uma experiência teste restrita que permite que você crie e execute testes no Estúdio de Aprendizado de Máquina Azure sem custos e sem autenticação. As sessões de convidado são não persistentes (não podem ser salvas) e limitadas a oito horas. Outras limitações incluem falta de suporte para R e Python, falta de APIs de preparo e capacidade de armazenamento e tamanho restritos do conjunto de dados. Por comparação, os usuários que escolhem se conectar com uma conta da Microsoft têm acesso completo à camada gratuita do Machine Learning Studio descrito acima, que inclui um espaço de trabalho persistente e recursos mais abrangentes. Escolha sua experiência gratuita do Machine Learning ao clicar no botão **Iniciar** em [https://studio.azureml.net](https://studio.azureml.net) e selecione Acesso de convidado ou entrar com uma conta da Microsoft.

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

<!---HONumber=AcomDC_0928_2016-->