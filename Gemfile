source "https://rubygems.org"

# This matches the exact Jekyll + plugin versions GitHub Pages runs in
# production, so what you see locally is what gets deployed.
gem "github-pages", group: :jekyll_plugins

group :jekyll_plugins do
  gem "jekyll-seo-tag"
  gem "jekyll-sitemap"
  gem "jekyll-feed"
end

# Windows / JRuby compatibility shims — harmless on macOS/Linux.
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]

# Silences a Ruby 3.x warning from older webrick-dependent gems.
gem "webrick", "~> 1.8"
