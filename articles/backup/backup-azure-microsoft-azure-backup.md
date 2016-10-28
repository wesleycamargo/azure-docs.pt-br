<properties
  pageTitle="Preparação do seu ambiente para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure | Microsoft Azure"
  description="Tenha certeza de que seu ambiente está preparado adequadamente para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure."
  services="backup"
  documentationCenter=""
  authors="PVRK"
  manager="shivamg"
  editor=""
  keywords="servidor de backup do Azure; cofre de backup"/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="07/20/2016"
  ms.author="jimpark;trinadhk;pullabhk;markgal"/>

# Preparação para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure

> [AZURE.SELECTOR]
- [Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Servidor de Backup do Azure (clássico)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (clássico)](backup-azure-dpm-introduction-classic.md)

Este artigo explica como preparar seu ambiente para fazer backup das cargas de trabalho usando o Servidor de Backup do Azure. Com o Servidor de Backup do Azure, você pode proteger cargas de trabalho de aplicativo como VMs do Hyper-V, o Microsoft SQL Server, o SharePoint Server, o Microsoft Exchange e os clientes de um único console. Você também pode proteger as informações como cargas de trabalho de um servidor (IaaS), como as VMs no Azure.

> [AZURE.NOTE] O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../resource-manager-deployment-model.md). Este artigo fornece informações e procedimentos para a restauração de VMs implantadas usando o modelo do Gerenciador de Recursos.

O Servidor de Backup do Azure herda grande parte da funcionalidade do backup das cargas de trabalho do Data Protection Manager (DPM). Este artigo tem um link com a documentação do DPM para explicar algumas das funcionalidades compartilhadas. Entretanto, o Servidor de Backup do Azure compartilha grande parte da mesma funcionalidade do DPM. O Servidor de Backup do Azure não fazer backup em fita, nem se integra no System Center.

## 1\. Escolher uma plataforma de instalação

A primeira etapa para colocar o Servidor de Backup do Azure em execução é configurar um Windows Server. Seu servidor pode estar no Azure ou ser local.

### Usando um servidor no Azure

Ao escolher um servidor executando o Servidor de Backup do Azure, é recomendável iniciar com uma imagem da galeria do Windows Server 2012 R2 Datacenter. O artigo [Criar sua primeira máquina virtual do Windows no portal do Azure](..\virtual-machines\virtual-machines-windows-hero-tutorial.md), fornece um tutorial para começar a trabalhar com a máquina virtual recomendada no Azure, mesmo se você nunca usou o Azure antes. Os requisitos mínimos recomendados para a máquina virtual (VM) do servidor devem ser: Standard A2 com 2 núcleos e 3.5 GB de RAM.

