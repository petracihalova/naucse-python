# Klondike Solitaire: Hra

Klondike Solitaire zbývá dát konečně dohromady kousky, které jsme
v několika posledních lekcích připravovali!

V těchto materiálech najdeš hotové funkce, které je dobré si prohlédnout
a porozumět jim, ale pak si je můžeš zkopírovat do svého kódu.
Velké procvičení seznamů a slovníků přijde na konci.

## Hra

Vzpomínáš si na schéma hry?

* Rozdej balíček a sloupečky karet
* Dokud hráč nevyhrál:
  * Zobraz stav hry
  * Zeptej se hráče, odkud a kam chce hrát
  * Je-li to možné:
    * Proveď tah
  * Jinak:
    * Vynadej hráči, že daný tah nedává smysl
* Pogratuluj hráči

V Pythonu to bude vypadat následovně.
Program si ulož do modulu `hra.py`:

```python
from klondike import udelej_hru, vypis_hru, presun_kartu, presun_nekolik_karet

print()

hra = udelej_hru()

while not hrac_vyhral(hra):
    vypis_hru(hra)
    odkud, kam = nacti_tah()
    try:
        udelej_tah(hra, odkud, kam)
    except ValueError as e:
        print('Něco je špatně:', e)

vypis_hru(hra)
print('Gratuluji!')
```

K tomu, abys doplnila funkce do této hry, budeš potřebovat namodelovat
onu `hru`.
Ta se skládá z několika balíčků/sloupečků, tedy seznamů karet.
Ve výpisu butou pojmenované A-Z:

{{ figure(img=static('game.png'), alt="Ukázka sloupečků") }}

* `U` je dobírací balíček, ze kterého se doplňuje `V`.
* `V` je balíček, ze kterého můžeš brát karty
* `W-Z` jsou cílové hromádky. Cílem hry je na ně přemístit všechny
  karty.
* `A-G` jsou sloupečky, kde se karty dají přeskládávat.

Těchto 13 pojmenovaných seznamů reprezentuje celý stav rozehrané hry.
Hru proto budeme reprezentovat slovníkem, kde klíče budou písmenka
a hodnoty pak jednotlivé seznamy.

Následující funkce takovou hru vytvoří:

```python
def udelej_hru():
    """Vrátí slovník reprezentující novou hru.
    """
    balicek = vytvor_balicek()

    hra = {
        'U': balicek,
    }

    # V-Z začínají jako prázdné seznamy
    for pismenko in 'VWXYZ':
        hra[pismenko] = []

    # A-G jsou sloupečky
    for pismenko, sloupec in zip('ABCDEFG', rozdej_sloupecky(balicek)):
        hra[pismenko] = sloupec

    return hra
```

Další funkce, `vypis_hru`, hru vypíše do konzole pomocí `print`.
Výsledek bude něco jako:

```plain
   U     V          W     X     Y     Z
 [???] [   ]      [   ] [   ] [   ] [   ]

   A     B     C     D     E     F     G
 [3♣ ] [???] [???] [???] [???] [???] [???]
       [5 ♥] [???] [???] [???] [???] [???]
             [6♣ ] [???] [???] [???] [???]
                   [5♠ ] [???] [???] [???]
                         [Q ♥] [???] [???]
                               [4♠ ] [???]
                                     [3 ♦]
```

V téhle funkci není nic moc objevného a testům záleží na každé mezeře,
takže si ji určitě zkopíruj:

```python
def vypis_hru(hra):
    """Vypíše hru textově.

    Tato funkce je jen pro zobrazení, používá proto přímo funkci print()
    a nic nevrací.
    """
    print()
    print('  U     V           W     X     Y     Z')
    print('{} {}       {} {} {} {}'.format(
        popis_vrchni_kartu(hra['U']),
        popis_vrchni_kartu(hra['V']),
        popis_vrchni_kartu(hra['W']),
        popis_vrchni_kartu(hra['X']),
        popis_vrchni_kartu(hra['Y']),
        popis_vrchni_kartu(hra['Z']),
    ))
    print()
    print('  A     B     C     D     E     F     G')
    vypis_sloupecky([
        hra['A'], hra['B'], hra['C'], hra['D'], hra['E'], hra['F'], hra['G']
    ])
    print()
```

Pro kontrolu můžeš pustit testy:

* Level 70: Funkce `udelej_hru` existuje
* Level 71: Funkce `udelej_hru` funguje dle zadání
* Level 80: Funkce `vypis_hru` existuje
* Level 81: Funkce `vypis_hru` funguje dle zadání


## Načtení tahu

Hra se bude ovládat zadáním dvou jmen balíčku: odkud a kam hráč chce kartu
přesunout.

Tahle funkce není součást logiky hry. Dej ji do `hra.py`, hned za `import`.

```python
def nacti_tah():
    while True:
        tah = input('Tah? ')
        try:
            jmeno_zdroje, jmeno_cile = tah.upper()
        except ValueError:
            print('Tah zadávej jako dvě písmenka, např. UV')
        else:
            return jmeno_zdroje, jmeno_cile
```

## Zástupné funkce

K úplné hře nám chybí ještě samotná logika hry: `hrac_vyhral` a `udelej_tah`.

