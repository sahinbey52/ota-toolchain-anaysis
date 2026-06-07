* DOSYA: udp-server.z1 (Projede kullanılan)
1. Dosyanın ELF Sınıfı, Mimarisi ve Giriş Adresi

Komut: file udp-server.z1
Çıktı: udp-server.z1: ELF 32-bit LSB executable, TI msp430, version 1 (embedded), statically linked, with debug_info, not stripped

Komut: msp430-readelf -h udp-server.z1
Çıktı:
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 ff 00 00 00 00 00 00 00 00 
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            Standalone App
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Texas Instruments msp430 microcontroller
  Version:                           0x1
  Entry point address:               0x3100
  Start of program headers:          52 (bytes into file)
  Start of section headers:          71928 (bytes into file)
  Flags:                             0x10000001
  Size of this header:               52 (bytes)
  Size of program headers:           32 (bytes)
  Number of program headers:         5
  Size of section headers:           40 (bytes)
  Number of section headers:         20
  Section header string table index: 17

Çıktılardan ELF sınıfı "ELF32", mimarisi "TI msp430" ve sistem başladığında işlemci veya bootloader'in ilk başlayacağı adres ise "0x3100" olarak görünmektedir.

2. Bölüm Analizi
Komut: msp430-readelf -S udp-server.z1
Çıktı:
There are 20 section headers, starting at offset 0x118f8:

Section Headers:
  [Nr] Name              Type            Addr     Off    Size   ES Flg Lk Inf Al
  [ 0]                   NULL            00000000 000000 000000 00      0   0  0
  [ 1] .text             PROGBITS        00003100 0000d4 00b7b2 00  AX  0   0  2
  [ 2] .rodata           PROGBITS        0000e8b4 00b888 00068b 00   A  0   0  4
  [ 3] .data             PROGBITS        00001100 00bf14 00014e 00  WA  0   0  2
  [ 4] .bss              NOBITS          0000124e 00c062 00175c 00  WA  0   0  2
  [ 5] .noinit           NOBITS          000029aa 00c062 000002 00  WA  0   0  2
  [ 6] .vectors          PROGBITS        0000ffc0 00c062 000040 00  AX  0   0  1
  [ 7] .comment          PROGBITS        00000000 00c0a2 000030 01  MS  0   0  1
  [ 8] .debug_aranges    PROGBITS        00000000 00c0d8 000318 00      0   0  8
  [ 9] .debug_info       PROGBITS        00000000 00c3f0 001b75 00      0   0  1
  [10] .debug_abbrev     PROGBITS        00000000 00df65 0009be 00      0   0  1
  [11] .debug_line       PROGBITS        00000000 00e923 00106d 00      0   0  1
  [12] .debug_frame      PROGBITS        00000000 00f990 000274 00      0   0  4
  [13] .debug_str        PROGBITS        00000000 00fc04 000473 01  MS  0   0  1
  [14] .debug_loc        PROGBITS        00000000 010077 0016a4 00      0   0  1
  [15] .debug_ranges     PROGBITS        00000000 01171b 000108 00      0   0  1
  [16] .gnu.attributes   LOOS+ffffff5    00000000 011823 000011 00      0   0  1
  [17] .shstrtab         STRTAB          00000000 011834 0000c4 00      0   0  1
  [18] .symtab           SYMTAB          00000000 011c18 0047d0 10     19 410  4
  [19] .strtab           STRTAB          00000000 0163e8 003e1a 00      0   0  1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings)
  I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
  O (extra OS processing required) o (OS specific), p (processor specific)

.text: "00003100" adresinden başlamaktedır. Sistemin ilk başladığı yer burasıdır ve program kodlarının yer aldığı kısımdır.
__.rodata:__ "0000e8b4" adresinden başlamaktadır. String ve const gibi salt okunur verilerin bulunduğu kısımdır.
__.data:__ "00001100" adresinden başlamaktedır. Başlangıç değeri atanmış global değişkenlerin bulunduğu kısımdır.
__.bss:__ "0000124e" adresinden başlamaktadır. Başlangıç değeri atanmamış global değişkenlerin bulunduğu kısımdır.
__.noinit:__ "000029aa" adresinden başlamaktadır. Sisteme reset atıldığında içeriği silinmeyen, başlangıç değeri olmayan değişkenlerin bulunduğu kısımdır.
__.vectors:__ "0000ffc0" adresinden başlamakteadır. Kesme geldiğinde dallanılacak olan fonksiyonların adreslerinin bulunduğu kısımdır.
__.comment:__ Derleyici bilgilerinin yer aldığı kısımdır ve cihaza yüklenmeyeceği için adres alanı yoktur.

3. Hafıza Analizi (Kod ve Veri Boyutları)
Komut: msp430-size udp-server.z1
Çıktı:
   text	   data	    bss	    dec	    hex	filename
  48765	    334	   5982	  55081	   d729	udp-server.z1

