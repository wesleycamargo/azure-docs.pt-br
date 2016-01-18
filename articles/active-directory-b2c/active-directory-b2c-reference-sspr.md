<properties
	pageTitle="Visualização do Active Directory B2C do Azure: redefinição de senha por autoatendimento | Microsoft Azure"
	description="Um tópico que demonstra como configurar a redefinição de senha por autoatendimento para os consumidores no Active Directory B2C do Azure"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: configurar a redefinição de senha por autoatendimento para os consumidores

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Esse recurso permite aos consumidores (que tenham se registrado em contas locais) redefinir as respectivas senhas por conta própria. Isso reduz significativamente a sobrecarga da equipe de suporte, principalmente se milhões de consumidores usarem seu aplicativo regularmente. Atualmente, só há suporte para usar um endereço de email verificado como um método de recuperação. Vamos incluir mais métodos de recuperação (número de telefone verificado, perguntas de segurança, etc.) futuramente. Por padrão, o diretório não terá a redefinição de senha por autoatendimento ativada. Use as etapas a seguir para ativá-la:

1. Entre no [Portal clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa ou de estudante, ou a mesma conta da Microsoft que você usou para criar o diretório.
2. Navegue até a extensão do Active Directory na barra de navegação do lado esquerdo.
3. Localize o diretório na guia **Diretório** e clique nele.
4. Clique na guia **Configurar**.
5. Role para baixo até a seção **política de redefinição de senha do usuário** e alterne a opção **usuários habilitados para redefinição de senha** para **SIM**. Observe que a opção **Endereço de Email Alternativo** está selecionada; deixe-a como está.

    ![Redefinição de senha de autoatendimento](./media/active-directory-b2c-reference-sspr/sspr.png)
 
6. Na parte inferior da página, clique em **Salvar**. Pronto!

Para testar, use o recurso "Executar agora" em qualquer política de entrada (que tenha contas locais como um provedor de identidade). Na página de entrada da conta local (em que você insere o endereço de email e a senha ou o nome de usuário e a senha), clique em **Não consegue acessar sua conta?** para verificar a experiência do consumidor.

> [AZURE.NOTE]As páginas de redefinição de senha por autoatendimento podem ser personalizadas usando o [recurso de identidade visual da empresa](active-directory-add-company-branding.md).

<!---HONumber=AcomDC_0107_2016-->