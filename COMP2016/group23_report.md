# **COMP2016 Group Project Report**

| Group Members  | ID       |
| -------------- | -------- |
| CHEN Xinyu     | 23270217 |
| HUA Chunyang   | 23259906 |
| ZHANG Xuanrui  | 23270128 |
| HONG Shangjing | 23259124 |

## **Database Design**
### **Conceptual Database Design**
#### **ER Diagram**
![[group23_ER.png]]
The ER diagram was constructed by identifying the main entities and their relationships based on the university library system's requirements. The entities are **Students** (attributes: student_no, name, gender, major) and **Books** (attributes: call_no, ISBN, title, author, amount, location). Relationships are modeled as associative entities: **Borrow** (student_no, call_no, borrow_date, due_date, renewed), **Renew** (student_no, call_no), and **Reservation** (student_no, call_no, reserve_date). These relationships are represented as diamonds in the ER diagram, connecting Students and Books in a many-to-many fashion. For instance, the Borrow relationship includes temporal attributes (borrow_date, due_date) to track borrowing periods, and Reservation includes reserve_date. Primary keys (e.g., student_no, call_no) and foreign keys (e.g., in Borrow) ensure referential integrity, while constraints like gender (M/F) and amount (>=0) are enforced.
### **Logical Database Design**
#### **Table Schema**
```sql
CREATE TABLE Students (
    student_no VARCHAR(10),
    name VARCHAR2(50) NOT NULL,
    gender CHAR(1),
    major VARCHAR2(50),
    PRIMARY KEY (student_no),
    CHECK (gender IN ('M', 'F'))
);

CREATE TABLE Books (
    call_no VARCHAR(6),
    ISBN VARCHAR(16) NOT NULL,
    title VARCHAR(5) NOT NULL,
    author VARCHAR(5) NOT NULL,
    amount INTEGER,
    location VARCHAR(100),
    PRIMARY KEY (call_no),
    UNIQUE (ISBN),
    CHECK (amount >= 0)
);

CREATE TABLE Borrow (
    student_no VARCHAR(10),
    call_no VARCHAR(20),
    borrow_date DATE NOT NULL,
    due_date DATE DEFAULT (SYSDATE + 28) NOT NULL,
    PRIMARY KEY (student_no, call_no, borrow_date),
    FOREIGN KEY (call_no) REFERENCES Books(call_no) ON DELETE CASCADE,
    FOREIGN KEY (student_no) REFERENCES Students(student_no) ON DELETE CASCADE,
    CHECK (due_date > borrow_date)
);

CREATE TABLE Reservation (
    student_no VARCHAR(10),
    call_no VARCHAR(20) NOT NULL,
    reserve_date DATE NOT NULL,
    PRIMARY KEY (student_no),
    FOREIGN KEY (student_no) REFERENCES Students(student_no) ON DELETE CASCADE,
    FOREIGN KEY (call_no) REFERENCES Books(call_no) ON DELETE CASCADE
);

CREATE TABLE Renew (
    student_no VARCHAR(10),
    call_no VARCHAR(20),
    PRIMARY KEY (student_no, call_no),
    FOREIGN KEY (student_no) REFERENCES Students(student_no) ON DELETE CASCADE,
    FOREIGN KEY (call_no) REFERENCES Books(call_no) ON DELETE CASCADE
);
```
To convert the ER diagram into relational schemes, each entity and relationship is mapped to a table. The **Students** table has student_no as the primary key, with name, gender, and major as attributes. The **Books** table uses call_no as primary key, with ISBN unique, title, author, amount, and location as attributes. The **Borrow** table, representing the borrowing relationship, has a composite primary key (student_no, call_no, borrow_date) and includes due_date and renewed, with foreign keys linking to Students and Books. The **Reservation** table has a composite primary key (student_no, call_no), with foreign keys referencing Students and Books, and includes reserve_date, while the **Renew** table has the primary key (student_no), as it origins from a many-to-one relationship set.
#### **Normal Forms Analysis**
Normalization was applied to ensure data integrity: all tables are in **1NF** (no multivalued attributes), **2NF** (no partial dependencies, as non-key attributes like due_date depend on the entire composite key), and **3NF** (no transitive dependencies, as attributes like amount in Books are directly dependent on the primary key call_no). This design eliminates redundancy and ensures consistency, aligning with the functional requirements for borrowing, renewing, and reserving books.

