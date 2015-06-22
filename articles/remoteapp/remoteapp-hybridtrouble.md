
<properties 
    pageTitle="Solucionar problemas de coleções híbridas - criação"
    description="Saiba como solucionar problemas de falhas de criação de coleção híbrida do RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="vikbucha" />



# Solucionar problemas de coleções híbridas - criação

Antes de solucionar falhas durante a criação de coleção híbrida, é importante entender como as coleções híbrida são criadas. Uma coleção híbrida requer que instâncias do RemoteApp sejam adicionadas ao domínio. Isso é feito durante a criação da coleção.  Quando o processo de criação de coleção se inicia, cópias das imagens do modelo que você carregou são criadas na VNET e adicionadas ao domínio usando o túnel VPN site a site resolvido com o registro de IP DNS especificado durante a criação da VNET.

Erros comuns vistos no Portal de Gerenciamento do Azure:

	DNS server could not be reached

	Could not join the domain. Unable to reach the domain.

Se você vir um dos erros anteriores, consulte os seguintes procedimentos:

- Verifique se as configurações de IP de DNS são válidas
- Certifique-se de que os registros de IP de DNS são registros de IP públicos ou fazem parte do "espaço de endereço local" especificado durante a criação de VNET
- Verifique o túnel VNET para garantir que está no estado ativo ou conectado 
- Certifique-se de que o lado local da conexão VPN não está bloqueando o tráfego de rede. Você pode verificar isso consultando os logs do software ou dispositivo VPN local.
- Certifique-se de que o domínio especificado durante a criação de coleção esteja em execução.

	ProvisioningTimeout


Se você receber esse erro, verifique as seguintes ações:

- Certifique-se de que o lado local da conexão VPN não está bloqueando o tráfego de rede. Você pode verificar isso consultando os logs do software ou dispositivo VPN local.
- Certifique-se de que a imagem de modelo do RemoteApp carregada corretamente foi preparada para o sistema corretamente. Você pode verificar os requisitos de imagem do RemoteApp aqui: http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/ 
- Tente criar uma VM usando a imagem de modelo carregada e certifique-se de que ela seja inicializada e executada corretamente (a) em um servidor local do Hyper-V; (b) criando uma VM IAAS do Azure na sua assinatura do Azure. Se a criação da VM falhar ou não iniciar, isso geralmente indica que a imagem de modelo não foi preparada corretamente e você terá de corrigi-la.

	DomainJoinFailed_InvalidUserNameOrPassword

	DomainJoinFailed_InvalidParameter

Se você vir um dos erros anteriores, consulte os seguintes procedimentos:

- Verifique se as credenciais inseridas para ingresso no domínio são válidas
- Verifique se as credenciais para ingresso no domínio estão corretas ou têm as permissões de associação de domínio apropriadas
- Verifique se a UO (unidade organizacional) está formatada corretamente e existe no Active Directory.


<!--HONumber=52--> 