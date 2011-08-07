require 'rubygems'
require 'bundler/setup'
require 'appraisal'

require 'rake'
require 'rake/testtask'
require 'rdoc/task'

desc 'Default: run all tests.'
task :default => :test

desc "Test state_machine."
Rake::TestTask.new(:test) do |t|
  integration = %w(active_model active_record data_mapper mongoid mongo_mapper sequel).detect do |name|
    Bundler.default_gemfile.to_s.include?(name)
  end
  
  t.libs << 'lib'
  t.test_files = integration ? Dir["test/unit/integrations/#{integration}_test.rb"] : Dir['test/{functional,unit}/*_test.rb'] + ['test/unit/integrations/base_test.rb']
  t.verbose = true
end

begin
  require 'rcov/rcovtask'
  namespace :test do
    desc "Test state_machine with Rcov."
    Rcov::RcovTask.new(:rcov) do |t|
      t.libs << 'lib'
      t.test_files = Dir['test/**/*_test.rb']
      t.rcov_opts << '--exclude="^(?!lib/)"'
      t.verbose = true
    end
  end
rescue LoadError
end

desc "Generate documentation for state_machine."
Rake::RDocTask.new(:rdoc) do |rdoc|
  rdoc.rdoc_dir = 'rdoc'
  rdoc.title    = 'state_machine'
  rdoc.options << '--line-numbers' << '--inline-source' << '--main=README.rdoc'
  rdoc.rdoc_files.include('README.rdoc', 'CHANGELOG.rdoc', 'LICENSE', 'lib/**/*.rb')
end

namespace :appraisal do
  desc "Run the given task for a particular integration's appraisals"
  task :integration do
    integration = ENV['INTEGRATION']
    
    Appraisal::File.each do |appraisal|
      if appraisal.name.include?(integration)
        appraisal.install
        Appraisal::Command.from_args(appraisal.gemfile_path).run
      end
    end
    
    exit
  end
end

load File.dirname(__FILE__) + '/lib/tasks/state_machine.rake'
