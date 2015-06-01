<properties 
	pageTitle="Criar Serviços BizTalk no portal de gerenciamento | Azure" 
	description="Saiba como provisionar ou criar um BizTalk Service no Portal de gerenciamento do Azure; MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>

# Criar um Serviço do BizTalk usando o portal de gerenciamento do Azure

Este tópico lista as etapas para criar um Serviço do BizTalk do Azure no Portal de Gerenciamento do Azure. Especificamente:

-   [Criar um Serviço do BizTalk][Criar um Serviço do BizTalk]
-   [Etapas pós-provisionamento][Etapas pós-provisionamento]
-   [Requisitos explicados][Requisitos explicados]
-   [Conexões híbridas - Novo!][Conexões híbridas - Novo!]

<div class="dev-callout"> 
<b>Dica</b> 
<p>Para fazer logon no Portal de Gerenciamento do Azure, voc&ecirc; precisa de uma conta e de uma assinatura do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, ser&aacute; poss&iacute;vel criar uma conta de avalia&ccedil;&atilde;o gratuita em quest&atilde;o de minutos. Consulte <a href="http://go.microsoft.com/fwlink/p/?LinkID=239738">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p> 
</div>

## <a name="BizTalk"></a>Criar um Serviço do BizTalk

Depende da Edição que você escolher, é possível que não todas as configurações de Serviço do BizTalk estejam disponíveis.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Na parte inferior do painel de navegação, selecione **NOVO**:
    ![Selecione o botão Novo][Selecione o botão Novo]

3.  Selecione **SERVIÇOS DO APLICATIVO** > **SERVIÇO DO BIZTALK** > **CRIAÇÃO PERSONALIZADA**:
    ![Selecione o Serviço do BizTalk e selecione Criação Personalizada][Selecione o Serviço do BizTalk e selecione Criação Personalizada]

4.  Insira as configurações do Serviço do BizTalk.

    |-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome do Serviço BizTalk** | Você pode inserir qualquer nome, mas seja específico. Alguns exemplos incluem:                                                                                                                                                                                                
                                    *minha_empresa*. biztalk.windows.net                                                                                                                                                                                                                                         
                                    *Meu_Aplicativo_da_Minha_Empresa*.biztalk.windows.net                                                                                                                                                                                                                     
                                    *meu_aplicativo*.biztalk.windows.net                                                                                                                                                                                                                                         
                                    ".biztalk.windows.net" é adicionado automaticamente ao nome que você inserir. Isso cria uma URL que é usada para acessar seu Serviço do BizTalk, como **https://*myapplication*.biztalk.windows.net**.                                                                        |
    | **Edição**                  | Se você estiver em fase de teste/desenvolvimento, escolha **Developer**. Se você estiver na fase de produção, use os [Serviços do BizTalk: Gráfico de edições][Serviços do BizTalk: Gráfico de edições] para determinar se **Premium**, **Standard**, ou **Basic** for a escolha certa para seu cenário de negócios. |
    | **Região**                  | Selecione a região geográfica para hospedar seu Serviço BizTalk.                                                                                                                                                                                                              |
    | **URL do domínio**          | **Opcional**. Por padrão, a URL do domínio é *YourBizTalkServiceName*.biztalk.windows.net. Um domínio personalizado também pode ser inserido. Por exemplo, se seu domínio for *contoso*, você pode inserir:                                                                   
                                    *Minha_Empresa*.contoso.com                                                                                                                                                                                                                                                  
                                    *Meu_Aplicativo_da_Minha_Empresa*.contoso.com                                                                                                                                                                                                                             
                                    *Meu_Aplicativo*.contoso.com                                                                                                                                                                                                                                                 
                                    *YourBizTalkServiceName*.contoso.com                                                                                                                                                                                                                                          |

    Selecione a seta de AVANÇO.

