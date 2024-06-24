
# VB.NET Console Application with SQLite

This repository contains a VB.NET console application that connects to a SQLite database and displays the table structure in a formatted manner.

## Prerequisites

Before you begin, ensure you have met the following requirements:
- You have installed [Visual Studio Community Edition](https://visualstudio.microsoft.com/).
- You have downloaded the [SQLite Tools](https://www.sqlite.org/download.html).

## Installation

### Step 1: Install Visual Studio

1. **Download and install Visual Studio:**
   - Go to the [Visual Studio download page](https://visualstudio.microsoft.com/).
   - Download and install the Community Edition.
   - During installation, select the **.NET Desktop Development** workload.

### Step 2: Download SQLite Tools

1. **Download the SQLite Command Line Tools:**
   - Go to the [SQLite download page](https://www.sqlite.org/download.html).
   - Download the file `sqlite-tools-win32-x86-3460000.zip`.
   - Extract the contents to a directory of your choice.

### Step 3: Create a SQLite Database

1. **Open Command Prompt (CMD):**
   - Press `Windows + R`, type `cmd`, and press `Enter`.

2. **Navigate to the directory where `sqlite3.exe` is located:**
   ```sh
   cd path\to\sqlite-tools-win32-x86-3460000
   ```

3. **Create a new SQLite database and a table:**
   ```sh
   sqlite3 your_database_file.db
   ```

   Inside the SQLite prompt, enter the following SQL commands to create a table:
   ```sql
   CREATE TABLE users (
       id INTEGER PRIMARY KEY AUTOINCREMENT,
       username VARCHAR(25) NOT NULL,
       passwd VARCHAR(25) NOT NULL,
       email VARCHAR(40) NOT NULL
   );
   .exit
   ```

### Step 4: Create and Run the VB.NET Console Application

1. **Open Visual Studio and create a new project:**
   - Click on `Create a new project`.
   - Select `Console App (.NET Core)` as the project type and `Visual Basic` as the language.
   - Name your project and click `Create`.

2. **Install the `System.Data.SQLite` NuGet package:**
   - Go to `Project > Manage NuGet Packages`.
   - Search for `System.Data.SQLite` and install it.

3. **Add the following code to `Module1.vb`:**
   ```vb
   Imports System.Data.SQLite

   Module Module1

       Sub Main()
           Dim database As String = "path\to\your_database_file.db" ' Path to SQLite database
           Dim tableName As String = "users" ' Table name

           Dim connString As String = $"Data Source={database};Version=3;"
           Using conn As New SQLiteConnection(connString)
               conn.Open()

               Dim query As String = $"PRAGMA table_info({tableName})"
               Dim cmd As New SQLiteCommand(query, conn)
               Dim reader As SQLiteDataReader = cmd.ExecuteReader()

               Dim headers() As String = {"Field", "Type", "Null", "Key", "Default", "Extra"}
               Dim table As New List(Of String())

               While reader.Read()
                   Dim field As String = reader("name").ToString()
                   Dim fieldType As String = reader("type").ToString()
                   Dim notNull As String = If(reader("notnull").ToString() = "1", "NO", "YES")
                   Dim key As String = If(reader("pk").ToString() = "1", "PRI", "")
                   Dim defaultValue As String = If(reader("dflt_value") IsNot DBNull.Value, reader("dflt_value").ToString(), "NULL")
                   Dim extra As String = If(fieldType.ToUpper().Contains("AUTOINCREMENT"), "auto_increment", "")

                   table.Add(New String() {field, fieldType, notNull, key, defaultValue, extra})
               End While

               PrintTable(headers, table)
               conn.Close()
           End Using

           Console.ReadLine()
       End Sub

       Sub PrintTable(headers As String(), table As List(Of String()))
           Dim columnWidths(headers.Length - 1) As Integer

           ' Calculate column widths
           For i As Integer = 0 To headers.Length - 1
               columnWidths(i) = headers(i).Length
           Next

           For Each row As String() In table
               For i As Integer = 0 To row.Length - 1
                   If row(i).Length > columnWidths(i) Then
                       columnWidths(i) = row(i).Length
                   End If
               Next
           Next

           ' Print table header
           PrintLine(headers, columnWidths)
           PrintSeparator(columnWidths)

           ' Print table rows
           For Each row As String() In table
               PrintLine(row, columnWidths)
           Next
       End Sub

       Sub PrintLine(row As String(), columnWidths As Integer())
           For i As Integer = 0 To row.Length - 1
               Console.Write("| " & row(i).PadRight(columnWidths(i)) & " ")
           Next
           Console.WriteLine("|")
       End Sub

       Sub PrintSeparator(columnWidths As Integer())
           For i As Integer = 0 To columnWidths.Length - 1
               Console.Write("+-" & New String("-"c, columnWidths(i)) & "-")
           Next
           Console.WriteLine("+")
       End Sub

   End Module
   ```

4. **Run the project:**
   - Click on `Start` (or press `F5`) to run the project.
   - The console application will display the table structure of the SQLite database in a formatted manner.

## Usage

After setting up the project, you can modify the `Main` method to query different tables or databases by changing the `database` and `tableName` variables accordingly.

## Contributing

If you have suggestions for improving this project, please open an issue or submit a pull request. All contributions are welcome!

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgements

- [SQLite](https://www.sqlite.org/)
- [Microsoft Visual Studio](https://visualstudio.microsoft.com/)
```

Sie können diesen Text kopieren und in Ihre `README.md`-Datei in Ihrem GitHub-Repository einfügen.