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
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 873c64dfbd4ad6ced9e5a9eeb80d7ad6dbc558a6
ms.lasthandoff: 03/17/2017


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>Recuperar arquivos de backup de máquina virtual do Azure (Versão Prévia)

O backup do Azure oferece a funcionalidade de restaurar [VMs e discos do Azure](./backup-azure-arm-restore-vms.md) de backups de VM do Azure. Este artigo explica como você pode recuperar itens, como arquivos e pastas, de um backup de VM do Azure.

> [!Note]
> Esse recurso está disponível para máquinas virtuais do Azure implantadas usando o modelo do Resource Manager e protegidas em um cofre de Serviços de Recuperação.
> Não há suporte para a recuperação de arquivos de um backup criptografado de VM.
>

## <a name="mount-the-volume-and-copy-files"></a>Montar os volume e copiar arquivos

1. Faça logon no [Portal do Azure](http://portal.Azure.com). Encontre o cofre de Serviços de Recuperação relevante e os itens de backup necessários.

2. Na folha de Item de Backup, clique em **Recuperação de Arquivo (Preview)**

    ![Abra o item de backup do cofre de Serviços de Recuperação](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    A folha **Recuperação de arquivo** será aberta.

    ![Folha de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. No menu suspenso **Selecionar ponto de recuperação**, selecione o ponto de recuperação que contém os arquivos desejados. Por padrão, o ponto de recuperação mais recente já está selecionado.

4. Clique em **Baixar Executável** (para VM do Windows Azure) ou **Baixar Script** (para VM Linux) para baixar o software que você usará para copiar os arquivos do ponto de recuperação.

  O arquivo executável/script cria uma conexão entre o computador local e o ponto de recuperação especificado.

5. No computador no qual você deseja recuperar os arquivos, execute o arquivo executável/script. Execute o script com credenciais de administrador. Se você executar o script em um computador com acesso restrito, verifique se há acesso a:

    - go.microsoft.com
    - Pontos de extremidade do Azure usados para backups de VM do Azure
    - porta de saída 3260

   Para o Linux, o script exige os componentes 'open-iscsi' e 'lshw' para se conectar ao ponto de recuperação. Se eles não existirem no computador no qual serão executados, ele solicitará a permissão para instalar os componentes relevantes, e os instalará mediante consentimento.
      
    ![Folha de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/executable-output.png)
    
   
   Você pode executar o script em qualquer máquina que tenha o mesmo sistema operacional (ou compatível) que a VM do backup. Consulte a [tabela Sistema operacional compatível](backup-azure-restore-files-from-vm.md#compatible-os) para saber mais sobre os sistemas operacionais compatíveis. Se a máquina virtual do Azure protegida usar espaços de Armazenamento do Windows (para VMs do Windows) ou Matrizes LVM/RAID (para VMs Linux), você não poderá executar o arquivo executável/script nesta máquina virtual. Em vez disso, execute-o em qualquer outra máquina com um sistema operacional compatível.

### <a name="compatible-os"></a>Sistema operacional compatível

#### <a name="for-windows"></a>Para Windows

A tabela a seguir mostra a compatibilidade entre os sistemas operacionais de computador e servidor. Ao recuperar arquivos, você não pode restaurar arquivos entre sistemas operacionais incompatíveis.

|Sistema operacional de servidor | Sistema operacional de cliente compatível  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

#### <a name="for-linux"></a>Para Linux

No Linux, o requisito fundamental é que o sistema operacional da máquina onde o script será executado deve oferecer suporte ao sistema de arquivos dos arquivos presentes na VM Linux de backup. Ao selecionar uma máquina para executar o script, verifique se ela tem o sistema operacional compatível e as versões mencionadas na tabela a seguir.

|Sistema operacional Linux | Versões  |
| --------------- | ---- |
| Ubuntu | 12.04 e acima |
| CentOS | 6.5 e acima  |
| RHEL | 6.7 e acima |
| Debian | 7 e acima |
| Oracle Linux | 6.4 e acima |

O script também exige componentes python e bash para executar e se conectar com segurança ao ponto de recuperação.

|Componente | Versão  |
| --------------- | ---- |
| bash | 4 e acima |
| python | 2.6.6 e acima  |


### <a name="identifying-volumes"></a>Identificação de Volumes

#### <a name="for-windows"></a>Para Windows

Quando você executa o arquivo executável, o sistema operacional monta os novos volumes e atribui letras de unidade. Você pode usar o Windows Explorer ou o Explorador de arquivos para procurar essas unidades. As letras de unidade atribuídas aos volumes podem não ser as mesmas letras da máquina virtual original, no entanto, o nome do volume é preservado. Por exemplo, se o volume na máquina virtual original era “disco de dados (E:\)”, esse volume pode ser anexado como “disco de dados ('Qualquer letra da unidade disponível':\)” no computador local. Busque em todos os volumes mencionados na saída do script até encontrar a pasta/arquivos.  
       
   ![Folha de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>Para Linux

No Linux, os volumes do ponto de recuperação são montados para a pasta onde o script é executado. Os discos anexados, volumes e os caminhos de montagem correspondente são mostrados adequadamente. Esses caminhos de montagem ficam visíveis aos usuários que têm acesso ao nível raiz. Percorra os volumes mencionados na saída do script.

  ![Folha Recuperação de Arquivo do Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Encerrando a conexão

Depois de identificar os arquivos e copiá-los para um local de armazenamento local, remova ou desmonte as unidades adicionais. Para desmontar as unidades na folha **recuperação de arquivos** no portal do Azure, clique em **Desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Quando os discos tiverem sido desmontados, você receberá uma mensagem informando que tudo correu bem. Pode levar alguns minutos até que a conexão seja atualizada para que você possa remover os discos.

No Linux, após a conexão com o ponto de recuperação ser interrompida, o sistema operacional não removerá automaticamente os caminhos de montagem correspondentes. Eles existem como volumes "órfãos" e são visíveis, mas geram um erro quando você acessa/grava os arquivos. Eles podem ser removidos manualmente. O script, quando executado, identifica esses volumes existentes em qualquer ponto de recuperação anterior e limpa mediante consentimento.

## <a name="special-configurations"></a>Configurações especiais

### <a name="windows-storage-spaces"></a>Espaços de Armazenamento do Windows

Os Espaços de Armazenamento do Windows são uma tecnologia no Armazenamento do Windows que permitem que você virtualize o armazenamento. Com os Espaços de Armazenamento do Windows, você pode agrupar discos padrão da indústria em pools de armazenamento e, em seguida, criar discos virtuais, chamados de espaços de armazenamento, no espaço disponível nesses pools de armazenamento.

Se a VM do Azure que passou por backup usa Espaços de Armazenamento do Windows, você não pode executar o script executável na mesma VM. Em vez disso, execute o script executável em qualquer outra máquina com um sistema operacional compatível.

### <a name="lvmraid-arrays"></a>Matrizes LVM/RAID

No Linux, o LVM (Gerenciador de volumes lógicos) e/ou Matrizes RAID de software são usados para gerenciar volumes lógicos em vários discos. Se a VM de Linux de backup usar LVM e/ou Matrizes RAID, não será possível executar o script na mesma VM. Em vez disso, execute o script em qualquer outro computador com sistema operacional compatível e que ofereça suporte a sistema de arquivos da VM de backup.

A saída do script exibe os discos de LVM e/ou Matrizes RAID e os volumes com o tipo de partição, conforme mostrado abaixo

   ![Folha Saída de LVM do Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Os comandos a seguir precisam ser executados pelo usuário para colocar essas partições online. 

**Para partições LVM**

```
$ pvs <volume name as shown above in the script output> 
```
Isso lista os nomes de grupo de volume em um volume físico.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```
Isso lista todos os volumes lógicos, os nomes e os seus caminhos em um grupo de volumes.

```
$ mount <LV path> </mountpath>
```
Para montar os volumes lógicos no caminho de sua escolha.


**Para Matrizes RAID**

```
$ mdadm –detail –scan
```
Isso exibe detalhes sobre todos os discos raid. O disco RAID relevante será exibido como`/dev/mdm/<RAID array name in the backed up VM>`

Use o comando de montagem se o disco RAID tiver volumes físicos.
```
$ mount [RAID Disk Path] [/mounthpath]
```

Se esse disco RAID tiver outro LVM configurado, siga o mesmo procedimento, conforme descrito acima, para partições LVM com o nome do volume igual ao nome do Disco RAID

## <a name="troubleshooting"></a>Solucionar problemas

Se você tiver problemas durante a recuperação de arquivos de máquinas virtuais, verifique a tabela a seguir para obter informações adicionais.

| Mensagem de erro/Cenário | Causas prováveis | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída de exe: *exceção ao se conectar ao destino* |O script não é capaz de acessar o ponto de recuperação    | Verifique se o computador atende aos requisitos de acesso mencionados acima|  
|    Saída de exe: *o destino já foi acessado por meio de uma sessão iSCSI.* |    O script já foi executado na mesma máquina e as unidades foram anexadas |    Os volumes do ponto de recuperação já foram anexados. Eles NÃO podem ser montados com as mesmas letras de unidade da VM original. Navegue por todos os volumes disponíveis no Explorer para encontrar o arquivo |
| Saída de exe: *esse script é inválido porque os discos foram desmontados por meio do portal/o limite de 12 horas foi excedido. Baixe um novo script do portal.* |    Os discos foram desmontados do portal ou o limite de 12 horas foi excedido |    Esse exe é inválido e não pode ser executado. Se você desejar acessar os arquivos desse ponto de recuperação a tempo, visite o portal para obter um novo exe|
| Na máquina na qual o exe é executado: novos volumes não serão desmontados depois que o botão de desmontagem for clicado |    O Iniciador iSCSI na máquina não está respondendo/atualizando sua conexão para o destino e manutenção do cache |    Aguarde alguns minutos depois que o botão de desmontagem for pressionado. Se os novos volumes ainda não estiverem desmontados, navegue por todos os volumes. Isso força o iniciador a atualizar a conexão e o volume é desmontado com uma mensagem de erro de que o disco não está disponível|
| Saída de exe: o script é executado com êxito, mas a mensagem "Novos volumes anexados" não é exibida na saída do script |    Esse é um problema temporário    | Os volumes já foram anexados. Abra o Explorer para navegar. Se você estiver usando a mesma máquina para todas as execuções de scripts, considere reiniciar a máquina, e a lista deverá ser exibida nas execuções subsequentes do exe. |
| Específico do Linux: não é possível exibir os volumes desejados | O sistema operacional da máquina na qual o script é executado pode não reconhecer o sistema de arquivos subjacente da VM de backup | Verifique se o ponto de recuperação é consistente com a falha ou consistentes com o arquivo. Se for consistente com o arquivo, execute o script em outro computador cujo sistema operacional reconheça o sistema de arquivos da VM de backup |

