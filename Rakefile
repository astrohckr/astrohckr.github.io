require 'rake'
require 'rubygems'
require 'jekyll'

desc 'Clean up generated site'
task :clean do
  cleanup
end

desc 'Preview on local machine (build with --auto)'
task :preview => :clean do
  Rake::Task[:tags].invoke
  jekyll("serve --drafts --watch --destination #{options['destination']}")
end

desc 'Build on local machine (build without --auto)'
task :build => :clean do
  Rake::Task[:tags].invoke
  jekyll("build --destination #{options['destination']}")
end

desc "Generate tags"
task :tags do
  include Jekyll::Filters

  site = Jekyll::Site.new(options)
  site.read_posts('')

  # To clear the existing tag pages.
  FileUtils.rm Dir.glob('tags/*.html')

  site.tags.each do |tag, posts|
    create_file_with_content tag_page(tag), File.join("tags", "#{sanitize(tag)}.html")
  end
end

desc "Given a title as an argument, create a new post file"
task :write do
  post_title = ENV['TITLE']
  skip_open = ENV['SKIP_OPEN'] # if specified, don't open in editor
  unless file_exists_at_path? file_path(post_title)
    create_file_with_content post_front_matter(post_title), file_path(post_title)
    if !skip_open
      open_file_in_editor file_path(post_title)
    end
  end
end

def file_exists_at_path?(file_path)
  if File.exist? file_path
    raise RuntimeError.new("Sorry, #{file_path} already exists!")
  end
end

def create_file_with_content(content, file_path)
  File.open(file_path, 'w') do |file|
    file.write content
  end
end

def file_path(post_title)
  File.join("_posts", post_filename(post_title))
end

def post_filename(post_title)
  "#{Time.now.strftime('%Y-%m-%d')}-#{sanitize(post_title)}.md"
end

def sanitize(post_title)
  post_title.
    gsub(/[^0-9a-z\s]/i, '').
    gsub(/\s/, '-').
    downcase
end

def post_front_matter(post_title)
%Q(---
layout: post
title: #{post_title}
date: #{Time.now.strftime('%Y-%m-%d %k:%M:%S')}
tags:
 - like
 - this
---)
end

def tag_page(tag)
%Q(---
layout: page
slug: logs
type: index
title: Posts tagged #{tag}
exclude_from_nav: true
---
{% assign tag_name="#{tag}" %}
{% include posts_by_tag.html %}
)
end

def open_file_in_editor(file_path)
  `#{options['editor']} #{file_path}`
  puts "Opening #{file_path} in #{options['editor']}."
end

def cleanup
  sh 'rm -rf _site'
end

def jekyll(directives = '')
  sh 'jekyll ' + directives
end

def options
  Jekyll.configuration({})
end
