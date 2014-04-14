.----------------. .----------------. .----------------. .----------------. .----------------. 
| .--------------. | .--------------. | .--------------. | .--------------. | .--------------. |
| | _____  _____ | | |   _____      | | |  _________   | | |  _______     | | |      __      | |
| ||_   _||_   _|| | |  |_   _|     | | | |  _   _  |  | | | |_   __ \    | | |     /  \     | |
| |  | |    | |  | | |    | |       | | | |_/ | | \_|  | | |   | |__) |   | | |    / /\ \    | |
| |  | '    ' |  | | |    | |   _   | | |     | |      | | |   |  __ /    | | |   / ____ \   | |
| |   \ `--' /   | | |   _| |__/ |  | | |    _| |_     | | |  _| |  \ \_  | | | _/ /    \ \_ | |
| |    `.__.'    | | |  |________|  | | |   |_____|    | | | |____| |___| | | ||____|  |____|| |
| |              | | |              | | |              | | |              | | |              | |
| '--------------' | '--------------' | '--------------' | '--------------' | '--------------' |
 '----------------' '----------------' '----------------' '----------------' '----------------' 
 
Ultra - Copyright 2014 Steven Goodwin. 

Released under the GNU GPL, version 3

Version 0.1
 
 

Ultra is an ultra small (<64k binary), ultra fast, all-in-memory web server incorporating
an in-built NoSQL datastore, a data processing language, SSI, multiple configurations, and logging. 

It is intended for IoT and BigData applications.


Compile:
cd Release
make
 

Usage:
ultra site_dir
ultra site_dir runmode
ultra site_dir runmode test

Where runmode determine the type of configuration to use, e.g. develop/live, and 'test' is a simple
test output for internal use only.

It outputs its PID (for later use) to stdout, along with info/warning messages. Error messages are
sent to stderr.



Whilst running:

kill -SIGHUP pid  	; reload the config and data files
kill -SIGUSR1 pid	; serialize/flush all data to disc



Conventions:

site/db/table_name			; name=value pairs, one on each line
site/docs/view_files.htm	; all html/ascii content
site/docs/assets/image.png	; all binary content
site/config/ultra.conf		; name=value pairs. e.g. port=8088 also dev.port=8089 for 'dev' runmode


Configuration: (inside ultra.conf)

port=8123					; >1024
usefork=true				; fork at each http request
maxrequestsize=32768		; max size of request packet

To have different parameters for different runmodes simply prefix the name with the runmode,
e.g.

live.port=8080
develop.port=8081

You can then invoke this configuration automatically by selecting the 'runmode' with,

ultra site_dir live

as mentioned above.


Database:

It's a simple heirarchical name-value pair. Any text page (as found in site/docs) can
use the format {db:users.3.name} to replace the {meta data} with the information from table
'users', where ID=3, and show the 'name' field.

You may have any number of fields, tables,subfields, and IDs as you wish. They may also 
be of any type.

You can amend the data in the DB with the format: {db:users.3.name=Name name} It will take
place whenever a page is loaded containing such a string.



Meta commands:

All {commands} are processed when a page is requested. You can also nest them 

Accessing and modifying the database:

{(db:table.id.field)}	; retrieve the field, and write to the stream
{(db!:table.id.field)}	; retrieve the field, but don't echo it. Used for nested expressions
{(db:table.id.field=n)}	; assign the number 'n' to the DB field
{(db:table.id.field?n)}	; assign the number 'n' to the DB field, provided n is not empty
{(db:table.id.field+n)}	; add the number 'n' to the DB field
{(db:table.id.field-n)}	; substract the number 'n' to the DB field

Note: All =+-? can be used with the 'db' or 'db!' versions.

By convention, the 'var' table is used to store local variables for processing. This allows us
take a specific field by nesting the var in a DB request, like this:

  {(db:users.{(db:var.id)}.name)}


Data processing:

{(op.range:value minimum maximum)}	; writes the value, clamped to the given range


Other commands include:

{(day)}  {(month)}  {(year)}  {(hours)}  {(minutes)}  {(seconds)}
{(home)}		; a link to the home page. i.e. /
{(get:arg)}			; take the argument from the GET request, i.e. url?arg=1
{(ssi:filename)}		; include the filename directly into the stream
{(link:file.htm)}		; a link
{(link:file.htm The file)} ; a link with nicer anchor text
{(redirect:url)}		; issues a command to the client to redirect
{(exec:command args)}	; shell out to arbitrary command
{(config:dump)}		; write the config to the stream
{(dbdump:all)}		; write the current DB to the stream
{(stats:all)}			; write the current logging stats (i.e. page accesses) to the stream
{(rem:ignore this)}


Notes to developers:

This is a 0.1 release for good reason - I started hacking around with the idea of a web server
and got side-tracked by "real life"! My first code was 47 lines of C, which grew to 200 of C,
which then grew to 1500 of C++. And then to this...

The language change happened when I looked at the amount of
OO and utility libraries I was using. I wasn't going to re-invent/re-learn an old C library
when I knew C++ would handle it well.

Consequently, half the code has a C-oriented mindset, while the rest thinks it's C++.

I'll unify it one day...