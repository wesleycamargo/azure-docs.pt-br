---
title: "Provisionar uma máquina virtual do SQL Server | Microsoft Docs"
description: "Criar e conectar-se a uma máquina virtual do SQL Server no Azure usando o Portal. Este tutorial usa o modo Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
editor: 
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 842b6e0b69661a91ebb997346b34da00576378c2


---
# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Provisionar uma máquina virtual do SQL Server no Portal do Azure
> [!div class="op_single_selector"]
> * [Portal](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
> 
> 

Este tutorial completo mostra a você como usar o Portal do Azure para provisionar uma máquina virtual com o SQL Server em execução.

A galeria de VMs (máquinas virtuais) do Azure inclui várias imagens que contêm o Microsoft SQL Server. Com alguns cliques, você pode selecionar uma das imagens de VM do SQL da galeria e provisioná-la em seu ambiente do Azure.

Neste tutorial, você irá:

* [Selecionar uma imagem de VM do SQL na galeria](#select-a-sql-vm-image-from-the-gallery)
* [Configurar e criar a VM](#configure-the-vm)
* [Abrir a VM usando a Área de Trabalho Remota.](#open-the-vm-with-remote-desktop)
* [Conectar-se ao SQL Server remotamente](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Selecionar uma imagem de VM do SQL na galeria
1. Faça logon no [portal do Azure](https://portal.azure.com) usando sua conta.
   
   > [!NOTE]
   > Se você não tiver uma conta do Azure, visite [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
   > 
   > 
2. No portal do Azure, clique em **Novo**. O portal abre a folha **Novo** . Os recursos de VM do SQL Server estão no grupo **Máquinas Virtuais** do Marketplace.
3. Na folha **Novo**, clique em **Máquinas Virtuais**.
4. Para ver todas as imagens disponíveis, clique em **Ver tudo** na folha **Máquinas Virtuais**.
   
    ![Folha Máquinas Virtuais do Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)
5. Em **Servidores do banco de dados**, clique em **SQL Server**. Talvez você precise rolar para baixo para localizar os **Servidores do banco de dados**. Examine os modelos disponíveis do SQL Server.
   
    ![Imagens de SQL da galeria de máquinas virtuais](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)
6. Cada modelo identifica uma versão do SQL Server e um sistema operacional. Selecione uma dessas imagens da lista. Examine a folha de detalhes que fornece uma descrição da imagem da máquina virtual.
   
   > [!NOTE]
   > As imagens de VM do SQL incluem os custos de licenciamento para o SQL Server no preço por minuto da VM que você cria. Há outra opção de BYOL (traga sua própria licença) e pagar apenas pela VM. Os nomes de imagem têm o prefixo {BYOL}. Para saber mais sobre essa opção, confira [Introdução ao SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
   > 
   > 
7. Em **Selecionar um modelo de implantação**, verifique se **Resource Manager** está selecionado. Resource Manager é o modelo de implantação recomendado para novas máquinas virtuais. Clique em **Criar**.
   
    ![Criar VM do SQL com o Resource Manager](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Configurar a VM
Há cinco folhas para configuração de uma máquina virtual do SQL Server.

| Etapa | Descrição |
| --- | --- |
| **Noções básicas** |[Definir as configurações básicas](#1-configure-basic-settings) |
| **Tamanho** |[Escolher o tamanho da máquina virtual](#2-choose-virtual-machine-size) |
| **Configurações** |[Configurar os recursos opcionais](#3-configure-optional-features) |
| **Configurações do SQL Server** |[Definir as configurações do SQL Server](#4-configure-sql-server-settings) |
| **Resumo** |[Examinar o resumo](#5-review-the-summary) |

## <a name="1-configure-basic-settings"></a>1. Definir as configurações básicas
Na folha **Básico** , forneça as seguintes informações:

* Digite um **Nome**exclusivo da máquina virtual.
* Especifique um **Nome de usuário** para a conta do administrador local na VM. Essa conta também é adicionada à função de servidor fixa **sysadmin** do SQL Server.
* Forneça uma **Senha**forte.
* Se você tem várias assinaturas, verifique se a assinatura é a correta para a nova VM.
* Na caixa **Grupo de recursos** , digite um nome para um novo grupo de recursos. Como alternativa, para usar um grupo de recursos existente, clique em **Selecionar existente**. Um grupo de recursos é uma coleção de recursos relacionados no Azure (máquinas virtuais, contas de armazenamento, redes virtuais etc.).
  
  > [!NOTE]
  > O uso de um novo grupo de recursos é útil se você estiver apenas testando ou aprendendo sobre implantações do SQL Server no Azure. Após concluir o teste, exclua o grupo de recursos para excluir automaticamente a VM e todos os recursos associados a esse grupo de recursos. Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
  > 
  > 
* Selecione um **Local** para essa implantação.
* Clique em **OK** para salvar as configurações.
  
    ![Folha de Noções Básicas do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Escolher o tamanho da máquina virtual
Na etapa **Tamanho**, escolha um tamanho de máquina virtual na folha **Escolher um tamanho**. A folha exibe inicialmente o tamanho recomendado da máquina baseado no modelo selecionado. Também calcula o custo mensal para execução da VM.

![Opções de tamanho de VM do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Para as cargas de trabalho de produção, recomendamos escolher um tamanho de máquina virtual que dê suporte ao [Armazenamento Premium](../storage/storage-premium-storage.md). Se você não precisar desse nível de desempenho, use o botão **Exibir tudo** , que mostra todas as opções de tamanho da máquina. Por exemplo, você pode usar um tamanho de máquina menor para um ambiente de teste ou desenvolvimento.

> [!NOTE]
> Para obter mais informações sobre tamanhos de máquinas virtuais, confira [Tamanhos de máquinas virtuais](virtual-machines-windows-sizes.md). Para ver as considerações sobre os tamanhos de VM do SQL Server, consulte as [Práticas recomendadas de desempenho para o SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md).
> 
> 

Escolha o tamanho da máquina e clique em **Selecionar**.

## <a name="3-configure-optional-features"></a>3. Configurar os recursos opcionais
Na folha **Configurações** , defina o armazenamento do Azure, a rede e o monitoramento da máquina virtual.

* Em **Armazenamento**, especifique um **Tipo de disco** Standard ou Premium (SSD). Armazenamento Premium é recomendado para cargas de trabalho de produção.

> [!NOTE]
> Se você selecionar Premium (SSD) para um tamanho de máquina que não dá suporte a armazenamento Premium, o tamanho da máquina será alterado automaticamente.  
> 
> 

* Em **Conta de armazenamento**, você pode aceitar o nome da conta de armazenamento provisionado automaticamente. Também pode clicar em **Conta de armazenamento** para escolher uma conta existente e configurar o tipo da conta de armazenamento. Por padrão, o Azure cria uma nova conta de armazenamento com o armazenamento com redundância local. Para saber mais sobre as opções de armazenamento, consulte [Replicação do Armazenamento do Azure](../storage/storage-redundancy.md).
* Em **Rede**, você pode aceitar os valores preenchidos automaticamente. Você também pode clicar em cada recurso para configurar manualmente a **Rede virtual**, **Sub-rede**, **Endereço IP público** e **Grupo de Segurança da Rede**. Para a finalidade deste tutorial, mantenha os valores padrão.
* O Azure habilita o **Monitoramento** por padrão com a mesma conta de armazenamento designada para a VM. Você pode alterar essas configurações aqui.
* Em **Conjunto de disponibilidades**, especifique um conjunto de disponibilidades. Para este tutorial, você pode selecionar **nenhum**. Se você planeja configurar Grupos de Disponibilidade SQL AlwaysOn, configure a disponibilidade para evitar recriar a máquina virtual.  Para obter mais informações, consulte [Gerenciar a Disponibilidade de Máquinas Virtuais](virtual-machines-windows-manage-availability.md).

Ao concluir as configurações, clique em **OK**.

## <a name="4-configure-sql-server-settings"></a>4. Definir as configurações do SQL Server
Na folha **Configurações do SQL Server** , defina as configurações e otimizações específicas para o SQL Server. As configurações possíveis para o SQL Server incluem as seguintes:

| Configuração |
| --- |
| [Conectividade](#connectivity) |
| [Autenticação](#authentication) |
| [Configuração de armazenamento](#storage-configuration) |
| [Aplicação de patch automatizada](#automated-patching) |
| [Backup Automatizado](#automated-backup) |
| [Integração do Cofre da Chave do Azure](#azure-key-vault-integration) |
| [R Services](#r-services) |

### <a name="connectivity"></a>Conectividade
Em **Conectividade SQL**, especifique o tipo de acesso desejado para a instância do SQL Server nesta VM. Para este tutorial, escolha **Pública (Internet)** para permitir conexões com o SQL Server a partir de máquinas ou serviços na Internet. Com essa opção selecionada, o Azure configura automaticamente o firewall e o grupo de segurança de rede para permitir o tráfego na porta 1433.  

![Opções de conectividade do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Para conectar-se ao SQL Server pela Internet, você precisará habilitar a Autenticação do SQL Server, que está descrita na próxima seção.

> [!NOTE]
> É possível adicionar mais restrições às comunicações de rede com a VM do SQL Server. Faça isso editando o Grupo de Segurança de Rede após a criação da VM. Para obter mais informações, consulte [O que é um NSG (Grupo de Segurança de Rede)?](../virtual-network/virtual-networks-nsg.md)
> 
> 

Se você preferir não permitir conexões com o Mecanismo de Banco de Dados pela internet, escolha uma das seguintes opções:

* **Local (apenas dentro da VM)** para permitir conexões com o SQL Server somente de dentro da VM.
* **Privada (dentro da Rede Virtual)** para permitir conexões com o SQL Server a partir de computadores ou serviços na mesma rede virtual.

> [!NOTE]
> A imagem de máquina virtual para o SQL Server Express edition não habilita automaticamente o protocolo TCP/IP. Isso é verdadeiro mesmo para as opções de conectividade Pública e Privada. Para o Express Edition, você deve usar o SQL Server Configuration Manager para [habilitar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a máquina virtual.
> 
> 

Em geral, melhore a segurança escolhendo a conectividade mais restritiva que seu cenário permite. No entanto, todas as opções são protegidas por meio de regras do Grupo de Segurança de Rede e por meio da Autenticação do SQL/Windows.

**Portaa** usada por padrão é 1433. Você pode especificar um número de porta diferente.
Para saber mais, consulte [Conectar uma Máquina Virtual do SQL Server (Gerenciador de Recursos) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Autenticação
Se você precisar da Autenticação do SQL Server, clique em **Habilitar** under **Autenticação do SQL**.

![Autenticação do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

> [!NOTE]
> Se você pretende acessar o SQL Server pela Internet (ou seja, a opção de conectividade Pública), habilite a autenticação do SQL aqui. O acesso público ao SQL Server exige o uso da Autenticação do SQL.
> 
> 

Se você habilitar a Autenticação do SQL Server, especifique um **Nome de logon** e **Senha**. Esse nome de usuário é configurado como um logon de Autenticação do SQL Server e membro da função de servidor fixa **sysadmin** . Consulte [Escolher um Modo de Autenticação](http://msdn.microsoft.com/library/ms144284.aspx) para saber mais sobre os Modos de Autenticação.

Se você não habilitar a Autenticação do SQL Server, poderá usar a conta local de Administrador na VM para se conectar à instância do SQL Server.

### <a name="storage-configuration"></a>Configuração de armazenamento
Clique em **Configuração de armazenamento** para especificar os requisitos de armazenamento.

![Configuração do armazenamento do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

> [!NOTE]
> Se você selecionar o armazenamento Standard, essa opção não estará disponível. A otimização de armazenamento automático está disponível apenas para o Armazenamento Premium.
> 
> 

Você pode especificar os requisitos como operações IOPs (entrada/saída por segundo), taxa de transferência em MB/s e tamanho total de armazenamento. Configure esses valores usando as escalas deslizantes. O portal calcula automaticamente o número de discos com base nesses requisitos.

Por padrão, o Azure otimiza o armazenamento para 5000 IOPs, 200 MB e 1 TB de espaço de armazenamento. Você pode alterar essas configurações de armazenamento com base na carga de trabalho. Em **Armazenamento otimizado para**, escolha uma das seguintes opções:

* **Geral** é a configuração padrão e dá suporte à maioria das cargas de trabalho.
* **transacional** otimiza o armazenamento para cargas de trabalho OLTP tradicionais do banco de dados.
* **Data warehouse** otimiza o armazenamento para as cargas de trabalho de análise e emissão de relatórios.

> [!NOTE]
> Os limites superiores nos controles deslizantes variam dependendo do tamanho selecionado para a máquina virtual.
> 
> 

### <a name="automated-patching"></a>Aplicação de patch automatizada
**Automated patching** está habilitada por padrão. A aplicação de patch automatizada permite que o Azure aplique patches automaticamente no SQL Server e no sistema operacional. Especifique um dia da semana, hora e duração de um período de manutenção. O Azure realiza a aplicação de patch na janela de manutenção. O agendamento do período de manutenção usa a localidade da VM para a hora. Se você não quiser que o Azure aplique automaticamente o patch no SQL Server e no sistema operacional, clique em **Desabilitar**.  

![Aplicação de patch automatizada do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Para saber mais, consulte [Aplicação de Patch Automatizada para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Backup Automatizado
Habilite backups automáticos do banco de dados para todos os bancos de dados em **Backup automatizado**. O backup automatizado está desabilitado por padrão.

Ao habilitar o backup automatizado do SQL, é possível configurar o seguinte:

* Período de retenção (dias) para backups
* Conta de armazenamento a ser usada para backups
* Opção de criptografia e senha para backups

Para criptografar o backup, clique em **Habilitar**. Em seguida, especifique a **Senha**. O Azure cria um certificado para criptografar os backups e usa a senha especificada para proteger esse certificado.

![Backup Automatizado do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 Para obter mais informações, veja [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Integração do Cofre da Chave do Azure
Para armazenar os segredos de segurança no Azure para a criptografia, clique em **Integração do cofre de chaves do Azure** e clique em **Habilitar**.

![Integração do Cofre da Chave do SQL Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

A tabela a seguir lista os parâmetros necessários para configurar a integração do Cofre da Chave do Azure.

| PARÂMETRO | Descrição | EXEMPLO |
| --- | --- | --- |
| **URL do cofre da chave** |O local do cofre da chave. |https://contosokeyvault.vault.azure.net/ |
| **Nome de entidade** |Nome de entidade de serviço do Active Directory do Azure Ele também é chamado de ID do Cliente. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Segredo da entidade** |Segredo da entidade de serviço do Azure Active Directory. O segredo também é chamado de Segredo do Cliente. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nome da credencial** |**Nome da credencial**: a integração AKV cria uma credencial no SQL Server, permitindo que a VM tenha acesso ao cofre da chave. Escolha um nome para essa credencial. |mycred1 |

Para saber mais, consulte [Configurar a Integração do Cofre de Chaves do Azure para o SQL nas VMs do Azure](virtual-machines-windows-ps-sql-keyvault.md).

Ao concluir as configurações do SQL Server, clique em **OK**.

### <a name="r-services"></a>R Services
Para o SQL Server 2016 Enterprise edition, você tem a opção de habilitar os [Serviços de R do SQL Server](https://msdn.microsoft.com/library/mt604845.aspx). Isso permite que você use análises avançadas com o SQL Server 2016. Clique em **Habilitar** on the **SQL Server Settings** .

![Habilitar os serviços de R do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

> [!NOTE]
> Para imagens do SQL Server que não são da edição 2016 Enterprise, a opção para habilitar os serviços de R está desabilitada.
> 
> 

## <a name="5-review-the-summary"></a>5. Examinar o resumo
Na folha **Resumo**, examine o resumo e clique em **OK** para criar o SQL Server, grupo de recursos e recursos especificados para essa VM.

Você pode monitorar a implantação do Portal do Azure. O botão **Notificações** na parte superior da tela mostra o status básico da implantação.

> [!NOTE]
> Para você ter uma ideia sobre os tempos de implantação, eu implantei uma VM do SQL na região Leste dos EUA com as configurações padrão. Essa implantação de teste levou um total de 26 minutos para ser concluída. Mas sua implantação pode ser rápida ou mais lenta de acordo com sua região e com as configurações selecionadas.
> 
> 

## <a name="open-the-vm-with-remote-desktop"></a>Abrir a VM usando a Área de Trabalho Remota.
Use as etapas a seguir para se conectar à máquina virtual com a Área de Trabalho Remota.

1. Após a compilação da VM do Azure, um ícone para a VM será exibido no painel do Azure. Você também pode encontrá-lo navegando por suas máquinas virtuais existentes. Clique na nova máquina virtual do SQL. Uma folha da **Máquina virtual** exibe os detalhes de sua máquina virtual.
2. Na parte superior da folha **Máquina virtual**, clique em **Conectar**.
3. O navegador baixa um arquivo RDP para a VM. Abra o arquivo RDP.
    ![Área de trabalho remota para VM do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
4. A Conexão de área de trabalho remota avisa você de que o distribuidor dessa conexão remota não pode ser identificado. Clique em **Conectar** para continuar.
5. Na caixa de diálogo **Segurança do Windows**, clique em **Usar outra conta**.
6. Em **Nome de usuário**, digite **\<nome usuário>**, onde <user name> é o nome de usuário especificado por você durante a configuração da VM. Você precisa adicionar uma barra invertida inicial antes do nome.
7. Digite a **Senha** que você configurou anteriormente para essa VM e clique em **OK** para conectar.
8. Se outra caixa de diálogo **Conexão da Área de Trabalho Remota** perguntar se você deseja conectar, clique em **Sim**.

Depois de se conectar à máquina virtual do SQL Server, você pode iniciar o SQL Server Management Studio e conectar-se à Autenticação do Windows usando suas credenciais de administrador local. Se você habilitou a Autenticação do SQL Server, também será possível se conectar com a Autenticação do SQL usando o logon e a senha do SQL configurados durante o provisionamento.

O acesso à máquina permite que você altere diretamente as configurações da máquina e do SQL Server com base em suas necessidades. Por exemplo, você pode definir as configurações do firewall ou alterar as configuração do SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Conectar-se ao SQL Server remotamente
Neste tutorial, selecionamos o acesso **Público** para a máquina virtual e a **Autenticação do SQL Server**. Essas configurações definiram automaticamente a máquina virtual para permitir conexões do SQL Server de qualquer cliente pela internet (supondo que o logon do SQL esteja correto).

> [!NOTE]
> Se você não selecionou a opção Público durante o provisionamento, será necessário executar etapas adicionais para acessar a instância do SQL Server pela Internet. Para saber mais, confira [Conectar uma Máquina Virtual do SQL Server](virtual-machines-windows-sql-connect.md).
> 
> 

As seções a seguir mostram como se conectar à instância do SQL Server em sua VM de um computador diferente pela internet.

> [!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como usar o SQL Server no Azure, consulte [SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e as [Perguntas Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Para obter uma visão geral em vídeo do SQL Server em máquinas virtuais do Azure, assista ao vídeo [Azure VM is the best platform for SQL Server 2016 (A VM do Azure é a melhor plataforma para o SQL Server 2016)](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016).

[Explorar o Roteiro de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) do SQL Server nas máquinas virtuais do Azure.




<!--HONumber=Nov16_HO2-->


