---
title: Como fornecer acesso remoto seguro a aplicativos locais
description: Aborda como usar o Proxy de Aplicativo do AD do Azure para fornecer acesso remoto seguro aos seus aplicativos locais.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: kgremban

---
# Como fornecer acesso remoto seguro a aplicativos locais
> [!NOTE]
> O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
> 
> 

Os funcionários de hoje querem ser produtivos em qualquer lugar, a qualquer momento e com qualquer dispositivo. Desejam trabalhar em seus próprios dispositivos, sejam eles laptops, telefones ou tablets. Além disso, eles esperam para poder acessar todos os seus aplicativos, tanto aplicativos SaaS na nuvem como aplicativo corporativos no local. O fornecimento de acesso a aplicativos locais sempre envolveu VPNs (redes virtuais privadas), DMZs (zonas desmilitarizadas) ou proxies reversos locais. Essas soluções são complexas e difíceis de proteger, além de serem caras para configurar e gerenciar.

Há uma opção melhor!

Uma força de trabalho moderna no mundo que prioriza a nuvem e a mobilidade precisa de uma solução moderna de acesso remoto. O Proxy de Aplicativo do Azure AD é um recurso da oferta do Azure Active Directory Premium e oferece acesso remoto como um serviço. Isso significa que é fácil de implantar, usar e gerenciar.

## O que é o Proxy de Aplicativo do Active Directory do Azure?
O Proxy de Aplicativo do Azure AD fornece SSO (logon único) e acesso remoto seguro para aplicativos da Web hospedados no local. Isso pode incluir sites do SharePoint, o Outlook Web Access ou qualquer outro aplicativo Web de LOB que você tenha. Esses aplicativos Web locais são integrados ao Azure AD, a mesma plataforma de identidade e controle que é usada pelo O365. Os usuários finais podem acessar seus aplicativos no local da mesma forma que acessam o O365 e outros aplicativos SaaS integrados ao Azure AD. Você não precisa alterar a infraestrutura de rede nem necessita VPN para fornecer essa solução para seus usuários.

## Por que é uma solução melhor?
O Proxy de Aplicativo do Azure AD fornece uma solução de acesso remoto simples, segura e econômica para todos os aplicativos locais.

Proxy de Aplicativo do Azure AD:

* Funciona na nuvem, assim, você pode economizar tempo e dinheiro. Soluções locais exigem que você configure e mantenha DMZs, servidores de borda ou outras infraestruturas complexas.
* É mais fácil de configurar e proteger do que soluções locais porque você não precisa abrir conexões de entrada através do firewall.
* Oferece excelente segurança. Ao publicar aplicativos usando o Proxy de Aplicativo do Azure AD, você pode tirar proveito dos controles de autorização avançados e da análise de segurança no Azure. Isso significa que você obtém recursos de segurança avançados para todos os aplicativos existentes sem a necessidade de alterá-los.
* Oferece aos usuários uma experiência de autenticação consistente. O logon único oferece aos usuários finais a facilidade e a simplicidade do acesso a todos os aplicativos de que precisam para serem produtivos com uma única senha.

## Que tipo de aplicativo funciona com o Proxy de Aplicativo do Azure AD?
Com o Proxy de Aplicativo do Azure AD, você pode acessar diferentes tipos de aplicativos internos:

* Aplicativos Web que usam a Autenticação Integrada do Windows para autenticação
* Aplicativos Web que usam o acesso baseado em formulário
* APIs Web que você deseja expor a aplicativos avançados em diferentes dispositivos
* Aplicativos hospedados por trás de um Gateway de Área de Trabalho Remota

## Como ele funciona?
O Proxy de Aplicativo funciona com a instalação de um serviço leve do Windows Server, chamado conector, dentro de sua rede. Com o conector, você não precisa abrir portas de entrada nem colocar nada no DMZ. Se você tiver alto tráfego em seus aplicativos, pode adicionar mais conectores e o serviço se encarrega do balanceamento de carga. Os conectores não têm monitoração de estado e extraem tudo da nuvem conforme o necessário.

Quando os usuários acessam aplicativos remotamente, conectam-se ao ponto de extremidade publicado. Os usuários são autenticados no Azure AD e, em seguida, são roteados por meio do conector para o aplicativo local.

 ![Diagrama do Proxy de Aplicativo do Azure](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. O usuário acessa o aplicativo usando o Proxy de Aplicativo e é direcionado para a página de entrada do Azure AD para autenticação.
2. Após a entrada bem-sucedida, um token é gerado e enviado ao usuário.
3. O usuário envia o token ao Proxy de Aplicativo, que recupera o nome UPN e o SPN (nome da entidade de segurança) do token e direciona a solicitação para o conector.
4. Em nome do usuário, o conector solicita um tíquete Kerberos que pode ser usado para a autenticação (Windows) interna. Isso é conhecido como Delegação Restrita de Kerberos.
5. O Active Directory recupera o tíquete Kerberos.
6. A permissão é enviada para o servidor de aplicativo e verificada.
7. A resposta é enviada por meio do Proxy de Aplicativo ao usuário.

### Logon único
O Proxy de Aplicativo do Azure AD oferece SSO (logon único ) a aplicativos que usam a IWA (Autenticação Integrada do Windows) ou aplicativos com reconhecimento de declaração. Se seu aplicativo usa a IWA, o Proxy de Aplicativo representa o usuário usando a delegação restrita de Kerberos para fornecer o SSO. Se você tem um aplicativo com reconhecimento de declaração que confia no Active Directory do Azure, o SSO é obtido porque o usuário já foi autenticado pelo AD do Azure.

## Como começar
Verifique se você tem uma assinatura premium ou básica do Azure AD e um diretório do Azure AD dos quais é um administrador global. Você também precisa de licenças básicas ou premium do AD do Azure para o administrador do diretório e os usuários que acessam os aplicativos. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md).

A configuração do Proxy de Aplicativo é realizada em duas etapas:

1. [Habilitar o Proxy de Aplicativo e configurar o conector](active-directory-application-proxy-enable.md)
2. [Publicar aplicativos](active-directory-application-proxy-publish.md) - use o assistente rápido e fácil para publicar seus aplicativos locais e torná-los acessíveis remotamente.

## O que vem a seguir?
Você pode fazer muito mais com o Proxy de Aplicativo:

* [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
* [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
* [Trabalho com aplicativos com reconhecimento de declaração](active-directory-application-proxy-claims-aware-apps.md)
* [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)

<!---HONumber=AcomDC_0824_2016-->