## **Functionalities**
### **Running Part**
```java
public String[] getInputs(String msg) {  
    System.out.print(msg);  
    String line = in.nextLine().trim();  
    if (line.equalsIgnoreCase("exit")) return null;  
    return line.split("\\s*,\\s*");  
}
```
```java
/**  
 * Show the options 
 */
public void showOptions() {  
    String[] options = {  
            "Search book by ISBN",  
            "Borrow a book",  
            "Return a book",  
            "Renew a book",  
            "Reserve a book",  
            "Exit"  
    };  
  
    System.out.println("\nChoose an option:");  
    for (int i = 0; i < options.length; i++) {  
        System.out.printf("(%d) %s\n", i + 1, options[i]);  
    }  
    System.out.print("Enter your choice: ");
}
```
```java
/**  
 * Run the management system 
 */
public void run() {  
    int choice;  
    loop:  
    while (true) {  
        showOptions();  
        String line = in.nextLine();  
        if (line.equalsIgnoreCase("exit")) break;  
  
        try {  
            choice = Integer.parseInt(line);  
        } catch (NumberFormatException e) {  
            choice = -1;  
        }  
  
        try {  
            switch (choice) {  
                case 1:  
                    searchBookByISBN();  
                    break;  
                case 2:  
                    borrowBook();  
                    break;  
                case 3:  
                    returnBook();  
                    break;  
                case 4:  
                    renewBook();  
                    break;  
                case 5:  
                    reserveBook();  
                    break;  
                case 6:  
                    break loop;  
                default:  
                    System.out.println("Invalid option.");  
            }  
        } catch (SQLException ex) {  
            System.out.println("Database error: " + ex.getMessage());  
        }  
    }  
    System.exit(0);  
}
```

### **Book Search**
```java
/**  
 * Search book details by ISBN 
 */
private void searchBookByISBN() throws SQLException {  
    String[] inputs = getInputs("Enter ISBN: ");  
    if (inputs == null || inputs.length < 1) return;  
  
    // Get the amount to check availability  
    String s = "SELECT amount FROM Books WHERE ISBN = ?";  
    PreparedStatement stm = conn.prepareStatement(s);  
    stm.setString(1, inputs[0]);  
    ResultSet r = stm.executeQuery();  
  
    if (!r.next()) {  
        System.out.println("Book not found.");  
    } else {  
        // Check the availability  
        if (r.getInt(1) < 1) {  
            System.out.println("This book is not available now");  
        } else {  
	        System.out.println("This book is available now");
            String sql = "SELECT title, author, amount, location FROM Books WHERE ISBN = ?";  
            PreparedStatement stmt = conn.prepareStatement(sql);  
            stmt.setString(1, inputs[0]);  
            ResultSet rs = stmt.executeQuery();  
  
            if (rs.next()) {  
                System.out.println("\nBook Details:");  
                System.out.printf("Title: %s\nAuthor: %s\nAvailable: %d\nLocation: %s\n",  
                        rs.getString("title"), rs.getString("author"),  
                        rs.getInt("amount"), rs.getString("location"));  
  
                checkReservationStatus(inputs[0]);  
            } else {  
                System.out.println("Book not found.");  
            }  
        }  
    }  
}
```
```java
/**  
 * Checks reservation status for a book 
 * @param isbn ISBN of the book to check  
 */
private void checkReservationStatus(String isbn) throws SQLException {  
    String sql = "SELECT COUNT(*) AS res_count FROM Reservation r " +  
            "JOIN Books b ON r.call_no = b.call_no " +  
            "WHERE b.ISBN = ?";  
    try (PreparedStatement stmt = conn.prepareStatement(sql)) {  
        stmt.setString(1, isbn);  
        ResultSet rs = stmt.executeQuery();  
        if (rs.next()) {  
            int resCount = rs.getInt("res_count");  
            if (resCount > 0) {  
                System.out.printf("This book has %d pending reservation(s).\n", resCount);  
            } else {  
                System.out.println("This book has no pending reservations.");  
            }  
        }  
    }  
}
```

