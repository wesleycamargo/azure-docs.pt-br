---
title: Guia do mercado de máquinas virtuais no Cloud Partner Portal para Azure | Microsoft Docs
description: Descreve a guia Marketplace usada na criação de uma oferta de VM do Marketplace do Azure.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: b1b62c68ef4e18f4d4d36a78078ad7431717b754
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332700"
---
# <a name="virtual-machine-marketplace-tab"></a>Guia de Marketplace de máquina virtual

A guia **Marketplace** da página **New Offer** permite que você forneça aos clientes em potencial informações e contratos de marketing, vendas e legais e gerencie leads gerados no mercado. Essa forma longa é dividida em quatro seções: **Visão geral**, **artefatos de Marketing**, **gerenciamento de clientes potenciais**, e **Legal**. 

## <a name="overview-section"></a>Seção de visão geral
Nesta seção, você insere as informações gerais sobre sua oferta do Azure Marketplace.  Um asterisco anexado (*) no nome do campo indica que é necessário.

![Seção Visão geral da guia Mercado no formulário Nova oferta para máquinas virtuais](./media/publishvm_008.png)

A tabela a seguir descreve o objetivo e o conteúdo desses campos.

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **Título**                 | Título da oferta, geralmente o nome longo e formal. Este título será exibido com destaque no mercado.  Comprimento máximo de 50 caracteres. |
| **Resumo**               | Breve propósito ou função da solução.  Comprimento máximo de 100 caracteres. |
| **Resumo longo**          | Finalidade ou a função da solução.  Comprimento máximo de 256 caracteres. |
| **Descrição**           | Descrição da solução  Comprimento máximo de 3000 caracteres, suporta formatação HTML simples. |
| **Canal de revendedor CSP da Microsoft** | Cloud Solution Providers (CSP) parceiro canal participar agora está disponível.  Consulte [Cloud Solution Providers](../../cloud-solution-providers.md) para obter mais informações sobre sua oferta por meio do Microsoft CSP de marketing de parceiros canais. |
| **Identificador de marketing**  | Um URL exclusivo para associar a essa oferta geralmente inclui sua organização e o nome da solução, com um comprimento máximo de 50 caracteres.  Por exemplo:  <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Visualizar códigos de inscrição** | Adicione de um a 100 identificadores de assinatura de pré-visualizadores. Essas assinaturas listadas na lista de permissões terão acesso à oferta assim que forem publicadas antes de serem publicadas. |
| **Links úteis**          | Adicione URLs a documentações, notas de versão, perguntas frequentes e assim por diante. |
| **Categorias sugeridas (máximo 5)** | A seleção múltipla de categorias comerciais e técnicas que oferecem pode ser melhor associada.  Máximo permitido de cinco.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Seção de artefatos de marketing

Essa segunda seção é dividida em três subseções: **Logotipos**, **captura de tela**, e **vídeos**. Os logotipos são os únicos artefatos de marketing necessários, no entanto, todos são altamente recomendados para o melhor apelo do cliente.

![Seção Artefatos de Marketing da guia Mercado no formulário Nova Oferta para máquinas virtuais](./media/publishvm_009.png)

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| *Logos*  |  |
| **Pequeno**                 | 40x40 pixel .ico bitmap                                                      |
| **Médio**                | 90x90 pixel .ico bitmap                                                      |
| **Grande**                 | 115x115 pixel .ico  bitmap                                                   |
| **Ampla**                  | bitmap do pixel de 255 x 115. ico                                                    |
| **Hero**                  | bitmap de 815 x 290.  Opcional, no entanto, uma vez carregado, o ícone do herói não pode ser excluído. |
| *Capturas de tela*  | Opcional, mas no máximo cinco capturas de tela por SKU. |
| **Nome**                  | Nome ou o título <!-- TODO - max char length? none specified in UI -->                               |
| **Imagem**                 | Imagem de captura de tela, 533, 324 pixel                                         |
| *Vídeos*  |  |
| **Nome**                  | Nome ou o título  <!-- TODO - max char length? -->                              |
| **Link**                  | URL do vídeo, hospedado no YouTube ou Vimeo                                        |
| **Miniatura**             | bitmap 533, 324                                                               |
|  |  |


