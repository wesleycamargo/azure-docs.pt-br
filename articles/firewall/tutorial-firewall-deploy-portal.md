---
title: Implantar e configurar o Firewall do Azure usando o portal do Azure
description: Neste tutorial, você aprenderá a implantar e configurar o Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: tutorial
ms.date: 7/11/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 84696b4135570168f8093b15f9a2deb4790eeebe
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480876"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure

O Firewall do Azure tem dois tipos de regra para controlar o acesso de saída:

- **Regras de aplicativo**

   Permite que você configure os FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados de uma sub-rede. Por exemplo, você pode permitir acesso ao *github.com* de sua sub-rede.
- **Regras de rede**

   Permite que você configure regras que contém o endereço de origem, o protocolo, a porta de destino e o endereço de destino. Por exemplo, você poderia criar uma regra para permitir o tráfego para a porta 53 (DNS) ao endereço IP do servidor DNS de sua sub-rede.

O tráfego de rede está sujeito às regras de firewall configuradas quando o tráfego de rede para o firewall foi roteado como a sub-rede de gateway padrão.

As regras de rede e aplicativo são armazenadas em *coleções de regras*. Uma coleção de regras é uma lista de regras que compartilham a mesma ação e as mesmas prioridades.  Uma coleção de regras de rede é uma lista de regras de rede e uma coleção de regras de aplicativo é uma lista de regras de aplicativo.

As coleções de regras de rede são sempre processadas antes de coleções de regras de aplicativo. Todas as regras são finais e, portanto, se uma correspondência for encontrada em uma coleção de regra de rede, as coleções de regras de aplicativo subsequentes para a sessão não serão processadas.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configurar regras de aplicativo
> * Configurar regras de rede
> * Testar o firewall



Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Os exemplos nos artigos do Firewall do Azure supõem que você já habilitou a visualização pública do Firewall do Azure. Para obter mais informações, consulte [Habilitar a versão prévia do Firewall do Azure](public-preview.md).

Para este tutorial, você pode criar uma única VNET com três sub-redes:
- **FW-SN**: o firewall está nessa sub-rede.
- **Workload-SN**: o servidor de carga de trabalho está nessa sub-rede. O tráfego de rede dessa sub-rede passa pelo firewall.
- **Jump-SN** -o servidor “jump” está nessa sub-rede. O servidor jump tem um endereço IP público a que você pode se conectar usando a Área de Trabalho Remota. A partir daí, você pode se conectar (usando outra Área de Trabalho Remota) ao servidor de carga de trabalho.

![Infraestrutura de rede do tutorial](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Este tutorial usa uma configuração de rede simplificada para facilitar a implantação. Para implantações de produção, é recomendável usar um [modelo hub-spoke](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), em que o firewall está em sua própria VNET e os servidores de carga de trabalho estão em VNETs emparelhadas na mesma região com uma ou mais sub-redes.



## <a name="set-up-the-network-environment"></a>Configurar o ambiente de rede
Primeiro, crie um grupo de recursos para conter os recursos necessários à implantação do firewall. Em seguida, crie uma VNET, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Entre no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).
1. Na página inicial do portal do Azure, clique em **Grupos de recursos** e clique em **Adicionar**.
2. Em **Nome do grupo de recursos**, digite **Test-FW-RG**.
3. Em **Assinatura**, selecione sua assinatura.
4. Em **Local do grupo de recursos**, selecione um local. Todos os recursos criados depois disso devem estar no mesmo local.
5. Clique em **Criar**.


### <a name="create-a-vnet"></a>Criar uma VNET
1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Redes virtuais**.
3. Clique em **Adicionar**.
4. Em **Nome**, digite **Test-FW-VN**.
5. Em **Espaço de endereço**, digite **10.0.0.0/16**.
7. Em **Assinatura**, selecione sua assinatura.
8. Em **Grupo de recursos**, selecione **Usar existente** e selecione **Test-FW-RG**.
9. Em **local**, selecione o mesmo local usado anteriormente.
10. Em **Sub-rede**, digite **AzureFirewallSubnet** em **Nome**.

    O firewall estará nessa sub-rede e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
11. Em **Intervalo de endereços**, digite **10.0.1.0/24**.
12. Use as outras configurações padrão e clique em **Criar**.

> [!NOTE]
> O tamanho mínimo da sub-rede AzureFirewallSubnet é /25.

### <a name="create-additional-subnets"></a>Criar sub-redes adicionais

Em seguida, crie sub-redes para o servidor jump e uma sub-rede para os servidores de carga de trabalho.

1. Na página inicial do portal do Azure, clique em **Grupos de recursos** e clique em **Test-FW-RG**.
2. Clique na rede virtual **Test-FW-VN**.
3. Clique em **Sub-redes** e em **+Sub-rede**.
4. Em **Nome**, digite **Workload-SN**.
5. Em **Intervalo de endereços**, digite **10.0.2.0/24**.
6. Clique em **OK**.

Crie outra sub-rede denominada **Jump-SN**, intervalo de endereços **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie as máquinas virtuais de jump e carga de trabalho e coloque-as nas sub-redes apropriadas.

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Computação**, clique em **Máquinas virtuais**.
3. Clique em **Adicionar**, clique em **Windows Server**, clique em **Windows Server 2016 Datacenter**e clique em **Criar**.

