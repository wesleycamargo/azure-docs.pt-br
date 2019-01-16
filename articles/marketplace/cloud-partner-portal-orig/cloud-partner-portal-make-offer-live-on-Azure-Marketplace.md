---
title: Ativar sua oferta de Máquina Virtual no Azure Marketplace
description: Ativar sua oferta de Máquina Virtual no Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ad22f1944d3fe9a088b66da4cf4df7136db497f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075321"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Ativar sua oferta de Máquina Virtual no Azure Marketplace
=========================================================

Após preencher todos os detalhes da oferta, será o momento de publicar a oferta e torná-la ativa no Azure Marketplace. Há alguns estágios pelos quais a oferta passa. Certifique-se de que o conteúdo de marketing e a imagem de VM atendem aos requisitos de qualidade para que sejam certificados pelo Azure e ativados no site.

![Sequência 0 de ativação da oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

Verificaremos esse processo com mais detalhes para compreender melhor o que acontece em cada etapa. Nesse processo, você precisará agir para garantir que a oferta continue avançando.

<a name="publishing-process"></a>Processo de publicação
------------------

Clique em "Publicar" na guia Editor para iniciar o processo de Publicação.

![Sequência 1 de ativação da oferta - Publicar](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

Na guia Status, você verá as etapas de Publicação e onde sua oferta está no processo.

![Sequência 2 de ativação da oferta - Status](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

Em qualquer ponto no processo de publicação, você também pode entrar e clicar na guia Todas as Ofertas para exibir o status mais recente de qualquer uma das suas ofertas. Clique diretamente no status da oferta e veja os detalhes de onde sua oferta está no processo de publicação.

![Sequência 3 de ativação da oferta - Todos os status de ofertas](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

Vamos percorrer cada uma das etapas de publicação e discutir o que acontece em cada etapa e quanto tempo cada etapa levará.

**Validar os pré-requisitos (\<1 dia)**

Ao clicar em "Publicar", uma verificação automática ocorrerá para garantir que você preencha todos os campos obrigatórios em sua oferta. Se algum campo não for preenchido, um aviso será exibido ao lado do campo e você precisará preenchê-lo com precisão. Depois, clique em "Publicar" novamente.

Assim que tiver concluído essa etapa corretamente, um pop-up solicitará um endereço de email. Este é o email no qual você receberá notificações de status publicação para o restante do processo de publicação. Depois de enviar seu endereço de email, esta etapa será concluída.

![Sequência 4 de ativação da oferta - Publicar a oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**Certificação (\<5 dias)**

É nesta etapa onde executamos vários testes para garantir que sua imagem de VM atenda aos requisitos de Certificação do Azure. Todas as orientações necessárias para garantir que você atenda aos requisitos de certificação estão [aqui](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md).

Como essa etapa pode levar vários dias, você poderá sair do Portal do Cloud Partner. Enviaremos uma notificação por email se houver erros para correção. Se tudo for aprovado com êxito, o processo passará automaticamente para a etapa de Provisionamento.

**Provisionamento (\<1 dia)**

Durante essa etapa, replicamos suas imagens para todos os data centers globais do Azure, e isso pode demorar até um dia.

**Empacotamento e registro de geração de cliente potencial (\<1 hora)**

Durante este estágio, combinamos o conteúdo técnico e de marketing no que será a página do produto no site.

Além disso, se você configurou o recurso de Geração de Cliente Potencial, validaremos se a integração de CRM está funcionando enviando um teste de cliente potencial para seu CRM. Você verá um registro com dados falsos preenchidos no CRM ou na Tabela do Azure após a conclusão dessa etapa. Toda a documentação para Geração de Cliente Potencial está localizada [aqui](./cloud-partner-portal-get-customer-leads.md).

**Oferta disponível em visualização**

Você receberá um email de notificação de que sua oferta concluiu com êxito as etapas necessárias para acessar à oferta em visualização. Durante esta etapa, você deve visualizar sua oferta e verificar se tudo está como deveria, e se a sua VM é implantada corretamente no ambiente de preparo.

*Apenas as assinaturas na lista de permissões podem fazer essa verificação.*

**Aprovação do publicador**

Depois de confirmar que tudo parece correto e funciona adequadamente no modo de visualização, você estará pronto para ativar. Clique em **Ativar** na guia **Status** e começaremos as etapas para ativar sua oferta na produção e no site. Geralmente, demorará várias horas desde o momento em que você clicar em Ativar até a oferta estar ativa no site. Enviaremos uma notificação por email quando sua oferta estiver oficialmente ativa no site.

![Sequência 5 de ativação da oferta - Ativar](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**Ativa**

Agora, a sua oferta está ativa no Microsoft Azure Marketplace e no Portal do Azure, e os clientes poderão ver e implantar a máquina virtual em suas assinaturas do Azure. A qualquer momento, clique na guia Todas as ofertas e veja o status de todas as suas ofertas listadas na coluna à direita. Você pode clicar no status para ver em detalhes o status do fluxo de publicação de sua oferta.

<a name="error-handling"></a>Tratamento de erros
--------------

Durante o processo de publicação, você pode encontrar um erro. Se isso acontecer, você receberá um email de notificação informando que ocorreu um erro e com instruções sobre as próximas etapas. Você também pode ver os erros a qualquer momento durante esse processo clicando na guia Status. Você verá em que ponto do processo o erro ocorreu junto com uma mensagem de erro descrevendo o que precisa ser resolvido.

![Sequência 6 de ativação da oferta - Mensagem de erro](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

Se você encontrar erros durante o processo de publicação, será necessário corrigi-los e, em seguida, clicar em "Publicar" para reiniciar o processo. Você precisa começar no início das etapas de publicação em Validar Pré-Requisitos ao republicar após a correção de algum erro.

Se você estiver enfrentando problemas para resolver um erro, abra uma solicitação de suporte para obter ajuda de nossos engenheiros de suporte.

![Sequência 7 de ativação da oferta - Obter suporte](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>Cancelar a solicitação de publicação
--------------------------------

Você pode iniciar o processo de publicação e ter que cancelar a solicitação. Você só poderá cancelar uma solicitação de publicação quando a solicitação de publicação atingir a etapa de aprovação do editor. Para cancelar, clique em 'Cancelar Publicação'. O status da publicação voltará à Etapa 1 e, para publicar novamente, você deverá clicar em Publicar e executar as etapas no status.

![Sequência 8 de ativação da oferta - Status](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

