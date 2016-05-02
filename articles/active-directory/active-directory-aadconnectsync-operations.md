<properties
   pageTitle="Sincronização do Azure AD Connect: considerações e tarefas operacionais | Microsoft Azure"
   description="Este tópico descreve as tarefas operacionais para a sincronização do Azure Connect AD e como preparar para operar esse componente."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/14/2016"
   ms.author="andkjell"/>

# Sincronização do Azure AD Connect: considerações e tarefas operacionais
O objetivo deste tópico é descrever as tarefas de operações da sincronização do Azure AD Connect.

## Modo de preparo
O modo de teste pode ser usado para vários cenários, incluindo:

-	Alta disponibilidade.
-	Teste e implantação de novas alterações de configuração.
-	Introdução de um novo servidor e encerramento do antigo.

Com um servidor no modo de preparo, você pode fazer alterações na configuração e visualizar as alterações antes de tornar o servidor ativo. Ele também permite executar sincronização e importação totais para verificar se todas as alterações são esperadas antes de você fazê-las em seu ambiente de produção.

Durante a instalação, você pode selecionar o servidor em **modo de preparo**. Isso tornará o servidor ativo para importação e sincronização, mas não fará qualquer exportação. Um servidor em modo de preparo não irá executar a sincronização de senha ou habilitar write-back de senha, mesmo que você selecione esses recursos. Quando você desabilita o modo de preparo, o servidor inicia a exportação e habilita a sincronização de senha e o write-back de senha (se habilitado).

Um servidor em modo de preparo continuará a receber alterações do Active Directory e do Azure AD. Portanto, ele sempre terá uma cópia das alterações mais recentes e poderá assumir as responsabilidades de outro servidor rapidamente. Se você fizer alterações de configuração no servidor primário, é sua responsabilidade para fazer as mesmas alterações para os servidores em modo de preparo.

Para aqueles com conhecimento das tecnologias mais antigas de sincronização, o modo de preparo é diferente, pois o servidor tem seu próprio banco de dados SQL. Isso permite que o servidor de modo de preparo esteja localizado em um datacenter diferente.

### Verifique a configuração de um servidor
Para aplicar esse método, siga estas etapas:

1. Preparar
2. Importar e sincronizar
3. Verificar
4. Servidor ativo do comutador

**Preparar**

1. Instale o Azure AD Connect, selecione **modo de preparo** e desmarque **Iniciar sincronização** na última página do assistente de instalação. Isso nos permite executar o mecanismo de sincronização manualmente. ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Faça o logon e o logoff e, no menu Iniciar, selecione **Serviço de Sincronização**.

**Importar e sincronizar**

1. Selecione **Conectores** e selecione o primeiro conector com o tipo **Serviços de Domínio do Active Directory**. Clique em **Executar**, selecione **Importação Completa** e **OK**. Faça isso para todos os conectores desse tipo.
2. Selecione o Conector com o tipo **Active Directory do Azure (Microsoft)**. Clique em **Executar**, selecione **Importação Completa** e **OK**.
4. Verifique se Conectores ainda está selecionado e, para cada conector com tipo **Serviços de Domínio do Active Directory**, clique em **Executar**, selecione **Sincronização Delta** e **OK**.
5. Selecione o Conector com o tipo **Active Directory do Azure (Microsoft)**. Clique em **Executar**, selecione **Sincronização Delta** e OK.

Você agora preparou a exportação das alterações para o Azure AD e AD local (se estiver usando implantação híbrida do Exchange). As próximas etapas permitirão que você inspecione o que está prestes a ser alterado antes de realmente começar a exportação para os diretórios.

**Verificar**

1. Inicie um prompt de comando e vá para `%Program Files%\Microsoft Azure AD Sync\bin`
2. Execute: `csexport "Name of Connector" %temp%\export.xml /f:x`<BR/> O nome do Conector pode ser encontrado no serviço de sincronização. Ele terá um nome semelhante a "contoso.com – AAD" do Azure AD.
3. Execute: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Agora você tem um arquivo em % temp % chamado export.csv que pode ser examinado no Microsoft Excel. Esse arquivo contém todas as alterações que estão prestes a ser exportadas.
5. Faça as alterações necessárias na configuração ou nos dados e execute essas etapas novamente (importar, sincronizar e verificar) até o momento estimado para que as alterações a serem exportadas ocorram.

