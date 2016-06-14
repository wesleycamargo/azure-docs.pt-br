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
	ms.date="04/18/2016"
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

Consulte [Conectar a um serviço Web do Aprendizado de Máquina](machine-learning-connect-to-azure-machine-learning-web-service.md) para obter mais informações.


## Perguntas sobre cobrança

**Como a cobrança do Aprendizado de Máquina funciona?**

Para obter informações sobre preços e cobrança, consulte [Preços do Aprendizado de Máquina](https://azure.microsoft.com/pricing/details/machine-learning/).

**O Aprendizado de Máquina tem uma avaliação gratuita?**

 Ao assinar uma avaliação gratuita do Azure, você pode testar quaisquer serviços do Azure por um mês. Para saber mais sobre a avaliação gratuita do Azure, visite [Perguntas Frequentes sobre a Avaliação Gratuita do Azure](/pricing/free-trial-faq/).

## Perguntas sobre o Estúdio de Aprendizado de Máquina

### Criando um experimento

**Há controle de versão ou integração Git para gráficos de experimento?**

Não. No entanto, o Estúdio de Aprendizado de Máquina retém cada iteração de um teste que não pode ser modificada por outros usuários. Para saber mais, confira [Gerenciar iterações de teste no Estúdio de Aprendizado de Máquina](machine-learning-manage-experiment-iterations.md).

### Importando e exportando dados para o Aprendizado de Máquina

**Para quais fontes de dados o Aprendizado de Máquina dá suporte?**

Dados podem ser carregados em um teste de Estúdio de Aprendizado de Máquina em uma destas três maneiras: carregando um arquivo local como um conjunto de dados, usando um módulo para importar dados de serviços de dados de nuvem ou importando um conjunto de dados salvo de outro teste. Consulte [Importar dados de treinamento para o Estúdio de Aprendizado de Máquina](machine-learning-data-science-import-data.md) para saber mais sobre os formatos de arquivo com suporte.


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

Se tiver uma pequena quantidade de dados e quiser expô-los por meio de uma URL http, você pode usar o módulo [Importar Dados][import-data]. Para uma quantidade maior de dados, transfira-os para o Armazenamento do Azure primeiro e use o módulo [Importar Dados][import-data] para colocá-los em seu teste.
<!--
<SEE CLOUD DS PROCESS>
-->

**Há uma funcionalidade interna de entrada de imagem?**

Você pode aprender sobre a funcionalidade de entrada de imagem na referência [Importar Imagens][image-reader].

### Módulos

**O algoritmo, a fonte de dados, o formato de dados ou a operação de transformação de dados que estou procurando não está no Estúdio de Aprendizado de Máquina do Azure. Quais são minhas opções?**

Você pode visitar o [fórum de feedback do usuário](http://go.microsoft.com/fwlink/?LinkId=404231) para ver solicitações de recurso que estamos acompanhando. Inclua seu voto em uma solicitação se um recurso que está procurando já foi solicitado. Se o recurso que está procurando não existe, crie uma nova solicitação. É possível exibir o status de sua solicitação neste fórum também. Acompanhamos esta lista de perto e atualizamos o status da disponibilidade do recurso com frequência. Além disso, com o suporte interno para R e Python, transformações personalizadas podem ser criadas conforme necessário.


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

O Estúdio de Aprendizado de Máquina fornece algoritmos de última geração, como Árvores de Decisão Aumentadas Escalonáveis, sistemas de Recomendação Bayesiana, Redes Neurais Profundas e Selvas de Decisão desenvolvidos na Microsoft Research. Pacotes de aprendizado de máquina escalonáveis de software livre, como Vowpal Wabbit, também estão incluídos. O Estúdio de Aprendizado de Máquina dá suporte a algoritmos de aprendizado de máquina para classificação binária e de múltiplas classes, de regressão e de clustering. Consulte a lista completa de [Módulos do Aprendizado de Máquina][machine-learning-modules].

**Vocês sugerem automaticamente o algoritmo correto do Aprendizado de Máquina para usar com meus dados?**

Não, mas há várias maneiras, no Estúdio de Aprendizado de Máquina, para comparar os resultados de cada algoritmo e determinar uma adequado para seu problema.

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

O Estúdio de Aprendizado de Máquina dá suporte a mais de 400 pacotes CRAN R atualmente. Aqui está a [lista atual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos os pacotes incluídos. Confira também [Estender seu experimento com R](machine-learning-extend-your-experiment-with-r.md) para saber como recuperar essa lista por conta própria. Se o pacote que desejar não estiver nesta lista, forneça o nome do pacote no [fórum de feedback do usuário](http://go.microsoft.com/fwlink/?LinkId=404231).

**É possível criar um módulo personalizado em R?**

Sim, consulte [Criar módulos personalizados em R no Aprendizado de Máquina do Azure](machine-learning-custom-r-modules.md) para saber mais.

**Há um ambiente REPL para R?**

Não, não há um ambiente REPL para R no estúdio.

### Módulo de Python

**É possível criar um módulo personalizado em Python?**

Atualmente, não, mas você pode usar um ou mais módulos [Executar Script Python][python] para obter o mesmo resultado.

**Há um ambiente REPL para Python?**

Você pode usar os blocos de notas Jupyter no Estúdio de Aprendizado de Máquina. Para saber mais, confira [Introdução aos blocos de anotações do Jupyter no Estúdio de Aprendizado de Máquina do Azure](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## Serviço Web

###Treinando modelos programaticamente

**Como posso readaptar os modelos de Aprendizado de Máquina de forma programática?**

Use as APIs de novos treinamentos. Para saber mais, confira [Readaptar os modelos de Aprendizado de Máquina de forma programática](machine-learning-retrain-models-programmatically.md). O exemplo de código também está disponível na [Demonstração de readaptação de Aprendizado de Máquina do Microsoft Azure](https://azuremlretrain.codeplex.com/).

### Criar

**Posso implantar o modelo localmente ou em um aplicativo sem conexão à Internet?**

Nº


**Há uma latência de linha de base que é esperada para todos os serviços Web?**

Consulte [Limites da assinatura do Azure](../azure-subscription-service-limits.md)

### Uso

**Quando seria melhor executar meu modelo de previsão como um serviço de Execução em Lotes em vez de um serviço de Resposta da Solicitação?**

O Serviço de Solicitação-Resposta (RRS) é um serviço Web de baixa latência e alta escala usado para fornecer uma interface para modelos sem monitoração de estado que são criados e implantados por meio do ambiente de testes. O BES (Serviço de Execução em Lote) é um serviço para classificar de forma assíncrona um lote de registros de dados. A entrada para o BES é semelhante à entrada de dados usada no RRS. A principal diferença é que o BES lê um bloco de registros de uma variedade de fontes, como os serviços de Blob e Tabela do Azure, o Banco de Dados SQL do Azure, o HDInsight (consulta de Hive) e fontes de HTTP. Para saber mais, consulte [Como consumir serviços Web do Aprendizado de Máquina](machine-learning-consume-web-services.md).

**Como posso atualizar o modelo para o serviço Web implantado?**

Atualizar um modelo preditivo para um serviço já implantado é tão simples quanto modificar e executar novamente o teste que você usou para criar e salvar o modelo adaptado. Uma vez que tiver uma nova versão do modelo adaptado disponível, o Estúdio de Aprendizado de Máquina perguntará se você deseja atualizar seu serviço Web. Consulte [Implantar um serviço Web do Aprendizado de Máquina](machine-learning-publish-a-machine-learning-web-service.md) para obter detalhes sobre como atualizar um serviço Web implantado.

Você também pode usar as APIs de novos treinamentos. Para saber mais, confira [Readaptar os modelos de Aprendizado de Máquina de forma programática](machine-learning-retrain-models-programmatically.md). O exemplo de código também está disponível na [Demonstração de readaptação de Aprendizado de Máquina do Microsoft Azure](https://azuremlretrain.codeplex.com/).

**Como posso monitorar meu serviço Web implantado na produção?**

Depois que um modelo de previsão for implantado, você poderá monitorá-lo no portal clássico do Azure. Cada serviço implantado tem seu próprio painel, onde você pode ver informações de monitoramento do serviço.

**Existe um lugar onde posso ver a saída do meu RRS/BES?**

Para RRS, a resposta do serviço Web é normalmente onde você vê o resultado. Você também pode escrevê-lo no Armazenamento de Blobs do Azure. Para BES, a saída é gravada em um blob, por padrão. Você também pode gravar a saída em um banco de dados ou tabela usando o módulo [Exportar Dados][export-data].

**Posso criar serviços Web apenas de modelos criados no Estúdio de Aprendizado de Máquina?**

Não, você também pode criar serviços Web diretamente no bloco de anotações do Jupyter e no RStudio.

**Onde posso encontrar informações sobre códigos de erro?**

Confira [Códigos de erro do módulo de Aprendizado de Máquina](https://msdn.microsoft.com/library/azure/dn905910.aspx) para obter uma lista dos códigos de erro e descrições.

## Escalabilidade

**Qual é a escalabilidade do serviço Web?**

Atualmente, o ponto de extremidade padrão é provisionado com 20 solicitações RRS simultâneas por ponto de extremidade. Você pode dimensionar as solicitações simultâneas a 200 solicitações por ponto de extremidade e os serviços Web a 10 mil pontos de extremidade por serviço Web, conforme descrito no artigo [Dimensionamento de pontos de extremidade de API](machine-learning-scaling-endpoints.md). Para BES, cada ponto de extremidade permite processar 40 solicitações por vez. Acima de 40 solicitações, as restantes são enfileiradas. Essas solicitações enfileiradas serão executadas automaticamente conforme a fila anda.


**Trabalhos em R são distribuídos entre nós?**

Nº


**Quantos dados posso usar para treinamento?**

Os módulos do Estúdio de Aprendizado de Máquina dão suporte a conjuntos de dados com até 10 GB de dados numéricos densos para casos de uso comuns. Se um módulo tiver mais de uma entrada, o tamanho total de todas as entradas juntas será de 10 GB. Você também pode criar amostras de conjuntos de dados maiores por meio de consultas ao Banco de Dados Azure SQL ou de pré-processamento com módulos [Aprendizado por contagens][counts] antes da ingestão.

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

**Quem tem acesso ao ponto de extremidade HTTP para o serviço Web por padrão? Como faço para restringir o acesso ao ponto de extremidade?**

Depois que um serviço Web for implantado, criaremos um ponto de extremidade padrão para esse serviço. O ponto de extremidade padrão pode ser chamado usando sua chave de API. Outros pontos de extremidade podem ser adicionados com suas próprias chaves no portal clássico do Azure ou de forma programática usando as APIs de Gerenciamento de Serviços Web. São necessárias chaves de acesso para fazer chamadas ao serviço Web. Para saber mais, consulte [Conectar a um serviço Web do Aprendizado de Máquina](machine-learning-connect-to-azure-machine-learning-web-service.md).


**O que acontece se minha conta de armazenamento do Azure não puder ser encontrada?**

O Estúdio de Aprendizado de Máquina depende de uma conta de armazenamento do Azure fornecida pelo usuário para salvar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Estúdio de Aprendizado de Máquina no momento em um espaço de trabalho é criado. Após o espaço de trabalho ser criado, se a conta de armazenamento for excluída e não puder mais ser encontrada, o espaço de trabalho deixará de funcionar e todos os testes nele falharão.

Se você excluir acidentalmente a conta de armazenamento, a única maneira de recuperar é recriar essa conta de armazenamento exatamente com o mesmo nome e exatamente na mesma região que a conta de armazenamento excluída. Depois disso, sincronize novamente a Chave de Acesso.


**O que acontecerá se a chave de acesso da minha conta de armazenamento não estiver sincronizada?**

O Estúdio de Aprendizado de Máquina depende de uma conta de armazenamento do Azure fornecida pelo usuário para salvar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Estúdio de Aprendizado de Máquina no momento em um espaço de trabalho é criado e as Chaves de Acesso são associadas a esse espaço de trabalho. Após o espaço de trabalho ser criado, se as Chaves de Armazenamento foram alteradas, o espaço de trabalho não poderá mais acessar a conta de armazenamento, deixará de funcionar e todos os testes nele falharão.

Se você tiver alterado as Chaves de Acesso da conta de armazenamento, sincronize novamente as Chaves de Acesso na configuração do espaço de trabalho no portal clássico do Azure


## Azure Marketplace

Confira as [Perguntas frequentes sobre a publicação e utilização de aplicativos no Marketplace do Aprendizado de Máquina](machine-learning-marketplace-faq.md).

## Suporte e treinamento

**Onde posso obter treinamento para o Aprendizado de Máquina do Azure?**

O [Centro de Documentação do Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/) hospeda tutoriais em vídeo e guias de instruções. Esses guias passo a passo fornecem uma introdução aos serviços e explicam o ciclo de vida de ciência dos dados da importação de dados, limpeza de dados, criação de modelos preditivos e implantação deles na produção com o Aprendizado de Máquina do Azure.

Incluiremos novo material continuamente no Centro de Aprendizado de Máquina. Você pode enviar solicitações de material de aprendizado adicional no Centro de Aprendizado de Máquina no [fórum de feedback do usuário](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Você também pode encontrar treinamento na [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning)

**Como fazer para obter suporte ao Aprendizado de Máquina do Azure?**

Para obter suporte técnico para o Aprendizado de Máquina do Azure, vá para [Suporte do Azure](/support/options/) e selecione **Aprendizado de Máquina**.

O Aprendizado de Máquina do Azure também possui um fórum de comunidade no MSDN, em que é possível fazer questões relacionadas ao Aprendizado de Máquina do Azure. O fórum é monitorado pela equipe do Aprendizado de Máquina do Azure. Visite o [Fórum do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


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

<!---HONumber=AcomDC_0608_2016-->