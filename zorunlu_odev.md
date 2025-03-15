# zorunlu_odev
; MSP430 - LED P1.0, Buton P2.1 (Low-Active)
; Butona basıldığında LED yanıp sönecek

    .cdecls C,LIST, "msp430.h"
    .text
    .global _main

_main:
    WDTCTL  =  WDTPW | WDTHOLD     ; Watchdog Timer'ı durdur
    BCSCTL1 =  CALBC1_1MHZ         ; 1 MHz saat frekansı
    DCOCTL  =  CALDCO_1MHZ         ; DCO frekansını ayarla
    
    ; Port Ayarları
    BIS.B   #00000001b, &P1DIR     ; P1.0 çıkış (LED)
    BIC.B   #00000010b, &P2DIR     ; P2.1 giriş (Button)
    
    BIS.B   #00000010b, &P2REN     ; P2.1 için pull-up/down direnci etkin
    BIS.B   #00000010b, &P2OUT     ; Pull-up direnci seçildi (high)
    
loop:
    BIT.B   #00000010b, &P2IN      ; P2.1 (buton) girişini kontrol et
    JNZ     loop                   ; Eğer butona basılmamışsa, döngüye devam et

    ; Butona basıldıysa LED’i yak-söndür
    BIS.B   #00000001b, &P1OUT     ; P1.0 (LED) açık
    CALL    #delay                 ; Bekleme
    BIC.B   #00000001b, &P1OUT     ; P1.0 (LED) kapalı
    CALL    #delay                 ; Bekleme

    JMP     loop                   ; Tekrar kontrol et

delay:
    MOV.W   #50000, R15            ; 50.000 döngü bekle
delay_loop:
    DEC.W   R15
    JNZ     delay_loop
    RET
