---
title: "Visão geral detalhada de planos do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como os planos do Serviço de Aplicativo para o Serviço de Aplicativo do Azure funcionam e como eles beneficiam sua experiência de gerenciamento."
keywords: "serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, custo de serviço de aplicativo"
services: app-service
documentationcenter: 
author: btardif
manager: wpickett
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5f4e2775a71c743c313831ce0cd567527c8ae5e2


---
# <a name="azure-app-service-plans-in-depth-overview"></a>Visão geral detalhada de planos de serviço de aplicativo do Azure
Um Plano do Serviço de Aplicativo representa um conjunto de recursos e capacidades que pode ser compartilhado entre seus aplicativos. Aplicativos Web, aplicativos móveis, aplicativos de funções ou aplicativos de API, no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) tudo é executado em um Plano do Serviço de Aplicativo. Esses planos dão suporte a cinco tipos de preço: *Gratuito*, *Compartilhado*, *Básico*, *Standard* e *Premium*. Cada tipo tem seus próprios recursos e capacidades. Aplicativos na mesma assinatura e localização geográfica podem compartilhar um plano. Todos os aplicativos que compartilham um plano podem usar todos os recursos e capacidades que são definidos pela camada do plano. Todos os aplicativos que estão associados a um plano são executados com os recursos definidos pelo plano.

Por exemplo, se seu plano for configurado para usar duas instâncias "pequenas" na camada de serviço padrão, todos os aplicativos associados a esse plano serão executados em ambas as instâncias e terão acesso à funcionalidade da camada de serviço padrão. Instâncias do plano nas quais aplicativos estão em execução são totalmente gerenciadas e altamente disponíveis.

Neste artigo, exploraremos as principais características, como camada e dimensionamento de um Plano do Serviço de Aplicativo e como elas entram em jogo ao gerenciar seus aplicativos.

## <a name="apps-and-app-service-plans"></a>Aplicativos e planos do Serviço de Aplicativo
Um aplicativo no Serviço de Aplicativo pode ser associado a apenas um plano de Serviço de Aplicativo de cada vez.

Os aplicativos e os planos estão contidos em um grupo de recursos. Um grupo de recursos funciona como o limite do ciclo de vida de todos os recursos que estão nele. Você pode usar grupos de recursos para gerenciar todas as partes de um aplicativo, coletivamente.

Já que um único grupo de recursos pode ter vários planos do Serviço de Aplicativo, você pode alocar aplicativos diferentes para recursos físicos diferentes. Por exemplo, você pode dividir os recursos entre ambientes de desenvolvimento, teste e produção. Ter ambientes separados para desenvolvimento/teste e produção permite isolar recursos. Dessa forma, os testes de carga em uma nova versão de seus aplicativos não competirão pelos mesmos recursos que seus aplicativos de produção, que estão atendendo a clientes reais.

Quando você tem vários planos em um único grupo de recursos, também pode definir um aplicativo que abrange várias regiões geográficas. Por exemplo, um aplicativo altamente disponível executando em duas regiões incluirá pelo menos dois planos, um para cada região e um aplicativo associado com cada plano. Nessa situação, todas as cópias do aplicativo serão associadas a um único grupo de recursos. Ter um grupo de recursos com vários planos e vários aplicativos faz com que seja fácil de gerenciar, controlar e exibir a integridade do aplicativo.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Criar um Plano do Serviço de Aplicativo ou usar um existente
Ao criar um aplicativo, você deverá considerar a criação de um grupo de recursos. Por outro lado, se o aplicativo que você está prestes a criar for um componente de um aplicativo maior, ele deverá ser criado no grupo de recursos alocado para o aplicativo maior.

Independentemente do novo aplicativo ser um aplicativo totalmente novo ou parte de um maior, é possível utilizar um plano do Serviço de Aplicativo existente para hospedá-lo ou criar um novo. Essa decisão é mais uma questão de capacidade e carga esperada.

Se esse novo aplicativo vai usar muitos recursos e têm fatores de dimensionamento diferentes dos outros aplicativos hospedados em um plano existente, recomendamos isolá-lo em seu próprio plano.

Quando você cria um novo plano, pode alocar um novo conjunto de recursos para seu aplicativo e ter mais controle sobre a alocação de recurso porque cada plano obtém seu próprio conjunto de instâncias.

Como você pode mover aplicativos entre planos, pode alterar a maneira como os recursos são alocados no aplicativo maior.

Finalmente, se você desejar criar um aplicativo em uma região diferente e essa região não tiver um plano existente, você terá que criar um plano na região para poder hospedar seu aplicativo nele.

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo
> [!TIP]
> Se tiver um ambiente de Serviço de Aplicativo, você poderá examinar a documentação específica para ambientes de Serviço de Aplicativo aqui: [Criar um Plano do Serviço de Aplicativo em um ambiente de Serviço de Aplicativo](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)
> 
> 

É possível criar um plano do Serviço de Aplicativo vazio por meio da experiência de navegação do plano do Serviço de Aplicativo ou como parte da criação do aplicativo.

