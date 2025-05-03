---
tags:
  - CS
  - SEM5_SO
  - Indice
aliases:
toc: true
---


# Aula 01
---

## 1.1 Introdução aos sistemas operacionais
----
- Fontes adicionais de referência: 
	- Livro Silberschatz: Capítulo 1, seção 1.1 
	- Livro Tanenbaum: Capítulo 1, seção 1.1 

**Conceitos:**
- [[Sistema operacional]]
- [[Sistema de computação|sistema computacional]]
- [[programas do sistema]]
- [[programas aplicativos]]

> [!NOTE]
> Possíveis perguntas:
> - Qual a diferença entre um programa do sistema e um programa aplicativo?
> - O que motiva o surgimento dos sistemas operacionais?



> [!important] Exercícios práticos Silberschatz
1. **Quais são as 3 finalidades principais de um sistema operacional?**
	1. Fornecer um ambiente para que um usuário de computador execute programas no hardware do computador de maneira conveniente e eficiente 
	2. Alocar os recursos separados do computador conforme é preciso para resolver o problema dado. O processo de alocação deve ser o mais claro e eficiente possível
	 	3. Como programa de controle, ele tem 2 funções principais
		 1. Supervisão da execução dos programas de usuário para evitar erros e uso impróprio do computador
		 	2. gerenciamento da operação e controle dos dispositivos de I/O






## 1.2 Organização de um sistema computacional 
---
- Fontes adicionais de referência:
	- Livro Silberschatz: Capítulo 1, seção 1.2 (exceto 1.2.3)
	- Livro Tanenbaum: Capítulo 1, seção 1.3 (exceto 1.3.3) 


**Conceitos novos:**
- [[programa bootstrap]]
- [[RAM|Memória principal]] 
- [[Memória secundária]]
- [[registrador]]
- [[controlador de memória]] 
- [[rotina de tratamento de interrupção]]

---
### Operação do sistema
---
Sistema computacional de uso geral é composto por
	uma ou + CPUs e controladores de dispositivos conectados por meio de um barramento comum, com acesso à memória compartilhada
	
Cada controlador é responsável por um tipo específico de dispositivo

CPU e controladores executam concorrentemente, competindo por ciclos da memória


![[Pasted image 20250228103211.png]]


O sistema operacional reage à ocorrência de eventos

- A ocorrência de um evento é indicada pelo que?
	- uma interrupção, que pode ser causada por hardware (controladores) ou software
- Como um dispositivo de E/S gera uma interrupção de hardware?
	- por meio do envio de um sinal pelo barramento (normalmente compartilhado) para o controlador de interrupções
- Quando ocorre uma interrupção de hardware, o que o controlador de interrupções faz?
	- controlador de interrupções (normalmente integrado à placa-mãe) recebe o sinal e decide como/quando trata-lo
		- tratamento de interrupções está habilitado?
		- interrupções mais prioritárias?
	 - controlador de interrupção sinaliza para a CPU o dispositivo que requer atenção (tratamento)
		 - sinal enviado para a CPU via barramento
- O que gera uma interrupção de software?
	- ocorre como consequência da execução de uma chamada de sistema (instrução TRAP) realizada por um programa em execução 
	- também pode ser causada como consequência de um erro (e.g., divisão por zero ou acesso ilegal à memória)
- O que acontece quando uma interrupção ocorre no sistema
	A ocorrência de uma interrupção interrompe a execução da CPU e transfere o controle para uma rotina de tratamento de interrupção
- vetor de interrupção contém o que?
	o endereço de todas as rotinas de tratamento de interrupção
- SO preserva o estado da CPU durante uma interrupção no sistema como? 
	guardando os valores dos [[registrador]]es e contador de programa

- Rotina de tratamento de interrupção é executada
- SO restaura os valores dos registradores e contador de programa e controle retorna para a CPU que retoma o processamento
- Novas interrupções são desabilitadas enquanto uma interrupção estiver sendo tratada

### Estrutura de armazenamento
---
A CPU só pode carregar instruções a partir da memória, logo, o programa deve estar armazenado (carregado) na memória para ser executado
	O programa precisa estar totalmente carregado na MP pra ser acessado? por enquanto sim, mas na verdade não

