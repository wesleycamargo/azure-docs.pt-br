---
title: Guia de publicação técnica de aplicativo SaaS | Microsoft Docs
description: Explica como publicar ofertas de aplicativos SaaS no marketplace apropriado.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d73040f11549741643d96913c42df49594b8d41
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804990"
---
<a name="saas-application-technical-publishing-guide"></a>Guia de publicação técnica de aplicativo SaaS
===========================================

Seja bem-vindo ao guia de publicação técnica de aplicativos SaaS! Este guia foi desenvolvido para ajudar candidatos e publicadores existentes a listar aplicativos e serviços no AppSource ou no Azure Marketplace utilizando a oferta de Aplicativos SaaS.

Para obter uma visão geral de todas as outras ofertas do Marketplace, confira o [Guia do Publicador do Marketplace](https://aka.ms/sellerguide).


<a name="what-are-pre-requisites-for-publishing-a-saas-app"></a>Quais são os pré-requisitos para a publicação de um aplicativo SaaS?
-------------------------------------------------

O portal de publicação fornece acesso baseado em função ao portal, permitindo que várias pessoas colaborem para a publicação de uma oferta. Para obter mais informações, confira [Gerenciar usuários](./cloud-partner-portal-manage-users.md). 

Antes de publicar uma oferta em nome de uma conta de publicador, uma das pessoas com função de *Proprietário* precisa concordar em cumprir os [Termos de uso](https://azure.microsoft.com/support/legal/website-terms-of-use/), a [Declaração de Privacidade da Microsoft](http://www.microsoft.com/privacystatement/default.aspx) e o [Contrato do Programa Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


<a name="creating-an-offer"></a>Criando uma oferta
-----------------

Esta seção descreve o processo de como criar uma nova oferta de aplicativo SaaS.

![Visão geral da oferta de SaaS](media/cpp-creating-saas-offers/saas-offer-overview.png)

A oferta de aplicativo SaaS consiste em cinco seções, descritas na tabela a seguir:

| **Seção de ofertas**  | **Descrição**                                                                                                     |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Configurações da oferta     | Ajuda a definir um nome e uma ID exclusivos para o aplicativo SaaS                                                                         |
| Informações técnicas     | Ajuda a configurar o tipo de solução de SaaS e fornece os detalhes de conexão para seu aplicativo                            |
| Test drive         | Seção opcional que permite que você defina um serviço que permitirá que os clientes testem sua oferta antes de comprá-la. |
| Detalhes da vitrine | Contém as seções de marketing, jurídica, gestão de cliente potencial e listagem:   <br/> - A seção de Marketing permite que você insira a descrição e os logotipos necessários para a oferta ser exibida corretamente no portal do usuário do marketplace.  <br/> - A Gestão de Cliente Potencial permite que você defina um local para onde redirecionar os clientes potenciais usuários finais gerados no portal do usuário final do Azure Marketplace.  <br/> - A seção Jurídica permite que você insira a documentação legal de Política de Privacidade e Termos de Uso.  |
| Contato            | Permite que você insira as informações de contato de suporte da oferta.                                                                  |
|  |  |


### <a name="creating-a-new-offer"></a>Criando uma nova oferta

Depois de entrar no Portal do Cloud Partner, selecione o item **Nova oferta** na barra de menus à esquerda, que exibe um menu de ofertas disponíveis. A imagem a seguir mostra um exemplo dessas ofertas:

![Nova oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer.png)

Selecione a oferta para a qual foi aprovado na lista; será aberto um formulário de nova oferta.

![Formulário de nova oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer-2.png)

A tabela a seguir descreve os campos da oferta:

| **Campos da oferta** | **Descrição**                                                                                            |
|------------------|----------------------------------------------------------------------------------------------------------- |
| ID da oferta         | Um identificador exclusivo para a oferta em um perfil de editor. Essa ID será visível em URLs de produto e relatórios de cobrança. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e pode ter um máximo de 50 caracteres. Observe que esse campo é bloqueado quando uma oferta entra no ar. Por exemplo, se um publicador, Contoso, publicar uma oferta com a ID de oferta sample-vm, ela será exibida no Azure Marketplace como: [https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview](https://azuremarketplace.microsoft.com/) |
| ID do fornecedor     | A ID do publicador é seu identificador exclusivo no Marketplace. Todas as suas ofertas devem estar anexadas à ID do editor. A ID do editor não pode ser modificada depois que a oferta for salva.                                                                                       |
| NOME             | Nome de exibição da oferta. É o nome que aparecerá no Azure Marketplace e no Portal do Azure. Ele pode ter um máximo de 50 caracteres. A orientação aqui é incluir um nome de marca reconhecível para o seu produto. Não inclua o nome da sua empresa aqui, a menos que seja a maneira como ela é comercializada. Se você estiver comercializando essa oferta em seu próprio site, certifique-se de que o nome seja exatamente como aparece no site.             |
|  |  |

Clique em **Salvar** para salvar o progresso. A próxima seção descreve a adição de planos à sua oferta.


### <a name="technical-information"></a>Informações técnicas

A seção de informações técnicas permite que você insira estas informações:

![Informações técnicas sobre a oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer-technical-info.png)

A decisão mais importante é entre habilitar uma listagem de SaaS ou o comércio. Se for uma listagem de SaaS, você precisará selecionar entre:

-   Gratuito – forneça uma URL do aplicativo SaaS em que os clientes possam obter seu aplicativo.
-   Avaliação gratuita – forneça uma URL do aplicativo SaaS em que os clientes possam executar a versão de avaliação antes de comprarem a oferta.
-   Entre em contato comigo – relevante apenas se você tiver um sistema de gestão de cliente potencial conectado; essa opção permite que os clientes peçam para ser contatados e um cliente potencial será compartilhado com você.

Se você tiver um aplicativo SaaS que está no Azure Marketplace e desejar habilitar o comércio por meio de transações da Microsoft, selecione **Vender por meio do Azure**.  
Para obter mais informações sobre como conectar seu aplicativo SaaS, confira [SaaS – Vender por meio do Azure](./cloud-partner-portal-saas-offer-subscriptions.md).


### <a name="test-drive"></a>Test drive

Criar uma experiência de avaliação gratuita para seus clientes é uma melhor prática para garantir que eles possam comprar com confiança. Dentre as opções de avaliação gratuita disponíveis, o Test Drive é a mais eficiente para gerar clientes potenciais de alta qualidade e aumentar a conversão deles.

Fornece aos clientes uma avaliação gratuita prática e autoguiada dos principais recursos e benefícios do seu produto, demonstrados em um cenário de implementação real.

![Test Drive da oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer-test-drive.png)


#### <a name="how-a-test-drive-works"></a>Como funciona um Test Drive

Um cliente potencial pesquisa e descobre seu aplicativo no Marketplace. O cliente entra e concorde com os termos de uso. Neste ponto, o cliente recebe o ambiente pré-configurado para testar durante um número fixo de horas, enquanto você recebe um cliente potencial altamente qualificado para acompanhar.

![Test Drive da oferta de SaaS 2](media/cpp-creating-saas-offers/saas-new-offer-test-drive-2.png)

Independentemente do grau de complexidade do seu aplicativo, o Test Drive da Microsoft ajudará a dar vida ao seu produto para o cliente. Há três tipos diferentes de Test Drives disponíveis, cada um com base no tipo de produto, cenário e marketplace que você estiver usando.

-   **Azure Resource Manager** – um Test Drive do ARM do Azure é um modelo de implantação que contém todos os recursos do Azure que compõem uma solução sendo construída pelo publicador. Os produtos adequados para esse tipo de Test Drive são aqueles que usam recursos somente do Azure.
-   **Aplicativo lógico** – um Test Drive de aplicativo lógico é um modelo de implantação que serve para abranger todas as arquiteturas da solução complexas. Todos os aplicativos ou produtos personalizados do Dynamics devem usar esse tipo de Test Drive.
-   **Power BI** – um Test Drive do Power BI consiste em um link incorporado a um painel personalizado. Qualquer produto que deseja demonstrar um objeto visual interativo do Power BI deve usar esse tipo de Test Drive. Basta fazer o upload da URL interna do Power BI.

As principais etapas de publicação para adicionar um Test Drive são:

1.  Definir o cenário do Test Drive
2.  Compilar e/ou modificar o modelo do Resource Manager
3.  Criar o manual passo a passo do Test Drive
4.  Republicar a oferta

Para obter mais informações, confira [Test Drive](./what-is-test-drive.md).


### <a name="storefront-details"></a>Detalhes da vitrine

Os Aplicativos de SaaS precisam que as duas primeiras seções de um resumo e de uma descrição sejam fornecidas sobre seu aplicativo.

![Detalhes da vitrine da oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer-storefront-details.png)

A tabela a seguir descreve os detalhes da vitrine da oferta:

| **Campos da oferta**        | **Descrição**                                                                                          |
|-------------------------| ---------------------------------------------------------------------------------------------------------|
| Resumo da oferta           | Resumo da proposta de valor da sua oferta. Ele será exibido na página de pesquisa da sua oferta. Ele deve ter um máximo de 100 caracteres.   |
| Descrição da oferta       | Descrição que será exibida na página de detalhes do seu aplicativo. Máximo permitido é de 1300 caracteres *Observação* este campo aceita conteúdo HTML com marcações, como &ltp\>, &lth1\>, &lth2\>, &ltli\> etc., o que permite que você torne o conteúdo muito mais apresentável. A equipe do portal de publicação está trabalhando para adicionar um recurso para habilitar a visualização de uma versão prévia dos detalhes da vitrine a fim de tornar o conteúdo iterativamente mais apresentável – enquanto isso, você pode usar qualquer ferramenta HTML em tempo real online, como http://htmledit.squarefree.com, para ver como ficaria a descrição. |
| Indústrias              | Selecione os setores aos quais sua oferta está mais alinhada. Se o aplicativo estiver relacionado a vários setores, você poderá selecionar um máximo de dois. |
| Categorias sugeridas    | Selecione as categorias às quais sua oferta está mais alinhada. Você pode selecionar um máximo de três categorias.     |
| Versão do aplicativo     | Insira o número de versão do seu aplicativo                                                                |
| Pesquisar palavras-chave (máx. de 3) | Insira até três palavras-chave de pesquisa que os clientes podem usar para localizar seu aplicativo no site da vitrine do Marketplace. |
|  |  |


### <a name="marketing-artifacts"></a>Artefatos de marketing

A seção de artefatos de marketing permite que você defina os ativos de marketing do Azure Marketplace, como logotipos, vídeos, capturas de tela e documentos:

![Artefatos de marketing da oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer-marketing-artifacts.png)

A tabela a seguir descreve os campos de marketing:

| **Campos da oferta** | **Descrição**                                                                                                          |
|------------------| ------------------------------------------------------------------------------------------------------------------------ |
| Logotipos            | Se você tiver um aplicativo SaaS de **Vender por meio do Azure**, deverá fornecer todas as imagens de logotipo. Se tiver apenas uma listagem, 2 logotipos serão necessários. Todos os logotipos transferidos por upload para o Portal do Cloud Partner devem usar estas diretrizes:     <br/> - Mantenha um baixo número de cores primárias e secundárias no seu logotipo. O design do Azure tem uma paleta de cores simples.     <br/> - Evite usar preto ou branco como a cor da tela de fundo do seu logotipo. As cores do tema do Portal do Azure são preto e branco. EM vez disso, use uma cor que destacaria seus logotipos no Portal do Azure. É recomendável usar cores primárias simples. Se você estiver usando uma tela de fundo transparente, verifique se os logotipos e o texto não são preto, branco ou azul.     <br/> - Não use uma tela de fundo gradiente no logotipo.     <br/> - Evite colocar texto, até mesmo o nome ou marca da sua empresa, no logotipo. A aparência do seu logotipo deve ser “simples” e deve evitar o uso de gradientes.    <br/> - A imagem do logotipo não deve ser estendida.                   |
| vídeos           | Permite que você adicione links de vídeos da sua oferta. É possível usar links para vídeos do YouTube e/ou do Vimeo, que são mostrados juntamente com sua oferta aos clientes. Você também precisará inserir uma imagem em miniatura do vídeo, com uma imagem png de 1280x720 pixels. É possível ter um máximo de quatro vídeos por oferta. |
| Documentos        | Permite que você adicione documentos de marketing à sua oferta. Todos os documentos devem estar no formato PDF; você pode ter um máximo de três documentos por oferta.                                                                                                                                                      |
| Capturas de tela      | Permite que você adicione capturas de tela à sua oferta. Há um máximo de cinco capturas de tela que podem ser adicionadas por oferta. O tamanho máximo da imagem é 1280x720 pixels.                                                                                                                                             |
| Links úteis     | Permite que você adicione URLs externas à sua oferta para ajudar a indicar diagramas de arquitetura ou outros sites que um cliente gostaria de ver.                                                                                                                                                              |
|  |  |

A imagem a seguir mostra como as informações são exibidas em um resultado de pesquisa do Marketplace:

![Informações do publicador de marketing da oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info.png)

A imagem a seguir mostra como a oferta é exibida no Marketplace depois que um cliente clica na peça de oferta menor:

![Informações do publicador de marketing da oferta de SaaS 2](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info-2.png)


### <a name="lead-management-and-legal-information"></a>Gestão de cliente potencial e informações jurídicas

A seção Jurídica permite que você defina a documentação jurídica da oferta.
Dois documentos jurídicos são necessários para cada oferta de aplicativo SaaS: Política de Privacidade e Termos de uso. Para obter mais informações, consulte  
[Configurar clientes potenciais](./cloud-partner-portal-get-customer-leads.md).

![Informações jurídicas de marketing da oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer-legal-info.png)

A tabela a seguir descreve as propriedades da seção Jurídica:

| **Campos da oferta**   | **Descrição**                                                                                 |
|--------------------| ----------------------------------------------------------------------------------------------- |
| URL da Política de Privacidade | A URL da Política de Privacidade da sua empresa.                                                       |
| Termos de Uso       | Os Termos de uso da sua oferta. Digite ou copie e cole os termos de uso aqui. É permitido o uso de HTML básico, de modo que esse campo aceita conteúdo em html com marcações como &ltp\>, &lth1\>, &lth2\>, &ltli\> etc. *Importante* É altamente recomendável que você examine e visualize o HTML que criou em um navegador antes de enviar a oferta. |
|  |  |


### <a name="contact-information"></a>Informações de contato

Nesta seção, você inserirá os contatos de suporte da sua empresa que serão responsáveis por dar suporte aos clientes que utilizarem a oferta.
Há três áreas principais: contato de engenharia, contato de suporte e URLs de suporte:

![Informações de contato de marketing da oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer-contact-info.png)


| **Contato**         | **Descrição**                                                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Contato de engenharia | Forneça o nome, email e número de telefone de um contato de engenharia que a Microsoft possa contatar em caso de problemas empresariais e de suporte. |
| Contato de suporte     | Forneça o nome, email, número de telefone e URL de suporte que seus clientes possam contatar quando tiverem solicitações de suporte.                  |
|  |  |


Quando sua oferta estiver pronta e você pressionar Publicar, a oferta passará por certificação. Testamos seu aplicativo SaaS por meio de uma verificação manual durante o teste da URL, se você tiver uma listagem ou pontos de extremidade, se tiver selecionado Vender por meio do Azure. Durante essa aprovação manual, também decidimos a vitrine apropriada em que seu aplicativo deve aparecer (AppSource, Azure Marketplace ou ambos).

<a name="updating-the-offer"></a>Atualizando a oferta
------------------

Há vários tipos de atualizações que você talvez queira fazer na oferta depois que ela for publicada e estiver ativa. Qualquer alteração que você fizer na nova versão da sua oferta deve ser salva e republicada para ser refletida no Marketplace.

<a name="deleting-an-existing-offer"></a>Excluindo uma oferta existente
--------------------------

Você poderá decidir remover sua oferta do Marketplace. A Remoção de Oferta garante que novos clientes não possam comprar ou implantar sua oferta; porém, não tem impacto sobre clientes existentes. O Encerramento de Oferta é o processo de encerrar o contrato de licenciamento e/ou serviço entre você e seus clientes existentes.

Diretrizes e políticas relacionadas à remoção e ao encerramento de ofertas são governadas pelo Contrato do Editor do Microsoft Marketplace (consulte a Seção 7) e pelas Políticas de Participação (consulte a Seção 6.2). Esta seção fornece informações sobre os diferentes cenários de exclusão com suporte e as etapas que você pode seguir para executá-los.

### <a name="delete-the-live-offer"></a>Excluir oferta ativa

Há vários aspectos que precisam ser atendidos em caso de solicitação para remover uma oferta ativa. Siga as etapas abaixo para obter orientações da equipe de suporte para remover uma oferta ativa do Azure Marketplace:

1.  Gerar um tíquete de suporte usando este link
2.  Na lista Tipo de problema, selecione **Gerenciando ofertas**; na lista Categoria, selecione **Modificando uma oferta e/ou SKU já em produção**.
3.  Enviar a solicitação

A equipe de suporte orientará você pelo processo de exclusão da oferta.

> [!NOTE] 
> A exclusão de uma oferta não afeta as compras atuais dela. As compras do cliente continuarão funcionando como antes.
No entanto, a oferta não estará disponível para novas compras depois que a exclusão for concluída.
