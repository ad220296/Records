# Records
# 📘 2.1 Records in PL/SQL

## 🔍 Einführung

In PL/SQL sind **Records** zusammengesetzte Datentypen, mit denen du mehrere Werte (z. B. wie eine Tabellenzeile) in einer einzigen Variablen speichern kannst. Sie sind vergleichbar mit "structs" in C oder "Datensätzen" in Pascal.

Sie werden verwendet, wenn:
- mehrere Werte logisch zusammengehören (z. B. Name + Gehalt)
- man eine ganze Tabellenzeile lesen möchte (`%ROWTYPE`)
- man kompakt Daten an Funktionen oder Prozeduren übergeben will

---

## 📦 Arten von Records

### ✅ 1. `%ROWTYPE` – automatisch erzeugt
Ein Record, der **alle Spalten** einer Tabelle oder View enthält.

```sql
DECLARE
    my_emp EMP%ROWTYPE;
BEGIN
    SELECT * INTO my_emp FROM EMP FETCH FIRST ROW ONLY;
    dbms_output.put_line(my_emp.ename || ' verdient ' || my_emp.sal);
END;
```

🔸 Vorteile:
- Spart Schreibarbeit
- Immer aktuell, wenn sich die Tabelle ändert

🔸 Nachteil:
- Enthält **alle Spalten**, auch wenn man nur wenige braucht

---

### ✅ 2. Selbst definierter Record
Ein Record, den du manuell definierst – mit nur den Feldern, die du brauchst.

```sql
DECLARE
    TYPE empinfo_t IS RECORD (
        empnum NUMBER,
        empsal NUMBER
    );
    my_emp empinfo_t;
BEGIN
    my_emp.empnum := 1001;
    my_emp.empsal := 2500;
    dbms_output.put_line('Emp-Nr: ' || my_emp.empnum || ', Gehalt: ' || my_emp.empsal);
END;
```

🔸 Vorteil:
- Nur die Felder, die man braucht
- Klar und übersichtlich

🔸 Nachteil:
- Muss bei Änderungen manuell angepasst werden

---

## 🔁 Übergabe an Prozeduren / Funktionen

Du kannst selbst definierte Records auch als **Parameter** verwenden, z. B. innerhalb eines Packages:

```sql
CREATE OR REPLACE PACKAGE salman AS
    TYPE anonemp_t IS RECORD (empnum NUMBER, empsal NUMBER);
    FUNCTION CALCBONUS(anonemp anonemp_t) RETURN NUMBER;
END;

CREATE OR REPLACE PACKAGE BODY salman AS
    FUNCTION CALCBONUS(anonemp anonemp_t) RETURN NUMBER IS
    BEGIN
        IF anonemp.empnum > 5000 THEN
            RETURN anonemp.empsal + 200;
        ELSE
            RETURN anonemp.empsal + 100;
        END IF;
    END;
END;
```

**Aufruf:**
```sql
DECLARE
    myemp salman.anonemp_t;
    bonus NUMBER;
BEGIN
    myemp.empnum := 4000;
    myemp.empsal := 1800;
    bonus := salman.CALCBONUS(myemp);
    dbms_output.put_line('Bonus: ' || bonus);
END;
```

---

## 🔄 Zuweisung und Kopieren von Records

Records werden in PL/SQL **by-value** übergeben:
```sql
my_emp2 := my_emp1;
```

Wenn du ein Feld von `my_emp2` änderst, bleibt `my_emp1` unverändert. Es wird also **nicht mit Zeiger gearbeitet**, sondern kopiert:

```sql
my_emp2.empsal := my_emp2.empsal + 100;
dbms_output.put_line(my_emp1.empsal); -- bleibt gleich
```

---

## 🧾 Vergleich `%ROWTYPE` vs. `IS RECORD`

| Merkmal                | `%ROWTYPE`           | `TYPE IS RECORD`        |
|------------------------|----------------------|--------------------------|
| Felder automatisch     | ✅                   | ❌ manuell definieren    |
| Bei Tabellenerweiterung| ✅ passt sich an     | ❌ manuell anpassen      |
| Nur bestimmte Felder   | ❌                   | ✅                       |
| Ideal für              | ganze Tabellenzeile  | eigene Strukturen        |

---

## ✅ Fazit

| Situation                                   | Empfehlung               |
|--------------------------------------------|---------------------------|
| Ganze Zeile aus Tabelle einlesen           | `%ROWTYPE`               |
| Nur einzelne Felder nötig                  | `TYPE IS RECORD`         |
| Record an Funktion oder Package übergeben  | `TYPE IS RECORD` im Header |
| Kopieren ohne Referenz                     | Beide (werden by-value übergeben) |

👉 Records sind ein extrem nützliches Werkzeug, um **strukturierte Daten einfach und übersichtlich zu verarbeiten**.

---

📁 Nächster Schritt: [Zum Beispiel-Repo für Records](https://github.com/ad220296/Records)
