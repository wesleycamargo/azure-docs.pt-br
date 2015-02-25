<properties title="Step 3: Create a new Azure Machine Learning experiment" pageTitle="Etapa 3: Criar um novo teste de Aprendizado de Máquina | Azure" description="Etapa 3: Criar uma nova experiência de treinamento no Azure Studio de aprendizado de máquina" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/02/2014" ms.author="garye" />


Esta é a quarta etapa do passo a passo, [Desenvolvendo uma Solução Preditiva com o AM do Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Criar um espaço de trabalho do AM][create-workspace]
2.	[Fazer upload de dados existentes][upload-data]
3.	**Criar um novo experimento**
4.	[Treinar e avaliar os modelos][train-models]
5.	[Publicar o serviço Web][publish]
6.	[Acessar o serviço Web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[criar novo]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Etapa 3: Criar um novo teste de Aprendizado de Máquina do Azure

Precisamos criar um novo teste no Estúdio AM que use o conjunto de dados que carregamos.  

1.	No Estúdio AM, clique em **+NOVO** na parte inferior da janela.
2.	Selecione **TESTE**.
3.	Na paleta do módulo à esquerda das telas de teste, expanda **Conjuntos de dados salvos**.
4.	Localize o conjunto de dados que você criou e arraste-o nas telas. Você também pode localizar o conjunto de dados inserindo o nome na caixa **Pesquisar** acima da paleta.  

## Preparar os dados
É possível exibir as 100 primeiras linhas dos dados, e algumas informações estatísticas de todo o conjunto de dados, clicando com o botão direito do mouse na porta de saída do conjunto de dados e selecionando **Visualizar**. Observe que o Estúdio AM já identificou o tipo de dados de cada coluna. Ele também já colocou títulos genéricos nas colunas, já que o arquivo de dados não veio com títulos nas colunas.  

Os títulos de coluna não são essenciais, mas facilitam o trabalho com os dados no modelo. Também, quando eventualmente publicarmos esse modelo em um serviço Web, os títulos ajudarão a identificar as colunas para o usuário do serviço.  

Podemos adicionar títulos de coluna usando o módulo **Editor de metadados**.  

1.	Na paleta de módulo, digite "metadados" na caixa **Pesquisar**. Você verá o **Editor de metadados** na lista de módulos.
2.	Clique e arraste o módulo **Editor de metadados** nas telas e arraste-o para baixo do conjunto de dados.
3.	Conecte o conjunto de dados ao **Editor de metadados**: clique na porta de saída do conjunto de dados e arraste a porta de entrada do **Editor de metadados**, em seguida, solte o botão do mouse. O conjunto de dados e o módulo permanecerão conectados mesmo se você mover um deles nas telas.
4.	No painel **Propriedades** à direita das telas, clique em **Iniciar seletor de coluna**.
5.	Verifique se "Todas as colunas" está selecionado no campo **Começa com**, remova quaisquer linhas e clique em **OK**. Isso direciona o **Editor de metadados** para operar em todas as colunas de dados.
6.	Para o parâmetro **Novo nome de coluna**, insira uma lista de nomes para as 21 colunas no conjunto de dados, separados por vírgulas e na ordem da coluna. Você pode obter os nomes de colunas na documentação do conjunto de dados no site UCI ou, por conveniência, você pode copiar e colar o seguinte:  

	Status da conta de verificação, Duração em meses, Histórico de crédito, Finalidade, Valor de crédito, Contas de poupança/obrigações, Emprego atual desde, Parcela em porcentagem do rendimento disponível, Status pessoal e sexo, Outros devedores, Residência atual desde, Propriedade, Idade, Outros planos de prestações, Hospedagem, Número de créditos existentes, Cargo, Número de pessoas dependentes, Telefone, Trabalhador estrangeiro, Risco de crédito  

O painel de Propriedades ficará semelhante a este:

![Properties for Metadata Editor][1] 

>Dica - Se deseja verificar os títulos das colunas, execute o teste (clique em **EXECUTAR** abaixo das telas do teste), clique com o botão direito do mouse na porta de saída do módulo **Editor de metadados** e selecione **Visualizar**. Você pode exibir a saída de qualquer módulo da mesma maneira para exibir o progresso dos dados durante o teste.

O teste deve se parecer como o seguinte:  

![Adding Metadata Editor][2]
 
## Criar conjuntos de dados de treinamento e teste
A próxima etapa do teste é gerar conjuntos de dados separados que serão utilizados para treinamento e teste de nosso modelo. Para isso, usamos o módulo **Dividir**.  

1.	Localize o módulo **Dividir**, arraste-o para a tela e conecte-o ao último módulo do **Editor de metadados**.
2.	Por padrão, a taxa de divisão é 0,5 e o parâmetro **Divisão aleatória** é definido. Isso significa que metade dos dados aleatórios sairá por uma porta do módulo de **Divisão**, e a outra metade sairá por outra porta. Você pode ajustar isso, bem como o parâmetro **Semente aleatória**, para alterar a divisão entre dados de treinamento e de pontuação. Para este exemplo, deixaremos como está.
	>Dica - A taxa de divisão determina essencialmente qual parcela dos dados sairá pela porta de saída à esquerda. Por exemplo, se você definir a taxa em 0,7, então, 70% dos dados sairão pela porta esquerda e 30% pela porta direita.  
	
Podemos usar as saídas do módulo **Dividir**, entretanto, da forma como desejarmos, mas vamos escolher usar a saída à esquerda para dados de treinamento e a saída à direita para dados de pontuação.  

Como mencionado no site UCI, o custo de uma classificação incorreta de um risco de crédito alto como baixo é cinco vezes maior do que o custo da classificação incorreta de um risco baixo como alto. Para isso, iremos gerar um novo conjunto de dados que reflita essa função de custo. No novo conjunto de dados, cada exemplo de alto é replicado cinco vezes, enquanto cada exemplo de baixo não será replicado.   

Podemos fazer essa replicação usando o código R:  

1.	Localize e arraste o módulo **Script R de Executar** nas telas do teste e conecte-o à porta de saída à esquerda do módulo **Dividir**.
2.	No painel **Propriedades**, exclua o texto padrão no parâmetro **Script** e insira este script: 

		dataset1 <- maml.mapInputPort(1)
		data.set<-dataset1[dataset1[,21]==1,]
		pos<-dataset1[dataset1[,21]==2,]
		for (i in 1:5) data.set<-rbind(data.set,pos)
		maml.mapOutputPort("data.set")


Precisamos fazer essa mesma operação de replicação para cada saída do módulo **Dividir** de forma que os dados de treinamento e pontuação tenham os mesmos ajustes de custo.

1.	Clique com o botão direito no módulo **Script R de Executar** e selecione **Copiar**.
2.	Clique com o botão direito do mouse nas telas de teste e selecione **Colar**.
3.	Conecte esse módulo **Script R de Executar** à porta de saída direita do módulo **Dividir**.  

>Dica - A cópia do módulo Executar script R contém o mesmo script que o módulo original. Quando você copia e cola um módulo nas telas, a cópia mantém todas as propriedades do original.  
>
Nosso teste agora se parece com esse:
 
![Adding Split module and R scripts][3]

**Avançar: [Treinar e avaliar os modelos][train-models]**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
