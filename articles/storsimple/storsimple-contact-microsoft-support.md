<properties 
   pageTitle="Contatar o Suporte da Microsoft | Microsoft Azure"
   description="Saiba como criar uma solicitação de suporte e iniciar uma sessão de suporte em seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="alkohli" />

# Contatar o Suporte da Microsoft

Se tiver problemas com sua solução Microsoft Azure StorSimple, você poderá criar uma solicitação de serviço de Suporte Técnico. Em uma sessão online com seu Engenheiro de Suporte, talvez você precise iniciar uma sessão de suporte em seu dispositivo StorSimple. Este artigo o orienta durante o processo de criação de uma solicitação de suporte e também para iniciar uma sessão de suporte na interface do Windows PowerShell de seu dispositivo StorSimple.

## Criar uma solicitação de suporte

Execute as seguintes etapas para criar uma solicitação de suporte:

#### Para criar uma solicitação de suporte

1. Uma solicitação de Suporte pode ser criada por meio do [Portal de Gerenciamento](http://manage.windowsazure.com/). No [Portal de Gerenciamento](http://manage.windowsazure.com/), clique em seu **Nome de Conta** e, em seguida, clique em **Contatar o Suporte da Microsoft**.

	![Contate o Suporte da MS por meio do Portal de Gerenciamento](./media/storsimple-contact-microsoft-support/IC777286.png)

1. Na caixa de diálogo **Contatar o Suporte da Microsoft**:


	- No menu suspenso, selecione o destino **Assinatura** associado ao serviço StorSimple Manager. Especifique **Tipo de Suporte** como **Técnico**. É necessário um plano de Suporte pago para habilitar o Suporte Técnico.

	1. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-contact-microsoft-support/IC740895.png) para **Criar Tíquete**.

1. Na janela **Suporte da Microsoft**, no menu suspenso **Produto**, escolha **StorSimple**.

	![Contatar o Suporte da Microsoft - Produto](./media/storsimple-contact-microsoft-support/IC777288.png)

1. Siga as instruções na tela para classificar corretamente sua solicitação e forneça uma descrição clara e específica do problema.

Depois que você enviar sua solicitação, um engenheiro de Suporte entrará em contato com você assim que possível para prosseguir com sua solicitação.

## Iniciar uma sessão de suporte no Windows PowerShell para StorSimple

Para solucionar problemas que possam ocorrer com o dispositivo StorSimple, você precisará contatar a equipe de Suporte da Microsoft. O Suporte da Microsoft talvez precise usar uma sessão de suporte para fazer logon em seu dispositivo.

Execute as seguintes etapas para iniciar uma sessão de suporte:

#### Para iniciar uma sessão de suporte

1. Acesse o dispositivo diretamente usando o console serial ou por meio de uma sessão de telnet de um computador remoto. Para fazer isso, siga as etapas em [Usar PuTTY para se conectar ao console serial do dispositivo](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

1. Na sessão que será aberta, pressione a tecla **Enter** para obter um prompt de comando.

1. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**.

1. No prompt, digite a seguinte senha:

	`Password1`

1. No prompt, digite o seguinte comando:

	`Enable-HcsSupportAccess`

1. Uma cadeia de caracteres criptografada será apresentada a você. Copie a cadeia de caracteres para um editor de texto como o Bloco de Notas.

1. Salve a cadeia de caracteres e envie-a em uma mensagem de email ao Suporte da Microsoft.

> [AZURE.IMPORTANT]Você pode desabilitar o acesso ao suporte executando `Disable-HcsSupportAccess`. O dispositivo StorSimple também tentará desabilitar o acesso ao suporte oito horas após a sessão ser iniciada. É uma prática recomendada alterar as credenciais de seu dispositivo StorSimple após iniciar uma sessão de suporte.

<!---HONumber=August15_HO6-->