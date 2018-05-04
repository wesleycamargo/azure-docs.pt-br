---
title: Usar as Instâncias de Contêiner do Azure como um agente de build Jenkins
description: Saiba como usar as Instâncias de Contêiner do Azure como um agente de build Jenkins.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Usar as Instâncias de Contêiner do Azure como um agente de build Jenkins

As instâncias de contêiner do Azure fornecem um ambiente sob demanda, expansível e isolado para executar carga de trabalho em contêiner. Devido a esses atributos, as instâncias de contêiner do Azure criam uma ótima plataforma para a execução de trabalhos de build Jenkins em grande escala. Este documento percorre a implantação e o uso de um servidor Jenkins pré-configurado com o ACI como um destino de compilação.

Para obter mais informações sobre as Instâncias de Contêiner do Azure, consulte [Sobre Instâncias de Contêiner do Azure][about-aci].

## <a name="deploy-jenkins-server"></a>Implantar o servidor Jenkins

No Portal do Azure, selecione **Criar um recurso** e pesquise **Jenkins**. Selecione a oferta Jenkins com um editor de **Microsoft** e selecione **Criar**.

Insira as informações a seguir no formulário básico e clique em **OK** quando concluído.

- **Nome** - nome para a implantação do Jenkins.
- **Nome de usuário** - esse nome de usuário é usado como usuário administrador da máquina virtual Jenkins.
- **Tipo de autenticação** - É recomendável chave pública SSH. Se selecionado, copie em uma chave pública SSH a ser usada ao efetuar logon na máquina virtual Jenkins.
- **Assinatura** - selecione uma assinatura do Azure.
- **Grupo de recursos** - crie um novo ou selecione um grupo de recursos existente.
- **Local**: selecione um local para o cluster.

