# SQLQuery
* 1a. Display the first and last names of all actors from the table `actor`.

select first_name,last_name from sakila.actor

* 1b. Display the first and last name of each actor in a single column in upper case letters. Name the column `Actor Name`.

select upper(concat(first_name," ",last_name)) as actor_name from actor

* 2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information?

select actor_id,first_name,last_name from sakila.actor where first_name like 'JOE'


* 2b. Find all actors whose last name contain the letters `GEN`:

select actor_id,first_name,last_name from sakila.actor where last_name like '%GEN%'

* 2c. Find all actors whose last names contain the letters `LI`. This time, order the rows by last name and first name, in that order:

select last_name,first_name from sakila.actor where last_name like '%LI%' order by last_name

* 2d. Using `IN`, display the `country_id` and `country` columns of the following countries: Afghanistan, Bangladesh, and China:

select country_id,country from country where country in ('Afghanistan','Bangladesh','China')

* 3a. You want to keep a description of each actor. You don't think you will be performing queries on a description, so create a column in the table `actor` named `description` and use the data type `BLOB` (Make sure to research the type `BLOB`, as the difference between it and `VARCHAR` are significant).

alter table actor add description blob

* 3b. Very quickly you realize that entering descriptions for each actor is too much effort. Delete the `description` column.

alter table actor drop column description

* 4a. List the last names of actors, as well as how many actors have that last name.

select last_name,count(last_name) from actor group by last_name order by last_name   

* 4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors

select last_name,count(last_name) as count from actor group by last_name having count(last_name) > 1;

* 4c. The actor `HARPO WILLIAMS` was accidentally entered in the `actor` table as `GROUCHO WILLIAMS`. Write a query to fix the record.

update actor set first_name='HARPO' where first_name like 'GROUCHO' and last_name like 'WILLIAMS'


* 4d. Perhaps we were too hasty in changing `GROUCHO` to `HARPO`. It turns out that `GROUCHO` was the correct name after all! In a single query, if the first name of the actor is currently `HARPO`, change it to `GROUCHO`.

update actor set first_name='GROUCHO' where first_name like 'HARPO'

* 5a. You cannot locate the schema of the `address` table. Which query would you use to re-create it?

  * Hint: <https://dev.mysql.com/doc/refman/5.7/en/show-create-table.html>
  
show create table address

* 6a. Use `JOIN` to display the first and last names, as well as the address, of each staff member. Use the tables `staff` and `address`:

select staff.first_name,staff.last_name,address.address from staff inner join address on staff.address_id=address.address_id;

* 6b. Use `JOIN` to display the total amount rung up by each staff member in August of 2005. Use tables `staff` and `payment`.

select staff.first_name,staff.last_name,sum(payment.amount) from staff inner join payment on payment.staff_id=staff.staff_id and payment_date like '2005-08%' group by staff.staff_id

* 6c. List each film and the number of actors who are listed for that film. Use tables `film_actor` and `film`. Use inner join.

select film.title, count(film_actor.actor_id) from film inner join film_actor on film.film_id=film_actor.film_id group by film_actor.film_id

* 6d. How many copies of the film `Hunchback Impossible` exist in the inventory system?

select count(inventory.film_id) from inventory where inventory.film_id=(select film.film_id from film where film.title='Hunchback Impossible')

* 6e. Using the tables `payment` and `customer` and the `JOIN` command, list the total paid by each customer. List the customers alphabetically by last name:

  select customer.first_name,customer.last_name,count(payment.amount) from customer inner join payment on customer.customer_id=payment.customer_id group by customer.customer_id order by customer.last_name

* 7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters `K` and `Q` have also soared in popularity. Use subqueries to display the titles of movies starting with the letters `K` and `Q` whose language is English.

select sub.title from (select film.title from film where film.language_id=1) sub where sub.title like "K%" or sub.title like "Q%" 

* 7b. Use subqueries to display all actors who appear in the film `Alone Trip`.

select actor.first_name,actor.last_name from actor where actor_id in 
(select film_actor.actor_id from film_actor where film_actor.film_id=
(select film.film_id from film where film.title="Alone Trip"))

* 7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.

* 7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as family films.

select film.title from film where film.film_id in 
(select film_category.film_id from film_category where film_category.category_id = 
(select category.category_id from category where category.name="Family"))

* 7e. Display the most frequently rented movies in descending order.

select inventory.film_id,film.title,count(inventory.film_id) as rental_count from film, inventory inner join rental on inventory.inventory_id=rental.inventory_id where film.film_id=inventory.film_id group by inventory.film_id order by rental_count desc

* 7f. Write a query to display how much business, in dollars, each store brought in.

* 7g. Write a query to display for each store its store ID, city, and country.

* 7h. List the top five genres in gross revenue in descending order. (**Hint**: you may need to use the following tables: category, film_category, inventory, payment, and rental.)

* 8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.

* 8b. How would you display the view that you created in 8a?

* 8c. You find that you no longer need the view `top_five_genres`. Write a query to delete it.

