# clean google search urls
Do you get tired of dirty google urls that looks like this:


[`https://www.google.com/search?q=count+number+of+indents+python&newwindow=1&client=ms-android-xiaomi-rev1&sxsrf=ALeKk03CrOO7sWb0vkDJ8qrJ8wAh-5_4Zg:1583569926724&source=lnms&tbm=isch&sa=X&ved=2ahUKEwiQhdbX-YfoAhXozsQBHZfXCGEQ_AUoAXoECA0QAw&biw=1996&bih=1146`](https://www.google.com/search?q=count+number+of+indents+python&newwindow=1&client=ms-android-xiaomi-rev1&sxsrf=ALeKk03CrOO7sWb0vkDJ8qrJ8wAh-5_4Zg:1583569926724&source=lnms&tbm=isch&sa=X&ved=2ahUKEwiQhdbX-YfoAhXozsQBHZfXCGEQ_AUoAXoECA0QAw&biw=1996&bih=1146)


It looks ugly and may contains some unwanted data like device name, browser window size etc.

[Simple python script](https://github.com/tandav/gists/blob/master/clean_url.py) can clean it up:

```py
import sys
from urllib.parse import urlparse, urlunparse, parse_qs, urlencode

def clean(url, allowed_params=('q', 'tbm')):
    o = urlparse(url)
    params = parse_qs(o.query)
    clean_params = {p: params[p] for p in params.keys() & allowed_params}
    clean_url = urlunparse(o._replace(query=urlencode(clean_params, doseq=True)))
    return clean_url

print(clean(sys.argv[1]))
```

<details>
<summary>python script details</summary>

Only allowed params for the url are `q` (search query) and `tbm` (type of search). Some common `tbm` types:
- `tbm=isch` images
- `tbm=vid` videos
- `tbm=nws` news
- etc

</details>

## put it into your `.zshrc`
To use the script I created simple shell function in my `.zshrc`:

```sh
uc() { python ~/Documents/spaces/etc/gists/clean_url.py $1 | tee /dev/tty | pbcopy }
```
`uc` means `url clean`

<details>
<summary>shell function details</summary>

I want to print clean url in terminal and also copy it to the clipboard. To do this I use a [trick](https://stackoverflow.com/a/5677265/4204843): split output of python script using `tee` into 2 streams: 1st pipe to `/dev/tty` (which is current terminal) and 2nd pipe to `pbcopy` (macOS tool that copies stdin to clipboard. On ubuntu you can use `xclip` or `xsel`)

</details>

## usage
Pass dirty url as argument to `uc`. You should also put url into quotes:

```
$ uc 'https://www.google.com/search?q=count+number+of+indents+python&newwindow=1&client=ms-android-xiaomi-rev1&sxsrf=ALeKk03CrOO7sWb0vkDJ8qrJ8wAh-5_4Zg:1583569926724&source=lnms&tbm=isch&sa=X&ved=2ahUKEwiQhdbX-YfoAhXozsQBHZfXCGEQ_AUoAXoECA0QAw&biw=1996&bih=1146'
https://www.google.com/search?q=count+number+of+indents+python&tbm=isch
```
second line is clean url, which is also copied to the clipboard.

Mybe someday I'll create a website where you can do this. (maybe it will be more convinient than CLI tool)

[comments](https://github.com/tandav/blog/issues/1)
