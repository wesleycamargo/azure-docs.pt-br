---
title: 'Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure'
description: Neste tutorial, você aprenderá a implantar e configurar o Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 1d7c880a56c79d516c3904c3f532eb7006f0b68c
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705830"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure

O controle do acesso à saída de rede é uma parte importante de um plano geral de segurança de rede. Por exemplo, talvez seja ideal limitar o acesso a sites da Web ou os endereços IP e as portas de saída que podem ser acessadas.

Uma maneira de controlar o acesso à saída de rede em uma sub-rede do Azure é com o Firewall do Azure. Com o Firewall do Azure, você pode configurar:

* Regras de aplicativo que definem FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados em uma sub-rede.
* Regras de rede que definem endereço de origem, protocolo, porta de destino e endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando o tráfego de rede para o firewall foi roteado como a sub-rede de gateway padrão.

Para este tutorial, você criará uma única VNET simplificada com três sub-redes para facilitar a implantação. Para implantações de produção, é recomendável usar um [modelo hub-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), em que o firewall está em sua própria VNET e os servidores de carga de trabalho estão em VNETs emparelhadas na mesma região com uma ou mais sub-redes.

- **AzureFirewallSubnet**: o firewall está nesta sub-rede.
- **Workload-SN**: o servidor de carga de trabalho está nessa sub-rede. O tráfego de rede dessa sub-rede passa pelo firewall.
- **Jump-SN** -o servidor “jump” está nessa sub-rede. O servidor jump tem um endereço IP público a que você pode se conectar usando a Área de Trabalho Remota. A partir daí, você pode se conectar (usando outra Área de Trabalho Remota) ao servidor de carga de trabalho.

![Infraestrutura de rede do tutorial](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configurar um aplicativo para permitir o acesso a github.com
> * Configurar uma regra de rede para permitir o acesso a servidores DNS externos
> * Testar o firewall

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="set-up-the-network"></a>Configurar a rede

Primeiro, crie um grupo de recursos para conter os recursos necessários à implantação do firewall. Em seguida, crie uma VNET, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para o tutorial.

1. Entre no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).
2. Na página inicial do portal do Azure, clique em **Grupos de recursos** > **Adicionar**.
3. Em **Nome do grupo de recursos**, digite **Test-FW-RG**.
4. Em **Assinatura**, selecione sua assinatura.
5. Em **Local do grupo de recursos**, selecione um local. Todos os recursos criados depois disso devem estar no mesmo local.
6. Clique em **Criar**.

### <a name="create-a-vnet"></a>Criar uma VNET

Essa VNET conterá três sub-redes.

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Redes virtuais**.
3. Clique em **Adicionar**.
4. Em **Nome**, digite **Test-FW-VN**.
5. Em **Espaço de endereço**, digite **10.0.0.0/16**.
6. Em **Assinatura**, selecione sua assinatura.
7. Em **Grupo de recursos**, selecione **Usar existente** > **Test-FW-RG**.
8. Em **local**, selecione o mesmo local usado anteriormente.
9. Em **Sub-rede**, digite **AzureFirewallSubnet** em **Nome**. O firewall estará nessa sub-rede e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
10. Em **Intervalo de endereços**, digite **10.0.1.0/24**.
11. Use as outras configurações padrão e clique em **Criar**.

> [!NOTE]
> O tamanho mínimo da sub-rede AzureFirewallSubnet é /25.

### <a name="create-additional-subnets"></a>Criar sub-redes adicionais

Em seguida, crie sub-redes para o servidor jump e uma sub-rede para os servidores de carga de trabalho.

1. Na página inicial do portal do Azure, clique em **Grupos de recursos** > **Test-FW-RG**.
2. Clique na rede virtual **Test-FW-VN**.
3. Clique em **Sub-redes** > **+ +Sub-rede**.
4. Em **Nome**, digite **Workload-SN**.
5. Em **Intervalo de endereços**, digite **10.0.2.0/24**.
6. Clique em **OK**.

