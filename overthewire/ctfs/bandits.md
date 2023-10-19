
<p align="center"><strong>LEVEL 0</strong> </p>
Fazer um SSH no <strong>bandit.labs.overthewire.org</strong> com usuário <strong>bandit0</strong>, senha <strong>bandit0</strong>

Comando(s): <code>ssh bandit0@bandit.labs.overthewire.org -p 2220</code>

<strong>FLAG:</strong> NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL
<br><br>

<p align="center"><strong>LEVEL 1</strong></p>
Pós fazer login no <strong>bandit1</strong> com a senha encontrada, existe um arquivo com nome de "-". Não é possivel usar cat/ls direto nele.

Comando(s): <code>ls ./-</code>

<strong>FLAG:</strong> rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi
<br><br>

<p align="center"><strong>LEVEL 2</strong></p> 
Existe um arquivo chamado "spaces in this filename", com quotes é possível referenciar apesar dos espaços sem ter problema na sintaxe.

Comando(s): <code>cat " "</code>

<strong>FLAG:</strong> aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG
<br><br>

<p align="center"><strong>LEVEL 3</strong><p align="center"> 
Mesmo com uma listagem de arquivos usando ls, não é possível encontrar nenhum arquivo. É possivel usando a flag <code>-a</code> (--all). O arquivo está ocultado (com um "." no começo do nome).

Comando(s): <code>ls -la</code>
<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>cat</code>

<strong>FLAG:</strong> 2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe
<br><br>

<p align="center"><strong>LEVEL 4</strong></p> 
A senha está num arquivo (<i>-file07</i>) com o prefixo -, mesmo truque do level 1 funciona.

Comando(s): <code>cat ./</code>
<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>strings ./</code>

<strong>FLAG:</strong> lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR
<br><br>
<p align="center"><strong>LEVEL 5</strong></p> 
A senha está armazenada no unico arquivo que humanos podem ler, não executavel e 1033 bytes de tamanho (<i>./maybehere07/.file2</i>)

Comando(s): <code>find . -type f -size -33b</code>
<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>cat</code>

<strong>FLAG:</strong> P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU
<br><br>

<p align="center"><strong>LEVEL 6</strong></p> 
A senha está armazenada em <i>algum lugar</i>, não obstante ela tem as seguintes propriedades:

<ul>
	<li>
	 Pertence ao grupo bandit6
	</li>
	<li>
	 É do usuário bandit7
	 </li>
	 <li>
	 Tem 33 bytes
	 </li>
</ul>
Comando(s): <code>find / -type f -group bandit6 -user bandit7</code>

<i>PS: quando usei o 33b não funcionou, por mais que o arquivo mostre ter 33b aparentemente tem menos.</i>

<strong>FLAG:</strong> z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S
<br><br>

<p align="center"><strong>LEVEL 7</strong></p> 
A senha está num arquivo, na linha que começa com "millionth", um grep é o suficiente.

Comando(s): <code>grep -i "millionth"</code>

<strong>FLAG:</strong> TESKZC0XvTetK0S9xNwm25STk5iWrBvP
<br><br>

<p align="center"><strong>LEVEL 8</strong></p> A senha está num arquivo cheio de caracteres, porém é a única linha única

Comando(s): <code>sort data.txt | uniq -u</code>

<i>PS: o sort organiza as linhas do texto, deixando ele em sequência das repetições</i>
<i>o uniq por si só não funciona porque ele só consegue identificar em linhas adjacentes (i.e uma linha após a outra/seguida da outra)</i>

<strong>FLAG:</strong> EN632PlfYiZbn3PhVK3XOGSlNInNE00t
<br><br>

<p align="center"><strong>LEVEL 9</strong></p> A senha está novamente num arquivo cheiod e caracteres, a senha é uma das poucas sequencias de texto legiveis, tem vários "====" antes da sequência.

Comando(s): <code>grep -f data.txt "====" -a</code>

<i>PS: o comando -a serve pra converter tudo em texto, inclusive a saida</i>

<strong>FLAG:</strong> G7w8LIi6J3kTb8A7j9LgrywtEUlyyp6s
<br><br>

<p align="center"><strong>LEVEL 10</strong></p> A senha está num arquivo, em base64

Comando(s): <code>cat data.txt | base64 -d</code>

<strong>FLAG:</strong> 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM
<br><br>

<p align="center"><strong>LEVEL 11</strong></p> 
A senha está num arquivo, porém está criptografada em ROT13 (cifra de césar com 13 caracteres desviados)

