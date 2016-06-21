<properties
	pageTitle="Como um modelo do Aprendizado de Máquina evolui de um teste para um serviço Web operacionalizado | Microsoft Azure"
	description="Uma visão geral da mecânica de como seu modelo do Aprendizado de Máquina do Azure evolui de um teste de desenvolvimento para um serviço Web operacionalizado."
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
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="garye"/>


# Como um modelo de Aprendizado de Máquina evolui de um experimento para um serviço Web operacionalizado

Um ***experimento*** é uma tela no Estúdio de Aprendizado de Máquina do Azure que permite desenvolver, executar, testar e iterar enquanto você cria um modelo de análise preditiva interativamente. Há uma grande variedade de módulos disponíveis que você pode usar para transferir dados para seu experimento, manipular os dados, treinar um modelo usando algoritmos de aprendizado de máquina, pontuar o modelo, avaliar os resultados e os valores finais de saída.

Quando estiver satisfeito com seu experimento, você poderá implantá-lo como um ***serviço Web do Azure*** para que os usuários possam enviá-lo novos dados e receber resultados.

Neste artigo, ofereceremos uma visão geral sobre a mecânica de como seu modelo de Aprendizado de Máquina evolui de um experimento de desenvolvimento para um serviço Web operacional.

>[AZURE.NOTE] Há outras maneiras de desenvolver e implantar modelos de aprendizado de máquina, mas este artigo se concentra em como usar o Estúdio de Aprendizado de Máquina. Para uma discussão sobre como criar um serviço Web de previsão com R, consulte a postagem do blog [Criar e implantar aplicativos Web de previsão usando o RStudio e o Aprendizado de Máquina do Azure](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).

Embora o Estúdio de Aprendizado de Máquina do Azure seja projetado principalmente para ajudar você a desenvolver e implantar um *modelo de análise preditiva*, é possível usar o Estúdio para desenvolver um experimento que não inclua um modelo de análise preditiva. Por exemplo, um experimento pode simplesmente inserir dados, manipulá-los e gerar os resultados. Assim como um experimento de análise preditiva, você pode implantar esse teste não preditivo como um serviço Web, mas esse é um processo mais simples porque o teste não está treinando ou pontuando um modelo de aprendizado de máquina. Embora não seja o uso normal do Estúdio, o incluiremos na discussão abaixo para que possamos dar uma explicação completa de como funciona o Estúdio.

## Desenvolvendo e implantando um serviço Web preditivo

Estes são os estágios pelos quais passa uma solução típica quando você a desenvolve e implanta usando o Estúdio de Aprendizado de Máquina:

