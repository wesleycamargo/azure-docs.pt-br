<properties
   pageTitle="Automação do Azure e Gerenciamento de Nuvem Híbrida : como automatizar a implantação de uma VM no Amazon Web Services"
   description="Automação do Azure e Gerenciamento de Nuvem Híbrida : como automatizar a implantação de uma VM no Amazon Web Services"
   services="automation"
   documentationCenter=""
   authors="tiander"
   manager="stevenka"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="tiandert; bwren" />

# Automação do Azure e gerenciamento de nuvem híbrida: como automatizar a implantação de uma VM no Amazon Web Services

Durante nossos eventos [Cloud Roadshow](https://microsoftcloudroadshow.com/cities) em que apresentei a Automação do Azure, houve várias perguntas indagando se esse recurso se destina apenas ao Azure. Felizmente, eu tinha uma demonstração agendada, sobre a qual vou falar sobre neste artigo do blog. Porém, a resposta-chave é que a **Automação do Azure não é apenas para o Azure, é para recursos locais *E* para gerenciar ambientes de nuvem híbrida também!** Assim, por exemplo, se é cliente do AWS (Amazon Web Services), a Automação do Azure também é para você!

## Objetivos

Neste artigo, falarei sobre como aproveitar a Automação do Azure para provisionar uma VM no AWS e dar a ela um nome específico, o que, no mundo do AWS, é conhecido como "marcação" da VM.

## Suposições

Para os fins deste artigo, presumo que você tenha uma conta de Automação do Azure já configurada e também uma assinatura do AWS.

Para obter mais informações sobre como configurar uma conta de Automação do Azure, confira nossa [página de documentação](automation-configuring.md).

## Preparação: armazenamento de credenciais do AWS

Para que a Automação do Azure "fale" com o AWS, você precisará recuperar suas credenciais do AWS e armazená-las como ativos na Automação do Azure.

Quando estiver conectado ao portal do AWS, clique no triângulo pequeno sob seu nome e clique em **Credenciais de Segurança**.

![Credencial de segurança do AWS](./media/automation-aws-deployment/73522ff9-30b5-431c-a3b6-5a33167827ab.png "Credencial de segurança do AWS")

Clique em **Chaves de Acesso**.

![Chaves de Acesso do AWS](./media/automation-aws-deployment/215d76d0-6e7f-4f55-9128-ba618d4514be.jpg "Chaves de Acesso do AWS")

Copie a chave de acesso e a chave de acesso secreta (opcionalmente, baixe o arquivo de chave para armazená-lo em um lugar seguro).

![Criar chaves de acesso do AWS](./media/automation-aws-deployment/81ca1d36-2814-478b-858f-8346f2a28211.png "Criar chaves de acesso do AWS")

## Ativos do AWS na Automação do Azure

Na etapa anterior, você copiou e salvou a **ID da Chave de Acesso** e a **Chave de Acesso Secreta** do AWS. Agora vamos armazená-las na Automação do Azure.

Navegue até a Conta de Automação, selecione **Ativos** - **Credenciais**, adicione uma nova credencial e nomeie-a como **AWScred**.

![imagem](./media/automation-aws-deployment/19c3669d-5259-4566-9479-5dbebb8ac733.png "imagem")

Forneça uma descrição opcional e insira a **ID de acesso** no campo **Nome de usuário** e a **Chave de Acesso Secreta** no campo rotulado como **Senha**. Salve suas credenciais do AWS.

## Módulo do PowerShell do Amazon Web Services

Nosso runbook de provisionamento de VM utilizará o módulo do PowerShell do AWS para fazer seu trabalho. O módulo do PowerShell do AWS é fornecido na [Galeria do PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/) e pode ser facilmente adicionado a uma conta de Automação por meio do botão **Implantar na Automação do Azure**.

![Importação de módulo PS do AWS para AA](./media/automation-aws-deployment/daa07d22-0464-4ec2-8c85-35525f95e5e4.png "Importação de módulo PS do AWS para AA")

Quando você clicar em **Implantar na Automação do Azure**, será levado para a página de logon do Azure e, após fornecer suas credenciais, verá a tela a seguir.

![Assistente de Importação de Módulo do AWS](./media/automation-aws-deployment/575cd4d9-2ca5-4540-869d-3919f91294af.png "Assistente de Importação de Módulo do AWS")

Selecione uma conta de Automação nova ou existente. Observe que não há uma caixa suspensa para uma conta existente. Portanto, não cometa erros de digitação e verifique se a região e o grupo de recursos de sua conta de Automação estão corretos. Execute as outras etapas para concluir a configuração e clique em **Criar**.

Se navegar para a Conta de Automação selecionado e for até **Ativos** - **Módulos**, agora você poderá ver o módulo do AWS com 1427 atividades. Incrível!

>[AZURE.NOTE] A importação de um módulo do PowerShell para a Automação do Azure consiste em duas etapas:
>
> 1.  Importar o módulo
> 2.  Extrair os cmdlets
>
>As atividades só serão mostradas depois que o módulo tiver concluído totalmente a importação e extraído os cmdlets, o que pode levar alguns minutos.

## Provisionar o runbook de VM do AWS

Agora que temos todos os pré-requisitos, é hora de criar o runbook que provisionará uma VM no AWS. Mostraremos também que você pode usar um script do PowerShell nativo na Automação do Azure, em vez do Fluxo de Trabalho do PowerShell.

Mais informações sobre as diferenças entre o PowerShell nativo e o fluxo de trabalho do PowerShell podem ser encontradas [aqui](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/). Informações sobre a criação do runbook gráfico podem ser encontradas [aqui](https://azure.microsoft.com/blog/azure-automation-graphical-and-textual-runbook-authoring/).

O script do PowerShell para o runbook pode ser baixado da [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).

Para baixar o script, abra uma sessão do PowerShell e digite: **Save-Script -Name New-AwsVM -Path <caminho>**

Após salvar o script do PowerShell, você poderá adicioná-lo à Automação do Azure como um runbook fazendo o seguinte:

Na **Conta de Automação**, clique em **Runbooks** e selecione **Adicionar um runbook**. Selecione **Criação Rápida** (Criar um novo runbook), forneça um nome para o runbook e, em **Tipo de Runbook**, selecione **PowerShell**. Em seguida, clique em **Criar**.

![Criar runbook do AWS](./media/automation-aws-deployment/4759ad00-f800-44ba-94be-307cf034a9df.png "Criar runbook do AWS")

Quando o editor de runbook for aberto, copie e cole o script do PowerShell na área de criação do runbook.

![imagem](./media/automation-aws-deployment/7e13addf-cae3-49a5-9d6c-28aa0b7263f4.png "imagem")

Observe o seguinte ao usar o script do PowerShell baixado:

-   O runbook contém diversos valores de parâmetros padrão, conforme mencionado na seção #ToDo. Avalie todos os valores padrão e atualize-os conforme necessário.
-   Verifique se você armazenou as credenciais do AWS em **Ativos**, com o nome **AWScred**.
-   A região que você usará dependerá de sua assinatura do AWS. No portal do AWS onde você também procurou suas credenciais de segurança, clique na seta ao lado de sua Conta para verificar a região.

![Região do AWS](./media/automation-aws-deployment/5a789953-7329-4f0e-9501-e2d3347a8730.png "Região do AWS")

-   Descubra [que região](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) você precisa fornecer no runbook. Por exemplo, minha região é o Oeste dos EUA (Oregon), que se traduz em us-west-2.

![Regiões do AWS](./media/automation-aws-deployment/3ee8065d-0480-4c78-a2e3-6062653e9cb6.png "Regiões do AWS")

-   Você pode obter uma lista de nomes de imagem ao usar o ISE do PowerShell, importar o módulo e fazer a autenticação no AWS substituindo **Get-AutomationPSCredential** no ambiente ISE por **$AwsCred = Get-Credential.** Serão solicitadas as credenciais, e você poderá fornecer a **ID de Chave de Acesso** e a **Chave de Acesso Secreta** como o nome de usuário e a senha. Veja o seguinte exemplo:


		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AWSRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password
		
		# Set up the environment to access AWS
		Set-AWSCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AWSRegion
		
		Get-EC2ImageByName -ProfileName AWSProfile 


Que produzirá este resultado:

![Obter imagens do AWS](./media/automation-aws-deployment/1b1b0e9f-1af0-471c-9f5e-43f4bb29f4ed.png "Obter imagens do AWS")

Copie e cole o nome de imagem de sua preferência em uma variável de automação referenciada no runbook, como **$InstanceType**. Como estou usando uma camada do AWS gratuita, selecionei **t2.micro** no runbook.

Saiba mais sobre os tipos de instâncias EC2 da Amazon [aqui](https://aws.amazon.com/ec2/instance-types/).

## Como testar o provisionamento do runbook de VM do AWS

Lista de verificação de simulação:

-   Os ativos para a autenticação no AWS foram criados e denominados como **AWScred**
-   O módulo do PowerShell do AWS foi importado para a Automação do Azure
-   Foi criado um novo runbook e valores de parâmetro foram verificados e atualizados conforme necessário
-   **Registros detalhados de log** e, opcionalmente, **Registros de progresso de log** nas configurações do runbook foram definidos como **Ativado**

Vamos iniciar o novo runbook fornecendo um **VMname.**

![Iniciar runbook New-AwsVM](./media/automation-aws-deployment/31cd9d1d-208e-4e96-897f-05e37bf5ee7d.png "Iniciar runbook New-AwsVM")

Como habilitamos **Registros detalhados de log** e **Registros de progresso de log**, podemos ver os **Fluxos** de saída em **Todos os Logs**.

![Saída de fluxo](./media/automation-aws-deployment/77191c69-37fa-4598-82d8-cf5dbadfaff5.png "Saída de fluxo")

Vamos verificar no AWS:

![VM implantada por console do AWS](./media/automation-aws-deployment/c5a16bd4-6c54-40d6-9811-7d8e6c5ec74d.png "VM implantada por console do AWS")

Ótimo!

Neste artigo, forneci um exemplo de como a Automação do Azure pode ser usada para gerenciar nuvens híbridas, por exemplo, criando uma VM no Amazon Web Services e aplicando uma marca personalizada para dar um nome a ela.

Aproveite a automação e até a próxima vez!

Tiander.

<!---HONumber=AcomDC_0204_2016-->