Todos os tipos de memória oferecem um array de bytes, onde cada byte tem seu próprio endereço

Interação é alcançada por meio de uma sequência de instruções `load` ou `store` para endereços de memória específicos

Qual o típico ciclo de execução de uma instrução?
	1. CPU busca instrução na memória principal
	2. armazena nos registradores
	3. decodifica instrução -> pode provocar nova busca por operandos
	4. executa a instrução


Ou seja: precisamos gerenciar as estruturas de armazenamento


Sistemas de armazenamentos estão organizados em uma hierarquia em função de:
1. velocidade
2. custo
3. volatilidade
![[Pasted image 20250228104655.png]]

HDs surgem na década de 70 por meio da necessidade de acesso rápido aleatorio

O disquete é um dispositivo magnetico de acesso randomico não otimo 

### Silberschatz
---
Para que um computador comece a operar — por exemplo, quando é ligado ou reiniciado — ele precisa ter um programa inicial para executar. Esse programa inicial, ou programa bootstrap, tende a ser simples. 

Ele inicializa todos os aspectos do sistema, dos registradores da CPU aos controladores de dispositivos e conteúdos da memória. O programa bootstrap precisa saber como carregar o sistema operacional e iniciar sua execução. Para alcançar esse objetivo, o programa tem que localizar e carregar na memória o kernel do sistema operacional.

Assim que o kernel é carregado e está em execução, ele pode começar a fornecer serviços para o sistema e seus usuários. 

Alguns serviços são fornecidos fora do kernel por programas do sistema que são carregados na memória em tempo de inicialização para se tornarem **processos do sistema, ou daemons do sistema, que são executados durante todo o tempo em que o kernel é executado.** Quando essa fase é concluída, o sistema está totalmente inicializado e aguarda que algum evento ocorra.

O hardware pode disparar uma interrupção a qualquer momento enviando um sinal à CPU, normalmente pelo bus do sistema. O software pode disparar uma interrupção executando uma operação especial denominada chamada de sistema (também conhecida como chamada de monitor).
![[Pasted image 20250303165152.png]]

Esse array de endereços, ou vetor de interrupções, é então indexado por um único número de dispositivo, fornecido com a solicitação de interrupção, de modo a que seja obtido o endereço da rotina de serviço de interrupção do dispositivo que a está causando

A arquitetura de interrupções também deve salvar o endereço da instrução interrompida. Muitos projetos antigos simplesmente armazenavam o endereço interrompido em uma localização fixa ou em uma localização indexada pelo número do dispositivo. **Arquiteturas mais recentes armazenam o endereço de retorno na pilha do sistema.** 

Se a rotina de interrupção precisar modificar o estado do processador — por exemplo, modificando os valores dos registradores — ela deve salvar explicitamente o estado corrente e, então, restaurar esse estado antes de retornar. Após a interrupção ser atendida, o endereço de retorno salvo é carregado no contador do programa, e a computação interrompida é retomada como se a interrupção não tivesse ocorrido.



À medida que descemos na hierarquia, o custo por bit geralmente decresce, enquanto o tempo de acesso em geral aumenta. Essa desvantagem é razoável; se determinado sistema de armazenamento fosse ao mesmo tempo mais rápido e menos caro que outro — sendo as demais propriedades idênticas —, então não haveria razão para utilizar a memória mais lenta e mais dispendiosa. Na verdade, muitos dispositivos de
armazenamento antigos, inclusive fita de papel e memórias de núcleo, foram relegados aos museus depois que a fita magnética e a memória semicondutora tornaram-se mais rápidas e mais baratas. Os quatro níveis mais altos de memória na Figura 1.4 podem ser construídos com o uso de memória semicondutora.Na hierarquia mostrada na Figura 1.4, os sistemas de armazenamento situados acima do disco de estado sólido são voláteis enquanto os que o incluem e estão abaixo dele são não voláteis.


## 1.3 **Estrutura de Entrada e Saída (E/S)**
---
- Fontes adicionais de referência:
	- Livro Silberschatz: Capítulo 1, seção 1.2.3
	- Livro Tanenbaum: Capítulo 5, seção 5.1.4 

Conceitos:
- [[Sistema de computação|sistema computacional]]

