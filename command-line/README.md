# KulWA Command Line

Materi yang akan dibahas pada KulWA tentang command line adalah berikut.

* Apa itu Command Line Interface?
* Apa itu Shell
* Command Line Argument
* Environment Variable
* Absolute vs Relative path
* Redirection
* Pipeline
* Exit Status
* Tentang .bashrc

Command line yang akan dibahas berbasis pada penggunaan Shell Bash yang populer digunakan pada Linux dan MacOS. 

Pembahasan pada KulWA ini tidak membahas pembuatan shell script tetapi sebatas penggunaan interpreter (prompt) saja. 

Disini kita juga tidak membahas perintah-perintah dasar seperti membuat file, pindah direktori dan sebagainya. Karena yang saya tekankan pada KulWA ini adalah konsep pada command line.

## Command Line Interface

Command Line Interface (CLI) adalah sebuah cara untuk berinteraksi dengan sebuah program komputer menggunakan baris perintah berbasis teks. Program disini dapat berupa Operating System (OS) itu sendiri atau sebuah program biasa yang berjalan diatas OS.

## Shell

Shell adalah sebuah program yang memungkinkan pengguna untuk berintaksi dengan OS menggunakan command line. Umumnya setiap OS memiliki shell default bawaan contohnya pada kebanyakan distro Linux adalah **Bash**. MacOS dan beberapa distro BSD juga menggunakan Bash sebagai default.

Contoh tampilan Bash shell pada linux salah satunya adalah sebagai berikut:

```
rio@localhost:~ 
```

Pada sistem operasi Unix/Unix-like biasanya digunakan simbol `$` dollar untuk menandakan interaksi dengan shell.

Hal ini berbeda dengan Windows yang bukan turunan UNIX atau UNIX-like, Windows memiliki **command.com** dan yang terbaru adalah **PowerShell** sebagai shell default untuk berintaksi dengan Windows menggunakan command line.

Contoh tampilan command prompt pada Windows adalah sebagai berikut:

```
C:\>
```

Disini kita tidak membahas program shell selain Bash misal zsh, ksh, ash dan beberapa lainnya. Kita hanya fokus pada penggunaan Bash karena salah satu shell yang paling populer.

Shell juga digunakan untuk menjalankan program lain, sehingga program tersebut berapa pada RAM OS. Hampir semua _user space_ program dijalankan menggunakan shell.

Sebagai contoh untuk menjalankan MySQL Server kita harus menjalankan program daemon yaitu `mysqld` pada shell terlebih dahulu.

## Command Line Argument

Secara sederhana argumen adalah nilai atau variable yang dibutuhkan dan ditempatkan setelah nama program dengan pemisah minimal satu karakter whitespace (spasi). 

Contoh pada program **cp** memerlukan dua argumen 1) File sumber dan 2) Lokasi tujuan.

```
$ cp /foo/bar /tmp/
       ^         ^
     Arg 1     Arg 2
```

### Command Line Option

Berbeda dengan argument, option adalah karakter yang digunakan untuk mempengaruhi eksekusi dari sebuah program, bisa terdiri dari satu huruf atau satu atau lebih kata. Masih bingung? Perhatikan contoh berikut.

```
$ rm -rf /foo/bar/
      ^
   Options
```

Pada contoh diatas kita menggunakan opsi **r** dan **f** saat melakukan penghapusan. Options yang terdiri dari satu huruf sering disebut Unix style options.

Contoh lain penggunaan options pada perintah **curl**.

```
$ curl --request POST \
--data 'username=foo&password=bar' \
'https://example.com/login'
```

Berikut penjelasan dari perintah diatas.

```
$ curl --request POST \
           ^      ^
        Option   Option Value 
        
--data 'username=foo&password=bar' \
   ^              ^
 Option         Option Value

'https://example.com/login'
          ^
       Argument 1
```

Options yang menggunakan kata sering disebut dengan GNU style options.

## Environment Variable

Environment variable digunakan untuk menyimpan konfigurasi yang mempengaruhi jalannya suatu program pada shell.

Untuk mengtahui daftar environment yang tersedia pada shell gunakan perintah env. Contoh output dari perintah tersebut adalah sebagai berikut.

```
$ env
root@localhost:~# env
XDG_SESSION_ID=155
TERM=xterm-256color
SHELL=/bin/bash
SSH_CLIENT=36.82.124.224 56002 10101
SSH_TTY=/dev/pts/2
USER=root
MAIL=/var/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
PWD=/root
LANG=en_US.UTF-8
SHLVL=1
HOME=/root
LOGNAME=root
SSH_CONNECTION=36.82.124.224 56002 139.162.39.129 10101
LC_CTYPE=UTF-8
LESSOPEN=| /usr/bin/lesspipe %s
XDG_RUNTIME_DIR=/run/user/0
LESSCLOSE=/usr/bin/lesspipe %s %s
_=/usr/bin/env
```

