# Revisão LFT – Laboratório de Fundamentos de Tecnologia

**Terminal Linux: comandos básicos, redirecionamento e permissões – resolvido, comentado e aplicado ao dia a dia de uma empresa de TI**

## Como usar este material

Cada questão mantém o **enunciado original** (em destaque) e traz:

- **Resolução:** os comandos prontos para digitar no terminal (bash).
- **Comentário:** o que cada parte do comando faz e por que a resposta é essa.
- **No dia a dia da empresa:** onde esse conhecimento aparece na rotina de infraestrutura, suporte e desenvolvimento.
- **Atenção:** armadilhas, erros comuns e boas práticas.

Convenções: `~` é o seu diretório pessoal (home); `Ctrl+D` encerra a entrada de dados do `cat`; linhas começando com `#` dentro dos blocos de código são comentários e não precisam ser digitadas. Nos exercícios que pedem "digite o seu nome", troque `Thiago` pelo seu nome completo.

## Notas sobre os enunciados

Alguns enunciados têm pequenos deslizes de digitação. Mantive o texto original e resolvi da forma que o exercício claramente pretende:

- **Arquivo 1, questão 13:** o enunciado fala em "diretório trabalho2", mas `trabalho2.txt` é um arquivo. O diretório correto é `atividade2`.
- **Arquivo 2, questão 4:** o caminho `/faeterj/lft/noite/revisao` começa com `/` (raiz do sistema), mas a estrutura foi criada na questão 1 dentro do home. O caminho correto é `~/faeterj/lft/noite/revisao`.
- **Arquivo 2, questão 10:** cita "questão 5", mas as cinco linhas originais (Um a cinco) foram digitadas na questão 4.
- **Arquivo 3, questão 3:** o trecho solto `meusdados/` ao final do enunciado sobrou da colagem e pertence à questão 4.

---

## Arquivo 1 - Comandos básicos LINUX

*Atue como um instrutor, professor ou profissional da área de tecnologia que usa estes conteúdos no dia a dia.*

### Questão 1

> Abrir o manual (man) do comando top. A propósito, o que mostra o comando top?

**Resolução**

```bash
man top        # abre o manual; setas/PgUp/PgDn navegam, /palavra busca, q sai
top            # executa o comando; q para sair
```

**Comentário:** o `top` mostra, em tempo real, os processos em execução (por padrão ordenados por uso de CPU) e um resumo do sistema: tempo ligado (uptime), *load average* (carga média de 1, 5 e 15 minutos), número de tarefas, uso de CPU, memória e swap. Dentro do `top`: `M` ordena por memória, `P` por CPU, `k` encerra um processo pelo PID, `q` sai.

**No dia a dia da empresa:** é o primeiro comando quando alguém diz "o servidor está lento". Em 10 segundos você vê quem está consumindo CPU/RAM e se a carga está alta.

**Atenção:** o `man` abre um paginador (`less`). Se você ficou "preso" na tela, é só apertar `q`.

### Questão 2

> Mostre a data atual do sistema.

**Resolução**

```bash
date
date +"%d/%m/%Y %H:%M:%S"    # formato brasileiro, opcional
```

**Comentário:** `date` imprime data e hora do sistema. Com `+FORMATO` você escolhe a saída (`%d` dia, `%m` mês, `%Y` ano, `%H:%M:%S` hora).

**No dia a dia da empresa:** usado para carimbar backups e logs, por exemplo `tar czf backup_$(date +%F).tar.gz /dados`.

**Atenção:** servidor com data/hora errada quebra logs, certificados (HTTPS) e agendamentos do `cron`.

### Questão 3

> Crie o diretório ~/RevisaoLFT

**Resolução**

```bash
mkdir ~/RevisaoLFT
ls -ld ~/RevisaoLFT     # confere que foi criado
```

**Comentário:** `mkdir` cria diretórios. O `~` é expandido pelo shell para o seu home, então o comando funciona de qualquer lugar.

**No dia a dia da empresa:** criar a pasta de um projeto ou de um cliente antes de começar a trabalhar.

**Atenção:** se o diretório já existir, o `mkdir` dá erro. Em scripts use `mkdir -p`, que não reclama.

### Questão 4

> No seu diretório ~/RevisaoLFT, crie os diretórios atividade1 e atividade2.

**Resolução**

```bash
cd ~/RevisaoLFT
mkdir atividade1 atividade2
ls
```

**Comentário:** o `mkdir` aceita vários nomes de uma vez. Também daria para criar sem sair do lugar: `mkdir ~/RevisaoLFT/atividade{1,2}` (o shell expande as chaves para os dois nomes).

**No dia a dia da empresa:** montar a estrutura padrão de um projeto, como `src`, `docs` e `tests`, em um único comando.

### Questão 5

> No seu diretório ~/RevisaoLFT, liste o conteúdo do diretório /etc

**Resolução**

```bash
cd ~/RevisaoLFT
ls /etc
ls -l /etc       # versão detalhada
```

**Comentário:** o `ls` recebe um caminho absoluto e lista aquele diretório sem você precisar entrar nele. `/etc` guarda as configurações do sistema (rede, serviços, usuários).

**No dia a dia da empresa:** conferir quais arquivos de configuração existem antes de alterar um serviço, por exemplo `ls /etc/nginx`.

### Questão 6

> Entre no diretório atividade1 e mostre o caminho do diretório corrente.

**Resolução**

```bash
cd atividade1
pwd
```

**Comentário:** `cd atividade1` usa caminho relativo (a partir de onde você está). O `pwd` (*print working directory*) mostra o caminho completo, algo como `/home/seu_usuario/RevisaoLFT/atividade1`.

**No dia a dia da empresa:** antes de rodar um comando destrutivo (`rm`, `mv`) o profissional confere o `pwd` para ter certeza de onde está.

### Questão 7

> Crie o arquivo trabalho1.txt dentro do diretório atividade1, e o arquivo trabalho2.txt dentro do diretório atividade2.

**Resolução**

```bash
# estando em ~/RevisaoLFT/atividade1
touch trabalho1.txt
touch ../atividade2/trabalho2.txt
ls . ../atividade2
```

**Comentário:** `touch` cria um arquivo vazio (ou atualiza a data de um arquivo existente). `..` significa "diretório pai", então `../atividade2` sobe um nível e entra em `atividade2`.

**No dia a dia da empresa:** criar arquivos de marcação, como um `.gitkeep` para o Git versionar uma pasta vazia, ou preparar arquivos de log antes de dar permissão a um serviço.

### Questão 8

> Copie o arquivo trabalho1.txt para atividade2.

**Resolução**

```bash
# estando em ~/RevisaoLFT/atividade1
cp trabalho1.txt ../atividade2/
ls ../atividade2
```

**Comentário:** `cp ORIGEM DESTINO`. Como o destino termina em diretório, o arquivo mantém o mesmo nome lá dentro. O original continua em `atividade1`.

**No dia a dia da empresa:** copiar um arquivo de configuração antes de editar (`cp nginx.conf nginx.conf.bkp`), garantindo caminho de volta.

**Atenção:** o `cp` sobrescreve o destino sem avisar. Use `cp -i` para ele perguntar antes.

### Questão 9

