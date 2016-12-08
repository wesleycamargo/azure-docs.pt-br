---
title: "Como reter um endereço IP virtual constante para um serviço de nuvem | Microsoft Docs"
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
ms.date: 08/15/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fb9635bfe1111189d72b68b24ac0bfbee8e87203


---
# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>Como manter um endereço IP virtual constante para um serviço de nuvem
Quando você atualiza um serviço de nuvem que é hospedado no Azure, você precisará garantir que o VIP (endereço IP virtual) do serviço não mude. Muitos serviços de gerenciamento de domínio usam o DNS (sistema de nome de domínio) para o registro de nomes de domínio. O DNS só funciona se o VIP permanece o mesmo. Você pode usar o **Assistente de Publicação** nas Ferramentas do Azure para garantir que o VIP do seu serviço de nuvem não seja alterado quando você atualizá-lo. Para obter mais informações sobre como usar o gerenciamento de domínio DNS para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publicando um serviço de nuvem sem alterar seu VIP
O VIP de um serviço de nuvem é alocado ao implantá-lo pela primeira vez no Azure em um ambiente específico, como o ambiente de Produção. O VIP não muda a menos que você exclua a implantação explicitamente ou que ela seja excluída implicitamente pelo processo de atualização de implantação. Para manter o VIP, você não pode excluir sua implantação e deve certificar-se também de que ela não seja excluída automaticamente pelo Visual Studio. Você pode controlar o comportamento especificando configurações de implantação no **Assistente de Publicação**, que dá suporte a várias opções de implantação. Você pode especificar uma nova implantação ou uma implantação de atualização, que pode ser incremental ou simultânea; ambos estes tipos de implantação de atualização retêm o VIP. Para obter definições desses tipos diferentes de implantação, consulte o [Assistente Publicar Aplicativo no Azure](vs-azure-tools-publish-azure-application-wizard.md).  Além disso, você pode controlar se a implantação anterior de um serviço de nuvem deve ou não ser excluída caso um erro ocorra. O VIP pode mudar inesperadamente se você não definir essa opção corretamente.

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>Para atualizar um serviço de nuvem sem alterar seu VIP
1. Depois que você tiver implantado o serviço de nuvem pelo menos uma vez, abra o menu de atalho do nó para o seu projeto do Azure e escolha **Publicar**. O assistente **Publicar Aplicativo do Azure** é exibido.
2. Na lista de assinaturas, escolha aquela que você deseja implantar e, em seguida, escolha o botão **Avançar** . A página **Configurações** do assistente é exibida.
3. Na guia **Configurações Comuns**, verifique se que o nome do serviço de nuvem que você está implantando, o **Ambiente**, a **Configuração de build** e a **Configuração do Serviço** estão todos corretos.
4. Na guia **Configurações Avançadas**, certifique-se que a conta de armazenamento e o rótulo de implantação estão corretos, que a caixa de seleção **Excluir implantação em caso de falha** está desmarcada e que a caixa de seleção de atualização de **Implantação** está selecionada. Selecionando a caixa de seleção de atualização de **Implantação** , você assegura que sua implantação não será excluída e seu VIP não será perdido quando você republicar o aplicativo. Ao limpar a **caixa de seleção Excluir implantação em caso de falha**, você assegura que seu VIP não será perdido se ocorrer um erro durante a implantação.
5. Para especificações adicionais de como você deseja que as funções sejam atualizadas, escolha o link **Configurações** ao lado da caixa **Atualização de implantação** e, em seguida, escolha a opção de atualização simultânea em ou de atualização incremental na caixa de diálogo de configurações da **Atualização de implantação**. Se você escolher atualização incremental, cada instância será atualizada uma após a outra, de modo que o aplicativo estará sempre disponível. Se você escolher atualização simultânea, todas as instâncias serão atualizadas ao mesmo tempo. A atualização simultânea é mais rápida, mas o serviço pode não estar disponível durante o processo de atualização.
6. Quando estiver satisfeito com suas configurações, escolha o botão **Avançar** .
7. Na página **Resumo** do assistente, verifique suas configurações e escolha o botão **Publicar**.
   
   > [!WARNING]
   > Se a implantação falhar, você deve solucionar o motivo da falha e reimplantar imediatamente, para evitar deixar seu serviço de nuvem em um estado corrompido.
   > 
   > 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a publicação do Azure por meio do Visual Studio, consulte o [assistente Publicar aplicativo do Azure](vs-azure-tools-publish-azure-application-wizard.md).




<!--HONumber=Nov16_HO3-->


