---
title: 'Edite metadados: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo editar metadados no serviço do Azure Machine Learning para alterar os metadados que está associado a colunas em um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cfee607aca155b6cf68e5bddc40eb9c752df5e34
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028809"
---
# <a name="edit-metadata-module"></a>Módulo editar metadados

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para alterar os metadados que está associado a colunas em um conjunto de dados. O tipo de valor e os dados do conjunto de dados será alterado depois de usar o **editar metadados** módulo. 
 
Alterações de metadados típicas podem incluir:
  
+ Tratando colunas Boolianas ou numéricas como valores categóricos  
  
+ Que indica qual coluna contém o *classe* rótulo, ou os valores que você deseja categoriar ou predizer  
  
+ Marcar colunas como recursos
  
+ Alterando a data/hora valores para um valor numérico, ou vice-versa  
  
+ Renomeando colunas
  
 Use [Editar metadados sempre que precisar modificar a definição de uma coluna, normalmente para atender aos requisitos para um módulo de downstream. Por exemplo, alguns módulos podem trabalhar somente com tipos de dados específicos ou exigem sinalizadores nas colunas, como `IsFeature` ou `IsCategorical`.  
  
 Depois de executar a operação necessária, você pode redefinir os metadados para seu estado original. 
  
## <a name="configure-edit-metadata"></a>Configurar editar metadados
  
1.  No Azure Machine Learning, adicione [editar metadados](./edit-metadata.md) módulo ao seu experimento e conecte-se o conjunto de dados que você deseja atualizar. Você pode encontrá-lo sob **Data Transformation**, no **manipular** categoria.
  
2.  Clique em **inicie o seletor de coluna** e escolha a coluna ou conjunto de colunas para trabalhar com. Você pode escolher colunas individualmente, por nome ou índice, ou você pode escolher um grupo de colunas, por tipo.  
  
3.  Selecione o **tipo de dados** opção se você precisar atribuir um tipo de dados diferentes para as colunas selecionadas. Alterar os dados de tipo pode ser necessário para determinadas operações: por exemplo, se seu conjunto de dados de origem tiver números tratados como texto, você deve alterá-los para um tipo de dados numérico antes de usar operações matemáticas. 

    + Os tipos de dados suportados são `String`, `Integer`, `Double`, `Boolean`, `DateTime`. 

    + Se várias colunas forem selecionadas, você deve aplicar as alterações de metadados **todos os** colunas selecionadas. Por exemplo, digamos que você escolher colunas numéricas de 2 a 3. Você pode alterá-los todos para um tipo de dados de cadeia de caracteres e renomeá-los em uma única operação. No entanto, é possível alterar uma coluna para um tipo de dados de cadeia de caracteres e a outra coluna de um float em um inteiro.
  
    + Se você não especificar um novo tipo de dados, os metadados da coluna são inalterado. 
    
    + O tipo de coluna e os valores serão alterados depois de executar o [editar metadados](./edit-metadata.md) operação. Você pode recuperar o tipo de dados original a qualquer momento usando [editar metadados](./edit-metadata.md) para redefinir o tipo de dados de coluna.  

    > [!NOTE]
    > Se você alterar qualquer tipo de número para o **DateTime** de tipo, deixe o **formato DateTime** espaço em branco o campo. Atualmente, não é possível especificar o formato de dados de destino.  

      
4.  Selecione o **categórico** opção para especificar que os valores nas colunas selecionadas devem ser tratados como categorias. 

    Por exemplo, você pode ter uma coluna que contém os números de 0,1 e 2, mas sabe que os números, na verdade, dizer "Smoker", "Não-smoker" e "Desconhecido". Nesse caso, sinalizando que a coluna como categórica você pode garantir que os valores não são usados em cálculos numéricos, apenas para agrupar os dados. 
  
5.  Use o **campos** opção se você quiser alterar a maneira que o Azure Machine Learning usa os dados em um modelo.

    + **Recurso**: Use esta opção para sinalizar uma coluna como um recurso, para uso com módulos que operam apenas em colunas de recurso. Por padrão, todas as colunas são inicialmente tratadas como recursos.  
  
    + **Rótulo**: Use esta opção para marcar o rótulo (também conhecido como o atributo previsível ou variável de destino). Muitos módulos exigem que pelo menos um (e somente um) coluna rótulo estar presentes no conjunto de dados. 
    
        Em muitos casos, o Azure Machine Learning pode deduzir que uma coluna contém um rótulo de classe, mas definindo esses metadados, você pode garantir que a coluna é identificada corretamente. Definir essa opção não altera os valores de dados, apenas a forma como alguns algoritmos de aprendizado de máquina tratar os dados.
  

  
    > [!TIP]
    >  Tem dados que não se ajusta a essas categorias?  Por exemplo, seu conjunto de dados pode conter valores como identificadores exclusivos que não são úteis como variáveis. Às vezes, as IDs podem causar problemas quando usados em um modelo. 
    >   
    >  Felizmente "nos bastidores" Azure Machine Learning mantém todos os seus dados, portanto, você não precisa excluir essas colunas do conjunto de dados. Quando você precisar executar operações em um conjunto especial de colunas, basta remover todas as outras colunas temporariamente usando o [selecionar colunas no conjunto de dados](./select-columns-in-dataset.md) módulo. Mais tarde você pode mesclar as colunas de volta para o conjunto de dados usando o [adicionar colunas](./add-columns.md) módulo.  
  
6. Use as opções a seguir para limpar as seleções anteriores e a restauração de metadados para os valores padrão.  
  
    + **Limpar recurso**: Use esta opção para remover o sinalizador de recurso.  
  
         Porque todas as colunas são inicialmente tratadas como recursos para os módulos que executam operações matemáticas, talvez seja necessário usar essa opção para impedir que colunas numéricas que estão sendo tratados como variáveis.
  
    + **Limpar rótulo**: Use esta opção para remover o **rótulo** metadados da coluna especificada.  
  
    + **Limpar pontuação**: Use esta opção para remover o **pontuação** metadados da coluna especificada.  
  
         Atualmente, a capacidade de marcar explicitamente uma coluna como uma pontuação não está disponível no Azure Machine Learning. No entanto, algumas operações resultam em uma coluna que está sendo sinalizada como uma pontuação internamente. Além disso, um módulo R personalizado pode gerar valores de pontuação.
  
  
7.  Para **novos nomes de coluna**, digite o novo nome das colunas da coluna selecionada.  
  
    + Nomes de coluna podem usar apenas caracteres que são compatíveis com o UTF-8 codificação. Cadeias de caracteres vazias, valores nulos ou que consistam inteiramente de espaços de nomes não são permitidos.  
  
    + Para renomear várias colunas, digite os nomes como uma lista separada por vírgulas na ordem dos índices de coluna.  
  
    + Todas as colunas selecionadas devem ser renomeadas. Você não pode omitir ou ignorar colunas.  
  
  
8.  Execute o experimento.  

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 