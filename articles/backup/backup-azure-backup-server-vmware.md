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
ms.date: 04/20/2017
ms.author: markgal;
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: faa3c9d5de759e4d8c0589153ea93e83a580f983
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="back-up-vmware-server-to-azure"></a>Fazer backup do servidor do VMware no Azure

Este artigo explica como configurar um servidor de Backup do Azure para proteger a carga de trabalho de servidor do VMware. Este artigo pressupõe que você já tenha o Servidor de Backup do Azure instalado. Se você não tiver o servidor de Backup do Azure instalado, consulte o artigo [Preparar-se para fazer backup de cargas de trabalho usando o servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).

O Servidor de Backup do Azure pode fazer backup ou proteger, VMware vCenter server versões 6.0 e 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma conexão segura com o servidor vCenter

Por padrão, o servidor de Backup do Azure se comunica com servidores vCenter via canal HTTPS. Para habilitar a comunicação segura, é recomendável que você instale o certificado de autoridade de certificação da VMware (CA) no servidor de Backup do Azure. Se você não exige uma comunicação segura e gostaria de desabilitar o requisito de HTTPS, consulte a seção [desabilitar protocolo de comunicação segura](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Para criar uma conexão segura entre o servidor de Backup do Azure e o servidor vCenter, importe o certificado confiável no servidor de Backup do Azure.

Normalmente você usa um navegador na máquina do servidor de Backup do Azure para se conectar ao servidor do vCenter via o vSphere cliente Web. Na primeira vez que você usar o localizador do servidor de Backup do Azure para se conectar ao servidor vCenter, a conexão não é segura. A imagem a seguir mostra a conexão não segura.

![exemplo de conexão segura ao servidor do VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Para corrigir esse problema e criar uma conexão segura, baixe os certificados CA de raiz confiável.

1. No navegador do servidor de Backup do Azure, digite a URL para o cliente Web vSphere.

  A página de logon do cliente Web vSphere aparece.

  ![cliente de web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

  Na parte inferior das informações para administradores e desenvolvedores, é o link para **Baixar certificados de autoridade de certificação confiável**.

  ![certificados confiáveis para baixar certificados de raíz confiável](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Se você não vir a página de logon do cliente Web vSphere, verifique as configurações de proxy do navegador.

2. Clique em **Baixar certificados de autoridade de certificação raiz confiável**.

  O servidor do vCenter baixa um arquivo em seu computador local. O nome do arquivo é **baixar**. Dependendo do seu navegador, você deve receber uma mensagem perguntando se deseja abrir ou salvar o arquivo.

  ![Baixar a mensagem quando certificados são baixados](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Salve o arquivo em um local no servidor de Backup do Azure. Quando você salvar o arquivo, adicione a extensão de nome de arquivo. zip.

  O arquivo é um arquivo. zip que contém as informações sobre os certificados. Adicionar a extensão. zip permite que você use ferramentas de extração.

4. Clique com o botão direito em **download.zip** e selecione Extrair tudo para extrair o conteúdo.

  O arquivo compactado extrai o conteúdo para uma pasta chamada **certificados**. Na pasta certificados, há dois tipos de arquivos. O arquivo do certificado raiz tem uma extensão como:.0,.1, ou. *number*. O arquivo CRL tem uma extensão que começa com .r0, .r1 e assim por diante. O arquivo da CRL é associado a um certificado.

  ![Baixe o arquivo extraído localmente ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Na pasta **certificados**, clique no arquivo de certificado raiz e clique em **Renomear**.

  ![renomeie o certificado raiz ](./media/backup-azure-backup-server-vmware/rename-cert.png)

  Altere a extensão do certificado raiz para .crt. Quando for perguntado se você tem certeza que deseja alterar a extensão - porque você pode alterar a função do arquivo, clique em Sim ou OK. Altere o ícone do arquivo para o ícone para um certificado raiz.

6. Clique com o botão direito no certificado raiz e no menu pop-up, selecione **Instalar certificado**.

  O Assistente para Importação de Certificados é aberto.

7. No Assistente de Importação de Certificados, selecione **Máquina Local** como o destino para o certificado e clique em **Avançar** para continuar.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

  Se você for questionado se deseja permitir alterações no computador, clique em Sim ou OK, para todas as alterações.

8. Na tela do repositório de certificados, selecione **Colocar todos os certificados no repositório a seguir** e clique em **Procurar** para escolher o armazenamento de certificados.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  A caixa de diálogo Selecionar repositório de certificados é aberta.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Selecione **autoridades de certificação confiáveis** como a pasta de destino para os certificados e clique em **OK**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

  O armazenamento de certificado escolhido é exibido na janela **Assistente de importação de certificado**. Clique em **Avançar**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Na tela Concluindo o Assistente para Importação de Certificados, verifique se o certificado está na pasta desejada e clique em **Concluir** para concluir o assistente.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  Uma caixa de diálogo é exibida, permitindo que você saiba se a importação foi bem-sucedida.

11. Faça logon no servidor vCenter para verificar se a conexão é segura.

  Se você tiver problemas para importar o certificado e não for possível estabelecer uma conexão segura, consulte a documentação do VMware vSphere em [Obtendo certificados de servidor](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Se você tiver limites de segurança em sua organização e não quiser habilitar o protocolo HTTPs, desabilite a comunicação segura através do Registro.

### <a name="disable-secure-communication-protocol"></a>Desabilite seu protocolo de comunicação segura

Se sua organização não exigir o protocolo de comunicação segura (HTTPS), use as etapas a seguir para desabilitar o HTTPS. Para desabilitar o comportamento padrão, crie uma chave do registro que ignora o comportamento padrão.

1. Copie e cole o texto a seguir em um aquivo .txt.

  ```
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```
2. Salve o arquivo com o nome **DisableSecureAuthentication.reg**, para o servidor de Backup do Azure.

3. Clique duas vezes no arquivo para ativar a entrada do registro.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Crie uma conta de usuário e a função no servidor vCenter

No servidor vCenter, uma função é um conjunto predefinido de privilégios. Um administrador de servidor no vCenter Server cria as funções e pares de contas de usuário com as funções para atribuir permissões. Para estabelecer as credenciais de usuário necessárias para fazer backup do servidor vCenter, crie uma função com privilégios específicos e associe a conta de usuário com a função.

O Servidor de Backup do Azure usa um nome de usuário e senha para autenticar com o servidor do vCenter. O Servidor de Backup do Azure utiliza essas credenciais como autenticação para todas as operações de backup.

Para adicionar uma função de servidor do vCenter e os privilégios de um administrador de backup:

1. Faça logon no servidor vCenter e no **Navegador** clique em **Administração**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. Na seção **Administração**, selecione **Funções** e, no painel **Funções**, clique no ícone Adicionar função (o símbolo +).

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  A caixa de diálogo **Criar Função** é aberta.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Na caixa de diálogo **Criar Função**, em **Nome da função**, digite *BackupAdminRole*. O nome da função pode ser o que você quiser, mas deve ser reconhecível para o objetivo da função.

4. Selecione os privilégios para a versão apropriada do vCenter e, em seguida, clique em **OK**. A tabela a seguir identifica os privilégios necessários para vCenter 6.0 e vCenter 5.5.

  Ao selecionar os privilégios, clique na divisa no rótulo principal para expandi-lo e exibir os privilégios de filho. Selecionar os privilégios necessários de máquina virtual, requer passar por vários 'níveis'. Você não precisa selecionar todos os privilégios do filho dentro de um privilégio pai.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Após clicar em **OK**, a nova função é exibida na lista no painel Funções.

|Privilégios do vCenter 6.0| Privilégios do vCenter 5.5|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-vcenter-server-user-account-and-permissions"></a>Criar conta de usuário do servidor vCenter e permissões

Após a função com privilégios, crie uma conta de usuário. A conta de usuário tem um nome e uma senha, que fornece as credenciais usadas para autenticação.

1. Para criar uma conta de usuário, no navegador do servidor vCenter, clique em **Usuários e Grupos**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  O painel Usuários e Grupos é exibido.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. No painel Usuários e Grupos do VMware, selecione a guia **Usuários**, clique no ícone Adicionar usuários (o símbolo +).

  A caixa de diálogo Novo Usuário é aberta.

3. Na caixa de diálogo Novo Usuário, preencha os campos e clique em **OK**. Neste exemplo, digite **BackupAdmin** para o nome de usuário. A senha deve ser forte.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  A nova conta de usuário aparece na lista.

4. Para associar a conta de usuário com a função, no navegador, clique em **Permissões Globais**. No painel Permissões Globais, clique na guia **Gerenciar** e, em seguida, clique no ícone Adicionar (o símbolo +).

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  A caixa de diálogo Permissões Globais Raiz - Adicionar Permissão é aberta.

5. Na caixa de diálogo **Permissão Global Raiz- adicionar permissão**, clique em **Adicionar** para escolher o usuário ou grupo.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  A caixa de diálogo Selecionar Usuários/Grupos é aberta.

6. Na caixa de diálogo **Selecione usuários/grupos**, escolha **BackupAdmin** e clique em **Adicionar**.

  A conta de usuário no campo Usuários tem o formato *domínio*`\`*nome de usuário*. Se você quiser usar um domínio diferente, escolha-o na lista de domínios.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  Clique em **OK** para adicionar os usuários selecionados à caixa de diálogo Adicionar Permissão.

7. Agora que você identificou o usuário, atribua o usuário à função. Na área de função atribuída, no menu suspenso, selecione a função **BackupAdminRole** e clique em **OK**.

  ![caixa de diálogo com erro de certificado ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Na guia Gerenciar das Permissões Globais, a nova conta de usuário e a função associada aparecem na lista.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Estabelecer as credenciais de servidor do vCenter no servidor de Backup do Azure

Antes de adicionar o servidor VMware para o Servidor de Backup do Azure, certifique-se de que você instalou a [Atualização 1 para o servidor de Backup do Microsoft Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Para abrir o servidor de Backup do Azure, clique duas vezes no ícone da área de trabalho do servidor de Backup do Azure.

  ![Ícone do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  Se você não conseguir localizar o ícone na área de trabalho, poderá abrir o Servidor de Backup do Azure na lista de aplicativos instalados. O nome do aplicativo de servidor de Backup do Azure é um Backup do Microsoft Azure.

2. No console do Servidor de Backup do Azure, clique em **Gerenciamento**, em seguida, clique em **Servidores de Produção** e na faixa de opções da ferramenta clique em **VMware gerenciar**.

  ![Console MABS](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  A caixa de diálogo Gerenciar Credenciais é aberta.

  ![caixa de diálogo de credenciais para gerenciar MABS](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Na caixa de diálogo Gerenciar Credenciais, clique em **Adicionar** para abrir a caixa de diálogo Adicionar Credenciais.

4. Na caixa de diálogo Adicionar Credenciais, digite um nome e uma descrição para a nova credencial. O nome da credencial, *Contoso Vcenter* no exemplo, é como você pode identificar a credencial no procedimento a seguir. Use o mesmo nome de usuário e senha que foi usada no servidor vCenter. Se o servidor vCenter e o servidor de Backup do Azure não estão no mesmo domínio, especifique o domínio no nome do usuário.

  ![caixa de diálogo de credenciais para gerenciar MABS](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

  Clique em **Adicionar** para adicionar a nova credencial para o Servidor de Backup do Azure. A nova credencial aparece na lista na caixa de diálogo Gerenciar Credenciais.
  ![caixa de diálogo de credenciais para gerenciar MABS](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Para fechar a caixa de diálogo Gerenciar credenciais, clique o **X** no canto superior direito.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Adicionar servidor VMware para o Servidor de Backup do Azure

Para abrir o assistente de Adição de Servidor de Produção:

1. No console do Servidor de Backup do Azure, clique em **Gerenciamento**, clique em **Servidor de Produção** e, em seguida, clique em **Adicionar**.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  O Assistente para Adição de Servidor de Produção é aberto.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Na tela Selecionar tipo do Servidor de Produção, selecione servidores VMware e clique em **Avançar**.

3. No endereço IP/nome do servidor, especifique o nome de domínio totalmente qualificado (FQDN) ou endereço IP do servidor VMware. Você pode inserir o nome de VMware se todos os servidores ESXi são gerenciados pelo mesmo Vcenter.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Na caixa de diálogo **Porta SSL**, insira a porta usada para se comunicar com o servidor VMware. Use a porta 443, que é a porta padrão, a menos que você saiba que uma porta diferente é necessária.

5. Na caixa de diálogo **Especificar credenciais**, selecione a credencial que você criou.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Clique em **Adicionar** para adicionar o servidor VMware à lista de **Servidores VMware Adicionados**e clique em **Avançar** para ir para a próxima tela do assistente.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na tela **Tarefas**, clique em **Adicionar** para adicionar o servidor VMware especificado para o Servidor de Backup do Azure.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Como o backup do servidor VMware é um backup sem agente, a adição do novo servidor acontece em segundos. A tela Concluir mostra os resultados.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Você pode adicionar vários servidores VMware para o Servidor de Backup do Azure, repetindo as etapas anteriores nesta seção.

Agora que você adicionou um servidor VMware para o Servidor de Backup do Azure, a próxima etapa é criar um grupo de proteção. O grupo de proteção especifica os diversos detalhes para retenção de curto e longo prazo e é onde você pode definir e aplica a política de backup. A política de backup é o agendamento para quando os backups são realizados e do que é feito backup.


## <a name="configure-a-protection-group"></a>Configurar grupo de proteção

Se você não tiver usado o System Center Data Protection Manager ou Servidor de Backup do Azure antes, confira o tópico [Planejar backups em disco](https://technet.microsoft.com/library/hh758026.aspx), para preparar o ambiente de hardware. Depois que você tiver verificado que tem um armazenamento adequado, use o Assistente de Novo Grupo de Proteção para adicionar as VMs específicas.

1. No console do Servidor de Backup do Azure, clique em **Proteção**e, na faixa de opções da ferramenta, clique em **Novo** para abrir o assistente Criar Novo Grupo de Proteção.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  O Assistente de Novo Grupo de Proteção é aberto.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  Clique em **Avançar** para ir para a tela **Selecionar tipo do grupo de proteção**.

2. Na tela Selecionar Tipo do Grupo de Proteção, escolha **Servidores** e clique em **Próximo**.

3. Na tela Selecionar Membros do Grupo, você pode ver os membros disponíveis e os membros que foram selecionados. Selecione os membros que você deseja proteger e clique em **Avançar**.

  ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

  Ao selecionar um membro, se você selecionar uma pasta que contenha outras pastas ou VMs, essas pastas e VMs também serão selecionadas. A inclusão das pastas e VMs na pasta pai é chamada de proteção de nível de pasta. Você pode excluir qualquer pasta ou a VM desmarcando a caixa de seleção.

  Se uma VM ou uma pasta que contenha uma VM já estiver protegida no Azure, você não poderá selecionar essa VM novamente. Ou seja, depois que uma máquina virtual é protegida no Azure, ela não pode ser protegida novamente, o que impede que pontos de recuperação duplicados sejam criados para uma máquina virtual. Se você quiser ver qual Servidor de Backup do Azure já protege um membro, passe o mouse sobre o membro para ver o nome do servidor de proteção.

4. Na tela Selecionar método de proteção de dados, digite um nome para o grupo de proteção. Proteção de curto prazo (no disco) e proteção online são selecionados. Se você quiser usar a proteção online (Azure), você deve usar a proteção de curto prazo em disco. Clique em **Avançar** para prosseguir para o intervalo de proteção de curto prazo.

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

  Quando estiver satisfeito com as metas de curto prazo, clique em **Avançar**.

7. Na tela Escolher Método de Criação de Réplica, especifique como deseja gerar a cópia inicial ou a réplica dos dados protegidos no Servidor de Backup do Azure.

  O padrão é **Automaticamente pela rede** e **Agora**. Se você usar o padrão, é recomendável especificar um horário de pico. Escolha **Mair tarde** e especifique um dia e hora.

  Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando mídia removível.

  Depois de fazer suas escolhas, clique em **Avançar**.

  ![Assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Na tela **Opções de Verificação de Consistência**, selecione como e quando automatizar as verificações de consistência. Você pode executar verificações de consistência quando dados de réplica se tornam inconsistentes ou de acordo com uma programação definida.

  Se você não desejar configurar a verificação de consistência automática, você pode executar uma verificação manual. Na área de proteção do console do servidor de Backup do Azure, clique no grupo de proteção e selecione **Executar verificação de consistência**.

  Clique em **Avançar** para se mover para a próxima tela.

9. Na tela **Especificar Dados da Proteção Online**, selecione as fontes de dados que você deseja proteger. Você pode selecionar os membros individualmente ou clicar em **Selecionar tudo** para escolher todos os membros. Depois de escolher os membros, clique em **Avançar**.

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

