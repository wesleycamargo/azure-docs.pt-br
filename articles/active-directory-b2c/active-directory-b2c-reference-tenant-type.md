---
title: Residência de dados e disponibilidade de região no Azure Active Directory B2C | Microsoft Docs
description: Um tópico sobre os tipos de locatários do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 928c6316ea964472faadc82213c4c1ff81c3e038
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608223"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Disponibilidade de região e residência de dados
Residência de dados e disponibilidade de região são dois conceitos muito diferentes que se aplicam a B2C do Azure AD diferentemente do restante do Azure. Este artigo irá explicar as diferenças entre esses dois conceitos e comparar como são aplicáveis para B2C do Azure AD versus Azure.

## <a name="summary"></a>Resumo
B2C do Azure AD está **disponível em todo o mundo** com a opção para **residência de dados nos Estados Unidos ou Europa**.

## <a name="concepts"></a>Conceitos
* **Disponibilidade da região** refere-se onde um serviço está disponível para uso.
* **Residência dados** refere-se ao armazenamento de dados do usuário.

## <a name="region-availability"></a>Disponibilidade de região
B2C do Azure AD está disponível em todo o mundo por meio da nuvem pública do Azure. 

Isso difere do modelo da maioria dos demais serviços do Azure, que une disponibilidade com residência de dados. É possível visualizar exemplos disso na página [Produtos Disponíveis por Região](https://azure.microsoft.com/regions/services/) e [Calculadora de preços B2C do Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residência de dadosResidência de dados
B2C do Azure AD armazena dados do usuário nos Estados Unidos ou na Europa.

Residência de dados é determinada com base em qual país/região é selecionada ao [criar um locatário B2C do Azure AD](active-directory-b2c-get-started.md).

![Captura de tela de um locatário de visualização](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Os dados residem nos Estados Unidos para os seguintes países/regiões:

> Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trinidad e Tobago

Os dados residem na Europa para os seguintes países/regiões:

> Argélia, Áustria, Azerbaijão, Bahrein, Belarus, Bélgica, Bulgária, Croácia, Chipre, República Tcheca, Dinamarca, Egito, Estônia, Finlândia, França, Alemanha, Grécia, Hungria, Islândia, Irlanda, Israel, Itália, Jordânia, Cazaquistão, Quênia, Kuwait, Letônia, Líbano, Liechtenstein, Lituânia, Luxemburgo, Macedônia do Norte, Malta, Montenegro, Marrocos, países baixos, Nigéria, Noruega, Omã, Paquistão, Polônia, Portugal, Catar, Romênia, Rússia, Arábia Saudita, Sérvia, Eslováquia, Eslovênia, África do Sul, Espanha, Suécia, Suíça, Tunísia, Turquia, Ucrânia, Emirados Árabes Unidos e Reino Unido.

Os demais países/regiões estão no processo de serem adicionados à lista.  Por enquanto, ainda é possível utilizar B2C do Azure AD, escolhendo qualquer um dos países/regiões acima.

> Afeganistão, Argentina, Austrália, Brasil, Chile, Colômbia, Equador, RAE de Hong Kong, Índia, Indonésia, Iraque, Japão, Coreia do Sul, Malásia, Nova Zelândia, Paraguai, Peru, Filipinas, Singapura, Sri Lanka, Taiwan, Tailândia, Uruguai e Venezuela.

## <a name="preview-tenant"></a>Locatário de visualização
Se você criou um locatário do B2C durante o período de visualização do Azure AD B2C, é provável que seu **Tipo de locatário** seja **Locatário de visualização**. Se esse for o caso, você DEVERÁ usar o locatário somente para fins de teste e desenvolvimento e NÃO para aplicativos de produção.

> [!IMPORTANT]
> Não há um caminho de migração de um locatário do B2C de visualização para um locatário do B2C de produção-escala. Observe que há problemas conhecidos quando você exclui um locatário B2C de visualização e recria um locatário B2C de escala de produção com o mesmo nome de domínio. Você precisa criar um locatário B2C de escala de produção com um nome de domínio diferente.


![Captura de tela de um locatário de visualização](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
