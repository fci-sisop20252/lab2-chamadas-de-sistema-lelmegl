# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 8 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**
Há diferença, pois o printf é capaz de escrever textos para o usuário, formatar dados e é utilizado para casos que não seja necessário uma performance crítica.
E o write é capaz de escrever, além de texto, dados binários, comtrolar quando enviar os dados e possui um comportamento previsível.

**3. Qual método é mais previsível? Por quê você acha isso?**
O método mais previsível é o write, já que sempre chama a syscall após a mensagem, já o print depende de um buffer para imprimir tudo.
```


## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: _____
- Bytes lidos: _____

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
O file descriptor 3 foi usado, pois os file 0 é a entrada, o 1 para saída e o 2 para erro, logo, o 3 é para os processos que estão sendo lidos.
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
O arquivo foi lido completamente, pois ele foi fechado, ou seja concluído.
```

**3. Por que verificar retorno de cada syscall?**

```
É preciso verificar o retorno de cada syscall para garantir que não houve nenhum erro no meio do processo.
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: 0 (esperado: 25)
- Caracteres: 0
- Chamadas read(): 21
- Tempo: 0.000389 segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |                 |           |
| 64          |                 |           |
| 256         |                 |           |
| 1024        |                 |           |

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quanto maior o buffer, menor o numero de syscalls
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
Não, elas podem retornar menos se houverem interrupções ou quando faltam dados no arquivo.
```

**3. Qual é a relação entre syscalls e performance?**

```
As syscalls implicam na variação entre o kernel e o que o usuário inseriu, consumindo certa quantidade de tempo. Logo, se houverem muitas syscalls a performance é reduzida, fazendo com que o buffer seja melhor em alguns casos.
```

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000160 segundos 
- Throughput: 8325.20 KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [ ] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Por que o write pode escrever menos bytes que o solicitado
```

**2. Que flags são essenciais no open() do destino?**

```
O_WRONLY
O_CREAT
O_TRUNC
```

**3. O número de reads e writes é igual? Por quê?**

```
Sim, pois cada read gera um write correspondente com o mesmo numero de bytes.
```

**4. Como você saberia se o disco ficou cheio?**

```
O write retornaria um valor de bytes lidos negativos, mostrando que o processo foi interrompido.
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
Os dados ficarão presos no buffer, causando perda de dados e vazamento de recursos
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Syscalls funcionam como o elo entre programas e o sistema operacional, permitindo que o usuário solicite serviços ao kernel para acessar recursos restritos de maneira controlada e segura.

```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
File descriptors são usados pelo sistema para identificar arquivos, dispositivos ou conexões. Eles permitem que os programas acessem esses recursos de forma padronizada.
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Quanto maior o buffer, maior a performance pois são usadas menos syscalls, que exigem bastante performance
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** ex4_copia

**Por que você acha que foi mais rápido?**

```
Provavelmente o buffer do exercício é maior que o buffer do sistema.
```

---

## 📤 Entrega
Certifique-se de ter:
- [ ] Todos os códigos com TODOs completados
- [ ] Traces salvos em `traces/`
- [ ] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
