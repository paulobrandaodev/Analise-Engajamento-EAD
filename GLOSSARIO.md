# Glossário do Projeto — Engajamento em EaD com Visão Computacional

> Explicado para quem tem 16 anos: sem jargão, com analogias do dia a dia.

## Dados e o problema

**Dataset (conjunto de dados)**
Uma coleção organizada de exemplos que o computador usa pra aprender. Pense numa caixa de flashcards de estudo: cada cartão é um exemplo, e quanto mais cartões (e mais variados), melhor você aprende. Aqui, o "dataset" é o DAiSEE: milhares de vídeos de alunos assistindo aula, cada um "rotulado" com o nível de engajamento.

**Rótulo (label)**
A resposta certa que acompanha cada exemplo, tipo o gabarito de uma prova. No DAiSEE, cada vídeo tem 4 rótulos: Tédio (Boredom), Engajamento (Engagement), Confusão (Confusion) e Frustração (Frustration), cada um de 0 a 3.

**Variável ordinal**
Uma nota que tem ordem, mas não é uma régua perfeita. Tipo o "nível de dificuldade" de uma fase de jogo: Fácil < Médio < Difícil < Insano. Sabemos a ordem, mas não sabemos se "Insano" é exatamente 2x mais difícil que "Médio". O engajamento (0 a 3) é assim.

**Classes desbalanceadas / desbalanceamento**
Quando um tipo de exemplo aparece muito mais que outro. Imagine uma sala de aula em que 94% dos alunos tiram nota boa e só 6% tiram nota ruim — se você "chutar sempre nota boa", acerta quase sempre, mas nunca identifica quem está com dificuldade. É exatamente o problema aqui: quase metade dos vídeos é nível 2, quase metade é nível 3, e os níveis 0 e 1 (baixo engajamento) somam menos de 6%.

**Split (Train / Validation / Test)**
Dividir o material de estudo em três pilhas: uma pra estudar (Train), uma pra fazer simulados e ajustar a estratégia (Validation), e uma reservada só pra prova final, que você não pode ter visto antes (Test). Isso evita "colar" — o modelo não pode ser avaliado com exemplos que já usou pra aprender.

**PII (informação de identificação pessoal)**
Dados que identificam uma pessoa específica (rosto reconhecível, nome, etc.). Um pipeline "sem PII" usa só pontos abstratos do rosto (tipo um boneco de pauzinhos), não a foto real — mais parecido com anotar "o cotovelo do goleiro subiu" do que mostrar o rosto do goleiro.

---

## Visão computacional (como o computador "vê" o rosto)

**Frame**
Uma foto única tirada de um vídeo. Um vídeo é uma sequência de fotos passando rápido (tipo um flipbook/folheto animado). "Extrair frames" é parar o flipbook em páginas específicas e salvar cada uma como imagem.

**Amostragem de frames**
Em vez de guardar TODAS as páginas do flipbook (caro e demorado), você pega só algumas páginas espalhadas (ex.: 20 fotos bem distribuídas ao longo do vídeo) — como tirar 20 fotos de um jogo de futebol de 90 minutos em vez de gravar cada segundo.

**Landmarks faciais (marcos faciais)**
Pontinhos marcados em posições-chave do rosto (cantos dos olhos, ponta do nariz, cantos da boca etc.), tipo os pontos de um "conecte os pontos" desenhado sobre o rosto. O computador rastreia como esses pontos se movem para inferir expressões.

**MediaPipe / MediaPipe FaceMesh**
Uma ferramenta pronta (feita pelo Google) que já sabe encontrar esses pontinhos no rosto automaticamente, tipo um aplicativo que already sabe "achar os olhos e a boca" numa foto, sem você precisar programar isso do zero.

**Iris (tracking de íris)**
Rastrear especificamente a parte colorida do olho, pra saber pra onde a pessoa está olhando — tipo perceber se alguém está "com o olho no boneco" (prestando atenção) ou olhando pro lado.

**Head pose (pose da cabeça) / solvePnP**
Estimar a orientação da cabeça no espaço 3D: está olhando reto pra frente, inclinada, virada de lado? É como um "giroscópio" pro rosto, calculado a partir da posição dos pontinhos numa foto 2D.

**EAR (Eye Aspect Ratio — proporção de abertura do olho)**
Um número que diz o quão aberto está o olho, calculado pela distância entre pálpebras. Quando esse número cai muito, geralmente é porque a pessoa piscou ou está com sono/cochilando — é basicamente um "detector de olho caindo".

**MAR (Mouth Aspect Ratio — proporção de abertura da boca)**
Igual ao EAR, mas pra boca: mede o quanto ela está aberta, útil pra detectar bocejo (sinal de tédio/cansaço).