text: ".text", ".rodata" ve ".vectors" kısımlarından oluşmaktadır ve 48765 byte bu kısımların toplam boyutudur.
data: ".data" kısmından oluşur ve ondalık olarak 334 byte boyutundadır.
bss: ".bss" ve ".noinit" kısımlarından oluşur ve 5982 byte bu kısımların toplam boyutudur.

Buradan cihaz donanımının yeterli olup olmadığı kontrol edilebilir.
Gerekli ROM alanı: text + data = 49099 byte = 47,95 kB
Gerekli RAM alanı: bss + data = 6316 byte = 6,17 kB

4. Sembol Tablosu ve Anlamlı Semboller
Komut: msp430-readelf -s udp-server.z1
Çıktı:
Symbol table '.symtab' contains 1149 entries:
   Num:    Value  Size Type    Bind   Vis      Ndx Name
     0: 00000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 00003100     0 SECTION LOCAL  DEFAULT    1 
     2: 0000e8b4     0 SECTION LOCAL  DEFAULT    2 
     3: 00001100     0 SECTION LOCAL  DEFAULT    3 
     4: 0000124e     0 SECTION LOCAL  DEFAULT    4 
     5: 000029aa     0 SECTION LOCAL  DEFAULT    5 
     6: 0000ffc0     0 SECTION LOCAL  DEFAULT    6 
     7: 00000000     0 SECTION LOCAL  DEFAULT    7 
     8: 00000000     0 SECTION LOCAL  DEFAULT    8 
     9: 00000000     0 SECTION LOCAL  DEFAULT    9 
    10: 00000000     0 SECTION LOCAL  DEFAULT   10 
    11: 00000000     0 SECTION LOCAL  DEFAULT   11 
    12: 00000000     0 SECTION LOCAL  DEFAULT   12 
    13: 00000000     0 SECTION LOCAL  DEFAULT   13 
    14: 00000000     0 SECTION LOCAL  DEFAULT   14 
    15: 00000000     0 SECTION LOCAL  DEFAULT   15 
    16: 00000000     0 SECTION LOCAL  DEFAULT   16 
    17: 00000000     0 FILE    LOCAL  DEFAULT  ABS contiki-main.c
    18: 00003380     0 NOTYPE  LOCAL  DEFAULT    1 __br_unexpected_
    19: 00000000     0 FILE    LOCAL  DEFAULT  ABS adxl345.c
    20: 000037e2    18 FUNC    LOCAL  DEFAULT    1 status
   ...
   1129: 000069f8    52 FUNC    GLOBAL DEFAULT    1 list_remove
  1130: 000000d9     0 NOTYPE  GLOBAL DEFAULT  ABS __UCB1CTL1
  1131: 0000122e     8 OBJECT  GLOBAL DEFAULT    3 sensors
  1132: 00000059     0 NOTYPE  GLOBAL DEFAULT  ABS __CACTL1
  1133: 000010f6     0 NOTYPE  GLOBAL DEFAULT  ABS __TLV_DCO_30_TAG
  1134: 00000020     0 NOTYPE  GLOBAL DEFAULT  ABS __P1IN
  1135: 0000001c     0 NOTYPE  GLOBAL DEFAULT  ABS __P4IN
  1136: 0000733a    40 FUNC    GLOBAL DEFAULT    1 packetbuf_attr_clear
  1137: 0000008b     0 NOTYPE  GLOBAL DEFAULT  ABS __ADC12MCTL11
  1138: 00006886   246 FUNC    GLOBAL DEFAULT    1 link_stats_input_callback
  1139: 000011ca    12 OBJECT  GLOBAL DEFAULT    3 simple_udp_process
  1140: 0000013e     0 NOTYPE  GLOBAL DEFAULT  ABS __SUMEXT
  1141: 00002966     2 OBJECT  GLOBAL DEFAULT    4 uip_slen
  1142: 00000031     0 NOTYPE  GLOBAL DEFAULT  ABS __P5OUT
  1143: 0000781e    10 FUNC    GLOBAL DEFAULT    1 queuebuf_numfree
  1144: 00002968    52 OBJECT  GLOBAL DEFAULT    4 uip_udp_conns
  1145: 0000d724    28 FUNC    GLOBAL DEFAULT    1 uipbuf_add_ext_hdr
  1146: 0000ce7a    44 FUNC    GLOBAL DEFAULT    1 uip_sr_free_all
  1147: 00006712     6 FUNC    GLOBAL DEFAULT    1 leds_init
  1148: 00007426    18 FUNC    GLOBAL DEFAULT    1 platform_init_stage_one

Burada derleyicinin ve bağlayıcının kod içindeki tüm fonksiyon adlarını, global değişken isimlerini, kaynak dosya adlarını ve donanımsal yazmaçları (register) hafıza adresleriyle eşleştirdiği sembol tablosu görünmektedir. "Num" tablodaki indeks numarasını, "Value" bellek adresini, "Size" sembolün bellekte kapladığı alanı, "Type" sembolün neyi temsil ettiğini, "Bind" sembolün görünürlük alanını, "Vis" sembolün dışarıya karşı görünürlüğünü, "Ndx" sembolün Bölüm Analizi'nde incelenen hangi ELF bölümü içinde yer aldığını ve "Name" sembolün temsil ettiği nesnenin gerçek adını göstermektedir.
Tabloda Contiki-NG işletim sisteminin ana programı "contiki-main.c", "__P1IN" ve "P5OUT" şeklinde donanımsal pinler ve UDP protokolünü yöneten "simple_udp_process" programlarının varlığı göze çarpmaktadır. Liste çok uzun olduğu için sadece başı ve sonu incelenmiştir.

