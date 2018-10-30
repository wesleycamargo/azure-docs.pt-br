---
title: Tutorial – Rotear tráfego para pontos de extremidade ponderados usando o Gerenciador de Tráfego do Azure | Microsoft Docs
description: Este artigo de tutorial descreve como rotear o tráfego para pontos de extremidade ponderados usando o Gerenciador de Tráfego.
services: traffic-manager
author: KumudD
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: kumud
ms.openlocfilehash: 171a9cfe5b5b63efde31c37cd49ef548f1e4ebcc
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649427"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-using-traffic-manager"></a>Tutorial: Controlar o roteamento de tráfego com pontos de extremidade ponderados usando o Gerenciador de Tráfego 

Este tutorial descreve como usar o Gerenciador de Tráfego para controlar o roteamento de tráfego do usuário entre pontos de extremidade usando o método de roteamento ponderado. Nesse método de roteamento, você atribui pesos para cada ponto de extremidade na configuração de perfil do Gerenciador de Tráfego e o tráfego do usuário é roteado com base no peso atribuído a cada ponto de extremidade. Cada peso é um inteiro de 1 a 1000. Quanto maior o valor do peso atribuído a um ponto de extremidade, mais alta é sua prioridade.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie duas VMs em execução de um site da Web básica no IIS
> * Crie duas VMs para exibir o Gerenciador de Tráfego em ação de teste
> * Configurar nome DNS para as VMs que executam IIS
> * Criar um perfil do Gerenciador de Tráfego
> * Criar pontos de extremidade no perfil do Gerenciador de Tráfego
> * Ver o Gerenciador de Tráfego em ação

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para ver o Gerenciador de Tráfego em ação, este tutorial exige que você implante o seguinte:
- duas instâncias de sites básicos em execução em diferentes regiões do Azure - **Leste dos EUA** e **Europa Ocidental**.
- duas VMs de teste para testar o Gerenciador de Tráfego do Microsoft Azure - uma VM no **Leste dos EUA** e a segunda VM na **Europa Ocidental**. As VMs de teste são usadas para ilustrar como o Gerenciador de Tráfego roteia o tráfego de usuário para um site que tem o peso mais alto atribuído ao seu ponto de extremidade.

### <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no Portal do Azure em https://portal.azure.com.

### <a name="create-websites"></a>Criar sites

Nesta seção, você criará duas instâncias de site que fornecem os dois pontos de extremidade para o perfil do Gerenciador de Tráfego em duas regiões do Azure. Para criar os dois sites da Web, conclua as seguintes etapas:
1. Criar duas VMs para executar um site básico - uma no **Leste dos EUA**e o outro na **Europa Ocidental**.
2. Instale o servidor IIS em cada VM e atualize a página de site padrão que descreve o nome da VM que um usuário está conectado ao visitar o site.

#### <a name="create-vms-for-running-websites"></a>Criar VMs para a execução de sites
Nesta seção, você criará duas VMs *myIISVMEastUS* e *myIISVMWEurope* nas regiões do Azure do **Leste dos EUA** e **Europa Ocidental**.

1. Na parte superior, canto esquerdo do portal do Microsoft Azure, selecione **Criar um recurso** > **Computar** > **Windows Server 2016 VM**.
2. Insira, ou selecione, as informações a seguir para **Princípios básicos**, aceite os padrões para as configurações restantes e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |NOME|myIISVMEastUS|
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
    |Nome da VM | myIISVMWEurope|
    |Rede virtual | Selecione **Rede Virtual**, em **Criar rede virtual**, para **Nome**, insira *myVNet2*, para a sub-rede, insira *mySubnet*.|
    |||
8. As VMs podem levar alguns minutos para serem criadas. Não continue com as etapas restantes até que ambas as VMs sejam criadas.

