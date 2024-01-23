org 100h

jmp start       ; jump over data declaration
        
msg:    db      "===============KALKULATOR===============",0dh,0ah,"1-Penjumlahan",0dh,0ah,"2-Perkalian",0dh,0ah,"3-Pengurangan",0dh,0ah,"4-Pembagian", 0Dh,0Ah, '$'
msg2:    db      0dh,0ah,"Masukkan angka pertama : $"
msg3:    db      0dh,0ah,"Masukkan angka kedua : $"
msg4:    db      0dh,0ah,"Isi yang bener napa error tuh!! $" 
msg5:    db      0dh,0ah,"Hasil : $" 
msg6:    db      0dh,0ah ,'Terimakasih Telah menggunakan Kalkulator ini :) ', 0Dh,0Ah, '$'

start:  mov ah,9
        mov dx, offset msg ;pertama kita akan menampilkan pesan pertama yang dimana dia mengugnakan int21h
        int 21h
        mov ah,0                       
        int 16h  ;kemudian kita akan menggunakan int 16h untuk membaca penekanan tombol, untuk mengetahui operasi yang dia pilih
        cmp al,31h ;jadi penekanan tombol akan disimpan semua, kita akan comapre dengan 1 addition
        je Addition
        cmp al,32h
        je Multiply
        cmp al,33h
        je Subtract
        cmp al,34h
        je Divide
        mov ah,09h
        mov dx, offset msg4
        int 21h
        mov ah,0
        int 16h
        jmp start
        
Addition:   mov ah,09h  ;disini penanganan operasi penjumlahan
            mov dx, offset msg2  ;pertama kita akan menampilkan pesan ini masukkan nomor pertama juga menggunakan int 21h
            int 21h
            mov cx,0 ;kita akan memanggil nomor Input untuk menangani input Anda, karena kami akan mengambil setiap nomor secara terpisah
            call InputNo  ;pertama kita akan pindah ke cx 0 karena kita akan menambahkannya nanti di nomor Input
            push dx
            mov ah,9
            mov dx, offset msg3
            int 21h 
            mov cx,0
            call InputNo
            pop bx
            add dx,bx
            push dx 
            mov ah,9
            mov dx, offset msg5
            int 21h
            mov cx,10000
            pop dx
            call View 
            jmp exit 
            
InputNo:    mov ah,0
            int 16h ;disini menggunakan int 16h untuk membaca input nomer     
            mov dx,0  
            mov bx,1 
            cmp al,0dh ;input yang terpilih akan disimpan juga, kita akan membandingkan dengan 0d yang mewakili tombol enter, untuk mengetahui apakah dia selesai memasukkan nomor atau tidak 
            je FormNo ;jika sudah memilih nomer yg diinginkan maka data tsb akan dimasukkan kedalam database, lalu di proses dan mengeluarkan output yg anda pilih
            sub ax,30h ;disini menggunakan subtract 30 untuk convert nilai dari intput ascii ke decimal
            call ViewNo ;disini memanggil input nomer ke layar
            mov ah,0 
            push ax  
            inc cx   
            jmp InputNo ;kemudian data dikirimkan kembali ke nomor input untuk mengambil nomor lain atau tekan enter          
   

FormNo:     pop ax  
            push dx      
            mul bx
            pop dx
            add dx,ax
            mov ax,bx       
            mov bx,10
            push dx
            mul bx
            pop dx
            mov bx,ax
            dec cx
            cmp cx,0
            jne FormNo
            ret   


       
       
View:  mov ax,dx
       mov dx,0
       div cx 
       call ViewNo
       mov bx,dx 
       mov dx,0
       mov ax,cx 
       mov cx,10
       div cx
       mov dx,bx 
       mov cx,ax
       cmp ax,0
       jne View
       ret


ViewNo:    push ax ;ax dan dx akan ke database karena akan mengubah nilai di sana saat melihat kemudian akan memunculkannya kembali
           push dx ;disini hanya untuk review saja
           mov dx,ax ;kami akan memindahkan nilai ke dx sebagai interupsi 21h berharap bahwa output disimpan di dalamnya
           add dl,30h ;
           mov ah,2
           int 21h
           pop dx  
           pop ax
           ret
      
   
exit:   mov dx,offset msg6
        mov ah, 09h
        int 21h  


        mov ah, 0
        int 16h

        ret
            
                       
Multiply:   mov ah,09h
            mov dx, offset msg2
            int 21h
            mov cx,0
            call InputNo
            push dx
            mov ah,9
            mov dx, offset msg3
            int 21h 
            mov cx,0
            call InputNo
            pop bx
            mov ax,dx
            mul bx 
            mov dx,ax
            push dx 
            mov ah,9
            mov dx, offset msg5
            int 21h
            mov cx,10000
            pop dx
            call View 
            jmp exit 


Subtract:   mov ah,09h
            mov dx, offset msg2
            int 21h
            mov cx,0
            call InputNo
            push dx
            mov ah,9
            mov dx, offset msg3
            int 21h 
            mov cx,0
            call InputNo
            pop bx
            sub bx,dx
            mov dx,bx
            push dx 
            mov ah,9
            mov dx, offset msg5
            int 21h
            mov cx,10000
            pop dx
            call View 
            jmp exit 
    
            
Divide:     mov ah,09h
            mov dx, offset msg2
            int 21h
            mov cx,0
            call InputNo
            push dx
            mov ah,9
            mov dx, offset msg3
            int 21h 
            mov cx,0
            call InputNo
            pop bx
            mov ax,bx
            mov cx,dx
            mov dx,0
            mov bx,0
            div cx
            mov bx,dx
            mov dx,ax
            push bx 
            push dx 
            mov ah,9
            mov dx, offset msg5
            int 21h
            mov cx,10000
            pop dx
            call View
            pop bx
            cmp bx,0
            je exit 
            jmp exit  