### **Book Borrow**
```java
/**  
 * Handle book borrowing operation 
 * Correponding triggers: CheckBorrowRules (validation), UpdateBookAmount (stock update) 
 */
private void borrowBook() throws SQLException {  
    String[] inputs = getInputs("Enter student number and call number (comma-separated): ");  
    if (inputs == null || inputs.length < 2) return;  
  
    String checkSql = "SELECT amount FROM Books WHERE call_no = ?";  
    try (PreparedStatement checkStmt = conn.prepareStatement(checkSql)) {  
        checkStmt.setString(1, inputs[1]);  
        ResultSet rs = checkStmt.executeQuery();  
        if (rs.next() && rs.getInt("amount") > 0) {  
            checkExistingReservation(inputs[0], inputs[1]);  
        }  
    }  
  
    String sql = "INSERT INTO Borrow (student_no, call_no, borrow_date) VALUES (?, ?, SYSDATE)";  
    try (PreparedStatement stmt = conn.prepareStatement(sql)) {  
        stmt.setString(1, inputs[0]);  
        stmt.setString(2, inputs[1]);  
        stmt.executeUpdate();  
        System.out.println("The borrowing succeeded");  
    } catch (SQLException ex) {  
        handleBorrowErrors(ex);  
    }  
}
```
```java
/**  
 * Handle borrow operation error codes 
 * @param ex SQLException containing error code  
 */
private void handleBorrowErrors(SQLException ex) {  
    switch (ex.getErrorCode()) {  
        case -20001:  
            System.out.println("Error: You have already borrowed 5 books");  
            break;  
        case -20002:  
            System.out.println("Error: You have at least one overdue book, you cannot make a new borrowing");  
            break;  
        case -20003:  
            System.out.println("Error: The book is not available at present");  
            break;  
        case -20004:  
            System.out.println("Error: The book is reserved by another student");  
            break;  
        default:  
            System.out.println("Database error: " + ex.getMessage());  
    }  
}
```
```java
/**  
 * Check if current user is the first reserver 
 * @param studentNo Student number to validate  
 * @param callNo Book call number to check  
 */
private void checkExistingReservation(String studentNo, String callNo) throws SQLException {  
    String sql = "SELECT student_no FROM Reservation " +  
            "WHERE call_no = ? ORDER BY reserve_date FETCH FIRST 1 ROWS ONLY";  
    try (PreparedStatement stmt = conn.prepareStatement(sql)) {  
        stmt.setString(1, callNo);  
        ResultSet rs = stmt.executeQuery();  
        if (rs.next()) {  
            String firstReserver = rs.getString("student_no");  
            if (!firstReserver.equals(studentNo)) {  
                System.out.println("Warning: You are not the first reserver for this book.");  
            }  
        }  
    }  
}
```
#### **Corresponding Triggers for Borrowing**
1. The **DecreaseBookAmount_Borrow** trigger updates the book quantity in the Books table and manages reservations when a Borrow record is inserted or deleted. For an INSERT (borrowing), it decrements the amount field for the book by 1. It then queries the Reservation table to find the earliest reservation for the book, ordered by reserve_date, and stores the student number in first_reserver. If found, it deletes the reservation to update the queue; if no reservation exists, it catches the NO_DATA_FOUND exception and do nothing to skip this part.
```sql
CREATE OR REPLACE TRIGGER DecreaseBookAmount_Borrow  
AFTER INSERT ON Borrow  
FOR EACH ROW  
DECLARE  
    first_reserver Students.student_no%TYPE;  
BEGIN  
    -- Handle new borrow  
    -- Decrease available quantity    
    UPDATE Books SET amount = amount - 1   
	WHERE call_no = :NEW.call_no;  
      
    BEGIN  
        -- Remove earliest reservation  
        SELECT student_no INTO first_reserver  
        FROM (  
            SELECT student_no   
            FROM Reservation   
            WHERE call_no = :NEW.call_no   
            ORDER BY reserve_date  
        ) WHERE ROWNUM = 1;  
          
        DELETE FROM Reservation   
        WHERE call_no = :NEW.call_no   
        AND student_no = first_reserver;  
    EXCEPTION  
        WHEN NO_DATA_FOUND THEN  
            NULL; -- No reservations to remove  
    END;  
END;  
/
```

2. The **CheckBorrowRules** trigger enforces borrowing rules before inserting a new Borrow record. It queries the earliest reservation for the book, storing the student number in first_reserver or setting it to NULL if none exists. It counts total reservations for the book in v_reserved_by_others. The trigger enforces four rules: if v_borrowed_count is 5 or more, which means a student borrows more than 5 books, it raises error -20001; if v_overdue_exists is greater than 0, which means the student has a overdue book, it raises error -20002; if v_book_available is 0 or less, meaning there is no extra books left, it raises error -20003;  if there are reservations and the student is not the first reserver, we should only provide the book to the student who make the first reservation. It raises error -20004. This mechanism ensures borrowing adheres to limits, availability, and reservation priorities, preventing invalid operations at the database level.
```sql
CREATE OR REPLACE TRIGGER CheckBorrowRules
BEFORE INSERT ON Borrow
FOR EACH ROW
DECLARE
    v_borrowed_count NUMBER;
    v_overdue_exists NUMBER;
    v_book_available NUMBER;
    v_reserved_by_others NUMBER;
    first_reserver Students.student_no%TYPE;
BEGIN
    -- Check current borrowed books
    SELECT COUNT(*) INTO v_borrowed_count
    FROM Borrow
    WHERE student_no = :NEW.student_no;
    
    -- Check overdue books
    SELECT COUNT(*) INTO v_overdue_exists
    FROM Borrow
    WHERE student_no = :NEW.student_no
    AND due_date < SYSDATE;
  
    -- Get book availability
    SELECT amount INTO v_book_available
    FROM Books WHERE call_no = :NEW.call_no;
  
    -- Check for first reserver
    BEGIN
        SELECT student_no INTO first_reserver
        FROM (
            SELECT student_no
            FROM Reservation
            WHERE call_no = :NEW.call_no
            ORDER BY reserve_date
        ) WHERE ROWNUM = 1;
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            first_reserver := NULL; -- No reservations
    END;
  
    -- Check reservations
    SELECT COUNT(*) INTO v_reserved_by_others
    FROM Reservation
    WHERE call_no = :NEW.call_no;
  
    -- Validate rules
    IF v_borrowed_count >= 5 THEN
        RAISE_APPLICATION_ERROR(-20001, 'You have already borrowed 5 books');
    ELSIF v_overdue_exists > 0 THEN
        RAISE_APPLICATION_ERROR(-20002, 'You have at least one overdue book, you cannot make a new borrowing');
    ELSIF v_book_available <= 0 THEN
        RAISE_APPLICATION_ERROR(-20003, 'The book is not available at present');
    ELSIF v_reserved_by_others > 0 AND first_reserver IS NOT NULL AND :NEW.student_no != first_reserver THEN
        RAISE_APPLICATION_ERROR(-20004, 'The book is reserved by another student');
    END IF;
END;
/
```