![Configurações básicas da implantação do Portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

No formulário de configurações adicionais, preencha os itens a seguir:

- **Tamanho** - Selecione a opção de tamanho apropriado para sua máquina virtual de Jenkins.
- **Tipo de disco de VM** - Especifique HDD (unidade de disco rígido) ou SSD (unidade de estado sólido) para o servidor Jenkins.
- **Rede virtual** - (Opcional) Selecione Rede virtual para modificar as configurações padrão.
- **Sub-redes** - Selecione Sub-redes, verifique as informações e selecione **OK**.
- **Endereço IP público** - A seleção do endereço IP público permite atribuir um nome personalizado, configurar a SKU e o método de atribuição.
- **Rótulo do nome de domínio** - Especifique o valor para a URL totalmente qualificada para a máquina virtual Jenkins.
- **Tipo de versão do Jenkins** - Selecione o tipo de versão desejado nas opções: LTS, build Semanal ou Azure Verificado.

![Configurações adicionais de implantação do Portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

Para integração de entidade de serviço, selecione **Auto(MSI)** para que a [Identidade de Serviço Gerenciada do Azure][managed-service-identity] crie automaticamente uma identidade de autenticação para a instância do Jenkins. Selecione Manual para fornecer as próprias credenciais da entidade de serviço.

Agentes de nuvem configuram uma plataforma baseada em nuvem para trabalhos de build do Jenkins. Para o propósito deste documento, selecione ACI. Com o agente de nuvem ACI, cada trabalho de build do Jenkins é executado em uma Instância de Contêiner do Azure.

![Configurações de integração de nuvem de implantação do Portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

Após concluir as configurações de integração, clique em **OK** e, em seguida, em **OK** novamente no resumo de validação. Clique em **Criar** no resumo dos Termos de uso. O servidor Jenkins demora alguns minutos para implantar.

## <a name="configure-jenkins"></a>Configurar o Jenkins

No Portal do Azure, navegue até o Grupo de Recursos do Jenkins, selecione a máquina virtual Jenkins e anote o nome DNS.

![Instruções de logon do Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

Navegue até o nome DNS da VM do Jenkins e copie a cadeia de caracteres SSH retornada.

![Instruções de logon do Jenkins](./media/container-instances-jenkins/jenkins-portal-04.png)

Abra uma sessão de terminal no sistema de desenvolvimento e cole a cadeia de caracteres SSH da última etapa. Atualize 'username' para o nome de usuário especificado ao implantar o servidor Jenkins.

Uma vez conectado, execute o comando a seguir para recuperar a senha inicial do administrador.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Deixe a sessão SSH e o túnel em execução e navegue até http://localhost:8080 em um navegador. Cole a senha de administrador inicial no campo, como mostra a imagem a seguir. Selecione **Continuar** quando concluído.

![Desbloquear Jenkins](./media/container-instances-jenkins/jenkins-portal-05.png)

Selecione **Instalar plug-ins sugeridos** para instalar todos os plug-ins recomendados do Jenkins.

![Instalar plug-in do Jenkins](./media/container-instances-jenkins/jenkins-portal-06.png)

Crie uma nova conta de usuário administrador. Essa conta é usada para fazer logon e trabalhar com a instância do Jenkins.

![Criar usuário do Jenkins](./media/container-instances-jenkins/jenkins-portal-07.png)

Selecione **Salvar e Concluir** quando terminar e, em seguida, **Começar a usar o Jenkins** para concluir a configuração.

O Jenkins agora está configurado e pronto para compilar e implantar código. Para este exemplo, um aplicativo Java simples é usado para demonstrar uma compilação do Jenkins em Instâncias de Contêiner do Azure.

## <a name="create-build-job"></a>Criar trabalho de build

Ao usar uma imagem de contêiner como um destino de build do Jenkins, é necessário especificar uma imagem que inclua todas as ferramentas necessárias para um build com êxito. Para especificar a imagem, selecione **Gerenciar Jenkins** > **Configurar sistema** e role até a seção **Nuvem**. Para este exemplo, atualize o valor da imagem do Docker para `microsoft/java-on-azure-jenkins-slave`.

Quando terminar, clique em **Salvar** para retornar ao painel do Jenkins.

![Configuração de nuvem do Jenkins](./media/container-instances-jenkins/jenkins-aci-image.png)

Agora, crie um trabalho de build do Jenkins. Selecione **Novo Item**, dê ao projeto de build um nome como `aci-java-demo`, selecione **projeto Freestyle** e clique em **OK**.

![Criar trabalho do Jenkins](./media/container-instances-jenkins/jenkins-new-job.png)

Em **Geral**, assegure-se de que **Restringir onde este projeto pode ser executado** está selecionado. Insira `linux` para a expressão de rótulo. Essa configuração assegura que esse trabalho de build execute na nuvem do ACI.

![Criar trabalho do Jenkins](./media/container-instances-jenkins/jenkins-job-01.png)

Em gerenciamento de código-fonte, selecione `Git` e insira `https://github.com/spring-projects/spring-petclinic.git` para a URL do repositório. Este repositório do GitHub contém o código do aplicativo de exemplo.

![Adicionar o código-fonte ao trabalho do Jenkins](./media/container-instances-jenkins/jenkins-job-02.png)

Em Build, selecione **adicionar um passo de build** e selecione `Invoke top-level Maven targets`. Insira `package` como a meta da etapa de build.

![Adicionar etapa de build do Jenkins](./media/container-instances-jenkins/jenkins-job-03.png)

Selecione **Salvar** quando terminar.

## <a name="run-the-build-job"></a>Executar o trabalho de build

Para testar o trabalho de build e observar as Instâncias de Contêiner do Azure como a plataforma de build, inicie um build manualmente.

Selecione **Compilar Agora** para iniciar um trabalho de build. Demora alguns minutos para o trabalho iniciar, quando em execução, você deverá ver o status semelhante às imagens a seguir.

![Adicionar etapa de build do Jenkins](./media/container-instances-jenkins/jenkins-job-status.png)

Enquanto o trabalho estiver em execução, abra o Portal do Azure e analise o grupo de recursos do Jenkins. Você deverá ver que uma Instância de Contêiner do Azure foi criada. É nesta instância que o trabalho do Jenkins está em execução.

![Builds dos Jenkins no ACI](./media/container-instances-jenkins/jenkins-aci.png)

Como o Jenkins executa mais trabalhos do que o número configurado de executores do Jenkins (padrão 2), várias Instâncias de Contêiner do Azure são criadas.

![Builds dos Jenkins no ACI](./media/container-instances-jenkins/jenkins-aci-multi.png)

Depois que todos os trabalhos de build forem concluídos, as instâncias de contêiner do Azure serão removidas.

![Builds dos Jenkins no ACI](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Jenkins no Azure, consulte, [Azure e Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md