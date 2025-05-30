---
tags:
  - CS
  - SEM5_SO
aliases:
  - IPC
  - interprocess communication
---
há uma necessidade por comunicação entre os [[processo - SO|processos]], de preferência de uma maneira bem estruturada sem usar interrupções

# 1. Como um processo pode passar informações para outro?
---
- se aplica de maneira diferente para os [[Thread]]s, já que eles compartilham de um espaço de endereçamento comum (threads em espaços de endereçamento diferentes que precisam comunicar- se são questões relativas à comunicação entre processos)
# 2. Como se certificar que 2 ou mais processos não se atrapalhem
---
- se aplica igualmente para os threads

# 3. Sequenciamento adequado quando dependências estão presentes
---
- se aplica igualmente para os threads