> No diretório atividade1, criar um arquivo chamado "numeros.txt", usando o comando cat, contendo os seguintes números:
> 100  
> 50  
> 25  
> 1  
> 2

**Resolução**

```bash
cat > numeros.txt
100
50
25
1
2
# pressione Ctrl+D para finalizar
cat numeros.txt     # confere o conteúdo
```

**Comentário:** sem arquivo de entrada, o `cat` lê o teclado (entrada padrão) até receber o fim de arquivo (`Ctrl+D`). O `>` redireciona o que ele "imprimiria" para dentro de `numeros.txt`.

**No dia a dia da empresa:** criar um arquivo pequeno direto no servidor, sem abrir editor, como uma lista de IPs liberados ou de hosts para um script.

**Atenção:** o `>` apaga o conteúdo anterior do arquivo. Para acrescentar, use `>>`.

### Questão 10

> Ainda no diretório atividade1 crie um arquivo chamado numeros2.txt que contenha a ordem inversa dos números exibidos no arquivo numeros.txt usando apenas um comando.

**Resolução**

```bash
tac numeros.txt > numeros2.txt
cat numeros2.txt     # 2, 1, 25, 50, 100
```

**Comentário:** `tac` é o `cat` ao contrário: imprime as linhas de trás para frente. O `>` grava o resultado em `numeros2.txt`. Tudo em um único comando, como pedido.

**No dia a dia da empresa:** ler um log do fim para o começo, para ver primeiro os eventos mais recentes (`tac /var/log/syslog | less`).

**Atenção:** não confunda com `sort -r`, que ordena em ordem decrescente (100, 50, 25, 2, 1) em vez de inverter a posição das linhas.

### Questão 11

> Mova o arquivo numeros2.txt para o diretório atividade2

**Resolução**

```bash
mv numeros2.txt ../atividade2/
ls ../atividade2
```

**Comentário:** `mv` move (ou renomeia) sem deixar cópia na origem. O arquivo sai de `atividade1` e aparece em `atividade2`.

**No dia a dia da empresa:** arquivar relatórios já processados, movendo-os da pasta de entrada para a pasta de histórico.

### Questão 12

> Apague o diretório atividade1

**Resolução**

```bash
cd ..                # sai do diretório antes de apagá-lo
rm -r atividade1     # apaga o diretório e tudo que está dentro
ls
```

**Comentário:** `atividade1` ainda tem `trabalho1.txt` e `numeros.txt`, então `rmdir` falharia com "Directory not empty". O `rm -r` remove de forma recursiva (diretório + conteúdo).

**No dia a dia da empresa:** limpar diretórios temporários de build ou de deploy antigos.

**Atenção:** o Linux não tem lixeira no terminal. Rodou `rm`, sumiu. Confira com `ls` antes e jamais use `rm -rf` com variável que pode estar vazia (por exemplo `rm -rf $DIR/`).

### Questão 13

> No diretório trabalho2, listar todos os arquivos com extensão .conf do diretório /etc e guardar essa lista em um arquivo chamado "listaconf.txt"

**Resolução**

```bash
cd ~/RevisaoLFT/atividade2       # o enunciado diz "trabalho2", mas o diretório é atividade2
ls /etc/*.conf > listaconf.txt
cat listaconf.txt
```

**Comentário:** o shell expande `/etc/*.conf` (curinga ou *glob*) para todos os arquivos terminados em `.conf` e entrega ao `ls`. O `>` grava a lista em `listaconf.txt`. Isso pega apenas o primeiro nível de `/etc`. Para varrer subpastas também:

```bash
find /etc -name "*.conf" > listaconf.txt 2>/dev/null
```

O `2>/dev/null` descarta as mensagens de "Permission denied" das pastas que você não pode ler.

**No dia a dia da empresa:** montar o inventário de arquivos de configuração para auditoria ou para incluir no backup antes de uma mudança.

### Questão 14

> Exiba as 5 primeiras linhas do arquivo listaconf.txt.

**Resolução**

```bash
head -n 5 listaconf.txt
```

**Comentário:** `head` mostra o início do arquivo (padrão: 10 linhas) e `-n 5` limita a 5. Seu par é o `tail`, que mostra o final.

**No dia a dia da empresa:** espiar o cabeçalho de um CSV ou de um arquivo enorme sem abri-lo inteiro.

### Questão 15

> Pesquise e descubra qual comando pode ser usado para exibir informações sobre seu SO. Execute o comando e salve (direcione) as informações em um arquivo.

**Resolução**

```bash
apropos "system information"     # como pesquisar: busca nos manuais (igual a man -k)
uname -a > info_so.txt           # kernel, hostname, arquitetura
cat /etc/os-release >> info_so.txt   # nome e versão da distribuição
cat info_so.txt
```

**Comentário:** `uname -a` mostra o nome do kernel, o hostname, a versão do kernel e a arquitetura. `/etc/os-release` informa a distribuição (Ubuntu, Debian etc.). Usei `>>` na segunda linha para acrescentar em vez de sobrescrever. Outras opções: `hostnamectl` e `lsb_release -a`.

**No dia a dia da empresa:** ao abrir chamado para fornecedor ou pedir ajuda ao time, a primeira coisa pedida é "qual a versão do SO e do kernel?". Esse arquivo vai anexado ao ticket.

---

## Arquivo 2 - Lista de comando linux pt 2

### Questão 1

> A partir do seu diretório pessoal crie a seguinte estrutura de diretórios com somente um comando: faeterj/lft/noite/revisao

**Resolução**

```bash
cd
mkdir -p faeterj/lft/noite/revisao
ls -R faeterj
```

**Comentário:** o `-p` (*parents*) cria toda a cadeia de diretórios de uma vez e não dá erro se algum já existir. Sem o `-p`, o `mkdir` falharia porque `faeterj` ainda não existe.

**No dia a dia da empresa:** scripts de deploy criam árvores como `/var/www/app/releases/2026-10-22/logs` com `mkdir -p`, e podem rodar várias vezes sem quebrar.

### Questão 2

> Visualize as quatro primeiras linhas do arquivo /etc/services.

**Resolução**

```bash
head -n 4 /etc/services
```

**Comentário:** `/etc/services` é a tabela que relaciona nome de serviço, porta e protocolo (por exemplo `ssh 22/tcp`). O `head -n 4` exibe só o começo.

**No dia a dia da empresa:** consultar rapidamente qual porta um serviço usa ao configurar firewall ou regras de rede (`grep -w 443 /etc/services`).

### Questão 3

> Mostre a última linha do arquivo /etc/passwd

**Resolução**

```bash
tail -n 1 /etc/passwd
```

**Comentário:** `tail -n 1` mostra a última linha. Em `/etc/passwd` cada linha é `usuario:x:UID:GID:comentário:home:shell`, e normalmente os usuários criados mais recentemente ficam no final.

**No dia a dia da empresa:** confirmar que um usuário acabou de ser criado (`useradd`). Lembrando que as senhas não ficam aqui, e sim em `/etc/shadow`.

### Questão 4

> Entre no diretório /faeterj/lft/noite/revisao. Use o comando cat para criar um arquivo contendo 5 linhas chamado linhas.txt. As linha digitadas devem ser:
> Um  
> dois  
> três  
> quatro  
> cinco

**Resolução**

