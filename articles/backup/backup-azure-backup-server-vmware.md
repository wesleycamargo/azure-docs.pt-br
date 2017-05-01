---
title: Usar o Servidor de Backup do Azure para proteger o servidor VMware | Microsoft Docs
description: Fazer backup de um servidor VMware no Azure ou em disco, com o Servidor de Backup do Azure. Use este artigo para proteger sua carga de trabalho do VMware.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/28/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 779841483e72b42725354d85a0f593aee7db8278
ms.lasthandoff: 04/11/2017


---
# <a name="back-up-vmware-server-to-azure"></a>Fazer backup do servidor do VMware no Azure

Este artigo explica como se conectar a um servidor VMware em um Servidor de Backup do Azure para que você possa fazer backup de conteúdo do servidor VMware para a nuvem. Este artigo pressupõe que você já tenha o Servidor de Backup do Azure instalado.

## <a name="create-secure-connection-to-vmware-server"></a>Criar uma conexão segura ao servidor do VMware

Para proteger um servidor VMware, o Servidor de Backup do Azure deve ser capaz de se conectar com segurança ao servidor VMware. Para habilitar a conexão segura, instale um certificado válido no VMware Server e no Servidor de Backup do Azure.

Quando você se conecta ao servidor VMware, se a URL não é segura, você precisa exportar o certificado para que a conexão para o site seja segura.
![exemplo de conexão segura ao servidor do VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

1. Clique em https (com o tachado) e, em seguida, no menu pop-up, clique no link de detalhes.

  Dependendo do seu navegador, talvez seja necessário clicar em **Configurações** > **Mais ferramentas** > **Ferramentas de desenvolvedor**e selecionar a guia Segurança.

  ![exemplo de mensagem de erro de conexão não segura](./media/backup-azure-backup-server-vmware/security-tab.png)

2. Nas informações de detalhes na guia Segurança, clique em **Exibir Certificado**.

  ![exemplo de mensagem de erro de conexão não segura](./media/backup-azure-backup-server-vmware/security-tab-view-certificate.png)

  A caixa de diálogo Certificado é aberta.

3. Na caixa de diálogo Certificado, clique na guia Caminho de Certificação.  

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/certificate-certification-path.png)

  O certificado realçado não é confiável porque, no caso desse certificado, o emissor não pôde ser encontrado. Pode haver outros motivos por que o certificado não é confiável.

4. Para exportar o certificado em seu computador local, clique na guia Detalhes e, em seguida, clique em Copiar para Arquivo.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/certificate-details-tab.png)

  O Assistente para Exportação de Certificados é aberto.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/certificate-wizard1.png)

  Clique em **Avançar** para percorrer o assistente.

5. Na tela do formato do arquivo de exportação, especifique o formato de sua preferência para o certificado. Se você não tiver um formato preferencial, aceite o formato de arquivo padrão para o certificado e clique em **Avançar**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/certificate-wizard1b.png)

6. Na tela Arquivo para Exportação, nomeie seu certificado e, em seguida, clique em Procurar para escolher o local para armazenar o certificado no computador local. Salve o certificado onde você possa encontrá-lo.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/certificate-wizard2.png)

7. Depois de exportar o certificado, vá para o local onde ele foi salvo, clique no certificado e, no menu, selecione **Instalar certificado**.

  O Assistente para Importação de Certificados é aberto.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/cert-import-wizard1.png)

8. No Assistente de Importação de Certificados, selecione **Máquina Local** como o destino para o certificado e clique em **Avançar** para continuar.

9. Na tela do repositório de certificados, selecione **Colocar todos os certificados no repositório a seguir** e clique em **Procurar**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  A caixa de diálogo Selecionar repositório de certificados é aberta.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/cert-store.png)

  Escolha a pasta de destino para os certificados e clique em **OK**. Se você não souber qual pasta a ser usada, escolha Autoridades de Certificação Raiz Confiáveis. A pasta de destino escolhida aparece na caixa de diálogo Repositório de certificados. Clique em **Avançar** para importar o certificado.

10. Na tela Concluindo o Assistente para Importação de Certificados, verifique se o certificado está na pasta desejada e clique em Concluir para concluir o assistente.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  Uma caixa de diálogo é exibida, permitindo que você saiba se a importação foi bem-sucedida.

