---
title: Criptografar discos em uma VM do Linux no Azure | Microsoft Docs
description: Como criptografar discos virtuais em uma VM Linux para aumentar a segurança usando a CLI do Azure 2.0
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 75ec087536d6f833a9a2106b1fdf4ed1fd73ef8e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38634613"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Como criptografar uma máquina virtual Linux no Azure
Para conformidade e segurança aprimoradas da VM (máquina virtual), os discos virtuais e a própria VM podem ser criptografados. VMs são criptografadas usando chaves criptográficas que são protegidas em um Azure Key Vault. Você controla essas chaves criptográficas e pode auditar seu uso. Este artigo detalha como criptografar discos virtuais em uma VM Linux usando a CLI do Azure 2.0. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que esteja em execução a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Visão geral da criptografia de disco
Discos virtuais em VMs do Linux são criptografados em repouso usando [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Não há nenhuma taxa para criptografar discos virtuais no Azure. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Uma entidade de serviço do Azure Active Directory fornece um mecanismo seguro para emitir essas chaves de criptografia, enquanto as VMs são ligadas e desligadas.

O processo de criptografia de uma VM é o seguinte:

1. Crie uma chave de criptografia em um Cofre de chaves do Azure.
2. Configure a chave de criptografia a ser usada para criptografar discos.
3. Para ler a chave de criptografia do Azure Key Vault, crie uma entidade de serviço do Azure Active Directory com as permissões apropriadas.
4. Execute o comando para criptografar seus discos virtuais, especificando a entidade de serviço do Azure Active Directory e a chave de criptografia apropriada a ser usada.
5. A entidade de serviço do Azure Active Directory solicita a chave de criptografia necessária do Azure Key Vault.
6. Os discos virtuais são criptografados usando a chave de criptografia fornecida.

## <a name="encryption-process"></a>Processo de criptografia
A criptografia de disco depende dos seguintes componentes adicionais:

* **Cofre de Chaves do Azure** – usado para proteger chaves criptográficas e segredos usados para o processo de criptografia/descriptografia do disco.
  * Se houver um, você pode usar um Cofre de Chaves do Azure existente. Não é necessário dedicar um Cofre de Chaves para criptografar discos.
  * Para separar os limites administrativos e visibilidade de chave, crie um Cofre de Chaves dedicado.
* **Azure Active Directory** – realiza a troca segura de chaves criptográficas necessárias e a autenticação para ações solicitadas.
  * Normalmente, você pode usar uma instância existente do Azure Active Directory para hospedar seu aplicativo.
  * A entidade de serviço fornece um mecanismo seguro para solicitar e receber as chaves de criptografia apropriadas. Você não está desenvolvendo um aplicativo real que se integra ao Azure Active Directory.

## <a name="requirements-and-limitations"></a>Requisitos e limitações
Requisitos e cenários com suporte para criptografia de disco:

* Os seguintes SKUs de servidor Linux – Ubuntu, CentOS, SUSE e SLES (SUSE Linux Enterprise Server) e Red Hat Enterprise Linux.
* Todos os recursos (por exemplo, Cofre de chaves, conta de Armazenamento e VM) devem pertencer à mesma assinatura e região do Azure.
* VMs Standard das séries A, D, DS, G, GS etc.
* Atualização das chaves de criptografia em uma VM Linux já está criptografado.

A criptografia de disco não tem suporte atualmente nos seguintes cenários:

* VMs de camada básica.
* VMs criadas com o modelo de implantação Clássico.
* Desabilitação da criptografia de disco do OS em VMs do Linux.
* Uso de imagens personalizadas do Linux.

Para saber mais sobre cenários com suporte e limitações, consulte [Azure Disk Encryption para VMs de IaaS](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Criar um Azure Key Vault e as chaves
Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myKey* e *myVM*.

A primeira etapa é criar um Cofre de Chaves do Azure para armazenar as chaves criptográficas. O Cofre de Chaves do Azure pode armazenar chaves, segredos ou senhas que permitem implementá-los de forma segura em seus aplicativos e serviços. Para criptografia de disco virtual, use o Cofre de chaves para armazenar uma chave de criptografia que é usada para criptografar ou descriptografar seus discos virtuais.

Habilite o provedor do Azure Key Vault dentro de sua assinatura do Azure com [az provider register](/cli/azure/provider#az-provider-register) e crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local `eastus`:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

O Cofre de chaves do Azure que contém as chaves criptográficas e recursos de computação associados, como armazenamento e a própria VM deve residir na mesma região. Crie um Azure Key Vault com [az keyvault create](/cli/azure/keyvault#az-keyvault-create) e habilite o Key Vault para uso com criptografia de disco. Especifique um nome exclusivo de Key Vault para *keyvault_name* da seguinte maneira:

```azurecli-interactive
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

É possível armazenar chaves de criptografia usando a proteção do Modelo de segurança de Hardware (HSM) ou software. Usar um HSM requer um Cofre de Chaves premium. Há um custo adicional para a criação de um Cofre de Chaves premium em vez do Cofre de Chaves padrão que armazena as chaves protegidas por software. Para criar um Cofre de Chaves premium, na etapa anterior, adicione `--sku Premium` ao comando. O exemplo a seguir usa chaves protegidas por software, desde a criação de um Key Vault padrão.

Para ambos os modelos de proteção, a plataforma do Azure deve ter acesso para solicitar as chaves criptográficas quando a VM é inicializada para descriptografar os discos virtuais. Criar uma chave de criptografia em seu Key Vault com [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create). O exemplo a seguir cria uma chave chamada *myKey*:

```azurecli-interactive
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-an-azure-active-directory-service-principal"></a>Criar uma entidade de serviço do Azure Active Directory
Quando os discos virtuais são criptografados ou descriptografados, especifique uma conta para lidar com a autenticação e a troca de chaves de criptografia do Key Vault. Essa conta, uma entidade de serviço do Azure Active Directory, permite que a plataforma do Azure solicite as chaves de criptografia apropriadas em nome da VM. Uma instância do Azure Active Directory padrão está disponível em sua assinatura, embora muitas organizações tenham diretórios do Azure Active Directory dedicados.

Crie uma entidade de serviço usando o Azure Active Directory com [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). O exemplo a seguir lê os valores de senha da entidade de serviço para uso em comandos posteriores:

```azurecli-interactive
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

A senha só será exibida quando você criar a entidade de serviço. Se desejar, exiba e grave a senha (`echo $sp_password`). Você pode listar suas entidades de serviço com [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) e exibir informações adicionais sobre uma entidade de serviço específica com [az ad sp show](/cli/azure/ad/sp#az-ad-sp-show).

Para criptografar ou descriptografar discos virtuais com êxito, as permissões na chave de criptografia armazenada no Key Vault devem ser definidas para permitir que a entidade de serviço do Azure Active Directory leia as chaves. Defina permissões em seu Key Vault com [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). No exemplo a seguir, a ID da entidade de serviço é fornecida por meio do comando anterior:

```azurecli-interactive
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Crie uma VM com [az vm create](/cli/azure/vm#az-vm-create) e anexe um disco de dados de 5 GB. Somente determinadas imagens do marketplace dão suporte à criptografia de disco. O exemplo a seguir cria uma VM chamada *myVM* usando uma imagem do *Ubuntu 16.04 LTS*:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH para a VM usando o *publicIpAddress* mostrado na saída do comando anterior. Crie uma partição e o sistema de arquivos e, em seguida, monte o disco de dados. Para obter mais informações, consulte [Conectar-se a uma VM Linux para montar o novo disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Feche a sessão SSH.


## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual
Para criptografar os discos virtuais, reúna todos os componentes anteriores:

1. Especifique a entidade de serviço e senha do Azure Active Directory.
2. Especifique o Cofre de chaves para armazenar os metadados para os discos criptografados.
3. Especifique as chaves de criptografia a serem usadas para criptografia e descriptografia.
4. Especifique se deseja criptografar o disco do sistema operacional, os discos de dados ou todos.

Criptografe sua VM com [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable). O exemplo a seguir usa as variáveis *$sp_id* e *$sp_password* do comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) anterior:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Leva algum tempo para o processo de criptografia de disco ser concluído. Monitorar o status do processo com [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

A saída deverá ser semelhante ao seguinte exemplo truncado:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress"
]
```

Aguarde até que o status para o sistema operacional disco relate **VMRestartPending**, então reinicie a VM com [az vm restart](/cli/azure/vm#az-vm-restart):

```azurecli-interactive
az vm restart --resource-group myResourceGroup --name myVM
```

O processo de criptografia de disco é finalizado durante o processo de inicialização, portanto, aguarde alguns minutos antes de verificar o status de criptografia novamente com [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

O status agora deve relatar o disco do sistema operacional e o disco de dados como **Criptografado**.


## <a name="add-additional-data-disks"></a>Adicionar discos de dados adicionais
Depois de criptografar seus discos de dados, é possível adicionar discos virtuais à sua VM e criptografá-los também. Por exemplo, permite adicionar um segundo disco virtual à VM da seguinte maneira:

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --disk myDataDisk \
    --new \
    --size-gb 5
```

Execute novamente o comando para criptografar os discos virtuais da seguinte forma:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre como gerenciar o Cofre de Chaves do Azure, incluindo a exclusão de chaves criptográficas e cofres, consulte [Gerenciar Cofres de Chaves usando a CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Para obter mais informações sobre criptografia de disco, como preparar uma VM personalizada criptografada para carregar no Azure, consulte [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
