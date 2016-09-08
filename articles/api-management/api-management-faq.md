<properties 
	pageTitle="Perguntas frequentes sobre Gerenciamento de API do Azure | Microsoft Azure" 
	description="Conheça as respostas a perguntas comuns, padrões e práticas recomendadas do Gerenciamento de API do Azure" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="sdanie"/>

# Perguntas frequentes sobre Gerenciamento de API do Azure

Conheça as respostas a perguntas comuns, padrões e práticas recomendadas do Gerenciamento de API do Azure

## Perguntas frequentes

-	[Como fazer uma pergunta à equipe de Gerenciamento de API?](#how-can-i-ask-a-question-to-the-api-management-team)
-	[Se um recurso está em Visualização, o que isso significa?](#what-does-it-mean-if-a-feature-is-in-preview)
-	[Quais são as opções com suporte para proteger a conexão entre o gateway de Gerenciamento de API e meus serviços de back-end?](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
-	[Como copiar uma instância de Gerenciamento de API para uma nova instância?](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
-	[Posso gerenciar minha instância de Gerenciamento de API por meio de programação?](#can-i-manage-my-api-management-instance-programmatically)
-	[Como adicionar um usuário ao grupo Administradores?](#how-can-i-add-a-user-to-the-administrators-group)
-	[Por que a política que desejo adicionar não está habilitada no editor de política?](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)
-	[Como posso obter o controle de versão de API com o Gerenciamento de API?](#how-can-i-achieve-api-versioning-with-api-management)
-	[Como posso configurar vários ambientes de produção e APIs, por exemplo, Sandbox e Produção?](#how-can-i-configure-multiple-environments-of-apis-for-example-sandbox-and-production)
-	[SOAP tem suporte no Gerenciamento de API?](#is-soap-supported-in-api-management)
-	[O endereço IP do gateway do Gerenciamento de API é constante? Posso usá-lo nas regras de firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-	[Posso configurar um servidor de autorização OAUth 2.0 com segurança ADFS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-	[Que método de roteamento o Gerenciamento de API usa quando implantado em vários locais geográficos?](#what-routing-method-does-api-management-use-when-deployed-to-multiple-geographic-locations)
-	[Posso criar uma instância do serviço de Gerenciamento de API usando um modelo do ARM?](#can-i-create-an-api-management-service-instance-using-an-arm-template)
-	[Posso usar um certificado SSL autoassinado para um back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-backend)
-	[Por que estou recebendo falha de autenticação ao tentar clonar o repositório GIT?](#why-am-i-getting-authentication-failure-when-i-try-to-clone-the-git-repository)
-	[O Gerenciamento de API funciona com a Rota Expressa?](#does-api-management-work-with-express-route)


### Como fazer uma pergunta à equipe de Gerenciamento de API?

-	Você pode postar suas perguntas no nosso [Fórum do MSDN de Gerenciamento de API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-	Você pode nos enviar um email para o endereço: `apimgmt@microsoft.com`.
-	Você pode nos enviar uma [solicitação de recurso](https://feedback.azure.com/forums/248703-api-management).

### Se um recurso está em Visualização, o que isso significa?

Um recurso em visualização está funcionalmente completo, mas está em visualização porque estamos buscando ativamente comentários sobre esse recurso. É possível que venhamos a fazer uma grande mudança em resposta aos comentários dos clientes. Portanto, recomendamos que não dependa do recurso para uso em ambientes de produção. Se você tiver comentários sobre os recursos em preview, informe-nos usando um dos mecanismos descritos em [Como fazer uma pergunta à equipe do Gerenciamento de API?](#how-can-i-ask-a-question-to-the-api-management-team).

### Quais são as opções com suporte para proteger a conexão entre o gateway de Gerenciamento de API e meus serviços de back-end?

Há várias opções diferentes com suporte.

1. Use a autenticação básica HTTP. Para saber mais, confira [Definir configurações de API](api-management-howto-create-apis.md#configure-api-settings).
2. Use a autenticação mútua de SSL conforme descrito em [Saiba como garantir serviços de back-end usando a autenticação de certificado do cliente no Gerenciamento de API do Azure](api-management-howto-mutual-certificates.md).
3. Use a lista branca de IPs em seu serviço de back-end. Se você tiver uma instância de Gerenciamento de API de camada Standard ou Premium, o endereço IP do gateway permanecerá constante e você poderá configurar sua lista branca para permitir esse endereço IP. Você pode recuperar o endereço IP da sua instância de Gerenciamento de API no **Painel** do Portal Clássico do Azure.
4. Você pode conectar sua instância de Gerenciamento de API a uma Rede Virtual do Azure (clássico). Para saber mais, confira [Como configurar conexões VPN no Gerenciamento de API do Azure](api-management-howto-setup-vpn.md).

### Como copiar uma instância de Gerenciamento de API para uma nova instância?

Há várias opções diferentes que você pode usar para copiar uma instância de serviço de Gerenciamento de API para uma nova instância.

-	Use o recurso de backup e restauração do Gerenciamento de API. Para saber mais, confira [Como implementar a recuperação de desastre usando restauração e backup de serviço no Gerenciamento de API no Azure](api-management-howto-disaster-recovery-backup-restore.md).
-	Crie o seu próprio recurso de backup e restauração usando a [API REST do Gerenciamento de API](https://msdn.microsoft.com/library/azure/dn776326.aspx) para salvar e restaurar as entidades desejadas de sua instância de serviço.
-	Baixe a configuração de serviço usando o Git e carregue-a novamente em uma nova instância. Para obter mais informações, consulte [Saiba como salvar e definir a configuração do seu serviço de Gerenciamento de API usando o Git](api-management-configuration-repository-git.md).

### Posso gerenciar minha instância de Gerenciamento de API por meio de programação?

Sim, você pode gerenciá-la usando a [API REST do Gerenciamento de API](https://msdn.microsoft.com/library/azure/dn776326.aspx), o [SDK da Biblioteca de Gerenciamento de Serviço do Gerenciamento de Serviço de API do Microsoft Azure](http://aka.ms/apimsdk) e cmdlets do PowerShell de [Implantação de serviço](https://msdn.microsoft.com/library/mt619282.aspx) e [Gerenciamento de serviço](https://msdn.microsoft.com/library/mt613507.aspx).

### Como adicionar um usuário ao grupo Administradores?

Isso pode ser feito por meio das seguintes etapas:

1. Fazer logon no novo [Portal do Azure](https://portal.azure.com)
2. Navegar até o grupo de recursos que contém a instância desejada do Gerenciamento de API
3. Adicionar o usuário desejado à função de "Colaborador de Gerenciamento de Api"

Depois disso, o colaborador recém-adicionado pode usar os [cmdlets](https://msdn.microsoft.com/library/mt613507.aspx) do Azure PowerShell para fazer logon como um administrador:

1. Usar o cmdlet `Login-AzureRmAccount` para fazer logon
2. Definir o contexto para a assinatura que contém o serviço usando `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`
3. Obter o token SSO usando `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`
4. Copie e cole a URL no navegador, e o usuário deverá ter acesso ao portal de administração


### Por que a política que desejo adicionar não está habilitada no editor de política?

Se a política que você quer adicionar não estiver habilitada, verifique se você está no escopo correto para essa política. Cada declaração de política é projetada para uso em determinados escopos e seções de política. Para examinar as seções da política e os escopos de uma política, verifique a seção **Uso** dessa política na [Referência à Política](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### Como posso obter o controle de versão de API com o Gerenciamento de API?

-	Você pode configurar APIs distintas no Gerenciamento de API que representam diferentes versões. Por exemplo, você pode ter `MyAPI v1` e `MyAPI v2` como duas APIs diferentes e os desenvolvedores podem escolher qual versão desejam usar.
-	Você também pode configurar sua API com uma URL de serviço que não inclui um segmento de versão, por exemplo, `https://my.api`. Você pode configurar um segmento de versão no modelo [URL de Regravação](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) de cada operação, por exemplo, pode ter uma operação com um [modelo de URL](api-management-howto-add-operations.md#url-template) de `/resource` e o modelo [URL de Regravação](api-management-howto-add-operations.md#rewrite-url-template) de `/v1/Resource`. Dessa forma, você poderá alterar o valor de segmento de versão de cada operação separadamente.
-	Se você quiser manter um segmento de versão "padrão" na URL de serviço da API, em operações selecionadas, poderá definir uma política que usa a política [Definir serviço de back-end](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) para alterar o caminho de solicitação de back-end.

### Como posso configurar vários ambientes de produção e APIs, por exemplo, Sandbox e Produção?

Neste momento, as opções são:

-	Você pode hospedar APIs distintas no mesmo locatário
-	Você pode hospedar as mesmas APIs em locatários diferentes

### SOAP tem suporte no Gerenciamento de API?

Atualmente, damos suporte limitado a SOAP no Gerenciamento de API do Azure. É um recurso que ainda estamos investigando. Temos bastante interesse em obter exemplos de WSDLs do cliente e uma descrição dos recursos de que eles precisam, pois isso nos ajudaria no nosso raciocínio. Contate-nos usando as informações de contato referenciadas em [Como fazer uma pergunta à equipe de Gerenciamento de API?](#how-can-i-ask-a-question-to-the-api-management-team)

Se você precisa ter isso em funcionamento, algumas de nossas comunidades sugeriram soluções alternativas. Confira [Gerenciamento de API do Azure - APIM, consumindo um serviço WCF SOAP sobre HTTP](http://mostlydotnetdev.blogspot.com/2015/03/azure-api-management-apim-consuming.html).

A implementação da solução dessa forma exige algumas configurações manuais de políticas, não dá suporte à importação/exportação de WSDL e os usuários precisarão formar o corpo de solicitações feitas usando o console de teste no portal do desenvolvedor.

### O endereço IP do gateway do Gerenciamento de API é constante? Posso usá-lo nas regras de firewall?

Nas camadas Standard e Premium, o endereço IP público (VIP) do locatário do Gerenciamento de API é estático para o tempo de vida do locatário, com várias exceções listadas abaixo. Observe que locatários da camada Premium configurados para implantação em várias regiões recebem um endereço IP público por região.

O endereço IP é alterado nas seguintes circunstâncias:

-	O serviço é excluído e recriado
-	A assinatura do serviço é suspensa (por exemplo, por falta de pagamento) e restabelecida
-	Uma rede virtual é adicionada ou removida (redes virtuais têm suporte somente na camada Premium)
-	O endereço regional muda se a região é limpa e inserida novamente (implantação de várias regiões com suporte somente na camada Premium)

O endereço IP (ou endereços, no caso de implantação de várias regiões) pode ser encontrado na página de locatário no Portal Clássico do Azure.

### Posso configurar um servidor de autorização OAUth 2.0 com segurança ADFS?

Para saber mais sobre como configurar esse cenário, confira [Usando ADFS no Gerenciamento de API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### Que método de roteamento o Gerenciamento de API usa quando implantado em vários locais geográficos? 

O Gerenciamento de API usa o [Método de roteamento de tráfego de desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method). O tráfego será roteado para o gateway de API mais próximo. Se uma região ficar offline, o tráfego de entrada será automaticamente roteado para o gateway mais próximo. Para obter mais informações sobre métodos de roteamento, confira [Métodos de roteamento do Gerenciador de Tráfego](../traffic-manager/traffic-manager-routing-methods.md).

### Posso criar uma instância do serviço de Gerenciamento de API usando um modelo do ARM?

Sim, confira os modelos de início rápido do [Serviço de Gerenciamento de API do Azure](http://aka.ms/apimtemplate).

### Posso usar um certificado SSL autoassinado para um back-end?

Sim. Siga as etapas abaixo:

1. Criar uma entidade de [Back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) usando a API de Gerenciamento
2. Definir a propriedade skipCertificateChainValidation como true
3. Quando você não quiser mais permitir certificados autoassinados, poderá excluir a entidade de back-end ou definir a propriedade skipCertificateChainValidation como false

### Por que estou recebendo falha de autenticação ao tentar clonar o repositório GIT? 

Se você estiver usando o Gerenciador de Credenciais do GIT ou tentando clonar o repositório por meio do Visual Studio, talvez você se depare com um problema com o diálogo de credencial do Windows, que limita o tamanho da senha para somente 127 caracteres e, portanto, trunca a senha que geramos. Estamos trabalhando para encurtar a senha. Por enquanto, use o GIT Bash para clonar.

### O Gerenciamento de API funciona com a Rota Expressa?

Sim!

<!---HONumber=AcomDC_0824_2016-->