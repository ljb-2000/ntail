def dump_load_path
  puts $LOAD_PATH.join("\n")
  found = nil
  $LOAD_PATH.each do |path|
    if File.exists?(File.join(path,"rspec"))
      puts "Found rspec in #{path}"
      if File.exists?(File.join(path,"rspec","core"))
        puts "Found core"
        if File.exists?(File.join(path,"rspec","core","rake_task"))
          puts "Found rake_task"
          found = path
        else
          puts "!! no rake_task"
        end
      else
        puts "!!! no core"
      end
    end
  end
  if found.nil?
    puts "Didn't find rspec/core/rake_task anywhere"
  else
    puts "Found in #{path}"
  end
end
require 'bundler'
require 'rake/clean'

require 'rake/testtask'

require 'cucumber'
require 'cucumber/rake/task'
gem 'rdoc' # we need the installed RDoc gem, not the system one
require 'rdoc/task'

include Rake::DSL

Bundler::GemHelper.install_tasks


Rake::TestTask.new do |t|
  t.pattern = 'test/tc_*.rb'
end


CUKE_RESULTS = 'results.html'
CLEAN << CUKE_RESULTS
Cucumber::Rake::Task.new(:features) do |t|
  t.cucumber_opts = "features --format html -o #{CUKE_RESULTS} --format pretty --no-source -x"
  t.fork = false
end

Rake::RDocTask.new do |rd|

  rd.main = "README.rdoc"

  rd.rdoc_files.include("README.rdoc","lib/**/*.rb","bin/**/*")
end

task :default => [:test,:features]

task :gemspec do
  @gemspec ||= eval(File.read(Dir["*.gemspec"].first))
end

desc "Validate the gemspec"
task :validate => :gemspec do
  @gemspec.validate
end
