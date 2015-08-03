<properties 
   pageTitle="Gerenciar servidores DNS usados por uma rede virtual (VNet)"
   description="Saiba como adicionar e remover servidores DNS em uma rede virtual (VNet)"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Gerenciar servidores DNS usados por uma rede virtual (VNet)

Você pode gerenciar a lista de servidores DNS usados em uma VNet no Portal de Gerenciamento ou no arquivo de configuração de rede. Você pode adicionar até 12 servidores DNS para cada VNet. Ao especificar servidores DNS, é importante verificar se os servidores DNS estão listados na ordem correta para seu ambiente. As listas de servidores DNS não funcionam em round robin. Elas são usadas na ordem em que foram especificadas. Se o primeiro servidor DNS na lista puder ser alcançado, o cliente usará esse servidor DNS independentemente de ele estar funcionando corretamente. Para alterar a ordem de servidor DNS para sua rede virtual, remova os servidores DNS da lista e adicione-as na ordem em que desejar.

>[AZURE.WARNING]Depois que a lista de DNS tiver sido atualizada, você deve reiniciar as máquinas virtuais localizadas na rede virtual para que obtenham as novas configurações de servidor DNS. As máquinas virtuais continuarão a usar a configuração atual até que sejam reiniciadas.

## Editar uma lista de servidores DNS para uma rede virtual usando o Portal de Gerenciamento

1. Faça logon no **Portal de Gerenciamento**.

1. No painel de navegação, clique em **Redes** e no nome da sua rede virtual na coluna **Nome**.

1. Clique em **Configurar**.

1. Em **Servidores DNS**, você pode configurar o seguinte:

	- **Para registrar (adicionar) um novo servidor DNS:** simplesmente digite o nome e o endereço IP nas caixas. Isso adicionará um servidor DNS à lista de servidores DNS da rede virtual e também registrará o servidor DNS no Azure.

	- **Para adicionar um servidor DNS que foi registrado anteriormente:** se você tiver registrado um servidor DNS no Azure, selecione-o na lista pré-populada.

	- **Para remover um servidor DNS de sua rede virtual:** clique no X ao lado do servidor que você deseja remover. Observe que isso remove o servidor somente dessa lista de rede virtual. O servidor DNS permanece registrado no Azure para que suas outras redes virtuais possam usá-lo. Para excluir um servidor DNS de sua assinatura, vá para a página **Redes -> Servidores DNS**.

	- **Para reordenar servidores DNS:** remova todos os servidores DNS listados e adicione-os de volta na ordem em que desejar. Lembre-se de que isso não é uma lista de DNS round robin.

	- **Para renomear um servidor DNS:** realce o servidor DNS na lista e digite o novo nome. Isso irá registrar um novo servidor DNS no Azure e adicioná-lo à lista de servidores DNS da sua rede virtual. O servidor DNS antigo e o seu endereço IP permanecerão registrados no Azure. Você poderá excluí-los na página **Servidores DNS** se não estiver usando-o em outras redes virtuais.

1. Clique em **Salvar** na parte inferior da página para salvar a nova configuração de servidores DNS.

1. Reinicie as máquinas virtuais localizadas na rede virtual para permitir que elas obtenham as novas configurações de DNS.

## Editar uma lista de servidores DNS usando um arquivo de configuração de rede

Para editar uma lista de servidores DNS usando um arquivo de configuração de rede, você primeiro deve exportar as configurações do Portal de Gerenciamento. Em seguida, deve editar o arquivo de configuração de rede e importá-lo novamente pelo Portal de Gerenciamento. Abaixo está uma lista de alto nível das etapas para concluir esse processo.

1. Exporte as configurações de rede virtual para um arquivo de configuração de rede. Para saber mais e obter mais etapas para exportar as configurações de rede, confira [Exportar configurações de rede virtual para um arquivo de configuração de rede](https://msdn.microsoft.com/library/azure/dn133804.aspx).

1. Especifique as informações do servidor DNS da sua rede virtual. Para saber mais sobre como especificar um servidor DNS, confira [Especificando um servidor DNS em um arquivo de configuração de rede virtual](https://msdn.microsoft.com/library/windowsazure/jj156098.aspx). Para saber mais sobre arquivos de configuração de rede, confira [Esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) e [Configurar uma rede Virtual usando um arquivo de configuração de rede](https://msdn.microsoft.com/library/azure/jj156097.aspx).

1. Importe o arquivo de configuração de rede. Para saber mais e obter mais etapas para importar o arquivo de configuração de rede, confira [Importar um arquivo de configuração de rede](https://msdn.microsoft.com/library/azure/jj156213.aspx).

1. Reinicie as máquinas virtuais localizadas na rede virtual para permitir que elas obtenham as novas configurações de DNS.

## Próximas etapas

[Como gerenciar as propriedades da rede virtual (VNet)](../virtual-networks-settings)

[Como usar endereços IP públicos em uma rede virtual](../virtual-networks-public-ip-within-vnet)

[Como excluir uma rede virtual (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=July15_HO4-->