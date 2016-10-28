<properties
	pageTitle="Trabalhando com Domínios Personalizados no Proxy de Aplicativo do AD do Azure | Microsoft Azure"
	description="Aborda como trabalhar com domínios personalizados no Proxy de Aplicativo do AD do Azure."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="kgremban"/>

# Trabalhando com domínios personalizados no Proxy de Aplicativo do AD do Azure

Usando um domínio padrão permite que você defina a mesma URL como URL interna e externa para acessar o aplicativo, para que seus usuários tenham que lembrar de apenas uma URL para acessar o aplicativo, independentemente de onde eles estejam acessando. Isso também permite criar um único atalho no Painel de Acesso para o aplicativo. Se você usar o domínio padrão fornecido pelo Proxy de Aplicativo do AD do Azure, não há nenhuma configuração adicional que seja necessária para habilitar o seu domínio. Se você usar um domínio personalizado, há algumas coisas que você precisa fazer para certificar-se de que o Proxy do Aplicativo reconheça seu domínio e valide seus certificados.

## Selecionando o domínio personalizado

1. Publique seu aplicativo seguindo as instruções em [Publicar aplicativos com o Proxy de Aplicativo](active-directory-application-proxy-publish.md).
2. Depois que o aplicativo aparecer na lista de aplicativos, selecione-o e clique em **Configurar**.
3. Em **URL Externa**, digite seu domínio personalizado.
4. Se a URL externa for https, você deverá carregar um certificado para que o Azure possa validar a URL do aplicativo. Você também pode carregar um certificado curinga que corresponda a URL Externa do aplicativo. Esse domínio deve estar na lista do seu [Domínios verificado do Azure](https://msdn.microsoft.com/library/azure/jj151788.aspx). O Azure deve ter um certificado para o domínio da URL do aplicativo ou um certificado curinga que corresponda à URL externa para o aplicativo.
5. Certifique-se de adicionar um registro DNS que roteie a URL interna para o aplicativo, que permite que você tenha a mesma URL para acesso interno e externo e um único atalho para o aplicativo na lista de aplicativos do usuário.

## Perguntas frequentes sobre como trabalhar com domínios personalizados

P: Posso selecionar um certificado já carregado sem carregá-lo novamente? R: Certificados carregados previamente são vinculados automaticamente a um aplicativo e há exatamente um certificado que corresponde ao nome de host do aplicativo.

P: Como fazer para adicionar um certificado e em que formato o certificado exportado deve ser carregado? R: O certificado deve ser carregado por meio da página de configuração do aplicativo. O certificado deve ser um arquivo PFX.

P: Certificados ECC podem ser usados? R: Não há nenhuma limitação explícita para os métodos de assinatura.

P: Certificados SAN podem ser usados? R: Sim.

P: Certificados curinga podem ser usados? R: Sim.

P: É possível usar um certificado diferente em cada aplicativo? R: Sim, a menos que os dois aplicativos compartilhem o mesmo host externo.

P: Se eu registrar um novo domínio, posso usar esse domínio? R: Sim, a lista de domínios é alimentada por meio da lista de domínios verificados do locatário.

P: O que acontece quando um certificado expira? R: Você receberá um aviso na seção do certificado na página de configuração do aplicativo. Quando um usuário tenta acessar o aplicativo, um aviso de segurança é exibido.

P: O que devo fazer se eu quiser substituir um certificado para um determinado aplicativo? R: Carregar um novo certificado da página de configuração de aplicativo.

P: Posso excluir um certificado e substituí-lo? R: Quando você carregar um novo certificado, se o certificado antigo não estiver em uso por outro aplicativo, ele será excluído automaticamente.

P: O que acontece quando um certificado é revogado? R: Verificações de revogação não são realizadas para certificados. Quando um usuário tenta acessar o aplicativo, dependendo do navegador, um aviso de segurança deve aparecer.

P: Posso usar um certificado autoassinado? A: Sim, os certificados autoassinados são permitidos. Observe que, se você estiver usando uma autoridade de certificação privada, o CDP (ponto de distribuição de lista de revogação de certificados) para o certificado deverá ser público.

P: Existe um lugar para ver todos os certificados para o meu locatário? R: Não há suporte para isso na versão atual.


## Consulte também

- [Publique aplicativos com proxy de aplicativo](active-directory-application-proxy-publish.md)
- [Habilitar logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Adicionar seu nome de domínio personalizado ao Azure AD](active-directory-add-domain.md)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)

<!---HONumber=AcomDC_0622_2016-->