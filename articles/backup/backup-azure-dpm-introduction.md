<properties
	pageTitle="Introdução ao backup do DPM do Azure | Microsoft Azure"
	description="Uma introdução ao backup de servidores de DPM usando o serviço de Backup do Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="jimpark"/>

# Introdução ao Backup do DPM do Azure

Este artigo fornece uma introdução ao uso do Backup do Microsoft Azure para proteger seus servidores e cargas de trabalho do Center Data Protection Manager. Lendo-o, você entenderá:

- Como funciona o backup do servidor DPM do Azure
- Os pré-requisitos para obter uma experiência positiva de backup
- Os erros típicos encontrados e como lidar com eles
- Cenários com suporte

O System Center DPM faz backup dos dados de arquivos e aplicativos. O backup dos dados no DPM pode ser feito em fita, em disco, ou no Azure com o Backup do Microsoft Azure. O DPM interage com o Backup do Azure da seguinte maneira:

- **DPM implantado como servidor físico ou máquina virtual local** — Se o DPM for implantado como servidor físico ou máquina virtual local Hyper-V, é possível fazer backup dos dados em um cofre de Backup do Azure além do backup em disco e em fita.
- **DPM implantado como máquina virtual do Azure** — No System Center 2012 R2 com atualização 3, o DPM pode ser implantado como máquina virtual do Azure. Se o DPM for implantado como máquina virtual do Azure, é possível fazer backup de dados em discos do Azure anexados à máquina virtual do Azure do DPM, ou descarregar o armazenamento de dados por meio de backup em um cofre de Backup do Azure.

## Por que fazer backup de seus servidores DPM?

Os benefícios comerciais do uso do Backup do Azure para backup de servidores DPM são:

- Para a implantação do DPM local, é possível usar o backup do Azure como alternativa para a implantação de longo prazo em fita.
- Para implantações do DPM no Azure, o Backup do Azure possibilita descarregar o armazenamento do disco do Azure, permitindo escalar verticalmente ao armazenar os dados mais antigos no Backup do Azure e os dados novos em disco.

## Como funciona o backup do servidor DPM?
Para fazer backup de uma máquina virtual, primeiro é necessário um instantâneo pontual dos dados. O serviço de Backup do Azure inicia o trabalho de backup no horário agendado e dispara a extensão de backup para obter um instantâneo. A extensão de backup é coordenada com o serviço VSS no convidado para obter consistência e chama a API de instantâneo de blob do serviço de Armazenamento do Azure depois que a consistência é atingida. Isso é feito para obter um instantâneo consistente dos discos da máquina virtual, sem a necessidade de desligá-la.

Depois que o instantâneo é criado, os dados são transferidos pelo serviço do Backup do Azure para o cofre de backup. O serviço se encarrega de identificar e transferir somente os blocos que foram alterados desde o último backup, tornando o armazenamento e a rede de backups eficientes. Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado. Esse ponto de recuperação pode ser visto no portal de gerenciamento do Azure.

>[AZURE.NOTE]Para máquinas virtuais do Linux, é possível apenas o backup consistente com arquivos.

## Pré-requisitos
Prepare o Backup do Azure para fazer backup dos dados do DPM da seguinte maneira:

1. **Crie um cofre de Backup** — Crie um cofre no console do Backup do Azure.
2. **Baixe credenciais do cofre** — No Backup do Azure, carregue o certificado de gerenciamento que você criou para o cofre.
3. **Instale o agente de Backup do Azure e registre o servidor** — No Backup do Azure, instale o agente em cada servidor DPM e registre o servidor DPM no cofre de backup.

