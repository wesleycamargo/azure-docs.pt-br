---
title: Preparar a imagem de VM do Azure para uso com a cloud-init | Microsoft Docs
description: "Como preparar uma imagem de VM do Azure já existente para implantação com cloud-init"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 2eb7510d4e76e4996e83f351a62c0b025b487df2
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Preparar uma imagem de VM do Azure do Linux para uso com cloud-init
Este artigo mostra como usar uma máquina virtual do Azure existente e prepará-la para ser reimplantada e estar pronta para usar cloud-init. A imagem resultante pode ser usada para implantar uma nova máquina virtual ou conjuntos de dimensionamento de máquinas virtuais, que podem ser ainda mais personalizados pela cloud-init no tempo de implantação.  Esses scripts cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure. Para obter mais informações de como o cloud-init funciona nativamente no Azure e as distribuições do Linux compatíveis, consulte [Visão geral de cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Pré-requisitos
Este documento assume que você já tem uma máquina virtual do Azure em execução executando uma versão compatível do sistema operacional Linux. Você já configurou o computador para atender às suas necessidades, instalou todos os módulos necessários, processou todas as atualizações necessárias e o testou para garantir que ele atende aos seus requisitos. 

## <a name="preparing-rhel-74--centos-74"></a>Preparando o RHEL 7.4 / CentOS 7.4
Você precisa se conectar por SSH à sua VM Linux e executar os seguintes comandos para instalar a cloud-init.

```bash
sudo yum install -y cloud-init gdisk
sudo yum check-update cloud-init -y
sudo yum install cloud-init -y
```

Atualize a seção `cloud_init_modules` em `/etc/cloud/cloud.cfg` para incluir os seguintes módulos:
```bash
- disk_setup
- mounts
```

Aqui está um exemplo da aparência de uma seção `cloud_init_modules` de uso geral.
```bash
 cloud_config_modules:
 - mounts
 - locale
 - set-passwords
 - rh_subscription
 - yum-add-repo
 - package-update-upgrade-install
 - timezone
 - puppet
 - chef
 - salt-minion
 - mcollective
 - disable-ec2-metadata
 - runcmd
 - disk_setup
 - mounts
```
Várias tarefas relacionadas ao provisionamento e tratamento de discos efêmeros precisam ser atualizadas em `/etc/waagent.conf`. Execute os seguintes comandos para atualizar as configurações apropriadas. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
```
Permita apenas o Azure como uma fonte de dados para o Agente Linux do Azure criando um novo arquivo `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` usando um editor de sua preferência com as seguintes linhas:

```bash
# This configuration file is provided by the WALinuxAgent package.
datasource_list: [ Azure ]
```

Se a imagem do Azure existente tiver um arquivo de permuta configurado e você desejar alterar a configuração do arquivo de permuta para novas imagens usando a cloud-init, será necessário remover o arquivo de permuta existente.

Para imagens baseadas em RedHat, siga as instruções no documento do RedHat a seguir explicando como [remover o arquivo de permuta](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/s2-swap-removing-file.html).

Para imagens CentOS com o arquivo de permuta habilitado, você pode executar o seguinte comando para desativar o arquivo de permuta:
```bash
sudo swapoff /mnt/resource/swapfile
```

Certifique-se de que a referência do arquivo de permuta seja removida de `/etc/fstab`, ele deve ser semelhante à seguinte saída:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Para economizar espaço e remover o arquivo de permuta, você pode executar o comando a seguir:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Etapa extra para imagem preparada de cloud-init
> [!NOTE]
> Se sua imagem era anteriormente uma imagem configurada e preparada de **cloud-init**, é necessário executar as etapas a seguir.

Os três comandos a seguir serão usados somente se a VM que está sendo personalizada para ser uma nova imagem de origem especializada tiver sido provisionada anteriormente por cloud-init.  Você NÃO precisará executá-los se sua imagem tiver sido configurada usando o Agente Linux do Azure.

```bash
sudo rm -rf /var/lib/cloud/instances/* 
sudo rm -rf /var/log/cloud-init*
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizando a configuração do Agente Linux 
Todas as imagens de plataforma do Azure têm o Agente Linux do Azure instalado, independentemente se elas foram configuradas por cloud-init ou não.  Execute o seguinte comando para concluir o desprovisionamento de usuário do computador Linux. 

```bash
sudo waagent -deprovision+user -force
```

Para obter mais informações sobre os comandos de desprovisionamento do Agente Linux do Azure, consulte [Agente Linux do Azure](agent-user-guide.md) para obter mais detalhes.

Saia da sessão SSH, em seguida, do shell bash, execute os seguintes comandos de AzureCLI para desalocar, generalizar e criar uma nova imagem de VM do Azure.  Substitua `myResourceGroup` e `sourceVmName` pelas informações adequadas refletindo sua sourceVM.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos adicionais de alterações de configuração do cloud-init, consulte o seguinte:
 
- [Adicionar um usuário do Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar o nome do host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e injetar chaves](tutorial-automate-vm-deployment.md)