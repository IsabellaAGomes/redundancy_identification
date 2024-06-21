# redundancy_identification
Este repositório é resultado da dissertação "Identificação Automática de Redundância em Sentenças: uma Abordagem Morfossintática com Large Language Models (LLM)”, onde são feitos experimentos utilizando os conjuntos de dados ASSIN (http://nilc.icmc.usp.br/assin/) e ASSIN 2 (https://sites.google.com/view/assin2/) em português em conjunto com o modelo pré treinado BERTimbau (https://huggingface.co/neuralmind/bert-base-portuguese-cased). O código permite fazer experimentos para a tarefa binária de identificação de redundância em texto tanto na versão do modelo puramente léxico quanto na versão com informação morfossintática (PoS) adicionada. Para fazer os experimentos com informação morfossintática, é necessário primeiro gerar as etiquetas morfossintáticas através do Porttagger (https://huggingface.co/spaces/Emanuel/porttagger).  Abaixo os passos para reproduzir o experimento.

# 1. Juntar os conjuntos de treino, validação e teste
Os conjuntos de dados ASSIN e ASSIN 2 são disponibilizados em arquivos xml que representam o conjunto de treino, validação e teste. O primeiro passo para reproduzir este experimento é a junção manual de todos estes conjuntos em um único xml. Esse passo deve ser feito tanto para o ASSIN quanto para o ASSIN 2, mas separadamente.

# 2. Gerar as etiquetas morfossintáticas e criação do atributo de redundância
O segundo passo trata-se da geração de etiquetas morfossintáticas para os tokens de todas as sentenças dos dois conjuntos. Para isso execute o arquivo step2_tagger3.ipynb preenchendo a variável input_file_name com o nome do arquivo .xml gerado no passo 1 (como por exemplo “assin2.xml”) e a variável output_file_name com o nome do arquivo de saída que será gerado com um atributo novo contendo as etiquetas morfossintáticas (como por exemplo “assin2-pos.xml”). Além da atribuição das etiquetas morfossintáticas, o algoritmo também irá criar o atributo “redudancy”, atribuindo 0 para sentenças com escala de similaridade inferior a 4 e 1 para sentenças com escala de similaridade 4 ou superior. Fazer download do arquivo de saída para o local desejado.

# 3. Redistribuição dos conjuntos
O passo 3 consiste da redistribuição dos conjuntos em treino (80%) e teste (20%). Para isso, execute o arquivo step_3_separacao_de_conjuntos.ipynb, preenchendo a variável input_file_name com o nome do arquivo gerado no passo 2. Depois preencha com o nome desejado para as variáveis output_training_file_name, output_testing_file_name, output_training_undersamplingA_file_name, output_training_undersamplingB_file_name, output_training_oversamplingA_file_name e output_training_oversamplingB_file_name respectivamente com o nome do conjunto de treino (80%), conjunto de teste (20%), conjunto de treino na versão undersampling (mantendo proporção de 45% para classe 0 e 55% para classe 1), conjunto de treino versão undersampling (mantendo proporção de 65% para classe 0 e 35% para classe 1), conjunto de treino na versão oversampling (mantendo proporção de 45% para classe 0 e 55% para classe 1) e conjunto de treino versoversampling (mantendo proporção de 65% para classe 0 e 35% para classe 1). Fazer download dos arquivos de saída para o local desejado.

# 4. Execução dos experimentos
O passo 4 consiste na execução dos experimentos propriamente ditos, que consistem em: 
	Para treino: carregar o conjunto de teste, converter as sentenças em embeddings usando a biblioteca da hugging face para o BERTimbau. Se o experimento for com PoS, serão gerados os embeddings das etiquetas morfossintáticas, alinhados aos tokens da sentença e concatenados aos embbedings dos tokens para gerar os embeddings finais. Se o experimento for sem PoS, os embeddings dos tokens das sentenças serão os embeddings finais. Os embeddings finais serão passadas para o BERTimbau para treino.
	Para teste: o conjunto de teste é carregado e cinco medidas de avaliação (acurácia, precisão, revocação, F-1 e macro-F1) são calculadas para avaliar o modelo.
 
Para o passo 4, as seguintes variáveis devem ser configuradas: 
- POS_PARAMETER = True para experimento com PoS e False para experimento sem PoS.
- GRAD_PARAMETER = True para experimento onde o gradiente é propagado para camadas mais profundas do modelo e False para experimentos onde o gradiente não é propagado para camadas mais profundas do modelo.
- Execucao = preencher com “TestModel” se o objetivo for apenas usar o algoritmo para teste (neste caso, a versão treinada do modelo deve ser carregada previamente); preencher com “TrainModel” se o objetivo for apenas usar o algoritmo para treinar. Preencher com “TrainTestModel” se o objetivo for apenas usar o algoritmo para treinar e testar.
- conjunto_treino = preencher o nome do conjunto de treino.
- conjunto_teste = preencher com o primeiro conjunto de teste que será utilizado para testar.
- conjunto_teste2 = preencher com o segundo conjunto de teste que será utilizado para testar.
- epochs = preencher com a quantidade de épocas que será utilizada no treinamento.

# Referências
BERTimbau - https://huggingface.co/neuralmind/bert-base-portuguese-cased
ASSIN - http://nilc.icmc.usp.br/assin/
ASSIN 2 - https://sites.google.com/view/assin2/
Porttagger -  https://huggingface.co/spaces/Emanuel/porttagger
