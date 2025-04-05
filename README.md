# 📘 2.1 Records in PL/SQL

Zu:

- 🧩 [Nested Tables (Array & Hashed)](https://github.com/ad220296/Nested_Tables)
- 📦 [Packages & Sichtbarkeit](https://github.com/ad220296/Packages)

## 🔍 Einführung

In PL/SQL sind **Records** zusammengesetzte Datentypen, mit denen du mehrere Werte (z. B. wie eine Tabellenzeile) in einer einzigen Variablen speichern kannst. Sie sind vergleichbar mit "Datensätzen" in Pascal oder **einfachen Klassen (`class`) in C#**.

> 📌 In C# ist eine `class` mit mehreren Eigenschaften vergleichbar:
> - PL/SQL `RECORD` ≈ C# `class` mit einzelnen Feldern (z. B. `EmpNo`, `Salary`)
> - `%ROWTYPE` ≈ C# `class` mit allen Spalten einer Tabelle als Properties

---

## 🧪 Übungsschritte – Records anwenden & verstehen

### 🔹 1. `%ROWTYPE` verwenden
```sql
SET SERVEROUTPUT ON;
DECLARE
    my_emp EMP%ROWTYPE;
BEGIN
    SELECT * INTO my_emp FROM EMP WHERE ename = 'KING';
    dbms_output.put_line('Nummer: ' || my_emp.empno || ', Gehalt: ' || my_emp.sal);
END;
/
```
📌 Du bekommst **alle Spalten** aus der EMP-Tabelle. Ideal für komplette Zeilen.

---

### 🔸 2. Eigenes Record erstellen (lokal)
```sql
DECLARE
    TYPE emp_basic_t IS RECORD (
        empno NUMBER,
        ename VARCHAR2(50)
    );
    empdata emp_basic_t;
BEGIN
    SELECT empno, ename INTO empdata FROM EMP WHERE empno = 7839;
    dbms_output.put_line('Nummer: ' || empdata.empno || ', Name: ' || empdata.ename);
END;
/
```
📌 Du speicherst nur **ausgewählte Spalten**. Gut für gezielte Datenverarbeitung.

---

### 🔁 3. Verhalten bei Zuweisung – by-value
```sql
DECLARE
    TYPE emp_t IS RECORD (
        empno emp.empno%TYPE,
        sal emp.sal%TYPE
    );
    r1 emp_t;
    r2 emp_t;
BEGIN
    SELECT empno, sal INTO r1 FROM emp WHERE empno = 7839;

    r2 := r1;
    r2.sal := r2.sal + 500;

    dbms_output.put_line('Original-Gehalt (r1): ' || r1.sal);
    dbms_output.put_line('Erhöhtes Gehalt (r2): ' || r2.sal);
END;
/
```
📌 Du siehst: **Records werden kopiert**, nicht referenziert. Änderungen wirken sich **nicht** auf das Original aus.

---

### 🧭 4. Record an Prozedur übergeben
```sql
DECLARE 
    TYPE empdata_t IS RECORD (
        empno emp.empno%TYPE,
        sal emp.sal%TYPE
    );
    empinfo empdata_t;

    PROCEDURE show_bonus(e IN empdata_t) IS
        bonus NUMBER;
    BEGIN
        bonus := e.sal * 0.1;
        dbms_output.put_line('Mitarbeiter: ' || e.empno);
        dbms_output.put_line('Gehalt: ' || e.sal);
        dbms_output.put_line('Bonus: ' || bonus);
    END;
BEGIN
    SELECT empno, sal INTO empinfo FROM emp WHERE empno = 7839;
    show_bonus(empinfo);
END;
/
```
📌 Du kannst ganze Records an Prozeduren übergeben – übersichtlich und flexibel!

---

### 🔄 5. Cursor + Record in Schleife
```sql
DECLARE
    CURSOR emp_cur IS
        SELECT empno, ename, sal FROM emp WHERE sal > 2000;

    emp_rec emp_cur%ROWTYPE;
BEGIN
    OPEN emp_cur;
    LOOP
        FETCH emp_cur INTO emp_rec;
        EXIT WHEN emp_cur%NOTFOUND;

        dbms_output.put_line('Empno: ' || emp_rec.empno ||
                             ', Name: ' || emp_rec.ename ||
                             ', Gehalt: ' || emp_rec.sal);
    END LOOP;
    CLOSE emp_cur;
END;
/
```
📌 Du verarbeitest Zeile für Zeile mit einem Record, der direkt aus dem Cursor stammt.

---

### 🚀 6. BULK COLLECT mit selbst definiertem Record-Typ
```sql
DECLARE
    TYPE empdata_t IS RECORD (
        empno emp.empno%TYPE,
        sal emp.sal%TYPE
    );
    TYPE emp_tab_t IS TABLE OF empdata_t;
    emp_list emp_tab_t;
BEGIN
    SELECT empno, sal BULK COLLECT INTO emp_list
    FROM emp
    WHERE sal > 2000;

    FOR i IN 1 .. emp_list.COUNT LOOP
        dbms_output.put_line('Empno: ' || emp_list(i).empno ||
                             ', Gehalt: ' || emp_list(i).sal);
    END LOOP;
END;
/
```
📌 Du speicherst mehrere Zeilen in einer Liste von Records – extrem praktisch und schnell.

---

### 🧠 7. BULK COLLECT mit `%ROWTYPE`
```sql
DECLARE
    TYPE emp_tab_t IS TABLE OF emp%ROWTYPE;
    emp_list emp_tab_t;
BEGIN
    SELECT * BULK COLLECT INTO emp_list
    FROM emp
    WHERE sal > 2000;

    FOR i IN 1 .. emp_list.COUNT LOOP
        dbms_output.put_line('Empno: ' || emp_list(i).empno ||
                             ', Name: ' || emp_list(i).ename ||
                             ', Gehalt: ' || emp_list(i).sal);
    END LOOP;
END;
/
```
📌 Du bekommst **alle Spalten** jeder Zeile – automatisch – als Record in einer Tabelle.

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

| Situation                                   | Empfehlung                         |
|--------------------------------------------|-------------------------------------|
| Ganze Zeile aus Tabelle einlesen           | `%ROWTYPE`                         |
| Nur einzelne Felder nötig                  | `TYPE IS RECORD`                   |
| Record an Funktion oder Package übergeben  | `TYPE IS RECORD` im Header         |
| Kopieren ohne Referenz                     | Beide (werden by-value übergeben)  |

👉 Records sind ein extrem nützliches Werkzeug, um **strukturierte Daten einfach und übersichtlich zu verarbeiten**.

---
