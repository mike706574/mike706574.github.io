---
layout: post
title: 'Silly macro #1'
---

Here's a silly macro.

{% highlight clojure %}
(defmacro let-map
  [kvs]
  `(let ~(vec kvs)
     ~(into {} (map #(vector (keyword %) %) (take-nth 2 kvs)))))
{% endhighlight %}
