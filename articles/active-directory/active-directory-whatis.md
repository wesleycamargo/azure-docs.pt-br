<properties 
                pageTitle="O que é o Active Directory do Azure?" 
                description="Use o Active Directory do Azure para estender suas identidades locais existentes para a nuvem ou desenvolva aplicativos integrados do AD do Azure." 
                services="active-directory" 
                documentationCenter="" 
                authors="markusvi" 
                manager="swadhwa" 
                editor=""/>

<tags 
                ms.service="active-directory" 
                ms.workload="identity" 
                ms.tgt_pltfrm="na" 
                ms.devlang="na" 
                ms.topic="article" 
                ms.date="07/14/2015" 
                ms.author="markusvi"/>


# O que é o Active Directory do Azure?





O Active Directory do Azure (AD do Azure) é o serviço de gerenciamento de identidade e diretório multilocatário com base em nuvem da Microsoft.

Para administradores de TI, o AD do Azure oferece uma solução acessível e fácil de usar para fornecer a funcionários e parceiros de negócios acesso de logon único (SSO) a [milhares de aplicativos SaaS na nuvem](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx), como Office365, Salesforce.com, DropBox e Concur.

Para desenvolvedores de aplicativos, o AD do Azure permite que você se concentre na criação de seu aplicativo, tornando rápida e simples a integração de uma solução de gerenciamento de identidade da mais alta qualidade e utilizada por milhões de empresas em todo o mundo.

O AD do Azure também inclui um conjunto completo de recursos de gerenciamento de identidade, incluindo multi-factor authentication, registro de dispositivos, gerenciamento de senhas de autoatendimento, gerenciamento de grupos de autoatendimento, gerenciamento de contas com privilégios, controle de acesso com base em função, monitoramento de uso de aplicativos, auditoria avançada e alertas e monitoramento de segurança. Esses recursos podem ajudar a proteger aplicativos com base na nuvem, simplificar os processos de TI, cortar custos e ajudar a garantir que as metas de conformidade corporativa sejam atingidas.

Além disso, com apenas [quatro cliques](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx), o AD do Azure pode ser integrado a um Active Directory do Windows Server existente, fornecendo às organizações a capacidade de aproveitar os investimentos locais de identidade existentes para gerenciar o acesso à nuvem com base em aplicativos SaaS.

Se você for um cliente do Office365, Azure ou Dynamics CRM Online, talvez não tenha percebido que já está usando o AD do Azure. Todo locatário do Office365, Azure e Dynamics CRM já é, na verdade, um locatário do AD do Azure. Quando desejar, você poderá começar a usar esse locatário para gerenciar o acesso a milhares de outros aplicativos na nuvem aos quais o AD do Azure se integra!





<center>![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png) </center>


## O AD do Azure é confiável?

O projeto multilocatário, distribuído geograficamente e de alta disponibilidade do AD do Azure significa que você pode confiar nele para suas necessidades comerciais mais críticas. Com execução em 28 data centers em todo o mundo com failover automático, você terá o conforto de saber que o AD do Azure é altamente confiável e que, mesmo se um data center ficar inativo, cópias de seu diretório de dados estarão ativas em pelo menos dois data centers mais dispersos regionalmente e disponíveis para acesso instantâneo.



## Quais são os benefícios do AD do Azure?

Sua organização pode usar o AD do Azure para melhorar a produtividade dos funcionários, simplificar os processos de TI, melhorar a segurança e reduzir custos de várias maneiras:

-	Adote rapidamente os serviços de nuvem, fornecendo aos funcionários e parceiros uma experiência de logon único fácil da plataforma dos recursos de serviços de provisionamento e gerenciamento de acesso a aplicativos SaaS totalmente automatizados do AD do Azure.
-	Capacite os funcionários com acesso aos aplicativos na nuvem da mais alta qualidade e recursos de serviço e autoatendimento de onde quer que eles precisem para trabalharem nos dispositivos que adoram usar.
-	Gerencie com facilidade e segurança o acesso de funcionários e fornecedores a suas contas de mídia social corporativa. 
-	Aprimore a segurança dos aplicativos com o acesso condicional e a autenticação multifator do AD do Azure.
-	Implemente um gerenciamento de acesso de aplicativo de autoatendimento consistente, permitindo que os proprietários de negócios se movam rapidamente e também reduzindo a sobrecarga e os custos de TI.
-	Monitore o uso dos aplicativos e proteja sua empresa contra ameaças avançadas com monitoramento e relatórios de segurança.
-	Proteja o acesso móvel (remoto) a aplicativos locais.






## Como posso começar?
-	Se você for um administrador de TI:
 - [Experimente!](https://azure.microsoft.com/pt-br/pricing/free-trial/) - você pode se inscrever em uma avaliação gratuita de 30 dias hoje mesmo e implantar sua primeira solução de nuvem em menos de 5 minutos usando este link
 - Leia nossa "Introdução ao AD do Azure" para obter dicas e truques sobre como colocar um locatário do AD do Azure em funcionamento rapidamente
-	Se você for um desenvolvedor:
 - Confira nosso [Guia de Desenvolvedores](https://msdn.microsoft.com/library/azure/ff800682.aspx) para o Active Directory do Azure<need link>
 - [Inicie uma avaliação](https://azure.microsoft.com/pt-br/pricing/free-trial/) – inscreva-se em uma avaliação gratuita de 30 dias hoje mesmo e comece a integrar seu aplicativo com o AD do Azure 



## Onde posso saber mais?

Temos inúmeros ótimos recursos online para ajudá-lo a aprender tudo sobre o AD do Azure. Aqui está uma lista de artigos excelentes para você começar:


- [Habilitando seu diretório para gerenciamento híbrido com o Azure AD Connect](active-directory-aadconnect.md)
- [Segurança adicional para um mundo sempre conectado](multi-factor-authentication.md)
- [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)
- [Implantando o gerenciamento de senhas e treinando os usuários para usá-lo](active-directory-passwords-best-practices.md)
- [Logon único e gerenciamento de acesso a milhares de aplicativos SaaS em qualquer nuvem](https://msdn.microsoft.com/library/azure/dn308590.aspx) 
- [Acesso remoto seguro ao aplicativo local](https://msdn.microsoft.com/library/azure/dn768219.aspx)
- [Gerenciamento de acesso de autoatendimento](https://msdn.microsoft.com/library/azure/dn641267.aspx) 
- [Descoberta de aplicativos de nuvem](https://msdn.microsoft.com/library/azure/mt143581.aspx)
- [Controle de acesso com base em integridade do dispositivo, usuário local e identidade](https://msdn.microsoft.com/library/azure/dn906873.aspx)
- [Utilize a nuvem para aprimorar e monitorar os sistemas de identidade locais](https://msdn.microsoft.com/library/azure/dn906722.aspx)
- [Plataforma baseada em padrão avançado para desenvolvedores](https://msdn.microsoft.com/library/azure/ff800682.aspx)

<!---HONumber=August15_HO6-->