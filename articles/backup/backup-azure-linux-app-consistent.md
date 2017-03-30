---
title: 'Backup do Azure: Backup consistente com o aplicativo de VMs Linux do Azure | Microsoft Docs'
description: Backup consistente com o aplicativo de VMs Linux do Azure
services: backup
documentationcenter: dev-center-name
author: anuragm
manager: shivamg
keywords: backup consistente com o aplicativo; backup de VM do Azure consistente com o aplicativo; Backup de VM Linux; Backup do Azure
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 3/20/2017
ms.author: anuragm;markgal
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 044b5d3834518b44209485d1c1a68f2ac0f8a455
ms.lasthandoff: 03/22/2017


---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Backup consistente com o aplicativo de VMs Linux do Azure (Visualização)

Este artigo fala sobre a estrutura pré e pós-script do Linux e como ela pode ser usada para fazer backup consistente com o aplicativo de VMs Linux do Azure.

> [!Note]
> A estrutura pré e pós-script tem suporte somente para máquinas virtuais Linux implantadas pelo Resource Manager, não há suporte para máquinas virtuais Clássicas ou do Windows.
>

## <a name="how-the-framework-works"></a>Como funciona a estrutura

A estrutura fornece uma opção para executar pré e pós-scripts personalizados ao obter um instantâneo de VM. O pré-script é executado logo antes de obter o instantâneo da VM, e o pós-script é executado imediatamente após a conclusão do instantâneo da VM. Isso oferece a flexibilidade para os usuários controlarem seus aplicativos e o ambiente ao fazer backup da VM.

Um cenário importante para essa estrutura é garantir o backup da VM de forma consistente com o aplicativo. O pré-script pode chamar APIs nativas do aplicativo para desativar as E/Ss e liberar o conteúdo da memória para o disco. Isso garantirá que o instantâneo seja consistente com o aplicativo, ou seja, o aplicativo surgirá quando a VM for iniciada após a restauração. O pós-script pode ser usado para descongelar as E/Ss usando APIs nativas do aplicativo, de modo que o aplicativo possa retomar as operações normais após o instantâneo da VM.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Etapas para configurar o pré-script e o pós-script

1. Faça logon na VM Linux que passará por backup como o usuário raiz.

