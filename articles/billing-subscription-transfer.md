---
title: Transferindo a propriedade de uma assinatura do Azure | Microsoft Docs
description: "Como transferir uma assinatura do Azure para outro usuário e algumas Perguntas Frequentes sobre o processo"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: f7b097883ca0340a3af83fc9531acfe571295994
ms.openlocfilehash: c0323a63676a784660146f84a7813cf2cd5de7b0


---
# <a name="transferring-ownership-of-an-azure-subscription"></a>Transferindo a propriedade de uma assinatura do Azure

É possível transferir facilmente sua assinatura a outro usuário no caso de assinaturas pré-pagas, do MSDN, do Action Pack ou do BizSpark. Você pode alterar o administrador da conta em qualquer assinatura pré-paga, do MSDN, do Action Pack ou do BizSpark que possuir, não importa o país em que você esteja localizado. Nós damos suporte à transferência de compras do Azure Marketplace para esses tipos de assinatura também. 

Talvez você queira transferir a propriedade de uma assinatura do Azure se você:

* Precisar passar a responsabilidade pela cobrança de sua assinatura do Azure para outra pessoa.
* Quiser alterar a conta usada para se inscrever no Azure. Talvez você tenha usado sua conta da Microsoft, mas na verdade gostaria de ter usado sua conta corporativa ou de estudante.
* Quiser mover sua assinatura do Azure de um diretório para outro.
* Tiver o Azure e o Office 365 em locatários diferentes e quiser consolidá-los.

Para alterar sua assinatura para uma oferta diferente, consulte [Trocar a assinatura do Azure por outra oferta](billing-how-to-switch-azure-offer.md). 

## <a name="how-to-transfer-ownership-of-an-azure-subscription"></a>Como transferir a propriedade de uma assinatura do Azure
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Entre em <https://account.windowsazure.com/Subscriptions>. Você precisa ser administrador da conta para executar uma transferência de propriedade. Para obter mais informações sobre como descobrir quem é o administrador da conta da assinatura, consulte as [Perguntas frequentes](#faq).
2. Selecione a assinatura para transferir.
3. Clique na opção **Transferir assinatura** .

   ![Guia de assinaturas de conta do Azure](./media/billing-subscription-transfer/image1.png)
4. Siga os prompts para especificar o destinatário.

   ![Caixa de diálogo de assinatura de transferência](./media/billing-subscription-transfer/image2.PNG)
5. O destinatário recebe automaticamente um email com um link de aceitação.

   ![Email de transferência de assinatura para o destinatário](./media/billing-subscription-transfer/image3.png)
6. O destinatário clica no link e segue as instruções, incluindo inserir suas informações de pagamento.

   ![Primeira página da Web de transferência de assinatura](./media/billing-subscription-transfer/image4.png)

   ![Segunda página da Web de transferência de assinatura](./media/billing-subscription-transfer/image5.png)
