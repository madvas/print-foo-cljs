## print-foo-cljs

This is fork of [print-foo](https://github.com/AlexBaranosky/print-foo). I added Clojurescript support and did some other edits.

A Clojure library of print debugging macros.  

Just add "print-" to the front of a normal `->>`, `->`, `let`, `defn`, `defn-`, `cond`, `if` and prints useful information.

If you're using [Cursive](https://cursive-ide.com/), I suggest creating macro or live template for prefixing `print-` or `pf/print-`

## Leiningen

```clj
[print-foo-cljs "2.0.3"]
```

## Usage

For Clojure
```clojure
(ns my-namespace
    (:require [print.foo :as pf]))
```

For Clojurescript
```clojure
(ns my-namespace
    (:require [print.foo :as pf :include-macros true]))
```


```clojure
print.foo=> (use 'print.foo)
nil

print.foo=> (+ 1 2 (print-and-return "ONE:: " (inc 4)))
ONE::5
=> 8

print.foo> (- 1000 (tap "RESULT:: " (+ 1 2 (inc 4))))
 *** RESULT::8
=> 992

print.foo> (def a (inc 4))
print.foo> (- 1000 (+ 1 2 (look a)))
 *** A:: 5
=> 992

print.foo> (- 1000 (look (+ 1 2 (inc 4))))
 *** (+ 1 2 (INC 4)):: 8
=> 992

print.foo=> (print-sexp (str (+ 3 4) (+ 5 (* 6 2)) 4))
(+ 3 4) 7
(* 6 2) 12
(+ 5 (* 6 2)) 17
(str (+ 3 4) (+ 5 (* 6 2)) 4) "7174"
=> "7174"

print.foo=> (print-->> [1 2 3] (mapv inc) (mapv dec))
[1 2 3] [1 2 3]
(mapv inc) [2 3 4]
(mapv dec) [1 2 3]
=> [1 2 3]

print.foo=> (print--> 1 inc dec inc dec)
1 1
inc 2
dec 1
inc 2
dec 1
=> 1

print.foo=> (print-let [a 2 b 3 c (+ a b) d (+ c 7)] c)
a 2
b 3
c 5
d 12
let result: 5
=> 5

print.foo=> (print-defn f [a b] (* a b))
#'user/f
print.foo=> (f 3 4)
a 3
b 4
defn 'f' result: 12
=> 12

print.foo=> (print-defn- g [a b] (+ a b))
#'user/f
print.foo=> (g 3 4)
a 3
b 4
defn- 'g' result: 7
=> 7

print.foo=> (print-cond nil 2 3 4)
test: 3
value: 4
=> 4

print.foo=> (print-cond-> {}
                          (pos? 1)
                          (assoc :a 1)

                          (pos? 2)
                          (merge {:b 2})

                          (neg? 2)
                          (merge {:c 3}))
test: (pos? 1)  value: {:a 1}
test: (pos? 2)  value: {:a 1, :b 2}
=> {:a 1, :b 2}

(print-cond->> [1 2 3]
               (pos? 1)
               (map inc)

               (neg? 2)
               (map dec))
test: (pos? 1)  value: (2 3 4)
=> (2 3 4)

print.foo=> (print-if (odd? 9) 1 2)
(odd? 9) true
1 1
=> 1

print.foo> (middleware->
            {:get-in [:session]
             :timings? true}}
            my-handler
            wrap-exception-handling
            wrap-params)

(defn m1 [x]
  (println x)
  x)
(defn m2 [x]
  (fn [y]
    (x (assoc y :m2 true))))
(defn m3 [x]
  (fn [y]
    (x (assoc y :m3 true))))

((middleware-> {:timings? true
                :get-in [:session]}
               m1
               m2
               m3)

 {:session {:token 1}})

;; prints:

"REQUEST - GOING INTO: m3"
{:token 1}
"REQUEST - GOING INTO: m2"
{:token 1}
{:session {:token 1}, :m3 true, :m2 true}
"RESPONSE - COMING OUT OF: m2"
{:token 1}
"RESPONSE - COMING OUT OF: m3"
{:token 1}
{:middleware-timings
 [{:middleware "m2", :middleware-elapsed 1}
  {:middleware "m3", :middleware-elapsed 3}]}
=> {:session {:token 1}, :m3 true, :m2 true}

```

## License

Distributed under the MIT License
