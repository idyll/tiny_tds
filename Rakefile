# encoding: UTF-8
require 'rake'
require 'rake/clean'
require 'rbconfig'
require 'rake/testtask'
require 'rake/extensiontask'
require 'rubygems/package_task'

# My notes for cross compile native Windows gem.
# 
# $ rbenv shell 1.8.7
# $ git clean -x -d -f
# $ bundle install
# $ unalias rake-compiler && unalias rake
# $ rake compile
# $ rake cross compile RUBY_CC_VERSION=1.8.7:1.9.3
# $ rake cross native gem RUBY_CC_VERSION=1.8.7:1.9.3

def test_libs
  ['lib','test']
end

def test_files
  Dir.glob("test/**/*_test.rb").sort
end

def gemspec
  @clean_gemspec ||= eval(File.read(File.expand_path('../tiny_tds.gemspec', __FILE__)))
end

Rake::TestTask.new do |t|
  t.libs = test_libs
  t.test_files = test_files
  t.verbose = true
end

Gem::PackageTask.new(gemspec) do |pkg|
  pkg.need_tar = false
  pkg.need_zip = false
end

task :compile => ["ports:freetds"] unless ENV['TINYTDS_SKIP_PORTS']

Rake::ExtensionTask.new('tiny_tds', gemspec) do |ext|
  ext.lib_dir = 'lib/tiny_tds'
  if RUBY_PLATFORM =~ /mswin|mingw/ then
    # Define target for extension (supporting fat binaries).
    RUBY_VERSION =~ /(\d+\.\d+)/
    ext.lib_dir = "lib/tiny_tds/#{$1}"
  else
    ext.cross_compile = true
    ext.cross_platform = ['i386-mingw32']
    ext.cross_config_options << "--disable-lookup"
  end
end

task :build => [:clean, :compile]

task :default => [:build, :test]

Dir["tasks/*.rake"].sort.each { |f| load f }

