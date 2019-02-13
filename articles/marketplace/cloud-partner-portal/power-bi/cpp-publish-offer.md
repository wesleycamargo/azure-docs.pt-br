---
title: Publicar uma oferta do Aplicativo do Power BI – Azure Marketplace | Microsoft Docs
description: Publique uma oferta de aplicativo do Power BI no Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665707"
---
# <a name="publish-power-bi-app-offer"></a>Publicar oferta de Aplicativo do Power BI

A última etapa, depois de definir a oferta no portal e criar os recursos técnicos associados, é enviar a oferta para publicação.  Para iniciar esse processo, clique no botão **Publicar** no menu vertical na janela **Nova Oferta**.  Para obter mais informações, consulte [Publicar ofertas no Azure Marketplace e no AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Etapas de publicação

O diagrama a seguir descreve as principais etapas do processo de publicação para "entrar em operação".

![Etapas do processo de publicação para o Aplicativo do Power BI](./media/publishing-process-steps.png)

A tabela a seguir descreve essas etapas e fornece uma estimativa de tempo máximo para sua conclusão:

|   Etapa de publicação            |   Hora     |   DESCRIÇÃO                                                                  |
| --------------------         |------------| ----------------                                                               |
| Validar os pré-requisitos       | 15 min     | Informações de oferta e configurações de oferta são validadas.                            |
| Certificação                | Um a sete dias   | A Equipe de Certificação do Power BI analisa sua oferta. Executamos o Aplicativo do Power BI por um teste de verificação manual instalando o aplicativo por meio da URL de instalação fornecida. As principais validações são executadas como parte do processo de certificação de aplicativo. Veja abaixo.         |
| Empacotamento                    | \< 1 hora  | Os ativos técnicos da oferta são empacotados para uso pelo cliente.                        |
| Registro de geração de cliente potencial | \< 1 hora  | Os sistemas de clientes potenciais são configurados e implantados.                                      |
| Aprovação do publicador            | \-         | Revisão final do editor e confirmação antes que a oferta seja publicada. Também haverá um link para ver sua oferta. Quando estiver satisfeito com a aparência de sua visualização, clique no botão **Entrar no Ar** na guia **Status**. Essa ação envia uma solicitação para a equipe de integração para listar seu aplicativo no AppSource.    |
| Live                         | \< 3 horas | Sua oferta está anunciada publicamente agora (“no ar”) no AppSource e os clientes poderão exibir e implantar seu aplicativo nas assinaturas do Power BI deles. Você também receberá um email de confirmação. A qualquer momento, clique na guia **Todas as ofertas** e veja o status de todas as suas ofertas listadas na coluna à direita. Você pode clicar na guia **Status** para ver em detalhes o status do fluxo de publicação de sua oferta. |
|   |   |

Aguarde até outo dias para este processo ser concluído. Depois que você percorrer essas etapas de publicação, sua oferta de Aplicativo do Power BI será listada na seção Aplicativos do Power BI do [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20).


### <a name="app-certification-process"></a>Processo de certificação de aplicativo

A equipe de integração da Microsoft usa o seguinte processo para validar seu envio de oferta do Power BI:

1. Os links de ajuda e os documentos legais são revisados.
2. As informações de Contato de Suporte são validadas.
3. A URL do instalador é usada para verificar a instalação correta. 
4. O aplicativo é verificado quanto à existência de malware e outros conteúdos maliciosos. 
5. A verificação é realizada para que o conteúdo exibido corresponda à descrição do aplicativo.
6. Operações relacionadas ao aplicativo funcionam conforme o esperado no Power BI: abrir relatórios e dashboards com os dados de exemplo, conectar-se a fontes de dados personalizadas, atualizar etc.

A Equipe de Certificação faz comentários quando encontra problemas.  Para obter mais informações sobre os requisitos de Aplicativo do Power BI, confira a [documentação do Aplicativo do Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Próximas etapas

Recomendamos que você monitore regularmente seu aplicativo no [Marketplace do AppSource](https://appsource.microsoft.com).  Além disso, você deve usar o recurso [Insights do Vendedor](../../cloud-partner-portal-orig/si-getting-started.md) do [Portal do Cloud Partner](https://cloudpartner.azure.com/#insights) para fornecer insights sobre seu uso e seus clientes do marketplace.  Você também pode executar certas [atualizações para sua oferta](./cpp-update-existing-offer.md).
