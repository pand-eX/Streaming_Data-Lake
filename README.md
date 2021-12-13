# Arquitetura Data Lake na AWS, Aquisição de dados em Striming com Apache Flume e Apache Nifi.

![1]
Essa será a arquitetura quando todo o projeto estiver pronto, mas irei dividir o projeto para não ficar muito grande então cada camada irei implementar e separar por projeto.

Todos os scripts estarão em anexo.

## Iniciando o Projeto
Tomando como partido o projeto anterior irei implementar agora a camada de Aquisição de dados em Streaming com flume e um Projeto a mais com Apache Nifi
![2]
![3]
![4]

- Um ponto importante é que você não precisa separar as coisas, ou seja, eu não tenho que trabalhar com dados em Batch de um lado e dado de streaming do outro na pratica sua arquitetura final deve contemplar os dois tipos de coletas de dados, ou seja, você pode unir todo o conceito em uma única arquitetura. No nosso Data Lake nós já temos o Sqoop aonde nós podemos criar um job e agendar uma carga em batch uma vez por dia por exemplo agora vamos acrescentar o Flume para trazer dados de streaming de acordo com nossa necessidade depois unimos esses dados para gerar uma nova massa de dados que vai alimentar nossa aplicação analíticas. Não existe uma única arquitetura você pode montar sua arquitetura de acordo com a necessidade do cliente de um processo de análise de um modo geral.

- Primeira coisa é identificar quais são as fontes de streaming de dados as fontes com a qual nós queremos trabalhar para o nosso processo de análise para nosso projeto em si então nós precisamos definir o sistema de coleta desse streaming de dados e de processamento desse streaming e depois como nós vamos armazenar tudo isso são várias questões envolvidas e várias tecnologias aqui é um exemplo de arquitetura eu posso por exemplo coletar os dados de streaming gravar no Data Lake processa depois com alguma outra ferramenta e então persistir os dados em um banco relacional por que não? Ou então em um banco de dados não relacional dependendo do tipo de processamento que eu for realizar. Uma outra alternativa eu posso coletar os dados já processar os dados durante o processo de coleta ou logo imediatamente após a coleta grava isso em um DL depois processa de acordo com sua ferramenta de análise e também você pode persistir os dados em um banco de dados eu tenho que persistir, ou seja, gravar todos os dados que coleto do streaming? Vai depende do meu objetivo do projeto com o qual você está trabalhando.

## Flume
![5]
![6]

- Diferença do Sqoop para o Flume. 
![7]

![8]

## Arquitetura do Flume!

![9]
![10]
![11]

-Cada agente desse e um source, channel e sink esses são exemplo de pipeline para que eu consiga montar em um ambiente distribuído.

![12]

- Mas existe uma outra razão para você implementar uma arquitetura como essa por conta da questão do estado dos eventos que nós estamos coletando, veja eu não estou mais trabalhando com lote de dados o lote de dados ele é estático ele não tem um comportamento ele não tem um evento simplesmente está gravado no banco de dados eu vou lá pego aquele dados e levo para algum lugar, mas na coleta em tempo real o dados muda de estado eles muda por exemplo de horário se você coleta dados do twitter eu tenho os dado sendo gerados ao longo do tempo então eles mudam de estado de um momento para outro e isso tem que ser considerável por essa razão na verdade umas das razão eu poderia criar um pipeline distribuído onde eu poderia cada um dos meus pipeline obter em dados em estados diferentes de acordo sempre com a nossa necessidade essa é uma das tupologia existe mais 2 Fan-Out e Fan-in.

![13]

- Quando utilizar Fan-out? Se eu tenho 3 Sink significa que eu terei uma grande quantidade de dados saindo desse pipeline por isso Fan-out, ou seja, para fora, ventilando para fora isso significa que eu tenho eu condições de armazenar esses dados eu posso ter por exemplo um grande DL aonde eu tenho habilidade, capacidade espaço em disco para armazenar esses dados se eu for processar dados em tempo real talvez essa topologia não seja a ideal porque eu terei uma quantidade muito grande de dados saindo do pipeline talvez a minha arquitetura de processamento em tempo real não seja razoável não seja suficiente para processar tudo isso então essa topologia ela é indicada quando você vai armazenar os dados eu coleto de uma fonte pode gerar uma enorme quantidade de dados desde que um source seja suficiente levo isso para 3 canais tenho 3 sink gerando a saída e vou armazenando isso em um DL.

![14]

E tenho ao contrário é claro, ou seja, estou coletando vários dados  de várias fontes mas lá na outra ponta eu não quero armazenar eu quero processar esses dados então o ideal e que eu tenho 1 sink um único canal de saída de modo que eu tenho condições depois de acoplar aqui nessa arquitetura nesse pipeline o meu apache Spark por exemplo ou apache storm ou alguma outra solução de processamento de dados em tempo real e com isso eu consigo alimentar os dados que saem do flume direto para minha solução de processamento e então de gerar resultado para minha análise é simples mas ao mesmo tempo extremamente poderoso a decisão de qual topologia eu vou usar depende alias como sempre do seu objetivo final. Tudo começa do seu objetivo de negócio se você não sabe para onde vai qualquer caminho serve não é isso? Primeira coisa é definir o que eu quero.

![15]

## Implementação física considerando 3 camadas.

![16]
![17]

- Repare que você pode ter 1 agente com 1 coletor ou 2 agentes com 1 coletor você tem muita flexibilidade na hora de montar sua arquitetura para implementação física.
Conectado via Flume no hadoop.

![18]

Fim do Projeto o resultado é incrível. Agora irei Implementar um Mini-Projeto com Apache NiFi.

## /\ Mini-Projeto Apache NiFi

- Principais Características do NiFi:
- Permite Automatizar o fluxo de dados entre sistemas
- Interface Drag and Drop
- Foco na configuração dos “Processors”
- Escalável em cluster de computadores
- Entrega garantida de dados

## Arquitetura e Implementação NiFi

![19]
![20]

![21]
![22]
- Depois de baixar e instalar conectamos via browser (vou pular essas etapas para não ficar muito grande o projeto, qualquer dúvida é só entrar em contato!)

![23]
![24]

- Repare que ele está funcionando, porém não envia no destino porque eu ainda não configurei, fiz um teste antes com o GetFile e ele recebe o arquivo e deixa na conexão. Eu vou colocando meus arquivos lá no meu diretório de fonte e o apache NiFi vai pegando os arquivos e vai enviando para o conector que por sua vez vai enfileirando esses Flows File até que ele possa se comunicar com processo de destino e agora vamos configurar o processo e destino.
Clica duas vezes no PutFile e marque a essas duas opções que basicamente diz quando você tiver sucesso ou falha simplesmente termine o relacionamento quando ele termina ele manda para o conector dizendo olha eu já fiz o meu trabalho ou ele falhou ou ele teve sucesso eu já fiz o meu trabalho podemos finalizar nosso relacionamento.

![25]
![26]
![27]
- E Salvou no nosso destino, mas repare que tem um erro nós criamos 2 arquivos teste.txt
Repare que no arquivo PutFile-Target tem uma caixa vermelha de Warning que diz:
![28]

- Se você já trabalhou com ETL sabe que isso e uma questão crítica dependendo como roda o processo ETL para resolvermos isso é só clicar duas vezes no PutFile e ir em Properties e mudar o parâmetro Conflict Resolution Strategy. Lembrando que você dar um Stop no processo para aparecer a configuração. Com isso ele vai sobrescrever e pronto.
![29]

Fim. Processo ETL incrível e bastante amigável.
