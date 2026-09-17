# hikerian-vysky

Výškové dlaždice Slovenska pre [Hikerian](https://github.com/murein/hikerian).

Sú to **dáta, nie kód** — repo existuje len preto, aby ich vedel servírovať
jsDelivr ako CDN.

## Čo to je

Dlaždice `256×256` v kódovaní **terrarium**:

```
výška [m] = R * 256 + G + B / 256 - 32768
```

Schéma adries je bežné Web Mercator XYZ: `{z}/{x}/{y}.png`, priblíženia
**5 až 13**.

## Odkiaľ to je

Zdrojom je **DMR 3.5** od ÚGKK SR — digitálny model reliéfu s mriežkou 10 m,
odvodený z vrstevníc, pokrývajúci celé Slovensko.

> Výškové dáta: DMR 3.5 © [ÚGKK SR](https://www.geoportal.sk/), CC BY 4.0

Mimo územia SR sú dlaždice doplnené z [AWS Terrain
Tiles](https://registry.opendata.aws/terrain-tiles/):

> Produced using Copernicus data and information funded by the European Union
> — EU-DEM layers.

## Prečo končia na z13

Priblíženie 13 je 12,5 m na pixel pri 49° s. š., čo je hrubšie než mriežka
zdroja (10 m) — a napriek tomu to stačí. DMR 3.5 je odvodený z vrstevníc a
pod ~40 m vlastný detail nemá; jeho druhá diferencia má na 10–40 m exponent
1,6–1,7, teda sa tam správa ako hladká interpolácia.

Zmerané: z14 by oproti z13 pridal od 0,08 m (Chočské vrchy) po 1,53 m RMS
(Vysoké Tatry) pri korelácii sklonov 0,97–0,99 — a stál by štvornásobok dát.

## Prečo je pokrytie obdĺžnik, nie hranica SR

S-JTSK je pootočený kužeľ, takže obdĺžnik zdrojových dát v Mercatore má rohy
bez údajov. Tam (a za hranicami) sú dlaždice doplnené z AWS a prechod je
prelnutý cez ~8 px, aby na hranici nevznikol schod vo výške. Vnútri obdĺžnika
teda dlaždica existuje vždy a aplikácia nepotrebuje ošetrovať 404.

## Presnosť zápisu

Výška je kvantizovaná na **0,25 m**. Terrarium kóduje na 1/256 m, ale ten
posledný bajt je pri prevzorkovaní šum, ktorý sa nedá komprimovať —
zaokrúhlenie zmenší dlaždicu asi trojnásobne a 0,25 m je stále hlboko pod
výškovou presnosťou DMR 3.5. Maximálna odchýlka zapísanej dlaždice od zdroja
je teda 0,125 m, čo je polovica kroku.

## Použitie

```
https://cdn.jsdelivr.net/gh/murein/hikerian-vysky@v1/{z}/{x}/{y}.png
```

Cez **tag**, nie cez vetvu: jsDelivr cachuje tag natrvalo (`immutable`), kým
`@main` preveruje po 12 hodinách.
