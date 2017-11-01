---
title: "Solucionar problemas da Sincronização de Arquivo do Azure (versão prévia) | Microsoft Docs"
description: "Solucionar problemas comuns com a Sincronização de Arquivo do Azure"
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
ms.openlocfilehash: 6bc5f2da2b8628671037b9257db746e73cd3afad
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Solucionar problemas da Sincronização de Arquivos do Azure (versão prévia)
A Sincronização de Arquivos do Azure (versão prévia) permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter todos os caches de que precisar ao redor do mundo.

Este artigo foi projetado para ajudá-lo a solucionar problemas e resolver problemas encontrados com a implantação da Sincronização de Arquivo do Azure. Caso isso não funcione, esse guia ilustra como coletar logs importantes do sistema para ajudar em uma investigação mais profunda dos problemas. Se você não vir a resposta para sua pergunta aqui, não hesite em entrar em contato conosco pelos seguintes canais (em ordem de dimensionamento):

1. Na seção de comentários desse artigo.
2. [Fórum do Armazenamento do Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Arquivos do Azure UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Suporte da Microsoft: Para criar um novo caso de suporte, navegue até a guia “Ajuda + suporte” no Portal do Azure e clique em “Nova solicitação de suporte”.

## <a name="agent-installation-and-server-registration"></a>Instalação do agente e registro do servidor
<a id="agent-installation-failures"></a>**Como solucionar problemas de falhas de instalação do agente**  
Se a instalação do agente de Sincronização de Arquivo do Azure estiver falhando, execute o seguinte comando em um prompt de comandos com privilégios elevados para habilitar o registro em log durante a instalação do agente:

```
StorageSyncAgent.msi /l*v Installer.log
```

Depois que a instalação falhar, examine o installer.log para determinar a causa. 

> [!Note]  
> A instalação do agente falhará se você optar por usar o Microsoft Update e o serviço Windows Update não estiver em execução.

<a id="server-registration-missing"></a>**O servidor não está listado em Servidores Registrados no Portal do Azure**  
Se um servidor não estiver listado em Servidores Registrados para um Serviço de Sincronização de Armazenamento, execute as seguintes etapas:
1. Faça logon no servidor que você deseja registrar.
2. Abra o Explorador de Arquivos e navegue até o diretório de instalação do Agente de Sincronização de Armazenamento (local padrão é `C:\Program Files\Azure\StorageSyncAgent`). 
3. Execute ServerRegistration.exe e siga o assistente para registrar o servidor com um Serviço de Sincronização de Armazenamento.

<a id="server-already-registered"></a>**O Registro do Servidor exibe a seguinte mensagem depois de instalar o agente de Sincronização de Arquivo do Azure: "Este servidor já está registrado"**  
![Uma captura de tela da caixa de diálogo de Registro do Servidor com a mensagem de erro “o servidor já está registrado”](media/storage-sync-files-troubleshoot/server-registration-1.png)

Esta mensagem é exibida se o servidor foi registrado anteriormente com um Serviço de Sincronização de Armazenamento. Para cancelar o registro do servidor com o Serviço de Sincronização de Armazenamento atual e registrar com o novo Serviço de Sincronização de Armazenamento, siga as etapas para [Cancelar o registro de um servidor com uma Sincronização de Arquivo do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se o servidor não estiver listado nos Servidores Registrados no Serviço de Sincronização de Armazenamento, execute os seguintes comandos do PowerShell no servidor onde você deseja cancelar o registro:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se o servidor for parte de um cluster, há um parâmetro `Reset-StorageSyncServer -CleanClusterRegistration` opcional que também removerá o registro de cluster.

<a id="web-site-not-trusted"></a>**Ao registrar um servidor, obtenho várias respostas de "sites não confiáveis". Por quê?**  
Esse erro ocorre porque a política **Segurança reforçada do Internet Explorer** está habilitada durante o registro do servidor. Para saber mais sobre como desabilitar corretamente a política **Segurança reforçada do Internet Explorer**, confira [Preparar servidores do Windows para uso com a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync) e [Como implantar a Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gerenciamento de grupos de sincronização
<a id="cloud-endpoint-using-share"></a>**Falha na criação de ponto de extremidade da nuvem com o seguinte erro: "O compartilhamento de arquivos do Azure especificado já está em uso por um ponto de extremidade da nuvem diferente"**  
Esse erro ocorre se o compartilhamento de arquivos do Azure já está em uso por outro ponto de extremidade da nuvem. 

Se você estiver recebendo esse erro e o compartilhamento de arquivos do Azure não está atualmente em uso por um ponto de extremidade da nuvem, execute as etapas a seguir para limpar os metadados de sincronização de arquivos do Azure no compartilhamento de arquivos do Azure:

> [!Warning]  
> Excluir os metadados em um compartilhamento de arquivos do Azure que está atualmente em uso por um ponto de extremidade da nuvem fará com que as operações de sincronização de arquivos do Azure falhem. 

1. Navegue até o seu compartilhamento de arquivos do Azure no Portal do Azure.  
2. Clique com botão direito no compartilhamento de arquivos do Azure e selecione **Editar metadados**
3. Clique com botão direito em SyncService e selecione **Excluir**.

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

Para determinar se sua função de conta de usuário tem as permissões apropriadas, execute o seguinte procedimento:  
* No portal do Azure, clique em **Grupos de Recursos**
* Selecione o grupo de recursos em que a conta de armazenamento está localizada e clique em **Controle de acesso (IAM)**
* Clique na **função** (por exemplo, Proprietário, Colaborador) para sua conta de usuário.
* Na lista **Provedor de Recursos**, selecione **Autorização da Microsoft** 
* **Atribuição de função** deve ter **Permissões de Leitura** e de **Gravação**
* **Definição de função** deve ter **Permissões de Leitura** e de **Gravação**

<a id="cloud-endpoint-deleteinternalerror"></a>**Falha na exclusão de ponto de extremidade com erro: MgmtInternalError**  
Esse problema pode ocorrer se a conta de armazenamento ou compartilhamento de Arquivo do Azure foi excluída antes de excluir o Ponto de extremidade de nuvem. Esse problema será corrigido em uma futura atualização e o Ponto de extremidade de nuvem pode ser excluído.

Para evitar que esse problema ocorra, exclua o Ponto de extremidade de nuvem antes de excluir a conta de armazenamento ou compartilhamento de Arquivo do Azure.

## <a name="sync"></a>Sincronizar
<a id="afs-change-detection"></a>**Criei um arquivo diretamente no meu compartilhamento dos Arquivos do Azure no SMB ou por meio do portal. Quanto tempo até que o arquivo seja sincronizado com os servidores no grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Como solucionar problemas de a sincronização não estar funcionando em um servidor**  
Se a sincronização estiver falhando em um servidor, execute o seguinte procedimento:
- Verifique se existe um ponto de extremidade do servidor no Portal do Azure para o diretório que você deseja sincronizar para um Compartilhamento de Arquivo do Azure:
    
    ![Uma captura de tela de um Grupo de Sincronização com um ponto de extremidade de nuvem e do servidor no Portal do Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- Examine os logs de eventos operacionais e de diagnóstico com o Visualizador de Eventos, localizado em `Applications and Services\Microsoft\FileSync\Agent`.
- Confirme se o servidor tem conectividade com a Internet.
- Verifique se o Serviço de Sincronização de Arquivo do Azure está em execução no servidor abrindo o snap-in do MMC de Serviços e verifique se o serviço do Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.

<a id="replica-not-ready"></a>**Falha na sincronização com erro: 0x80c8300f – a réplica não está pronta para executar a operação necessária**  
Esse erro será esperado se você criar um Ponto de extremidade de nuvem e usar um compartilhamento de Arquivo do Azure que contém dados. Após a conclusão da detecção de alteração no compartilhamento de Arquivos do Azure (pode levar até 24 horas), a sincronização deve começar a funcionar corretamente.

<a id="broken-sync-files"></a>**Como solucionar problemas de falha de sincronização de arquivos individuais**  
Se estiver ocorrendo a falha de sincronização de arquivos individuais, execute o seguinte procedimento:
- Examine os logs de eventos operacionais e de diagnóstico em `Applications and Services\Microsoft\FileSync\Agent` com o Visualizador de Eventos
- Verifique se não há nenhum identificador aberto no arquivo
    - Observação: a Sincronização de Arquivo do Azure periodicamente gera instantâneos do VSS para arquivos para sincronizar arquivos com identificadores abertos

## <a name="cloud-tiering"></a>Disposição em camadas de nuvem 
<a id="files-fail-tiering"></a>**Como solucionar problemas de arquivos que falham ao serem dispostos em camadas**  
Se os arquivos falharem ao serem dispostos em camadas para os Arquivos do Azure, execute as etapas a seguir:

- Verifique se existem arquivos no Compartilhamento de Arquivo do Azure
    - Observação: um arquivo deve ser sincronizado com um Compartilhamento de Arquivo do Azure antes de poder ser disposto em camadas
- Examine os logs de eventos operacionais e de diagnóstico localizados em `Applications and Services\Microsoft\FileSync\Agent` com o Visualizador de Eventos
- Confirme se o servidor tem conectividade com a Internet 
- Verifique se os drivers de filtro de Sincronização de Arquivo do Azure (StorageSync.sys e StorageSyncGuard.sys) estão em execução
    - Abra um prompt de comandos com privilégios elevados, execute `fltmc` e verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados

<a id="files-fail-recall"></a>**Como solucionar problemas de arquivos que falham quando seu recall é realizado**  
Se os arquivos falharem quando seu recall é realizado, execute as seguintes etapas:
- Examine os logs de eventos operacionais e de diagnóstico localizados em `Applications and Services\Microsoft\FileSync\Agent` com o Visualizador de Eventos
- Verifique se existem arquivos no Compartilhamento de Arquivo do Azure
- Confirme se o servidor tem conectividade com a Internet 
- Verifique se os drivers de filtro de Sincronização de Arquivo do Azure (StorageSync.sys e StorageSyncGuard.sys) estão em execução
    - Abra um prompt de comandos com privilégios elevados, execute `fltmc` e verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados

<a id="files-unexpectedly-recalled"></a>**Como solucionar problemas da realização de recall inesperada de arquivos em um servidor**  
Antivírus, backup e outros aplicativos que leem grandes quantidades de arquivos causarão recalls indesejados a menos que eles respeitem o atributo offline e ignorem a leitura do conteúdo desses arquivos. Ignorar arquivos offline para esses produtos que dão suporte a isso ajudará a evitar recalls indesejados ao realizar operações como exames de antivírus ou trabalhos de backup.

Consulte seu fornecedor de software sobre como configurar sua solução para ignorar a leitura de arquivos offline.

Mais recalls indesejados podem ocorrer em outros cenários, como ao procurar arquivos no Explorador de Arquivos. Abrir uma pasta com arquivos em camadas de nuvem no Explorador de Arquivos pode resultar em recalls indesejados, mais ainda se o antivírus estiver habilitado no servidor.

## <a name="general-troubleshooting"></a>Solução de problemas gerais
Se você encontrar problemas com a Sincronização de Arquivo do Azure em um servidor, comece executando o seguinte:
- Examine os logs de eventos operacionais e de diagnóstico no Visualizador de Eventos
    - Os problemas de sincronização, disposição em camadas e recall são registrados nos logs de eventos operacionais e de diagnóstico em `Applications and Services\Microsoft\FileSync\Agent`
    - Problemas de gerenciamento de um servidor (por exemplo, definições de configuração) são registrados nos logs de eventos operacionais e de diagnósticos em `Applications and Services\Microsoft\FileSync\Management`
- Verifique se o serviço de Sincronização de Arquivo do Azure está em execução no servidor
    - Abra o snap-in do MMC de Serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução
- Verifique se os drivers de filtro de Sincronização de Arquivo do Azure (StorageSync.sys e StorageSyncGuard.sys) estão em execução
    - Abra um prompt de comandos com privilégios elevados, execute fltmc e verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados

Se o problema não for resolvido depois de executar as etapas acima, execute a ferramenta de AFSDiag realizando as seguintes etapas:
1. Crie um diretório que será usado para salvar a saída da AFSDiag (por exemplo, c:\output).
2. Abra uma janela do PowerShell com privilégios elevados e execute os seguintes comandos (pressione Enter depois de cada comando):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. Para o nível de rastreamento do modo de kernel da Sincronização de Arquivo do Azure, insira 1 (a menos que especificado para criar rastreamentos mais detalhados) e pressione Enter.
4. Para o nível de rastreamento do modo de usuário da Sincronização de Arquivo do Azure, insira 1 (a menos que especificado para criar rastreamentos mais detalhados) e pressione Enter.
5. Reproduza o problema e pressione D quando terminar.
6. Um arquivo .zip contendo os arquivos de rastreamento e os logs estará no diretório de saída especificado.

## <a name="see-also"></a>Consulte também
- [Perguntas frequentes da Arquivos do Azure](storage-files-faq.md)
- [Solucionar problemas de Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Solucionar problemas de Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
