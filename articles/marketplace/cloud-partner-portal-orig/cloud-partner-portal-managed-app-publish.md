---
title: Publicar um aplicativo gerenciado pelo Azure no Azure Marketplace
description: Publicar um aplicativo gerenciado pelo Azure no Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6e5bcd6d9923b4051d44d51ff1a2534bc5e02f41
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445653"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>Publicar um aplicativo gerenciado pelo Azure no Azure Marketplace 
========================================================

Este artigo lista as diversas etapas envolvidas na publicação de uma oferta de aplicativo gerenciado no Azure Marketplace.

<a name="pre-requisites-for-publishing-a-managed-application"></a>Pré-requisitos para publicar um aplicativo gerenciado 
---------------------------------------------------

Pré-requisitos para listagem no Azure Marketplace

1.  Técnicos

    -   [Criar modelos do Gerenciador de Recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Modelos de Início Rápido do Azure:

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   Criar uma definição de interface do usuário

        -   [Criar o arquivo de definição de interface do usuário](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  Não técnico (requisitos de negócios)

    -   Sua empresa (ou sua subsidiária) deve estar localizada em um país de origem de venda ao qual o Azure Marketplace dá suporte

    -   O produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Azure Marketplace

    -   Você é responsável por fornecer suporte técnico a seus clientes: gratuito, pago ou por meio do suporte da comunidade.

    -   Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.

    -   Você deve fornecer conteúdo que atenda aos critérios da oferta a serem listados no Azure Marketplace e no Portal de gerenciamento do Azure

    -   Você deve concordar com os termos das Políticas de participação do Azure Marketplace e o Contrato do fornecedor

    -   Você precisa concordar em cumprir os Termos de uso, a Política de Privacidade da Microsoft e o Contrato do Programa de Certificação do Microsoft Azure.

<a name="how-to-create-a-new-azure-application-offer"></a>Como criar uma nova oferta de Aplicativo Azure 
-------------------------------------------

Depois de atender aos pré-requisitos, você está pronto para começar a criar sua oferta de aplicativo gerenciado. Antes de começar, uma visão geral de uma oferta e uma SKU

**Oferta**

Uma oferta de Aplicativo Azure corresponde a uma classe de oferta de produto de um editor. Se você tem uma nova solução/aplicativo a ser publicado no Azure Marketplace, uma nova oferta é a melhor opção. Uma oferta é uma coleção de SKUs. Cada oferta aparece como sua própria entidade no Azure Marketplace.

**SKU**

Um SKU é uma oferta com a menor unidade de compra adquirível. Enquanto estão dentro da mesma classe de produto (oferta), os SKUs permitem diferenciar entre os diferentes recursos compatíveis, quer se trate de oferta gerenciada ou não gerenciada ou de modelos de cobrança.

Adicione vários SKUs se quiser dar suporte a diferentes modelos de cobrança, como BYOL (Traga Sua Própria Licença), PAYG (pago conforme o uso), etc. 

Adicione vários SKUs quando cada SKU der suporte a um conjunto de recursos diferente e cada um desses conjuntos tiver preços diferentes.

Um SKU aparece sob a oferta pai no Azure Marketplace, embora apareça como sua própria entidade comprável no portal do Azure.

1.  Entre no [Portal de Parceiros de Nuvem](http://cloudpartner.azure.com).

2.  Na barra de navegação à esquerda, clique em \"+ Nova oferta\" e selecione \"Aplicativos do Azure\".

    ![Nova oferta](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Uma exibição de \"Editor\" de nova oferta aparece e você pode começar a criar.

4.  Os \"formulários\" que precisam ser preenchidos estão visíveis à esquerda no modo de exibição \"Editor\". Cada \"formulário\" consiste em um conjunto de campos que devem ser preenchidos. Os campos obrigatórios estão marcados com um asterisco vermelho (\*).

    > Há quatro formas principais de criar um Aplicativo Gerenciado

    -   Configurações da oferta

    -   SKUs

    -   Marketplace

    -   Suporte

<a name="how-to-fill-out-the-offer-settings-form"></a>Como preencher o formulário de Configuração da oferta 
---------------------------------------

O formulário de configurações da oferta é um formulário básico para especificar as configurações da oferta.
Os diferentes campos são descritos abaixo.

**ID da oferta**

Um identificador exclusivo para a oferta em um perfil de editor.
Isso estará visível em URLs de produto, modelos do Resource Manager e relatórios de cobrança. Somente caracteres alfanuméricos minúsculos ou traços (-) são permitidos. Ele não pode terminar com um traço e pode ter um máximo de 50 caracteres. Esse campo é bloqueado quando uma oferta é ativada.

**ID do editor**

Este menu suspenso permite que você escolha o perfil de fornecedor em que deseja publicar essa oferta. Esse campo é bloqueado quando uma oferta é ativada.

**Nome**

O nome de exibição da oferta. O nome que aparecerá no Azure Marketplace e no portal do Azure. Ele pode ter um máximo de 50 caracteres. A orientação aqui é incluir um nome de marca reconhecível para o seu produto. Não inclua o nome da sua empresa aqui, a menos que seja a maneira como ele é comercializado. Se você estiver comercializando essa oferta em seu próprio site, certifique-se de que o nome seja exatamente como aparece no site.

Clique em \"Salvar\" para salvar o progresso. A próxima etapa seria adicionar SKUs para a oferta.

<a name="how-to-create-skus"></a>Como criar SKUs 
------------------

Clique no formulário \"SKUs\". Aqui você pode ver uma opção para \"Adicionar um SKU\" e clicar nela permite que você insira uma \"ID de SKU\".

![SKUs de nova oferta](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

\"ID da SKU\" é um identificador exclusivo para a SKU dentro de uma oferta. Essa ID será visível em URLs de produto, relatórios de cobrança e modelos ARM. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-).
Ela não pode terminar com um traço e pode ter um máximo de 50 caracteres. Esse campo é bloqueado quando uma oferta é ativada. Pode existir várias SKUs em uma oferta. É necessário uma SKU para cada imagem que você planeja publicar.

Após a adição de um SKU, ele aparecerá na lista de SKUs dentro do formulário \"SKUs\". Clique no nome da SKU para ver os detalhes dessa SKU específica. Aqui estão alguns detalhes para alguns dos campos.

Depois de clicar em \"Novo SKU\", é necessário preencher o formulário a seguir:

![Nova oferta – novo SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Como preencher a seção de detalhes do SKU 

**Título** – forneça um título para esse SKU. É isso que será exibido na galeria para esse item.

**Resumo** – fornece um breve resumo para esse SKU. Esse texto aparecerá imediatamente sob o título.

**Descrição** – fornece uma descrição detalhada sobre o SKU.

**Tipo de SKU** – os valores permitidos são \"Aplicativo gerenciado\" e \"Modelos de solução\". Neste caso, você selecionará \"Aplicativo gerenciado\".

### <a name="how-to-fill-package-details-section"></a>Como preencher a seção Detalhes do pacote 

A seção de pacote tem os seguintes campos que precisam ser preenchidos

![Nova oferta – novo pacote de SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**Versão atual** – forneça uma versão para o pacote que você fará upload.
Ela deve estar no formato:

**Arquivo de pacote** – o pacote contém os seguintes arquivos que estão compactados em um arquivo zip.

applianceMainTemplate.json – o arquivo do modelo de implantação que é usado para implantar a solução/aplicativo e criar os recursos definidos nele. Você pode encontrar mais detalhes sobre como criar arquivos de modelo de implantação aqui – <https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json – esse arquivo é usado pelo portal em azure.com para gerar a interface do usuário para o provisionamento dessa solução/aplicativo. Você pode encontrar mais detalhes sobre como criar esse arquivo aqui – <https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>

mainTemplate.json – o arquivo de modelo que contém somente o recurso Microsoft.Solution/appliances. As principais propriedades desse recurso a serem consideradas são as seguintes:

-   \"kind\" – o valor deve ser \"Marketplace\" no caso de cenário de aplicativo gerenciado pelo Marketplace
-   \"ManagedResourceGroupId\": o grupo de recursos na assinatura do cliente em que todos os recursos definidos no applianceMainTemplate.json serão implantados.
-   \"PublisherPackageId\": a cadeia de caracteres que identifica exclusivamente o pacote. 

O valor deve ser construído da maneira a seguir – é uma concatenação de \[publisherId\].\[OfferId\]-preview\[SKUID\].\[PackageVersion\].
Cada um desses valores pode ser obtida conforme mostrado abaixo.

Esse pacote deve conter quaisquer outros modelos ou scripts aninhados necessários para provisionar com êxito esse aplicativo. Os arquivos mainTemplate.json, applianceMainTemplate.json e applianceCreateUIDefinition.json devem estar presentes na pasta raiz.

**Autorizações** – essa propriedade define quem obtém acesso e o nível de acesso aos recursos em assinaturas de clientes. Isso permite que o publicador gerencie o aplicativo em nome do cliente.

-   PrincipalId – o identificador do Azure Active Directory de um usuário, grupo de usuários ou aplicativo que recebeu determinadas permissões (conforme descrito pela Definição de Função) nos recursos na assinatura do cliente.

-   Definição de função – é uma lista de todas as funções RBAC internas fornecidas pelo Azure AD. Você pode selecionar a função mais apropriada, o que permitirá que você gerencie os recursos em nome do cliente.

Várias autorizações podem ser adicionadas. No entanto, é recomendável criar um grupo de usuários do Active Directory e especificar sua ID em \"PrincipalId\..  Isso permitirá a adição de mais usuários ao grupo de usuários sem necessidade de atualizar a SKU.

Você pode encontrar mais detalhes sobre o RBAC aqui – <https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>Formulário do Marketplace
----------------

O formulário do marketplace em uma oferta de Aplicativo Azure solicita campos que serão exibidos no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [Portal do Azure](https://portal.azure.com/). Aqui estão alguns detalhes para alguns dos campos.

#### <a name="preview-subscription-ids"></a>IDs de assinatura para versão prévia

Insira aqui uma lista de IDs de Assinatura do Azure que você gostaria que tivessem acesso à oferta quando ela for publicada. Essas assinaturas da lista de permissões permitirão que você teste a oferta visualizada antes de ativá-la. O portal de parceiro permite até 100 assinaturas na lista de permissões.

#### <a name="suggested-categories"></a>Categorias sugeridas

Selecione até cinco categorias na lista fornecida às quais a sua oferta melhor pode ser associada. As categorias selecionadas serão usadas para mapear a oferta até as categorias de produto disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com) e [Portal do Azure](https://portal.azure.com/).

Aqui estão alguns dos locais em que os dados fornecidos neste formulário aparecem.

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>Portal em azure.com

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>Diretrizes de logotipo

Todos os logotipos carregados no Portal do Cloud Partner devem seguir as diretrizes abaixo:

-   O design do Azure tem uma paleta de cores simples. Mantenha um baixo número de cores primárias e secundárias no seu logotipo.

-   As cores do tema do portal em azure.com são branca e preta. Evite usar essas cores como a cor da tela de fundo dos seus logotipos. Use uma cor que destaque seus logotipos no portal em azure.com.
    É recomendável usar cores primárias simples. **Se você estiver usando uma tela de fundo transparente, verifique se os logotipos e o texto não são de cor preta, branca ou azul.**

-   Não use uma tela de fundo gradiente no logotipo.

-   Evite colocar texto, até mesmo o nome ou marca da sua empresa, no logotipo.
    A aparência do seu logotipo deve ser \'simples\' e deve evitar usar gradientes.

-   Evite esticar o logotipo.

##### <a name="hero-logo"></a>Logotipo Hero

O logotipo Hero é opcional. O editor pode optar por não carregar um logotipo Hero. No entanto, uma vez carregado, o ícone Hero não pode ser excluído. Nesse caso, o parceiro deve seguir as diretrizes do Azure Marketplace para ícones Hero.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Diretrizes adicionais para o ícone do logotipo Hero

-   O nome de exibição do editor, o título do plano e o resumo longo da oferta são exibidas em uma fonte branca. Evite usar cores claras na tela de fundo do ícone Hero. Telas de fundo pretas, brancas e transparentes não são permitidas para ícones Hero.

-   Quando a oferta é listada, o nome de exibição do editor, o título do plano, o resumo longo da oferta e o botão de criação são inseridos programaticamente no logotipo Hero. Você não precisa inserir nenhum texto ao projetar o logotipo Hero. Deixe a espaços vazios à direita para o nome de exibição do editor, o título do plano, o resumo longo da oferta, etc. Eles são incluídos programaticamente.
    Os espaços vazios para o texto devem ser de 415x100 à direita, com deslocamento de 370 px da esquerda.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>Formulário de suporte
------------

O seguinte formulário a ser preenchido é o formulário de suporte. Este formulário solicita contatos de suporte de sua empresa.  Alguns exemplos são suas informações de contato de engenharia e informações de contato do atendimento ao cliente.

<a name="how-to-publish-an-offer"></a>Como publicar uma oferta
-----------------------

Agora que você tem o rascunho da oferta, a próxima etapa é publicar a oferta no Azure Marketplace. Clique no botão \"Publicar\" para iniciar o processo de ativar sua oferta.