```bash
cd ~/faeterj/lft/noite/revisao     # o caminho real é dentro do home (ver notas)
cat > linhas.txt
Um
dois
três
quatro
cinco
# Ctrl+D
cat linhas.txt
```

**Comentário:** mesma técnica da lista anterior: `cat > arquivo` lê o teclado até o `Ctrl+D`.

### Questão 5

> Use o comando cat para acrescentar novas linhas ao arquivo linhas.txt. As novas linhas deverão ser inseridas no final do texto já existente no arquivo até que seja digitado a palavra "finaliza" para encerrar a execução do comando.

**Resolução**

```bash
cat >> linhas.txt << finaliza
seis
sete
oito
finaliza
cat linhas.txt
```

**Comentário:** o `>>` acrescenta ao final sem apagar. O `<< finaliza` é um *here-document*: o shell lê tudo o que você digita até encontrar uma linha contendo somente `finaliza`, que encerra a entrada (e não entra no arquivo). O enunciado não define o texto das novas linhas, então usei `seis`, `sete` e `oito`.

**No dia a dia da empresa:** *here-documents* são muito usados em scripts para gerar arquivos de configuração, serviços do systemd e entradas de cron sem abrir editor.

### Questão 6

> Use o comando cp para fazer uma cópia do arquivo linhas.txt do exercício anterior com o nome linhasCopia.txt. Mostre o conteúdo do arquivo linhasCopia.txt.

**Resolução**

```bash
cp linhas.txt linhasCopia.txt
cat linhasCopia.txt
```

**Comentário:** `cp ORIGEM DESTINO` com o destino sendo um nome novo cria a cópia já renomeada.

**Atenção:** o Linux diferencia maiúsculas de minúsculas, então `linhasCopia.txt` e `linhascopia.txt` são arquivos diferentes.

### Questão 7

> O comando echo imprime uma string na saída padrão do shell, ou seja imprime no display a string digitada. No caso de uma digitação errada do comando echo (por exemplo zecho) será retornado um erro na saída padrão que também é o display. Qual o comando completo altera a saída padrão do erro para um arquivo com nome logerros.txt

**Resolução**

```bash
zecho teste 2> logerros.txt
cat logerros.txt     # bash: zecho: command not found (a mensagem varia conforme a distro)
```

**Comentário:** todo processo tem três canais: `0` entrada padrão (stdin), `1` saída padrão (stdout) e `2` saída de erro (stderr). O `2>` redireciona apenas o canal de erros para o arquivo. Variações úteis:

- `2>>` acrescenta os erros ao final do arquivo em vez de sobrescrever.
- `> saida.txt 2>&1` (ou `&> saida.txt`) manda saída normal e erros para o mesmo arquivo.
- `2>/dev/null` descarta os erros.

**No dia a dia da empresa:** em tarefas agendadas no `cron`, separar saída e erros é rotina: `./backup.sh >> backup.log 2>> backup_erros.log`. Assim, quem monitora olha só o arquivo de erros.

### Questão 8

> Explique o que acontecerá e justifique o resultado, ao ser digitado o seguinte comando:
> zecho vou inserir nova linha > linhasCopia.txt

**Resolução**

```bash
zecho vou inserir nova linha > linhasCopia.txt
wc -c linhasCopia.txt     # 0 linhasCopia.txt
```

**Resultado:** aparece na tela a mensagem de erro `zecho: command not found` e o arquivo `linhasCopia.txt` fica **vazio** (0 bytes). Ele perde todo o conteúdo que tinha.

**Justificativa:** o shell trata o redirecionamento **antes** de executar o comando. Ao ver `> linhasCopia.txt`, ele já abre o arquivo para escrita e o trunca (apaga o conteúdo). Só depois tenta executar `zecho`, que não existe. A mensagem de erro vai para o canal 2 (a tela), pois apenas o canal 1 foi redirecionado, e nada é gravado no arquivo.

**No dia a dia da empresa:** é um erro clássico e perigoso. Um `comando_errado > /etc/algum.conf` zera uma configuração de produção. Boas práticas: usar `>>` quando a ideia é acrescentar, fazer backup antes (`cp -i`) e, se quiser uma proteção do próprio shell, ativar `set -o noclobber`, que impede o `>` de sobrescrever arquivos existentes.

### Questão 9

> Liste a partir do diretório atual todos os arquivos (inclusive ocultos) em detalhes do diretório /etc exibindo-os de 25 em 25 linhas.

**Resolução**

```bash
ls -la /etc | more -25
```

**Comentário:** `-l` mostra o formato longo (permissões, dono, grupo, tamanho, data) e `-a` inclui os arquivos ocultos (começam com ponto, além de `.` e `..`). O pipe `|` entrega essa saída ao `more`, e `-25` faz o `more` parar a cada 25 linhas (em versões novas também funciona `more -n 25`). Espaço avança a página e `q` sai. Como o caminho `/etc` é absoluto, o comando funciona de qualquer diretório.

**No dia a dia da empresa:** paginar saídas longas é rotina: `ls -la /etc | less`, `dmesg | less`, `journalctl | less`.

### Questão 10

> Crie uma forma para que o arquivo linhas.txt contenha as linhas originais digitadas na questão 5 desta lista, seguidas pelo conteúdo de /etc/passwd que deverá ser seguido do conteúdo original de linhas.txt (linhas originais digitadas na questão 5). Ou seja, ao final o arquivo linhas.txt deverá conter:
> Um  
> dois  
> três  
> quatro  
> cinco  
> ...  
> "conteúdo /etc/passwd"  
> Um  
> dois  
> três  
> quatro  
> cinco  
> ...

**Resolução**

```bash
head -n 5 linhas.txt > /tmp/originais.txt                  # guarda só as 5 linhas originais
cat /tmp/originais.txt /etc/passwd /tmp/originais.txt > linhas.txt
rm /tmp/originais.txt                                      # limpa o temporário
cat linhas.txt
```

**Comentário:** a saída precisa ser: originais, depois `/etc/passwd`, depois originais de novo. O jeito "óbvio" falha:

```bash
cat linhas.txt /etc/passwd linhas.txt > linhas.txt    # ERRADO
```

Aqui o shell trunca `linhas.txt` antes de o `cat` começar a ler, então o `cat` reclama ("input file is output file") e o resultado sai quebrado. A saída é usar um **arquivo temporário**: guarda as linhas originais, monta o resultado em outro lugar e só então grava em `linhas.txt`. O `head -n 5` garante que entram apenas as cinco linhas originais (Um a cinco), mesmo que a questão 5 tenha acrescentado outras.

**No dia a dia da empresa:** o padrão "gera em um arquivo temporário e depois substitui" é a forma segura de alterar arquivos de configuração e de dados em scripts.

---

## Arquivo 3 - Lista de permissões usando linux em anexo

### Resumo de teoria (para consulta rápida)

- A permissão `-rwxr-x---` se lê assim: 1º caractere é o tipo (`-` arquivo, `d` diretório); depois três blocos de três letras: **dono (u)**, **grupo (g)** e **outros (o)**.
- Valores: `r` = 4, `w` = 2, `x` = 1, `-` = 0. Some por bloco: `7` = rwx, `6` = rw-, `5` = r-x, `4` = r--, `0` = ---.
- **Em arquivos:** `r` lê o conteúdo, `w` altera, `x` executa.
- **Em diretórios:** `r` lista os nomes (`ls`), `w` cria, apaga e renomeia entradas dentro dele, `x` permite entrar (`cd`) e acessar itens pelo nome. Sem `x` o diretório fica "trancado".
- O sistema usa **uma só classe** por pessoa, nesta ordem: se você é o dono, valem as permissões do dono; senão, se está no grupo, valem as do grupo; senão valem as de outros. Não somam.
- O `root` ignora essas checagens (para executar, precisa existir `x` para alguém).

### Questão 1

> Um usuário tenta executar o arquivo script.sh com o comando:
> ./script.sh
> Mas recebe a mensagem: Permission denied.
> As permissões do arquivo são:
> -rw-r--r--
> Qual é o problema?

**Resolução**

```bash
ls -l script.sh          # -rw-r--r--  (644): ninguém tem x
chmod u+x script.sh      # só o dono passa a executar -> -rwxr--r--
# ou, para o dono e também grupo/outros executarem:
chmod 755 script.sh      # -rwxr-xr-x
./script.sh
```

**Comentário:** o arquivo não tem permissão de **execução (x)** para ninguém: `rw-` para o dono e `r--` para grupo e outros. Para o `./script.sh` rodar, precisa do `x`. Uma alternativa, que só exige leitura, é chamar o interpretador: `bash script.sh`.

**No dia a dia da empresa:** acontece o tempo todo com scripts de deploy ou de backup copiados de um zip, de um pendrive ou do Windows, que perdem o bit de execução.

**Atenção:** além do `x`, o script precisa de uma primeira linha com o interpretador (`#!/bin/bash`) e o sistema de arquivos não pode estar montado com a opção `noexec`.

### Questão 2

> 0 arquivo abaixo possui as seguintes permissões:
> -rwxr-x--- 1 alberto alberto 20 Apr 16 15:27 texto1.txt
> Quem pode executar esse arquivo?

**Resolução**

```bash
ls -l texto1.txt
# -rwxr-x--- 1 alberto alberto ...
#  rwx = dono (alberto)   r-x = grupo (alberto)   --- = outros
```

**Resposta:** podem executar o usuário **alberto** (dono, `rwx`) e os **membros do grupo alberto** (`r-x`). Outros usuários não têm nenhuma permissão (`---`). O `root` também consegue, pois existe `x` para alguém.

**Comentário:** na linha, o primeiro `alberto` é o dono e o segundo é o grupo. A leitura é por blocos de três letras, e vale somente o bloco da classe a que a pessoa pertence.

**Atenção:** é um arquivo `.txt`. A permissão de execução existe, mas um arquivo de texto sem conteúdo de script não faria nada de útil se fosse executado.

### Questão 3

> Um usuário deseja permitir que todos os usuários possam ler um arquivo chamado dados.txt.
> Qual comando resolve corretamente?
> meusdados/

**Resolução**

```bash
chmod a+r dados.txt      # a = all (dono, grupo e outros) ganham leitura
# equivalentes: chmod ugo+r dados.txt  |  chmod o+r dados.txt (basta para "outros")
# ou fixando o valor exato:
chmod 644 dados.txt      # rw-r--r--
```

**Comentário:** `chmod a+r` **adiciona** leitura para todos e preserva o resto das permissões. Já o `644` **define** o valor exato (dono lê e escreve, os demais só leem). O trecho `meusdados/` no fim do enunciado é resto da colagem e pertence à questão 4.

**No dia a dia da empresa:** liberar um relatório ou um arquivo de configuração para leitura geral, como um `index.html` que o servidor web (usuário `www-data`, que cai em "outros") precisa ler.

**Atenção:** para ler o arquivo, a pessoa também precisa de `x` em todos os diretórios do caminho até ele.

### Questão 4

> Um diretório possui as permissões:
> drwx------ 2 alberto alberto 64 Apr 16 15:32 meusdados
> drwx------
> Outro usuário tenta executar o seguinte comando cd meusdados/ a partir do diretório pai de meusdados/ , mas não consegue.
> Qual o motivo?

**Resolução**

```bash
ls -ld meusdados
# drwx------ 2 alberto alberto ... meusdados
cd meusdados/            # (outro usuário) -> Permission denied
```

**Resposta:** o usuário é "outros" e esse bloco está `---`: não tem a permissão de **execução (x)** no diretório, que é justamente a que permite **entrar** nele (`cd`). Só o dono (`alberto`, `rwx`) tem acesso.

**Para liberar** (se fosse a intenção do dono):

```bash
chmod o+x meusdados      # outros podem entrar (e acessar arquivos pelo nome)
chmod o+rx meusdados     # entrar e também listar o conteúdo
```

**No dia a dia da empresa:** é exatamente o comportamento desejado em `/home/usuario` (700): cada pessoa só enxerga a própria pasta. Também é o problema típico de "o site dá 403" quando um diretório do caminho está sem `x` para o usuário do servidor web.

### Questão 5

> Um arquivo possui permissões:
> -rw-rw-r--
> O dono decide impedir que o grupo altere o arquivo, mas quer manter a leitura.
> Qual comando deve usar?

**Resolução**

```bash
chmod g-w arquivo        # -rw-rw-r-- vira -rw-r--r--
# ou, pelo modo octal:
chmod 644 arquivo
```

**Comentário:** `g-w` remove (`-`) a escrita (`w`) do grupo (`g`) e não mexe em mais nada, então a leitura do grupo permanece. O resultado é `rw-r--r--` (644).

**No dia a dia da empresa:** travar um arquivo de configuração que a equipe só deve consultar, não alterar.

### Questão 6

> Um diretório qualquer possui permissões:
> drwxr-xr-x
> Um usuário comum tenta remover um arquivo dentro dele, mas não consegue.
> Qual pode ser a causa?

**Resolução**

```bash
ls -ld diretorio
# drwxr-xr-x  -> só o dono tem w
rm diretorio/arquivo     # (usuário comum, não dono) -> Permission denied
chmod g+w diretorio      # solução, feita pelo dono ou por root, se o usuário estiver no grupo
```

**Resposta:** o usuário comum não é o dono, então cai em grupo ou outros (`r-x`): ele pode **entrar e listar**, mas **não tem** `w` **no diretório**. Remover um arquivo é alterar a lista de entradas do diretório, e isso depende da permissão `w` (mais `x`) **do diretório**, não do arquivo.

**Outras causas possíveis:** o *sticky bit* no diretório (`drwxrwxrwt`, como em `/tmp`, onde só o dono do arquivo pode apagá-lo), atributo imutável no arquivo (`chattr +i`) ou sistema de arquivos montado somente leitura.

**No dia a dia da empresa:** "não consigo apagar esse arquivo de log/upload" quase sempre é permissão de diretório, e não do arquivo.

### Questão 7

> Um administrador executa:
> chmod 755 arquivo.sh
> Quais são as permissões resultantes? Explique o motivo

**Resolução**

```bash
chmod 755 arquivo.sh
ls -l arquivo.sh         # -rwxr-xr-x
```

**Resposta:** `-rwxr-xr-x`.

**Motivo:** cada dígito é a soma de `r`=4, `w`=2 e `x`=1, na ordem dono, grupo, outros:

- `7` = 4+2+1 = **rwx** (dono lê, escreve e executa)
- `5` = 4+0+1 = **r-x** (grupo lê e executa)
- `5` = 4+0+1 = **r-x** (outros leem e executam)

**No dia a dia da empresa:** `755` é o padrão para scripts, binários e diretórios públicos. Arquivos comuns ficam em `644`. Se alguém sugerir `chmod 777`, desconfie: dá escrita a todo mundo e quase nunca é a solução correta.

### Questão 8

> Um usuário tenta listar o conteúdo de um diretório:
> ls diretorio/
> Mas recebe erro de permissão.
> As permissões são:
> d--xr-xr-x
> Qual permissão está faltando para listar corretamente?

**Resolução**

```bash
ls -ld diretorio
# d--xr-xr-x   dono: --x   grupo: r-x   outros: r-x
chmod u+r diretorio      # devolve a leitura ao dono
ls diretorio/
```

**Resposta:** falta a permissão de **leitura (r)** para o **dono**. Listar o conteúdo de um diretório exige `r`.

**Comentário:** como grupo e outros já têm `r-x`, só quem cai na classe do dono fica sem listar. Logo, o usuário do erro é o dono: ele tem `x` (consegue entrar e acessar itens pelo nome), mas sem `r` não consegue ver a lista de nomes.

**No dia a dia da empresa:** é o caso do "consigo abrir o arquivo se eu souber o nome, mas não consigo listar a pasta", típico quando alguém aplicou `chmod` errado em um diretório compartilhado.

### Questão 9

> Um arquivo tem permissões:
> -rw-------
> Outro usuário tenta copiar o arquivo com:
> cp arquivo.txt copia.txt
> O que acontecerá?
> A) A cópia será realizada normalmente
> B) A cópia falhará por falta de leitura
> C) A cópia falhará por falta de execução
> D) A cópia criará um arquivo vazio

**Resposta: alternativa B.**

```bash
cp arquivo.txt copia.txt
# cp: cannot open 'arquivo.txt' for reading: Permission denied
```

**Comentário:** para copiar, o `cp` precisa **ler** o conteúdo da origem. Com `-rw-------`, só o dono lê; "outro usuário" cai em *outros* (`---`) e recebe `Permission denied`.

- **A** está errada: sem leitura não há cópia.
- **C** está errada: copiar não exige `x` no arquivo (execução só é necessária para rodar o arquivo).
- **D** está errada: o `cp` abre a origem antes de criar o destino, então falha sem gerar `copia.txt`.

**No dia a dia da empresa:** chaves privadas SSH (`~/.ssh/id_rsa`) ficam em `600` justamente para que ninguém mais consiga ler ou copiar. O próprio `ssh` recusa usar uma chave com permissão aberta.

### Questão 10

> Um diretório possui permissões:
> drwx-wx--x
> Qual das ações abaixo um usuário externo (others) pode realizar?
> – Listar arquivos
> – Entrar no diretório
> – Criar arquivos
> – Ler arquivos
> Justifique sua resposta

**Resolução**

```bash
ls -ld diretorio
# drwx-wx--x   dono: rwx   grupo: -wx   outros: --x
```

**Resposta:** o usuário externo pode apenas **Entrar no diretório**.

**Justificativa:** o bloco de *others* é `--x`:

- **Listar arquivos:** não, exige `r` no diretório, que outros não têm.
- **Entrar no diretório:** **sim**, o `x` permite o `cd` e atravessar o diretório.
- **Criar arquivos:** não, exige `w` no diretório, que outros não têm (só o dono e o grupo têm).
- **Ler arquivos:** não pelas permissões do diretório. Com `x`, o usuário consegue acessar um item *se souber o nome exato*, mas ler o conteúdo ainda depende da permissão `r` do próprio arquivo, que nada tem a ver com o diretório.

**No dia a dia da empresa:** o `--x` serve para compartilhar arquivos específicos sem expor a lista do que existe na pasta (por exemplo, uma pasta de entrega em que cada cliente só sabe o nome do próprio arquivo).

### Questão 11

> Suponha que você tem em um diretório /home/Empresa os subdiretórios memorandos, contratos, vendas e clientes. Cada um destes subdiretórios possui centenas e centenas de arquivos e subdiretórios que foram criados por você. Agora, você quer proteger o diretório /home/Empresa de forma que somente você e os usuários do seu grupo UNIX possam abrir e listar o conteúdo do diretório, mas apenas você possa salvar arquivos dentro do diretório. Indique qual a sequência de comandos que deve ser executada para garantir os objetivos acima.

**Resolução**

```bash
cd /home
ls -ld Empresa            # confere dono, grupo e permissões atuais
id -gn                    # confere qual é o seu grupo principal
chgrp SEU_GRUPO Empresa   # só se o grupo do diretório não for o seu
chmod 750 Empresa         # rwxr-x---
ls -ld Empresa            # esperado: drwxr-x--- ... Empresa
```

**Comentário:** `750` atende os três requisitos:

- **Dono (7 = rwx):** abre, lista e salva arquivos.
- **Grupo (5 = r-x):** `x` abre (entra) e `r` lista, mas sem `w` não salva nada.
- **Outros (0 = ---):** nenhum acesso.

Não use `-R`: a proteção é do **diretório** `/home/Empresa`, e já bloqueia o acesso a tudo que está abaixo, porque ninguém de fora consegue atravessá-lo para chegar aos subdiretórios. Aplicar `chmod -R 750` mexeria nas centenas de arquivos e ainda os tornaria executáveis sem necessidade.

**No dia a dia da empresa:** é o desenho clássico de pasta de departamento: o responsável escreve, o time lê, o resto não enxerga. Se quiser garantir também que o grupo não altere os subdiretórios, remova a escrita do grupo neles: `find /home/Empresa -type d -exec chmod g-w {} +`.

### Questão 2 (bloco final do arquivo)

> Crie o arquivo meusdados.txt dentro do diretório ~/exercicios_01. Em seguida, indique como usar chmod para definir as permissões abaixo:
> ● Leitura e escrita para você e outros.
> ● Nenhuma permissão para usuários de seu grupo UNIX;
> ● Somente você pode listar os arquivos do diretório;
> Todos podem ler os arquivos do diretório, mas somente você e usuários de seu grupo UNIX podem listar os arquivos do diretório

**Resolução**

```bash
mkdir -p ~/exercicios_01
touch ~/exercicios_01/meusdados.txt
```

**(a) Arquivo: leitura e escrita para você e outros, nada para o grupo**

```bash
chmod 606 ~/exercicios_01/meusdados.txt
# equivalente: chmod u=rw,g=,o=rw ~/exercicios_01/meusdados.txt
ls -l ~/exercicios_01/meusdados.txt     # -rw----rw-
```

**(b) Diretório: somente você pode listar**

```bash
chmod 700 ~/exercicios_01               # drwx------
```

**(c) Diretório: todos podem ler os arquivos, mas só você e o seu grupo listam**

```bash
chmod 751 ~/exercicios_01               # drwxr-x--x
chmod 644 ~/exercicios_01/*             # arquivos legíveis por todos
```

**Comentário:**

- **(a)** `606` = dono `rw-` (6), grupo `---` (0), outros `rw-` (6). Curiosidade: quem é do seu grupo **fica sem acesso**, mesmo "outros" tendo `rw`, porque o sistema usa só a classe mais específica da pessoa.
- **(b)** para **listar** é preciso `r` no diretório. Com `700` só o dono tem `r` (e `x` para entrar).
- **(c)** `751`: dono `rwx`, grupo `r-x` (entra e lista), outros `--x` (entram, mas não listam). Como "todos leem os arquivos", os arquivos precisam de `r` para outros (`644`) e o diretório de `x` para outros, para chegarem até eles pelo nome.

**No dia a dia da empresa:** montar pasta compartilhada em que a equipe vê a lista de arquivos e terceiros só acessam o que receberam o nome.

### Questão 3 (bloco final do arquivo)

> Você se encontra em uma situação difícil. Você criou um diretório ~/Trab_Prog_01 e nele você criou 4 (quatro) arquivos, cada um contendo um programa em linguagem ShellScrpt, para serem entregues ao professor da disciplina de LFT. Como você é um aluno consciente e correto (e não quer dar moleza), você executou os comandos UNIX abaixo com o objetivo de bloquear, para quaisquer usuários exceto você mesmo, o acesso de leitura e escrita aos arquivos:
> cd
> chmod -R 600 Trab_Prog_01
> Mas, depois de executados estes comandos, NEM MESMO VOCÊ consegue agora acessar seus próprios arquivos. O que você fez errado? Como corrigir o problema, mantendo o seu objetivo inicial?

**Resolução**

```bash
# o que foi feito (e deu problema)
cd
chmod -R 600 Trab_Prog_01
ls -ld Trab_Prog_01              # drw-------  -> o diretório ficou sem x
cd Trab_Prog_01                  # Permission denied

# correção
chmod 700 ~/Trab_Prog_01         # devolve o x ao dono, só no diretório (sem -R)
ls -ld ~/Trab_Prog_01            # drwx------
ls -l ~/Trab_Prog_01             # arquivos continuam -rw-------
```

**O que foi feito errado:** o `-R` aplicou `600` (`rw-`) **também ao próprio diretório** `Trab_Prog_01`. Em diretório, o `x` é o que permite **entrar e acessar o que está dentro**. Sem ele, nem o dono consegue abrir os arquivos, mesmo que cada arquivo tenha `rw-` para ele.

**Como corrigir:** o dono sempre pode mudar as permissões do que é dele, então basta devolver o `x` ao diretório com `chmod 700` (sem `-R`). Os quatro arquivos já estão `600` (só o dono lê e escreve), o que cumpre o objetivo inicial: ninguém além do dono acessa.

**Como fazer certo de primeira:** separando diretório de arquivo, ou deixando o `chmod` decidir:

```bash
chmod -R u=rwX,go= ~/Trab_Prog_01
# ou separando:
find ~/Trab_Prog_01 -type d -exec chmod 700 {} +
find ~/Trab_Prog_01 -type f -exec chmod 600 {} +
```

O `X` maiúsculo dá `x` apenas a diretórios (e a arquivos que já eram executáveis). O `go=` zera as permissões de grupo e outros.

**Atenção:** com `600` os scripts **não podem ser executados** com `./script.sh`. Rode com `bash script.sh` ou use `700` nos arquivos, se quiser executar direto.

**No dia a dia da empresa:** `chmod -R` com número fixo é a causa de muita dor de cabeça, como deixar `/var/www` inteiro quebrado ou diretórios inacessíveis. Prefira `find -type d` / `find -type f`, ou o `X` do modo simbólico.

---

## Arquivo 4 - Revisão AV1

### Questão 1

> Resolva as situações propostas abaixo
> a) A partir do seu diretório pessoal crie a seguinte estrutura de diretórios com somente um comando: ~/faeterj/lft/REVISAO
> b) A partir do seu diretório pessoal, entre no diretório ~/faeterj
> c) Crie um arquivo vazio com o nome revisao1.txt.
> d) Entre com o comando para imprimir o texto <digite o seu nome>, redirecionando a saída do comando para o arquivo revisao1.txt.

**Resolução**

```bash
# a)
mkdir -p ~/faeterj/lft/REVISAO
# b)
cd ~/faeterj
# c)
touch revisao1.txt
# d)
echo "Thiago" > revisao1.txt
cat revisao1.txt
```

**Comentário:** (a) o `-p` cria a cadeia inteira em um comando; (b) `cd` com caminho absoluto a partir do home; (c) `touch` cria o arquivo vazio; (d) `echo` imprime o texto e o `>` joga a saída dentro do arquivo, substituindo o conteúdo (que estava vazio).

**No dia a dia da empresa:** `echo "texto" > arquivo` é a forma mais rápida de gerar um arquivo de uma linha, como um `VERSION` ou um arquivo de marcação de deploy.

### Questão 2

> Ainda no diretório ~/faeterj Use o comando cat para criar um arquivo chamado revisao2.txt e digitar as linhas abaixo. As linhas digitadas devem ser:
> revisao1 = < Digite qual o comando responde a questão 1 >
> revisao2 = < Digite qual o comando responde a questão 2 >

**Resolução**

```bash
cat > revisao2.txt
revisao1 = mkdir -p ~/faeterj/lft/REVISAO ; cd ~/faeterj ; touch revisao1.txt ; echo "Thiago" > revisao1.txt
revisao2 = cat > revisao2.txt
# Ctrl+D
cat revisao2.txt
```

**Comentário:** o enunciado pede que cada linha traga o **comando que respondeu** à respectiva questão. Na primeira, os quatro comandos da questão 1 ficam separados por `;` (que executa um após o outro). Como o `cat` só lê o teclado, o que for digitado é gravado literalmente, sem o shell interpretar nada.

### Questão 3

> Acrescentar uma nova linha ao arquivo revisao2.txt, conforme abaixo. A nova linha deverá ser inserida no final do texto já existente no arquivo revisao2.txt, sem sobrescrever o conteúdo e até que seja digitado a palavra "finaliza" para encerrar a execução do comando.
> revisao3 = < Digite qual o comando responde a questão 3>

**Resolução**

```bash
cat >> revisao2.txt << finaliza
revisao3 = cat >> revisao2.txt << finaliza
finaliza
cat revisao2.txt
```

**Comentário:** o `>>` acrescenta no final sem apagar o que já existe, e o `<< finaliza` faz o `cat` ler até uma linha que seja **somente** `finaliza`. A linha `revisao3 = cat >> revisao2.txt << finaliza` é o conteúdo gravado (ela traz a palavra, mas não está sozinha na linha, então não encerra a entrada). A última linha `finaliza` encerra e **não** entra no arquivo.

### Questão 4

> No diretório ~/faeterj junte o conteúdo dos arquivos revisao1.txt e revisao2.txt no arquivo revisao1-3.txt

**Resolução**

```bash
cat revisao1.txt revisao2.txt > revisao1-3.txt
cat revisao1-3.txt
```

**Comentário:** `cat` vem de *concatenate*: com dois ou mais arquivos, imprime um após o outro, e o `>` grava o resultado no novo arquivo. A ordem dos argumentos é a ordem no resultado.

**No dia a dia da empresa:** juntar logs rotacionados ou várias partes de um arquivo (`cat access.log.1 access.log > todos.log`).

### Questão 5

