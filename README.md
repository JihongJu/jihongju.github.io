# jihongju.github.io
The source code of my personal blog. The blog is up on https://jihongju.github.io/.


### Installation

Pull from Github

```
$ git pull https://github.com/JihongJu/jihongju.github.io.git
```

Install [Ruby](https://www.ruby-lang.org/en/documentation/installation/) if you don't have Ruby installed, e.g.,

```
sudo apt-get install ruby ruby-dev build-essential
```


[Add the following lines to the bash profile](https://jekyllrb.com/docs/installation/ubuntu/), e.g. `.bashrc`
```
# Install Ruby Gems to ~/gems
export GEM_HOME=$HOME/gems
export PATH=$HOME/gems/bin:$PATH
```

Install Bundler

```
$ gem install bundler
```

Navigate to the site directory and install Jekyll using bundler

```
$ cd jihongju.github.io
$ bundle install
```


### Usage

Run on local machines

```
bundle exec jekyll serve -w
```
