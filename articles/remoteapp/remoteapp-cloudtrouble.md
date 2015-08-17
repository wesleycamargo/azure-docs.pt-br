
<properties 
    pageTitle="Solucionar problemas de coleções na nuvem do RemoteApp - criação"
    description="Saiba como solucionar problemas de falhas de criação de coleção de nuvem do RemoteApp" 
    services="remoteapp" 
	documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="vikbucha" />



# Solucionar problemas na criação de coleções em nuvem do RemoteApp

Erros comuns vistos no Portal de Gerenciamento do Azure:

	DNS server could not be reached
	ProvisioningTimeout

Coleções de nuvem geralmente falham durante a criação por você estar usando imagens personalizadas. Se você receber um dos erros anteriores e estiver usando uma imagem personalizada para criar a coleção, verifique os seguintes itens:

- Certifique-se de que a imagem personalizada carregada atende aos requisitos de imagem. 
- Geralmente, o problema comum é que a imagem não foi corretamente preparada para o sistema.  
- Verifique se a imagem pode inicializar no Hyper-V ou tente criar uma VM IAAS diretamente na assinatura do Azure usando a imagem. Se a VM falhar ao inicializar e não iniciar, isso geralmente indica que a imagem personalizada não estava preparada corretamente. Verifique se a imagem personalizada foi criada seguindo as instruções em Como criar uma imagem de modelo personalizado para o RemoteApp

Se você estiver usando uma das imagens Microsoft incluídas em sua assinatura, tente criar novamente a coleção. Se o problema persistir, contate o suporte da Microsoft.

	PlatformImageTrialModeOnly

Se você receber esse erro, isso geralmente significa que você atualizou para uma conta paga, mas está tentando usar uma imagem fornecido pela Microsoft que é válida somente durante o modo de avaliação do serviço. Nesse caso, tente criar novamente sua coleção de nuvem, mas não se esqueça de especificar a imagem correta.
 

<!---HONumber=August15_HO6-->