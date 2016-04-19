<properties 
	pageTitle="Exemplo de IoT do MyDriving do Azure – Início Rápido | Microsoft Azure" 
	description="Comece com um aplicativo que seja uma demonstração abrangente de como projetar um sistema de IoT com o Microsoft Azure, incluindo o Stream Analytics, o Aprendizado de Máquina e os Hubs de Eventos." 
	services="" 
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="iot-suite" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2016" 
	ms.author="awills"/>

# Sistema de IoT MyDriving: início rápido

O MyDriving é um sistema que demonstra o design e a implementação de uma solução típica de [IoT](iot-suite-overview.md) (Internet das Coisas), que coleta a telemetria dos dispositivos, processa esses dados na nuvem e aplica o aprendizado de máquina para fornecer uma resposta adaptável. A demonstração registra os dados das suas viagens de carro, usando os dados tanto do seu telefone celular quanto de um adaptador de Diagnóstico Integrado (OBD), que coleta informações do sistema de controle do carro. Ele usa esses dados para fornecer comentários sobre o seu estilo de conduzir em comparação com outros usuários.


![](./media/iot-solution-get-started/image5.png)

A finalidade real do MyDriving é para você começar a criar sua própria solução de IoT. Mas antes disso, vamos fazer você começar com o aplicativo MyDriving em si - como um membro da nossa equipe de usuário de testes. Isso proporciona a você uma experiência do aplicativo e do sistema por trás dele como um consumidor, antes de nos aprofundarmos na arquitetura. Ele também te apresenta ao HockeyApp — uma maneira interessante de gerenciar as distribuições de alfa e beta dos seus aplicativos para os usuários de testes.

## Use a experiência móvel

**Pré-requisitos**:

Dispositivo Android, iOS ou Windows 10.

## Instalação no Android e no Windows Phone 10

Em seu dispositivo:

1.  **Permita o desenvolvimento de aplicativos**

    -   Android: em **Configurações**, **Segurança**, permita aplicativos de **Fontes desconhecidas**.

    -   Windows 10: em **Configurações**, **Atualizações**, **Para desenvolvedores**, defina o **Modo de desenvolvedor**.

2.  **Junte-se à nossa equipe de testes beta**.

    O HockeyApp facilita a distribuição das primeiras versões do seu aplicativo para os usuários de teste.

    Em seu dispositivo móvel:

    -   **Inscreva-se/Entre no** HockeyApp em <https://rink.hockeyapp.net>.
    
        (Se você estiver usando o Windows 10, use o navegador Edge).

        *Participantes do Build 2016* – entre com o mesmo email MSA registrado para a conferência, usando um dos botões da Microsoft. Você já se inscreveu no HockeyApp.

        ![](./media/iot-solution-get-started/image1.png)

3.  **Baixe e instale** o aplicativo daqui:

    -   Android: <http://rink.io/spMyDrivingAndroid>

    -   Windows Phone 10: <http://rink.io/spMyDrivingUWP>

        Há dois itens. Instale o certificado de pessoas confiáveis. Em seguida, instale o aplicativo.
    
*Problemas de inicialização no Windows Phone 10?* Pode ser que seu telefone esteja com uma ou duas atualizações atrasadas. Verifique se você tem as atualizações mais recentes, ou instale:

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 
 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 
 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx) 
   

## Instalação do iOS

### Participantes do Build 2016

Se você estiver no Build 2016, baixe o aplicativo como um membro da nossa equipe de testes no HockeyApp.

1.  Em seu dispositivo iOS, entre no <https://rink.hockeyapp.net>. Use um dos botões de credenciais do Microsoft e entre com o mesmo email MSA registrado com a conferência. (Não use os campos de email e senha).

    ![](./media/iot-solution-get-started/image1.png)

2.  No painel do HockeyApp, selecione o MyDriving e baixe-o.

3.  Autorize a versão beta do HockeyApp:

    Acesse **Configurações** & gt;**Geral** & gt;**Gerenciamento de Perfis e Dispositivos.**

    Confie no certificado **Bit Stadium GmbH**.

### Se você não participou do Build 2016

Lançaremos o aplicativo na iOS Store em breve.

Por enquanto, você pode compilar e implantar o aplicativo por conta própria:

-   Baixe o código [do GitHub].

-   Compile e implante [usando o Xamarin].

Descubra mais detalhes no [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs).

## Obtenha um Adaptador OBD (opcional)

Essa é a parte que faz deste um sistema de Internet das Coisas real! Você pode usar o aplicativo sem um sistema, mas é mais divertido com o sistema real, e ele não é caro.

O OBD (Diagnóstico interno) é o recurso do seu carro que a garagem usa para ajustá-lo e diagnosticar ruídos estranhos e lâmpadas de aviso. A menos que seu carro seja muito antigo, você encontrará um soquete sob o painel. Com o conector à direita, você pode obter a métrica de desempenho do mecanismo e fazer alguns ajustes. Um conector OBD pode ser adquirido por um valor barato em locais comuns. Ele se conecta usando Bluetooth ou WiFi para um aplicativo em seu telefone.