No [Portal do Azure](https://portal.azure.com), clique em **Novo** > **Web + Móvel** e, em seguida, selecione **Aplicativo Web** ou outra variante de aplicativo de Serviço de Aplicativo.
![Crie um aplicativo no portal do Azure.][createWebApp]

Você pode selecionar ou criar o plano de Serviço de Aplicativo para o novo aplicativo.

 ![Crie um plano do Serviço de Aplicativo.][createASP]

Para criar um novo Plano do Serviço de Aplicativo, clique em **[+] Criar Novo**, digite o nome do **Plano do Serviço de Aplicativo** e selecione uma **Localização** apropriada. Clique em **Tipo de preço**e selecione um tipo de preço apropriado para o serviço. Selecione **Exibir tudo** para exibir mais opções de preço, como **Gratuito** e **Compartilhado**. Depois de selecionar o tipo de preço, clique no botão **Selecionar** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Mover um aplicativo para um plano de Serviço de Aplicativo diferente
Você pode mover um aplicativo para um plano do Serviço de Aplicativo diferente no [portal do Azure](https://portal.azure.com). Você pode mover aplicativos entre planos, desde que os planos estejam no mesmo grupo de recursos e na mesma região geográfica.

Para mover um aplicativo para outro plano, vá para o aplicativo que deseja mover. No menu **Configurações**, procure **Alterar Plano do Serviço de Aplicativo**.

**Alterar Plano do Serviço de Aplicativo** abre o seletor **Plano do Serviço de Aplicativo**. Nesse ponto, você poderá selecionar um plano existente ou criar um novo. Apenas planos válidos (no mesmo grupo de recursos e na mesma localização geográfica) são mostrados.

![Seletor de plano do Serviço de Aplicativo.][change]

Cada plano tem seu próprio tipo de preço. Por exemplo, ao mover um site de uma camada Gratuita para uma camada Standard, seu aplicativo poderá usar todos os recursos da camada Standard.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Clonar um aplicativo para um plano diferente do Serviço de Aplicativo
Se desejar mover o aplicativo para uma região diferente, uma alternativa será a clonagem do aplicativo. A clonagem fará uma cópia de seu aplicativo em um ambiente ou plano do Serviço de Aplicativo novo ou existente em qualquer região.

 ![Clone um aplicativo.][appclone]

Você pode encontrar a opção **Clonar Aplicativo** no menu **Ferramentas**.

A clonagem tem algumas limitações que você pode ler a respeito em [Clonagem de aplicativo do Serviço de Aplicativo do Azure usando o portal do Azure](../app-service-web/app-service-web-app-cloning-portal.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano de Serviço de Aplicativo
Há três maneiras de dimensionar um plano:

* **Alterar o tipo de preço do plano**. Por exemplo, um plano na camada Básica pode ser convertido para a camada Standard ou Premium e todos os aplicativos associados a esse plano poderão usar os recursos oferecidos na nova camada de serviço.
* **Alterar a contagem de instâncias do plano**. Por exemplo, um plano na camada Básica que usa instâncias pequenas pode ser alterado para usar instâncias grandes. Todos os aplicativos associados a esse plano poderão usar a memória adicional e os recursos de CPU oferecidos pelo tamanho de instância maior.
* **Alterar a contagem de instâncias do plano**. Por exemplo, um plano Standard que é escalado horizontalmente para três instâncias pode ser dimensionado para dez instâncias. Um plano Premium pode ser escalado horizontalmente para 20 instâncias (sujeito a disponibilidade). Todos os aplicativos associados a esse plano poderão usar a memória adicional e os recursos de CPU oferecidos pela contagem de instâncias maior.

É possível alterar o tipo de preço e o tamanho de instância clicando no item **Escalar Verticalmente** nas configurações do aplicativo ou do Plano do Serviço de Aplicativo. As alterações são aplicadas ao Plano do Serviço de Aplicativo e afetam todos os aplicativos hospedados por ele.

 ![Defina valores para escalar um aplicativo verticalmente.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Limpeza do Plano do Serviço de Aplicativo
Os **Planos do Serviço de Aplicativo** que não têm aplicativos associados a eles ainda incorrerão em taxas, pois eles continuam a reservar a capacidade de computação configurada nas propriedades de escala do Plano do Serviço de Aplicativo.
Para evitar encargos inesperados, quando o último aplicativo hospedado em um Plano do Serviço de Aplicativo é excluído, o Plano do Serviço de Aplicativo vazio resultante também é excluído.

## <a name="summary"></a>Resumo
Os planos de Serviço de Aplicativo representam um conjunto de recursos e capacidades que pode ser compartilhado entre seus aplicativos. Os planos do Serviço de Aplicativo oferecem a flexibilidade para alocar aplicativos específicos para um conjunto de recursos e otimizar ainda mais sua utilização de recursos do Azure. Dessa forma, se quiser economizar dinheiro em seu ambiente de teste, você poderá compartilhar um plano entre vários aplicativos. Você também pode maximizar a produtividade para seu ambiente de produção dimensionando-o em várias regiões e planos.

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png



<!--HONumber=Nov16_HO3-->


