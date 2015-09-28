<properties
   pageTitle="Redefinir um Gateway de VPN do Azure | Microsoft Azure"
   description="Este artigo o orienta sobre como redefinir o Gateway de VPN do Azure. Observe que este artigo se refere a gateways de VPN criados usando o modelo de implantação clássico."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="cherylmc"/>

# Redefinir um Gateway de VPN do Azure usando o PowerShell


Este artigo o guiará pela redefinição do Gateway de VPN do Azure usando cmdlets do PowerShell. Essas instruções se aplicam ao modelo clássico de implantação. Ainda não temos um cmdlet ou API REST para redefinir um gateway de VPN para redes virtuais criadas usando o modelo de implantação do Gerenciador de Recursos. Eles estão em processo. Você pode saber se o gateway de VPN foi criado usando o modelo de implantação clássico, exibindo sua rede virtual no portal de visualização. Redes virtuais que foram criadas usando o modelo de implantação clássico são mostradas na parte de Rede Virtual (clássica) do portal de visualização.

Redefinir o gateway de VPN do Azure é útil se você perde a conectividade VPN entre locais em um ou mais túneis de VPN S2S. Nessa situação, os dispositivos VPN locais estão funcionando corretamente, mas não são capazes de estabelecer túneis IPsec com os gateways de VPN do Azure. Quando você usa o cmdlet *Reset-AzureVNetGateway*, ele reinicializa o gateway e, em seguida, aplica novamente a ele as configurações entre instalações. O gateway manterá o endereço IP público que já tem. Isso significa que você não precisa atualizar a configuração do roteador VPN com um novo endereço IP público do gateway de VPN do Azure.


Antes de redefinir o gateway, verifique os principais itens listados abaixo para cada túnel VPN site a site (S2S) de IPsec. Qualquer incompatibilidade nos itens resultará na desconexão de túneis VPN S2S. Verificar e corrigir as configurações para seus gateways de VPN do Azure e locais fará com que você evite reinicializações desnecessárias e interrupções para as outras conexões de trabalho nos gateways.

Verifique os itens a seguir antes de redefinir o gateway.

- Os VIPs (endereços IP da Internet) para o gateway de VPN do Azure e o gateway de VPN local estão configurados corretamente em ambas a políticas de VPN do Azure e de VPN local.
- A chave pré-compartilhada deve ser a mesma em ambos o gateway de VPN do Azure e o gateway de VPN local.
- Se você aplicar a configuração de IPsec/IKE específica, como criptografia, algoritmos de hash e PFS (Perfect Forward Secrecy), verifique se que o gateway de VPN do Azure e o gateway de VPN local têm as mesmas configurações.


## Redefinir um Gateway de VPN usando o PowerShell

O cmdlet do PowerShell para redefinir o gateway de VPN do Azure é *Reset-AzureVNetGateway*. Cada gateway de VPN do Azure é composto de duas instâncias VM em execução em uma configuração de modo de espera-ativo. Quando o comando é emitido, a instância ativa atual do gateway de VPN do Azure será reinicializada imediatamente. Haverá um breve intervalo durante o failover da instância ativa (sendo reinicializado) à instância em espera. O intervalo deve ser menor que um minuto.

O exemplo a seguir redefine o gateway de VPN do Azure para a rede virtual chamada "ContosoVNet".
 
			D:\PS> Reset-AzureVNetGateway –VnetName “ContosoVNet” 

	 		Error          :
	 		HttpStatusCode : OK
	 		Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	 		Status         : Successful
			RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
			StatusCode     : OK


Se a conexão não é restaurada após a primeira reinicialização, execute o mesmo comando novamente para reiniciar a segunda instância VM (o novo gateway ativo). Se as duas reinicializações são solicitadas back-to-back, haverá um período um pouco mais longo durante o qual as duas instâncias VM (ativas e em espera) estão sendo reinicializadas. Essa situação causará um intervalo maior na conectividade VPN, exigindo até 2 a 4 minutos para que as VMs concluam as reinicializações.

Após duas reinicializações, se você ainda estiver tendo problemas de conectividade entre locais, abra um ticket de suporte do portal do Azure para entrar em contato com o Suporte do Microsoft Azure.


## Próximas etapas
	
Consulte a [Referência do PowerShell](https://msdn.microsoft.com/library/azure/mt270366.aspx) para obter mais informações sobre esse cmdlet.

<!---HONumber=Sept15_HO3-->