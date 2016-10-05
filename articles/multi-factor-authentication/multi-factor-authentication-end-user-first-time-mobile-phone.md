<properties
	pageTitle="Usar o telefone celular como método de contato com o Azure MFA"
	description="Esta página mostrará aos usuários como usar o telefone celular como o método de contato principal para o Azure MFA."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Usar o telefone celular como método de contato com o Azure Multi-Factor Authentication

Se desejar usar seu telefone celular como o método de contato principal, você poderá usar este artigo. Ele vai ajudar a configurar a autenticação multifator para usar o telefone celular para uma ligação ou um texto como seu método de contato.

## Para usar o telefone celular como o método de contato
<ol>
<li>Selecione Telefone de Autenticação na lista suspensa</li>

<center>![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)</center>


<li>Especifique o seu país na lista suspensa e insira o número do celular.</li>
<li>Selecione o modo que você prefere usar com o celular — texto ou ligação.</li>
<li>Clique em Próximo.</li>
<li>Clique no botão Verificar Agora. Isso iniciará uma ligação ou enviará um texto para seu celular. Certifique-se de que ele esteja com você. Dependendo do modo selecionado, Texto ou Ligação, sua resposta será diferente. <ul><li>Se o modo de texto foi selecionado, um código de 6 dígitos será enviado como texto para você. Insira esse código na caixa que é exibida no navegador</li> <li>Se o modo de ligação foi selecionado, você receberá uma chamada telefônica. Atenda à ligação usando a tecla # do telefone.</li></ul>
<li>Clique em Avançar</li>
<li>Até aqui, você configurou o método de contato e agora é hora de configurar as senhas de aplicativo para aplicativos que não usam navegador, como o Outlook 2010 ou anterior. Se você não usa esses aplicativos, clique em **Concluído**. Caso contrário, passe para a próxima etapa.
<li>Se estiver usando esses aplicativos, copie a senha de aplicativo fornecida.</li>

<li>Cole a senha que foi copiada na área de transferência no seu aplicativo que não usa navegador. Para ver as etapas de aplicativos individuais, como Outlook e Lync, consulte Como alterar a senha no seu email para a senha de aplicativo e Como alterar a senha no seu aplicativo para a senha de aplicativo.</li>
<li>Clique em Concluído.</li>



<center>![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-phone/app.png)</center>

<!---HONumber=AcomDC_0921_2016-->