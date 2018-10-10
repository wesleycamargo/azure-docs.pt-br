---
title: Configurar o método de roteamento de tráfego da sub-rede usando o Gerenciador de Tráfego do Azure | Microsoft Docs
description: Este artigo explica como configurar o Gerenciador de Tráfego para rotear o tráfego de sub-redes de usuário para pontos de extremidade específicos.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: 20c34b820eb326a18be1c4298b0850a58599be64
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956227"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Direcionar o tráfego para pontos de extremidade específicos com base na sub-rede do usuário usando o Gerenciador de Tráfego

Este artigo descreve como configurar o método de roteamento de tráfego de sub-rede. O método de roteamento de tráfego de **sub-rede** permite que você mapeie um conjunto de intervalos de endereços IP para pontos de extremidade específicos e quando o Gerenciador de Tráfego recebe uma solicitação, ele inspeciona o IP de origem da solicitação e retorna o ponto de extremidade associado a ele. 

Neste tutorial, usando o roteamento da sub-rede, dependendo do endereço IP de consulta do usuário, o tráfego é roteado para um site interno ou um site de produção.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie duas VMs em execução de um site da Web básica no IIS
> * Crie duas VMs para exibir o Gerenciador de Tráfego em ação de teste
> * Configurar nome DNS para as VMs que executam IIS
> * Criar um perfil do Gerenciador de Tráfego para rotear o tráfego com base na sub-rede do usuário
> * Criar pontos de extremidade no perfil do Gerenciador de Tráfego
> * Ver o Gerenciador de Tráfego em ação


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para ver o Gerenciador de Tráfego em ação, este tutorial exige que você implante o seguinte:
- dois sites básicos em execução em diferentes regiões do Azure – **Leste dos EUA** (funciona como o site interno) e **Europa Ocidental** (funciona como o site de produção).
- duas VMs de teste para testar o Gerenciador de Tráfego do Microsoft Azure - uma VM no **Leste dos EUA** e a segunda VM na **Europa Ocidental**. 

As VMs de teste são usadas para ilustrar como o Gerenciador de Tráfego encaminha o tráfego de usuário para o site interno ou para o site de produção com base na sub-rede da qual a consulta do usuário se origina.

### <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no Portal do Azure em https://portal.azure.com.

### <a name="create-websites"></a>Criar sites

Nesta seção, você criará duas instâncias de site que fornecem os dois pontos de extremidade para o perfil do Gerenciador de Tráfego em duas regiões do Azure. Criar dois sites inclui as seguintes etapas:
1. Criar duas VMs para executar um site básico - uma no **Leste dos EUA**e o outro na **Europa Ocidental**.
2. Instale o servidor IIS em cada VM e atualize a página de site padrão que descreve o nome da VM que um usuário está conectado ao visitar o site.

#### <a name="create-vms-for-running-websites"></a>Criar VMs para a execução de sites
Nesta seção, você criará duas VMs, *InternalWebsite* e *ProdWebsite*, nas regiões do Azure **Leste dos EUA** e **Europa Ocidental**.

1. Na parte superior, canto esquerdo do portal do Microsoft Azure, selecione **Criar um recurso** > **Computar** > **Windows Server 2016 VM**.
2. Insira, ou selecione, as informações a seguir para **Princípios básicos**, aceite os padrões para as configurações restantes e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |NOME|InternalWebsite|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupo de recursos| Selecione **Novo**e digite *myResourceGroupTM1*.|
    |Local padrão| Selecione **Leste dos EUA**.|
    |||
4. Selecione um tamanho da VM em **Escolher um tamanho**.
5. Selecione os seguintes valores para **Configurações** e selecione **OK**:
    
    |Configuração|Valor|
    |---|---|
    |Rede virtual| Selecione **Rede Virtual**, em **Criar rede virtual**, para **Nome**, insira *myVNet1*, para a sub-rede, insira *mySubnet*.|
    |Grupo de Segurança de Rede|Selecione **Básico**, e na lista **Selecionar as portas de entrada públicas**, selecione **HTTP** e **RDP** |
    |Diagnóstico de inicialização|Selecione **Desabilitado**.|
    |||
6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implantação da VM.

7. Conclua as etapas 1 a 6 novamente, com as seguintes alterações:

    |Configuração|Valor|
    |---|---|
    |Grupo de recursos | Selecione **Novo**e digite *myResourceGroupTM2*|
    |Local padrão|Europa Ocidental|
    |Nome da VM | ProdWebsite|
    |Rede virtual | Selecione **Rede Virtual**, em **Criar rede virtual**, para **Nome**, insira *myVNet2*, para a sub-rede, insira *mySubnet*.|
    |||
8. As VMs podem levar alguns minutos para serem criadas. Não continue com as etapas restantes até que ambas as VMs sejam criadas.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar o IIS e personalizar a página da Web padrão

Nesta seção, você deve instalar o servidor IIS em duas VMs - *myIISVMEastUS*  & *myIISVMWEurope*e, em seguida, atualizar a página padrão do site. A página do site personalizada mostra o nome da VM que você está se conectando ao visitar o site em um navegador da web.

