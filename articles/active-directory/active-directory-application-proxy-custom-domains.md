<properties
	pageTitle="Trabalhando com Domínios Personalizados no Proxy de Aplicativo do AD do Azure | Microsoft Azure"
	description="Aborda como trabalhar com domínios personalizados no Proxy de Aplicativo do AD do Azure."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/19/2015"
	ms.author="rkarlin"/>

# Trabalhando com domínios personalizados no Proxy de Aplicativo do AD do Azure
> [AZURE.NOTE]O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

Usando um domínio padrão permite que você defina a mesma URL como URL interna e externa para acessar o aplicativo, para que seus usuários tenham que lembrar de apenas uma URL para acessar o aplicativo, independentemente de onde eles estejam acessando e isso permite que você crie um único atalho no Painel de Acesso para o aplicativo. Se você usar o domínio padrão fornecido pelo Proxy de Aplicativo do AD do Azure, não há nenhuma configuração adicional que seja necessária para habilitar o seu domínio. Se você usar um domínio personalizado, há algumas coisas que você precisa fazer para certificar-se de que o Proxy do Aplicativo reconheça seu domínio e valide seus certificados.

## Selecionando o domínio personalizado

1. Publique seu aplicativo seguindo as instruções em Publique Aplicativos com o Proxy do Aplicativo.
2. Depois que o aplicativo aparecer na lista de aplicativos, selecione-o e clique em **Configurar**.
3. Em **URL Externa**, digite seu domínio personalizado.
4. Se a URL externa for https, você deverá carregar um certificado para que o Azure possa validar a URL do aplicativo. Você também pode carregar um certificado curinga que corresponda a URL Externa do aplicativo. Esse domínio deve estar na lista do seu [Domínios verificado do Azure](https://msdn.microsoft.com/library/azure/jj151788.aspx). O Azure deve ter um certificado para o domínio da URL do aplicativo ou um certificado curinga que corresponda à URL externa para o aplicativo.
5. Certifique-se de adicionar um registro DNS que roteie a URL interna para o aplicativo, que permite que você tenha a mesma URL para acesso interno e externo e um único atalho para o aplicativo na lista de aplicativos do usuário.

## Perguntas frequentes sobre como trabalhar com domínios personalizados

P: Posso selecionar um certificado já carregado sem carregá-lo novamente? <br> R: Certificados carregados previamente são vinculados automaticamente a um aplicativo e há exatamente um certificado que corresponde ao nome de host do aplicativo. <br> …<br> P: Como adicionar um certificado e qual formato deveria ter o certificado exportado para ser carregado? <br> R: Em seguida, o certificado deve ser carregado a partir da página de configuração do aplicativo. O certificado deve ser um arquivo PFX. <br> …<br> P: Os certificados ECC podem ser usados? <br> R: Não há nenhuma limitação explícita em métodos de assinatura. <br> …<br> P: Os certificados SAN podem ser usados? <br> R: Sim.<br> …<br> P: Os certificados curinga podem ser usados? <br> R: Sim. <br> …<br> P: Um certificado diferente pode ser usado em cada aplicativo? <br> R: Sim, a menos que os dois aplicativos compartilhem o mesmo host externo. <br> …..<br> P: Se eu registrar um novo domínio, posso usar esse domínio? <br> R: Sim, a lista de domínios é alimentada a partir da lista de domínio verificada do inquilino. <br> …<br> P: O que acontece quando um certificado expira? <br> R: Você receberá um aviso na seção do certificado na página de configuração do aplicativo. Quando um usuário tenta acessar o aplicativo, um aviso de segurança será exibido. <br> …<br> P: O que devo fazer se quiser substituir um certificado para um determinado aplicativo? <br> R: Carregar um novo certificado da página de configuração de aplicativo<br> ...<br> P: Posso excluir um certificado e substituí-lo? <br> R: quando você carregar um novo certificado, se o certificado antigo não estiver em uso por outro aplicativo, ele será automaticamente excluído<br> ...<br> P: O que acontece quando um certificado é revogado<br> R: Verificações de revogação de não são executadas para certificados. Quando um usuário tenta acessar o aplicativo, dependendo do navegador, um aviso de segurança deve aparecer.<br> …<br> P: Posso usar um certificado autoassinado? <br> A: Sim, os certificados autoassinados são permitidos. Observe que se você estiver usando uma autoridade de certificação privada, o CDP (ponto de distribuição de lista de revogação de certificados) para o certificado deve ser público. <br> ...<br> P: Existe um lugar para ver todos os certificados para o meu locatário? <br> R: Isso não é suportado na versão atual.<br>



## Consulte também
Você pode fazer muito mais com o Proxy de Aplicativo:

- [Publique aplicativos com proxy de aplicativo](active-directory-application-proxy-publish.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabalhando com aplicativos com reconhecimento de declarações](active-directory-application-proxy-claims-aware-apps.md)- [Solucionar problemas que surgirem com o Proxy de aplicativo](active-directory-application-proxy-troubleshoot.md)

## Saiba mais sobre o Proxy de Aplicativo
- [Veja nossa ajuda online](active-directory-application-proxy-enable.md)
- [Confira o blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)
- [Assista aos nossos vídeos no Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionais

* [Inscrever-se no Azure como uma organização](sign-up-organization.md)
* [Identidade do Azure](fundamentals-identity.md)

<!---HONumber=Oct15_HO4-->