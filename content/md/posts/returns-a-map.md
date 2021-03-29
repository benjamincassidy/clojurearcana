{:title "Returns a map"
 :author "cassidy"
 :description "Maps are without a doubt the most useful and ubuiquitous data structure in Clojure. Therefore the core library has many functions that return a map. In this article, we'll explore some of these functions and  applications for them."
 :date "2021-03-28"
 :tags ["clojure.core" "map" "maps" "clojure" "clojurescript"]}
 
Without a doubt the most useful and ubiquitous data structure in Clojure is the map. There are some key core functions that both operate on maps and return maps when given other data structures. This article will focus on some of these functions and explore their usefulness.

# `frequencies`

The first function that we'll look at is the `frequencies` function. It takes a collection and returns a map of the count of each item in the sequence with the item as the key and the count as the value. It can be used to determine if a collection or string has any duplicates:

```clojure
user> (->> (frequencies coll)
           (filter #(> (second %) 1))
           (empty?)) ;; => true when collection contains duplicates
```

# `group-by`

`group-by` is similar to `frequencies` in that it consumes a collection and returns a map, but it allows you to pass a function to apply to each element in the collection and groups items in the map by the output of that function. For example, say you want to partition a collection of numbers by odd and even numbers:

```clojure
user> (group-by even? '[0 1 2 3 4 5 6 7 8 9]) 
        ;; => {true [0 2 4 6 8], false [2 3 5 7 9]}
```

But wait there's more. Let's say you're processing a list of maps of data representing a car and you want to group all cars by color.

```clojure
user> (def cars ({:make "ford" :model "flex" :color "blue"}, 
                 {:make "ford" :model "flex" :color "white"}, 
                 {:make "toyota", :model "FJ cruiser" :color "white"}))
user> (group-by :color cars)
        ;; => {"blue" [{:make "ford", :model "flex", :color "blue"}],
        ;;     "white" [{:make "ford", :model "flex", :color "white"}
        ;;              {:make "toyota", :model "FJ Cruiser", :color "white"}]}
```

Any function can be used as the second argument, so you can create custom groupings to process whatever data you need to process.

# `merge`

`merge` does what its name implies. It merges two maps into one. If a key exists in a map later in the list of arguments it clobbers the value existing in the map. There's not really that much more to say about this function so let's see it in action.

```clojure
user> (merge {:a 1 :b 2 :c 3} {:c 10 :d 8 :e 9})
        ;; => {:a 1 b: 2 :c 10 :d 8 :e 9}
```

# `merge-with`

`merge-with` does what `merge` does, but provides you as the developer a mechanism to deal with situtations where a key that exists in the former map is found in a latter map. Instead of clobbering the value as `merge` does, `merge-with` takes a function as an argument that handles these situations. When a key is found in the latter map the value is set according to the result of `(f val-in-former val-in-latter)`. Check it out.

```clojure
user> (merge-with + {:a 1 :b 2 :c 3} {:c 10 :d 8 :e 9})
        ;; => {:a 1 :b 2 :c 13 :d 8 :e 9}
```

As you can see the value of c is `13` instead of `10` as it would be with `merge`. The new value is the result of `(+ 3 10)`.

# `select-keys`

`select-keys` is a way to get a map with only the keys you want. Such situations occur many times in Clojure code. Say, for example, that you're writing a REST API and you make a database call to get some data in response to a user request. The database returns a map and you need some of those fields to do some processing, but you only want to return a subset of the fields to the user.

```clojure
user> (def user-info (get-user-info db userId))
user> user-info
{:name "Paul Atreides"
 :title "Kwisatz Haderach"
 :start-date "10191"
 :origin "Caladan"}
user> (select-keys user-info [:name :title])
{:name "Paul Atreides"
 :title "Kwisatz Haderach"}
```

# `zipmap`

The final function we'll be looking at today is `zipmap`. This function takes two collections, one of keys and one of values and creates a new map with each key mapped to the corresponding value.

```clojure
user> (zipmap [:a :b :c] [1 2 3 4])
{:a 1 :b 2 :c 3}
```

Notice how the value `4` is not present in the new map. If there are more values than there are keys, the unused values will simply be discarded.

# Conclusion

We've highlighted some useful functions in clojure.core which return maps. Most of these operate on non-map types, transforming collections into maps. Though the examples here are superficial, these functions are in fact quite powerful. These functions can be somewhat hidden in the documentation though since much of the beginner literature doesn't introduce many of the useful library functions. I hope you've found this useful.

Ciao for now!
