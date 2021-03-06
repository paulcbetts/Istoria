$:.unshift(File.join(File.dirname(__FILE__), 'lib'))

require 'cucumber/rake/task'

Cucumber::Rake::Task.new do |t|
    t.cucumber_opts = %w{--format pretty} + (ENV["TAGS"] ? ["--tags", "#{ENV["TAGS"]}"] : [])
end

# Vagrant brilliantly uses a C library to parse its JSON config files
unless defined?(RUBY_ENGINE) && RUBY_ENGINE == "jruby" 
  require 'vagrant'
  Vagrant::Env.load!
end

task :test => [
  :cucumber
]

task :reset_db do
  `mkdir -p tmp/dev_db`
  `rm -rf tmp/dev_db/*`
  `mkdir -p tmp/dev_db/solr`
end

task :db do
  `sunspot-solr start --data-directory=tmp/dev_db/solr`
  system("mongod --dbpath tmp/dev_db -vvv")
  `sunspot-solr stop`
end

task :repl do
  require 'importers/tumblrxml_importer'
  Mongo::Connection.new().db('istoria-repl')
  MongoMapper.database = 'istoria-repl'

  ti = TumblrXmlImporter.new
  ti.import 'features/fixtures/tumblr_basic.xml'
  system('irb -r "lib/types"')

  Mongo::Connection.new().drop_database 'istoria-repl'
end
