---
title: Tutorial que usa padrões para aprimorar as previsões do LUIS – Azure | Microsoft Docs
titleSuffix: Azure
description: Neste tutorial, use o padrão de intenções para aprimorar as previsões de entidade e intenção do LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265309"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Tutorial: Usar padrões para aprimorar previsões

Neste tutorial, use o padrão para aumentar a previsão de entidade e de intenção.  

> [!div class="checklist"]
* Como identificar que um padrão ajudaria seu aplicativo
* Como criar um padrão 
* Como usar entidades predefinidas e personalizadas em um padrão 
* Como verificar aprimoramentos na previsão de padrões
* Como adicionar uma função a uma entidade para localizar entidades baseadas em contexto
* Como adicionar um Pattern.any para localizar entidades de forma livre

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="import-humanresources-app"></a>Importar o aplicativo HumanResources
Este tutorial importa um aplicativo HumanResources. O aplicativo tem três intenções: None, GetEmployeeOrgChart e GetEmployeeBenefits. O aplicativo tem duas entidades: número predefinido e funcionário. A entidade Funcionário é uma entidade simples para extrair o nome de um funcionário. 

1. Crie um novo arquivo do aplicativo LUIS e dê a ele o nome `HumanResources.json`. 

2. Copie a seguinte definição do aplicativo para o arquivo:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. Na página **Aplicativos** do LUIS, selecione **Importar novo aplicativo**. 

4. Na caixa de diálogo **Importar novo aplicativo**, selecione o arquivo `HumanResources.json` que você criou na etapa 1.

5. Selecione a intenção **GetEmployeeOrgChart** e mude do **Modo de exibição de entidades** para o **Modo de exibição de tokens**. Várias declarações de exemplo são listadas. Cada declaração contém um nome, que é uma entidade Funcionário. Observe que cada nome é diferente e que a organização das palavras é diferente para cada declaração. Essa diversidade ajuda o LUIS a aprender uma ampla variedade de declarações.

    ![Captura de tela da página Intenção com o Modo de exibição de entidades ativado](media/luis-tutorial-pattern/utterances-token-view.png)

6. Selecione **Treinar** na barra de navegação superior para treinar o aplicativo. Aguarde a barra verde de êxito.

7. Selecione **Testar** no painel superior. Insira `Who does Patti Owens report to?` e selecione enter. Selecione **Inspecionar** embaixo da declaração para ver mais informações sobre o teste.
    
    O nome do funcionário, Patti Owens, ainda não foi usado em uma declaração de exemplo. Esse é um teste para ver quão bem o LUIS aprendeu essa declaração para a intenção `GetEmployeeOrgChart` e a entidade Funcionário deve ser `Patti Owens`. O resultado deve estar abaixo de 50% (0,50) para a intenção `GetEmployeeOrgChart`. Embora a intenção seja correta, a pontuação é baixa. A entidade Employee também é corretamente identificada como `Patti Owens`. Os padrões aumentam essa pontuação de previsão inicial. 

    ![Captura de tela do painel de teste](media/luis-tutorial-pattern/original-test.png)

8. Feche o painel de teste selecionando o botão **Testar** na navegação superior. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Os padrões ensinam ao LUIS declarações comuns com menos exemplos
Devido à natureza do domínio de recursos humanos, há algumas maneiras comuns de perguntar sobre relações de funcionário em organizações. Por exemplo: 

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Essas declarações estão perto demais para determinar a exclusividade contextual de cada uma sem fornecer muitos exemplos de declaração. Adicionando um padrão para uma intenção, o LUIS aprende padrões de declaração comuns para uma intenção sem fornecer muitos exemplos de declaração. 

As declarações de modelo de exemplo para essa intenção incluem:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

O padrão é uma combinação da correspondência de expressão regular e aprendizado de máquina. Em seguida, forneça alguns exemplos de declaração de modelo para o LUIS aprender o padrão. Esses exemplos, juntamente com as declarações de intenção, dão ao LUIS um reconhecimento melhor de quais declarações se ajustam à intenção e onde, dentro da declaração, a entidade existe. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Adicionar as declarações de modelo

1. Na navegação esquerda, em **Melhorar o desempenho do aplicativo**, selecione **Padrões** na navegação esquerda.

