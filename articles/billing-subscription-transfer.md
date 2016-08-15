<properties
   pageTitle="Transferindo a propriedade de uma assinatura do Azure | Microsoft Azure"
   description="Como transferir uma assinatura do Azure para outro usuário e algumas Perguntas Frequentes (FAQ) sobre o processo"
   services=""
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor=""
   tags="billing,top-support-issue"/>

<tags
   ms.service="billing"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="07/21/2016"
   ms.author="genli"/>

# Transferindo a propriedade de uma assinatura do Azure

Você:

- Precisa passar a cobrança de propriedade de sua assinatura do Azure para outra pessoa?
- Deseja alterar a conta usada para se inscrever no Azure? Talvez tenha usado sua conta da Microsoft, mas deve usar sua conta do trabalho ou da escola em vez disso?
- Deseja mover sua assinatura do Azure de um diretório para outro?
- Ter o Azure e o Office 365 em diferentes locatários e quer consolidar?

Agora você pode fazer isso facilmente no Centro de Contas do Microsoft Azure, para assinaturas pré-pagas, do MSDN, Action Pack ou BizSpark. Adicionamos a capacidade de transferir sua assinatura para outro usuário. Em outras palavras, agora você pode alterar o administrador da conta em qualquer assinatura pré-paga, do MSDN, Action Pack ou do BizSpark que você possuir, não importa o país em que você está localizado. Agora, oferecemos suporte a transferência de compras do Azure Marketplace para esses tipos de assinatura também.

> [AZURE.NOTE]  Para alterar sua assinatura para uma oferta diferente, consulte [Trocar sua assinatura do Azure por outra uma oferta](billing-how-to-switch-azure-offer.md) para obter mais informações.

