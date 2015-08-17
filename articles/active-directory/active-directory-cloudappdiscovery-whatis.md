<properties 
	pageTitle="Como descobrir aplicativos na nuvem não aprovados, usados em minha organização" 
	description="Este tópico descreve o que é o Cloud App Discovery e por que usá-lo." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Como descobrir aplicativos na nuvem não aprovados, usados em minha organização

Nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos na nuvem usados pelos usuários para realizar seu trabalho. Como resultado, os administradores geralmente têm preocupações relativas a acesso não autorizado a dados corporativos, possível vazamento de dados e outros riscos de segurança inerentes nos aplicativos. Como eles não sabem quantos ou quais aplicativos são usados, até mesmo começar a criar um plano para lidar com esses riscos parece ser desanimador.

Você pode resolver esses problemas usando o Cloud App Discovery. Cloud App Discovery é um recurso Premium do Active Directory do Azure que permite descobrir aplicativos na nuvem usados pelos funcionários de sua organização.


**Com o Cloud App Discovery é possível:**

* Descobrir aplicativos em uso e medir o uso pelo número de usuários, volume de tráfego ou número de solicitações ao aplicativo na Web. 
* Identificar os usuários que estão usando um aplicativo. 
* Exportar dados para adição de análise offline. 
* Priorizar aplicativos para colocar sob o controle da TI e integrá-los facilmente para habilitar o gerenciamento de usuário e logon único. 

Com o Cloud App Discovery, a parte de recuperação de dados é realizada por agentes executados em computadores em seus ambientes. As informações de uso do aplicativo coletadas pelos agentes são enviadas por um canal criptografado seguro para o serviço Cloud App Discovery. O serviço Cloud App Discovery avalia os dados e gera relatórios que você pode usar para analisar o ambiente.


<center>![Como funciona o Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)</center>

##Próximas etapas


* Para saber mais sobre como Cloud App Discovery funciona, consulte [Introdução ao Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) 
* Para considerações de segurança e privacidade, consulte [Considerações de privacidade e segurança do Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) 
* Para obter informações sobre como implantar o agente Cloud App Discovery em um ambiente corporativo com: 
 * Gerenciamento de Política de Grupo do Active Directory, consulte [Guia de implantação de política de grupo do Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) 
 * Microsoft System Center Configuration Manager, consulte [Guia de implantação do System Center do Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) 
 * Servidores proxy com portas personalizadas, consulte [Configurações de Registro do Cloud App Discovery para servidores proxy com portas personalizadas](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) 





**Recursos adicionais**


* [Cloud App Discovery - Log de alteração de agente ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery - Perguntas frequentes](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=August15_HO6-->