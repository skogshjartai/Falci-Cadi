# Falcı Cadı – İnteraktif Tarot Deneyimi

## Açıklama
**Falcı Cadı** , Processing 3 üzerinde geliştirilmiş retro piksel sanat stilinde bir interaktif tarot oyunudur. Kullanıcıya dört ana başlık (Aşk, Kariyer, Benlik, Ölüm) üzerinden kişisel bir tarot kehaneti sunar.

## Özellikler
- Karşılama sahnesinde daktilo efekti ve tıklama sesi  
- Hover ile hareketlenen konu kartları (Aşk, Kariyer, Benlik, Ölüm)  
- Karıştırılmış desteden seçilen tarot kartlarının altın çerçeve hover efekti  
- Seçilen kartın büyük boy ekran gösterimi ve daktilo eşliğinde yorum  
- Arka plan müziği, parlama ve tıklama efektleri  
- Tekrarlanabilir oynanış, her turda farklı kart sıralaması
## Kullanım
- Programı başlattığınızda cadı sizi daktilo efektiyle karşılayacak.
- Ekrana tıklayın → konu kartları (Aşk, Kariyer, Benlik, Ölüm) belirecek.
- Fareyle kartların üzerine gelin, kısa açıklamaları alt kısımdan okuyun, tıklayın.
- Tarot destesi yüklenecek, bir kart seçin.
- Seçilen kart büyük ekranda belirecek ve daktilo efektiyle yorumunuz gösterilecek.
- Yeniden oynamak için ekrana tıklayın.
 ## Kod Yapısı
- setup(): Pencere boyutu, varlık yükleme, desteyi karıştırma
- draw(): Sahne kontrolü ve çizim yönlendirme
- drawGiris(), drawKonuKartlari(), drawTarotKartlari() vs.: Sahneye özel çizim fonksiyonları
- drawTypeWithSound(): Daktilo efekti ve ses senkronizasyonu
- mousePressed(): Tıklama ile sahne geçişleri, kart seçimi, reset
- shuffleTarotOrder(), resetAll(): Yardımcı fonksiyonlar