2. Selecione a intenção **GetEmployeeOrgChart** e insira as seguintes declarações de modelo, uma de cada vez, selecionando enter após cada declaração de modelo:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    A sintaxe `{Employee}` marca o local da entidade dentro da declaração de modelo, bem como qual entidade é. 

    ![Captura de tela da inserção de declarações de modelo para intenção](./media/luis-tutorial-pattern/enter-pattern.png)

3. Selecione **Treinar** na barra de navegação superior. Aguarde a barra verde de êxito.

4. Selecione **Testar** no painel superior. Insira `Who does Patti Owens report to?` na caixa de texto. Selecione Enter. Essa é a mesma declaração testada na seção anterior. O resultado deve ser maior para a intenção `GetEmployeeOrgChart`. 

    Agora a pontuação é muito melhor. O LUIS aprendeu o padrão relevante para a intenção sem fornecer muitos exemplos.

    ![Captura de tela do painel de teste com resultado de pontuação alta](./media/luis-tutorial-pattern/high-score.png)

    A entidade é encontrada primeiro e o padrão é encontrado depois, indicando a intenção. Se tiver um resultado do teste em que a entidade não foi detectada e, portanto, o padrão não foi encontrado, será necessário adicionar mais declarações de exemplo sobre a intenção (não o padrão). 

5. Feche o painel de teste selecionando o botão **Testar** na navegação superior.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Usar uma entidade com uma função em um padrão
O aplicativo LUIS é usado para ajudar a mover os funcionários de um local para outro. Uma declaração de exemplo é `Move Bob Jones from Seattle to Los Colinas`. Cada local na declaração tem um significado diferente. Seattle é o local de origem e Los Colinas é o local de destino da movimentação. Para diferenciar esses locais no padrão, nas seguintes seções, você criará uma entidade simples para local com duas funções: origem e destino. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Criar uma nova intenção para mover pessoas e ativos
Crie uma nova intenção para declarações que se tratam de mover pessoas ou ativos.