### PATH Variable

Salah satu environment variable yang terpenting adalah **PATH**. Nilai variable ini digunakan untuk menentukan lokasi program yang diekseksi. 

Ketika kita mengetikkan nama program pada shell misal **ls** maka shell akan mencoba mencarinya pada direktori yang ada pada variable **PATH**. Contoh jika kita menghapus nilai dari PATH maka banyak perintah yang tidak dikenali.

```
$ export PATH=
$ ls
-bash: ls: No such file or directory
```

Sebagai contoh kita akan membuat sebuah program sederhana yang menampilkan string "Hello World".

```
$ mkdir /tmp/kulwa
$ cat > /tmp/kulwa/hello.sh
#!/bin/bash

echo "Hello World"
```

Tekan Control+D untuk menyimpan. Kemudian beri flag execute.

```
$ chmod +x /tmp/kulwa/hello.sh
```

Jika kita berada pada direktori **/** dan coba menjalankan perintah ```hello.sh``` maka program tersebut tidak dikenali.

```
$ pwd
/
$ hello.sh
hello.sh: command not found
```

Sekarang coba kita masukkan direktori **/tmp/kulwa** kedalam environment variable PATH.

```
$ export PATH=/tmp/kulwa:$PATH
$ hello.sh
Hello World
```

## Absolute Path vs Relative Path

Path dalam sebuah shell mengacu pada sebuah lokasi file atau direktori sehingga dapat dikategorikan dalam dua tipe yaitu absolute dan relative. 

Absolute path atau full path adalah lokasi yang dilihat dari root direktori sehingga posisi direktori sekarang tidak berpengaruh. Diawali **/** pada Linux/MacOS/Unix dan C:\ (atau huruf drive lain) pada Windows.

Contoh absolute path untuk program ```ls``` adalah ```/bin/ls``` ketika kita berada pada direktori /home adalah.

```
$ pwd
/home
$ /bin/ls /
bin   dev  home  lib64 	     media  opt   root 	sbin  sys  usr
boot  etc  lib 	 lost+found  mnt    proc  run  	srv   tmp  var
```

Relative Path adalah lokasi yang dilihat dair posisi direktori saat ini. Direktori saat ini ditandai dengan single dot "." sedangkan direktori parent ditandangan dengan double dot "..".

Contoh relative path untuk program ```ls``` ketika kita berada pada direktori /home adalah kita perlu naik satu direktori.

```
$ pwd
/home
$ ./../bin/ls /
bin   dev  home  lib64 	     media  opt   root 	sbin  sys  usr
boot  etc  lib 	 lost+found  mnt    proc  run 
```

Penggunaan tanda "./" adalah opsional tetapi diperlukan kita akan mengeksekusi sebuah program pada direktori yang sama. Sebagai contoh mari kita eksekusi program **hello.sh** yang telah kita buat sebelumnya.

```
$ cd /tmp/kulwa
$ ./hello.sh
Hello World
```

Tanda ./ pada perintah ```./hello.sh``` menandakan bahwa kita mengeksekusi program bernama ```hello.sh``` pada direktori saat ini dalam kasus ini adalah kita sedang berada pada ```/tmp/kulwa```.

## Redirection

Redirection adalah sebuah proses untuk pengalihan dari satu file descriptor ke lainnya. Didalam shell secara default terdapat tiga buah file descriptor yaitu Standard Input (STDIN), Standard Output (STDOUT) dan Standard Error (STDERR).

Setiap file descriptor memiliki kode masing-masing, ketiganya adalah sebagai berikut.

- STDIN (Keyboard) -> File descriptor 0
- STDOUT (Screen) -> File descriptor 1
- STDERR (Screen) -> File descriptor 2

### Simbol Redirection

**Simbol ```>```** digunakan untuk mengalihkan output ke sebuah file. Jika file tersebut belum ada maka akan dibuat dan jika file tersebut terdapat konten maka akan ditimpa.

Perhatikan contoh berikut.

```
$ echo "Sekarang tanggal $( date )" > /tmp/tanggal.txt
$ cat /tmp/tanggal.txt
Sekarang tanggal Fri Mar  1 06:59:22 WIB 2019
```

**Simbol ```>>```** digunakan untuk mengalihkan output ke sebuah file. Jika file tersebut belum ada maka akan dibuat dan jika file tersebut terdapat konten maka akan ditambahkan.

```
$ echo "Baris satu" > /tmp/myfile.txt
$ echo "Baris dua" >> /tmp/myfile.txt
Baris satu
Baris dua
```

**Simbol ```<```** digunakan untuk mengambil input dari file bukan STDIN.

Setiap aplikasi yang mengambil input dari STDIN dapat menggunakan simbol ini sebagai alternatif input. Kita ambil contoh program penghitung kata ```wc```.

```
$ wc -w < /tmp/myfile
    4
```

Lalu bagaimana dengan STDERR? File descriptor ini mirip dengan STDOUT hanya berbeda kode dan program yang benar harusnya menuliskan error ke STDERR bukan ke STDOUT. 

Perhatikan contoh berikut.

```
$ ls -l /foo/bar > /tmp/error.txt
ls: /foo/bar: No such file or directory
$ cat /tmp/error.txt
```

Dapat kita lihat bahwa output dari perintah ls yang error tidak tersimpan ke file /tmp/error.txt padahal sudah output sudah diredirect kesana. Mengapa demikian?

Hal itu dikarenakan ketika program ```ls``` menghasilkan error (file tidak ditemukan) ia tidak menuliskan ke STDOUT melainkan ke STDERR. Mari kita coba lagi dengan menangkap STDERR.

```
$ ls -l /foo/bar 2> /tmp/error.txt
$ cat /tmp/error.txt
ls: /foo/bar: No such file or directory
```

Alternatif lain adalah kita mengalihkan STDERR ke STDOUT lalu mengalihkan STDOUT ke file.

```
$ ls -l /foo/bar > /tmp/error.txt 2>&1
$ cat /tmp/error.txt
ls: /foo/bar: No such file or directory
```

Pada perintah diatas kita meredirect STDERR ke STDOUT dengan perintah **2>&1** baru output STDOUT kita redirect ke /tmp/error.txt.

**TIPS**: Jika kita ingin meredirect output tetapi tidak ingin menyimpan ke file, kita dapat menggunakan special file **/dev/null**. 

