# OpenStreet mapy

**OsmAnd+**
f-droid - FOSS verze 

Rozcestník odkazů:
* http://download.geofabrik.de/
  stažení OSM dat pro celé státy, generováno denně (link přímo pro [Českou republiku](http://download.geofabrik.de/europe/czech-republic.html))


## Poznámky k mapování

### Cesty, pěšiny
`highway=path` je šíře < 2m, typicky lesní pěšiny
`highway=track` je šířka dostatečná pro projetí autem/traktorem

### Odpadkové koše, tříděný odpad

* **Odpadkové koše při zastávkách MHD**, pokud jsou součástí zastávky, neznačit jako samostatné objekty, ale jako atribut `bin=yes` k node `public_transport=platform` ([tag:public_transport=platform](https://wiki.openstreetmap.org/wiki/Cs:Tag:public_transport%3Dplatform))
* kontejnery pro **tříděný odpad**, které jsou vedle sebe neznačit jako samostatné nodes, ale jako jeden s více atributy [tag:amenity=recycling](https://wiki.openstreetmap.org/wiki/Cs:Tag:amenity%3Drecycling)


## Příprava dat pro OsmAnd
OsmAnd potřebuje vektorová data uložená ve formátu .OBF. Jenže surová data jsou uložená ve formátu .OSM (případně jako .PBF).

Pro převod je třeba OsmAndMapCreator. Ten se dá spouštět pro jediný soubor interaktivně, ale naštěstí aji automatizovaně.

Konkrétní příklad, jak provést dávkovou konverzi do .OBF:
https://help.openstreetmap.org/questions/29155/how-to-convert-an-osm-file-to-obf-via-command-line

Problém je ale v objemu dat, který se dá zpracovat najednou. Celý Česká republika má cca 700MB, ale i když jsem OsmAndMapCreatoru věnoval hodně operační paměti (`-Xms1G -Xmx6G`), stejně to nestačilo a konverze skončila na nedostatku paměti.

Nuže jsem potřeboval nejdřív velký vstupní soubor rozdělit na menší, které už se dají zpracovat.

### mkgmap-splitter
Ke stažení:

> http://www.mkgmap.org.uk/download/splitter.html

Rozdělení vstupního souboru `czech-republic-latest.osm.pbf` a výsledné části uloží do adresáře `chunks`:
```
java -jar splitter.jar --output-dir=chunks czech-republic-latest.osm.pbf
```


## Vyhledání tagů `fixme` přes Overpass
Overpass: https://overpass-turbo.eu/
Popis jazyka *Overpass QL*: https://wiki.openstreetmap.org/wiki/Overpass_API/Overpass_QL
```
[out:json][bbox:{{bbox}}];

(
  node["fixme"];
  way["fixme"];
  relation["fixme"];
  node[name~"^tbd$",i];
  way[name~"^tbd$",i];
  relation[name~"^tbd$",i];
  node[ref~"^tbd$",i];
  way[ref~"^tbd$",i];
  relation[ref~"^tbd$",i];
  node[~"."~"fixme"];
  way[~"."~"fixme"];
  relation[~"."~"fixme"];
);

out body;
>;
out skel qt;
```


## Zobrazení `fixme` tagů v mapě

To je zajímavé přdevším pro editory OpenStreet map, proto jsou hned po nainstalování OsmAndu skryté:

Jak ji zapnout:
1. Zobraz si hlavní menu `☰`
2. Klini na **Configure map**
3. Klikni na **Detaily** (je to až na konci)
4. Zatrhnout **OSM mapper assistant**

Původně jsem si myslel, že si toto renderování budu potřebovat zapnout ručně a tak jsem došel k tomuto přehledu, kde je popsané, jak je vykreslování definované:
> https://osmand.net/help/docs/Custom_Rendering_How-To.htm
