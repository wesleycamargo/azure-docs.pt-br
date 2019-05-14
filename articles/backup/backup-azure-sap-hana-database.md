---
title: Fazer backup de um banco de dados do SAP HANA no Azure com o Backup do Azure | Microsoft Docs
description: Este tutorial explica como fazer backup de um banco de dados do SAP HANA no Azure com o serviço de Backup do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: 5ed41013535e4591d88bff5c017c1fcf4c4053cc
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237801"
---
# <a name="back-up-an-sap-hana-database"></a>Fazer backup de um banco de dados do SAP HANA

[O Backup do Azure](backup-overview.md) suporta o backup de bancos de dados do SAP HANA no Azure.

> [!NOTE]
> Esse recurso está atualmente em visualização pública. Ele atualmente não está pronto para produção e não tem uma garantia de SLA. 


## <a name="scenario-support"></a>Suporte ao cenário

**Suporte** | **Detalhes**
--- | ---
**Áreas geográficas com suporte** | Sudeste da Austrália, Leste da Austrália <br> Sul do Brasil <br> No Canadá, Leste do Canadá <br> Sudeste Asiático, Ásia Oriental <br> Leste dos EUA, Leste dos EUA 2, oeste dos EUA, oeste dos EUA, oeste dos EUA 2, Centro-Norte dos EUA, centro dos EUA, Centro-Sul dos EUA<br> Índia Central, Sul da Índia <br> Leste do Japão, Oeste do Japão<br> Coreia Central, Sul da Coreia <br> Norte da Europa, Europa Ocidental <br> Sul do Reino Unido, oeste do Reino Unido
**Sistemas operacionais VM** | SLES 12 com SP2 ou SP3.
**Versões com suporte do HANA** | SSDC no HANA 1.x, MDC no HANA 2. x < = SPS03


### <a name="current-limitations"></a>Limitações atuais

- Você só pode fazer backup de bancos de dados do SAP HANA em execução em VMs do Azure.
- Você só pode configurar o backup do SAP HANA no portal do Azure. O recurso não pode ser configurado com o PowerShell, CLI ou API REST.
- Você só pode fazer backup de bancos de dados no modo de escala vertical.
- Você pode fazer backup de logs de banco de dados a cada 15 minutos. Backups de log somente começam a fluir depois que um backup completo com êxito para o banco de dados foi concluída.
- Você pode fazer backups completos e diferenciais. Atualmente, não há suporte para backup incremental.
- Você não pode modificar a política de backup depois de aplicá-la para backups do SAP HANA. Se você quiser fazer backup com configurações diferentes, crie uma nova política ou atribuir uma política diferente. 
    - Para criar uma nova política, no cofre, clique **diretivas** > **políticas de Backup** > **+ adicionar** > **SAP HANA em VM do Azure**e especifique as configurações de política.
    - Para atribuir uma política diferente, nas propriedades da VM que executa o banco de dados, clique no nome de política atual. Em seguida, na **política de Backup** página você pode selecionar uma política diferente a ser usado para o backup.




## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que fazer o seguinte antes de configurar backups:

1. Na VM executando o banco de dados do SAP HANA, instale o Microsoft official [tempo de execução do .NET Core 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) pacote. Observe que:
    - Você só precisa de **dotnet-runtime-2.1** pacote. Você não precisa **aspnetcore-runtime-2.1**.
    - Se a VM não tiver internet acesse, espelho ou fornecem um cache offline para dotnet-runtime-2.1 (e todos os RPMs dependentes) do pacote Microsoft feed especificado na página.
    - Durante a instalação do pacote, você pode ser solicitado para especificar uma opção. Nesse caso, especifique **solução 2**.

        ![Opção de instalação do pacote](./media/backup-azure-sap-hana-database/hana-package.png)

2.  Na VM, instalar e habilitar os pacotes de driver ODBC da oficial SLES pacote/mídia usando o zypper, da seguinte maneira:

    ``` 
    sudo zypper update
    sudo zypper install unixODBC
    ```
4.  Permitir a conectividade da VM com a internet, para que ele pode acessar o Azure, conforme descrito no procedimento a seguir.


### <a name="set-up-network-connectivity"></a>Configurar a conectividade de rede

Todas as operações, a VM do HANA SAP precisa ter conectividade com endereços IP públicos do Azure. Operações de VM (descoberta de banco de dados, configurar backups, agendar backups, restaurar pontos de recuperação e assim por diante) não funcionam sem conectividade. Estabelece conectividade, permitindo o acesso aos intervalos de IP de datacenter do Azure: 