2. Baixe VMSnapshotPluginConfig.json do [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e copie-o na pasta /etc/azure em todas as VMs que passarão por backup. Crie o diretório /etc/azure se ele ainda não existir.

3. Copie o pré-script e o pós-script para seu aplicativo em todas as VMs que passarão por backup. Você pode copiar os scripts em qualquer local na VM, e precisa atualizar o caminho completo dos arquivos de script no arquivo VMSnapshotPluginConfig.json

4. Verifique se estas permissões existem para os arquivos:

   - VMSnapshotPluginConfig.json - Permissão "600", ou seja, apenas o usuário "raiz" deve ter permissões de "leitura" e "gravação" nesse arquivo, nenhum usuário deve ter permissões de "execução".
   - Arquivo pré-script - Permissão "700", ou seja, apenas o usuário "raiz" deve ter permissões de "leitura", "gravação" e "execução" nesse arquivo.
   - Pós-script - Permissão "700", ou seja, apenas o usuário "raiz" deve ter permissões de "leitura", "gravação" e "execução" nesse arquivo.
   
   > [!Note]
   > A estrutura fornece muita energia para os usuários, portanto, é muito importante que esteja completamente segura, e somente o usuário "raiz" deve ter acesso aos arquivos críticos json e de script.
   > Se caso os requisitos acima não forem atendidos, o script não será executado, resultando falha do sistema de arquivos e do backup consistente.
   >
   
5. Configurar VMSnapshotPluginConfig.json conforme descrito nos detalhes abaixo
    - **pluginName** - deixe esse campo como está, caso contrário os scripts não funcionarão conforme o esperado.
    - **preScriptLocation** - forneça o caminho completo do pré-script na VM a passar pelo backup.
    - **postScriptLocation** - forneça o caminho completo do pós-script na VM a passar pelo backup.
    - **preScriptParams** - qualquer parâmetro opcional que precise ser passado ao pré-script, todos os parâmetros devem estar entre aspas e separados por vírgulas no caso de vários parâmetros.
    - **postScriptParams** - qualquer parâmetro opcional que precise ser passado ao pós-script, todos os parâmetros devem estar entre aspas e separados por vírgulas no caso de vários parâmetros.
    - **preScriptNoOfRetries** - número de vezes que o pré-script deve ser repetido no caso de qualquer erro antes do encerramento. 0 significa apenas uma tentativa, e sem nenhuma repetição em caso de falha.
    - **postScriptNoOfRetries** - número de vezes que o pós-script deve ser repetido no caso de qualquer erro antes do encerramento. 0 significa apenas uma tentativa, e sem nenhuma repetição em caso de falha.
    - **timeoutInSeconds** - tempos limites individuais para o pré-script e o pós-script.
    - **continueBackupOnFailure** - defina esse valor como verdadeiro se você quiser que o Backup do Azure passe por fallback para um backup consistente com falha/ consistente com o sistema de arquivos, no caso de falha de pré-script ou pós-script. Definir isso como falso causará a falha do backup no caso de falha do script (exceto no caso de uma VM de disco único, na qual ele realizará um backup consistente com a falha independentemente dessa configuração).
    - **fsFreezeEnabled** - isso especifica se Linux fsfreeze deve ser chamado durante a obtenção do instantâneo da VM para garantir a consistência do sistema de arquivos. Recomendamos manter essa configuração como verdadeiro, a menos que seu aplicativo tenha dependência na desabilitação do fsfreeze.
    
6. Agora a estrutura do script está configurada, se o backup da VM já estiver configurado, o próximo backup chamará os scripts e disparará o backup consistente com o aplicativo. Se o backup da VM não estiver configurado, configure usando [Backup de máquinas virtuais do Azure para cofres dos Serviços de Recuperação.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Solucionar problemas

Adicione os logs apropriados ao escrever seu pré-script e pós-script e revise os logs de script para corrigir quaisquer problemas de script. Se você ainda tiver problemas para executar scripts, veja a tabela a seguir para saber mais.

| Erro | Mensagem de erro | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |O Pré-Script retornou um erro, portanto talvez o backup não seja consistente com o aplicativo.    | Examine os logs de falha do script para corrigir o problema.|  
|    Post-ScriptExecutionFailed |    O Pós-Script retornou um erro que pode afetar o estado do aplicativo. |    Examine os logs de falha do script para corrigir o problema e verificar o estado do aplicativo. |
| Pre-ScriptNotFound |    O Pré-Script não foi encontrado no local especificado no arquivo de configuração VMSnapshotPluginConfig.json. |    Verifique se o Pré-Script está presente no caminho especificado no arquivo de configuração para garantir o backup consistente com o aplicativo.|
| Post-ScriptNotFound |    O Pós-Script não foi encontrado no local especificado no arquivo de configuração VMSnapshotPluginConfig.json |    Verifique se o Pós-Script está presente no caminho especificado no arquivo de configuração para garantir o backup consistente com o aplicativo.|
| IncorrectPluginhostFile |    O arquivo Pluginhost que vem com a extensão VmSnapshotLinux está corrompido. Portanto, o pré-script e o pós-script não podem ser executados, e o backup não será consistente com o aplicativo.    | Desinstale a extensão VmSnapshotLinux. Ela será automaticamente instalada novamente com o próximo backup para corrigir o problema. |
| IncorrectJSONConfigFile | O arquivo VMSnapshotPluginConfig.json está incorreto. Portanto, o pré-script e pós-script não podem ser executados, e o backup não será consistente com o aplicativo | Baixe a cópia do [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e configure-o novamente |
| InsufficientPermissionforPre-Script | Para executar scripts, o usuário raiz deve ser o proprietário do arquivo, e o arquivo deve ter permissões "700", ou seja, somente o proprietário deve ter permissões de "leitura", "gravação" e "execução" | Verifique se o usuário "raiz" é o "proprietário" do arquivo de script e se somente o proprietário tem permissões de "leitura", "gravação" e "execução". |
| InsufficientPermissionforPost-Script | Para executar scripts, o usuário raiz deve ser o proprietário do arquivo, e o arquivo deve ter permissões "700", ou seja, somente o proprietário deve ter permissões de "leitura", "gravação" e "execução" | Verifique se o usuário "raiz" é o "proprietário" do arquivo de script e se somente o proprietário tem permissões de "leitura", "gravação" e "execução". |
| Pre-ScriptTimeout | A execução do pré-script de backup consistente com o aplicativo atingiu o tempo limite. | Verifique o script e aumente o tempo limite no arquivo VMSnapshotPluginConfig.json localizado em /etc/azure. |
| Post-ScriptTimeout | A execução do pós-script de backup consistente com o aplicativo atingiu o tempo limite. | Verifique o script e aumente o tempo limite no arquivo VMSnapshotPluginConfig.json localizado em /etc/azure. |

## <a name="next-steps"></a>Próximas etapas
[Configurar o backup da VM para um cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