**Noções básicas sobre o arquivo export.csv**

A maior parte do arquivo é autoexplicativa. Algumas siglas para entender o conteúdo:

- OMODT – Object Modification Type. Indica se a operação em nível de objeto é um Add, Update ou Delete.
- AMODT – Attribute Modification Type. Indica se a operação em um nível de atributo é um Add, Update ou Delete.

Se o valor do atributo tiver valores múltiplos, nem todas as alterações serão exibidas. Somente o número de valores adicionados e removidos será visível.

**Servidor ativo do comutador**

1. No servidor atualmente ativo, desligue o servidor (FIM/DirSync/Azure AD Sync) para que ele não exporte para o Azure AD ou defina-o no modo de preparação (Azure AD Connect).
2. Execute o assistente de instalação no servidor no **modo de preparo** e desabilite o **modo de preparo**. ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## Recuperação de desastre
Parte do design de implementação é planejar o que fazer em caso de desastre, quando você pode perder o servidor de sincronização. Há modelos diferentes para uso e qual deles usar dependerá de vários fatores, incluindo:

-	Quão tolerável é para você não poder fazer alterações em objetos no Azure AD durante o tempo de inatividade?
-	Se você usar a sincronização de senha, os usuários aceitarão que devem usar a senha antiga no Azure AD se a alterarem no local?
-	Você tem uma dependência em operações em tempo real, como write-back de senha?

Dependendo das respostas a essas perguntas e da política da sua organização, uma das estratégias abaixo pode ser implementada:

-	Recriar quando necessário.
-	Ter um servidor em espera reserva, conhecido como **modo de preparo**.
-	Usar máquinas virtuais.

Uma vez que a sincronização do Azure AD Connect tem uma dependência em um banco de dados SQL, você também deve examinar a seção Alta Disponibilidade do SQL se não usar o SQL Express, que vem com o Azure AD Connect.

### Recriar quando necessário
Uma estratégia viável é planejar para a recriação do servidor quando necessário. Em muitos casos, a instalação do mecanismo de sincronização, a importação e a sincronização inicial podem ser concluídas em algumas horas. Se não houver um servidor reserva disponível, é possível usar temporariamente um controlador de domínio para hospedar o mecanismo de sincronização.

O servidor do mecanismo de sincronização não armazena qualquer estado sobre os objetos para que o banco de dados possa ser reconstruído com os dados no Active Directory e no Azure AD. O atributo **sourceAnchor** é usado para unir os objetos do local e da nuvem. Se você recriar o servidor com objetos locais existentes e a nuvem, o mecanismo de sincronização na reinstalação corresponderá esses dois novamente. As coisas que você precisa documentar e salvar são as alterações de configuração feitas no servidor, como regras de sincronização e de filtragem. Eles devem ser aplicados novamente antes de iniciar a sincronização.

### Ter um servidor em espera reserva - modo de preparo
Se você tiver um ambiente mais complexo, é recomendável ter um ou mais servidores em espera. Durante a instalação, você pode habilitar um servidor em **modo de preparo**.

Para obter mais detalhes, consulte [modo de preparo](#staging-mode).

### Usar máquinas virtuais
Um método comum e com suporte é a execução do mecanismo de sincronização em uma máquina virtual. Se o host tiver um problema, a imagem com o servidor do mecanismo de sincronização pode ser migrada para outro servidor.

### Alta disponibilidade de SQL
Quando não estiver usando o SQL Server Express que vem com o Azure AD Connect, a alta disponibilidade do SQL Server também deve ser considerada. A única solução de alta disponibilidade com suporte é o SQL clustering. Soluções sem suporte incluem espelhamento e Sempre ativo.

## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0420_2016-->