11. Faça logon na VM VMware para verificar se você tem uma conexão segura com o servidor VMware. O Servidor de Backup do Azure conecta-se ao servidor do VMware em um canal HTTPs seguro. Se você tiver limites de segurança em sua organização e não quiser habilitar o protocolo HTTPs, desabilite a comunicação segura através do Registro. No entanto, é recomendável que você instale os certificados no Servidor de Backup do Azure e VMware no servidor para permitir uma comunicação segura.


## <a name="create-role-and-user-account-on-vmware-server"></a>Criar conta de usuário e função no servidor do VMware

O Servidor de Backup do Azure se comunica com um servidor remoto do VMware por meio da autenticação de credenciais do usuário do VMware especificado. O Servidor de Backup do Azure autentica as credenciais do usuário do VMware para todas as operações de backup. Use o Assistente de Adição de Servidor de Produção do Servidor de Backup do Azure para habilitar o Servidor de Backup do Azure para estabelecer a comunicação segura com o servidor VMware. Há três fases para configurar a relação entre o Servidor de Backup do Azure e 

- Criar uma função de usuário que tenha privilégios atribuídos
- Criar uma conta de usuário com credenciais - nome de usuário e senha
- Adicione a conta de usuário do servidor VMware para o Servidor de Backup do Azure

, ao executar o Assistente para Adição de Servidor de Produção. O par de nome de usuário e senha é armazenado como uma credencial.


### <a name="create-user-role-and-add-privileges"></a>Criar função de usuário e adicionar privilégios
A conta de usuário do VMware, que é especificada na credencial do Servidor de Backup do Azure, deve ter certos privilégios associados. No entanto, privilégios são associados a uma função de usuário, portanto, primeiro vamos criar uma função de usuário e, em seguida, adicionar privilégios específicos para a função. Os privilégios que estão associados à função de usuário para um administrador de backup.

1. Para criar uma nova função de usuário do VMware, faça logon no seu servidor do VMware e, no painel **Navegador**, clique em **Administração**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. Para criar uma nova função, na seção **Administração**, selecione **Funções** e, no painel **Funções**, clique no ícone Adicionar função (o símbolo +).

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  A caixa de diálogo **Criar Função** é aberta.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Na caixa de diálogo **Criar Função**, em **Nome da função**, digite um nome para a função. Neste exemplo, usaremos o nome *BackupAdminRole*. O nome da função pode ser o que você quiser, mas o nome deve ser reconhecível para a função.

4. Selecione os privilégios para aplicá-los à função de usuário. Selecione os privilégios na lista a seguir. Ao selecionar os privilégios, clique na divisa no rótulo pai para expandir o pai e exibir os privilégios de filho. Você não precisa selecionar todos os privilégios do filho dentro de um privilégio pai.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  - Privilege.Datastore.AllocateSpace.label
  - Privilege.Global.ManageCustomerFields.label
  - privilege.Network.Assign.label
  - Privilege.VirtualMachine.Config.AddNewDisk.label
  - Privilege.VirtualMachine.Config.AdvanceConfig.label
  - Privilege.VirtualMachine.Config.ChangeTracking.label
  - Privilege.VirtualMachine.Config.HostUSBDevice.label
  - Privilege.VirtualMachine.Config.SwapPlacement.label  
  - Privilege.VirtualMachine.Interact.PowerOff.label
  - Privilege.VirtualMachine.Inventory.Create.label
  - Privilege.VirtualMachine.Provisioning.DiskRandomRead.summary
  - Privilege.VirtualMachine.State.CreateSnapshot.label
  - Privilege.VirtualMachine.State.RemoveSnapshot.label
</br>

  Depois de selecionar os privilégios, clique em **OK**. A nova função é exibida na lista no painel Funções.

### <a name="create-a-user-account-and-assign-permissions"></a>Criar uma conta de usuário e atribuir permissões

Depois de definir a função de usuário com privilégios, crie uma conta de usuário. Ao criar a conta de usuário, você pode atribuí-la a uma função de usuário específica, que concede à conta os privilégios associados. A conta de usuário tem um nome e uma senha, que fornece as credenciais usadas para autenticação.

