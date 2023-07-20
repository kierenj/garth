# Garth

Garmin SSO auth + Connect client

## Background

Garth is meant for personal use and follows the philosiphy that your data is
your data. You should be able to download it and analyze it in the way that
you'd like. In my case, that means processing with Google Colab, Pandas,
Matplotlib, etc.

There are already a few Garmin Connect libraries. Why write another?

### Authentication

The most important reasoning was building a library with authentication that
worked on [Google Colab](https://colab.research.google.com/) and didn't require
Cloudscraper. Garth, in comparison:

1. Uses the same embedded SSO as the mobile app
1. Only requires `requests`
1. Supports MFA
1. Supports saving and resuming sessions to avoid the need to log in each time
you run a script—particularly useful if you have MFA enabled
1. Appears to be working on Google Colab thus far

### Python 3.10+

Google Colab, currently, uses 3.10. We should take advantage of all the goodies
that come along with it. If you need to use an earlier version of Python, there
are other libraries that will meet your needs. There's no intetion to backport.

### JSON vs HTML

Using `garth.connectapi()` allows you to make requests routed to the Connect API
and receive JSON vs needing to parse HTML. You can use the same endpoints the
mobile app uses.

This also goes back to authentication. Garth manages the necessary Bearer
Authentication (along with auto-refresh) necessary to make requests routed to
the Connect API.

## Instructions

### Install

```bash
pip install garth
```

### Authenticate and save session

```python
import garth
from getpass import getpass

email = input("Enter email address: ")
password = getpass("Enter password: ")
# If there's MFA, you'll be prompted during the login
garth.login(email, password)

garth.save("~/.garth")
```

### Attempt to resume session

```python
import garth
from garth import GarthException
from requests import HTTPError

garth.resume("~/.garth")
try:
    garth.client.auth_token.refresh()
except (GarthException, HTTPError):
    # Session is expired
```

## Connect API

### Wellness

```python
sleep = garth.connectapi(
    f"/wellness-service/wellness/dailySleepData/{garth.client.username}",
    params={"date": "2023-07-05", "nonSleepBufferMinutes": 60),
)
```

### Usersummary

```python
stress =  garth.connectapi(f'/usersummary-service/stats/stress/weekly/2023-07-05/52'):
```

## Google Colabs

[Graph 28-day rolling average of daily stress](https://colab.research.google.com/github/matin/garth/blob/main/colabs/stress.ipynb)
