---
title: 'Exemplo do Azure IoT do MyDriving: Início rápido | Microsoft Docs'
description: Introdução a um aplicativo que apresenta uma demonstração abrangente de como projetar um sistema de IoT usando o Microsoft Azure, incluindo o Stream Analytics, o Aprendizado de Máquina e os Hubs de Eventos.
services: ''
documentationcenter: .net
suite: ''
author: harikmenon
manager: douge

ms.service: multiple
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2016
ms.author: harikm

---
# Sistema de IoT MyDriving: início rápido
O MyDriving é um sistema que demonstra o design e a implementação de uma solução típica de [IoT](iot-suite-overview.md) (Internet das Coisas), que coleta a telemetria dos dispositivos, processa esses dados na nuvem e aplica o aprendizado de máquina para fornecer uma resposta adaptável. A demonstração registra os dados das suas viagens de carro, usando os dados tanto de seu telefone celular quanto de um adaptador que coleta informações do sistema de controle do carro. Ele usa esses dados para fornecer comentários sobre o seu estilo de conduzir em comparação com outros usuários.

A finalidade real do MyDriving é ensiná-lo a criar sua própria solução de IoT. Mas antes disso, vamos apresentá-lo ao uso do aplicativo MyDriving – como membro de nossa equipe de usuário de teste. Isso proporciona a você uma experiência do aplicativo e do sistema por trás dele como um consumidor, antes de se aprofundar na arquitetura. Ele também apresenta o HockeyApp – uma maneira interessante de gerenciar as distribuições de alfa e beta de seus aplicativos para usuários de teste.

## Usar a experiência móvel
Você poderá usar o aplicativo MyDriving se tiver um dispositivo Android, iOS ou Windows 10.

### Instalação do Android e do Windows Mobile 10
Em seu dispositivo:

1. Permitir aplicativos de desenvolvimento:
   
   * Android: em **Configurações** > **Segurança**, permita aplicativos de **Fontes desconhecidas**.
   * Windows 10: em **Configurações**, **Atualizações**, **Para Desenvolvedores**, defina o **Modo de desenvolvedor**.
