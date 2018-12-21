---
title: Atualizar uma oferta existente para o Azure Marketplace | Microsoft Docs
description: Explica como atualizar uma oferta do Azure Marketplace existente usando o Portal do Cloud Partner.
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: bfe2b0c70c8b912f6489ed461f5bcf6f233ed60d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805027"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Atualizar uma oferta existente para o Azure Marketplace
==============================================

Há vários tipos de atualizações que você poderá aplicar à oferta depois que ela ficar ativa.   O [Portal do Cloud Partner](https://cloudpartner.azure.com/) tem vários recursos para ajudá-lo a editar uma oferta a fim de garantir que as alterações estejam corretas, incluindo:

-  Adicionar uma nova versão da imagem de VM (máquina virtual) a um SKU existente
-  Alterar as regiões em que um SKU está disponível
-  Adicionar novos SKUs
-  Atualizar metadados do Marketplace para SKUs ou ofertas 
-  Atualizar preços em ofertas pagas conforme o uso
-  Comparar recursos
-  Histórico de recursos

Depois de modificar uma oferta ou um SKU, ele deve ser republicado antes que as alterações sejam ativadas.
Este artigo explica os diferentes aspectos da atualização de sua oferta de VM.


<a name="unpermitted-changes-to-vm-offersku"></a>Alterações não permitidas na oferta/SKU de VM
-----------------------------------

Há alguns atributos de um SKU ou oferta de VM que não poderão ser modificados depois que a oferta estiver ativa no Azure Marketplace.

-  ID da oferta e ID do editor da oferta.
-  ID de SKU de SKUs existentes.
-  Contagem de disco de dados de SKUs existentes.
-  Alterações no modelo de cobrança/licença para SKUs existentes.


<a name="updating-the-vm-image-version-for-a-sku"></a>Atualizando a versão da imagem de VM para um SKU
---------------------------------------

A imagem de VM para um SKU da sua oferta talvez tenha sido atualizada com recursos adicionais, patches de segurança, etc. Nessas situações, convém atualizar a imagem de VM ao qual seu SKU faz referência. Use as etapas a seguir para atualizar uma imagem de VM. 

1.  Faça logon no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2.  Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3.  No formulário **SKUs**, clique no SKU cuja imagem de VM você gostaria de atualizar.
4.  Em **Versão do disco**, clique em **+Nova versão do disco** para adicionar uma nova imagem de VM.
5.  Forneça a **Versão do disco** das novas imagens de VM. A versão do disco precisa seguir o formato de [versão semântica](http://semver.org/). Versões devem ser no formato X.Y.Z, onde X, Y e Z são inteiros. Verifique se a nova versão que você está fornecendo é maior do que as versões anteriores. Caso contrário, o novo número de versão não é exibido no portal do Azure ou no Azure Marketplace ao republicar.
6.  Para a **URL do VHD do sistema operacional**, insira o URI da SAS (assinatura de acesso compartilhado) criado para o VHD do sistema operacional. Observe que a contagem de discos de dados não pode ser alterada entre versões diferentes do SKU. Se as versões anteriores tinham discos de dados configurados, essa nova versão também deve ter os discos de dados configurados.
7.  Clique em **Publicar** para disparar o fluxo de trabalho de publicação para que a sua nova versão de VM fique ativa no marketplace do Azure e no portal do Azure.


<a name="changing-regions-a-sku-is-available-in"></a>Alterando regiões em que um SKU está disponível
--------------------------------------

Ao longo do tempo, talvez você queira disponibilizar sua oferta/SKU em regiões mais.
Como alternativa, talvez você queira parar de dar suporte à oferta/SKU em uma determinada região.
Para implementar essas alterações, siga as etapas abaixo.

1.  Faça logon no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2.  Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3.  No formulário **SKUs**, clique no SKU cuja disponibilidade por região você gostaria de atualizar.
4.  Clique no botão **Selecionar países** no campo **Disponibilidade por país/região**.
5.  Na pop-up de disponibilidade por região, adicione/remova as regiões desejadas para este SKU.
6.  Clique em **Publicar** para disparar o fluxo de trabalho de publicação para atualizar a disponibilidade por região dos seus SKUs.

Se um SKU estiver sendo disponibilizado em uma nova região, você poderá especificar preços para essa região específica por meio da funcionalidade **Exportar dados de preços**. Se você estiver adicionando novamente uma região que estava disponível anteriormente, não será possível atualizar seus preços, visto que alterações de preços não são permitidas.


<a name="adding-a-new-sku"></a>Adicionando um novo SKU
----------------

Para disponibilizar um novo SKU para sua oferta existente, siga as etapas abaixo.

1.  Faça logon no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2.  Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3.  No formulário **SKUs**, clique em **Adicionar novo SKU** e forneça uma **ID do SKU** no pop-up.
4.  Siga o restante das etapas detalhadas em [Publicar uma máquina virtual no Azure Marketplace](./cloud-partner-portal-publish-virtual-machine.md).
5.  Clique em **Publicar** para disparar o fluxo de trabalho de publicação para que seu novo SKU fique ativo.


<a name="updating-offer-marketplace-metadata"></a>Atualizando metadados do marketplace da oferta.
------------------------------------

Talvez haja situações em que seja necessário atualizar os metadados de marketplace associados à sua oferta como atualizar seus logotipos da empresa, etc. Use as etapas a seguir para atualizar os metadados da oferta.

1.  Faça logon no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2.  Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3.  Acesse o formulário do **Marketplace** e siga as instruções aqui para fazer alterações.
4.  Clique em **Publicar** para disparar o fluxo de trabalho de publicação para que suas alterações fiquem ativas.


<a name="updating-pricing-on-published-offers"></a>Como atualizar o preço em ofertas publicadas
------------------------------------

Depois que sua oferta paga conforme o uso é publicada, você não pode aumentar o preço de um SKU existente, mas pode criar um novo SKU sob a mesma oferta, excluir o SKU antigo e, em seguida, republicar a oferta. Também permitimos que você diminua o preço em ofertas já publicadas. Para diminuir o preço da oferta:

1.  Clique no SKU para o qual você deseja diminuir o preço.
2.  Se tiver definido o preço no GUI 1x1, você poderá alterar o preço diretamente na interface do usuário. Se definir preços por meio de planilha de importação/exportação, você só poderá diminuir os preços por meio do recurso de importação/exportação.
3.  Clique em **Salvar**.
4.  Republique sua oferta e ative-a.

O preço ficará visível para novos clientes assim que ela estiver completamente ativa no site, e todos os novos clientes pagarão o novo preço reduzido.

Para os clientes existentes, a diminuição de preço será refletida retroativamente para o início do ciclo de cobrança durante o qual a diminuição de preço entrou em vigor.
Se eles já foram cobrados pelo ciclo durante o qual ocorreu uma diminuição de preço, eles receberão um reembolso durante seu próximo ciclo de cobrança para cobrir o preço reduzido.


<a name="simplified-currency-pricing"></a>Preço em moeda simplificada
---------------------------

A partir de setembro de 2018, uma nova seção chamada **Preço em moeda simplificada** está visível para você. A Microsoft está simplificando os negócios do Azure Marketplace, permitindo preços e coleções mais previsíveis de seus clientes em todo o mundo. Essa simplificação é obtida, reduzindo o número de moedas em que podemos faturar seus clientes.

A nova seção tem preços nessas novas moedas. Depois que todos os clientes forem migrados para essas novas moedas de liquidação, a seção de preço original será desativada e apenas a seção "Preço em moeda simplificada" permanecerá.

Você tem até 1º de novembro de 2018 para definir um novo preço para as regiões nas quais a moeda de liquidação está mudando. Você não poderá aumentar o preço para as regiões nas quais a moeda de liquidação não está sendo alterada. A seguir estão as regiões nas quais a moeda está sendo alterada:

| País/Região                  | Código ISO2 | Moeda de cobrança simplificada |
|--------------------------|-----------|-----------------------------|
| **Argélia**              | DZ        | USD                         |
| **Argentina**            | AR        | USD                         |
| **Bahrein**              | BH        | USD                         |
| **Belarus**              | BY        | USD                         |
| **Brasil**               | BR        | BRL                         |
| **Bulgária**             | BG        | EUR                         |
| **Chile**                | CL        | USD                         |
| **Colômbia**             | CO        | USD                         |
| **Costa Rica**           | CR        | USD                         |
| **Croácia**              | HR        | EUR                         |
| **República Tcheca**       | CZ        | EUR                         |
| **Egito**                | EG        | USD                         |
| **Guatemala**            | GT        | USD                         |
| **Hong Kong**            | HK        | USD                         |
| **Hungria**              | HU        | EUR                         |
| **Islândia**              | IS        | EUR                         |
| **Indonésia**            | ID        | USD                         |
| **Israel**               | IL        | USD                         |
| **Jordânia**               | JO        | USD                         |
| **Cazaquistão**           | KZ        | USD                         |
| **Quênia**                | KE        | USD                         |
| **Kuwait**               | KW        | USD                         |
| **Liechtenstein**        | LI        | EUR                         |
| **Macedônia (FYRO)**     | MK        | USD                         |
| **Malásia**             | MY        | USD                         |
| **México**               | MX        | USD                         |
| **Montenegro**           | ME        | USD                         |
| **Marrocos**              | MA        | USD                         |
| **Nigéria**              | NG        | USD                         |
| **Omã**                 | OM        | USD                         |
| **Paquistão**             | PK        | USD                         |
| **Paraguai**             | PY        | USD                         |
| **Peru**                 | PE        | USD                         |
| **Filipinas**          | PH        | USD                         |
| **Polônia\***             | PL        | EUR                         |
| **Catar**                | QA        | USD                         |
| **Romênia**              | RO        | EUR                         |
| **Arábia Saudita**         | SA        | USD                         |
| **Sérvia**               | RS        | USD                         |
| <ph x="1" type="bpt">&lt;bpt id="p1"&gt;**&lt;/bpt&gt;</ph>Singapura<ph x="2" type="ept">&lt;ept id="p1"&gt;**&lt;/ept&gt;</ph>            | SG        | USD                         |
| **África do Sul**         | ZA        | USD                         |
| **Tailândia**             | TH        | USD                         |
| **Trinidad e Tobago**  | TT        | USD                         |
| **Tunísia**              | TN        | USD                         |
| **Turquia**               | TR        | USD                         |
| **Ucrânia**              | UA        | USD                         |
| **Emirados Árabes Unidos** | AE        | USD                         |
| **Uruguai**              | UY        | USD                         |
|  |  |  |

> [!NOTE]
> Se você usar APIs para publicar sua oferta, você poderá ver uma nova seção dentro da oferta denominada **JSON**. Isso poderia ser anotado como `virtualMachinePricingV2` ou `monthlyPricingV2`, dependendo do tipo de oferta.

Se você tiver alguma dúvida em torno dessa alteração, entre em contato com o suporte do Azure Marketplace.


<a name="compare-feature"></a>Recurso Comparar
---------------

Quando você faz alterações em uma oferta já publicada, você pode usar o recurso *Comparar* para auditar as alterações que foram feitas. Para utilizar o Comparar:

1.  Em qualquer ponto no processo de edição, você pode clicar no botão **Comparar** para sua oferta.

2.  Exibir versões lado a lado de metadados e ativos de marketing.


<a name="history-of-publishing-actions"></a>Histórico de ações de publicação
-----------------------------

Para exibir qualquer atividade de histórico de publicação, clique na guia **Histórico** no painel de navegação esquerdo do Portal do Cloud Partner. Aqui, você poderá exibir as ações de data/hora que foram executadas durante o tempo de vida das ofertas do Azure Marketplace.
