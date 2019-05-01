---
title: Solução de problemas – Azure Disk Encryption para VMs de IaaS | Microsoft Docs
description: Este artigo fornece dicas de solução de problemas para o Microsoft Azure Disk Encryption para VMs de IaaS do Windows e Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 4715ec92c4ee45733cc0eb2839c533f9ee8968fe
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694123"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de solução de problemas do Azure Disk Encryption

Este guia destina-se a profissionais de TI, analistas de segurança da informação e administradores de nuvem cujas organizações usam o Azure Disk Encryption. Este artigo é para ajudar na solução de problemas relacionados à criptografia de disco.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

## <a name="bkmk_Ubuntu14"></a> Atualizar o kernel padrão para o Ubuntu 14.04 LTS

A imagem do Ubuntu 14.04 LTS é fornecida com uma versão padrão do kernel de 4.4. Esta versão do kernel tem um problema conhecido no qual o Encerrador de Memória Insuficiente encerra incorretamente o comando dd durante o processo de criptografia do sistema operacional. Esse bug foi corrigido no mais recente kernel do Linux ajustado para o Azure. Para evitar esse erro, antes de habilitar a criptografia na imagem, atualize para o [kernel ajustado para o Azure 4.15](https://packages.ubuntu.com/trusty/linux-azure) ou posterior usando os comandos a seguir:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Depois de reiniciar a VM para o novo kernel, a nova versão do kernel pode ser confirmada usando:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Atualizar o agente de máquina Virtual do Azure e as versões da extensão

Operações de criptografia de disco do Azure podem falhar em imagens de máquina virtual usando versões sem suporte do agente de máquina Virtual do Azure. Imagens do Linux com as versões de agente anteriores ao 2.2.38 devem ser atualizadas antes de habilitar a criptografia. Para obter mais informações, consulte [como atualizar o agente Linux do Azure em uma máquina virtual](../virtual-machines/extensions/update-linux-agent.md) e [suporte de versão mínima para agentes de máquina virtual no Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

A versão correta da extensão do agente de convidado Microsoft.Azure.Security.AzureDiskEncryption ou Microsoft.Azure.Security.AzureDiskEncryptionForLinux também é necessária. Versões da extensão são mantidas e atualizadas automaticamente pela plataforma, quando os pré-requisitos de agente de máquina Virtual do Azure são atendidos, uma versão com suporte do agente de máquina virtual é usada.

A extensão Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux foi preterida e não é mais suportada.  

## <a name="unable-to-encrypt-linux-disks"></a>Não é possível criptografar discos do Linux

Em alguns casos, a criptografia de disco do Linux parece estar paralisada na "criptografia de disco do OS iniciada" e o SSH está desabilitado. O processo de criptografia pode levar entre 3 e 16 horas para ser concluído em uma imagem da galeria de estoque. Se discos de dados com diversos terabytes forem adicionados, o processo poderá levar dias.

A sequência de criptografia de disco do SO Linux desmonta a unidade do SO temporariamente. Em seguida, ela realiza a criptografia bloco a bloco de todo o disco do SO, antes de remontá-lo em seu estado criptografado. Diferentemente da Criptografia de Disco do Azure no Windows, a Criptografia de Disco do Linux não permite o uso simultâneo da VM enquanto a criptografia está em andamento. As características de desempenho da VM podem fazer uma diferença significativa no tempo necessário para concluir a criptografia. Essas características incluem o tamanho do disco e se a conta de armazenamento é padrão ou premium (SSD).

Para verificar o status de criptografia, sondar o **ProgressMessage** campo retornado da [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) comando. Enquanto a unidade do SO está sendo criptografada, a VM entra em um estado de manutenção e desabilita o SSH para evitar qualquer interrupção no processo em andamento. A mensagem **EncryptionInProgress** será reportada a maior parte do tempo enquanto a criptografia estiver em andamento. Horas depois, uma mensagem **VMRestartPending** solicitará a reinicialização da VM. Por exemplo: 


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
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

Quando a criptografia é habilitada com [credenciais do Azure AD](azure-security-disk-encryption-prerequisites-aad.md), a VM de destino deve permitir a conectividade com pontos de extremidade do Azure Active Directory e pontos de extremidade do Key Vault. Os pontos de extremidade de autenticação do Active Directory do Azure atuais são mantidos nas seções 56 e 59 da documentação [Intervalos de endereços IP e URLs do Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges). As instruções do Key Vault são fornecidas na documentação sobre como [Acessar o Azure Key Vault por trás de um firewall](../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure 
A VM precisa conseguir acessar o ponto de extremidade do [Serviço de Metadados de Instância do Azure](../virtual-machines/windows/instance-metadata-service.md), que usa um endereço IP não roteável conhecido (`169.254.169.254`) que pode ser acessado somente na VM.  Não há suporte para as configurações de proxy que alteram o tráfego HTTP local para esse endereço (por exemplo, adicionando um cabeçalho X-Forwarded-For).

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

1. Digite o seguinte comando:

   ```
   bdehdcfg.exe -target default
   ```

1. Esse comando cria uma partição de sistema de 550 MB. Reinicialize o sistema.

1. Use DiskPart para verificar os volumes e continue.  

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

## <a name="troubleshooting-encryption-status"></a>Solução de problemas do status da criptografia 

O portal poderá exibir um disco criptografado mesmo depois que ele tiver sido descriptografado dentro da VM.  Isso pode ocorrer quando comandos de baixo nível são usados para descriptografar diretamente o disco da VM, em vez de usar os comandos de gerenciamento do Azure Disk Encryption nível mais alto.  O nível mais alto de comandos não apenas retirar o disco de dentro da VM, mas fora da VM que eles também atualizar as configurações de criptografia no nível de plataforma importantes e as configurações de extensão associadas à VM.  Se eles não são mantidos em alinhamento, a plataforma não poderá relatar o status de criptografia ou provisionar a VM adequadamente.   

Para desativar corretamente o Azure Disk Encryption, iniciar a partir de um bom estado conhecido com a criptografia habilitada e, em seguida, usar o [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) e [AzVMDiskEncryptionExtension remover](/powershell/module/az.compute/remove-azvmdiskencryptionextension) Powershell comandos, ou o [desabilitar a criptografia de vm az](/cli/azure/vm/encryption) comando da CLI. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu mais sobre alguns problemas comuns no Azure Disk Encryption e como solucioná-los. Para saber mais sobre esse serviço e seus recursos, confira os seguintes artigos:

- [Aplicar a criptografia de disco na Central de Segurança do Azure](../security-center/security-center-apply-disk-encryption.md)
- [Criptografia de dados em repouso do Azure](azure-security-encryption-atrest.md)