1. Para criar uma nova conta de usuário no servidor do VMware, no painel de navegação, clique em **Usuários e Grupos**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  O painel Usuários e Grupos é exibido.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. No painel Usuários e Grupos do VMware, na guia Usuários, clique no ícone Adicionar usuários (o símbolo +).

  A caixa de diálogo Novo Usuário é aberta.

3. A conta de usuário que você cria contém o par de nome e senha de usuário que é usado como credenciais. Na caixa de diálogo Novo Usuário, preencha os campos e clique em **OK**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  A nova conta de usuário aparece na lista.

4. Agora que criou a conta de usuário, você deve associá-la à função de usuário (que tem as permissões desejadas). No painel de navegação, clique em **Permissões Globais**. No painel Permissões Globais, clique na guia **Gerenciar** e, em seguida, clique no ícone Adicionar (o símbolo +).

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  A caixa de diálogo Permissões Globais Raiz - Adicionar Permissão é aberta.

5. Na caixa de diálogo **Permissão Global Raiz- adicionar permissão**, clique em **Adicionar** para escolher o usuário ou grupo.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  A caixa de diálogo Selecionar Usuários/Grupos é aberta.

6. Na caixa de diálogo **Selecionar Usuários/Grupos**, selecione o usuário da conta que você criou e clique em **Adicionar**. A conta de usuário selecionada aparece no campo de usuários. O nome de usuário será exibido no campo de usuários no formato *domínio*`\`*nome de usuário*.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  Clique em **OK** para adicionar os usuários selecionados à caixa de diálogo Adicionar Permissão.

7. Agora que você identificou o usuário, atribua o usuário à função. Na área de função atribuída, no menu suspenso, selecione a função e clique em **OK**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Na guia Gerenciar das Permissões Globais, a nova conta de usuário e a função associada aparecem na lista.


### <a name="add-the-vmware-user-account-credentials-to-azure-backup-server"></a>Adicione as credenciais de conta de usuário do VMware para o Servidor de Backup do Azure

Antes de adicionar o servidor VMware para o Servidor de Backup do Azure, certifique-se de que você instalou a [Atualização 1 para o servidor de Backup do Microsoft Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Clique no ícone seguinte (localizado na área de trabalho de servidor) para abrir o console do Servidor de Backup do Azure.

  ![Ícone do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  Se você não conseguir localizar o ícone na área de trabalho, poderá abrir o Servidor de Backup do Azure na lista de aplicativos instalados. Na lista de aplicativos instalados, o aplicativo de Servidor de Backup do Azure é chamado de Backup do Microsoft Azure.

2. No console do Servidor de Backup do Azure, clique em **Gerenciamento**, em seguida, clique em **Servidores de Produção** e na faixa de opções da ferramenta clique em **VMware gerenciar**.

  ![Console MABS](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  A caixa de diálogo Gerenciar Credenciais é aberta.

  ![caixa de diálogo de credenciais para gerenciar MABS](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Na caixa de diálogo Gerenciar Credenciais, clique em **Adicionar** para abrir a caixa de diálogo Adicionar Credenciais.

4. Na caixa de diálogo Adicionar Credenciais, digite um nome e uma descrição para a nova credencial. O nome de usuário e a senha devem ser os mesmos que você usou ao criar a conta de usuário no servidor do VMware.

  ![caixa de diálogo de credenciais para gerenciar MABS](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog.png)

  Clique em **Adicionar** para adicionar a nova credencial para o Servidor de Backup do Azure. A nova credencial aparece na lista na caixa de diálogo Gerenciar Credenciais.
  ![caixa de diálogo de credenciais para gerenciar MABS](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Clique no **X** no canto superior direito para fechar a caixa de diálogo Gerenciar Credenciais.


## <a name="add-vmware-server-to-azure-backup-server"></a>Adicionar servidor VMware para o Servidor de Backup do Azure

Para abrir o Assistente de Adição de Servidor de Produção

1. No console do Servidor de Backup do Azure, clique em **Gerenciamento**, clique em **Servidor de Produção** e, em seguida, clique em **Adicionar**.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  O Assistente para Adição de Servidor de Produção é aberto.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Na tela Selecionar tipo do Servidor de Produção, selecione servidores VMware e clique em **Avançar**.

3. No endereço IP/nome do servidor, especifique o nome de domínio totalmente qualificado (FQDN) ou endereço IP do servidor VMware. Você pode inserir o nome de VMware se todos os servidores ESXi são gerenciados pelo mesmo Vcenter.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Na caixa de diálogo **Porta SSL**, insira a porta usada para se comunicar com o servidor VMware. Use a porta 443, que é a porta padrão, a menos que você saiba que uma porta diferente é necessária.

5. Na caixa de diálogo **Especificar Credenciais**, você pode criar uma nova credencial ou selecionar uma credencial existente. Na seção anterior, você criou uma credencial. Selecione essa credencial.

  Se não existir uma credencial disponível ou você precisar criar uma nova credencial, clique em **Adicionar nova credencial**, crie a nova credencial e clique em **OK**.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/specify-new-cred.png)

6. Clique em **Adicionar** para adicionar o servidor VMware à lista de **Servidores VMware Adicionados**e clique em **Avançar para ir para a próxima tela do assistente.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na tela **Tarefas**, clique em **Adicionar** para adicionar o servidor VMware especificado para o Servidor de Backup do Azure.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Como o backup do servidor VMware é um backup sem agente, a adição do novo servidor acontece em segundos. Você pode adicionar vários servidores VMware para o Servidor de Backup do Azure, repetindo as etapas anteriores nesta seção.

Agora que você adicionou um servidor VMware para o Servidor de Backup do Azure, a próxima etapa é criar um grupo de proteção. O grupo de proteção especifica os diversos detalhes para retenção de curto e longo prazo e é onde você pode definir e aplica a política de backup. A política de backup é o agendamento para quando os backups são realizados e do que é feito backup.


## <a name="configure-a-protection-group-to-back-up-vmware-server"></a>Configurar um grupo de proteção para fazer backup do servidor do VMware

Se você não tiver usado o System Center Data Protection Manager ou Servidor de Backup do Azure antes, confira o tópico [Planejar backups em disco](https://technet.microsoft.com/library/hh758026.aspx), para preparar o ambiente de hardware. Depois que você tiver verificado que tem um armazenamento adequado, use o Assistente de Novo Grupo de Proteção para adicionar as VMs específicas.

1. No console do Servidor de Backup do Azure, clique em **Proteção**e, na faixa de opções da ferramenta, clique em **Novo** para abrir o assistente Criar Novo Grupo de Proteção.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  O Assistente de Novo Grupo de Proteção é aberto.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  Clique em **Avançar** para ir para a tela **Selecionar tipo do grupo de proteção**.

2. Na tela Selecionar Tipo do Grupo de Proteção, escolha **Servidores** e clique em **Próximo**.

3. Na tela Selecionar Membros do Grupo, você pode ver os membros disponíveis e os membros que foram selecionados. Selecione os membros que você deseja proteger e clique em **Avançar**.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/server-add-to-selected-members.png)

  Ao selecionar um membro, se você selecionar uma pasta que contenha outras pastas ou VMs, essas pastas e VMs também serão selecionadas. A inclusão das pastas e VMs na pasta pai é chamada de proteção de nível de pasta. Você pode excluir qualquer pasta ou a VM desmarcando a caixa de seleção.

  Se uma VM ou uma pasta que contenha uma VM já estiver protegida no Azure, você não poderá selecionar essa VM novamente. Ou seja, depois que uma máquina virtual é protegida no Azure, ela não pode ser protegida novamente, o que impede que pontos de recuperação duplicados sejam criados para uma máquina virtual. Se você quiser ver qual Servidor de Backup do Azure já protege um membro, passe o mouse sobre o membro para ver o nome do servidor de proteção.

4. Na tela Selecionar método de proteção de dados, digite um nome para o grupo de proteção. Em seguida, para **Método de proteção**, selecione onde você deseja fazer backup dos dados. Backup dos dados em disco para proteção de curto prazo. É feito backup dos dados na nuvem (Azure) para proteção de longo prazo. Clique em **Avançar** para prosseguir para o intervalo de proteção de curto prazo.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Na tela Especificar Objetivos de Curto Prazo, para **Intervalo de retenção**, especifique o número de dias em que deseja reter pontos de recuperação *armazenado em disco*. Se você quiser alterar o tempo e os dias quando os pontos de recuperação são criados, clique em **Modificar**. Os pontos de recuperação de curto prazo são backups completos. Não são backups incrementais. Quando estiver satisfeito com as metas de curto prazo, clique em **Avançar**.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Na tela Examinar Alocação do Disco, examine e, se necessário, modifique o espaço em disco para as VMs. As alocações de disco recomendadas são baseadas no período de retenção especificado na tela anterior, no tipo de carga de trabalho e no tamanho dos dados protegidos (identificados na etapa 3).  

  - Tamanho de dados - tamanho dos dados no grupo de proteção.
  - Espaço em disco - a quantidade de espaço em disco recomendada para o grupo de proteção. Se você quiser modificar essa configuração, deverá alocar espaço total ligeiramente maior do que o valor de acordo com o qual estima que cada fonte de dados aumentará.
  - Colocar os dados - se você ativar a colocação, várias fontes de dados na proteção poderão ser mapeadas para uma única réplica e volume de ponto de recuperação. A colocação não tem suporte para todas as cargas de trabalho.
  - Aumentar automaticamente - se você habilitar essa configuração, se os dados do grupo protegido ultrapassarem a alocação inicial, o DPM tentará aumentar o tamanho do disco em 25%.
  - Detalhes do pool de armazenamento - mostra o status atual do armazenamento do pool, incluindo o tamanho do disco total e restante.

    ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

  Ao terminar, clique em **Avançar**.

7. Na tela Escolher Método de Criação de Réplica, especifique como deseja gerar a cópia inicial ou a réplica dos dados protegidos no Servidor de Backup do Azure.

  O padrão é **Automaticamente pela rede** e **Agora**. Se você usar o padrão, é recomendável especificar um horário de pico. Escolha **Mair tarde** e especifique um dia e hora.

  Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando mídia removível.

  Depois de fazer suas escolhas, clique em **Avançar**.

  ![Assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Na tela **Opções de Verificação de Consistência**, selecione como e quando automatizar as verificações de consistência. Você pode executar verificações de consistência quando dados de réplica se tornam inconsistentes ou de acordo com uma programação definida.

  Se você não desejar configurar a verificação de consistência automática, poderá executar uma verificação manual a qualquer momento clicando com o botão direito do mouse no grupo de proteção na área de proteção do console do Servidor de Backup do Azure e selecionando Executar Verificação de Consistência.

  Clique em **Avançar** para se mover para a próxima tela.

9. Na tela **Especificar Dados da Proteção Online**, selecione as fontes de dados que você deseja proteger. Você pode selecionar os membros individualmente ou clicar em **Selecionar tudo** para escolher todos os membros. Depois de escolher os membros, clique em **Avançar*.

  ![Assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Na tela **Especificar Agendamento de Backup Online**, especifique a agenda para gerar pontos de recuperação de backup em disco. Quando o ponto de recuperação é gerado, ele é transferido para o cofre de Serviços de Recuperação no Azure. Quando estiver satisfeito com o agendamento de backup online, clique em **Avançar**.

  ![Assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Na tela Especificar a Política de Retenção Online, indique por quanto tempo deseja manter os dados de backup no Azure. Depois de definir a política, clique em **Avançar**.

  ![Assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/retention-policy.png)

  Não há um tempo limite para manter dados no Azure. Ao armazenar dados de ponto de recuperação no Azure, o único limite é que você não pode ter mais de 9999 pontos de recuperação por instância protegida. Neste exemplo, a instância protegida é o servidor VMware.

12. Na tela de resumo, examine os detalhes do grupo de proteção. Observe os membros do grupo e as configurações. Quando estiver satisfeito com as configurações, clique em **Criar Grupo**.

  ![Assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Próximas etapas
Se você usar o Servidor de Backup do Azure para proteger as cargas de trabalho do VMware, poderá estar interessado em usar o Servidor de Backup do Azure para proteger o [Microsoft Exchange server](./backup-azure-exchange-mabs.md), um [farm do Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md) ou um [SQL Server](./backup-azure-sql-mabs.md).

Confira [Solucionar problemas de Servidor de Backup do Azure](./backup-azure-mabs-troubleshoot.md) para obter informações sobre problemas ao registrar o agente e configurar o grupo de proteção e problemas com trabalhos de backup.

