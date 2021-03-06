---
layout: post
title: "Automatically backing up your remote database on deploy"
id: 4cf5b678dabe9d2c610004a4
updated: 2010-11-30T21:44:08-05:00
date: 2007-02-09T10:08:00-05:00
tags:
- code
redirect_from: /blog/archives/2007/02/09/automatically-backing-up-your-remote-database-on-deploy/
---

Ever had a migration fail when you're deploying a sparkling new release of your snazzy web app, leaving your database in an inconsistent state? I have. Fortunately, a faulty migration has never completely hosed my production database, but I have had to ssh in, comment some lines out of a migration, and re-run it.

Now, I can rest assured that I'll never have the experience of hosing my production database without being able to recover it. Here are Capistrano recipes to backup your remote production database whenever you run <code>migrate</code> on your remote database.

{% highlight ruby %}
require 'yaml'

desc "Backup the remote production database"
task :backup, :roles => :db, :only => { :primary => true } do
  filename = "#{application}.dump.#{Time.now.to_i}.sql.bz2"
  file = "/tmp/#{filename}"
  on_rollback { delete file }
  db = YAML::load(ERB.new(IO.read(File.join(File.dirname(__FILE__), 'database.yml'))).result)['production']
  run "mysqldump -u #{db['username']} --password=#{db['password']} #{db['database']} | bzip2 -c > #{file}"  do |ch, stream, data|
    puts data
  end
  `mkdir -p #{File.dirname(__FILE__)}/../backups/`
  get file, "backups/#{filename}"
  # capistrano < 1.4
  # `rsync #{user}@#{roles[:db][0].host}:#{filename} #{File.dirname(__FILE__)}/../backups/`
  delete file
end

desc "Backup the database before running migrations"
task :before_migrate do
  backup
end
{% endhighlight %}

The backup recipe is adapted from ones presented by [Caboo.se](http://blog.caboo.se/articles/2006/12/28/a-better-capistrano-backup) and [Bojan Mihelac](http://source.mihelac.org/articles/2007/01/11/capistrano-get-method-download-files-from-server). I modified it to use my local database.yml. Thanks to [Daniel Morrison](http://daniel.collectiveidea.com/blog), who came up with the idea of running the backup task before migrating.
