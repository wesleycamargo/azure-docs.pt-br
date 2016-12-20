---
title: "Tarefas comuns de gerenciamento de serviço de nuvem | Microsoft Docs"
description: "Saiba como gerenciar serviços de nuvem no portal do Azure. Esses exemplos usam o portal do Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 2501b6480e81b236995c37db7171a4ed1429dcbf
ms.openlocfilehash: 3400c137088800398e3757ecfcfb5007044ea66c


---
# <a name="how-to-manage-cloud-services"></a>Como gerenciar serviços de nuvem
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-how-to-manage-portal.md)
> * [Portal clássico do Azure](cloud-services-how-to-manage.md)
> 
> 

O serviço de nuvem é gerenciado na área **Serviços de Nuvem (clássico)** do portal do Azure. Este artigo descreve algumas ações comuns necessárias ao gerenciamento dos serviços de nuvem. Que inclui a atualização, a exclusão, o dimensionamento e a promoção de uma implantação de teste para produção.

Obtenha mais informações sobre como dimensionar seu serviço de nuvem [aqui](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Como atualizar uma função ou implantação de Serviço de Nuvem
Se você precisar atualizar o código do aplicativo para o seu serviço de nuvem, use **Atualizar** na folha do serviço de nuvem. Você pode atualizar única função ou todas as funções. Para atualizar, você pode carregar um novo pacote de serviços ou o arquivo de configuração do serviço.

1. No [Portal do Azure][Portal do Azure], selecione o serviço de nuvem que você deseja atualizar. Esta etapa abrirá a folha da instância do serviço de nuvem.
2. Na folha, clique no botão **Atualizar** .
   
    ![Botão Atualizar](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Atualize a implantação com um novo arquivo de pacote de serviço (.cspkg) e o arquivo de configuração de serviço (.cscfg).
   
    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Como opção** , atualize o rótulo de implantação e a conta de armazenamento. 
5. Se alguma das funções tiver uma instância de função, selecione **Implantar mesmo se uma ou mais funções contiverem uma única instância** para permitir que a atualização continue. 
   
    O Azure pode garantir apenas 99,95 por cento de disponibilidade do Serviço de Nuvem durante uma atualização do Serviço de Nuvem se cada função tiver pelo menos duas instâncias de função (máquinas virtuais). Com duas instâncias de função, uma máquina virtual processará as solicitações do cliente enquanto a outra é atualizada.
    
6. Marque a opção **Iniciar implantação** para que a atualização seja aplicada após terminar o upload do pacote.
7. Clique em **OK** para iniciar a atualização do serviço.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Como permutar implantações para promover a implantação de preparo para produção
Ao optar por implantar uma nova versão do serviço de nuvem, prepare e teste a nova versão em seu ambiente de preparo. Use **Permutar** para alternar as URLs pelas quais as duas implantações são endereçadas e para promover uma nova versão para produção. 

É possível permutar implantações na página **Serviços de Nuvem** ou no painel.

1. No [Portal do Azure][Portal do Azure], selecione o serviço de nuvem que você deseja atualizar. Esta etapa abrirá a folha da instância do serviço de nuvem.
2. Na folha, clique o botão **Trocar** .
   
    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. O seguinte aviso de confirmação é aberto.
   
    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Após verificar as informações da implantação, clique em **OK** para trocar as implantações.
   
    A permuta da implantação acontece rapidamente pois a única coisa alterada são os endereços IP virtuais (VIP) para as implantações.
   
    Para economizar custos de computação, você pode excluir a implantação de preparo após verificar que sua implantação de produção está funcionando conforme o esperado.

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Como vincular um recurso a um Serviço de Nuvem
O portal do Azure não vinculada recursos como o portal clássico do Azure atual. Em vez disso, implante recursos adicionais para o mesmo grupo de recursos que está sendo usado pelo Serviço de Nuvem.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Como excluir implantações e um Serviço de Nuvem
Antes de poder excluir um serviço de nuvem, você deve excluir cada implantação existente.

Para economizar custos de computação, você pode excluir a implantação de preparo após verificar que sua implantação de produção está funcionando conforme o esperado. Você será cobrado por custos de computação de instâncias de função implantadas que estejam paradas.

Use o procedimento a seguir para excluir uma implantação ou seu serviço de nuvem. 

1. No [Portal do Azure][Portal do Azure], selecione o serviço de nuvem que você deseja excluir. Esta etapa abrirá a folha da instância do serviço de nuvem.
2. Na folha, clique no botão **Excluir** .
   
    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Você pode excluir o serviço de nuvem inteiro marcando **Serviços de nuvem e suas implantações** ou escolhendo **Implantação de produção** ou **Implantação de preparo**.
   
    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/delete-blade.png) 

4. Clique no botão **Excluir** na parte inferior.
5. Para excluir o Serviço de Nuvem, clique em **Excluir o Serviço de Nuvem**. Em seguida, clique em **Sim**no prompt de confirmação.

> [!NOTE]
> Quando um serviço de nuvem for excluído, e o monitoramento detalhado estiver configurado, você deverá excluir manualmente os dados de sua conta de armazenamento. Para obter informações sobre onde encontrar as tabelas de métricas, consulte [este](cloud-services-how-to-monitor.md) artigo.
> 
> 

[Portal do Azure]: https://portal.azure.com

## <a name="next-steps"></a>Próximas etapas
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).




<!--HONumber=Nov16_HO3-->


