# America's Test Kitchen heroku-buildpack-apt
Adds support for apt-based dependencies during both compile and runtime.

This repo (originally forked from [ddollar/heroku-buildpack-apt](https://github.com/ddollar/heroku-buildpack-apt) ), was used to install dependencies during the Heroku build process for migrating The Feed app from Heroku's Cedar-10 to Cedar-14 stack. The following apache dependencies were absent in Cedar-14 so apache wouldn't run until these dependencies could be installed & found when starting the app:
[Heroku's list of Ubuntu Packages on Cedar and Cedar-14](https://devcenter.heroku.com/articles/cedar-ubuntu-packages)
    libjpeg62
    libssl0.9.8 (which includes a dependency, libcrypto.so.0.9.8)

## Usage

This buildpack works in conjuction with ATK's [heroku-buildpack-multi](https://github.com/Americastestkitchen/heroku-buildpack-multi)
... (which was forked from Heroku's [heroku-buildpack-multi](https://github.com/heroku/heroku-buildpack-multi).

The heroku-buildpack-multi calls on your app's existing buildpacks such as https://github.com/Americastestkitchen/heroku-buildpack-apt and https://github.com/Americastestkitchen/heroku-wordpress-php

I removed the test folder which contained Aptfile & .buildpacks files to avoid confusion about how and where they are used.

The Aptfile & .buildpacks files are used on the app's code base in the root of the project folder: see them in [ATK's Feed app](https://github.com/Americastestkitchen/feed)

---
## Examples: The following is from original repo instructions https://github.com/ddollar/heroku-buildpack-apt (once again, keep in mind that they had originally included samples of test/Aptfile & test/.buildpacks just for explanatory purposes which do not actually operate from within this buildpack's codebase).

#### Aptfile

    libpq-dev
    http://downloads.sourceforge.net/project/wkhtmltopdf/0.12.1/wkhtmltox-0.12.1_linux-precise-amd64.deb

#### .buildpacks

    https://github.com/ddollar/heroku-buildpack-apt
    https://github.com/heroku/heroku-buildpack-ruby

#### Gemfile

    source "https://rubygems.org"
    gem "pg"

### Compile with [Anvil](https://github.com/ddollar/anvil-cli)

    $ heroku plugins:install https://github.com/ddollar/heroku-build

    $ heroku create apt-pg-test

    $ heroku build . -b ddollar/multi -r
	Checking for app files to sync... done, 2 files needed
	Uploading: 100.0%
	Launching build process... done
	Preparing app for compilation... done
	Fetching buildpack... done
	Detecting buildpack... done, Multipack
	Fetching cache... done
	Compiling app...
	=====> Downloading Buildpack: https://github.com/ddollar/heroku-buildpack-apt
	=====> Detected Framework: Apt
	  Updating apt caches
	  ...
	  Installing libpq-dev_8.4.17-0ubuntu10.04_amd64.deb
	  Installing libpq5_8.4.17-0ubuntu10.04_amd64.deb
	  Writing profile script
	=====> Downloading Buildpack: https://github.com/heroku/heroku-buildpack-ruby
	=====> Detected Framework: Ruby
	  Installing dependencies using Bundler version 1.3.2
	  ...
	Putting cache... done
	Creating slug... done
	Uploading slug... done
	Success, slug is https://api.anvilworks.org/slugs/00000000-0000-0000-0000-0000000000.tgz

### Check out the PG library version

    $ heroku run bash -a apt-pg-test
    ~ $ irb
	irb(main):001:0> require "pg"
	=> true
	irb(main):002:0> PG::version_string
	=> "PG 0.15.1"

## License

MIT
