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
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 36268d0153f4f61d6e6132f4e6a878b11cf7f6c2


---
# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C: criar um locatário do Azure AD B2C
Para começar a usar o Azure AD (Microsoft Azure Active Directory) B2C, siga as três etapas descritas neste artigo.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Etapa 1: inscrever-se para uma assinatura do Azure
Se você já tiver uma assinatura do Azure, ignore esta etapa. Se não tiver, inscreva-se para obter uma [assinatura do Azure](../active-directory/sign-up-organization.md) e receber acesso ao Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Etapa 2: criar um locatário do Azure AD B2C
Use as seguintes etapas para criar um novo locatário B2C do AD do Azure. Atualmente, os recursos de B2C não podem ser ativados em seus locatários existentes.

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para se inscrever no Azure.
2. Clique em **Novo** > **Serviços de Aplicativos** > **Active Directory** > **Diretório** > **Criação Personalizada**.
   
    ![Captura de tela do início da criação de um locatário](./media/active-directory-b2c-get-started/new-directory.png)
3. Escolha o **Nome**, **Nome de Domínio** e **País ou Região** para seu locatário.
4. Marque a opção que indica **Este é um diretório B2C**.
5. Clique na marca de seleção para concluir a ação.
   
    ![Captura de tela da marca de seleção para criar um diretório do B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)
6. O locatário foi criado e aparecerá na extensão do Active Directory. Um Administrador Global do locatário também pode ser criado. Você pode adicionar outros Administradores Globais conforme necessário.
   
   > [!IMPORTANT]
   > Se você está planejando usar um locatário B2C para um aplicativo de produção, leia o artigo sobre [escala de produção versus locatários de visualização do B2C](active-directory-b2c-reference-tenant-type.md). Observe que há problemas conhecidos quando você exclui um locatário B2C existente e o recria com o mesmo nome de domínio. Você precisa criar um locatário B2C com um nome de domínio diferente.
   > 
   > 

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Etapa 3: navegar até a folha de Recursos do B2C no Portal do Azure
1. Navegue até a extensão do Active Directory na barra de navegação do lado esquerdo.
2. Localize o locatário na guia **Diretório** e clique nele.
3. Clique na guia **Configurar** .
4. Clique no link **Gerenciar configurações de B2C** na seção **Administração de B2C**.
   
    ![Captura de tela da configuração do diretório do B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)
5. O Portal do Azure mostrando a folha de recursos do B2C será aberto em uma nova janela ou guia do navegador.
   
   > [!IMPORTANT]
   > Pode levar de dois a três minutos para que o locatário fique acessível no portal do Azure. Se você repetir essas etapas, após algum tempo isso será corrigido. Caso contrário, contate o suporte.
   > 
   > 
6. Fixe essa folha no seu Quadro inicial para facilitar o acesso. (A ferramenta Fixar é marcada em vermelho no canto superior direito da folha de recursos).
   
    ![Captura de tela da folha de recursos do B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)
   
   > [!NOTE]
   > Você pode gerenciar usuários e grupos, autoatendimento para configuração de redefinição de senha e recursos de identidade visual da empresa do locatário no [Portal Clássico do Azure](https://manage.windowsazure.com/).
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




<!--HONumber=Nov16_HO3-->


