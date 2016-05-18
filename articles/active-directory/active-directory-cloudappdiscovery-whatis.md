<properties
	pageTitle="Encontrando aplicativos em nuvem não gerenciados com o Cloud App Discovery | Microsoft Azure"
	description="Fornece informações sobre a localização e o gerenciamento de aplicativos com o Cloud App Discovery, quais são seus benefícios e como ele funciona."
	services="active-directory"
	keywords="cloud app discovery, gerenciamento de aplicativos"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/30/2016"
	ms.author="markvi"/>

# Encontrando aplicativos em nuvem não gerenciados com o Cloud App Discovery

## Visão geral
Nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos em nuvem que os membros de sua organização usam para realizar seu trabalho. É fácil ver por que os administradores teriam preocupações sobre o acesso não autorizado aos dados corporativos, perda de dados e outros riscos de segurança. Essa falta de reconhecimento pode fazer com que a criação de um plano para lidar com esses riscos de segurança pareça assustadora.

O Cloud App Discovery é um recurso Premium do Active Directory (AD) do Azure que permite descobrir os aplicativos em nuvem usados pelas pessoas em sua organização.

**Com o Cloud App Discovery é possível:**

- Descubra os aplicativos em nuvem usados e meça o uso pelo número de usuários, volume de tráfego ou número de solicitações Web para o aplicativo.
- Identificar os usuários que estão usando um aplicativo.
- Exporte os dados para a análise offline.
- Coloque esses aplicativos sob controle da TI e habilite o logon único para o gerenciamento de usuário.

## Como ele funciona
1. Os agentes de uso dos aplicativos são instalados nos computadores dos usuários.
2. As informações de uso do aplicativo coletadas pelos agentes são enviadas por um canal criptografado seguro para o serviço Cloud App Discovery.
3. O serviço Cloud App Discovery avalia os dados e gera relatórios.

![Diagrama do Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)

Para iniciar com o Cloud App Discovery, confira [Introdução ao Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

## Artigos relacionados
- [Considerações de privacidade e segurança no Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
- [Guia de Implantação da Política de Grupo do Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)
- [Guia de Implantação do System Center do Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)
- [Configurações de Registro do Cloud App Discovery para Servidores Proxy com Portas Personalizadas](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
- [Cloud App Discovery - Log de Alteração de Agente ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
- [Cloud App Discovery - Perguntas Frequentes](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)
- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!---HONumber=AcomDC_0504_2016-->