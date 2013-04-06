class Command
    attr_reader :description

    def initialize description
        @description = description
    end

    def execute; end
end

class SetStructure < Command
    def initialize structure
        super "Create a directory structure"
        $structure = structure # return value
    end
end

class SetDirectory < Command
    def initialize
        super "Ask user some questions to help us determine the name of the directory"
    end

    def execute
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

        $directory = directory_path # return value
    end
end

class SetSite < Command
    def initialize
        super "Ask user some questions to help us determine the name of the site"
    end

    def execute
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

        $site = site_name # return value
    end
end

class CreateDirectory < Command
    def initialize directory, site_name
        super "Attempt to create the site directory and inform the user of any issues or further information required"
    end

    def execute
        @directory, @site_name = $directory, $site

        if @directory === 'Desktop' then
            Dir.chdir(File.expand_path '~' + '/' + @directory)
        else
            begin
                Dir.chdir @directory
            rescue
                puts %Q{
Hey there was an error: #{$!}
We'll create the site on your Desktop instead.
                }
                Dir.chdir(File.expand_path '~' + '/Desktop')
            end
        end

        if !File.directory? @site_name then
            Dir.mkdir @site_name
            Dir.chdir @site_name
        else
            puts %Q{
The folder "#{@site_name}" already exists, are you sure you want to continue: Y or N?
Note: we'll replace the directory completely!
            }

            if STDIN.gets.strip.match /y|Y/ then
                FileUtils.rm_r @site_name # couldn't use Dir.rmdir unless the directory was actually empty
                Dir.mkdir @site_name
                Dir.chdir @site_name
            else
                puts "OK, no problem - please try again."
            end
        end
    end
end

class CreateContent < Command
    @@run = nil

    def initialize structure
        super "Create the file structure (and any subsequent files) specified by the user"
        @structure = structure
        execute if !!@@run
    end

    def execute
        @@run = true # this class variable is needed for the inner recursive calls back to this class

        @structure.each do |key, item|
            if @structure.class === 'Array' then
                create_file key
            elsif item.respond_to? 'each' then
                item.each do |key, item|
                    Dir.mkdir key
                    Dir.chdir key
                    CreateContent.new item
                    Dir.chdir '..'
                end
            else
                create_file(key, item)
            end
        end
    end

    def create_file newfile, content = ''
        file = File.new(newfile, 'w')
        file.puts content
        file.close
    end
end

class CompositeCommand < Command
    def initialize
        @commands = []
    end

    def add_command cmd
        @commands << cmd
    end

    def execute
        @commands.each { |cmd| cmd.execute }
    end

    # At any point we can call this method to find out what would be run if we called `execute` on this class
    def description
        description = ''
        @commands.each { |cmd| puts cmd.description }
        description # return value
    end
end

class Complete < Command
    def initialize
        super "Informs the user everything is OK and then opens a Finder window pointing to the newly created site"
    end

    def execute
        puts "OK, everything seemed to be set-up without any errors."
        %x{ open . } # open up the window for the user
    end
end

task :build do 
    $directory, $site = '', '' # defaults required to be pre-set due to `execute` methods not run until later time

    cmds = CompositeCommand.new

    cmds.add_command(SetStructure.new Hash['assets' => {
            'images'  => [],
            'styles'  => ['base.css'],
            'scripts' => ['require.js', 'bootstrap.js']
        },
        'index.html' => 'some HTML content',
        'humans.txt' => 'some text content'])
    
    cmds.add_command(SetDirectory.new)
    cmds.add_command(SetSite.new)
    cmds.add_command(CreateDirectory.new($directory, $site))
    cmds.add_command(CreateContent.new $structure)
    cmds.add_command(Complete.new)

    puts %Q{
Here is a break-down of the tasks that will be run:
    }
    puts cmds.description
    
    cmds.execute
end