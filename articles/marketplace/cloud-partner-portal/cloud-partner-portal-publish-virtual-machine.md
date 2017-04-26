---
title: "Publicar uma máquina virtual no Azure Marketplace | Microsoft Docs"
description: "Este artigo fornece detalhes sobre a publicação uma máquina virtual pelo Portal do Cloud Partner"
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6e5b1eb51c10bb51762f6815eda72add1341a4ad
ms.lasthandoff: 04/18/2017


---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Publicar uma máquina virtual no Azure Marketplace

Este artigo lista as diversas etapas envolvidas na publicação de uma máquina virtual no Azure Marketplace.

## <a name="what-are-pre-requisites-for-publishing-a-vm"></a>Pré-requisitos para a publicação de uma VM

Pré-requisitos para listagem no Azure Marketplace

1.  Técnicos

    -   [Pré-requisitos técnicos para criação de uma imagem de máquina virtual para o Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

    -   [Criando e carregando um VHD do Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    -   [Criar e testar uma VM do Linux por meio de uma imagem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

    -   [Criar e carregar um VHD do Windows](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

    -   [Criar e testar uma VM do Windows por meio de uma imagem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

    -   [Como solucionar problemas comuns encontrados durante a criação do VHD](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting )


2.  Não técnico (requisitos de negócios)

    -   Sua empresa (ou sua subsidiária) deve estar localizada em um país de origem de venda ao qual o Azure Marketplace dá suporte

    -   O produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Azure Marketplace

    -   Você é responsável por disponibilizar suporte técnico aos clientes de forma comercialmente justa, seja gratuito, pago ou por meio do suporte à comunidade.

    -   Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.

    -   Você deve fornecer conteúdo que atenda aos critérios da oferta a serem listados no Azure Marketplace e no Portal de gerenciamento do Azure

    -   Você deve concordar com os termos das Políticas de participação do Azure Marketplace e o Contrato do fornecedor

    - Você deve concordar em cumprir os [Termos de Uso](https://azure.microsoft.com/support/legal/website-terms-of-use/), a [Política de privacidade da Microsoft](http://www.microsoft.com/privacystatement/default.aspx) e o [Contrato do programa de certificação do Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-vm-offer"></a>Como criar um nova oferta de VM

Depois de atender a todos os pré-requisitos, você está pronto para começar a criar sua oferta de VM (Máquina Virtual). Antes de começar, uma visão geral de uma oferta e uma SKU

### <a name="offer"></a>Oferta
Uma oferta de máquina virtual corresponde a uma oferta de classe de produto de um fornecedor. Se você tiver um novo tipo de máquina virtual/produto que queira disponibilizar no Azure Marketplace, uma nova oferta será a melhor opção. Uma oferta é uma coleção de SKUs. Cada oferta aparece como sua própria entidade no Azure Marketplace. 

### <a name="sku"></a>SKU
Uma SKU é a menor unidade de compra de uma oferta. Embora esteja dentro da mesma classe de produto (oferta), as SKUs permite diferenciar entre os diversos recursos, tipos de imagem de VM e modelos de cobrança com suporte. 

Adicione várias SKUs se quiser dar suporte a modelos diferentes de cobrança, como BYOL (Traga Sua Própria Licença), PAYG (Pré-Pago) etc. Adicione várias SKUs quando cada SKU der suporte a um conjunto diferente de recursos e cada um deles tiver preços diferentes. Adicione também várias SKUs se houver diferentes imagens de VM para nuvens públicas versus soberanas. 

Uma SKU aparece sob a oferta pai no Azure Marketplace, embora apareça como sua própria entidade de compra no portal do Azure. 

1.  Faça logon no [Portal do Cloud Partner](http://cloudpartner.azure.com/).

2.  Na barra de navegação à esquerda, clique em “+ Nova oferta” e selecione “Máquinas Virtuais”.

    ![publishvm1][1]

3.  Uma nova exibição de oferta do "Fornecedor" aparece agora para você e estamos prontos para começar a criar.

   ![publishvm2][2]

4.  Os "formulários" que precisam ser preenchidos estão visíveis à esquerda no modo de exibição "Editor". Cada "formulário" consiste em um conjunto de campos que devem ser preenchidos. Campos necessários são marcados com um asterisco vermelho (\*).

> Há 4 formulários principais para a criação de uma VM

-   Configurações da oferta

-   SKUs

-   Marketplace

-   Suporte

## <a name="how-to-fill-out-the-offer-settings-form"></a>Como preencher o formulário de Configuração da oferta
O formulário de configurações da oferta é um formulário básico para especificar as configurações da oferta. Os diferentes campos são descritos abaixo.

### <a name="offer-id"></a>ID da oferta
Esse é um identificador exclusivo para a oferta em um perfil de fornecedor. Essa ID será visível em URLs de produto, relatórios de cobrança e modelos ARM. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e pode ter um máximo de 50 caracteres. Observe que esse campo é bloqueado quando uma oferta entra no ar.

Para exemplo, se um fornecedor do **contoso** publicar uma oferta com a ID **sample-vm**, ela será exibida no Azure Marketplace como **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**

### <a name="publisher-id"></a>ID do fornecedor
Este menu suspenso permite que você escolha o perfil de fornecedor em que deseja publicar essa oferta. Observe que esse campo é bloqueado quando uma oferta entra no ar.

### <a name="name"></a>Nome
Nome de exibição da oferta. Esse é o nome que aparecerá no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [Portal do Azure](https://portal.azure.com/). Ele pode ter um máximo de 50 caracteres. A orientação aqui é incluir um nome de marca reconhecível para o seu produto. Não inclua o nome da sua empresa aqui a menos que seja sua forma de comercialização. Se você estiver comercializando essa oferta em seu próprio site, certifique-se de que o nome seja exatamente como aparece no site.

Clique em “Salvar” para salvar o progresso. A próxima etapa seria adicionar SKUs para a oferta.

## <a name="how-to-create-skus"></a>Como criar SKUs
Clique no formulário "SKUs". Aqui você pode ver uma opção para "Adicionar uma SKU" clicando na que permitirá que você insira uma “ID de SKU”.

![publishvm4][4]

Este é um identificador exclusivo para a SKU dentro de uma oferta. Essa ID será visível em URLs de produto, relatórios de cobrança e modelos ARM. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e pode ter um máximo de 50 caracteres. Observe que esse campo é bloqueado quando uma oferta entra no ar. Pode existir várias SKUs em uma oferta. É necessário uma SKU para cada imagem que você planeja publicar.

Após a adição de uma SKU, ela aparecerá na lista de SKUs dentro do formulário "SKUs". Clique no nome da SKU para ver os detalhes dessa SKU específica. Aqui estão alguns detalhes para alguns dos campos.

### <a name="hide-this-sku"></a>Ocultar esta SKU
Esse sinalizador permite que você defina se a SKU específica ficará visível no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [Portal do Azure](https://portal.azure.com/) aos clientes. Oculte a SKU se só a quiser disponível por meio de modelos da solução e não para compra individual.

### <a name="cloud-availability"></a>Disponibilidade na nuvem 
Este campo permite que você defina a disponibilidade da SKU nas diferentes nuvens do Azure.

#### <a name="public-azure"></a>Azure público 

Esta SKU será implantada nos clientes em todas as regiões do Azure público com integração ao Marketplace.

#### <a name="azure-government-cloud"></a>Nuvem do Azure Governamental 

Esta SKU será implantada na nuvem do Azure Governamental. Antes de publicar no [Azure Governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), recomendamos que os fornecedores testem e validem se a solução funciona conforme o esperado. Para preparar e testar, solicite uma conta de avaliação [aqui](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). Observe que o Microsoft Azure Governamental é uma nuvem de comunidade do governo, com acesso controlado para clientes federais, estaduais, locais ou tribais dos EUA e parceiros qualificados para atender a essas entidades.

### <a name="countryregion-availability"></a>Disponibilidade para país/região
Este campo determina as regiões em que a SKU estará disponível para compra. Você precisa considerar com atenção onde você pretende disponibilizar suas SKUs. Alguns países são classificados como "País de Imposto Remetido da Microsoft".

-   No "País de Imposto Remetido da Microsoft", a Microsoft coleta os impostos dos clientes e paga (envia) taxas ao governo.

-   Em outros, os parceiros são responsáveis por coletar impostos dos clientes e pagar impostos ao governo. Se você optar por vender nesses países, deverá ter a funcionalidade para calcular e pagar neles os impostos.

![publishvm5][5]

### <a name="pricing"></a>Preços 
Existe suporte atualmente para 2 tipos de modelos de preços.

#### <a name="bring-your-own-license-byol"></a>BYOL (Traga sua Própria Licença)

Você gerencia o licenciamento do software em execução na VM. A Microsoft cobrará somente os custos de infraestrutura.

#### <a name="usage-based-monthly-billed-sku"></a>SKU cobrado mensalmente com base no uso

Os clientes são cobrados por hora com base nas taxas definidas pelos fornecedores sobre tamanhos de VM. No caso do modelo de **cobrança por hora** dos SKUs, o preço total será a soma do custo do software cobrado pelo editor e do custo da infraestrutura cobrado pela Microsoft. Esse custo total será exibido para o cliente como um preço por hora e mensal quando ele considerar a compra. A cobrança nesse caso será mensal.

Dentro do modelo com base no uso, há configurações adicionais necessárias.

##### <a name="free-trial"></a>Avaliação gratuita

Especifique se quer oferecer uma avaliação gratuita aos clientes. Nesse modelo, o cliente não é cobrado o custo do software pelos primeiros 30 dias (gratuitamente) após implantar a VM. Após os 30 dias, ele é cobrados por hora com base nas taxas de definidas pelos fornecedores do modelo por hora.

##### <a name="per-core-pricing"></a>Por preços de núcleo

Você pode definir preços por núcleo para a SKU. Para isso, basta inserir um preço básico para um único núcleo e nós calculamos automaticamente os preços para o restante dos núcleos. Insira os preços em USD no portal e calcularemos automaticamente os preços para outras regiões. Verifique os preços em outras regiões usando **Exportar Dados de Preços**

![publishvm6][6]

##### <a name="discrete-pricing"></a>Preços discretos

Defina o preço para cada conjuntos de núcleos individualmente se quiser tabelar o preço de cada núcleo separadamente.

![publishvm7][7]

##### <a name="export-import-pricing"></a>Preços de importação-exportação

Você tem a flexibilidade de exportar preços configurados pelo portal para fazer alterações pela interface do excel. Isso também permite verificar preços por região e preços em moedas locais. Se você clicar em **Preços de Exportação** um arquivo do Excel será baixado com detalhes de preços preenchidos. Você poderá editá-los dentro do excel e, em seguida, usar os **Preços de Importação** para importar as alterações feitas. Os preços importados refletirão também no portal.

Nesse excel de preços, os preços das diferentes regiões estão listados na moeda local. A taxa de câmbio que usamos é atualizada diariamente.

![publishvm8][8]

##### <a name="important-notes-about-pricing"></a>Observações importantes sobre preços

-   Os preços não são alteráveis depois que uma oferta entra no ar. Ainda será possível adicionar/remover regiões com suporte.

-   Esse preço será cobrado do usuário além dos [preços da máquina virtual do Azure](http://aka.ms/vmpricingdetails).

-   Os preços são definidos para todas as regiões em moeda local nas taxas de moeda disponíveis no momento da definição de preços.

-   Para definir ou exibir o preço de cada região individualmente, exporte a planilha de preços e importe com preços personalizados.

### <a name="vm-images"></a>Imagens da VM
A próxima seção a ser concluída será a seção Imagens da VM. Antes de ir para essa seção, você precisa ter pronto o VHD que quer publicar. Aqui estão alguns links que ajudam você a criar o VHD:

-   [Pré-requisitos técnicos para criação de uma imagem de máquina virtual para o Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Criando e carregando um VHD do Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Criar e testar uma VM do Linux por meio de uma imagem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Criar e carregar um VHD do Windows](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Criar e testar uma VM do Windows por meio de uma imagem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Como solucionar problemas comuns encontrados durante a criação do VHD](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting )


Quando o VHD estiver pronto, você pode começar a preencher esta seção. Aqui estão alguns detalhes para alguns dos campos.

#### <a name="recommended-vm-sizes"></a>Tamanhos de VM recomendados

Selecione até seis tamanhos de máquina virtual recomendados. Essas são recomendações exibidas para o cliente na folha Tipo de Preço e no Azure Marketplace no Portal do Azure quando eles decidem comprar e implantar sua imagem. **Essas são apenas recomendações. O cliente é capaz de selecionar qualquer tamanho de VM que acomode os discos especificados em sua imagem.** Abaixo é como os tamanhos de VM recomendados serão exibidos ao cliente no Portal do Azure.

![publishvm9][9]

#### <a name="open-ports"></a>Abrir portas

Especifique as portas que você quer abrir e disponibilizar. Essas portas são abertas durante a implantação das VMs.

#### <a name="adding-vm-images"></a>Adicionar imagens de VM

A próxima etapa seria adicionar uma imagem de VM à SKU. Você pode adicionar até 8 versões de disco por SKU. Somente o número mais alto de versão do disco para determinada SKU aparecerá no Azure Marketplace. Outras poderão ser vistas por meio de APIs. 

Inicie clicando o "+ Nova versão" na seção **Versão do disco**. Isso mostrará uma coleção de campos que precisam ser preenchidos.

##### <a name="vm-image-version"></a>Versão de imagem da VM

A versão de imagem da VM precisa seguir o formato de [versão semântica](http://semver.org/). Versões devem ser no formato X.Y.Z, onde X, Y e Z são inteiros. Imagens em SKUs diferentes podem ter diferentes versões principais e secundárias. Versões em uma SKU só devem ser alterações incrementais, aumentando a versão de patch (Z de X.Y.Z).

##### <a name="os-vhd-url"></a>URL DE VHD DO SO

Insira o [URI de assinatura de acesso compartilhado](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) criado para o VHD do sistema operacional.

Se houver discos de dados associados a essa SKU, você poderá optar por adicioná-los clicando no link "+ Novo disco de dados". Isso trará os campos adicionais a serem preenchidos.

##### <a name="lun-vhd-url"></a>URL DO VHD DE LUN

Insira o [URI de assinatura de acesso compartilhado](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) para seu disco de dados.

##### <a name="lun-number"></a>Número de LUN

Atribua um número a esse LUN. Esse número será reservado para esse disco de dados nessa SKU.

Observe que depois de publicar uma SKU com determinada versão de VM e discos de dados, eles ficam bloqueados e não podem ser modificados. Para versões adicionais de VM acrescentadas à SKU, o número de discos de dados aos quais ela precisa dar suporte não pode ser alterado.

##### <a name="common-sas-url-issues--fixes"></a>Problemas comuns e correções da URL de SAS

| **Problema**                                                           | **Mensagem de falha**                                                           | **Correção**                                                   | **Link da documentação**                                                                                    |
|---------------------------------------------------------------------|-------------------------------------------------------------------------------|-----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| Falha ao copiar imagens – "?" não foi encontrado na URL SAS             | Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido.    | Atualizar a URL SAS usando as ferramentas recomendadas                | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| Falha ao copiar imagens – Os parâmetros “st” e “se” não existem na URL SAS | Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido.    | Atualizar a URL SAS com datas de Início e Término         | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| Falha ao copiar as imagens – “sp=rl” não existe URL SAS                  | Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido     | Atualizar a URL SAS com permissões definidas como “Leitura” e “Listagem” | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| Falha ao copiar imagens – A URL SAS tem espaços em branco no nome do vhd   | Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido.    | Atualize a URL SAS sem espaços em branco                   | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| Falha ao copiar imagens – Erro de autorização da URL SAS             | Falha: Copiando Imagens. Não é possível baixar o blob devido a um erro de autorização | Gere novamente a URL SAS                                    | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |

## <a name="marketplace-form"></a>Formulário do Marketplace
O formulário do marketplace em uma oferta de máquina virtual solicita campos que serão exibidos no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [Portal do Azure](https://portal.azure.com/). Aqui estão alguns detalhes para alguns dos campos.

#### <a name="preview-subscription-ids"></a>Visualizar IDs de assinatura

Insira aqui uma lista de IDs de assinatura do Azure aos quais você quer ter acesso para a oferta publicada. Essas assinaturas da lista branca permitirão que você teste a oferta visualizada antes de ativá-la. O portal de parceiros permite até 100 assinaturas na lista branca.

#### <a name="suggested-categories"></a>Categorias sugeridas

Selecione até 5 categorias na lista fornecida com as quais sua oferta pode ser melhor associada. As categorias selecionadas serão usadas para mapear a oferta até as categorias de produto disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com) e [Portal do Azure](https://portal.azure.com/).

Aqui estão alguns dos locais em que os dados fornecidos neste formulário aparecem.

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10][10]

![publishvm11][11]

![publishvm15][15]

##### <a name="azure-portal"></a>Portal do Azure

![publishvm12][12]

![publishvm13][13]

##### <a name="logo-guidelines"></a>Diretrizes de logotipo

Todos os logotipos carregados no Portal do Cloud Partner devem seguir as diretrizes abaixo:

-   O design do Azure tem uma paleta de cores simples. Mantenha um baixo número de cores primárias e secundárias no seu logotipo.

-   As cores do tema do portal do Azure são branco e preto. Portanto, evite usar essas cores como a cor de fundo dos seus logotipos. Use uma cor que destaque seus logotipos no portal do Azure. É recomendável usar cores primárias simples. **Se estiver usando um plano de fundo transparente, certifique-se de que os logotipos/o texto não sejam preto, branco ou azul.**

-   Não use uma tela de fundo gradiente no logotipo.

-   Evite colocar texto, até mesmo o nome ou marca da sua empresa, no logotipo. A aparência do seu logotipo deve ser “simples” e deve evitar usar gradientes.

-   O logotipo não deve ser estendido.

##### <a name="hero-logo"></a>Logotipo Hero

O logotipo Hero é opcional. O editor pode optar por não carregar um logotipo Hero. No entanto, uma vez carregado, o ícone Hero não pode ser excluído. Nessa ocasião, o parceiro deve seguir as diretrizes do Azure Marketplace para ícones Hero.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Diretrizes adicionais para o ícone do logotipo Hero

-   O nome de exibição do editor, o título do plano e o resumo longo da oferta são exibidas em uma fonte branca. Portanto, você deve evitar cores claras no plano de fundo do ícone Hero. Planos de fundo pretos, brancos e transparentes não são permitidos para ícones Hero.

-   O nome de exibição do editor, o título do plano, o resumo longo da oferta e o botão de criação são inseridos programaticamente no logotipo Hero depois que a oferta é listada. Portanto, você não deve inserir texto ao projetar o logotipo Hero. Deixe espaço vazio à direita porque o texto (ou seja, o nome de exibição do editor, o título do plano e o resumo longo da oferta) será incluído programaticamente por nós nessa área. O espaço vazio para o texto deve ser de 415x100 à direita (e ele é deslocado 370px à esquerda).

![publishvm14][14]

#### <a name="lead-management"></a>Gerenciamento de leads

Para preencher as configurações de gerenciamento de leads da oferta, siga as instruções [aqui](./cloud-partner-portal-get-customer-leads.md#how-to-connect-your-crm-system-with-the-cloud-partner-portal)

## <a name="support-form"></a>Formulário de suporte
O seguinte formulário a ser preenchido é o formulário de suporte. Esse formulário solicita contatos de suporte da sua empresa, como informações de contato de engenharia e informações de contato de suporte ao cliente. 

## <a name="how-to-publish-an-offer"></a>Como publicar uma oferta
Agora que você tem o rascunho da oferta, a próxima etapa seria publicar a oferta no Azure Marketplace. Siga as instruções para [ativar a oferta no Azure Marketplace](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) 

[1]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png
[2]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png
[3]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm3.png
[4]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png
[5]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png
[6]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png
[7]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png
[8]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png
[9]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png
[10]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png
[11]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png
[12]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png
[13]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png
[14]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png
[15]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png
