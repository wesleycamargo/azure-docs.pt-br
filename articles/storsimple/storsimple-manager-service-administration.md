<properties 
   pageTitle="Administração do serviço StorSimple Manager | Microsoft Azure"
   description="Saiba como gerenciar seu dispositivo StorSimple usando o serviço StorSimple Manager no Portal de Gerenciamento do Azure."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/11/2015"
   ms.author="alkohli" />

# Usar o serviço StorSimple Manager para administrar o seu dispositivo StorSimple

## Visão geral

Este artigo descreve a interface do serviço StorSimple Manager, incluindo como se conectar a ele, as várias opções disponíveis e links para os fluxos de trabalho específicos que podem ser executados nessa interface do usuário. Essa orientação se aplica tanto ao dispositivo StorSimple físico quanto ao virtual.

Neste artigo, você aprenderá:

- Conectar-se ao serviço StorSimple Manager
- Navegar para a interface do usuário do serviço StorSimple Manager
- Administrar o dispositivo StorSimple Manager por meio do serviço StorSimple


## Conectar-se ao serviço StorSimple Manager

O serviço StorSimple Manager é executado no Microsoft Azure e se conecta a vários dispositivos StorSimple. Você pode usar o Portal de Gerenciamento do Microsoft Azure central em execução em um navegador para gerenciar esses dispositivos. Para se conectar ao serviço StorSimple Manager, faça o seguinte:

#### Para conectar-se ao serviço