> Ainda no diretório ~/faeterj use o comando cp para fazer uma cópia em ~/faeterj/lft/REVISAO do arquivo revisao1-3.txt da questão anterior com o nome revisaolft.txt. Ainda no diretório faeterj/ mostre o conteúdo do arquivo revisaolft.txt.

**Resolução**

```bash
cp revisao1-3.txt lft/REVISAO/revisaolft.txt
cat lft/REVISAO/revisaolft.txt
```

**Comentário:** o destino traz o caminho **e** o novo nome, então o `cp` copia e renomeia em uma tacada. O `cat` mostra o resultado sem sair de `~/faeterj`, usando caminho relativo.

### Questão 6

> Use o comando tr para substituir as letras 'a' por 'A' do texto contido no arquivo revisaolft.txt de forma definitiva. (DICA: a solução pode usar vários comandos).

**Resolução**

```bash
tr 'a' 'A' < lft/REVISAO/revisaolft.txt > lft/REVISAO/temp.txt
mv lft/REVISAO/temp.txt lft/REVISAO/revisaolft.txt
cat lft/REVISAO/revisaolft.txt
```

**Comentário:** o `tr` só lê da entrada padrão (por isso o `<`) e só escreve na saída padrão: ele **não edita o arquivo no lugar**. Para a troca ser "definitiva", o resultado vai para um arquivo temporário (`>`) e depois o temporário substitui o original (`mv`).

**Atenção:** o erro clássico é `tr 'a' 'A' < arq > arq`. O shell zera `arq` antes do `tr` ler, e o arquivo fica vazio. Outro detalhe: o `tr` troca caractere por caractere e só o `a` simples, então `ã` e `á` não mudam. Uma alternativa em um comando é `sed -i 's/a/A/g' arquivo`.

**No dia a dia da empresa:** o padrão "temporário e depois `mv`" é usado em scripts para trocar arquivos de forma segura, pois o `mv` dentro do mesmo sistema de arquivos é praticamente instantâneo.

### Questão 7

> Sem sair do diretório faeterj/ mude as permissões do arquivo revisaolft.txt para somente leitura para o usuário dono e nenhuma outra permissão para o grupo de usuários e para outros usuários usando o modo octal.

**Resolução**

```bash
chmod 400 lft/REVISAO/revisaolft.txt
ls -l lft/REVISAO/revisaolft.txt        # -r--------
```

**Comentário:** em octal, são três dígitos (dono, grupo, outros): `4` = `r--` para o dono; `0` = `---` para o grupo; `0` = `---` para outros. Resultado: `-r--------`.

**No dia a dia da empresa:** `400` é o padrão para chaves privadas e arquivos de credenciais que não devem ser alterados nem lidos por ninguém além do dono.

### Questão 8

> Sem sair do diretório faeterj/ execute os seguintes comandos:
> cat ~/faeterj/lft/REVISAO/revisaolft.txt
> echo Tirei 10 em LFT > ~/faeterj/lft/REVISAO/revisaolft.txt
> Avalie os resultados de cada comando e explique de forma detalhada o que está acontecendo

**Resolução**

```bash
cat ~/faeterj/lft/REVISAO/revisaolft.txt
echo Tirei 10 em LFT > ~/faeterj/lft/REVISAO/revisaolft.txt
# bash: /home/usuario/faeterj/lft/REVISAO/revisaolft.txt: Permission denied
cat ~/faeterj/lft/REVISAO/revisaolft.txt     # o conteúdo continua igual
```

**Avaliação do 1º comando:** com o `cat`, **funciona**. O arquivo está `-r--------` e você é o dono, então a classe aplicada é a do dono, que tem `r`. O `cat` exibe o conteúdo (já com os `A` maiúsculos da questão 6).

**Avaliação do 2º comando:** com o `echo ... > arquivo`, **falha** com `Permission denied`. O `>` exige abrir o arquivo para **escrita**, e o dono só tem `r` (sem `w`). Quem faz essa abertura é o próprio shell, antes de o `echo` rodar, e como a abertura é negada o `echo` nem chega a executar. Como o arquivo nem chegou a ser aberto para escrita, ele **não é truncado**: o conteúdo permanece intacto (o `cat` final comprova).

**Como resolver, se quisesse gravar:** o dono pode mudar as próprias permissões: `chmod u+w arquivo` (ou `chmod 600 arquivo`). O `root` ignoraria essa trava.

**No dia a dia da empresa:** a permissão `400` protege arquivos importantes contra gravação acidental, até pelo próprio dono. E um detalhe que pega muita gente: `sudo echo texto > /etc/arquivo` **também falha**, porque o `>` é feito pelo shell do seu usuário, e não pelo `sudo`. A forma correta é `echo texto | sudo tee /etc/arquivo`.

---

## Arquivo 5 - Simulação real de prova

### Questão 1

> Crie a seguinte estrutura de diretórios:
> ~/provaLFT/avaliacao1
> ~/provaLFT/avaliacao2

**Resolução**

```bash
mkdir -p ~/provaLFT/avaliacao1 ~/provaLFT/avaliacao2
ls -R ~/provaLFT
```

**Comentário:** o `-p` cria `provaLFT` automaticamente, e o `mkdir` aceita os dois caminhos de uma vez.

### Questão 2

> Entre no diretório ~/provaLFT/avaliacao1. Crie o arquivo prova1.txt ao mesmo tempo em que utilize o comando para "imprimir na tela" o texto <digite o seu nome>, redirecionando a saída do comando para o arquivo prova1.txt. A criação do arquivo e o redirecionamento do texto deve ser executado em um único comando.

**Resolução**

```bash
cd ~/provaLFT/avaliacao1
echo "Thiago" > prova1.txt
cat prova1.txt
```

**Comentário:** o `>` **cria** o arquivo se ele não existir e grava nele a saída do `echo`. Por isso não precisa de `touch` antes: criação e gravação acontecem no mesmo comando.

### Questão 3

> Ainda no diretório ~/provaLFT/avaliacao1, use o comando cat para criar um arquivo chamado prova2.txt e digitar o conteúdo abaixo, pelo mesmo comando. As linhas digitadas devem ser:
> Linha1 - Revisão LFT  
> Linha2 - 16 de outubro de 2026

**Resolução**

```bash
cat > prova2.txt
Linha1 - Revisão LFT
Linha2 - 16 de outubro de 2026
# Ctrl+D
cat prova2.txt
```

**Comentário:** `cat >` lê o teclado e grava no arquivo até o `Ctrl+D`. Cuidado para digitar o texto exatamente como no enunciado, com acentos e espaços.

### Questão 4

> Resolva as situações:
> a) Considerando que está dentro do diretório ~/provaLFT/avaliacao2, qual o comando correto deve ser usado para mover o arquivo prova1.txt, criado na questão anterior, para dentro de avaliacao2, alterando o nome do arquivo para questao1.txt, usando endereço relativo ? (Atenção: deve ser efetuado com um comando somente).
> b) Volte para o diretório ~/provaLFT/avaliacao1 com um comando

**Resolução**

```bash
# a) estando em ~/provaLFT/avaliacao2
cd ~/provaLFT/avaliacao2
mv ../avaliacao1/prova1.txt questao1.txt

# b) voltar para avaliacao1
cd ../avaliacao1
```

