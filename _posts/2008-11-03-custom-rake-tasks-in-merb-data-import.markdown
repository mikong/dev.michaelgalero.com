--- 
wordpress_id: 22
layout: post
title: "Custom rake tasks in Merb: Data Backup and Import"
tags: []

categories: 
- slug: ruby
  title: Ruby
- slug: merb
  title: Merb
wordpress_url: http://devblog.michaelgalero.com/2008/11/03/custom-rake-tasks-in-merb-data-import/
---
There are a lot of [data import solutions in Rails](http://railspikes.com/2008/2/1/loading-seed-data), most of which depend on ActiveRecord. Since Merb supports ActiveRecord too, you can use those solutions in your Merb app. But I'm using DataMapper in my Merb app, so I had to look for another way.

This article shows how to create a simple rake task in a Merb + DataMapper project. It then talks about the Data Backup and Import rake tasks db:dump_data and db:load_data. I've added some notes for those with a Rails background.

### A simple rake task

When I generated my Merb app, the lib folder wasn't generated. It looked something like this:

<pre>
sample_app
|--&gt; app
|--&gt; autotest
|--&gt; config
|--&gt; doc
|--&gt; gems
|--&gt; merb
|--&gt; public
|--&gt; spec
|--&gt; Rakefile
`--&gt; tasks
</pre>

The folder structure seems to suggest that you write your custom rake tasks under the tasks folder of your Merb app, but this is not the case. Read the Rakefile and you can see these 3 important details:

- Add your custom tasks named file_name.rake in /lib/tasks.
- The Merb environment is initialized to the MERB_ENV value, or 'rake' environment if MERB_ENV is not set.
- To start the runner environment, in case you need access to your application's classes, there is a task called :merb_env.

So the location is just like in Rails, i.e. in the lib/tasks folder. Try creating a custom.rake file in the lib/tasks folder and add the following:

<pre>
  desc "Print all classes that include DataMapper::Resource."
  task :print_dm_resources =&gt; :merb_env do
    DataMapper::Resource.descendants.each do |resource|
      puts resource
    end
  end
</pre>

The rake task above depends on the :merb_env task in order to access the application's models. This is just like a rake task in Rails that depends on the :environment task. To run the task:

<pre>
$ MERB_ENV=development rake print_dm_resources
</pre>

### Data Backup and Import

The following rake tasks are based off of the [rake file](http://blog.leetsoft.com/files/code/backup.rake) provided in Tobias Lutke's old blog post about [migration between databases](http://blog.leetsoft.com/2006/5/29/easy-migration-between-databases). I've translated it to work with Merb + DataMapper:

<pre>
namespace :db do

  def interesting_tables
    DataMapper::Resource.descendants.reject! do |table|
      [Merb::DataMapperSessionStore].include?(table)
    end
  end

  desc "Dump data from the current environment's DB."
  task :dump_data =&gt; :merb_env do
    dir = Merb.root_path("config/data/#{Merb.env}")
    FileUtils.mkdir_p(dir)
    FileUtils.chdir(dir)

    interesting_tables.each do |table|
      puts "Dumping #{table}..."
      File.open("#{table}.yml", 'w+') { |f| YAML.dump(table.all.collect(&amp;:attributes), f) }
    end
  end

  desc "Load data (from config/data/&lt;environment&gt;) into the current environment's DB."
  task :load_data =&gt; :merb_env do
    dir = Merb.root_path("config/data/#{Merb.env}")
    FileUtils.mkdir_p(dir)
    FileUtils.chdir(dir)

    adapter = DataMapper.repository(:default).adapter

    interesting_tables.each do |table|
      table.transaction do |txn|
        puts "Loading #{table} data..."
        YAML.load_file("#{table}.yml").each do |fixture|
          adapter.execute("INSERT INTO #{table.name.pluralize.snake_case} (#{fixture.keys.join(",")}) VALUES (#{fixture.values.collect {|value| adapter.send(:quote_column_value, value)}.join(",")})")
        end
      end
    end
  end
end
</pre>

Add the above to your custom rake file. To dump the data from your development environment, run the following:

<pre>
$ MERB_ENV=development rake db:dump_data
</pre>

This will create the folder config/data/development if it doesn't exist yet, and generate a ModelName.yml file for each of your models that included DataMapper::Resource. It is necessary to specify MERB_ENV, otherwise the environment will be initialized to the 'rake' environment and the folder config/data/rake will be created instead.

Then as you migrate your database to the latest version of your models, our database is cleared of its data (a side effect when using DataMapper's automigrate):

<pre>
$ rake db:automigrate
</pre>

After migrating, we can just reload the data using our other rake task:

<pre>
$ MERB_ENV=development rake db:load_data
</pre>

If you were to use the sample process above, you might want to update the yaml files to handle the changes that happened with the migration. If there's a new model, you can just create a new yaml file for it.

Here are some of the things you can do with the script:

- change the path where the yaml files are stored
- use a different file format for the data (quite a big change though)
- edit interesting_tables method to exclude more models
- clear tables before loading the data (this could be dangerous!)
- create a task that depends on dump_data, automigrate, and load_data

I didn't clear the tables before loading the data because I prefer that the rake task throw an error when I'm running it on a populated database.

There are probably better ways to approach this problem. In my case though, I just needed a quick solution to reload my data after running automigrate.
