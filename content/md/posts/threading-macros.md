{:title "Threading macros"
 :date "2021-03-24"
 :description "Threading macros are one of the most useful features of the Clojure core library. Similar to a fluent API in object-oriented programming languages, these macros are both more powerful and more flexible."
 :author "cassidy"}
 
When a new developer comes to Clojure from an imperative or object-oriented programming language, we often attempt to program imperatively using Clojure instead of learning to program the Clojure way. This makes sense, of course, and often the newcomer will struggle for some time to grok functional programming style and the Clojure way in particular. Typically this manifests in strange constructs like nested `let` blocks or passing of values through the `let` bindings themselves. For example a neophyte might devise something like this:

```clojure
(let [a 7]
  (let [b (do-something a)]
    (let [c (something-else b)]
  (compute-final-value c))))
```

or slightly less bad:

```clojure
(let [a 7
      b (do-something a)
      c (something-else b)]
  (compute-final-value c))
```

Imperative programming conditions us to think like this, and Clojure is an expressive enough language that it permits us to abuse it in such a manner. Nevertheless, if you spend any amount of time reading Clojure code you'll come across the threading macros: `->`, `->>`, `as->`, `cond->`, and so on. These macros support a functional style of programming in a syntactically elegant way.

# Thread first `->`

The thread first macro takes the result of one function and "threads" it as the first argument to the next. This is handy when one needs to chain a set of function calls and doesn't want to deal with the cognitive load of nesting those calls inside one another.

```clojure
(- (* (/ 10 2) 4) 3) => 17
```

can be rewritten using a threading macro as:

```clojure
(-> (/ 10 2)
    (* 4)
    (- 3)) ;; => 17
```

It's much clearer to reason about what is happening in this code and easier to make changes; all the while this code follows good functional style.

# Thread last `->>`

The thread last does the same thing as the thread first macro except that it "threads" the result of one function into the next as the last argument.

Thus this:

```clojure
(- 3 (* 4 (/ 10 2))) ;; => -17
```

becomes this:

```clojure
(->> (/ 10 2)
     (* 4)
     (- 3)) ;; => -17
```

# Thread as `as->`

But reality is that not all functions will be chained in such a way that the threading needs to happen in the first or the last position. There will be times you may even want to thread into the second of 3 arguments. That's where the `as->` macro comes in. It takes two fixed arguments, the value to thread and a binding symbol which is the placeholder determing where the threading happens. It's difficult to come up with an example for this using only the functions in clojure.core because they're well planned out, functions which work on similar data structures expect that data structure in the same position. Nevertheless, when using libraries or during Java interop situations will arise wherein you'll want to chain function calls, but the argument position will change. So let's say you've parsed some JSON user input and you neeed to sanitize the input before inserting it into a database:

```clojure
(as-> {:colors ["Blue" "Orange" "Green"]} m
      (get m :colors)
      (map clojure.string/lower-case m)
      (vec m))
```

What's going on here? Well first the map with the key `:colors` is being bound to the symbol `m`, then it is passed to the `get` function in the first position. The output of that is bound to `m` before it is passed in to `map` in last position.

# `some->` and `some->>`

What if we want to short-circuit and not evaluate a function if the preceding function returns `nil`. Well that's where `some->` and `some->>` come in. They work the same as `->` and `->>` respectively, but if any function in the pipeline returns `nil` then the entire expression will evaluate to `nil` instead of the next function throwing an exception. Let's look at our previous example, if the key `:colors` isn't in the map it will return `nil`.

```clojure
(some-> {}
        (get :colors)
        (vec))
```

This will evaluate to `nil` instead of `[]`.

# `cond->` and `cond->>`

The final threading macro is the `cond->` macro which is useful for situations where one may want to guard against the evaluation of one of the expressions in the pipeline under certain circumstances, but continue evaluation of the rest. For each pair of forms, if the expression on the left evaluates to true, then the return value of the last expression evaluated will be threaded as the first argument to the expressino on the right.

```clojure
(defn simple-type [x]
  (cond-> nil
    (integer? x) (str "integer")
    (string? x) (str "string")
    (boolean? x) (str "boolean")
    (seqable? x) (str " seqable")))

(simple-type "hello") ;; => "string seqable"
```

# whew!

Well that's it for the threading macros. As you become comfortable using them, your code will become cleaner, easier to read, and more idiomatic Clojure.

Ciao for now!
