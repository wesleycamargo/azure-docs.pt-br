---
title: Publicar um modelo de solução | Microsoft Docs
description: Publique um modelo de solução no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: 5320b8d5ca7456a6f1b0fdd1372c9f39ac1edfb0
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219402"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>Publicar um modelo de solução no Azure Marketplace

Este artigo fornece as etapas para publicar um modelo de solução da oferta para o Azure Marketplace.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos técnicos e não técnicos se aplicam à listagem de um modelo de solução no Azure Marketplace.

### <a name="technical"></a>Técnicos

- [Compreender os modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

- Modelos de Início Rápido do Azure:

    - [Documentação do modelo de início rápido do Azure](https://azure.microsoft.com/documentation/templates/)

    - [Documentação de início rápido do Azure no GitHub](https://github.com/azure/azure-quickstart-templates)

 - [Criar arquivo de interface do usuário do portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

### <a name="non-technical-business-requirements"></a>Não técnico (requisitos de negócios)

-   Sua empresa (ou sua subsidiária) deve estar localizada em um país de origem de venda ao qual o Azure Marketplace dá suporte.

-   O produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Azure Marketplace.

-   Você é responsável por disponibilizar suporte técnico aos clientes de forma comercialmente justa, seja ele gratuito, pago ou por meio do suporte da comunidade.

-   Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.

-   Forneça conteúdo que atenda aos critérios da oferta a serem listados no Azure Marketplace e no Portal de Gerenciamento do Azure.

-   Concorde com os termos das Políticas de Participação do Azure Marketplace e com o Contrato do Editor.

-   Concorde em cumprir os Termos de uso, a Política de privacidade da Microsoft e o Contrato do programa de certificação do Microsoft Azure.

## <a name="before-you-begin"></a>Antes de começar

Depois de atender a todos os pré-requisitos, você pode começar a criar sua oferta de modelo de solução. Antes de começar, examine as informações da SKU e a oferta a seguir.

**Oferta**

Uma oferta de Aplicativo Azure corresponde a uma classe de oferta de produto de um editor. Se você tiver um novo tipo de solução/aplicativo que deseje disponibilizar no Azure Marketplace, uma oferta nova é a melhor abordagem. Uma oferta é uma coleção de SKUs. Cada oferta aparece como sua própria entidade no Azure Marketplace.

**SKU**

Uma SKU é a menor unidade de compra de uma oferta. Embora esteja dentro da mesma classe de produto (oferta), as SKUs permitem diferenciar entre os diversos recursos para os quais há suporte. Por exemplo, a oferta é gerenciada ou não gerenciada, e há suporte para diferentes modelos de cobrança.

Adicione várias SKUs nos cenários a seguir:
- Você quer dar suporte a diferentes modelos de cobrança, como BYOL (traga sua própria licença) ou PAYG (pago conforme o uso).
- Cada SKU dá suporte a um conjunto diferente de recursos e cada conjunto de recursos tem preços diferentes.

Uma SKU aparece sob a oferta pai no Azure Marketplace e aparece como sua própria entidade comprável no portal do Azure.

## <a name="to-create-a-new-offer"></a>Para criar uma nova oferta

1.  Entre no [Portal de Parceiros de Nuvem](http://cloudpartner.azure.com/).

2.  Na barra de navegação à esquerda, selecione **+ Nova oferta** e depois selecione **Aplicativos do Azure**.

    ![Criar uma oferta](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Em **Nova oferta**, selecione **Editor**.

    ![Editor de nova oferta](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  Em **Editor**, você fornecerá informações nas seguintes exibições:
    - Configurações da oferta
    - SKUs
    - Marketplace
    - Suporte

Cada visualização contém um conjunto de campos para você preencher. Os campos necessários são indicados com um asterisco vermelho (\*)

## <a name="to-configure-offer-settings"></a>Para definir as Configurações da Oferta

1. Configure os seguintes campos de **Identidade da Oferta** nas Configurações da oferta.

    **ID da oferta**

     Um identificador exclusivo para a oferta em um perfil de editor. Essa ID é visível em URLs de produto, relatórios de cobrança e modelos ARM. Você só pode usar caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e pode ter um máximo de 50 caracteres. 
    >[!Note]
    >Esse campo é bloqueado quando uma oferta é ativada.

    **ID do Editor**

    Uma lista suspensa para o perfil do editor. Escolha o perfil sob o qual você deseja publicar a oferta. 
    >[!Note]
    >Esse campo é bloqueado quando uma oferta é ativada.

    **Nome**

    O nome de exibição da oferta. O nome é mostrado no Azure Marketplace e no portal do Azure. Ele pode ter um máximo de 50 caracteres. Use as diretrizes a seguir para o nome da oferta:
    -  Inclua um nome de marca reconhecível para o seu produto. 
    - Não inclua o nome de sua empresa aqui, a menos que seja a maneira como a oferta é comercializada.
    - Se você estiver comercializando essa oferta em seu próprio site, verifique se o nome é idêntico ao nome no seu site.

2. Selecione **Salvar** para concluir as Configurações da oferta.
para sua oferta.

## <a name="to-create-skus"></a>Para criar SKUs
------------------

1. Selecione **SKUs**. 

    ![Novo SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    A ID do SKU é um identificador exclusivo para o SKU dentro de uma oferta. Essa ID é visível em URLs de produto, relatórios de cobrança e modelos ARM. A ID do SKU:
    - Só pode ter um máximo de 50 caracteres.
    - Só pode ser composta de caracteres alfanuméricos minúsculos ou traços (-).
    - A ID não pode terminar com um traço.

    >[!Note]
    >Depois que um SKU é adicionado, ele aparece na lista de SKUs na exibição de SKUs. Para ver os detalhes do SKU, selecione o nome dele. 

2. Selecione **Novo SKU** para fornecer as informações mostradas na captura de tela a seguir. 

    ![Detalhes do SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>Configurações do SKU

Forneça as configurações do SKU a seguir.

- **Título** – um título para o SKU. Esse título é mostrado na galeria para este item.

- **Resumo** – uma breve descrição resumida do SKU. (O comprimento máximo é de 100 caracteres.)

- **Descrição** – uma descrição detalhada do SKU.

- **Tipo de SKU** – uma lista suspensa com estes valores: "Aplicativo gerenciado (versão prévia)" e "Modelo de solução". Para este cenário, selecione **Modelo de solução**.

- **Disponibilidade de nuvem** – o local do SKU. O padrão é Azure Público.

### <a name="package-details"></a>Detalhes do pacote

Depois de concluir as configurações do SKU, forneça os seguintes detalhes do pacote.

![Detalhes do pacote](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **Versão atual** – a versão do pacote que você fará upload. Ela deve estar no formato – .

- **Arquivo de pacote** – esse pacote contém os arquivos a seguir, que estão salvos em um arquivo zip.

    -   MainTemplate.json – o arquivo do modelo de implantação que é usado para implantar a solução/aplicativo e criar os recursos definidos para a solução. Para obter mais informações, confira [como criar arquivos de modelo de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)

    -   createUIDefinition.json – esse arquivo é usado pelo portal do Azure para gerar a interface do usuário para o provisionamento dessa solução/aplicativo. Para obter mais informações, confira [Criar uma interface do usuário do portal do Azure para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

    >[!IMPORTANT]
    >Esse pacote deve conter quaisquer outros modelos ou scripts aninhados que sejam necessários para provisionar esse aplicativo. Os arquivos mainTemplate.json e createUIDefinition.json precisam estar na pasta raiz.

## <a name="to-configure-the-marketplace"></a>Para configurar o Marketplace

Use o modo de exibição do Marketplace para configurar os campos que são exibidos para a oferta no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal do Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Visualizar IDs de assinatura

A lista de IDs de assinatura do Azure que você quer que tenham acesso à oferta quando ela for publicada. Essas assinaturas da lista branca permitirão que você teste a oferta em versão prévia antes de ativá-la. O portal de parceiro permite até 100 assinaturas na lista branca.

### <a name="suggested-categories"></a>Categorias sugeridas

Selecione até 5 categorias na lista fornecida com as quais sua oferta pode ser melhor associada. As categorias selecionadas serão usadas para mapear a oferta até as categorias de produto disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com) e [Portal do Azure](https://portal.azure.com/).

Os exemplos a seguir mostram as informações do Marketplace no Azure Marketplace e no portal do Azure.

**Azure Marketplace**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Portal do Azure**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>Diretrizes de logotipo

Siga estas diretrizes para quaisquer logotipos carregados no Portal do Cloud Partner:

-   O design do Azure tem uma paleta de cores simples. Mantenha um baixo número de cores primárias e secundárias no seu logotipo.

-   As cores do tema do portal do Azure são branco e preto. Evite usar essas cores como a cor da tela de fundo dos seus logotipos. Use uma cor que destaque seus logotipos no portal do Azure. É recomendável usar cores primárias simples.

    >[!Note] 
    >Se estiver usando um plano de fundo transparente, assegure que os logotipos/o texto não sejam pretos, brancos ou azuis.

-   Não use um fundo gradiente no logotipo.

-   Evite colocar texto no logotipo. Isso inclui o nome da sua empresa ou marca. A aparência do seu logotipo deve ser *simples* e deve evitar usar gradientes.

-   O logotipo não deve ser esticado.

#### <a name="hero-logo"></a>Logotipo Hero

O logotipo Hero é opcional. O editor pode optar por não carregar um logotipo Hero. No entanto, depois que o logotipo é carregado, ele não pode ser excluído. O parceiro precisa seguir as diretrizes do Azure Marketplace para ícones Hero.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Diretrizes adicionais para o ícone do logotipo Hero

-   O nome de exibição do editor, o título do plano e o resumo longo da oferta são exibidos usando uma fonte branca. Evite usar qualquer cor clara na tela de fundo. Telas de fundo pretas, brancas e transparentes não são permitidas para ícones Hero.

-   O nome de exibição do editor, o título do plano, o resumo longo da oferta e o botão Criar são inseridos programaticamente no logotipo Hero depois que a oferta é listada. Não insira nenhum texto quando você estiver projetando o logotipo Hero. Deixe um espaço vazio à direita do logotipo. Esse espaço deve ter 415 x 100 pixels e ser deslocado em 370 px em relação à extremidade esquerda.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>Para configurar o Suporte

Use a exibição de Suporte para fornecer as seguintes informações:

- Contatos de suporte de sua empresa, tais como os de engenharia.
- Contatos do atendimento ao cliente.

## <a name="to-publish-the-offer"></a>Para publicar a oferta

A etapa final é publicar a oferta. Selecione **Publicar**.