**Fallback (frames sem rosto detectado)**
Um "plano B" para quando a câmera não consegue achar o rosto (pessoa saiu do quadro, luz ruim). Em vez de travar, o sistema usa um valor substituto (placeholder), tipo colocar "não sei" numa pesquisa em vez de deixar em branco.

**rPPG (fotopletismografia remota)**
Técnica que tenta estimar sinais do corpo (tipo batimento cardíaco) só olhando pra variações sutis de cor na pele pelo vídeo — como se a câmera "sentisse o pulso" à distância, sem tocar a pessoa.

---

## Features e representação dos dados

**Feature (característica/atributo)**
Uma informação numérica que descreve o exemplo, usada pelo modelo pra decidir. Se você fosse adivinhar se alguém está entediado, suas "features" seriam pistas tipo "boceja muito?", "olha pro celular?", "pisca devagar?". No projeto, features são coisas como abertura do olho, ângulo da cabeça etc.

**Features tabulares (agregadas por clipe)**
Resumir um vídeo inteiro em uma linha de números (tipo média, desvio-padrão, percentis dessas features ao longo do clipe) — é como resumir o boletim de um aluno no ano todo em "média final" e "variação de notas", em vez de guardar nota de cada prova separadamente.

**Sequência temporal**
Guardar a informação passo a passo no tempo, mantendo a ordem — tipo assistir o filme completo em vez de só ler o resumo. Necessário quando "como a atenção mudou ao longo do tempo" importa, não só a média.

**Aumento de dados (data augmentation)**
Criar variações artificiais dos exemplos que você já tem pra "esticar" o dataset, tipo tirar a mesma foto com pequenos ajustes (espelhar, girar um pouco) pra treinar com mais variedade sem precisar de mais fotos reais.

---

## Modelos de Machine Learning (ML clássico)

**Modelo**
O "aluno" que está aprendendo a partir dos exemplos, pra depois conseguir prever a resposta certa em casos novos.

**Random Forest (floresta aleatória)**
Um grupo de várias "árvores de decisão" (sequências de perguntas tipo "o olho está fechado? sim/não → boceja? sim/não...") que votam entre si. É tipo perguntar a opinião de várias pessoas diferentes e ficar com a resposta mais votada, em vez de confiar em uma pessoa só.

**XGBoost**
Parecido com o Random Forest, mas as árvores são construídas uma depois da outra, cada uma tentando corrigir o erro da anterior — como um grupo de estudo em que cada aluno foca em resolver justamente as questões que os colegas anteriores erraram.

**SVM (Support Vector Machine / Máquina de Vetores de Suporte)**
Um método que tenta traçar a "linha divisória" mais larga possível entre grupos diferentes, tipo desenhar a cerca mais afastada possível entre dois times num campo, deixando o máximo de espaço de segurança dos dois lados.

**GridSearchCV (busca em grade com validação cruzada)**
Testar várias combinações de "configurações" do modelo (tipo testar várias receitas de bolo variando quantidade de açúcar e tempo de forno) e ver qual combinação dá o melhor resultado, usando validação cruzada pra não se enganar.

**Validação cruzada (cross-validation) / folds**
Dividir os dados de treino em pedaços (ex.: 5 "folds"/fatias) e treinar/testar várias vezes, revezando qual fatia é usada pra testar — tipo fazer 5 simulados diferentes embaralhando quais questões você usa pra estudar e quais pra se testar, pra ter mais confiança no resultado.

**Feature importance (importância de features) / permutação**
Um ranking de quais pistas mais ajudaram o modelo a acertar. "Importância por permutação" testa isso embaralhando uma característica de cada vez e vendo o quanto o modelo piora — se embaralhar "abertura do olho" faz o modelo errar muito mais, essa característica era importante.

**SMOTE (oversampling sintético)**
Técnica pra "criar" exemplos artificiais da classe rara, interpolando entre exemplos reais parecidos — tipo, se você só tem 2 fotos de um evento raro, criar fotos "intermediárias" entre elas pra ter mais material de treino daquele grupo raro.

**Oversampling / Undersampling**
Oversampling = duplicar/gerar mais exemplos da classe rara. Undersampling = descartar exemplos da classe comum. É como igualar times de um jogo: ou você chama mais gente pro time menor, ou tira gente do time maior, pra ficar mais parelho.

**class_weight='balanced' (peso de classe)**
Dizer ao modelo "erros na classe rara doem mais", dando mais importância a cada erro nessa classe — tipo um professor que dá peso maior pra questão difícil na correção, pra compensar que poucos alunos acertam ela.

