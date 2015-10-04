```ruby
script alpha

#not behind protocal_type so its just a function that gets called in the same process
import i8::translate

#[main]
def::command yell [ :name :message -loud -nice =language[en, sp] =to ...rest ] do

    @yell_phrase = @name + ": ";

	if -loud do
		@yell_phrase += @message.to_uppercase();
	else
		@yell_phrase += @message;
	end	

	if -nice do @yell_phrase += "...please" end

	if -language do
		@yell_phrase = translate(@yellphrase, @language);
	end

	if -to then
		write @yell_phrase to @to 
	else
		write @yell_phrase
	end

end

#here write is a string
for @line in read::lines from::file @log do 
   #here write is a file...any string calls get deligated through deref ;)
   write @line to::file @log.extend_name("auto-copy")
end

# from::https @auth? @addr_with_port
# import https::from::{get, socket as http_socket} 
# from::get @url  
# from::http_socket @address
for @word in read::words from::net(@addr) do 
   write @word to::net @addr.extend_path("api/response/path")
end

for @char in read::chars from::stdin do 
   write @char #simply writes to stdout
   write::error @char to::stderr #formats @char with the write::error writer and then prints it to stderr
   write::info @char to::stdout 
   write::warning @char to::stdout
   write::inspiravetion @char to::stdout #create easy logging hooks
end

def::command foo do

end

def::reader stack [ @depth ] do
    @str = ""
    @d = 0

    for @c in read::char do
        if @d <= @depth do
            @str += @c
            @d++
            yield @str #Iterator returns Some(@str)
            continue
        end

        break
    end
end #Iterator returns None

# read::matches('Regex+.*.js/') //regex pattern...no "strings" just "patterns"
# read::chunks('|') //delimeter
# read::* is really just iterators 
# from::* is really just readers
# to::* is really just writers
# write::* is really just different writing policies...append, overwrite, prepend, insert, failifexists
#this might be an associated types problem...as in all writers but take different options?
#for to::file also include to::file::{prepend, append, overwrite, create_or_fail}
```

# builtins:
1.move
	-move @source/dir/ to @dest/dir recursively #all
	-move @source/dir/*.js to @dest/dir #some
	-move [ @a @b @c ] from @source/dir to @dest/dir #these

2.copy $a to $b [recursively]
	-copy @source/dir to @dest/dir recursively #all
	-copy @source/dir/*.js to @dest/dir #some
	-copy [ @a @b @c ] from @source/dir to @dest/dir #these

3.
	-delete @source/dir recursively
	-delete @source/dir/*.js
	-delete [ @a @b @c ] 

4.$a exists

# sigils
    @variable //script scoped variable
    $variable //script local env variable
    #variable //global env variable

# Goals
    -readable
    -intuitive
    -handle parsing args
    -cross platform
    -file/folder manipulation
    -calling other scripts
    -script error handling
    -batch processes made easy
    -importing functions from other scripts
    -autodocs/usage
    -easy file/commandline/network io
    -easy logging

# Protocals
    -User Definable
         read::* => def::reader //iterators over readers
         write::* => def::writer //formatters that pass output to writers
         cmd::* => def::command //commandline commands that get their options parsed so the command running is easy ...always get their own process
            //command protocols let you customize what to do based on the result of a command
            //ONLY SAVE AND RETURN COMMAND OUTPUT/STATE/CTXT
            //IF THEY TRY TO SAVE IT IN A VAR
            ->cmd::run('tmutil ${positional_params} ${flags}') 
            ->cmd::yell 
        flow::* => def::cmdflow //chain of commands with easy error handling flow 
    -Builtin
        from::* => source // readers
        to:* => dest //writers
        or::* => //error handling
            -a failure crashes the whole process by default
            -or::retry(N)
            -or::goto/call/run()
            -or::continue #let it fail


# Imports
    -importing a protocal from another script goes <script>::<protocal_type>::<protocal>