5.  Inserir as configurações de banco de dados e de armazenamento:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Monitorando/arquivando a conta de armazenamento</strong></td>
    <td align="left">Selecione uma conta de armazenamento existente ou selecione Criar uma nova conta de armazenamento.<br /><br /> Se você criar uma nova conta de Armazenamento, digite o <strong>Nome da conta de armazenamento</strong>.</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Banco de Dados de Acompanhamento</strong></td>
    <td align="left">Se você usar um banco de dados SQL do Azure, não poderá ser usado por outro Serviço do BizTalk. Você precisará do nome e da senha de logon inseridos na criação do Banco de Dados SQL Azure.<br /><br />
    <div class="dev-callout"> 
<b>Dica</b> 
<p>Crie o banco de dados de acompanhamento e a Conta de Armazenamento de monitoramento/arquivamento na mesma regi&atilde;o que o Servi&ccedil;o BizTalk.</p> 
</div></td>
    </tr>
    </tbody>
    </table>

    Selecione a seta de AVANÇO.

6.  Insira as configurações do banco de dados:

    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome**                       | Disponível quando a opção **Criar uma nova instância do Banco de Dados SQL** foi selecionada na tela anterior.                                                     
                                       Insira um nome do Banco de Dados SQL a ser usado pelo seu Serviço BizTalk.                                                                                         |
    | **Servidor**                   | Disponível quando a opção **Criar uma nova instância do Banco de Dados SQL** foi selecionada na tela anterior.                                                     
                                       Seleciona um Servidor de banco de dados SQL ou crie um novo servidor de banco de dados SQL.                                                                        |
    | **Nome de Logon do Servidor**  | Digite o nome de usuário do logon.                                                                                                                                 |
    | **Senha de Logon do Servidor** | Digite a senha do logon.                                                                                                                                           |
    | **Região**                     | Disponível quando a opção **Criar uma nova instância do Banco de Dados SQL** está selecionada. Selecione a região geográfica para hospedar seu Banco de Dados SQL. |

Selecione a marca de seleção para concluir o assistente. O ícone de progresso é exibido:
![O ícone de progresso é mostrado ao concluir][O ícone de progresso é mostrado ao concluir]

Ao concluir, o Serviço BizTalk do Azure será criado e estará pronto para seus aplicativos. As configurações padrão são suficientes. Se você desejar alterar as configurações padrão, selecione **SERVIÇOS BIZTALK** no painel de navegação à esquerda e selecione seu Serviço BizTalk. Configurações adicionais são exibidas nas [guias Painel, Monitor e Escala.][guias Painel, Monitor e Escala.] na parte superior.

Dependendo do estado do Serviço BizTalk, existem algumas operações que não podem ser concluídas. Para obter uma lista dessas operações, consulte [Gráfico de estado do serviço BizTalk (a página pode estar em inglês)][Gráfico de estado do serviço BizTalk (a página pode estar em inglês)].

## <a name="PostProv"></a>Etapas pós-provisionamento

-   [Instalar o certificado em um computador local][Instalar o certificado em um computador local]
-   [Adicionar um certificado pronto para produção][Adicionar um certificado pronto para produção]
-   [Obter o namespace do Controle de Acesso][Obter o namespace do Controle de Acesso]

#### <a name="InstallCert"></a>Instalar o certificado em um computador local

Como parte do provisionamento do Serviço do BizTalk, um certificado autoassinado é criado e associado com a assinatura do seu serviço do BizTalk. Você deve baixar este certificado e instalá-lo em computadores de onde você implemente os aplicativos do Serviço do BizTalk ou envie mensagens para um ponto de extremidade do Serviço do BizTalk.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **SERVIÇOS DO BIZTALK** no painel de navegação à esquerda e selecione o seu Serviço BizTalk.
3.  Clique na guia **Painel**.
4.  Clique em **Baixar o certificado SSL**.
    ![Modificar o certificado SSL][Modificar o certificado SSL]
5.  Clique duas vezes no certificado e execute-o com o assistente para instalar o certificado. Certifique-se de instalar o certificado no armazenamento **Autoridades de certificação de raiz confiáveis**.

