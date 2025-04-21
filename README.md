# Assembly.manajemen
```nasm
section .text
    global _start
```
🔸 section .text
Ini memberitahu assembler bahwa bagian ini berisi kode program (instruksi yang akan dieksekusi oleh CPU).
🔸 global _start
Instruksi ini digunakan supaya _start bisa dikenali sebagai titik awal program, seperti main() di bahasa C. Linker butuh tahu dari mana eksekusi dimulai.
```

_start:
    ; Membuat file (creat)
    mov eax, 8              ; syscall: creat
    mov ebx, nama_file      ; nama file
    mov ecx, 0777           ; permission: rwx
    int 0x80

    mov [fd_out], eax       ; simpan file descriptor
```
🧠 Penjelasan:

eax = 8 → syscall nomor 8 adalah creat, untuk membuat file baru.
ebx = nama_file → nama file yang ingin dibuat (filesaya.txt).
ecx = 0777 → izin akses file (rwx untuk user/group/other).
int 0x80 → memanggil sistem operasi untuk mengeksekusi syscall.
mov [fd_out], eax → hasilnya (file descriptor) disimpan ke variabel fd_out.
```
    ; Menulis pesan ke dalam file
    mov eax, 4              ; syscall: write
    mov ebx, [fd_out]       ; file descriptor
    mov ecx, pesan          ; buffer isi
    mov edx, len            ; panjang isi
    int 0x80
```
🧠 Penjelasan:

eax = 4 → syscall nomor 4 adalah write, untuk menulis data.
ebx = [fd_out] → ambil file descriptor dari file yang tadi dibuat.
ecx = pesan → alamat awal data yang mau ditulis (bellshade assembly).
edx = len → panjang data yang akan ditulis.
int 0x80 → minta sistem operasi menulis ke file.
```
    ; Menutup file
    mov eax, 6              ; syscall: close
    mov ebx, [fd_out]
    int 0x80
```
🧠 Penjelasan:
eax = 6 → syscall untuk menutup file.
ebx = [fd_out] → file descriptor dari file yang mau ditutup.
int 0x80 → sistem akan menutup file tersebut.
```
    ; Menampilkan pesan akhir ke terminal
    mov eax, 4              ; syscall: write
    mov ebx, 1              ; STDOUT
    mov ecx, akhir_pesan
    mov edx, akhir_len
    int 0x80
```
 Penjelasan:
eax = 4 → syscall untuk menulis.
ebx = 1 → berarti STDOUT (layar terminal).
ecx = akhir_pesan → string yang mau ditampilkan (tulis ke dalam file).
edx = akhir_len → panjang string-nya.
int 0x80 → minta OS tampilkan di terminal
```
    ; Membuka kembali file untuk dibaca
    mov eax, 5              ; syscall: open
    mov ebx, nama_file
    mov ecx, 0              ; read-only
    int 0x80

    mov [fd_in], eax        ; simpan file descriptor
```
🧠 Penjelasan:
eax = 5 → syscall untuk membuka file.
ebx = nama_file → file yang mau dibuka (filesaya.txt).
ecx = 0 → mode baca (read-only).
int 0x80 → minta OS buka file.
mov [fd_in], eax → simpan file descriptor untuk membaca.
```
    ; Membaca isi file ke buffer
    mov eax, 3              ; syscall: read
    mov ebx, [fd_in]
    mov ecx, info
    mov edx, 26
    int 0x80
```
🧠 Penjelasan:
eax = 3 → syscall untuk membaca file.
ebx = [fd_in] → file descriptor yang didapat dari open.
ecx = info → buffer untuk menyimpan hasil baca.
edx = 26 → jumlah byte yang ingin dibaca.
int 0x80 → jalankan syscall.
```
    ; Menutup file
    mov eax, 6
    mov ebx, [fd_in]
    int 0x80
```
🧠 Penjelasan:

Tutup file setelah selesai membaca.
```
    ; Menampilkan isi file ke terminal
    mov eax, 4
    mov ebx, 1
    mov ecx, info
    mov edx, 26
    int 0x80
```
🧠 Penjelasan:
Menampilkan isi buffer info ke layar terminal.
📦 Hasil: Kamu akan melihat “bellshade assembly” di terminal.


```
    ; Keluar program
    mov eax, 1              ; syscall: exit
    xor ebx, ebx            ; return code 0
    int 0x80
```
🧠 Penjelasan:
eax = 1 → syscall exit
ebx = 0 → kode keluar (normal).
int 0x80 → program selesai.
```
section .data
nama_file db 'filesaya.txt', 0
pesan db 'bellshade assembly'
len equ $ - pesan

akhir_pesan db 'tulis ke dalam file', 0xA
akhir_len equ $ - akhir_pesan

section .bss
fd_out resd 1
fd_in  resd 1
info   resb 26
```

![image](https://github.com/user-attachments/assets/52ee89fd-c72e-4d8b-9fb8-89cb01c5c871)
. Kesimpulan:
Output "bellshade assembly........" muncul karena program membaca 26 byte, padahal isi file cuma 18 byte. Sisanya menampilkan karakter kosong (ditampilkan sebagai titik-titik).

- Solusi:
Ganti mov edx, 26 dengan mov edx, len agar hanya membaca sesuai panjang sebenarnya.

- Dengan begitu, output akan bersih tanpa karakter tambahan.