7. Sucesso! Agora a assinatura será transferida.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)
* <a name="whoisaa"></a> **Quem é o administrador da conta da assinatura?**

  O administrador da conta é a pessoa que assinou ou comprou a assinatura do Azure. Ele está autorizado a acessar o [Centro de Contas](https://account.windowsazure.com/Home/Index) e a realizar várias tarefas de gerenciamento, como criar assinaturas, cancelar assinaturas, alterar a cobrança de uma assinatura ou alterar o administrador de serviços. Se você não tiver certeza de quem é o administrador da conta de uma assinatura, use as etapas a seguir para descobrir.

  1. Entre no [Portal do Azure](https://portal.azure.com).
  2. No menu Hub, selecione **Assinatura**.
  3. Selecione a assinatura que você deseja verificar e olhe as **Configurações**.
  4. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .  
* **Uma transferência de assinatura resulta em qualquer tempo de inatividade do serviço?**

  Não há nenhum impacto no serviço. Transferir a assinatura cancela a assinatura para o Administrador da Conta atual e cria uma assinatura na conta do destinatário. A nova assinatura é associada aos serviços do Azure subjacentes. A ID da assinatura permanece a mesma.
* **Como usar esse processo para alterar o diretório da assinatura?**   
  Uma assinatura do Azure é criada no diretório ao qual o Administrador da Conta pertence. Para alterar o diretório, transfira a assinatura para uma conta de usuário no diretório de destino. Quando o usuário conclui as etapas para aceitar a transferência, a assinatura passa automaticamente para o diretório de destino.
* **Se eu assumir a propriedade da cobrança de uma assinatura de outra organização, eles continuarão a ter acesso aos meus recursos?**

  Se a assinatura for transferida para outro locatário, os usuários associados ao locatário anterior perderão o acesso à assinatura. Mesmo que um usuário não seja mais um administrador ou coadministrador de serviços, ele ainda terá acesso à assinatura por meio de outros mecanismos de segurança. Estão incluídos:

  * Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para obter mais informações, consulte [criar e carregar um certificado de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
  * Teclas de acesso para serviços como Armazenamento. Para saber mais, consulte [Sobre as contas de Armazenamento do Azure](storage/storage-create-storage-account.md)
  * Credenciais de Acesso remoto para serviços como Máquinas Virtuais do Azure

  Esta não é uma lista completa. O destinatário deve considerar a atualização de qualquer segredo associado ao serviço, se for necessário restringir o acesso a seus recursos. A maioria dos recursos pode ser atualizada da seguinte maneira:

  1. Vá para o [Portal do Azure](https://portal.azure.com).
  2. No menu Hub, selecione **Todos os recursos**.
  3. Selecione o recurso. 
  4. Na folha de recursos, clique em **configurações**. Aqui você pode exibir e atualizar os segredos existentes.
* **Se eu transferir a assinatura no meio do ciclo de cobrança, o destinatário pagará por todo o ciclo de cobrança?**

  O remetente é responsável pelo pagamento por qualquer uso que foi relatado até o ponto no qual a transferência foi concluída. O destinatário é responsável por uso relatado a partir do momento da transferência em diante. Pode haver algum uso que ocorreu antes da transferência, mas foi relatado posteriormente. O uso é incluído na conta do destinatário.
* **O destinatário tem acesso ao histórico de cobrança e de uso?**

  As únicas informações disponíveis para o destinatário serão o valor da última conta ou, se a assinatura tiver sido transferida antes que a primeira conta fosse gerada, o saldo atual. O restante do histórico de cobrança e uso não é transferido com a assinatura.
* **A oferta pode ser alterada durante uma transferência?**

  A oferta deve permanecer a mesma. Para alterar sua oferta, você deve [entrar em contato com o suporte](http://go.microsoft.com/fwlink/?LinkID=619338).
* **Posso transferir uma assinatura para uma conta de usuário em outro país?**

  Não, não há suporte para transferir uma assinatura para uma conta de usuário em outro país. A conta de usuário do destinatário deve estar no mesmo país.
* **O destinatário pode usar um método de pagamento diferente?**

  Sim. Mas o histórico de cobrança da assinatura é dividido em duas contas.  

* **O método de pagamento será afetado depois que eu transferir uma assinatura do Azure?**

Para aceitar uma transferência de assinatura, um cartão de crédito ou um método de pagamento semelhante deve ser fornecido para pagar pela assinatura. Por exemplo, se Pedro transfere uma assinatura para Clara e ela aceita a transferência, Clara também deverá fornecer um método de pagamento que ela usará a pagar pela assinatura. Depois que a transferência for concluída, Pedro não será cobrado pela assinatura que transferiu para Clara.

* **Como faço para migrar dados e serviços da minha assinatura do Azure para uma nova assinatura?**

  Consulte [Mover recursos para um novo grupo de recursos ou uma nova assinatura](./azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Próximas etapas depois de aceitar a posse de uma assinatura
1. Agora, você é o Administrador da conta. Examine e atualize o Administrador de serviços e os Coadministradores. Gerencie administradores no [portal clássico do Azure](https://manage.windowsazure.com) acessando configurações. [Saiba mais](http://go.microsoft.com/fwlink/?LinkID=533293).
2. Você também pode usar o controle de acesso baseado em função (RBAC) para a sua assinatura e serviços. Visite o [Portal do Azure](https://portal.azure.com) [Saiba mais sobre o RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. Atualize as credenciais associadas aos serviços desta assinatura. Estão incluídos:
   * Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para saber mais, confira [Criar e carregar um certificado de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
   * Teclas de acesso para serviços como Armazenamento. Para saber mais, consulte [Sobre as contas de Armazenamento do Azure](storage/storage-create-storage-account.md)
   * Credenciais de Acesso remoto para serviços como Máquinas Virtuais do Azure
4. Atualizar alertas de cobrança para essa assinatura no [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions). [Saiba mais](http://go.microsoft.com/fwlink/?LinkID=533292)
5. Se estiver trabalhando com um parceiro, considere a atualização da ID do parceiro nesta assinatura. Você pode atualizar a ID do parceiro no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions).


## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente. 





<!--HONumber=Dec16_HO2-->