#### <a name="AddCert"></a>Adicionar um certificado pronto para produção

O certificado autoassinado que é criado automaticamente enquanto os Serviços do BizTalk são provisionados é destinado apenas para o uso em ambientes de desenvolvimento. Para cenários de produção, você deve substituí-lo com um certificado pronto para produção.

1.  Na guia **Painel**, clique em **Atualizar o certificado SSL**.
2.  Procure seu certificado SSL privado (*Nome_do_certificado*.pfx) que inclui o nome do seu Serviço BizTalk, insira a senha e selecione a caixa de seleção.

#### <a name="ACS"></a>Obter o namespace do Controle de Acesso

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Selecione **SERVIÇOS BIZTALK** no painel de navegação à esquerda e selecione o seu Serviço BizTalk.
3.  Na barra de tarefas, selecione **Informação de conexão**:
    ![Selecione a informação de conexão][Selecione a informação de conexão]

4.  Copiar os valores de Controle de Acesso.

Ao implantar um projeto do Serviço BizTalk no Visual Studio, você insere esse namespace do Controle de Acesso. O Namespace do Controle de Acesso é criado automaticamente para seu Serviço BizTalk.

Os valores de Controle de acesso podem ser usados com qualquer aplicativo. Quando os Serviços do BizTalk do Windows Azure estiverem criados, esse namespace do Controle de Acesso controla a autenticação com sua implantação do Serviço BizTalk. Se você desejar alterar a assinatura ou gerenciar o namespace, selecione **ACTIVE DIRECTORY** no painel de navegação à esquerda e selecione seu namespace. A barra de tarefas lista suas opções.

Um clique em **Gerenciar** abre o Portal de Gerenciamento do Controle de Acesso. No Portal de Gerenciamento de Controle de Acesso, o Serviço do BizTalk usa **Identidades de serviço**:
![Identidades de Serviço do ACS no Portal de Gerenciamento de Controle de Acesso][Identidades de Serviço do ACS no Portal de Gerenciamento de Controle de Acesso]

A identidade do serviço do Controle de Acesso é um conjunto de credenciais que permitem que aplicativos ou clientes façam autenticação diretamente com o Controle de Acesso e recebam um token.

**Importante**
O Serviço BizTalk usa **Proprietário** para a identidade do serviço padrão e o valor da **Senha**. Se você usar o valor de Chave Simétrica em vez do valor de Senha, pode ocorrer o seguinte erro:

*Não foi possível conectar à conta do Serviço de Gerenciamento do Controle de Acesso com as credenciais especificadas*

[Gerenciando o namespace de seu ACS (a página pode estar em inglês)][Gerenciando o namespace de seu ACS (a página pode estar em inglês)] lista algumas diretrizes e recomendações.

## <a name="Requirements"></a>Requisitos explicados

Esses requerimentos não se aplicam a Edição Free.

<table border="1">

<tr bgcolor="FAF9F9">

<td>
**O que você precisa**

</td>

<td>
**Por que você o precisa**

</td>

</tr>

<tr>

<td>
Assinatura do Azure

</td>

<td>
A assinatura determina quem pode fazer logon no Portal de Gerenciamento do Azure. O titular da conta cria a assinatura em [Assinaturas do Azure][Assinaturas do Azure].
A conta do Azure pode ter várias assinaturas e pode ser gerenciada por quem for permitido. Por exemplo, o titular da sua conta do Azure cria uma assinatura chamada *BizTalkServiceSubscription* e dá acesso a essa assinatura aos administradores do BizTalk em sua empresa (por exemplo, <ContosoBTSAdmins@live.com>). Neste cenário, os administradores do BizTalk fazem logon no Portal de Gerenciamento do Azure e têm acesso total aos serviços hospedados na assinatura, inclusive os Serviços BizTalk do Azure. Os administradores do BizTalk não são os titulares da conta do Azure e, portanto, não têm acesso às informações de cobrança.
[Gerenciar assinaturas e Contas de Armazenamento no Portal de Gerenciamento do Azure][Gerenciar assinaturas e Contas de Armazenamento no Portal de Gerenciamento do Azure] fornece mais informações.

