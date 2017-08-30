---
title: "Solução de problemas do Azure Disk Encryption| Microsoft Docs"
description: "Este artigo fornece dicas de solução de problemas para o Microsoft Azure Disk Encryption para VMs de IaaS do Windows e Linux."
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 5f482a92b8fcd71a1b767fcc5741bc57605997ea
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de resolução de problemas do Azure Disk Encryption

Este guia é para profissionais de TI (tecnologia da informação), analistas de segurança de informações e administradores de nuvem cujas organizações estão usando o Azure Disk Encryption e precisam de orientações para solucionar problemas relacionados com criptografia de disco.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Solução de problemas de criptografia de disco do SO Linux

A criptografia de disco do SO Linux deve desmontar a unidade do SO antes de executá-lo através do processo de criptografia de disco completo.   Se não for possível, uma mensagem de erro "falha ao desmontar após ..." provavelmente ocorrerá.

Isso é muito provável quando a criptografia de disco do SO for tentada em um ambiente de VM de destino modificado ou alterado a partir da imagem da galeria de estoque com suporte.  Exemplos de desvios da imagem com suporte, que podem interferir com a capacidade da extensão de desmontar a unidade do SO incluem:
- Imagens personalizadas que não correspondem mais a um sistema de arquivos com suporte e/ou esquema de particionamento.
- Imagens personalizadas com aplicativos como antivírus, Docker, SAP, MongoDB ou Apache Cassandra em execução no sistema operacional antes da criptografia.  Esses aplicativos são difíceis de serem encerrados e, quando eles retêm identificadores de arquivos abertos para a unidade do sistema operacional, a unidade não pode ser desmontada, causando falha.
- Os scripts personalizados que são executados em momento próximo ao da etapa de criptografia podem interferir e causar essa falha. Isso pode acontecer quando um modelo do Resource Manager define múltiplas extensões para executar simultaneamente, ou quando uma extensão de script personalizada ou outra ação é executada simultaneamente para criptografia de disco.   Serializar e isolar tais etapas pode resolver o problema.
- Quando o SELinux não tiver sido desabilitado antes de habilitar a criptografia, a etapa de desmontagem falhará.  O SELinux pode ser novamente habilitado após a conclusão da criptografia.
- Quando o disco do SO estiver usando um esquema de LVM (embora o suporte limitado ao disco de dados de LVM esteja disponível, o disco do SO do LVM não está)
- Quando os requisitos mínimos de memória não são atendidos (7 GB é sugerido para criptografia de disco do SO)
- Quando as unidades de dados foram montadas recursivamente no diretório /mnt/ ou outro (por exemplo, /mnt/data1, /mnt/data2, /data3 + /data3/data4, etc.)
- Quando outros [pré-requisitos](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) do Azure Disk Encryption para Linux não forem atendidos

## <a name="unable-to-encrypt"></a>Não será possível criptografar

Em alguns casos, a criptografia de disco do Linux parece estar paralisada na "criptografia de disco do OS iniciada" e o SSH está desabilitado. Esse processo pode levar entre 3 e 16 horas para ser concluído em uma imagem da galeria de estoque.  Se discos de dados de diversos TB de tamanho são adicionados, o processo pode levar dias. A sequência de criptografia de disco do SO Linux desmonta temporariamente a unidade do SO e executa a criptografia bloco a bloco de todo o disco do SO, antes de remontar em seu estado criptografado.   Ao contrário do Azure Disk Encryption no Windows, o Linux Disk Encryption não permite o uso simultâneo da VM enquanto a criptografia está em andamento.  As características de desempenho da VM, incluindo o tamanho do disco, e se a conta de armazenamento tem suporte pelo armazenamento padrão ou premium (SSD), podem influenciar muito o tempo necessário para concluir a criptografia.

