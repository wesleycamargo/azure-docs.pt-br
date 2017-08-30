---
title: "Visão geral detalhada de planos do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como os planos do Serviço de Aplicativo para o Serviço de Aplicativo do Azure funcionam e como eles beneficiam sua experiência de gerenciamento."
keywords: "serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, custo de serviço de aplicativo"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: f97be571d104e3cc1c6ee732886fa7133ba0dc83
ms.contentlocale: pt-br
ms.lasthandoff: 08/19/2017

---
# <a name="azure-app-service-plans-in-depth-overview"></a>Visão geral detalhada de planos de serviço de aplicativo do Azure

Os Planos do Serviço de Aplicativo representam a coleta de recursos físicos usados para hospedar seus aplicativos.

Os Planos do Serviço de Aplicativo definem:

- Região (Oeste dos EUA, Leste dos EUA, etc.)
- Contagem da escala (uma, duas, três instâncias, etc.)
- Tamanha da instância (Pequena, Média, Grande)
- SKU (Gratuito, Compartilhado, Básico, Standard, Premium)

Aplicativos Web, Aplicativos Móveis, Aplicativos de API, Aplicativos de Funções (ou Funções) no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) todos executados em um plano do Serviço de Aplicativo.  Aplicativos na mesma assinatura e região podem compartilhar um Plano do Serviço de Aplicativo. 

Todos os aplicativos atribuídos a um **Plano do Serviço de Aplicativos** compartilham os recursos definidos pelo plano. Esse compartilhamento economiza dinheiro ao hospedar vários aplicativos em um único plano do Serviço de Aplicativo.

O seu **Plano do Serviço de Aplicativo** pode ser dimensionado dos SKUs **Gratuito** e **Compartilhado** para os SKUs **Básico**, **Standard** e **Premium**, dando a você mais recursos e funções ao longo do caminho.

Depois que seu Plano do Serviço de Aplicativo for definido como um SKU **Básico** ou superior, então será possível controlar o **tamanho** e dimensionar a contagem de VMs.

Por exemplo, se seu plano for configurado para usar duas instâncias "pequenas" na camada de serviço padrão, todos os aplicativos associados a esse plano serão executados em ambas as instâncias. Os aplicativos também terão acesso aos recursos da camada de serviço padrão. Instâncias do plano nas quais aplicativos estão em execução são totalmente gerenciadas e altamente disponíveis.

> [!IMPORTANT]
> O **SKU** e a **Escala** do plano do Serviço de Aplicativo determina o custo e não o número de aplicativos hospedados nele.

Neste artigo, exploraremos as principais características, como camada e dimensionamento de um Plano do Serviço de Aplicativo e como elas entram em jogo ao gerenciar seus aplicativos.

## <a name="apps-and-app-service-plans"></a>Aplicativos e planos do Serviço de Aplicativo

Um aplicativo no Serviço de Aplicativo pode ser associado a apenas um plano de Serviço de Aplicativo de cada vez.

Os aplicativos e os planos estão contidos em **grupo de recursos**. Um grupo de recursos funciona como o limite do ciclo de vida de todos os recursos que estão nele. Você pode usar grupos de recursos para gerenciar todas as partes de um aplicativo, coletivamente.

Já que um único grupo de recursos pode ter vários planos do Serviço de Aplicativo, você pode alocar aplicativos diferentes para recursos físicos diferentes.

Por exemplo, você pode dividir os recursos entre ambientes de desenvolvimento, teste e produção. Ter ambientes separados para desenvolvimento/teste e produção permite isolar recursos. Dessa forma, os testes de carga em uma nova versão de seus aplicativos não competirão pelos mesmos recursos que seus aplicativos de produção, que estão atendendo a clientes reais.

Quando você tem vários planos em um único grupo de recursos, também pode definir um aplicativo que abrange várias regiões geográficas.

Por exemplo, um aplicativo altamente disponível executando em duas regiões incluirá pelo menos dois planos, um para cada região e um aplicativo associado com cada plano. Nessa situação, todas as cópias do aplicativo serão associadas a um único grupo de recursos. Ter um grupo de recursos com vários planos e vários aplicativos faz com que seja fácil de gerenciar, controlar e exibir a integridade do aplicativo.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Criar um Plano do Serviço de Aplicativo ou usar um existente

Ao criar um aplicativo, você deverá considerar a criação de um grupo de recursos. Por outro lado, se esse aplicativo for um componente para um aplicativo maior, crie-o dentro do grupo de recursos alocado ao aplicativo maior.

Independentemente do aplicativo ser totalmente novo ou fazer parte de um aplicativo maior, é possível optar por usar um plano existente para hospedá-lo ou criar um novo. Essa decisão é mais uma questão de capacidade e carga esperada.

É recomendável isolar seu aplicativo em um novo Plano do Serviço de Aplicativo quando:

- O aplicativo faz uso intensivo de recursos.
- O aplicativo tem fatores de dimensionamento diferentes dos outros aplicativos hospedados em um plano existente.
- O aplicativo precisa de recursos em uma região geográfica diferente.

Dessa forma, você pode alocar um novo conjunto de recursos para seu aplicativo e ter mais controle sobre seus aplicativos.

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