![Criar uma máquina virtual](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar o IIS e personalizar a página da Web padrão

Nesta seção, você deve instalar o servidor IIS em duas VMs - *myIISVMEastUS*  & *myIISVMWEurope*e, em seguida, atualizar a página padrão do site. A página do site personalizada mostra o nome da VM que você está se conectando ao visitar o site em um navegador da web.

1. Clique em **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, clique em *myIISVMEastUS*, que está localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Visão geral**, clique em **Conectar**e, em seguida, em **Conectar-se a máquina virtual**, selecione **Fazer download do arquivo RDP**. 
3. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM. 
4. Selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Se você receber o aviso, selecione **Sim** ou **Continuar**, para prosseguir com a conexão.
6. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Gerenciador do Servidor**.
7. Inicie o Windows PowerShell na VM1 e usando os comandos a seguir para instalar o servidor do IIS e atualizar o arquivo htm padrão.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Instalar o IIS e personalizar a página da web](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Feche a conexão RDP com *myIISVMEastUS*.
9. Repita as etapas de 1 a 8 criando uma conexão de RDP com a VM *myIISVMWEurope* dentro do grupo de recursos *myResourceGroupTM2* para instalar o IIS e personalizar sua página da web padrão.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurar nome DNS para as VMs que executam IIS

O Gerenciador de Tráfego roteia o tráfego de usuário com base no nome DNS dos pontos de extremidade de serviço. Nesta seção, você configura os nomes DNS para os servidores IIS - *myIISVMEastUS* e *myIISVMWEurope*.

1. Clique em **Todos os Recursos** no menu esquerdo e, em seguida, na lista de recursos, selecione *myIISVMEastUS*, que está localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Visão Geral**, em **Nome DNS**, selecione **Configurar**.
3. Na página **Configuração**, em rótulo de nome DNS, adicione um nome exclusivo e, em seguida, selecione **Salvar**.
4. Repita as etapas de 1 a 3 para a VM denominada *myIISVMWEurope* que está localizada no grupo de recursos *myResourceGroupTM1*.

### <a name="create-a-test-vm"></a>Criar uma VM de teste

Nesta seção, você pode criar uma VM – *mVMEastUS*. Você usará essa VM para testar como o Gerenciador de Tráfego roteia o tráfego para o ponto de extremidade de site que tem o valor de peso mais alto.

1. Na parte superior, canto esquerdo do portal do Microsoft Azure, selecione **Criar um recurso** > **Computar** > **Windows Server 2016 VM**.
2. Insira, ou selecione, as informações a seguir para **Princípios básicos**, aceite os padrões para as configurações restantes e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |NOME|myVMEastUS|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupo de recursos| Selecione **Existente** e *myResourceGroupTM1*.|
    |||

4. Selecione um tamanho da VM em **Escolher um tamanho**.
5. Selecione os seguintes valores para **Configurações** e selecione **OK**:
    |Configuração|Valor|
    |---|---|
    |Rede virtual| Selecione **Rede Virtual**, em **Criar rede virtual**, para **Nome**, insira *myVNet3*, para a sub-rede, insira *mySubnet*.|
    |Grupo de Segurança de Rede|Selecione **Básico**, e na lista **Selecionar as portas de entrada públicas**, selecione **HTTP** e **RDP** |
    |Diagnóstico de inicialização|Selecione **Desabilitado**.|
    |||

6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implantação da VM.
8. A VM demora alguns minutos para criar. Não continue com as etapas restantes até que a VM seja criada.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego
Criar um perfil do Gerenciador de Tráfego com base no método de roteamento **Ponderado**.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Perfil do Gerenciador de Tráfego**  >  **Criar**.
2. No **perfil Criar Gerenciador de Tráfego**, insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e selecione **Criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | NOME                   | Esse nome deve ser exclusivo na zona trafficmanager.net e resulta no nome DNS, trafficmanager.net, que é usado para acessar o seu perfil do Gerenciador de Tráfego.                                   |
    | Método de roteamento          | Selecione o método de roteamento **Ponderado**.                                       |
    | Assinatura            | Selecione sua assinatura.                          |
    | Grupo de recursos          | Selecione **Existente** e *myResourceGroupTM1*. |
    |        |   |

    ![Criar um perfil do Gerenciador de Tráfego](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos de extremidade do Gerenciador de Tráfego

Adicione as duas VMs executando os servidores IIS, *myIISVMEastUS*  & *myIISVMWEurope*, para rotear o tráfego de usuário para eles.

1. Na barra de pesquisa do portal, pesquise o nome do Perfil do Gerenciador de Tráfego que você criou na seção anterior e selecione o perfil do gerenciador de tráfego nos resultados que são exibidos.
2. Em **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, clique em **Pontos de Extremidade** e clique em **Adicionar**.
3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **OK**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Tipo                    | Ponto de extremidade do Azure                                   |
    | NOME           | myEastUSEndpoint                                        |
    | Tipo de recurso de destino           | Endereço IP público                          |
    | Recurso de destino          | **Escolha um endereço IP Público** para mostrar a lista de recursos com endereços IP públicos na mesma assinatura. Em **Recursos**, selecione o endereço IP público denominado *myIISVMEastUS-ip*. Isso é o endereço IP público do servidor IIS VM no Leste dos EUA.|
    |  Peso      | Insira **100**.        |
    |        |           |

4. Repita as etapas 2 e 3 para adicionar outro ponto de extremidade chamado *myWestEuropeEndpoint* para o endereço IP público *ip myIISVMWEurope* que está associado à VM do servidor IIS denominado *myIISVMWEurope*  e em **Peso**, insira **25**. 
5.  Quando a adição de ambos os pontos de extremidade estiver concluída, eles serão exibidos em **Perfil do Gerenciador de Tráfego** com seu status de monitoramento como **Online**.

## <a name="test-traffic-manager-profile"></a>Testar perfil de Gerenciador de Tráfego
Para exibir o Gerenciador de Tráfego em ação, conclua as seguintes etapas:
1. Determine o nome DNS do seu perfil do Gerenciador de Tráfego.
2. Veja o Gerenciador de Tráfego em ação.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinar o nome DNS do Perfil do Gerenciador de Tráfego
Neste tutorial, para manter a simplicidade, você usará o nome DNS do perfil do Gerenciador de Tráfego do Microsoft Azure para visitar os sites da Web. 

Você pode determinar o nome DNS do seu perfil do Gerenciador de Tráfego conforme a seguir:

1.  Na barra de pesquisa do portal, procure o nome do **Perfil do Gerenciador de Tráfego** criado na seção anterior. Nos resultados exibidos, clique no perfil do gerenciador de tráfego.
1. Clique em **Visão Geral**.
2. O **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego criado recentemente. Nas implantações de produção, você configura um nome de domínio intuitivo para apontar para o nome de domínio do Gerenciador de Tráfego, usando um registro DNS CNAME.

   ![Nome DNS do Gerenciador de Tráfego](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Ver o Gerenciador de Tráfego em ação
Nesta seção, você pode ver que o Gerenciador de Tráfego é a ação. 

1. Selecione **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, clique em *myIISVMEastUS*, que está localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Visão geral**, clique em **Conectar**e, em seguida, em **Conectar-se a máquina virtual**, selecione **Fazer download do arquivo RDP**. 
3. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM. 
4. Selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Se você receber o aviso, selecione **Sim** ou **Continuar**, para prosseguir com a conexão. 
6. Em um navegador da Web na VM *myVMEastUS*, digite o nome do DNS do seu perfil do Gerenciador de Tráfego para visualizar seu site. Você será encaminhado para o site hospedado no servidor IIS *myIISVMEastUS*, pois ele recebe um peso mais alto de **100** em comparação com o servidor IIS *myIISVMWEurope* (com valor de peso de ponto de extremidade mais baixo, **25**).

   ![Testar perfil de Gerenciador de Tráfego](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>Excluir o perfil do Gerenciador de Tráfego
Quando não forem mais necessários, exclua os grupos de recursos que você criou neste tutorial. Para fazer isso, selecione o grupo de recursos (**ResourceGroupTM1** ou **ResourceGroupTM2**) e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Rotear o tráfego para pontos de extremidade específicos com base na localização geográfica do usuário](traffic-manager-configure-geographic-routing-method.md)


