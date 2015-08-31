<properties 
   pageTitle="Usando o Emulator Express para executar e depurar um serviço de nuvem localmente"
   description="Usando o Emulator Express para executar e depurar um serviço de nuvem localmente"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/12/2015"
   ms.author="patshea" />

# Usando o Emulator Express para executar e depurar um serviço de nuvem localmente

##Visão geral

Ao usar o Emulator Express, você poderá testar e depurar um serviço de nuvem sem executar o Visual Studio como um administrador. É possível definir as configurações do projeto para usar o Emulator Express ou o emulador completo, dependendo dos requisitos do seu serviço de nuvem. Para saber mais sobre o emulador completo, consulte [Executar um aplicativo Azure no Emulador de Computação]http://go.microsoft.com/fwlink/p/?LinkId=623050). O Emulator Express foi incluído primeiro no Azure SDK 2.1 e, a partir do Azure SDK 2.3, é o emulador padrão.

## Usando o Emulator Express no Visual Studio IDE

Quando você cria um novo projeto no Azure SDK 2.3 ou posterior, o Emulator Express já está selecionado. No caso de projetos existentes que foram criados com uma versão anterior do SDK, siga estas etapas para selecionar o Emulator Express.

### Para configurar um projeto para usar o Emulator Express

1. No menu de atalho do projeto do Azure, escolha **Propriedades** e, em seguida, escolha a guia **Web**.

1. Em **Servidor de Desenvolvimento Local**, escolha o botão de opção **Usar o IIS Express**. O Emulator Express não é compatível com o Servidor Web do IIS.

1. Em **Emulador**, escolha o botão de opção **Usar o Emulator Express**.

    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## Iniciando o Emulator Express em um prompt de comando

Em um prompt de comando, você pode iniciar a versão expressa do Emulador de Computação do Azure, csrun.exe, usando a opção /useemulatorexpress.

## Limitações

Antes de usar o Emulator Express, você deve estar ciente de algumas limitações:

- O Emulator Express não é compatível com o Servidor Web do IIS.

- O serviço de nuvem pode conter várias funções, mas cada função é limitada a uma instância.

- Você não pode acessar números de porta abaixo de 1000. Por exemplo, se você usar um provedor de autenticação que normalmente usa uma porta abaixo de 1000, talvez seja necessário alterar esse valor para um número de porta acima de 1000.

- As limitações que se aplicam ao Emulador de Computação do Azure também se aplicam ao Emulator Express. Por exemplo, você não pode ter mais de 50 instâncias de função por implantação. Consulte [Executar o aplicativo Azure no Emulador de Computação](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## Próximas etapas

[Depurando serviços de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=623041)

<!---HONumber=August15_HO8-->