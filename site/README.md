rustc-perf
----------

This is the website for Rust compiler performance monitoring. The website
contains a backend to process the raw data and expose it to the frontend,
which displays graphs to the user.

This also contains a GitHub bot to trigger on-demand benchmarking.

https://github.com/rust-lang/rustc-timing contains the raw data
from which rustc-perf pulls.

Setup
-----

Apache can also be omitted from the setup, in which case the backend will function normally on
either port 2346, or the port given by the environment variable `PORT`.

```
sudo apt-get install apache2 git
git clone https://github.com/rust-lang-nursery/rustc-perf.git /var/www/html/
cd /var/www/html/rustc-perf
git clone https://github.com/rust-lang/rustc-timing.git data
```

The following lines in `/etc/apache2/sites-enabled/000-default.conf` allow the
frontend in its current configuration to get data from the API.

The proxy_http module also needs to be enabled, which can be done via `a2enmod proxy_http`.

```
    ProxyPass / http://localhost:2346
    ProxyPassReverse / http://localhost:2346
```

The backend currently depends on a POST request being sent to the `/perf/onpush` URL
(with above configuration) when new data is made available in the timings repo. This
will intiate a git pull in the timings repo directory passed on startup.

Launching
---------

Note that this binary expects its static files under `/site/static` not `/static`. It is meant to be run from the root of this repository, not the `site` directory.
It can be run with the following command from root-dir:

```
cargo run --bin site --release data
```

The release flag is on purpose, reducing startup time ~80%.

The first time you do this the Rust repo is cloned, so it will take a minute or
two (or more if you have a slow internet connection) before the web server
starts up.
