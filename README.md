**Sistem Interactiv de Procesare pentru Chitara**
   Proiectul consta in realizarea unui procesor de efecte digital interactiv in mediul Max MSP, conceput pentru procesarea unui semnal de chitara. Scopul principal a fost crearea unui sistem flexibil in care instrumentistul poate modifica parametrii sunetului (Overdrive, Delay, Tremolo) in timp real, folosind ecranul unui telefon mobil ca controller wireless prin protocolul OSC (Open Sound Control).

## (Instalare)
...

## (Utilizare)
...

## (Istoric)
**Realizarea proiectului**
Receptia si Separarea Datelor (udpreceive si route)
- udpreceive 9000: Acest obiect asculta portul UDP 9000 si capteaza pachetele de date OSC transmise de pe telefon.
- route /touch1 /touch2 /touch3: Separa fluxurile de date in functie de adresele OSC transmise (coordonatele pentru degetul 1, degetul 2, degetul 3), trimitandu-le spre ramurile corespunzatoare de efecte.

Despachetarea Coordonatelor (unpack 0. 0.)
- Aplicatia de pe telefon trimite coordonatele sub forma unei liste de doua numere de tip float (axa X si axa Y).
- Am folosit unpack 0. 0. pentru a desface aceasta lista in doua mesaje individuale. Argumentul 0. (cu punct) este critic, deoarece ii specifica obiectului ca datele extrase trebuie tratate ca numere zecimale (float), pastrand acuratetea pozitiilor de pe ecran. Prima iesire (axa X) este folosita mai departe pentru modularea parametrilor (cum ar fi frecvența filtrului svf~ sau viteza pentru cycle~).

Logica de Detectare a Deconectarii (> -1. si sel 1)
Aceasta sectiune rezolva o problema importanta de control: detectarea momentului in care degetul paraseste ecranul telefonului.
- Atat timp cat degetul se afla pe ecran, valorile transmise sunt pozitive. Cand degetul este ridicat, aplicatia trimite valoarea -1. pentru a semnala absenta atingerii (dupa cum se vede in mesajul de test /touch1 -1. -1.).
-  > -1. (Mai mare decat -1): Acest operator logic verifica fluxul de date. Daca degetul este pe ecran, conditia este adevarata si obiectul scoate valoarea 1. Daca degetul este ridicat (si valoarea devine -1), conditia este falsa si obiectul scoate 0.
- sel 1 (Select): Prinde valoarea 1 (adica starea in care degetul este activ pe ecran) si trimite un impuls de tip bang. Acest impuls comanda obiectul selector~ 4, schimband canalele audio si activand efectul corespunzator degetului care atinge ecranul in acel moment.

Mentinerea Starii si Filtrarea Parametrilor (if si clip)
-  if $f1 >= 0. then $f1: Obiectul este utilizat pentru a crea functia de Freeze. Cand ridic degetul de pe telefon si valoarea sare la -1., blocul if opreste aceasta valoare negativa. El lasa sa treaca mai departe doar numerele mai mari sau egale cu 0.. In acest fel, parametrii efectelor raman blocati pe ultima valoare pozitiva valida, prevenind prabusirea sunetului sau salturile bruste.

-  clip 0. 1. / clip 0. 0.9: Limiteaza valorile intr-un interval fix de siguranta pentru a preveni distorsiunile digitale extreme sau feedback-ul infinit in modulele de delay (tapin~ / tapout~) si overdrive.
-  scale: Adapteaza intervalul standard de pe telefon (0-1) la valorile reale de care au nevoie obiectele audio (de exemplu, mapeaza intervalul 0. 1. in frecvente de la 200 Hz la 3000 Hz pentru filtrul de tip state-variable svf~).


## (Link-uri)
...

# Dezvoltarea proiectului

Pentru început:

1. Creează-ți cont pe Github
2. Download și install [Github Desktop](https://desktop.github.com/)
3. Citește [acest ghid](https://charlesmartin.com.au/blog/2020/08/09/student-project-repository) și ține la îndemână [Markdown Cheat Sheet](https://www.markdownguide.org/cheat-sheet).

Apoi, procesul este următorul (inspirat de [aici](https://cs.anu.edu.au/courses/comp1720/deliverables/05-major-project/#submission-process)):

1. *fork* al acestui template către propriul tău cont de Github

![](assets/fork.gif)

_(dacă preferi cumva ca repo-ul să nu fie vizibil de către public, îl poți seta ca Private din Settings - "Change visibility". Atunci trebuie să mă adaugi drept colaborator, ca eu să am acces.)_

2. *clone* al repo-ului din Github Desktop pentru a-l downloada local

![](assets/clone.gif)

3. *commit* și *push* pe măsură ce lucrezi la proiect. Ultima versiune push-ată pe server înainte de deadline va conta pentru evaluare.

![](assets/commit.gif)

## Elemente obligatorii

1. Acest readme completat. Titlu, descriere, mod de utilizare, istoric, link-uri utile.

   Poți include și imagini și chiar [gif-uri animate](https://www.screentogif.com/), sau link-uri către materiale audio/video.
   
   Vezi [aici](https://charlesmartin.com.au/blog/2020/08/09/student-project-repository) mai multe sugestii.

2. [Declarația de originalitate](statement-of-originality.yml) completată. Tot ce nu este inclus acolo va fi considerat 100% contribuție proprie.

    *(formatul este adaptat de [aici](https://gitlab.cecs.anu.edu.au/comp1720/2018/comp1720-2018-major-project/-/blob/master/statement-of-originality.yml). Da, este un pic ironic să refolosim un doc [de altundeva](https://cs.anu.edu.au/courses/comp1720/resources/faq/#how-do-i-fill-out-my-statement-of-originality), dar menționăm sursa deci nu este plagiat!)*

3. Proiectul în sine. Tot codul trebuie să fie prezent, proiectul trebuie să poată rula conform instrucțiunilor din readme. Dacă e nevoie de asset-uri mari (sunete, video etc), [folosește Git LFS](https://git-lfs.github.com/) sau include link de download în instrucțiunile de instalare.

