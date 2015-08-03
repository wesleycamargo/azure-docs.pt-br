<properties 
	pageTitle="Configurar uma rede virtual usando um arquivo de configuração de rede" 
	description="Instruções para exportar e importar um arquivo de configuração de rede no Portal de gerenciamento para criar ou modificar as redes virtuais." 
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
	ms.date="07/09/2015"
	ms.author="cherylmc"/>

# Configurar uma rede virtual usando um arquivo de configuração de rede

## Visão geral

Para configurar sua rede virtual, você pode usar o Assistente do Portal de gerenciamento, ou pode criar e importar um arquivo de configuração de rede. O Azure usa o arquivo de configuração de rede para definir as configurações de rede virtual.

Talvez você prefira usar o Assistente do Portal de gerenciamento para criar inicialmente a configuração da rede virtual e faça as alterações subsequentes por edições diretas no arquivo de configuração. Por exemplo, se você estiver configurando várias redes virtuais para assinaturas separadas, convém primeiro criar um arquivo de configuração de rede usando o Assistente do Portal de gerenciamento. Você pode exportar o arquivo para usar como modelo, editá-lo para especificar configurações diferentes e, em seguida, importá-lo novamente no Portal de gerenciamento. Isso pode ser uma maneira eficiente de criar várias redes virtuais quando você tiver mais de uma assinatura.

Ou, se você quiser fazer alterações em seus parâmetros de configuração de rede antes de implantar serviços de nuvem ou máquinas virtuais na rede, você pode exportar o arquivo, editá-lo e importá-lo novamente no Azure. Você também pode usar um arquivo de configuração de rede para fazer backup das configurações de rede se quiser recriar sua rede virtual.

## Criando e modificando um arquivo de configuração de rede 
A maneira mais fácil de criar um arquivo de configuração de rede é exportar as configurações de rede de uma configuração de rede virtual existente e, então modificar o arquivo para conter as configurações que você deseja para configurar suas redes virtuais. Você também pode obter um arquivo de exemplo e modificá-lo.

Para editar o arquivo de configuração de rede, basta abrir o arquivo, fazer as alterações apropriadas e salvá-los. Você pode usar qualquer editor *xml* para fazer alterações no arquivo de configuração de rede.

Você deve seguir atentamente as diretrizes para configurações de esquema de arquivo de configuração de rede. Quando você cria o arquivo de configuração de rede, as configurações no arquivo substituirão as configurações que você fez nessa assinatura no Azure. Se você fizer alterações nos valores do arquivo que não são compatíveis com as diretrizes das configurações, sua rede virtual poderá não estar configurada da maneira desejada e, em alguns casos, o Azure não permitirá que você importe o arquivo. Para obter informações sobre as configurações específicas contidas em um arquivo de configuração de rede, consulte [Esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

O Azure considera uma sub-rede com algo implantado como "em uso". Quando uma sub-rede estiver em uso, ela não poderá ser modificada. Antes de modificar, mova qualquer coisa que você tenha implantado na sub-rede para uma sub-rede diferente que não está sendo modificada. Consulte [Mover uma VM ou instância de função para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md).



## Exporte e importe as configurações de rede virtual usando o Portal de gerenciamento  
Você pode importar e exportar definições de configuração de rede contidas no arquivo de configuração de rede usando o PowerShell ou o Portal de Gerenciamento. As instruções a seguir ajudarão você a exportar e importar usando o Portal de gerenciamento.

### Para exportar as configurações de rede
Quando você exporta, todas as configurações para as redes virtuais na sua assinatura serão gravadas em um arquivo .xml.

1. Faça logon no **Portal de Gerenciamento**.
2. No Portal de Gerenciamento, na parte inferior da página **Redes**, clique em **Exportar**. 
3. Na janela **Exportar a configuração de rede**, verifique se você selecionou a assinatura para a qual deseja exportar as configurações de rede. Em seguida, clique na marca de seleção no canto inferior direito. 
4. Quando for solicitado, salve o arquivo *NetworkConfig.xml* no local de sua escolha.


### Para importar as configurações de rede

1. No **Portal de Gerenciamento**, no painel de navegação no canto inferior esquerdo, clique em **Novo**.
2. Clique em **Serviços de Rede** -> **Rede Virtual** -> **Importar configuração**.
3. Na página **Importar o arquivo de configuração de rede**, navegue até o arquivo de configuração de rede e, em seguida, clique na seta **Avançar**.
4. Na página **Criar sua rede**, você verá as informações na tela mostrando quais seções da sua configuração de rede serão alteradas ou criadas. Se as alterações estiverem corretas para você, clique na marca de seleção para continuar para atualizar ou criar sua rede virtual. 


## Próximas etapas
Para obter mais informações sobre configurações de rede virtual, consulte:

-  [Esquema de configuração de rede - configurações opcionais para serviços de nuvem](https://msdn.microsoft.com/library/azure/jj156091.aspx)
-  [Sobre as configurações de rede virtual no Portal de Gerenciamento](https://msdn.microsoft.com/library/azure/jj156074.aspx)
-  [Perguntas frequentes sobre rede virtual](https://msdn.microsoft.com/library/azure/dn133803.aspx)





 

<!---HONumber=July15_HO4-->