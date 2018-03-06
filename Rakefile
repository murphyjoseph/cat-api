
task :app do
  require "./app"
end

Dir[File.dirname(__FILE__) + "/tasks/*.rb"].sort.each do |path|
  require path
end

desc 'Open an irb session preloaded with this library'
task :console do
  sh 'irb -I lib -r ./app.rb'
end

desc "create a migration file"
file :create_migration do
  ARGV.each { |a| task a.to_sym do ; end }
  filename = ARGV[1]
  raise "need filename" if filename.nil? or filename.empty?
  other_migrations =  Dir[File.dirname(__FILE__) + "/db/migrate/*.rb"]

  if other_migrations.empty?
    migration_stamp = "000"
  else
    migration_stamp = other_migrations.map do |path|
      path.split("/").last.split(".rb").first.split(/[^\d{3}]/).first
    end.max_by{|x| x.to_f }
  end

  next_migration = "%03d" % (migration_stamp.to_f + 1)
  cleaned_file_name = "#{next_migration}_#{filename.gsub(" ", "_").gsub("-", "_").downcase}.rb"

  migration_path = File.join(File.dirname(__FILE__), "/db/migrate/")
  file_path = migration_path + cleaned_file_name
  touch file_path # to create the file technically shouldn't touch an existing one
  File.open(file_path, 'wb+') do |file|
    file.binmode
    file.write "Sequel.migration do\r\n\s\schange do\r\n\s\s\s\s### Add Migration\r\n\s\send\r\nend"
  end

end