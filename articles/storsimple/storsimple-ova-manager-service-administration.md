<properties 
   pageTitle="Administração da Matriz Virtual do StorSimple Manager | Microsoft Azure"
   description="Saiba como gerenciar sua Matriz Virtual do StorSimple local usando o serviço StorSimple Manager no portal clássico do Azure."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/17/2016"
   ms.author="alkohli" />

# Usar o serviço StorSimple Manager para administrar o StorSimple Virtual Array

![fluxo do processo de instalação](./media/storsimple-ova-manager-service-administration/manage4.png)

## Visão geral

Este artigo descreve a interface do serviço StorSimple Manager, incluindo como conectá-la e as várias opções disponíveis e fornece links para os fluxos de trabalho específicos que podem ser executados nessa interface do usuário.

Após ler este artigo, você saberá como:

- Conectar-se ao serviço StorSimple Manager
- Navegar para a interface do usuário do serviço StorSimple Manager
- Administrar sua matriz Virtual StorSimple por meio do serviço StorSimple Manager

> [AZURE.NOTE] Para exibir as opções de gerenciamento disponíveis para o dispositivo da série 8000 StorSimple, vá para [Usar o serviço StorSimple Manager para administrar o dispositivo](storsimple-manager-service-administration.md).

## Conectar-se ao serviço StorSimple Manager

O serviço StorSimple Manager é executado no Microsoft Azure e conecta-se a várias matrizes Virtual StorSimple. Você pode usar o Portal Clássico do Microsoft Azure central em execução em um navegador para gerenciar esses dispositivos. Para se conectar ao serviço StorSimple Manager, faça o seguinte:

#### Para conectar-se ao serviço

1. Acesse [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. Usando suas credenciais de conta da Microsoft, faça logon no Portal Clássico do Microsoft Azure (no lado superior direito do painel).

3. Role para baixo o painel de navegação esquerdo para acessar o serviço StorSimple Manager.

    ![role até o serviço](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## Navegue para a interface do usuário do serviço StorSimple Manager

A hierarquia de navegação para a interface do usuário do StorSimple Manager é mostrada na tabela a seguir.

- A página de aterrissagem do **StorSimple Manager** leva para as páginas de nível de serviço da interface do usuário aplicáveis a todas as Matrizes Virtuais dentro de um serviço.

- A página **Dispositivos** leva você às páginas de interface do usuário de nível do dispositivo aplicáveis a uma Matriz Virtual específica.

#### Hierarquia de navegação do serviço StorSimple Manager

|Página de aterrisagem|Páginas de nível de serviço|Páginas de nível de dispositivo|
|---|---|---|
|Serviço StorSimple Manager|Painel (serviço)|Painel (dispositivo)|
||Dispositivos →|Monitoramento|
||Catálogo de backup|Compartilhamentos (servidor de arquivos) ou </br>Volumes (servidor iSCSI)|
||Configurar (serviço)|Configurar (dispositivo)|
||Trabalhos|Manutenção|
||Alertas|

## Usar o serviço StorSimple Manager para executar tarefas de gerenciamento

A tabela a seguir mostra um resumo de todas as tarefas comuns de gerenciamento e fluxos de trabalho complexos que podem ser executados na interface do usuário do serviço StorSimple Manager. Essas tarefas são organizadas com base nas páginas de interface do usuário nas quais elas foram iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

#### Fluxos de trabalho do StorSimple Manager

|Se você quiser fazer isso...|Vá para a página da interface do usuário...|Utilize este procedimento|
|---|---|---|
|Criar um serviço</br>Excluir um serviço</br>Obter a chave de registro</br>Regenerar a chave de registro do serviço|Serviço StorSimple Manager|[Implantar o serviço StorSimple Manager](storsimple-ova-manage-service.md)|
|Alterar a chave de criptografia de dados do serviço</br>Exibir os logs operacionais|Serviço StorSimple Manager → Painel|[Usar o painel de serviço do StorSimple](storsimple-ova-service-dashboard.md)|
|Desativar uma Matriz Virtual</br>Excluir uma Matriz Virtual|Serviço StorSimple Manager → Dispositivos|[Desativar ou excluir uma matriz Virtual](storsimple-ova-deactivate-and-delete-device.md)|
|Failover de dispositivo e recuperação de desastre</br>Pré-requisitos de Failover</br>Failover para um dispositivo virtual</br>Recuperação de desastres de continuidade de negócios (BCDR)</br>Erros durante a recuperação de desastre|Serviço StorSimple Manager → Dispositivos|[Failover de dispositivo e a recuperação de desastre para sua StorSimple Virtual Array](storsimple-ova-failover-dr.md)|
|Fazer backup de compartilhamentos e volumes</br>Fazer um backup manual</br>Alterar o agendamento de backup</br>Exibir backups existentes|Serviço StorSimple Manager → Catálogo de backup|[Fazer backup de sua Matriz Virtual StorSimple](storsimple-ova-backup.md)|
|Restaurar compartilhamentos de um conjunto de backup</br>Restaurar volumes de um conjunto de backup</br>Recuperação em nível de item (somente servidor de arquivos)|Serviço StorSimple Manager → Catálogo de backup|[Restaurar de um backup de sua Matriz Virtual StorSimple](storsimple-ova-restore.md)|
|Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Excluir uma conta de armazenamento|Serviço StorSimple Manager → Configurar|[Gerenciar contas de armazenamento para a Matriz Virtual StorSimple](storsimple-ova-manage-storage-accounts.md)|
|Sobre os registros de controle de acesso</br>Adicionar ou modificar um registro de controle de acesso </br>Excluir um registro de controle de acesso|Serviço StorSimple Manager → Configurar|[Gerenciar registros de controle de acesso para a Matriz Virtual StorSimple](storsimple-ova-manage-acrs.md)|
|Exibir detalhes do trabalho|Serviço StorSimple Manager → Trabalhos| [Gerenciar trabalhos do StorSimple Virtual Array](storsimple-ova-manage-jobs.md)|
|Definir configurações de alerta</br>Receber notificações de alerta</br>Gerenciar alertas</br>Revisar alertas|Serviço StorSimple Manager → Alertas|[Exibir e gerenciar alertas para a Matriz Virtual StorSimple](storsimple-ova-manage-alerts.md)|
|Modificar a senha de administrador do dispositivo|Serviço StorSimple Manager → Dispositivos → Configurar|[Alterar a senha do administrador do dispositivo da Matriz Virtual StorSimple](storsimple-ova-change-device-admin-password.md)|
|Instalar as atualizações do software|Serviço StorSimple Manager → Dispositivos → Manutenção|[Atualizar a Matriz Virtual](storsimple-ova-update.md)|

>[AZURE.NOTE] Você deve usar a [Interface do usuário da web local](storsimple-ova-web-ui-admin.md) para as seguintes tarefas:
>
>- [Recuperar a chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Criar um pacote de suporte](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Parar e reiniciar uma Matriz Virtual](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##Próximas etapas
Para obter informações sobre a interface do usuário da web e como usá-la, acesse [Usar a interface do usuário da web do StorSimple para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->