# AIPractice
A workspace for practicing AI

<h2>How to execute:</h2>
streamlit run week4/libApp/app.py

<h2>High level design</h2>

| Sno | Functionality | Validations |
| --- | --- | --- |
| 1 | Add member | ISBN uniqueness — rejects duplicates |
| 2 | Add books | Email uniqueness |
| 3 | Borrow books | Book exists and has available book count > 0· Member exists · Member is active · Active borrows < 3 · Book has copies available · Member doesn't already hold this book |
| 4 | Return books | Transaction exists · Transaction is an open borrow (not already returned) |
| 5 | Fine calculation | Maximun days to return book is 15 days , if the return overdues , 5 rupees per day is charged |
| 6 | Reports : |  |
|  | a. Active Loaned Books with Overdue rows highlighted with fine |  |
|  | b. Books borrowed by given member |  |
|  | c. Genre Popularity |  |
|  | d. Top Borrowed Books |  |
|  | e. Full Transaction History |  |


<h2>Design :</h2>

•	Clean separation of UI and logic — appHelper.py could be unit-tested independently 
•	Append-only transaction log gives a complete audit trail 
•	Every mutating function returns updated state rather than relying on global mutation 
•	The member_books junction table avoids scanning all transactions for relationship queries
•	Workflows

1)	UI (tab_quick_borrow)
•	Member ID typed → find_member() → check status == "active"
•	Book title typed → filter books where available_copies > 0 and title matches
o	User clicks "Borrow" button invoke borrow_book() [appHelper.py]
o	Validations 
1. find_book()          → exists?
2. find_member()        → exists?
3. member.status        → "active"?
4. get_open_member_borrows() → count < 3?
5. book.available_copies → > 0?
6. get_active_borrow()  → not already borrowed this book?
                        All pass → create transaction record
            book["available_copies"] -= 1
            if available_copies == 0 → book["status"] = "unavailable"
            update/insert member_books link  
save_all() → writes all 4 JSON files to disk         
            
2)	UI (tab_return)
    
    User enters borrow Transaction ID → clicks "Return Book"
        return_book() [appHelper.py]
            
            1. find_transaction()   → borrow txn exists?
            2. Check status == "borrowed" AND no borrow_transaction_id field (ensures it's an original borrow, not already a return record)
            3. calculate_fine(due_date, return_date)
               → overdue_days = max(0, return_date - due_date)
               → fine = overdue_days × ₹5.00/day
            4. Create new return transaction record (references original via borrow_transaction_id)
            5. book["available_copies"] += 1
            6. Append return txn ID to member_books["transaction_ids"]            
        save_all()


<h1>Output</h1>
Add Book
Insert book with existing ISBN – should throw error
![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add1.png?raw=true)

Add Book with correct data:

![](week4/libApp/output/add2.png?raw=true)
 
<h1>Register Member:</h1>

<h3>Add  member with existing email ID : below error is thrown </h3>
A member with e-mail {email_id} is already registered.
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/output/add3.png?raw=true)
<h3>Register member with correct data</h3>
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add4.png?raw=true)
<h3>Borrow Books :</h3>
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add5.png?raw=true)
<h3>Return Book :</h3>
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add6.png?raw=true)
 
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add7.png?raw=true)
 
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add8.png?raw=true)
<h3>Returning overdue book:</h3>
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add9.png?raw=true)
 
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add10.png?raw=true)
 



<h3>Display Member details</h3>
  ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add11.png?raw=true)
<h3>Reports:</h3>
<h3>Dashboard </h3>
<h3>All Active borrowed Books</h3>
  
![image info](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add12.png?raw=true)

<h3>Genre Popularity, Top Borrowed Books</h3>
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add13.png?raw=true)
<h3>Full Transaction History</h3>
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add14.png?raw=true)
<h3>Book Inventory</h3>
 ![](https://github.com/rajeshwarisubramani/AIPractice/blob/practice1/week4/libApp/add15.png?raw=true)
  