### Criar um cofre de backup
Para iniciar o backup de suas máquinas virtuais do Azure, você precisa primeiro criar um cofre de backup. O cofre é uma entidade que armazena todos os backups e pontos de recuperação que foram criados ao longo do tempo. O cofre também contém as políticas de backup que serão aplicadas às máquinas virtuais que passarão por backup.

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com/).
2. Clique em **Novo** -> **Serviços de Dados** -> **Serviços de Recuperação** -> **Cofre de Backup** > **Criação Rápida**. Se você tem várias assinaturas associadas à sua conta organizacional, escolha a assinatura correta a ser associada ao cofre de backup. Em cada assinatura do Azure, é possível ter vários cofres de backup para organizar os dados que estão sendo protegidos.
3. Em **Nome**, digite um nome amigável para identificar o cofre. Ele precisa ser exclusivo para cada assinatura.
4. Em **Região**, selecione a região geográfica para o cofre. Observe que o cofre deve estar na mesma região que as máquinas virtuais que você deseja proteger. Se você tiver máquinas virtuais em diferentes regiões, crie um cofre em cada uma delas. Não é necessário especificar contas de armazenamento para armazenar os dados de backup – o cofre de backup e o serviço de Backup do Azure cuidarão disso automaticamente.
    > [AZURE.NOTE][regiões com suporte](http://azure.microsoft.com/regions/#services)

5. Em **Assinatura**, digite a assinatura do Azure que você deseja usar com o cofre de backup.
6. Clique em **Criar cofre**. ![Criar cofre de backup](./media/backup-azure-dpm-introduction/backup_vaultcreate.png)

    Pode levar algum tempo para que o cofre de backup seja criado. Monitore as notificações de status na parte inferior do portal. ![Criar notificação de cofre](./media/backup-azure-dpm-introduction/creating-vault.png)

    Uma mensagem confirmará que o cofre foi criado com êxito e ele será listado na página de Serviços de Recuperação como **Ativo**.

    ![Lista de cofres de backup](./media/backup-azure-dpm-introduction/backup_vaultslist.png)

7. Clique no cofre de backup para ir até a página **Início Rápido**, em que são mostradas as instruções para backup de servidores DPM. ![Instruções de backup de máquina virtual na página Painel](./media/backup-azure-dpm-introduction/vmbackup-instructions.png)

    > [AZURE.NOTE]Certifique-se de que a opção de redundância de armazenamento apropriada esteja selecionada logo depois que o cofre for criado. Leia mais sobre [como definir a opção de redundância de armazenamento no cofre de backup](http://azure.microsoft.com/documentation/articles/backup-azure-backup-create-vault/#azure-backup---storage-redundancy-options).

### Baixar as credenciais do cofre
1. Clique em **Serviços de Recuperação** e clique em cofre de backup. Na página **Início rápido**, clique em **Baixar as credenciais do cofre** para baixar o arquivo de credenciais e salve em um local seguro. Não é possível editar as credenciais, então não é necessário abrir o local. Por motivos de segurança, a chave no arquivo expira após 48 horas.

2. Copie o arquivo para um local seguro e que possa ser acessado facilmente pelos servidores DPM que você deseja registrar no cofre de Backup do Azure. Você precisará selecionar o arquivo ao instalar o agente de Backup do Azure.

### Instale o agente de Backup do Azure e registre o servidor
É necessário baixar o arquivo de instalação do agente e executá-lo em cada servidor DPM que contenha os dados que você deseja fazer backup. Os agentes são armazenados no **Centro de Download do Azure** e têm o seu próprio processo de instalação. Quando você executa a instalação, o Agente é instalado e o servidor DPM é registrado com o cofre. Observe que:

- Você precisará de permissões administrativas no servidor DPM para instalar o Agente.
- Para instalar em vários servidores DPM, você pode colocar o arquivo do instalador em um recurso de rede compartilhada ou usar a Política de Grupo ou produtos de gerenciamento, tais como o System Center Configuration Manager para instalar o agente.
- Não é necessário reiniciar o servidor DPM após a instalação.

#### Para instalar o agente de backup e registrar o servidor

1. Na página **Início Rápido** do cofre do Backup do Azure em **Baixar o Agente do Backup do Azure**, selecione clique em **Para Windows Server, System Center Data Protection Manager ou cliente Windows**. Baixe o aplicativo no servidor DPM em que você deseja executá-lo.
2. Execute o arquivo de instalação **MARSAgentInstaller.exe**. Aceite os termos de serviço e selecione para instalar qualquer software de pré-requisito ausente.
3. Na página **Configurações de instalação**, selecione a **Pasta de instalação** e **Local do cache**.

    A pasta do local de cache padrão é <system drive>:\Program Files\Azure Backup Agent. No local do cache, o processo de instalação cria uma pasta chamada de **Scratch** na pasta **Azure Backup Agent**. O local do cache deve ter pelo menos 2,5 gigabytes (GB) (ou 10% do tamanho dos dados dos quais será feito backup no Azure) de espaço livre. Somente os administradores e membros do sistema local do grupo Administradores têm acesso ao diretório de cache para evitar ataques de negação de serviço.

4. Na página **Configuração de Proxy**, defina as configurações personalizadas de proxy para a conexão entre o Agente e o Azure. Se você não configurar todas as configurações, as configurações padrão de acesso de Internet no servidor DPM serão usadas. Observe que, se você estiver usando um servidor proxy que requeira autenticação, é necessário inserir os detalhes nesta página.
5. Na página **Opt-In de atualizações da Microsoft**, é recomendável habilitar atualizações. Se o servidor já estiver habilitado para atualizações automáticas, essa etapa será ignorada. Observe que as configurações do Microsoft Update são para todas as atualizações de produtos da Microsoft e não são exclusivas do Agente de Backup do Azure.
6. A página de **Instalação** é exibida. A instalação verifica se o software necessário está instalado e conclui a configuração. Quando estiver pronta, você receberá uma mensagem informando que o Agente de Backup do Azure foi instalado com êxito. Neste momento, você poderá verificar se há atualizações. É recomendável que você permita que a verificação de atualizações seja realizada.
7. Clique em **Prosseguir para o registro** para registrar o servidor no cofre.
8. Na página **Identificação de cofre**, selecione o arquivo de registro do cofre gerado no cofre de Backup do Azure.
9. Na página **Configuração de criptografia**, especifique os detalhes de senha ou gere automaticamente uma senha.
10. Clique em Gerar senha seguido de Copiar para a área de transferência. Você receberá uma mensagem de que sua senha foi copiada para a área de transferência. Agora é uma boa hora para abrir o bloco de notas e colar a senha da área de transferência e salvar o arquivo, e também imprimir o arquivo e guardá-lo. Clique em Registro para registrar o servidor DPM com o Cofre de Backup.

    > [AZURE.TIP]
11. Clique em **Registrar**.

    Após a conclusão do registro, o console do DPM mostra a disponibilidade do Backup do Azure.

    O Backup do Azure sempre criptografará os dados na origem com a senha (cadeia de caracteres alfanuméricos) especificada por você ou gerada automaticamente.
    >[AZURE.NOTE]O Backup do Azure nunca mantém a senha e, se você perdê-la, os dados não poderão ser restaurados ou recuperados. É altamente recomendável salvar a chave em um local externo.

Quando você especificar uma senha e clicar em **Concluir**, demora alguns segundos para que o agente registre o servidor de produção no cofre de backup. Assim que o registro com o cofre termina um resumo, a página **Registro de servidor** é exibida.

## Requisitos (e limitações)

- O DPM pode ser executado como servidor físico ou máquina virtual Hyper-V instalado no System Center 2012 SP1 ou System Center 2012 R2. Também pode ser executado como máquina virtual do Azure em execução no System Center 2012 R2 com pelo menos Pacote cumulativo de atualizações 3 do DPM 2012 R2 ou máquina virtual do Windows em VMWare em execução no System Center 2012 R2 com pelo menos Pacote cumulativo de atualizações 5.
- Se você estiver executando o DPM com o System Center 2012 SP1, instale o Rollup de atualização 2 do System Center Data Protection Manager SP1. Isso é necessário antes da instalação do Agente de Backup do Azure.
- O servidor DPM deve ter o Windows PowerShell e o .net Framework 4.5 instalados.
- O DPM pode fazer backup da maioria das cargas de trabalho no Backup do Azure. Para obter uma lista completa do que tem suporte, consulte os itens de suporte do Backup do Azure abaixo.
- Os dados armazenados no Backup do Azure não podem ser recuperados com a opção "copiar em fita".
- Você precisará de uma conta Azure com o recurso de Backup do Azure habilitado. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Leia sobre os [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
- O uso o Backup do Azure requer que o Agente de Backup do Azure esteja instalado nos servidores onde você deseja fazer backup. Cada servidor deve ter pelo menos 2,5 GB de espaço de armazenamento livre local para o local do cache, embora sejam recomendados 15 GB de espaço de armazenamento livre local para o local do cache.
- Os dados serão armazenados no armazenamento do cofre do Azure. Não há nenhum limite para a quantidade de dados de backup em um cofre de Backup do Azure, mas o tamanho de uma fonte de dados (por exemplo, máquina virtual ou banco de dados) não deve exceder 1,65 TB.

No Azure, é possível fazer backup dos seguintes tipos de arquivo:

- Criptografados (apenas backups completos)
- Compactados (suporte para backups incrementais)
- Esparsos (suporte para backups incrementais)
- Compactados e esparsos (tratados como esparsos)

E os seguintes não têm suporte:

- Não há suporte para servidores em sistemas de arquivo que diferenciam maiúsculas de minúsculas.
- Links físicos (ignorados)
- Pontos de nova análise (ignorados)
- Criptografados e compactados (ignorados)
- Criptografados e esparsos (ignorados)
- Fluxo compactado
- Fluxo esparso

>[AZURE.NOTE]No System Center 2012 DPM com SP1 em diante, é possível fazer backup de cargas de trabalho protegidas por DPM para o Azure usando o Backup do Microsoft Azure.

<!---HONumber=62-->