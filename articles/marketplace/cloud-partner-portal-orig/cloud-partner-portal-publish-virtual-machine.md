---
title: Oferta de máquina virtual do autor | Microsoft Docs
description: Publicar uma máquina virtual no Azure Marketplace.
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: d77dad52e75253de02fd079d791861356c4c5e1f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247068"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Publicar uma máquina virtual no Azure Marketplace

Este artigo fornece as etapas necessárias para publicar uma oferta de máquina virtual no Azure Marketplace.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos técnicos e não técnicos aplicam a publicação de uma máquina virtual no Azure Marketplace

### <a name="technical"></a>Técnicos

-   [Pré-requisitos técnicos para criação de uma imagem de máquina virtual para o Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Crie e carregue um VHD do Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Criar e testar uma VM do Linux por meio de uma imagem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Crie e carregue um VHD do Windows ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Criar e testar uma VM do Windows por meio de uma imagem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Como solucionar problemas comuns encontrados durante a criação do VHD](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

-   [Recomendações de segurança para imagens do Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>Não técnico (requisitos de negócios)

 -   Sua empresa (ou sua subsidiária) está localizada em um país de origem de venda com suporte no Azure Marketplace

-   O produto deve ser licenciado de forma que seja compatível com os modelos de cobrança com suporte no Azure Marketplace

-   Você é responsável por disponibilizar o suporte técnico a clientes de modo comercialmente razoável. Esse suporte pode ser gratuito, pago, ou pelo suporte da comunidade.

-   Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.

-   Forneça o conteúdo que atenda aos critérios da oferta a serem listados no Azure Marketplace e no Portal de Gerenciamento do Azure.

-   Concorde com os termos das Políticas de Participação do Azure Marketplace e com o Contrato do Editor.

-   Você concorda em cumprir com os da [termos de uso](https://azure.microsoft.com/support/legal/website-terms-of-use/) , [Política de Privacidade da Microsoft](https://www.microsoft.com/privacystatement/default.aspx), e [contrato de programa de certificação do Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

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

1.  Entre no [portal do Cloud Partner](http://cloudpartner.azure.com/).

2.  Na barra de navegação à esquerda, selecione **+ Nova oferta**e, em seguida, selecione **Máquinas Virtuais**.

    ![Nova oferta para máquinas virtuais](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  Em **Nova oferta**, selecione **Editor**.

![Editor de nova oferta](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  Em **Editor**, você fornecerá informações nas seguintes exibições:
    - Configurações da oferta
    - SKUs
    - Marketplace
    - Suporte: cada modo de exibição contém um conjunto de campos para você preencher. Os campos necessários são indicados com um asterisco vermelho (\*)

## <a name="to-configure-offer-settings"></a>Para definir as Configurações da Oferta

1. Configure os seguintes campos de **Identidade da Oferta** nas Configurações da oferta.

    **ID da oferta**

     Um identificador exclusivo para a oferta em um perfil de publicador. Essa ID está visível em URLs de produto, modelos do Azure Resource Manager e relatórios de cobrança. Você só pode usar caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e pode ter um máximo de 50 caracteres. Por exemplo, se um editor **contoso** publica uma oferta com a ID de oferta **exemplo-vm**, ele aparece no Azure marketplace como **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**  
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

## <a name="to-create-a-sku"></a>Para criar um SKU

1. Selecione **SKUs**. 
2. Selecione **Adicionar uma SKU** para inserir uma ID de SKU. Uma ID de SKU é um identificador exclusivo para a SKU dentro de uma oferta. Essa ID está visível em URLs de produto, modelos do Azure Resource Manager e relatórios de cobrança. A ID da SKU:
    - Só pode ter um máximo de 50 caracteres.
    - Só pode ser composta de caracteres alfanuméricos minúsculos ou traços (-).
    - A ID não pode terminar com um traço.

    ![Adicionar uma SKU](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>Configurar detalhes da SKU

Depois de você adicionar uma SKU, ele aparece na lista de SKUs na exibição de SKUs. Para ver os detalhes da SKU, selecione o nome dela. Você pode usar o modo de exibição de detalhes para adicionar as informações nos campos a seguir.

### <a name="hide-this-sku"></a>Ocultar esta SKU

Use essa configuração para gerenciar a visibilidade da SKU. Se "Ocultar esta SKU" estiver desativado, O SKU estará visível nO [Azure Marketplace](https://azuremarketplace.microsoft.com) e, no [portal do Azure](https://portal.azure.com/) aos clientes. Oculte a SKU se só a quiser disponível por meio de modelos da solução e não para compra individual.

### <a name="cloud-availability"></a>Disponibilidade na nuvem

Este campo permite que você defina a disponibilidade da SKU nas diferentes nuvens do Azure.

**Azure público**

Esta SKU pode ser implantada para os clientes em todas as regiões públicas do Azure que tem a integração do Marketplace.

**Nuvem do Azure Governamental**

Esta SKU pode ser implantada na nuvem do Azure Governamental. Antes de publicar no [Azure Governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), recomendamos que os fornecedores testem e validem se a solução funciona conforme o esperado. Para teste e o estágio [solicite uma conta de avaliação](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). 

>[!Note]
>O Microsoft Azure Governamental é uma nuvem de comunidade do governo, com acesso controlado para clientes federais, estaduais, locais ou tribais dos EUA e parceiros qualificados para atender a essas entidades.

### <a name="countryregion-availability"></a>Disponibilidade para país/região

Este campo identifica as regiões em que a SKU estará disponível para compra. Você precisa considerar com atenção onde você pretende disponibilizar suas SKUs. Alguns países são classificados como "País de Imposto Remetido da Microsoft".

-   No "País de Imposto Remetido da Microsoft", a Microsoft coleta os impostos dos clientes e paga (envia) taxas ao governo.

-   Em outros países, os parceiros são responsáveis por coletar os impostos dos clientes e pagá-los ao governo. Se você optar por vender nesses países, deverá ter a funcionalidade para calcular e pagar neles os impostos.

![Selecione a disponibilidade por país/região](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>Preços

Atualmente, há suporte para dois modelos de preços.

#### <a name="bring-your-own-license-byol"></a>BYOL (Traga sua Própria Licença)

Você gerencia o licenciamento do software em execução na VM. A Microsoft cobrará somente os custos de infraestrutura.

#### <a name="usage-based-monthly-billed-sku"></a>SKU cobrado mensalmente com base no uso

Os clientes são cobrados por hora com base nas taxas definidas pelos fornecedores sobre tamanhos de VM. No caso do modelo de **cobrança por hora** dos SKUs, o preço total será a soma do custo do software cobrado pelo editor e do custo da infraestrutura cobrado pela Microsoft. Esse custo total será exibido para o cliente como um preço por hora e mensal quando ele considerar a compra. A cobrança nesse caso será mensal.

Dentro do modelo com base no uso, há configurações adicionais necessárias.

**Teste Gratuito**

Especifique se quer oferecer uma avaliação gratuita aos clientes.
Aqui o cliente não paga pelo custo do software pelos primeiros 30/90 dias (dependendo da seleção) depois de implantar a VM. Depois de decorrido o período de avaliação gratuita, é cobrado por hora com base nas taxas definidas pelos editores no modelo por hora.

**Por preços de núcleo**

Você pode definir preços por núcleo para a SKU. Para isso, basta inserir um preço básico para um único núcleo e nós calculamos automaticamente os preços para o restante dos núcleos. Insira os preços em USD no portal e calcularemos automaticamente os preços para outras regiões. Verifique os preços em outras regiões usando **Exportar Dados de Preços**

![Por preços de núcleo](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**Preços discretos**

Defina o preço para cada conjuntos de núcleos individualmente se quiser tabelar o preço de cada núcleo separadamente.

![Preços discretos](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**Preços de importação-exportação**

Você tem a flexibilidade de exportar preços configurados pelo portal para fazer alterações pela interface do excel. Isso também permite verificar preços por região e preços em moedas locais.
Se você clicar em **Preços de Exportação** um arquivo do Excel será baixado com detalhes de preços preenchidos. Você poderá editá-los dentro do excel e, em seguida, usar os **Preços de Importação** para importar as alterações feitas.
Os preços importados refletirão também no portal.

Nesse excel de preços, os preços das diferentes regiões estão listados na moeda local. A taxa de câmbio que usamos é atualizada diariamente.

![Preços de importação-exportação com exemplos de taxa de câmbio](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   Os preços não podem ser alterados depois que uma oferta entra no ar. Ainda será possível adicionar/remover regiões com suporte.
>-   Esse preço será cobrado para o usuário além do preço da máquina virtual do  [Azure\'](https://aka.ms/vmpricingdetails).
>-   Os preços são definidos para todas as regiões em moeda local, usando as taxas de moeda disponíveis no momento da definição de preços.
>-   Para definir ou exibir o preço de cada região individualmente, exporte a planilha de preços e importe com preços personalizados.

## <a name="vm-images"></a>Imagens da VM

A próxima seção a ser concluída será a seção Imagens da VM. Antes de ir para essa seção, você precisa ter pronto o VHD que quer publicar. Aqui estão alguns links que ajudam você a criar o VHD:

-   [Pré-requisitos técnicos para criação de uma imagem de máquina virtual para o Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Criando e carregando um VHD do Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Criar e testar uma VM do Linux por meio de uma imagem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Criar e carregar um VHD do Windows ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Criar e testar uma VM do Windows por meio de uma imagem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Como solucionar problemas comuns encontrados durante a criação do VHD](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

Quando o VHD estiver pronto, você pode começar a preencher esta seção.
Aqui estão alguns detalhes para alguns dos campos.

### <a name="recommended-vm-sizes"></a>Tamanhos de VM recomendados

Selecione até seis tamanhos de máquina virtual recomendados. Essas são recomendações exibidas para o cliente na folha Tipo de Preço e no Azure Marketplace no Portal do Azure quando eles decidem comprar e implantar sua imagem. **Essas são apenas recomendações. O cliente é capaz de selecionar qualquer tamanho de VM que acomode os discos especificados em sua imagem.**  A captura de tela a seguir mostra os tamanhos de VM recomendados que um cliente verá no portal do Azure.


![Tamanhos de VM recomendados](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>Abrir portas

Especifique as portas que você quer abrir e disponibilizar. Essas portas são abertas durante a implantação das VMs.

### <a name="adding-vm-images"></a>Adicionar imagens de VM

A próxima etapa é adicionar uma imagem de VM para sua SKU. Você pode adicionar até 8 versões de disco por SKU. Somente o número mais alto de versão do disco para determinada SKU aparecerá no Azure Marketplace. Outras poderão ser vistas por meio de APIs.

Em **versão do disco**, selecione **+ Nova versão**. Isso mostra os campos a seguir que você precisa preencher.

#### <a name="vm-image-version"></a>Versão de imagem da VM

A versão de imagem da VM precisa seguir o formato de [versão semântica](http://semver.org/). Versões devem ser no formato X.Y.Z, onde X, Y e Z são inteiros. Imagens em SKUs diferentes podem ter diferentes versões principais e secundárias. Versões em uma SKU só devem ser alterações incrementais, aumentando a versão de patch (Z de X.Y.Z).

#### <a name="os-vhd-url"></a>URL DE VHD DO SO

Insira o [URI de assinatura de acesso compartilhado](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) criado para o VHD do sistema operacional.

Se houver discos de dados associados a essa SKU, você pode optar por adicionar esses discos, selecionando o link **+ Novo disco de dados**. Essa ação exibe campos adicionais para que você preencha.

#### <a name="lun-vhd-url"></a>URL DO VHD DE LUN

Insira o [URI de assinatura de acesso compartilhado](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) para seu disco de dados.

#### <a name="lun-number"></a>Número de LUN

Atribua um número a esse LUN. Esse número será reservado para esse disco de dados nessa SKU.

>[!Note]
>Depois de publicar uma SKU com determinada versão de VM e discos de dados, elas ficam bloqueadas e não podem ser alteradas. Para versões adicionais de VM acrescentadas à SKU, o número de discos de dados aos quais ela precisa dar suporte não pode ser alterado.

#### <a name="common-sas-url-issues--fixes"></a>Problemas comuns e correções da URL de SAS

| Problema                                                                 | Mensagem                                                                           | Correção                                                           |  Vincular à documentação                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| Falha ao copiar imagens – "?" não foi encontrado na URL SAS                | Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido.       | Atualizar a URL SAS usando as ferramentas recomendadas                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Falha ao copiar imagens – Os parâmetros “st” e “se” não existem na URL SAS   | Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido.        | Atualizar a URL SAS com datas de Início e Término             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Falha ao copiar as imagens – “sp=rl” não existe URL SAS                    | Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido         | Atualizar a URL SAS com permissões definidas como “Leitura” e “Listagem”     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Falha ao copiar imagens – A URL SAS tem espaços em branco no nome do vhd     | Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido.        | Atualize a URL SAS sem espaços em branco                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Falha ao copiar imagens – Erro de autorização da URL SAS               | Falha: Copiando Imagens. Não é possível baixar o blob devido a um erro de autorização     | Gere novamente a URL SAS                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>Para configurar o Marketplace

Use o modo de exibição do Marketplace para configurar os campos que são exibidos para a oferta no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal do Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Visualizar IDs de assinatura

A lista de IDs de assinatura do Azure que você quer que tenham acesso à oferta quando ela for publicada. Essas assinaturas da lista de permissões permitirão que você teste a oferta em versão prévia antes de ativá-la. O portal de parceiro permite até 100 assinaturas na lista de permissões.

### <a name="suggested-categories"></a>Categorias sugeridas

Selecione até 5 categorias na lista fornecida com as quais sua oferta pode ser melhor associada. As categorias selecionadas serão usadas para mapear a oferta até as categorias de produto disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com) e [Portal do Azure](https://portal.azure.com/).

Os exemplos a seguir mostram as informações do Marketplace no Azure Marketplace e no portal do Azure.

**Azure Marketplace**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Portal do Azure**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)



![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


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

![Exemplo de logotipo hero](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>Gerenciamento de leads

Para configurar as configurações de gerenciamento de clientes potenciais da oferta, siga [estas instruções](./cloud-partner-portal-get-customer-leads.md).

## <a name="to-configure-support"></a>Para configurar o Suporte

Use a exibição de Suporte para fornecer as seguintes informações:

- Contatos de suporte de sua empresa, tais como os de engenharia.
- Contatos do atendimento ao cliente.

## <a name="to-publish-the-offer"></a>Para publicar a oferta

A etapa final é publicar a oferta. Siga [estas instruções para entrar no ar com sua oferta](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md).
