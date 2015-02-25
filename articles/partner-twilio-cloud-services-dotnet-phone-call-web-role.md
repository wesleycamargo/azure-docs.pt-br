<properties 
	pageTitle="Como fazer uma chamada telefônica do Twilio (.NET) - Azure" 
	description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Amostras de código gravadas no .NET." 
	services="" 
	documentationCenter=".net" 
	authors="devinrader" 
	manager="twilio" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>




<h1>Como fazer uma chamada telefônica usando o Twilio em uma função web no Azure</h1>

Este guia demonstra como usar o Twilio para fazer uma chamada de uma página da Web hospedada no Azure. O aplicativo resultante solicita valores de chamada telefônica ao usuário, conforme mostrado na seguinte captura de tela.

![Azure call form using Twilio and ASP.NET][twilio_dotnet_basic_form]



<h2>Sumário</h2>
- [Pré-requisitos](#twilio-prereqs)
- [Como: Criar um formulário da web para fazer uma chamada](#howtocreateform)
- [Como: Como criar o código para fazer a chamada](#howtocreatecode)
- [Próximas etapas](#nextsteps)
- [Confira também](#seealso)


<h2><a name="twilio-prereqs"></a>Pré-requisitos</h2>

Você precisará fazer o seguinte para usar o código deste tópico:

1. Obter uma conta e um token de autenticação do Twilio. Para uma introdução ao Twilio, inscreva-se em [https://www.twilio.com/try-twilio][try_twilio]. Você pode avaliar os preços em [http://www.twilio.com/pricing][twilio_pricing]. Para obter informações sobre a API fornecida pela Twilio, consulte [http://www.twilio.com/voice/api][twilio_api].
2. Adicione a biblioteca do .NET do Twilio à sua função web. Consulte "Para adicionar as bibliotecas do Twilio ao seu projeto de função web", posteriormente neste tópico.

Você deve estar familiarizado com a criação de uma função web básica no Azure.

<h2><a name="howtocreateform"></a>Como: Criar um formulário da Web para fazer uma chamada</h2>

<h3><a id="use_nuget"></a>Para adicionar as bibliotecas do Twilio ao seu projeto de função web:</h3>

1.  Abra sua solução no Visual Studio.
2.  Clique com o botão direito do mouse em **Referências**.
3.  Clique em **Gerenciar Pacotes NuGet**.
4.  Clique em **Online**.
5.  Na caixa Pesquisar Online, digite  *twilio*.
6.  Clique em **Instalar** no pacote Twilio.

O código a seguir mostra como criar um formulário Web para recuperar dados do usuário para fazer uma chamada. Neste exemplo, uma função web do ASP.NET chamada **TwilioCloud** é criada.

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

<h2><a id="howtocreatecode"></a>Como: Criar o código para fazer a chamada</h2>
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

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
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

A chamada é feita, e o ponto de extremidade do Twilio, a versão da API e o status da chamada são exibidos. A captura de tela a seguir mostra a saída de uma execução de exemplo.

![Azure call response using Twilio and ASP.NET][twilio_dotnet_basic_form_output]

Mais informações sobre TwiML podem ser localizadas em [http://www.twilio.com/docs/api/twiml][twiml]. Para obter mais informações sobre &lt;Dizer&gt; e outros verbos da Twilio podem ser encontrados em [http://www.twilio.com/docs/api/twiml/say][twilio_say].

<h2><a id="nextsteps"></a>Próximas etapas</h2>
Esse código foi fornecido para mostrar a funcionalidade básica usando o Twilio em uma função web do ASP.NET no Azure. Antes de implantar o Azure na produção, convém adicionar mais tratamento de erro ou outros recursos. Por exemplo:

* Em vez de usar um formulário da Web, você pode usar o armazenamento de Blob do Azure ou uma instância do Banco de Dados SQL do Azure para armazenar números de telefone e texto de chamada. Para obter mais informações sobre como usar blobs no Azure, consulte[Como usar o serviço de armazenamento de Blob do Azure no .NET][howto_blob_storage_dotnet]. Para obter informações sobre como usar o Banco de Dados SQL, consulte [Como usar o Banco de Dados SQL do Azure em aplicativos .NET][howto_sql_azure_dotnet].
* Você pode usar RoleEnvironment.getConfigurationSettings para recuperar a ID da conta e o token de autenticação do Twilio nas definições da configuração da implantação, em vez de embutir valores no código de seu formulário. Para obter informações sobre a classe RoleEnvironment, consulte o [namespace Microsoft.WindowsAzure.ServiceRuntime][azure_runtime_ref_dotnet].
*  Leia as diretrizes de segurança da Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].
* Saiba mais sobre a Twilio em [https://www.twilio.com/docs][twilio_docs].

##<a name="seealso"></a>Confira também
* [Como usar o Twilio para funcionalidades de voz e SMS do Azure](../twilio-dotnet-how-to-use-for-voice-sms/)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /pt-br/develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/pt-br/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/pt-br/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx


<!--HONumber=42-->