**Conceitos novos:**
- [[controlador de dispositivo]]
- [[DMA]]
- [[Operação de ES síncrona x assíncrona]]


**Possíveis perguntas:**
- Qual é a vantagem do acesso direto à memória (DMA) em comparação com a transferência de dados convencional?
	Reduzir a carga de trabalho da CPU

---
Sistema computacional é formado por CPU e vários controladores de dispositivo conectados por barramento comum (eles competem pelo acesso ao barramento comum)
	dispositivos de E/S e a CPU podem executar concorrentemente

Cada [controlador de dispositivo] é responsável por um tipo de dispositivo em particular (alguns controladores podem controlar mais de um dispositivo (e.g., controlador SCSI – Small Computer Systems Interface) )


- SO possui um driver de dispositivo para cada controlador, o que esse driver faz?
	o driver entende o controlador e oferece uma interface uniforme para interação (acesso à memória do controlador)

- pq existe esse driver de dispositivo?
	 pq pode ser que tenha variações de hardware p hardware, ent o SO n precisa saber se comunicar com tds os dispositivos



Tipicamente em uma operação de E/S, o que ocorre?
	1. o SO via driver de dispositivo carrega as informações necessárias para a realização da operação nos registradores do dispositivo
	2. Controlador do dispositivo examina o conteúdo dos registradores para determinar qual ação (operação) tomar, e.g., ler um caractere do teclado
	3. [Controlador] inicia a transferência dos dados do dispositivo para o buffer local
	4. Quando a transferência é concluída, o controlador do dispositivo informa ao driver do dispositivo por meio de uma interrupção que a operação terminou
	5. Driver do dispositivo retorna o controle para o SO, possivelmente retornando os dados ou um ponteiro para os dados no caso de uma operação de leitura, em uma operação de escrita o driver retorna informações de status (operação de escrita retorna a quantidade de bytes salvos)


Esquema inadequado para a transferência de grandes volumes de dados (sobrecarrega a CPU)
Solução:
	uso da técnica acesso direto à memória (DMA) pelo dispositivo
	CPU fica "liberada " para executar outras tarefas


## 1.4. Arquitetura dos sistemas computacionais 
---
Parte 04:
- Fonte adicional de referência:
	- Livro Silberschatz: Capítulo 1, seção 1.3


**Conceitos novos:**
- [[sistemas monoprocessados]]
- [[sistemas multiprocessados]]
- [[sistemas hyperthreading]]
- [[Sistemas multicore|sistemas multinúcleo]]
- [[sistemas em cluster]]
---

> [!important] Exercícios práticos Silberschatz
> 

1. Enfatizamos a necessidade de que um sistema operacional faça uso eficaz do hardware de computação. 
	1. Quando é apropriado que um sistema operacional abandone esse princípio e “desperdice” recursos?
		 	- Sistemas monousuários devem maximizar o seu uso para o usuário
	2. Por que esse sistema não esta realmente desperdiçando recursos?
	 		- Uma GUI pode "desperdiçar" ciclos de CPU, mas otimizar a interação do usuário com o sistema

Hoje em dia os sistemas operacionais já incluem aplicações como navegadores e correios eletrônicos como parte de sua instalação, mas nem sempre foi assim... Argumente pró e contra a inclusão dessas aplicações como parte do sistema operacional.

---



# Aula 02
---

## Multiprogramação
---
Fonte adicional de referência:
- Livro Silberschatz: Capítulo 1, seção 1.4 

**Conceitos:**
**Conceitos novos:**
- [[Monoprogramação]]
- [[Multiprogramação]]
- [[multitarefa]]

**Possíveis perguntas:**
- Qual foi o primeiro sistema operacional que quebrou o paradigma da monoprogramação?
	UNIX

---



## Operações do Sistema Operacional 
---
Fonte adicional de referência: 
- Livro Silberschatz: Capítulo 1, seção 1.5 

**Conceitos novos:**
- [[modo de operação]]
- [[bit especial]]
- [[Proteção de memória]]
- [[instruções privilegiadas]]
- [[Proteção de CPU]]