1. Clique em **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, clique em *myIISVMEastUS*, que está localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Visão geral**, clique em **Conectar**e, em seguida, em **Conectar-se a máquina virtual**, selecione **Fazer download do arquivo RDP**. 
3. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM. 
4. Selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Se você receber o aviso, selecione **Sim** ou **Continuar**, para prosseguir com a conexão.
6. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Gerenciador do Servidor**.
7. Inicie o Windows PowerShell na VM, *InternalWebsite* e use os comandos a seguir para instalar o servidor do IIS e atualizar o arquivo htm padrão.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```
8. Feche a conexão RDP com a VM *InternalWebsite*.
9. Repita as etapas de 1 a 6 criando uma conexão de RDP com a VM *ProdWebsite* dentro do grupo de recursos *myResourceGroupTM2* para instalar o IIS e personalizar sua página da Web padrão.
10. Inicie o Windows PowerShell na VM *ProdWebsite* e use os comandos a seguir para instalar o servidor do IIS e atualizar o arquivo htm padrão.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurar nome DNS para as VMs que executam IIS

O Gerenciador de Tráfego roteia o tráfego de usuário com base no nome DNS dos pontos de extremidade de serviço. Nesta seção, você configura os nomes DNS para os servidores IIS – *InternalWebsite* e *ProdWebsite*.

1. Clique em **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, selecione *InternalWebsite*, que está localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Visão Geral**, em **Nome DNS**, selecione **Configurar**.
3. Na página **Configuração**, em rótulo de nome DNS, adicione um nome exclusivo e, em seguida, selecione **Salvar**.
4. Repita as etapas de 1 a 3 para a VM denominada *ProdWebsite* que está localizada no grupo de recursos *myResourceGroupTM2*.

### <a name="create-test-vms"></a>Criar VMs de teste

Nesta seção, você criará uma VM (*UserVMUS* e *UserVMEurope*) em cada região do Azure (**Leste dos EUA** e **Europa Ocidental**. Você usará essas máquinas virtuais para testar como o Gerenciador de Tráfego roteia o tráfego para o servidor mais próximo do IIS ao navegar até o site.

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Computação** > **Datacenter do Windows Server 2016**.
2. Insira, ou selecione, as informações a seguir para **Princípios básicos**, aceite os padrões para as configurações restantes e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |NOME|*UserVMUS*|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupo de recursos| Selecione **Existente** e *myResourceGroupTM1*.|
    |||

4. Selecione um tamanho da VM em **Escolher um tamanho**.
5. Selecione os seguintes valores para **Configurações** e selecione **OK**:
    |Configuração|Valor|
    |---|---|
    |Rede virtual| Selecione **Rede virtual**. Em **Criar rede virtual**, para **Nome**, insira *myVNet3* e para sub-rede, insira *mySubnet3*.|
    |Grupo de Segurança de Rede|Selecione **Básico**, e na lista **Selecionar as portas de entrada públicas**, selecione **HTTP** e **RDP** |
    |Diagnóstico de inicialização|Selecione **Desabilitado**.|
    |||

6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implantação da VM.

7. Conclua as etapas 1 a 5 novamente, com as seguintes alterações:

    |Configuração|Valor|
    |---|---|
    |Nome da VM | *UserVMEurope*|
    |Grupo de recursos | Selecione **Existente** e em seguida, digite *myResourceGroupTM2*|
    |Rede virtual | Selecione **Rede virtual**. Em **Criar rede virtual**, para **Nome**, insira *myVNet4* e para sub-rede, insira *mySubnet4*.|
    |||

8. As VMs podem levar alguns minutos para serem criadas. Não continue com as etapas restantes até que ambas as VMs sejam criadas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego
Crie um perfil de Gerenciador de Tráfego que permite que você retorne pontos de extremidade específicos com base no IP de origem da solicitação.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Perfil do Gerenciador de Tráfego**  >  **Criar**.
2. No **perfil Criar Gerenciador de Tráfego**, insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e selecione **Criar**:
    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | NOME                   | Esse nome deve ser exclusivo na zona trafficmanager.net e resulta no nome DNS, trafficmanager.net, que é usado para acessar o seu perfil do Gerenciador de Tráfego.                                   |
    | Método de roteamento          | Selecione o método de roteamento **Sub-rede**.                                       |
    | Assinatura            | Selecione sua assinatura.                          |
    | Grupo de recursos          | Selecione **Existente** e insira *myResourceGroupTM1*. |
    | |                              |
    |
  
    ![Criar um perfil do Gerenciador de Tráfego](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos de extremidade do Gerenciador de Tráfego

Adicione as duas VMs executando os servidores IIS, *InternalWebsite*  & *ProdWebsite*, para rotear tráfego de usuário com base na sub-rede da consulta do usuário.

1. Na barra de pesquisa do portal, pesquise o nome do Perfil do Gerenciador de Tráfego que você criou na seção anterior e selecione o perfil do gerenciador de tráfego nos resultados que são exibidos.
2. Em **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, clique em **Pontos de Extremidade** e clique em **Adicionar**.
3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **OK**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Tipo                    | Ponto de extremidade do Azure                                   |
    | NOME           | myInternalWebSiteEndpoint                                        |
    | Tipo de recurso de destino           | Endereço IP público                          |
    | Recurso de destino          | **Escolha um endereço IP Público** para mostrar a lista de recursos com endereços IP públicos na mesma assinatura. Em **Recursos**, selecione o endereço IP público denominado *InternalWebsite-ip*. Isso é o endereço IP público do servidor IIS VM no Leste dos EUA.|
    |  Configurações de roteamento de sub-rede    |   Adicione o endereço IP da VM de teste *UserVMUS*. Toda consulta de usuário originada dessa VM será direcionada para o *InternalWebSiteEndpoint*.    |

4. Repita as etapas 2 e 3 para adicionar outro ponto de extremidade chamado *ProdWebsiteEndpoint* para o endereço IP público *ProdWebsite-ip* que está associado à VM do servidor do IIS denominado *ProdWebsite*. Para **Configurações de roteamento de sub-rede**, adicione o endereço IP da VM de teste *UserVMEurope*. Toda consulta de usuário dessa VM de teste será encaminhada para o ponto de extremidade *myProdWebsiteEndpoint*.
5.  Quando a adição de ambos os pontos de extremidade estiver concluída, eles serão exibidos em **Perfil do Gerenciador de Tráfego** com seu status de monitoramento como **Online**.

 
## <a name="test-traffic-manager-profile"></a>Testar perfil de Gerenciador de Tráfego
Nesta seção, você testa como o Gerenciador de Tráfego encaminha o tráfego de usuário de uma determinada sub-rede para um ponto de extremidade específico. Para exibir o Gerenciador de Tráfego em ação, conclua as seguintes etapas:
1. Determine o nome DNS do seu perfil do Gerenciador de Tráfego.
2. Visualizar o Gerenciador de Tráfego em ação conforme a seguir:
    - Na VM de teste (*UserVMUS*) que está localizada na região do **Leste dos EUA**, em um navegador da Web, navegue até o nome DNS do seu perfil do Gerenciador de Tráfego.
    - Na VM de teste (*UserVMEurope*) que está localizada na região do **Europa Ocidental**, em um navegador da Web, navegue até o nome DNS do seu perfil do Gerenciador de Tráfego.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinar o nome DNS do Perfil do Gerenciador de Tráfego
Neste tutorial, para manter a simplicidade, você usará o nome DNS do perfil do Gerenciador de Tráfego do Microsoft Azure para visitar os sites da Web. 

Você pode determinar o nome DNS do seu perfil do Gerenciador de Tráfego conforme a seguir:

1.  Na barra de pesquisa do portal, procure o nome do **Perfil do Gerenciador de Tráfego** criado na seção anterior. Nos resultados exibidos, clique no perfil do gerenciador de tráfego.
1. Clique em **Visão Geral**.
2. O **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego criado recentemente. Nas implantações de produção, você configura um nome de domínio intuitivo para apontar para o nome de domínio do Gerenciador de Tráfego, usando um registro DNS CNAME.

### <a name="view-traffic-manager-in-action"></a>Ver o Gerenciador de Tráfego em ação
Nesta seção, você pode ver que o Gerenciador de Tráfego é a ação. 

1. Selecione **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, clique em *myUserVMUS*, que está localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Visão geral**, clique em **Conectar**e, em seguida, em **Conectar-se a máquina virtual**, selecione **Fazer download do arquivo RDP**. 
3. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM. 
4. Selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Se você receber o aviso, selecione **Sim** ou **Continuar**, para prosseguir com a conexão. 
1. Em um navegador da Web na VM *UserVMUS*, digite o nome DNS do seu perfil do Gerenciador de Tráfego para exibir o site. Como o endereço IP da VM *UserVMUS* está associado ao ponto de extremidade *myInternalWebsiteEndpoint*, o navegador da Web inicia o servidor do site de teste *InternalWebsite*.

2. Em seguida, conecte-se à VM *UserVMEurope* localizada na **Europa Ocidental** seguindo as etapas 1 a 5 e navegue até o nome de domínio de perfil do Gerenciador de Tráfego dessa VM. Como o endereço IP da VM *UserVMEurope* está associado ao ponto de extremidade *myProductionWebsiteEndpoint*, o navegador da Web inicia o servidor do site de teste *ProdWebsite*. 
  
## <a name="delete-the-traffic-manager-profile"></a>Excluir o perfil do Gerenciador de Tráfego
Quando não for mais necessário, exclua os grupos de recursos (**ResourceGroupTM1** e **ResourceGroupTM2**). Para fazer isso, selecione o grupo de recursos (**ResourceGroupTM1** ou **ResourceGroupTM2**) e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [método de roteamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de roteamento de prioridade](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de roteamento geográfico](traffic-manager-configure-geographic-routing-method.md).