## **Book Return**
```java
/**  
 * Handle book return operation 
 * Corresponding triggers: UpdateBookAmount (stock update), DeleteRenewAfterReturn (cleanup) 
 */
private void returnBook() throws SQLException {  
    String[] inputs = getInputs("Enter student number and call number (comma-separated): ");  
    if (inputs == null || inputs.length < 2) {  
        System.out.println("Invalid input.");  
        return;  
    }  
  
    try {  
        conn.setAutoCommit(false);  
  
        String deleteSql = "DELETE FROM Borrow WHERE student_no = ? AND call_no = ?";  
        try (PreparedStatement stmt = conn.prepareStatement(deleteSql)) {  
            stmt.setString(1, inputs[0]);  
            stmt.setString(2, inputs[1]);  
            int rowsAffected = stmt.executeUpdate();  
            if (rowsAffected > 0) {  
                System.out.println("Book returned successfully.");  
            } else {  
                System.out.println("No active borrow record found.");  
            }  
        }  
  
        conn.commit();  
    } catch (SQLException ex) {  
        conn.rollback();  
        throw ex;  
    } finally {  
        conn.setAutoCommit(true);  
    }  
}
```
#### **Corresponding Triggers for Return**
1. In the **IncreaseBookAmount_Return** trigger, for a DELETE (returning), it increments the amount field by 1, reflecting the book’s return. The trigger ensures real-time inventory accuracy and fairness in reservation allocation, executing automatically after each Borrow table deletion.
```sql
CREATE OR REPLACE TRIGGER IncreaseBookAmount_Return  
AFTER DELETE ON Borrow  
FOR EACH ROW  
BEGIN
    -- Handle return (delete from Borrow)  
    -- Increase available quantity    
    UPDATE Books SET amount = amount + 1  
    WHERE call_no = :OLD.call_no;  
END;  
/
```

2. The **DeleteRenewAfterReturn** tirgger deletes the reservation after the reserver successfully borrow the book
```sql
CREATE OR REPLACE TRIGGER DeleteRenewAfterReturn
AFTER DELETE ON Borrow
FOR EACH ROW
BEGIN
    DELETE FROM Renew
    WHERE student_no = :OLD.student_no
    AND call_no = :OLD.call_no;
END;
/
```

## **Book Renew**
```java
/**  
 * Handle book renewal operation
 * Corresponding trigger: CheckRenewRules (validation) 
 */
private void renewBook() throws SQLException {  
    String[] inputs = getInputs("Enter student number and call number (comma-separated): ");  
    if (inputs == null || inputs.length < 2) return;  
  
    String sql = "INSERT INTO Renew (student_no, call_no) VALUES (?, ?)";  
    try (PreparedStatement stmt = conn.prepareStatement(sql)) {  
        stmt.setString(1, inputs[0]);  
        stmt.setString(2, inputs[1]);  
        stmt.executeUpdate();  
        System.out.println("The borrowed book is renewed successfully");  
    } catch (SQLException ex) {  
        handleRenewErrors(ex);  
    }  
}
```
```java
/**  
 * Handle renewal operation error codes
 * @param ex SQLException containing error code  
 */
private void handleRenewErrors(SQLException ex) {  
    switch (ex.getErrorCode()) {  
        case -20011:  
            System.out.println("Error: You have at least one overdue book, you cannot make a renewal");  
            break;  
        case -20012:  
            System.out.println("Error: The renewal is not yet available");  
            break;  
        case -20013:  
            System.out.println("Error: The book is reserved by another student");  
            break;  
        case -20014:  
            System.out.println("Error: No active borrowing record");  
            break;  
        case -20016:  
            System.out.println("Error: This book cannot be renewed again");  
            break;  
        default:  
            System.out.println("Renewal failed: " + ex.getMessage());  
    }  
}
```
#### **Corresponding Triggers for Renewal**
1. The **CheckRenewRules** trigger enforces renewal rules before inserting a new Renew record and updates the due date in the Borrow table. If a student already have a overdue book, it shows error -20011 to stop renewing directly. It retrieves the latest borrow_date for the student and book from the Borrow table, raising error -20014 if no record exists. After searching the Due dates, if there is more than 2 weeks before deadline, meaning there is no need for renew this book, it raises error -20012. It checks for reservations, if any reservations of this book exists, it should be given to the first reserver. It will raise error -20013.If a student already renewed this book once, it shows error -20016. If all checks pass, it updates the Borrow record’s due_date by adding 14 days.
```sql
CREATE OR REPLACE TRIGGER CheckRenewRules  
BEFORE INSERT ON Renew  
FOR EACH ROW  
DECLARE  
    v_latest_borrow_date DATE;  
    v_due_date DATE;  
    v_renew_window_start DATE;  
    v_overdue_count NUMBER;  
    v_reservation_exists NUMBER;  
    v_renew_exists NUMBER;  
BEGIN  
    -- Check for overdue books  
    SELECT COUNT(*) INTO v_overdue_count  
    FROM Borrow  
    WHERE student_no = :NEW.student_no  
      AND due_date < SYSDATE;  
  
    IF v_overdue_count > 0 THEN  
        RAISE_APPLICATION_ERROR(-20011, 'You have at least one overdue book, you cannot make a renewal');  
    END IF;  
  
    -- Get latest borrow date  
    SELECT MAX(borrow_date) INTO v_latest_borrow_date  
    FROM Borrow   
    WHERE student_no = :NEW.student_no  
      AND call_no = :NEW.call_no;  
  
    IF v_latest_borrow_date IS NULL THEN  
        RAISE_APPLICATION_ERROR(-20014, 'No active borrowing record found');  
    END IF;  
  
    -- Get original due date  
    SELECT due_date INTO v_due_date  
    FROM Borrow  
    WHERE student_no = :NEW.student_no  
      AND call_no = :NEW.call_no  
      AND borrow_date = v_latest_borrow_date;  
      
    -- Check reservations  
    SELECT COUNT(*) INTO v_reservation_exists  
    FROM Reservation   
    WHERE call_no = :NEW.call_no;  
  
    IF v_reservation_exists > 0 THEN  
        RAISE_APPLICATION_ERROR(-20013, 'The book is reserved by another student');  
    END IF;  
  
    -- Check for duplicate renew  
    SELECT COUNT(*) INTO v_renew_exists   
    FROM Renew   
    WHERE student_no = :NEW.student_no   
    AND call_no = :NEW.call_no;  
      
    IF v_renew_exists > 0 THEN  
        RAISE_APPLICATION_ERROR(-20016, 'This book cannot be renewed again');  
    END IF;  
  
    -- Calculate renewal window (last 2 weeks)  
    v_renew_window_start := v_latest_borrow_date +   
        ((v_due_date - v_latest_borrow_date)/2);  
  
    IF SYSDATE < v_renew_window_start THEN  
        RAISE_APPLICATION_ERROR(-20012, 'The renewal is not yet available');  
    END IF;  
  
    -- Extend due date  
    UPDATE Borrow  
    SET due_date = v_due_date + 14  
    WHERE student_no = :NEW.student_no  
      AND call_no = :NEW.call_no  
      AND borrow_date = v_latest_borrow_date;  
END;  
/
```

