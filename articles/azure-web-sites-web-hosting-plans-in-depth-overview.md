<properties 
	pageTitle="Visão geral detalhada de planos do Serviço de Aplicativo do Azure" 
	description="Saiba como os planos do Serviço de Aplicativo para o Serviço de Aplicativo do Azure funcionam e como eles beneficiam sua experiência de gerenciamento." 
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2014" 
	ms.author="byvinyal"/>

#Visão geral detalhada dos planos do Serviço de Aplicativo do Azure#

Um **plano do Serviço de Aplicativo** representa um conjunto de recursos e a capacidade que podem ser compartilhados entre vários aplicativos no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714), inclusive Aplicativos Web, Aplicativos Móveis, Aplicativos Lógicos ou Aplicativos de API. Esses planos dão suporte a cinco camadas de preços (**Grátis**, **Compartilhado**, **Básico**, **Padrão** e **Premium**), em que cada camada tem seus próprios recursos e capacidade. Aplicativos na mesma assinatura e localização geográfica podem compartilhar um plano. Todos os aplicativos que compartilham um plano podem aproveitar todos os recursos e recursos definidos pela camada do plano. Todos os aplicativos associados a determinado plano são executados nos recursos definidos pelo plano. Por exemplo, se seu plano for configurado para usar duas instâncias "pequenas" na camada de serviço padrão, todos os aplicativos associados a esse plano serão executados em ambas as instâncias e terão acesso à funcionalidade da camada de serviço padrão. Instâncias do plano nas quais aplicativos estão em execução são totalmente gerenciadas e altamente disponíveis.

Neste artigo, exploraremos as principais características, como camada e dimensionamento de um plano de Serviço de Aplicativo e como elas entram em jogo ao gerenciar seus aplicativos.

##Aplicativos e plano de Serviço de Aplicativo

Um aplicativo no Serviço de Aplicativo pode ser associado a apenas um plano de Serviço de Aplicativo de cada vez. 

Os aplicativos e os planos estão contidos em um grupo de recursos. Um grupo de recursos funciona como o limite do ciclo de vida de todos os recursos contidos nele. Os grupos de recursos permitem que você gerencie todas as partes de um aplicativo, coletivamente.

A capacidade de ter vários planos de Serviço de Aplicativo em um único grupo de recursos permite que você aloque aplicativos diferentes para recursos físicos diferentes. Por exemplo, isso permite a separação de recursos entre ambientes de desenvolvimento, teste e produção. Um cenário para isso é quando talvez você queira alocar um plano com seu próprio conjunto dedicado de recursos para seus aplicativos de produção e um segundo plano para seus ambientes de desenvolvimento e teste. Dessa forma, os testes de carga em uma nova versão de seus aplicativos não competirão pelos mesmos recursos que seus aplicativos de produção, que estão atendendo a clientes reais.

Ter vários planos em um único grupo de recursos também o habilita a definir um aplicativo que abrange várias regiões geográficas. Por exemplo, um aplicativo altamente disponível executando em duas regiões incluirá dois planos, um para cada região e um aplicativo associado com cada plano. Nessa situação, todas as cópias do aplicativo serão associadas a um único grupo de recursos. Ter uma exibição única de um grupo de recursos com vários planos e vários aplicativos faz com que seja fácil de gerenciar, controlar e exibir a integridade do aplicativo.

## Criar um novo plano de Serviço de Aplicativo versus usar um plano existente

Ao criar um novo aplicativo, deve-se considerar a criação de um novo grupo de recursos quando o aplicativo que você está prestes a criar representa um projeto inteiramente novo. Nesse caso, criar um novo grupo de recursos, plano e aplicativo é a escolha certa.

Se o aplicativo que você está prestes a criar for um componente de um aplicativo maior, esse aplicativo Web deverá ser criado no grupo de recursos alocado para o aplicativo maior.

Independentemente do novo aplicativo ser um aplicativo totalmente novo ou parte de um maior, é possível utilizar um plano de Serviço de Aplicativo existente para hospedá-lo ou criar um novo. Essa é mais uma questão de capacidade e carga esperada. Se esse novo aplicativo fará uso intenso de recursos e terá fatores de dimensionamento diferentes dos outros aplicativos hospedados em um plano existente, recomenda-se isolá-lo em seu próprio plano.

Criar um novo plano permite alocar um novo conjunto de recursos para seu aplicativo Web e oferece maior controle sobre a alocação de recurso, já que cada plano é criado com seu próprio conjunto de instâncias.
 
Ter a capacidade de mover aplicativos entre planos também permite alterar a forma como os recursos são alocados pelo aplicativo maior.
 
