# Data-science

-- DATABASE DESIGN AND APPLICATIONS
-- FINAL PRACTICAL TEST
-- Replace REGNO with your registration number without punctuation.

-- SECTION A: NORMALISATION AND TABLE CREATION

-- QUESTION 1: Create and select the database

CREATE DATABASE dda_library_REGNO;
USE dda_library_REGNO;


-- QUESTION 2: Create the four normalised tables
-- QUESTION 3: Implement relationships
-- QUESTION 5: Primary keys and unique constraints
-- QUESTION 6: NOT NULL constraints
-- QUESTION 7: DueDate CHECK constraint
-- QUESTION 8: Status column, DEFAULT and CHECK constraint

CREATE TABLE Programmes (
    ProgrammeID INT AUTO_INCREMENT PRIMARY KEY,
    ProgrammeName VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE Students (
    StudentNo VARCHAR(10) PRIMARY KEY,
    StudentName VARCHAR(100) NOT NULL,
    ProgrammeID INT NOT NULL,
    FOREIGN KEY (ProgrammeID)
        REFERENCES Programmes(ProgrammeID)
);

CREATE TABLE Books (
    BookCode VARCHAR(10) PRIMARY KEY,
    BookTitle VARCHAR(150) NOT NULL,
    Category VARCHAR(50) NOT NULL
);

CREATE TABLE Borrowings (
    BorrowID VARCHAR(10) PRIMARY KEY,
    StudentNo VARCHAR(10) NOT NULL,
    BookCode VARCHAR(10) NOT NULL,
    BorrowDate DATE NOT NULL,
    DueDate DATE NOT NULL,
    Status VARCHAR(10) NOT NULL DEFAULT 'Borrowed',

    FOREIGN KEY (StudentNo)
        REFERENCES Students(StudentNo),

    FOREIGN KEY (BookCode)
        REFERENCES Books(BookCode),

    CONSTRAINT chk_due_date
        CHECK (DueDate >= BorrowDate),

    CONSTRAINT chk_status
        CHECK (Status IN ('Borrowed', 'Returned'))
);


-- QUESTION 4: Insert the supplied records
-- Insert each programme only once.

INSERT INTO Programmes (ProgrammeName)
VALUES ('BSc CS'), ('BSc IT');

INSERT INTO Students (StudentNo, StudentName, ProgrammeID)
VALUES
('S101', 'Amina Kato',
 (SELECT ProgrammeID FROM Programmes
  WHERE ProgrammeName = 'BSc CS')),
('S102', 'Brian Okello',
 (SELECT ProgrammeID FROM Programmes
  WHERE ProgrammeName = 'BSc IT')),
('S103', 'Cathy Nsubuga',
 (SELECT ProgrammeID FROM Programmes
  WHERE ProgrammeName = 'BSc CS'));

INSERT INTO Books (BookCode, BookTitle, Category)
VALUES
('B01', 'SQL Basics', 'Database'),
('B02', 'Data Modelling', 'Database'),
('B03', 'Networks Intro', 'Networking');

INSERT INTO Borrowings
    (BorrowID, StudentNo, BookCode, BorrowDate, DueDate)
VALUES
('L001', 'S101', 'B01', '2026-10-05', '2026-10-19'),
('L002', 'S101', 'B02', '2026-10-06', '2026-10-20'),
('L003', 'S102', 'B01', '2026-10-07', '2026-10-21'),
('L004', 'S103', 'B03', '2026-10-08', '2026-10-22'),
('L005', 'S102', 'B03', '2026-10-09', '2026-10-23');


-- Display the inserted records

SELECT * FROM Programmes;
SELECT * FROM Students;
SELECT * FROM Books;
SELECT * FROM Borrowings;


-- SECTION B: CONSTRAINTS AND INTEGRITY TESTING

-- QUESTION 9(a): Unknown StudentNo
-- Execute this statement separately.
-- Capture the foreign key error message.

-- INSERT INTO Borrowings
--     (BorrowID, StudentNo, BookCode, BorrowDate, DueDate)
-- VALUES
-- ('L007', 'S999', 'B01', '2026-10-10', '2026-10-24');


-- QUESTION 9(b): DueDate earlier than BorrowDate
-- Execute this statement separately.
-- Capture the CHECK constraint error message.

-- INSERT INTO Borrowings
--     (BorrowID, StudentNo, BookCode, BorrowDate, DueDate)
-- VALUES
-- ('L008', 'S101', 'B01', '2026-10-20', '2026-10-10');


-- QUESTION 9(c): Duplicate BorrowID
-- Execute this statement separately.
-- Capture the duplicate primary key error message.

-- INSERT INTO Borrowings
--     (BorrowID, StudentNo, BookCode, BorrowDate, DueDate)
-- VALUES
-- ('L001', 'S102', 'B02', '2026-10-11', '2026-10-25');


-- QUESTION 10: Insert a valid borrowing without Status
-- The default Status should be Borrowed.

INSERT INTO Borrowings
    (BorrowID, StudentNo, BookCode, BorrowDate, DueDate)
VALUES
('L006', 'S103', 'B01', '2026-10-12', '2026-10-26');

SELECT *
FROM Borrowings
WHERE BorrowID = 'L006';


-- SECTION C: DATA MANIPULATION

-- QUESTION 11: Update L002 to Returned

UPDATE Borrowings
SET Status = 'Returned'
WHERE BorrowID = 'L002';

SELECT *
FROM Borrowings
WHERE BorrowID = 'L002';


-- QUESTION 12: Insert book B04

INSERT INTO Books (BookCode, BookTitle, Category)
VALUES ('B04', 'Introduction to Python', 'Programming');

SELECT *
FROM Books
WHERE BookCode = 'B04';


-- QUESTION 13: Delete B04 without changing borrowing records

DELETE FROM Books
WHERE BookCode = 'B04';

SELECT *
FROM Books
WHERE BookCode = 'B04';


-- SECTION D: SQL RETRIEVAL AND REPORTING

-- QUESTION 14: Display borrowing details using JOINs

SELECT
    b.BorrowID,
    s.StudentName,
    bk.BookTitle,
    b.BorrowDate,
    b.Status
FROM Borrowings AS b
INNER JOIN Students AS s
    ON b.StudentNo = s.StudentNo
INNER JOIN Books AS bk
    ON b.BookCode = bk.BookCode
ORDER BY b.BorrowID;


-- QUESTION 15: Count borrowings per student
-- Include student names and order from highest to lowest.

SELECT
    s.StudentName,
    COUNT(b.BorrowID) AS NumberOfBorrowings
FROM Students AS s
LEFT JOIN Borrowings AS b
    ON s.StudentNo = b.StudentNo
GROUP BY s.StudentNo, s.StudentName
ORDER BY NumberOfBorrowings DESC;


-- QUESTION 16: Borrowings due on or before 21 October 2026

SELECT
    BorrowID,
    StudentNo,
    BookCode,
    BorrowDate,
    DueDate,
    Status
FROM Borrowings
WHERE DueDate <= '2026-10-21'
ORDER BY DueDate ASC;
