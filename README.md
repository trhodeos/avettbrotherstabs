# avettbrotherstabs.com fork

## Download a copy

```shell
pip install wayback_machine_downloader
mkdir avettbrotherstabs
wayback_machine_downloader http://avettbrotherstabs.com:80/wiki/ -d avettbrotherstabs/ -t 20180110175659
```

## Scrape the HTML

Put this into a file:

```python
import sys
from bs4 import BeautifulSoup, Comment

def ExtractTitle(soup):
    return "<h1>" + soup.find(id='firstHeading').span.text + "</h1>"

def ExtractContent(soup):
    d = soup.find(id='mw-content-text')
    for element in d(text=lambda text: isinstance(text, Comment)):
        element.extract()
    output = ''
    for element in d:
        output += str(element)
    return output

with open(sys.argv[1], 'r') as content_file:
  content = content_file.read()
soup = BeautifulSoup(content,  'html.parser')

output = ExtractTitle(soup) + ExtractContent(soup)
with open(sys.argv[2], 'w') as f:
  f.write(output)
```

and run it:

```bash
pip install beautifulsoup4
find -L avettbrotherstabs | grep html$ | grep -v Special | sed "s/'/\\\\'/" | xargs -I{} ~/html_to_md.py "{}" "{}.out"
```

## Use pandoc to convert into markdown

First install from pandoc.org. Then:

```bash
find -L avettbrotherstabs | grep out$ |  sed "s/'/\\\\'/" | xargs -I{} pandoc -r html -w gfm -o "{}.md" "{}"
```

## Copy over files
for f in avettbrotherstabs/wiki/*/*md; do new_loc=${f/avettbrotherstabs\/wiki\//}; new_loc=${new_loc/\/index.html.out/}; cp "$f" \
    "/tmp/$new_loc"; done


