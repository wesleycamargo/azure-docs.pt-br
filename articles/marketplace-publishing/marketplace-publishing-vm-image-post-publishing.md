---
title: "Gerenciar uma imagem de máquina virtual no Azure Marketplace | Microsoft Docs"
description: "Guia detalhado sobre como gerenciar sua imagem de máquina virtual no Azure Marketplace após a publicação inicial."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 766e686170c35f3899e070671654c337d3dc642f
ms.lasthandoff: 04/29/2017


---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guia de pós-produção para ofertas de máquina virtual no Azure Marketplace
Este artigo explica como você pode atualizar uma oferta de Máquina Virtual online no Azure Marketplace. Ele também orienta você durante o processo de adição de um ou mais SKUs a uma oferta existente e de remoção de uma oferta de Máquina Virtual online ou de um SKU do Azure Marketplace.

Depois que uma oferta/SKU é preparada no [Portal do Azure](http://portal.azure.com), você não pode alterar os campos apresentados abaixo:

* **Identificador da Oferta:** [portal de Publicação -> Máquinas Virtuais -> Selecione sua Oferta -> guia Imagens de VM -> Identificador da Oferta]
* **Identificador do SKU:** [portal de Publicação -> Máquinas Virtuais -> Selecione sua Oferta -> guia SKUs -> Adicionar um SKU]
* **Namespace do Editor:** [portal de Publicação -> Máquinas Virtuais -> guia Passo a Passo -> Conte-nos Sobre sua Empresa (encontrado em "Etapa 2 Registrar sua Empresa") -> Namespace do Editor -> Namespace]

Depois que uma oferta/SKU for listada no [Azure Marketplace](http://azure.microsoft.com/marketplace), você não poderá alterar os campos apresentados abaixo:

* **Identificador da Oferta:** [portal de Publicação -> Máquinas Virtuais -> Selecione sua Oferta -> guia Imagens de VM -> Identificador da Oferta]
* **Identificador do SKU:** [portal de Publicação -> Máquinas Virtuais -> Selecione sua Oferta -> guia SKUs -> Adicionar um SKU]
* **Namespace do Editor:** [Portal de publicação -> Máquinas Virtuais -> guia Passo a Passo -> Conte-nos Sobre sua Empresa (Encontrado na Etapa 2 Registrar) -> Namespace do Editor -> Namespace]
* **Portas** [portal de Publicação -> Máquinas Virtuais -> Selecione sua Oferta -> guia Imagens de VM -> Abrir Portas]
* **Alteração de preços de SKUs listados**
* **Modelos de cobrança de SKUs listados**
* **Remoção de regiões de cobrança de SKUs listados**
* **Alteração da contagem de discos de dados dos SKUs listados**

## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. Como atualizar os detalhes técnicos de uma SKU
Você pode adicionar uma nova versão à SKU listada e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **IMAGENS DE VM** .
4. Na seção **SKUs** da guia **IMAGENS DE VM** , localize a SKU que você deseja atualizar.
5. Depois disso, adicione um novo número de versão da SKU e clique no botão **"+"** . A nova versão deve estar no formato X.Y.Z, onde X, Y, Z são inteiros. As alterações de versão só devem ser incrementais.
6. Na caixa **URL do VHD de sistema operacional** , adicione o URI da assinatura de acesso compartilhado criado para o VHD do sistema operacional e salve as alterações.

   > [!IMPORTANT]
   > Você não pode aumentar/diminuir a contagem de discos de dados de uma SKU listada. Você precisa criar uma nova SKU nesse caso. Consulte a seção [3. Como adicionar uma nova SKU em uma oferta listada](#3-how-to-add-a-new-sku-under-a-live-offer) para obter orientações detalhadas.
   >
   >
7. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
8. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. Como atualizar os detalhes não técnicos de uma oferta ou uma SKU
Você pode atualizar os detalhes não técnicos (marketing, jurídico, suporte, categorias) de sua oferta online ou de sua SKU no Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 Atualizar a descrição e os logotipos da oferta
Você pode atualizar os detalhes da oferta e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão **PORTUGUÊS (BR)** .
5. No menu do lado esquerdo, clique na guia **DETALHES** . Na seção *DESCRIÇÃO* da guia **DETALHES** , você pode atualizar o título da oferta, o resumo da oferta, o resumo longo da oferta e salvar as alterações.

   > [!NOTE]
   > Cuide do seguinte enquanto estiver atualizando os detalhes da SKU.
   > **Não insira texto duplicado nas descrições da oferta e da SKU. Não insira texto duplicado no título da SKU e no resumo longo da oferta. Não insira texto duplicado no título da SKU e no resumo da oferta.**
   >
   >

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. Na seção *LOGOTIPOS* da guia **DETALHES** , você pode atualizar os logotipos. No entanto, verifique se os logotipos seguem as [diretrizes do Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte a seção Etapa 1: Fornecer conteúdo de marketing do Marketplace -> Detalhes -> Diretrizes de Logotipo do Azure Marketplace).

   > [!NOTE]
   > O ícone Hero é opcional. Você pode optar por não carregar um ícone Hero. No entanto, uma vez carregado o ícone Hero, não haverá provisão para excluí-lo do portal de Publicação. Nesse caso, você deve seguir as [diretrizes do ícone Hero](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte a seção Etapa 1: Fornecer conteúdo de marketing do Marketplace -> Detalhes -> Diretrizes adicionais para a faixa de logotipo Hero).
   >
   >
7. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Atualizar a descrição da SKU
Você pode atualizar os detalhes da SKU e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão **PORTUGUÊS (BR)** .
5. No menu do lado esquerdo, clique na guia **PLANOS** . Na seção *SKUs* da guia **PLANOS** , você pode atualizar o título da SKU, o resumo da SKU e os detalhes da descrição da SKU e salvar as alterações.

   > [!NOTE]
   > Cuide do seguinte enquanto estiver atualizando os detalhes da SKU. **Não insira texto duplicado nas descrições da oferta e da SKU. Não insira texto duplicado no título da SKU e no resumo longo da oferta. Não insira texto duplicado no Título da SKU e no resumo da oferta.**
   >
   >
6. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este link
7. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 Alterar os links existentes adicionar novos links
Você pode alterar os links existentes ou adicionar novos links e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão **PORTUGUÊS (BR)** .
5. No menu do lado esquerdo, clique na guia **LINKS** .
6. Se você quiser adicionar um novo link, na seção *Links* , clique no botão **ADICIONAR LINK** . A caixa de diálogo *Adicionar Link* será aberta. Nessa caixa de diálogo, você poderá adicionar os campos Título e URL do link e salvar as alterações. Você pode inserir qualquer link que contenha informações que possam ajudar os clientes.
7. Se você quiser atualizar ou excluir um link existente, selecione o link apropriado e clique no botão Editar ou no botão Excluir adequadamente.

   > [!NOTE]
   > Verifique se os links que você inseriu nesta seção estão funcionando corretamente, pois esses links são validados durante o processo de solicitação de produção.
   >
   >
8. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 Alterar uma imagem de exemplo existente ou adicionar uma nova imagem de exemplo
Você pode alterar as imagens de exemplo existentes ou adicionar novas imagens de exemplo e publicar novamente sua oferta seguindo as etapas abaixo:

> [!NOTE]
> Somente uma imagem de exemplo é exibida em [https://portal.azure.com](https://portal.azure.com).
>
>

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão **PORTUGUÊS (BR)** .
5. No menu do lado esquerdo, clique na guia **IMAGENS DE EXEMPLO** .
6. Se você deseja adicionar uma nova imagem de exemplo, na seção *Imagens de exemplo* , clique no botão **CARREGAR UMA NOVA IMAGEM** e salve as alterações.

   > [!NOTE]
   > Incluir uma imagem de exemplo é uma etapa opcional.
   >
   >
7. Se você quiser atualizar ou excluir uma imagem de exemplo existente, em seguida, localize a imagem de exemplo apropriada e clique no botão **SUBSTITUIR IMAGEM** ou no botão Excluir conforme for apropriado.
8. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5 Atualizar o conteúdo legal
Você pode atualizar o conteúdo legal e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão **PORTUGUÊS (BR)** .
5. No menu do lado esquerdo, clique na guia **LEGAL** . Na seção *Legal* , você pode atualizar suas políticas/termos de uso. Insira ou cole as políticas/termos na caixa de texto *Termos de Uso* e salve as alterações.
6. O limite de caracteres para os termos legais de uso é 1.000.000 caracteres.
7. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
8. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 Atualizar as informações de suporte
Você pode atualizar as informações de suporte e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SUPORTE** .
4. Na seção *Contato de Engenharia* da guia **SUPORTE** , você pode atualizar os detalhes do contato. Esses detalhes são usados apenas para comunicações internas entre o parceiro e a Microsoft.
5. Na seção *Atendimento ao Cliente* da guia **SUPORTE**, você pode atualizar os detalhes de contato do Suporte, como **Nome, Email, Telefone** e **URL de Suporte**. Esses detalhes são usados apenas para comunicações internas entre o parceiro e a Microsoft.

   > [!NOTE]
   > Se você quiser fornecer suporte apenas por email, forneça um número de telefone fictício na seção **Atendimento ao Cliente** . Nesse caso, o email fornecido será usado.
   >
   >
6. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
7. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 Atualizar as categorias
Você pode atualizar a seção de categorias para sua oferta e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **CATEGORIAS** .
4. Na seção *Categorias* , você pode atualizar as categorias da oferta e salvar as alterações. Você pode selecionar até cinco categorias para a galeria do Azure Marketplace.
5. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
6. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. Como adicionar uma nova SKU em uma oferta listada
Você pode adicionar uma nova SKU sob sua oferta online seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SKUs** . Depois disso, clique no botão **ADICIONAR UM SKU**.  Uma nova caixa de diálogo será aberta. Insira um identificador de SKU em minúsculas. Marque a caixa de seleção do modelo cobrança traga seu próprio (BYOL) se quiser publicar a nova SKU com o modelo de cobrança BYOL. Caso contrário, desmarque a caixa de seleção para BYOL. Depois disso, clique na marca de escala na caixa de diálogo para criar uma nova SKU. Se você não optar pelo modelo de cobrança BYOL para a nova SKU, o modelo de cobrança será automaticamente definido como Por Hora para a nova SKU. Se quiser habilitar a avaliação gratuita de 30 dias do modelo de Cobrança por hora, clique na opção “Um Mês” em “Há uma avaliação gratuita disponível?”. Caso contrário, selecione "NENHUMA AVALIAÇÃO". [Observação: a opção “Há uma avaliação gratuita disponível ?” só será exibida se você NÃO tiver selecionado BYOL na caixa de diálogo ao criar o novo SKU.]

   > [!IMPORTANT]
   > A opção "Ocultar esta SKU do Marketplace porque ela sempre deverá ser comprada por meio de um modelo de solução" só deverá ser marcada como "SIM" se você tiver aprovação para publicar uma oferta de modelo de solução no Azure Marketplace. Caso contrário, essa opção sempre deverá ser marcada como "NÃO".
   >
   >
4. Agora, no menu do lado esquerdo, clique na guia **IMAGENS DE VM** e descubra a nova SKU que você criou.
5. Para configurar a nova SKU, consulte a ETAPA 5 deste [link](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) para obter diretrizes.
6. Para adicionar o material de marketing à nova SKU, veja a seção Etapa 1: Fornecer conteúdo de marketing do Marketplace -> Detalhes -> números 2 a 5 deste [link](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para adicionar as informações de preços à nova SKU, consulte a seção 2.1. Defina os preços de VM deste [link](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
9. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. Como alterar a contagem de discos de dados para uma SKU listada
Você não pode aumentar/diminuir a contagem de discos de dados de uma SKU listada. Você precisa criar uma nova SKU nesse caso. Consulte a seção [3. Como adicionar uma nova SKU em uma oferta online](#3-how-to-add-a-new-sku-under-a-live-offer) para obter orientações detalhadas.

## <a name="5----how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5.    Como excluir uma oferta listada do Azure Marketplace
Há vários aspectos que precisam ser atendidos no caso de uma solicitação para remover uma oferta online. Siga as etapas abaixo para obter as diretrizes da equipe de suporte para remover uma oferta listada do Azure Marketplace:

1. Gere um tíquete de suporte usando este [link](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)
2. Selecione o Tipo de problema como **“Gerenciando ofertas”** e selecione a Categoria como **“Modificando uma oferta e/ou uma SKU já em produção”**
3. Enviar a solicitação

A equipe de suporte orientará você pelo processo de exclusão da oferta/SKU.

> [!NOTE]
> Você sempre poderá excluir a oferta enquanto ela estiver no status Rascunho (isto é, ainda não em PREPARO ou em PRODUÇÃO) clicando no botão **DESCARTAR RASCUNHO** na guia **HISTÓRICO**.
>
>

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. Como excluir uma SKU listada do Azure Marketplace
Você pode excluir uma SKU listada do Azure Marketplace seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No painel do lado esquerdo, clique na guia **SKUs** .
4. Selecione a SKU que você deseja excluir e clique no botão Excluir para essa SKU.
5. Quando terminar, navegue até a guia PUBLICAR no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente a oferta no Azure Marketplace.
6. Depois que a oferta for publicada novamente no Azure Marketplace, a SKU será excluída do Azure Marketplace e do Portal do Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. Como excluir a versão atual de uma SKU listada do Azure Marketplace
Você pode excluir a versão atual de uma SKU listada do Azure Marketplace seguindo as etapas abaixo. Quando o processo for concluído, a SKU será revertida para a versão anterior.

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No painel do lado esquerdo, clique na guia **IMAGENS DE VM** .
4. Selecione a SKU cuja versão atual você deseja excluir e clique no botão Excluir para essa versão.
5. Quando terminar, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente a oferta no Azure Marketplace.
6. Depois que a oferta for publicada novamente no Azure Marketplace, a versão atual da SKU listada será excluída do Azure Marketplace e do Portal do Azure. O SKU será revertido para a versão anterior.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. Como reverter o preço de listagem para os valores de produção
Alterei o preço de uma SKU listada (ou removi as regiões de cobranças de uma SKU listada). Como não há suporte no Azure Marketplace, quero reverter as alterações para os valores de produção. Como posso fazer isso?

Siga as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **PREÇOS** .
4. Na guia Preços, selecione uma região cujos preços que você deseja redefinir.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. No caso de SKUs com modelo de cobrança por hora, redefina os preços de todos os núcleos como estão em produção na região selecionada. Para SKUs com modelo de faturamento BYOL, disponibilize a SKU na região marcando a caixa de seleção para a SKU em questão na seção DISPONIBILIDADE DA SKU COM LICENÇA EXTERNA (BYOL) (consulte a captura de tela abaixo).

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Agora, clique no botão **DEFINIR PREÇOS PARA OUTROS MERCADOS AUTOMATICAMENTE COM BASE NOS PREÇOS DOS ESTADOS UNIDOS**.

   > [!NOTE]
   > O rótulo do botão pode ser diferente dependendo da região selecionada. Como nós selecionamos Estados Unidos durante a criação desse documento, o botão está rotulado como "Definir preços para outros mercados automaticamente com base nos preços dos Estados Unidos" na captura de tela abaixo.
   >
   >

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. O assistente de definição automática de preços será aberto. A primeira página exibe a seleção do mercado base. Faça sua seleção e passe para a próxima página clicando no botão **"->"**.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. A opção para selecionar os núcleos e planos será exibida na página 2. Selecione os planos desejados e os núcleos e clique no botão "->".

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. A página 3 exibe os mercados/as regiões. Clique no botão Ativar/Desativar Tudo para selecionar todas as regiões ou marque as caixas das regiões manualmente. Clique no botão "->" para passar para a próxima página.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. A página 4 exibe as taxas de câmbio. Clique no botão Concluir para concluir as etapas. O assistente redefinirá os preços de acordo com sua seleção.
11. Agora, navegue até a guia Preços e clique no botão "EXIBIR RESUMO E ALTERAÇÕES".
    Selecione "Rascunho" na seção "Exibir Versão" e "Produção" na seção "Comparar com" (consulte a captura de tela abaixo). Se você não vir nenhuma diferença entre os preços, isso significa que os preços foram revertidos para os valores de produção com êxito.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Depois de fazer as alterações, navegue até a guia PUBLICAR e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
13. Depois de testar sua oferta em preparo, navegue até a guia PUBLICAR no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. Como reverter o modelo de cobrança para os valores de produção
Alterei o modelo de cobrança de uma SKU listada. Como não há suporte no Azure Marketplace, quero reverter as alterações para os valores de produção. Como posso fazer isso?

Siga as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SKUs** .
4. Clique o botão EDITAR para reverter o modelo de cobrança. Uma janela será aberta. Marque ou desmarque a caixa de seleção **A cobrança e o licenciamento são realizados fora do Azure (também conhecido como Traga sua própria licença)** conforme for apropriado.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Quando terminar, consulte a resposta da pergunta 8 neste documento para reverter os preços.
6. Em seguida, navegue até a guia **PUBLICAR** no portal de Publicação e envie a oferta por push para preparo para testá-la. Quando terminar de testar a oferta, clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. Como reverter a configuração de visibilidade de uma SKU listada para o valor de produção
Siga as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SKUs** .
4. Selecione a SKU e reverta a configuração de visibilidade para o valor de produção.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Quando terminar de fazer as alterações, clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

## <a name="see-also"></a>Consulte também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
* [Entendendo os relatórios de pagamento](marketplace-publishing-report-payout.md)
* [Como alterar seu incentivo ao revendedor de Provedor de Soluções na Nuvem](marketplace-publishing-csp-incentive.md)
* [Solução de problemas comuns de publicação no Marketplace](marketplace-publishing-support-common-issues.md)
* [Obtenha suporte como um editor](marketplace-publishing-get-publisher-support.md)
* [Criando uma imagem de máquina virtual no local](marketplace-publishing-vm-image-creation-on-premise.md)
* [Criar uma máquina virtual que executa o Windows no Portal de Visualização do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