Finalmente, se você desejar criar um novo aplicativo em uma região diferente, e essa região não tiver um plano existente, você terá que criar um novo plano na região para poder hospedar seu aplicativo nele.

##Perguntas frequentes sobre planos de Serviço de Aplicativo

**Pergunta**: Como criar um plano de Serviço de Aplicativo?

**Resposta**: Não é possível criar um plano de Serviço de Aplicativo vazio. No entanto, um novo plano pode ser criado explicitamente durante a criação do aplicativo.

Para fazer isso no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), clique em **NOVO**, selecione **Web + móvel** e selecione **Aplicativos Web**, **Aplicativos Móveis**, **Aplicativos Lógicos** ou **Aplicativos de API**. Você pode selecionar ou criar o plano de Serviço de Aplicativo para o novo aplicativo.
 
![App Service Plan F.A.Q.](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png)

**Pergunta**: Como atribuir um aplicativo a um **Plano de Serviço de Aplicativo**?

**Resposta**: Os aplicativos podem ser atribuídos a um plano existente durante o processo de criação.

Para fazer isso no [Portal do Azure](http://portal.azure.com), clique em **NOVO**, selecione **Web + Móvel** e selecione **Aplicativos Web**, **Aplicativos Móveis**, **Aplicativos Lógicos** ou **Aplicativos de API**. Você pode selecionar ou criar o plano de Serviço de Aplicativo para o novo aplicativo. Clicar em **Ou selecionar existente** fornecerá a lista de planos existentes dentre os quais você pode escolher.

![App Service Plan F.A.Q.](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png)
 
**Pergunta**: Como mover um aplicativo para um plano de Serviço de Aplicativo diferente?

**Resposta**: Você pode mover um aplicativo para um plano de Serviço de Aplicativo diferente no [Portal do Azure](http://portal.azure.com). Os aplicativos podem ser movidos entre planos na mesma região geográfica.

Para mover um aplicativo para outro plano, navegue até o aplicativo que você deseja mover e clique em **Alterar Plano de Serviço de Aplicativo**.
 
Isso abrirá a folha do Plano de Serviço de Aplicativo. Nesse ponto, você poderá selecionar um plano existente ou criar um novo. Planos em uma localização geográfica diferente permanecem esmaecidos e não podem ser selecionados.

![App Service Plan F.A.Q.](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png)

Observe que cada plano tem sua própria camada de preços. Quando você mover um site de uma camada **Grátis** para uma camada **Padrão**, seu aplicativo poderá aproveitar todos os recursos da camada **Padrão**.

**Pergunta**: Como dimensionar um plano de Serviço de Aplicativo?

**Resposta**: Há três maneiras de dimensionar um plano:

- Altere a **camada de preços** do plano. Por exemplo, um plano na camada **Básica** pode ser convertido para a camada **Padrão** ou **Premium**, e todos os aplicativos associados a esse plano poderão aproveitar os recursos oferecidos na nova camada de serviço.
- Altere o **tamanho da instância** do plano. Por exemplo, um plano na camada **Básica** usando instâncias **pequenas** pode ser alterado para usar instâncias **grandes**. Todos os aplicativos associados a esse plano poderão aproveitar a memória adicional e os recursos de CPU oferecidos pelo tamanho de instância maior.
- Altere a **contagem de instâncias** do plano. Por exemplo, um plano **Padrão** dimensionado para três instâncias pode ser dimensionado para 10 instâncias, e um plano **Premium** (visualização) pode ser dimensionado para 20 instâncias (com algumas restrições). Todos os aplicativos associados a esse plano poderão aproveitar a memória adicional e os recursos de CPU oferecidos pela contagem de instâncias maior.

Na imagem abaixo, é possível ver a folha **Plano de Serviço de Aplicativo**, bem como a folha **Camada de Preços**. Clicar na parte de **Camada de Preços** na folha **Plano de Serviço de Aplicativo** expande a folha **Camada de Preços**, em que você pode alterar a camada de preços e o tamanho de instância para o plano.
 
![App Service Plan F.A.Q.](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png)

##Resumo

Os planos de Aplicativo de Serviço representam um conjunto de recursos e habilidades que você pode compartilhar através de seus aplicativos. Os planos de Serviço de Aplicativo oferecem a flexibilidade para alocar aplicativos específicos para determinado conjunto de recursos e otimizar ainda mais sua utilização de recursos do Azure. Dessa forma, se quiser economizar dinheiro em seu ambiente de teste, você poderá compartilhar um plano entre vários aplicativos. Você também pode maximizar a produtividade para seu ambiente de produção dimensionando-o em várias regiões e planos.

## O que mudou

* Para obter um guia para a alteração de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e Seu Impacto sobre os Serviços Existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->