---
title:  "Sublime text에서 jekyll 사용하기"
date:   2015-03-05 18:41:23
categories: jekyll
---


## Sublime Text에서 jekyll 블로그 포스팅 하기

jekyll에 포스팅 하려면 상단에 일정한 양식을 써야 한다.
  
매번 직접 타이핑 하기 힘드니 sublime text의 플러그인을 만들어보자.

![1]({{ site.url }}/assets/sublime_text_new_plugin.png)

파일 이름은 `add_jekyll_title.py`로 만들자
{% highlight python %}
import sublime, sublime_plugin
import datetime
from string import Template

class AddJekyllTitleCommand(sublime_plugin.TextCommand):
    def run(self, edit):
        timestamp = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        title = Template( "\n".join(["---", 
            "title:  \"title\"", 
            "date:   $timestamp", 
            "categories: ", 
            "---", 
            ""]) 
        ).safe_substitute(dict(timestamp=timestamp))
        self.view.run_command("insert_snippet", { "contents": "%s" %  title } )
{% endhighlight %}


이제 `key binding`을 설정하자.
![1]({{ site.url }}/assets/sublime_text_key_binding.png)


`keys`에 원하는 단축키를 설정하고, `command`에 위에서 만든 플러그인 파일명을 넣는다.
{% highlight json %}
[
    {"keys": ["ctrl+shift+."], "command": "add_jekyll_title" }
]
{% endhighlight %}

저장하면 끝

  
  
사용법은 새 창에서 조금전 설정한 단축키를 눌러주면 된다.