## **Book Reserve**
```java
/**  
 * Handle book reservation operation 
 * Corresponding trigger: CheckReserveRules (validation) 
 */
private void reserveBook() throws SQLException {  
    String[] inputs = getInputs("Enter student number and call number (comma-separated): ");  
    if (inputs == null || inputs.length < 2) return;  
  
    String sql = "INSERT INTO Reservation (student_no, call_no, reserve_date) " +  
            "VALUES (?, ?, SYSDATE)";  
    try (PreparedStatement stmt = conn.prepareStatement(sql)) {  
        stmt.setString(1, inputs[0]);  
        stmt.setString(2, inputs[1]);  
        stmt.executeUpdate();  
        System.out.println("The reservation succeeded");  
    } catch (SQLException ex) {  
        handleReserveErrors(ex);  
    }  
}
```
```java
/**  
 * Handle reservation operation error codes 
 * @param ex SQLException containing error code  
 */
private void handleReserveErrors(SQLException ex) {  
    switch (ex.getErrorCode()) {  
        case -20021:  
            System.out.println("Error: The book is now available. No reservation is required");  
            break;  
        case -20022:  
            System.out.println("Error: Reserving a borrowed book is not allowed");  
            break;  
        case -20023:
	        System.out.println("Error: Multiple reservations are not allowed");
	        break;
        default:  
            System.out.println("Database error: " + ex.getMessage());  
    }  
}
```
#### **Corresponding Triggers for Reservation**
1. The **CheckReserveRules** trigger ensures reservations are only for unavailable books and not for books already borrowed by the student. It retrieves the book’s amount from the Books table, raising error -20021 if the book is available (amount > 0) that don't need to make reservations. If the student aready borrow this book, it raises error -20022. Also , a student can only make 1 reservation at a time, raising error -20023 othervise.
```sql
CREATE OR REPLACE TRIGGER CheckReserveRules
BEFORE INSERT ON Reservation
FOR EACH ROW
DECLARE
    v_book_available NUMBER;
    v_current_borrow NUMBER;
    v_existing_reservations NUMBER;
BEGIN
    -- Check book availability
    SELECT amount INTO v_book_available
    FROM Books WHERE call_no = :NEW.call_no;
  
    IF v_book_available > 0 THEN
        RAISE_APPLICATION_ERROR(-20021, 'The book is now available. No reservation is required');
    END IF;
  
    -- Check current borrow status
    SELECT COUNT(*) INTO v_current_borrow
    FROM Borrow
    WHERE student_no = :NEW.student_no
    AND call_no = :NEW.call_no;
  
    IF v_current_borrow > 0 THEN
        RAISE_APPLICATION_ERROR(-20022, 'Reserving a borrowed book is not allowed');
    END IF;
    
    -- Check existing reservation of this student
    SELECT COUNT(*) INTO v_existing_reservations
    FROM Reservation
    WHERE student_no = :NEW.student_no;

    IF v_existing_reservations > 0 THEN
        RAISE_APPLICATION_ERROR(-20023, 'Multiple reservations are not allowed');
    END IF;
END;
/
```


