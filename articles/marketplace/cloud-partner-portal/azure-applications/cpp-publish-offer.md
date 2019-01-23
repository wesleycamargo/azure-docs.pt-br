---
title: Publicar oferta de aplicativo do Azure – Azure Marketplace | Microsoft Docs
description: Descreva o processo e etapas para publicar uma oferta do aplicativo do Azure no Azure Marketplace.
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
ms.date: 01/10/2018
ms.author: pbutlerm
ms.openlocfilehash: deab8298d09972a4e60be5c72d6d7e43dc2683e6
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329983"
---
# <a name="publish-azure-application-offer"></a>Publicar oferta de aplicativo do Azure

Você pode publicar uma oferta logo após criá-la fornecendo as informações na página **Nova Oferta**. Selecione **publicar** para iniciar o processo de publicação.

O diagrama a seguir mostra as principais etapas no processo de publicação para uma oferta para "entrar no ar".

![Etapas de publicação da oferta](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Descrição detalhada de etapas de publicação

A tabela a seguir lista e descreve cada etapa de publicação, além de fornecer um tempo estimado para a conclusão de cada etapa.  Estimativas de tempo em "dias" são definidas como dias úteis, que não incluem finais de semana e feriados.

|  **Etapa de publicação**           | **Hora**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar os pré-requisitos         | < 15 min    | Informações de oferta e configurações de oferta são validadas.                        |
| Validar configurações de receita influenciadas | < 15 min  |      |
| Certificação                  | < 1 dia     | A oferta é analisada pela equipe de certificação do Azure. A oferta é exeminada em busca de vírus, malware, conformidade de segurança e problemas de segurança. A oferta é verificada para ver se atende aos critérios de qualificação. Para mais informações, consulte os [pré-requisitos](./cpp-prerequisites.md). Comentários serão fornecidos se um problema for encontrado. |
| Validação de test drive          | < 2 horas   | (Opcional) Se há um Test Drive, a Microsoft valida que ele pode ser implantado e replicado.  |
| Registro de embalagem e geração de leads | < 1 hora  | Os recursos técnicos da oferta são empacotados para uso do cliente e os sistemas de leads são configurados e implantados. |
|  Aprovação do publicador             |  manual    | Revisão final do editor e confirmação antes que a oferta seja publicada. A oferta agora está disponível para versão prévia.  Você pode implantar sua oferta nas assinaturas selecionadas (nas etapas a informações de oferta) para verificar se ele atende a todos os seus requisitos.  Após você verificar a oferta, selecione **Go Live** para que sua oferta possa passar para a próxima etapa. |
| Revisão da Microsoft                | 7 a 14 dias | A Microsoft revisa holisticamente seu aplicativo do Azure e contata você via email se algum problema é descoberto.  O comprimento desta etapa depende da complexidade do aplicativo, dos problemas descobertos e de quão prontamente você responde a eles.  |
| Live                           | < 1 dia | A oferta é lançada, replicada para as regiões especificadas e disponibilizada ao público. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |
 
Você pode monitorar o processo de publicação na guia **Status** para sua oferta no Portal do Cloud Partner.

![Guia Status para uma oferta de aplicativo do Azure](./media/offer-status-tab.png)

Depois de concluir o processo de publicação, a oferta será listada na [categoria de aplicativo do Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).


## <a name="errors-and-review-feedback"></a>Erros e comentários de revisão

Além de exibir o status da publicação da sua oferta, a guia **Status** também exibe mensagens de erro e comentários da etapa **revisão da Microsoft**.  Normalmente, problemas de revisão são referenciados como uma PR (solicitação de pull).  Cada solicitação de pull está vinculada a um item online do VSTS (Visual Studio Team Services, renomeado para [Azure DevOps](https://azure.microsoft.com/services/devops/)), que contém detalhes sobre o problema.  A imagem a seguir exibe um exemplo de uma referência de PR de revisão.  Para situações mais complexas, as equipes de revisão e de suporte podem entrar em contato com você por email. 

![Comentários de revisão de exibição da guia de Status](./media/status-tab-ms-review.png)

Você precisa resolver cada problema reportado antes que a oferta continue para o processo de publicação.  O diagrama a seguir ilustra como esse processo de comentários se relaciona com o processo de publicação.

![Etapas de publicação com comentários do VSTS](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>Acesso do VSTS

Para exibir os itens do VSTS referenciados em comentários de revisão, é preciso conceder a autorização adequada aos publicadores.  Caso contrário, os novos publicadores recebem uma página de resposta de `401 - Not Authorized`.  Para solicitar acesso ao sistema do VSTS de revisão de oferta, execute as seguintes etapas:

1. Colete as seguintes informações:
    - Sua ID e nome de publicador
    - Tipo de oferta (aplicativo do Azure), nome da oferta e ID do SKU
    - O link da solicitação de pull, por exemplo: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` Essa URL pode ser recuperada da mensagem de notificação ou do endereço da página de resposta 401.
    - Os endereços de email dos indivíduos de sua organização de publicação à qual você deseja obter acesso.  Eles devem incluir os endereços de proprietário que você forneceu quando se registrou como um publicador no Portal do Cloud Partner.
2. Crie um incidente de suporte.  Na barra de título do Portal do Cloud Partner, selecione o botão **Ajuda** e, em seguida, escolha **Suporte** no menu.  Seu navegador da Web padrão deverá iniciar e navegar até a página de novo incidente de suporte da Microsoft.  (Talvez você precise entrar primeiro.)
3. Especifique o **Tipo de problema** como **integração do marketplace** e **Categoria** como **Problema de acesso**, em seguida, selecione **Iniciar solicitação**.

    ![Categoria de tíquete de suporte](./media/support-incident1.png)

4. Na página **Etapa 1 de 2**, forneça suas informações de contato e selecione **Continuar**.
5. Na página **Etapa 2 de 2**, especifique um título do incidente (por exemplo `Request VSTS access`) e forneça as informações coletadas na primeira etapa (acima).  Leia e aceite o contrato e selecione **Enviar**.

Se a criação de incidentes for bem-sucedida, uma página de confirmação será exibida.  Salve as informações de confirmação para sua referência.  O Suporte da Microsoft deve responder à sua solicitação de acesso dentro de alguns dias úteis.


## <a name="next-steps"></a>Próximas etapas

Depois que um aplicativo do Azure for publicado, você poderá [Atualizar uma oferta existente](./cpp-update-existing-offer.md) para refletir a mudança de requisitos empresariais ou técnicos. 