1. Selecione **Intenções** no painel de navegação esquerdo
2. Selecione **Criar nova intenção**
3. Dê à nova intenção o nome `MoveAssetsOrPeople`
4. Adicione declarações de exemplo:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Captura de tela da declaração de exemplo para a intenção MoveAssetsOrPeople](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    A finalidade das declarações de exemplo é oferecer exemplos suficientes. Se, posteriormente no teste, a entidade de local não for detectada e, consequentemente, o padrão não for detectado, volte para esta etapa e adicione mais exemplos. Em seguida, treine e teste novamente. 

5. Marque as entidades nas declarações de exemplo com a entidade Funcionário selecionando o nome e o sobrenome em uma declaração e selecionando a entidade Funcionário na lista.

    ![Captura de tela de declarações em MoveAssetsOrPeople marcado com a entidade Funcionário](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Selecione o texto `portland` na declaração `move travis hinton from portland to orlando`. Na caixa de diálogo pop-up, insira o nome da nova entidade `Location` e selecione **Criar nova entidade**. Escolha o tipo de entidade **Simples** e selecione **Concluído**.

    ![Captura de tela da criação da nova entidade de local](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Marque o restante dos nomes de local nas declarações. 

    ![Captura de tela de todas as entidades marcadas](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    O padrão da escolha e da ordem de palavras é óbvio na imagem anterior. Se você **não** estiver usando padrões e as declarações na intenção tiverem um padrão óbvio, isso será uma boa indicação de que você deveria estar usando padrões. 

    Se você esperasse uma ampla variedade de declarações, em vez de um padrão, elas seriam o exemplo errado de declarações. Nesse caso, você desejaria declarações amplamente variáveis na escolha de termos ou de palavras, no comprimento da declaração e no posicionamento da entidade. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Adicionar função à entidade de local 
Funções só podem ser usadas para padrões. Adicione as funções de Origem e Destino à entidade Local. 

1. Selecione **Entidades** no painel de navegação esquerdo e **Local** na lista de entidades.

2. Adicione as funções `Origin` e `Destination` à entidade.

    ![Captura de tela da nova entidade com funções](./media/luis-tutorial-pattern/location-entity.png)

    As funções não são marcadas na página de intenções MoveAssetsOrPeople, porque as funções não existem em declarações de intenção. Elas existem em declarações de modelo de padrão. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Adicionar declarações de modelo que usam funções de local e de destino
Adicione declarações de modelo que usam a nova entidade.

1. Selecione **Padrões** no painel de navegação esquerdo.

2. Selecione a intenção **MoveAssetsOrPeople**.

3. Insira uma nova declaração de modelo usando a nova entidade `Move {Employee} from {Location:Origin} to {Location:Destination}`. A sintaxe de uma entidade e de uma função dentro de uma expressão de modelo é `{entity:role}`.

    ![Captura de tela da nova entidade com funções](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Treine o aplicativo para a nova intenção, entidade e padrão.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Testar o novo padrão quanto à extração de dados de função
Valide o novo padrão com um teste.

1. Selecione **Testar** no painel superior. 
2. Insira a declaração `Move Tammi Carlson from Bellingham to Winthrop`.
3. Selecione **Inspecionar** embaixo do resultado para ver os resultados de teste para a entidade e para a intenção.

    ![Captura de tela da nova entidade com funções](./media/luis-tutorial-pattern/test-with-roles.png)

    As entidades são encontradas primeiro; em seguida, o padrão é encontrado, indicando a intenção. Se tiver um resultado do teste em que as entidades não forem detectadas e, portanto, o padrão não for encontrado, será necessário adicionar mais declarações de exemplo sobre a intenção (não o padrão). 

4. Feche o painel de teste selecionando o botão **Testar** na navegação superior.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Use a entidade Pattern.any para encontrar entidades de forma livre em um padrão
Este aplicativo HumanResources também ajuda os funcionários a encontrar formulários de empresa. Muitos formulários têm títulos que variam em comprimento. O comprimento variável inclui frases que podem confundir o LUIS com relação a onde termine o nome do formulário. Usando uma entidade **Pattern.any** em um padrão permite especificar o início e o fim do nome do formulário para que o LUIS extraia corretamente o nome do formulário. 

### <a name="create-a-new-intent-for-the-form"></a>Criar uma nova intenção para o formulário
Crie uma nova intenção para declarações que estão procurando formulários.

1. Selecione **Intenções** no painel de navegação esquerdo.

2. Selecione **Criar nova intenção**.

3. Dê à nova intenção o nome `FindForm`.

4. Adicione uma declaração de exemplo.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Captura de tela da nova entidade com funções](./media/luis-tutorial-pattern/intent-findform.png)

    O título do formulário é `What to do when a fire breaks out in the Lab`. A declaração está solicitando o local do formulário e também está perguntando quem precisa assiná-lo para validar que o funcionário o leu. Sem uma entidade Pattern.any, seria difícil entender onde o título do formulário termina e extrair o título do formulário como uma entidade da declaração.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Criar uma entidade Pattern.any para o título do formulário
A entidade Pattern.any permite entidades de comprimento variável. Isso só funciona em um padrão, porque o ele marca o início e o fim da entidade. Se você achar que seu padrão, quando ele incluir um Pattern.any, extrai as entidades incorretamente, use uma [lista explícita](luis-concept-patterns.md#explicit-lists) para corrigir esse problema. 

1. Selecione **Entidades** no painel de navegação esquerdo.

2. Selecione **Criar nova entidade**. 

3. Dê um nome à entidade `FormName` com o tipo **Pattern.any**. Para esse tutorial específico, não é necessário adicionar nenhuma função à entidade.

    ![Imagem da caixa de diálogo para o nome e o tipo de entidade](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Adicionar um padrão que usa o Pattern.any

1. Selecione **Padrões** no painel de navegação esquerdo.

2. Selecione a intenção **FindForm**.

3. Insira uma declaração de modelo usando a nova entidade `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Captura de tela da declaração de modelo usando a entidade pattern.any](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Treine o aplicativo para a nova intenção, entidade e padrão.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testar o novo padrão quanto à extração de dados de forma livre
1. Selecione **Testar** na barra superior para abrir o painel de teste. 

2. Insira a declaração `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Selecione **Inspecionar** embaixo do resultado para ver os resultados de teste para a entidade e para a intenção.

    ![Captura de tela da declaração de modelo usando a entidade pattern.any](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    A entidade é encontrada primeiro e o padrão é encontrado depois, indicando a intenção. Se tiver um resultado do teste em que as entidades não forem detectadas e, portanto, o padrão não for encontrado, será necessário adicionar mais declarações de exemplo sobre a intenção (não o padrão).

4. Feche o painel de teste selecionando o botão **Testar** na navegação superior.

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Para fazer isso, selecione o menu de três pontos (...) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar lista de frases para aprimorar a previsão](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions