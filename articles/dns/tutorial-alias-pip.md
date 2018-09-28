---
title: Tutorial - Criar um registro de alias de DNS do Azure para se referir a um endereço IP público do Azure.
description: Este tutorial mostra como configurar um registro de alias de DNS do Azure para se referir a um endereço IP público do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 842015d853790e3ac78214cca6a70becb7f9fadf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991207"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Tutorial: Configurar um registro de alias para se referir a um endereço IP público do Azure 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma infraestrutura de rede
> * Crie uma máquina virtual do servidor web
> * Criar um registro de alias
> * Testar o registro de alias


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter um nome de domínio disponível que você possa hospedar no DNS do Azure com o qual testar. Você deve ter controle total desse domínio, incluindo a capacidade de definir os registros de NS (nome do servidor) para o domínio.

Para obter instruções para hospedar seu domínio no DNS do Azure, consulte o [Tutorial: hospedar seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo usado para este tutorial é contoso.com, mas você deve usar seu próprio nome de domínio.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede
Primeiro, crie uma rede virtual e uma sub-rede para colocar seus servidores web.
1. Entre no Portal do Azure em http://portal.azure.com.
2. Na parte superior esquerda no portal, clique em **Criar um recurso**, digite *grupo de recursos* na caixa de pesquisa e crie um grupo de recursos denominado **RG-DNS-Alias-pip**.
3. Clique em **Criar um recurso**, clique em **Networking** e, em seguida, clique em **Rede virtual**.
4. Crie uma rede virtual denominada **VNet-Server**, coloque-a no grupo de recursos **RG-DNS-Alias-pip** e o nomeie a sub-rede **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Crie uma máquina virtual do servidor web
1. Clique em **Criar um recurso**, clique em **VM do Windows Server 2016**.
2. Digite **Web-01** para o nome e coloque a máquina virtual no grupo de recursos **RG-DNS-Alias-TM**. Digite um nome de usuário, senha e clique em **OK**.
3. Para **tamanho**, escolha um SKU com 8 GB de RAM.
4. Para **Configurações**, selecione a rede virtual **VNet-Servers**, a sub-rede **SN-Web**. Para as portas de entrada públicas, selecione **HTTP**, **HTTPS**, e **RDP (3389)** e, em seguida, clique em **OK**.
5. Na página Resumo, clique em **Criar**.

   Isso levará alguns minutos para ser concluído.

### <a name="install-iis"></a>Instalar o IIS

Instale o IIS em **Web-01**.

1. Conecte-se ao **Web-01** e entre.
2. No painel Gerenciador do Servidor, clique em **Adicionar funções e recursos**.
3. Clique em **Próximo** três vezes e, na página **Funções do Servidor**, selecione **Servidor Web (IIS)**.
4. Clique em **Adicionar Recursos** e clique em **Próximo**.
5. Clique em **Próximo** quatro vezes e, em seguida, clique em **Instalar**.

   Isso levará alguns minutos para ser concluído.
6. Quando a instalação for concluída, clique em **Fechar**.
7. Abra um navegador da web e navegue até **localhost** para verificar se a página da web do IIS padrão aparece.

## <a name="create-an-alias-record"></a>Criar um registro de alias

Crie um registro de alias que aponta para o endereço IP público.

1. Clique em sua zona DNS do Azure para abrir a zona.
2. Clique em **Conjunto de registros**.
3. Na caixa de texto **Nome** **web01**.
4. Deixe o **tipo** como um registro **A**.
5. Clique na caixa de seleção **Conjunto de registros do alias**.
6. Clique em **Escolher serviço do Azure** e selecione o endereço IP público **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testar o registro de alias

1. No grupo de recursos **RG-DNS-Alias-pip**, clique na máquina virtual **Web-01**. Observe o endereço IP público.
1. Em um navegador da web, navegue até o nome de domínio totalmente qualificado para a máquina virtual Web01-01. Por exemplo: **web01.contoso.com**. Você deve ver a página da web do IIS padrão.
2. Feche o navegador da web.
3. Pare a máquina virtual **Web-01** e, em seguida, reinicie-a.
4. Quando ela for reiniciada, observe o novo endereço IP público para a máquina virtual.
5. Abra um novo navegador e navegue novamente até o nome de domínio totalmente qualificado para a máquina virtual Web01-01. Por exemplo: **web01.contoso.com**.
6. Isso ainda deve funcionar, uma vez que você usou um registro de alias para apontar para o recurso de endereço IP público e não um registro A padrão.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode excluir o grupo de recursos **RG-DNS-Alias-pip** para excluir todos os recursos criados para este tutorial.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um registro de alias para se referir a um endereço IP público do Azure. Para saber mais sobre os aplicativos Web e o DNS do Azure, continue com o tutorial para aplicativos Web.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
