---
title: "Azure Active Directory B2C: locatários de produção-escala versus locatários B2C de visualização | Microsoft Docs"
description: "Um tópico sobre os tipos de locatários do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7e52c9d331c6967f029427c7eb03f13f65c3a507


---
# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure Active Directory B2C: locatários de produção e escala versus locatários de visualização do B2C
Se planeja escrever um aplicativo de produção no Azure AD (Azure Active Directory) B2C, você precisará verificar se tem o "tipo" de locatário certo para ativação. Para ver o que você tem, siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure e confira em **Tipo de locatário**.

## <a name="summary"></a>Resumo
O Azure AD B2C dá suporte a aplicativos de produção APENAS em locatários do B2C de **Escala de produção** na América do Norte.

| Tipo de locatário | Países/regiões | Com disponibilidade geral? |
| --- | --- | --- |
| **Locatário de escala de produção** |Países/regiões na América do Norte |Sim |
| **Locatário de escala de produção** |Todos os países/regiões, exceto a América do Norte |Não |
| **Locatário de visualização** |Todos os países/regiões |Não |

> [!NOTE]
> Locatários do Azure AD B2C (para consumidores) não estão disponíveis no momento em alguns países ou regiões em que os locatários do Azure AD (para funcionários) estão disponíveis. Leia as seções a seguir para obter mais detalhes.
> 
> 

## <a name="production-scale-b2c-tenant-in-north-america"></a>Locatário do B2C de produção-escala na América do Norte
Se você [criou seu locatário do B2C](active-directory-b2c-get-started.md) na América do Norte, ou seja, em um dos seguintes países ou regiões: Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trinidad e Tobago, E o **Tipo de locatário** em sua interface do usuário de Administrador do B2C diz **Produção-escala**, seu locatário pode ser usado para aplicativos de produção.

> [!NOTE]
> Locatários de produção-escala podem ser dimensionados para centenas de milhões de identidades do consumidor por locatário.
> 
> 

![Captura de tela de um locatário de produção-escala](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>Locatário do B2C de visualização em qualquer país/região
Se você criou um locatário do B2C durante o período de visualização do Azure AD B2C, é provável que seu **Tipo de locatário** seja **Locatário de visualização**. Se esse for o caso, você DEVERÁ usar o locatário somente para fins de teste e desenvolvimento e NÃO para aplicativos de produção.

> [!IMPORTANT]
> Não há um caminho de migração de um locatário do B2C de visualização para um locatário do B2C de produção-escala. Observe que há problemas conhecidos quando você exclui um locatário B2C de visualização e recria um locatário B2C de escala de produção com o mesmo nome de domínio. Você precisa criar um locatário B2C de escala de produção com um nome de domínio diferente.
> 
> 

![Captura de tela de um locatário de visualização](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>Locatário do B2C de produção-escala fora da América do Norte
Atualmente o Azure AD B2C NÃO está disponível fora da América do Norte. No entanto, você pode criar e usar locatários de produção-escala para fins de desenvolvimento e teste em um dos seguintes países ou regiões: Argélia, Áustria, Azerbaijão, Bahrein, Belarus, Bélgica, Bulgária, Croácia, Chipre, República Tcheca, Dinamarca, Egito, Estônia, Finlândia, França, Alemanha, Grécia, Hungria, Islândia, Irlanda, Israel, Itália, Jordânia, Cazaquistão, Quênia, Kuwait, Líbano, Letônia, Liechtenstein, Lituania, Luxemburgo, ARI da Macedônia, Malta, Montenegro, Marrocos, Países Baixos, Nigéria, Noruega , Omã, Paquistão, Polônia, Portugal, Catar, Romênia, Rússia, Arábia Saudita, Sérvia, Eslováquia, Eslovênia, África do Sul, Espanha, Suécia, Suíça, Tunísia, Turquia, Ucrânia, Emirados Árabes Unidos e Reino Unido.

Depois que o Azure AD B2C anunciar o lançamento nos países ou regiões acima, você poderá continuar a usar esses locatários de escala de produção e ativar seus aplicativos de produção, sem perda de dados.

## <a name="availability-of-b2c-tenants"></a>Disponibilidade de locatários do B2C
Os locatários do B2C estão indisponíveis no momento nos seguintes países ou regiões: Afeganistão, Argentina, Austrália, Brasil, Chile, Colômbia, Equador, RAE de Hong Kong, Índia, Indonésia, Iraque, Japão, Coreia, Malásia, Nova Zelândia, Paraguai, Peru, Filipinas, Cingapura, Sri Lanka, Taiwan, Tailândia, Uruguai e Venezuela. Planejamos incluí-los no futuro.




<!--HONumber=Nov16_HO3-->


