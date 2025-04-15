# oak-quiz

![Screenshot 2025-04-15 095331](https://github.com/user-attachments/assets/9b349d4f-a34a-4913-9335-b5055646b342)



section .data
   
    pesan db "alokasi 16 kb dari memori", 10
    
    len equ $ - pesan

section .text
    
    global _start

_start:
    
    ; syscall brk: dapatkan alamat heap awal
   
    mov eax, 45
    
    xor ebx, ebx
    
    int 0x80

    add eax, 16384      ; alokasi 16KB = 16*1024
    mov ebx, eax
    mov eax, 45
    int 0x80

    cmp eax, 0
    jl exit             ; jika gagal alokasi, keluar

    mov edi, eax        ; alamat paling akhir dari heap
    sub edi, 4
    mov ecx, 4096       ; 4096 dword = 16KB
    xor eax, eax        ; isi dengan 0
    std
    rep stosd           ; isi memori dengan 0
    cld

    ; tampilkan pesan
    mov eax, 4          ; syscall write
    mov ebx, 1          ; stdout
    mov ecx, pesan      ; alamat pesan
    mov edx, len        ; panjang pesan
    int 0x80
exit:
  
    mov eax, 1          ; syscall exit
    
    
    xor ebx, ebx        ; status 0
    
    int 0x80


# Penjelasan Kode:

# Bagian .data

section .data
    
    pesan db "alokasi 16 kb dari memori", 10
   
    len equ $ - pesan

1. pesan: String yang akan ditampilkan ke layar.

2. 10 = ASCII newline (\n), untuk pindah baris.

3. len: Panjang dari string, dihitung otomatis dari awal sampai akhir pesan.

# Bagian .text (kode program)

section .text

global _start

1. section .text: Bagian tempat kode dijalankan.

2. global _start: Memberi tahu linker bahwa program dimulai dari label _start.

# _start – Titik awal eksekusi
 1. Alokasikan memori (menggunakan syscall brk)

mov eax, 45         ; syscall number untuk brk

xor ebx, ebx        ; EBX = 0 → minta alamat akhir heap sekarang

int 0x80            ; jalankan syscall

- Memanggil syscall brk(0) → meminta alamat akhir heap sekarang.

add eax, 16384      ; tambahkan 16KB ke alamat heap

mov ebx, eax        ; EBX = alamat baru yang ingin kita alokasikan

mov eax, 45         ; syscall brk lagi

int 0x80

- Menambahkan 16KB (16384 byte) ke heap, lalu memanggil brk lagi untuk mengalokasikannya.

cmp eax, 0

jl exit             ; keluar jika error

- Jika brk gagal, program langsung keluar.

 2. Isi memori dengan 0

mov edi, eax        ; EDI = akhir alamat heap yang dialokasikan

sub edi, 4          ; mundur 4 byte karena kita akan mengisi dari belakang

mov ecx, 4096       ; 4096 dword (4 byte) = 16KB

xor eax, eax        ; nilai yang akan ditulis (0)

std                 ; set arah pengisian dari belakang ke depan

rep stosd           ; isi ECX kali: [EDI] = EAX (0)

cld                 ; reset arah pengisian ke default (maju)


- Mengisi seluruh blok memori 16KB dengan nilai 0.

- stosd = store double word (4 byte).

- rep stosd = ulangi stosd sebanyak ecx kali.

3. Tampilkan pesan ke layar 

mov eax, 4          ; syscall write

mov ebx, 1          ; file descriptor 1 = stdout

mov ecx, pesan      ; alamat pesan

mov edx, len        ; panjang pesan

int 0x80

- Menampilkan string "alokasi 16 kb dari memori" ke terminal.

4. Keluar dari program

exit:

mov eax, 1          ; syscall exit

xor ebx, ebx        ; status keluar = 0

int 0x80

- Mengakhiri program dengan status 0.









