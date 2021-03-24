{:title "Fun with strings"
 :author "cassidy"
 :description "Clojure provides excellent utilities for dealing with strings. Strings are also sequences and can be treated and processed as such. In this article we'll explore a fun way to solve a common problem using sequence functions on strings."
 :date "2021-03-21"}
 
Any program of significance, especially those that deal with outside input, has to deal with strings. Java provides an ample set of utilities and methods for handling strings, but the clojure.string namespace provides utilities for string manipulation in a more idiomatic way. Here we'll explore some of these core functions and how to use them and then we'll look at advanced string handling and some interesting problems with strings.

# Core functions

The core string library has all the functionality you've come to expect from string handling features in core libraries in other languages. You can, of course, trim whitespace `(trim s)`, `(triml s)`, and `(trimr s)`. You can determine if a substring exists `(includes? s sub)`, `(starts-with? s sub)`, and `(ends-with? s sub)`. Capitalize `(capitalize s)` and lowercase `(lower-case s)` a string. And replace `(replace s match replacement)`, `(replace-first s match replacement)`. Nothing here is new, nor specific to Clojure. But the way that Clojure treats strings can lead to some interesting solutions to common problems.

# Counting strings with duplicate characters

Let us say that we have a list of names and we want to determine if any names have duplicate characters. Well Clojure strings are in fact sequences and any function that works on a sequence works on a string.

```clojure-repl
user> (first "string")
\s
user> (last "string")
\g
user> (rest "string")
(\t \r \i \n \g)
```

So now we have a sequence of characters. How would we go about determining if there were any duplicates in this string?

```clojure
(->> "some string"
     (frequencies)
     (filter #(> (second %) 1))
     (empty?)
     (not)) => true
```

Cool. This code works to determine if there are duplicate letters in a given string. But our original problem asked us to determine which strings in a list of strings have duplicate characters. So lets take this list:

```clojure
("apple" "pear" "orange" "strawberry" "tomato")
```

What we want back is:

```clojure
("apple" "strawberry" "tomato")
```

Which is as simple as using our code above as a filter:

```clojure
(filter (fn [x] (->> x
                     (frequencies)
                     (filter #(> (second %) 1))
                     (empty?)
                     (not)))
        '("apple" "pear" "orange" "strawberry" "tomato"))
```

# Conclusion

What we've seen here is just one simple consequence of being able to treat strings as sequences. There are further implications which we will explore in future articles.

Ciao for now!
