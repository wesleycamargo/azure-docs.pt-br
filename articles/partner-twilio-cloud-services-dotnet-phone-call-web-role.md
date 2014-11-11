<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="MicrosoftHelp@twilio.com; larryf" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; larryf" />

# Como fazer uma chamada telefônica usando a Twilio em uma função web no Azure

Este guia demonstra como usar a Twilio para fazer uma chamada de uma página da web hospedada no Azure. O aplicativo resultante solicita valores de chamada telefônica ao usuário, conforme mostrado na seguinte captura de tela.

![Formulário de chamada do Azure usando a Twilio e o ASP.NET][Formulário de chamada do Azure usando a Twilio e o ASP.NET]

## Sumário

-   [Pré-requisitos][Pré-requisitos]
-   [Como: Criar um formulário da web para fazer uma chamada][Como: Criar um formulário da web para fazer uma chamada]
-   [Como: Como criar o código para fazer a chamada][Como: Como criar o código para fazer a chamada]
-   [Próximas etapas][Próximas etapas]
-   [Consulte também][Consulte também]

## <a name="twilio-prereqs"></a>Pré-requisitos

Você precisará fazer o seguinte para usar o código deste tópico:

1.  Obter uma conta e um token de autenticação da Twilio. Para começar com a Twilio, inscreva-se em [][]<https://www.twilio.com/try-twilio></a>. Você pode avaliar os preços em [][1]<http://www.twilio.com/pricing></a>. Para obter informações sobre a API fornecida pela Twilio, consulte [][2]<http://www.twilio.com/voice/api></a>.
2.  Verifique o número do seu telefone com a Twilio. Para obter informações sobre como verificar o número do seu telefone, consulte [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Como alternativa ao uso de um número existente, você pode adquirir um número de telefone da Twilio.
    Para a finalidade deste exemplo, você usará o número de telefone de área restrita da Twilio para enviar uma mensagem ao telefone verificado. Você pode usar o número de telefone de área restrita apenas para envio a números de telefone verificados.
3.  Adicione a biblioteca do .NET da Twilio à sua função web. Consulte "Para adicionar as bibliotecas da Twilio ao seu projeto de função web", posteriormente neste tópico.

Você deve estar familiarizado com a criação de uma função web básica no Azure.

## <a name="howtocreateform"></a>Como: Criar um formulário da web para fazer uma chamada

### <span id="use_nuget"></span></a>Para adicionar as bibliotecas da Twilio ao seu projeto de função web:

1.  Abra sua solução no Visual Studio.
2.  Clique com o botão direito do mouse em **Referências**.
3.  Clique em **Gerenciar Pacotes NuGet**
4.  Clique em **Online**.
5.  Na caixa Pesquisar Online, digite *tiwlio*.
6.  Clique em **Instalar** no pacote Twilio.

O código a seguir mostra como criar um formulário da web para recuperar dados do usuário para fazer uma chamada. Neste exemplo, uma função web do ASP.NET chamada **TwilioCloud** é criada.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <span id="howtocreatecode"></span></a>Como: Como criar o código para fazer a chamada

O código a seguir, que é chamado quando o usuário preenche o formulário, cria a mensagem de chamada e gera a chamada. Neste exemplo, o código é executado no manipulador de eventos onclick do botão no formulário. (Use sua conta e o token de autenticação da Twilio em vez dos valores de espaço reservado atribuídos a **accountSID** e **authToken** no código a seguir.)

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

A chamada é feita, e o ponto de extremidade da Twilio, a versão da API e o status da chamada são exibidos. A captura de tela a seguir mostra a saída de uma execução de exemplo.

![Resposta de chamada do Azure usando a Twilio e o ASP.NET][Resposta de chamada do Azure usando a Twilio e o ASP.NET]

Mais informações sobre TwiML podem ser localizadas em [][4]<http://www.twilio.com/docs/api/twiml></a>. Mais informações sobre \<Say\> e outros verbos da Twilio podem ser localizadas em [][5]<http://www.twilio.com/docs/api/twiml/say></a>.

## <span id="nextsteps"></span></a>Próximas etapas

Esse código foi fornecido para mostrar a funcionalidade básica usando a Twilio em uma função web do ASP.NET no Azure. Antes de implantar o Azure na produção, convém adicionar mais tratamento de erros ou outros recursos. Por exemplo:

-   Em vez de usar um formulário da web, você pode usar o armazenamento de Blob do Azure ou uma instância do Banco de Dados SQL do Azure para armazenar números de telefone e texto de chamada. Para obter mais informações sobre como usar blobs no Azure, consulte [Como usar o serviço de armazenamento de Blob do Azure][Como usar o serviço de armazenamento de Blob do Azure]. Para obter informações sobre como usar o Banco de Dados SQL, consulte [Como usar o Banco de Dados SQL do Azure em aplicativos .NET][Como usar o Banco de Dados SQL do Azure em aplicativos .NET].
-   Você pode usar RoleEnvironment.getConfigurationSettings para recuperar a ID da conta e o token de autenticação da Twilio nas definições da configuração da implantação, em vez de embutir valores no código de seu formulário. Para obter informações sobre a classe RoleEnvironment, consulte [Microsoft.WindowsAzure.ServiceRuntime Namespace][Microsoft.WindowsAzure.ServiceRuntime Namespace].
-   Leia as diretrizes de segurança da Twilio em [][6]<https://www.twilio.com/docs/security></a>.
-   Saiba mais sobre a Twilio em [][7]<https://www.twilio.com/docs></a>.

## <a name="seealso"></a>Confira também

-   [Como usar o Twilio para funcionalidades de voz e SMS do Azure][Como usar o Twilio para funcionalidades de voz e SMS do Azure]

  [Formulário de chamada do Azure usando a Twilio e o ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
  [Pré-requisitos]: #twilio-prereqs
  [Como: Criar um formulário da web para fazer uma chamada]: #howtocreateform
  [Como: Como criar o código para fazer a chamada]: #howtocreatecode
  [Próximas etapas]: #nextsteps
  [Consulte também]: #seealso
  []: http://www.twilio.com/try-twilio
  [1]: http://www.twilio.com/pricing
  [2]: http://www.twilio.com/voice/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Resposta de chamada do Azure usando a Twilio e o ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png
  [4]: http://www.twilio.com/docs/api/twiml
  [5]: http://www.twilio.com/docs/api/twiml/say
  [Como usar o serviço de armazenamento de Blob do Azure]: https://www.windowsazure.com/pt-br/develop/net/how-to-guides/blob-storage/
  [Como usar o Banco de Dados SQL do Azure em aplicativos .NET]: https://www.windowsazure.com/pt-br/develop/net/how-to-guides/sql-database/
  [Microsoft.WindowsAzure.ServiceRuntime Namespace]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
  [6]: http://www.twilio.com/docs/security
  [7]: http://www.twilio.com/docs
  [Como usar o Twilio para funcionalidades de voz e SMS do Azure]: ../twilio-dotnet-how-to-use-for-voice-sms/