Proteger as cargas de trabalho com o Servidor de Backup do Azure tem muitas nuanças. O artigo [Instalar o DPM como uma máquina virtual do Azure](https://technet.microsoft.com/library/jj852163.aspx), ajuda a explicar essas nuanças. Leia o artigo na íntegra antes de implantar a máquina.

### Usando um servidor local

Se você não deseja executar o servidor de base no Azure, poderá executar o servidor em uma VM do Hyper-V, uma VM do VMware ou um host físico. Os requisitos mínimos recomendados para o hardware do servidor são 2 núcleos e 4 GB de RAM. Os sistemas operacionais suportados são listados na tabela a seguir.

| Sistema operacional | Plataforma | SKU |
| :------------- |-------------| :-----|
|Windows Server 2012 R2 e SPs mais recentes|	64 bits|	Standard, Datacenter, Foundation|
|Windows Server 2012 e SPs mais recentes|	64 bits|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 e SPs mais recentes |64 bits|	Standard, Workgroup|
|Windows Storage Server 2012 e SPs mais recentes |64 bits |Standard, Workgroup|


Você pode eliminar duplicadas do armazenamento DPM usando a Eliminação de Duplicação do Windows Server. Saiba mais sobre como o [DPM e a eliminação de duplicação](https://technet.microsoft.com/library/dn891438.aspx) funcionam juntos quando implantados nas VMs do Hyper-V.

> [AZURE.NOTE]  Você não pode instalar o Servidor de Backup do Azure em uma máquina executada como um controlador de domínio.

Se você planeja associar este servidor a um domínio em algum momento, é recomendável que a atividade de ingressão no domínio seja concluída antes da instalação do Servidor de Backup do Azure. Mover uma máquina do Servidor de Backup do Azure existente para um novo domínio após a implantação *não tem suporte*.

## 2\. Cofre dos Serviços de Recuperação

Se você enviar os dados de backup para o Azure ou se os mantiver localmente, o software precisará estar conectado ao Azure. Para ser mais específico, a máquina do Servidor de Backup do Azure precisa ser registrada com um cofre dos Serviços de Recuperação.

Para criar um cofre dos Serviços de Recuperação:

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. No menu Hub, clique em **Procurar** e na lista de recursos, digite **Serviços de Recuperação**. Quando você começar a digitar, a lista será filtrada com base em sua entrada. Clique em **Cofre dos Serviços de Recuperação**.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>

    A lista de cofres dos Serviços de Recuperação é exibida.

3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)

    A folha do cofre dos Serviços de Recuperação será aberta, solicitando que você forneça um **Nome**, **Assinatura**, **Grupo de recursos** e **Local**.

    ![Criar Cofre de Serviços de Recuperação - etapa 5](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)

4. Em **Nome**, insira um nome amigável para identificar o cofre. O nome precisa ser exclusivo para a assinatura do Azure. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

5. Clique em **Assinatura** para ver a lista de assinaturas disponíveis. Se você não tiver certeza sobre qual assinatura usar, utilize a assinatura padrão (ou sugerida). Só haverá múltiplas opções se sua conta organizacional estiver associada a várias assinaturas do Azure.

6. Clique em **Grupo de recursos** para ver a lista dos Grupos de recursos disponíveis ou clique em **Novo** para criar um novo Grupo de recursos. Para obter informações completas sobre os Grupos de recursos, consulte [Visão geral do Azure Resource Manager](../resource-group-overview.md)

7. Clique em **Local** para selecionar a região geográfica do cofre.

8. Clique em **Criar**. Talvez demore um pouco para o cofre de Serviços de Recuperação ser criado. Monitore as notificações de status na área superior direita no portal. Depois que o cofre é criado, ele é aberto no portal.

### Definir replicação de armazenamento

A opção de replicação de armazenamento permite que você escolha entre o armazenamento com redundância geográfica e armazenamento com redundância local. Por padrão, seu cofre tem armazenamento com redundância geográfica. Deixe a opção definida como armazenamento com redundância geográfica se este for seu backup principal. Escolha o armazenamento com redundância local se quiser uma opção mais barata que não seja tão durável. Leia mais sobre as opções de armazenamento com [redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage) e [redundância local](../storage/storage-redundancy.md#locally-redundant-storage) na [Visão geral da replicação do Armazenamento do Azure](../storage/storage-redundancy.md).

Para editar a configuração de replicação de armazenamento:

1. Selecione seu cofre para abrir o painel do cofre e a folha Configurações. Se a folha **Configurações** não abrir, clique em **Todas as configurações** no painel do cofre.

2. Na folha **Configurações**, clique em **Infraestrutura do Backup** > **Configuração do Backup** para abrir a folha **Configuração do Backup**. Na folha **Configuração do Backup**, escolha a opção de replicação do armazenamento para seu cofre.

    ![Lista de cofres de backup](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Depois de escolher a opção de armazenamento para o cofre, você estará pronto para associar a VM ao cofre. Para iniciar a associação, você deverá descobrir e registrar as máquinas virtuais do Azure.

## 3\. Pacote de software

### Baixando o pacote de software
1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Se você já tiver um cofre dos Serviços de Recuperação aberto, vá para a etapa 3. Se você não tiver um cofre dos Serviços de Recuperação aberto, mas estiver no portal do Azure, no menu Hub, clique em **Procurar**.

    - Na lista de recursos, digite **Serviços de Recuperação**.
    - Quando você começar a digitar, a lista será filtrada com base em sua entrada. Quando vir a opção **Cofres dos Serviços de Recuperação**, clique nela.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

    A lista de cofres de Serviços de Recuperação aparecerá.

    - Na lista de cofres de Serviços de Recuperação, selecione um cofre.

    O painel de cofres selecionados será aberto.

    ![Abrir a folha do cofre](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

3. A folha de configurações abre por padrão. Se ela estiver fechada, clique em **Configurações** para abrir a folha de configurações.

    ![Abrir a folha do cofre](./media/backup-azure-microsoft-azure-backup/vault-setting.png)

4. Clique em **Backup** em **Introdução** para abrir o Assistente de Introdução.

    ![Guia de introdução ao backup](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

5. Na Introdução que é aberta, a tela Objetivos do Backup será selecionada automaticamente. ![Backup-metas-padrão-aberto](./media/backup-azure-microsoft-azure-backup/getting-started.png)

    Na seção **Objetivos do backup**, selecione *local* para *onde a sua carga de trabalho está sendo executada*.

    ![local e cargas de trabalho como metas](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

6. Selecione as cargas de trabalho que você deseja proteger usando o Servidor de Backup do Azure em *quais cargas de trabalho você deseja proteger* e clique em **OK**.

    > [AZURE.NOTE] Se você planeja proteger apenas arquivos e pastas, é recomendável usar o Agente de Backup do Azure. Se você planeja proteger mais cargas de trabalho do que apenas arquivos e pastas, ou se no futuro planeja expandir as necessidades de proteção, selecione todas as cargas de trabalho.

    Isso irá alterar o Assistente de Introdução para preparar a infraestrutura para proteger as cargas de trabalho no local ou no Azure.

    ![Guia de Introdução do assistente para alterar](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

7. Na folha **Preparar infraestrutura** que é aberta, clique nos links de **Download** para instalar o Servidor de Backup do Azure e baixar as credenciais do cofre. Você usa as credenciais do cofre durante o registro do Servidor de Backup do Azure para o cofre dos serviços de recuperação. Os links levam você para o Centro de Download, onde o pacote de software pode ser baixado.

    ![Preparar a infraestrutura para o Servidor de Backup do Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

8. Selecione todos os arquivos e clique em **Avançar**. Baixe todos os arquivos provenientes da página de download do Backup do Microsoft Azure e coloque todos os arquivos na mesma pasta.

    ![Centro de download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Como o tamanho do download de todos os arquivos juntos é superior a 3G, em um link de download de 10 Mbps link ele poderá levar até 60 minutos para ser concluído.


### Extraindo o pacote de software

Depois de baixar todos os arquivos, clique em **MicrosoftAzureBackupInstaller.exe**. Isso iniciará o **Assistente de Instalação do Backup do Microsoft Azure** para extrair os arquivos de instalação para um local especificado. Continue no assistente e clique no botão **Extrair** para iniciar o processo de extração.

> [AZURE.WARNING] Pelo menos 4 GB de espaço livre são necessários para a extração dos arquivos de instalação.


![Assistente de Instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Após concluir o processo de extração, marque a caixa para iniciar o *setup.exe* recém-extraído e começar a instalação do Servidor de Backup do Microsoft Azure e clique no botão **Concluir**.

### Instalando o pacote de software

1. Clique em **Backup do Microsoft Azure** para iniciar o assistente de instalação.

    ![Assistente de Instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

2. Na tela de Boas-Vindas, clique no botão **Avançar**. Isso o levará à seção *Verificações de Pré-Requisitos*. Nessa tela, clique no botão **Verificar** para determinar se os pré-requisitos de hardware e software do Servidor de Backup do Azure foram atendidos. Se todos os pré-requisitos tiverem sido atendidos com êxito, você verá uma mensagem indicando que o computador atende aos requisitos. Clique no botão **Avançar**.

    ![Servidor de Backup do Azure - Boas-vindas e Verificação de pré-requisitos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

3. O Servidor de Backup do Microsoft Azure requer o SQL Server Standard e o pacote de instalação do Servidor de Backup do Azure é fornecido com os binários do SQL Server apropriados. Ao iniciar uma nova instalação do Servidor de Backup do Azure, você deverá escolher a opção **Instalar nova Instância do SQL Server com esta Instalação** e clicar no botão **Verificar e Instalar**. Depois que os pré-requisitos tiverem sido instalados com êxito, clique em **Avançar**.

    ![Servidor de Backup do Azure - verificação do SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se ocorrer uma falha com uma recomendação de reiniciar o computador, faça isso e clique em **Verificar Novamente**.

    > [AZURE.NOTE] O Servidor de Backup do Azure não funcionará com uma instância remota do SQL Server. A instância que está sendo usada pelo Servidor de Backup do Azure precisa ser local.

4. Forneça um local para a instalação dos arquivos do servidor de Backup do Microsoft Azure e clique em **Avançar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    O local temporário é um requisito para o backup do Azure. Verifique se o local temporário é de pelo menos 5% dos dados planejados para fazer backup na nuvem. Para proteção de disco, será necessário configurar discos separados após a conclusão da instalação. Para saber mais sobre pools de armazenamento, consulte [Configurar os pools de armazenamento e o armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).

5. Forneça uma senha forte para as contas de usuário local restritas e clique em **Avançar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)

6. Selecione se você deseja usar o *Microsoft Update* para verificar se há atualizações e clique em **Avançar**.

    >[AZURE.NOTE] É recomendável fazer o Windows Update ser redirecionado para o Microsoft Update, que oferece segurança e atualizações importantes para o Windows e outros produtos, como o Servidor de Backup do Microsoft Azure.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

7. Examine o *Resumo das Configurações* e clique em **Instalar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

8. A instalação acontece em fases. Na primeira fase, o Agente de Serviços de Recuperação do Microsoft Azure é instalado no servidor. O assistente também verifica a conectividade direta com a Internet. Se houver conectividade com a Internet, você poderá prosseguir com a instalação; caso contrário, será necessário fornecer detalhes de proxy para se conectar à Internet.

    A próxima etapa será configurar o Agente de Serviços de Recuperação do Microsoft Azure. Como parte da configuração, você precisará fornecer as credenciais do cofre para registrar a máquina no cofre dos Serviços de Recuperação. Você fornecerá uma senha para criptografar/descriptografar os dados enviados entre o Azure e seus locais. Você pode gerar automaticamente uma senha ou fornecer sua própria senha mínima de 16 caracteres. Prossiga com o assistente até o agente ser configurado.

    ![Pré-requisito 2 do Servidor de Backup do Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)

9. Assim que o registro do Servidor de Backup do Microsoft Azure for concluído com êxito, o assistente de instalação geral prosseguirá na instalação e na configuração dos componentes do SQL Server e do Servidor de Backup do Azure. Após a conclusão da instalação do componente do SQL Server, os componentes do Servidor de Backup do Azure serão instalados.

    ![Servidor de Backup do Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)


Quando a etapa de instalação for concluída, os ícones da área de trabalho do produto também terão sido criados. Basta clicar duas vezes no ícone para iniciar o produto.

### Adicionar armazenamento de backup

A primeira cópia de backup é mantida no armazenamento anexado ao computador do Servidor de Backup do Azure. Para saber mais sobre a adição de discos, consulte [Configurar os pools de armazenamento e o armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).

> [AZURE.NOTE] Você precisará adicionar armazenamento de backup mesmo se planejar enviar dados para o Azure. Na arquitetura atual do Servidor de Backup do Azure, o cofre do Backup do Azure mantém a *segunda* cópia dos dados, enquanto o armazenamento local mantém a primeira (e obrigatória) cópia de backup.

## 4\. Conectividade de rede

O Servidor de Backup do Azure requer conectividade com o serviço de Backup Azure para que o produto funcione com êxito. Para validar se o computador tem conectividade com o Azure, use o cmdlet ```Get-DPMCloudConnection``` no console do PowerShell do Servidor de Backup do Azure. Se a saída do cmdlet for TRUE, existe conectividade; caso contrário, não existe nenhuma conectividade.

Ao mesmo tempo, a assinatura do Azure deve estar em um estado íntegro. Para descobrir o estado de sua assinatura e gerenciá-la, faça logon no [portal da assinatura](https://account.windowsazure.com/Subscriptions).

Quando você souber o estado da conectividade do Azure e da assinatura do Azure, poderá usar a tabela abaixo para descobrir o impacto sobre a funcionalidade de backup/restauração oferecida.

| Estado de conectividade | Assinatura do Azure | Backup no Azure| Backup em disco | Restaurar do Azure | Restaurar do disco |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Conectado | Ativo | Permitido | Permitido | Permitido | Permitido |
| Conectado | Expirado | Parada | Parada | Permitido | Permitido |
| Conectado | Provisionamento Cancelado | Parada | Parada | Parado e pontos de recuperação do Azure excluídos | Parada |
| Perda de conectividade > 15 dias | Ativo | Parada | Parada | Permitido | Permitido |
| Perda de conectividade > 15 dias | Expirado | Parada | Parada | Permitido | Permitido |
| Perda de conectividade > 15 dias | Provisionamento Cancelado | Parada | Parada | Parado e pontos de recuperação do Azure excluídos | Parada |

### Recuperação de perda de conectividade
Se você tiver um firewall ou um proxy que esteja impedindo o acesso do Azure, precisará incluir os seguintes endereços de domínio na lista de permissões no perfil do firewall/proxy:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

Depois que a conectividade com o Azure tiver sido restaurada para o computador do Servidor de Backup do Azure, as operações que puderem ser executadas serão determinadas pelo estado de assinatura do Azure. A tabela acima tem detalhes sobre as operações permitidas depois que a máquina estiver "Conectada".

### Tratando os estados de assinatura

É possível fazer uma assinatura do Azure passar de um estado *Expirado* ou *Desprovisionado* para o estado *Ativo*. No entanto, isso terá algumas implicações no comportamento do produto enquanto o estado não for *Ativo*:

- Uma assinatura *Desprovisionada* perderá a funcionalidade durante o período em que estiver desprovisionada. Ao se tornar *Ativa*, a funcionalidade de backup/restauração do produto será reativada. Os dados de backup no disco local também poderão ser recuperados caso tenham sido mantidos por um período de retenção suficientemente grande. No entanto, os dados de backup no Azure serão irremediavelmente perdidos depois da assinatura entrar no estado *Desprovisionado*.
- Uma assinatura *Expirada* só perderá a funcionalidade até ficar *Ativa* novamente. Nenhum backup agendado para o período em que a assinatura estiver *Expirada* será executado.


## Solucionar problemas

Se o servidor do Microsoft Azure Backup falhar com erros durante a fase de instalação (ou no backup ou na restauração), consulte o [documento de códigos de erro](https://support.microsoft.com/kb/3041338) para saber mais. Você também pode consultar as [Perguntas frequentes relacionadas ao Backup do Azure](backup-azure-backup-faq.md)


## Próximas etapas

É possível obter informações detalhadas sobre como [preparar seu ambiente para o DPM](https://technet.microsoft.com/library/hh758176.aspx) no site do Microsoft TechNet. Ele também contém informações sobre as configurações com suporte, nas quais o Servidor de Backup do Azure pode ser implantado e usado.

Você pode usar estes artigos para obter um entendimento mais profundo sobre a proteção da carga de trabalho usando o servidor de Backup do Microsoft Azure.

- [Backup do SQL Server](backup-azure-backup-sql.md)
- [Backup do servidor do SharePoint](backup-azure-backup-sharepoint.md)
- [Backup do servidor alternativo](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0803_2016-->