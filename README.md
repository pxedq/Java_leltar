# Java_leltar
```
import java.io.File;
import java.io.FileNotFoundException;
import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.Scanner;
import java.util.TreeMap;

public class Main {
    public class Adat {
        public String megnevezes;
        public int beszerzes;
        public int darab;
        public int egysegar;

        public Adat(String sor) {
            String[] s = sor.split(";");
            megnevezes = s[0];
            beszerzes = Integer.parseInt(s[1]);
            darab = Integer.parseInt(s[2]);
            egysegar = Integer.parseInt(s[3]);
        }
    }

    private ArrayList<Adat> adatok = new ArrayList<>();

    public Main() {
        // --- 0. feladat ---
        betolt("leltar.csv");
        System.out.printf("0) A beolvasott leltári tételek száma: %d\n", adatok.size());
        int osszAr = 0;
        for (Adat a : adatok) osszAr += a.darab * a.egysegar;
        System.out.printf("   A benne lévő eszközök összára: %,d,-Ft\n", osszAr);

        // --- 1. feladat ---
        Adat legdragabb = adatok.get(0);
        for (Adat a : adatok) {
            if (a.egysegar > legdragabb.egysegar) {
                legdragabb = a;
            }
        }
        System.out.printf("1) A legdrágább eszköz: %s (%,d,-Ft)\n", legdragabb.megnevezes, legdragabb.egysegar);

        // --- 2. feladat ---
        int elsoEv = adatok.get(0).beszerzes;
        int utolsoEv = adatok.get(0).beszerzes;
        for (Adat a : adatok) {
            if (a.beszerzes < elsoEv) elsoEv = a.beszerzes;
            if (a.beszerzes > utolsoEv) utolsoEv = a.beszerzes;
        }
        System.out.printf("2) A leltár a %d-%d éveket tartalmazza\n", elsoEv, utolsoEv);

        // --- 3. feladat ---
        TreeMap<Integer, Integer> darabok = new TreeMap<>();
        for (int ev=elsoEv; ev<=utolsoEv; ev++) darabok.put(ev, 0);
        for (Adat a : adatok) {
            darabok.put(a.beszerzes, darabok.get(a.beszerzes) + a.darab);
        }
        System.out.println("3) Az egyes években vásárolt eszközök darabszáma:");
        for (int ev : darabok.keySet()) {
            if (darabok.get(ev) != 0) {
                System.out.printf("   %d : %d darab eszköz\n", ev, darabok.get(ev));
            } else {
                System.out.printf("   %d : Nincs eszköz\n", ev);
            }
        }

        // --- 4. feladat ---
        int evekszama = utolsoEv - elsoEv + 1;
        int[] evErtek = new int[evekszama];
        for (int ev = elsoEv;  ev<=utolsoEv; ev++) evErtek[ev-elsoEv] = 0;
        for (Adat a : adatok) evErtek[a.beszerzes - elsoEv] += a.darab * a.egysegar;

        int legnagyobbEv = elsoEv;
        for (int ev=elsoEv; ev<=utolsoEv; ev++) {
            if (evErtek[ev-elsoEv] > evErtek[legnagyobbEv-elsoEv]) legnagyobbEv = ev;
        }

        System.out.printf("4) A legnagyobb összértékű beszerzés éve: %d\n", legnagyobbEv);
        System.out.printf("   Ekkor a beszerzés összértéke: %,d,-Ft\n", evErtek[legnagyobbEv-elsoEv]);

        // --- 5. feladat ---
        Adat legErtek = adatok.get(0);
        for (Adat a : adatok) {
            if (a.darab  * a.egysegar > legErtek.darab * legErtek.egysegar) legErtek = a;
        }
        System.out.println("5) A legnagyobb értékű beszerzés:");
        System.out.printf("   %d darab %s = %,d,-Ft\n", legErtek.darab, legErtek.megnevezes, legErtek.darab * legErtek.egysegar );

        // --- 6. feladat ---
        PrintWriter ki = null;
        try {
            ki = new PrintWriter(new File("kezdes.txt"), "utf-8");
            for (Adat a : adatok) {
                if (a.beszerzes == elsoEv) {
                    ki.printf("%d x %s = %d,-Ft\r\n", a.darab, a.megnevezes, a.darab * a.egysegar);
                }
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (ki != null) ki.close();
        }
        System.out.println("6) Az első év adatai kiírva a kezdes.txt fájlba");
    }

    private void betolt(String fajlnev) {
        Scanner be = null;
        try {
            be = new Scanner(new File(fajlnev), "utf-8");
            be.nextLine();
            while (be.hasNextLine()) {
                adatok.add(new Adat(be.nextLine()));
            }
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        }
    }

    public static void main(String[] args) {
        new Main();
    }
}
```
### leltar.csv
```
Megnvezés;Beszerzés éve;Darab;Egységár
Íróasztal;2019;5;52000
Irodai forgószék;2019;5;34500
Árnyékoló;2020;3;22990
Acer laptop;2018;2;250000
...
```
##Feladat
```
 A leltar.csv fájl különböző eszközök adatait tartalmazza,
 pontosvesszővel elválasztva, utf-8 kódolással. VIGYÁZAT, az első sor fejléc!
 Hozzunk létre egy Leltar nevű projektet és oldjuk meg a következő feladatokat!

 0) Olvassuk be a fájl adatait egy megfelelő adatszerkezetbe,
    és jelenítsük meg a beolvasott adatok számát!.....................(2p)
    Írjuk ki leltárban lévő eszközök árának összegét!.................(1p)
 1) Keressük meg és írjuk ki melyik a legdrágább eszköz!..............(1p)
 2) Írjuk ki melyik évtől melyik évig vannak adatok a leltárban!......(1p)
 3) Jelenítsük meg az egyes években vásárolt eszközök darabszámát!....(2p)
    Amelyik évben nem volt vásárálás, ott legyen "Nincs eszköz"!......(1p)
 4) Számoljuk ki melyik évben mennyibe kerültek a beszerzések!........(2p)
    Ezek közül íjuk ki a legnagyobb összértékű beszerzés évét!........(1p)
    Írjuk ki ebben az évben a beszerzés összértékét is!...............(1p)
 5) Jelenítsük meg a legnagyobb értékű beszerzés adatait!.............(1p)
 6) Írjuk ki a kezdes.txt fájlba a mintának megfelelően
    az első évben beszerzett eszközök adatait!........................(2p)

 Minta:
 0) A beolvasott leltári tételek száma: 17
    A benne lévő eszközök összára: 2 857 560,-Ft
 1) A legdrágább eszköz: Légkondícionáló (280 000,-Ft)
 2) A leltár a 2018-2024 éveket tartalmazza
 3) Az egyes években vásárolt eszközök darabszáma:
    2018 : 5 darab eszköz
    2019 : 10 darab eszköz
    2020 : 36 darab eszköz
    2021 : Nincs eszköz
    2022 : 8 darab eszköz
    2023 : 4 darab eszköz
    2024 : 1 darab eszköz
 4) A legnagyobb összértékű beszerzés éve: 2018
    Ekkor a beszerzés összértéke: 1 097 000,-Ft
 5) A legnagyobb értékű beszerzés:
    3 darab Fujitsu számítógép = 597 000,-Ft
 6) Az első év adatai kiírva a kezdes.txt fájlba

 kezdes.txt:
 2 x Acer laptop = 500000,-Ft
 3 x Fujitsu számítógép = 597000,-Ft
```