> [!question]
> 
>  - Como o temporizador ajuda na proteção da CPU?
>  - Qual é a função do bit de modalidade no sistema operacional
>  - O que os registradores de base e limite controlam na proteção de memória?
>  - Descreva como funciona a mudança de modos de operação em um sistema operacional.
>  - Discuta as implicações de não ter um modo de operação privilegiado em sistemas computacionais.
>  - Por que a proteção de memória deve incluir o vetor de interrupção e as rotinas de tratamento de interrupção?
>  - Quais das instruções são privilegiadas?
> 	 - ler a hora atual do sistema (não)
> 		 - os programas do usuário executam diretamente essa insturção?
> 		 - n, essa operação se da por meio ed uma chamada ao sistema
> 		 - se sim, poderia causar problemas de sincronização
> 	 - acessar o conteúdo de uma variável global (pode ser ler ou modificar) (não)
> 	 - sim: 
> 		 - passar do modo usuário para o modo monitor
> 		 - desativar interrupções
> 		 - definir o valor do temporizador
>  - Alguns sistemas computacionais podem não oferecem um modo de operação privilegiado no hardware. É possível construir um sistema operacional seguro para esses sistemas? Dê argumentos mostrando se isso é ou não possível.


---

O compartilhamento de recursos requer garantias de que um programa com comportamento incorreto (deliberadamente ou não) não possa causar danos ao sistema operacional ou aos demais programas em execução


## Funções de um Sistema Operacional 
---
Fonte adicional de referência:
- Livro Silberschatz: Capítulo 1, seções 1.6 – 1.9

**Conceitos novos:**
- [[processo - SO|processo]]
- [[arquivo| arquivo - SO]]
- [[gerenciamento de armazenamento]]
- [[cache]]
- [[spooling]]


> [!question] DEBATE
>   muitos sistemas SMP tem diferentes níveis de caches, um nível é local para cada núcleo de processamento, e outro nível é compartilhado entre todos os núcleos de processamento
>   Por que os sistemas de cache são projetados dessa forma?
>   https://www.docentes.univasf.edu.br/max.santana/material/aoc-ii/SistemasMultiprocessados.pdf
>   Alternativa:
>   	só um global 
>   		- só locais: dificuldade = sincronizar = manter coerencia de cache


---
Principais funções incluem:
- gerenciamento de processos
- gerenciamento de memória
- gerenciamento de armazenamento
- proteção e segurança


**Atividade de gerenciamento de processos:**
- criar e excluir processos
- executar o escalonamento de processos 
- suspender e retomar processos
- oferecer mecanismos para a comunicação e sincronização de processos
	- essa comunicação pode se dar por meio de mecanismos de memória compartilhada ou troca de msg


**Gerenciamento de memória** ([[RAM|Memória principal]])

Pq existe a necessidade de [gerenciamento de memória]?
	Diferentes programas normalmente ocupam a memória de um dispositivo computacional simultaneamente

**Atividades de gerenciamento de memória**
- controlar que partes da memória estão sendo usadas atualmente e por quem
- decidir quais [[processo - SO|processo]]devem ser carregados quando o espaço de memória se tornar disponível
- alocar e liberar espaço de memória conforme a necessidade

**Proteção e segurança**
- Proteção consiste de qualquer mecanismo que controle o acesso dos processos ou usuários aos recursos definidos pelo sistema computacional
- Segurança é a defesa de um sistema contra ataques internos ou externos
	- vírus/vermes (worms)/cavalos de troia (trojans)
	- ataques de recusa (negação) de serviço
	- roubo de serviço (uso não autorizado)
- Proteção e segurança exigem que os sistemas primeiramente sejam capazes de identificar os usuários para então determinar o que cada usuário pode fazer
- Maioria dos sistemas mantém uma lista de nomes de usuários com identificadores de usuários (IDs de usuários, IDs de segurança)
- ID de usuário é associado a todos os arquivos e processos daquele usuário 
	- definição de controle (privilégios) de acesso
- Usuários podem ser agrupados por meio de identificadores de grupo (ID de grupo)
	- membros grupo possuem mesmos privilégios
- Usuários podem escalar privilégios para obter permissões adicionais para uma atividade

# Aula 03
---

## Serviços de um sistema operacional e interfaces com o usuário
---
Fonte adicional de referência:
- Livro Silberschatz: Capítulo 2, seções 2.1 e 2.2

