---
title: '(preterido) Perguntas frequentes: publicar e usar aplicativos do Machine Learning no Azure Marketplace | Microsoft Docs'
description: "(preterido) Perguntas frequentes sobre a publicação de aplicativos do Machine Learning no Azure Marketplace"
services: machine-learning
documentationcenter: 
author: bharaths
manager: jhubbard
editor: cgronlun
ms.assetid: 26b3a1e7-8b9a-4004-98bc-17456d4c25e8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: bharaths
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 85a58e09e05fdb50984055ab25c4c2fe520dab4d
ms.openlocfilehash: 9e2d4a2d46bd461a71217cd2b2d75f5a50e8b617


---
# <a name="deprecated-publishing-and-using-machine-learning-apps-in-the-azure-marketplace-faq"></a>(preterido) Publicação e utilização de aplicativos do Machine Learning no Azure Marketplace: Perguntas Frequentes

> [!NOTE]
> O DataMarket e os Serviços de Dados estão sendo desativados e as assinaturas existentes serão desativadas e canceladas a partir de 31/3/2017. Como resultado, este artigo está sendo preterido. 
> 
> Como alternativa, você pode publicar suas experiências com o Machine Learning na [Galeria do Cortana Intelligence](https://gallery.cortanaintelligence.com/) para o benefício da comunidade de ciência de dados. Para saber mais, confira [Compartilhar e descobrir soluções na Galeria do Cortana Intelligence](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-gallery-how-to-use-contribute-publish).


## <a name="questions-about-consuming-from-marketplace"></a>Perguntas sobre o consumo do Marketplace
**1. Por que recebo a seguinte mensagem de erro depois que insiro a entrada para o serviço Web:**

**A solicitação resultou em um erro de back-end ou em tempo limite de back-end. A equipe está investigando o problema. Lamentamos pelo inconveniente. (500)**

Seus parâmetros de entrada podem não estar de acordo com o formato necessário para o serviço Web específico. Consulte o link de documentação correspondente para localizar o formato correto para parâmetros de entrada e as limitações do serviço Web.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**2. Se eu copiar o link da API para o serviço Web que vejo na página "Explorar esse conjunto de dados" e colá-lo em outra janela do navegador, quais credenciais devo usar para acessar os resultados e como vê-los?**

Você deve usar sua conta do Marketplace como o nome de usuário e a chave de conta principal como a senha. A chave de conta principal pode ser encontrada na página **Explorar este conjunto de dados** na descrição do serviço Web (clique no botão **mostrar**). O resultado pode ser exibido no navegador ou ele pode estar disponível para download, dependendo do navegador que você estiver usando.

**3. Por que recebo a seguinte mensagem de erro depois de inserir a entrada para o serviço Web na página "Explorar esse conjunto de dados":** 

**Ocorreu um erro inesperado ao processar a sua solicitação. Tente novamente.**

Um ou mais parâmetros de entrada do serviço Web podem ter excedido o limite de tamanho ao consumir o serviço Web na página **explorar este conjunto de dados** do Marketplace. Os serviços podem ser chamados com um comprimento de entrada maior usando métodos HTTP POST. Para obter exemplos, consulte [Soluções de exemplo usando R no Aprendizado de Máquina e publicadas no Marketplace](machine-learning-r-csharp-web-service-examples.md).

**4. Por que não vejo nada na guia “GERENCIADOR DE API” no Repositório do Portal Clássico do Azure?** 

Esse é um problema conhecido no Marketplace do Portal Clássico do Azure. A equipe está trabalhando para resolver esse problema. 

## <a name="questions-about-publishing-from-azure-machine-learning-on-marketplace"></a>Perguntas sobre a publicação de Aprendizado de Máquina do Azure no Marketplace
**1. Por que as minhas transações de logotipos ou imagens não são atualizadas para o meu serviço Web?** 

Os logotipos e imagens são armazenados em cache no portal de publicação, e pode levar até 10 dias para o novo logotipo ou imagem ser atualizado no portal.

**2. Por que a guia "Detalhes" do meu serviço Web no Marketplace está mostrando uma mensagem de erro?**

Há um problema conhecido no Marketplace ao se conectar ao Aprendizado de Máquina do Azure para detalhes do serviço. A equipe está trabalhando para resolver esse problema.

**3. Por que o código de exemplo R em serviços Web de Aprendizado de Máquina do Azure não funciona para consumir os serviços Web no Marketplace?**

Os sistemas de autenticação são diferentes ao se conectar aos serviços Web de Aprendizado de Máquina do Azure diretamente comparados ao conectar-se a esses serviços Web por meio do Marketplace. Os serviços no Marketplace são serviços OData e podem ser chamados com métodos GET ou POST. 

**4. Por que os links de suporte das minhas ofertas de serviço Web não estão atualizando corretamente para algumas das minhas ofertas?**

Os links de suporte são globais por editor, não por oferta. 

**5. Como eu publico um serviço Web com o modo de entrada em lote no Marketplace?**

No momento, não há suporte para o modo de entrada de lote em serviços Web do Marketplace.

**6. Quem devo contatar para obter ajuda se tiver dúvidas sobre como me tornar um editor de dados ou se tiver problemas durante a publicação?**

Entre em contato com a equipe do Azure Marketplace em <mailto:datamarketbd@microsoft.com> para obter mais informações.




<!--HONumber=Jan17_HO2-->


