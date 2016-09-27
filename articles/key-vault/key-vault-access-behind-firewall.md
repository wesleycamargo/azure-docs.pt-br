<properties
	pageTitle="Acessar o Cofre de Chaves por trás do firewall | Microsoft Azure"
	description="Saiba como acessar o Cofre de Chaves a partir de um aplicativo por trás de um firewall"
	services="key-vault"
	documentationCenter=""
	authors="amitbapat"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/13/2016"
	ms.author="ambapat"/>

# Acessando o Cofre de Chaves por trás do firewall
### P: meu aplicativo cliente do cofre de chaves precisa estar atrás de um firewall. Quais endereços IPs/hosts/portas devo abrir para habilitar o acesso ao cofre de chaves?

Para acessar um cofre de chaves, seu aplicativo cliente do cofre de chaves deve ser capaz de acessar vários pontos de extremidade para várias funcionalidades.

- Autenticação (usando o Azure Active Directory)
- Gerenciamento do Cofre de Chaves (que inclui a criação/leitura/atualização/exclusão e também a configuração de políticas de acesso) por meio do Azure Resource Manager
- O acesso e o gerenciamento de objetos (chaves e segredos) armazenados no cofre de chaves em si, passa pelo ponto de extremidade específico do cofre de chaves (por exemplo, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).

Dependendo do ambiente e configuração, há algumas variações.

## Portas

Todo o tráfego para o cofre de chaves de todas as três funções (autenticação, gerenciamento e acesso ao plano de dados) passa por HTTPS: porta 443. No entanto, para CRL, haverá ocasionalmente tráfego HTTP (porta 80). Os clientes que dão suporte a OCSP não devem chegar a CRL, mas ocasionalmente podem chegar a [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).

## Autenticação

O aplicativo cliente do Cofre de Chaves precisará acessar os pontos de extremidade do Azure Active Directory para autenticação. O ponto de extremidade usado depende da configuração de locatário do AAD e do tipo de entidade: entidade de usuário, entidade de serviço e tipo de conta, por exemplo, Conta da Microsoft ou ID da organização.

| Tipo de entidade | Ponto de extremidade:porta |
|----------------|---------------|
| Usuário usando a Conta da Microsoft<br> (por exemplo, user@hotmail.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure Governamental dos EUA:**<br> login- us.microsoftonline.com:443<br><br>**Azure Alemanha:**<br> login.microsoftonline.de:443<br><br> e <br>login.live.com:443 |
| Entidade de usuário/serviço usando a ID da organização com o AAD (por exemplo, user@contoso.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure Governamental dos EUA:**<br> login- us.microsoftonline.com:443<br><br>**Azure Alemanha:**<br> login.microsoftonline.de:443 |
| Entidade de usuário/serviço usando a ID da organização+ADFS ou outro ponto de extremidade federado (por exemplo, user@contoso.com) | Todos os pontos de extremidade acima para a ID da organização mais ADFS ou outros pontos de extremidade federados |

Há outros cenários complexos possíveis. Consulte [Fluxo de autenticação do Azure Active Directory](/documentation/articles/active-directory-authentication-scenarios/), [Integrando aplicativos com o Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) e [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para saber mais.

## Gerenciamento do Cofre de Chaves

Para o gerenciamento do Cofre de Chaves (CRUD e configuração de política de acesso), o aplicativo cliente do cofre de chaves precisa acessar o ponto de extremidade do Azure Resource Manager.

| Tipo de operação | Ponto de extremidade:porta |
|----------------|---------------|
| Operações do plano de controle do Cofre de Chaves<br> por meio do Azure Resource Manager | **Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure Governamental dos EUA:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 |
| Graph API do Active Directory do Azure | **Global:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure Governamental dos EUA:**<br> graph.windows.net:443<br><br> **Azure Alemanha:**<br> graph.cloudapi.de:443 |

## Operações do Cofre de Chaves

Para todas as operações de criptografia e gerenciamento de objetos (chaves e segredos) do cofre de chaves, o cliente do cofre de chaves precisa acessar o ponto de extremidade do cofre de chaves. Dependendo do local de seu Cofre de Chaves, o sufixo DNS do ponto de extremidade é diferente. O ponto de extremidade do Cofre de Chaves está no formato: <nome-do-cofre>.<sufixo-dns-específico-da-região> conforme descrito na tabela a seguir.

| Tipo de operação | Ponto de extremidade:porta |
|----------------|---------------|
| Operações do Cofre de Chaves, como operações criptográficas em chaves, chaves e segredos criados/lidos/atualizados/excluídos, definir/obter marcações e outros atributos em objetos de cofre de chaves (chaves/segredos) | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure Governamental dos EUA:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## Intervalos de endereços IP

O serviço Cofre de Chaves, por sua vez, usa outros recursos do Azure, como infraestrutura PaaS e, portanto, não é possível fornecer determinado intervalo de endereços IP que os pontos de extremidade do serviço Cofre de Chaves terá em algum momento. No entanto, se seu firewall dá suporte apenas a intervalos de endereço IP, consulte o documento [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Para autenticação e identidade (Azure Active Directory), seu aplicativo deve ser capaz de se conectar aos pontos de extremidade descritos em [Endereços de autenticação e identidade](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## Próximas etapas

- Se você tiver dúvidas sobre o Cofre de Chaves, visite os [Fóruns do Cofre de Chaves do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->