**Conceitos novos:**
- [[interpretador de comandos]]
---
![[Pasted image 20250314104623.png]]


batch -> primeira interface que surgiu p a execução de programas, sem a participação do usuário
	esse tipo de interface ainda existe?

comando linux sh -> executa os diferentes comandos que estão no arquivo  (exemplo atual de batch)

Serviços:
- Prover interface com o usuário
	- forma de interação do usuário com o sistema operacional. 
	- Pode assumir várias formas:
		- interface linha de comando
		- batch 
		gráfica
- Executar um programa
	- capacidade do sistema para carregar um programa na memória e executá-lo
- Prover suporte para as operações de entrada e saida
	- como os programas do usuário não podem executar operações de E/S diretamente, o sistema operacional precisa prover meios para realizar a E/S
- Manipulação do sistema de arquivos
	- capacidade do programa para ler, gravar, criar e excluir arquivos
- Suporte à comunicação (tanto na mesma máquina quanto em máquinas diferentes)
	- troca de informações entre processos sendo executados no mesmo computador ou em sistemas diferentes ligados por uma rede 
	- implementada através de memória compartilhada ou pela passagem de mensagens
- Detecção de erro
	- assegura computação correta detectando erros na CPU, memória, dispositivos de E/S ou nos programas do usuário

Funções adicionais existem, não para auxiliar o usuário, mas para garantir
operações eficientes do sistema
- alocação de recursos -> os recursos são alocados a diversos usuários ou a múltiplas tarefas sendo executadas ao mesmo tempo
- proteção e segurança -> assegura que todo o acesso aos recursos do sistema seja controlado
- contabilidade -> controla e registra que usuários usam quanto e que tipo de recursos do computador para contabilizar cobrança ou para acumular estatísticas de uso

Suporte ao LPC ->[[Local Procedure Call]] oposto? Remote PC

sockets ?

Oq significa carregar um programa p memoria
1. acha o programa no mem sec
2. ver se ter memoria livre na principal
3. copiar pra mp
4. executar

alguns serviços o so faz pq:
- programa aplicativo n consegue
- é mais facil pro so implementar



### Interfaces com o usuário
---

Muitos comandos são dados ao sistema operacional pelas instruções de controle, que cuidam de:
- criação e gerenciamento de processos
- tratamento de E/S
- gerenciamento do armazenamento secundário
- gerenciamento da memória principal
- acesso ao sistema de arquivos
- proteção

- O programa que lê e interpreta instruções de controle é genericamente conhecido como [interpretador de comandos]
### Interface gráfica
---
Interface amigável com o usuário, também conhecida como [[GUI]]:
- consiste de um sistema de janelas e menus baseado em um mouse
- inventada no início dos anos 70 pela Xerox Palo Alto Research Center (PARC)

Metáfora de área de trabalho baseada em ícones e menus que representam programas, arquivos, diretórios e funções do sistema

A maioria dos sistemas de propósito geral inclui tanto um interpretador de comandos quanto uma interface gráfica:
- Windows possui uma GUI com interpretador de comandos
- Mac OS X possui uma GUI (originariamente chamada “Aqua”) no topo de um kernel UNIX com alguns shells disponíveis

Dispositivos móveis com telas sensíveis ao toque (touchscreen) utilizam novo modelo de interação baseado em toques e gestos
	teclado virtual também é utilizado para entrada de texto



chamada do sistema -> como um programa solicita algo do so



## Chamadas do sistema
---
Fonte adicional de referência:
- Livro Silberschatz: Capítulo 2, seções 2.3 – 2.5
- Livro Tanenbaum: Capítulo 1, seção 1.6

**Conceitos novos:**
- [[chamada de sistema]]
- [[API]]
-  [[sistema de suporte ao tempo de execução]] 
- [[programas do sistema]]


> [!question] Perguntas
> 
> - Por que utilizar uma API ao invés de acessar as chamadas do sistema diretamente?
> 	- APIs aumentam a portabilidade dos programas
> 	- programa irá compilar e executar em qualquer sistema que suporte a mesma API
> 	- chamadas dos sistemas reais normalmente são mais detalhadas e difíceis de manipular do que as funções disponíveis em um biblioteca (API)
> - Relacione cinco serviços fornecidos por um sistema operacional. Explique como cada um oferece conveniência aos usuários. Em que casos seria impossível que os  programas no nível do usuário oferecessem esses serviços? Explique.
> - Seria possível para o usuário desenvolver um novo interpretador de comandos usando a interface de chamadas do sistema fornecida pelo Sistema Operacional?
---


