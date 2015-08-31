Devido ao desenvolvimento contínuo, a versão do SDK do Android instalada no Eclipse talvez não coincida com a versão do código. O SDK do Android referenciado neste tutorial é versão 21, a mais recente no momento desta edição. O número da versão pode aumentar à medida que aparecem novas versões do SDK e é recomendável usar a versão mais recente disponível.

Dois sintomas da incompatibilidade de versão são:

1. Examine o Console do Eclipse no painel inferior. Você pode ver mensagens de erro do formulário "**Não é possível resolver o destino ' android n'**".

2. Os objetos Android padrão no código que deve ser resolvido com base em instruções `import` podem gerar mensagens de erro.

Se um deles for exibido, a versão do SDK do Android instalado no Eclipse poderá não coincidir com o destino do SDK do projeto baixado. Para verificar a versão, faça as seguintes alterações:


1. No Eclipse, clique em **Janela** clique em **Gerenciador de SDK do Android**. Se você não tiver instalado a versão mais recente da plataforma do SDK, clique para instalá-la. Anote o número da versão.

2. Abra o arquivo de projeto **AndroidManifest.xml**. Verifique se no elemento **uses-sdk**, **targetSdkVersion** está definido como a versão mais recente instalada. A marcação **uses-sdk** deve ser semelhante a esta:
 
	 	    <uses-sdk
	 	        android:minSdkVersion="8"
	 	        android:targetSdkVersion="21" />
	
3. No Explorador de Pacotes do Eclipse, clique com o botão direito do mouse no nó do projeto, escolha **Propriedades** e, na coluna à esquerda, escolha **Android**. Verifique se a opção **Destino da Compilação do Projeto** está definida como a mesma versão do SDK que o **targetSdkVersion**.

<!---HONumber=August15_HO6-->