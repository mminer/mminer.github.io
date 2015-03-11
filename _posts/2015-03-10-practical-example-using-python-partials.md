---
title: "A Practical Example of Using Python Partials"
---

You're unlikely to encounter Python [partials](https://docs.python.org/3/library/functools.html#partial-objects) during everyday development, but they're useful in the right scenario. Here's one of them.[^1]

*(This post demonstrates a use for partials, but it's light on details. For an informative overview of these suckers read [Cleaner Code Through Partial Function Application](http://chriskiehl.com/article/Cleaner-coding-through-partially-applied-functions/).)*

Some REST APIs page results, returning a limited number of entries at a time. Fine. Sometimes though we want to retrieve the entire dataset. Not an inherently difficult task --- simply make requests until no pages remain. Here's a reusable function that does exactly this.

```python
from itertools import chain
import requests

def fetch_all_pages(url):
    """Fetches paged results from a REST endpoint."""
    def fetch_page(next_page):
        """Generator that fetches pages of results until none are left."""
        while next_page:
            response = requests.get(next_page)
            yield response.json()

            # Get next page from the response's Link header.
            next_page = response.links.get('next', {}).get('url')

    results = chain.from_iterable(page for page in fetch_page(url))
    return results

# Fetch all commits in Zulko/moviepy GitHub repository.
url = 'https://api.github.com/repos/Zulko/moviepy/commits'
results = fetch_all_pages(url)
print(list(results))
```

This works well, but it assumes a GET request with no headers or authentication or other info we might wish to pass along to the API. The call to `requests.get(next_page)` ought to accept any arguments the caller wants to send to it. Even better, it shouldn't care whether we're doing a GET or a POST or whether we're reusing a Requests [Session object](http://docs.python-requests.org/en/latest/user/advanced/#session-objects). This is where partials help.

Let's modify `fetch_all_pages` to accept a function that we'll call instead of `requests.get` (though we'll make `requests.get` the default for cases when the first version of our function fit the bill).

```python
from itertools import chain
import requests

def fetch_all_pages(url, request_func=requests.get):
    """Fetches paged results from a REST endpoint."""
    def fetch_page(next_page):
        """Generator that fetches pages of results until none are left."""
        while next_page:
            response = request_func(next_page)
            yield response.json()

            # Get next page from the response's Link header.
            next_page = response.links.get('next', {}).get('url')

    results = chain.from_iterable(page for page in fetch_page(url))
    return results
```

Now we can do something nifty: using partials, we can construct a request function with custom arguments beforehand and pass it to `fetch_all_pages`. When `request_func(next_page)` is called, those custom arguments as well as the page URL are all sent together to the underlying function.

```python
# Fetch all commits in Zulko/moviepy GitHub repository (but only since 2015).
from functools import partial
url = 'https://api.github.com/repos/Zulko/moviepy/commits'
request_func = partial(requests.get, params={'since': '2015-01-01T00:00:00Z'})
results = fetch_all_pages(url, request_func)
print(list(results))
```

This technique makes `fetch_all_pages` much more reusable. Need to include custom authentication headers with your request? No problem. Want to send cookies to the API? Easy. No further modifications to our function are necessary. High fives for everybody.

---

[^1]: Code snippets [on GitHub](https://github.com/mminer/blog-code/tree/master/practical-example-using-python-partials).
