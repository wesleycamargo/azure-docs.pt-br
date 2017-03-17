---
title: "Gerenciar servidores DNS usados por uma rede virtual (Clássica) - Portal do Azure (Clássico) | Microsoft Docs"
description: "Saiba como adicionar e remover servidores DNS em uma rede virtual (Clássica) usando o Portal do Azure (Clássico)."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: b582be7d-dc78-4cfe-a766-185bd7e5dc68
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: b765fb94f881453ae6a90ec0ae6b6f06843b3aa2
ms.lasthandoff: 02/28/2017


---
# <a name="manage-dns-servers-used-by-a-virtual-network-classic-using-the-azure-portal-classic"></a>Gerenciar servidores DNS usados por uma rede virtual (Clássica) usando o Portal do Azure (Clássico)

Você pode gerenciar a lista de servidores DNS usados em uma rede virtual (VNet) no Portal do Azure (Clássico) ou no arquivo de configuração de rede. Você pode adicionar até 12 servidores DNS para cada VNet. Ao especificar servidores DNS, é importante verificar se os servidores DNS estão listados na ordem correta para seu ambiente. As listas de servidores DNS não funcionam em round robin. Elas são usadas na ordem em que foram especificadas. Se o primeiro servidor DNS na lista puder ser alcançado, o cliente usará esse servidor DNS independentemente de ele estar funcionando corretamente. Para alterar a ordem de servidor DNS para sua rede virtual, remova os servidores DNS da lista e adicione-as na ordem em que desejar.

> [!WARNING]
> Depois que a lista de DNS tiver sido atualizada, você deve reiniciar as máquinas virtuais localizadas na rede virtual para que obtenham as novas configurações de servidor DNS. As máquinas virtuais continuarão a usar a configuração atual até que sejam reiniciadas.
> 
> 

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-azure-portal-classic"></a>Editar uma lista de servidores DNS para uma rede virtual usando o Portal do Azure (Clássico)
1. Faça logon no [Portal do Azure (Clássico)](https://manage.windowsazure.com).
2. No painel de navegação, clique em **Redes** e no nome da sua rede virtual na coluna **Nome**.
3. Clique em **Configurar**.
4. Em **Servidores DNS**, você pode configurar o seguinte:
   
   * **Para registrar (adicionar) um novo servidor DNS:** simplesmente digite o nome e o endereço IP nas caixas. Isso adicionará um servidor DNS à lista de servidores DNS da rede virtual e também registrará o servidor DNS no Azure.
   * **Para adicionar um servidor DNS que foi registrado anteriormente:** se você tiver registrado um servidor DNS no Azure, selecione-o na lista pré-populada.
   * **Para remover um servidor DNS de sua rede virtual:** clique no X ao lado do servidor que você deseja remover. Observe que isso remove o servidor somente dessa lista de rede virtual. O servidor DNS permanece registrado no Azure para que suas outras redes virtuais possam usá-lo. Para excluir um servidor DNS de sua assinatura, vá para a página **Redes -> Servidores DNS**.
   * **Para reordenar servidores DNS:** remova todos os servidores DNS listados e adicione-os de volta na ordem em que desejar. Lembre-se de que isso não é uma lista de DNS round robin.
   * **Para renomear um servidor DNS:** realce o servidor DNS na lista e digite o novo nome. Isso irá registrar um novo servidor DNS no Azure e adicioná-lo à lista de servidores DNS da sua rede virtual. O servidor DNS antigo e o seu endereço IP permanecerão registrados no Azure. Você poderá excluí-los na página **Servidores DNS** se não estiver usando-o em outras redes virtuais.
5. Clique em **Salvar** na parte inferior da página para salvar a nova configuração de servidores DNS.
6. Reinicie as máquinas virtuais localizadas na rede virtual para permitir que elas obtenham as novas configurações de DNS.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Editar uma lista de servidores DNS usando um arquivo de configuração de rede
Para editar uma lista de servidores DNS usando um arquivo de configuração de rede, você primeiro deve exportar as configurações do Portal de Gerenciamento. Em seguida, você editará o arquivo de configuração de rede e o importará novamente por meio do Portal do Azure (Clássico). Abaixo está uma lista de alto nível das etapas para concluir esse processo.

1. Exporte as configurações de rede virtual para um arquivo de configuração de rede. Para saber mais e obter mais etapas para exportar as configurações de rede, confira [Exportar configurações de rede virtual para um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).
2. Especifique as informações do servidor DNS da sua rede virtual. Para saber mais sobre como especificar um servidor DNS, confira [Especificando um servidor DNS em um arquivo de configuração de rede virtual](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Para saber mais sobre arquivos de configuração de rede, confira [Esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) e [Configurar uma rede Virtual usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).
3. Importe o arquivo de configuração de rede. Para saber mais e obter mais etapas para importar o arquivo de configuração de rede, confira [Importar um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).
4. Reinicie as máquinas virtuais localizadas na rede virtual para permitir que elas obtenham as novas configurações de DNS.


