
<properties 
    pageTitle="Carregar uma imagem personalizada"
    description="Saiba como criar uma imagem personalizada para o RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="ericor" />



# Carregar uma imagem personalizada

Agora que você criou sua imagem de modelo personalizada ou atualizou-a com as alterações, é preciso carregar a imagem para a biblioteca de imagens do Azure RemoteApp. Siga estas etapas. 


## Antes de começar

1.      Verifique se a imagem personalizada cumpre os [requisitos de imagem](..\remoteapp-imagereqs) e [requisitos do aplicativo](..\remoteapp-appreqs).
2.      Instale o [módulo PowerShell do Azure](install-configure-powershell.md).

## Passo a passo sobre como carregar a imagem personalizada

1.      Abra o Portal de Gerenciamento do Azure e navegue até a página do RemoteApp.
2.      Na guia **Imagens do modelo**, clique em **Carregar** na parte inferior da página.
4.      Insira um nome amigável para a imagem e especifique o local da conta de armazenamento. Certifique-se de que o local seja o mesmo que o da sua coleção do RemoteApp ou um local em que você deseja criar uma. 
5.      Quando solicitado, baixe o script para seu computador local.
6.      Copie os parâmetros de comando na caixa de texto para a área de transferência.
7.      Abra uma janela elevada do Windows PowerShell  
8.      Na janela do Windows PowerShell com privilégios elevados, navegue para o mesmo diretório para o qual você baixou o script.
9.      Cole o comando copiado e pressione **Enter**.

	O processo de carregamento será iniciado e a duração pode depender de vários fatores, incluindo a velocidade da rede e o tamanho da imagem

11.    Se o carregamento falhar devido a uma interrupção da rede ou problemas semelhantes, você sempre poderá retomar o processo de carregamento. Para retomar um carregamento, execute o script novamente usando a mesma linha de comando.

**Aviso:** nunca modifique o script de carregamento. Verificações específicas foram implementadas para garantir que a imagem cumpra os requisitos de imagem e os requisitos do aplicativo.

## Problemas comuns

- Certifique-se de usar o Windows PowerShell, não o PowerShell do Azure.  Você precisa instalar o módulo PowerShell do Azure, pois determinados módulos são necessários durante o processo de carregamento. 
- Nunca altere o script, as validações existem para sua conveniência.
- Se o arquivo vhd for bloqueado durante o carregamento, copie o arquivo ou mova-o para um novo local e tente novamente. Pode haver algum processo do Windows impedindo o carregamento.  

<!--HONumber=52-->