- Você pode baixar o [intervalos de endereços IP](https://www.microsoft.com/download/details.aspx?id=41653) para datacenters do Azure e, em seguida, permitir o acesso a esses endereços IP.
- Se você estiver usando grupos de segurança de rede (NSGs), você pode usar o AzureCloud [marca de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para permitir que o IP público do Azure todos os endereços. Você pode usar o [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) para modificar as regras NSG.

## <a name="onboard-to-the-public-preview"></a>Integrar a visualização pública

Integrar a visualização pública da seguinte maneira:

- No portal, registre sua ID de assinatura para o provedor de serviço dos serviços de recuperação por [seguindo este artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Para o PowerShell, execute este cmdlet. Ele deverá ser concluída como "Registrado".

    ```
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Descobrir os bancos de dados


1. No cofre, no **guia de Introdução**, clique em **Backup**. Na **onde sua carga de trabalho é executada?**, selecione **SAP HANA na VM do Azure**.
2. Clique em **iniciar descoberta**. Isso inicia a descoberta de VMs do Linux desprotegidos na região do cofre.

   - Após a descoberta, VMs não protegidas são exibidos no portal, listados por nome e grupo de recursos.
   - Se uma VM não estiver listada, conforme o esperado, verifique se ele já é feito em um cofre.
   - Várias VMs podem ter o mesmo nome mas pertencem a diferentes grupos de recursos.

3. Na **selecionar máquinas virtuais**, clique no link para baixar o script que fornece permissões para o serviço de Backup do Azure acessar as VMs do SAP HANA para a descoberta de banco de dados
4. Execute o script em cada VM hospedando bancos de dados do SAP HANA que você deseja fazer backup.
5. Depois de executar o script em VMs, em **selecionar máquinas virtuais**, selecione as VMs. Em seguida, clique em **descobrir BDs**.
6. O Backup do Azure descobre todos os bancos de dados do SAP HANA na VM. Durante a descoberta, o Backup do Azure registra a VM com o cofre e instala uma extensão na VM. Nenhum agente é instalado no banco de dados.

    ![Descobrir bancos de dados do SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)
    
## <a name="configure-backup"></a>Configurar o backup  

Habilite o backup.

1. Na etapa 2, clique em **configurar o Backup**.
2. Na **selecionar itens para fazer backup**, selecione todos os bancos de dados que você deseja proteger > **Okey**.
3. Na **política de Backup** > **escolher política de backup**, criar uma nova política de backup para os bancos de dados, de acordo com as instruções abaixo.
4. Depois de criar a política sobre a **Backup** menu, clique em **habilitar backup**.
5. Acompanhar o progresso de configuração de backup na **notificações** área do portal.

### <a name="create-a-backup-policy"></a>Criar uma política de backup
Uma política de backup define quando são realizados backups e quanto tempo eles são mantidos.

- Uma política é criada no nível do cofre.
- Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.

Especifique as configurações de política da seguinte maneira:

1. Em **Nome da política**, insira um nome para a nova política.
2. Em **Política de Backup Completo**, selecione uma **Frequência de Backup** escolhendo **Diária** ou **Semanal**.
   - **Diariamente**: Selecione a hora e fuso horário no qual o trabalho de backup começa.
   
       - Você deve executar um backup completo. Você não pode desativar essa opção.
       - Clique em **Backup Completo** para exibir a política.
       - Você não pode criar backups diferenciais para backups diários completos.
       
   - **Semanal**: Selecione o dia da semana, hora e fuso horário no qual o trabalho de backup é executado.
3. Na **período de retenção**, definir as configurações de retenção para o backup completo.
    - Por padrão, todas as opções são selecionadas. Desmarque qualquer limites de intervalo de retenção que você não deseja usar e configurar aqueles que você faça.
    - O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
    - Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
    - O backup para um dia específico é marcado e mantido com base no período de retenção semanal e configuração.
    - Os intervalos de retenção mensal e anual comportam-se de maneira semelhante.

4. No **política de Backup completo** menu, clique em **Okey** para aceitar as configurações.
5. Selecione **Backup diferencial** para adicionar uma política de diferencial.
6. Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência.
    - No máximo, você pode acionar um backup diferencial por dia.
    - Backups diferenciais podem ser retidos por até 180 dias. Se você precisar de retenção mais longa, deverá usar os backups completos.

    > [!NOTE]
    > Atualmente, não há suporte para backups incrementais. 

7. Clique em **Okey** para salvar a política e retornar ao principal **política de Backup** menu.
8. Selecione **Backup de Log** para adicionar uma política de backup de log transacional
    - Na **Backup de Log**, selecione **habilitar**.
    - Defina os controles de retenção e frequência.

    > [!NOTE]
    > Backups de log somente começam a fluir após a conclusão de um backup completo com êxito.

9. Clique em **Okey** para salvar a política e retornar ao principal **política de Backup** menu.
10. Depois de terminar de definir a política de backup, clique em **Okey**.


## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Execute backups de acordo com o agendamento da política. Você pode executar um backup sob demanda da seguinte maneira:


1. No menu do cofre, clique em **Itens de backup**.
2. Na **itens de Backup**, selecione a VM executando o banco de dados do SAP HANA e, em seguida, clique em **fazer Backup agora**.
3. Na **fazer Backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
4. Monitorar as notificações do portal. Você pode monitorar o andamento do trabalho no painel do cofre > **trabalhos de Backup** > em andamento. Dependendo do tamanho do banco de dados, a criação do backup inicial pode demorar um pouco.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Executar o backup do SAP HANA Studio em um banco de dados com o Backup do Azure habilitado

Se você quiser executar um backup local (usando o HANA Studio) de um banco de dados que está sendo feito backup com o Backup do Azure, faça o seguinte:

1. Aguarde até que qualquer completo ou backups de log para o banco de dados concluir. Verifique o status no SAP HANA Studio.
2. Desabilitar backups de log e definir o catálogo de backup para o sistema de arquivos para o banco de dados relevante.
3. Para fazer isso, clique duas vezes em **systemdb** > **Configuration** > **Selecionar banco de dados** > **filtro (Log)** .
4. Definir **enable_auto_log_backup** à **não**.
5. Definir **log_backup_using_backint** à **falso**.
6. Faça backup do banco de dados um completo ad-hoc.
7. Aguarde até que o backup completo e backup de catálogo para concluir.
8. Reverta as configurações anteriores para aquelas para o Azure:
    - Definir **enable_auto_log_backup** à **Sim**.
    - Definir **log_backup_using_backint** à **verdadeiro**.



## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre](backup-azure-arm-vms-prepare.md) fazendo backup de VMs do Azure.


