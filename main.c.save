/* Téma matice a moduly - varianta s polem pevných rozměrů
 *
 * Autor: Roman Grmela 6.G
 * Datum: 6.11. 2024
 *
 * Verze: 2.1
 * Errata:
 * - maticeVymenRadky: viz záhlaví souboru matice.h
 * - maticeCtiZeSouboru: viz záhlaví souboru matice.h
 * - maticeCtiZeVstupu: viz záhlaví souboru matice.h
 * - testFileRW: Opraveno nesmyslné alokování prázdné matice před čtením
 *   ze souboru.
 */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>
#include <ctype.h>
#include <math.h>
#include <float.h>
#include <time.h>

#include "gvid.h"
#include "matice.h"


/** \brief Otestuje operace maticeAlokuj, maticeRandom a maticeTiskni. */
void testInit(void)
{
  printf("==========================================\n");
  printf("Test vytvoreni a inicializace matice\n");
  Tmatice * matice = maticeAlokuj(50000000, 30000000);
//  Tmatice * matice = maticeAlokuj(5, 3);
//  maticeNastavPrvky(matice, 1.2);
  if (matice == NULL)
  { // Matici se nepovedlo vytvořit, tak vytisknu proč a skončím.
    maticeAktualniChyboveHlaseni(stderr);
    return;
  }
  maticeRandom(matice);
  maticeTiskni(matice);

  maticeUvolni(matice);

  printf("==========================================\n");
}


/**
 * @brief Tiskne řešení soustavy rovnic, které je uloženo v poli neznámých x.
 *
 * Tato funkce vytiskne všechny hodnoty neznámých x, které jsou výsledkem výpočtu
 * Gauss-Seidelovy metody, tedy řešení soustavy lineárních rovnic.
 *
 * @param x Pole obsahující řešení soustavy rovnic (hodnoty neznámých).
 * @param radku Počet řádků matice, tj. počet neznámých v soustavě rovnic.
 */
void tiskReseniPole(float *x, int radku) {
    printf("Reseni soustavy rovnic:\n");
    for (int i = 0; i < radku; i++) {
        printf("x[%d] = %f\n", i, x[i]);
    }
}




/**
 * @brief Funkce ověřuje, zda je čtvercová část rozšířené matice diagonálně dominantní podle řádků.
 *
 * @param m Ukazatel na rozšířenou matici soustavy.
 * @return true, pokud je čtvercová část matice řádkově diagonálně dominantní, jinak false.
 */
bool jeDDM(Tmatice *m) {
    for (int i = 0; i < m->radku; ++i) {
        float diagonalniPrvek = fabs(m->prvek[i][i]);
        float sumaOstatnich = 0.0;

        // Procházíme pouze čtvercovou část matice, tj. matici bez posledního (rozšířeného) sloupce.
        for (int j = 0; j < m->radku; ++j) {
            if (j != i) {
                sumaOstatnich += fabs(m->prvek[i][j]);
            }
        }

        // Kontrola diagonální dominance pro aktuální řádek
        //DIAGONALNI ABSOL. H. MUSI BYT OSTRE VETSI NEZ SOUCET OSTATNICH ABSOLUTNICH HODNOT
        if (diagonalniPrvek <= sumaOstatnich) {
            return false; // Řádek není diagonálně dominantní
        }
    }
    return true; // Všechny řádky čtvercové části matice splňují podmínku diagonální dominance
}



bool testDDMatice(Tmatice* m){
   if(!jeDDM(m)){
    printf("Matice neni diagonalne dominantni!\n");
    printf("-------------------------------------------\n");

    return false;
   }

   else{
    printf("Matice je diagonalne dominantni.\n");
    printf("-------------------------------------------\n");

    return true;
   }
}



/**
 * @brief Úprava matice pro iterativní výpočet: vydělí každý řádek diagonálním prvkem
 *        a nastaví diagonální prvky na 0. Úprava se týká celého řádku, včetně absolutního členu.
 *
 *        Nemusíme testovat, zda je diagonální prvek nulový, protože matice je řádkově diagonálně
 *        dominantní a tedy všechny diagonální prvky jsou nenulové.
 *
 * @param m Ukazatel na rozšířenou matici soustavy.
 */
void upravMatici(Tmatice *m) {
    for (int i = 0; i < m->radku; ++i) {
        float diagonalniPrvek = m->prvek[i][i];

        // Vydělíme celý řádek včetně absolutního členu diagonálním prvkem
        for (int j = 0; j < m->sloupcu; ++j) {
            m->prvek[i][j] /= diagonalniPrvek;
        }

        // Nastavíme diagonální prvek na 0
        m->prvek[i][i] = 0.0;
    }
}