## **Java Classes**
### **Library Management System Class**
**(Rewrite on the Flight Manager Class in lab materials)**
*This is the template of the LibraryManagementSystem Class.*
*All source code of funtions are showed in Functionalities above*
```java
import java.sql.PreparedStatement;  
import java.sql.ResultSet;  
import java.sql.SQLException;  
import java.util.Scanner;

public class LibraryManagementSystem extends DBClient {  
  
    private final Scanner in = new Scanner(System.in);
    
    public String[] getInputs(String msg) {}
    public void showOptions() {}
    public void run() {}
    
    private void searchBookByISBN(){}
    private void checkReservationStatus(String isbn){}
    private void borrowBook(){}
    private void handleBorrowErrors(SQLException ex){}
    private void checkExistingReservation(String studentNo, String callNo){}
	private void returnBook(){}
	private void renewBook(){}
	private void handleRenewErrors(SQLException ex){}
	private void reserveBook()
	private void handleReserveErrors(SQLException ex){}
	
	public LibraryManagementSystem() {  
	    System.out.println("Library Management System");  
	    run();  
	}   
	public static void main(String[] args) {  
	    new LibraryManagementSystem();  
	}
}
```
### **DBClient Class**
**(Sourced from lab materials)**
*This class is totally reused from the lab contents, no change is done on this class*
```java
import com.jcraft.jsch.JSch;  
import com.jcraft.jsch.JSchException;  
import com.jcraft.jsch.Session;  
  
import javax.swing.*;  
import java.awt.*;  
import java.sql.Connection;  
import java.sql.DriverManager;  
import java.sql.SQLException;  
import java.util.Properties;  
  
/**  
 * Do not modify this class. 
 * DBClient class is used to establish a connection to the Oracle database. 
 */
public class DBClient {  
    Connection conn = null;  
    // Database Host  
    final String databaseHost = "orasrv1.comp.hkbu.edu.hk";  
    // Database Port  
    final int databasePort = 1521;  
    // Database name  
    final String database = "pdborcl.orasrv1.comp.hkbu.edu.hk";  
    final String gateway = "faith.comp.hkbu.edu.hk";  
    final int port = 22;  
    final String forwardHost = "localhost";  
    int forwardPort;  
    Session session = null;  
  
    // JDBC connecting host  
    String jdbcHost;  
    // JDBC connecting port  
    int jdbcPort;  
  
    int exitCode = 0;  
  
  
    public DBClient() {  
        /*  
        The following code segment is used to add a shutdown hook into the current runtime. 
        A shutdown hook, which is a specific code block, will be executed when the program terminates, 
        whether due to a normal shutdown procedure or an exception.
        */        
        Runtime.getRuntime().addShutdownHook(new Thread(()->{  
            close();  
        }));  
  
        if (!loginGateway()) {  
            System.out.println("Login gateway failed, please re-examine your username and password!");  
            System.exit(-1);  
        }  
        if (!loginDB()) {  
            System.out.println("Login database failed, please re-examine your username and password!");  
            System.exit(-1);  
        }  
    }  
  
    /**  
     * Get YES or NO.     
     *     
     * @return boolean     
     */
	boolean getYESorNO(String message) {  
        JPanel panel = new JPanel();  
        panel.add(new JLabel(message));  
        JOptionPane pane = new JOptionPane(panel, JOptionPane.PLAIN_MESSAGE, JOptionPane.YES_NO_OPTION);  
        JDialog dialog = pane.createDialog(null, "Question");  
        dialog.setVisible(true);  
        boolean result = JOptionPane.YES_OPTION == (int) pane.getValue();  
        dialog.dispose();  
        return result;  
    }  
  
    /**  
     * Get username & password.     
     *     
     * @return username & password     
     */    
    String[] getUsernamePassword(String title, String content) {  
        JPanel panel = new JPanel();  
  
        final JTextField usernameField = new JTextField();  
        usernameField.setPreferredSize(new Dimension(120, 30));  
  
        final JPasswordField passwordField = new JPasswordField();  
        passwordField.setPreferredSize(new Dimension(120, 30));  
  
        panel.setLayout(new FlowLayout());  
        panel.setPreferredSize(new Dimension(360, 140));  
  
        JLabel contentArea = new JLabel(content);  
        contentArea.setPreferredSize(new Dimension(360, 40));  
        panel.add(contentArea);  
  
        JPanel childPanel = new JPanel();  
        panel.add(childPanel);  
        childPanel.setLayout(new FlowLayout());  
        childPanel.add(new JLabel("Username"));  
        childPanel.add(usernameField);  
  
        childPanel = new JPanel();  
        panel.add(childPanel);  
        childPanel.setLayout(new FlowLayout());  
        childPanel.add(new JLabel("Password"));  
        childPanel.add(passwordField);  
  
  
        JOptionPane pane = new JOptionPane(panel, JOptionPane.PLAIN_MESSAGE, JOptionPane.OK_CANCEL_OPTION) {  
            private static final long serialVersionUID = 1L;  
  
            @Override  
            public void selectInitialValue() {  
                usernameField.requestFocusInWindow();  
            }  
        };  
        JDialog dialog = pane.createDialog(null, title);  
        dialog.setVisible(true);  
        dialog.dispose();  
        return new String[]{usernameField.getText(), new String(passwordField.getPassword())};  
    }  
  
    /**  
     * Logs in to the gateway server.     
     *     
     * @return true if the login to the gateway server is successful, otherwise false.
     */    
    public boolean loginGateway() {  
        if (getYESorNO("Use SSH tunnel or not?")) { // if using ssh tunnel  
            String[] namePwd = getUsernamePassword("Gateway Login", "<html>Please input your <font color='red'><b>COMP</b></font> username and password</html>");  
            String sshUser = namePwd[0];  
            String sshPwd = namePwd[1];  
            try {  
                if (sshUser.trim().length() == 0 || sshPwd.trim().length() == 0)  
                    return false;  
  
                session = new JSch().getSession(sshUser, gateway, port);  
                session.setPassword(sshPwd);  
                Properties config = new Properties();  
                config.put("StrictHostKeyChecking", "no");  
                session.setConfig(config);  
                session.connect();  
                session.setPortForwardingL(forwardHost, 0, databaseHost, databasePort);  
                forwardPort = Integer.parseInt(session.getPortForwardingL()[0].split(":")[0]);  
            } catch (JSchException e) {  
                e.printStackTrace();  
                return false;  
            }  
            jdbcHost = forwardHost;  
            jdbcPort = forwardPort;  
        } else {  
            jdbcHost = databaseHost;  
            jdbcPort = databasePort;  
        }  
        return true;  
    }  
  
    /**  
     * Logs in to the Oracle database.     
     *     
     * @return true if the login to the database is successful, otherwise false.     
     */    
    public boolean loginDB() {  
        String[] namePwd = getUsernamePassword("Database Login", "<html>Please input your <font color='red'><b>Database</b></font> username and password</html>");  
        String username = namePwd[0];  
        String password = namePwd[1];  
        if (username.trim().length() == 0 || password.trim().length() == 0)  
            return false;  
  
        String URL = "jdbc:oracle:thin:@" + jdbcHost + ":" + jdbcPort + "/" + database;  
  
        try {  
            System.out.println("Logging " + URL + " ...");  
            conn = DriverManager.getConnection(URL, username, password);  
            return true;  
        } catch (SQLException e) {  
            e.printStackTrace();  
            return false;  
        }  
    }  
  
    /**  
     * Release all resources and terminate this program.    
	 */    
    public void close() {  
        System.out.println("\n\n");  
        try {  
            if (conn != null) {  
                conn.close();  
                System.out.println("Disconnected from the database.");  
            }  
            if (session != null) {  
                session.disconnect();  
                System.out.println("Disconnected from the gateway server.");  
            }  
        } catch (SQLException e) {}  
        finally {  
            if (exitCode >= 0)  
                System.out.println("Thanks for using! Bye...");  
            else {  
                System.out.println("This program terminated unexpectedly due to a problem.");  
            }  
        }  
    }  
  
}
```

