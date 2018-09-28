---
title: Tutorial – Criar um registro de alias do DNS do Azure para dar suporte a nomes de domínio apex com o Gerenciador de Tráfego
description: Este tutorial mostra como configurar um registro de alias do DNS do Azure para dar suporte usando seu nome de domínio apex com o Gerenciador de Tráfego.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 4a5d8968861f6f2938e605d2f7106529ca401df4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967431"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio apex com o Gerenciador de Tráfego 

Você pode criar um registro de alias para o nome de domínio apex (por exemplo, contoso.com) fazer referência a um perfil do Gerenciador de Tráfego. Portanto, em vez de usar um serviço de redirecionamento para isso, você pode configurar o DNS do Azure para fazer referência a um perfil do Gerenciador de Tráfego diretamente da sua zona. 


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VM host e uma infraestrutura de rede
> * Criar um perfil do Gerenciador de Tráfego
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
2. Na parte superior esquerda no portal, clique em **Criar um recurso**, digite *grupo de recursos* na caixa de pesquisa e crie um grupo de recursos denominado **RG-DNS-Alias-TM**.
3. Clique em **Criar um recurso**, clique em **Networking** e, em seguida, clique em **Rede virtual**.
4. Crie uma rede virtual denominada **VNet-Server**, coloque-a no grupo de recursos **RG-DNS-Alias-TM** e o nomeie a sub-rede **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Criar duas máquinas virtuais do servidor web
1. Clique em **Criar um recurso**, clique em **VM do Windows Server 2016**.
2. Digite **Web-01** para o nome e coloque a máquina virtual no grupo de recursos **RG-DNS-Alias-TM**. Digite um nome de usuário, senha e clique em **OK**.
3. Para **tamanho**, escolha um SKU com 8 GB de RAM.
4. Para **Configurações**, selecione a rede virtual **VNet-Servers**, a sub-rede **SN-Web**.
5. Clique em **Endereço IP público** e, em **Atribuição**, clique em **Estático** e, em seguida, clique em **OK**.
6. Para as portas de entrada públicas, selecione **HTTP**, **HTTPS**, e **RDP (3389)** e, em seguida, clique em **OK**.
7. Na página Resumo, clique em **Criar**.

   Isso levará alguns minutos para ser concluído.
6. Repita esse procedimento para criar outra máquina virtual denominada **Web-02**.

### <a name="add-a-dns-label"></a>Adicione um rótulo DNS
Os endereços IP públicos precisam de um rótulo DNS para trabalhar com o Gerenciador de Tráfego.
1. No grupo de recursos **RG-DNS-Alias-TM**, clique no endereço IP público **Web-01-ip**.
2. Em **Configurações**, clique em **Configurar**.
3. Na caixa de texto do rótulo de nome DNS, digite **web01pip**.
4. Clique em **Salvar**.

Repita esse procedimento para o endereço IP público **Web-02-ip**, usando **web02pip** para o rótulo de nome DNS.

### <a name="install-iis"></a>Instalar o IIS

Instale o IIS em ambos **Web-01** e **Web 02**.

1. Conecte-se ao **Web-01** e entre.
2. No painel Gerenciador do Servidor, clique em **Adicionar funções e recursos**.
3. Clique em **Próximo** três vezes e, na página **Funções do Servidor**, selecione **Servidor Web (IIS)**.
4. Clique em **Adicionar Recursos** e clique em **Próximo**.
5. Clique em **Próximo** quatro vezes e, em seguida, clique em **Instalar**.

   Isso levará alguns minutos para ser concluído.
6. Quando a instalação for concluída, clique em **Fechar**.
7. Abra um navegador da web e navegue até **localhost** para verificar se a página da web do IIS padrão aparece.

Repita esse processo para instalar o IIS no **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

Due 

1. Abra o grupo de recursos **RG-DNS-Alias-TM** e clique no endereço IP público **Web-01-ip**. Anote o endereço IP para uso posterior. Repita para o endereço IP público **Web-02-ip**.
1. Clique em **Criar um recurso**, clique em **Networking** e, em seguida, clique em **perfil de Gerenciador de Tráfego**.
2. Para o nome, digite **TM-alias-test** e coloque-o no grupo de recursos **RG-DNS-Alias-TM**.
3. Clique em **Criar**.
4. Quando a implantação for concluída, clique em **Ir para o recurso**.
5. Na página de perfil do Gerenciador de Tráfego, em **Configurações**, clique em **Pontos de Extremidade**.
6. Clique em **Adicionar**.
7. Para **tipo**, selecione **Ponto de extremidade externo**, para **Nome** digite **EP-Web01**.
8. Na caixa de texto **Nome de domínio totalmente qualificado (FQDN) ou IP**, digite o endereço IP para **Web-01-ip** que você anotou anteriormente.
9. Selecione o mesmo **local** que seus outros recursos relacionados e, em seguida, clique em **OK**.

Repita esse procedimento para adicionar o ponto de extremidade **Web-02**, usando o endereço IP que você anotou anteriormente para **Web-02-ip**.

## <a name="create-an-alias-record"></a>Criar um registro de alias

Crie um registro de alias que aponta para o perfil do Gerenciador de Tráfego.

1. Clique em sua zona DNS do Azure para abrir a zona.
2. Clique em **Conjunto de registros**.
3. Deixe a caixa de texto **Nome** vazia para representar o nome de domínio apex (por exemplo, contoso.com).
4. Deixe o **Tipo** como um registro **A**.
5. Clique na caixa de seleção **Conjunto de registros do alias**.
6. Clique em **Escolher serviço do Azure** e selecione o perfil do Gerenciador de Tráfego **TM-alias-test**.

## <a name="test-the-alias-record"></a>Testar o registro de alias

1. Em um navegador da web, navegue até seu nome de domínio apex (por exemplo, contoso.com). Você deve ver a página da web do IIS padrão. Feche o navegador da web.
2. Desligue a máquina virtual **Web-01** e aguarde alguns minutos para que ela seja completamente desligada.
3. Abra um novo navegador da web e navegue até seu nome de domínio apex novamente.
4. Você verá novamente a página do IIS padrão, uma vez que o Gerenciador de Tráfego tiver resolvido a situação e direcionado o tráfego para **Web-02**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode excluir o grupo de recursos **RG-DNS-Alias-TM** para excluir todos os recursos criados para este tutorial.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um registro de alias para usar seu nome de domínio apex para fazer referência a um perfil do Gerenciador de Tráfego. Para saber mais sobre os aplicativos Web e o DNS do Azure, continue com o tutorial para aplicativos Web.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
