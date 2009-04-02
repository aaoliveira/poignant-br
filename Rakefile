LANGUAGES = FileList['lang-*'].map do |file| 
  file.gsub(/lang-(\w+)/,'\1')
end

desc "List all known localizations"
task :localizations do
  str = LANGUAGES.join(', ')
  puts "Available localizations: " + str
end

task :target do
  if ARGV.size <= 1  # Only tasks is given
    TARGET = './poignant-site'
    $stderr.puts "*warning* no path given on command line: using default #{TARGET}"
  else
    TARGET = ARGV[1]
  end
end

desc "Create default (english) version of the (poignant) guide"
task :default => [ :target ] do
  generate_for( 'en', TARGET )
end

desc "Create all versions of the (poignant) guide"
task :all => [ :target ] do
  LANGUAGES.each do |language|
    generate_for( language, TARGET )
  end
end

LANGUAGES.each do |lang|
  desc "Create #{lang} version of the (poignant) guide"
  [lang, "lang-#{lang}"].each do |lang_cmd|
    task lang_cmd.intern => [ :target ] do
      generate_for( lang , TARGET )
    end
  end
end

desc "Basic info on how to call rake with this file"
task :info do
  print "\nrake <" + LANGUAGES.join("|") + "|all|default> [path]\n\n"
  puts "Will generate the book (localized or all versions) in path."
  puts "If path is not given it will default to ./poignant-site"
  puts "Each language version will then go in a directory named "
  puts "after its name (i.e. en, fr ..)"
  puts
end

def generate_for( language , path)
  target = File.join( path, language )
  puts "About to generate for #{language} in #{target}"
  sh "ruby scripts/poignant.rb #{target} #{language}"
end

desc "Combina os trechos do poignant em um unico arquivo"
task :merge_pt do
  File.open('lang-pt/poignant.yml', "w") do |output|
    files = Dir["lang-pt/*.yml"]
    files -= %w[lang-pt/localization.yml lang-pt/poignant.yml lang-pt/poignant_merged.yml lang-pt/poignant_final.yml]
    files.sort!
    files.each do |ind|
      output.write(File.read(ind))
    end
  end
end

desc "Task for cruise Control"
task :cruise do
  CruiseControl::invoke_rake_task 'merge_pt'
  CruiseControl::invoke_rake_task 'pt'
end