## **Test Data**
```sql
PROMPT INSERT STUDENTS TABLE;  
  
INSERT INTO Students (student_no, name, gender, major) VALUES ('12345678', 'A', 'M', 'Comp');  
INSERT INTO Students (student_no, name, gender, major) VALUES ('11111111', 'B', 'M', 'Math');  
INSERT INTO Students (student_no, name, gender, major) VALUES ('22222222', 'C', 'F', 'COMM');  
INSERT INTO Students (student_no, name, gender, major) VALUES ('33333333', 'D', 'F', 'COMM');  
INSERT INTO Students (student_no, name, gender, major) VALUES ('44444444', 'E', 'M', 'Comp');  
INSERT INTO Students (student_no, name, gender, major) VALUES ('55555555', 'F', 'M', 'COMM');  
INSERT INTO Students (student_no, name, gender, major) VALUES ('66666666', 'G', 'F', 'Math');  
INSERT INTO Students (student_no, name, gender, major) VALUES ('77777777', 'H', 'M', 'Comp');  
  
PROMPT INSERT BOOKS TABLE;  
  
INSERT INTO Books (call_no, ISBN, title, author, amount, location) VALUES ('A0000', '0-306-40615-1', 'AA', 'XX', 0, 'S1E01');  
INSERT INTO Books (call_no, ISBN, title, author, amount, location) VALUES ('B0000', '0-306-40615-2', 'BB', 'YY', 0, 'S2E02');  
INSERT INTO Books (call_no, ISBN, title, author, amount, location) VALUES ('C1111', '0-306-40615-3', 'CC', 'ZZ', 2, 'D1E11');  
INSERT INTO Books (call_no, ISBN, title, author, amount, location) VALUES ('B0001', '0-306-40615-4', 'DD', 'UU', 2, 'G1E00');  
INSERT INTO Books (call_no, ISBN, title, author, amount, location) VALUES ('A1111', '0-306-40615-5', 'EE', 'VV', 2, 'B1E00');  
INSERT INTO Books (call_no, ISBN, title, author, amount, location) VALUES ('D0101', '0-306-40615-6', 'FF', 'WW', 1, 'B2E11');  
INSERT INTO Books (call_no, ISBN, title, author, amount, location) VALUES ('E0000', '0-306-40615-7', 'GG', 'PP', 0, 'X0E22');  
INSERT INTO Books (call_no, ISBN, title, author, amount, location) VALUES ('E0100', '0-306-40615-8', 'HH', 'QQ', 2, 'X0E21');  
INSERT INTO Books (call_no, ISBN, title, author, amount, location) VALUES ('E0111', '0-306-40615-9', 'II', 'RR', 0, 'X0E44');  
  
PROMPT INSERT BORROW TABLE;  
  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('11111111', 'D0101', TO_DATE('24/Mar/2025', 'DD/MON/YYYY'), TO_DATE('21/Apr/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('55555555', 'A1111', TO_DATE('23/Mar/2025', 'DD/MON/YYYY'), TO_DATE('20/Apr/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('22222222', 'B0000', TO_DATE('31/Mar/2025', 'DD/MON/YYYY'), TO_DATE('12/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('11111111', 'A0000', TO_DATE('01/Apr/2025', 'DD/MON/YYYY'), TO_DATE('29/Apr/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('33333333', 'A0000', TO_DATE('03/Apr/2025', 'DD/MON/YYYY'), TO_DATE('01/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('11111111', 'B0000', TO_DATE('03/Apr/2025', 'DD/MON/YYYY'), TO_DATE('15/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('44444444', 'C1111', TO_DATE('04/Apr/2025', 'DD/MON/YYYY'), TO_DATE('16/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('44444444', 'A0000', TO_DATE('06/Apr/2025', 'DD/MON/YYYY'), TO_DATE('04/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('33333333', 'C1111', TO_DATE('06/Apr/2025', 'DD/MON/YYYY'), TO_DATE('04/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('33333333', 'A1111', TO_DATE('06/Apr/2025', 'DD/MON/YYYY'), TO_DATE('04/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('33333333', 'B0001', TO_DATE('06/Apr/2025', 'DD/MON/YYYY'), TO_DATE('04/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('44444444', 'D0101', TO_DATE('10/Apr/2025', 'DD/MON/YYYY'), TO_DATE('08/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('33333333', 'D0101', TO_DATE('10/Apr/2025', 'DD/MON/YYYY'), TO_DATE('08/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('44444444', 'A1111', TO_DATE('14/Apr/2025', 'DD/MON/YYYY'), TO_DATE('12/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('55555555', 'C1111', TO_DATE('18/Apr/2025', 'DD/MON/YYYY'), TO_DATE('16/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('22222222', 'E0111', TO_DATE('19/Apr/2025', 'DD/MON/YYYY'), TO_DATE('17/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('11111111', 'E0000', TO_DATE('20/Apr/2025', 'DD/MON/YYYY'), TO_DATE('18/May/2025', 'DD/MON/YYYY'));  
INSERT INTO Borrow (student_no, call_no, borrow_date, due_date) VALUES ('44444444', 'B0001', TO_DATE('21/Apr/2025', 'DD/MON/YYYY'), TO_DATE('19/May/2025', 'DD/MON/YYYY'));

PROMPT INSERT RESERVATION TABLE;  
  
INSERT INTO Reservation (student_no, call_no, reserve_date) VALUES ('12345678', 'A0000', TO_DATE('20/Apr/2025', 'DD/MON/YYYY'));  
INSERT INTO Reservation (student_no, call_no, reserve_date) VALUES ('66666666', 'E0000', TO_DATE('22/Apr/2025', 'DD/MON/YYYY'));  
  
PROMPT INSERT RENEW TABLE;  
  
INSERT INTO Renew (student_no, call_no) VALUES ('22222222', 'B0000');  
INSERT INTO Renew (student_no, call_no) VALUES ('11111111', 'B0000');  
INSERT INTO Renew (student_no, call_no) VALUES ('44444444', 'C1111');  
  
COMMIT;
```

## **Dropping Tables (Constraints) and Triggers**
```sql
PROMPT DROP TRIGGERS;  
  
DROP TRIGGER DecreaseBookAmount_Borrow;  
DROP TRIGGER IncreaseBookAmount_Return;  
DROP TRIGGER CheckBorrowRules;  
DROP TRIGGER CheckRenewRules;  
DROP TRIGGER CheckReserveRules;  
DROP TRIGGER DeleteRenewAfterReturn;  

COMMIT;

PROMPT DROP TABLES;  
  
DROP TABLE Students CASCADE CONSTRAINTS;  
DROP TABLE Books CASCADE CONSTRAINTS;  
DROP TABLE Borrow CASCADE CONSTRAINTS;  
DROP TABLE Reservation CASCADE CONSTRAINTS;  
DROP TABLE Renew CASCADE CONSTRAINTS;  
  
COMMIT;  
```