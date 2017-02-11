---
layout: post
title: 'A silly Clojure macro'
---
In Clojure, let's say you want to produce a map, but some values in the map depend on other values.

The most obvious way to do that is to use *let*:

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

Why would you do this? You probably wouldn't.
