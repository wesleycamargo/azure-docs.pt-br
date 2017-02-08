---
title: Como vincular uma assinatura do Azure ao Azure AD B2C | Microsoft Docs
description: "Guia passo a passo para habilitar a cobrança de locatário do Azure AD B2C em uma assinatura do Azure."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
translationtype: Human Translation
ms.sourcegitcommit: 0475c0f209cde80177df7dbf23eaf8dd17a44752
ms.openlocfilehash: b5754a08e0683344cc97bdc664ed26ef9a9cf34d


---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>Vinculação de uma assinatura do Azure a um locatário do Azure B2C para pagar encargos de uso
> [!IMPORTANT]
> em breve. Esse recurso não está disponível para todos os locatários do B2C.

Os encargos de uso em andamento para o Azure Active Directory B2C (ou Azure AD B2C) são cobrados de uma assinatura do Azure. É necessário para o administrador de locatários vincule explicitamente o locatário do Azure AD B2C a uma assinatura do Azure depois de criar o locatário do B2C em si.  Esse link é obtido com a criação do recurso “Locatário do Azure AD B2C " na assinatura de destino do Azure. Vários locatários do B2C podem ser vinculados a uma única assinatura do Azure juntamente com outros recursos do Azure (por exemplo, VMs, armazenamento de dados, LogicApps)


> [!IMPORTANT]
> Obtenha as informações mais recentes sobre o uso de cobrança e preços para B2C na seguinte página: [Preços do Azure AD B2C](
https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>Etapa 1 - Criar um locatário do Azure AD B2C

A criação do locatário do B2C deve ser concluída primeiro. Ignore esta etapa se você já tiver criado seu Locatário do B2C de destino. [Introdução ao Azure AD B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-get-started/)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Etapa 2 - Abrir o portal do Azure no Locatário do Azure AD que mostra a sua assinatura do Azure
Navegue até portal.azure.com. Alterne para o locatário do Azure AD que mostra a assinatura do Azure que você deseja usar. Este locatário do Azure AD é diferente do locatário do B2C. No portal do Azure, clique no nome de conta no canto superior direito do painel para selecionar o locatário do Azure AD. Uma assinatura do Azure é necessária para continuar. [Obter uma assinatura do Azure](https://account.windowsazure.com/signup?showCatalog=True)

![Alternar para o Locatário do Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>Etapa 3 - Criar um recurso de locatário do B2C no Azure Marketplace
Abra o Marketplace clicando no ícone Marketplace ou selecionando o "+" verde no canto superior esquerdo do painel.  Procurar e selecionar o Azure Active Directory B2C. Selecione Criar.
![Selecionar o Marketplace](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![Pesquisar AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

O diálogo de criação do Recurso do Azure AD B2C abrange os seguintes parâmetros:

1. Locatário do Azure AD B2C – selecione um locatário do Azure AD B2C no menu suspenso.  Somente os locatários do Azure AD B2C qualificados são mostrados.  Os locatários do B2C qualificados atendem a estas condições: você é o administrador global do locatário do B2C e o locatário do B2C não está atualmente associado a uma assinatura do Azure

2. Nome do Recurso do Azure AD B2C - é pré-selecionada para corresponder ao nome de domínio do locatário do B2C

3. Assinatura – uma assinatura ativa do Azure no qual você é um administrador ou um coadministrador.  Vários locatários do Azure AD B2C podem ser adicionados a uma assinatura do Azure

4. Grupo de Recursos e local do Grupo de Recursos - esse artefato ajuda a organizar vários recursos do Azure.  Essa opção não afeta o local, o desempenho ou o status de cobrança do locatário do B2C

5. Fixe no painel para ter um acesso mais fácil às informações de cobrança do locatário do B2C e às configurações do locatário do B2C ![Criar recurso do B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>Etapa 4 - Gerenciar os recursos do Locatário do B2C (opcional)
Depois que a implantação for concluída, um novo recurso "Locatário do B2C" será criado no grupo de recursos de destino e à assinatura do Azure relacionada.  Você verá um novo recurso do tipo "Locatário do B2C" adicionado junto com outros recursos do Azure.

![Criar recurso do B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

Clicando no recurso de locatário do B2C, será possível
- Clicar no nome da assinatura para examinar as informações de cobrança. Consultar Cobrança e Uso.
- Clicar em configurações do Azure AD B2C para abrir uma nova guia do navegador diretamente na folha Configurações do locatário do B2C
- Enviar uma solicitação de suporte
- Mover o recurso de locatário do B2C para outra assinatura do Azure ou para outro Grupo de Recursos.  Essa opção altera a assinatura do Azure que receberá os encargos de uso.

![Configurações de Recursos do B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)


## <a name="next-steps"></a>Próximas etapas
Quando essas etapas forem concluídas para cada um dos seus locatários do B2C, sua assinatura do Azure será cobrada de acordo com os detalhes do Azure Direct ou Enterprise Agreement.
- Examinar o uso e a cobrança selecionados para a assinatura do Azure
- Examine os relatórios de uso detalhado do dia a dia usando a API de Relatórios de Uso (TBD)



<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    



<!--HONumber=Dec16_HO5-->