**Comentário (a):** o `..` sobe um nível (de `avaliacao2` para `provaLFT`) e `../avaliacao1/prova1.txt` aponta para a origem, **endereço relativo**. O destino `questao1.txt`, sem caminho, é o diretório atual (`avaliacao2`). Um único `mv` move e renomeia.

**Comentário (b):** `cd ../avaliacao1` também é relativo. Alternativas: `cd ~/provaLFT/avaliacao1` (absoluto) ou `cd -` (volta ao diretório anterior, imprimindo o caminho).

**No dia a dia da empresa:** `cd -` é um atalho muito usado para alternar entre dois diretórios (por exemplo, entre a pasta do código e a pasta de logs).

### Questão 5

> No diretório ~/provaLFT/avaliacao1, acrescentar novas linhas ao arquivo prova2.txt, conforme abaixo. A nova linha deverá ser inserida no final do texto já existente no arquivo prova2.txt, sem sobrescrever o conteúdo e até que seja digitado a palavra "finaliza" para encerrar a execução do comando.
> Linha 3 -  A prova será dia 23 de outubro  
> Linha 4 - Não esqueçam que a entrega das listas é até a data da prova.  
> Linha 5 -  Bons estudos!

**Resolução**

```bash
cat >> prova2.txt << finaliza
Linha 3 -  A prova será dia 23 de outubro
Linha 4 - Não esqueçam que a entrega das listas é até a data da prova.
Linha 5 -  Bons estudos!
finaliza
cat prova2.txt
```

**Comentário:** `>>` acrescenta ao final sem sobrescrever e `<< finaliza` encerra a entrada quando a palavra aparece sozinha em uma linha. Se você usasse `>` no lugar de `>>`, perderia as linhas 1 e 2.

### Questão 6

> Resolva as situações:
> a) No diretório ~/provaLFT/avaliacao1, junte o conteúdo dos arquivos questao1.txt (~/provaLFT/avaliacao2) e prova2.txt (~/provaLFT/avaliacao1), no arquivo questões1-4.txt.
> b) Ainda no diretório ~/provaLFT/avaliacao1, mova o arquivo questões1-4.txt do exercício anterior com o nome provalft.txt para ~/provaLFT/avaliacao2
> c) Sem sair do diretório ~/provaLFT/avaliacao1 Mude as permissões do arquivo provalft.txt para SEM leitura, escrita e execução outros usuários SEM alterar as permissões do usuário dono e do grupo de usuários. Posteriormente, mude o usuário dono e o grupo de usuários para root.
> d) Ainda no diretório ~/provaLFT/avaliacao1 mostre o conteúdo do arquivo provalft.txt, sem utilizar permissões de superusuário. Informe o comando completo que foi utilizado e o resultado obtido. Justifique a sua resposta com base no resultado obtido.

**Resolução (a)**

```bash
# estando em ~/provaLFT/avaliacao1
cat ../avaliacao2/questao1.txt prova2.txt > questões1-4.txt
cat questões1-4.txt
```

**Comentário (a):** o `cat` junta na ordem dos argumentos: primeiro `questao1.txt` (que está em `avaliacao2`, por isso `../avaliacao2/`), depois `prova2.txt`. O resultado é gravado no diretório atual.

**Resolução (b)**

```bash
mv questões1-4.txt ../avaliacao2/provalft.txt
ls ../avaliacao2
```

**Comentário (b):** o `mv` move e renomeia ao mesmo tempo: o destino traz o diretório (`../avaliacao2/`) e o novo nome (`provalft.txt`).

**Resolução (c)**

```bash
chmod o-rwx ../avaliacao2/provalft.txt              # tira tudo de "outros"; dono e grupo ficam como estão
sudo chown root:root ../avaliacao2/provalft.txt     # dono root e grupo root
ls -l ../avaliacao2/provalft.txt
```

**Comentário (c):** no modo simbólico, `o-rwx` mexe **somente** na classe *outros*, removendo leitura, escrita e execução, e preserva o que dono e grupo já tinham (se o arquivo era `644`, passa a `640`). O `chown dono:grupo` troca dono e grupo de uma vez e exige superusuário, por isso o `sudo`. A ordem importa: o `chmod` vem **antes**, porque depois que o dono vira `root` você perde o direito de alterar as permissões sem `sudo`.

**Resolução (d)**

```bash
cat ../avaliacao2/provalft.txt
# cat: ../avaliacao2/provalft.txt: Permission denied
```

**Resultado obtido:** `Permission denied` (permissão negada). O conteúdo não é exibido.

**Justificativa:** após o `chown`, o arquivo está `-rw-r----- root root`. Você não é o dono (`root`) e não faz parte do grupo `root`, então é tratado como **outros**, e a permissão de outros foi zerada (`---`). Sem a permissão `r`, o `cat` não consegue ler. Com `sudo cat` funcionaria, mas o enunciado proíbe usar superusuário.

**No dia a dia da empresa:** é o desenho de segurança de arquivos sensíveis: chaves, `.env` de produção, `/etc/shadow`, com dono `root` e acesso fechado a outros. Para que um serviço ou grupo específico leia sem abrir para todos, cria-se um grupo dedicado (`chgrp app arquivo` + `chmod 640 arquivo`) em vez de liberar para "outros".

---

## Cola rápida

**Navegação e arquivos**

- `pwd` mostra o diretório atual; `cd DIR` entra; `cd ..` sobe; `cd -` volta ao anterior; `cd` vai ao home.
- `ls -la` lista tudo, com detalhes; `ls -ld DIR` mostra as permissões do próprio diretório.
- `mkdir -p a/b/c` cria a cadeia; `touch arq` cria vazio; `cp -i ORIG DEST` copia; `mv ORIG DEST` move/renomeia; `rm -r DIR` apaga recursivamente.

**Ler e juntar conteúdo**

- `cat` mostra ou junta; `tac` mostra invertido; `head -n N` e `tail -n N` mostram começo e fim; `more` e `less` paginam.
- `man COMANDO` abre o manual; `apropos TERMO` pesquisa nos manuais.

**Redirecionamento**

- `>` sobrescreve; `>>` acrescenta; `<` lê de arquivo; `2>` redireciona erros; `2>&1` junta erros e saída; `2>/dev/null` descarta erros.
- `cat > arq` (termina com `Ctrl+D`); `cat >> arq << palavra` (termina em `palavra`).
- `|` liga a saída de um comando à entrada do outro (`ls -la /etc | more -25`).
- O shell trata o redirecionamento **antes** de rodar o comando: `cmd > arq` já zera `arq`.

**Permissões**

- Octal: `4` = r, `2` = w, `1` = x, somando por bloco (dono, grupo, outros). `755` = rwxr-xr-x; `644` = rw-r--r--; `700` = rwx------; `600` = rw-------; `400` = r--------; `750` = rwxr-x---.
- Simbólico: `chmod u+x arq`, `chmod g-w arq`, `chmod o-rwx arq`, `chmod a+r arq`, `chmod u=rwX,go= DIR`.
- Dono e grupo: `chown dono:grupo arq` (precisa de root); `chgrp grupo arq`.
- Diretório: `r` lista, `w` cria/apaga/renomeia, `x` entra e acessa pelo nome.
- Nunca resolva problema de permissão com `chmod -R 777`.
