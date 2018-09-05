---
title: Usar o DPM para fazer backup de cargas de trabalho no Azure
description: Uma introdução ao backup de dados do DPM em um cofre dos Serviços de Recuperação do Azure.
services: backup
author: adigan
manager: nkolli
keywords: Gerenciador de proteção de dados do System Center, gerenciador de proteção de dados, backup do dpm
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: adigan
ms.openlocfilehash: 873e7066bcf51b32c3a7a54e845ffd5a744f407f
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745428"
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparando-se para fazer backup de cargas de trabalho no Azure com o DPM
> [!div class="op_single_selector"]
> * [Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Este artigo explica como fazer backup de dados do DPM (System Center Data Protection Manager) no Azure; incluindo:

* Como fazer backup de dados do servidor DPM no Azure
* Os pré-requisitos para obter uma experiência positiva de backup
* Os erros típicos encontrados e como lidar com eles
* Cenários com suporte

> [!NOTE]
> O Azure tem dois modelos de implantação para a criação e o trabalho com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo fornece informações e procedimentos para a restauração de VMs implantadas usando o modelo do Gerenciador de Recursos.
>
>

O [System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) faz backup dos dados de arquivos e aplicativos. Mais informações sobre as cargas de trabalho compatíveis podem ser encontradas [aqui](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). O backup dos dados no DPM pode ser feito em fita, em disco, ou no Azure com o Backup do Microsoft Azure. O DPM interage com o Backup do Azure da seguinte maneira:

* **DPM implantado como servidor físico ou máquina virtual local** – o DPM implantado como servidor físico ou máquina virtual local Hyper-V faz backup dos dados em um cofre dos Serviços de Recuperação além do backup em disco e em fita.
* **DPM implantado como uma máquina virtual do Azure** — A partir do System Center 2012 R2 com a Atualização 3 ativada, você pode implantar o DPM em uma máquina virtual do Azure. Se o DPM for implantado como uma máquina virtual do Azure, você poderá fazer backup de dados em discos do Azure anexados à VM ou descarregar o armazenamento de dados fazendo backup em uma área segura dos Serviços de Recuperação.

## <a name="why-back-up-dpm-to-azure"></a>Por que fazer backup do DPM no Azure?
Os benefícios comerciais do backup de servidores DPM para o Azure incluem:

* Para implantação do DPM no local, utilize o Azure como uma alternativa à implantação de longo prazo em fita.
* Para implantar o DPM em uma VM no Azure, descarregue o armazenamento do disco do Azure. O armazenamento de dados mais antigos no cofre dos Serviços de Recuperação permite escalar verticalmente os negócios, armazenando novos dados no disco.

## <a name="prerequisites"></a>Pré-requisitos
Prepare o Backup do Azure para fazer backup dos dados do DPM da seguinte maneira:

1. **Criar um cofre dos Serviços de Recuperação** – crie um cofre no portal do Azure.
2. **Baixar credenciais do cofre** – baixe as credenciais que você usa para registrar o servidor DPM com a área segura dos Serviços de Recuperação.
3. **Instalar o Agente de Backup do Azure** — Instale o agente em cada servidor DPM.
4. **Registrar o servidor** — Registre o servidor DPM com o cofre dos Serviços de Recuperação.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Definições importantes
Aqui estão algumas definições importantes do backup para Azure for DPM:

1. **Credencial do Cofre** — As Credenciais do Cofre são necessárias para autenticar o computador para enviar os dados de backup para um cofre identificado no serviço de Backup do Azure. Pode ser baixado do cofre e é válido por 48 horas.
2. **Senha** — A Senha é usada para criptografar os backups para a nuvem. Salve o arquivo em um local seguro, ele será obrigatório durante uma operação de recuperação.
3. **PIN de Segurança** — Se você tiver habilitado as [Configurações de Segurança](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) do cofre, o PIN de Segurança será necessário para executar operações de backup essenciais. Essa autenticação multifator adiciona outra camada de segurança. 
4. **Pasta de recuperação** — é a frase para a qual os backups da nuvem são temporariamente baixados durante recuperações de nuvem. Seu tamanho deve ser aproximadamente igual ao tamanho dos itens de backup que você deseja recuperar em paralelo.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="edit-storage-replication"></a>Editar a replicação de armazenamento

A replicação de armazenamento permite que você escolha entre o armazenamento com redundância geográfica e armazenamento com redundância local. Por padrão, seu cofre tem armazenamento com redundância geográfica. Se o cofre for seu backup principal, deixe a opção definida como armazenamento com redundância geográfica. Se você quiser uma opção mais barata que não seja tão durável, use o procedimento a seguir para configurar o armazenamento com redundância local. Leia mais sobre as opções de armazenamento com [redundância geográfica](../storage/common/storage-redundancy-grs.md) e [redundância local](../storage/common/storage-redundancy-lrs.md) na [Visão geral da replicação do Armazenamento do Azure](../storage/common/storage-redundancy.md).

Para editar a configuração de replicação de armazenamento:

> [!NOTE] 
> Configure a replicação de armazenamento antes de disparar o backup inicial. Se você decidir alterar a configuração de replicação de armazenamento após o backup do item protegido, será necessário interromper a proteção no cofre antes de mudar a configuração de armazenamento.
>  

1. Selecione o cofre e abra o respectivo painel.

2. Na seção **Gerenciar**, clique em **Infraestrutura de Backup**.

3. No menu **Configuração de Backup**, escolha a opção de replicação de armazenamento para o cofre.

    ![Lista de cofres de backup](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

    Depois de escolher a opção de armazenamento para o cofre, você estará pronto para associar a VM ao cofre. Para iniciar a associação, você deverá descobrir e registrar as máquinas virtuais do Azure.

## <a name="download-vault-credentials"></a>Baixar as credenciais do cofre
O arquivo de credenciais do cofre é um certificado gerado pelo portal para cada cofre de backup. O portal, em seguida, carrega a chave pública no ACS (Serviço de Controle de Acesso). Durante o fluxo de trabalho de registro do computador, a chave privada do certificado é disponibilizada para o usuário, o que autentica o computador. Com base na autenticação, o serviço de Backup do Azure envia dados para o cofre identificado.

As credenciais do cofre são usadas somente durante o fluxo de trabalho de registro. É responsabilidade do usuário garantir que o arquivo de credenciais do cofre não seja comprometido. Se o controle das credenciais for perdido, as credenciais do cofre poderão ser usadas para registrar outros computadores no cofre. No entanto, os dados de backup são criptografados usando uma frase secreta que pertence ao cliente, por isso os dados de backup existentes não poderão ser comprometidos. Para atenuar esse problema, as credenciais do cofre se expiram após 48 horas. Baixe as novas credenciais do cofre tantas vezes quantas forem necessárias. No entanto, apenas o último arquivo de credencial de cofre pode ser usado durante o fluxo de trabalho de registro.

O arquivo de credencial do cofre é baixado por meio de um canal seguro no Portal do Azure. O serviço de Backup do Azure não tem ciência da chave privada do certificado e a chave privada não está disponível no portal ou no serviço. Use as etapas a seguir para baixar o arquivo de credenciais do cofre para um computador local.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Abra o cofre dos Serviços de Recuperação que você deseja registrar em um servidor do DPM.

3. O menu de configurações abre por padrão. Se estiver fechado, clique em **Configurações** no painel do cofre para abrir o menu. No menu **Configurações**, clique em **Propriedades**.

    ![Menu Abrir Cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Na Página de propriedades, em **Credenciais de Backup**, clique em **Baixar**. O portal gera o arquivo de credencial de cofre, que fica disponível para download.

    ![Baixar](./media/backup-azure-dpm-introduction/vault-credentials.png)

O portal gera uma credencial de cofre usando uma combinação do nome do cofre com a data atual. Clique em **Salvar** para baixar as credenciais do cofre para a pasta de downloads da conta local ou selecione Salvar Como no menu Salvar para especificar um local para as credenciais do cofre. Demorará um minuto para que o arquivo seja gerado.

### <a name="note"></a>Observação
* Certifique-se de que as credenciais do cofre sejam salvas em um local que pode ser acessado no seu computador. Se forem armazenadas em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.
* O arquivo de credenciais do cofre é usado somente durante o fluxo de trabalho de registro.
* O arquivo de credenciais do cofre expira após 48 horas e pode ser baixado no portal.

## <a name="install-backup-agent"></a>Instalar o Backup Agent
Após a criação do cofre de Backup do Azure, um agente deverá ser instalado em cada um dos computadores com o Windows (o Windows Server, o cliente do Windows, o servidor do System Center Data Protection Manager ou o computador do Azure Backup Server), o que permitirá o backup de dados e de aplicativos no Azure.

1. Abra o cofre dos Serviços de Recuperação no qual você deseja registrar o computador do DPM.
2. O menu de configurações abre por padrão. Se estiver fechado, clique em **Configurações** para abrir o menu de configurações. No menu Configurações, clique em **Propriedades**.

    ![Menu Abrir Cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na página de Configurações, clique em **Baixar** no **Agente de Backup do Azure**.

    ![Baixar](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Quando o agente for baixado, execute o MARSAgentInstaller.exe para iniciar a instalação do Agente de Backup do Azure. Escolha a pasta de instalação e a pasta de rascunho necessárias para o agente. O local do cache especificado deve ter espaço livre, que é pelo menos 5% dos dados de backup.

4. Se você usar um servidor proxy para se conectar à Internet, na tela **Configuração de Proxy** , insira os detalhes do servidor proxy. Se você usar um proxy autenticado, insira os detalhes de nome de usuário e senha nessa tela.

5. O agente de Backup do Azure instala o .NET Framework 4.5 e o Windows PowerShell (se ele ainda não estiver disponível) para concluir a instalação.

6. Depois que o agente for instalado, **Feche** a janela.

   ![Feche](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

7. Para **Registrar o servidor DPM** no cofre, na guia **Gerenciamento**, clique em **Online**. Em seguida, selecione **Registrar**. Será aberto o Assistente para Registrar a Instalação.

8. Se você usar um servidor proxy para se conectar à Internet, na tela **Configuração de Proxy** , insira os detalhes do servidor proxy. Se você usar um proxy autenticado, insira os detalhes de nome de usuário e senha nessa tela.

    ![Configuração de Proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Na tela de credenciais do cofre, procure e selecione o arquivo de credenciais do cofre que foi baixado anteriormente.

    ![Credenciais do cofre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    O arquivo de credenciais do cofre é válido somente por 48 horas (após o download do portal). Se você encontrar qualquer erro nessa tela (por exemplo "o arquivo de credenciais do cofre fornecido expirou"), faça logon no Portal do Azure e baixe o arquivo de credenciais do cofre novamente.

    Certifique-se de que o arquivo de credenciais do cofre esteja disponível em um local que pode ser acessado pelo aplicativo de instalação. Se você encontrar erros relacionados a acesso, copie o arquivo de credenciais do cofre para um local temporário nesse computador e repita a operação.

    Se você encontrar um erro de credencial de cofre inválida (por exemplo, “Credenciais do cofre fornecidas inválidas”), significa que o arquivo está corrompido ou não tem as últimas credenciais associadas ao serviço de recuperação. Repita a operação depois de baixar um novo arquivo de credencial de cofre no portal. Esse erro normalmente será exibido se o usuário clicar na opção **Baixar credencial de cofre** no portal do Azure, em uma sequência rápida. Nesse caso, apenas o segundo arquivo de credencial de cofre é válido.

10. Para controlar o uso da largura de banda durante o trabalho e as horas de folga, na tela **Configuração de Limitação** , você pode definir os limites de uso de largura de banda e definir as horas de trabalho e de folga.

    ![Configuração de Limitação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. Na tela **Configuração de Pasta de Recuperação** , navegue até a pasta em que os arquivos baixados do Azure serão preparados temporariamente.

    ![Configuração de Pasta de Recuperação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. Na tela **Configuração de criptografia** , você pode gerar uma senha ou fornecer uma (mínio de 16 caracteres). Lembre-se de salvar a senha em um local seguro.

    ![Criptografia](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Se a senha for perdida ou esquecida, a Microsoft não poderá ajudar na recuperação dos dados de backup. O usuário final é proprietário da senha de criptografia, isto é, a Microsoft não tem visibilidade da senha que é usada pelo usuário final. Salve o arquivo em um local seguro, pois ela é obrigatória durante uma operação de recuperação.
    >
    >

13. Depois de clicar no botão **Registrar** , o computador estará registrado com êxito no cofre e agora você estará pronto para iniciar o backup no Microsoft Azure.

14. Ao usar o Data Protection Manager, é possível modificar as configurações especificadas durante o fluxo de trabalho de registro clicando na opção **Configurar** ao selecionar **Online** na guia **Gerenciamento**.

## <a name="requirements-and-limitations"></a>Requisitos (e limitações)
* O DPM pode ser executado como servidor físico ou máquina virtual Hyper-V instalado no System Center 2012 SP1 ou System Center 2012 R2. Também pode ser executado como máquina virtual do Azure em execução no System Center 2012 R2 com pelo menos Pacote cumulativo de atualizações 3 do DPM 2012 R2 ou máquina virtual do Windows em VMWare em execução no System Center 2012 R2 com pelo menos Pacote cumulativo de atualizações 5.
* Se você estiver executando o DPM com o System Center 2012 SP1, instale o Rollup de atualização 2 do System Center Data Protection Manager SP1. Isso é necessário antes da instalação do Agente de Backup do Azure.
* O servidor DPM deve ter o Windows PowerShell e o .net Framework 4.5 instalados.
* O DPM pode fazer backup da maioria das cargas de trabalho no Backup do Azure. Para obter uma lista completa do que tem suporte, consulte os itens de suporte do Backup do Azure abaixo.
* Os dados armazenados no Backup do Azure não podem ser recuperados com a opção "copiar em fita".
* Você precisará de uma conta Azure com o recurso de Backup do Azure habilitado. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Leia sobre os [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
* O uso o Backup do Azure requer que o Agente de Backup do Azure esteja instalado nos servidores onde você deseja fazer backup. Cada servidor deve ter pelo menos 5% do tamanho dos dados de que está sendo feito backup, disponível como armazenamento local livre. Por exemplo, um backup de 100 GB de dados requer um mínimo de 5 GB de espaço livre na localização temporária.
* Os dados serão armazenados no armazenamento do cofre do Azure. Não há nenhum limite para a quantidade de dados de backup em um cofre de Backup do Azure, mas o tamanho de uma fonte de dados (por exemplo, máquina virtual ou banco de dados) não deve ultrapassar 54400 GB.

No Azure, é possível fazer backup dos seguintes tipos de arquivo:

* Criptografados (apenas backups completos)
* Compactados (suporte para backups incrementais)
* Esparsos (suporte para backups incrementais)
* Compactados e esparsos (tratados como esparsos)

E os seguintes não têm suporte:

* Não há suporte para servidores em sistemas de arquivo que diferenciam maiúsculas de minúsculas.
* Links físicos (ignorados)
* Pontos de nova análise (ignorados)
* Criptografados e compactados (ignorados)
* Criptografados e esparsos (ignorados)
* Fluxo compactado
* Fluxo esparso

> [!NOTE]
> Do System Center 2012 DPM com SP1 em diante, você pode fazer backup de cargas de trabalho protegidas no Azure.
>
>
