---
tags:
  - CS
  - SEM5_SO
aliases:
  - condições de disputa
  - race condition
---
# Condições de corrida
---
- Condições de corrida são situações onde dois ou mais [[processo - SO|processo]]s acessam dados compartilhados e o resultado final depende da ordem em que os processos são executados (o que, em última instância, é ditado pelo mecanismo de escalonamento do SO)
	- depuração difícil
- Condições de corrida são evitadas por meio de **mecanismos de exclusão mútua**
	- exclusão mútua garante que somente um processo estará usando os dados compartilhados num dado momento


# Perguntas
---
- Pq a depuração de programas com condições de corrida é difícil? 
	- Os resultados da maioria dos testes não encontram nada, mas de vez em quando algo esquisito e inexplicável acontece. 
	- Infelizmente, com o incremento do [[paralelismo]] pelo maior número de núcleos, as condições de corrida estão se tornando mais comuns
- Evitar condições de corrida é suficiente para garantir que processos em paralelo cooperem de modo correto e eficiente usando dados compartilhados?
	- Não, precisamos de:
	1. Dois processos jamais podem estar simultaneamente dentro de suas [[região crítica|regiões críticas]].
	2. Nenhuma suposição pode ser feita a respeito de velocidades relativas dos processos ou do número de [[CPU]]s.
	3. Nenhum processo executando fora de sua região crítica pode bloquear qualquer processo.
	4. Nenhum processo pode ter que esperar eternamente para entrar em sua região crítica ou ficar lá indefinidamente
	Requisitos para uma solução

![[Pasted image 20250512090408.png]]