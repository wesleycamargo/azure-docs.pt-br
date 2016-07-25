<properties
   pageTitle="Gerenciar uma imagem de máquina virtual no Azure Marketplace | Microsoft Azure"
   description="Guia detalhado sobre como gerenciar sua imagem de máquina virtual no Azure Marketplace após a publicação inicial."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="hascipio;"/>

# Guia de pós-produção para ofertas de máquina virtual no Azure Marketplace

Este artigo explica como você pode atualizar uma oferta de Máquina Virtual online no Azure Marketplace. Ele também orienta você durante o processo de adição de um ou mais SKUs a uma oferta existente e de remoção de uma oferta de Máquina Virtual online ou de um SKU do Azure Marketplace.

**Assim que o SKU estiver online no Azure Marketplace, você não poderá atualizar os detalhes mostrados abaixo:**

- **Identificador de SKU**
- **Identificador do Publicador**
- **Identificador de Oferta**
- **Alteração de preços**
- **Alteração de modelo de cobrança**
- **Remoção de regiões de cobrança**


## 1\. Como atualizar os detalhes técnicos de uma SKU

Você pode atualizar uma imagem de VM e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **IMAGENS DE VM**.
4. Na seção **SKUs** da guia IMAGENS DE VM, localize a SKU que você deseja atualizar. Depois disso, atualize o número de versão da SKU. A nova versão deve estar no formato X.Y.Z, onde X, Y, Z são inteiros. As alterações de versão só devem ser incrementais.
5. Na caixa **URL do VHD de sistema operacional**, atualize o URI da assinatura de acesso compartilhado criado para o VHD do sistema operacional e salve as alterações.
6. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este [link](marketplace-publishing-vm-image-test-in-staging.md)
7. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img01.png)

## 2\. Como atualizar os detalhes não técnicos de uma oferta ou uma SKU

Você pode atualizar os detalhes não técnicos (marketing, jurídico, suporte, categorias) de sua oferta online ou de sua SKU no Azure Marketplace.

### 2\.1 Atualizar a descrição e os logotipos da oferta

Você pode atualizar os detalhes da oferta e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING**.
4. Clique no botão **PORTUGUÊS (BR)**.
5. No menu do lado esquerdo, clique na guia **DETALHES**. Na seção *DESCRIÇÃO* da guia **DETALHES**, você pode atualizar o título da oferta, o resumo da oferta, o resumo longo da oferta e salvar as alterações.

    >[AZURE.NOTE] Cuide do seguinte enquanto estiver atualizando os detalhes da SKU. **Não insira texto duplicado nas descrições da oferta e da SKU. Não insira texto duplicado no título da SKU e no resumo longo da oferta. Não insira texto duplicado no título da SKU e no resumo da oferta.**

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.png)

6. Na seção *LOGOTIPOS* da guia **DETALHES**, você pode atualizar os logotipos. No entanto, verifique se os logotipos seguem as [diretrizes do Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (veja a seção Etapa 1: Fornecer conteúdo de marketing do Marketplace -> Detalhes -> Diretrizes de Logotipo do Azure Marketplace).

    >[AZURE.NOTE] O ícone Hero é opcional. Você pode optar por não carregar um ícone Hero. No entanto, uma vez carregado o ícone Hero, não haverá provisão para excluí-lo do portal de Publicação. Nesse caso, você deve seguir as [diretrizes do ícone Hero](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte a seção Etapa 1: Fornecer conteúdo de marketing do Marketplace -> Detalhes -> Diretrizes adicionais para a faixa de logotipo Hero).

7. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este [link](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.1.png)

### 2\.2. Atualizar a descrição da SKU

Você pode atualizar os detalhes da SKU e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING**.
4. Clique no botão **PORTUGUÊS (BR)**.
5. No menu do lado esquerdo, clique na guia **PLANOS**. Na seção *SKUs* da guia **PLANOS**, você pode atualizar o título da SKU, o resumo da SKU e os detalhes da descrição da SKU e salvar as alterações.

    >[AZURE.NOTE] Cuide do seguinte enquanto estiver atualizando os detalhes da SKU. **Não insira texto duplicado nas descrições da oferta e da SKU. Não insira texto duplicado no título da SKU e no resumo longo da oferta. Não insira texto duplicado no Título da SKU e no resumo da oferta.**

6. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este link
7. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.2.png)