> Você precisa ser administrador da conta para executar uma transferência de propriedade. Para saber mais sobre como descobrir quem é o administrador da conta da assinatura, confira as [Perguntas frequentes](#faq).

## Como transferir a propriedade de uma assinatura do Azure

> [AZURE.VIDEO transfer-an-azure-subscription]

1.  Entre em <https://account.windowsazure.com/Subscriptions>

2.  Selecione a assinatura para transferir.

3.  Clique na opção **Transferir assinatura**.

    ![Guia assinaturas de conta do Azure](./media/billing-subscription-transfer/image1.png)

4.  Siga os prompts para especificar o destinatário.

    ![Caixa de diálogo de assinatura de transferência](./media/billing-subscription-transfer/image2.PNG)

5.  O destinatário obtém automaticamente um email com um link de aceitação.

    ![Email de transferência de assinatura para o destinatário](./media/billing-subscription-transfer/image3.png)

6.  O destinatário clica no link e segue as instruções, incluindo inserir suas informações de pagamento.

    ![Primeira página da Web de transferência de assinatura](./media/billing-subscription-transfer/image4.PNG)

    ![Segunda página da Web de transferência de assinatura](./media/billing-subscription-transfer/image5.PNG)

7. Sucesso! Agora a assinatura será transferida.

<a id="faq"></a>
## Perguntas frequentes (FAQ)

-   **Como posso saber quem é o administrador da conta da assinatura?**

    Você pode confirmar que é o administrador da conta da assinatura da seguinte maneira:

    1. Entre no [Portal do Azure](https://portal.azure.com).
    2. No menu Hub, selecione **Assinatura**.
    3. Selecione a assinatura que você deseja verificar e, em seguida, selecione **Configurações**.
    4. Escolha **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta**.

-   **Uma transferência de assinatura resulta em qualquer tempo de inatividade do serviço?**

    Não há nenhum impacto no serviço. Isso efetivamente cancela a assinatura do atual administrador da conta e cria uma nova na conta do destinatário, mas associa os serviços do Azure subjacentes à nova assinatura. A ID da assinatura permanece a mesma.

-   **Como usar esse mecanismo para alterar o diretório para assinatura?**-Uma assinatura do Azure é criada no diretório ao qual o administrador da conta pertence. Portanto, para alterar o diretório, basta transferir a assinatura para uma conta de usuário no diretório de destino. Quando o usuário conclui as etapas para aceitar a transferência, a assinatura avançará automaticamente para o diretório de destino.

-   **Se eu assumir a propriedade de cobrança de uma assinatura de outra organização, eles continuarão a ter acesso aos meus recursos?**

    Se a assinatura é transferida para outro locatário, os usuários associados ao locatário anterior perdem o acesso à assinatura. Mesmo que um usuário não seja mais um administrador ou coadministrador de serviços, ele ainda terá acesso à assinatura por meio de outros mecanismos de segurança. Estão incluídos:
    - Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para obter mais informações, consulte [criar e carregar um certificado de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -	Teclas de acesso para serviços como Armazenamento. Para obter mais informações, consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -	Credenciais de Acesso remoto para serviços como Máquinas Virtuais do Azure

    Esta não é uma lista completa. O destinatário deve considerar a atualização de qualquer segredo associado ao serviço, se for necessário restringir o acesso a seus recursos. A maioria dos recursos pode ser atualizada da seguinte maneira:

    1.   Abra o Portal do Azure em [*https://portal.azure.com*](https://portal.azure.com)

    2.    Clique em Procurar tudo - & gt; Todos os recursos

    3.    Selecione o recurso. Isso abrirá a folha de recursos.

    4.    Na folha de recursos, clique em **configurações**. Aqui você pode exibir e atualizar os segredos existentes.


-   **Se eu transferir a assinatura no meio do ciclo de cobrança, o destinatário paga por todo o ciclo de cobrança?**

    O remetente é responsável pelo pagamento por qualquer uso que foi relatado até o ponto no qual a transferência foi concluída. O destinatário é responsável por uso relatado a partir do momento da transferência em diante. Pode haver algum uso que ocorreu antes da transferência, mas foi relatado posteriormente. Isso será incluído na lista do destinatário.

-   **O destinatário tem acesso ao histórico de cobrança e de uso?**

    Neste momento, as únicas informações reveladas para o destinatário são o valor da última conta (ou o saldo atual, se a assinatura foi transferida antes da geração da primeira lista). O restante do histórico de cobrança e uso não é transferido com a assinatura.

-   **A oferta pode ser alterada durante uma transferência?**

    A oferta deve permanecer a mesma. Para alterar sua oferta, você deve [entrar em contato com o suporte](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Posso transferir uma assinatura para uma conta de usuário em outro país?**

    Não, desta vez. Não há suporte para isso. A conta de usuário do destinatário deve estar no mesmo país.

-   **O destinatário pode usar um mecanismo de pagamento diferente?**

    Sim. Há limitações aqui: agora o histórico de cobrança da assinatura é dividido em duas contas. Mas a vantagem é que você pode fazer isso sem precisar [entre em contato com o suporte](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **O método de pagamento será afetado depois que eu transferir uma assinatura do Azure?**

    Para aceitar uma transferência de assinatura, um cartão de crédito ou o método de pagamento semelhante deve ser fornecido para pagar pela assinatura. Por exemplo, se Pedro transfere uma assinatura para Clara e ela aceita a transferência, Clara também deve fornecer um método de pagamento que ela usará a pagar pela assinatura. Depois que a transferência for concluída, Pedro não será cobrado pela assinatura que ele transferiu para Clara.

## Próximas etapas depois de aceitar a posse de uma assinatura

1. Agora, você é o Administrador da conta. Examine e atualize o Administrador de serviços e os Coadministradores. Gerencie administradores no [portal clássico do Azure](https://manage.windowsazure.com) acessando configurações. [Saiba mais](http://go.microsoft.com/fwlink/?LinkID=533293).
2. Você também pode usar o controle de acesso baseado em função (RBAC) para a sua assinatura e serviços. Visite o [portal do Azure](https://portal.azure.com) [Saiba mais sobre o RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. Atualize as credenciais associadas aos serviços desta assinatura. Estão incluídos:
    - Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para saber mais, confira [Criar e carregar um certificado de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -	Teclas de acesso para serviços como Armazenamento. Para obter mais informações, consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -	Credenciais de Acesso remoto para serviços como Máquinas Virtuais do Azure
4. Atualizar alertas de cobrança para essa assinatura no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions) [Saiba mais](http://go.microsoft.com/fwlink/?LinkID=533292)
5. 	Se estiver trabalhando com um parceiro, considere a atualização da ID do parceiro nesta assinatura. Você pode fazê-lo no [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions).

<!---HONumber=AcomDC_0803_2016-->