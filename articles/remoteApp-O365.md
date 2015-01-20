<properties title="Using Office365 with Azure RemoteApp" pageTitle="Usando o Office 365 com o RemoteApp do Azure" description="Saiba como o Office 365 e o Azure RemoteApp funcionam juntos" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Usando o Office 365 com o RemoteApp do Azure

Você tem duas opções para hospedar aplicativos do Office no RemoteApp: O Office 365 ProPlus ou a versão de avaliação do Office 2013 Professional Plus.

##Office 365 ProPlus 
Você pode criar uma coleção de RemoteApp usando a imagem de modelo do Office 365 ProPlus. Esta opção permite estender o serviço Office 365 para o RemoteApp. Você deve ter um plano de assinatura existente e seus usuários devem ser licenciados para o serviço Office 365 ProPlus, sozinhos ou planos de serviço por meio do Office 365. As instâncias de RemoteApp dos aplicativos Office contará como uma das cinco instâncias de instalação permitidas para cada usuário, e eles serão ativados totalmente. Você pode gerenciar licenças do Office 365 dos usuários no [Portal de administração do Office 365](https://portal.office365.com/). Para obter mais informações sobre [Planos de serviço do Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  

A opção Office 365 ProPlus estará disponível durante a avaliação gratuita de 30 dias e no modo de produção, mas esta é a única opção com suporte após o término da avaliação.  

Observe que você também pode criar uma imagem de modelo personalizada que contenha o Office 365 ProPlus. Para construir essa imagem de modelo, siga as [etapas de implantação](http://technet.microsoft.com/pt-br/library/dn782858(v=office.15). aspx) para o Office 365 ProPlus nos Serviços de Área de Trabalho Remota (RDS).

##Versão de avaliação do Office 2013 Professional Plus 
Durante uma avaliação de 30 dias do RemoteApp, você pode usar a imagem de modelo do Office 2013 Professional Plus (avaliação) para criar uma coleção de RemoteApp. Você pode atribuir usuários a esta coleção de avaliação usando suas contas de trabalho do Active Directory do Azure ou contas da Microsoft. Nenhuma assinatura adicional é necessária.

Essa é uma ótima opção para testar e ter uma boa noção do Office no RemoteApp. No entanto, essa opção é destinada somente para avaliação e teste. As coleções de RemoteApp criadas usando a imagem de modelo do Office 2013 Professional Plus (avaliação) não podem ser transferidas para o modo de produção e serão desabilitadas no final do período de avaliação.

##Alternância de avaliação para a produção
Quando você inicia sua avaliação gratuita de 30 dias, uma observação na seção do portal do RemoteApp lhe dirá quanto tempo de avaliação você tem antes que você precise fazer a transição para uma conta paga. Você pode ativar sua conta e alternar para o modo de produção usando o link deste documento. 

Quando você ativar sua conta, isso afetará todas as coleções de RemoteApp em sua conta. 

- Coleções que executam o Windows Server 2012 R2 ou as imagens de modelo do Office 365 ProPlus farão a transição para a produção perfeitamente. Todos os dados de usuário e configurações, incluindo sessões em andamento, permanecerão intactas.
- Se você carregou imagens de modelo personalizada, as coleções usando essas imagens também mudarão perfeitamente.
- A imagem de modelo do Office 2013 Professional Plus (avaliação) destina-se somente para avaliação. Coleções com esta imagem de modelo não podem ser transferidas para produção. Elas serão colocadas no estado "desativadas".


Se você não fizer a transição para o modo de produção até a expiração da sua avaliação, as coleções de RemoteApp serão desabilitadas. Não se preocupe - suas configurações e dados de usuários serão salvas por outros 90 dias para que você ainda possa ativar o serviço e alternar para o modo de produção sem qualquer perda de dados.

<!--HONumber=35.2-->
