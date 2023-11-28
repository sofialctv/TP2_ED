# Simulação de Realização de Exames de Raio-X
Este repositório tem por objetivo armazenar os códigos elaborados no Trabalho de Programação 2 da disciplina de Estruturas de Dados [2023/2], ministrada pelo Prof. Thiago M. Paixão ([@thiagopx](https://github.com/thiagopx)).

### Autores

- Davidson Carvalho dos Santos: [@Davidsonnj](https://github.com/Davidsonnj)
- Sofia de Alcantara: [@sofialctv](https://github.com/sofialctv)

## Objetivo do Trabalho
O trabalho consiste em simular o processo de realização de exames de raio-X em um hospital, com ênfase na organização da fila para laudo médico. A cada momento, pacientes chegam ao hospital e exames são realizados mediante disponibilidade de aparelhos. A _IA_ (simulada) sugere diagnósticos preliminares e os exames são encaminhados para laudo de acordo com a disponibilidade da equipe de radiologistas. Métricas de desempenho, como tempo médio de laudo e exames realizados fora do prazo, são calculadas. O objetivo é entender o processo e avaliar a eficiência do sistema, contribuindo para futuras otimizações.

As principais competências a serem desenvolvidas neste trabalho incluem:
- Implementação de um sistema de simulação.
- Uso e implementação de filas.
- Implementação de módulos e TADS.

> 📰 _A especificação completa do trabalho pode ser lida acessando o arquivo specification.pdf_

## 📝 Sumário

- [Instalação e Execução](#instalação-e-execução)
- [Funcionamento do programa](#funcionamento-do-programa)
  - [Raciocínio para o cálculo das métricas](#raciocínio-para-o-cálculo-das-métricas)
  - [Organização do projeto](#organização-do-projeto)
- [Principais Estruturas & Decisões de Implementação](#principais-estruturas--decisões-de-implementação)
  - [ListPatient](#listpatient)
  - [QueueExam](#queueexam)
  - [ListMachines](#listmachines)
  - [ExamRecord](#examrecord)
  - [QueueReport](#queuereport)
  - [Pathologies](#pathologies)
  - [Radiologist](#radiologist)
- [Conclusões finais](#conclusões-finais)

# Instalação e Execução
Certifique-se de conter pré-instalado em seu sistema um **compilador C (por exemplo, GCC)**. 
 
Para instalar o programa em seu computador, siga os passos a seguir:
1. Clone o repositório em seu Desktop e salve-o no local desejado:
```bash
git clone https://github.com/sofialctv/TP2_ED
```

2. Abra seu terminal, escreva ```cd``` e cole o caminho no qual o repositório está salvo, como no exemplo:
```
cd C:\Users\ThiagoPX\Documents\Estrutura-de-dados-TP-2\TP2_ED
```
3. Compile o código e execute-o digitando o seguinte comando:
```
make
```
Como resultado, serão gerados os arquivos ```hospital.exe```, ```main.o``` e ```simulation.o```. 

4. Por fim, em seu terminal digite o nome do arquivo:
```
data.txt
````
Uma opção alternativa consiste em baixar o .zip com a pasta do projeto, extrair os arquivos, compilar e executar.

⚠️ **Atenção**: Durante a execução do programa, atente-se às orientações dadas no terminal, bem como as mensagens de erro que podem ser apresentadas.

# Funcionamento do programa
Ao executar o programa, o usuário será informado a cada 10 unidades de tempo, o relatório das métricas do sistema, que são as seguintes:
- **Tempo médio de laudo (TML):** Esta métrica calcula o tempo médio que os exames ocupam a fila para laudo. Quanto menor o tempo médio de espera, mais eficiente é o sistema.
- **Tempo médio de laudo por patologia (TMP):** Calcula o tempo médio que os exames de uma patologia específica aguardam na fila antes de serem laudados, o que ajuda a identificar se alguma patologia está enfrentando atrasos significativos.
- **Quantidade de exames realizados após o limite de tempo estabelecido (QEL):** Esta métrica rastreia quantos exames foram laudados após o limite de tempo aceitável estabelecido pelo hospital (7.200 unidades de tempo). Isso ajuda a avaliar a capacidade do sistema de cumprir prazos críticos.

Vale ressaltar que, no total, a simulação tem tempo máximo de 43.200 unidades de tempo.

## Raciocínio para o cálculo das métricas
### TML (Tempo médio de laudo)

O cálculo do tempo médio de laudo é feito considerando o tempo total que os exames ocupam na fila para laudo e dividindo pelo número de exames laudados. Isso nos dá uma medida da eficiência do sistema em processar os laudos. É calculado da seguinte maneira:

```TML = Tempo total de todos os exames na fila de laudo / Número total de exames laudados```


### TMP (Tempo médio de laudo por patologia)

O cálculo do tempo médio de laudo por patologia é realizado para cada patologia específica, calculando o tempo total que os exames dessa patologia esperam na fila de laudo e dividindo pelo número total de exames dessa patologia laudados. Isso nos ajuda a identificar se alguma condição específica está enfrentando atrasos significativos. A fórmula para cada patologia é:

```TMP_patologia = Tempo total de exames da patologia na fila de laudo / Número total de exames da patologia laudados```


### QEL (Quantidade de exames realizados após o limite de tempo estabelecido)

A quantidade de exames realizados após o limite de tempo estabelecido representa o número de exames que excederam o tempo máximo aceitável para laudo. Esse limite é definido pelo hospital em 7200 unidades de tempo. O cálculo é direto, contando quantos exames foram laudados após esse limite.

## Organização do projeto
O trabalho conta com os principais arquivos:
- **`data.txt`**: contém nosso _banco de dados_ de pacientes, com seus respectivos nomes, CPF e idade;
- **`simulation.c`**: contém as principais funções e structs que viabilizam a simulação do exame de raio-X;
- **`simulation.h`**: contém o cabeçalho das funções utilizadas;
- **`main.c`**: onde as funções e structs da simulação são implementadas; 

# Principais Estruturas & Decisões de Implementação
A seguir, temos a descrições das principais estruturas de dados utilizadas no programa, bem como as principais decisões tomadas durante a implementação

### __ListPatient__

A ``struct Patient``, descrita a seguir, é quem inicia o raciocínio do trabalho: com ela, os dados dos pacientes que chegam ao hospital são armazenados. Decidimos utilizar uma padronização dos IDs, de forma que o primeiro paciente possui ID = 1000, o segundo ID = 1002 e assim por diante.

```
struct Patient {

  char name[50]; /* Nome do paciente */
  char cpf[20];  /* CPF do paciente */
  int age;       /* Idade do paciente */
  int id;        /* Identificador do paciente */
};
```
Ainda trabalhando diretamente com a ``struct Patient``, temos as seguintes funções:

- ``ListPatient`` consiste em uma lista duplamente encadeada de pacientes representada por uma estrutura que mantém referências para o primeiro e o último elemento da lista, contendo as seguintes funções:
- ``ListPatient_create``: Inicializa uma nova lista de pacientes vazia.
- ``ListPatient_size``: Retorna o tamanho aAtual da lista de pacientes.
- ``ListPatient_insert``: Insere um novo paciente na lista.
- ``ListPatient_remove``: Remove um paciente da lista.
- ``ListPatient_free``: Libera a memória alocada para a lista de pacientes.

Optamos por utilizar uma lista duplamente encadeada na implementação de ``ListPatient`` devido a inserção eficiente de novos pacientes, que chegam a todo momento no hoaspital. 

### __QueueExam__
Quanto à fila de exames, utilizamos de fato uma fila como método de implementação, utilizando ponteiros e mantendo referências para o primeiro e o último elemento da fila. As funções relacionadas a ``QueueExam`` são:
- ``QueueExam_create``: Inicializa uma nova fila de exames vazia.
- ``QueueEmpty``: Verifica se a fila de exames está vazia.
- ``QueueEnqueue``: Adiciona um novo exame à fila.
- ``QueueDequeue``: Remove o primeiro exame da fila.
- ``QueueFree``: Libera a memória alocada para a fila de exames.

### __ListMachines__
Parte do programa envolve o uso de aparelhos responsáveis por realizarem o exame de raio-X. A disponibilidade dos aparelhos é conferida através da função ``ListMachines_getAvailable``, que juntamente com outras funções relacionadas à ``ListMachines`` tornam a implementação desse trecho do programa possível (suas assinaturas podem ser verificadas em ``simulation.h`` ). 

Os aparelhos são representados pela struct a seguir:
```
struct Machines {
  int examDuration; /* Duração de exame do aparelho */
  int patientID;    /* ID do paciente que está ocupando a máquina */
  int time;
  Machines *next;
  Machines *prev;
};
```
Os aparelhos foram armazenados em uma lista duplamente encadeada, como destacado a seguir:

```
struct ListOfMachines {
  int count;       /* Contador de máquinas */
  Machines *first; /* Ponteiro para a primeiro máquina da lista */
  Machines *last;  /* Ponteiro para a último máquina da lista */
};
```
Essa decisão foi tomada pensando na possibilidade de adição ou exclusão de aparelhos pelo hospital, o que não foi implementado uma vez que o programa não possui essa proposta, mas o contexto ainda assim foi considerado.

### __ExamRecord__
Consiste no laudo de exame. As funções relacionadas podem ser verificadas em  em ``simulation.h``.

### __QueueReport__
Já ``QueueReport`` é uma fila de registros de exames para laudo representada também por uma fila. As funções relacionadas a essa estrutura são:
- ``QueueReport_create``: Inicializa uma nova fila de registros de exames vazia.
- ``QueueReportEmpty``: Verifica se a fila de registros de exames está vazia.
- ``Exam_Record``: Registra exames para laudo na fila de registros.
- ``QueueDequeue_report``: Remove o primeiro registro de exame da fila.
- ``QueueReport_print``: Função para imprimir os registros de exames na fila de laudos.

### __Pathologies__

A ``struct Pathologies`` é utilizada para armazenar as características de condição e gravidade das doenças, descritas com mais detalhes na Tabela 1. 

```
struct Pathologies {

  char condition[20]; /* Condição do Paciente */
  int urgency;        /* Gravidade da condição */
};
```
As funções relacionadas a essa estrutura são:
- ``Pathologies_create``: Inicializa uma nova lista de patologias vazia.
- ``Pathologies_size``: Retorna o tamanho atual da lista de patologias.
-``Pathologies_insert``: Insere uma nova patologia na lista.
-``Pathologies_remove``: Remove uma patologia da lista.
- ``Pathologies_free``: Libera a memória alocada para a lista de patologias.

### __Radiologist__
Por fim, temos as estruturas relativas ao trecho do programa que conta com a presença dos radiologistas, responsáveis pelo preparo final do laudo.

````
struct Radiologist
{
  int occupation;
  int patientID;
  int durationRad;  /* Duração do processo de preparação do laudo */
  int time;
  Radiologist *next;
  Radiologist *prev;
};
````

# Conclusões finais
O trabalho foi muito desafiador, com uso de diversas estruturas diferentes que se relacionam de alguma forma. As principais dificuldades foram na criação de uma linha de raciocínio, interpretando corretamente o que foi solicitado na especificação e criando os TADs de forma que o resultado final (o relatório de métricas) fosse viável. Apesar do desafio, concordamos que o projeto foi uma excelente oportunidade de treinar nossos conhecimentos em tópicos da disciplina de Estrutura de Dados, sobretudo na implementação das estruturas trabalhadas em sala, com destaque para listas encadeadas e filas.# TP2_ED
