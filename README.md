# Mainframe Override

You've caught wind of an EmptyStack Solutions plan to break down the city's
public transportation system! Someone on a popular online forum has bragged about
much money they're going to make by investing in EmptyStack's latest model of
self-driving taxi, claiming, "My dad works there and he's got some serious inside
intel." Hack into the mainframe again to sabotage their project before all the
city's trains and buses grind to a halt!

## Getting Started

Use `psql` to run the provided `init.sql` file, which will create a local database
named `mainframe_override` and populate it with some tables.

- `forum_posts` contains all the posts written on the online forum.
- `forum_accounts` contains information about the online forum accounts.
- `emptystack_accounts` contains credentials for EmptyStack Solutions employee accounts.

## Instructions

Just like the previous mainframe workshop, your goal is to provide the correct admin
credentials to `node mainframe -stop` in order to shut down the project.

In order to access EmptyStack's internal data, you will have to find an employee's
credentials and log in via `node mainframe`. Getting that information will require
some detective work!

Your first clue: you know that the forum post was made in April 2048, and the author
mentioned something about EmptyStack and their dad, who shares the same last name and
is also an active participant in the forum.

As you explore the `mainframe_override` database, write your SQL queries in
`submission.sql`. Use `--` to comment your thoughts and observations.

## Walkthrough

Use `psql` to explore the tables in `mainframe_override`.
That first clue is enough for you to figure out the rest!
If you would like a more guided approach, or you just want to focus on practicing SQL,
feel free to follow the steps below.

<details>
<summary>See walkthrough</summary>

1. Get the username of the person who made the post about EmptyStack in `forum_posts`.

SELECT author FROM forum_posts WHERE content ILIKE '%EmptyStack%' AND date >= '2048-04-01' AND date < '2048-05-01';
Results: smart-money-44

2. Get the last name of the person associated with that username in `forum_accounts`.

SELECT username, last_name FROM forum_accounts WHERE username IN ('steep-mechanic
-65', 'smart-money-44');
Results: Steele

3. Find all other accounts with the same last name.

SELECT last_name, first_name FROM forum_accounts WHERE last_name ILIKE 'Steele';
Results: 3

4. Find all accounts in `emptystack_accounts` with the same last name.

SELECT last_name FROM emptystack_accounts WHERE last_name ILIKE 'Steele' AND first_name 'Andrew';
Result: 1

5. There will only be one EmptyStack employee with a forum account. Use their credentials
   to access `node mainframe`, which will output a new `sql` file for you to run.

SELECT username, password FROM emptystack_accounts WHERE last_name ILIKE 'Steele' AND first_name ILIKE 'Andrew';
Results: 
    username    |  password   
----------------+-------------
 triple-cart-38 | password456

6. Find the message in `emptystack_messages` that mentions a project involving
   self-driving taxis. That message is sent from an admin account and also reveals
   the project code.

SELECT * FROM emptystack_messages WHERE subject ILIKE '%taxi%' OR body ILIKE '%taxi%';
  id   |     from     |       to       |   subject    |                            body                            
-------+--------------+----------------+--------------+------------------------------------------------------------
 LidWj | your-boss-99 | triple-cart-38 | Project TAXI | Deploy Project TAXI by end of week. We need this out ASAP.

7. Get the credentials for the admin account from `emptystack_accounts`.


mainframe_override=# SELECT username, password FROM emptystack_accounts WHERE username ILIKE 'your-boss-99';
   username   |    password    
--------------+----------------
 your-boss-99 | notagaincarter

8. Get the ID of the project from `emptystack_projects`.

SELECT id FROM emptystack_projects WHERE code ILIKE 'taxi';
    id    
----------
 DczE0v2b

9. Use that information to stop the project: `node mainframe -stop`!

(base) ashleypyka@MacBookPro block33mainframe % node mainframe -stop
WARNING: admin access required. Unauthorized access will be logged.
Username: your-boss-99
Password: notagaincarter
Welcome, your-boss-99.
Project ID: DczE0v2b
Initiating project shutdown sequence...
5...
4...
3...
2...
1...
Project shutdown complete.
(base) ashleypyka@MacBookPro block33mainframe % 

</details>

## Submission

Please submit the link to your public GitHub repository.