### 2\.3 Alterar os links existentes adicionar novos links

Você pode alterar os links existentes ou adicionar novos links e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING**.
4. Clique no botão **PORTUGUÊS (BR)**.
5. No menu do lado esquerdo, clique na guia **LINKS**.
6. Se você quiser adicionar um novo link, na seção *Links*, clique no botão **ADICIONAR LINK**. A caixa de diálogo *"Adicionar Link"* será aberta. Nessa caixa de diálogo, você poderá adicionar os campos Título e URL do link e salvar as alterações. Você pode inserir qualquer link que contenha informações que possam ajudar os clientes.
7. Se você quiser atualizar ou excluir um link existente, selecione o link apropriado e clique no botão Editar ou no botão Excluir adequadamente.

    >[AZURE.NOTE] Verifique se os links que você inseriu nesta seção estão funcionando corretamente, pois esses links são validados durante o processo de solicitação de produção.

8. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este [link](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.3.png)

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### 2\.4 Alterar uma imagem de exemplo existente ou adicionar uma nova imagem de exemplo

Você pode alterar as imagens de exemplo existentes ou adicionar novas imagens de exemplo e publicar novamente sua oferta seguindo as etapas abaixo:

>[AZURE.NOTE] Somente uma imagem de exemplo é exibida no [https://portal.azure.com](https://portal.azure.com).

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING**.
4. Clique no botão **PORTUGUÊS (BR)**.
5. No menu do lado esquerdo, clique na guia **IMAGENS DE EXEMPLO**.
6. Se você deseja adicionar uma nova imagem de exemplo, na seção *Imagens de exemplo*, clique no botão **CARREGAR UMA NOVA IMAGEM** e salve as alterações.

    >[AZURE.NOTE] Incluir uma imagem de exemplo é uma etapa opcional.

7. Se você quiser atualizar ou excluir uma imagem de exemplo existente, em seguida, localize a imagem de exemplo apropriada e clique no botão **SUBSTITUIR IMAGEM** ou no botão Excluir adequadamente.

8. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este [link](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.4.png)

### 2\.5 Atualizar o conteúdo legal

Você pode atualizar o conteúdo legal e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING**.
4. Clique no botão **PORTUGUÊS (BR)**.
5. No menu do lado esquerdo, clique na guia **LEGAL**. Na seção *Legal*, você pode atualizar suas políticas/termos de uso. Insira ou cole as políticas/termos na caixa de texto *Termos de uso* e salve as alterações.
6. O limite de caracteres para os termos legais de uso é 1.000.000 caracteres.
7. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este [link](marketplace-publishing-vm-image-test-in-staging.md)
8. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.5.png)

### 2\.6 Atualizar as informações de suporte

Você pode atualizar as informações de suporte e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SUPORTE**.
4. Na seção *Contato de Engenharia* da guia **SUPORTE**, você pode atualizar os detalhes do contato. Esses detalhes são usados apenas para comunicações internas entre o parceiro e a Microsoft.
5. Na seção *Atendimento ao Cliente* da guia **SUPORTE**, você pode atualizar os detalhes de contato do Suporte, como **Nome, Email, Telefone** e **URL de Suporte**. Esses detalhes são usados apenas para comunicações internas entre o parceiro e a Microsoft.

    >[AZURE.NOTE] Se você quiser fornecer suporte apenas por email, forneça um número de telefone fictício na seção **Atendimento ao cliente**. Nesse caso, o email fornecido será usado.

6. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este [link](marketplace-publishing-vm-image-test-in-staging.md)
7. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

### 2\.7 Atualizar as categorias

Você pode atualizar a seção de categorias para sua oferta e publicar novamente sua oferta seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com)
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **CATEGORIAS**.
4. Na seção *Categorias*, você pode atualizar as categorias da oferta e salvar as alterações. Você pode selecionar até cinco categorias para a galeria do Azure Marketplace.
5. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este [link](marketplace-publishing-vm-image-test-in-staging.md)
6. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

## 3\. Como adicionar uma nova SKU a uma oferta online

Você pode adicionar uma nova SKU sob sua oferta online seguindo as etapas abaixo:

