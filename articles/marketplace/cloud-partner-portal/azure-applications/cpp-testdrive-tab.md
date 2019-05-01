---
title: Unidade de teste de oferta de aplicativo do Azure | O Azure Marketplace
description: Como configurar o test drive da oferta do Aplicativo Azure no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 42e533cdcedfb47a46934f77714d61a640a8d7d1
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942858"
---
# <a name="azure-applications-test-drive-tab"></a>Guia Test Drive de aplicativos do Azure

Use a guia Test Drive para fornecer uma experiência de avaliação a seus clientes.

## <a name="test-drive-benefits"></a>Benefícios do Test Drive

Criar uma experiência de avaliação gratuita para seus clientes é uma melhor prática para garantir que eles possam comprar com confiança. Dentre as opções de avaliação gratuita disponíveis, o Test Drive é a mais eficiente para gerar clientes potenciais de alta qualidade e aumentar a conversão deles.

Fornece aos clientes uma avaliação gratuita prática e autoguiada dos principais recursos e benefícios do seu produto, demonstrados em um cenário de implementação real.

## <a name="how-a-test-drive-works"></a>Como funciona um test drive

Um cliente potencial pesquisa e descobre seu aplicativo no Marketplace. O cliente entra e concorde com os termos de uso. Neste ponto, o cliente recebe o ambiente pré-configurado para testar durante um número fixo de horas, enquanto você recebe um cliente potencial altamente qualificado para acompanhar. Para obter mais informações, confira [O que é um test drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Configurando um test drive

Use as etapas a seguir para habilitar e configurar um test drive.

### <a name="to-enable-a-test-drive"></a>Para habilitar um test drive:

1. Em **Nova Oferta**, selecione a guia **Test Drive**.
2. Em **Test Drive**, selecione **Sim** para **Habilitar um test drive**.

   ![Habilitar um test drive](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Para configurar um test drive:

Depois de habilitar um test drive, você vai preencher os formulários a seguir para configurá-lo:
  
 - Detalhes
 - Configuração Técnica
 - Detalhes da assinatura para implantação do Test Drive

A próxima captura de tela mostra todos os formulários de test drive. Um asterisco (*) junto ao nome do campo indica que ele é obrigatório. 

![Configurar um test drive](./media/managed-app-configure-testdrive.png)

A tabela a seguir descreve os campos necessários para configurar o test drive para seu aplicativo gerenciado.  Os campos anexados com um asterisco são obrigatórios.

|      Campo         |  DESCRIÇÃO      |
|  ---------------   |  ---------------  |
| **Descrição\***  |  Descreva o que pode ser feito em seu test drive. Você pode usar marcas HTML básicas para formatar essa descrição. Por exemplo, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; e títulos.                |
| **Manual do usuário\***  |  Faça upload de um manual do usuário que seus clientes podem usar para percorrer a experiência do test drive. Esse documento deve ser um arquivo .pdf.    |
| **Vídeo de demonstração do Test Drive** |  Uma orientação em vídeo opcional de sua unidade de teste. Um cliente pode assistir a esse vídeo antes fazer o test drive. Forneça uma URL para o vídeo no YouTube ou Vimeo. Se selecionar **+ Adicionar Vídeo**, você será solicitado a fornecer as seguintes informações:<ul><li>NOME</li><li>URL</li><li>Miniatura (formato PNG, 533 x 324 pixels)</li></ul>  |
| **Instâncias\***      | Configure a quantidade de instâncias que deseja, em quais regiões e em que velocidade seus clientes podem obter o test drive. Para obter mais informações, confira [Como publicar um test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Teste de unidade de duração (horas)\*** | Insira um número inteiro para o número de horas. O intervalo permitido é de 1 a 999. |
| **Modelo ARM de teste de unidade\***     | Carregue um arquivo compactado (.zip) que tem os modelos do Azure Resource Manager para seu aplicativo. Para obter mais informações, confira [Test drive do Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Informações de acesso\***          | Fornece informações de acesso depois que o cliente obtiver o test drive. Por exemplo, uma URL para acessar o test drive e informações de logon. . Você pode usar marcas HTML básicas para formatar essa descrição. Por exemplo, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; e títulos. |
| **Id da assinatura do Azure\***       | Concede acesso aos serviços do Azure e ao portal do Azure. A assinatura é onde o uso de recursos é relatado e os serviços são cobrados. Caso ainda não tenha uma Assinatura do Azure separada apenas para test drives, crie uma.  |
| **Id de locatário do Azure AD\***          | Forneça um locatário existente no Azure Active Directory ou crie um novo para esse test drive.  |
| **Id de aplicativo do Azure AD\***             | Crie e registre um aplicativo. A Microsoft usa esse aplicativo para realizar operações em sua instância de Test Drive.  |
| **Chave de aplicativo do Azure AD\***            | Crie uma chave de autenticação para o aplicativo e cole-a neste campo.   |
|  |  |

Depois de fornecer todas as informações necessárias, selecione **Salvar** para terminar de configurar o test drive.


## <a name="next-steps"></a>Próximas etapas

[Guia Marketplace](./cpp-marketplace-tab.md)
