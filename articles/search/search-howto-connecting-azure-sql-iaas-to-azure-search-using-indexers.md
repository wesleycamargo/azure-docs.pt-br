<properties 
	pageTitle="Configurar uma conexão de um indexador do Azure Search ao SQL Server em uma máquina virtual do Azure | Microsoft Azure | Indexadores" 
	description="Habilite conexões criptografadas e configure o firewall para permitir conexões com o SQL Server em uma VM (máquina virtual) do Azure de um indexador no Azure Search." 
	services="search" 
	documentationCenter="" 
	authors="jack4it" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/06/2016" 
	ms.author="jackma"/>

# Configurar uma conexão de um indexador do Azure Search ao SQL Server em uma VM do Azure

Conforme observado em [Conectando o Banco de Dados SQL do Azure ao Azure Search usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), é possível criar indexadores no **SQL Server em VMs do Azure** (ou **VMs do SQL Azure**) usando o Azure Search, mas há dois pré-requisitos relacionados à segurança que precisam ser verificados primeiro.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Habilitar conexões criptografadas

O Azure Search exige uma conexão segura para ler dados do seu banco de dados. Isso significa que você precisa habilitar conexões criptografadas na sua VM do SQL Azure configurando um certificado SSL.

As etapas para habilitar conexões criptografadas com o SQL Server estão documentadas em [Enable Encrypted Connections to the Database Engine](https://msdn.microsoft.com/library/ms191192.aspx) (Habilitar conexões criptografadas com o mecanismo de banco de dados), mas para conexões públicas com a Internet, como uma conexão do Azure Search com uma VM do SQL Azure, há alguns requisitos adicionais para que ela funcione.

### Especificar um FQDN no certificado SSL

O nome da entidade do certificado SSL deve ser o nome de domínio totalmente qualificado (ou **FQDN**) da VM do SQL Azure. É o mesmo FQDN que você especificará na cadeia de conexão do banco de dados ao criar uma fonte de dados no serviço de pesquisa. Um FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com` para VMs do **Resource Manager**. Se você ainda estiver em VMs **clássicas**, ele será formatado como `<your-cloud-service-name.cloudapp.net>`. Você pode encontrar o FQDN da sua VM do SQL Azure como o nome DNS/rótulo no [Portal do Azure](https://portal.azure.com/).

### Usar REGEDIT para configurar o certificado SSL

O SQL Server Configuration Manager não é capaz de mostrar o certificado SSL do FQDN no menu suspenso **Certificado**, conforme descrito na documentação. A solução alternativa é configurar o certificado SSL editando esta chave de Registro: **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server[MSSQL13.MSSQLSERVER]\\MSSQLServer\\SuperSocketNetLib\\Certificate**. A parte *[MSSQL13.MSSQLSERVER]* varia com base no nome da instância e na versão do SQL Server. Essa chave precisa ser atualizada com a **impressão digital** do certificado SSL instalado na VM do SQL Azure.

### Conceder permissões à conta de serviço

Verifique se a conta de serviço do SQL Server recebeu permissão apropriada na chave privada do certificado SSL. Se você ignorar essa etapa, o SQL Server não será iniciado.

## Configurar o firewall para permitir conexões do Azure Search

É comum configurar o firewall e o ponto de extremidade ou a ACL (lista de controle de acesso) do Azure correspondente para dar acesso à sua VM do Azure a outras pessoas. É provável que você já tenha definido essa configuração para permitir que a lógica do seu próprio aplicativo se conecte à VM do SQL Azure. Uma conexão do Azure Search com sua VM do SQL Azure não é diferente. Se você ainda não fez isso, veja a seguir algumas práticas de segurança que devem ser lembradas.

Se você estiver usando VMs do **Resource Manager**, veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure (Resource Manager)](../virtual-machines/virtual-machines-windows-sql-connect.md). Se ainda estiver nas VMs **clássicas**, veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure (implantação clássica)](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

### Restringir o acesso ao endereço IP do serviço de pesquisa

Em qualquer modelo de implantação, ao configurar conexões do Azure Search, é altamente recomendável restringir o acesso ao endereço IP do seu serviço de pesquisa na ACL, em vez de abrir totalmente as VMs do SQL Azure para todas as solicitações de conexão. Você pode descobrir facilmente o endereço IP fazendo ping do FQDN (por exemplo, `<your-search-service-name>.search.windows.net`) do seu serviço de pesquisa.

### Configurar um intervalo de endereços IP

Observe que se o serviço de pesquisa tiver apenas uma unidade de pesquisa (isto é, uma réplica e uma partição), o endereço IP poderá mudar durante a reinicialização do serviço de rotina. Para evitar falhas de conexão, você deve especificar o intervalo de endereços IP da região do Azure em que o serviço de pesquisa é provisionado. A lista de intervalos IP do qual os endereços IP públicos são alocados a recursos do Azure é publicada em [intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Incluir endereços IP do portal do Azure Search

Além disso, se você estiver usando o Portal do Azure para criar um indexador, a lógica do portal do Azure Search também precisará acessar a VM do SQL Azure durante a criação. Os endereços IP do portal do Azure Search podem ser encontrados executando ping de `stamp1.search.ext.azure.com` e `stamp2.search.ext.azure.com`.

## Próximas etapas

Com os requisitos de configuração acima resolvidos, agora você pode especificar um SQL Server na VM do Azure como a fonte de dados de um indexador do Azure Search. Confira [Conectando o banco de dados SQL do Azure ao Azure Search usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) para obter mais informações.

<!---HONumber=AcomDC_0907_2016-->