---
layout: post
title:  "datomic, functions and expectations"
date:   2014-05-08 23:00:00
categories: clojure datomic
---

Bare in mind the usual disclaimer that none of this is revelatory. Just a dump of what I've done in the hope that it helps me make sense of it.

Anyway... Clojure, Datomic and Testing. It's always (for me) been hard to test SQL database schemas and functions. One of the features about Datomic is that it separates (decomplects?) the query handling and the transaction handling from each other and the underlying datastore. This means you can use a variety of types datastores and change as needs arises. It also allows for an in memory database which is ideal for testing.

We generally use Expectations for our test framework. It's small and simple. Anyway to test the schema and any database functions you can do this.

{% highlight clojure %}
(def db-uri "datomic:mem://db")

(defn setup
  {:expectations-options :before-run}
  []
  (drop-database! db-uri)
  (db-init db-uri)
  (let [db (d/db (d/connect db-uri))]
    (def test-db  db)
    (def func1 (-> (d/entity db :func1) :db/fn))
    (def func2 (-> (d/entity db :func2) :db/fn))))
{% endhighlight %}

(setup) with the :expectations-options metadata is a function that is run every time expectations loads the namespace.

(db-init ...) simply does a create-database, transacts the schema, transacts the database functions. All this done in the mearest of moments in memory. 

The test-db var gives you access to an initialised database that combined with the datomic (with) function and the :db-after returned value allows you test whatever transactions and database functions without having to pull down and recreate a database.

The func1, func2 vars enable you to directly call your stored datomic database functions in your code. They might be simple validation functions that check the input and return it or an Exception if invalid. They might create transaction data for consistent entities. You can test the transaction data without transacting it. It's just data. Or you can test the result of the transaction using the returned :tempids to query the entity that should have been created.

{% highlight clojure %}

(expect RuntimeException (func1 {}))
(expect {:book/title "Clojure rocks"} (func1 {:book/title "Clojure rocks"}))
{% endhighlight %}

So combine this with lein autoexpect you are all set to TDD (if that's your bag) your schema and database functions to your hearts content.