```
$ echo "KulWA PHPID Surabaya" > /dev/null
```

File /dev/null adalah semacam "blackhole" file yang tidak akan menyimpan apapun.

## Pipeline

Pipeline adalah sebuah Inter-Process-Communication (IPC) yang disediakan oleh shell agar output dari sebuah proses dapat menjadi input untuk proses yang lain. Pipeline adalah salah satu fitur krusial dari sebuah shell.

Kita ambil contoh penggunaan pipe secara sederhana yaitu dengan mengubah huruf kecil menjadi huruf besar memanfaatkan program **tr**.

```
$ echo "phpid surabaya" | tr '[:lower:]' '[:upper:]'
```

Contoh yang lebih kompleks misal kita ingin mendapatkan process id (PID) dari MySQL daemon yaitu mysqld. Kita akan memanfaatkan beberapa program dan melakukan piping antar proses agar mendapatkan hasil yang diinginkan.

```
$ ps -e | grep mysqld
 3441 ?        03:09:39 mysqld
```

Untuk mendapatkan PID dari mysqld kita akan menggunakan program **awk** dengan menampilkan hanya kolom pertama.

```
$ ps -e | grep mysqld | awk '{print $1}'
3441
```

## Exit Status

Setiap program yang selesai dieksekusi oleh shell akan mengembalikan exit code. Berikut ini adalah exit code yang dikenali.

**Exit code 0** -> Tidak ada error  
**Exit code selain 0** -> Terjadi error saat program dijalankan

Dapat dilihat hanya terdapat dua exit code 0 dan selain 0. Untuk mengetahui exit code dari sebuah program yang dieksekusi pada shell digunakan varibel special ```$?```.

Perhatikan contoh berikut.

```
$ ls /
$ echo $?
0
$ ls /foo/bar
$ echo $?
2
```

Kapan kita memerlukan exit code ini? Satu waktu kita mungkin ingin mengeksekusi beberapa program sekaligus hanya jika eksekusi program yang sebelumnya sukses dijalankan.

```
$ mkdir /tmp/kulwa/logs && \
cd /tmp/kulwa/logs && \
touch data.log
```

Pada contoh perintah diatas:  
1. Pertama kita membuat direktori dahulu  
2. Kemudian kita pindah ke direktori yang baru saja dibuat  
3. Terakhir kita membuat sebuah file dalam direktori baru tersebut

Tanda **&&** digunakan jika kita ingin mengeksekusi perintah yang dilakukan hanya jika perintah sebelumnya sukses dijalankan. 

Selain itu terdapat tanda **||** yang digunakan jika kita ingin mengeksekusi perintah yang dilakukan jika perintah sebelumnya terdapat error.

```
$ touch /foo.txt 2> /dev/null || \
echo "File tidak dapat dibuat, kemungkinan permission error"
```

## Tentang .bashrc

Ketika Bash shell dijalankan ia akan mencoba membaca special file ~/.bashrc. Jadi ketika kita ingin menambahkan custom PATH atau alias atau custom function yang lain kita dapat meletakkan pada file ~/.bashrc.

### Menginstall Beberapa Versi Program



## Tentang

| | |
| ----- | ----------------------------------------- |
| Judul | KulWA Command Line Linux - PHPID Surabaya |
| Penulis | Rio Astamal \<rio@rioastamal.net> |
| Format | Markdown |
| URL | https://github.com/rioastamal-examples/
| Lisensi | [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/)
