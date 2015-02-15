---
title:  "ToStringBuilder"
date:   2015-02-12 18:09:00
categories: java
---



{% highlight java %}
import org.apache.commons.lang3.builder.ToStringBuilder;

public class NoteDto {
    private int noteId;
    private String title;
    private int ownId;
    private int expire;
    // ...
    @Override
    public String toString() {
        return ToStringBuilder.reflectionToString(this);
    }
}
{% endhighlight %}


각 맴버별로 toString()값을 더해주지 않아도 된다.