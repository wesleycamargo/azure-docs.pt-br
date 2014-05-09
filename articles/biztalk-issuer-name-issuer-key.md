<properties linkid="manage-services-biztalk-services-issuer-name-issuer-key" urlDisplayName="Nome e chave do emissor" pageTitle="Nome e chave do emissor nos Serviços BizTalk | Azure" metaKeywords="Serviços BizTalk, BizTalk, nome do emissor, chave do emissor, Azure" description="Saiba como recuperar o nome e a chave do emissor do Barramento de Serviço ou do ACS (Controle de Acesso) nos Serviços BizTalk." metaCanonical="" services="biztalk-services" documentationCenter="" title="Serviços BizTalk: nome e chave do emissor" authors="mandia" solutions="" manager="paulettm" editor="susanjo" />




# Serviços BizTalk: nome e chave do emissor

Os Serviços BizTalk do Azure usam o nome e a chave do emissor do Barramento de Serviço e o nome e a chave do emissor do Controle de Acesso. Especificamente:


<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Tarefa</strong></td>
<td><strong>Qual nome e chave do emissor</strong></td>
</tr>
<tr>
<td>Implantando seu aplicativo do Visual Studio</td>
<td>Nome e chave do emissor do Controle de Acesso</td>
</tr>
<tr>
<td>Configurando o Portal dos Serviços BizTalk do Azure</td>
<td>Nome e chave do emissor do Controle de Acesso</td>
</tr>
<tr>
<td>Criando retransmissões de LOB com os Serviços do Adaptador do BizTalk no Visual Studio</td>
<td>Nome e chave do emissor do Service Bus</td>
</tr>
</table>


Este tópico lista as etapas para recuperar o nome e a chave do emissor. 

## Nome e chave do emissor do Controle de Acesso
O nome e a chave do emissor do Controle de Acesso são usados pelo seguinte:

- Seu aplicativo do Serviço do BizTalk do Azure criado no Visual Studio: para implantar seu aplicativo do Serviço do BizTalk no Visual Studio para Azure, você insere o nome e a chave do emissor do Controle de Acesso. 
- Portal dos Serviços BizTalk do Azure: na primeira vez que você faz logon no Portal dos Serviços BizTalk, você insere o nome do Serviço do BizTalk como o provedor de serviços e insere o nome e a chave do emissor do Controle de Acesso.

### Para recuperar o nome e a chave do emissor do Controle de acesso

1. Faça logon no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação à esquerda, selecione **Serviço do BizTalk**.
3. Selecione o Serviço do BizTalk. 
4. Selecione **Informações de Conexão** na barra de tarefas. O Namespace do Controle de Acesso, o Emissor Padrão (Nome do Emissor) e a Chave Padrão (Chave do Emissor) são listados e podem ser copiados e colados.<br/><br/>
Para resumir:<br/>
Nome do Emissor = Emissor Padrão<br/>
Chave do Emissor = Chave Padrão


Você também pode clicar em **Abrir Portal de Gerenciamento ACS** para recuperar os valores de Controle de Acesso:

1. Faça logon no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação à esquerda, selecione **Serviço do BizTalk**.
3. Selecione o Serviço do BizTalk.
4. Selecione o botão Informações de Conexão e selecione **Abrir Portal de Gerenciamento ACS**.
5. No Portal, em **Configurações de Serviço**, clique em **Identidades de Serviço**. Isso exibe sua Identidade de Serviço, que é o valor do Nome do Emissor do Controle de Acesso. Clique no link da identidade do serviço para consultar a senha, que é o valor da chave do emissor. Os valores podem ser copiados.<br/><br/>
Por exemplo, em **Identidades de Serviço**, você verá "proprietário". O "proprietário" é o nome do emissor de Controle de Acesso. Quando você clica no link "proprietário", você verá a **Senha**. Quando você clica no link "Senha", você verá o valor. O valor dessa Senha é sua Chave do Emissor do Controle de Acesso. <br/><br/>
Para resumir:<br/>
Nome do Emissor = Nome da Identidade do Serviço<br/>
Chave do Emissor = Valor da Senha

No painel de navegação à esquerda, você também pode selecionar **Active Directory** para recuperar os valores do Controle de Acesso. 

<div class="dev-callout">
<b>Importante</b> 
<p>Quando o namespace do Controle de Acesso é criado usando o <strong>Active Directory</strong>, uma Identidade de Serviço <strong>não</strong> é criada automaticamente. Quando você provisiona um Serviço do BizTalk, um Namespace de Controle de Acesso, uma Identidade de Serviço chamada "proprietário" (Nome do Emissor), uma Senha (Chave do Emissor) e uma Chave Simétrica são criados automaticamente.</p> 
<p><a href="http://go.microsoft.com/fwlink/p/?LinkID=303942">Como usar o Serviço de Gerenciamento do ACS para configurar identidades de serviço</a> fornece mais informações sobre identidades de serviço do Controle de Acesso.</p>
</div>


## Nome e chave do emissor do Barramento de Serviço
O nome e a chave do emissor do Barramento de Serviço são usados pelos Serviços do Adaptador do BizTalk. No seu projeto de Serviços do BizTalk no Visual Studio, você usa os Serviços do Adaptador do BizTalk para se conectar a um sistema de linha de negócios (LOB) no local. Para se conectar, você cria a retransmissão de LOB e insere os detalhes do seu sistema de LOB. Ao fazer isso, você também insere o nome e a chave do emissor do Barramento de Serviço.

### Para recuperar o nome e a chave do emissor do Barramento de Serviço

1. Faça logon no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação à esquerda, clique em **Barramento de Serviço**.
3. Clique em seu namespace. Na barra de tarefas, clique em **Informações de Conexão**. Isso exibe o **Emissor padrão** (Nome do Emissor) e a **Chave Padrão** (Chave do Emissor). Os valores podem ser copiados.<br/><br/>
Para resumir:<br/>
Nome do Emissor = Emissor Padrão<br/>
Chave do Emissor = Chave Padrão

## Avançar
Tópicos adicionais sobre Serviços BizTalk do Azure:

-  [Instalando o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriais: Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## Consulte também
-  [Como usar o Serviço de Gerenciamento do ACS para configurar identidades de serviço](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços BizTalk: gráfico do status do provisionamento (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Serviços BizTalk: guias Painel, Monitor e Escala (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Serviços BizTalk: backup e restauração (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços BizTalk: limitação (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