2. Junte-se à nossa equipe de teste beta inscrevendo-se ou entrando no [HockeyApp](https://rink.hockeyapp.net). O HockeyApp facilita a distribuição das primeiras versões do seu aplicativo para os usuários de teste.
   
   Se estiver usando o Windows 10, use o navegador Edge.
   
   Se você foi um participante do Build 2016, entre com o mesmo email da conta da Microsoft registrada para a conferência, usando um dos botões da Microsoft. Você já se inscreveu no HockeyApp.
   
   ![Tela de entrada do HockeyApp](./media/iot-solution-get-started/image1.png)
3. Baixe e instale o aplicativo aqui:
   
   * [Android](http://rink.io/spMyDrivingAndroid)
   * [Windows 10](http://rink.io/spMyDrivingUWP)
   
   Há dois itens. Instale o certificado em **Pessoas Confiáveis**. Em seguida, instale o aplicativo.

*Problemas ao iniciar o aplicativo no Windows Mobile 10?* Talvez seu telefone esteja com uma ou duas atualizações atrasadas. Verifique se você tem as atualizações mais recentes, ou instale:

* [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx)
* [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx)
* [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)

### Instalação do iOS
Se você participou no Build 2016, baixe o aplicativo como membro de nossa equipe de teste no HockeyApp:

1. Em seu dispositivo iOS, entre no [HockeyApp](https://rink.hockeyapp.net). Use um dos botões de entrada da Microsoft e entre com o mesmo email da conta da Microsoft registrado para a conferência. (Não use os campos de email e senha).
   
   ![Tela de entrada do HockeyApp](./media/iot-solution-get-started/image1.png)
2. No painel do HockeyApp, selecione MyDriving e baixe-o.
3. Autorize a versão beta do HockeyApp:
   
   a. Vá para **Configurações** > **Geral** > **Gerenciamento de Perfis e Dispositivos.**
   
   b. Confie no certificado **Bit Stadium GmbH**.

Se você não participou no Build 2016, é possível criar e implantar o aplicativo por conta própria:

1. Baixe o código [no GitHub].
2. Crie e implante [usando o Xamarin].

Encontre mais detalhes no [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs).

## Obter um adaptador OBD (opcional)
Essa é a parte que faz deste um sistema de Internet das Coisas real! Você pode usar o aplicativo sem um sistema, mas é mais divertido com o sistema real, e ele não é caro.

O OBD (Diagnóstico interno) é o recurso do seu carro utilizado pela garagem para ajustá-lo e diagnosticar ruídos estranhos e lâmpadas de aviso. A menos que seu carro seja uma verdadeira antiguidade, você encontrará um soquete em algum lugar na cabine, normalmente, atrás de uma aba sob o painel. Com o conector à direita, você pode obter a métrica de desempenho do mecanismo e fazer alguns ajustes. Um conector OBD pode ser adquirido por um valor barato em locais comuns. Ele se conecta usando Bluetooth ou Wi-Fi a um aplicativo em seu telefone.

Nesse caso, vamos conectar seu carro na nuvem. A conexão direta do OBD é feita com seu telefone, mas nosso aplicativo funciona como uma retransmissão. A telemetria do seu carro é enviada diretamente para o hub IoT do MyDriving, no qual é processada para registrar suas viagens e avaliar seu estilo de conduzir.

Para conectar um dispositivo OBD:

1. Verifique se seu carro tem um soquete OBD.
2. Obtenha um adaptador OBD:
   
   * Se estiver usando um telefone Android ou Windows, você precisará de um adaptador OBD II habilitado para Bluetooth. Usamos a [Ferramenta de Digitalização BAFX Products 34t5 Bluetooth OBDII].
   * Se estiver usando um telefone iOS, você precisará de um adaptador OBD habilitado para Wi-Fi. Usamos a [Ferramenta de Digitalização OBDLink MX Wi-Fi: scanner de adaptador/diagnóstico OBD].
3. Siga as instruções que acompanham o adaptador OBD para conectá-lo ao seu telefone. Lembre-se:
   
   * Um adaptador Bluetooth deve ser emparelhado com o telefone, na página **Configurações**.
   * Um adaptador Wi-Fi deve ter um endereço no intervalo 192.168.xxx.xxx.
4. Se tiver vários carros, você poderá obter um adaptador separado para cada (no máximo três).

Se não tiver um adaptador OBD, o aplicativo ainda enviará os dados de localização e velocidade do receptor GPS do telefone para o back-end e perguntará se você deseja simular um OBD.

É possível saber mais sobre como o aplicativo usa os dados do adaptador OBD e as opções para criar seu próprio dispositivo OBD na seção 2.1 “Dispositivos IoT” no [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs).

## Usar o aplicativo
Inicie o aplicativo. Há um guia de Início Rápido para orientar você sobre como ele funciona.

### Acompanhar suas viagens
Toque no botão Gravar (círculo vermelho grande na parte inferior da tela) para iniciar uma viagem e toque novamente para encerrá-la.

![Ilustração do botão Gravar para o acompanhamento da viagem](./media/iot-solution-get-started/image2.png)

Toda vez que iniciar uma viagem e, se não houver nenhum dispositivo OBD, será perguntado se você deseja usar o simulador.

Ao final de uma viagem, clique no botão Parar, e você obterá um resumo.

![Exemplo de um resumo de viagem](./media/iot-solution-get-started/image3.png)

### Examinar suas viagens
![Exemplo de uma viagem anterior](./media/iot-solution-get-started/image4.png)

### Examinar seu perfil
![Exemplo de um perfil de estilo de conduzir](./media/iot-solution-get-started/image5.png)

## Envie-nos seus comentários sobre o teste
Uma vez que criamos o MyDriving para ajudar a iniciar rapidamente seus próprios sistemas de IoT, certamente desejamos saber de você como ele funciona. Fale conosco se:

* Você encontra dificuldades ou desafios.
* Há um ponto de extensão que tornaria isso mais adequado ao seu cenário.
* Você encontra uma maneira mais eficiente de atender a determinadas necessidades.
* Você tem outras sugestões para melhorar o MyDriving ou essa documentação.

No próprio aplicativo MyDriving, é possível usar o mecanismo interno de comentários do HockeyApp: no iOS e Android, basta balançar seu telefone ou usar o comando de menu **Comentários**. Isso automaticamente anexa uma captura de tela para que saibamos sobre o que você está falando. E, no caso de eventuais falhas, o HockeyApp coletará os logs delas para nos informar sobre o ocorrido. Também é possível fornecer comentários por meio do [portal do HockeyApp].

Também é possível arquivar um [problema no GitHub] ou deixar um comentário abaixo (edição pt-br).

Aguardamos seu contato!

## Próximas etapas
* Explore o [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs) para entender como projetamos e criamos todo o sistema do MyDriving.
* [Crie e implante um sistema por conta própria](iot-solution-build-system.md) usando nossos scripts do Azure Resource Manager. O [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs) também o orienta pelas áreas nas quais fará a maioria das personalizações.

[no GitHub]: https://github.com/Azure-Samples/MyDriving
[usando o Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
[Ferramenta de Digitalização BAFX Products 34t5 Bluetooth OBDII]: http://www.amazon.com/gp/product/B005NLQAHS
[Ferramenta de Digitalização OBDLink MX Wi-Fi: scanner de adaptador/diagnóstico OBD]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
[portal do HockeyApp]: https://rink.hockeyapp.org
[problema no GitHub]: https://github.com/Azure-Samples/MyDriving/issues

<!---HONumber=AcomDC_0713_2016-->