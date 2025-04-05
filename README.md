# 📘 2.1 Records in PL/SQL

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
    TYPE emp_short_t IS RECORD (
        empno NUMBER,
        sal   NUMBER
    );
    e1 emp_short_t;
    e2 emp_short_t;
BEGIN
    e1.empno := 1001;
    e1.sal := 2000;

    e2 := e1;
    e2.sal := e2.sal + 100;

    dbms_output.put_line('e1.sal = ' || e1.sal); -- 2000
    dbms_output.put_line('e2.sal = ' || e2.sal); -- 2100
END;
/  
```
📌 Du siehst: **Records werden kopiert**, nicht referenziert. Änderungen wirken sich **nicht** auf das Original aus.

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
| Record an Funktion oder Package übergeben  | `TYPE IS RECORD` im Header (kommt später) |
| Kopieren ohne Referenz                     | Beide (werden by-value übergeben) |

👉 Records sind ein extrem nützliches Werkzeug, um **strukturierte Daten einfach und übersichtlich zu verarbeiten**.

---

📁 Nächster Schritt: [Zum Beispiel-Repo für Records](https://github.com/ad220296/Records)
