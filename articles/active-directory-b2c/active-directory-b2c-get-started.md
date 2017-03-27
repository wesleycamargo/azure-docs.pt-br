---
title: "Azure Active Directory B2C: criar um locatário do Azure Active Directory B2C | Microsoft Docs"
description: "Um tópico sobre como criar um locatário zure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 02bb7286f6cd42f86e6cc920742a86d57ed93b60
ms.lasthandoff: 03/09/2017


---
# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C: criar um locatário do Azure AD B2C
Para começar a usar o Microsoft Azure Active Directory (Azure AD) B2C, siga as quatro etapas descritas neste artigo.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Etapa 1: inscrever-se para uma assinatura do Azure
Se você já tiver uma assinatura do Azure, ignore esta etapa. Se não tiver, inscreva-se para obter uma [assinatura do Azure](../active-directory/sign-up-organization.md) e receber acesso ao Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Etapa 2: criar um locatário do Azure AD B2C
Use as seguintes etapas para criar um novo locatário B2C do AD do Azure. Atualmente, os recursos de B2C não podem ser ativados em seus locatários existentes.

1. Entre no [portal do Azure](https://portal.azure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para se inscrever no Azure.
2. Clique em **Novo**(ou no botão + se recolhido) e no campo **Pesquisar no marketplace** insira "Azure Active Directory B2C".
   
    ![Captura de tela para encontrar a opção Azure AD B2C](./media/active-directory-b2c-get-started/find-azure-ad-b2c.png)
3. No resultado, clique em **Azure Active Directory B2C**

    ![Captura de tela de resultado para o Azure Active Directory B2C](./media/active-directory-b2c-get-started/find-azure-ad-b2c-result.png)
4. Será exibida uma página com os detalhes sobre B2C.  Clique em **Criar** na parte inferior para começar a configurar o novo locatário do Azure Active Directory B2C.
5. Clique em **Criar um novo Locatário Azure AD B2C**.
6. Escolha o **Nome da organização, Nome de domínio e País ou Região** para seu locatário.

    ![Captura de tela do formulário para criar um novo locatário](./media/active-directory-b2c-get-started/create-new-b2c-tenant.png)
7. Clique em **Criar** para criar seu novo locatário.  Isso pode levar alguns minutos, você será alertado nas suas notificações quando ela for concluída.

8. O locatário foi criado e aparecerá na extensão do Active Directory. Um Administrador Global do locatário também pode ser criado. Você pode adicionar outros Administradores Globais conforme necessário.
   
   > [!IMPORTANT]
   > Se você está planejando usar um locatário B2C para um aplicativo de produção, leia o artigo sobre [escala de produção versus locatários de visualização do B2C](active-directory-b2c-reference-tenant-type.md). Observe que há problemas conhecidos quando você exclui um locatário B2C existente e o recria com o mesmo nome de domínio. Você precisa criar um locatário B2C com um nome de domínio diferente.
   > 
   > 

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Etapa 3: navegar até a folha de Recursos do B2C no Portal do Azure
1. Expanda **Mais serviços** abaixo da barra de navegação à esquerda.
2. Procure por **Azure AD B2C** e clique no resultado (é possível adicioná-lo aos favoritos para facilitar o acesso no futuro).

    ![Captura de tela de pesquisa no painel de navegação para Azure AD B2C](./media/active-directory-b2c-get-started/navigate-to-azure-ad-b2c.png)

3. O Portal do Azure mostrando a folha de recursos do B2C será aberto em uma nova janela ou guia do navegador.
   
4. Fixe essa folha no seu Quadro inicial para facilitar o acesso. (A ferramenta Fixar está no canto superior direito da folha de recursos).
   
    ![Captura de tela da folha de recursos do B2C e botão de fixar](./media/active-directory-b2c-get-started/b2c-pin-tenant.png)

## <a name="step-4-link-your-azure-ad-b2c-tenant-to-your-azure-subscription"></a>Etapa 4: Vincular seu locatário do Azure AD B2C à sua assinatura do Azure
Se você estiver planejando usar seu locatário B2C para aplicativos de produção, será necessário vincular seu locatário do Azure AD B2C a sua assinatura do Azure para pagar pelos encargos de uso. Leia [este artigo](active-directory-b2c-how-to-enable-billing.md) para saber como fazer isso.

   > [!IMPORTANT]
   > Se você não vincular seu locatário do Azure AD B2C à sua assinatura do Azure, verá uma mensagem de aviso ("Nenhuma assinatura vinculada a este locatário B2C ou A assinatura precisa de sua atenção.") na folha de recursos B2C no Portal do Azure. É importante que você execute esta etapa antes de enviar seus aplicativos para produção.
   > 
   > 

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Acesso fácil à folha de recursos do B2C no portal do Azure
Para melhorar a capacidade de descoberta, adicionamos um atalho para a folha de recursos do B2C no portal do Azure.

1. Entre no portal do Azure como Administrador Global de seu locatário do B2C. Se você já entrou em um locatário diferente, alterne os locatários (no canto superior direito).
2. Clique em **Procurar** na barra de navegação à esquerda.
3. Clique em **Azure AD B2C** para acessar a folha de recursos do B2C.
   
    ![Captura de tela de Navegar para a folha de recursos do B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Próximas etapas
Saiba como registrar um aplicativo com o Azure AD B2C e compilar um aplicativo de Início Rápido lendo [Azure Active Directory B2C: registrar seu aplicativo](active-directory-b2c-app-registration.md).


