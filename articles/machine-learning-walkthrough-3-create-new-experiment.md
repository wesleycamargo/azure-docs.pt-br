<properties title="Step 3: Create a new Azure Machine Learning experiment" pageTitle="Step 3: Create a new Machine Learning experiment | Azure" description="Step 3: Create a new training experiment in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

Esta é a terceira etapa da explicação passo a passo: [Desenvolvendo uma Solução Preditiva com o AM do Azure][Desenvolvendo uma Solução Preditiva com o AM do Azure]:

1.  [Criar um espaço de trabalho do AM][Criar um espaço de trabalho do AM]
2.  [Fazer upload de dados existentes][Fazer upload de dados existentes]
3.  **Criar um novo experimento**
4.  [Treinar e avaliar os modelos][Treinar e avaliar os modelos]
5.  [Publicar o serviço Web][Publicar o serviço Web]
6.  [Acessar o serviço Web][Acessar o serviço Web]

------------------------------------------------------------------------

# Etapa 3: Criar um novo teste de Aprendizado de Máquina do Azure

Precisamos criar um novo teste no Estúdio AM que use o conjunto de dados que carregamos.

1.  No Estúdio AM, clique em **+NOVO** na parte inferior da janela.
2.  Selecione **TESTE**.
3.  Na paleta do módulo à esquerda das telas de teste, expanda **Conjuntos de dados salvos**.
4.  Localize o conjunto de dados que você criou e arraste-o nas telas. Você também pode localizar o conjunto de dados inserindo o nome na caixa **Pesquisar** acima da paleta.

## Preparar os dados

É possível exibir as 100 primeiras linhas dos dados, e algumas informações estatísticas de todo o conjunto de dados, clicando com o botão direito do mouse na porta de saída do conjunto de dados e selecionando **Visualizar**. Observe que o Estúdio AM já identificou o tipo de dados de cada coluna. Ele também já colocou títulos genéricos nas colunas, já que o arquivo de dados não veio com títulos nas colunas.

Os títulos de coluna não são essenciais, mas facilitam o trabalho com os dados no modelo. Também, quando eventualmente publicarmos esse modelo em um serviço Web, os títulos ajudarão a identificar as colunas para o usuário do serviço.

Podemos adicionar títulos de coluna usando o módulo **Editor de metadados**.

1.  Na paleta de módulo, digite "metadados" na caixa **Pesquisar**. Você verá o **Editor de metadados** na lista de módulos.
2.  Clique e arraste o módulo **Editor de metadados** nas telas e arraste-o para baixo do conjunto de dados.
3.  Conecte o conjunto de dados ao **Editor de metadados**: clique na porta de saída do conjunto de dados e arraste a porta de entrada do **Editor de metadados**, em seguida, solte o botão do mouse. O conjunto de dados e o módulo permanecerão conectados mesmo se você mover um deles nas telas.
4.  No painel **Propriedades** à direita das telas, clique em **Ativar seletor de coluna**.
5.  Verifique se a opção "Todas as colunas" está selecionada na lista suspensa e clique em **OK**. Isso direciona o **Editor de metadados** para operar em todas as colunas de dados.
6.  Para o parâmetro **Novo nome de coluna**, insira a lista de nomes das 21 colunas no conjunto de dados, separados por vírgulas e na ordem da coluna. Você pode obter os nomes de colunas na documentação do conjunto de dados no site UCI ou, por conveniência, você pode copiar e colar o seguinte:

    Status da conta de verificação, Duração em meses, Histórico de crédito, Finalidade, Valor de crédito, Contas de poupança/obrigações, Emprego atual desde, Parcela em porcentagem do rendimento disponível, Status pessoal e sexo, Outros devedores, Residência atual desde, Propriedade, Idade, Outros planos de prestações, Hospedagem, Número de créditos existentes, Cargo, Número de pessoas dependentes, Telefone, Trabalhador estrangeiro, Risco de crédito

O painel de Propriedades ficará semelhante a este:

![Propriedades do Editor de metadados][Propriedades do Editor de metadados]

> Dica - Se deseja verificar os títulos das colunas, execute o teste (clique em **EXECUTAR** abaixo das telas do teste), clique com o botão direito do mouse na porta de saída do módulo **Editor de metadados** e selecione **Visualizar**. Você pode exibir a saída de qualquer módulo da mesma maneira para exibir o progresso dos dados durante o teste.

Agora o teste deve se parecer como isto:

![Adicionando Editor de metadados][Adicionando Editor de metadados]

## Criar conjuntos de dados de treinamento e teste

A próxima etapa do teste é gerar conjuntos de dados separados que serão utilizados para treinamento e teste de nosso modelo. Para isso, usamos o módulo **Dividir**.

1.  Localize o módulo **Dividir**, arraste-o nas telas e conecte-o ao último módulo do **Editor de metadados**.
2.  Por padrão, a taxa de divisão é 0,5 e o parâmetro **Divisão aleatória** é definido. Isso significa que metade dos dados aleatórios sairá por uma porta do módulo de **Divisão**, e a outra metade sairá por outra porta. Você pode ajustar isso, bem como o parâmetro **Semente aleatória**, para alterar a divisão entre dados de treinamento e de pontuação. Para este exemplo, deixaremos como está.

    > Dica - A taxa de divisão determina essencialmente qual parcela dos dados sairá pela porta de saída à esquerda. Por exemplo, se você definir a taxa em 0,7, então, 70% dos dados sairão pela porta esquerda e 30% pela porta direita.

Podemos usar as saídas do módulo **Divisão**, entretanto, da forma como desejarmos, mas vamos escolher usar a saída à esquerda para dados de treinamento e a saída à direita para dados de pontuação.

Como mencionado no site UCI, o custo de uma classificação incorreta de um risco de crédito alto como baixo é cinco vezes maior do que o custo da classificação incorreta de um risco baixo como alto. Para isso, iremos gerar um novo conjunto de dados que reflita essa função de custo. No novo conjunto de dados, cada exemplo de alto é replicado cinco vezes, enquanto cada exemplo de baixo não será replicado.

Podemos fazer essa replicação usando o código R:

1.  Localize e arraste o módulo **Executar script R** nas telas do teste e conecte-o à porta de saída à esquerda do módulo **Divisão**.
2.  No painel **Propriedades**, exclua o texto padrão no parâmetro **Script** e insira esse script:

        dataset1 <- maml.mapInputPort(1)
        data.set<-dataset1[dataset1[,21]==1,]
        pos<-dataset1[dataset1[,21]==2,]
        for (i in 1:5) data.set<-rbind(data.set,pos)
        maml.mapOutputPort("data.set")

Precisamos fazer essa mesma operação de replicação para cada saída do módulo **Divisão** de forma que os dados de treinamento e pontuação tenham os mesmos ajustes de custo.

1.  Clique com o botão direito do mouse no módulo **Executar script R** e selecione **Copiar**.
2.  Clique com o botão direito do mouse nas telas de teste e selecione **Colar**.
3.  Conecte esse módulo **Executar script R** à porta de saída à direita do módulo **Dividir**.

> Dica - A cópia do módulo Executar script R contém o mesmo script que o módulo original. Quando você copia e cola um módulo nas telas, a cópia mantém todas as propriedades do original.
>
> Nosso teste agora se parece com esse:

![Adicionando módulo Divisão e Scripts R][Adicionando módulo Divisão e Scripts R]

  [Desenvolvendo uma Solução Preditiva com o AM do Azure]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Criar um espaço de trabalho do AM]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Fazer upload de dados existentes]: ../machine-learning-walkthrough-2-upload-data/
  [Treinar e avaliar os modelos]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Publicar o serviço Web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Acessar o serviço Web]: ../machine-learning-walkthrough-6-access-web-service/
  [Propriedades do Editor de metadados]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
  [Adicionando Editor de metadados]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
  [Adicionando módulo Divisão e Scripts R]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