</td>

</tr>

<tr>

<td>
Banco de Dados SQL Azure

</td>

<td>
Armazena as tabelas, exibições e os procedimentos armazenados usados pelo Serviço do BizTalk, incluindo os dados de Acompanhamento.
Quando você cria um serviço do BizTalk, você pode usar um Servidor SQL do Azure existente, Banco de dados SQL do Azure, ou criar automaticamente um Servidor ou Banco de dados novo.
A escala do Banco de dados SQL é configurada automaticamente. Tipicamente a escala padrão é suficiente para um Serviço BizTalk. Alterar a escala afeta ao preço. Consulte [Contas e faturamento no banco de dados SQL do Azure][Contas e faturamento no banco de dados SQL do Azure]
**Observações**

-   Quando você criar um novo SQL Server e Banco de Dados do Azure, os Serviços do Azure são habilitados automaticamente. O Serviço do BizTalk requer que os Serviços do Azure sejam habilitados.
-   Se você criar um novo Banco de Dados SQL do Azure em um SQL Server do Azure existente, as regras do firewall do servidor não serão alterados. Como resultado, é possível que outros Serviços do Azure não tenham permissão de acesso aos bancos de dados do servidor.

</td>

</tr>

<tr>

<td>
Namespace do Controle de Acesso do Azure

</td>

<td>
Autenticados com os Serviços do BizTalk do Azure. Ao implantar um projeto do Serviço BizTalk no Visual Studio, você insere esse namespace do Controle de Acesso. Quando você cria um Serviço BizTalk, o Namespace do Controle de Acesso é criado automaticamente.

</td>

</tr>
</p>
<tr>
<td>
Conta de Armazenamento do Azure

</td>
<td>
Dá acesso às tabelas, aos blobs e às filas usadas pelos Serviços do BizTalk para fazer o seguinte:

-   Arquivos de log que monitoram o Serviço do BizTalk. A saída do monitoramento também é exibida na guia Monitoramento no Portal de Gerenciamento do Azure.
-   Ao criar um contrato X12 ou AS2 entre parceiros, você pode habilitar o recurso de Arquivamento para armazenar propriedades de mensagens. Esses dados são salvos na Conta de Armazenamento.

 Quando cria um Serviço BizTalk, você pode usar uma Conta de Armazenamento existente ou criar automaticamente uma nova Conta de Armazenamento.
 As configurações de Armazenamento padrão são suficientes para um Serviço BizTalk.
 Quando você cria uma Conta de Armazenamento, uma chave primária e uma chave secundária são criadas automaticamente. Essas chaves controlam o acesso à sua conta de armazenamento. O Serviço BizTalk usa automaticamente a chave primária.
 Consulte [Armazenamento][Armazenamento] para obter mais informações.

</td>
</tr>
<tr>
<td>
Certificado SSL privado

</td>
<td>
Quando um Serviço do BizTalk do Azure for criado, uma URL HTTPS que inclui o nome do seu Serviço do BizTalk também é criado. Esta URL é configurada automaticamente para usar um certificado autoassinado apenas de desenvolvimento. Para produção, você necessita um certificado SSL privado.
 **Informações importantes sobre o certificado SSL**

-   A data de expiração do certificado deve ser menor do que cinco anos.
-   Todos os certificados privados exigem uma senha. Saiba essa senha e, como uma prática recomendada, compartilhe essa senha com seus administradores.
-   Os certificados autoassinados são usados em um ambientes de desenvolvimento/teste. Ao usar certificados autoassinados, importe o certificado para o armazenamento de certificados pessoais e o armazenamento de certificados de Autoridades de Certificação Raiz Confiáveis.

Ao enviar a solicitação de certificado e produção à sua autoridade de certificação, forneça as seguintes propriedades de certificado:

-   **Uso avançado de chave**: No mínimo, os Serviços do BizTalk do Azure exigem a Autenticação de Servidor.
-   **Nome comum**: Insira o nome de domínio totalmente qualificado (FQDN) da sua URL de Serviço do BizTalk do Azure. Consulte [Criar um Serviço do BizTalk][Criar um Serviço do BizTalk] neste tópico.

 Um certificado novo ou diferente pode ser adicionado após o Serviço BizTalk ser criado.

</td>
</tr>
</table>
## <a name="HC"></a>Conexões Híbridas

Quando você cria um Serviço do BizTalk do Azure, a guia **Conexões híbridas** está disponível:

![Guia de Conexões Híbridas][Guia de Conexões Híbridas]

As Conexões Híbridas são usadas para conectar um site do Azure ou um serviço móvel do Azure a um recurso local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs da web HTTP, e os serviços Web mais personalizados. As Conexões híbridas e o Serviço do adaptador do BizTalk são diferentes. O Serviço do Adaptador do BizTalk é usado para conectar os Serviços do BizTalk do Azure a um sistema de Linha de Negócios (LOB) local.

Consulte [Conexões híbridas][Conexões híbridas] para saber mais, incluindo a criação e gerenciamento de Conexões híbridos.

## Avançar

Agora que o Serviço do BizTalk é criado, familiarize-se com os diferentes [Serviços do BizTalk: guias Painel, Monitoramento e Escala][guias Painel, Monitor e Escala.] (a página pode estar em inglês). Seu Serviço BizTalk está pronto para os seus aplicativos. Para começar a criar aplicativos, visite [Serviços BizTalk do Azure (a página pode estar em inglês)][Serviços BizTalk do Azure (a página pode estar em inglês)].

## Consulte também

-   [Serviços do BizTalk: Tabela de edições][Serviços do BizTalk: Gráfico de edições]
-   [Serviços do BizTalk: Gráfico de estado][Gráfico de estado do serviço BizTalk (a página pode estar em inglês)]
-   [Serviços do BizTalk: Backup e restauração][Serviços do BizTalk: Backup e restauração]
-   [Serviços do BizTalk: limitação][Serviços do BizTalk: limitação]
-   [Serviços do BizTalk: nome e chave do emissor][Serviços do BizTalk: nome e chave do emissor]
-   [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)][Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]
-   [Conexões Híbridas][Conexões híbridas]

  [Criar um Serviço do BizTalk]: #BizTalk
  [Etapas pós-provisionamento]: #PostProv
  [Requisitos explicados]: #Requirements
  [Conexões híbridas - Novo!]: #HC
  [Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Selecione o botão Novo]: ./media/biztalk-provision-services/WABS_New.png
  [Selecione o Serviço do BizTalk e selecione Criação Personalizada]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
  [Serviços do BizTalk: Gráfico de edições]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [O ícone de progresso é mostrado ao concluir]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
  [guias Painel, Monitor e Escala.]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Instalar o certificado em um computador local]: #InstallCert
  [Adicionar um certificado pronto para produção]: #AddCert
  [Obter o namespace do Controle de Acesso]: #ACS
  [Modificar o certificado SSL]: ./media/biztalk-provision-services/WABS_QuickGlance.png
  [Selecione a informação de conexão]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
  [Identidades de Serviço do ACS no Portal de Gerenciamento de Controle de Acesso]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
  [Assinaturas do Azure]: https://account.windowsazure.com/Subscriptions
  [Gerenciar assinaturas e Contas de Armazenamento no Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=267577
  [Contas e faturamento no banco de dados SQL do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=234930
  [Armazenamento]: http://go.microsoft.com/fwlink/p/?LinkID=285671
  [Guia de Conexões Híbridas]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
  [Conexões híbridas]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Serviços do BizTalk: Backup e restauração]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Serviços do BizTalk: limitação]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Serviços do BizTalk: nome e chave do emissor]: http://go.microsoft.com/fwlink/p/?LinkID=303941

<!--HONumber=46--> 