1. Navegue até [http://azure.microsoft.com/](http://azure.microsoft.com/)

1. Usando suas credenciais de conta da Microsoft, faça logon no Portal de Gerenciamento do Microsoft Azure (no lado superior direito do painel).

1. Role para baixo o painel de navegação esquerdo para acessar o serviço StorSimple Manager.


## Navegar para a interface do usuário do StorSimple Manager

A hierarquia de navegação para a interface do usuário do StorSimple Manager é mostrada na tabela a seguir.

- A página de aterrissagem do **StorSimple Manager** leva para as páginas de nível de serviço da interface do usuário aplicáveis a todos os dispositivos de um serviço.

- A página **Dispositivos** leva para as páginas de interface do usuário de nível do dispositivo aplicáveis a um dispositivo específico.

- A página **Contêineres de volume** leva você à página volume que mostra todos os volumes associados a um dispositivo.


#### Hierarquia de navegação do serviço StorSimple Manager

|Página de aterrisagem|Páginas de nível de serviço|Páginas de nível de dispositivo|Páginas de nível de dispositivo|
|---|---|---|---|
|Serviço StorSimple Manager|Painel de serviço|Painel do dispositivo||
||Dispositivos →|Monitor|| 
||Catálogo de backup|Contêineres de volume|Volumes| 
||Configurar (Serviço)|Políticas de backup|| 
||Trabalhos|Configurar (Dispositivo)|| 
||Alertas|Manutenção||


## Administrar o dispositivo StorSimple por meio do serviço StorSimple Manager

A tabela a seguir mostra um resumo de todas as tarefas comuns de gerenciamento e fluxos de trabalho complexos que podem ser executados na interface do usuário do serviço StorSimple Manager. Essas tarefas são organizadas com base nas páginas de interface do usuário nas quais elas foram iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

#### Fluxos de trabalho do StorSimple Manager

|Se você quiser fazer isso...|Vá para a página da interface do usuário...|Utilize este procedimento.|
|---|---|---|
|Criar um serviço </br>Excluir um serviço</br>Obter chave de registro do serviço</br>Regenerar chave de registro do serviço|Serviço StorSimple Manager|[Implantar um serviço StorSimple Manager](storsimple-manage-service.md)
|Alterar a chave de criptografia de dados do serviço</br>Exibir os logs operacionais|Serviço StorSimple Manager → Painel|[Use o painel de serviço do StorSimple Manager](storsimple-service-dashboard.md)|
|Desativar um dispositivo</br>Excluir um dispositivo|Serviço StorSimple Manager → Dispositivos|[Desativar ou excluir um dispositivo](storsimple-deactivate-and-delete-device.md)|
|Saiba mais sobre o failover de dispositivo e a recuperação de desastres</br>Failover para um dispositivo físico</br>Failover para um dispositivo virtual</br>Recuperação de desastres de continuidade de negócios (BCDR)|Serviço StorSimple Manager → Dispositivos|[Failover e recuperação de desastres para o seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Lista de backups para um volume</br>Selecionar um conjunto de backup</br>Excluir um conjunto de backup|Serviço StorSimple Manager → Catálogo de backup|[Gerenciar backups](storsimple-manage-backup-catalog.md)|
|Clonar um volume|Serviço StorSimple Manager → Catálogo de backup|[Clonar um volume](storsimple-clone-volume.md)|
|Restaurar um conjunto de backup|Serviço StorSimple Manager → Catálogo de backup|[Restaurar um conjunto de backup](storsimple-restore-from-backup-set.md)|
|Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Excluir uma conta de armazenamento</br>Rotação chave de contas de armazenamento|Serviço StorSimple Manager → Configurar|[Gerenciar contas de armazenamento](storsimple-manage-storage-accounts.md)|
|Sobre modelos de largura de banda</br>Adicionar um modelo de largura de banda</br>Editar um modelo de largura de banda</br>Excluir um modelo de largura de banda</br>Usar um modelo de largura de banda padrão</br>Criar um modelo de largura de banda de dia inteiro que comece em um horário especificado|Serviço StorSimple Manager → Configurar|[Gerenciar modelos de largura de banda](storsimple-manage-bandwidth-templates.md)|
|Sobre registros de controle de acesso</br>Criar um registro de controle de acesso</br>Editar um registro de controle de acesso</br>Excluir um registro de controle de acesso|Serviço StorSimple Manager → Configurar|[Gerenciar registros de controle de acesso](storsimple-manage-acrs.md)|
|Exibir detalhes do trabalho</br>Cancelar um trabalho|Serviço StorSimple Manager → Trabalhos|[Gerenciar trabalhos](storsimple-manage-jobs.md)
|Receber notificações de alerta</br>Gerenciar alertas</br>Revisar alertas|Serviço StorSimple Manager → Alertas|[Exibir e gerenciar alertas do StorSimple](storsimple-manage-alerts.md)
|Exibir iniciadores conectados</br>Localizar o número de série do dispositivo</br>Localizar o IQN de destino|Serviço StorSimple Manager → Dispositivos → Painel|[Usar o painel do dispositivo StorSimple Manager](storsimple-device-dashboard.md)|
|Criar gráficos de monitoramento|Serviço StorSimple Manager → Dispositivos → Monitor|[Monitorar o dispositivo StorSimple](storsimple-monitor-device.md)|
|Adicionar um contêiner de volume</br>Modificar um contêiner de volume</br>Excluir um contêiner de volume|Serviço StorSimple Manager → Dispositivos → Contêineres de volume|[Gerenciar contêineres de volume](storsimple-manage-volume-containers.md)|
|Adicionar um volume</br>Modificar um volume</br>Colocar um volume offline</br>Excluir um volume</br>Monitorar um volume|Serviço StorSimple Manager → Dispositivos → Contêineres de volume → Volumes|[Gerenciar volumes](storsimple-manage-volumes.md)|
|Modificar as configurações do dispositivo</br>Modificar as configurações de tempo</br>Modificar as configurações de DNS.md</br>Configurar interfaces de rede|Serviço StorSimple Manager → Dispositivos → Configurar|[Modificar a configuração do dispositivo para seu dispositivo StorSimple](storsimple-modify-device-config.md)|
|Exibir configurações de proxy Web|Serviço StorSimple Manager → Dispositivos → Configurar|[Configurar o proxy Web para o seu dispositivo](storsimple-configure-web-proxy.md)|
|Modificar senha de administrador do dispositivo</br>Modificar senha do StorSimple Snapshot Manager|Serviço StorSimple Manager → Dispositivos → Configurar|[Alterar senhas de StorSimple](storsimple-change-passwords.md)|
|Configurar o gerenciamento remoto|Serviço StorSimple Manager → Dispositivos → Configurar|[Conectar remotamente ao seu dispositivo StorSimple](storsimple-remote-connect.md)|
|Configurar definições de alerta|Serviço StorSimple Manager → Dispositivos → Configurar|[Exibir e gerenciar alertas do StorSimple](storsimple-manage-alerts.md)|
|Configure o CHAP para o seu dispositivo StorSimple|Serviço StorSimple Manager → Dispositivos → Configurar|[Configure o CHAP para o seu dispositivo StorSimple](storsimple-configure-chap.md)|
|Adicionar uma política de backup</br>Adicionar ou modificar uma agenda</br>Excluir uma política de backup</br>Fazer um backup manual</br>Criar uma política de backup personalizada com vários volumes e agendamentos|Serviço StorSimple Manager → Dispositivos → Políticas de backup|[Gerenciar políticas de backup](storsimple-manage-backup-policies.md)|
|Parar controladores do dispositivo</br>Reiniciar controladores do dispositivo</br>Desligar controladores de dispositivo</br>Redefinir o dispositivo para os padrões de fábrica</br>(As opções acima são somente para o dispositivo local)|Serviço StorSimple Manager → Dispositivos → Manutenção|[Gerenciar controlador de dispositivo StorSimple](storsimple-manage-device-controller.md)|
|Saiba mais sobre os componentes de hardware StorSimple</br>Monitorar o status do hardware</br>(As opções acima são somente para o dispositivo local)|Serviço StorSimple Manager → Dispositivos → Manutenção|[Componentes de hardware do monitor](storsimple-monitor-hardware-status.md)|
|Criar um pacote de suporte|Serviço StorSimple Manager → Dispositivos → Manutenção|[Criar e gerenciar pacotes de suporte](storsimple-create-manage-support-package.md)|
|Instalar as atualizações do software|Serviço StorSimple Manager → Dispositivos → Manutenção|[Atualizar seu dispositivo](storsimple-update-device.md)|

![Vídeo disponível](./media/storsimple-manager-service-administration/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo com orientações sobre a interface de usuário do serviço StorSimple Manager, clique [aqui](http://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

##Próximas etapas
Se você tiver problemas com a operação diária do seu dispositivo StorSimple ou com qualquer um de seus componentes de hardware, consulte:

- [Solucionar problemas de um dispositivo operacional](storsimple-troubleshoot-operational-device.md)
- [Usar LEDs indicadores de monitoramento do StorSimple](storsimple-monitoring-indicators.md)

Se você não conseguir resolver os problemas e precisar criar uma solicitação de serviço, consulte:

-  [Contatar Suporte da Microsoft](storsimple-contact-microsoft-support.md)

<!---HONumber=Oct15_HO3-->