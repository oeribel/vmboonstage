# VMBO on stage


Hieronder volgt 


## 1. Voorbereiding
1. Download en installeer Blender vanaf https://www.blender.org/
2. Open Blender
3. Click splashscreen weg
4. Ga naar Tab scripting
5. Sluit de overbodige deelvensters door in de header van het deelvenster op de rechtermuis te clicken en dan `close area`
6. Click op `new`  
Het scherm ziet er ongeveer als volgt uit
![](./images/screenshot_1.png)
7. typ in het text/script/python venster aan de rechterkant, het volgende:  
    ```
    import bpy

    if __name__ == "__main__":
        print('hello world')
    ```
    _Als Blender nu vanuit de commandline opgestart wordt, het bestand  geopent en het script uitgevoerd wordt, zal er op de commandline hello world verschijnen.  
    Dit kan alleen met een omweg in Blender zelf getoond worden en dat is voor nu een brug te ver_

## Inlezen van een bestand
1. Het openen en lezen van het bestand:
    ```
    sheet = open('C:\Users\berend\Desktop\cijfers.csv', 'r')
    ```
2. Het 'opdelen' van het bestand per regel
    ```
    regels = sheet.readlines()
    ```
3. Een count en een loop over de regels
    ```
    regelnr = 0
    for regel in regels:
        regelnr += 1
        print("Regel {}: {}".format(regelnr, regel.strip()))
    ```
In totaal hebben we dan dus:
```
import bpy

if __name__ == "__main__":
    sheet = open('C:\Users\berend\Desktop\cijfers.csv', 'r')
    regels = sheet.readlines()

    regelnr = 0
    for regel in regels:
        regelnr += 1
        print("Regel {}: {}".format(regelnr, regel.strip()))
```

## Inlezen eerste regel : vakken

1. Check of we de eerste regel te pakken hebben
    ```
    if regelnr == 1:
    ```
2. Het 'opdelen' van de regel in vakken
    ```
        vakken = regel.split(";")
    ```
3. Loop over de vakken
    ```
        vaknr = 0
        for vak in vakken:
            vaknr += 1
            if vaknr > 1:
                print("Vak {}: {}".format(vaknr, vak.strip()))
    ```
In totaal hebben we dan dus:
```
import bpy

if __name__ == "__main__":
    sheet = open('C:\Users\berend\Desktop\cijfers.csv', 'r')
    regels = sheet.readlines()

    regelnr = 0
    for regel in regels:
        regelnr += 1
        if regelnr == 1:
            vakken = regel.split(";")
            vaknr = 0
            for vak in vakken:
                vaknr += 1
                if vaknr > 1:
                    print("Vak {}: {}".format(vaknr, vak.strip()))
```

## Herschrijven eerste regel

1. Haal de eerste regel uit de lijst met regels
    ```
    regelVakken = regels[0]
    ```
2. Splits de regel in vakken door ; en loop daaroverheen vanaf het element met index 0
    ```
    for vak in regelVakken.split(";")[1:]:
        print(vak)
    ```
In totaal hebben we dan dus:
```
import bpy

if __name__ == "__main__":
    sheet = open('C:\Users\berend\Desktop\cijfers.csv', 'r')
    regels = sheet.readlines()

    regelVakken = regels[0]
    for vak in regelVakken.split(";")[1:]:
        print(vak)
```

## Invoegen tekst
1. We maken een methode
    ```
    def invoegenTekst(inTeVoegenTekst):
        print(inTeVoegenTekst)
    ```
2. We meken een object text in Blender
    ```
    def invoegenTekst(inTeVoegenTekst, naamTekst):
        tekst = bpy.data.curves.new(naamTekst, "FONT")
        obj = bpy.data.objects.new(naamTekst, tekst)
        bpy.context.collection.objects.link(obj)
        tekst.body = inTeVoegenTekst
    ```
 
> ### _Reset_
> _Omdat we de methodes vaker aanroepen nu we aan het ontwikkelen zijn, worden er elke keer extra objecten aangemaakt. Om dit te voorkomen maken we een kleine methode die onze workspace elke keer reset. Maak de volgende methode aan en roep deze elke keer als eerste aan_
>
> _De methode:_
> ```
> def reset():
>     objs = bpy.data.objects
>     with bpy.context.temp_override(selected_objects=objs):
>         bpy.ops.object.delete()
> ```
> _De aanroep:_
> ```
> if __name__ == "__main__":
>     reset()
> ```
3. De tekst op de juiste locatie zetten
    1. Voeg een extra argument toe aan de methode met default waarde
    ```
    def invoegenTekst(inTeVoegenTekst, naamTekst, posY = 0):
        ...
        obj.location = (0, posY, 0)
    ```
    2. Geef het extra argument mee
    ```
    vaknr = 0
        for vak in regelVakken.split(";")[1:]:
            vaknr += 1
            invoegenTekst(vak, vak, vaknr)
    ```
In totaal hebben we dan dus:
```
import bpy

def invoegenTekst(inTeVoegenTekst, naamTekst, posY = 1):
    tekst = bpy.data.curves.new(naamTekst, "FONT")
    obj = bpy.data.objects.new(naamTekst, tekst)
    obj.location = (0, posY, 0)
    bpy.context.collection.objects.link(obj)
    tekst.body = inTeVoegenTekst
    
def reset():
    objs = bpy.data.objects
    with bpy.context.temp_override(selected_objects=objs):
        bpy.ops.object.delete()

if __name__ == "__main__":
    reset()
    sheet = open('/home/berend/Graphics/blender/models/_school/cijfers.csv', 'r')
    regels = sheet.readlines()
    
    regelVakken = regels[0]
    vaknr = 0
    for vak in regelVakken.split(";")[1:]:
        vaknr += 1
        invoegenTekst(vak, vak, vaknr)
```