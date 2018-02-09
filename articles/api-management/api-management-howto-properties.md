---
title: "Como usar as propriedades nas políticas de Gerenciamento de API do Azure"
description: "Saiba como usar as propriedades nas políticas de Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: e0559380f6d686a4e559779c4271ea85106558d6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Como usar as propriedades nas políticas de Gerenciamento de API do Azure
As políticas de gerenciamento de API são um recurso poderoso do sistema que permitem que o portal do Azure altere o comportamento da API por meio da configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. É possível construir declarações de política usando valores de texto literais, expressões de política e propriedades. 

Cada instância de serviço do Gerenciamento de API tem uma coleção de propriedades de pares de chave/valor que são globais à instância do serviço. Essas propriedades podem ser usadas para gerenciar valores de cadeia de caracteres constantes em todas as configurações e as políticas de API. Cada propriedade pode ter os seguintes atributos:

| Atributo | type | DESCRIÇÃO |
| --- | --- | --- |
| Nome de exibição |string |Cadeia de caracteres alfanuméricos usada para referenciar a propriedade nas políticas. |
| Valor |string |O valor da propriedade. Ele não pode ficar vazio ou conter apenas espaços em branco. |
|Segredo|booleano|Determina se o valor é um segredo e se deve ser criptografado ou não.|
| Marcas |matriz de cadeias de caracteres |Marcas opcionais que, quando fornecidas, podem ser usadas para filtrar a lista de propriedades. |

![Valores nomeados](./media/api-management-howto-properties/named-values.png)

Os valores de propriedade podem conter cadeias de caracteres literais e [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx). Por exemplo, o valor de `ExpressionProperty` é uma expressão de política que retorna uma cadeia de caracteres que contém a data e hora atuais. A propriedade `ContosoHeaderValue` é marcada como um segredo e, portanto, seu valor não é exibido.

| NOME | Valor | Segredo | Marcas |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |Falso |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |Falso | |

## <a name="to-add-and-edit-a-property"></a>Para adicionar e editar uma propriedade

![Adicionar uma propriedade](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **Valores nomeados**.
3. Pressione **+Adicionar**.

  Nome e Valor são valores obrigatórios. Se o valor dessa propriedade for confidencial, marque a caixa de seleção Valor confidencial. Insira uma ou mais marcas opcionais para ajudar a organizar suas propriedades e, em seguida, clique em Salvar.
4. Clique em **Criar**.

Quando a propriedade é criada, você pode editá-la clicando na propriedade. Se você alterar o nome da propriedade, todas as políticas que fizerem referência a essa propriedade serão automaticamente atualizadas para usar o novo nome.

Para saber mais sobre como editar uma propriedade usando a API REST, confira [Editar uma propriedade usando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Para excluir uma propriedade

Para excluir uma propriedade, clique em **Excluir** ao lado da propriedade que você quer excluir.

> [!IMPORTANT]
> Se a propriedade for consultada por quaisquer políticas, não será possível excluí-la até que você remova a propriedade de todas as políticas que a utilizam.
> 
> 

Para saber mais sobre como excluir uma propriedade usando a API REST, confira [Excluir uma propriedade usando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Para pesquisar e filtrar propriedades

A guia **Valores nomeados** inclui pesquisa e filtragem de recursos para ajudá-lo a gerenciar suas propriedades. Para filtrar a lista de propriedades pelo nome da propriedade, insira um termo de pesquisa na caixa de texto **Propriedade de pesquisa** . Para exibir todas as propriedades, desmarque a caixa de texto **Propriedade de pesquisa** e pressione enter.

Para filtrar a lista de propriedades por valores de marca, insira uma ou mais marcas na caixa de texto **Filtrar por marcas** . Para exibir todas as propriedades, desmarque a caixa de texto **Filtrar por marcas** e pressione enter.

## <a name="to-use-a-property"></a>Para usar uma propriedade

Para usar uma propriedade em uma política, coloque o nome da propriedade entre chaves como `{{ContosoHeader}}`, conforme mostrado no exemplo a seguir:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma política `set-header` e `ContosoHeaderValue` é usado como o valor desse cabeçalho. Quando essa política é avaliada durante uma solicitação ou uma resposta para o gateway de Gerenciamento de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídos pelos valores de suas respectivas propriedades.

As propriedades podem ser usadas como valores de atributo ou de elemento, como mostra o exemplo anterior, mas também podem ser inseridas ou combinadas com parte de uma expressão de texto literal, como mostra o exemplo a seguir: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

As propriedades também podem conter expressões de política. No exemplo a seguir, o `ExpressionProperty` é usado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando essa política é avaliada, `{{ExpressionProperty}}` é substituído por seu valor: `@(DateTime.Now.ToString())`. Como o valor é uma expressão de política, a expressão é avaliada e a política prossegue com a execução.

Você pode testar isso no portal do desenvolvedor chamando uma operação que tenha uma política com propriedades no escopo. No exemplo a seguir, uma operação é chamada com os dois exemplos de políticas `set-header` anteriores com propriedades. Observe que a resposta contém dois cabeçalhos personalizados configurados usando políticas com propriedades.

![Portal do desenvolvedor][api-management-send-results]

Se você analisar o [rastreamento do Inspetor de API](api-management-howto-api-inspector.md) de uma chamada que inclui os dois exemplos de política anteriores com propriedades, será possível ver as duas políticas `set-header` com os valores de propriedade inseridos, bem como a avaliação da expressão de política para a propriedade que continha a expressão de política.

![Rastreamento do Inspetor de API][api-management-api-inspector-trace]

Enquanto os valores de propriedade podem conter expressões de política, os valores de propriedade não podem conter outras propriedades. Se um texto contendo uma referência de propriedade for usado para um valor de propriedade, por exemplo, `Property value text {{MyProperty}}`, essa referência de propriedade não será substituída e será incluída como parte do valor da propriedade.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como trabalhar com políticas
  * [Políticas no Gerenciamento de API](api-management-howto-policies.md)
  * [Referência de política](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