Aby ti hra aspoň trochu fungovala, vytvoř si zástupné funkce,
které nic nekontrolují a nenechají tě vyhrát.
Dej ji do `hra.py`, opět hned za `import`:

```python
def hrac_vyhral(hra):
    """Vrací True, pokud je hra vyhraná.
    """
    return False

def udelej_tah(hra, jmeno_odkud, jmeno_kam):
    """Udělá tah z jednoho místa na druhé.

    Místa jsou označovány velkými písmeny (např. 'A', 'V' nebo 'X').

    Není-li tah možný, vyhodí ValueError s popisem problému.
    """
    presun_kartu(hra[jmeno_odkud], hra[jmeno_kam], True)
```

Obě bude ještě potřeba upravit, ale teď už si můžeš hru víceméně zahrát!
Zkus si to!


## Jiné rozhraní

Celý tento projekt píšeš ve funkcích s daným jménem a s daným počtem a významem
argumentů.
To má dvě výhody.

První z nich je testování: připravené testy importují tvé funkce a zkouší je,
takže si můžeš být jist{{a}}, že fungují.

Druhá je zajímavější: máš-li logiku hry, funkce `udelej_hru` `udelej_tah`
a `hrac_vyhral`, napsané podle specifikací, může je použít i jakýkoli jiný
program – ne jen ten, který jsi napsal{{a}} ty.

Jeden takový si můžeš vyzkoušet:

* Nainstaluj si do virtuálního prostředí knihovnu `pyglet`, která umí ovládat
  grafická okýnka:

  ```console
  (venv)$ python -m pip install pyglet
  ```

* Stáhni si do aktuálního adresáře soubory [ui.py] a [cards.png].

  [ui.py]: {{ static('ui.py') }}
  [cards.png]: {{ static('cards.png') }}

* Hru spusť pomocí:

  ```console
  (venv)$ python ui.py
  ```

Hra považuje chyby `ValueError` za chyby uživatele, tedy tahy proti pravidlům.
Zobrazí je v terminálu a v titulku okýnka.
Ostatní chyby by ve správném programu neměly nastat; objeví se jako normální
chybové hlášky na terminálu.

*Obrázky karet jsou z [Board Game Pack](https://kenney.nl/assets/boardgame-pack)
studia [kenney.nl](https://kenney.nl).*


## Logika hry

Zbývá doplnit „pravidla hry“ do dvou funkcí, `hrac_vyhral` a `udelej_tah`.
To už bude na tobě.

### hrac_vyhral

Hráč vyhrál, pokud jsou všechny karty na cílových hromádkách `W`-`Z`.

### udelej_tah

Když tah není podle pravidel, funkce `udelej_tah` vyhodí `ValueError`.

Možné tahy:
* `U`→`V`:
  * V balíčku `U` musí něco být
  * Přesouvá se jedna karta; otočí se lícem nahoru
* `V`→`U`:
  * V balíčku U nesmí být nic
  * Přesouvají se všechny karty, seřazené v opačném pořadí;
    otočí se rubem nahoru (tj. volej dokola
    `presun_kartu(hra['V'], hra['U'], False)` dokud ve V něco je)
* Balíček `V` nebo sloupeček `A`-`G` (zdroj) → hromádka `W`-`Z`: 
  * Přesouvá se jedna karta
  * Je-li cílová hromádka prázdná:
    * Musí to být eso
  * Jinak:
    * Přesouvaná karta musí mít stejnou barvu jako vrchní karta cílové hromádky
    * Přesouvaná karta musí být o 1 vyšší než vrchní karta cílové hromádky
  * Je-li zdroj po přesunu neprázdný, jeho vrchní karta se otočí lícem nahoru
* Balíček `V` → „cílový“ sloupeček `A`-`G`
  * Přesouvá se jedna karta
  * Přesouvaná karta musí pasovat\*⁾ na cílový sloupeček
* „Zdrojový“ sloupeček `A`-`G` → „cílový“ sloupeček `A`-`G`
  * Přesouvá se několik karet
    * (zkontroluj všechny možnosti: 1 až počet karet ve zdrojovém sloupečku;
      vždy je max. jedna správná možnost) 
  * Všechny přesouvané karty musí být otočené lícem nahoru
  * První z přesouvaných karet musí pasovat\*⁾ na cílový sloupeček
* Cíl `W`-`Z` → sloupeček `A`-`G` (nepovinné – jen v některých variantách hry)
  * Přesouvá se jedna karta
  * Přesouvaná karta musí pasovat\*⁾ na cílový sloupeček

\*⁾ Kdy přesouvaná karta pasuje na sloupeček?
* Je-li sloupeček prázdný:
  * Karta musí být král
* Jinak:
  * Barva přesouvané karty musí být opačná než barva vrchní karty sloupečku, tedy:
    * Červená (♥ nebo ♦) jde dát jen na černou (♠ nebo ♣)
    * Černá (♠ nebo ♣) jde dát jen na červenou (♥ nebo ♦)
  * A zároveň musí hodnota přesouvané karty být o 1 nižší než hodnota vrchní
    karty sloupečku.
