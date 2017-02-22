---
title: 'Backup do Azure: Recuperar arquivos e pastas de um backup de VM do Azure | Microsoft Docs'
description: "Recuperar arquivos de um ponto de recuperação de uma máquina virtual do Azure"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "recuperação a nível de item; recuperação de arquivos de backup da VM do Azure; restaurar arquivos de uma VM do Azure"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/6/2017
ms.author: pullabhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: 5c31fcac727a1206cb416cbe7340392a2816729a
ms.openlocfilehash: 93b689c132ec7e391a9107e4e3eb73a48e3ba09d


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>Recuperar arquivos de backup de máquina virtual do Azure (Versão Prévia)

O backup do Azure oferece a funcionalidade de restaurar [VMs e discos do Azure](./backup-azure-arm-restore-vms.md) de backups de VM do Azure. Este artigo explica como você pode recuperar itens, como arquivos e pastas, de um backup de VM do Azure.

> [!Note]
> Esse recurso está disponível para máquinas virtuais do Azure implantadas usando o modelo do Resource Manager e protegidas em um cofre de Serviços de Recuperação.
> Atualmente, a recuperação de arquivos de um backup de VM do Microsoft Azure está na versão prévia. A recuperação de arquivos de uma VM do Linux Azure estará disponível em breve.
Atualmente, não há suporte para a recuperação de arquivos de um backup criptografado de VM.
>

## <a name="mount-the-volume-and-copy-files"></a>Montar os volume e copiar arquivos

1. Faça logon no [Portal do Azure](http://portal.Azure.com). Encontre o cofre de Serviços de Recuperação relevante e os itens de backup necessários.

2. Na folha de Item de Backup, clique em **Recuperação de Arquivo (Preview)**

    ![Abra o item de backup do cofre de Serviços de Recuperação](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    A folha **Recuperação de arquivo** será aberta.

    ![Folha de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. No menu suspenso **Selecionar ponto de recuperação**, selecione o ponto de recuperação que contém os arquivos desejados. Por padrão, o ponto de recuperação mais recente já está selecionado.

4. Clique em **Baixar arquivo executável** para baixar o software que você usará para copiar os arquivos do ponto de recuperação.

  O arquivo executável é um script que cria uma conexão entre o computador local e o ponto de recuperação especificado.

5. No computador no qual você deseja recuperar os arquivos, execute o arquivo executável. Você deve executar o script com credenciais de administrador. Se você executar o script em um computador com acesso restrito, verifique se há acesso a:

    - go.microsoft.com
    - Pontos de extremidade do Azure usados para backups de VM do Azure
    - porta de saída 3260

    ![Folha de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Você pode executar o script em qualquer máquina que tenha o mesmo sistema operacional (ou compatível) que a máquina usada para gerar o ponto de recuperação. Consulte a [tabela Sistema operacional compatível](backup-azure-restore-files-from-vm.md#compatible-os) para saber mais sobre os sistemas operacionais compatíveis. Se a máquina virtual protegida do Azure usa espaços de armazenamento do Windows, você não pode executar o script executável nesta máquina virtual. Em vez disso, execute o script executável em qualquer outro computador que use os espaços de armazenamento do Windows. É recomendável executar o script executável em um computador com um sistema operacional compatível.

    ![Folha de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

### <a name="compatible-os"></a>Sistema operacional compatível

A tabela a seguir mostra a compatibilidade entre os sistemas operacionais de computador e servidor. Ao recuperar arquivos, você não pode restaurar arquivos entre sistemas operacionais incompatíveis.

|Sistema operacional de servidor | Sistema operacional de cliente compatível  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |


### <a name="drive-letter-assignments"></a>Atribuições de letra da unidade

Quando você executa o script, o sistema operacional monta novos volumes e atribui letras de unidade. Você pode usar o Windows Explorer ou o Explorador de arquivos para procurar essas unidades. As letras de unidade atribuídas aos volumes podem não ser as mesmas letras da máquina virtual original, no entanto, o nome do volume é preservado. Por exemplo, se o volume na máquina virtual original era “disco de dados (E:\)”, esse volume pode ser anexado como “disco de dados ('Qualquer letra da unidade disponível':\)” no computador local. Busque em todos os volumes mencionados na saída do script até encontrar a pasta/arquivos.  


## <a name="closing-the-connection"></a>Encerrando a conexão

Depois de identificar os arquivos e copiá-los para um local de armazenamento local, remova ou desmonte as unidades adicionais. Para desmontar as unidades na folha **recuperação de arquivos** no portal do Azure, clique em **Desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Quando os discos tiverem sido desmontados, você receberá uma mensagem informando que tudo correu bem. Pode levar alguns minutos até que a conexão seja atualizada para que você possa remover os discos.

## <a name="windows-storage-spaces"></a>Espaços de Armazenamento do Windows

Os Espaços de Armazenamento do Windows são uma tecnologia no Armazenamento do Windows que permitem que você virtualize o armazenamento. Com os Espaços de Armazenamento do Windows, você pode agrupar discos padrão da indústria em pools de armazenamento e, em seguida, criar discos virtuais, chamados de espaços de armazenamento, no espaço disponível nesses pools de armazenamento.

Se a VM do Azure que passou por backup usa Espaços de Armazenamento do Windows, você não pode executar o script executável nesta VM. Em vez disso, execute o script executável em qualquer outro computador que use os espaços de armazenamento do Windows. É recomendável executar o script executável em um computador com um sistema operacional compatível.

## <a name="troubleshooting"></a>Solucionar problemas

Se você tiver problemas durante a recuperação de arquivos de máquinas virtuais, verifique a tabela a seguir para obter informações adicionais.

| Mensagem de erro/Cenário | Causas prováveis | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída de exe: *exceção ao se conectar ao destino* |O script não é capaz de acessar o ponto de recuperação    | Verifique se o computador atende aos requisitos de acesso mencionados acima|  
|    Saída de exe: *o destino já foi acessado por meio de uma sessão iSCSI.* |    O script já foi executado na mesma máquina e as unidades foram anexadas |    Os volumes do ponto de recuperação já foram anexados. Eles NÃO podem ser montados com as mesmas letras de unidade da VM original. Navegue por todos os volumes disponíveis no Explorer para encontrar o arquivo |
| Saída de exe: *esse script é inválido porque os discos foram desmontados por meio do portal/o limite de 12 horas foi excedido. Baixe um novo script do portal.* |    Os discos foram desmontados do portal ou o limite de 12 horas foi excedido |    Esse exe é inválido e não pode ser executado. Se você desejar acessar os arquivos desse ponto de recuperação a tempo, visite o portal para obter um novo exe|
| Na máquina na qual o exe é executado: novos volumes não serão desmontados depois que o botão de desmontagem for clicado |    O Iniciador iSCSI na máquina não está respondendo/atualizando sua conexão para o destino e manutenção do cache |    Aguarde alguns minutos depois que o botão de desmontagem for pressionado. Se os novos volumes ainda não estiverem desmontados, navegue por todos os volumes. Isso força o iniciador a atualizar a conexão e o volume é desmontado com uma mensagem de erro de que o disco não está disponível|
| Saída de exe: o script é executado com êxito, mas a mensagem "Novos volumes anexados" não é exibida na saída do script |    Esse é um problema temporário    | Os volumes já foram anexados. Abra o Explorer para navegar. Se você estiver usando a mesma máquina para todas as execuções de scripts, considere reiniciar a máquina, e a lista deverá ser exibida nas execuções subsequentes do exe. |



<!--HONumber=Feb17_HO1-->


