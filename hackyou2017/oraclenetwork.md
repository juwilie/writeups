Oracles Network (Network 200)
====

Task: nc 109.233.56.90 11068

Нужно написать бота для игры в "Быки и коровы", чтобы он выиграл какое-то количество раз.

Гугление дает нам такого бота: https://rosettacode.org/wiki/Bulls_and_cows/Player

НО, в этом боте не хватает нуля в цифрах, бот будет отваливаться, если повезет и в числе будет ноль, так что добавляем ноль и допиливаем работу с nc.

```python
from itertools import permutations
from random import shuffle
import socket 

try:
    raw_input
except:
    raw_input = input
try:
    from itertools import izip
except:
    izip = zip
 
digits = '0123456789'
size = 5
 
def parse_score(score):
    score = score.strip().split(',')
    return tuple(int(s.strip()) for s in score)
 
def scorecalc(guess, chosen):
    bulls = cows = 0
    for g,c in izip(guess, chosen):
        if g == c:
            bulls += 1
        elif g in chosen:
            cows += 1
    return bulls, cows
 
choices = list(permutations(digits, size))
shuffle(choices)
answers = []
scores  = []

sock = socket.socket()
sock.connect(('109.233.56.90', 11068))
data = sock.recv(1024)
print(data)
data = sock.recv(1024)
print(data)

print ("Playing Bulls & Cows with %i unique digits\n" % size)

print('\nFirst:\n') 

count = 1

while True:
    score = ''
    ans = choices[0]
    answers.append(ans)
    #print ("(Narrowed to %i possibilities)" % len(choices))
    print("Trying %s..." % ''.join(ans))
   # print("Guess %2i is %*s. Answer (Bulls, cows)? "
    #                  % (len(answers), size, ''.join(ans)))
    tosend = ''.join(ans)+'\n'
    sock.send(bytes(str.encode(tosend)))
    data = sock.recv(1024)
    #print(data)
    if str.encode('number is') in data:
        print("GUESSED!")
        count = count + 1
        print('\nNumber number %d\n' % count)
        data = sock.recv(1024)
        print(data)
        choices = list(permutations(digits, size))
        shuffle(choices)
        answers = []
        scores  = []
        continue
    data2 = sock.recv(1024)
   # print(data2)
    a = [chr(data[17]),chr(data[24])]
    print("Bulls %c, Cows %c..." % (a[0], a[1]) )
    score = ''.join(a)
    score = score[:1] + ',' + score[1:]
    #print(score)
    score = parse_score(score)
    scores.append(score)
    choices = [c for c in choices if scorecalc(c, ans) == score]
    if not choices:
        print ("Bad scoring? nothing fits those scores you gave:")
        print ('  ' +
               '\n  '.join("%s -> %s" % (''.join(an),sc)
                           for an,sc in izip(answers, scores)))
        choices = list(permutations(digits, size))
        shuffle(choices)
        answers = []
        scores  = []
        break

```
