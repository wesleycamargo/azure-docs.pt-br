<properties 
   pageTitle="Criar uma rede virtual" 
   description="Percorra as etapas para criar facilmente uma rede virtual básica." 
   services="virtual-network" 
   documentationCenter="" 
   authors="cherylmc" 
   manager="adinah" 
   editor="tysonn"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/20/2015"
   ms.author="cherylmc"/>

# Criar uma rede virtual 



Quando você cria uma rede virtual, seus serviços e VMs na VNet podem se comunicar com segurança entre si sem ter que utilizar a Internet. Criar uma rede virtual somente em nuvem exclusiva é um processo relativamente rápido e fácil. Como uma rede virtual somente em nuvem não é destinada a conectividade entre locais, você não precisará adquirir e configurar um dispositivo VPN ou certificados de autenticação. 

Depois de criar sua rede virtual, você pode adicionar novas VMs e instâncias de PaaS a ela. Observe que se você usar o Portal de Gerenciamento para criar suas VMs, certifique-se de selecionar **Da Galeria** para que você possa especificar a rede virtual. Isso é importante porque você não poderá voltar e colocar uma VM em uma rede virtual depois de criar a máquina virtual.

[Azure Note] **Use este procedimento para criar uma rede virtual somente em nuvem exclusiva.** Devido à maior complexidade associada com a criação de uma configuração entre locais, não use este procedimento para criar uma rede virtual que posteriormente será conectada à rede local. Se você quiser criar uma conexão segura entre locais entre o Azure e sua rede local, consulte [Sobre conectividade segura entre locais](https://msdn.microsoft.com/library/azure/dn133798.aspx).

## <a name="CreateyourVNet"></a>Criar sua rede virtual

1. Faça logon no **Portal de Gerenciamento**.
2. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.
3. Na página **Detalhes da Rede Virtual**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita. Para obter mais informações sobre as configurações na página de detalhes, consulte a seção **Detalhes da Rede Virtual** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	-  **Nome:** Nome da sua rede virtual. Você usará esse nome de rede virtual quando implantar suas VMs e serviços, portanto, é melhor não fazer um nome complicado.

	-  **Local:** Na lista suspensa, selecione a região desejada. Sua rede virtual será criada no datacenter do Azure localizado na região especificada.



4. Na página **Servidores DNS e Conectividade VPN**, não faça nenhuma alteração. Simplesmente avance para a próxima página clicando na seta. Por padrão, o Azure fornece resolução de nomes básica para sua rede virtual. É possível que seus requisitos de resolução de nome sejam mais complexos do que pode ser tratado pela resolução de nomes básica do Azure. Nesse caso, posteriormente convém adicionar uma máquina virtual executando DNS à sua rede virtual. Para obter mais informações sobre DNS e resolução de nomes do Azure, consulte [Resolução de Nome](https://msdn.microsoft.com/library/azure/jj156088.aspx). 
5. A página **Espaços de Endereço de Rede Virtual** é onde você insere o espaço de endereço que você deseja usar para este VNet. A menos que você precise de um determinado intervalo de endereço IP interno para suas VMs ou deseja criar uma subrede específica para VMs que receberão um DIP estático, você não precisa fazer nenhuma alteração nessa página. Se você quiser criar várias subredes, é possível fazer isso nesta página clicando em **Adicionar subrede**. Para obter mais informações sobre as configurações na página de detalhes, consulte a seção **Detalhes da Rede Virtual** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](https://msdn.microsoft.com/library/azure/jj156074.aspx).

	-  Para obter mais informações sobre as configurações na página de detalhes, consulte a seção **Detalhes da Rede Virtual** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	-  Como você não conectará essa rede virtual privada à sua rede local usando uma configuração de VPN entre locais, não precisará coordenar essas configurações com seus intervalos de endereços IP de rede existentes no local. Se você acha que talvez queira criar uma configuração entre locais posteriormente, precisará coordenar os espaços de endereços agora com os intervalos que já existem no seu site local para evitar problemas de roteamento. Alterar os intervalos posteriormente pode ser um pouco complicado e geralmente resulta em precisar reimplantar o


6. Clique na marca de seleção no canto inferior direito da página Espaços de Endereço da Rede Virtual e sua rede virtual começará a ser criada. Quando sua rede virtual tiver sido criada, você verá a denominação Criada listada sob Status na página Rede no Portal de Gerenciamento.
7. Depois que sua rede virtual tiver sido criada, você poderá implantar a sua VNet. Por exemplo, se você quiser implantar uma VM em seu VNet, consulte [Adicionar uma Máquina Virtual a uma Rede Virtual](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/). Selecione **Da Galeria** durante a criação de uma nova VM para ter a opção de selecionar sua rede virtual. Observe que se você tiver instâncias existentes de VMs e PaaS implantadas, elas não podem simplesmente ser movidas para sua nova VNet. Isso ocorre porque as definições de configuração de rede são configuradas para elas durante a implantação. Você precisará implantá-as novamente para a nova VNet.



## Próximas etapas
-  [Visão geral técnica de rede virtual](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

 
-  [Adicionar uma máquina virtual a uma rede virtual](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [Perguntas frequentes sobre rede virtual](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Configurar uma Rede Virtual usando arquivos de configuração de rede](http://azure.microsoft.com/documentation/articles/virtual-networks-using-network-configuration-file/)

-  [Resolução de nomes do Azure](http://go.microsoft.com/fwlink/?LinkId=248097)
 



<!--HONumber=47-->