**Focal loss (perda focal)**
Uma forma de treinar que faz o modelo focar mais nos exemplos difíceis/raros e "ignorar" um pouco os fáceis que ele já acerta sempre — como estudar focando no capítulo que você mais erra, em vez de repassar de novo o que você já sabe de cor.

---

## Aprendizado profundo / Deep Learning

**Rede Neural / Deep Learning**
Um modelo inspirado (de forma bem simplificada) em como neurônios se conectam no cérebro, com várias camadas processando a informação em sequência — cada camada refina um pouco mais o entendimento, tipo um telefone sem fio em que cada pessoa (camada) refina a mensagem antes de passar adiante.

**CNN (Convolutional Neural Network / Rede Neural Convolucional)**
Um tipo de rede especializada em imagens, que aprende a reconhecer padrões visuais (bordas, formas, texturas) automaticamente — como aprender a reconhecer um gato primeiro pelas orelhas, depois pelo formato do corpo, sem alguém te dar essas regras prontas.

**1D-CNN**
Mesma ideia da CNN, mas aplicada a uma sequência de números ao longo do tempo (não uma imagem 2D) — útil pra achar "padrões que se repetem" numa sequência temporal de features.

**RNN / LSTM / BiLSTM**
Redes feitas pra "lembrar" o que veio antes numa sequência, tipo ler uma frase palavra por palavra guardando o contexto. LSTM é uma versão que lembra melhor por mais tempo. BiLSTM lê a sequência nos dois sentidos (do começo pro fim e do fim pro começo), tipo ler uma frase normalmente e depois de trás pra frente pra confirmar o contexto.

**Transformer (Video Transformer)**
Um tipo de rede que, em vez de ler passo a passo, olha pra sequência inteira de uma vez e decide "quais partes prestar mais atenção" — como ler uma página inteira e grifar só os trechos mais importantes, em vez de ler palavra por palavra sequencialmente.

**Transfer learning (aprendizado por transferência)**
Aproveitar um modelo que já aprendeu algo parecido antes (em outro dataset) e só ajustar pro seu problema específico — tipo alguém que já sabe andar de bicicleta aprender a andar de moto mais rápido, porque já tem noção de equilíbrio.

**Fine-tuning**
O "ajuste fino" de um modelo pré-treinado usando seus próprios dados — continuar o treino de quem já sabia algo parecido, só refinando pro seu caso específico.

**Zero-shot**
Pedir pro modelo responder sobre algo que ele nunca viu exemplo nenhum de treino específico, só usando conhecimento geral — tipo perguntar pra alguém "isso é engajamento ou tédio?" sem nunca ter mostrado exemplos antes, confiando só no "bom senso" da pessoa.

**LoRA (Low-Rank Adaptation)**
Uma forma "econômica" de ajustar um modelo gigante, mexendo só numa parte pequena dele em vez de retreinar tudo — como colocar um acessório novo numa roupa pronta em vez de costurar a roupa inteira de novo.

**ResNet / EfficientNet / MobileNet**
Arquiteturas famosas e prontas de redes neurais pra imagem, cada uma com um equilíbrio diferente de "quão precisa" vs. "quão leve/rápida" ela é — tipo escolher entre um carro potente e pesado (ResNet) ou um carro mais leve e econômico (MobileNet) pra fazer a mesma viagem.

**ST-GCN (Spatial-Temporal Graph Convolutional Network)**
Uma rede que trata os pontos do rosto/corpo como uma "teia" conectada (grafo) e aprende tanto como os pontos se relacionam entre si (espacial) quanto como mudam no tempo — imagine analisar não só onde estão os fios de uma marionete, mas como eles se movem juntos ao longo da apresentação.

**Época (epoch) / Early stopping**
Uma "época" é uma passada completa do modelo por todos os exemplos de treino, tipo reler o material de estudo do início ao fim uma vez. "Early stopping" é parar de estudar assim que os simulados (validação) pararem de melhorar, pra não ficar decorando demais e "viciar" no material de treino.

**Overfitting (sobreajuste)**
Quando o modelo "decora" os exemplos de treino em vez de aprender o padrão geral, e por isso vai mal em exemplos novos — tipo decorar as respostas exatas de uma prova antiga e se dar mal quando a prova nova muda as perguntas.

---

## Avaliação e métricas

**Acurácia**
Porcentagem de acertos totais. Cuidado: enganosa quando os grupos são desbalanceados — é tipo dizer "acertei 95% da prova" quando 95% das perguntas eram "verdadeiro" e você só respondeu "verdadeiro" pra tudo.

