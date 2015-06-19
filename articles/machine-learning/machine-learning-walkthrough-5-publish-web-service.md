<properties 
	pageTitle="Etapa 5: Publicar o serviço Web de Aprendizado de Máquina | Azure" 
	description="Solução alternativa. Etapa 5: Publicar um experimento de pontuação no Estúdio de Aprendizado de Máquina do Microsoft Azure como um serviço Web de aprendizado de máquina do Azure" 
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
	ms.date="01/29/2015" 
	ms.author="garye"/>


Esta é a quinta etapa do passo a passo: [Desenvolvendo uma Solução Preditiva com o AM do Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Criar um espaço de trabalho de AM][create-workspace]
2.	[Carregar dados existentes][upload-data]
3.	[Criar um novo teste][create-new]
4.	[Treinar e avaliar os modelos][train-models]
5.	**Publicar o serviço Web**
6.	[Acessar o serviço Web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Etapa 5: Publicar o serviço Web de Aprendizado de Máquina do Azure

Para tornar esse modelo preditivo útil aos outros, o publicaremos como um serviço Web no Azure. Um usuário poderá enviar ao serviço um conjunto de dados de aplicativo de crédito e o serviço retornará a previsão do risco de crédito.  

Para fazer isso, você precisa:  

-	Preparar o teste de modo que ele fique pronto para ser publicado
-	Publicá-lo em um servidor temporário no qual poderemos testá-lo
-	Provê-lo para o servidor ativo depois de julgarmos que esteja pronto  

Antes de continuar, você deve criar uma cópia desse teste para edição. Essa é a maneira de poder retornar ao teste de treinamento sempre que precisar para usar em novos trabalhos com seus modelos.  

1.	Clique em **Salvar Como** abaixo da tela.
2.	Dê um nome significativo à cópia do teste. O padrão é anexar "- Cópia" ao nome original do teste. Em nosso caso, vamos chamá-lo de "Previsão de risco de crédito - Teste de pontuação".
3.	Clique em **OK**.  


Agora você pode ver ambos, o teste original e a cópia, listados na lista TESTES do Estúdio AM.  

![Experiments list][1]
 
##Preparar o teste de pontuação
Precisamos fazer duas coisas para preparar nosso modelo para ser publicado como um serviço Web.  

Primeiro, precisamos converter o teste de um *teste de treinamento* em um *teste de pontuação*. Até este ponto, estávamos usando nosso modelo de treinamento. Mas o serviço publicado não fará mais treinamento - ele fará pontuações das entradas do usuário. Portanto, salvaremos uma cópia do modelo que treinamos e depois eliminaremos todos os componentes de nosso teste, que foram aplicados no treinamento.  

Em segundo lugar, o serviço Web AM do Azure aceitará entrada do usuário e retornará um resultado; portanto, precisamos identificar aqueles pontos de entrada e saída em nosso teste.  

###Converter de um teste de treinamento para um teste de pontuação
Digamos que decidimos que o modelo de árvore aumentada foi o melhor modelo a usar. Assim, a primeira coisa a fazer é remover os módulos de treinamento SVM.  

1.	Excluir o **Computador de vetor de suporte de duas classes**
2.	Excluir o **Modelo de Treinamento** e módulos do tipo **Módulo de Pontuação** que estavam conectados a ele
3.	Excluir o módulo **Transformar dados por escala**  

Agora salvaremos o modelo de árvore aumentada que treinamos. Podemos, então, remover os módulos restantes no teste que usamos para treinamento e substituí-los pelo modelo treinado.  

1.	Clique com o botão direito do mouse na porta de saída do módulo **Treinar modelo** restante e selecione **Salvar como modelo treinado**.
2.	Insira um nome e uma descrição para o modelo treinado. Para esse exemplo, chamaremos de "Previsão de risco de crédito".

	>**Observação**: Depois de salvarmos o modelo treinado, ele aparecerá na paleta do módulo e estará disponível para ser usado em outros testes.

3.	Localize esse modelo na paleta do módulo digitando "risco de crédito". Na caixa **Pesquisar**, arraste o modelo treinado **Previsão de risco de crédito** para as telas do teste.
4.	Exclua os módulos **Árvore de decisão aumentada de duas classes** e **Treinar Modelo**.
5.	Conecte a saída do modelo **Previsão de risco de crédito** à entrada esquerda do módulo **Modelo de Pontuação**.   

Agora, precisamos salvar a versão treinada do modelo em nosso teste no lugar dos módulos de treinamento originais.  

Há mais componentes no teste que adicionamos apenas para treinamento e para avaliar nossos dois algoritmos do modelo. Podemos removê-los também:  

-	**Divisão**
-	Ambos os módulos **Executar script R**
-	**Avaliar modelo**  

Mais uma coisa: Os dados do cartão de crédito original incluíam a coluna de Risco de crédito. Nós usamos essa coluna no módulo de **Treinar modelo** para que ela pudesse treinar o modelo para prever esses valores. Mas agora que o modelo já foi treinado, não queremos continuar a usar essa coluna - o modelo treinado vai prever esse valor para nós. Para remover essa coluna do fluxo de dados, usamos o módulo **Coluna do projeto**.  

1.	Localize e arraste o módulo **Coluna do projeto** na tela.
2.	Conecte esse módulo à saída do módulo **Editor de Metadados** (agora que os módulos **Dividir** e **Executar Script R** foram removidos)
3.	Selecione o módulo **Colunas do Projeto** e clique em **Ativar seletor de coluna**.
4.	Deixe "Todas as colunas" na lista suspensa.
5.	Clique no sinal de mais (+) para criar uma nova linha de listas suspensas.
6.	Na nova lista suspensa, selecione "Excluir nomes de coluna" e insira "Risco de crédito" no campo de texto (você também pode especificar a coluna por número de coluna, 21).
7.	Clique em **OK**.

	>Dica - O seletor de coluna segue a lógica das listas suspensas, na sequência em que aparecem. Nesse caso, direcionamos o módulo **Colunas do projeto** para "aprovar todas as colunas, exceto a coluna 'Risco de crédito'." Se tivéssemos deixado de lado a primeira lista suspensa, o módulo não passaria absolutamente nenhuma coluna.

8.	Conecte a saída do módulo **Colunas do Projeto** à entrada direita do módulo **Modelo de Pontuação**.  

Agora o teste deve se parecer como isto:  

![Scoring the trained model][2]  

###Selecione a entrada e saída do serviço
No modelo original, os dados a serem pontuados foram passados pela porta de entrada direita ("Conjunto de dados") do módulo **Modelo de Pontuação**, e o resultado de pontuação apareceu na porta de saída ("Conjunto de dados pontuado"). Quando o serviço está executando, desejamos que os dados do usuário e os resultados usem as mesmas portas.  

1.	Clique com o botão direito do mouse na porta de entrada direita do módulo **Modelo de pontuação** e selecione **Definir como Entrada de Publicação**. Os dados do usuário precisarão incluir todos os dados do vetor de recurso.

	>**Dica** - Se você precisar executar alguma manipulação nos dados do usuário antes de passá-los para o módulo de pontuação (como a maneira como usamos um módulo **Transformar Dados por Escala** para preparar dados para o modelo SVM), basta deixar o módulo no serviço Web e definir a entrada do serviço para a porta de entrada desse módulo.

2.	Clique com o botão direito do mouse na porta de saída e selecione **Definir como Saída de Publicação**. A saída do módulo do Modelo de Pontuação será retornada pelo serviço. Isso inclui o vetor de recurso, mais a previsão de risco de crédito e o valor de probabilidade em pontuação.

	>**Dica** - Se desejar que o serviço Web retorne somente parte desses dados - por exemplo, não desejar o fator de recurso inteiro - você pode adicionar um módulo **Colunas do Projeto** depois do módulo **Modelo de Pontuação**, e configurá-lo para excluir as colunas que não deseja mais, definindo também a saída do módulo **Colunas do Projeto** ara ser a saída do serviço Web.  
  

>**Observação** - Você pode estar imaginando porque deixamos o conjunto de dados UCI Dados de Crédito Alemão e seus módulos associados conectados ao módulo **Modelo de Pontuação**. O serviço usará os dados do usuário, não o conjunto de dados original, portanto, porque deixá-los conectados?

É verdade que o serviço não precisa dos dados de cartão de crédito originais. Mas ele precisa do esquema para esses dados, que inclui informações como quantas colunas existem e quais colunas são numéricas. Essas informações de esquema são necessárias para interpretar os dados do usuário. Deixamos esses componentes conectados para que o módulo de pontuação tenha o esquema de conjunto de dados quando o serviço estiver sendo executado. Os dados não são usados, somente o esquema.  

Execute o teste uma última vez (clique em **EXECUTAR**). Se você deseja verificar se o modelo ainda está funcionando, clique com o botão direito do mouse na saída do módulo **Modelo de Pontuação** e selecione **Visualizar**. Você verá os dados originais exibidos, juntamente com o valor de risco de crédito ("Rótulos de pontuação") e o valor de probabilidades de pontuação ("Probabilidades de pontuação").  

##Publicar o serviço Web
Para publicar um serviço Web derivado de nosso teste, clique em **PUBLICAR SERVIÇO WEB** abaixo das telas e clique em **SIM** quando solicitado. O Estúdio AM publica o teste como um serviço Web no servidor temporário AM, e o leva ao painel de serviço.   

>**Dica** - Você pode atualizar o serviço Web após tê-lo publicado. Por exemplo, se desejar alterar seu modelo, basta editar o teste de treinamento que você salvou anteriormente, puxar os parâmetros do modelo e salvar o modelo treinado (substituindo um que você salvou antes). Quando abrir o teste de pontuação novamente, você verá um aviso informando que algo foi alterado (esse será seu modelo treinado) e você poderá atualizar o teste. Quando você publicar o teste novamente, então, ele substituirá o serviço Web, agora usando seu modelo atualizado.  

Você pode configurar o serviço clicando na guia **CONFIGURAÇÃO**. Assim, você pode modificar o nome do serviço (é dado o nome do teste por padrão) e fornecer uma descrição para ele. Também pode adicionar rótulos mais simpáticos para as colunas de entrada e saída.  

Iremos tratar da chave **PRONTO PARA PRODUÇÃO?** um pouco mais tarde.  

##Teste do serviço Web
Na página **PAINEL**, clique no link **Teste** sob **Serviços Temporários**. Um diálogo aparecerá solicitando dados de entrada para o serviço. Existem as mesmas colunas que apareciam no conjunto de dados de risco de crédito alemão original.  

Insira um conjunto de dados e clique em **OK**.  

Os resultados gerados pelo serviço Web são exibidos na parte inferior do painel. A maneira como nós configuramos o serviço, os resultados exibidos, são gerados pelo módulo de pontuação.   

##Promover o serviço Web no servidor ativo
Até aqui o serviço foi executado no servidor temporário do AM. Quando você estiver pronto para continuar no real, poderá solicitar que ele seja promovido para o servidor ativo.  

Na guia **CONFIGURAÇÃO**, clique em "SIM" próximo a **PRONTO PARA PRODUÇÃO?** Isso envia um aviso ao seu administrador de TI informando que esse serviço Web está pronto para ser ativado. O administrador, então, pode promovê-lo para ativação.

![Promoting the service to the live environment][3]  

----------

**Avançar: [Acessar o serviço Web][access-ws]**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png

<!--HONumber=46--> 
 