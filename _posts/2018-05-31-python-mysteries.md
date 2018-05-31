---
layout: post
category : python
tagline: "Mysterious stuff"
tags : [python, mystery]
---
{% include JB/setup %}

# Mysterious python stuff

 What do you believe this will print? 
{% highlight python %}
    for _ in range(100):
        def _(_=42):
            return _

    _()
{% endhighlight %}
