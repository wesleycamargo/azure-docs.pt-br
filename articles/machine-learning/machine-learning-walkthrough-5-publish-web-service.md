<properties 
	pageTitle="Etapa 5: publicar o serviço Web do Aprendizado de Máquina | Microsoft Azure" 
	description="Etapa 5 - desenvolver um passo a passo da solução de previsão: publicar um teste de pontuação no Estúdio de Aprendizado de Máquina como um serviço Web." 
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
	ms.date="07/10/2015" 
	ms.author="garye"/>


# Etapa 5 do passo a passo: publicar o serviço Web do Aprendizado de Máquina do Azure

Esta é a quinta etapa do passo a passo: [Desenvolvendo uma Solução Preditiva com o AM do Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Publicar o serviço Web**
6.	[Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para tornar esse modelo preditivo útil aos outros, o publicaremos como um serviço Web no Azure.

Até este ponto, estávamos testando nosso modelo de treinamento. Mas o serviço publicado não fará mais treinamento - ele fará pontuações das entradas do usuário. Vamos então nos preparar e, em seguida, publicar esse experimento como um serviço Web de trabalho que os usuários podem acessar. Um usuário poderá enviar ao serviço um conjunto de dados de aplicativo de crédito e o serviço retornará a previsão do risco de crédito.

Para fazer isso, você precisa:

- Converter o *teste de treinamento* que criamos em um *teste de pontuação*
- Publicar o experimento de pontuação como um serviço Web

Mas, primeiro, precisamos reduzir esse teste um pouco. No momento, temos dois modelos diferentes de experimento, mas agora precisamos selecionar um modelo para publicar.

Digamos que decidimos que o modelo de árvore aumentada foi o melhor modelo a usar. Portanto, a primeira coisa a fazer é remover o módulo [Computador de vetor de suporte de duas classes][two-class-support-vector-machine] e os módulos que foram usados para treiná-lo. Talvez você queira fazer uma cópia do teste clicando primeiro em **Salvar como** na parte inferior da tela do teste.

É preciso excluir os seguintes módulos:

1.	[Computador de vetor de suporte de duas classes][two-class-support-vector-machine]
2.	Módulos [Treinar modelo][train-model] e [Modelo de pontuação][score-model] que foram conectados a ele
3.	[Normalizar dados][normalize-data] (ambos)
4.	[Avaliar modelo][evaluate-model]

Agora estamos prontos para publicar este modelo.

##Converter o teste de treinamento para um teste de pontuação

Converter em um teste de pontuação envolve três etapas:

1. Salvar o modelo que treinamos e substituir os nossos módulos de treinamento por ele
2. Cortar o teste para remover os módulos necessários somente para treinamento
3. Definir onde os nós de entrada e saída do serviço Web devem estar

Felizmente, todas as três etapas podem ser feitas clicando em **Criar teste de pontuação** na parte inferior da tela de teste.

Quando você clica em **Criar teste de pontuação**, várias coisas acontecem:

- O modelo que treinamos é salvo como um módulo **Modelo treinado** no palete de módulo à esquerda da tela de teste (você o encontrará no palete em **Modelos treinados**).
- Os módulos que foram usados para treinamento são removidos. Especificamente:
  - [Árvore de decisão aumentada em duas classes][two-class-boosted-decision-tree]
  - [Modelo de treinamento][train-model] 
  - [Divisão][split]
  - o segundo módulo [Executar Script R][execute-r-script] que foi usado para dados de teste
- O modelo treinado salvo é adicionado ao teste.
- Os módulos **Entrada de serviço Web** e **Saída de serviço Web** são adicionados.

> [AZURE.NOTE]O experimento foi salvo em duas partes: o teste de treinamento original e o novo teste de pontuação. Você pode acessar qualquer um deles usando as guias na parte superior da tela do teste.

É necessário executar uma etapa adicional com o nosso teste. O Estúdio de Aprendizado de Máquina removeu um módulo [Executar Script R][execute-r-script] quando ele removeu o módulo [Divisão][split], mas deixou o outro módulo [Executar Script R][execute-r-script]. Como o módulo só foi usado para treinamento e teste (ele forneceu uma função importante nos dados de exemplo), podemos agora removê-lo e conectar o [Editor de metadados][metadata-editor] ao [Modelo de pontuação][score-model].

Agora o teste deve se parecer como isto:

![Pontuando o modelo treinado][4]


Você pode estar se perguntando por que deixamos o conjunto de dados de Dados de cartão de crédito alemão UCI no teste de pontuação. O serviço usará os dados do usuário, não o conjunto de dados original, portanto, porque deixá-los conectados?

É verdade que o serviço não precisa dos dados de cartão de crédito originais. Mas ele precisa do esquema para esses dados, que inclui informações como quantas colunas existem e quais colunas são numéricas. Essas informações de esquema são necessárias para interpretar os dados do usuário. Deixamos esses componentes conectados para que o módulo de pontuação tenha o esquema de conjunto de dados quando o serviço estiver sendo executado. Os dados não são usados, somente o esquema.

Execute o teste uma última vez (clique em **EXECUTAR**). Se você deseja verificar se o modelo ainda está funcionando, clique na saída do módulo [Modelo de Pontuação][score-model] e selecione **Exibir Resultados**. Você verá os dados originais exibidos, juntamente com o valor de risco de crédito ("Rótulos de pontuação") e o valor de probabilidades de pontuação ("Probabilidades de pontuação").

##Publicar o serviço Web

Para publicar um serviço Web derivado de nosso teste, clique em **PUBLICAR SERVIÇO WEB** abaixo das telas. O Estúdio AM publica a experiência como um serviço Web e leva você até o painel de serviço.

> [AZURE.TIP]Você pode atualizar o serviço Web após tê-lo publicado. Por exemplo, se você quiser alterar o seu modelo, basta editar o teste de treinamento, ajustar os parâmetros de modelo e clicar em **PUBLICAR SERVIÇO WEB**. Quando você publicar o teste novamente, então, ele substituirá o serviço Web, agora usando seu modelo atualizado.

Você pode configurar o serviço clicando na guia **CONFIGURAÇÃO**. Assim, você pode modificar o nome do serviço (é dado o nome do teste por padrão) e fornecer uma descrição para ele. Também pode adicionar rótulos mais simpáticos para as colunas de entrada e saída.

##Teste do serviço Web
Na página **PAINEL**, clique no link **Teste** em **Ponto de extremidade padrão**. Um diálogo aparecerá e solicitará dados de entrada para o serviço. Existem as mesmas colunas que apareciam no conjunto de dados de risco de crédito alemão original.

Insira um conjunto de dados e clique em **OK**.

Os resultados gerados pelo serviço Web são exibidos na parte inferior do painel. A maneira como nós configuramos o serviço, os resultados exibidos, são gerados pelo módulo de pontuação.


----------

**Em seguida: [acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png


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
 

<!---HONumber=July15_HO4-->