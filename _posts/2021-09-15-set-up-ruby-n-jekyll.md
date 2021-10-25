---
title: Set Up Ruby and Jekyll
tags: [Misc, Ruby]
---

## Uninstall Ruby on Mac
To see how Ruby was installed:
```
which -a ruby
```

Remove Ruby and dependencies from Homebrew:
```
brew uninstall --force ruby
brew autoremove
brew list ruby
```

See [Install Ruby 3.0 · macOS Big Sur or Catalina](https://mac.install.guide/ruby/9.html)

## Install Ruby
With Homebrew:
```
brew install ruby
```

Add the brew ruby and gems path to zsh:
```
echo 'export PATH="/usr/local/opt/ruby/bin:/usr/local/lib/ruby/gems/3.0.0/bin:$PATH"' >> ~/.zshrc

source ~/.zshrc
which ruby
ruby -v
```

## Install Jekyll
Install the bundler and jekyll gems:
```
gem install --user-install bundler jekyll

ruby -v
```

Append your path file with the following, replacing the X.X with the first two digits of your Ruby version:
```
echo 'export PATH="$HOME/.gem/ruby/X.X.0/bin:$PATH"' >> ~/.zshrc
```

Check that GEM PATHS points to your home directory:
```
gem env
```

See [Jekyll on macOS](https://jekyllrb.com/docs/installation/macos/)

## TeXt Theme
安装 Ruby 依赖包:
```
bundle install --path vendor/bundle
```

本地预览:
```
bundle exec jekyll serve
```

## Debug
(1) Description:
```
/vendor/bundle/ruby/3.0.0/gems/jekyll-4.2.0/lib/jekyll/commands/serve/servlet.rb:3:in `require': cannot load such file -- webrick (LoadError)
```

Solution: Add `gem "webrick"` to the Gemfile.
