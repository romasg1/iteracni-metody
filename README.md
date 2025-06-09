# Iterační metody pro řešení soustav lineárních rovnic

Tento projekt implementuje dvě známé iterační metody pro řešení soustav lineárních rovnic:

- **Gauss-Seidelova metoda**
- **Jacobiho metoda**

Cílem je ověřit, zda je vstupní matice diagonálně dominantní, a následně ji použít pro výpočet řešení pomocí jedné z těchto metod.

---

## 🔧 Spuštění projektu

Pro spuštění:

1. **Otevři Code::Blocks.**
2. **Přetáhni soubor `.cbp` (Code::Blocks Project File) do okna Code::Blocks.**
3. **Stiskni F9 pro kompilaci a spuštění.**

---

## 🧪 Testovací soubory

Projekt pracuje s textovými soubory obsahujícími soustavy rovnic. Každý soubor začíná dvěma čísly:
<počet řádků> <počet sloupců>

...následují jednotlivé hodnoty matice, včetně pravých stran rovnic (rozšířená matice).

### 📁 `test1.txt`

- Matice **není diagonálně dominantní**.
- Není vhodná pro použití iteračních metod.
- Program zobrazí upozornění.

### 📁 `test2.txt`

- Matice **je diagonálně dominantní**.
- It. metody (Gauss-Seidel, Jacobi) zde fungují správně.
- Příklad přesnosti: `0.0001`

---

## ⚙️ Funkcionalita

- Detekce diagonální dominance.
- Automatická úprava matice pro výpočet.
- Výpočet řešení pomocí Gauss-Seidelovy nebo Jacobiho metody.
- Výpis výsledného vektoru neznámých `x`.

---

## 📌 Poznámky

- Program vyžaduje vstupní textový soubor – zadejte jeho název po spuštění.
- Pro správný běh doporučujeme použít připravené soubory `test1.txt` nebo `test2.txt`.

---

## ✍️ Autoři

- **Roman Grmela** – implementace iteračních metod, struktura programu  
- **David Martínek** – modul pro práci s maticemi
