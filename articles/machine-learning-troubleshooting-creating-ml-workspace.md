<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace | Azure" description="Solutions for common issues in creating and connecting to an Azure Machine Learning workspace " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

# Guia de Solução de Problemas: Criando e se conectando a um espaço de trabalho de aprendizado de máquina do Azure

Este guia oferece soluções para alguns desafios encontrados frequentemente ao configurar espaços de trabalho de ML do Azure.

## Proprietário do espaço de trabalho

Quando você cria um novo espaço de trabalho de ML do Azure, a ID inserida no campo PROPRIETÁRIO DO ESPAÇO DE TRABALHO deve ser uma conta válida da Microsoft (antigo Windows Live ID), como <john-contoso@live.com> ou <john-contoso@hotmail.com>. Não pode ser uma conta que não seja da Microsoft, como seu e-mail corporativo. Para criar uma conta gratuita da Microsoft, acesse [www.live.com][].

## Regiões permitidas

Atualmente, o ML do Azure está disponível para visualização pública na região centro-sul dos EUA. Caso sua assinatura não inclua a região centro-sul dos EUA, o erro “Você não tem assinaturas nas regiões permitidas. Regiões permitidas: Centro-sul dos EUA” pode aparecer.

Para solucionar o problema, selecione “Contatar Suporte da Microsoft” (abaixo) a partir do Portal do Azure e selecione **Cobrança** como **TIPO DE SUPORTE** para que a região seja adicionada à sua assinatura. O ML da Azure acrescentará diversas regiões no decorrer do tempo.

![Contatar Suporte da Microsoft][]

## Conta de armazenamento

O serviço de ML do Azure precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente no Centro-Sul dos Estados Unidos ou criar uma nova conta de armazenamento quando criar o novo espaço de trabalho do ML (se tiver uma cota para criar uma nova conta de armazenamento). Para ver se você pode criar uma nova conta de armazenamento, no Portal do Azure, acesse **Configurações** e, depois, **Uso**.

![Criar espaço de trabalho][]

Após o novo espaço de trabalho do ML ter sido criado, você pode entrar no ML Studio usando a conta da Microsoft especificada como conta do proprietário do espaço de trabalho. Caso ocorra o erro “Espaço de trabalho não encontrado”, semelhante à captura de tela abaixo, siga os passos a seguir para solucionar o problema.

![Espaço de trabalho não encontrado][]

1.  Liberar os cookies do navegador.

    Se você usa o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.

    ![Opções da Internet ][]

    Na guia **Geral**, clique em **Excluir…**

    ![Guia Geral][]

    Na caixa de diálogo **Excluir Histórico de Navegação**, certifique-se de que **Cookies e dados de sites** esteja selecionado e clique em **Excluir**.

    ![Excluir cookies][]

2.  Após os cookies terem sido liberados, reinicie o navegador e acesse [][]<https://studio.azureml.net></a>. Quando forem solicitados nome de usuário e senha, insira os dados da mesma conta da Microsoft que você especificou como conta do proprietário do espaço de trabalho.

Nosso objetivo é fazer com que a experiência com o ML do Azure seja a mais simples e tranquila possível. Publique quaisquer comentários ou problemas abaixo ou no [Fórum do ML do Azure][] para nos ajudar a atendê-lo melhor.

  [www.live.com]: http://www.live.com
  [Contatar Suporte da Microsoft]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
  [Criar espaço de trabalho]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
  [Espaço de trabalho não encontrado]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
  [Opções da Internet ]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
  [Guia Geral]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
  [Excluir cookies]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
  []: https://studio.azureml.net
  [Fórum do ML do Azure]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning
