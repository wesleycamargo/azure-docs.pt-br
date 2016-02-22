<properties
	pageTitle="Etapa 5: Implantar o serviço Web de Aprendizado de Máquina | Microsoft Azure"
	description="Etapa 5 do passo-a-passo Desenvolver uma solução preditiva: implantar um teste preditivo no Estúdio de Aprendizado de Máquina como um serviço Web."
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
	ms.date="02/03/2016"
	ms.author="garye"/>


# Etapa 5 do passo-a-passo: Implantar o serviço Web de Aprendizado de Máquina do Azure

Esta é a quinta etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Implantar o serviço Web**
6.	[Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para que outras pessoas possam usar o modelo de previsão que desenvolvemos neste passo a passo, nós o implantaremos um serviço Web no Azure.

Até este ponto, estávamos testando nosso modelo de treinamento. Mas o serviço implantado não será mais para treinamento — ele vai gerar previsões pontuando a entrada do usuário com base em nosso modelo. Então, vamos fazer algumas preparações para converter esse teste de um teste de ***treinamento*** para um teste de ***previsão***.

Este é um processo em duas etapas:

1. Converter o *teste de treinamento* que criamos em um *teste preditivo*
2. Implantar o teste preditivo como um serviço Web

Mas, primeiro, precisamos reduzir esse teste um pouco. No momento, temos dois modelos diferentes no teste, mas queremos apenas um modelo quando implantarmos como um serviço Web.

Digamos que decidimos que o modelo de árvore aumentada foi o melhor modelo a usar. Portanto, a primeira coisa a fazer é remover o módulo [Computador de vetor de suporte de duas classes][two-class-support-vector-machine] e os módulos que foram usados para treiná-lo. Talvez você queira fazer uma cópia do teste clicando primeiro em **Salvar como** na parte inferior da tela do teste.

É preciso excluir os seguintes módulos:

- [Computador de vetor de suporte de duas classes][two-class-support-vector-machine]
- Módulos [Treinar modelo][train-model] e [Modelo de pontuação][score-model] que foram conectados a ele
- [Normalizar dados][normalize-data] (ambos)
- [Avaliar modelo][evaluate-model]

Selecione o módulo e pressione a tecla Delete, ou clique com o botão direito do mouse no módulo e selecione **Excluir**.

Agora, estamos prontos para implantar esse modelo usando a [Árvore de Decisão Aumentada em Duas Classes][two-class-boosted-decision-tree].

## Converter o teste de treinamento em um teste preditivo

Fazer a conversão em um teste preditivo envolve três etapas:

1. Salvar o modelo com que treinamos e substituir nossos módulos de treinamento
2. Cortar o teste para remover os módulos necessários somente para treinamento
3. Definir onde o serviço Web aceitará entrada e onde gerará saída

Felizmente, as três etapas podem ser realizadas apenas clicando em **Implantar Serviço Web** na parte inferior da tela de teste (selecione a opção **Serviço Web Preditivo**).

Quando você clica em **Implantar Serviço Web**, várias coisas acontecem:

- O modelo que treinamos é salvo como um único módulo **Modelo treinado** no palete de módulo à esquerda da tela de teste (você o encontrará em **Modelos treinados**).
- Os módulos que foram usados para treinamento são removidos. Especificamente:
  - [Árvore de decisão aumentada em duas classes][two-class-boosted-decision-tree]
  - [Modelo de treinamento][train-model]
  - [Dados Divididos][split]
  - o segundo módulo [Executar Script R][execute-r-script] que foi usado para dados de teste
- O modelo treinado salvo é adicionado de volta ao teste.
- Os módulos **Entrada de serviço Web** e **Saída de serviço Web** são adicionados.

> [AZURE.NOTE] O teste foi salvo em duas partes em guias que foram adicionados na parte superior da tela do teste: o teste de treinamento original está sob a guia **Teste de treinamento** e o teste de previsão recém-criado está em **Teste de previsão**.

É necessário executar uma etapa adicional com esse teste específico. Adicionamos dois módulos [Executar Script R][execute-r-script] para fornecer uma função importante aos dados de treinamento e teste. Não precisamos fazer isso no modelo final. O Estúdio de Aprendizado de Máquina removeu um módulo [Executar Script R][execute-r-script] quando removeu o Módulo [Dividir][split], portanto, agora podemos remover o outro e conectar o [Editor de Metadados][metadata-editor] diretamente ao [Modelo de Pontuação][score-model].

Agora o teste deve se parecer como isto:

![Pontuando o modelo treinado][4]


> [AZURE.NOTE] Você pode estar se perguntando por que deixamos o conjunto de dados de Dados de cartão de crédito alemão UCI no teste preditivo. O serviço usará os dados do usuário, não o conjunto de dados original, portanto, porque deixar o conjunto de dados original no modelo?
>
>É verdade que o serviço não precisa dos dados de cartão de crédito originais. Mas ele precisa do esquema para esses dados, que inclui informações como quantas colunas existem e quais colunas são numéricas. Essas informações de esquema são necessárias para interpretar os dados do usuário. Deixamos esses componentes conectados para que o módulo de pontuação tenha o esquema de conjunto de dados quando o serviço estiver sendo executado. Os dados não são usados, somente o esquema.

Execute o teste uma última vez (clique em **Executar**). Se você deseja verificar se o modelo ainda está funcionando, clique na saída do módulo [Modelo de Pontuação][score-model] e selecione **Exibir Resultados**. Você verá os dados originais exibidos, juntamente com o valor de risco de crédito ("Rótulos de pontuação") e o valor de probabilidades de pontuação ("Probabilidades de pontuação").

## Implantar o serviço Web

Para implantar um serviço Web derivado de nosso teste, clique em **Implantar Serviço Web** abaixo da tela. O Estúdio de Aprendizado de Máquina implanta o teste como um serviço Web e leva você até o painel para o serviço Web. Desse momento em diante, você pode retornar ao teste (**Exibir instantâneo** ou **Exibir mais recente**) e executar um teste simples do serviço Web (botão **Testar** - consulte **Testar o serviço Web** abaixo). Há também informações para a criação de aplicativos que podem acessar o serviço Web (mais sobre isso na próxima etapa deste passo a passo).

![Painel de serviço Web][6]

> [AZURE.TIP] Você pode atualizar o serviço Web depois de implantá-lo. Por exemplo, se você quiser alterar o modelo, basta editar o teste de treinamento, ajustar os parâmetros de modelo e clicar em **Implantar Serviço Web**. Quando você implantar o teste novamente, ele substituirá o serviço Web, agora usando seu modelo atualizado.

Você pode configurar o serviço clicando na guia **CONFIGURAÇÃO**. Assim, você pode modificar o nome do serviço (é dado o nome do teste por padrão) e fornecer uma descrição para ele. Também pode adicionar rótulos mais simpáticos para as colunas de entrada e saída.

![Configurar o serviço Web][5]

## Teste do serviço Web
Na página **PAINEL**, clique no botão **Testar** em **Ponto de extremidade padrão**. Um diálogo aparecerá e solicitará dados de entrada para o serviço. Existem as mesmas colunas que apareciam no conjunto de dados de risco de crédito alemão original.

Insira um conjunto de dados e clique em **OK**.

No serviço Web, os dados entram por meio do módulo **Entrada do serviço Web**, por meio do módulo [Editor de Metadados][metadata-editor] e para o [Modelo de Pontuação][score-model], onde é pontuado. Os resultados saem, então, do serviço Web por meio da **saída do serviço Web**.

> [AZURE.TIP] Da maneira como configuramos o teste de previsão, todos os resultados do módulo [Modelo de Pontuação][score-model] são retornados. Isso inclui todos os dados de entrada, o valor do risco de crédito e a probabilidade de pontuação. Se deseja retornar algo diferente - por exemplo, apenas o valor do risco de crédito-, você pode inserir um módulo [Colunas do Projeto][project-columns] entre [Modelo de Pontuação][score-model] e a **Saída do serviço Web** para eliminar colunas que você não deseja que o serviço Web retorne.

## Gerenciar o serviço Web
Assim que tiver implantado o serviço Web, você pode gerenciá-lo do [portal clássico do Azure](https://manage.windowsazure.com).

1. Entre no [Portal clássico do Azure](https://manage.windowsazure.com).
2. No painel de serviços do Microsoft Azure, clique em **APRENDIZADO DE MÁQUINA**.
3. Clique no espaço de trabalho.
4. Clique na guia **SERVIÇOS WEB**.
5. Clique no serviço Web que acabamos de criar.
6. Clique no ponto de extremidade "padrão".

A partir daqui, você pode fazer coisas como monitorar como o serviço Web está se saindo e fazer ajustes de desempenho alterando quantas chamadas simultâneas o serviço pode processar. Você pode até mesmo publicar seu serviço Web no Azure Marketplace.

Para obter mais informações, consulte:

- [Criando pontos de extremidade](machine-learning-create-endpoint.md)
- [Dimensionando serviço Web](machine-learning-scaling-webservice.md)
- [Publicar o Serviço Web do Aprendizado de Máquina do Azure no Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

----------

**Em seguida: [acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)**

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
[project-columns]: https://msdn.microsoft.com/pt-BR/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

<!---HONumber=AcomDC_0211_2016-->