/**
 * @brief Vypočítá novou hodnotu neznámé x[r] v rámci Gauss-Seidelovy metody.
 *
 * Tato funkce počítá hodnotu x[r] na základě vzorce:
 *  x[r] = b[r] - Σ (a[r][j] * x[j]) pro j ≠ r,
 * kde b[r] je hodnota v posledním sloupci řádku r a a[r][j] jsou koeficienty matice soustavy.
 * Na diagonále matice jsou nuly, takže pro j = r se součet nezmění.
 *
 * @param m Ukazatel na matici soustavy, která obsahuje koeficienty a hodnoty b.
 * @param x Pole neznámých, jejichž hodnoty budou počítány.
 * @param r Index řádku, pro který se počítá hodnota x[r].
 *
 * @return Nová hodnota x[r] vypočtená podle Gauss-Seidelovy metody.
 */
float vypocetXr(Tmatice *m, float *x, int r)
{
    float sum = 0.0;

    // Sčítáme součty pro všechny prvky v řádku
    for (int j = 0; j < m->radku; j++) {
        sum += m->prvek[r][j] * x[j]; // Součet součinů pro všechny j
    }

    // Výpočet nové hodnoty x[r] podle vzorce
    return m->prvek[r][m->sloupcu - 1] - sum; // b[r] - suma(a[r][j] * x[j])
}





/**
 * @brief Aplikuje Gauss-Seidelovu metodu pro řešení soustavy lineárních rovnic.
 *
 * Tato funkce implementuje Gauss-Seidelovu iterační metodu, která se používá k aproximaci řešení
 * soustavy lineárních rovnic. Předpokládá, že matice soustavy je diagonálně dominantní, a v průběhu
 * iteračních kroků počítá nové hodnoty neznámých v poli x. Proces iterace pokračuje, dokud není dosaženo
 * požadované přesnosti ε pro všechny neznámé.
 *
 * V každé iteraci se vypočítá nová hodnota pro každý řádek matice na základě předchozích hodnot
 * v poli x. Funkce používá k testování přesnosti logickou proměnnou, která určuje, zda všechny hodnoty
 * x byly dostatečně přesné pro daný krok.
 *
 * @param m Ukazatel na matici soustavy, která obsahuje koeficienty a hodnoty b.
 * @param eps Požadovaná přesnost pro ukončení iterací.
 * @param x Pole neznámých, které se postupně aktualizuje v průběhu iterací.
 */
void reseniGS(Tmatice *m, float eps, float *x)
{
    bool jePresny = false;

    // Iterace pokračují, dokud není dosaženo požadované přesnosti pro všechny neznámé
    while (!jePresny) {
        jePresny = true; // Na začátku každé iterace si optimisticky předpokládáme, že všechny hodnoty jsou přesné

        // Pro každý řádek
        for (int r = 0; r < m->radku; r++) {
            float xpred = x[r]; // Uložíme si předchozí hodnotu pro tento řádek ..POMOCNÁ PROMĚNNÁ

            // Vypočteme novou hodnotu pro x[r] podle Gauss-Seidelovy metody
            x[r] = vypocetXr(m, x, r);

            // Kontrola přesnosti pro tento řádek, logická operace AND
            jePresny = jePresny && (fabs(xpred - x[r]) < eps); // Pokud je rozdíl větší než eps, nastaví se jePresny na false
        }//konec for cyklu..
    }
}




/**
 * Funkce reseniJacobi řeší soustavu lineárních rovnic pomocí Jacobiho metody.
 *
 * @param m     Ukazatel na strukturu Tmatice, která obsahuje soustavu rovnic ve formě matice.
 *              Matice musí být čtvercová a v diagonálně dominantní formě pro zajištění konvergence.
 * @param eps   Požadovaná přesnost řešení. Iterace končí, jakmile rozdíl mezi novou a starou hodnotou
 *              všech neznámých je menší než tato hodnota.
 * @param x     Pole typu float o délce odpovídající počtu neznámých (počet řádků matice). Toto pole
 *              slouží jako úložiště pro hodnoty řešení a obsahuje počáteční odhad, který byl před voláním
 *              této funkce inicializován na nuly.
 */
