# VS-CTF-2024-Write-Up
---
## Team : L0r3m_1p5um
## Leaderboard : 240 of 964 Team
---

## Sanity Check (web)
### Deskripdi
Melakukan analisis pada website yang diberikan `https://sanity-check.vsc.tf/`. saat membuka web kita tidak dapat melakukan apa-apa, bahkan tidak dapat melakukan inspect element, maka kita menggunakkan `curl`.
```
$ curl https://sanity-check.vsc.tf/
```

### Output
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sanity Check</title>
    <style>
        body {
            background-color: #000021;
            font-family: "JetBrains Mono", monospace;
        }

        h1 {
            background: linear-gradient(to right, #fe5d00, #fe8c00);
            background-clip: text;
            -webkit-background-clip: text;
            color: transparent;
            text-align: center;
        }

        .centered {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }
    </style>
    <script>
        setInterval("debugger", 10)
    </script>
</head>

<body oncontextmenu="return false">
    <script>
        document.addEventListener('contextmenu', (e) => {
            e.preventDefault();
        }
        );
        document.onkeydown = function (e) {
            if (event.keyCode == 123) {
                return false;
            }
            if (e.ctrlKey && e.shiftKey && e.keyCode == 'I'.charCodeAt(0)) {
                return false;
            }
            if (e.ctrlKey && e.shiftKey && e.keyCode == 'C'.charCodeAt(0)) {
                return false;
            }
            if (e.ctrlKey && e.shiftKey && e.keyCode == 'J'.charCodeAt(0)) {
                return false;
            }
            if (e.ctrlKey && e.keyCode == 'U'.charCodeAt(0)) {
                return false;
            }
        }
    </script>
    <div class="centered">
        <h1>you know what to do</h1>
        <!-- vsctf{c0ngratulati0ns_y0u_viewed_the_s0urc3!...welcome_to_vsctf_2024!} -->
    </div>
</body>

</html>
```

<br> didapati flagnya **vsctf{c0ngratulati0ns_y0u_viewed_the_s0urc3!...welcome_to_vsctf_2024!}**

## Discord (misc)
### Deskripsi
Mencari flag didalam [Discord](https://discord.com/invite/agpgGW372M), pada channel #announcements terdapat sebuah flag

<br> didapati flagnya **vsctf{welcome_to_vsctf_2024!}**

## not-quite-caesar (crypto)
### Deskripsi 
Melakukan dekripsi sebuah ciphertext sebuah caesar cipher

### nqc.py
```
import random
random.seed(1337)
ops = [
    lambda x: x+3,
    lambda x: x-3,
    lambda x: x*3,
    lambda x: x^3,
]


flag = list(open("flag.txt", "rb").read())
out = []
for v in flag:
    out.append(random.choice(ops)(v))
print(out)
```

### out.txt
```
[354, 112, 297, 119, 306, 369, 111, 108, 333, 110, 112, 92, 111, 315, 104, 102, 285, 102, 303, 100, 112, 94, 111, 285, 97, 351, 113, 98, 108, 118, 109, 119, 98, 94, 51, 56, 159, 50, 53, 153, 100, 144, 98, 51, 53, 303, 99, 52, 49, 128]
```

<br> kita melakukan pemrograman inverse function pada python diatas
```
import random

random.seed(1337)

rev_ops = [
    lambda x: x - 3,
    lambda x: x + 3,
    lambda x: x // 3,
    lambda x: x ^ 3,
]

with open('out.txt', 'r') as f:
    out = eval(f.read())

flag = ''
for o in out:
    flag += chr(random.choice(rev_ops)(o))
print(flag)
```

<br> didapati flagnya **vsctf{looks_like_ceasar_but_isnt_a655563a0a62ef74}**

## intro-reversing (rev)
### Deskripsi 
Dekompilasi dengan Ghidra.
```
undefined8 main(void)

{
  int local_c;
  
  for (local_c = 0; local_c < 0x8ae; local_c = local_c + 0xca) {
    printf("%.*s\n",0xca,flag + local_c);
    sleep(0xb1aaf);
  }
  return 0;
}
```

<br> Biner untuk sleep adalah sebagai berikut.
```
001011a6 bf af 1a        MOV        EDI,0xb1aaf
                 0b 00
001011ab e8 c0 fe        CALL       <EXTERNAL>::sleep                                uint sleep(uint __seconds)
                 ff ff
```

<br> modifikasi nilai menjadi 0
```
bf af 1a 0b 00 -> bf 00 00 00 00
```

<br> menjalankan ulang `./chall`
```
                                 /$$      /$$$$$$   /$$$   /$$          /$$$$$$$$         /$$$$$$                  /$$$$$$              /$$$$$$  /$$$$$$$  /$$$$$$$   /$$                       /$$ /$$$  
                                | $$     /$$__  $$ /$$_/ /$$$$         |__  $$__/        /$$$_  $$                /$$__  $$            /$$__  $$| $$__  $$| $$____/ /$$$$                      | $$|_  $$ 
 /$$    /$$ /$$$$$$$  /$$$$$$$ /$$$$$$  | $$  \__/| $$  |_  $$   /$$$$$$$ | $$  /$$$$$$ | $$$$\ $$        /$$$$$$|__/  \ $$ /$$    /$$|__/  \ $$| $$  \ $$| $$     |_  $$   /$$$$$$$   /$$$$$$ | $$  | $$ 
|  $$  /$$//$$_____/ /$$_____/|_  $$_/  | $$$$    /$$$    | $$  | $$__  $$| $$ /$$__  $$| $$ $$ $$       /$$__  $$  /$$$$$/|  $$  /$$/   /$$$$$/| $$$$$$$/| $$$$$$$  | $$  | $$__  $$ /$$__  $$| $$  | $$$
 \  $$/$$/|  $$$$$$ | $$        | $$    | $$_/   |  $$    | $$  | $$  \ $$| $$| $$  \__/| $$\ $$$$      | $$  \__/ |___  $$ \  $$/$$/   |___  $$| $$__  $$|_____  $$ | $$  | $$  \ $$| $$  \ $$|__/  | $$/
  \  $$$/  \____  $$| $$        | $$ /$$| $$      \ $$    | $$  | $$  | $$| $$| $$      | $$ \ $$$      | $$      /$$  \ $$  \  $$$/   /$$  \ $$| $$  \ $$ /$$  \ $$ | $$  | $$  | $$| $$  | $$      | $$ 
   \  $/   /$$$$$$$/|  $$$$$$$  |  $$$$/| $$      |  $$$ /$$$$$$| $$  | $$| $$| $$      |  $$$$$$/      | $$     |  $$$$$$/   \  $/   |  $$$$$$/| $$  | $$|  $$$$$$//$$$$$$| $$  | $$|  $$$$$$$ /$$ /$$$/ 
    \_/   |_______/  \_______/   \___/  |__/       \___/|______/|__/  |__/|__/|__/       \______//$$$$$$|__/      \______/     \_/     \______/ |__/  |__/ \______/|______/|__/  |__/ \____  $$|__/|___/  
                                                                                                |______/                                                                              /$$  \ $$           
                                                                                                                                                                                     |  $$$$$$/           
                                                                      
```

<br> didapati flagnya **vsctf{1nTr0_r3v3R51ng!}**

## baby-game (algo)
### Deskripsi
Baris pertama menunjukkan panjang vertikal sel = panjang horizontal. Baris berikutnya menunjukkan x1 y1 x2 y2 sebagai posisi Caring Koala (x1, y1) dan posisi Red Panda (x2, y2). Caring Koala bergerak secara horizontal dan vertikal secara bergantian, dan jika mereka bertemu pada posisi yang sama mereka menang, jadi kirimkan program untuk menampilkan pemenangnya, dan jika benar sebuah bendera akan ditampilkan. Cobalah mendekatkan satu sama lain dan lihat apa yang terjadi.
Dalam kasus grid 2x2 seperti yang ditunjukkan di sebelah kiri, C (Caring Koala) akan bergerak maju seperti yang ditunjukkan di bawah ini, dan R (Red Panda) akan memasuki kotak lawan dan menang.
Dalam kasus susunan paling kiri dari grid 3x3 di bawah ini, C (Caring Koala) bergerak maju seperti yang ditunjukkan di bawah ini, dan C (Caring Koala) memasuki kotak lawan dan menang.
```
#include <iostream>
using namespace std;
int main() {
    int n;
    int x1, x2, y1, y2, diff;

    scanf("%d", &n);
    scanf("%d %d %d %d", &x1, &y1, &x2, &y2);
    diff = x1 - x2 + y1 - y2;
    if (diff % 2 == 0) {
        cout << "Red Panda" << endl;
    } else {
        cout << "Caring Koala" << endl;
    }

    return 0;
}
```

<br> didapati flagnya **vsctf{baby_game_pure_luck_uwu}**

## fall-guys (misc)
### Deskripsi 
Melakukan pencarian comment yang ditinggalkan pada game community fall-guys dari author: Sahuang

<br> melakukan pencarian game fall-guys, kami menemukannya di **steam** kemudian melakukan pencarian akun Sahuang dengan ciri bermain game fall-guys, didapati akunnya ber-username Sahuang97 dengan total play hours fall-guys mencapai +600 jam.

<br> didapati flagnya **vsctf{what_is_your_crown_rank}**

# Beberapa flag yang didapat sesudah event berakhir

## spinner (web)
### Deskripsi
Mencari flag pada web, pada js kita diharuskan memutar kursor sebanyak 9999 kali untuk mendapat flag
```
function sendRepeatedData() {
            const centerX = window.innerWidth / 2;
            const centerY = window.innerHeight / 2;
            const numMessages = 100000; // Number of messages to send

            for (let i = 0; i < numMessages; i++) {
                const x = Math.random() * window.innerWidth; // Random x coordinate
                const y = Math.random() * window.innerHeight; // Random y coordinate

                const message = {
                    x: x,
                    y: y,
                    centerX: centerX,
                    centerY: centerY
                };

                socket.send(JSON.stringify(message));
            }
        }

        // Call the function to send repeated data
        sendRepeatedData();
```

<br> didapat flagnya **vsctf{i_ran_out_of_flag_ideas_so_have_this_random_string_2CSJzbfeWqVBnwU5q8}**