Mas, nesse caso, vamos conectar seu carro na nuvem. OK, a conexão direta do OBD é com seu telefone; mas nosso aplicativo funciona como uma retransmissão. A telemetria do seu carro é enviada diretamente para o Hub IoT do MyDriving, no qual ela é processada para registrar suas viagens e avaliar seu estilo de conduzir.

### Conectar um dispositivo OBD


1.  Verifique se o seu carro tem o conector OBD (provavelmente tem, a menos que você goste de carros antigos). Ele fica localizado em algum lugar na cabine, normalmente atrás de uma aba sob o painel.

2.  Obtenha um adaptador OBD. Usamos esses tipos:

    Se estiver usando:

    -   um telefone **Android ou Windows**, você precisará de um adaptador **habilitado para Bluetooth OBD II**. Usamos a [ferramenta de digitalização BAFX Products 34t5 Bluetooth OBDII].

    -   um telefone **iOS**, você precisará de um adaptador **habilitado para WiFi**. Usamos a [ferramenta de digitalização OBDLink MX Wi-Fi: scanner de adaptador/diagnóstico OBD].

3.  Siga as instruções que acompanham o seu adaptador OBD para conectá-lo ao seu telefone.

    -   Um adaptador Bluetooth deve ser pareado com o telefone, na página Configurações.

    -   Um adaptador WiFi deve ter um endereço no intervalo 192.168.xxx.xxx.

4.  Se você tiver vários carros, você poderá obter um adaptador separado para cada (no máximo três).

Se você não tiver um adaptador OBD, o aplicativo ainda enviará os dados de localização e velocidade do receptor GPS do telefone para o back-end e perguntará se você deseja simular um OBD.

Você pode saber mais sobre como o aplicativo usa os dados do adaptador OBD e as opções para criar seu próprio dispositivo OBD na seção 2.1 “Dispositivos de IoT” no [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs).

## Usando o aplicativo

**Inicie** o aplicativo. Há um guia de Início Rápido para orientar você sobre como ele funciona.

-   **Acompanhe suas viagens.** Toque no botão de registro (círculo vermelho grande na parte inferior da tela) para iniciar uma viagem e toque novamente para encerrá-la.


    ![](./media/iot-solution-get-started/image2.png)

-   Toda vez que iniciar uma viagem e, se não houver nenhum dispositivo OBD, será perguntado se você deseja usar o simulador.

-   No final de uma viagem, clique no botão Parar, e você obterá um resumo:

    ![](./media/iot-solution-get-started/image3.png)

-   **Examine suas viagens:**

    ![](./media/iot-solution-get-started/image4.png)

-   **Examine seu perfil**:

    ![](./media/iot-solution-get-started/image5.png)

-   **Envie-nos seus comentários sobre o teste** usando o botão no aplicativo ou balançando seu telefone! Isso automaticamente anexa uma captura de tela para que saibamos sobre o que você está falando. E, se houver eventuais falhas, o HockeyApp coletará os registros delas para nos informar sobre o ocorrido.

## Comentários 

Uma vez que criamos o MyDriving para ajudar a iniciar rapidamente seus próprios sistemas de IoT, certamente desejamos saber de você como ele funciona. Fale conosco se você enfrentar problemas ou desafios, se houver um ponto de extensão que seria mais adequado para o seu cenário, se você encontrar uma maneira mais eficiente para cumprir com determinadas necessidades ou se tiver sugestões para melhorar o MyDriving ou essa documentação.

No aplicativo MyDriving em si, você pode usar o mecanismo interno de comentários HockeyApp: no iOS e no Android, basta balançar seu telefone ou usar o comando de menu Comentários. Você também pode fornecer comentários por meio do [portal HockeyApp].

Você também pode arquivar um [problema no GitHub] ou deixar um comentário abaixo (edição pt-br).

Aguardamos seu contato!

## Próximas etapas

-   Explore o [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs) para entender como projetamos e criamos todo o sistema do MyDriving.

-   [Crie e implante um sistema por conta própria](iot-solution-build-system.md) usando nossos scripts do Azure Resource Manager. O [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs) também orienta você através das áreas nas quais você fará a maioria das personalizações.

  [do GitHub]: https://github.com/Azure-Samples/MyDriving
  [usando o Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [ferramenta de digitalização BAFX Products 34t5 Bluetooth OBDII]: http://www.amazon.com/gp/product/B005NLQAHS
  [ferramenta de digitalização OBDLink MX Wi-Fi: scanner de adaptador/diagnóstico OBD]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [portal HockeyApp]: https://rink.hockeyapp.org
  [problema no GitHub]: https://github.com/Azure-Samples/MyDriving/issues

<!---HONumber=AcomDC_0406_2016-->