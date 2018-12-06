---
title: Ative sua oferta de Aplicativo Azure no Azure Marketplace | Microsoft Docs
description: .
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
ms.date: 11/15/2018
ms.author: pbutlerm
ms.openlocfilehash: 18a8e6ae8ab3bd4299c6a014f938e73a2a021492
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263580"
---
<a name="make-your-azure-application-offer-live-on-azure-marketplace"></a>Ative sua oferta de aplicativo Azure no Azure Marketplace 
===========================================================

Agora que você já preencheu todos os detalhes da oferta, chegou a hora de publicá-la e ativá-la no Azure Marketplace. Há algumas etapas para verificar se o conteúdo de marketing e o aplicativo atendem aos requisitos de qualidade da Certificação do Azure, antes da ativação no site.

![Fluxo de publicação](./media/cloud-partner-portal-publish-managed-app/publish_flow.png)

Vamos percorrer esse processo mais detalhadamente para entender melhor o que acontece durante cada etapa e os itens de ação nesse processo para verificar se sua oferta continua a progredir.

<a name="publishing-process"></a>Processo de publicação 
------------------

Você clicará em \"Publicar\" na guia **Editor** para iniciar o processo de publicação.

![Sequência de ativação da oferta 1 – publicar](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish.png)

Na guia **Status**, você verá as etapas de publicação e em qual etapa sua oferta está.

![Sequência de ativação da oferta 2 – fluxo de trabalho](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_workflow.png)

Em qualquer ponto no processo de publicação, você também pode entrar e clicar na guia **Todas as Ofertas**para exibir o status mais recente de qualquer uma das suas ofertas. Clique diretamente no status da oferta e veja os detalhes de onde sua oferta está no processo de publicação.

> [!WARNING]
> Se a atribuição de uso do cliente não estiver habilitada, você receberá a seguinte mensagem de erro durante o processo de publicação: "Atribuição de uso do cliente parceiro do Azure em falta em um ou mais modelos do Azure Resource Manager. Para resolver, adicione um GUID de rastreamento para o modelo do Azure Resource Manager em pacotes de mainTemplate.json para o plano a seguir: servicenow. Para obter mais informações, consulte http://aka.ms/customerusageattribution.” 

Vamos percorrer cada uma das etapas de publicação e discutir o que acontece em cada etapa e quanto tempo cada etapa levará.

### <a name="validate-prerequisites-1-day"></a>Validar os pré-requisitos (\<1 dia) 

Quando você clicar em \"Publish\", uma verificação automática ocorrerá para garantir que você preencheu todos os campos obrigatórios da oferta. Se algum campo não estiver preenchido, um aviso será exibido ao lado do campo e você precisará preenchê-lo com precisão. Depois, clique em \'Publicar\' novamente.

Após a conclusão correta dessa etapa, um pop-up será exibido solicitando um endereço de email. Este é o email no qual você receberá notificações de status publicação para o restante do processo de publicação. Depois de enviar seu endereço de email, esta etapa será concluída.

![Sequência de ativação da oferta 1 – publicar 1](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish1.png)

### <a name="certification-5-days"></a>Certificação (\<5 dias) 

É nesta etapa que executaremos vários testes para garantir que o pacote do Aplicativo Azure atenda aos requisitos da Certificação do Azure.

Como esta etapa pode demorar vários dias, você pode sair do Portal de Parceiros de Nuvem.nuvem. Nós lhe enviaremos uma notificação por email se houver erros. Se tudo for aprovado com êxito, o processo passará automaticamente para a etapa de Provisionamento.

### <a name="packaging-and-lead-generation-registration-1-hour"></a>Empacotamento e registro de geração de cliente potencial (\<1 hora) 

Durante este estágio, combinamos o conteúdo técnico e de marketing no que será a página do produto no site.

### <a name="offer-available-in-preview"></a>A oferta está disponível para visualização 

Você receberá um email de notificação de que sua oferta concluiu com êxito as etapas necessárias para acessar à oferta em visualização. Nesta etapa, você deverá visualizar sua oferta e verificar se está certo tudo. Verifique se a VM está implantada corretamente no ambiente de preparo.

Apenas as assinaturas na lista de permissões podem fazer essa verificação.\*

### <a name="publisher-sign-out"></a>Saída do editor 

Depois de confirmar que tudo parece correto e funciona adequadamente no modo de visualização, você estará pronto para ativar. Clique em Ativar na guia Status e começaremos as etapas para ativar sua oferta na produção e no site. Geralmente, demorará várias horas desde o momento em que você clicar em Ativar até a oferta estar ativa no site. Enviaremos uma notificação por email quando sua oferta estiver oficialmente ativa no site.

![Sequência de ativação da oferta 6 – Ativar](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_goLive.png)

### <a name="microsoft-review"></a>Revisão da Microsoft 

Quando estiver tudo pronto e você clicar em \"Ativar\", um site de trabalho será criado no Visual Studio online, seguido por uma revisão de código do conteúdo do pacote realizada pela Microsoft. A revisão de código abordará os padrões e as práticas recomendadas a serem usados ao criar modelos, dicas e truques para otimizar a criação de recursos. Se os comentários fizerem com que o editor faça alterações nos arquivos, o processo de publicação precisará ser iniciado novamente. A publicação atual será cancelada e você precisará publicar novamente com os comentários já resolvidos.

### <a name="live"></a>Live

Agora, sua oferta está ativa no Azure Marketplace e no Portal do Azure. Os clientes poderão exibir e implantar seu aplicativo Azure gerenciado em suas assinaturas do Azure. Você pode clicar na guia Todas as Ofertas e ver o status de todas as suas ofertas listadas na coluna à direita. Você pode clicar no status para ver em detalhes o status do fluxo de publicação de sua oferta.

### <a name="error-handling"></a>Tratamento de erros 

Se houver algum erro, você receberá um email de notificação informando que ocorreu um erro e contendo instruções sobre as próximas etapas. Você também pode ver os erros a qualquer momento durante esse processo clicando na guia Status. Você verá em que ponto do processo o erro ocorreu junto com uma mensagem de erro descrevendo o que precisa ser resolvido.

Se você encontrar erros durante o processo de publicação, será necessário corrigi-los e, em seguida, clicar em \'Publicar\' para reiniciar o processo. Você deve começar no início das etapas de publicação em Validar Pré-requisitos ao publicar novamente após a correção de qualquer erro.

Se você estiver enfrentando problemas para resolver um erro, abra uma solicitação de suporte para obter ajuda de nossos engenheiros de suporte.

### <a name="canceling-the-publishing-request"></a>Cancelar a solicitação de publicação

Você pode iniciar o processo de publicação e ter que cancelar a solicitação. Você só pode cancelar uma solicitação de publicação depois que a solicitação de publicação atingir a etapa de Aprovação do Publicador. Para cancelar, clique em \'Cancelar Publicação\'. O status da publicação voltará à Etapa 1 e, para publicar novamente, você deverá clicar em Publicar e executar as etapas no status.