## Estrutura de um sistema operaciona
---
Fonte adicional de referência:
- Livro Silberschatz: Capítulo 2, seções 2.6 e 2.7
- Livro Tanenbaum: Capítulo 1, seções 1.7.1 – 1.7.3

**Conceitos novos:**

---

### Projeto e implementação de SOs
---
Requisitos dos usuários
- Barato
- Fácil de usar e aprender
- Confiável e seguro
- Rápido
Requisitos do sistema
- Fácil de projetar, implementar e manter
- Flexível, confiável e eficiente
Separação entre política e mecanismo
- Princípio fundamental no projeto de um SO
- Garantia de flexibilidade

> [!question] O que a política e o mecanismo de um SO define?
> Política define o que será feito, enquanto que o mecanismo define como será feito



Exemplo:
- **política**: compartilhar o tempo de uso da CPU entre diversos programas (impedir o uso exclusivo da CPU)
	- cada programa terá uma quantidade máxima de tempo em pode permanecer em execução 
- **mecanismo**: temporizador que indica quando um programa deve interromper sua execução



**Implementação dos OSs**
- Historicamente em linguagem de montagem ([[Assembly]])
- Atualmente em linguagens de alto nível ([[C]], [[C++]], [[C#]]), com algumas rotinas escritas em Assembly
	- WIndows 10, Linux, Mac OS



Código escrito em uma linguagem de alto nível:
- pode ser escrito mais rápido 
- é mais compacto
- é mais fácil de entender e depurar
SO é <mark style="background: #FFB86CA6;">muito mais fácil de portar (passar para outro hardware)</mark> se for escrito em uma linguagem de alto nível

Primeiro so desenvolvido em linguagem de mais alto nível
	UNIX



### Estrutura simples
---
Sistemas que não possuem uma estrutura bem definida

Exemplo MS-DOS:
- escrito para fornecer o máximo de funcionalidade no menor espaço possível -> não dividido em módulos
- embora o MS-DOS tenha alguma estrutura, suas interfaces e níveis de funcionalidade não são bem separados
![[Pasted image 20250319093405.png]]


### Estrutura em camadas
---
- SO é dividido em várias camadas (níveis), cada qual construída no topo das camadas inferiores
- camada é a mais inferior (camada 0) -> hardware
- camada é a mais alta (camada N) -> Interface com o usuário
- Camadas são definidas tal que cada camada faça uso das funções (operações) e serviços da camada inferior

**UNIX**
- em função das limitações de hardware, o SO UNIX originariamente tinha uma estrutura bastante limitada
- duas camadas:
	- programas do sistema
	- kernel
- Kernel
	- parte entre a interface de chamada do sistema e o hardware
	- provê um grande número de funções, tais como gerência de processos, memória, arquivos, etc


![[Pasted image 20250501173532.png]]


Estrutura monolítica do kernel (grande quantidade de funcionalidades combinadas em um único nível) faz com que muitos considerem o sistema UNIX original também como um sistema de estrutura simples


### Estrutura em micronúcleo
---
Método de estruturação do SO através da remoção de todos os componentes não essenciais do núcleo para programas do sistema e do usuário

Qual a principal função do micronúcleo?
	Principal função do micronúcleo é prover comunicação entre o programa do usuário e demais serviços sendo executados no “espaço” do usuário -> comunicação baseada na troca de mensagens

![[Pasted image 20250501173716.png]]

- vantagens da estrutura em micronúcleo?
	- facilidade de estender o SO (núcleo mantém-se inalterado)
	- facilidade portar para novas arquiteturas
	- maior confiabilidade (menos código rodando no modo núcleo)
	- maior segurança
- desvantagens da estrutura em micronúcleo?
	- baixo desempenho na comunicação do espaço do usuário para o espaço do núcleo

![[Pasted image 20250501173800.png]]

![[Pasted image 20250501173948.png]]




### Estrutura em módulos
--- 
Muitos sistemas operacionais modernos utilizam uma estrutura modular
	e.g., Solaris, Linux, Mac OS X, Windows
Kernel possui serviços nucleares e vincula serviços adicionais por meio de módulos em tempo de inicialização e execução

**Características:**
- uso de técnicas orientadas a objeto (definição de interfaces)
- cada componente básico é separado 
- cada componente comunica-se com os outros por meio de interfaces conhecidas
- cada componente pode ser carregado conforme a necessidade dentro do núcleo
![[Pasted image 20250319094544.png]]

Compare Estrutura em módulos com a estruturação em camadas
	Modelo possui semelhanças com a [estruturação em camadas], embora com maior flexibilidade pois cada módulo pode chamar qualquer outro módulo

Compare Estrutura em módulos com a [estruturação de micronúcleo]
	Modelo também possui semelhanças a abordagem de micronúcleo, já que o módulo principal possui apenas funções nucleares e o conhecimento de como carregar e se comunicar com outros módulos, -porém mais eficiente pois módulos não precisam invocar a transmissão de mensagens para se comunicarem


### Sistemas híbridos
---
A maioria dos sistemas operacionais modernos não utilizam um único modelo de estrutura
Sistemas híbridos combinam múltiplas abordagens para resolver problemas de desempenho, segurança e usabilidade

 Tanto o kernel do Linux quanto o kernel do Solaris são monolíticos por questões de desempenho, mas também são modulares para permitir a adição dinâmica de novas funções
 
 Windows possui uma estrutura amplamente monolítica combinada com estrutura de micronúcleo para suporte a subsistemas separados

Mac OS X combina as estruturas de camadas com micronúcleo

![[Pasted image 20250501174326.png]]

Android utiliza uma estrutura em camadas no topo de um kernel Linux modificado

![[Pasted image 20250501174345.png]]




# Aula 04
---

## 4.1. Introdução a processos
---
Fontes adicionais de referência:
- Livro Silberschatz: Capítulo 3, seção 3.1
- Livro Tanenbaum: Capítulo 2, seções 2.1 (parte introdutória), 2.1.1, 2.1.5 e 2.1.6

Conceitos novos:
- [[processo - SO|processo]]
- ---


## 4.2. Estruturas de controle
---
Fontes adicionais de referência:
- Livro Silberschatz: Capítulo 3, seção 3.1
- Livro Tanenbaum: Capítulo 2, seções 2.1 (parte introdutória), 2.1.1, 2.1.5 e 2.1.6

Conceitos novos:
- [[Bloco de controle de processo]]
---
![[Pasted image 20250501183951.png]]

O SO gerencia recursos computacionais em benefício dos diversos processos que executam no sistema

> [!important] Questão fundamental:
> quais informações o SO precisa manter para poder controlar os processos e gerenciar os recursos em benefícios deles?
> - SO precisa manter informações sobre o estado corrente de cada processo e de cada recurso



Estruturas de controle são construídas para cada entidade que o sistema operacional gerencia:
- tabelas de memória
- tabelas de E/S
- tabelas de arquivos
- tabelas de processos



## 4.3. Escalonamento
---
Fontes adicionais de referência:
- Livro Silberschatz: Capítulo 3, seção 3.2
- Livro Tanenbaum: Capítulo 2, seção 2.1.6

Conceitos novos:
- [[Escalonador]]
- [[processos IO-Bound]]
- [[processos CPU-Bound]]
- [[troca de contexto de um processo]]
---
### Filas do sistema
---
- Processo é colocado em diferentes filas ao longo de sua existência -> processos migram entre as filas
- **Fila de tarefas (Job Queue)**: conjunto de todos os processos no sistema
- **Fila de prontos (Ready Queue)**: conjunto de todos os processos residindo na memória principal que estão prontos e esperando para serem executados
- **Fila de dispositivo (Device Queue):** conjunto dos processos esperando um dispositivo de E/S

![[Pasted image 20250501192352.png]]

### Escalonador
---
Os processos podem ser descritos como:
- [[processos I/O-Bound]]
	- gastam mais tempo realizando E/S do que cálculos;
	- muitos surtos de CPU curtos
	- exemplo: editor de texto
- [[processos CPU-Bound]]
	- gastam mais tempo realizando cálculos; poucos surtos de CPU muito longos
- PS:
	- surto de CPU = CPU burst = vc exige demais da cpu
	- todo processo a ser executado vai ter surtos de CPU
	- qual o único processo que n executa E/s?
		- idol


## Operações sobre processos
---
Fontes adicionais de referência:
- Livro Silberschatz: Capítulo 3, seção 3.3
- Livro Tanenbaum: Capítulo 2, seções 2.1.2, 2.1.3 e 2.1.4












# Aula 05
---

## 5.1 Introdução a threads
---
Fontes adicionais de referência:
- Livro Silberschatz: Capítulo 4, seções 4.1 e 4.2
- Livro Tanenbaum: Capítulo 2, seções 2.2.1 e 2.2.2

**Conceitos novos:**
- [[Thread]]
- [[multithreading]]
- [[paralelismo]]
---
### Fluxos de execução
Um programa sequencial consiste de um único fluxo de execução, o qual realiza uma certa tarefa computacional
- não executam dois ou mais trechos de código “simultaneamente”
- a maioria dos programas simples tem essa característica
- Grande parte do software de maior complexidade escrito hoje em dia faz uso de mais de uma linha de execução


Modelo de processos é baseado em dois conceitos independentes, quais são eles?
	- Agrupamento de recursos
	- Thread (linha/fluxo) de execução


### agrupamento de recursos

processo pode ser visto como um meio de agrupar recursos relacionados 
	- recursos são utilizados na execução do processo
	- e.g., processo apresenta um espaço de endereçamento que contém os códigos e os dados dos programas e outros recursos, tais como arquivos abertos, processos filhos, alarmes pendentes, etc

### thread de execução

thread tem um contador de programa, registradores e pilha que controla qual instrução deve ser executada a seguir e valores das variáveis de trabalho

Tradicionalmente, processo está associado
a um programa em execução e a um
conjunto de recursos
n Em um ambiente multithreading, processos
estão associados somente à propriedade
de recursos e os threads às atividades de
execução (i.e., threads constituem as
unidades de despacho)
Threads permitem que múltiplas execuções
ocorram no mesmo ambiente do processo,
com um grande grau de independência
umas das outras
n Múltiplos threads executando de forma
concorrente em um processo
q análogo à execução de múltiplos processos de
forma concorrente em um único computador


## 5.2 Modelos multithreading, aspectos do uso de threads, Exemplos de sistemas operacionais
---
Fontes adicionais de referência:
- Livro Silberschatz: Capítulo 4, seções 4.3, 4.4 (exceto 4.4.3), 4.6 e 4.7
- Livro Tanenbaum: Capítulo 2, seções 2.2.4 e 2.2.5



## Threads em Java
---
Fontes adicionais de referência:
- Livro Silberschatz: Capítulo 4, seção 4.4.3
- https://docs.oracle.com/javase/tutorial/essential/concurrency/threads.html
---




# Aula 06
---

## 6.1 Conceitos básicos
---
Fontes adicionais de referência:
- Livro Silberschatz: Capítulo 6, seções 6.1 e 6.2
- Livro Tanenbaum: Capítulo 2, seção 2.4.1

---

**Ciclos de CPU e de E/S**

A execução de um processo consiste em uma sequência intercalada de [[ciclos de CPU ]]("CPU Burst cycles") com longos períodos de espera por E/S. Esses ciclos de espera podem ser aproveitados para disparar a execução de outros processos (ideia central da multiprogramação => a [[CPU]] é utilizada no max com isso)


- [[scheduling da CPU]]


---
## 6.2. Algoritmos de escalonamento
---
Fontes adicionais de referência:
- Livro Silberschatz: Capítulo 6, seção 6.3
- Livro Tanenbaum: Capítulo 2, seções 2.4.2 e 2.4.3

Conceitos novos:
- [[algoritmos de scheduling da CPU]]
---


## 6.3 Escalonamento em múltiplos processadores  Exemplos de sistemas operacionais
----
Fonte adicional de referência:
- Livro Silberschatz: Capítulo 6, seção 6.5 e 6.7

---














