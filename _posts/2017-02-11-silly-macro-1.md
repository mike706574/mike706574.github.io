---
layout: post
title: 'A silly Clojure macro'
---
In Clojure, let's say you want to produce a map, but some values in the map depend on other values.

The most obvious way to do that is to use **let**:

{% highlight clojure %}
(let [foo 1]
  {:foo foo
   :bar (inc foo)})
;; -> {:foo 1, :bar 2}
{% endhighlight %}

If you're silly, you could use a macro like this:

{% highlight clojure %}
(defmacro let-map
  [kvs]
  `(let ~(vec kvs)
     ~(into {} (map #(vector (keyword %) %) (take-nth 2 kvs)))))
{% endhighlight %}

When we use it, here's what our example looks like:

{% highlight clojure %}
(let-map [foo 1
          bar (inc foo)])
;; -> {:foo 1, :bar 2}
{% endhighlight %}

Why would you do this? You probably wouldn't, but here's a real situation where it might be handy:

{% highlight clojure %}
(rf/reg-sub
  :page
  (fn [{:keys [page-number movies] :as db} _]
    (when movies
      (let [movie-count (count movies)
            page-count (quot movie-count 10)
            page-index (* (dec page-number) 10)
            page (subvec movies page-index (+ page-index 10))]
        {:movie-count movie-count
         :page-count page-count
         :page-index page-index
         :page page}))))
{% endhighlight %}

With the macro, we can slim it down a little:

{% highlight clojure %}
(rf/reg-sub
  :page
  (fn [{:keys [page-number movies] :as db} _]
    (when movies
      (let-map [movie-count (count movies)
                page-count (quot movie-count 10)
                page-index (* (dec page-number) 10)
                page (subvec movies page-index (+ page-index 10))]))))
{% endhighlight %}

It saved us a few lines, but it's probably not worth the cost of defining it as a private function or importing it from some other namespace.
