<properties
	pageTitle="Perguntas Frequentes sobre o Aprendizado de Máquina do Azure | Microsoft Azure"
	description="Introdução ao Aprendizado de Máquina do Azure: perguntas frequentes sobre cobrança, recursos e limitações de um serviço de nuvem para modelagem preditiva simplificada."
	keywords="introdução ao aprendizado de máquina, modelagem preditiva, o que é aprendizado de máquina"
	services="machine-learning"
	documentationCenter=""
	authors="pablissima"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="paulettm"/>

# Perguntas frequentes (FAQ) sobre o Aprendizado de Máquina do Azure: cobrança, suporte, recursos e limitações

Essas perguntas frequentes são perguntas sobre o Aprendizado de Máquina do Azure, um serviço de nuvem para modelagem preditiva e soluções de operacionalização através de serviços Web. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte.

## Perguntas gerais

**O que é Aprendizado de Máquina do Azure?**

O Aprendizado de Máquina do Azure é um serviço totalmente gerenciado que você pode usar para criar, testar, operar e gerenciar soluções analíticas preditivas na nuvem. Com apenas um navegador, agora você pode entrar, fazer upload de dados e iniciar imediatamente experimentos de aprendizado de máquina. Modelagem de previsão do tipo "arrastar e soltar", um grande palete de módulos e uma biblioteca de modelos de início tornam as tarefas comuns de Aprendizado de Máquina algo rápido e simples. Para saber mais, consulte [Visão geral do serviço de Aprendizado de Máquina do Azure](/services/machine-learning/). Para obter uma introdução sobre o Aprendizado de Máquina que aborda conceitos e terminologia principal, consulte [Introdução ao Aprendizado de Máquina do Azure](machine-learning-what-is-machine-learning.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**O que é o Estúdio de Aprendizado de Máquina?**

O Estúdio de Aprendizado de Máquina é um ambiente de bancada de trabalho que você acessa por meio de um navegador da Web. O Estúdio de Aprendizado de Máquina hospeda uma paleta de módulos com uma interface de composição visual que permite criar um fluxo de trabalho de ciência de dados de ponta a ponta na forma de um experimento.

Para saber mais sobre o Estúdio do Aprendizado de Máquina, consulte [O que é o Estúdio de Aprendizado de Máquina](machine-learning-what-is-ml-studio.md)

**O que é a o serviço de API de Aprendizado de Máquina do Azure?**

O serviço de API do Aprendizado de Máquina permite implantar modelos preditivos no Estúdio de Aprendizado de Máquina como serviços Web escaláveis e tolerantes a falhas. Os serviços Web criados pelo serviço de API do Aprendizado de Máquina são APIs REST, que fornecem uma interface para comunicação entre aplicativos externos e seu modelo analítico preditivo.

Consulte [Conectar a um serviço Web do Aprendizado de Máquina](machine-learning-connect-to-azure-machine-learning-web-service.md) para obter mais informações.


## Perguntas sobre cobrança

**Como a cobrança do Aprendizado de Máquina funciona?**

Para obter informações sobre preços e cobrança, consulte [Preços do Aprendizado de Máquina](http://azure.microsoft.com/pricing/details/machine-learning/).

**O Aprendizado de Máquina tem uma avaliação gratuita?**

 Ao assinar uma avaliação gratuita do Azure, você pode testar quaisquer serviços do Azure por um mês. Para saber mais sobre a avaliação gratuita do Azure, visite [Perguntas Frequentes sobre a Avaliação Gratuita do Azure](/pricing/free-trial-faq/).

## Perguntas sobre o Estúdio de Aprendizado de Máquina

### Criando um experimento
**Há controle de versão ou integração Git para gráficos de experimento?**

Não, mas sempre que um experimento é executado a versão do gráfico é mantida e não pode ser modificada por outros usuários.

### Importando e exportando dados para o Aprendizado de Máquina
**Para quais fontes de dados o Aprendizado de Máquina dá suporte?**

Os dados podem ser carregados no Estúdio de Aprendizado de Máquina de uma de duas formas: ao fazer upload de arquivos locais como conjunto de dados ou usando o módulo de leitor para importar dados. Arquivos locais podem ser transferidos por upload adicionando novos conjuntos de dados ao Estúdio de Aprendizado de Máquina. Consulte [Importar dados de treinamento para o Estúdio de Aprendizado de Máquina](machine-learning-import-data.md) para saber mais sobre os formatos de arquivo com suporte.


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
- Validação Cruzada, Parâmetros de Varredura, Regressão Ordinal e Classes múltiplas, um versos todos, quando o número de iterações é muito grande.

Para conjuntos com mais de alguns GB, você deve fazer upload de dados para o armazenamento do Azure ou Banco de Dados SQL do Azure ou usar o HDInsight, em vez de fazer upload diretamente do arquivo local.


####<a id="UploadLimit"></a>Quais são os limites para o upload de dados?
Para conjuntos com mais de 2 GB, faça upload dos dados para o armazenamento do Azure ou Banco de Dados SQL do Azure ou use o HDInsight em vez de fazer upload diretamente do arquivo local.

**Eu posso ler dados da Amazon S3?**

Se tiver uma pequena quantidade de dados e quiser expô-los por meio de uma URL http, você pode usar o módulo [Leitor][reader]. Para qualquer quantidade maior de dados, transfira-os para o armazenamento do Azure primeiro e, em seguida, use o modulo [Leitor][reader] para colocá-los em seu experimento.
<!--
<SEE CLOUD DS PROCESS>
-->

**Há uma funcionalidade interna de entrada de imagem?**

Você pode aprender sobre a funcionalidade de entrada de imagem na referência [Leitor de Imagem][image-reader].

### Módulos

**O algoritmo, a fonte de dados, o formato de dados ou a operação de transformação de dados que estou procurando não está no Estúdio AM do Azure. Quais são minhas opções?**

É possível visitar o [fórum de feedback do usuário](http://go.microsoft.com/fwlink/?LinkId=404231) para ver solicitações de recurso que estamos acompanhando. Inclua seu voto nesta solicitação se um recurso que está procurando já tiver sido solicitado. Se o recurso que está procurando não existir, crie uma nova solicitação. É possível exibir o status de sua solicitação neste fórum também. Acompanhamos esta lista de perto e atualizamos o status da disponibilidade do recurso com frequência. Além do suporte interno para R e Python, transformações personalizadas podem ser criadas conforme necessário.


**Posso trazer meu código existente para o Estúdio AM?**

Sim, é possível levar seus códigos existentes do R para o Estúdio AM e executá-los no mesmo teste com os aprendizes fornecidos pelo Aprendizado de Máquina do Azure e implantá-los como um serviço Web por meio do Aprendizado de Máquina do Azure. Consulte [Estender seu experimento com R](machine-learning-extend-your-experiment-with-r.md).

**É possível usar algo como um [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir um modelo?**

Não, não há suporte para isso. No entanto, códigos personalizados em R e Python podem ser usados para definir um módulo.


### Processamento de dados

**Há uma capacidade de visualizar dados (além de visualizações de R) interativamente dentro do experimento?**

Clicando na saída de um módulo, você pode visualizar os dados e obter estatísticas.

**Ao visualizar resultados ou dados no navegador, o número de linhas e colunas é limitado, por que?**

Como os dados são transmitidos ao navegador e podem ser grandes, o tamanho dos dados é limitado para evitar lentidão do Estúdio AM. É melhor baixar os dados/resultados e usar o Excel ou outra ferramenta para visualizar todos os dados.

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

Atualmente, o Estúdio de Aprendizado de Máquina dá suporte a mais de 400 pacotes de R e essa lista continua crescendo. Consulte [Ampliar seu experimento com R](machine-learning-extend-your-experiment-with-r.md) para saber como obter uma lista dos pacotes de R com suporte. Se o pacote que desejar não estiver nesta lista, forneça o nome do pacote no [fórum de feedback do usuário](http://go.microsoft.com/fwlink/?LinkId=404231).

**É possível criar um módulo personalizado em R?**

Sim, consulte [Criar módulos personalizados em R no Aprendizado de Máquina do Azure](machine-learning-custom-r-modules.md) para saber mais.

**Há um ambiente REPL para R?**

Não, não há um ambiente REPL para R no estúdio.

### Módulo de Python

**É possível criar um módulo personalizado em Python?**

Não atualmente, mas com o módulo Python padrão ou um conjunto deles, o mesmo resultado pode ser obtido.

**Há um ambiente REPL para Python?**

Você pode usar os blocos de notas Jupyter no Estúdio de aprendizado de máquina. Para saber mais, consulte [Introdução aos blocos de notas Jupyter no Estúdio AM do Azure](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)

## Serviço Web

###Treinando modelos programaticamente

**Como posso treinar novamente modelos do AzureML programaticamente?** Use as APIs de novos treinamentos. Há código de exemplo disponível [aqui](https://azuremlretrain.codeplex.com/).

### Criar

**Posso implantar o modelo localmente ou em um aplicativo sem conexão à Internet?** Nº


**Há uma latência de linha de base que é esperada para todos os serviços Web?**

Consulte [Limites da assinatura do Azure](../azure-subscription-service-limits.md)

### Uso

**Quando seria melhor executar meu modelo de previsão como um serviço de Execução em Lotes em vez de um serviço de Resposta da Solicitação?**

O Serviço de Solicitação-Resposta (RRS) é um serviço Web de baixa latência e alta escala usado para fornecer uma interface para modelos sem monitoração de estado que são criados e implantados por meio do ambiente de testes. O BES (Serviço de Execução em Lote) é um serviço para classificar de forma assíncrona um lote de registros de dados. A entrada para o BES é semelhante à entrada de dados usada no RRS. A principal diferença é que o BES lê um bloco de registros de uma variedade de fontes, como os serviços de Blob e Tabela do Azure, o Banco de Dados SQL do Azure, o HDInsight (consulta de Hive) e fontes de HTTP. Para saber mais, consulte [Como consumir serviços Web do Aprendizado de Máquina](machine-learning-consume-web-services.md).

**Como posso atualizar o modelo para o serviço Web implantado?**

Atualizar um modelo preditivo para um serviço já implantado é tão simples quanto modificar e executar novamente o experimento usado para criar e salvar o modelo treinado. Uma vez que tiver uma nova versão do modelo treinado disponível, o Estúdio AM perguntará se você deseja atualizar seu serviço Web de preparação. Depois que a atualização é aplicada ao serviço Web de preparo, a mesma atualização ficará disponível para ser aplicada também ao serviço Web de produção. Consulte [Implantar um serviço Web do Aprendizado de Máquina](machine-learning-publish-a-machine-learning-web-service.md) para obter detalhes sobre como atualizar um serviço Web implantado.

Você também pode usar as APIs de novos treinamentos. O código de exemplo está disponível [aqui](https://azuremlretrain.codeplex.com/).

**Como posso monitorar meu serviço Web implantado na produção?**

Após um modelo de previsão ser colocado em produção, você pode monitorá-lo no portal do Azure. Cada serviço implantado tem seu próprio painel, onde você pode ver informações de monitoramento do serviço.

**Existe um lugar onde posso ver a saída do meu RRS/BES?**

Para RRS, a resposta do serviço Web é normalmente onde você vê o resultado. Você também pode gravá-la em um blob. Para BES, a saída é gravada em um blob, por padrão. Você também pode gravar a saída em um banco de dados ou tabela usando o módulo Gravador.

 ** Posso criar serviços Web apenas de modelos criados no Estúdio? 
Não. Você também pode criar serviços Web diretamente no bloco de notas Jupyter e no RStudio.

## Escalabilidade

**Qual é a escalabilidade do serviço Web?**

Atualmente, o ponto de extremidade padrão é provisionado com 20 solicitações RRS simultâneas por ponto de extremidade. Você pode dimensionar as solicitações simultâneas a 200 solicitações por ponto de extremidade e é possível dimensionar cada serviço Web em 10.000 pontos de extremidade, conforme descrito no artigo [pontos de extremidade de API de dimensionamento](machine-learning-scaling-endpoints.md). Para BES, cada ponto de extremidade permite processar 40 solicitações por vez e solicitações adicionais, além de 40 solicitações que são enfileiradas. Essas solicitações enfileiradas serão executadas automaticamente conforme a fila anda.


**Trabalhos em R são distribuídos entre nós?**

Nº


**Qual é a quantidade de dados com que posso treinar?**

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
- Validação Cruzada, Parâmetros de Varredura, Regressão Ordinal e Classes múltiplas, um versos todos, quando o número de iterações é muito grande.

Para conjuntos com mais de alguns GB, você deve fazer upload de dados para o armazenamento do Azure ou Banco de Dados SQL do Azure ou usar o HDInsight, em vez de fazer upload diretamente do arquivo local.


**Há qualquer limitação de tamanho de vetores?**

Linhas e colunas são têm a limitação do .NET de Int Máximo: 2.147.483.647.

**O tamanho da VM em que ele está sendo executado em pode ser ajustado?**

Nº

## Segurança e disponibilidade

**Quem tem acesso ao ponto de extremidade http para o serviço Web implantado na produção por padrão? Como fazer para restringir o acesso ao terminal?**

Depois que um serviço Web for implantado, criamos um ponto de extremidade padrão para esse serviço. O ponto de extremidade padrão é implantado na produção e pode ser chamado usando sua chave de API. Pontos de extremidade extras podem ser adicionados com suas próprias chaves do Portal do Azure ou programaticamente usando as APIs de gerenciamento de serviço Web. Elas são necessárias para fazer chamadas ao serviço Web em produção e preparação. Para saber mais, consulte [Conectar a um serviço Web do Aprendizado de Máquina](machine-learning-connect-to-azure-machine-learning-web-service.md).


**O que acontece se minha Conta de Armazenamento não puder ser encontrada?**

O Estúdio de Aprendizado de Máquina depende de uma Conta de Armazenamento do Azure fornecida pelo usuário para salvar dados intermediários ao executar o fluxo de trabalho. Esta Conta de Armazenamento é fornecida ao Estúdio de Aprendizado de Máquina no momento em um espaço de trabalho é criado. Após o espaço de trabalho ser criado, se a Conta de armazenamento for excluída e não puder mais ser encontrada, o espaço de trabalho deixará de funcionar e todos os experimentos nele falharão.

Se você excluir acidentalmente a Conta de armazenamento, a única maneira de recuperar é recriar essa Conta de Armazenamento exatamente com o mesmo nome e exatamente na mesma Região que a conta excluída. Depois disso, sincronize novamente a Chave de Acesso.


**O que acontece se a Chave de Acesso da minha Conta de Armazenamento não estiver sincronizada?** O Estúdio de Aprendizado de Máquina depende de uma Conta de Armazenamento do Azure fornecida pelo usuário para salvar dados intermediários ao executar o fluxo de trabalho. Esta Conta de Armazenamento é fornecida ao Estúdio de Aprendizado de Máquina no momento em um espaço de trabalho é criado e as Chaves de Acesso são associadas a esse espaço de trabalho. Após o espaço de trabalho ser criado, se as Chaves de Armazenamento foram alteradas, o espaço de trabalho não poderá mais acessar a Conta de Armazenamento, deixará de funcionar e todos os experimentos nele falharão.

Se você tiver alterado as Chaves de Acesso da Conta de Armazenamento, certifique-se de ressincronizar as Chaves de Acesso na configuração do espaço de trabalho no Portal do Azure


## Azure Marketplace

Consulte as [Perguntas frequentes sobre a publicação e utilização de aplicativos no Marketplace do Aprendizado de Máquina](machine-learning-marketplace-faq.md)

## Suporte e treinamento

**Onde posso obter treinamento para o AM do Azure?**

O [Centro de Documentação do Aprendizado de Máquina do Azure](/services/machine-learning/) hospeda tutoriais em vídeo e guias de instruções. Esses guias passo a passo fornecem uma introdução aos serviços e explicam o ciclo de vida de ciência dos dados da importação de dados, limpeza de dados, criação de modelos preditivos e implantação deles na produção com o AM do Azure.

Incluiremos novo material continuamente no Centro de Aprendizado de Máquina. É possível enviar solicitações de material de aprendizado adicional no Centro de Aprendizado de Máquina no [fórum de feedback do usuário](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Você também pode encontrar treinamento na [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning)

**Como fazer para obter suporte ao Aprendizado de Máquina do Azure?**

Para obter suporte técnico para o Aprendizado de Máquina do Azure, vá até [Suporte do Azure](/support/options/) e selecione **Aprendizado de Máquina**.

O Aprendizado de Máquina do Azure também possui um fórum de comunidade no MSDN, em que é possível fazer questões relacionadas ao AM do Azure. O fórum é monitorado pela equipe do AM do Azure. Visite o [Fórum do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=Nov15_HO4-->