---
title: 'Portal do Azure: criar Instância Gerenciada do Banco de Dados SQL | Microsoft Docs'
description: Crie uma Instância Gerenciada do Banco de Dados SQL em uma rede virtual e use o SSMS para restaurar o backup do banco de dados da Wide World Importers.
keywords: tutorial banco de dados sql, criar uma instância gerenciada do banco de dados sql
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 03/14/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 774a761465cfd886b85378a35dd43ac656a7ee48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Criar uma Instância Gerenciada do Banco de Dados SQL no portal do Azure

Este tutorial demonstra como criar uma Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia) usando o portal do Azure em uma sub-rede dedicada de uma VNet (rede virtual). Ele mostra como conectar-se à Instância Gerenciada usando o SQL Server Management Studio em uma máquina virtual na mesma VNet e restaurar um backup, de um banco de dados armazenado no armazenamento de blobs do Azure, na Instância Gerenciada.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT]
> Para obter uma lista de regiões nas quais a Instância Gerenciada está disponível no momento, consulte [Migrar os bancos de dados para um serviço totalmente gerenciado com a Instância Gerenciada do Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="whitelist-your-subscription"></a>Colocar sua assinatura na lista de permissões

A Instância Gerenciada está sendo lançada inicialmente como uma versão prévia pública controlada que requer sua assinatura na lista de permissões. Se sua assinatura não ainda estiver na lista de permissões, use as etapas a seguir para receber e aceitar os termos de versão prévia e enviar uma solicitação de lista de permissões.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Localize **Instância Gerenciada** e selecione **Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia)**.
3. Clique em **Criar**.

   ![managed instance create](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Selecione sua assinatura, clique em **Termos da versão prévia**e forneça as informações solicitadas.

   ![managed instance preview terms](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. Clique em **OK** quando concluir.

   ![managed instance preview terms](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > Enquanto aguarda a aprovação da versão prévia, você pode continuar e concluir as próximas seções deste tutorial.

## <a name="configure-a-virtual-network-vnet"></a>Configurar uma VNet (rede virtual)

As etapas a seguir mostram como criar uma nova VNet (rede virtual) do [ Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) para uso pela Instância Gerenciada. Para saber mais sobre a configuração de rede virtual, confira [Configuração de VNet de Instância Gerenciada](sql-database-managed-instance-vnet-configuration.md).

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Localize e, em seguida, clique em **Rede Virtual**, verifique se o **Gerenciador de Recursos** está selecionado como o modo de implantação e, em seguida, clique em **Criar**.

   ![virtual network create](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Preencha o formulário de rede virtual com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Espaço de endereço**|Um intervalo de endereços válido, como 10.14.0.0/24|Nome do endereço da rede virtual na notação CIDR.|
   |**Assinatura**|Sua assinatura|Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions).|
   |**Grupo de recursos**|Um grupo de recursos válido (novo ou existente)|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Localidade**|Qualquer local válido| Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).|
   |**Nome da sub-rede**|Qualquer nome de sub-rede válido, como mi_subnet|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Intervalo de endereços da sub-rede**|Um endereço de sub-rede válido, como 10.14.0.0/28. Use um espaço de endereço de sub-rede menor do que o próprio espaço de endereço para permitir sobrar espaço para criar outras sub-redes na mesma VNet, como uma sub-rede para a hospedagem de aplicativos de teste/de cliente ou sub-redes de gateway para conectar-se localmente ou de outras VNets.|O intervalo de endereços da sub-rede na notação CIDR. Ele deve estar contido no espaço de endereço da rede virtual|
   |**Pontos de extremidade de serviço**|Desabilitado|Habilitar um ou mais pontos de extremidade de serviço para a sub-rede|
   ||||

   ![formulário de criação de rede virtual](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Clique em **Criar**.

## <a name="create-new-route-table-and-a-route"></a>Criar nova tabela de rota e uma rota

As etapas a seguir mostram como criar uma rota de Internet de Próximo Salto 0.0.0.0/0.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Localize e clique em **Tabela de rotas** e, em seguida, clique em **Criar** na página Tabela de rotas. 

   ![route table create](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. Preencha o formulário de tabela de rotas com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Assinatura**|Sua assinatura|Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions).|
   |**Grupo de recursos**|Selecionar o grupo de recursos que você criou no procedimento anterior|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Localidade**|Selecionar o local que você especificou no procedimento anterior| Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).|
   |**Desabilitar a propagação de rotas BCP**|Desabilitado||
   ||||

   ![formulário de criação da tabela de rotas](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. Clique em **Criar**.
5. Abra a tabela de rotas quando terminar de ser criada.

   ![route table](./media/sql-database-managed-instance-tutorial/route-table.png)

6. Clique em **Rotas** e em **Adicionar**.

   ![route table add](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  Adicione **Rotas de Internet de Próximo Salto 0.0.0.0/0** como a **única** rota usando as informações na tabela abaixo:

    | Configuração| Valor sugerido | DESCRIÇÃO |
    | ------ | --------------- | ----------- |
    |**Nome da rota**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
    |**Prefixo de endereço**|0.0.0.0/0|O endereço IP de destino na notação CIDR que se aplica a essa rota.|
    |**Tipo do próximo salto**|Internet|O próximo salto lida com os pacotes correspondentes para essa rota|
    |||

    ![rota](./media/sql-database-managed-instance-tutorial/route.png)

8. Clique em **OK**.

## <a name="to-apply-the-route-table-to-the-managed-instance-subnet"></a>Para aplicar a tabela de rotas à sub-rede da Instância Gerenciada

As etapas a seguir mostram como definir a nova tabela de rotas na sub-rede da Instância Gerenciada.

1. Para definir a tabela de rotas na sub-rede da Instância Gerenciada, abra a rede virtual que você criou anteriormente.
2. Clique em **sub-redes** e clique na sub-rede da Instância Gerenciada (**mi_subnet** na captura de tela a seguir).

    ![subnet](./media/sql-database-managed-instance-tutorial/subnet.png)

11. Clique em **Tabela de rotas** e selecione **myMI_route_table**.

    ![definir tabela de rotas](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. Clique em **Salvar**

    ![definir tabela de rotas - salvar](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>Criar uma Instância Gerenciada

As etapas a seguir mostram como criar sua Instância Gerenciada depois que a versão prévia é aprovada.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Localize **Instância Gerenciada** e selecione **Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia)**.
3. Clique em **Criar**.

   ![managed instance create](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Selecione sua assinatura e verifique se os termos da versão prévia mostram a opção **Aceito**.

   ![managed instance preview accepted](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. Preencha o formulário de Instância Gerenciada com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | DESCRIÇÃO |
   | ------ | --------------- | ----------- |
   |**Nome da Instância Gerenciada**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Logon de administrador da Instância Gerenciada**|Um nome de usuário válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Senha**|Qualquer senha válida|A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Grupo de recursos**|O grupo de recursos criado anteriormente||
   |**Localidade**|O local selecionado anteriormente|Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).|
   |**Rede virtual**|A rede virtual que você criou anteriormente|

   ![managed instance create form](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. Clique em **Tipo de preço** para dimensionar os recursos de computação e armazenamento e revisar as opções de tipo de preço. Por padrão, a instância obtém 32 GB de espaço de armazenamento sem custo adicional, que podem não ser suficientes para seus aplicativos.
6. Use os controles deslizantes ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais. 
   ![managed instance create form](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. Ao concluir, clique em **Aplicar** para salvar sua seleção.  
8. Clique em **Criar** para implantar a Instância Gerenciada.
9. Clique no ícone **Notificações** para exibir o status da implantação.
 
   ![deployment progress](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. Clique em **Implantação em andamento** para abrir a janela Instância Gerenciada e monitorar melhor o progresso da implantação.
 
   ![deployment progress 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Enquanto ocorre a implantação, continue no próximo procedimento.

> [!IMPORTANT]
> Para a primeira instância em uma sub-rede, o tempo de implantação é normalmente muito maior do que no caso de instâncias subsequentes; às vezes, mais de 24 horas até a conclusão. Não cancele a operação de implantação porque ela dura mais que o esperado. Esse período de tempo para implantar a primeira instância é uma situação temporária. Espere uma redução significativa do tempo de implantação logo após o início da versão prévia pública.

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>Criar uma nova sub-rede na rede virtual para uma máquina virtual

As etapas a seguir mostram como criar uma segunda sub-rede na rede virtual para uma máquina virtual na qual você instala o SQL Server Management Studio e conecta à sua Instância Gerenciada.

1. Abra o recurso de rede virtual.
 
   ![VNET](./media/sql-database-managed-instance-tutorial/vnet.png)

2. Clique em **Sub-redes** e em **+Sub-rede**.
 
   ![add subnet](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. Preencha o formulário de sub-rede com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Intervalo de endereços (bloco CIDR)**|Um intervalo de endereços válido na VNet (use o padrão)||
   |**Grupo de segurança de rede**|Nenhum||
   |**Tabela de rotas**|Nenhum||
   |**Pontos de extremidade de serviço**|Nenhum||

   ![vm subnet details](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. Clique em **OK**.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Criar uma máquina virtual na nova sub-rede na rede virtual

As etapas a seguir mostram como criar uma máquina virtual na mesma rede virtual na qual a Instância Gerenciada está sendo criada. 

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter** ou **Windows 10**. Esta seção do tutorial está usando o Windows Server. A configuração no Windows 10 é bastante semelhante. 

   ![compute](./media/sql-database-managed-instance-tutorial/compute.png)

3. Preencha o formulário de máquina virtual com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Tipo de disco da VM**|SSD|Os SSDs fornecem o melhor custo-benefício de preço e desempenho.|   
   |**Nome de usuário**|Um nome de usuário válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Senha**|Qualquer senha válida|A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).| 
   |**Assinatura**|Sua assinatura|Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions).|
   |**Grupo de recursos**|O grupo de recursos criado anteriormente||
   |**Localidade**|O local selecionado anteriormente||
   |**Já tem uma licença do Windows**|Não |Se você possui licenças do Windows com SA (Assistência de Software) ativa, use o Benefício Híbrido do Azure para economizar custo computacional|
   ||||

   ![virtual machine create form](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

3. Clique em **OK**.
4. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. Para este tutorial, você só precisa de uma máquina virtual pequena.

    ![Tamanhos de VM](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. Clique em **Selecionar**.
6. No formulário **Configurações**, clique em **sub-rede** e selecione **vm_subnet**. Não escolha a sub-rede em que a Instância Gerenciada é provisionada; em vez disso, escolha outra sub-rede na mesma rede virtual.

    ![Configurações da VM](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

7. Clique em **OK**.
8. Na página Resumo, analise os detalhes da oferta e clique em **Criar** para iniciar a implantação da máquina virtual.
 
## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

As etapas a seguir mostram como se conectar à sua máquina virtual recém-criada usando uma conexão de área de trabalho remota.

1. Após a conclusão da implantação, vá para o recurso da máquina virtual.

    ![VM](./media/sql-database-managed-instance-tutorial/vm.png)  

2. Clique no botão **Conectar** nas propriedades da máquina virtual. Um arquivo do protocolo RDP (.rdp) é criado e baixado.
3. Para se conectar à sua VM, abra o arquivo RDP baixado. 
4. Se solicitado, clique em **Conectar**. Em um Mac, você precisa de um cliente RDP, como este [Cliente de Área de Trabalho Remota](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) da Mac App Store.

5. Insira o nome de usuário e a senha especificados na criação da máquina virtual e clique em **Ok**.

6. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.

Você está conectado à máquina virtual no painel do Gerenciador do Servidor.

> [!IMPORTANT]
> Não continue até que a Instância Gerenciada seja provisionada com êxito. Depois que ela é provisionada, recupere o nome do host da instância no campo **Instância Gerenciada** da guia **Visão Geral** da Instância Gerenciada. O nome é semelhante a este: **drfadfadsfd.tr23.westus1 a.worker.database.windows.net**.

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>Instalar o SSMS e conectar-se à Instância Gerenciada

As etapas a seguir mostram como baixar e instalar o SSMS e conectar-se à sua Instância Gerenciada.

1. No Gerenciador do Servidor, clique em **Servidor Local** no painel esquerdo.

    ![server manager properties](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. No painel **Propriedades**, clique em **Ativado** para modificar a configuração de Segurança Reforçada do Internet Explorer.
3. Na caixa de diálogo **a Configuração de Segurança Reforçada do Internet Explorer**, clique em **Desativado** na seção Administradores da caixa de diálogo e clique em **OK**.

    ![internet explorer enhanced security configuration](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. Abra o **Internet Explorer** na barra de tarefas.
5. Selecione **Usar as configurações de segurança e compatibilidade recomendadas** e clique em **OK** para concluir a configuração do Internet Explorer 11.
6. Insira https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms na caixa de endereço de URL e clique em **Enter**. 
7. Baixe a versão mais recente do SQL Server Management Studio e clique em **Executar** quando solicitado.
8. Quando solicitado, clique em **Instalar** para começar.
9. Quando a instalação for concluída, clique em **Fechar**.
10. Abra o SSMS.
11. Na caixa de diálogo **Conectar ao Servidor**, digite o **nome do host* da sua Instância Gerenciada na caixa **Nome do servidor**, selecione **Autenticação do SQL Server** , forneça o logon e a senha e, em seguida, clique em **Conectar**.

    ![conexão ssms](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Depois de se conectar, você pode exibir seus bancos de dados do sistema e do usuário no nó Bancos de Dados e vários objetos nos nós Segurança, Objetos do Servidor, Replicação, Gerenciamento, SQL Server Agent e XEvent Profiler.

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Baixar arquivo Wide World Importers - backup padrão

Use as etapas a seguir para baixar o arquivo Wide World Importers - backup padrão.

Usando o Internet Explorer, insira https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak na caixa de endereço de URL e, ao receber a solicitação, clique em **Salvar** para salvar esse arquivo na pasta **Downloads**.

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Criar conta de armazenamento do Azure e carregar o arquivo de backup

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Localize **Armazenamento** e clique em **Conta de Armazenamento** para abrir o formulário da conta de armazenamento.

   ![do Azure](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Preencha o formulário de conta de armazenamento com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Modelo de implantação**|Modelo de recursos||
   |**Tipo de conta**|Armazenamento de blob||
   |**Desempenho**|Standard ou Premium|Unidades magnéticas ou SSDs|
   |**Replicação**|Armazenamento com redundância local||
   |**Camada de acesso (padrão)|Inativo ou ativo||
   |**Transferência segura é necessária**|Desabilitado||
   |**Assinatura**|Sua assinatura|Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions).|
   |**Grupo de recursos**|O grupo de recursos criado anteriormente|| 
   |**Localidade**|O local selecionado anteriormente||
   |**Redes virtuais**|Desabilitado||

4. Clique em **Criar**.

   ![storage account details](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. Depois que a implantação da conta de armazenamento tiver êxito, abra sua nova conta de armazenamento.
6. Em **Configurações**, clique em **Assinatura de Acesso Compartilhado** para abrir o formulário de SAS (assinatura de acesso compartilhado).

   ![sas form](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. No formulário SAS, modifique os valores padrão conforme desejado. Observe que a data/hora de validade é, por padrão, de apenas 8 horas.
8. Clique em **Gerar SAS**.

   ![sas form completed](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Copie e salve o **token SAS** e a **URL SAS do servidor de blobs**.
10. Em **Configurações**, clique em **Contêineres**.

    ![contêineres](./media/sql-database-managed-instance-tutorial/containers.png)

11. Clique em **+ Contêiner** para criar um contêiner e armazenar o arquivo de backup.
12. Preencha o formulário de contêiner com as informações solicitadas, usando as informações na tabela abaixo:

    | Configuração| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Nível de acesso público**|Privado (sem acesso anônimo)||

    ![container detail](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Clique em **OK**.
14. Depois que o contêiner tiver sido criado, abra-o.

    ![contêiner](./media/sql-database-managed-instance-tutorial/container.png)

15. Clique em **Propriedades do contêiner** e copie a URL para o contêiner.

    ![container URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Clique em **Carregar** para abrir a o formulário **Carregar blob**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. Procure a pasta de download e selecione o arquivo **AdventureWorks2016.bak**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Clique em **Carregar**.
19. Não continue até que o upload seja concluído.

    ![upload complete](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Restaurar o banco de dados da Wide World Importers de um arquivo de backup

Com o SSMS, use as etapas a seguir para restaurar o banco de dados da Wide World Importers para sua Instância Gerenciada do arquivo de backup.

1. No SSMS, abra uma nova janela de consulta.
2. Use o script a seguir para criar uma credencial SAS, fornecendo a URL para o contêiner da conta de armazenamento e a chave SAS, conforme indicado.

   ```
CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
, SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![credencial](./media/sql-database-managed-instance-tutorial/credential.png)

3. Use o script a seguir para criar e verificar a credencial de SAS e a validade do backup, fornecendo a URL para o contêiner com o arquivo de backup:

   ```
   RESTORE FILELISTONLY FROM URL = 
   'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![file list](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Use o script a seguir para restaurar o banco de dados do Adventure Works 2012 de um arquivo de backup, fornecendo a URL para o contêiner com o arquivo de backup:

   ```
   RESTORE DATABASE [Wide World Importers] FROM URL =
  'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![restore executing](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Para acompanhar o status de restauração, execute a consulta abaixo em uma nova sessão de consulta:

   ```
SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
, dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
FROM sys.dm_exec_requests r 
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![restore percent complete](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Quando a restauração for concluída, exiba-a no Pesquisador de Objetos. 

    ![restore complete](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a Instância Gerenciada, consulte [O que é uma Instância Gerenciada?](sql-database-managed-instance.md).
- Para saber mais sobre a configuração de rede virtual, confira [Configuração de VNet de Instância Gerenciada](sql-database-managed-instance-vnet-configuration.md).
- Para obter informações sobre como se conectar a aplicativos, confira [Conectar aplicativos](sql-database-managed-instance-connect-app.md).
- Para obter um tutorial usando o DMS (Serviço de Migração de Banco de Dados do Azure) para a migração, confira [Migração de Instância Gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
