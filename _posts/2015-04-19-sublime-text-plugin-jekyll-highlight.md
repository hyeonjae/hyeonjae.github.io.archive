---
title:  "Sublime text 플러그인: 코드 하이라이팅 블럭 변환하기"
date:   2015-04-19 15:32:14
categories: jekyll
---

# Jekyll 형식으로 코드 하이라이팅 블럭 변환하기


Sublime text로 Jekyll 마크다운을 작성하다보면, Jekyll형식의 코드 하이라이팅 블럭과 Markdown형식의 코드 하이라이팅 차이때문에 불편함을 느낀다.

# 일반적인 마크다운
{% highlight text %}
```python
def foo():
	return 'hello'*10
```
{% endhighlight %}

# Jekyll 형식 마크다운
{% highlight text %}
{ % highlight python % }
def foo():
	return 'hello'*10
{ % endhighlight % }
{% endhighlight %}

Jekyll에서 GFM으로, GFM에서 Jekyll로 변환하는 플러그인을 만들어보자.


# 1. 플러그인 생성 
우선 `[Tools] - [New Plugin...]`으로 새 플러그인 파일을 만든다.  
그리고 아래와 같이 작성한다.  
{% highlight python %}
# convert_highlight_markdown_to_jekyll.py
import sublime, sublime_plugin
import re

class ConvertHighlightMarkdownToJekyllCommand (sublime_plugin.TextCommand):
	def run(self, edit):
		region = sublime.Region(0, self.view.size())
		text = self.view.substr(sublime.Region(0, self.view.size()))

		start = re.compile(r'^```([A-z]+)$', re.M)
		end = re.compile(r'^```[ ]{0,2}$', re.M)

		text = re.sub(start, r'{ % highlight \1 % }', text)
		text = re.sub(end, r'{ % endhighlight % }', text)

		self.view.replace(edit, region, text)
{% endhighlight %}
파일명은 반드시 Class명과 같게 해야한다. Class명은 파스칼 표기법으로, 파일명은 스네이크 표기법으로 맞춰줘야한다.

반대로 변환해주는 플러그인도 만들어 준다.
{% highlight python %}
# convert_highlight_jekyll_to_markdown.py
import sublime, sublime_plugin
import re

class ConvertHighlightJekyllToMarkdownCommand (sublime_plugin.TextCommand):
	def run(self, edit):
		region = sublime.Region(0, self.view.size())
		text = self.view.substr(sublime.Region(0, self.view.size()))

		start = re.compile(r'^{ % highlight ([A-z]+) % }$', re.M)
		end = re.compile(r'^{ % endhighlight % }$', re.M)

		text = re.sub(start, r'```\1', text)
		text = re.sub(end, r'```', text)

		self.view.replace(edit, region, text)
{% endhighlight %}
이제 단축키로 위 플러그인을 실행시킬 수 있도록 키바인딩 설정을 해주자.

# 2. 키 바인딩
`[Preferences] - [Key Binding - User]`를 선택하면 키바인딩 설정 파일이 나온다. 아래와 같이 위에서 작성한 플러그인 파일을 설정하자.
{% highlight json %}
[
	{"keys": ["ctrl+shift+."], "command": "add_jekyll_title" },
	{"keys": ["ctrl+shift+["], "command": "convert_highlight_markdown_to_jekyll" },
	{"keys": ["ctrl+shift+]"], "command": "convert_highlight_jekyll_to_markdown" }
]
{% endhighlight %}
저장하고, `ctrl+shift+[`와 `ctrl+shift+]`를 눌러주면 된다.


_소스 코드를 그대로 복사, 붙여넣기 하지마세요._  
_`{ % highlight % }`에서 `{`와 `%` 사이의 공백을 지우고 쓰세요._  
  
__참고자료__  
[https://www.sublimetext.com/docs/3/api_reference.html#sublime.Edit](https://www.sublimetext.com/docs/3/api_reference.html#sublime.Edit)