### <a name="logo-guidelines"></a>Diretrizes de logotipo

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Todos os logotipos enviados para o Cloud Partner Portal devem seguir as diretrizes:

*  O design do Azure tem uma paleta de cores simples. Mantenha um baixo número de cores primárias e secundárias no seu logotipo.
*  As cores do tema do portal do Azure são branco e preto. Portanto, evite usar essas cores como a cor de fundo dos seus logotipos. Use uma cor que destaque seus logotipos no portal do Azure. É recomendável usar cores primárias simples. Se você estiver usando plano de fundo transparente, certifique-se de que os logotipos / texto não sejam branco, preto ou azul.
*  Não use um fundo gradiente em seu logotipo.
*  Evite colocar o texto - até mesmo sua empresa ou nome de marca - no logotipo. A aparência do seu logotipo deve ser "plana" e deve evitar gradientes.
*  Não estique o logotipo.

#### <a name="hero-logo"></a>Logotipo Hero

O logotipo do herói é opcional; no entanto, uma vez carregado, o ícone do herói não pode ser excluído.  O ícone do logotipo do herói deve seguir as diretrizes:

*  Planos de fundo pretos, brancos e transparentes não são permitidos para ícones de heróis.
*  Evite usar qualquer cor clara como plano de fundo do ícone do herói.  O nome de exibição do Publicador, o título do plano e o resumo longo da oferta são exibidos em cor de fonte em branco e devem se destacar em relação ao segundo plano.
*  Evite usar a maioria dos textos enquanto estiver projetando o logotipo do herói.  O nome do editor, o título do plano, o resumo longo da oferta e um botão de criação são incorporados programaticamente dentro do ícone de herói quando a oferta é listada. 
* Inclua um retângulo não utilizado no lado direito do seu ícone de herói, de tamanho 415x100 pixels e deslocamento de 370 px a partir da esquerda.  

Por exemplo, o ícone herói a seguir é para o Serviço de Contêiner do Azure.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Exemplo de ícone de herói para o Serviço de Contêiner do Azure](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Exemplo de informações de marketing 

A imagem a seguir demonstra como as informações de marketing são exibidas na página principal do produto Microsoft Windows Server.

![Página de produto de exemplo para o Microsoft Windows Serverx](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Seção de gerenciamento de leads
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

A terceira seção permite coletar leads de clientes gerados a partir de suas ofertas do Azure Marketplace. Ele oferece as seguintes opções de armazenamento (de uma lista suspensa) para essa informação de lead.

* **Nenhum** - as informações de lead padrão não são coletadas.
* Tabela do Azure - gravada na tabela do Azure especificada por uma cadeia de conexão.
* Dynamics CRM Online - escrito para o [Online do Microsoft Dynamics 365](https://dynamics.microsoft.com/) instância, especificada por uma URL e credenciais de autenticação.
* Ponto de extremidade HTTPS - gravado no ponto de extremidade HTTPS especificado como uma carga útil JSON.
* Marketo - gravado na instância [Marketo](https://www.marketo.com/) especificada, especificada por ID do servidor, ID munchkin e ID do formulário.
* SalesForce – gravado em um [Salesforce](https://www.salesforce.com/) banco de dados, especificado por um identificador de objeto.

Depois de publicar sua oferta, a conexão do lead é validada e um lead de teste é enviado automaticamente para o destino configurado. As informações de lead devem ser gerenciadas continuamente e essas configurações devem ser prontamente atualizadas sempre que forem feitas alterações em sua arquitetura de gerenciamento de clientes.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Seção legal

Esta última seção permite que você forneça os dois documentos legais necessários para cada oferta: Política de Privacidade e Termos de Uso.

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **URL da política de privacidade**    | URL para sua política de privacidade postada                                            |
| **Termos de uso**          | política como texto sem formatação ou HTML simple.  <!-- TODO - max char length? -->       |
|  |  |

<br/>

Nas próximas guias [suporte](./cpp-support-tab.md), você fornecerá os recursos de suporte técnico e de usuário para sua oferta.