**Noções básicas**

1. Em **Nome**, digite **Srv-Jump**.
5. Digite um nome de usuário e uma senha.
6. Em **Assinatura**, selecione sua assinatura.
7. Em **Grupo de recursos**, clique em **Usar existente** e selecione **Test-FW-RG**.
8. Em **local**, selecione o mesmo local usado anteriormente.
9. Clique em **OK**.

**Tamanho**

1. Escolha um tamanho apropriado para uma máquina virtual de teste que executa o Windows Server. Por exemplo, **B2ms** (8 GB de RAM, 16 GB de armazenamento).
2. Clique em **Selecionar**.

**Configurações**

1. Em **Rede**, selecione **Test-FW-VN** como **Rede virtual**.
2. Em **Sub-rede**, selecione **Jump-SN**.
3. Em **Selecionar portas de entrada públicas**, selecione **RDP (3389)**. 

    Você pode limitar o acesso ao seu endereço IP público, mas precisa abrir a porta 3389 para poder conectar uma área de trabalho remota para o servidor jump. 
2. Deixe as outras configurações padrão e clique em **OK**.

**Resumo**

Revise o resumo e clique em **Criar**. Isso levará alguns minutos para ser concluído.

Repita esse processo para criar outra máquina virtual denominada **Srv-Work**.

Use as informações na tabela a seguir para definir as **Configurações** da máquina virtual Srv-Work. O restante da configuração é o mesmo da máquina virtual Srv-Jump.


|Configuração  |Valor  |
|---------|---------|
|Sub-rede|Workload-SN|
|Endereço IP público|Nenhum|
|Selecionar portas de entrada públicas|Nenhuma porta de entrada pública|


## <a name="deploy-the-firewall"></a>Implantar o firewall

1. Na página inicial do portal, clique em **Criar um recurso**.
2. Clique em **Rede**e, após **Em destaque**, clique em **Ver todos**.
3. Clique em **Firewall**e clique em **Criar**. 
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
10. Clique em **Sub-redes** e em **Associar**.
11. Clique em **Rede virtual**e selecione **Test-FW-VN**.
12. Em **Sub-rede**, clique em **Workload-SN**.
13. Clique em **OK**.
14. Clique em **Rotas** e em **Adicionar**.
15. Em **Nome da rota**, digite **FW-DG**.
16. Em **Prefixo de endereço**, digite **0.0.0.0/0**.
17. Em **Tipo do próximo salto**, selecione **Solução de virtualização** .

    O Firewall do Azure é, na verdade, um serviço gerenciado, mas a solução de virtualização funciona nessa situação.
1. Em **endereço do próximo salto**, digite o endereço IP privado do firewall anotado anteriormente.
2. Clique em **OK**.


## <a name="configure-application-rules"></a>Configurar regras de aplicativo


1. Abra **Test-FW-RG**e clique no firewall **Test-FW01**.
1. Na página **Test-FW01**, em **Configurações**, clique em **Regras**.
2. Clique em **Adicionar coleção de regras de aplicativo**.
3. Em **Nome**, digite **App-Coll01**.
1. Digite **200** em **Prioridade**.
2. Em **Ação**, selecione **Permitir**.

6. Em **Regras**, digite **AllowGH** como **Nome**.
7. Em **Endereços de Origem**, digite **10.0.2.0/24**.
8. Em **Protocol:port**, digite **http, https**. 
9. Em **FQDNS de destino**, digite **github.com**
10. Clique em **Adicionar**.

> [!NOTE]
> O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. Os FQDNs de infraestrutura permitidos incluem:
>- Acesso de computação ao PIR (Repositório de Imagem da Plataforma).
>- Acesso ao armazenamento de status de discos gerenciados.
>- Diagnóstico do Windows
>
> Você pode substituir essa coleção de regras de infraestrutura interna criando uma coleção de regras de aplicativo *negar tudo* que é processada por último. Ela sempre será processada antes da coleção de regras de infraestrutura. Tudo o que não estiver na coleção de regras de infraestrutura será negado por padrão.

## <a name="configure-network-rules"></a>Configurar regras de rede

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

1. No portal do Azure, reveja as configurações de rede da máquina virtual **Srv-Work** e anote o endereço IP privado.
2. Conectar-se a uma área de trabalho remota à máquina virtual **Srv-Jump** e de lá abra uma conexão de área de trabalho remota para o endereço IP privado **Srv-Work**.

5. Abra o Internet Explorer e navegue até http://github.com.
6. Clique em **OK** e em **Fechar** nos alertas de segurança.

   Você deve ver a página inicial do GitHub.

7. Navegue até http://www.msn.com.

   Você deve ser bloqueado pelo firewall.

Agora que você verificou se as regras de firewall estão funcionando:

- Você pode navegar para o FQDN permitido, mas não para os outros.
- É possível resolver nomes DNS usando o servidor DNS externo configurado.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou, se não forem mais necessários, exclua o grupo de recursos **Test-FW-RG** para excluir todos os recursos relacionados ao firewall.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um firewall
> * Criar uma rota padrão
> * Configurar regras de firewall de rede e aplicativo
> * Testar o firewall

Em seguida,você pode monitorar os logs do Firewall do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
