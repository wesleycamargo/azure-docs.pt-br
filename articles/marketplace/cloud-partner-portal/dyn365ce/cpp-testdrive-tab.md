---
title: Guia de Test Drive da oferta Dynamics 365 para o aplicativo de envolvimento do cliente | O Azure Marketplace
description: Como configurar o test drive para uma oferta de aplicativo do Dynamics 365 for Customer Engagement no AppSource Marketplace.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5bb5f39ef5f5bce09a8639ba9eedc6d042e60c1d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942336"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Guia Test Drive do aplicativo do Dynamics 365 for Customer Engagement

Use a guia **Test Drive** para criar uma experiência de avaliação para seus clientes.  Ela fornece aos clientes uma avaliação prática e autoguiada dos principais recursos e benefícios de sua oferta, demonstrados em um cenário de implementação do mundo real.  Dentre as opções de avaliação gratuita disponíveis, o Test Drive é a mais eficiente para gerar clientes potenciais de alta qualidade e aumentar a conversão deles.  Para saber mais, consulte [O que é um test drive?](../test-drive/what-is-test-drive.md)

A experiência de Test Drive para aplicativos do Dynamics 365 é executada automaticamente como uma solução hospedada pela Microsoft.  Para obter mais informações, confira [Test drive hospedado](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive).

A guia Test Drive tem três seções potenciais: **Test Drive**, **Detalhes** e **Configuração Técnica**.  As duas últimas seções serão exibidas somente depois que você habilitar a funcionalidade de Test Drive.  Um asterisco (*) anexado ao nome do campo indica que é obrigatório. 


## <a name="test-drive-section"></a>Seção Test Drive

Para habilitar essa funcionalidade, selecione **Sim** em **Habilitar um Test Drive**.


## <a name="details-section"></a>Seção Detalhes

Você fornecerá informações básicas de Test Drive na seção **Detalhes**.   

![Seção Detalhes de Test Drive](./media/test-drive-tab-details.png)

A tabela a seguir descreve os campos necessários para configurar o test drive para seu aplicativo do Dynamics 365. Os campos obrigatórios são indicados por um asterisco (*).

|      Campo                    |    DESCRIÇÃO                  |
|    ---------                  |  ---------------                |
|      Descrição\*            |   Descreva o que pode ser feito em seu test drive. Você pode usar marcas HTML básicas para formatar essa descrição. Por exemplo, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; e títulos.  |
|  Manual do usuário\*                |   Faça upload de um manual do usuário que seus clientes podem usar para percorrer a experiência do test drive. Esse documento deve ser um arquivo .pdf. |
|  Vídeo de demonstração do test drive (opcional) |  Você pode fornecer um vídeo com um passo a passo do test drive. Um cliente pode assistir a esse vídeo antes fazer o test drive. Forneça uma URL para o vídeo no YouTube ou Vimeo. Se selecionar **+ Adicionar Vídeo**, você será solicitado a fornecer as seguintes informações:<ul><li>NOME</li><li>URL</li><li>Miniatura (formato PNG, 533 x 324 pixels)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>Seção Configuração Técnica

nesta seção, você fornecerá detalhes técnicos sobre o test drive.

![Seção Detalhes de Test Drive](./media/test-drive-tab-tech-config.png)

Onde os campos têm as seguintes finalidades.  Os campos obrigatórios são indicados por um asterisco (*).

|      Campo                    |    DESCRIÇÃO                  |
|    ---------                  |  ---------------                |
| Tipo de teste de unidade\*            | Escolha **Hospedado pela Microsoft (Dynamics 365 for Customer Engagement)**.  |
| Unidades de teste simultânea máxima\*    | Número de instâncias simultâneas de um test drive ativo a qualquer momento. Cada usuário consumirá uma licença do Dynamics enquanto seu test drive estiver ativo, portanto, verifique se você tem pelo menos esse número de licenças do Dynamics disponíveis para usuários do test drive. O valor recomendado é de 3 a 5.  |
| Teste de unidade de duração (horas)\*   | Número máximo de horas durante as quais a instância de Test Drive do usuário ficará ativa. Após esse período, a instância será desprovisionada do locatário. O valor recomendado é de 2 a 24 horas, dependendo da complexidade do aplicativo. O usuário sempre poderá solicitar outro Test Drive se atingir o tempo limite e desejar fazer uma nova avaliação.  |
| URL da instância\*                  | URL para a qual o Test Drive navegará inicialmente. Normalmente, essa é a URL da instância do Dynamics 365 que contém o aplicativo e os dados de exemplo instalados.  |
| ID de locatário do Azure AD\*            | GUID do locatário do Azure para sua instância do Dynamics 365. Para recuperar esse valor, faça logon no portal do Azure e navegue para **Azure Active Directory** > **Selecionar Propriedades** > **Copiar a ID de Diretório**.  |
| ID de aplicativo do Azure AD\*               | GUID do aplicativo do Azure AD  |
| Chave de aplicativo do Azure AD\*              | Segredo do aplicativo do Azure AD, por exemplo: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Nome do locatário do AD do Azure\*          | Nome do locatário do Azure para a instância do Dynamics 365. Use o formato <nomedolocatário.>onmicrosoft.com, por exemplo: `testdrive.onmicrosoft.com`  |
| URL da API da Web de instância\*          | URL da API Web da instância do Dynamics 365. Recupere esse valor fazendo logon em sua instância do Microsoft Dynamics 365 e navegando para **Configurações** > **Personalização** > **Recursos para Desenvolvedores** > **API Web da Instância (copie esta URL)**. Valor de exemplo: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| Nome da função\*                     | Nome da função de segurança personalizada do Dynamics 365 criada para o Test Drive e que será atribuída aos usuários quando eles o executarem, por exemplo, `testdriveuser`. |
|  |  |

Depois de fornecer todas as informações necessárias, selecione **Salvar**.


## <a name="next-steps"></a>Próximas etapas

Em seguida, você fornecerá informações de marketing e vendas na [guia Detalhes da Vitrine](./cpp-storefront-details-tab.md).

