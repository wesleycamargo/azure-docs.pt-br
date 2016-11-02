<properties
    pageTitle="Etapa 5: Implantar o serviço Web do Machine Learning | Microsoft Azure"
    description="Etapa 5 de Desenvolver um passo a passo da solução preditiva: Implantar um teste preditivo no Machine Learning Studio como um serviço Web."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="garye"/>



# <a name="walkthrough-step-5:-deploy-the-azure-machine-learning-web-service"></a>Etapa 5 do passo a passo: Implantar o serviço Web do Azure Machine Learning

Esta é a quinta etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  **Implantar o serviço Web**
6.  [Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para que outras pessoas possam usar o modelo de previsão que desenvolvemos neste passo a passo, nós o implantaremos como um serviço Web no Azure.

Até este ponto, estávamos testando nosso modelo de treinamento. Mas o serviço implantado não fará mais o treinamento – ele gera previsões pontuando a entrada do usuário com base em nosso modelo. Então, vamos fazer algumas preparações para converter esse teste de um teste de ***treinamento*** em um teste ***preditivo***. 

É um processo de duas etapas:  

1. Converter o *teste de treinamento* que criamos em um *teste preditivo*
2. Implantar o teste preditivo como um serviço Web

Mas, primeiro, precisamos reduzir esse teste um pouco. No momento, temos dois modelos diferentes no teste, mas queremos apenas um modelo ao implantar isso como um serviço Web.  

Digamos que decidimos que o modelo de árvore aumentada foi o melhor modelo a usar. Portanto, a primeira coisa a fazer é remover o módulo [Computador de vetor de suporte de duas classes][two-class-support-vector-machine] e os módulos usados para treinamento. Talvez você queira fazer uma cópia do teste clicando primeiro em **Salvar como** na parte inferior da tela do teste.

É preciso excluir os seguintes módulos:  

- [Máquina de vetor de suporte de duas classes][two-class-support-vector-machine]
- Módulos [Modelo de treinamento][train-model] e [Modelo de pontuação][score-model] que estavam conectados a ele
- [Normalizar dados][normalize-data] (ambos)
- [Avaliar modelo][evaluate-model]

Selecione o módulo e pressione a tecla Excluir, ou clique com o botão direito do mouse no módulo e selecione **Excluir**.

Agora estamos prontos para implantar esse modelo usando a [Árvore de decisão aumentada duas classes][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o teste de treinamento em um teste preditivo

Fazer a conversão em um teste preditivo envolve três etapas:

1. Salvar o modelo com que treinamos e substituir nossos módulos de treinamento
2. Cortar o teste para remover os módulos necessários somente para treinamento
3. Definir onde o serviço Web aceitará entrada e onde ele gera a saída

Felizmente, as três etapas podem ser realizadas apenas clicando em **Configurar serviço Web** na parte inferior da tela de teste (selecione a opção **Serviço Web preditivo**).

Quando você clica em **Instalar serviço Web**, várias coisas acontecem:

- O modelo treinado é salvo como um único módulo **Modelo treinado** na paleta de módulos à esquerda da tela de teste (você o encontrará em **Modelos Treinados**).
- Os módulos que foram usados para treinamento são removidos. Especificamente:
  - [Arvore de decisão aumentada duas classes][two-class-boosted-decision-tree]
  - [Treinar modelo][train-model]
  - [Dividir dados][divide]
  - o segundo módulo [Executar Script R][execute-r-script] usado para dados do teste
- O modelo treinado salvo é adicionado de volta ao teste.
- Os módulos **Entrada de serviço Web** e **Saída de serviço Web** são adicionados.

> [AZURE.NOTE] O teste foi salvo em duas partes em guias que foram adicionadas na parte superior da tela de teste: o teste de treinamento original está na guia **Teste de treinamento** e o teste preditivo recém-criado está em **Teste preditivo**.

É necessário executar uma etapa adicional com esse teste específico.
Adicionamos dois módulos [Executar Script R][execute-r-script] para fornecer uma função importante aos dados de treinamento e teste. Não precisamos fazer isso no modelo final.

O Machine Learning Studio removeu um módulo [Executar Script R][execute-r-script] quando ele removeu o módulo [Divisão][split]. Agora, podemos remover o outro e conectar o [Editor de metadados][metadata-editor] diretamente ao [Modelo de pontuação][score-model].    

Agora o teste deve se parecer como isto:  

![Pontuando o modelo treinado][4]  

> [AZURE.NOTE] Você pode estar se perguntando por que deixamos o conjunto de dados de Dados de cartão de crédito alemão UCI no teste preditivo. O serviço usará os dados do usuário, não o conjunto de dados original, portanto, porque deixar o conjunto de dados original no modelo?
>
>É verdade que o serviço não precisa dos dados de cartão de crédito originais. Mas ele precisa do esquema para esses dados, que inclui informações como quantas colunas existem e quais colunas são numéricas. Essas informações de esquema são necessárias para interpretar os dados do usuário. Deixamos esses componentes conectados para que o módulo de pontuação tenha o esquema de conjunto de dados quando o serviço estiver sendo executado. Os dados não são usados, somente o esquema.  

Execute o teste uma última vez (clique em **Executar**). Se você quiser verificar se o modelo ainda está funcionando, clique na saída do módulo [Modelo de pontuação][score-model] e selecione **Exibir Resultados**. Você verá que os dados originais são exibidos, juntamente com o valor de risco de crédito ("Rótulos de pontuação") e o valor de probabilidade de pontuação ("Probabilidades de pontuação"). 

## <a name="deploy-the-web-service"></a>Implantar o serviço Web

Você pode implantar o teste como um serviço Web clássico ou um novo serviço Web com base no Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Implantar como serviço Web clássico ###

Para implantar um serviço Web clássico derivado do teste, clique em **Implantar Serviço Web** abaixo da tela e selecione **Implantar Serviço Web [clássico]**. O Machine Learning Studio implanta o teste como um serviço Web e leva você até o painel desse serviço Web. Desse momento em diante, você pode retornar ao teste (**Exibir instantâneo** ou **Exibir mais recente**) e executar um teste simples do serviço Web (veja **Testar o serviço Web** abaixo). Há também informações para a criação de aplicativos que podem acessar o serviço Web (mais sobre isso na próxima etapa deste passo a passo).

![Painel de serviço Web][6]

Você pode configurar o serviço clicando na guia **CONFIGURAÇÃO** . Assim, você pode modificar o nome do serviço (é dado o nome do teste por padrão) e fornecer uma descrição para ele. Também pode adicionar rótulos mais simpáticos para as colunas de entrada e saída.  

![Configurar o serviço Web][5]  

### <a name="deploy-as-a-new-web-service"></a>Implantar como um novo serviço Web

Para implantar um novo serviço Web derivado do teste, clique em **Implantar Serviço Web** abaixo da tela e **Implantar Serviço Web [novo]**. O Machine Learning Studio transfere para a página de teste de implantação de serviços Web do Azure Machine Learning.

Insira um nome para o serviço Web e selecione um plano de preços. Se você tiver um plano de preços existente, selecione-o, caso contrário, você deverá criar um novo plano de preços para o serviço. 

1.  Na lista suspensa **Plano de Preços**, selecione um plano existente ou selecione a opção **Selecionar novo plano**.
2.  Em **Nome do Plano**, digite um nome que identifique o plano na conta.
3.  Selecione uma dos **Níveis de Planos Mensais**. As camadas do plano usam como padrão os planos da região padrão e o serviço Web é implantado nessa região.

Clique em **Implantar** e a página **Início Rápido** do serviço Web é exibida.

Você pode configurar o serviço clicando na opção de menu **Configurar** . Assim, você pode modificar o nome do serviço (é dado o nome do teste por padrão) e fornecer uma descrição para ele. 

Para testar a seleção do serviço Web, clique na opção de menu **Testar** (veja **Testar o serviço Web** abaixo). Para obter informações sobre a criação de aplicativos que podem acessar o serviço Web, clique na opção de menu **Consumir** (mais sobre isso na próxima etapa deste passo a passo).

> [AZURE.TIP] Você pode atualizar o serviço Web depois de implantá-lo. Por exemplo, se você quiser alterar o modelo, edite o teste de treinamento, ajuste os parâmetros de modelo e clique em **Implantar Serviço Web**. Em seguida, selecione **Implantar Serviço Web [clássico]** ou **Implantar Serviço Web [novo]**. Quando você implantar o teste novamente, ele substituirá o serviço Web, agora usando o modelo atualizado.  

## <a name="test-the-web-service"></a>Testar o serviço Web

### <a name="test-a-classic-web-service"></a>Implantar um serviço Web clássico

Você pode testar o serviço no Machine Learning Studio ou no portal de serviços Web do Azure Machine Learning. Testes no portal de serviços Web do Azure Machine Learning têm a vantagem de permitir que você habilite 

**Testar no Machine Learning Studio**

Na página **PAINEL**, clique no botão **Testar** em **Ponto de extremidade padrão**. Um diálogo aparecerá e solicitará dados de entrada para o serviço. Existem as mesmas colunas que apareciam no conjunto de dados de risco de crédito alemão original.  

Insira um conjunto de dados e clique em **OK**. 

**Testar no portal de serviços Web do Azure Machine Learning**

Na página **PAINEL**, clique no link da visualização **Testar** em **Ponto de extremidade padrão**. A página de teste no portal de serviços Web do Azure Machine Learning para o ponto de extremidade de serviço Web é aberta e solicita dados para o serviço. Existem as mesmas colunas que apareciam no conjunto de dados de risco de crédito alemão original.

Clique em **Solicitação-Resposta**. 

No serviço Web, os dados entram pelo módulo **Entrada de serviço Web** e o módulo [Editor de Metadados][metadata-editor] e até o módulo [Modelo de pontuação][score-model] em que ele é pontuado. Os resultados saem do serviço Web pela **saída do serviço Web**.

**Testar um novo serviço Web**

No portal de serviços Web do Azure Machine Learning, clique em **Testar** na parte superior da página. A página de **Teste** é aberta e você pode inserir dados para o serviço. Os campos de entrada exibidos correspondem às mesmas colunas que apareciam no conjunto de dados de risco de crédito alemão original. 

Insira um conjunto de dados e clique em **Testar solicitação-resposta**.

Os resultados do teste serão exibidos no lado direito da página na coluna de saída. 

Durante o teste no portal de serviços Web do Azure Machine Learning, você pode habilitar dados de exemplo e usá-los para testar o serviço de solicitação-resposta. Se você criou o serviço Web no Machine Learning Studio, os dados de exemplo serão retirados dos dados usados para treinar o modelo.

> [AZURE.TIP] Da maneira como configuramos o teste preditivo, todos os resultados do módulo [Modelo de Pontuação][score-model] são retornados. Isso inclui todos os dados de entrada, o valor do risco de crédito e a probabilidade de pontuação. Se você quiser retornar algo diferente (por exemplo, apenas o valor do risco de crédito), poderá inserir um módulo [Colunas do Projeto][project-columns] entre [Modelo de Pontuação][score-model] e a **Saída do serviço Web** para eliminar as colunas que você não quer que o serviço Web retorne. 

## <a name="manage-the-web-service"></a>Gerenciar o serviço Web

**Gerenciar um serviço Web clássico**

Assim que implantar o serviço Web clássico, poderá gerenciá-lo no [Portal clássico do Azure](https://manage.windowsazure.com).

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel de serviços do Microsoft Azure, clique em **APRENDIZADO DE MÁQUINA**.
3. Clique no espaço de trabalho.
4. Clique na guia **Serviços Web**.
5. Clique no serviço Web que criamos.
6. Clique no ponto de extremidade "padrão".

Aqui, você pode fazer coisas como monitorar como o serviço Web está se saindo e fazer ajustes de desempenho alterando quantas chamadas simultâneas o serviço pode processar.
Você pode até mesmo publicar seu serviço Web no Azure Marketplace.

Para obter mais informações, consulte:

- [Criando pontos de extremidade](machine-learning-create-endpoint.md)
- [Dimensionando o serviço Web](machine-learning-scaling-webservice.md)
- [Publicar o serviço Web do Azure Machine Learning no Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

**Gerenciar um serviço Web no portal de serviços Web do Azure Machine Learning**

Assim que você implantar o serviço Web, clássico ou novo, poderá gerenciá-lo no [portal de serviços Web do Azure Machine Learning](https://servics.azureml.net).

Para monitorar o desempenho do serviço Web:

1. Entre no [portal de serviços Web do Azure Machine Learning](https://servics.azureml.net).
2. Clique em **Serviços Web**.
3. Clique no seu serviço Web.
4. Clique em **Painel**.

----------

**Em seguida: [Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/


<!--HONumber=Oct16_HO2-->