## Gereksinimler
- [Processing 3](https://processing.org/download)  
- `processing.sound` kütüphanesi (Sketch → Import Library → Add Library → Sound)

## Kurulum
1. Bu repoyu klonlayın veya ZIP olarak indirin:  
   ```bash
   git clone https://github.com/skogshjartai/FalciCadi/upload/main
   import processing.sound.*;

SoundFile ap;             
SoundFile parlases;       
SoundFile daktiloSesi;    
SoundFile tiklama;        

PImage bg, masa, golge, metinkutusu;
PImage[] cadi = new PImage[2];
PImage[] mum = new PImage[2];
PImage[] konuKartlari = new PImage[4];

String[] tarotKartIsimleri = { "asiklar", "ay", "azize", "basrahip", "buyucu", "dunya","guc", "gunes", "imparator", "imparatorice", "joker","kadercarki", "kule", "savasarabasi", "seytan","thedeath", "yargi", "yildiz"};
PImage[] tarotArkalar;
PImage[] tarotOnler;
int[] tarotOrder;    

String[] kartAciklamalari = {
  "Kalbinin yolu karmaşıktır. Geçmişin izleri, geleceğin seçimlerini etkiler. Aşk hakkında bilmek istediğin varsa, kartlar hissettiklerini açığa çıkarabilir.",
  "Yolun çalışmakla, çabalamakla çizilmiş. Başarı, fırsat ve değişim... Kartlar, önündeki engelleri ve potansiyel adımları gösterir.",
  "Kendini tanımak, dış dünyayı anlamaktan önce gelir. Kim olduğunu, kim olabileceğini merak ediyorsan... Kartlar seni sana gösterebilir.",
  "Hayatın en kesin gerçeği, çoğu zaman en az konuşulanıdır. Ölümle ilgili sorular, sadece cesaretle sorulmalıdır. Kartlar, bilinmeyene dair semboller taşır."
};
String[] kartCadiSozleri = {
  "Demek aşk hayatını merak ediyorsun... Şimdi hisset ve bir kart seç.",
  "Kariyer yolculuğun seni düşündürüyor demek... Şimdi hisset ve bir kart seç.",
  "Kendini tanımaya mı geldin? Şimdi hisset ve bir kart seç.",
  "Ölüm üzerine düşünmek cesaret ister... Şimdi hisset ve bir kart seç."
};
String[][] yorumlar = {
    // ASIKLAR
  {
    "Bu aşk seni büyütmüyorsa, yalnızlık daha onurlu olabilir. Ama bu sefer \ngerçekten kalbinden seçmişsin gibi duruyor.",
    "Kararsızlık seni içeriden kemiriyor. Ne istediğini bilmiyorsan, hiçbir yere varamazsın.",
    "Kendinle barış içinde değilsin çünkü başkasının gözlerinden bakıyorsun kendine.",
    "Seçim yaptın. Bazı yollar kapandı. Ama bu kapanış, ardında pişmanlık bırakmazsa gerçek bir vedadır."
  },

  // AY
  {
    "Kalbini kemiren bir şeyler var ama adını koyamıyorsun. Sezgilerin sana fısıldıyor; \nduymazdan gelme.",
    "Gölge oyunları içinde kaldın. İş yerinde herkes dürüst değil. \nNe gördüğüne değil, ne hissettiğine güven.",
    "Bilinçaltında bir fırtına kopuyor. Bastırdığın duygular seni yönetmeye başlamış bile.",
    "Ölüm sandığın şey aslında yüzleşemediğin korkun. Kabullen, hafifleyeceksin."
  },
  // AZIZE
  {
    "Aşkının altında söylenmemiş çok şey var. Ama bazen sırlar sevgiye hizmet eder.",
    "Bilgeliğinle dikkat çekiyorsun ama sessiz gücün tehdit kabul ediliyor olabilir.",
    "Derinlerde bilge bir yanın var, ama hala onu görecek cesaretin yok.",
    "Gerçek sessizlik, bazı defterlerin kapanmasıdır. İçin içini yemesin, olan oldu."
  },
  // BASRAHIP
  {
    "Aşkın bir kural kitabına sıkışmış gibi. Kalbinin sesini bastıran şey gelenek mi korku mu?",
    "Düzen seni koruyor ama boğuyor da. Sınır çizmek başarı getirir ama cesaret ister.",
    "Sana öğretilen her şey doğru değil. Kendi yolunu çizmenin zamanı geldi.",
    "Kurallar yıkılmak için var. Geçmişin ağırlığını sırtından at."
  },
  // BUYUCU
  {
    "Kendi cazibene bile dikkat et. Ne istediğini bil ve ona büyüyle değil, yürekle git.",
    "Konuşmadan insanları ikna ediyorsun. Ama oyun oynuyorsan döner dolaşır seni bulur.",
    "Gücünü yanlış yerden alıyorsun. Kendine büyü değil, dürüstlük et.",
    "Eski yöntemlerin işe yaramıyor. Yepyeni bir benlik doğmak üzere."
  },
    // DUNYA
  {
    "Bu sefer tamamlandın. Aradığın kişiyi değil, kendini buldun.",
    "Her şey bir döngüydü. Emeklerinin karşılığını alıyorsun ama bu son değil; \nyeni bir faz.",
    "Artık kendini reddetmeyi bırak. Olduğun haliyle de tamamsın.",
    "Sonuna geldin ama bu son, yıkım değil. \nBu sefer sonrasında yıkılmadan devam edebileceksin."
  },
  // GUC
  {
    "Sevgiyle yaklaşmak kolay değil. Ama bağırarak sevilmez kimse, hatırla.",
    "Direncin etkileyici ama bazen bırakmak da cesaret ister.",
    "Kontrol sende değilken bile ayakta kaldın. Şimdi kendi alanını sahiplenme vakti.",
    "Tükendin sandın ama hala ayaktasın. Bu da güç sayılmaz mı?"
  },
  // GUNES
  {
    "Bu sefer gerçekten parlıyorsun. Aşk içinde değil, senin içinde yeşeriyor.",
    "Çabaladın ve sonunda görünür oldun. Herkes ışığını fark ediyor artık.",
    "Kendini saklamaktan yoruldun. Artık sahne senin.",
    "Uykudan uyanır gibi. Karanlık gitti; artık bahar sende kalacak."
  },
  // IMPARATOR
  {
    "Duygularını kontrol altına aldın ama o hisler hâlâ orada. Bastırmak çözüm değil.",
    "İş dünyası bir savaşsa, sen komutansın. Ama askerlerini de dinlemeyi unutma.",
    "Kontrol arzunla bastırdığın benliğin artık dile geliyor. Dinle onu.",
    "Çevreni kontrol ettin, şimdi içini toparla. Dış otorite iç disiplinsizliği örtmez."
  },
  // IMPARATORICE
  {
    "Bir sevgi, bir şefkat sarmış seni. Ama dikkat et, bu bollukta tembellik de filizlenebilir.",
    "İlham, bereket ve üretkenlik zamanı. Ne ekersen şimdi biçersin.",
    "Kendine bakım lüks değil, zorunluluk. Artık kendini annelikle iyileştiriyorsun.",
    "Doğurganlık sadece bedenle ilgili değil. İçinden bir fikir doğmak üzere."
  },
  // JOKER
  {
    "Yeni bir aşka mı atlıyorsun? Deli cesareti mi bu yoksa hak edilmiş bir özgürlük mü?",
    "Ne yapacağını bilmiyorsun ama bu kötü bir şey değil. Bazı yollar bilinmeden yürünür.",
    "Tanımlar seni boğuyor. Kendine hiçbir etiket koymadan yaşa bu dönemi.",
    "Her şeyin bittiğini sandın. Ama senin için her bitiş, başlama bahanesi zaten."
  },
  // KADERCARKI
  {
    "Aşk döner, bazen de seni ezer. Ama bu sefer şans yanında gibi duruyor.",
    "Yukarı çıkan neyse, aşağı da inebilir. İş hayatında iniş çıkışa hazır ol.",
    "Değişim kaçınılmaz. Direnmek yerine yön ver.",
    "Kapanan bir sayfa değil bu; yepyeni bir kitap başlıyor."
  },
  // KULE
  {
    "Bir sarsıntı geliyor. Aşka dair inandığın ne varsa yerle bir olabilir.\nTemize çekmeye hazır ol.",
    "Maskeler düşüyor. İş dünyasında güvenin altı boş kalabilir.",
    "Kendin sandığın şey yıkılıyor. Korkma; gerçek sen enkazdan çıkacak.",
    "Her şey çökerken ayakta kalan tek şey gerçektir. Tutun ona."
  },
  // SAVASARABASI
  {
    "Kime güveneceğini artık sen seçiyorsun. Aşkın kontrolü senin elinde.",
    "Rakip çok, hız yüksek. Ama odaklı kalırsan kazanırsın.",
    "İçindeki yönsüzlük savaş arabasını savurabilir. Denge şart.",
    "Gidiyorsun ama nereye? Hedefsiz zafer yoktur."
  },
  // SEYTAN
  {
    "Tutku mu, bağımlılık mı? Seni tutsak edenin gözleri mi yoksa kendi arzuların mı?",
    "Sana sunulan her fırsat hayırlı değil. Özellikle çok parlıyorsa dikkat et.",
    "Özgürüm diyorsun ama zincir senin boynunda. Onu çözmeden yürüme.",
    "Bağlandığın şey seni çürütüyorsa, artık bir şeyleri yakma vakti gelmiştir."
  },
  // THEDEATH
  {
    "Bitti. Direnme. Bu aşk cenazeyi kaldırdı artık.",
    "İş bitti. Hakkını verdiysen yas tutma, yenisine alan aç.",
    "Bir benliğin öldü. Bu iyi haber; artık başka türlü biri olabilirsin.",
    "Bir dönem kapandı. Yasını tut, sonra toprağı kaz, yeni tohumlar ek."
  },
  // YARGI
  {
    "Geçmiş kapını çalıyor. Cevap vermeye hazır mısın? \nBazen ikinci şanslar ilkinden daha ağırdır.",
    "Yaptıklarının hesabı geliyor. Korkma, hak ettiğini alacaksın.",
    "Kendinle yüzleşiyorsun. Acıtıyor ama özgürleştiriyor da.",
    "Vicdanın yargıç, hafiflediğini hissedeceksin. Bir karar seni özgür bırakacak."
  },
  // YILDIZ
  {
    "Tam da bitti sandığın anda biri elini tuttu. Bu umut gerçek.",
    "Zor bir dönemden sonra gelen bu ilhamı kaçırma. Çok değerli.",
    "İçinden taşan bir ışık var. Artık kendini saklamana gerek yok.",
    "Şifalanıyorsun. Ama bu, tüm acıların boşuna olduğu anlamına gelmez.\n Hepsi seni sen yaptı."
  }
};

int hoverIndex = -1;
int selectedCard = -1;
boolean daktiloCalisiyor = false;
int sahne = 0;
int hoverIndexKonu = -1;
int hoverIndexTarot = -1;
int seciliKonu = -1;
int secilenTarotKart = -1;
boolean tarotKartAcildi = false;

PFont minecraftFont;
int typeStart;
int tarotSatir, tarotKartSayisi, tarotPerRow;
float tarotH, tarotW;
int gapX = 10, gapY = 18;

String girisMesaj = "Sen de sonunda bana geldin... Hoşgeldin!\nHangi konuda geleceğini öğrenmek istersin? ";

void setup() {
 size(800, 680);
 daktiloSesi = new SoundFile(this, "daktilo.wav");
 parlases = new SoundFile(this, "parla.mp3");
 tiklama = new SoundFile(this, "tiklama.mp3");
 ap = new SoundFile(this, "arkaplanmuzik.wav");
 ap.loop();

  bg          = loadImage("arkaplan.png");
  masa        = loadImage("masa.PNG");
  cadi[0]     = loadImage("cadi1.png");
  cadi[1]     = loadImage("cadi2.png");
  mum[0]      = loadImage("mum1.PNG");
  mum[1]      = loadImage("mum2.PNG");
  golge       = loadImage("golge.PNG");
  metinkutusu = loadImage("metinkutusu.PNG");

String[] konuKartIsimleri = {"ask", "kariyer", "benlik", "olum"};
for (int i = 0; i < 4; i++) { konuKartlari[i] = loadImage(konuKartIsimleri[i] + ".png"); }

tarotKartSayisi = tarotKartIsimleri.length;
tarotArkalar = new PImage[tarotKartSayisi];
tarotOnler   = new PImage[tarotKartSayisi];
for (int i = 0; i < tarotKartSayisi; i++) { tarotArkalar[i] = loadImage(tarotKartIsimleri[i] + "1.png"); tarotOnler[i]   = loadImage(tarotKartIsimleri[i] + ".png"); }

  tarotSatir  = 2;
  tarotPerRow = tarotKartSayisi / tarotSatir;
  tarotH      = 110;
  tarotW      = tarotH * 0.65;
  shuffleTarotOrder();

minecraftFont = createFont("Minecraftia-Regular.ttf", 14);
textFont(minecraftFont);
typeStart = millis();
}

void draw() {
background(0);
image(bg, 0, 0);

int masaY = height - masa.height - 80;  
image(masa, 0, masaY);
int mumFrame = (frameCount / 15) % 2;      
int mumY = masaY + 10;
image(mum[mumFrame], 25, mumY);
image(mum[mumFrame], 510, mumY);

int cadBoy   = 420;
int cadX = (width - cadBoy) / 2;
int cadY = (height - 80 - cadBoy) / 2 - 10;
int cadFrame = (frameCount / 20) % 2;
image(cadi[cadFrame], cadX, cadY);

if      (sahne == 0) drawGiris();
else if (sahne == 1) drawKonuKartlariSahne();
else if (sahne == 2) drawKonuSozSahne();
else if (sahne == 3) drawTarotSahne();
}

void drawGiris() {
drawMetinKutusu();
drawTypeWithSound(girisMesaj, 20, height-60, 40);

int elapsed = millis() - typeStart;
int chars   = min(girisMesaj.length(), elapsed / 40);
if (chars >= girisMesaj.length()) {
textAlign(CENTER, CENTER);
textSize(16);
fill(255);
text("DEVAM ETMEK İÇİN EKRANA TIKLA.", width/2, height/2);
textAlign(LEFT, TOP);
  }
}

void drawKonuKartlariSahne() {
drawKonuKartlari();
drawMetinKutusu();
if (hoverIndexKonu != -1) { fill(255); text(kartAciklamalari[hoverIndexKonu], 20, height-60, 760, 60); }
}

void drawKonuKartlari() {
int bosluk = 27;
int kartW   = 150;
int kartH   = 167;
int startX  = (width - (4*kartW + 3*bosluk)) / 2;
int y       = 160;
    hoverIndexKonu = -1;
    for (int i = 0; i < 4; i++) {
        int x = startX + i*(kartW + bosluk);
        boolean hovering = (mouseX > x 
                         && mouseX < x + kartW
                         && mouseY > y
                         && mouseY < y + kartH);
 if (hovering) {hoverIndexKonu = i;}
image(konuKartlari[i], x, y + (hovering ? -10 : 0), kartW, kartH);
    }
}

void drawKonuSozSahne() {
  drawMetinKutusu();
  drawTypeWithSound(kartCadiSozleri[seciliKonu], 20, height-60, 40);
  textAlign(CENTER, CENTER);
  textSize(16);
  fill(200);
  text("DEVAM ETMEK İÇİN EKRANA TIKLA", width/2, height/2);
  textAlign(LEFT, TOP);
}

void drawTarotSahne() {
drawTarotKartlari();
drawMetinKutusu();
if (tarotKartAcildi && seciliKonu >= 0) { drawTypeWithSound(yorumlar[secilenTarotKart][seciliKonu], 20, height-60, 40); }
}

void drawTarotKartlari() {
hoverIndexTarot = -1;

if (tarotKartAcildi) {
float h = 350;
float w = h * 0.65;
image( tarotOnler[secilenTarotKart], width/2 - w/2, 120, w, h ); return; }

float totalW = tarotPerRow * tarotW + (tarotPerRow - 1) * gapX;
float sx = (width - totalW) / 2;

for (int i = 0; i < tarotKartSayisi; i++) {
int row = i / tarotPerRow;
int col = i % tarotPerRow;
float x = sx + col * (tarotW + gapX);
float y = 180 + row * (tarotH + gapY);

int gi = tarotOrder[i];
image(tarotArkalar[gi], x, y, tarotW, tarotH);

boolean hovering = mouseX > x
                && mouseX < x + tarotW
                && mouseY > y
                && mouseY < y + tarotH;
if (hovering) { hoverIndexTarot = i; stroke(255, 180, 60); strokeWeight(3); noFill(); rect(x - 2, y - 2, tarotW + 4, tarotH + 4, 8); noStroke(); }
  }
}

int indexOfSelectedInOrder() {
  for (int i = 0; i < tarotKartSayisi; i++) {
    if (tarotOrder[i] == secilenTarotKart) return i;
  }
  return -1;
}

void drawMetinKutusu() {
  image(metinkutusu, 0, height-80);
  textFont(minecraftFont);
  textAlign(LEFT, TOP);
  fill(255);
}

void drawTypewriter(String full, int x, int y, int speed) {
  int elapsed = millis() - typeStart;
  int chars   = min(full.length(), elapsed/speed);
  fill(255);
  text(full.substring(0, chars), x, y);
}

void drawTypeWithSound(String full, int x, int y, int speed) {
  int elapsed = millis() - typeStart;
  int chars   = min(full.length(), elapsed/speed);
  fill(255);
  text(full.substring(0, chars), x, y);
  if (chars > 0 && chars < full.length()) {
    if (!daktiloCalisiyor) {
      daktiloSesi.loop();
      daktiloCalisiyor = true;
    }
  } else if (daktiloCalisiyor) {
    daktiloSesi.stop();
    daktiloCalisiyor = false;
  }
}

void mousePressed() {
  if (sahne == 0) {
    tiklama.play();
    sahne = 1;
    typeStart = millis();
    daktiloSesi.stop();
    daktiloCalisiyor = false;
  }
  else if (sahne == 1 && hoverIndexKonu != -1) {
    tiklama.play();
    seciliKonu = hoverIndexKonu;
    sahne = 2;
    typeStart = millis();
    daktiloSesi.stop();
    daktiloCalisiyor = false;
  }
  else if (sahne == 2) {
    tiklama.play();
    sahne = 3;
    typeStart = millis();
    daktiloSesi.stop();
    daktiloCalisiyor = false;
  }
  else if (sahne == 3 && !tarotKartAcildi && hoverIndexTarot != -1) {
    int gi = tarotOrder[hoverIndexTarot];
    secilenTarotKart = gi;
    tarotKartAcildi = true;
    typeStart = millis();
    parlases.play();
  }
  else if (sahne == 3 && tarotKartAcildi) {
    tiklama.play();
    resetAll();
  }
}

void resetAll() {
sahne = 0;
hoverIndexKonu = hoverIndexTarot = -1;
seciliKonu = secilenTarotKart = -1;
tarotKartAcildi = false;
typeStart = millis();
shuffleTarotOrder();
daktiloSesi.stop();
daktiloCalisiyor = false;
}

void shuffleTarotOrder() {
tarotOrder = new int[tarotKartSayisi];
for (int i = 0; i < tarotKartSayisi; i++) tarotOrder[i] = i;
for (int i = tarotKartSayisi-1; i > 0; i--) {
int j = (int)random(i+1);
int tmp = tarotOrder[i];
tarotOrder[i] = tarotOrder[j];
tarotOrder[j] = tmp;
  }
}