Para verificar o status, o campo ProgressMessage retornado do comando [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) pode ser sondado.   Enquanto a unidade do SO está sendo criptografada, a VM entra em um estado de manutenção e o SSH também é desabilitado para evitar qualquer interrupção no processo em andamento.  EncryptionInProgress será relatado pela maior parte do tempo enquanto a criptografia estiver em andamento, seguido várias horas depois com uma mensagem VMRestartPending solicitando a reinicialização da VM.  Por exemplo:


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

Quando for solicitado a reiniciar a máquina virtual e, depois de reiniciar a VM, e aguardar de 2 a 3 minutos para o reinício e para as etapas finais serem realizadas no destino, a mensagem de status indicará que a criptografia finalmente foi concluída.   Quando essa mensagem estiver disponível, espera-se que a unidade do SO criptografada esteja pronta para uso e que a VM esteja novamente utilizável.

Nos casos em que essa sequência não foi observada, ou se as informações de inicialização, a mensagem de progresso ou outros indicadores de erro relatam que a criptografia do SO falhou no meio desse processo (por exemplo, se você estiver vendo o erro "falha ao desmontar" descrito neste guia), é recomendável restaurar a VM de volta ao instantâneo ou ao backup feito imediatamente antes da criptografia.  Antes da próxima tentativa, sugere-se reavaliar as características da VM e garantir que todos os pré-requisitos sejam satisfeitos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Solução de problemas do Azure Disk Encryption por trás de um firewall
Quando a conectividade estiver restrita por requisitos de proxy, firewall ou NSG (grupo de segurança de rede), a capacidade da extensão para executar as tarefas necessárias poderá ser interrompida.   Isso pode resultar em mensagens de status como "Status de extensão não disponível na VM" e em cenários esperados que não conseguem finalizar.  As seções que a seguir possuem alguns problemas de firewall comuns que poderão ser investigados.

### <a name="network-security-groups"></a>Grupos de segurança de rede
Qualquer configuração do grupo de segurança de rede aplicada ainda deve permitir que o ponto de extremidade atenda aos [pré-requisitos](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) da configuração de rede documentada para criptografia de disco.

### <a name="azure-keyvault-behind-firewall"></a>Azure Keyvault por trás do firewall
A VM deve poder acessar cofre de chaves. Consulte as diretrizes sobre o acesso ao cofre de chaves por trás de um firewall que é mantido pela equipe do [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall).

### <a name="linux-package-management-behind-firewall"></a>Gerenciamento de pacotes Linux por trás do firewall
Em tempo de execução, o Azure Disk Encryption para Linux depende do sistema de gerenciamento de pacotes da distribuição de destino para instalar os pré-requisitos necessários antes de habilitar a criptografia.  Se as configurações do firewall impedem que a VM seja capaz de baixar e instalar esses componentes, então, falhas subsequentes são esperadas.    As etapas para configurar isso podem variar de acordo com a distribuição.  No Red Hat, quando um proxy é necessário, é vital garantir que o subscription-manager e o yum estejam configurados corretamente.  Consulte [este](https://access.redhat.com/solutions/189533) artigo de suporte do Red Hat sobre esse tópico.  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Solução de problemas de Server Core do Windows Server 2016

No Server Core do Windows Server 2016, o componente bdehdcfg não está disponível por padrão. Esse componente é exigido pelo Azure Disk Encryption.

Para solucionar esse problema, copie os quatro arquivos a seguir de uma VM Data Center do Windows Server 2016 para a pasta c:\windows\system32 da imagem do Server Core:

```
bdehdcfg.exe
bdehdcfglib.dll
bdehdcfglib.dll.mui
bdehdcfg.exe.mui
```

Em seguida, execute o seguinte comando:

```
bdehdcfg.exe -target default
```

Isso criará uma partição de sistema de 550 MB e, em seguida, após uma reinicialização, você poderá usar o Diskpart para verificar os volumes e continuar.  

Por exemplo:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu mais sobre alguns problemas comuns no Azure Disk Encryption e como solucionar problemas. Para obter mais informações sobre esse serviço e sua capacidade, leia:

- [Aplicar a criptografia de disco na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Criptografar uma Máquina Virtual do Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Criptografia de dados em repouso no Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

