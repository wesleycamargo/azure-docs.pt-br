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
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 23f111bef6a68115e4474f3c13e91d69d7e89e1c
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Solucionar problemas da Sincronização de Arquivos do Azure (versão prévia)
Use a Sincronização de arquivos do Azure (versão prévia) para centralizar os compartilhamentos de arquivos de sua organização em Arquivos do Azure, sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo foi projetado para ajudá-lo a solucionar problemas e resolver problemas encontrados com a implantação da Sincronização de arquivos do Azure. Nós também descrevemos como coletar logs importantes do sistema para ajudar em uma investigação mais profunda dos problemas. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. A seção de comentários deste artigo.
2. O [Fórum do Armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
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

<a id="agent-installation-websitename-failure"></a>**Falha na instalação do agente com esse erro: "O assistente do agente de sincronização de armazenamento foi encerrado prematuramente"**  
Esse problema pode ocorrer se o nome padrão do site do ISS for alterado. Para contornar esse problema, renomeie o site padrão do IIS como "Default Web Site" e tente novamente a instalação. O problema será corrigido em uma futura atualização do agente. 

<a id="server-registration-missing"></a>**O servidor não está listado em servidores registrados no portal do Azure**  
Se algum servidor não estiver listado em **Servidores registrados** de um Serviço de Sincronização de Armazenamento:
1. Faça logon no servidor que você deseja registrar.
2. Abra o Explorador de arquivos e, em seguida, vá para o diretório de instalação do agente de sincronização de armazenamento (o local padrão é C:\Program Files\Azure\StorageSyncAgent). 
3. Execute ServerRegistration.exe e siga o assistente para registrar o servidor com um Serviço de Sincronização de Armazenamento.

<a id="server-already-registered"></a>**O Registro do Servidor exibe a seguinte mensagem depois de instalar o agente de Sincronização de arquivos do Azure: "Este servidor já está registrado"** 

![Uma captura de tela da caixa de diálogo de Registro do Servidor com a mensagem de erro “o servidor já está registrado”](media/storage-sync-files-troubleshoot/server-registration-1.png)

Esta mensagem é exibida se o servidor foi registrado anteriormente com um Serviço de Sincronização de Armazenamento. Para cancelar o registro do servidor com o Serviço de Sincronização de Armazenamento atual e registrar com o novo Serviço de Sincronização de Armazenamento, siga as etapas para [Cancelar o registro de um servidor com uma Sincronização de arquivos do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se o servidor não estiver listado em **Servidores registrados** no Serviço de Sincronização de Armazenamento, no servidor cujo registro você deseja cancelar, execute os seguintes comandos do PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se o servidor for parte de um cluster, você pode usar opcional *StorageSyncServer Reset - CleanClusterRegistration* parâmetro também para remover o registro de cluster.

<a id="web-site-not-trusted"></a>**Ao registrar um servidor, obtenho várias respostas de "sites não confiáveis". Por quê?**  
Esse erro ocorre porque a política **Segurança reforçada do Internet Explorer** está habilitada durante o registro do servidor. Para saber mais sobre como desabilitar corretamente a política **Segurança reforçada do Internet Explorer**, confira [Preparar servidores do Windows para uso com a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e [Como implantar a Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gerenciamento de grupo de sincronização
<a id="cloud-endpoint-using-share"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "O FileShare do Azure especificado já está sendo usado por um CloudEndpoint diferente"**  
Esse problema ocorre quando o compartilhamento de arquivos do Azure já está sendo usado por outro ponto de extremidade de nuvem. 

Se essa mensagem aparecer e o compartilhamento de arquivos do Azure não estiver sendo usado por um ponto de extremidade de nuvem no momento, conclua as seguintes etapas para limpar os metadados da Sincronização de arquivos do Azure no compartilhamento de arquivos do Azure:

> [!Warning]  
> Excluir os metadados em um compartilhamento de arquivos do Azure que está sendo usado no momento por um ponto de extremidade de nuvem faz com que as operações da Sincronização de arquivos do Azure falhem. 

1. Navegue até o seu compartilhamento de arquivos do Azure no Portal do Azure.  
2. Clique com botão direito no compartilhamento de arquivos do Azure e selecione **Editar metadados**.
3. Clique com botão direito em **SyncService** e selecione **Excluir**.

<a id="cloud-endpoint-authfailed"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "AuthorizationFailed"**  
Esse problema ocorre quando a conta de usuário não tem direitos suficientes para criar um ponto de extremidade de nuvem. 

Para criar um ponto de extremidade de nuvem, sua conta de usuário deve ter as seguintes permissões de Autorização da Microsoft:  
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

<a id="server-endpoint-createjobfailed"></a>**Falha na criação do ponto de extremidade do servidor, com este erro: "MgmtServerJobFailed" (código de erro:-2134375898)**                                                                                                                           
Esse problema ocorre se o caminho do ponto de extremidade de servidor estiver no volume do sistema e a camada de nuvem estiver habilitada. A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.

<a id="server-endpoint-deletejobexpired"></a>**Falha na exclusão de ponto de extremidade do servidor, com este erro: "MgmtServerJobExpired"**                
Esse problema ocorre se o servidor estiver offline ou não tem conectividade de rede. Se o servidor não estiver mais disponível, cancele o registro do servidor no portal que excluirá os pontos de extremidade do servidor. Para excluir os pontos de extremidade do servidor, siga as etapas descritas em [Cancelar o registro de um servidor com a Sincronização de Arquivos do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

## <a name="sync"></a>Sincronizar
<a id="afs-change-detection"></a>**Se eu criar um arquivo diretamente em meu compartilhamento de arquivos do Azure usando SMB ou por meio do portal, quanto tempo levará para que o arquivo seja sincronizado com os servidores no grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**A sincronização falha em um servidor**  
Se a sincronização falhar em um servidor:
1. Verifique se existe um ponto de extremidade do servidor no portal do Azure para o diretório que você deseja sincronizar com um compartilhamento de arquivos do Azure:
    
    ![Uma captura de tela de um grupo de sincronização com um ponto de extremidade de nuvem e um ponto de extremidade do servidor no portal do Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. No Visualizador de Eventos, examine os logs de eventos operacionais e de diagnóstico, localizados em aplicativos e Services\Microsoft\FileSync\Agent.
    1. Confirme se o servidor tem conectividade com a Internet.
    2. Verifique se o serviço de Sincronização de arquivos do Azure está em execução no servidor. Abra o snap-in do MMC de Serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.

<a id="replica-not-ready"></a>**Falha na sincronização com erro: 0x80c8300f – a réplica não está pronta para executar a operação necessária**  
Esse problema é esperado quando você cria um ponto de extremidade de nuvem e usa um compartilhamento de arquivos do Azure que contém dados. Quando o trabalho de detecção de alteração for concluída em execução no compartilhamento de arquivos do Azure (pode levar até 24 horas), a sincronização deve começar a funcionar corretamente.

<a id="broken-sync-files"></a>**Como solucionar problemas de falha de sincronização de arquivos individuais**  
Se os arquivos individuais não sincronizar:
1. No Visualizador de Eventos, examine os logs de eventos operacionais e de diagnóstico, localizados em aplicativos e Services\Microsoft\FileSync\Agent.
2. Verifique se não há nenhum identificador aberto no arquivo.

    > [!NOTE]
    > A Sincronização de arquivos do Azure periodicamente gera instantâneos do VSS para arquivos para sincronizar arquivos com identificadores abertos.

## <a name="cloud-tiering"></a>Disposição em camadas de nuvem 
Há dois caminhos para falhas na definição de camadas de nuvem:

- A definição de camadas de arquivos pode falhar, o que significa que a Sincronização de arquivos do Azure tenta definir camadas de arquivos nos Arquivos do Azure sem sucesso.
- A recuperação de arquivos pode falhar, o que significa que o filtro do sistema de arquivos da Sincronização de arquivos do Azure (StorageSync.sys) falha ao baixar dados quando um usuário tenta acessar um arquivo que foi definido em camadas.

Há duas classes principais de falhas que podem ocorrer por meio de um desses caminhos de falha:

- Falhas no armazenamento em nuvem
    - *Problemas transitórios de disponibilidade do serviço de armazenamento*. Consulte [SLA (Contrato de Nível de Serviço) para Armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) para obter mais informações.
    - *Compartilhamento de arquivos do Azure inacessível*. Essa falha normalmente acontece quando você exclui o compartilhamento de arquivos do Azure quando ele ainda é um ponto de extremidade de nuvem em um grupo de sincronização.
    - *Conta de armazenamento inacessível*. Essa falha normalmente acontece quando você exclui a conta de armazenamento enquanto ela ainda tem um compartilhamento de arquivos do Azure que é um ponto de extremidade de nuvem em um grupo de sincronização. 
- Falhas de servidor 
    - *O filtro do sistema de arquivos da Sincronização de arquivos do Azure (StorageSync.sys) não está carregado*. Para responder às solicitações de definição de camadas/recuperação, o filtro do sistema de arquivos da Sincronização de arquivos do Azure deve ser carregado. O filtro pode não ser carregado por diversos motivos, mas o motivo mais comum é quando um administrador o descarrega manualmente. O filtro do sistema de arquivos da Sincronização de arquivos do Azure deve ser carregado sempre para que a Sincronização de arquivos do Azure funcione corretamente.
    - *Ponto de nova análise ausente, corrompido ou desfeito por algum outro motivo*. Um ponto de nova análise é uma estrutura de dados especial em um arquivo que consiste em duas partes:
        1. Uma marca de nova análise, que indica para o sistema operacional que o filtro do sistema de arquivos da Sincronização de arquivos do Azure (StorageSync.sys) pode precisar executar alguma ação na E/S do arquivo. 
        2. Dados de nova análise, que indica para o filtro do sistema de arquivos qual é o URI do arquivo no ponto de extremidade de nuvem associado (o compartilhamento de arquivos do Azure). 
        
        A maneira mais comum de corromper um ponto de nova análise é quando um administrador tenta modificar a marca ou seus dados. 
    - *Problemas de conectividade de rede*. Para a definir camadas ou recuperar um arquivo, o servidor deve ter conectividade com a Internet.

As seções a seguir indicam como solucionar problemas de definição de camadas de nuvem e determinar se o problema é do armazenamento em nuvem ou do servidor.

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
