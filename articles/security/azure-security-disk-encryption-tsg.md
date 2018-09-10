---
title: Solução de problemas do Azure Disk Encryption | Microsoft Docs
description: Este artigo fornece dicas de solução de problemas para o Microsoft Azure Disk Encryption para VMs de IaaS do Windows e Linux.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2018
ms.author: mstewart
ms.openlocfilehash: e63d798c24159777711c9cdd765e40b44826a530
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888722"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de solução de problemas do Azure Disk Encryption

Este guia destina-se a profissionais de TI, analistas de segurança da informação e administradores de nuvem cujas organizações usam o Azure Disk Encryption. Este artigo é para ajudar na solução de problemas relacionados à criptografia de disco.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Solução de problemas de criptografia de disco do SO Linux

A criptografia de disco do SO (sistema operacional) Linux deve desmontar a unidade do SO antes de executá-lo por meio do processo de criptografia de disco completo. Se não for possível desmontar a unidade, é provável que ocorra uma mensagem de erro de "falha ao desmontar após ...".

Esse erro pode ocorrer quando a criptografia de disco do sistema operacional é tentada em um ambiente de VM de destino que foi alterado da imagem da galeria de ações suportada. Desvios da imagem compatível podem interferir com a capacidade da extensão de desmontar a unidade do SO. Exemplos de desvios podem incluir os seguintes itens:
- Imagens personalizadas que não correspondem mais a um sistema de arquivos ou esquema de particionamento suportado.
- Não há suporte para aplicativos grandes, como SAP, MongoDB, Apache Cassandra e Docker, quando eles estão instalados e executados no sistema operacional antes da criptografia. A Criptografia de Disco do Azure não consegue encerrar esses processos com segurança, conforme necessário na preparação da unidade do sistema operacional para criptografia de disco. Se ainda houver processos ativos mantendo alças de arquivos abertos na unidade do sistema operacional, a unidade do sistema operacional não poderá ser desmontada, resultando em uma falha na criptografia da unidade do sistema operacional. 
- Scripts personalizados que são executados próximos à hora de encerramento da criptografia que está sendo habilitada ou se qualquer outra alteração estiver sendo feita na VM durante o processo de criptografia. Esse conflito pode acontecer quando um modelo do Azure Resource Manager define múltiplas extensões para executar simultaneamente ou quando uma extensão de script personalizada ou outra ação é executada simultaneamente com a criptografia de disco. Serializar e isolar tais etapas pode resolver o problema.
- O SELinux (Security Enhanced Linux) não foi desabilitado antes da habilitação da criptografia, fazendo com que a etapa de desmontagem falhe. O SELinux pode ser reabilitado após a conclusão da criptografia.
- O disco do SO usa um esquema LVM (Gerenciador de Volume Lógico). Embora o suporte ao disco de dados LVM limitado esteja disponível, um disco de SO LVM, não.
- Os requisitos mínimos de memória não são atendidos (7 GB é sugerido para criptografia de disco do SO).
- As unidades de dados são montadas recursivamente no diretório /mnt/ ou uma na outra (por exemplo, /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Outros [pré-requisitos](azure-security-disk-encryption-prerequisites.md) do Azure Disk Encryption para Linux não são atendidos.

## <a name="unable-to-encrypt"></a>Não será possível criptografar

Em alguns casos, a criptografia de disco do Linux parece estar paralisada na "criptografia de disco do OS iniciada" e o SSH está desabilitado. O processo de criptografia pode levar entre 3 e 16 horas para ser concluído em uma imagem da galeria de estoque. Se discos de dados com diversos terabytes forem adicionados, o processo poderá levar dias.

A sequência de criptografia de disco do SO Linux desmonta a unidade do SO temporariamente. Em seguida, ela realiza a criptografia bloco a bloco de todo o disco do SO, antes de remontá-lo em seu estado criptografado. Diferentemente da Criptografia de Disco do Azure no Windows, a Criptografia de Disco do Linux não permite o uso simultâneo da VM enquanto a criptografia está em andamento. As características de desempenho da VM podem fazer uma diferença significativa no tempo necessário para concluir a criptografia. Essas características incluem o tamanho do disco e se a conta de armazenamento é padrão ou premium (SSD).

Para verificar o status da criptografia, sonde o campo **ProgressMessage** retornado do comando [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). Enquanto a unidade do SO está sendo criptografada, a VM entra em um estado de manutenção e desabilita o SSH para evitar qualquer interrupção no processo em andamento. A mensagem **EncryptionInProgress** será reportada a maior parte do tempo enquanto a criptografia estiver em andamento. Horas depois, uma mensagem **VMRestartPending** solicitará a reinicialização da VM. Por exemplo: 


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Depois que você for solicitado a reinicializar a VM e após a reinicialização da VM, deverá aguardar de 2 a 3 minutos pela reinicialização e pelas etapas finais a serem executadas no destino. A mensagem do status muda quando a criptografia é concluída. Depois que essa mensagem estiver disponível, espera-se que a unidade do SO criptografada esteja pronta para uso e que a VM esteja pronta para ser usada novamente.

Nos seguintes casos, recomendamos restaurar a VM de volta para o instantâneo ou backup feito logo antes da criptografia:
   - Se a sequência de reinicialização descrita anteriormente não acontecer.
   - Se as informações de inicialização, a mensagem de progresso ou outros indicadores de erro reportarem que a criptografia do SO falhou no meio desse processo. Um exemplo de mensagem é o erro "falha ao desmontar", que é descrito neste guia.

Antes da próxima tentativa, reavaliar as características da VM e garantir que todos os pré-requisitos sejam atendidos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Solucionando problemas do Azure Disk Encryption por trás de um firewall
Quando a conectividade estiver restrita por requisitos de proxy, firewall ou NSG (grupo de segurança de rede), a capacidade da extensão para executar as tarefas necessárias poderá ser interrompida. Essa interrupção pode resultar em mensagens de status como "Status da extensão não disponível na VM". Em cenários esperados, a criptografia não é concluída. As seções que se seguem têm alguns problemas comuns de firewall que podem ser investigados.

### <a name="network-security-groups"></a>Grupos de segurança de rede
Qualquer configuração do grupo de segurança de rede aplicada ainda deve permitir que o ponto de extremidade atenda aos [pré-requisitos](azure-security-disk-encryption-prerequisites.md#bkmk_GPO) da configuração de rede documentada para criptografia de disco.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault por trás de um firewall
A VM deve poder acessar um cofre de chaves. Veja as orientações sobre acesso ao cofre de chaves por trás de um firewall mantido pela equipe do [Azure Key Vault](../key-vault/key-vault-access-behind-firewall.md). 

### <a name="linux-package-management-behind-a-firewall"></a>Gerenciamento de pacotes Linux por trás do firewall

Em tempo de execução, o Azure Disk Encryption para Linux depende do sistema de gerenciamento de pacotes da distribuição de destino para instalar os componentes de pré-requisitos necessários antes de habilitar a criptografia. Se as configurações do firewall impedirem a VM de baixar e instalar esses componentes, falhas subsequentes serão esperadas. As etapas para configurar o sistema de gerenciamento de pacotes podem variar de acordo com a distribuição. No Red Hat, quando um proxy é necessário, é preciso garantir que o gerenciador de assinaturas e o yum sejam configurados corretamente. Para saber mais, confira [How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533) (Como solucionar problemas do gerenciador de assinaturas e do yum).  


## <a name="troubleshooting-windows-server-2016-server-core"></a>Solução de problemas de Server Core do Windows Server 2016

No Windows Server 2016 Server Core, o componente bdehdcfg não está disponível por padrão. Esse componente é exigido pelo Azure Disk Encryption. É usado para dividir o volume do sistema do volume do sistema operacional, o que é feito apenas uma vez para o tempo de vida da VM. Esses binários não são necessários durante as operações posteriores de criptografia.

Para contornar esse problema, copie os quatro arquivos a seguir de uma VM do Data Center do Windows Server 2016 para o mesmo local no Núcleo do Servidor:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. Digite o seguinte comando:

   ```
   bdehdcfg.exe -target default
   ```

   3. Esse comando cria uma partição de sistema de 550 MB. Reinicialize o sistema.

   4. Use DiskPart para verificar os volumes e continue.  

Por exemplo: 

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu mais sobre alguns problemas comuns no Azure Disk Encryption e como solucioná-los. Para saber mais sobre esse serviço e seus recursos, confira os seguintes artigos:

- [Aplicar a criptografia de disco na Central de Segurança do Azure](../security-center/security-center-apply-disk-encryption.md)
- [Criptografia de dados em repouso do Azure](azure-security-encryption-atrest.md)