![](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figura 1 - Estágios de um modelo típico de análise preditiva*

### O teste de treinamento

O ***teste de treinamento*** é a tela de teste inicial no Estúdio de Aprendizado de Máquina. A finalidade do teste de treinamento é fornecer a você um lugar para desenvolver, testar, iterar e, eventualmente, treinar um modelo de aprendizado de máquina. Você pode até mesmo treinar vários modelos simultaneamente já que procura pela melhor solução, mas assim que terminar de testar, você selecionará um único modelo treinado e eliminará o restante do experimento. Para obter um exemplo de como desenvolver um teste de análise preditiva, veja [Desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### O teste preditivo

Quando você já tiver um modelo treinado em seu teste de treinamento, clique em **Configurar Serviço Web** no Estúdio de Aprendizado de Máquina, e o Estúdio passará pelo processo de converter seu teste de treinamento em um ***teste preditivo***. A finalidade do experimento preditivo é usar o modelo treinado para pontuar novos dados, com o objetivo de se tornar posteriormente operacionalizado como um serviço Web do Azure.

Essa conversão é feita para você pelas seguintes etapas:

-   Converter o conjunto de módulos usados para treinamento em um único módulo e salvá-lo como um modelo treinado

-   Eliminar quaisquer módulos externos não relacionados à pontuação

-   Adicionar portas de entrada e saída que serão usadas pelo possível serviço Web

Pode haver mais alterações que você queira fazer para ter seu experimento preditivo pronto para ser implantado como um serviço Web. Por exemplo, se você quiser que o serviço Web gere apenas um subconjunto dos resultados, você pode adicionar um módulo de filtragem antes da porta de saída.

O teste de treinamento não é descartado neste processo de conversão. Quando o processo for concluído, você terá duas guias no Estúdio: uma para o teste de treinamento e outra para o teste preditivo. Dessa forma, antes de implantar o serviço Web, você poderá alterar o teste de treinamento e recriar o teste preditivo. Ou você pode salvar uma cópia do teste de treinamento para iniciar outra linha de teste.

>[AZURE.NOTE] Quando você clica em **Configurar Serviço Web**, um processo automático é iniciado para converter seu teste de treinamento em um teste preditivo, e isso funciona bem na maioria dos casos. Mas se o seu teste de treinamento for complexo (por exemplo, se você tiver vários caminhos para treinamento reunidos), convém fazer essa conversão manualmente. Para obter mais detalhes sobre como esse processo de conversão funciona, veja [Converter um teste de treinamento do Aprendizado de Máquina em um teste preditivo](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### O serviço Web

Quando já estiver satisfeito com seu experimento preditivo, clique em **Implantar Serviço Web** para tornar seu modelo operacional, implantando-o como um ***Serviço Web do Azure***. Os usuários podem agora enviar dados para seu modelo usando a API REST do serviço Web e receber os resultados de volta. Para obter mais informações sobre como fazer isso, veja [Como consumir um serviço Web do Aprendizado de Máquina do Azure implantado de um teste do Aprendizado de Máquina](machine-learning-consume-web-services.md).

Após a implantação do serviço Web, o experimento preditivo e o serviço Web permanecerão conectados, e você poderá se movimentar entre eles:

|***Nesta página...***|***clique aqui...***|***para abrir esta página...***|
| ------------------- | --------------- | ---------------------- |
|tela de experimento do Estúdio|**Vá para o serviço Web**|configuração de serviço Web no Estúdio|
|configuração de serviço Web no Estúdio|**Exibir mais recente**|tela de experimento do Estúdio|
|configuração de serviço Web no Estúdio|**Gerenciar pontos de extremidade...**|gerenciamento de ponto de extremidade no Portal Clássico do Azure|
|gerenciamento de ponto de extremidade no Portal Clássico do Azure|**Editar no Estúdio**|tela de experimento do Estúdio|

![](media\machine-learning-model-progression-experiment-to-web-service\connections-between-experiment-and-web-service.png)

*Figura 2 - Conexões entre um experimento e o serviço Web*

## Caso não típico: criando um serviço Web não preditivo

Se o seu teste não treinar um modelo de análise preditiva, você não precisará criar um teste de treinamento e um teste de pontuação. Há apenas um teste, e você pode implantá-lo como um serviço Web (o Estúdio do Aprendizado de Máquina detecta se o teste contém um modelo preditivo analisando os módulos usados).

Depois que você tiver iterado no experimento e estiver satisfeito com ele:

1.  Clique em **Configurar Serviço Web** - nós de entrada e saída são adicionados automaticamente

2.  Clique em **Executar**

3.  Clique em **Implantar Serviço Web**

Seu serviço Web está agora implantado e você pode acessá-lo e gerenciá-lo como um serviço Web preditivo.

## Botões do serviço Web

No Estúdio de Aprendizado de Máquina, os botões do serviço da Web - **Configurar Serviço Web** e **Implantar Serviço Web** - têm nomes e funções diferentes dependendo de onde você está no processo de desenvolvimento.

**O experimento contém um modelo preditivo**

Se o experimento treinar e pontuar um modelo preditivo, os botões do serviço Web executarão estas funções:

|**Tipo de experimento**|**Botão**|**O que ele faz**|
| -------------------- | -------- | -------------- |
|Experimento em desenvolvimento|**Configurar Serviço Web**|Fornece duas opções|
|&nbsp;|- **Serviço Web Preditivo**|Converte o teste em um teste de treinamento e em um teste de pontuação|
|&nbsp;|- **Treinando novamente o Serviço Web**|Converte o teste em um teste de novo treinamento (consulte a seção "Atualizando" abaixo)|
|Experimento de treinamento|**Configurar Serviço Web**|Fornece duas opções|
|&nbsp;|- **Atualizar teste preditivo**|Atualiza o teste preditivo associado com as alterações feitas no teste de treinamento|
|&nbsp;|- **Treinando novamente o serviço Web**|Converte o teste de treinamento em um teste de novo treinamento (veja a seção “Atualizando” abaixo)|
|&nbsp;|- *ou* - **Implantar serviço Web**|Se você tiver configurado o teste de novo treinamento para implantação, ele será implantado como um serviço Web|
|Teste preditivo|**Implantar Serviço Web**|Implanta o teste preditivo como um serviço Web|

**O experimento *não* contém um modelo preditivo**

Se o teste não treinar nem pontuar um modelo preditivo, os botões do serviço Web serão muito mais simples:

|**Tipo de experimento**|**Botão**|**O que ele faz**|
| -------------------- | -------- | -------------- |
|Experimento em desenvolvimento|**Configurar Serviço Web**|Prepara o experimento para implantação como um Serviço Web|
|Experimento preparado para implantação|**Implantar Serviço Web**|Implanta o experimento como um serviço Web, abre a página de configuração do serviço Web|

## Atualização de seu serviço Web

Agora que você implantou seu experimento como um serviço Web, o que acontecerá se precisar atualizá-lo?

Depende do que você precisa atualizar:

**Para alterar a entrada ou saída, ou para modificar como o serviço Web manipula dados**

Se você não estiver alterando o modelo, mas estiver apenas alterando como o serviço Web manipula os dados, poderá editar o experimento preditivo e clicar em **Implantar Serviço Web** novamente. O serviço Web será parado, o experimento preditivo atualizado será implantado e o serviço Web será iniciado novamente.

Veja um exemplo: suponha que seu experimento preditivo retorne toda a linha de dados de entrada com o resultado previsto. Talvez você queira que o serviço Web retorne apenas o resultado. Adicione então um módulo **Selecionar Colunas no Conjunto de Dados** no experimento preditivo, logo antes da porta de saída, para excluir colunas que não sejam o resultado. Quando você clicar em **Implantar Serviço Web** novamente, o serviço Web será atualizado.

**Você quer treinar novamente o modelo com novos dados**

Se você desejar manter a modelo de aprendizado de máquina, mas se quiser treiná-lo novamente com novos dados, terá duas opções:

1.  **Treinar novamente o modelo enquanto o serviço Web está em execução** -se você quiser treinar novamente seu modelo enquanto o serviço Web preditivo estiver em execução, poderá fazer isso fazendo algumas modificações no teste de treinamento para transformá-lo em um ***teste de novo treinamento*** e poderá implantá-lo como um ***serviço Web de novo treinamento***. Para obter instruções sobre como fazer isso, veja [Treinar novamente os modelos do Aprendizado de Máquina de forma programática](machine-learning-retrain-models-programmatically.md).

2.  **Volte para o teste de treinamento original e use dados de treinamento diferentes para desenvolver seu modelo** - seu teste preditivo está vinculado ao serviço Web, mas o teste de treinamento não é diretamente vinculado dessa maneira. Se você modificar o teste de treinamento original e clicar em **Configurar Serviço Web**, será criado um *novo* teste preditivo que, quando implantado, criará um *novo* serviço Web. Isso não atualiza simplesmente o serviço Web original.

    Portanto, se você precisar modificar o teste de treinamento, abra-o e clique em **Salvar como** para fazer uma cópia. Isso deixará intacto o teste de treinamento original, o experimento preditivo e o serviço Web. Agora você pode criar um novo serviço Web com suas alterações. Depois de implantar o novo serviço Web, você poderá decidir se deseja interromper o serviço Web anterior ou mantê-lo em execução juntamente com o novo.

**Você deseja treinar um modelo diferente**

Se você deseja fazer alterações em seu teste preditivo original, como selecionar um algoritmo de aprendizado de máquina diferente, tentar um método diferente de treinamento etc., siga o segundo procedimento descrito acima para treinar novamente seu modelo: abra o teste de treinamento, clique em **Salvar como** para fazer uma cópia e inicie o novo caminho de desenvolvimento de modelo, criando o teste preditivo e implantando o serviço Web. Isso criará um novo serviço Web não relacionado ao original - você pode decidir qual deles, ou ambos, continuará em execução.

## Leitura adicional

Para obter mais detalhes sobre esse processo, consulte os seguintes artigos:

-   convertendo o teste - [Converter um teste de treinamento do Aprendizado de Máquina em um teste preditivo](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   implantando o serviço Web - [Implantar um serviço Web do Aprendizado de Máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md)

-   treinando novamente o modelo - [Treinar novamente os modelos do Aprendizado de Máquina de forma programática](machine-learning-retrain-models-programmatically.md)

Para obter exemplos do processo inteiro, consulte:

-   [Tutorial de aprendizado de máquina: Crie sua primeira experiência no Studio de Aprendizado de Máquina do Azure](machine-learning-create-experiment.md)

-   [Passo a passo: Desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)

<!---HONumber=AcomDC_0608_2016-->