Crie outra sub-rede denominada **Jump-SN**, intervalo de endereços **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie as máquinas virtuais de jump e carga de trabalho e coloque-as nas sub-redes apropriadas.

1. No portal do Azure, clique em **Criar um recurso**.
2. Clique em **Computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista em destaque.
3. Insira esses valores para a máquina virtual:

    - *Test-FW-RG* para o grupo de recursos.
    - *Srv-Jump* - para o nome da máquina virtual.
    - *azureuser* – para o nome de usuário do administrador.
    - *Azure123456!* para a senha.

4. Em **Regras de porta de entrada**, para **Portas de entrada públicas**, clique em **Permitir portas selecionadas**.
5. Em **Selecionar portas de entrada**, selecione **RDP (3389)**.

6. Aceite os outros padrões e clique em **Próximo: Discos**.
7. Aceite os padrões de disco e clique em **Próximo: Rede**.
8. Verifique se **Test-FW-VN** está selecionado para a rede virtual e se a sub-rede é **Jump-SN**.
9. Para **IP Público**, clique em **Criar novo**.
10. Digite **Srv-Jump-PIP** para o nome do endereço IP público e clique em **OK**.
11. Aceite os outros padrões e clique em **Próximo: Gerenciamento**.
12. Clique em **Desligar** para desabilitar o diagnóstico de inicialização. Aceite os outros padrões e clique em **Revisar + criar**.
13. Revise as configurações na página de resumo e clique em **Criar**.

Repita esse processo para criar outra máquina virtual denominada **Srv-Work**.

Use as informações na tabela a seguir para definir a máquina virtual Srv-Work. O restante da configuração é o mesmo da máquina virtual Srv-Jump.

|Configuração  |Valor  |
|---------|---------|
|Sub-rede|Workload-SN|
|IP público|Nenhum|
|Porta de entrada públicas|Nenhum|

## <a name="deploy-the-firewall"></a>Implantar o firewall

Implante o firewall na VNET.

1. Na página inicial do portal, clique em **Criar um recurso**.
2. Clique em **Rede**e, após **Em destaque**, clique em **Ver todos**.
3. Clique em **Firewall** > **Criar**. 
4. Na página **Criar um Firewall**, use a tabela abaixo para configurar o firewall:
   
   |Configuração  |Valor  |
   |---------|---------|
   |NOME     |Test-FW01|
   |Assinatura     |\<sua assinatura\>|
   |Grupo de recursos     |**Usar existente**: Test-FW-RG |
   |Local padrão     |Selecionar o mesmo local usado anteriormente|
   |Escolher uma rede virtual     |**Usar existente**: Test-FW-VN|
   |Endereço IP público     |**Criar novo**. O endereço IP público deve ser do tipo SKU Standard.|

2. Clique em **Revisar + Criar**.
3. Reveja o resumo e clique em **Criar** para criar o firewall.

   Isso levará alguns minutos para ser implantado.
4. Após a implantação ser concluída, vá para o grupo de recursos **Test-FW-RG** e clique no firewall **Test-FW01**.
6. Anote o endereço IP privado. Você o usará mais tarde quando criar a rota padrão.

## <a name="create-a-default-route"></a>Criar uma rota padrão

Para a sub-rede **Workload-SN**, configure a rota de saída padrão para atravessar o firewall.

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Tabelas de rotas**.
3. Clique em **Adicionar**.
4. Em **Nome**, digite **Firewall-route**.
5. Em **Assinatura**, selecione sua assinatura.
6. Em **Grupo de recursos**, selecione **Usar existente** e selecione **Test-FW-RG**.
7. Em **local**, selecione o mesmo local usado anteriormente.
8. Clique em **Criar**.
9. Clique em **Atualizar**e clique na tabela de rotas **Firewall-route**.
10. Clique em **Sub-redes** > **Associar**.
11. Clique em **Rede virtual** > **Test-FW-VN**.
12. Em **Sub-rede**, clique em **Workload-SN**. Não deixe de selecionar apenas a sub-rede **Workload-SN** para essa rota, caso contrário, o firewall não funcionará corretamente.

