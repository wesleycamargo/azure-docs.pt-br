<properties 
	pageTitle="Como gerenciar um serviço de nuvem | Microsoft Azure" 
	description="Saiba como gerenciar serviços de nuvem no portal do Azure. Esses exemplos usam o portal do Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015"
	ms.author="adegeo"/>


# Como gerenciar serviços de nuvem

> [AZURE.SELECTOR]
- [Azure classic portal](cloud-services-how-to-manage.md)
- [Azure portal](cloud-services-how-to-manage-portal.md)

Na área **Serviços de Nuvem** do portal do Azure, você pode atualizar uma função de serviço ou uma implantação, promover uma implantação de preparo para produção, vincular recursos ao Serviço de Nuvem para que você possa ver as dependências de recursos e dimensionar os recursos em conjunto, além de excluir um Serviço de Nuvem ou uma implantação.


## Como atualizar uma função ou implantação de Serviço de Nuvem

Se você precisar atualizar o código do aplicativo para o seu serviço de nuvem, use **Atualizar** na folha do serviço de nuvem. Você pode atualizar única função ou todas as funções. Você precisará carregar um novo pacote de serviços e o arquivo de configuração do serviço.

1. No [portal do Azure][], selecione o serviço de nuvem que você deseja atualizar. Isso abrirá a folha da instância do serviço de nuvem.

2. Na folha, clique no botão **Atualizar**.

    ![Botão Atualizar](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Atualize a implantação com um novo arquivo de pacote de serviço (.cspkg) e o arquivo de configuração de serviço (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Como opção**, atualize o rótulo de implantação e a conta de armazenamento.

5. Se a atualização alterar o número de funções ou o tamanho de qualquer função, marque a caixa de seleção **Permitir a atualização se os tamanhos ou o número de funções for alterado** para permitir que a atualização continue.

	>[AZURE.WARNING]Lembre-se que, se você alterar o tamanho de uma função (u seja, o tamanho de uma máquina virtual que hospede uma instância de função) ou o número de funções, cada instância de função (máquina virtual) deverá ter outra imagem criada e todos os dados locais serão perdidos.

6. Se alguma das funções de serviço tiver uma instância de função, marque a caixa de seleção **Atualizar mesmo se uma ou mais funções contiverem uma única instância** para permitir que a atualização continue.

	O Azure pode garantir apenas 99,95 por cento de disponibilidade do Serviço de Nuvem durante uma atualização do Serviço de Nuvem se cada função tiver pelo menos duas instâncias de função (máquinas virtuais). Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada.

8. Clique em **OK** para iniciar a atualização do serviço.



## Como permutar implantações para promover a implantação de preparo para produção

Use **Permutar** para promover uma implantação de preparo de um Serviço de Nuvem para produção. Ao optar por implantar uma nova versão do serviço de nuvem, você pode preparar e testar a nova versão em seu ambiente de preparo do serviço em nuvem enquanto os clientes estão usando a versão atual em produção. Quando estiver pronto para promover a nova versão para produção, você poderá usar **Permutar** para alternar as URLs pelas quais as duas implantações são endereçadas.

É possível permutar implantações na página **Serviços de Nuvem** ou no painel.

1. No [portal do Azure][], selecione o serviço de nuvem que você deseja atualizar. Isso abrirá a folha da instância do serviço de nuvem.

2. Na folha, clique o botão **Trocar**.

    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. O seguinte aviso de confirmação é aberto.

	![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Após verificar as informações da implantação, clique em **OK** para trocar as implantações.

	A permuta da implantação acontece rapidamente pois a única coisa alterada são os endereços IP virtuais (VIP) para as implantações.

	Para economizar custos de computação, você pode excluir a implantação no ambiente de preparo quando tiver certeza que a nova implantação de produção está funcionado como o esperado.

## Como vincular um recurso a um Serviço de Nuvem

O portal do Azure não vinculada recursos como o portal clássico do Azure atual. Em vez disso, você deve implantar recursos adicionais para o mesmo grupo de recursos que está sendo usado pelo Serviço de Nuvem.

## Como excluir implantações e um Serviço de Nuvem

Antes de poder excluir um serviço de nuvem, você deve excluir cada implantação existente.

Para economizar custos de computação, você pode excluir a implantação de preparo após verificar que sua implantação de produção está funcionando conforme o esperado. Você é cobrado pelos custos de computação das instâncias de função mesmo que um Serviço de Nuvem não esteja em execução.

Use o procedimento a seguir para excluir uma implantação ou seu serviço de nuvem.

1. No [portal do Azure][], selecione o serviço de nuvem que você deseja excluir. Isso abrirá a folha da instância do serviço de nuvem.

2. Na folha, clique no botão **Excluir**.

    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Você pode excluir o serviço de nuvem inteiro marcando **Serviços de nuvem e suas implantações** ou escolhendo **Implantação de produção** ou **Implantação de preparo**.

    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Clique no botão **Excluir** na parte inferior.

5. Para excluir o Serviço de Nuvem, clique em **Excluir o Serviço de Nuvem**. Em seguida, clique em **Sim** no prompt de confirmação.

> [AZURE.NOTE]Se o monitoramento detalhado estiver configurado para seu serviço de nuvem, o Azure não exclui os dados de monitoramento de sua conta de armazenamento quando você exclui o serviço de nuvem. Você precisará excluir manualmente os dados. Para obter informações sobre onde encontrar as tabelas de métricas, consulte [este](cloud-services-how-to-monitor.md) artigo.

[portal do Azure]: https://portal.azure.com

## Próximas etapas

* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_1203_2015-->