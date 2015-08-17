<properties
   pageTitle="Definindo dependências em modelos do Gerenciador de Recursos do Azure"
   description="Descreve como definir um recurso como dependente de outro recurso durante a implantação."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="mmercuri"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="mmercuri"/>

# Definindo dependências em modelos do Gerenciador de Recursos do Azure

Para um determinado recurso, pode haver várias dependências upstream e filho que são essenciais para o sucesso de sua topologia. Você pode definir dependências em outros recursos usando as propriedades **dependsOn** e **resources** de um recurso. Uma dependência também pode ser especificada usando a função **reference**.

    {
        "name": "<name-of-the-resource>",
        "type": "<resource-provider-namespace/resource-type-name>",
        "apiVersion": "<supported-api-version-of-resource>",
        "location": "<location-of-resource>",
        "tags": { <name-value-pairs-for-resource-tagging> },
        "dependsOn": [ <array-of-related-resource-names> ],
        "properties": { <settings-for-the-resource> },
        "resources": { <dependent-resources> }
    }

 Também há links de recursos que podem definir as relações entre os recursos e dar suporte à definição dessas relações entre os grupos de recursos.

## dependsOn

Para uma determinada máquina virtual, você pode depender de que um recurso de banco de dados seja provisionado com êxito. Em outro caso, você pode depender de que vários nós sejam instalados em seu cluster antes de implantar uma máquina virtual com a ferramenta de gerenciamento de cluster.

Em seu modelo, a propriedade dependsOn permite definir a dependência de um recurso. O valor dela pode ser uma lista separada por vírgulas de nomes de recursos. As dependências entre recursos são avaliadas e os recursos são implantados na ordem de dependência. Quando os recursos não dependem uns dos outros, é possível que tentem ser implantados paralelamente.

Embora você talvez queira usar dependsOn para mapear as dependências entre os seus recursos, é importante entender por que você está fazendo isso, uma vez que o desempenho de sua implantação pode ser afetado. Por exemplo, se você estiver fazendo isso porque deseja documentar como os recursos são interconectados, dependsOn não é a abordagem correta. O ciclo de vida de dependsOn é apenas para a implantação, não ficando disponível após a implantação. Após a implantação, não há como consultar essas dependências. Ao usar dependsOn, você corre o risco de afetar o desempenho, uma vez que é possível fazer com que o mecanismo de implantação pare inadvertidamente de usar paralelismo em situações nas quais isso seria necessário. Para documentar e fornecer capacidade de consulta para as relações entre os recursos, você deve usar a [vinculação de recursos](resource-group-link-resources.md).

Esse elemento não é necessário se a função de referência for usada para obter uma representação de um recurso, pois um objeto de referência implica uma dependência no recurso. Na verdade, se houver a opção para usar uma referência em vez de dependsOn, as diretrizes são usar a função de referência e fazer referências implícitas. A justificativa aqui é novamente o desempenho. As referências definem as dependências implícitas que são sabidamente necessárias, uma vez que são referenciadas no modelo. Por causa da presença delas, elas são relevantes, evitando novamente a otimização para desempenho e para evitar o risco potencial de fazer com que o mecanismo de implantação pare de evitar paralelismo desnecessariamente.

## recursos

A propriedade resources permite especificar os recursos filho relacionados ao recurso que está sendo definido. Os recursos filho só podem ser definidos em 5 níveis de profundidade. É importante observar que não é criada uma dependência implícita entre um recurso filho e o pai. Se precisar que o recurso filho seja implantado após o recurso pai, você deve declarar explicitamente essa dependência com a propriedade dependsOn.

## Função reference

A função reference permite que uma expressão derive seu valor de outro nome JSON e de pares de valor ou de recursos de tempo de execução. Expressões de referência declaram de maneira implícita que um recurso depende de outro. A propriedade representada por **propertyPath** abaixo é opcional e, se não for especificada, a referência será ao recurso.

    reference('resourceName').propertyPath

Você pode usar esse elemento ou o elemento dependsOn para especificar dependências, mas não é necessário usar ambos para o mesmo recurso dependente. As diretrizes são usar a referência implícita para evitar o risco de que um elemento dependsOn desnecessário faça com que o mecanismo de implantação pare inadvertidamente de realizar aspectos da implantação em paralelo.

Para obter mais informações, consulte [Função reference](../resource-group-template-functions/#reference).

## Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de Recursos do Azure, consulte [Criando modelos](resource-group-authoring-templates.md). 
- Para obter uma lista das funções disponíveis em um modelo, consulte [Funções de modelo](resource-group-template-functions.md).

<!---HONumber=August15_HO6-->