13. Clique em **OK**.
14. Clique em **Rotas** > **Adicionar**.
15. Em **Nome da rota**, digite **FW-DG**.
16. Em **Prefixo de endereço**, digite **0.0.0.0/0**.
17. Em **Tipo do próximo salto**, selecione **Solução de virtualização** .

    O Firewall do Azure é, na verdade, um serviço gerenciado, mas a solução de virtualização funciona nessa situação.
18. Em **endereço do próximo salto**, digite o endereço IP privado do firewall anotado anteriormente.
19. Clique em **OK**.

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicativo

Essa é a regra de aplicativo que permite o acesso de saída para github.com.

1. Abra **Test-FW-RG**e clique no firewall **Test-FW01**.
2. Na página **Test-FW01**, em **Configurações**, clique em **Regras**.
3. Clique na guia **Coleção de regras de aplicativo**.
4. Clique em **Adicionar coleção de regras de aplicativo**.
5. Em **Nome**, digite **App-Coll01**.
6. Digite **200** em **Prioridade**.
7. Em **Ação**, selecione **Permitir**.
8. Em **Regras**, **FQDNs de Destino**, para **Nome**, digite **AllowGH**.
9. Em **Endereços de Origem**, digite **10.0.2.0/24**.
10. Em **Protocol:port**, digite **http, https**.
11. Em **FQDNS de destino**, digite **github.com**
12. Clique em **Adicionar**.

O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. Para saber mais, veja [FQDNs de infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

Essa é a regra de rede que permite o acesso de saída para dois endereços IP na porta 53 (DNS).

1. Clique na guia **Coleção de regras de rede**.
1. Clique em **Adicionar coleção de regras de rede**.
2. Em **Nome**, digite **Net-Coll01**.
3. Digite **200** em **Prioridade**.
4. Em **Ação**, selecione **Permitir**.

6. Em **Regras**, digite **AllowDNS** como **Nome**.
8. Em **Protocolo**, selecione **UDP**.
9. Em **Endereços de Origem**, digite **10.0.2.0/24**.
10. Em Endereço de destino, digite **209.244.0.3,209.244.0.4**
11. Em **Portas de Destino**, digite **53**.
12. Clique em **Adicionar**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Altere os endereços DNS primário e secundário para o adaptador de rede **Srv-Wprk**

Para fins de teste neste tutorial, você vai configurar os endereços DNS primários e secundários. Isso não é um requisito geral do Firewall do Azure.

1. No portal do Azure, abra o grupo de recursos **Test-FW-RG**.
2. Clique no adaptador de rede da máquina virtual **Srv-Work**.
3. Em **Configurações**, clique em **Servidores DNS**.
4. Em **Servidores DNS**, clique em **Personalizado**.
5. Digite **209.244.0.3** na caixa de texto **Adicionar servidor DNS** e **209.244.0.4** na caixa de texto seguinte.
6. Clique em **Salvar**. 
7. Reinicie a máquina virtual **Srv-Work**.

## <a name="test-the-firewall"></a>Testar o firewall

Agora teste o firewall para confirmar se ele funciona conforme o esperado.

1. No portal do Azure, reveja as configurações de rede da máquina virtual **Srv-Work** e anote o endereço IP privado.
2. Conectar-se a uma área de trabalho remota à máquina virtual **Srv-Jump** e de lá abra uma conexão de área de trabalho remota para o endereço IP privado **Srv-Work**.

5. Abra o Internet Explorer e navegue até http://github.com.
6. Clique em **OK** > **Fechar** nos alertas de segurança.

   Você deve ver a página inicial do GitHub.

7. Navegue até http://www.msn.com.

   Você deve ser bloqueado pelo firewall.

Agora que você verificou se as regras de firewall estão funcionando:

- Você pode navegar para o FQDN permitido, mas não para os outros.
- É possível resolver nomes DNS usando o servidor DNS externo configurado.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou, se não forem mais necessários, exclua o grupo de recursos **Test-FW-RG** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
