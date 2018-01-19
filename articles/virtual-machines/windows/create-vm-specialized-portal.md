---
title: Criar uma VM do Windows de um VHD especializado no portal do Azure | Microsoft Docs
description: Crie uma nova VM do Windows em um VHD no portal do Azure.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: cynthn
ms.openlocfilehash: 33e94777047ea8a116ae6f393439521356a53509
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>Crie uma VM de um VHD usando o portal do Azure


Há várias maneiras de criar VMs no Azure. Se você já tiver um VHD para usar ou quiser copiar o VHD de uma VM existente para usar, você pode criar uma nova VM anexando o VHD como disco do sistema operacional. Esse processo *anexa* o VHD para uma nova VM como o disco do sistema operacional.

Você também pode criar uma nova VM do VHD de uma VM que foi excluído. Por exemplo, se você tiver uma VM do Azure que não está funcionando corretamente, exclua a VM e use o VHD para criar uma nova VM. Você pode reutilizar o mesmo VHD ou criar cópia do VHD criando um instantâneo e, em seguida, criar um novo disco gerenciado do instantâneo. Isso requer algumas etapas adicionais, mas garante que mantenha o VHD original e também fornece um instantâneo como recurso se necessário.

Você tem uma VM no local que você deseja usar para criar uma VM no Azure. Você pode carregar o VHD e anexá-lo a uma nova VM. Para carregar um VHD, você precisa usar o PowerShell ou outra ferramenta para carregá-lo em uma conta de armazenamento, em seguida, crie um disco gerenciado a partir do VHD. Para obter mais informações, consulte [Carregar um VHD especializado](create-vm-specialized.md#option-2-upload-a-specialized-vhd)

Se você quiser usar uma VM ou um VHD para criar várias VMs, você não deve usar esse método. Para implantações maiores, você deve [criar uma imagem](capture-image-resource.md) e [usar essa imagem para criar várias VMs](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Copiar um disco

Crie um instantâneo e, em seguida, crie um disco do instantâneo. Isso permite manter o VHD original como um recurso.

1. No menu do lado esquerdo, clique em **Todos os recursos**.
2. Na lista suspensa **Todos os tipos**, desmarque **Selecionar tudo** e, em seguida, role para baixo e selecione **Discos** para localizar os discos disponíveis.
3. Clique no disco que você deseja usar. A página **Visão geral** para o disco é aberta.
4. Na página de visão geral, no menu da parte superior, clique em **+ Criar instantâneo**. 
5. Insira um nome para o instantâneo.
6. Escolha um **Grupo de Recursos** para o instantâneo. Você pode usar um grupo de recursos existente ou criar um novo.
7. Escolha se deseja usar o armazenamento padrão (HDD) ou o Premium (SDD).
8. Quando terminar, clique em **Criar** para criar o instantâneo.
9. Depois que o instantâneo foi criado, clique em **+ Criar um recurso** no menu à esquerda.
10. Na barra de pesquisa, digite **disco gerenciado** e selecione **Discos Gerenciados** da lista.
11. Na página **Discos Gerenciados**, clique em **Criar**.
12. Digite um nome para o disco.
13. Escolha um **Grupo de Recursos** para o disco. Você pode usar um grupo de recursos existente ou criar um novo. Esse também será o grupo de recursos onde você cria a VM do disco.
14. Escolha se deseja usar o armazenamento padrão (HDD) ou o Premium (SDD).
15. Em **Tipo de fonte**, certifique-se de que **Instantâneo** está selecionado.
16. Na lista suspensa **Fonte instantâneo**, selecione o instantâneo que você deseja usar.
17. Faça quaisquer outros ajustes conforme necessário e, em seguida, clique em **Criar** para criar o disco.

## <a name="create-a-vm-from-a-disk"></a>Criar uma VM de um disco

Após criar o disco gerenciado VHD que você deseja usar, você pode criar a VM no portal.

1. No menu do lado esquerdo, clique em **Todos os recursos**.
2. Na lista suspensa **Todos os tipos**, desmarque **Selecionar tudo** e, em seguida, role para baixo e selecione **Discos** para localizar os discos disponíveis.
3. Clique no disco que você deseja usar. A página **Visão geral** para o disco é aberta.
Na página de visão geral, verifique se **ESTADO DE DISCO** está listado como **Desconectado**. Se não estiver, talvez seja necessário desanexar o disco da VM ou excluir a VM para liberar o disco.
4. No menu na parte superior do painel, clique em **+Criar VM**.
5. Na página **Básico** para a nova VM, digite um nome e selecione um grupo recurso existente ou crie um novo.
6. Na página **Tamanho**, selecione uma página de tamanho da VM e, em seguida, clique em **Selecionar**.
7. Na página **Configurações**, você pode deixar que o portal criar todos os novos recursos ou selecionar uma **Rede Virtual** e **Grupo de segurança de rede** existentes. O portal sempre cria uma nova NIC e endereço IP público para a nova VM. 
8. Altere as opções de monitoramento e adicione as extensões conforme necessário.
9. Quando terminar, clique em **OK**. 
10. Se a configuração da VM passar a validação, clique em **OK** para iniciar a implantação.

## <a name="next-steps"></a>Próximas etapas

Você também pode usar o PowerShell para [carregar um VHD no Azure e criar uma VM especializada](create-vm-specialized.md).