1. Faça logon no [portal de Publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SKUs**. Depois disso, clique no botão **ADICIONAR UMA SKU**. Uma nova caixa de diálogo será aberta.. Insira um identificador de SKU em minúsculas. Marque a caixa de seleção do modelo cobrança traga seu próprio (BYOL) se quiser publicar a nova SKU com o modelo de cobrança BYOL. Caso contrário, desmarque a caixa de seleção para BYOL. Depois disso, clique na marca de escala na caixa de diálogo para criar uma nova SKU. Se você não optar pelo modelo de cobrança BYOL para a nova SKU, o modelo de cobrança será automaticamente definido como Por Hora para a nova SKU. Se quiser habilitar a avaliação gratuita de 30 dias do modelo de Cobrança por hora, clique na opção “Um Mês” em “Há uma avaliação gratuita disponível?”. Caso contrário, selecione “NENHUMA AVALIAÇÃO”. [Observação: a opção “Há uma avaliação gratuita disponível?” só será mostrada se você NÃO tiver selecionado BYOL na caixa de diálogo durante a criação do novo SKU.]

    >[AZURE.IMPORTANT] A opção "Ocultar esta SKU do Marketplace porque ela sempre deverá ser comprada por meio de um modelo de solução" só deverá ser marcada como "SIM" se você tiver aprovação para publicar uma oferta de modelo de solução no Azure Marketplace. Caso contrário, essa opção sempre deverá ser marcada como "NÃO".

4. Agora, no menu do lado esquerdo, clique na guia **IMAGENS DE VM** e descubra a nova SKU que você criou.
5. Para configurar a nova SKU, consulte a ETAPA 5 deste [link](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) para obter diretrizes.
6. Para adicionar o material de marketing à nova SKU, consulte a seção Etapa 1: Fornecer conteúdo de marketing do Marketplace -> Detalhes -> números 2 a 5 deste [link](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para adicionar as informações de preços à nova SKU, consulte a seção 2.1. Definir os preços de VM deste [link](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Depois de fazer as alterações, navegue até a guia **PUBLICAR** e clique no botão **ENVIAR POR PUSH PARA PREPARO**. Para obter orientações detalhadas sobre como testar a sua oferta no ambiente de preparo, veja este [link](marketplace-publishing-vm-image-test-in-staging.md)
9. Depois de testar sua oferta em preparo, navegue até a guia **PUBLICAR** no portal de Publicação e clique no botão **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para publicar novamente sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img03.1-1.png)

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img03.1-2.png)

## 4\. Como excluir uma oferta online ou uma SKU do Azure Marketplace

Há vários aspectos que precisam ser atendidos no caso de uma solicitação para remover uma oferta online. Siga as etapas abaixo para obter as diretrizes da equipe de suporte para remover uma oferta online/SKU do Azure Marketplace:

1.	Gerar um tíquete de suporte usando este [link](https://support.microsoft.com/pt-BR/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=pt-BR&supportregion=pt-BR&pesid=15635&ccsid=635993707583706681)
2.	Selecione o Tipo de problema como **"Gerenciando ofertas"** e selecione a Categoria como **"Modificando uma oferta e/ou SKU já em produção"**
3.	Enviar a solicitação

A equipe de suporte orientará você pelo processo de exclusão da oferta/SKU.

>[AZURE.NOTE] Você sempre poderá excluir a oferta enquanto ela estiver no status Rascunho (isto é, ainda não em PREPARO ou em PRODUÇÃO) clicando no botão **DESCARTAR RASCUNHO** na guia **HISTÓRICO**.

## Veja também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
- [Entendendo o relatório de percepções do vendedor](marketplace-publishing-report-seller-insights.md)
- [Entendendo os relatórios de pagamento](marketplace-publishing-report-payout.md)
- [Como alterar seu incentivo ao revendedor de Provedor de Soluções na Nuvem](marketplace-publishing-csp-incentive.md)
- [Solução de problemas comuns de publicação no Marketplace](marketplace-publishing-support-common-issues.md)
- [Obtenha suporte como um editor](marketplace-publishing-get-publisher-support.md)
- [Criando uma imagem de máquina virtual no local](marketplace-publishing-vm-image-creation-on-premise.md)
- [Criar uma máquina virtual que executa o Windows no Portal de Visualização do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

<!---HONumber=AcomDC_0713_2016-->