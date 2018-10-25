---
title: Criar uma oferta de módulo do IoT Edge | Microsoft Docs
description: Como publicar um novo módulo do IoT Edge para o Marketplace.
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: d3cc1a09963c5f7fee613af24c63fd15b1cfffee
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804981"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>Como publicar um novo módulo do IoT Edge no Portal do Cloud Partner

Este artigo descreve as etapas para publicar uma nova oferta de módulo do IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Para publicar um módulo do IoT Edge no Azure Marketplace, você precisa atender aos pré-requisitos a seguir.

-   [Acesso ao CPP (Portal do Cloud Partner)](https://cloudpartner.azure.com/#alloffers). Para obter mais informações, confira o [guia de publicação](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   Hospede seu módulo do IoT Edge em um Registro de Contêiner do Azure.

-   Preparar os metadados do módulo do IoT Edge (incluindo lista parcial):

    -   Title

    -   Descrição (formato HTML básico)

    -   Um logotipo no formato de imagem png e nos seguintes tamanhos: 40 x 40, 90 x 90, 115 x 115 e 255 x 115 (em pixels).

    -   Termos de uso e política de privacidade

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>Preparar sua listagem do Módulo do IoT Edge no CPP
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>Criar uma nova oferta do tipo Módulo do IoT Edge 

Siga estas etapas para preparar sua listagem do Módulo do IoT Edge:

-   Entre na sua [conta do CPP](https://cloudpartner.azure.com/).

>[!Note]
>Para obter informações gerais sobre o Portal do Cloud Partner, você pode verificar a [documentação de aprendizado](https://cloudpartner.azure.com/#learn)

-   Selecione **Nova oferta** e, em seguida, selecione **Módulo do IoT Edge**.

>[!NOTE]
>Um módulo do IoT Edge é um contêiner feito especificamente para ser executado no IoT Edge. Os cenários abordados por um módulo do IoT Edge precisam fazer sentido em um contexto do IoT Edge. Ele também inclui as definições de configuração padrão para simplificar a implantação em um dispositivo IoT Edge. O contêiner também pode incluir o SDK de Módulo do IoT Edge para habilitar a comunicação com o Hub IoT e o edgeHub.

### <a name="define-your-offer-settings"></a>Definir as configurações da oferta

Na guia Configurações da Oferta, insira as informações da oferta.

![Identidade da oferta](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   A **ID da oferta** identifica sua oferta no CPP de modo exclusivo e pode ser usada em URLs voltadas ao cliente.

-   O **Nome** só pode ser visto por você para fazer referência a essa oferta no CPP.

### <a name="create-one-or-more-skus"></a>Criar um ou mais SKUs

Cada SKU corresponde a uma imagem de contêiner. Você precisa ter pelo menos um SKU e pode adicionar mais de um. Há duas partes em um SKU:

-   Metadados do SKU

-   Metadados do contêiner

**Para criar um SKU:**

Selecione a guia **SKUs** e, em seguida, selecione **Novo SKU**.

![Novo SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

Os metadados do SKU contêm os campos a seguir, que são necessários:
- ID do SKU – um identificador exclusivo.
- Título – o título do SKU, com até 50 caracteres.
- Resumo – uma breve descrição, com até 100 caracteres.
- Descrição – uma descrição longa.
- Ocultar este SKU – o padrão é **Não**.
   
![Detalhes do SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>Metadados do módulo do IoT Edge e o registro de contêiner

Os metadados do módulo do IoT Edge têm informações de referência de imagem que são armazenadas no ACR (Registro de Contêiner do Azure). O Azure Marketplace copia a imagem para o Registro do Marketplace público e está disponível para os clientes após a certificação. Todas as solicitações de usuário para consumir uma imagem do módulo do IoT Edge são servidas do registro de contêiner do Marketplace.

![Imagens de contêiner](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**Imagens de contêiner**

Os Detalhes do repositório da imagem têm os seguintes campos obrigatórios:

-   **ID da assinatura** – a ID da assinatura do Azure em que o Registro do ACR está presente.

-   **Nome do grupo de recursos** – o nome do grupo de recursos que contém o Registro do ACR.

-   **Nome do Registro** – o nome do Registro do ACR.

-   **Nome do repositório** – o nome do repositório. Uma vez definido, esse valor não pode ser alterado posteriormente. O nome deve ser exclusivo para que nenhuma outra oferta em sua conta tenha o mesmo nome.

-   **Nome de usuário** – o nome de usuário associado com o ACR (nome de usuário de administrador).

-   **Senha** – a senha associada ao ACR.

    >[!Note]
    >O nome de usuário e senha são necessários para assegurar que os parceiros tenham acesso ao ACR descrito no processo de publicação.

Quando estiver publicando uma imagem do módulo do IoT Edge, você poderá fornecer uma ou mais marcas de imagem. Verifique se você adicionou uma marca 'latest' (o padrão) ao seu módulo, de modo que você possa identificar facilmente o módulo durante os testes.

Você também pode especificar as seguintes especificações do módulo do IoT Edge:

-   **createOptions** – a createOptions padrão a ser passado, de modo que este Módulo do IoT Edge possa ser iniciado imediatamente.

-   **twin:** – o gêmeo padrão a ser passado para que este módulo do IoT Edge possa ser iniciado imediatamente ao usar o SDK do módulo do IoT.

-   **routes:** – as rotas padrão a serem passadas para que este módulo do IoT Edge possa ser iniciado imediatamente ao usar o SDK do módulo do IoT.

### <a name="describe-your-iot-edge-module-for-your-customers"></a>Descrever o módulo do IoT Edge para os clientes

Na guia Marketplace, adicione o conteúdo de marketing específico. Essas informações são o conteúdo que ficará publicamente visível e listado no Azure Marketplace.

![Visão geral do módulo do IoT Edge](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **Título** – o título do seu módulo do IoT Edge que é voltado ao público.

-   **Resumo** – o resumo do módulo do IoT Edge que é voltado ao público na maioria das páginas, como a navegação de páginas.

-   **Resumo longo** – o resumo do módulo do IoT Edge que está voltado ao público quando o módulo está em destaque. 

-   **Descrição** – a descrição do módulo do IoT Edge que está voltado ao público na página de detalhes do produto. (Você pode usar marcas HTML básicas para formatar sua descrição.)

-   **Identificador de marketing** – um identificador exclusivo que é usado para criar a URL do produto. Esta URL está no seguinte formato: *azuremarketplace.microsoft.com/ptbr/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier*.

-   **IDs de assinatura de versão prévia** – os usuários que tiverem acesso a essas assinaturas poderão ver o módulo do IoT Edge após a etapa de certificação e antes de ele ser ativado.

-   **Links úteis** – você pode adicionar até 10 links que serão mostrados em sua página de detalhes do produto.

-   **Categorias sugeridas** – selecione até cinco categorias. Elas serão mostradas em sua página de detalhes do produto. Atualmente, todos os módulos do IoT Edge são mostrados nas categorias *Internet das Coisas \> módulo do IoT Edge* nas páginas de navegação.

-   **Logotipos** – faça upload de suas imagens de logotipo do módulo do IoT Edge no formato PNG. Use exatamente os seguintes tamanhos: 40 x 40, 90 x 90, 115 x 115 e 255 x 115 (em pixels).

-   **Capturas de tela** – capturas de tela são exibidas na sua página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o módulo do IoT Edge faz e como ele funciona. Você pode exibir diagramas de arquitetura ou usar ilustrações de caso por exemplo.

-   **Vídeos** – vídeos são exibidos em sua página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o módulo do IoT Edge faz e como ele funciona.

-   **Gerenciamento de clientes potenciais** – você pode escolher um sistema para coletar todos os clientes potenciais que mostrem interesse em seu produto.

-   **Privacidade** – você precisa ter uma URL apontando para sua política de privacidade.

-   **Termos de uso** – você precisa ter termos de uso. Você pode usar marcas HTML para formatar essa página ou apontar para uma de suas outras páginas.

### <a name="enter-your-support-contact-information"></a>Inserir as informações de contato de suporte

Na guia Suporte, forneça as informações **Contato de engenharia** e **Atendimento ao cliente**.

![Contatos de suporte](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>Certificar o módulo do IoT Edge

Depois de fornecer todas as informações necessárias, selecione **Publicar** para enviar seu módulo do IoT Edge para certificação. Você verá uma linha do tempo mostrando as etapas no processo de certificação.

**Verificação de módulo**

O módulo é verificado pela nossa equipe de certificação. Depois que o módulo for certificado, você obterá um link privado para testá-lo. Se você precisar fazer alterações depois de testar, edite os metadados da sua oferta e envie o módulo novamente à equipe de certificação. 

## <a name="publish-your-iot-edge-module"></a>Como publicar o módulo do IoT Edge

Quando você concluir o teste e estiver pronto para publicar, selecione **Colocar no ar** para publicar seu módulo IoT Edge.

>[!Important]
>Se você quiser ter o módulo do IoT Edge anunciado no evento Ignite, o módulo precisará ter se tornado público até 23/09/2018.