> [!TIP]
> Se você tiver um Ambiente do Serviço de Aplicativo, poderá examinar a documentação específica para Ambientes do Serviço de Aplicativo aqui: [Criar um Plano do Serviço de Aplicativo em um Ambiente do Serviço de Aplicativo](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

É possível criar um plano do Serviço de Aplicativo vazio por meio da experiência de navegação do plano do Serviço de Aplicativo ou como parte da criação do aplicativo.

No [Portal do Azure](https://portal.azure.com), clique em **Novo** > **Web + Móvel** e, em seguida, selecione **Aplicativo Web** ou outra variante de aplicativo de Serviço de Aplicativo.

![Crie um aplicativo no portal do Azure.][createWebApp]

Você pode selecionar ou criar o plano de Serviço de Aplicativo para o novo aplicativo.

 ![Crie um plano do Serviço de Aplicativo.][createASP]

Para criar um Plano do Serviço de Aplicativo, clique em **[+] Criar Novo**, digite o nome do **Plano do Serviço de Aplicativo** e selecione uma **Localização** apropriada. Clique em **Tipo de preço**e selecione um tipo de preço apropriado para o serviço. Selecione **Exibir tudo** para exibir mais opções de preço, como **Gratuito** e **Compartilhado**. Depois de selecionar o tipo de preço, clique no botão **Selecionar** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Mover um aplicativo para um plano de Serviço de Aplicativo diferente

Você pode mover um aplicativo para um Plano do Serviço de Aplicativo diferente no [Portal do Azure](https://portal.azure.com). Você pode mover aplicativos entre planos, desde que os planos estejam no mesmo grupo de recursos e na mesma região geográfica.

Para mover um aplicativo para outro plano:

- Navegue até o aplicativo que deseja mover.
- No **Menu**, procure a seção **Plano do Serviço de Aplicativo**.
- Selecione **Alterar plano do Serviço de Aplicativo** para iniciar o processo.

**Alterar Plano do Serviço de Aplicativo** abre o seletor **Plano do Serviço de Aplicativo**. Neste ponto, você pode escolher um plano existente para mover esse aplicativo.

> [!IMPORTANT]
> A interface do usuário de seleção de Plano do Serviço de Aplicativo é filtrada pelos seguintes critérios:
> - Existe dentro do mesmo grupo de recursos
> - Existe na mesma região geográfica
> - Existe dentro do mesmo espaço da Web
>
> Um espaço Web é uma construção lógica dentro do Serviço de Aplicativo que define um agrupamento de recursos do servidor. Uma região geográfica (por exemplo, Oeste dos EUA) contém muitos espaços na Web para alocar os clientes que usam o Serviço de Aplicativo. No momento, os recursos do Serviço de Aplicativo não podem ser movidos entre espaços da Web.
>

![Seletor de plano do Serviço de Aplicativo.][change]

Cada plano tem seu próprio tipo de preço. Por exemplo, mover um site de uma camada Gratuita para uma camada Standard habilita todos os aplicativos aplicados a ela a usarem os recursos da camada Standard.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Clonar um aplicativo para um plano diferente do Serviço de Aplicativo

Se desejar mover o aplicativo para uma região diferente, uma alternativa será a clonagem do aplicativo. A clonagem faz uma cópia do seu aplicativo em um novo plano do Serviço de Aplicativo existente em qualquer região.

É possível localizar **Clonar aplicativo** na seção **Ferramentas de Desenvolvimento** do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações que você pode ler a respeito em [Clonagem de aplicativo do Serviço de Aplicativo do Azure usando o portal do Azure](../app-service-web/app-service-web-app-cloning-portal.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano de Serviço de Aplicativo

Há três maneiras de dimensionar um plano:

- **Alterar o tipo de preço do plano**. Um plano na camada Básica pode ser convertido em Standard e todos os aplicativos atribuídos a ela poderão usar os recursos da camada Standard.
- **Alterar a contagem de instâncias do plano**. Por exemplo, um plano na camada Básica que usa instâncias pequenas pode ser alterado para usar instâncias grandes. Todos os aplicativos associados a esse plano poderão usar a memória adicional e os recursos de CPU oferecidos pelo tamanho de instância maior.
- **Alterar a contagem de instâncias do plano**. Por exemplo, um plano Standard que é escalado horizontalmente para três instâncias pode ser dimensionado para dez instâncias. Um plano Premium pode ser escalado horizontalmente para 20 instâncias (sujeito a disponibilidade). Todos os aplicativos associados a esse plano poderão usar a memória adicional e os recursos de CPU oferecidos pela contagem de instâncias maior.

É possível alterar o tipo de preço e o tamanho de instância clicando no item **Escalar Verticalmente** nas configurações do aplicativo ou do Plano do Serviço de Aplicativo. As alterações são aplicadas ao Plano do Serviço de Aplicativo e afetam todos os aplicativos hospedados por ele.

 ![Defina valores para escalar um aplicativo verticalmente.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Limpeza do Plano do Serviço de Aplicativo

> [!IMPORTANT]
> Os **Planos do Serviço de Aplicativo** que não têm aplicativos associados a eles ainda incorrerão em encargos, pois continuam a reservar a capacidade de computação.

Para evitar encargos inesperados, quando o último aplicativo hospedado em um Plano do Serviço de Aplicativo é excluído, o Plano do Serviço de Aplicativo vazio resultante também é excluído.

## <a name="summary"></a>Resumo

Os planos de Serviço de Aplicativo representam um conjunto de recursos e capacidades que pode ser compartilhado entre seus aplicativos. Os planos do Serviço de Aplicativo oferecem a flexibilidade para alocar aplicativos específicos para um conjunto de recursos e otimizar ainda mais sua utilização de recursos do Azure. Dessa forma, se quiser economizar dinheiro em seu ambiente de teste, você poderá compartilhar um plano entre vários aplicativos. Você também pode maximizar a produtividade para seu ambiente de produção dimensionando-o em várias regiões e planos.

## <a name="whats-changed"></a>O que mudou

- Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png

