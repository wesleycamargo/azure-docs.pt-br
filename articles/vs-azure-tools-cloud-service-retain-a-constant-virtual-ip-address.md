---
title: "Como reter um endereço IP virtual constante para um serviço de nuvem do Azure | Microsoft Docs"
description: "Saiba como assegurar que o VIP (endereço IP virtual) do seu serviço de nuvem do Azure não mude."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 12d73e2f490acc92bb4a2e1014bb24f7f672f713
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Como reter um endereço IP virtual constante para um serviço de nuvem do Azure
Quando você atualiza um serviço de nuvem que é hospedado no Azure, você precisará garantir que o VIP (endereço IP virtual) do serviço não mude. Muitos serviços de gerenciamento de domínio usam o DNS (sistema de nome de domínio) para o registro de nomes de domínio. O DNS só funciona se o VIP permanece o mesmo. Você pode usar o **Assistente de Publicação** nas Ferramentas do Azure para garantir que o VIP do seu serviço de nuvem não seja alterado quando você atualizá-lo. Para obter mais informações sobre como usar o gerenciamento de domínio DNS para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publicando um serviço de nuvem sem alterar seu VIP
O VIP de um serviço de nuvem é alocado ao implantá-lo pela primeira vez no Azure em um ambiente específico, como o ambiente de Produção. O VIP só será alterado se você excluir a implantação explicitamente ou se ela for excluída implicitamente pelo processo de atualização de implantação. Para manter o VIP, você não pode excluir sua implantação e deve certificar-se também de que ela não seja excluída automaticamente pelo Visual Studio. Você pode controlar o comportamento especificando configurações de implantação no **Assistente de Publicação**, que dá suporte a várias opções de implantação. Você pode especificar uma nova implantação ou uma implantação de atualização, que pode ser incremental ou simultânea; ambos estes tipos de implantação de atualização retêm o VIP. Para obter definições desses tipos diferentes de implantação, consulte o [Assistente Publicar Aplicativo no Azure](vs-azure-tools-publish-azure-application-wizard.md).  Além disso, você pode controlar se a implantação anterior de um serviço de nuvem deve ou não ser excluída caso um erro ocorra. O VIP pode mudar inesperadamente se você não definir essa opção corretamente.

## <a name="updating-a-cloud-service-without-changing-its-vip"></a>Atualizando um serviço de nuvem sem alterar seu VIP
1. Crie ou abra um projeto de serviço de nuvem do Azure no Visual Studio. 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e, no menu de contexto, selecione **Publicar**.

    ![Menu Publicar](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

1. Na caixa de diálogo **Publicar Aplicativo do Azure**, selecione a assinatura do Azure na qual você deseja implantar, conecte-se, se necessário, e selecione **Avançar**.

    ![Publicar aplicativo do Azure: conexão](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

1. Na guia **Configurações Comuns**, verifique se o nome do serviço de nuvem no qual você está implantando, o **Ambiente**, a **Configuração de Build** e a **Configuração do Serviço** estão todos corretos.

    ![Publicar aplicativo do Azure – configurações comuns](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

1. Na guia **Configurações Avançadas**, verifique se a conta de armazenamento e o rótulo de implantação estão corretos, se a caixa de seleção **Excluir implantação em caso de falha** está desmarcada e se a caixa de seleção **Atualização de implantação** está marcada. Ao marcar a caixa de seleção **Atualização de implantação**, você garante que a implantação não será excluída e que o VIP não será perdido quando você republicar o aplicativo. Ao limpar a **caixa de seleção Excluir implantação em caso de falha**, você garante que o VIP não será perdido se ocorrer um erro durante a implantação.

    ![Publicar aplicativo do Azure – configurações avançadas](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

1. (Opcional) Para especificações adicionais de como você deseja que as funções sejam atualizadas, selecione **Configurações**, ao lado de **Atualização de implantação**. Selecione **Atualização incremental** ou **Atualização simultânea**. Se você escolher **Atualização incremental**, cada instância será atualizada uma após a outra, de modo que o aplicativo estará sempre disponível. Se você escolher **Atualização simultânea**, todas as instâncias serão atualizadas ao mesmo tempo. A atualização simultânea é mais rápida, mas o serviço pode não estar disponível durante o processo de atualização. Quando terminar, escolha **Avançar**.

    ![Publicar aplicativo do Azure – configurações de atualização de implantação](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

1. Ao retornar à caixa de diálogo **Publicar Aplicativo do Azure**, selecione **Avançar** até que a página **Resumo** seja exibida. Verifique as configurações e, em seguida, selecione **Publicar**.
   
    ![Publicar aplicativo do Azure –resumo](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Próximas etapas
- [Usando o Assistente Publicar Aplicativo do Azure do Visual Studio](vs-azure-tools-publish-azure-application-wizard.md)


