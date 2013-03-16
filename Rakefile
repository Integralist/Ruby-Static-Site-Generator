task :build do 
    structure = {
        'assets' => {
            'images'  => [],
            'styles'  => ['base.css'],
            'scripts' => ['require.js', 'bootstrap.js']
        },
        'index.html' => 'some HTML content',
        'humans.txt' => 'some text content'
    }
    create_site_directory(set_directory, set_site_name)
    create_content structure
    
    puts "OK, everything seemed to be set-up without any errors."
    
    %x{ open . } # open up the window for the user
end

def set_directory
    STDOUT.puts %q{
I'm going to create a site structure for you. 
So can you give me a directory path to start from?
If not I'll create it on your Desktop...
    }

    directory_path = STDIN.gets.strip

    response = if directory_path.empty? then
        directory_path = 'Desktop'
        %q{No problem, I'll start from the Desktop.}
    else 
        %Q{
You told me to create this site here: "#{directory_path}".}
    end

    puts response

    directory_path # return value
end

def set_site_name
    STDOUT.puts %q{
What's the name of your site?
    }

    site_name = STDIN.gets.strip

    response = if site_name.empty? then
        site_name = 'Test Site'
        %q{No problem, I'll use a generic name.

Give me a second and I'll be right back...
        }
    else 
        %Q{
So the name of your site is: "#{site_name}".

Give me a second and I'll be right back...
        }
    end

    puts response

    site_name # return value
end

def create_site_directory directory, site_name
    if directory === 'Desktop' then
        Dir.chdir(File.expand_path '~' + '/' + directory)
    else
        begin
            Dir.chdir directory
        rescue
            puts %Q{
Hey there was an error: #{$!}
We'll create the site on your Desktop instead.
            }
            Dir.chdir(File.expand_path '~' + '/Desktop')
        end
    end

    if !File.directory? site_name then
        Dir.mkdir site_name
        Dir.chdir site_name
    else
        puts %Q{
The folder "#{site_name}" already exists, are you sure you want to continue: Y or N?
Note: we'll replace the directory completely!
        }

        continue = STDIN.gets.strip
        
        if continue.match /y|Y/ then
            FileUtils.rm_r site_name # couldn't use Dir.rmdir unless the directory was actually empty
            Dir.mkdir site_name
            Dir.chdir site_name
        else
            puts "OK, no problem - please try again."
        end
    end
end

def create_file newfile, content = ''
    file = File.new(newfile, 'w')
    file.puts content
    file.close
end

def create_content structure
    structure.each do |key, item|
        if structure.class === 'Array' then
            create_file key
        elsif item.respond_to? 'each' then
            item.each do |key, item|
                Dir.mkdir key
                Dir.chdir key
                create_content item
                Dir.chdir '..'
            end
        else
            create_file(key, item)
        end
    end
end