void reseniJacobi(Tmatice *m, float eps, float *x) {
    int radku = m->radku;
    float xpred[radku]; // Pomocné pole pro hodnoty z předchozí iterace

        // Uložíme hodnoty z předchozí iterace..
        for (int r = 0; r < radku; r++) {
            xpred[r] = x[r];
        }

    bool jePresny = false; // Předpokládáme, že zatím nejsme dostatečně přesní

    // Iterace pokračují, dokud není dosaženo požadované přesnosti pro všechny neznámé
    while (!jePresny) {
        jePresny = true; // Předpokládáme, že všechny hodnoty jsou přesné na začátku každé iterace

        // Pro každý řádek matice
        for (int r = 0; r < radku; r++) {
            // Vypočítáme novou hodnotu pro x[r] podle Jacobiho metody
            x[r] = vypocetXr(m, xpred, r); // Používáme xpred (minulou iteraci) pro výpočet nové hodnoty..ZDE BUDOU VYSLEDKY..

            // Kontrola přesnosti pro tento řádek
            // Pokud je rozdíl mezi novou a starou hodnotou větší než epsilon, nastaví se jePresny na false
            jePresny = jePresny && (fabs(xpred[r] - x[r]) < eps); // Používáme podmínku, která se aktualizuje pro každý řádek
        }
         // Přepíšeme xpred novými hodnotami na konci každé iterace
        for (int r = 0; r < radku; r++) {
            xpred[r] = x[r];
        }

    }//konec while
}





void testujGS(void){
   char nazev[15];
   printf("Zadej nazev souboru: ");
   scanf("%14s", nazev);
   FILE* f=fopen(nazev, "r");
   if(f == NULL){
    printf("Soubor se nepodarilo otevrit pro cteni...");
    return;
   }

   Tmatice* m=maticeCtiZeSouboru(f);
   printf("-------------------------------------------\n");
   printf("Nactena matice vypada takto: \n\n");
   maticeTiskni(m);//NACTENA MATICE...
   printf("-------------------------------------------\n");

   //TEST JE DIAGONALNI MATICE...
   if(!testDDMatice(m)){
     printf("Zkus vytvorit fci ktera ji zkusi upravit...\n");//!!!!!!!!!!!!!!!!!!!!!!!
     fclose(f);
     return;
   }
   //OPTIMALIZACE MATICE PRO ZJEDNODUSENI VZORCE
   upravMatici(m);
   printf("Matice po optimalizaci vypada takto: \n\n");
   maticeTiskni(m);
   printf("-------------------------------------------\n");

   float eps;
   // Získání požadované přesnosti pro Gauss-Seidelovu metodu
   printf("Zadej pozadovanou presnost pro Gauss-Seidelovu metodu: ");
   scanf("%f", &eps);
   printf("-------------------------------------------\n");

   // Statické pole pro neznámé (velikost podle počtu řádků matice)
   float x[m->radku];  // Statické pole pro řešení soustavy

   // Inicializace pole x nulami
   for (int i = 0; i < m->radku; i++) {
       x[i] = 0.0;
   }

   // Výpočet řešení pomocí Gauss-Seidelovy metody
   reseniGS(m, eps, x);
   tiskReseniPole(x, m->radku);
   printf("-------------------------------------------\n");
   //UKLIDIT
   maticeUvolni(m);
   fclose(f);
}


void testujJacobi(void){
   char nazev[15];
   printf("Zadej nazev souboru: ");
   scanf("%14s", nazev);

   FILE* f=fopen(nazev, "r");
   if(f == NULL){
    printf("Soubor se nepodarilo otevrit pro cteni...");
    return;
   }
   Tmatice* m=maticeCtiZeSouboru(f);
   printf("-------------------------------------------\n");
   printf("Nactena matice vypada takto: \n\n");
   maticeTiskni(m);//NACTENA MATICE...
   printf("-------------------------------------------\n");
   //TEST JE DIAGONALNI MATICE...
   if(!testDDMatice(m)){
     printf("Zkus vytvorit fci ktera ji zkusi upravit...\n");//!!!!!!!!!!!!!!!!!!!!!!!
     fclose(f);
     return;
   }
   //OPTIMALIZACE MATICE PRO ZJEDNODUSENI VZORCE
   upravMatici(m);
   printf("Matice po optimalizaci vypada takto: \n\n");
   maticeTiskni(m);
   printf("-------------------------------------------\n");

   float eps;
   // Získání požadované přesnosti pro Jacobiho metodu
   printf("Zadej pozadovanou presnost pro Jacobiho metodu: ");
   scanf("%f", &eps);
   printf("-------------------------------------------\n");
   // Statické pole pro neznámé (velikost podle počtu řádků matice)
   float x[m->radku];  // Statické pole pro řešení soustavy

   // Inicializace pole x nulami
   for (int i = 0; i < m->radku; i++) {
       x[i] = 0.0;
   }

   // Výpočet řešení pomocí Gauss-Seidelovy metody
   reseniJacobi(m, eps, x);
   tiskReseniPole(x, m->radku);
   printf("-------------------------------------------\n");
   //UKLIDIT
   fclose(f);
   maticeUvolni(m);
}



/** Startovní bod programu. */
int main(void)
{
  // Co nepotřebuješ, si můžeš zakomentovat.

  srand(time(NULL));

  //testujGS();
  testujJacobi();

  return EXIT_SUCCESS;
}
