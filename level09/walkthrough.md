
```
nmap 10.12.100.0/24

Nmap scan report for 10.12.100.12
Host is up (0.0060s latency).
Not shown: 994 closed tcp ports (conn-refused)
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
80/tcp  open  http
143/tcp open  imap
443/tcp open  https
993/tcp open  imaps


ssh laurie@10.12.100.12
330b845f32185747e4f8ca15d40ca59796035c89ea809fb5d30f4da83ecf45a4
```

`for IP in 10.12.100.{12..20} ; do ssh -o ConnectTimeout=3 laurie@$IP ; done`

```
laurie@BornToSecHackMe:~$ ls
bomb  README
laurie@BornToSecHackMe:~$ cat README
Diffuse this bomb!
When you have all the password use it as "thor" user with ssh.

HINT:
P
 2
 b

o
4

NO SPACE IN THE PASSWORD (password is case sensitive).
laurie@BornToSecHackMe:~$ file bomb
bomb: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.0.0, not stripped
```


```
(gdb) info func

0x08048b20  phase_1
0x08048b48  phase_2
0x08048b98  phase_3
0x08048ca0  func4
0x08048ce0  phase_4
0x08048d2c  phase_5
0x08048d98  phase_6
0x08048e94  fun7
0x08048ee8  secret_phase
0x08048f50  sig_handler
0x08048fb4  invalid_phase
0x08048fd8  read_six_numbers
0x08049018  string_length
0x08049030  strings_not_equal
0x0804908c  open_clientfd
0x08049160  initialize_bomb
0x0804917c  blank_line
0x080491b0  skip
0x080491fc  read_line
0x080492c0  send_msg
0x080494fc  explode_bomb
0x0804952c  phase_defused
```