<i>PS: Existiam comandos, mas por mais que eu não entendesse os comandos, eu entendi a lógica por trás, então resolvi fazer a minha própria solução</i>

Código sujo:
```
string = "Gur cnffjbeq vf WIAOOSFzMjXXBC0KoSKBbJ8puQm5lIEi"
final_string = ""
def rot13(v):
    ord_v = ord(v.upper())
    string_rot13 = 0
    if v == ' ':
        string_rot13 = 32
    elif ord_v in range(65,91):
        if ord_v < 78 and ord_v < 91:
            string_rot13 = ord_v + 13
        elif ord_v >= 78 and ord_v < 91:
            string_rot13 = ord_v - 13
    else:
        string_rot13 = ord_v
    return string_rot13
for i in string:
    if i.islower():
        final_string += chr(rot13(i)).lower()
    else:
        final_string += chr(rot13(i))
print(final_string)

```

Comando(s): Nenhum usado, porém,

<code>"cifra" | cat | tr "$(echo -n {A..Z} {a..z} | tr -d ' ')" "$(echo -n {N..Z} {A..M} {n..z} {a..m} | tr -d ' ')"</code>

<br>&emsp; &emsp; &emsp; &emsp; &emsp; funcionaria de forma similar.
<strong>FLAG:</strong> JVNBBFSmZwKKOP0XbFXOoWyoutu8chDz5yVRv
<br><br>

<p align="center"><strong>Level 12</strong></p> 
A senha está armazenada em um arquivo que é um hex dump de um arquivo comprimido diversas vezes.

Primeiramente é necessário usar o <code>xxd</code> com a flag em <i>reverse</i> (<code>-r</code>) para conseguir os binários de volta e logo armazenar em outro arquivo.

Depois vem uma série de <code>file</code>(s) para saber qual tipo de arquivo é baseado no cabeçalho do arquivo, trocar a extensão do arquivo baseado nesse mesmo tipo e extrair com o programa apropriado para aquela extensão.

Até que apenas sobre o data8.bin, que é um texto comum, e nele tem a flag.

Comando(s): <code>xxd -r data.txt > data</code>
<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>file data</code>
<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>mv data data.gz/data.bz2/data.tar</code>
<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>gzip -d/tar -xf/bzip2 -d data</code>

<strong>FLAG:</strong> wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw
<br><br>

<p align="center"><strong>LEVEL 13</strong></p>
A senha para o próximo nível só pode ser acessada pelo usuário do próprio nível, é necessário usar uma privatekey do SSH.

