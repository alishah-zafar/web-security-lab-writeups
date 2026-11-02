SQL Injection: Retrieving Hidden Data

Platform: PortSwigger Web Security Academy
Category: Web Exploitation
Vulnerability: SQL Injection (SQLi)
Tool: Burp Suite (embedded browser)
Difficulty: Apprentice
Status: Successfully completed

1. Objective

The objective of this lab was to exploit a SQL injection vulnerability in a product-category filter to retrieve products that were not normally displayed by the application.

2. Initial Reconnaissance

I opened the lab in Burp Suite’s embedded browser and explored the shop page. The application displayed several product categories, including Clothing, Shoes and Accessories, Gifts, Lifestyle, and Tech gifts.

Selecting a category changed the products shown on the page. I noticed that the selected category was reflected in a URL parameter, making it an input point to investigate.

3. Testing the Input

I edited the category value directly in the browser’s URL. Adding a single quote to the value produced an error response. This suggested that the input might be interacting with a database query and was not being handled safely.

I then experimented with the SQL syntax. At first, I placed the double-hyphen comment marker incorrectly, which produced unexpected results. After correcting the payload’s syntax and placement, I used:

' OR 1=1--

4. Why the Payload Worked

The expression 1=1 is always true. The OR operator means that the overall condition can be true if either side of the expression is true.

When user-controlled input is inserted unsafely into a SQL WHERE clause, this kind of condition can change the logic of the query and cause it to return more records than the application intended to show.

The -- sequence acts as a SQL comment marker in the context of this lab, commenting out the remainder of the query. The exact effect depends on the database and the structure of the original query.

A simplified example of a query that filters products might look like this:

SELECT * FROM products
WHERE category = 'Gifts'
AND released = 1;

This is an illustrative example, not a confirmed copy of the application’s actual query.

5. Results and Evidence

Before the successful test, the Gifts category displayed a limited set of products. After the successful injection, the page displayed a much larger set of products, including products that had not previously appeared. PortSwigger then displayed its lab-completion confirmation.

Before exploitation

Before exploitation

Figure 1. Original Gifts category view.

After exploitation

After exploitation

Figure 2. Expanded product results and lab-completion confirmation.

6. Challenges and Troubleshooting

My first attempt with a trailing single quote returned an error. I also initially placed the -- comment marker incorrectly while experimenting with the payload. The results were unexpected until I corrected the syntax and its placement.

This showed me that small syntax differences can affect how an injection attempt behaves.

7. Key Learnings

• A URL parameter that controls a product filter can be an input point worth testing.
• The OR operator can change the logic of a WHERE clause when user input is incorporated into a query unsafely.
• 1=1 is an always-true condition.
• SQL comment syntax and payload placement matter.
• Burp Suite’s embedded browser can be used to interact with an application while testing its inputs.
• I moved beyond knowing what the OR operator does in theory and saw how it can affect a database query through a URL parameter.

8. Remediation

Developers should use parameterized queries (prepared statements) so that user input is treated as data rather than executable SQL. They should avoid constructing SQL statements through string concatenation, validate input where appropriate, and ensure database accounts have only the permissions required by the application.

Parameterized queries are the primary defence here: they keep SQL instructions separate from user-supplied values. Input validation and least-privilege database permissions provide additional layers of protection.

9. Conclusion

I successfully completed my first PortSwigger Web Security Academy SQL injection lab. By testing the product-category parameter and applying a Boolean condition, I demonstrated how unsafe handling of user input can expose records that are not normally displayed.

The most valuable part of this exercise was seeing how a logical operator I already knew could be applied in a real web-application testing scenario.

────────

This write-up documents a deliberately vulnerable training lab completed in PortSwigger Web Security Academy. Testing was performed within the authorised lab environment.
