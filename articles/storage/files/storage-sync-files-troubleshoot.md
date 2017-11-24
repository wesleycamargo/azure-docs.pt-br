---
title: "Solucionar problemas da Sincronização de Arquivo do Azure (versão prévia) | Microsoft Docs"
description: "Solucionar problemas comuns com a Sincronização de arquivos do Azure."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 265c5f660c4bee53a2faf4a073384587eb3f65fc
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Solucionar problemas da Sincronização de Arquivos do Azure (versão prévia)
Use a Sincronização de arquivos do Azure (versão prévia) para centralizar os compartilhamentos de arquivos de sua organização em Arquivos do Azure, sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo foi projetado para ajudá-lo a solucionar problemas e resolver problemas encontrados com a implantação da Sincronização de arquivos do Azure. Nós também descrevemos como coletar logs importantes do sistema para ajudar em uma investigação mais profunda dos problemas. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. A seção de comentários deste artigo.
2. O [Fórum do Armazenamento do Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata).
3. O [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. O Suporte da Microsoft. Para criar uma nova solicitação de suporte, no Portal do Azure, na guia **Ajuda**, selecione o botão **Ajuda + suporte** e, em seguida, selecione **Nova solicitação de suporte**.

## <a name="agent-installation-and-server-registration"></a>Instalação do agente e registro do servidor
<a id="agent-installation-failures"></a>**Como solucionar problemas de falhas de instalação do agente**  
Se a instalação do agente de Sincronização de arquivos do Azure estiver falhando, execute o seguinte comando em um prompt de comandos com privilégios elevados para habilitar o registro em log durante a instalação do agente:

```
StorageSyncAgent.msi /l*v Installer.log
```

Examine o installer.log para determinar a causa da falha de instalação. 

> [!Note]  
> A instalação do agente falhará se sua máquina usar o Microsoft Update e o serviço Windows Update não estiver em execução.

<a id="server-registration-missing"></a>**O servidor não está listado em Servidores Registrados no Portal do Azure**  
Se um servidor não estiver listado em **Servidores Registrados** para um Serviço de Sincronização de Armazenamento, execute as seguintes etapas:
1. Faça logon no servidor que você deseja registrar.
2. Abra o Explorador de arquivos e, em seguida, vá para o diretório de instalação do agente de sincronização de armazenamento (o local padrão é C:\Program Files\Azure\StorageSyncAgent). 
3. Execute ServerRegistration.exe e siga o assistente para registrar o servidor com um Serviço de Sincronização de Armazenamento.

<a id="server-already-registered"></a>**O Registro do Servidor exibe a seguinte mensagem depois de instalar o agente de Sincronização de arquivos do Azure: "Este servidor já está registrado"** 

![Uma captura de tela da caixa de diálogo de Registro do Servidor com a mensagem de erro “o servidor já está registrado”](media/storage-sync-files-troubleshoot/server-registration-1.png)

Esta mensagem é exibida se o servidor foi registrado anteriormente com um Serviço de Sincronização de Armazenamento. Para cancelar o registro do servidor com o Serviço de Sincronização de Armazenamento atual e registrar com o novo Serviço de Sincronização de Armazenamento, siga as etapas para [Cancelar o registro de um servidor com uma Sincronização de arquivos do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se o servidor não estiver listado nos **Servidores Registrados** no Serviço de Sincronização de Armazenamento, execute os seguintes comandos do PowerShell no servidor onde você deseja cancelar o registro:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se o servidor for parte de um cluster, você pode usar opcional *StorageSyncServer Reset - CleanClusterRegistration* parâmetro também para remover o registro de cluster.

<a id="web-site-not-trusted"></a>**Ao registrar um servidor, obtenho várias respostas de "sites não confiáveis". Por quê?**  
Esse erro ocorre porque a política **Segurança reforçada do Internet Explorer** está habilitada durante o registro do servidor. Para saber mais sobre como desabilitar corretamente a política **Segurança reforçada do Internet Explorer**, confira [Preparar servidores do Windows para uso com a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e [Como implantar a Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gerenciamento de grupos de sincronização
<a id="cloud-endpoint-using-share"></a>**Falha na criação de ponto de extremidade da nuvem com o seguinte erro: "O compartilhamento de arquivos do Azure especificado já está em uso por um ponto de extremidade da nuvem diferente"**  
Esse erro ocorre se o compartilhamento de arquivos do Azure já está em uso por outro ponto de extremidade da nuvem. 

Se você estiver recebendo esse erro e o compartilhamento de arquivos do Azure não está atualmente em uso por um ponto de extremidade da nuvem, execute as etapas a seguir para limpar os metadados de Sincronização de arquivos do Azure no compartilhamento de arquivos do Azure:

> [!Warning]  
> Excluir os metadados em um compartilhamento de arquivos do Azure que está atualmente em uso por um ponto de extremidade da nuvem fará com que as operações de Sincronização de arquivos do Azure falhem. 

1. Navegue até o seu compartilhamento de arquivos do Azure no Portal do Azure.  
2. Clique com botão direito no compartilhamento de arquivos do Azure e selecione **Editar metadados**.
3. Clique com botão direito em **SyncService** e selecione **Excluir**.

<a id="cloud-endpoint-authfailed"></a>**Falha na criação de ponto de extremidade de nuvem com erro: AuthorizationFailed**  
Esse problema ocorrerá se a conta de usuário não tiver direitos suficientes para criar um Ponto de extremidade de nuvem. 

Para criar um Ponto de extremidade de nuvem, sua conta de usuário deve ter as seguintes permissões de Autorização da Microsoft:  
* Leitura: Obter a definição da função
* Gravação: Criar ou atualizar definição de função personalizada
* Leitura: Obter a atribuição da função
* Gravação: Criar a atribuição da função

As seguintes funções internas têm as permissões de Autorização da Microsoft adequadas:  
* Proprietário
* Administrador de Acesso do Usuário

Para determinar se sua função de conta de usuário tem as permissões necessárias:  
1. No portal do Azure, clique em **Grupos de recursos**.
2. Selecione o grupo de recursos em que a conta de armazenamento está localizada e clique em **Controle de acesso (IAM)**.
3. Selecione o **função** (por exemplo, o proprietário ou colaborador) para sua conta de usuário.
4. Na lista **Provedor de Recursos**, selecione **Autorização da Microsoft**. 
    * **Atribuição de função** deve ter **Permissões de Leitura** e de **Gravação**.
    * **Definição de função** deve ter **Permissões de Leitura** e de **Gravação**.

<a id="cloud-endpoint-deleteinternalerror"></a>**Falha na exclusão de ponto de extremidade com erro: MgmtInternalError**  
Esse problema pode ocorrer se a conta de armazenamento ou compartilhamento de Arquivo do Azure foi excluída antes de excluir o Ponto de extremidade de nuvem. Esse problema será corrigido em uma atualização futura. Nesse momento, você poderá excluir um ponto de extremidade de nuvem depois que você excluir a conta de armazenamento ou compartilhamento de arquivos do Azure.

Para evitar que esse problema ocorra, exclua o Ponto de extremidade de nuvem antes de excluir a conta de armazenamento ou compartilhamento de Arquivo do Azure.

## <a name="sync"></a>Sincronizar
<a id="afs-change-detection"></a>**Se eu criei um arquivo diretamente em meu compartilhamento de arquivos do Azure no SMB ou por meio do portal, quanto tempo leva para o arquivo a ser sincronizado servidores no grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**A sincronização falha em um servidor**  
Se a sincronização falhar em um servidor:
1. Verifique se existe um ponto de extremidade do servidor no Portal do Azure para o diretório que você deseja sincronizar para um Compartilhamento de Arquivo do Azure:
    
    ![Uma captura de tela de um Grupo de Sincronização com um ponto de extremidade de nuvem e do servidor no Portal do Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. No Visualizador de Eventos, examine os logs de eventos operacionais e de diagnóstico, localizados em aplicativos e Services\Microsoft\FileSync\Agent.
    1. Confirme se o servidor tem conectividade com a Internet.
    2. Verifique se o serviço de Sincronização de arquivos do Azure está em execução no servidor. Abra o snap-in do MMC de Serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.

<a id="replica-not-ready"></a>**Falha na sincronização com erro: 0x80c8300f – a réplica não está pronta para executar a operação necessária**  
Esse erro será esperado se você criar um Ponto de extremidade de nuvem e usar um compartilhamento de Arquivo do Azure que contém dados. Quando o trabalho de detecção de alteração for concluída em execução no compartilhamento de arquivos do Azure (pode levar até 24 horas), a sincronização deve começar a funcionar corretamente.

<a id="broken-sync-files"></a>**Como solucionar problemas de falha de sincronização de arquivos individuais**  
Se os arquivos individuais não sincronizar:
1. No Visualizador de Eventos, examine os logs de eventos operacionais e de diagnóstico, localizados em aplicativos e Services\Microsoft\FileSync\Agent.
2. Verifique se não há nenhum identificador aberto no arquivo.

    > [!NOTE]
    > A Sincronização de arquivos do Azure periodicamente gera instantâneos do VSS para arquivos para sincronizar arquivos com identificadores abertos.

## <a name="cloud-tiering"></a>Disposição em camadas de nuvem 
<a id="files-fail-tiering"></a>**Como solucionar problemas de arquivos que falham ao serem dispostos em camadas**  
Se os arquivos não camada para arquivos do Azure:

1. Verifique se existem arquivos no Compartilhamento de Arquivo do Azure.

    > [!NOTE]
    > Um arquivo deve ser sincronizado com um Compartilhamento de Arquivo do Azure antes de poder ser disposto em camadas.
2. No Visualizador de Eventos, examine os logs de eventos operacionais e de diagnóstico, localizados em aplicativos e Services\Microsoft\FileSync\Agent.
    1. Confirme se o servidor tem conectividade com a Internet. 
    2. Verifique se os drivers de filtro de Sincronização de arquivos do Azure (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
        - Em um prompt de comandos com privilégios elevados, digite `fltmc`. Verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados.

<a id="files-fail-recall"></a>**Como solucionar problemas de arquivos que falham quando seu recall é realizado**  
Se os arquivos não ser recuperados:
1. No Visualizador de Eventos, examine os logs de eventos operacionais e de diagnóstico, localizados em aplicativos e Services\Microsoft\FileSync\Agent.
    1. Verifique se existem arquivos no Compartilhamento de Arquivo do Azure.
    2. Confirme se o servidor tem conectividade com a Internet. 
    3. Verifique se os drivers de filtro de Sincronização de arquivos do Azure (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
        - Em um prompt de comandos com privilégios elevados, digite `fltmc`. Verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados.

<a id="files-unexpectedly-recalled"></a>**Como solucionar problemas da realização de recall inesperada de arquivos em um servidor**  
Antivírus, backup e outros aplicativos que leem grandes quantidades de arquivos causarão recalls indesejados a menos que eles respeitem o atributo offline e ignorem a leitura do conteúdo desses arquivos. Ignorar arquivos off-line de produtos que dão suporte a essa opção ajuda a evitar recuperações não intencionais durante operações, como verificações antivírus ou trabalhos de backup.

Consulte seu fornecedor de software sobre como configurar sua solução para ignorar a leitura de arquivos offline.

Recuperações não intencionais também podem ocorrer em outros cenários, como quando você estiver pesquisando arquivos no Explorador de arquivos. Abrir uma pasta com arquivos de nuvem em camadas no Explorador de arquivos no servidor pode resultar em recuperações não intencionais. Isso é ainda mais provável se uma solução antivírus estiver habilitada no servidor.

## <a name="general-troubleshooting"></a>Solução de problemas gerais
Se você encontrar problemas com a Sincronização de arquivos do Azure em um servidor, comece executando o seguinte:
1. No Visualizador de Eventos, examine os logs de eventos operacionais e de diagnóstico.
    - Os problemas de sincronização, disposição em camadas e recall são registrados nos logs de eventos operacionais e Services\Microsoft\FileSync\Agent.
    - Problemas relacionados ao gerenciamento de um servidor (por exemplo, definições de configuração) são registrados nos logs de eventos operacionais e de diagnóstico em aplicativos e Services\Microsoft\FileSync\Management.
2. Verifique se o serviço de Sincronização de arquivos do Azure está em execução no servidor:
    - Abra o snap-in do MMC de Serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.
3. Verifique se os drivers de filtro de Sincronização de arquivos do Azure (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
    - Em um prompt de comandos com privilégios elevados, digite `fltmc`. Verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados.

Se o problema não for resolvido, execute a ferramenta de AFSDiag:
1. Crie um diretório que será usado para salvar a saída da AFSDiag (por exemplo, C:\Output).
2. Abra uma janela do PowerShell com privilégios elevados e execute os seguintes comandos (pressione Enter depois de cada comando):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Para o nível de rastreamento do modo de kernel da Sincronização de arquivos do Azure, insira **1** (a menos que especificado para criar rastreamentos mais detalhados) e pressione Enter.
4. Para o nível de rastreamento do modo de usuário da Sincronização de arquivos do Azure, insira **1** (a menos que especificado para criar rastreamentos mais detalhados) e pressione Enter.
5. Reproduzir o problema. Quando tiver terminado, clique em **D**.
6. Um arquivo. zip que contém logs e arquivos de rastreamento é salvo no diretório de saída que você especificou.

## <a name="see-also"></a>Consulte também
- [Perguntas frequentes da Arquivos do Azure](storage-files-faq.md)
- [Solucionar problemas de Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Solucionar problemas de Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
