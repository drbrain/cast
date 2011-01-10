# -*- mode: ruby -*-

task :default => :test

require 'rubygems'
require 'rake/testtask'
require 'rubygems/package_task'

FILES = FileList['README', 'ChangeLog', '{lib,ext,doc,test}/**/*', 'ext/yylex.c', 'lib/cast/c.tab.rb']

# cast_ext
file 'ext/cast_ext.so' => FileList['ext/*.c', 'ext/yylex.c'] do |t|
  cd 'ext' do
    ruby 'extconf.rb'
    sh 'make'
  end
end

# lexer
file 'ext/yylex.c' => 'ext/yylex.re' do |t|
  sh "re2c #{t.prerequisites[0]} > #{t.name}"
end

# parser
file 'lib/cast/c.tab.rb' => 'lib/cast/c.y' do |t|
  sh "racc #{t.prerequisites[0]}"
end

desc "Build."
task :lib => FileList['lib/cast/*.rb', 'lib/cast/c.tab.rb', 'ext/cast_ext.so']

desc "Run unit tests."
Rake::TestTask.new(:test => :lib) do |t|
  t.libs << 'ext' << 'test'
  t.test_files = FileList['test/*_test.rb']
  t.verbose = true
end

desc "Run irb with cast loaded."
task :irb => :lib do
  sh 'irb -Ilib:ext -rcast'
end

spec = Gem::Specification.new do |s|
  s.name = 'cast'
  s.summary = "C parser and AST constructor."
  s.version = '0.1.0'
  s.author = 'George Ogata'
  s.email = 'george.ogata@gmail.com'
  s.homepage = 'http://cast.rubyforge.org'
  s.rubyforge_project = 'cast'

  s.platform = Gem::Platform::RUBY
  s.extensions << 'ext/extconf.rb'
  s.files = FILES.to_a
  s.autorequire = 'cast'
  s.test_file = 'test/run.rb'
  s.has_rdoc = false

  s.add_development_dependency 'racc'
  s.requirements << 're2c'
end

Gem::PackageTask.new spec

desc "Remove temporary files in build process"
task :clean do
  rm_f 'ext/*.o'
end

desc "Remove all files built from initial source files"
task :clobber => [:clean] do
  rm_f 'ext/yylex.c'
  rm_f 'lib/cast/c.tab.rb'
  rm_f 'ext/cast_ext.so'
  rm_f 'ext/Makefile'
  rm_f 'pkg'
end