Usei [esse](https://www.cloudbolt.io/blog/linux-how-to-login-with-a-ssh-private-key/) artigo de referência.

Comando(s): <code>chmod 600 sshkey.private</code>
<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>ssh -i sshkey.private bandit14@... -p 2220</code>

<strong>FLAG:</strong> fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq
<br><br>

<p align="center"><strong>LEVEL 14</strong></p>
 Após acessar a senha no caminho <i>"/etc/bandit_pass/bandit14"</i>, é necessário enviar a senha para o localhost na porta <i>30000</i>, a resposta é a flag pro próximo nivel.

Comando(s): <code>nc 0.0.0.0 30000</code>

<strong>FLAG:</strong> jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt
<br><br>

<p align="center"><strong>LEVEL 15</strong></p> 
É possível adquirir a próxima senha enviando a flag antiga pro localhost na porta 30001, dessa vez usando uma conexão SSL criptgrafada.

Comando(s): <code>openssl s_client -connect 127.0.0.1:30001</code>

<strong>FLAG:</strong> JQttfApK4SeyHwDlI9SXGR50qclOAil1
<br><br>

<p align="center"><strong>LEVEL 16</strong></p>
É como o nível 15, porém a porta que será necessário enviar a flag está entre a porta <i>31000</i> e a <i>32000</i>, sendo que as possíveis portas devem ser filtradas por conexão SSL e só uma delas tem a resposta, o resto apenas retorna o que foi enviado.

Inicialmente eu tentei rodar um nmap(1) pra achar portas nesse range, só que apesar de achar algumas portas, nada aberto. Pensei que talvez fosse um filtro mas logo percebi que não era o caso porque quando usei a flag --version-intensity 1 nenhuma porta retornou.

Então resolvi fazer e um script em bash para verificar as portas em que eu achei, o que infelizmente não me levou a lugar nenhum. Foi ai que então resolvi rodar o script com o range de 31000 a 32000 em vez da lista de portas que consegui no nmap.
```
#!/bin/bash  
  
for i in {31000..32000}; do  
    echo $i  
    if openssl s_client -connect 127.0.0.1:$i 2>&1 | grep -q "refused"; []  
    then  
        echo "Porta $i sem sucesso"  
    fi  
done
```
<i>PS: esse processo ainda é manual e inefetivo caso a quantidade de portas abertas fosse muito grande.</i>
<i>cada vez que a listagem parava uma conexão havia sido feita, que eu acaba por não poder ver pela (acredito) expressão 2>&1 na linha de comando.</i>

A primeira porta que achei foi a <i>31518</i>, que não funcionou. Porém logo após ela a <i>31790</i> foi a correta, enviei a flag e recebi uma private key SSH de volta.

Comando(s): <code>nmap -p[31000-32000] 127.0.0.1</code>
<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>openssl s_client -connect 127.0.0.0.1:porta</code>

<i># Comandos que usei pra automatizar a organização das portas num arquivo de texto logo depois de receber do nmap (não necessariamente útil):
</i><br>
<code>$ cat a.txt
31016/tcp closed ka-sddp
31020/tcp closed autotrac-acp
 ...
</code>
<code>$ cut -d "/" -f1 a.txt</code><br>
<code>$ awk '{print $1","}' ports.txt | tr '\n' ' '</code>

Usei [esse](https://stackoverflow.com/questions/16931244/checking-if-output-of-a-command-contains-a-certain-string-in-a-shell-script) artigo de referência.

<p align="center"><strong>FLAG:</strong></p>

```
-----BEGIN RSA PRIVATE KEY-----

MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ

imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ

Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu

DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW

JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX

x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD

KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl

J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd

d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC

YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A

vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama

+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT

8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx

SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd

HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt

SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A

R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi

Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg

R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu

L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni

blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU

YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM

77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b

dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3

vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=

-----END RSA PRIVATE KEY-----
```
<br><br>

<p align="center"><strong>LEVEL 17</strong></p>
Existiam dois arquivos, <i>passwords.new</i> e <i>passwords.old</i>, imediatamente eu sabia que se tratava de dizer a diferença dos dois.

Pós usar o <code>diff</code> eu apenas usei a senha do arquivo novo e consegui fazer o login via ssh.

Comando(s): <code>diff passwords.new passwords.old</code>

<strong>FLAG:</strong> hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg
<br><br>

<p align="center"><strong>LEVEL 18</strong></p> 
Quando tentei logar com ssh a sessão era instantaneamente terminada com a mensagem "Bye Bye!", ainda no <strong>bandit17</strong> rodei um <code>ls</code> na pasta do <strong>bandit18</strong> e vi um arquivo chamado <i>"readme"</i>.

Resolvi rodar o ssh novamente porém com um <code>cat readme</code> no fim, para em vez de abrir de tentar formar uma conexão e efetivamente logar no usuário, apenas obter o conteúdo do readme.

Comando(s): <code>ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme</code>

<strong>FLAG:</strong> awhqfNnAbc1naukrpqDYcF95h7HoMTrC
<br><br>

<p align="center"><strong>LEVEL 19</strong></p>
Existe um executável que consegue rodar um comando como outro usuário (<strong>bandit20</strong>), é possivel obter a senha (localizada em <i>/etc/bandit_pass/bandit20</i>) através dele.

Comando(s): <code>./bandit20-do cat /etc/bandit_pass/bandit20</code>

<strong>FLAG:</strong> VxCazJaVykI6W36BkBU0mJTCM8rR95XT
<br><br>

<p align="center"><strong>LEVEL 20</strong></p>
Existe um executável, dessa vez esse conecta-se pelo <i>localhost</i> a uma porta que você especificar e lê uma linha de texto enviada, se for a senha do nível anterior, a nova senha é enviada por esse mesmo tunel na saída.

Comando(s): <code>nc -lnvp <porta></code>

<i>PS: fiz o netcat após entrar numa outra sessão ssh/usuário na mesma rede</i>

<strong>FLAG:</strong> NvEJF7oVjkddltPSrdKEFOllh9V1IBcq
<br><br>

<p align="center"><strong>LEVEL 21</strong></p> 
Existe uma crontab condizente com o próximo nivel chamada <i>cronjob_bandit22</i> (<strong>/etc/cron.d/</strong>), nela toda vez que o sistema for reiniciar o usuário <strong>bandit22</strong> vai executar um script e enviar a saida para null.

Após verificar o programa ele simplesmente dá permissões comuns (<i>664</i>) para uma um arquivo temporario (<i>/tmp/</i>) e joga os valores da flag (<i>/etc/bandit_pass/bandit22</i>) pro arquivo.

Comando(s): <code>ls</code>
<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>cat</code>

<strong>FLAG:</strong> WdDozAdTM2z9DiFEQ2mGlwngMfj4EZff
<br><br>

<p align="center"><strong>LEVEL 22</strong></p>
Existe uma crontab condizente com o próximo nível chamada <i>cronjob_bandit23</i>, (<strong>/etc/cron.d/</strong>) como a anterior. O código nessa basicamente transforma o nome do usuário atual numa <i>hash md5</i> e retira todos os espaços, após isso cria um arquivo temporario (<i>/tmp/</i>) e envia a senha a senha do nível anterior pra ele.

O algorítimo original (do arquivo-fonte) é esse:
```
myname=$(whoami)  
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
```
Por lógica, se o <strong>bandit22</strong> (<i>whoami</i>) tem uma pasta temporária, seria possível a mesma tarefa existir pra outros usuários. Então eu tentei o mesmo comando, porém com a string "bandit23".
```
Comando(s): echo "I am user bandit23" | md5 sum | cut -d ' ' -f 1
```
<strong>FLAG:</strong> QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G
<br><br>

<p align="center"><strong>LEVEL 23</strong></p>
Esse não consegui resolver sozinho e tive que ler um walkthrough simplesmente porque esqueci que um cron é um daemon que se executa baseado num periodo de tempo.

Usei [esse](https://mayadevbe.me/posts/overthewire/bandit/level24/) artigo de referência.

Era impossível chegar a solução pelo simples fato de que eu estava tentando executar o script pelo meu usuário atual (<strong>bandit23</strong>) em vez de deixar ele <strong>se</strong> executar (como <strong>bandit24</strong>). Não tinha permissão para criar as pastas necessárias para conseguir inserir um script malicioso, e travei ai.

Comando(s): N/A

<strong>FLAG:</strong> VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar
<br><br>

<p align="center"><strong>LEVEL 24</strong></p>
Tem um daemon na porta <i>30002</i> que vai me retornar a senha pro <strong>bandit25</strong> se eu enviar a senha do <strong>bandit24</strong> com um pin de <i>4 números secretos</i>, separado por um espaço. Não tem como saber do código que não seja por bruteforce.g

Fui tentar um simples telnet para ver como funciona a interação com o servidor.

![Output do sistema](https://github.com/evildocument/writeups/blob/main/overthewire/imgs/bandit24_level1.png)

Então imeditamente, eu comecei a escrever um script em Python pra tentar resolver.

O script completo:
```
### 1  
import socket  
PASS = "VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar"  
HOST = "127.0.0.1"  
PORT = 30002  
### 1  
## 2  
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:  
    sock.connect((HOST,PORT))  
    receiving = b""   
    counter = 0  
    BUFF_SIZE = 4096  
## 2  
## 2.1  
    while True:  
        part = sock.recv(BUFF_SIZE)  
        print(part.decode('utf-8'))  
        if len(part) < BUFF_SIZE:  
            break  
## 2.1  
## 3  
    for i in range(1000, 9999+1):  
        counter = i  
        sock.sendall((PASS + ' ' + str(counter) + '\n').encode())  
        print(f"enviado: {PASS} {counter}")  
## 3  
## 4  
        receiving = sock.recv(4096)  
        string_msg = receiving.decode('utf-8').strip("\n")  
        print(string_msg)  
##4  
##4.1  
        if string_msg[:5].lower() == "wrong":  
            continue  
        else:  
            break  
##4.1  
##5  
    print(f"Sequência correta: {PASS}, {counter}")  
    print(receiving.decode('utf-8').strip("\n"))  
    sock.close()  
##5
```
<ol>
	<li>
	A primeira parte consiste apenas de definir constantes que serão usadas durante o programa e importar a biblioteca de redes que será usada para fazer o cliente para a conexão.
	</li>
	<li>
	Faz um objeto tipo socket e nomeia de "sock", a partir disso conecta (.connect) ao IP e porta especificados nas constantes de antes, além de criar a variável que será usada para receber as mensagens enviada, o contador (pin-code) e o tamanho do buffer para a proxima seção.
	</li>
		&emsp; &emsp; 2.1 Faz um loop no qual é recebido um buffer com capacidade para 4kb, e caso a informação recebida seja inferior a esse tamanho, o loop simplesmente assume que a mensagem acabou e é quebrado.
	<li>
	Faz o loop que será usado para contar de 1000 até 9999, imediatamente o contador (counter) que tinha o valor de 0 agora assume qualquer valor seja o da iteração (de 1000 até 9999), e por fim, o objeto socket (sock) envia a senha e o valor do contador (o pin-code) -- o \n é necessário, quebrei muito a cabeça pra descobrir.
	</li>
	Após tudo isso, imprime o que foi enviado para manter controle.
	<li>
	Recebe qualquer mensagem de volta (pós enviar a senha e pin-code), após isso, converte a mensagem recebida para uma string e amarzena numa variável (string_msg), essa mesma variável é impressa na tela para manter controle.
	</li>
	&emsp;&emsp; 4.1 Checa se a string (string_msg) tem nos seus seis caractéres iniciais a palavra "wrong" (A mensagem de erro do servidor), se tiver, continua com o loop, ou seja, se essa for a vez do pin-code 1000 e falhou, vai para o 1001 e por ai em diante...<br>
&emsp;&emsp;&emsp;&emsp; Caso ache, quebra o loop.
<li>
	5. Como o loop acabou (ou foi quebrado) se assume que a sequência correta foi encontrada, então é impresso a senha e o ultimo valor do contador. Após isso a variável responsável por receber mensagens (receiving) é decodificada e convertida em uma string e imprimida na tela, essa é a última mensagem do servidor após o desafio ter sido finalizado.
</li>
</ol>

Por fim, a conexão é fechada invocando o método close() no objeto sock

![Output do sistema pós flag](https://github.com/evildocument/writeups/blob/main/overthewire/imgs/bandit24_level2.png)

Comando(s): N/A

<strong>FLAG:</strong> p7TaowMYrmu23Ol8hiZh9UvD0O9hpx8d

<p align="center"><strong>LEVEL 26</strong></p> Existe um binário (bandit27-do) no diretório do bandit26, é possível usar ele para executar comandos como o bandit27.
<br><br>
Comando(s): <code>./bandit27-do cat /etc/bandit_pass/bandit27</code>
<br><br>
<strong>FLAG</strong>: YnQpBuifNMas1hcUFk70ZmqkhUU2EuaS
<br><br>

<p align="center"><strong>LEVEL 27</strong></p> Existe um repositório git no usuário bandit27-git que é possível clonar usando a senha do bandit27, a flag está no arquivo README<br><br>
		Comando(s): <code>git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo bandit27_repo</code><br><br>
<strong>FLAG</strong>: AVanL161y9rsbcJIsFHuw35rjaOM19nR
<br><br>

<p align="center"><strong>LEVEL 28</strong></p>
		Comando(s): <code>git log</code>
		<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>git show</code><br><br>
<strong>FLAG</strong>: tQKvmcwNYcFS6vmPHIUSI3ShmsrQZK8S
<br><br>


<p align="center"><strong>LEVEL 29</strong></p> 
		Comando(s): <code>git log --all -p --</code><br><br>
<br><br>
<strong>FLAG</strong>: xbhV3HpNGlTIdnjUrdAlPzc2L6y9EOnS
<br><br>


<p align="center"><strong>LEVEL 30</strong></p>
		Comando(s): <code>git tag -l</code><br><br>
  São etiquetas que demarcam um ponto (commit) que representa alguma mudança significativa no seu código, ou seja, uma versão (ou release) do seu projeto.
Sendo assim o comando <code>git reset tag2</code> irá retornar o código para o estado onde ele se encontrava no commit representado pela tag2, ou seja, nesse caso, o commit 393a7ba.<br><br>
<strong>FLAG</strong>: OoffzGDlzhAlerFJ2cAiz1D41JW1Mhmt

<p align="center"><strong>LEVEL 31</strong></p>
		Comando(s): <code>git add</code>
		<br>&emsp; &emsp; &emsp; &emsp; &emsp; <code>git push &lt;origin&gt; &lt;branch&gt;</code><br><br>
<strong>FLAG</strong>: rmCBvG56y58BXzv98yZGdO7ATVL5dW8y
<br><br>

<p align="center"><strong>LEVEL 32</strong></p> 
Ao fazer o login no ssh, você está diretamente dentro de um shell dash, sendo que todos os caracteres alfabeticos que você digita são todos interpretados como maiusculos, a solução é usar os caracteres especiais, já que não são alfabeticos e são predefinidos.
o $0 executa o nome do shell usado ao fazer login, ou seja, /bin/sh no caso da maquina. se eu quisesse VER qual shell é usado em vez de invoca-lo, teria que usar o echo antes (echo $0).<br><br>
Comando(s): <code>$0</code><br><br>
<strong>FLAG</strong>: odHo63fHiFqcWWJG9rLiLDtPm45KzUKy
<br><br>