**Baseline trivial (classe majoritária)**
O "modelo preguiçoso": sempre prever a resposta mais comum, sem nem olhar pros dados. Serve de linha de base pra provar que seu modelo de verdade está aprendendo algo, e não só "chutando o óbvio".

**Matriz de confusão**
Uma tabela que mostra, pra cada classe real, o que o modelo previu — tipo um quadro comparando "gabarito vs. resposta do aluno" pergunta por pergunta, mostrando não só quantos acertos, mas em que trocou uma resposta por outra.

**Precisão (precision)**
Das vezes que o modelo disse "sim", quantas realmente eram "sim"? Tipo, de todos os alarmes de incêndio que tocaram, quantos eram incêndio de verdade (e não fumaça de churrasco).

**Recall (revocação/sensibilidade)**
Dos casos que realmente eram "sim", quantos o modelo conseguiu pegar? Tipo, de todos os incêndios reais que aconteceram, quantos o alarme realmente detectou (mesmo que também dispare alguns alarmes falsos).

**Macro-F1**
Uma média que combina precisão e recall, calculada separadamente pra cada classe e depois tirando a média simples entre elas — isso faz a classe rara "pesar igual" às classes comuns na nota final, ao contrário da acurácia, que deixa a classe rara ser "engolida".

**Balanced accuracy (acurácia balanceada)**
Parecido com a acurácia normal, mas calculando o acerto separado por classe e tirando a média — evita que a classe grande "infle" artificialmente a nota, tipo tirar a média das notas por matéria em vez de só olhar o total geral (que seria dominado pela matéria com mais provas).

**Cohen's kappa**
Mede o quanto o modelo acerta *além do que aconteceria só por sorte/chute*. Kappa perto de 0 = "não é melhor que chutar"; kappa alto = "realmente está aprendendo o padrão". É tipo descontar o "acerto por sorte" numa prova de múltipla escolha.

**AUC-ROC**
Um número (0 a 1) que resume o quão bem o modelo separa dois grupos (ex.: engajado vs. desengajado) em todos os "níveis de exigência" possíveis. Quanto mais perto de 1, melhor; 0,5 é equivalente a jogar uma moeda.

**Intervalo de confiança (IC) / Bootstrap**
Uma faixa de valores que diz "o resultado real provavelmente está entre X e Y", em vez de um número único, pra saber se a diferença entre dois modelos é real ou só coincidência da amostra. "Bootstrap" gera essa faixa reamostrando os dados de teste várias vezes com reposição — tipo repetir o mesmo simulado com pequenas variações de quais questões entraram, várias vezes, pra ver se a nota fica sempre parecida.

**Significância estatística**
Uma evidência de que um resultado não é só coincidência — como quando dois times empatam tantas vezes seguidas que fica claro que "não é sorte", tem algo estrutural ali.

**CORAL / regressão ordinal / perda ordinal**
Uma forma de treinar o modelo que respeita a ordem das classes (0 < 1 < 2 < 3), penalizando mais um erro "longe" (confundir 0 com 3) do que um erro "perto" (confundir 2 com 3) — tipo numa prova valorizada, errar por 1 nível de dificuldade pesa menos do que errar feio, por vários níveis.

---

## Ambiente técnico e reprodutibilidade

**GPU / CPU**
CPU é o "processador geral" do computador, bom pra tarefas variadas mas mais lento em cálculos repetitivos em massa. GPU é especializada em fazer muitas contas simples ao mesmo tempo (originalmente pra gráficos de jogos), o que acelera MUITO o treino de redes neurais — como a diferença entre uma pessoa fazendo contas uma de cada vez (CPU) vs. uma sala cheia de gente fazendo contas simples simultaneamente (GPU).

**ffmpeg**
Um programa de linha de comando que lê, corta e converte vídeos/áudios — a "canivete suíço" usado aqui pra tirar frames específicos dos vídeos.

**Ambiente virtual (venv) / versão do Python**
Uma "caixinha isolada" com uma versão específica do Python e das bibliotecas, separada do resto do computador — evita que instalar coisas pra este projeto bagunce outros projetos, tipo ter uma caixa de ferramentas própria pra cada obra em vez de misturar tudo numa só.

**Seed (semente) / random_state**
Um número que "trava" a aleatoriedade do computador pra ela ser sempre igual quando você repetir o experimento — tipo embaralhar um baralho sempre da mesma forma exata, pra poder repetir o mesmo jogo depois e comparar resultados de forma justa.

**Requirements.txt**
Lista com o nome e a versão exata de cada biblioteca usada no projeto — a "lista de ingredientes com quantidade exata" pra qualquer pessoa conseguir recriar a mesma receita depois.