6. Kesme Vektörleri veya Başlangıç Adresi ile İlişkili Bilgiler
Komut: msp430-objdump -d udp-server.z1
Çıktı:
udp-server.z1:     file format elf32-msp430

Disassembly of section .text:

00003100 <__watchdog_support>:
    3100:	55 42 20 01 	mov.b	&0x0120,r5	
    3104:	35 d0 08 5a 	bis	#23048,	r5	;#0x5a08
    3108:	82 45 aa 29 	mov	r5,	&0x29aa	

0000310c <__init_stack>:
    310c:	31 40 00 31 	mov	#12544,	r1	;#0x3100
...
Disassembly of section .vectors:

0000ffc0 <__ivtbl_32>:
    ffc0:	80 33 80 33 80 33 80 33 80 33 80 33 80 33 80 33     .3.3.3.3.3.3.3.3
    ffd0:	80 33 80 33 80 33 80 33 80 33 80 33 80 33 80 33     .3.3.3.3.3.3.3.3
    ffe0:	fa 36 7e 37 48 35 cc 35 80 33 80 33 80 33 b2 37     .6~7H5.5.3.3.3.7
    fff0:	2e 36 92 37 dc 37 80 33 08 36 80 33 80 33 00 31     .6.7.7.3.6.3.3.1

Burada sistemin başladığı adres olan "0x3100" adresinde başlangıçta "Watchdog Zamanlayıcıyı" ayarlayan ve RAM temizliği gibi işlemlerin başlatılmasını sağlayan "__watchdog_support" fonksiyonu görülmektedir. En altta ile kesme vektörleri yer almaktadır. "fffe" ve "ffff" adresleri ters olarak yan yana koyulduğunda sistemin başladığı "3100" adresi görülmektedir. Buradan "fffe" ve "ffff" adreslerindeki vektörün sistemi yeniden başlatma vektörü olduğu anlaşılmaktadır.

8. ELF ve Binary Açıklaması

Yukarıdaki başlıklarda yapılan incelemeler de dikkata alındığında incelenen dosya içerisinde sadece makine kodu değil ayrıca başlıklar, bölüm tablosu, sembol tablosu gibi birçok veri içerdiği görülmektedir. Ancak binary dosyalarında sadece makine kodu bulunmaktadır. Bu sebeple bu firmware dosyaları  "ELF executable" olarak değerlendirilmektedir.

9. String Analizi
Komut: msp430-strings udp-server.z1
Çıktı:
...
ADXL345 sensor
Accelerometer process
Button
CC2420 driver
Main
INFO
[%-4s: %-10s] 
Starting Contiki-NG-release/v5.1-96-g713e72f54-dirty
- Routing: %s
- Net: %s
- MAC: %s
- 802.15.4 PANID: 0x%04x
- 802.15.4 Default channel: %u
Node ID: %u
Link-layer address: 
Tentative link-local IPv6 address: 
CSMA
Ctimer process
Event timer
(NULL LL addr)
%02x
INFO
[%-4s: %-10s] 
CC2420 CCA threshold %i
Join
Local repair
Global repair
Poison routes
Significant rank update
Got parent
Heard old version from root
Inconsistent DIO version
Multicast DIS
Reachable
Init root
DAO max rtx
RPL Lite
Sensors
sicslowpan
Simple UDP process
Stack
[%-4s: %-10s] 
Check failed: %u vs. %u
Stack check
TCP/IP stack
TMP102 sensor
new-firmware.z1
INFO
[%-4s: %-10s] 
Paket %u alindi ve yazildi.
Tum paketler alindi. Yeni firmware dogrulamasi basliyor...
Hesaplanan CRC32: %08lX
Gelen CRC32: %08lX
Yuklenmeye hazir yeni firmware alimi tamamlandi. CRC OK!
Butunluk Hatasi! Beklenen: %08lX, Hesaplanan: %08lX
Dosya siliniyor...
HATA: Flasa yazma hatasi!
HATA: Flasa yazma hatasi! new-firmware.z1 dosyasi acilamadi.
HATA: Checksum hatasi! Paket: %u
UDP server
(NULL IP addr)
::FFFF:%u.%u.%u.%u
xmem_erase: bad size
xmem_erase: bad offset
(null)
% $J
6~7H5

Buradan elde kaynak kodlar olmadan aktif olan sensörler, kullanılan işletim sistemi, çalışma esnasında ekrana basılan loglar, yazılımın ne yapmaya çalıştığı gibi bilgilere ulaşılabilmektedir. Projede kullanılan alıcı düğüm incelendiği için paket alımıyla ilgili loglar görünmektedir.
