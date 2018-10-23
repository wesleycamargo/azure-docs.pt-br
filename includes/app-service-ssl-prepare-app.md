---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 858d098f18604d1360af8509eb8a0cec6fa7d0c7
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354508"
---
## <a name="prepare-your-web-app"></a>Preparar o aplicativo Web

Para associar um certificado SSL personalizado (um certificado de terceiros ou certificado de Serviço de Aplicativo) ao aplicativo Web, o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) deve estar na camada **Básico**, **Standard**, **Premium** ou **Isolado**. Nesta etapa, você precisa ter certeza de que seu aplicativo Web está no tipo de preço com suporte.

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Abra o [Portal do Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navegue até seu aplicativo Web

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do aplicativo Web.

![Selecionar aplicativo Web](./media/app-service-ssl-prepare-app/select-app.png)

Você aterrissou na página de gerenciamento do aplicativo Web.  

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

No painel de navegação à esquerda da página do aplicativo Web, role até a seção **Configurações** e selecione **Escalar verticalmente (plano do Serviço de Aplicativo)**.

![Menu Escalar verticalmente](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Certifique-se de que o aplicativo Web não esteja na camada **F1** ou **D1**. A camada atual do seu aplicativo Web é realçada por uma caixa azul escuro.

![Verificar tipo de preço](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Não há suporte para SSL personalizado na camada **F1** ou **D1**. Se precisar escalar verticalmente, siga as etapas da próxima seção. Caso contrário, feche a página **Escalar verticalmente** e vá para [Carregar e associar o certificado SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Escalar verticalmente seu Plano do Serviço de Aplicativo

Selecione qualquer uma das camadas não gratuitas (**B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

![Escolha um tipo de preço](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

![Escalar verticalmente a notificação](./media/app-service-ssl-prepare-app/scale-notification.png)

